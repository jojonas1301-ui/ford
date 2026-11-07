# Servidor próprio do Nuvio para usar em duas TVs

Este projeto contém o backend oficial **Nuvio Self-Host**, com um guia em
português para manter conta, perfis, biblioteca e progresso no mesmo servidor.

> **Para o caso destas duas TVs Roku, sem VPS e sem domínio:** use primeiro o
> **[guia específico para Roku](docs/roku-sem-vps.md)**. O canal de Roku é um
> fork beta não oficial e perfis criados no próprio Roku não são enviados à
> conta Nuvio. Um servidor próprio não corrige essa limitação.
>
> **Importante:** os arquivos do self-host estão prontos para instalação, mas
> isso ainda não coloca um servidor no ar. É necessário rodá-los em uma máquina
> Linux com Docker e configurar um domínio HTTPS. A tela “Servidor próprio” do
> Nuvio apenas conecta o aplicativo a um servidor que já está funcionando.

## Antes de migrar: tente corrigir o perfil que não aparece

Nas **duas TVs**:

1. Atualize o aplicativo Nuvio.
2. Em **Servidor próprio**, toque em **Voltar ao servidor oficial**.
3. Saia da conta e entre novamente com **exatamente o mesmo e-mail**.
4. Se o perfil ainda aparecer em algum aparelho, abra o Nuvio nele e aguarde a
   sincronização.
5. Feche e abra o Nuvio nas TVs. Se o perfil não aparecer em nenhum aparelho,
   consulte o dashboard da conta antes de criar outro com o mesmo nome.

Perfis pertencem a uma conta. Eles não são compartilhados entre dois e-mails
diferentes nem entre o servidor oficial e um servidor próprio.

## Se você ainda quer um servidor próprio

Siga o **[guia completo para instalar e conectar as duas TVs](docs/guia-duas-tvs.md)**.

### Você vai precisar de

- um servidor Linux 64 bits (`amd64` ou `arm64`) que possa ficar ligado;
- Docker Engine e Docker Compose v2;
- Git, OpenSSL, curl e `jq`;
- um domínio ou subdomínio, por exemplo `nuvio.seudominio.com.br`;
- portas TCP `80` e `443` liberadas;
- acesso ao e-mail e à senha da conta Nuvio que tem os perfis.

Uma VPS é a opção mais simples para uso contínuo. O modo `localhost` do projeto
serve somente para testes no próprio computador, **não para conectar as TVs**.

### Resumo da instalação

No servidor Linux, dentro da pasta deste projeto, substitua o domínio de
exemplo pelo seu e execute:

```sh
./nuvio setup --domain nuvio.seudominio.com.br --no-import-account
./nuvio up
./nuvio doctor
./nuvio credentials
```

Depois, importe a conta oficial que contém seus perfis:

```sh
./nuvio import-account
```

O comando pede a senha somente no terminal do servidor. **Não envie sua senha,
tokens ou chaves privadas pelo chat.**

### O que colocar nas duas TVs

Em **Servidor próprio (opcional)**, use os valores mostrados por
`./nuvio credentials`:

```text
Servidor/Backend URL: https://nuvio.seudominio.com.br
Publishable key:      <a chave pública gerada na instalação>
```

Toque em **Usar este servidor** e entre com o **mesmo e-mail e a mesma senha
nas duas TVs**. Não use `localhost`, `127.0.0.1` nem uma chave `service_role`.

## Avisos para não perder dados

- A importação copia os dados da conta oficial naquele momento; ela não mantém
  uma sincronização contínua entre o Nuvio Cloud e o servidor próprio.
- Depois da migração, deixe as duas TVs no mesmo servidor. Ao voltar uma delas
  ao servidor oficial, as alterações posteriores não acompanharão essa troca.
- Sessões, MFA, passkeys, PINs e credenciais de trackers/provedores não são
  importados. Será necessário reconectar esses serviços.
- Execute `./nuvio backup` regularmente e guarde os backups fora do servidor.
- Nunca publique `.env`, backups, senha do banco, `SERVICE_ROLE_KEY` ou
  `SUPABASE_SECRET_KEY`.

## Comandos úteis

| Comando | Função |
| --- | --- |
| `./nuvio status` | Ver os serviços em execução |
| `./nuvio doctor` | Testar dashboard, login, API, banco e armazenamento |
| `./nuvio credentials` | Mostrar endereço e chave pública para os aplicativos |
| `./nuvio logs caddy` | Diagnosticar HTTPS e o domínio |
| `./nuvio backup` | Fazer backup do banco e dos arquivos |
| `./nuvio down` | Parar sem apagar os dados |
| `./nuvio up` | Iniciar novamente e verificar os serviços |

## Origem e documentação

O backend é baseado no projeto oficial
[NuvioMedia/self-host](https://github.com/NuvioMedia/self-host), licenciado sob
Apache 2.0. A versão importada e as alterações locais estão registradas em
[UPSTREAM.md](UPSTREAM.md).

- **[Roku sem VPS e sem domínio — comece aqui](docs/roku-sem-vps.md)**
- [Guia em português para duas TVs com servidor próprio](docs/guia-duas-tvs.md)
- [Documentação oficial de instalação](docs/self-hosting.md)
- [Configuração dos aplicativos](docs/client-configuration.md)
- [Segurança](SECURITY.md)
