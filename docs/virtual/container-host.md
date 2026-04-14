---
title: Configure as a container host
toc: true
---

We want to be able to run a container host on our machine, to allow containers to run on it. For this, we will install [Docker Engine](https://docs.docker.com/engine/).

## What is a container?

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another.

## What is Docker?

Docker is an open source platform that allows applications and services to be packaged into containers, along with all required dependencies. Those containers can then be executed directly. You can run many Docker containers, each with its own application, on a single machine. Those applications will be isolated from one another, thus providing data security and reliability.

## Perform the installation

There are 2 different ways to install Docker engine:
- [Using the apt repository](https://docs.docker.com/engine/install/debian/#install-using-the-repository) (recommended)
- [Using the convenience script](https://docs.docker.com/engine/install/debian/#install-using-the-convenience-script) (faster, but might be unsafe)

## Post-installation steps

The official documentation describes [multiple post-installation steps](https://docs.docker.com/engine/install/linux-postinstall/). We recommend applying the following ones.

### Run docker without sudo

By default, you must use `sudo` for every Docker command. You can add your current user to the `docker` group to run commands more easily.

```sh
sudo adduser $USER docker
```

You should now either logout and log back in, or execute the following to apply the changes to your current session

```sh
newgrp docker
```

### Verify the installation

Nothing beats a good old `hello-world` test to check that all is running as expected. Confirm that Docker is installed and running correctly by executing the following:

```sh
docker run --rm hello-world
```

You can also validate that it is running with the right `aarch64` 64 bits architecture by executing:

```sh
docker info | grep Architecture
```

## Restrict access to containers

> ‼️ Installing Docker on a machine that runs a firewall using UFW poses significant security risks, which we need to mitigate. Essentially, the issue is that Docker bypasses the UFW rules and the **published ports can be accessed from outside**. This is [confirmed in the Docker documentation](https://docs.docker.com/engine/network/packet-filtering-firewalls/#docker-and-ufw).

To fix this, no configuration change is required on the Docker side. We need to edit the UFW configuration file `/etc/ufw/after.rules` and add the following rules at the end of it:

```
# BEGIN UFW AND DOCKER
*filter
:ufw-user-forward - [0:0]
:ufw-docker-logging-deny - [0:0]
:DOCKER-USER - [0:0]
-A DOCKER-USER -j ufw-user-forward

-A DOCKER-USER -m conntrack --ctstate RELATED,ESTABLISHED -j RETURN
-A DOCKER-USER -m conntrack --ctstate INVALID -j DROP
-A DOCKER-USER -i docker0 -o docker0 -j ACCEPT

-A DOCKER-USER -j RETURN -s 10.0.0.0/8
-A DOCKER-USER -j RETURN -s 172.16.0.0/12
-A DOCKER-USER -j RETURN -s 192.168.0.0/16

-A DOCKER-USER -j ufw-docker-logging-deny -m conntrack --ctstate NEW -d 10.0.0.0/8
-A DOCKER-USER -j ufw-docker-logging-deny -m conntrack --ctstate NEW -d 172.16.0.0/12
-A DOCKER-USER -j ufw-docker-logging-deny -m conntrack --ctstate NEW -d 192.168.0.0/16

-A DOCKER-USER -j RETURN

-A ufw-docker-logging-deny -m limit --limit 3/min --limit-burst 10 -j LOG --log-prefix "[UFW DOCKER BLOCK] "
-A ufw-docker-logging-deny -j DROP

COMMIT
# END UFW AND DOCKER
```

You find complete documentation on these rules on [https://github.com/chaifeng/ufw/readme.md](https://github.com/chaifeng/ufw-docker?tab=readme-ov-file#solving-ufw-and-docker-issues)

We can apply this change by executing the following:

```sh
sudo systemctl restart ufw
```
