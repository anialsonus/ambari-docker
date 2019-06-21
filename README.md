# Ambari on Docker

This projects aim is to help you to get started with Ambari.

## Install Docker

Follow the description at the docker getting started page for your appropriate OS: ([Linux](http://docs.docker.com/linux/started/), [Mac](http://docs.docker.com/mac/started/), [Windows](http://docs.docker.com/windows/started/))

### OSX
Ambari containers started by ambari-function are using bridge networking. This means that you will not be able to communicate with containers directly
from host unless you specify the route to containers. You can do this with:

```
# Getting the IP of docker-machine or boot2docker
docker-machine ip <name-of-docker-vm>
# or
boot2docker ip

# Setting up the
sudo route add -net 172.17.0.0/16 <docker-machine or boot2docker>
# e.g:
sudo route add -net 172.17.0.0/16 192.168.99.100
```
**Note:**  the above mentioned route command will not survive a reboot and you need to execute again after reboot of your machine.


## Starting containers

This will start (and download if you never used it before) an image based on
Centos 7 with pre-installed Ambari 2.6.2 ready to install ADH 1.6

This git repository also contains an ambari-functions script
which will launch all the necessary containers to create a fully functional cluster. Download the file and source it:
```
. ambari-functions or source ambari-functions
```
Now you can issue commands with `adh-`prefix like:
```
adh-settings
```
To start a 3 node cluster:
```
adh-start-cluster 3
```
It will launch containers like this (1 Ambari server 2 agents 1 consul server):
```
CONTAINER ID        IMAGE                          COMMAND                  STATUS              NAMES
52b563756d26        arenadata/ambari-agent        "/usr/sbin/init syste"    Up 9 seconds        amb2
ddfc8f00d30a        arenadata/ambari-agent        "/usr/sbin/init syste"    Up 10 seconds       amb1
ca87a0fb6306        arenadata/ambari-server       "/usr/sbin/init syste"    Up 12 seconds       adh-server
7d18cc35a6b0        sequenceiq/consul:v0.5.0-v6   "/bin/start -server -"    Up 17 seconds       adh-consul
```

Now you can reach the Ambari UI on the adh-server container's 8080 port. Type the `adh-settings` for IP:
```
adh-settings
...
AMBARI_SERVER_IP=172.17.0.17
```

## Cluster deployment via blueprint

Once the container is running, you can deploy a cluster. Instead of going to
the webui, we can use ambari-shell, which can interact with ambari via cli,
or perform automated provisioning. We will use the automated way, and of
course there is a docker image, with prepared ambari-shell in it:

```
adh-shell
```

Ambari-shell uses Ambari's [Blueprints](https://cwiki.apache.org/confluence/display/AMBARI/Blueprints)
capability. It posts a cluster definition JSON to the ambari REST api,
and 1 more json for cluster creation, where you specify which hosts go
to which hostgroup.

Ambari shell will show the progress in the upper right corner.
