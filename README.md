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

A plataforma de integração que facilita a jornada de automação e redução de custos da sua empresa.


## Procedimento de Instalação e Configuração

- [Configura SO](#configura-so)
- [Cria conta no SO](#cria-conta-no-so)
- [Instala Git](#instala-git)
- [Instala e Configura MONGODB](#instala-e-configura-MONGODB)
- [Instala nvm](#instala-nvm)
- [Instala e Configura AIO Integrador](#instala-e-configura-aio-integrador)
- [Configura Ansible](#configura-ansible)
- [Configura ControlM](#configura-controlm)

## Configura SO

Antes de iniciar os procedimentos verifique se o servidor possui acesso a internet, se o acesso for via proxy <a href="https://github.com/onsac/AIO-Integrador/blob/master/Configura%C3%A7%C3%A3o%20de%20um%20proxy%20corporativo.md">Configurando o acesso via Proxy</a>


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

<strong>NOTA : Não use @ na senha</strong>

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
git config —-global credential.helper store
```
Altere o Password
```sh
echo "https://onsac:<pwd>@bitbucket.org" > ~/.git-credentials
```
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
git clone https://onsac@bitbucket.org/onsac/aio-node-logs.git
```
```sh
git clone https://onsac@bitbucket.org/onsac/aio-node-api.git
```
```sh
git clone https://onsac@bitbucket.org/onsac/aio-node-snmp.git
```
```sh
git clone https://onsac@bitbucket.org/onsac/aio-app.git
```
```sh
git clone https://onsac@bitbucket.org/onsac/aio-api.git
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

Criação dos usuários da API e Administração

```sh
cd /aio/aiop/aio-setup
```
```sh
node setupUsers admin ######### true n
```
```sh
node setupUsers aiointegrador ######### true s
```
Start aio-ansible

```sh
cd /aio/aiop/aio-setup

node aio-ansible-start.js
```

## Configura Ansible

Configuração dos Playbooks no <strong>Servidor Ansible Tower</strong>

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
Preparando o Ansible para integração com AIO

Para permitir a execução nos servidores onde os JOBs são executados originalmente pelo Control-M, precisamos adicionar os hosts (nodeId) e use (runAs), o nome da credencial deve seguir o padrão (host@user)

Usuário Linux
<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible(1).png" alt="Tela-ansible(1)" >
</p>
Usuário Windowns
<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible(9).jpeg" >
</p>

Criar um inventário com o nome (aio-ansible)

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible(2).png" alt="Tela-ansible(2)" >
</p>

Adiocione os respectivos Hosts (nodeId) onde teremos execuções de JOBs

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(3).png" alt="Tela-ansible(3)" >
</p>

```sh
ansible_connection: ssh
ansible_python_interpreter: '{{ ansible_playbook_python }}'
```

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(4).png" alt="Tela-ansible(4)" >
</p>

```sh
ansible_port: 5985
ansible_connection: winrm
ansible_winrm_transport: credssp
ansible_winrm_server_cert_validation: ignore
ansible_winrm_credssp_disable_tlsv1_2: true
ansible_winrm_message_encryption: always
ansible_python_interpreter: '{{ ansible_playbook_python }}'
```
<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible(8).jpeg" >
</p>

Criar um projeto com o nome (aio-ansible), do tipo Manual apontando para o diretório (aio-ansible)

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(5).png" alt="Tela-ansible(5)" >
</p>

Criar o usuário do integrador (aiointegrador)
<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(6).jpeg" alt="Tela-ansible(6)" >
</p>

Adicionar as respectivas permissões ao usuário

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(7).png" alt="Tela-ansible(7)" >
</p>

Para concluir a configuração, volte no Servidor do AIO

```sh
su - aio
```
```sh
cd /aio/aiop/aio-setup
```
```sh
node set_hash.js <senha do aiointegrador no ansible>
```
```sh
node set_hash.js <senha do aiointegrador no control-m>
```
```sh
node set_hash.js <senha do aiointegrador no integrador>
```
```sh
vi /aio/aiop/.production-aio-config-geral.yml
```
Procure "ANSIBLE"
Altere a url e password

```sh
            - sistema  : ANSIBLE
              url      : https://<servidor ansible>
              api      : /api/v2/
              inventory: aio-ansible
              user     : aiointegrador
              password : <hash senha ansible>
```
Procure "sistema  : Control-M"
Altere o hostname, port, user e password

```sh
            - sistema  : Control-M
              hostname : <servidor control-m>
              port     : <port>
              api      : /automation-api
              versao   : 900-500
              user     : <aiointegrador>
              password : <hash senha control-m>
```
Procure "aioapi  :"
Altere o user e password

```sh
    aioapi  :
        url        : http://aio.onsac.com
        port       : 8081
        user       : <aiointegrador>
        password   : <hash senha integrador>
        module     : aio-api
```


## Configura ControlM

Na console do Control-M Configuration Manager, crie um usuário para o integrador (aiointegrador)

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%201.jpeg" alt="Tela-control-m (1)" >
</p>

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%202.jpeg" alt="Tela-control-m (2)" >
</p>

Coloque no grupo AdminGroup

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%203.jpeg" alt="Tela-control-m (3)" >
</p>

Entre na console do Control-M Application Integrator

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%204.png" alt="Tela-control-m (4)" >
</p>

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%205.jpeg" alt="Tela-control-m (5)" >
</p>

Baixe o arquivo do plugin aioansible do servidor do AIO Integrador, que fica na pasta /aio/aiop/aio-ansible/projects

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%206.jpeg" >
</p>

Importe o arquivo do plugin

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%207.jpeg.jpeg" >
</p>

Clique no plugin 

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%208.jpeg" alt="Tela-control-m (8)" >
</p>

Clique no botão Deploy

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%209.jpeg" alt="Tela-control-m (9)" >
</p>

Selecione o agente desejado e clique no botão Deploy

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2010.jpeg" alt="Tela-control-m (10)" >
</p>

Caso o agente não apareça na lista, siga os passos a seguir para realizar o Deploy do Application Pack

No CCM entre na opção manager

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2027.png" alt="Tela-control-m (27)" >
</p>
 Crie a atividade de Upgrade e Install do Application Pack no servidor desejado.
 
<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2028.jpeg" alt="Tela-control-m (28)" >
</p>

Aguarde a conclusão da instalação. e Volte no Application Integrator para seguir com o Deploy do plugin

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2029.jpeg" alt="Tela-control-m (29)" >
</p>

Aguarde a conclusão com sucesso do processo de deploy, e na sequência volte para a Console co Control-M Configuration Manager, para criar o profile. 
Selecione o agente onde foi realizado o Deploy, clicando com o botão direito.

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2011.jpeg" alt="Tela-control-m (11)" >
</p>

Crie um profile para cada prefixo de folder seguindo o exemplo abaixo, para concluir a configuração do Plugin aioansible

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2012.jpeg" alt="Tela-control-m (12)" >
</p>

Na console do Control-M entre no domínio Planer para organizar as Folders com os seus respectivos tipos de JOBs, lembrando que nessa primeira etapa a integração contempla os JOBs do tipo SO (Command, Script e EmbededScript). 

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2013.jpeg" alt="Tela-control-m (13)" >
</p>

Nota : Se existir um job de tipo não compatível o fluxo inteiro não será executado no Ansible.

O nome da nova Folder deverá ter o mesmo prefixo definido no profile no CCM.

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2014.jpeg" alt="Tela-control-m (14)" >
</p>

Criando um job do tipo (aioansible)

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2015.jpeg" alt="Tela-control-m (15)" >
</p>

Selecione o novo tipo, se não estiver aparecendo na sua console, faça o logout e na sequência login novamente para a console carregar o novo template.

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2016.jpeg" alt="Tela-control-m (16)" >
</p>

Arraste e solte no centro da tela, conforme abaixo

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2017.jpeg" alt="Tela-control-m (17)" >
</p>

Preencha todos os campos requeridos, que estão sinalizados em vermelho.

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2018.jpeg" alt="Tela-control-m (18)" >
</p>

Selecione o profile, vc precisa selecionar ou preencher o nodeid caso o profile tenha sido criado em um servidor diferente do Control-M Server.

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2019.jpeg" alt="Tela-control-m (19)" >
</p>

Selecione o Datacenter desejado

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2020.jpeg" alt="Tela-control-m (20)" >
</p>

Selecione a folder desejada, na lista de seleção só aparecerá as folders que tiverem o mesmo prefixo configurado no profile.

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2021.jpeg" alt="Tela-control-m (21)" >
</p>

Conclua a configuração do JOB, ajustando o nome do JOB, Folder, Application e SubApplication.

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2023.jpeg" alt="Tela-control-m (23)" >
</p>

Ordene o JOB

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2024.jpeg" alt="Tela-control-m (24)" >
</p>

Acompanhe no Control-M Monitoring

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2025.jpeg" alt="Tela-control-m (25)" >
</p>

Acompanhe na Console do Ansible

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2026.jpeg" alt="Tela-control-m (28)" >
</p>

Acompanhe no Servidor do AIO

```sh
tail -f /aio/.pm2/logs/aio-ansible-out.log
```
## Configura aio-app

Configuração SNMP no Control-M

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Control-m/Tela-Control-m%2030.jpeg" >
</p>
```sh
Executar o comando :
ctmipc -DEST all -MSGID CFG
```
