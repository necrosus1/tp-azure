
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
```

🌞 Custom lancement du conteneur
```
curl http://13.73.48.134:7777
```

## Part II : Images

Construisez votre propre Dockerfile
```
FROM debian:latest

RUN apt update -y && apt upgrade -y
RUN apt install -y apache2

COPY apache2.conf /etc/apache2/apache2.conf
COPY index.html /var/www/html/index.html

EXPOSE 80
CMD ["apache2", "-DFOREGROUND"]
```

part3.md (WikiJS docker-compose)
```yaml
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
  wiki:
    image: requarks/wiki:2
    restart: unless-stopped
    ports:
      - "3000:3000"
```

## Make your own meow
Dockerfile
```
FROM python:3.9-slim
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

## Part IV : Docker security
🌞 Devenir root
```
docker run --rm -v /:/mnt alpine chroot /mnt cat /etc/shadow
```

🌞 Utiliser Trivy
```
sudo trivy image ghcr.io/requarks/wiki:2
sudo trivy image mysql:5.7
sudo trivy image dockerfile
sudo trivy image nginx:latest
```

