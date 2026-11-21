# Homelab

Servidor Proxmox VE de nó único que aloja serviços auto-alojados,
maioritariamente em contentores LXC, com acesso remoto via WireGuard
e Pangolin e armazenamento em ZFS.

## Objetivos

- Aprender virtualização, redes e gestão de infraestrutura na prática
- Substituir serviços cloud por alternativas self-hosted
- Documentar e reproduzir a infraestrutura

## Arquitetura
![Arquitetura do homelab](diagrams/arquitetura.png)

## Hardware
| Componente | Especificação |
|---|---|
| CPU | Intel Core i5-8250U |
| RAM | ~20 GB |
| Boot | SSD 238 GiB |
| Armazenamento | ZFS: 931 GiB + 465 GiB |

## Serviços

| Serviço | Função | Tipo | Instalação | Exposto? |
|---|---|---|---|---|
| Vault (Ubuntu + Cockpit) | Armazenamento partilhado e SMB | LXC | Manual | VPN |
| WireGuard + WGDashboard | Acesso VPN à rede interna | LXC | Community-Scripts | VPN |
| Jellyfin | Streaming de video | LXC | Community-Scripts | Pangolin |
| MySpeed | Teste de velocidade automático | LXC | Community-Scripts | VPN |
| Docker | Servidor Minecraft | LXC | Community-Scripts | VPN |
| Librespeed | Teste de velocidade interno | LXC | Community-Scripts | VPN |
| Navidrome | Streaming de audio | LXC | Community-Scripts | Pangolin |
| Aria2 | Gestor de Downloads | LXC | Community-Scripts | VPN |
| Pingvin | Partilha de Ficheiros | LXC + Docker | Docker Compose | Pangolin |
| Stirling PDF | Ferramenta de PDF | LXC | Community-Scripts | Pangolin |
| Nextcloud | Drive Pessoal | LXC + Docker | Docker Compose | Pangolin |
| N8N | Automatização por flow | LXC + Docker | Docker Compose | Pangolin |
| Immich | Backup de fotos e videos | LXC | Community-Scripts | Pangolin |
| Pangolin | Proxy Zero-Trust | LXC + Docker | Docker Compose | Direto |
| Cloudflare DDNS | DDNS | LXC | Community-Scripts | VPN |

## Rede e acesso remoto

O acesso divide-se em dois caminhos, consoante o objetivo:

**Serviços públicos (Pangolin)**
Os serviços que uso fora de casa, como Jellyfin, Navidrome e Nextcloud,
são expostos através do Pangolin, que funciona como reverse proxy com
túnel e gere automaticamente os certificados HTTPS. O Cloudflare DDNS
mantém o domínio apontado para o IP atual, já que o IP doméstico é
dinâmico.

**Acesso administrativo (WireGuard)**
Para manutenção uso o WireGuard, que dá acesso completo à rede interna.
A gestão da infraestrutura nunca passa pela internet pública.

| Tipo de acesso | Ferramenta | Para quê |
|---|---|---|
| Público | Pangolin + Cloudflare DDNS | Serviços de utilização diária |
| Privado | WireGuard | Administração e manutenção |
| Público (direto) | Porta dedicada | Servidor de Minecraft |

## Segurança

### Princípios
- **Superfície de exposição mínima:** só o necessário está acessível
  a partir da internet.
- **Administração só por VPN:** Proxmox, SSH e Portainer nunca são
  expostos.
- **Autenticação em camadas:** os serviços com login próprio usam-no;
  os restantes ficam protegidos pelo login do Pangolin.
- **Restrição geográfica:** o Pangolin bloqueia acessos de fora de
  Portugal e da Suíça.
- **HTTPS em tudo o que é público**, com certificados geridos
  automaticamente.

### Superfície exposta
| Componente | Exposto? | Proteção |
|---|---|---|
| Serviços web (Jellyfin, Nextcloud, ...) | Sim | HTTPS, login, filtro geográfico |
| WireGuard | Sim (UDP) | Autenticação por chaves |
| Servidor Minecraft | Sim | ... |
| Proxmox, SSH, Portainer | Não | Apenas via VPN |

## Backups
...

## Lições aprendidas
...

## Próximos passos
- [ ] ...