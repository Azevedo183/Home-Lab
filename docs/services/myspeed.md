# MySpeed

**Função:** testes automáticos de velocidade da ligação à internet
**Tipo:** contentor LXC (não privilegiado)
**Instalação:** Proxmox VE Helper Script (Community-Scripts)
**Acesso:** apenas via VPN

## Porquê o MySpeed

O MySpeed executa testes de velocidade de forma automática e guarda o
histórico, com gráficos. Permite acompanhar a qualidade da ligação à
internet ao longo do tempo e perceber se a velocidade contratada está
a ser cumprida, em vez de depender de testes feitos à mão.

Não deve ser confundido com o **LibreSpeed**, que também faz parte do
homelab mas mede a velocidade da **rede interna**. O MySpeed mede a
ligação **à internet**.

## Notificações

O resultado de cada teste é enviado automaticamente para o Telegram:

1. O MySpeed envia o resultado para um **webhook do n8n**.
2. O n8n encaminha-o para o meu **Telegram**.

Assim consigo acompanhar a velocidade da ligação sem abrir o
dashboard, e é um exemplo de integração entre dois serviços do
homelab.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 1 core |
| RAM | 1 GB (+ 512 MB de swap) |
| Disco do contentor | 4 GB |

É um serviço muito leve, adequado a um servidor com recursos limitados.

## Configuração relevante do contentor

```
unprivileged: 1
features: keyctl=1,nesting=1
onboot: 1
```

Contentor não privilegiado, sem acesso extra a dispositivos, e com
arranque automático junto com o Proxmox.

## Acesso e segurança

- Não está exposto à internet: a interface web só é acessível a partir
  da rede interna ou através da VPN (WireGuard).
- Contentor não privilegiado e sem montagens de dispositivos.

## Melhorias previstas

- Enviar um alerta apenas quando a velocidade descer abaixo de um
  valor mínimo, em vez de notificar todos os testes.
