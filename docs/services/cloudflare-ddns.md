# Cloudflare DDNS

**Função:** manter o domínio apontado para o IP público atual
**Tipo:** contentor LXC (não privilegiado)
**Instalação:** Proxmox VE Helper Script (Community-Scripts)
**Acesso:** apenas via VPN

## Porquê o Cloudflare DDNS

O IP público de casa é dinâmico: pode mudar a qualquer momento, por
exemplo quando o router reinicia. Sem este serviço, o domínio
passaria a apontar para um endereço antigo e os serviços expostos
deixariam de ser acessíveis a partir de fora.

O Cloudflare DDNS verifica periodicamente o IP público e, quando ele
muda, atualiza automaticamente o registo DNS do domínio na Cloudflare.
É a base do acesso externo: o [Pangolin](pangolin.md) depende de o
domínio estar sempre correto.

## Como funciona

1. O serviço consulta o IP público atual.
2. Compara-o com o registo DNS guardado na Cloudflare.
3. Se forem diferentes, atualiza o registo através da API da
   Cloudflare.

A autenticação com a Cloudflare é feita com um token de API, que fica
apenas dentro do contentor e nunca é publicado.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 2 cores |
| RAM | 1 GB (+ 512 MB de swap) |
| Disco do contentor | 3 GB |

É um dos contentores mais pequenos do homelab, o que faz sentido para
uma tarefa tão simples.

## Configuração relevante do contentor

```
unprivileged: 1
features: nesting=1,keyctl=1
onboot: 1
timezone: Europe/Lisbon
```

Contentor não privilegiado, sem acesso extra a dispositivos e sem
volumes partilhados, com arranque automático junto com o Proxmox.

## Acesso e segurança

- Não está exposto à internet: não tem nenhuma interface pública e só
  é gerido a partir da rede interna ou através da VPN (WireGuard).
- Contentor não privilegiado, sem montagens de dispositivos e isolado
  do volume de dados partilhado.
- O token da API fica apenas no contentor.

## Melhorias previstas

- Usar um token da Cloudflare com as permissões mínimas, apenas para
  editar o DNS do domínio em causa.
- Receber um aviso no Telegram, através do [n8n](n8n.md), quando o IP
  mudar ou a atualização falhar.
