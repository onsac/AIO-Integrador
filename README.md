<p align="center">
  <a href="https://onsac.com/">
    <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/AIO%20INTEGRADOR.png" >
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

- [Requisitos de Infraestrutura](#requisitos-de-infraestrutura)
- [Verifica e Configura PROXY](#verifica-e-configura-proxy)
- [Instala e Configura AIO Integrador](#instala-e-configura-aio-integrador)
- [Configura Ansible](#configura-ansible)
- [Configura ControlM](#configura-controlm)

## Requisitos de Infraestrutura

Para um volume de até 1.000 transações por segundo, considere a arquitetura abaixo:

| Componente | Descrição |
| --- | --- |
| SO | Centos ou Redhat |
| CPU | 4 VCPUs |
| Memória Ram | 16 GB |
| Disco | 130GB : 30GB SO + 100GB APP + DB |

## Verifica e Configura PROXY

Se sua empresa utiliza PROXY corporativo nos servidores para permitir o acesso a internet, então é pré-requisito realizar a configuração abaixo para seguir com a instalação do AIO Integrador

Deve-se editar o arquivo /etc/environment adicionando as seguintes linhas: 

```sh
http_proxy="http://USUARIO:SENHA@IP_PROXY:PORTA"
https_proxy="http://USUARIO:SENHA@IP_PROXY:PORTA"
ftp_proxy="http:///USUARIO:SENHA@IP_PROXY:PORTA"
no_proxy=localhost,127.0.0.0/8,192.168.*,10.*
```

## Instala e Configura AIO Integrador

Deploy dos componentes da solução
```sh
wget --no-cache --no-check-certificate -qO- https://raw.githubusercontent.com/onsac/aio-init/main/aio-init.sh | bash -s <Informe a Chave>
```

Pós setup
```sh
sudo su - aio
cd /aio/aiop/aio-setup
node aio-setup
```

## Configura Ansible

Verifique e instale o Python, pip e plugin

Para administrar seus servidores Windows, você precisará instalar os seguintes pacotes:

```sh
python-pip
pip
pywinrm
```

Instale os pacotes do Python.

```sh
sudo yum -y install python-pip pip
```

Instale o WinRM (Gerenciamento Remoto do Windows).

```sh
pip install pywinrm
```

Configuração dos Playbooks no <strong>Servidor Ansible Tower</strong>

```sh
mkdir /var/lib/awx/projects/aio-ansible
```
```sh
cd /var/lib/awx/projects/aio-ansible
```
```sh
scp aio@<aio.onsac.com>:/aio/aiop/aio-ansible/projects/*.yml ./
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

Linux

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(3).png" alt="Tela-ansible(3)" >
</p>

```sh
ansible_connection: ssh
ansible_python_interpreter: '{{ ansible_playbook_python }}'
```

Windows

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

Em cada host windows será necessário liberar a porta 5985 no Firewall e executar os comandos abaixo :

Um script do PowerShell está disponível para configurar automaticamente sua máquina. Este script irá configurar automaticamente o WinRM (Windows Remote Management) e abrir o firewall.

Faça o download do script do PowerShell no Github.
```sh
https://github.com/stylersnico/ansible/blob/115aaeb17c4e13d6ef8420acd7143fddbf33ea5f/examples/scripts/ConfigureRemotingForAnsible.ps1
```

Modifique as regras de execução dos scripts do PowerShell para permitir a execução do script.

Set-ExecutionPolicy RemoteSigned

Execute o script.

```sh
.\ConfigureRemotingForAnsible.ps1
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

Notifications no Ansible

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(10).jpeg" alt="Tela-ansible(10)" >
</p>

<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela%20-ansible%20(11).jpeg" alt="Tela-ansible(11)" >
</p>

 TARGET URL : `http://aio.onsac.com:8899/api/aioansible/notification`

 HTTP HEADERS :

```sh
{
  "content-type": "application/json",
  "restusername": "aiointegrador",
  "restpassword": "U2FsdGVkX18HEahvwpUCuWR29AQBe/8OTkIOVcarWuM="
}
```
<p align="center">
     <img src="https://github.com/onsac/AIO-Integrador/blob/master/Telas-Configura%C3%A7%C3%A3o/Telas-Asible/Tela-ansible%20(12).jpeg" alt="Tela-ansible(12)" >
</p>

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
- aioansible-14-02-2021.ctmai

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

Para verificar a porta da API do Control-M, entre no servidor do Control-M e execute o comando abaixo :

```sh
emweb_status
```
```sh
-------------- EmwebStatus Begin ------------------
Checking Tomcat process
Tomcat Process [UP]
===========================================================
[HTTP Connectors]
        Port: [18080], Status: [UP]
[HTTPS Connectors]
        Port: [8443], Status: [UP]
===========================================================
--> Web server [UP]
===========================================================

web server is running [ http://<em server>:18080/ ]
-------------- EmwebStatus End ------------------
```
Verifique a porta em `[HTTPS Connectors]` e se o Status está `[UP]`

Verifique no browser o acesso à pagina do Control-M Automation API na URL abaixo
```sh
https://<em server>:8443/automation-api/swagger-ui.html
```

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
Executar o comando no servidor do Control-m:
ctmipc -DEST all -MSGID CFG
Recycle o gateway para ativar a configuração

```
