<p align="center">
  <a href="https://onsac.com/">
    <img src="https://onsac.com/wp-content/uploads/2020/08/tl.png" alt="Bootstrap logo" width="200" height="165">
  </a>
</p>

<h3 align="center">AIO-Integrador</h3>

<p align="center">
  Automações e Integrações sem limitações (AIops/DEVops)
  <br>
  <a href="https://onsac.com/"><strong>Conheça mais sobre nosso serviço »</strong></a>
  </p>
  
  
  ## AIO Integrador

A plataforma de integração que facilitará suas operações e otimiza o gerenciamento de volume de incidentes da sua empresa.


## Documento estalação AIO Integrador.

- [Check Prerequisites](#check-prerequisites)
- [Criar Usuário](#criar-usuário)
- [Instalar Git](#instalar-git)
- [Instalar e Configurar MONGODB](#instalar-e-configurar-MONGODB)
- [Documentation](#documentation)
- [Contributing](#contributing)
- [Community](#community)
- [Versioning](#versioning)
- [Creators](#creators)
- [Thanks](#thanks)
- [Copyright and license](#copyright-and-license)

## Check Prerequisites
    yum update
</br>
	  
    hostnamectl
</br> 

	vi /etc/hostname
</br>

	vi /etc/hosts
    
```sh  
## AIO Integrador
192.168.1.64    aio-app1.onsac.com
## Ansible
192.168.1.71    co8-ansible.onsac.com
## CTMEM
192.168.1.75    ctm900.onsac.com
```

	vi /etc/security/limits.conf
  
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
ping aio-app1.onsac.com
ping co8-ansible.onsac.com
ping ctm900.onsac.com
```
## Criar Usuário
```sh
sudo useradd -d /aio -m -c "AIO Integrador" -s /bin/bash aio
```
```sh
sudo passwd aio
```
```sh
usermod -aG wheel aio
```
## Instalar Git
```sh
yum install -y git
```
```sh
git --version
```
## Instalar e Configurar MONGODB 
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

Criar Usuário
```sh
mongo

```sh
use admin
db.createUser({user: "admin",pwd: "onSAC030",roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]})
```
```sh
use aio
db.createUser({user: "aiouser",pwd: "aioUSER030",roles: [{ role: "readWrite", db: "aio" }, { role: "userAdmin", db: "admin" }, { role: "userAdminAnyDatabase", db: "admin" }]})   ```  
```sh
exit
```
Segurança
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
Testar 
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


















