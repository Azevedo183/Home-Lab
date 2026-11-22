# WireGuard + WGDashboard

**Função:** VPN para acesso administrativo à rede interna
**Tipo:** contentor LXC (não privilegiado)
**Instalação:** Proxmox VE Helper Script (Community-Scripts)
**Acesso:** ponto de entrada exposto no router (UDP)

## Porquê o WireGuard

A administração da infraestrutura (Proxmox, SSH, interfaces de gestão)
nunca é exposta à internet. Para a conseguir fazer a partir de fora de
casa, uso uma VPN: o WireGuard é leve, rápido e simples de configurar,
e dá acesso completo à rede interna quando é necessário.

O **WGDashboard** é a interface web que uso para gerir o WireGuard:
criar e revogar dispositivos, gerar os ficheiros de configuração e os
códigos QR para telemóvel, e ver quem está ligado.

## Como funciona

1. O dispositivo remoto liga-se ao ponto de entrada do WireGuard, que
   é a única porta UDP aberta para este fim.
2. A autenticação é feita por chaves criptográficas, uma por
   dispositivo.
3. Depois de ligado, o dispositivo fica na rede interna e pode aceder
   ao Proxmox e aos serviços que não estão expostos.
4. O Cloudflare DDNS mantém o domínio apontado para o IP atual, o que
   permite ligar por nome mesmo com IP dinâmico.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 1 core |
| RAM | 512 MB (+ 512 MB de swap) |
| Disco do contentor | 16 GB |

É um serviço muito leve, o que mostra que uma VPN completa não exige
hardware dedicado.

## Configuração relevante do contentor

O contentor é **não privilegiado** (*unprivileged*). A única permissão
extra é o acesso ao dispositivo `/dev/net/tun`, que o WireGuard
precisa para criar a interface de rede virtual:

```
unprivileged: 1
features: keyctl=1,nesting=1
startup: order=1
lxc.cgroup2.devices.allow: c 10:200 rwm
lxc.mount.entry: /dev/net/tun dev/net/tun none bind,create=file
```

- `startup: order=1` faz com que seja um dos primeiros contentores a
  arrancar, para que o acesso remoto esteja disponível logo após um
  reinício do servidor.
- O contentor arranca automaticamente com o Proxmox (`onboot`).

## Segurança

- Contentor não privilegiado, com apenas o acesso ao dispositivo TUN.
- A interface web do WGDashboard não é exposta à internet.
- Cada dispositivo tem a sua própria chave e pode ser revogado
  individualmente a partir do dashboard.
- Esta VPN é o único caminho de administração: o Proxmox, o SSH e o
  Portainer nunca são expostos diretamente.

## Melhorias previstas

- Incluir a configuração do WireGuard (chaves e dispositivos) na
  futura estratégia de backups, para não ter de recriar cada
  dispositivo em caso de falha.
- Limitar o acesso de cada dispositivo ao estritamente necessário, em
  vez de acesso total à rede interna.