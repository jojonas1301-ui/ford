# Guia completo: um servidor Nuvio e o mesmo perfil em duas TVs

Este guia usa uma conta principal do Nuvio e um backend próprio. O objetivo é
que as duas TVs vejam os mesmos perfis, addons, biblioteca e progresso.

## 1. Entenda o problema antes de mudar de servidor

Para os perfis aparecerem em mais de uma TV, estas quatro condições precisam
ser verdadeiras:

1. O Nuvio está atualizado nas duas TVs.
2. As duas usam o **mesmo endereço de backend**.
3. As duas estão conectadas ao **mesmo e-mail**.
4. O perfil já foi sincronizado por pelo menos um aparelho.

Um servidor próprio não corrige automaticamente contas diferentes e não combina
duas contas em uma só. Se cada TV usa um e-mail diferente, escolha qual será a
conta principal antes de continuar.

### Teste rápido sem migrar

Se você ainda não decidiu hospedar um backend, tente primeiro:

1. Se o perfil ainda aparecer em algum aparelho, anote o nome dele. Se não
   aparecer em nenhum, consulte o dashboard da conta oficial.
2. Em ambas as TVs, abra as configurações do Nuvio e selecione **Voltar ao
   servidor oficial**.
3. Saia da conta nas duas.
4. Entre novamente com o mesmo e-mail e a mesma senha.
5. No aparelho em que o perfil ainda aparece, abra o Nuvio e aguarde alguns
   minutos para sincronizar.
6. Reinicie o aplicativo nas TVs.

Não crie outro perfil vazio com o mesmo nome: isso não recupera o perfil
original. Se o problema continuar, a instalação própria abaixo oferece controle
sobre a conta e sobre os dados, mas exige manutenção e um servidor sempre
ligado.

## 2. Prepare a hospedagem e o domínio

### Requisitos

- Servidor Linux 64 bits (`amd64` ou `arm64`).
- Docker Engine com Docker Compose v2.
- Git, OpenSSL e curl.
- `jq`, necessário para importar a conta oficial.
- Um domínio ou subdomínio.
- Portas TCP `80` e `443` disponíveis para o Caddy incluído no projeto.

Uma VPS costuma ser mais adequada que um computador doméstico, pois precisa
ficar ligada sempre que uma TV for usar a conta. O backend não hospeda filmes
ou séries; ele guarda a conta e os dados de sincronização.

Verifique as ferramentas:

```sh
docker --version
docker compose version
git --version
openssl version
curl --version
jq --version
```

### DNS

No provedor do seu domínio, crie um registro:

```text
Tipo:  A
Nome:  nuvio
Valor: <o IPv4 público do servidor>
```

Neste exemplo, o endereço será `nuvio.seudominio.com.br`. Use um registro
`AAAA` somente se o servidor realmente tiver IPv6 configurado.

No firewall da VPS e do provedor, libere somente o necessário:

- `22/TCP` para sua administração SSH, preferencialmente restrito ao seu IP;
- `80/TCP` e `443/TCP` para o Nuvio e os certificados HTTPS.

Não exponha publicamente as portas `3000`, `8000`, `5432` ou `6543`.

> Os comandos abaixo devem ser executados no servidor Linux, na pasta que
> contém este projeto e o arquivo `nuvio`. Eles não devem ser digitados no
> campo “Servidor próprio” da TV.

## 3. Instale o backend

Substitua **todas** as ocorrências de `nuvio.seudominio.com.br` pelo domínio que
você configurou:

```sh
./nuvio setup --domain nuvio.seudominio.com.br --no-import-account
```

O setup cria senhas e chaves exclusivas no arquivo `.env`, com permissão de
leitura restrita. Não envie esse arquivo para ninguém e não o adicione ao Git.

Inicie os serviços:

```sh
./nuvio up
```

A primeira inicialização baixa os contêineres, cria o banco, aplica as
migrações e solicita o certificado HTTPS. Pode levar alguns minutos.

Verifique a instalação:

```sh
./nuvio status
./nuvio doctor
./nuvio credentials
```

O comando `doctor` precisa validar dashboard, Discovery, autenticação, banco,
REST, Storage e Functions. Não continue para a importação enquanto houver erro
nesses testes.

Você também pode testar no navegador:

```text
https://nuvio.seudominio.com.br/.well-known/nuvio
https://nuvio.seudominio.com.br/health
```

O endereço Discovery mostra configuração pública do aplicativo. Ele não deve
revelar senha do banco nem chave `service_role`.

## 4. Importe seus perfis do Nuvio Cloud

Depois que todos os testes passarem:

```sh
./nuvio import-account
```

O importador solicitará:

- o e-mail da conta oficial;
- a senha dessa conta, digitada sem aparecer no terminal;
- confirmação antes de substituir dados locais do mesmo e-mail.

Digite essas informações **somente no terminal do seu servidor**. Não mande a
senha pelo chat e não use um backend desconhecido para fazer a importação.

A importação transfere os dados disponíveis da conta, incluindo perfis, addons,
plugins, biblioteca, progresso, histórico, coleções e configurações cobertas
pelo backup oficial. Ela não importa sessões, MFA/passkeys, hashes de PIN,
tokens de trackers nem credenciais de provedores.

Ao final:

