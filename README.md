## Install docker

```bash
sudo apt update -y && sudo apt upgrade -y

sudo apt install apt-transport-https ca-certificates curl software-properties-common

```

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```bash
sudo apt update
sudo apt-cache policy docker-ce
sudo apt install docker-ce -y
```

Check status docker

```bash
sudo systemctl status docker
```

Add docker to sudo role

```bash
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
newgrp docker
docker --version
```

## Install docker-compose

```bash
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose version
```

## Common command used

**Delete all containers**

```bash
docker rm -f $(docker ps -a -q)
```

**Delete all volumes**

```bash
docker volume rm $(docker volume ls -q)
```

**Delete all images**

```bash
docker rmi $(docker images -a -q)
```
