# Configuração de um proxy corporativo

Quando o AIO é usado em uma empresa, você provavelmente precisará configurar todas as ferramentas para contornar o proxy corporativo.

Você pode tentar configurar as variáveis e o ambiente ou usar uma ferramenta como `Cntlm.HTTP_PROXYHTTPS_PROXY `

Mas isso provavelmente não será suficiente, então você precisará configurar separadamente todas as ferramentas que são usadas com AIO.

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



