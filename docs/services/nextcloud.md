# Nextcloud

**Função:** drive pessoal, para guardar e sincronizar ficheiros
**Tipo:** contentor LXC (privilegiado) com Docker
**Instalação:** Docker Compose, dentro de um LXC criado com o script Docker (Community-Scripts)
**Acesso:** exposto via Pangolin (quando ativo)
**Estado:** desativado de momento, por falta de recursos

## Estado atual

O contentor está desligado e não arranca automaticamente com o
Proxmox (`onboot: 0`), porque o servidor não tem recursos para o
manter ativo em simultâneo com os restantes serviços. A configuração
está completa e funcionou em Docker. Será reativado quando houver
mais capacidade, como previsto nos próximos passos do projeto.

## Porquê o Nextcloud

O Nextcloud é uma plataforma open source para guardar e sincronizar
ficheiros entre dispositivos. Permite ter uma drive pessoal no meu
próprio servidor, sem depender de serviços cloud de terceiros.

## Instalação

O contentor foi criado com o script de Docker LXC e o Nextcloud corre
lá dentro com Docker Compose. Tal como os outros serviços Docker, tem
o seu próprio LXC, o que o mantém separado dos restantes.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 6 cores |
| RAM | 6 GB (+ 6 GB de swap) |
| Disco do contentor | 32 GB |
| Volume de dados | 1000 GB, montado em `/data` |

É o contentor com mais recursos atribuídos entre os serviços expostos,
o que explica a dificuldade em mantê-lo ativo no hardware atual.

## Armazenamento

O contentor tem acesso ao volume de dados de 1000 GB do Proxmox,
montado em `/data` com um *mount point*:

```
mp0: data:<volume>,mp=/data,size=1000G
```

É o mesmo volume partilhado por outros contentores do homelab.

## Configuração relevante do contentor

```
features: nesting=1,fuse=1
onboot: 0
```

O `nesting` é necessário para correr Docker dentro de um LXC. O
contentor é **privilegiado** e tem passagem de dispositivos
(`/dev/dri`, portas série e framebuffer) configurada pelo script de
instalação.

## Acesso e segurança

Quando está ativo:

- Exposto fora de casa através do **Pangolin**, com HTTPS e
  certificados geridos automaticamente.
- Acesso restrito a Portugal e Suíça (regra no Pangolin).
- Autenticação com o login do próprio Nextcloud.

## Melhorias previstas

- Reativar o serviço quando houver mais recursos disponíveis.
- Antes de o reativar, restringir as permissões de dispositivos do
  contentor e avaliar passá-lo a não privilegiado, por guardar
  ficheiros pessoais e estar exposto à internet.
- Publicar o ficheiro Compose, sem segredos, em `compose/nextcloud/`.
- Incluir os dados e a base de dados do Nextcloud na futura estratégia
  de backups.
