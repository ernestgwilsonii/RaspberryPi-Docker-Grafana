# RaspberryPi-Docker-Grafana
Raspberry Pi Docker Grafana

```
REF: https://grafana.com/grafana/download/9.0.0-beta3?platform=docker <- Latest beta from June 7, 2022

time docker pull grafana/grafana-enterprise:9.0.0-beta3

# To get a tag list, add this bash function
function list-docker-tags(){
    wget -q https://registry.hub.docker.com/v1/repositories/$1/tags -O -  | sed -e 's/[][]//g' -e 's/"//g' -e 's/ //g' | tr '}' '\n'  | awk -F: '{print $3}' | grep -v ^master
}

list-docker-tags grafana/grafana-enterprise


# Manual testing
################
docker run --rm --name=grafana -p 3000:3000 grafana/grafana-enterprise:9.0.0-beta3
docker run -d --name=grafana -p 3000:3000 grafana/grafana-enterprise:9.0.0-beta3
docker ps
# http://RaspberryPiAddress:3000 <-- admin/admin and set a new password
docker exec -it grafana bash
ls -alF /etc/grafana
ls -alF /var/lib/grafana
#docker stop grafana
#docker rm grafana


####################################################
# Grafana (in a Docker Container) for Raspberry Pi #
#                         REF: https://grafana.com #
####################################################
# ARM 64bit for Raspberry Pi:
REF: https://grafana.com/grafana/download/8.4.4?platform=docker

###############################################################################
# First time setup #
####################
# Grafana (stand-alone for Docker Swarm)
# REF: https://hub.docker.com/r/grafana/grafana/
# and http://docs.grafana.org/installation/configuration/

# Create the bind mount directories and copy files to the correct node based on constraints in the docker-compose!
mkdir -p /opt/docker/grafana/etc/grafana
mkdir -p /opt/docker/grafana/var/lib/grafana
chmod a+rw -R /opt/docker/grafana

##########
# Deploy #
##########
# Deploy the stack into a Docker Swarm
docker stack deploy -c docker-compose.yml grafana-stack
#docker stack rm grafana-stack

# Verify
docker service ls | grep grafana-stack
docker service logs -f grafana-stack_grafana
###############################################################################
```
