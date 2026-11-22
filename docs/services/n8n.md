# n8n

**Função:** automatização de workflows entre os serviços do homelab
**Tipo:** contentor LXC com Docker
**Instalação:** Docker Compose
**Acesso:** exposto via Pangolin

## Porquê o n8n

O n8n é uma plataforma de automatização self-hosted com um editor
visual. Uso-o para ligar os serviços do homelab entre si e a serviços
externos, e para receber avisos no Telegram sem escrever aplicações
de raiz.

## Workflows

Os workflows enviam as notificações para um grupo do Telegram com
tópicos, usando um tópico diferente consoante o tipo de aviso. As
mensagens de rotina são enviadas em silêncio, para não incomodar.

### 1. Resultado dos testes de velocidade

```
MySpeed → webhook do n8n → mensagem no Telegram
```

Cada vez que o [MySpeed](myspeed.md) termina um teste, chama um
webhook do n8n. O n8n envia uma mensagem com o ping, o upload e o
download.

### 2. IP público e DuckDNS

```
Agendamento → IP público → mensagem no Telegram
            → atualização do DuckDNS → verificação → mensagem de sucesso ou erro
```

Um gatilho agendado faz duas coisas em paralelo:

- consulta o IP público (através de um serviço externo) e envia-o para
  o Telegram;
- atualiza o registo DuckDNS e verifica se a resposta foi `OK`.
  Conforme o resultado, envia uma mensagem de sucesso ou de erro.

### 3. Conteúdo novo no Jellyfin

```
Jellyfin → webhook do n8n → formatação da data → consulta ao OMDb → Telegram
```

Quando é adicionado conteúdo ao [Jellyfin](jellyfin.md), o plugin de
webhooks chama o n8n. Um nó de código em JavaScript trata os dados e
formata a data. Depois, uma consulta à API do OMDb (pelo identificador
IMDb) obtém o cartaz, a sinopse, os géneros, o elenco e as
classificações.

O resultado é uma mensagem no Telegram com o cartaz e os detalhes do
filme, com ligações para o TMDb, o IMDb, o Trakt e o próprio Jellyfin.

### 4. Reinício diário do Traefik

```
Agendamento (às 8h) → ligação SSH ao Pangolin → docker restart traefik
```

Todos os dias às 8h, o n8n liga-se por SSH ao contentor do Pangolin e
reinicia o Traefik, o proxy que o Pangolin usa por baixo. Se o comando
falhar, o workflow continua sem bloquear.

## Segurança

- O n8n está exposto através do Pangolin, com HTTPS e o login próprio
  do n8n.
- As credenciais do Telegram e do SSH ficam no gestor de credenciais
  do n8n, e não dentro dos nós dos workflows.
- Os workflows não são publicados no repositório sem remover antes
  tokens, chaves de API, identificadores e domínios.

## Melhorias previstas

- Mover as chaves de API e tokens que ainda estão dentro dos nós para o
  gestor de credenciais do n8n.
- Usar autenticação por chave nos acessos SSH e um utilizador com
  permissões limitadas.
- Publicar os workflows exportados, já sem segredos, na pasta
  `compose/n8n/workflows/`.
