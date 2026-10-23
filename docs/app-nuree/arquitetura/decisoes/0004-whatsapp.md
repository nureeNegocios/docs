# 4. Envio por WhatsApp: porta de envio e adapter Baileys

- **Status:** aceito
- **Data:** 2026-10-22
- **Depende de:** [ADR 3](0003-estilo.md)
- **Issue:** [nureeNegocios/api#32](https://github.com/nureeNegocios/api/issues/32)

## Problema

A régua de comunicação (E9) precisa mandar mensagem por WhatsApp. **Não existe envio oficial
gratuito**: o que é grátis é o software, não o canal. Qual provider, e como trocá-lo depois sem
reescrever a régua?

## Opções consideradas

- **Cloud API da Meta** — oficial e estável; cobra por conversa, exige WABA verificada e **template
  aprovado** para iniciar conversa. O modelo de mensagem da Nuree é texto livre com variáveis: cada
  modelo viraria um template a aprovar na Meta.
- **Evolution API** — servidor pronto (REST + painel) sobre o Baileys. Mais um serviço para operar,
  com banco e credenciais próprios, para usar uma fração do que ele faz.
- **whatsapp-web.js** — dirige um Chrome headless. Pesado no worker e frágil a mudança do WhatsApp Web.
- **Baileys** (`@whiskeysockets/baileys`) — biblioteca, sem browser, mantida; é o motor da própria
  Evolution API. **Não oficial**: o número pode ser banido.

## Decisão

**Baileys, atrás de uma porta de envio.**

- O domínio fala com `WhatsAppAdapter` (`send`, `start`, `stop`, `logout`); a falha volta
  **classificada** — transitória (volta à fila com backoff) ou definitiva (número sem WhatsApp,
  telefone impossível: encerra na primeira). É dela que o retry depende.
- O adapter é escolhido por `WHATSAPP_PROVIDER`: `baileys` (padrão em produção) ou `fake` (padrão em
  desenvolvimento e teste — só registra em log e simula o pareamento; a suíte nunca fala com o
  WhatsApp).
- **Um número só, da Nuree**, para todas as empresas: uma credencial, um socket, **só no worker**
  (FF-5). A api lê o estado no banco e manda ordens (parear, cancelar, desconectar) por fila.
- A credencial multi-device mora **no Postgres** (`whatsapp_auth_keys`), e não em arquivo do
  container: em disco efêmero ela se perderia a cada deploy e o número pediria pareamento de novo.
- O Baileys é ESM e a api compila em CommonJS: entra por `import()` dinâmico, num arquivo só (o
  adapter). Fitness function garante que nada mais o importa.

## Consequências

- **Risco de ban.** Mitigação: um número dedicado, só mensagem transacional da régua (nada de
  disparo em massa), opt-out respeitado (api#34). Se o número cair, a régua fica muda e a tela de
  Conexão vira alarme em todo o console — o que estava agendado espera, dentro da janela de 2 h.
- **Custo de reverter:** um arquivo novo que implementa a porta (a Cloud API), e o
  `WHATSAPP_PROVIDER` trocado. Modelo, gatilho, disparo e situação não mudam. O que muda de verdade
  na Cloud API é o **template aprovado**: o modelo da Nuree passaria a precisar de um id de template
  da Meta — custo de produto, não de código.
- A biblioteca está em release candidate (`7.0.0-rc14`), pinada na versão exata: o protocolo do
  WhatsApp muda sem aviso, e atualizar é decisão explícita, com teste de pareamento.
