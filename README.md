# Jnekins spring kubernetes
- Instale o vagrant
- Comandos:
```
vagrant init -m (gera o arquivo)
vagrant up
```
- Dentro da maquina do jenkinsserver, instale o java, docker e o jenkins:
```
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -

sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
    
sudo apt-get update

sudo apt-get install jenkins
```
