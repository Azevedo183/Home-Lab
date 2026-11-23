# Pangolin

**Função:** reverse proxy com túnel e controlo de acesso (zero-trust) para os serviços públicos
**Tipo:** contentor LXC (não privilegiado) com Docker
**Instalação:** Docker Compose, dentro de um LXC criado com o script Docker (Community-Scripts)
**Acesso:** ponto de entrada exposto no router

## Porquê o Pangolin

O Pangolin é a porta de entrada dos serviços que uso fora de casa.
Em vez de abrir uma porta no router para cada serviço, só o Pangolin
fica exposto e é ele que encaminha os pedidos para o serviço certo.
Trata ainda de três coisas importantes:

- **HTTPS:** obtém e renova automaticamente os certificados.
- **Autenticação:** protege com login os serviços que não têm o seu
  próprio.
- **Regras de acesso:** bloqueia pedidos que não venham de Portugal ou
  da Suíça.

Por baixo, o Pangolin usa o Traefik como proxy e inclui um componente
de túnel baseado em WireGuard.

## Como funciona

1. O domínio aponta para o IP de casa, mantido atualizado pelo
   Cloudflare DDNS.
2. O pedido chega ao Pangolin, que verifica o acesso (país e
   autenticação).
3. Se for permitido, o Pangolin encaminha-o para o serviço de destino,
   dentro da rede interna.

Os serviços que passam por aqui estão identificados com "Pangolin" na
coluna "Exposto?" da tabela de serviços do README.

## Instalação

O contentor foi criado com o script de Docker LXC e o Pangolin corre
lá dentro com Docker Compose. O ficheiro está disponível em
[`compose/pangolin/`](../../compose/pangolin/docker-compose.yml).
A configuração do Pangolin (chaves, domínio e utilizadores) não é
publicada, por conter informação sensível.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 2 cores |
| RAM | 1 GB (+ 512 MB de swap) |
| Disco do contentor | 12 GB |

É um serviço leve para a importância que tem: todo o acesso externo
passa por ele.

## Configuração relevante do contentor

```
unprivileged: 1
features: nesting=1,keyctl=1
startup: order=1
onboot: 1
lxc.cgroup2.devices.allow: c 10:200 rwm
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
```

- O contentor é **não privilegiado**. A única permissão extra é o
  acesso ao dispositivo `/dev/net/tun`, necessário para o túnel
  WireGuard do Pangolin.
- `startup: order=1` faz com que seja um dos primeiros contentores a
  arrancar (tal como o WireGuard), para o acesso externo estar
  disponível logo após um reinício do servidor.
- Não monta o volume de dados partilhado, o que o mantém isolado do
  armazenamento dos outros serviços.
- O fuso horário está definido para `Europe/Lisbon`.

## Automatização

O Traefik é reiniciado automaticamente todos os dias às 8h por um
workflow do [n8n](n8n.md).

## Segurança

- É o único ponto de entrada dos serviços web públicos: os restantes
  serviços não têm portas abertas no router.
- Contentor não privilegiado, com apenas o acesso ao dispositivo TUN.
- Sem acesso ao volume de dados partilhado.
- Restrição geográfica e autenticação aplicadas antes de qualquer
  pedido chegar aos serviços.

## Melhorias previstas

- Incluir a configuração e a base de dados do Pangolin na futura
  estratégia de backups, para poder restaurar o acesso externo
  rapidamente em caso de falha.
- Ativar a autenticação de dois fatores na conta de administração, se
  ainda não estiver ativa.
