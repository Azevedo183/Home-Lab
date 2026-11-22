# Jellyfin

**Função:** streaming de vídeo (filmes e séries)
**Tipo:** contentor LXC
**Instalação:** Proxmox VE Helper Script (Community-Scripts)
**Acesso:** exposto via Pangolin

## Porquê o Jellyfin

O Jellyfin é open source, não exige subscrição nem contas externas, o
que permite manter a biblioteca de media totalmente sob o meu controlo.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 6 cores |
| RAM | 2 GB (+ 2 GB de swap) |
| Disco do contentor | 64 GB |
| Volume de dados | 1000 GB, montado em `/data` |

## Armazenamento dos media

Os ficheiros de vídeo não ficam no disco do contentor. O acesso é feito
através de um volume de dados de 1000 GB do storage `data` do Proxmox,
montado dentro do contentor em `/data` com um mount point (`mp0`):

```
mp0: data:<volume>,mp=/data,size=1000G
```

Assim, os dados ficam separados do sistema do contentor e podem
sobreviver a uma reinstalação do Jellyfin.

## Transcodificação por hardware

O Jellyfin usa aceleração por hardware, com a GPU integrada do
processador passada ao contentor através de `/dev/dri`. Excerto
relevante da configuração do LXC:

```
lxc.cgroup2.devices.allow: c 226:0 rwm
lxc.cgroup2.devices.allow: c 226:128 rwm
lxc.mount.entry: /dev/dri dev/dri none bind,optional,create=dir
lxc.mount.entry: /dev/dri/renderD128 dev/dri/renderD128 none bind,optional,create=file
```

A aceleração é depois ativada nas definições de reprodução do próprio
Jellyfin. Isto permite transcodificar vídeo sem sobrecarregar o
processador, importante num servidor com apenas 2 GB de RAM atribuídos
a este serviço.

## Acesso e segurança

- Exposto fora de casa através do **Pangolin**, com HTTPS e
  certificados geridos automaticamente.
- Acesso restrito a Portugal e Suíça (regra no Pangolin).
- Autenticação com o login do próprio Jellyfin.

## Melhorias previstas

- **Televisão em direto:** ligar um sintonizador USB de TDT (televisão
  digital terrestre) ao servidor e integrá-lo no Jellyfin, para ver
  canais em direto na mesma interface da biblioteca de media.
- **Backups da configuração:** incluir a configuração do Jellyfin
  (utilizadores e metadados) na futura estratégia de backups, para
  poder restaurar o serviço sem o configurar de raiz.