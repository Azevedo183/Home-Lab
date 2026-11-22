# LibreSpeed

**Função:** teste de velocidade da rede interna
**Tipo:** contentor LXC (não privilegiado)
**Instalação:** Proxmox VE Helper Script (Community-Scripts)
**Acesso:** apenas via VPN

## Porquê o LibreSpeed

O LibreSpeed é um teste de velocidade open source que corre no próprio
homelab. Como o servidor de testes está dentro da rede, mede a
velocidade entre o meu dispositivo e o servidor **sem passar pela
internet**. Isto permite avaliar a rede local, por exemplo a ligação
Wi-Fi ou a cablagem, sem a influência do operador.

Não deve ser confundido com o [MySpeed](myspeed.md), que mede a
ligação **à internet**. Os dois complementam-se: um mostra o
desempenho da rede interna, o outro o da ligação externa.

Esta instalação usa a versão com servidor em Rust (`librespeed-rust`),
que é leve.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 1 core |
| RAM | 512 MB (+ 512 MB de swap) |
| Disco do contentor | 4 GB |

É um dos serviços mais leves do homelab.

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
