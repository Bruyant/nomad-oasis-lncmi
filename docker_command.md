 sudo apt update
 sudo apt upgrade
 ssh hm@130.120.45.150 : mdp= HMpourOD2026! | si vous souhaitez vous connecter à la machine Highmagnetos
 git config --global user.email "nicolas.bruyant@lncmi.cnrs.fr"
 git config --global user.name "Bruyant"

Cloner les repos et suivre premier déploiement comme indiqué dans readme de nomad_oasis_lncmi.

#PROCESS D'UTILISATION DOCKER POUR NOMAD OASIS LNCMI

## Tester l'image Jupyter Nomad-tools_pymagnetos en local

docker build -f src/nomad_tools_pymagnetos/north_tools/north_tool_pymagnetos/Dockerfile     -t ghcr.io/bruyant/nomad_tools_pymagnetos:latest .
docker run -p 8888:8888 ghcr.io/bruyant/nomad_tools_pymagnetos:latest


### Après validation du plugin nomad_tools_pymagnetos, faire les commits & push de diff et créer un nouveau tag de release. (VOIR DOCS DIRECTEMENT DOCS DANS REPOS:README DE NORTH_TOOL)

### Mettre à jour le plugin dans nomad_oasis_lncmi avec la nouvelle release du plugin, faire le commit et attendre nouvelle génération d'images avec la CI.

### Après validation de la CI, suivre les étapes ci-dessous : 
cd nomad_oasis_lncmi
git pull 

### Récupérer USERID de votre docker et mettre à jour dans docker-compose.yml:service NORTH
getent group docker | -- mettre le resulat dans nano docker-compose.yml

#### ENSUITE :
COMPOSE_PROJECT_NAME=nomad_oasis_lncmi docker compose pull
COMPOSE_PROJECT_NAME=nomad_oasis_lncmi docker compose up -d
#### Normalement, jusqu'ici tout devrait marcher sans problème et vous pouvez tester NOMAD sur votre localhost : http://localhost/nomad/'
  466  docker ps -a | -- Pour vérifier tous les containers lancés, certains comme ceux dans NORTH apparaissent uniquement après le launch depuis NOMAD.

Vous pouvez les inspecter individuellement comme ceci : docker volume inspect <nomad_oasis_postgresql> -- nom d'un volume ou voir les logs via par exple: docker logs <north_tool_pymagnetos>. 

Veuillez à mettre entre les <> le nom de l'image que vous pouvez récupérer via la champ 'NAMES' dans la commande 'docker ps -a'.

 
##### Pour toute modification ultérieure qui entraine la génération de nouvelles images via la CI, suivre les étapes ci-dessous.
  COMPOSE_PROJECT_NAME=nomad_oasis_lncmi docker compose stop
  COMPOSE_PROJECT_NAME=nomad_oasis_lncmi docker compose down
  COMPOSE_PROJECT_NAME=nomad_oasis_lncmi docker compose down -v
  docker system prune -a --volumes
  COMPOSE_PROJECT_NAME=nomad_oasis_lncmi docker compose pull
  COMPOSE_PROJECT_NAME=nomad_oasis_lncmi docker compose up -d

#### Pour lister les volumes docker montés, voici la commande (volumes précisés dans docker-compose.yml à la fin du fichier) : docker volume ls
  
#### Pour supprimer les volumes relatifs au projet nomad_oasis: docker volume rm $(docker volume ls -q | grep nomad_oasis)
#### POUR SUPPRIMER TOUS LES CONTAINERS : docker rm -f $(docker ps -aq) -- a faire avec précaution si vous avez d'autres d'autres containers pour d'autres projets'

#### POUR SUPPRIMER TOUS LES VOLUMES: docker volume rm $(docker volume ls -q) -- a utiliser avec précaution également

docker ps -a | Pour lister les containers 

#### POUR VOIR LES LOGS D'UN CONTAINER:  docker logs nomad_oasis_lncmi-north-1 
  

docker network ls -- pour lister les réseaux dans le docker

docker inspect nomad_oasis_lncmi-north-1 #### POUR INSPECTER UN CONTAINER 
  
docker compose ls -- pour voir tous les docker lancés avec docker compose

## NB: N'oubliez pas de faire un git pull si vous faites des modifs depuis Github ou Github codespaces'
