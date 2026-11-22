# Navidrome

**Função:** streaming de música
**Tipo:** contentor LXC (não privilegiado)
**Instalação:** Proxmox VE Helper Script (Community-Scripts)
**Acesso:** exposto via Pangolin

## Porquê o Navidrome

O Navidrome é um servidor de música open source e leve, que permite
ouvir a minha biblioteca musical a partir de qualquer lugar, sem
depender de serviços de streaming. É compatível com a API Subsonic, o
que permite usar várias aplicações cliente, no telemóvel e no
computador, além da interface web.

É o equivalente em áudio do [Jellyfin](jellyfin.md), que trata do
vídeo.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 2 cores |
| RAM | 1 GB (+ 512 MB de swap) |
| Disco do contentor | 4 GB |
| Volume de dados | 1000 GB, montado em `/data` |

## Armazenamento

A música não fica no disco do contentor. Tal como o Jellyfin, o
Navidrome acede a um volume de dados de 1000 GB do Proxmox, montado
em `/data` com um *mount point*:

```
mp0: data:<volume>,mp=/data,size=1000G
```

É o mesmo volume partilhado por outros contentores do homelab, o que
mantém todos os ficheiros num só sítio.

## Configuração relevante do contentor

```
unprivileged: 1
features: keyctl=1,nesting=1
onboot: 1
```

Contentor não privilegiado, sem acesso extra a dispositivos, e com
arranque automático junto com o Proxmox.

## Acesso e segurança

- Exposto fora de casa através do **Pangolin**, com HTTPS e
  certificados geridos automaticamente.
- Acesso restrito a Portugal e Suíça (regra no Pangolin).
- Autenticação com o login do próprio Navidrome.
- Contentor não privilegiado e sem montagens de dispositivos.

## Melhorias previstas

- Montar o volume de dados como só de leitura (`ro=1`), já que o
  Navidrome só precisa de ler os ficheiros de música. Reduz o impacto
  de uma falha de segurança num serviço exposto.
