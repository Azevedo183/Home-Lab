# Immich

**Função:** gestão e backup de fotos e vídeos
**Tipo:** contentor LXC (privilegiado)
**Instalação:** Proxmox VE Helper Script (Community-Scripts)
**Acesso:** exposto via Pangolin (quando ativo)
**Estado:** desativado de momento, por falta de recursos

## Estado atual

O contentor está desligado e não arranca automaticamente com o
Proxmox (`onboot: 0`), porque o servidor não tem recursos para o
manter ativo em simultâneo com os restantes serviços. Será reativado
quando houver mais capacidade, como previsto nos próximos passos do
projeto.

## Porquê o Immich

O Immich é uma plataforma open source para guardar, organizar e
pesquisar fotos e vídeos, com aplicação móvel e cópia automática do
telemóvel. É uma alternativa self-hosted a serviços como o Google
Fotos, que mantém as minhas fotografias no meu próprio servidor.

## Recursos

| Recurso | Valor |
|---|---|
| CPU | 8 cores |
| RAM | 7 GB (+ 4 GB de swap) |
| Disco do contentor | 20 GB |
| Volume de dados | 1000 GB, montado em `/data` |

É o contentor com mais recursos atribuídos de todo o homelab. O Immich
usa aprendizagem automática (por exemplo, para reconhecer pessoas e
pesquisar por conteúdo) e processa vídeo, o que exige muito
processador e memória. É a principal razão de estar desativado.

## Armazenamento

O contentor tem acesso ao volume de dados de 1000 GB do Proxmox,
montado em `/data` com um *mount point*:

```
mp0: data:<volume>,mp=/data,size=1000G
```

É o mesmo volume partilhado por outros contentores do homelab.

## Configuração relevante do contentor

```
features: nesting=1
onboot: 0
```

O contentor é **privilegiado** e tem passagem de dispositivos
(`/dev/dri`, portas série e framebuffer) configurada pelo script de
instalação. O acesso ao `/dev/dri` permite usar a placa gráfica
integrada para acelerar tarefas.

## Acesso e segurança

Quando está ativo:

- Exposto fora de casa através do **Pangolin**, com HTTPS e
  certificados geridos automaticamente.
- Acesso restrito a Portugal e Suíça (regra no Pangolin).
- Autenticação com o login do próprio Immich.

## Melhorias previstas

- Reativar o serviço quando houver mais recursos disponíveis.
- Antes de o reativar, restringir as permissões de dispositivos do
  contentor e avaliar passá-lo a não privilegiado, por guardar
  fotografias pessoais e estar exposto à internet.
- Incluir a biblioteca de fotos e a base de dados do Immich na futura
  estratégia de backups, para que o servidor não seja a única cópia.
