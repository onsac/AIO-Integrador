<p align="center">
  <a href="https://onsac.com/">
    <img src="https://onsac.com/wp-content/uploads/2020/08/tl.png" alt="Bootstrap logo" width="200" height="180">
  </a>
</p>

<h3 align="center">AIO Integrador</h3>

<p align="center">
  Automações e Integrações sem limitações (AIops/DEVops)
  <br>
  <a href="https://onsac.com/"><strong>Conheça mais sobre nosso serviço »</strong></a>
  </p>
  
  
## AIO Integrador

A plataforma de integração que facilitará suas operações e otimiza o gerenciamento de volume de incidentes da sua empresa.


## Procedimento de Instalação e Configuração

- [Configura SO](#configura-so)
- [Cria conta no SO](#cria-conta-no-so)
- [Instala Git](#instala-git)
- [Instala e Configura MONGODB](#instala-e-configura-MONGODB)
- [Instala nvm](#instala-nvm)
- [Instala e Configura AIO Integrador](#instala-e-configura-aio-integrador)
- [Configura Ansible](#configura-ansible)
- [Configura Control-M](#configura-controlm)

## Configura SO

Hostname e IPs

```sh 
hostnamectl
```
```sh 
vi /etc/hostname
```
```sh 
vi /etc/hosts
```    
```sh  
## AIO Integrador
192.168.1.64    aio-app1
## Ansible
192.168.1.71    ansible
## CTMEM
192.168.1.75    controlm
```
Parâmetros do SO

```sh
vi /etc/security/limits.conf
```  
```sh
*               hard    nofile          64000
*               soft    nofile          64000
*               hard    nproc           64000
*               soft    nproc           64000

```
```sh
reboot
```
```sh
hostnamectl
```
```sh
ulimit -a
```
```sh
ping aio-app1
ping ansible
ping controlm
```
## Cria conta no SO
```sh
sudo useradd -d /aio -m -c "AIO Integrador" -s /bin/bash aio
```
```sh
sudo passwd aio
```
```sh
usermod -aG wheel aio
```
## Instala Git
```sh
yum update -y
```
```sh
yum install -y git
```
```sh
git --version
```
## Instala e Configura MONGODB 

Instalação do MONGODB

```sh
vi /etc/yum.repos.d/mongodb-org-4.0.repo
```
```sh
[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
```
```sh
sudo yum install -y mongodb-org
```

Configuração do MONGODB

```sh
vi /etc/mongod.conf
```
```sh
net:
  bindIp: 0.0.0.0
```
```sh
vi /usr/lib/systemd/system/mongod.service
```
```sh
#Environment="OPTIONS=-f /etc/mongod.conf"
Environment="OPTIONS=--quiet -f /etc/mongod.conf"
```
```sh
sudo systemctl start mongod
```
```sh
sudo systemctl daemon-reload
```
```sh
sudo systemctl status mongod
```
```sh
sudo systemctl enable mongod
```
```sh
sudo systemctl stop mongod
```
```sh
sudo systemctl restart mongod
```

Criação do Usuário no DB
```sh
mongo
```
```sh
use admin
db.createUser({user: "admin",pwd: "onSAC030",roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]})
```

```sh
use aio
```
```sh
db.createUser({user: "aiouser",pwd: "aioUSER030",roles: [{ role: "readWrite", db: "aio" }, { role: "userAdmin", db: "admin" }, { role: "userAdminAnyDatabase", db: "admin" }]})
```
```sh
exit
```
Habilitando a segurança no MONGODB

```sh
vi /etc/mongod.conf
```
```sh
security:
  authorization: enabled
```
```sh
sudo systemctl restart mongod
```
Testando a conexão com os novos usuários
```sh
mongo
```
```sh
use aio 
db.auth("aiouser", "aioUSER030")
```
```sh
use admin
db.auth("admin", "onSAC030")
```
```sh
mongo mongodb://aiouser:aioUSER030@127.0.0.1:27017/aio
```

## Instala nvm

Instalação do NODEJS e NPM

```sh
sudo su - aio
```
```sh
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```
```sh
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
```
```sh
nvm install 11
```

## Instala e Configura AIO Integrador

Deploy dos componentes da solução

```sh
mkdir /aio/aiop
```
```sh
cd /aio/aiop
```
```sh
git clone https://onsac@bitbucket.org/onsac/aio-setup.git	
``` 
```sh
git clone https://onsac@bitbucket.org/onsac-aio/aio-ansible.git
```
```sh
git clone https://onsac@bitbucket.org/onsac-aio/aio-api.git
```
```sh
git clone https://onsac@bitbucket.org/onsac-aio/aio-app.git
```
```sh
ln -s /aio/aiop/aio-setup/.aio/aio-prd-config-geral.yml .production-aio-config-geral.yml
```
```sh
ln -s /aio/aiop/aio-setup/.aio/aio-prd-config-regra.yml .production-aio-config-regra.yml
```
Instalação do PM2

```sh
npm install -g pm2
```
```sh
pm2 install pm2-logrotate 
```
```sh
pm2 link svnfgywh46k3e51 dlrucnwgh2w7bzs
```
```sh
pm2 startup
```
```sh
sudo env PATH=$PATH:/aio/.nvm/versions/node/v11.15.0/bin /aio/.nvm/versions/node/v11.15.0/lib/node_modules/pm2/bin/pm2 startup systemd -u aio --hp /aio
```
```sh
pm2 start /aio/aiop/aio-ansible/aio-ansible.js
```
```sh
pm2 start /aio/aiop/aio-ansible/aio-ansible-track.js
```
```sh
pm2 start /aio/aiop/aio-api/aio-api.js
```
```sh
pm2 start /aio/aiop/aio-app/aio-app.js
```
Criação dos usuários da API e Administração

```sh
cd /aio/aiop/aio-setup
```
```sh
node setupUsers admin ######### true n
```
```sh
node setupUsers aiointegrador ######### true n
```
## Configura Ansible

Configuração dos Playbooks
```sh
mkdir /var/lib/awx/projects/aio-ansible
```
```sh
cd /var/lib/awx/projects/aio-ansible
```
```sh
wget https://github.com/onsac/aio-ansible-jobs/blob/master/aio-ansible-playbook-Job_Command.yml
```
```sh
wget https://github.com/onsac/aio-ansible-jobs/blob/master/aio-ansible-playbook-Job_EmbeddedScript.yml
```
```sh
wget https://github.com/onsac/aio-ansible-jobs/blob/master/aio-ansible-playbook-Job_Script.yml
```
<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible(1).png" alt="Tela-ansible(1)" >
</p>

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible(2).png" alt="Tela-ansible(2)" >
</p>

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(3).png" alt="Tela-ansible(3)" >
</p>

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(4).png" alt="Tela-ansible(4)" >
</p>


<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(5).png" alt="Tela-ansible(5)" >
</p>

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(6).png" alt="Tela-ansible(6)" >
</p>


<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(7).png" alt="Tela-ansible(7)" >
</p>



## Configura Control-M

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/aio-config-geral.PNG" alt="Configuração Geral" >
</p>









