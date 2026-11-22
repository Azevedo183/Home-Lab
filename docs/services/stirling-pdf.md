# Stirling PDF

**Função:** ferramentas de edição e conversão de PDF
**Tipo:** contentor LXC (não privilegiado)
**Instalação:** Proxmox VE Helper Script (Community-Scripts)
**Acesso:** exposto via Pangolin

## Porquê o Stirling PDF

O Stirling PDF é uma aplicação web open source com muitas ferramentas
para PDF: juntar, dividir, converter, comprimir, rodar páginas, entre
outras. Ao correr no meu servidor, os ficheiros são processados
localmente, sem serem enviados para sites de terceiros, o que é
importante quando os documentos contêm informação pessoal.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 4 cores |
| RAM | 4 GB (+ 512 MB de swap) |
| Disco do contentor | 8 GB |

É dos contentores com mais memória atribuída entre os serviços leves.
O Stirling PDF é uma aplicação Java, que costuma precisar de mais
memória do que serviços escritos noutras linguagens.

## Configuração relevante do contentor

```
unprivileged: 1
features: keyctl=1,nesting=1
onboot: 1
```

Contentor não privilegiado, sem acesso extra a dispositivos e sem
volumes partilhados, com arranque automático junto com o Proxmox.

## Acesso e segurança

- Exposto fora de casa através do **Pangolin**, com HTTPS e
  certificados geridos automaticamente.
- Acesso restrito a Portugal e Suíça (regra no Pangolin).
- Acesso protegido por autenticação (login do Pangolin).
- Contentor não privilegiado, isolado do volume de dados partilhado.

## Melhorias previstas

- Manter o serviço atualizado, por ser exposto à internet e processar
  ficheiros enviados pelos utilizadores.
