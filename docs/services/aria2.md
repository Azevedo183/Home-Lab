# Aria2

**Função:** gestor de downloads
**Tipo:** contentor LXC (não privilegiado)
**Instalação:** Proxmox VE Helper Script (Community-Scripts)
**Acesso:** apenas via VPN

## Porquê o Aria2

O Aria2 é um gestor de downloads leve e open source, que corre no
próprio servidor e funciona em segundo plano. Suporta vários
protocolos (HTTP, FTP, BitTorrent, entre outros) e é controlado através
de uma interface web, o que permite lançar e acompanhar descargas sem
deixar o computador ligado.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 2 cores |
| RAM | ~1 GB (+ 512 MB de swap) |
| Disco do contentor | 8 GB |
| Volume de dados | 1000 GB, montado em `/data` |

## Armazenamento

Os ficheiros descarregados não ficam no disco do contentor. O Aria2
usa o volume de dados de 1000 GB do Proxmox, montado em `/data` com um
*mount point*:

```
mp0: data:<volume>,mp=/data,size=1000G
```

É o mesmo volume partilhado por outros contentores do homelab. Assim,
o que é descarregado fica logo disponível para os restantes serviços,
sem ser preciso copiar ficheiros.

## Configuração relevante do contentor

```
unprivileged: 1
features: keyctl=1,nesting=1
onboot: 1
```

Contentor não privilegiado, sem acesso extra a dispositivos, e com
arranque automático junto com o Proxmox.

## Acesso e segurança

- Não está exposto à internet: só é acessível a partir da rede interna
  ou através da VPN (WireGuard).
- Contentor não privilegiado e sem montagens de dispositivos.

## Melhorias previstas

- Limitar o espaço que os downloads podem ocupar, para que não encham
  o volume partilhado e afetem os outros serviços que o usam.
