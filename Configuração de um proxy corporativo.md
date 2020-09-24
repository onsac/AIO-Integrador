# Configuração de um proxy corporativo

Quando o JHipster é usado em uma empresa, você provavelmente precisará configurar todas as ferramentas para contornar o proxy corporativo.

Você pode tentar configurar as variáveis e o ambiente ou usar uma ferramenta como `Cntlm.HTTP_PROXYHTTPS_PROXY `

Mas isso provavelmente não será suficiente, então você precisará configurar separadamente todas as ferramentas que são usadas com JHipster.

# Introdução

Supondo que seu proxy seja definido com:

- Username
- Senha
- Host
- Porta
A configuração resultante é: `http://username:password@host:port`

Se você usar Cntlm,então sua configuração seria: . Caso contrário, siga os próximos passos para configurar cada ferramenta individualmente.`127.0.0.1:3128`

# Configuração NPM

Use estes comandos:
```sh
npm config set proxy http://username:password@host:port
npm config set https-proxy http://username:password@host:port
```
Ou você pode editar diretamente seu arquivo:`~/.npmrc`
```sh
proxy=http://username:password@host:port
https-proxy=http://username:password@host:port
https_proxy=http://username:password@host:port
```

# Configuração de Fios

Use estes comandos:
```sh
yarn config set proxy http://username:password@host:port
yarn config set https-proxy http://username:password@host:port
```
# Configuração do Git

Use estes comandos:
```sh
git config --global http.proxy http://username:password@host:port
git config --global https.proxy http://username:password@host:port
```
Ou você pode editar diretamente seu arquivo:`~/.gitconfig`
```sh
[http]
        proxy = http://username:password@host:port
[https]
        proxy = http://username:password@host:port
```
# Configuração Maven

Edite a sessão em seu arquivo:`proxies` `~/.m2/settings.xml`
```sh
<proxies>
    <proxy>
        <id>id</id>
        <active>true</active>
        <protocol>http</protocol>
        <username>username</username>
        <password>password</password>
        <host>host</host>
        <port>port</port>
        <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
</proxies>
```
# Maven Wrapper

Crie um novo arquivo dentro da pasta do projeto e defina as propriedades de acordo:.`mvn/jvm.config`
```sh
-Dhttp.proxyHost=host 
-Dhttp.proxyPort=port 
-Dhttps.proxyHost=host 
-Dhttps.proxyPort=port 
-Dhttp.proxyUser=username 
-Dhttp.proxyPassword=password
```
# Configuração Gradle
Adicione o abaixo em seu arquivo e em seu arquivo se você estiver baixando o invólucro sobre um `proxygradle.properties` `gradle/wrapper/gradle-wrapper.properties`

Se você quiser definir essas propriedades globalmente, então adicione-as em `arquivoUSER_HOME/.gradle/gradle.properties`
```sh
## Proxy setup
systemProp.proxySet="true"
systemProp.http.keepAlive="true"
systemProp.http.proxyHost=host
systemProp.http.proxyPort=port
systemProp.http.proxyUser=username
systemProp.http.proxyPassword=password
systemProp.http.nonProxyHosts=local.net|some.host.com

systemProp.https.keepAlive="true"
systemProp.https.proxyHost=host
systemProp.https.proxyPort=port
systemProp.https.proxyUser=username
systemProp.https.proxyPassword=password
systemProp.https.nonProxyHosts=local.net|some.host.com
## end of proxy setup
```
# Docker

Docker nativo
Dependendo do seu SISTEMA OPERACIONAL, você tem que editar um arquivo específico ( ou ).`/etc/sysconfig/docker` `/etc/default/docker`

Então, você tem que reiniciar o serviço docker com: .`sudo service docker restart`

Não se aplicará ao sistema. Consulte esta página no docker para configurar o proxy.

Docker com docker-machine
Você pode criar sua máquina-docker com:
```sh
docker-machine create -d virtualbox \
    --engine-env HTTP_PROXY=http://username:password@host:port \
    --engine-env HTTPS_PROXY=http://username:password@host:port \
    default
```
Ou você pode editar o arquivo .`~/.docker/machine/machines/default/config.json`


