# Docker (host dos contentores e servidor Minecraft)

**Função:** host Docker para serviços em Docker Compose, incluindo o servidor de Minecraft
**Tipo:** contentor LXC (privilegiado) com Docker
**Instalação:** Proxmox VE Helper Script (Community-Scripts) + Docker Compose
**Acesso:** serviços internos via VPN; o servidor de Minecraft está exposto diretamente

## Porquê um LXC com Docker

Alguns serviços são distribuídos como imagens Docker e organizados em
ficheiros Docker Compose. Em vez de criar um contentor LXC para cada
um, concentro-os num único LXC com Docker, o que simplifica a gestão e
poupa recursos.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 8 cores |
| RAM | ~13 GB (+ 2 GB de swap) |
| Disco do contentor | 64 GB |
| Volume de dados | 1000 GB, montado em `/data` |
| Volume Docker | 500 GB, montado em `/docker` |

## Armazenamento

O contentor tem dois volumes adicionais do Proxmox, montados com
*mount points*:

- **`/data` (1000 GB):** volume de dados partilhado, o mesmo que também
  é montado noutros contentores, como o Jellyfin.
- **`/docker` (500 GB):** espaço dedicado aos ficheiros e dados dos
  serviços Docker, separado do disco do sistema.

```
mp0: data:<volume>,mp=/data,size=1000G
mp1: disks:<volume>,mp=/docker,size=500G
```

## Configuração relevante do contentor

```
unprivileged: 0
features: nesting=1,keyctl=1
startup: order=2
onboot: 1
```

- `nesting` e `keyctl` são necessários para correr Docker dentro de um
  LXC.
- `startup: order=2` faz com que arranque depois do WireGuard
  (`order=1`), mas cedo, para os serviços estarem disponíveis após um
  reinício do servidor.
- O contentor é **privilegiado**, o que simplifica o acesso aos volumes
  partilhados mas aumenta o risco de segurança (ver abaixo).

## Servidor Minecraft

Corre em Docker Compose com a imagem `itzg/minecraft-server`.
Atualmente esta com a seguinte configuração

| Item | Valor |
|---|---|
| Modpack | Project Infinity 0.1 (CurseForge) |
| Versão | Minecraft 1.20.1 com Forge |
| Instalação do modpack | Automática a partir do CurseForge |
| Memória máxima | 12 GB |
| Reinício | Automático, todos os dias às 7h |
| Dados | Pasta `mcdata`, junto ao ficheiro Compose |

Os mods que só funcionam no cliente são excluídos através da variável
`CF_EXCLUDE_MODS`, porque podem impedir o servidor de arrancar.

Os docker-compose de todas as instancias encontram-se em arquivo em [`compose/minecraft/`](../../compose/mineccraft)

## Segurança

- Só o servidor de Minecraft está exposto diretamente, através de uma
  porta aberta no router. Os restantes serviços deste contentor não
  são acessíveis a partir da internet.
- A administração do contentor (SSH, Portainer) só é possível através
  da VPN.
- O contentor é privilegiado, por isso uma falha no servidor de
  Minecraft teria mais impacto do que num contentor não privilegiado.

## Melhorias previstas

- Passar o contentor a não privilegiado, ou isolar o servidor de
  Minecraft num contentor ou VM próprio, para limitar o impacto de uma
  falha de segurança.
- Rever a memória do contentor face aos 12 GB do Minecraft, para deixar
  margem aos restantes serviços Docker.
- Incluir o mundo do Minecraft na futura estratégia de backups.
