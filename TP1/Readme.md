# **TP1: Containers**

Dans ce TP on va aaborder plusieurs points autour de la conteneurisation.

## **Part I: Docker basics** 

### **1. Install**

Let's go taper des p'tites commandes :

Add Docker's official GPG key:
    - sudo apt-get update
    - sudo apt-get install ca-certificates curl
    - sudo install -m 0755 -d /etc/apt/keyrings
    - sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
    - sudo chmod a+r /etc/apt/keyrings/docker.asc

Sur cette partie des commandes on créer un répertoire dans lequel on va mettre la clé GPG (GNU Privacy Guard) de Docker.

Le GNU Privacy Guard est un logiciel qui permet la transmission de messages électroniques signés et chiffrés, garantissant ainsi leurs authenticité, intégrité et confidentialité. 
      
Add the repository to Apt sources:

    - echo \  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
    - sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    - sudo apt-get update

Ici, on créer ajoute le dépôt docker aux sources APT. Les dépôts APT sont des "sources de logiciels", concrètement des serveurs qui contiennent un ensemble de paquets. À l'aide d'un outil appelé gestionnaire de paquets, vous pouvez accéder à ces dépôts, les télécharger et installer les logiciels de votre choix. 

Puis : 

    - sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    - sudo docker run hello-world

Installation de docker. 

Start docker :

    - sudo systemctl start docker
 
Pour l’activer directement au démarrage de la machine :

    - sudo systemctl enable docker 

On vérifie le status avec :

    - sudo systemctl status docker

Ajouter votre utilisateur au groupe docker :
    
    - sudo usermod -aG docker $(whoami)

déconnexion :

    - exit ou ctrl d

reconnexion 

Bon c'est bien beau on a docker mais maintenant go l'utiliser :)

### **2. Vérifier l'install**

Bon j'vais pas détailler ici, on à les commandes de base : docker ps, docker run... Bah on les tape et on voit ce que ça fait

### **3. Lancement de conteneurs**

A. Utiliser la commande docker run

    - docker run --name web -p 9999:80 nginx

B.  Rendre le service dispo sur internet

On ouvre un port sur l’interface web d’Azure :

Paramètre réseau > Règle > créer une règle de port > Règle d’entrée > en entrée et sortie on laisse Any et en numéro de port on met 80 et protocole TCP

Ensuite sur la VM on ouvre un nouveau terminal et on fait un :
 
    - curl <IP_MACHINE> : curl 20.251.169.36
```
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to nginx!</title>
    <style>
        html { color-scheme: light dark; }
        body { 
            width: 35em; 
            margin: 0 auto;
            font-family: Tahoma, Verdana, Arial, sans-serif; 
        }
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
</html>
```

C. Custom un peu le lancement du conteneur

d’abord on créer un fichier nommé : simon.conf 
```
server {
  # on définit le port où NGINX écoute dans le conteneur
  listen 7777;
  
  # on définit le chemin vers la racine web
  # dans ce dossier doit se trouver un fichier index.html
  root /var/www/; 
}
```

Ensuite on créer un fichier index.html
 on écrit : Hello

commande pour lancer le docker : 
  
  - docker run --name toto --memory='512m' --memory-swap='512m' -v /home/simon/Part1/simon.conf:/etc/nginx/conf.d/simon.conf -v /home/simon/Part1/index.html:/var/www/index.html -p 9999:7777 nginx 

preuve :

  - curl http://localhost:9999

Hello

## **Part II: Images**

### **1. Construire votre propre image**

On écrit le Dockerfile, en gros c'est un fichier texte qui permet de créer une image et qui définit tout ce qui est nécessaire pour exécuter une application dans un conteneur.

  - cat Dockerfile

``` 
FROM ubuntu:latest

RUN apt update -y 
RUN apt install -y apache2

RUN echo "ServerName localhost" >> /etc/apache2/apache2.conf

COPY apache2.conf /etc/apache2/apache2.conf

COPY index.html /var/www/html/index.html


RUN mkdir -p /var/log/apache2 
RUN ln -sf /dev/stdout /var/log/apache2/access.log 
RUN ln -sf /dev/stderr /var/log/apache2/error.log

EXPOSE 80

CMD ["apachectl", "-D", "FOREGROUND"]

```
  - docker build -t my_own_app .        <!--Ici on construit l'image à partir du dossier dans lequel on se trouve (.) et on lui donne un nom (-t) -->
  - docker run -p 8889:80 my_own_app  <!-- Ici on lance le conteneur docker que l'on vient de build -->

  - curl http://localhost:8889


```
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bienvenue sur mon serveur Apache</title>
</head>
<body>
    <h1>Hello world !</h1>
</body>
</html>
```

## **Part III: docker-compose**

**2. WikiJS**

  - curl http://20.251.169.36:80

```
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta charset="UTF-8">
    <meta name="viewport" content="user-scalable=yes, width=device-width, initial-scale=1, maximum-scale=5">
    <meta name="theme-color" content="#1976d2">
    <meta name="msapplication-TileColor" content="#1976d2">
    <meta name="msapplication-TileImage" content="/_assets/favicons/mstile-150x150.png">
    
    <title>Wiki.js Setup</title>

    <!-- Favicons -->
    <link rel="apple-touch-icon" sizes="180x180" href="/_assets/favicons/apple-touch-icon.png">
    <link rel="icon" type="image/png" sizes="192x192" href="/_assets/favicons/android-chrome-192x192.png">
    <link rel="icon" type="image/png" sizes="32x32" href="/_assets/favicons/favicon-32x32.png">
    <link rel="icon" type="image/png" sizes="16x16" href="/_assets/favicons/favicon-16x16.png">
    <link rel="mask-icon" href="/_assets/favicons/safari-pinned-tab.svg" color="#1976d2">
    <link rel="manifest" href="/_assets/manifest.json">

    <!-- Site Configuration -->
    <script>
        var siteConfig = { "title": "Wiki.js" };
    </script>

    <!-- Stylesheets -->
    <link type="text/css" rel="stylesheet" href="/_assets/css/setup.22871ffac1b643eed4d9.css">

    <!-- Scripts -->
    <script type="text/javascript" src="/_assets/js/runtime.js?1738531300"></script>
    <script type="text/javascript" src="/_assets/js/setup.js?1738531300"></script>
</head>
<body>
    <div id="root">
        <setup wiki-version="2.5.306"></setup>
    </div>
</body>
</html>
```

**3. Make your own image**

  - cat Dockerfile 

```
FROM python:3

RUN mkdir /opt/app/

WORKDIR /opt/app

COPY toto/ .

RUN pip install -r requirements.txt

CMD [ "/usr/local/bin/python3.13", "app.py" ]
```

  - cat docker-compose.yaml 

```
services:
  web:
    image: own-app:latest
    build: .
    ports:
      - "10000:8888"
  db:
    image: "redis:alpine"
```


## **Part IV: Docker security**

Prouvez que vous pouvez devenir root

  - docker run -v /:/mnt:ro -it alpine cat /etc/shadow 

```
root:*::0:::::
bin:!::0:::::
daemon:!::0:::::
lp:!::0:::::
sync:!::0:::::
shutdown:!::0:::::
halt:!::0:::::
mail:!::0:::::
news:!::0:::::
uucp:!::0:::::
cron:!::0:::::
ftp:!::0:::::
sshd:!::0:::::
games:!::0:::::
ntp:!::0:::::
guest:!::0:::::
nobody:!::0:::::
```
