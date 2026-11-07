# Roku: recuperar o perfil sem VPS e sem domínio

Este é o caminho recomendado para quem usa o canal **Nuvio TV for Roku** mostrado
na captura de tela. Esse canal é um fork não oficial em beta. Para o problema
de perfis, comece pelo servidor oficial: um servidor próprio não corrige a
limitação descrita abaixo.

## A causa mais provável

A documentação do canal informa que **perfis criados no Roku não são enviados
para a conta Nuvio**. Eles ficam somente naquele aparelho.

Além disso, ao sincronizar uma conta, o canal substitui a lista local de perfis
pela lista recebida da conta. Por isso, um perfil criado pela página local do
Roku pode desaparecer depois do login ou da sincronização.

Não desinstale o canal e não apague perfis ou dados enquanto tenta recuperar o
acesso. Histórico e biblioteca que existiam somente em um perfil local podem não
ser transferidos automaticamente para um perfil novo na nuvem.

## 1. Confirme o perfil no site oficial

No celular ou computador:

1. Abra [https://nuvio.tv](https://nuvio.tv).
2. Entre na sua conta.
3. Abra o painel de gerenciamento de perfis.
4. Verifique se o perfil desejado aparece ali.

Se ele **não aparecer**, crie o perfil pelo painel oficial. Se já aparecer, não
crie uma cópia com o mesmo nome.

O perfil precisa existir na conta oficial antes de o Roku conseguir baixá-lo.
Use uma única conta para as duas TVs; cada pessoa pode ter um perfil dentro
dessa conta.

## 2. Atualize o canal nos dois Rokus

No canal, abra **Ajustes → Sobre** e anote a versão. Em 7 de novembro de 2026,
a versão mais recente do repositório do canal é **0.1.2 beta**.

Se estiver em uma versão anterior:

1. Baixe `NuvioTV-Roku-v0.1.2-beta.zip` da página oficial de releases do canal:
   <https://github.com/Esmequiinn/NuvioTVSmart-Roku/releases/tag/roku-v0.1.2>.
2. Não descompacte o ZIP.
3. Abra `http://IP-DO-ROKU` em um aparelho conectado ao mesmo Wi-Fi.
4. Entre com o usuário `rokudev` e a senha de desenvolvedor que você escolheu
   ao ativar o Developer Mode.
5. Envie o ZIP e selecione **Install with zip**.

Esse procedimento atualiza o canal; a documentação informa que perfis e addons
locais são preservados pela atualização. Não desinstale o canal para atualizar.

Para encontrar o IP, use as configurações de rede do Roku ou a tela exibida no
Developer Mode.

## 3. Volte cada Roku ao servidor oficial

Faça este procedimento separadamente nos dois aparelhos:

1. Deixe o canal Nuvio aberto no Roku.
2. Em um celular ou computador no **mesmo Wi-Fi**, abra:

   ```text
   http://IP-DO-ROKU:8888
   ```

   Substitua `IP-DO-ROKU` pelo endereço da TV que está configurando. Essa é a
   página local mostrada na captura de tela, não o endereço de um servidor na
   internet.

3. Abra a seção **Conta**.
4. Em **Servidor próprio (opcional)**, toque em **Voltar ao servidor oficial**.
5. Entre novamente com o mesmo e-mail e a mesma senha usados em `nuvio.tv`, ou
   use o QR em **Ajustes → Conta → Entrar com QR** no Roku.
6. Aguarde a mensagem informando que a conta está sincronizando.

Ao voltar ao servidor oficial, o canal encerra a sessão anterior. Portanto, é
necessário fazer login depois dessa etapa.

## 4. Force e confira a sincronização

No Roku:

1. Vá para **Ajustes → Conta → Sincronizar agora**.
2. Aguarde terminar.
3. Abra o seletor de perfis.
4. Confirme que aparece o mesmo perfil que está no painel de `nuvio.tv`.
5. Saia do canal com o botão Home e abra-o novamente para testar uma nova
   sincronização.

Repita no segundo Roku, usando:

- o servidor oficial;
- o mesmo e-mail;
- a mesma conta;
- o perfil criado ou confirmado no painel oficial.

## 5. Se a página do celular não abrir

O endereço `http://IP-DO-ROKU:8888` funciona apenas localmente:

- o canal precisa estar aberto;
- o celular/computador e o Roku precisam estar no mesmo Wi-Fi;
- desligue os dados móveis durante o teste;
- não use `https://` nesse endereço local;
- verifique se o roteador não está com isolamento de clientes ou uma rede de
  visitantes bloqueando a comunicação.

Use o endereço de cada Roku separadamente. O IP de uma TV não abrirá a página
da outra.

## 6. Se o perfil ainda não aparecer

Anote estas informações **sem enviar senha nem tokens**:

- a versão do canal em **Ajustes → Sobre**;
- o modelo e a versão do sistema do Roku;
- se o perfil aparece ou não em `nuvio.tv`;
- a mensagem exata exibida por **Sincronizar agora**;
- se o perfil foi criado originalmente pelo Roku ou pelo site/aplicativo oficial.

Com essas informações é possível distinguir entre um perfil somente local, uma
conta diferente e uma falha de sincronização do canal beta.

## Por que não usar túnel temporário?

Tecnicamente seria possível testar um backend em um computador com Docker e um
túnel HTTPS temporário. Porém, serviços como o Cloudflare Quick Tunnel mudam o
endereço ao reiniciar, não têm garantia de disponibilidade e deixam de funcionar
quando o computador ou o túnel é desligado. Isso faria as TVs perderem a conta e
exigiria configurar tudo novamente.

Para uso diário em duas TVs, as opções sensatas são:

1. **Servidor oficial do Nuvio**, sem custo de hospedagem e sem manutenção — a
   opção recomendada para este caso.
2. Servidor próprio em VPS com domínio HTTPS estável, quando houver interesse e
   disponibilidade para manter essa infraestrutura.
