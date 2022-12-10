# RaspberryPi-Docker-Grafana
Raspberry Pi Docker Grafana

```
REF: https://grafana.com/grafana/download/9.3.1?platform=docker <- Latest from December 10, 2022

time docker pull grafana/grafana-enterprise:9.3.1


##################
# Manual testing #
##################
docker run --rm --name=grafana -p 3000:3000 grafana/grafana-enterprise:9.3.1
docker run -d --name=grafana -p 3000:3000 grafana/grafana-enterprise:9.3.1
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

###############################################################################
# Query examples #
##################

# Guage: Current Solar Flux Index
from(bucket:"db_map")
    |> range(start: -2m, stop: now())
    |> filter(fn: (r) => r._measurement == "telegraf_solar_data" and r._field == "sfi")

# Guage: Current Planetary A
from(bucket:"db_map")
    |> range(start: -2m, stop: now())
    |> filter(fn: (r) => r._measurement == "telegraf_solar_data" and r._field == "a")

# Guage: Current Planetary K
from(bucket:"db_map")
    |> range(start: -2m, stop: now())
    |> filter(fn: (r) => r._measurement == "telegraf_solar_data" and r._field == "k")

# Time series: Solar Flux Index
from(bucket:"db_map")
    |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
    |> filter(fn: (r) => r._measurement == "telegraf_solar_data" and r._field == "sfi")
    |> aggregateWindow(every: $__interval, fn: mean)

# Time series: Planetary A
from(bucket:"db_map")
    |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
    |> filter(fn: (r) => r._measurement == "telegraf_solar_data" and r._field == "a")
    |> aggregateWindow(every: $__interval, fn: mean)

# Time series: Planetary K
from(bucket:"db_map")
    |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
    |> filter(fn: (r) => r._measurement == "telegraf_solar_data" and r._field == "k")
    |> aggregateWindow(every: $__interval, fn: mean)

# Time series: Combined - Solar Flux Index, Planetary A and Planetary K
from(bucket:"db_map")
    |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
    |> filter(fn: (r) => r._measurement == "telegraf_solar_data" and r._field == "sfi" or r._field == "k" or r._field == "a")
    |> aggregateWindow(every: $__interval, fn: mean)
```
