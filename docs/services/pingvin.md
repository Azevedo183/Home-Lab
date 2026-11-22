# Pingvin Share

**Função:** partilha de ficheiros através de ligações
**Tipo:** contentor LXC (não privilegiado) com Docker
**Instalação:** Docker Compose, dentro de um LXC criado com o script Docker (Community-Scripts)
**Acesso:** exposto via Pangolin

## Porquê o Pingvin Share

O Pingvin Share é uma plataforma de partilha de ficheiros self-hosted,
uma alternativa a serviços como o WeTransfer. Permite criar ligações de
partilha com prazo de validade e proteção por palavra-passe, sem enviar
os ficheiros para serviços de terceiros.

## Instalação

O contentor foi criado com o script de Docker LXC e o Pingvin Share
corre lá dentro com Docker Compose. O ficheiro está disponível em
[`compose/pingvin/`](../../compose/pingvin/docker-compose.yml).

Cada serviço Docker do homelab tem o seu próprio LXC, o que os mantém
isolados uns dos outros.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 2 cores |
| RAM | 2 GB (+ 512 MB de swap) |
| Disco do contentor | 8 GB |
| Volume de dados | 1000 GB, montado em `/data` |

## Armazenamento

O contentor tem acesso ao volume de dados de 1000 GB do Proxmox,
montado em `/data` com um *mount point*:

```
mp0: data:<volume>,mp=/data,size=1000G
```

É o mesmo volume partilhado por outros contentores do homelab.

## Configuração relevante do contentor

```
unprivileged: 1
features: keyctl=1,nesting=1
onboot: 1
```

O `nesting` e o `keyctl` são necessários para correr Docker dentro de
um LXC. O contentor é não privilegiado e arranca automaticamente com
o Proxmox.

## Acesso e segurança

- Exposto fora de casa através do **Pangolin**, com HTTPS e
  certificados geridos automaticamente.
- Acesso restrito a Portugal e Suíça (regra no Pangolin).
- Autenticação com o login do próprio Pingvin Share.
- Contentor não privilegiado e sem montagens de dispositivos.

## Melhorias previstas

- Limitar o tamanho máximo e a validade das partilhas, para que não
  encham o volume partilhado.
- Manter a imagem Docker atualizada, por ser um serviço exposto.
