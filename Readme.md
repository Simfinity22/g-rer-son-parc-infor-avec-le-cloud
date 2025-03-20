#**TP1: Containers**

Dans ce TP on va aaborder plusieurs points autour de la conteneurisation.

##**Part I: Docker basics** 

###**1. Install**

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

###**2. Vérifier l'install**

Bon j'vais pas détailler ici, on à les commandes de base : docker ps, docker run... Bah on les tape et on voit ce que ça fait

###**3. Lancement de conteneurs**

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

##**Part II: Images**



##**Part III: docker-compose**

##**Part IV: Docker security**

 
