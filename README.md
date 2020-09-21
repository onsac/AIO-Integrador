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

- [Check Prerequisites](#check prerequisites)
- [Criar Usuário](#criar usuário)
- [What's included](#whats-included)
- [Bugs and feature requests](#bugs-and-feature-requests)
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