1. Abra `https://nuvio.seudominio.com.br` no navegador.
2. Entre com o mesmo e-mail e a mesma senha da conta importada.
3. Confirme que os perfis aparecem no dashboard.
4. Reconecte Trakt, Simkl, MDBList, debrid e outros provedores quando
   necessário.

Se a importação mostrar um erro, não apague o banco nem execute comandos SQL
aleatórios. Guarde a mensagem de erro sem capturar senhas ou tokens e use
`./nuvio logs` para diagnosticar.

### A importação não é uma ponte permanente

O Cloud e o self-host são backends separados. A importação copia o estado da
conta naquele instante. Alterações feitas depois no servidor próprio não voltam
automaticamente para o servidor oficial, e alterações no oficial não entram
automaticamente no próprio.

Por isso, configure **todas** as TVs no mesmo backend depois da migração.

## 5. Conecte a primeira TV

Na primeira TV (de preferência uma em que o perfil aparecia antes):

1. Abra as configurações de conta do Nuvio.
2. Localize **Servidor próprio (opcional)**.
3. Em `https://seu-servidor.com`, digite exatamente:

   ```text
   https://nuvio.seudominio.com.br
   ```

4. Em **Publishable key**, cole a chave pública impressa por:

   ```sh
   ./nuvio credentials
   ```

5. Toque em **Usar este servidor**.
6. Entre com o e-mail e a senha da conta importada.
7. Abra o seletor de perfis e confirme que o perfil esperado está lá.

Não coloque barra ou caminho depois do domínio. Não use `localhost`,
`127.0.0.1`, `http://` nem o endereço `https://api.nuvio.tv`, que é o backend
oficial.

A `Publishable key` é configuração pública do cliente. Nunca cole nos
aplicativos a `SERVICE_ROLE_KEY`, `SUPABASE_SECRET_KEY`, senha do PostgreSQL ou
senha do Studio.

## 6. Conecte a segunda TV

Repita exatamente o mesmo procedimento:

- mesmo `https://nuvio.seudominio.com.br`;
- mesma `Publishable key`;
- mesmo e-mail;
- mesma senha.

Depois de entrar, selecione o perfil desejado. Pessoas diferentes podem usar
perfis diferentes dentro da mesma conta; para ver o mesmo perfil nas duas TVs,
selecione o mesmo nome nas duas.

Para o primeiro teste, prefira e-mail e senha. Depois que a conexão estiver
funcionando, teste o login por QR se o seu aplicativo oferecer essa opção.

## 7. Faça um teste de sincronização

1. Na TV 1, adicione um título conhecido à biblioteca ou favorite um item.
2. Aguarde a sincronização e feche o Nuvio.
3. Abra o mesmo perfil na TV 2 e verifique a alteração.
4. Faça o caminho inverso com outro item.
5. Confira também o dashboard do servidor no navegador.

Algumas preferências visuais e configurações do aparelho podem ser locais. O
teste mais importante é confirmar que o perfil existe e que biblioteca ou
progresso sincronizam.

## 8. Se algo não funcionar

### O perfil aparece em uma TV, mas não na outra

- Confira o e-mail exibido em cada aparelho; e-mails parecidos ainda são contas
  diferentes.
- Compare caractere por caractere a Backend URL nas duas TVs.
- Confirme que nenhuma TV voltou ao servidor oficial.
- Feche totalmente e reabra o aplicativo.
- Atualize o Nuvio e refaça o login na TV que não sincroniza.
- Verifique no dashboard se o perfil realmente está salvo no servidor.

### A TV não aceita o endereço

- Use o domínio HTTPS completo, não `localhost` nem IP de loopback.
- Abra `https://SEU-DOMINIO/.well-known/nuvio` no celular usando dados móveis;
  se não abrir, o problema é DNS, firewall ou HTTPS, não a TV.
- Rode `./nuvio doctor`.
- Veja os erros do Caddy com `./nuvio logs caddy` e pressione `Ctrl+C` para
  sair dos logs.
- Confirme que as portas `80` e `443` chegam ao servidor correto.

### O login funciona, mas os dados estão vazios

- Confirme que `./nuvio import-account` terminou sem erro antes de conectar as
  TVs.
- Entre no dashboard do mesmo domínio e veja se os dados estão lá.
- Não tente recuperar o perfil criando outro com o mesmo nome.
- Se você entrou no servidor oficial por engano, volte ao endereço próprio.

### Preciso desfazer a mudança

No Nuvio, selecione **Voltar ao servidor oficial** e entre novamente na conta
Cloud. Os dados antigos que já estavam no Cloud continuam lá, mas mudanças
feitas somente no self-host depois da importação não serão copiadas
automaticamente.

## 9. Backups e manutenção

Faça um backup depois de validar a importação e repita regularmente:

```sh
./nuvio backup
```

Os backups ficam em `backups/` e contêm dados da conta. Copie-os para um local
seguro fora da VPS. Antes de atualizar o backend, faça outro backup.

Comandos do dia a dia:

```sh
./nuvio status          # estado dos contêineres
./nuvio doctor          # testes de saúde
./nuvio credentials     # dados públicos para os clientes
./nuvio restart         # reiniciar serviços
./nuvio down            # parar sem apagar os dados
./nuvio up              # iniciar de novo
```

Não use `docker compose down -v` nem apague `volumes/`: essas operações podem
destruir o banco e os arquivos. Mantenha o sistema operacional e o Docker
atualizados.
