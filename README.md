<p align="center">
  <img src="docs/img/banner.png" alt="InvHUB — Inventário de T.I." width="100%">
</p>

<p align="center">
  <img alt="Python" src="https://img.shields.io/badge/Python-3.11-3776AB?logo=python&logoColor=white">
  <img alt="FastAPI" src="https://img.shields.io/badge/FastAPI-009688?logo=fastapi&logoColor=white">
  <img alt="PostgreSQL" src="https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white">
  <img alt="JavaScript" src="https://img.shields.io/badge/JavaScript-sem%20framework-F7DF1E?logo=javascript&logoColor=black">
  <img alt="PWA" src="https://img.shields.io/badge/PWA-5A0FC8?logo=pwa&logoColor=white">
  <img alt="PowerShell" src="https://img.shields.io/badge/PowerShell-5391FE?logo=powershell&logoColor=white">
  <img alt="Status" src="https://img.shields.io/badge/status-em%20desenvolvimento-f59e0b">
</p>

<p align="center">
  <a href="#sobre">Sobre</a> ·
  <a href="#funcionalidades">Funcionalidades</a> ·
  <a href="#telas">Telas</a> ·
  <a href="#arquitetura">Arquitetura</a> ·
  <a href="#evolução">Evolução</a> ·
  <a href="#código-fonte">Código-fonte</a> ·
  <a href="#autor">Autor</a>
</p>

---

## Sobre

O **InvHUB** é um sistema web de inventário de T.I. que substituiu a planilha de
controle de patrimônio da empresa onde eu trabalhava.

A planilha não tinha histórico, aceitava códigos repetidos e deixava equipamentos
"esquecidos" no nome de quem já tinha saído da empresa. Além disso, só uma pessoa
conseguia editar por vez.

O InvHUB virou a fonte única de informação da T.I.: quem está com cada equipamento,
onde ele está, em que estado e tudo o que aconteceu com ele. Também coleta sozinho os
dados dos computadores e das impressoras e avisa antes de o problema aparecer.

| | |
|---|---|
| ⏱️ **Desenvolvimento** | mais de 5 semanas, 11 versões até agora |
| 🧪 **Testes automatizados** | mais de 500 |
| 🔌 **Rotas de API** | 144 |
| 🗄️ **Tabelas no banco** | 35 |

## Funcionalidades

**📦 Inventário**

- Equipamentos em uso (por colaborador), em estoque, em manutenção e consumíveis
- Ficha de cada equipamento: dono, linha do tempo, garantia, manutenções, fotos e dados do Windows
- Admissão com kit de entrega e desligamento, quando os equipamentos voltam ao estoque
- Garantia, contratos de aluguel, baixas e sugestão de quais equipamentos renovar

**💬 Chat em português, sem IA externa**

- Comandos como `atribuir 0044 para mariana` ou `quem tem o notebook 2008`
- Toda ação mostra uma prévia antes de ser aplicada. Se o nome for ambíguo, o sistema pergunta.

**🏷️ Etiquetas e leitura**

- Etiquetas com **QR Code** e/ou **código de barras Code 128**, prontas para imprimir
- Leitura pela câmera do celular (Android e iPhone, sem internet) ou por pistola USB/Bluetooth
- Conferência física do inventário: mostra o que falta e o que está fora do lugar

**🤖 Coleta automática**

- Agente em PowerShell nos computadores: hardware, Windows, antivírus, programas e monitores
- Impressoras via **SNMP**: contador de páginas e nível de toner, com cliente SNMP próprio
- Avisos de disco cheio, antivírus desligado, peça retirada de um PC e **previsão de quando o toner acaba**

**📄 Documentos e rotinas**

- Termo de responsabilidade e de devolução gerados automaticamente (PDF ou Word)
- Relatórios gerenciais, exportação para Excel e resumo semanal por e-mail
- Manutenção preventiva agendada e backups automáticos

**🔐 Acesso**

- Login com perfis (Admin, Chefe, Operador e RH) e permissões configuráveis por usuário
- Funciona no celular como aplicativo (PWA), inclusive com a rede instável

## Telas

> Todas as telas usam **dados fictícios** (empresa "ACME").

| Painel | Chat com prévia |
|---|---|
| ![Painel](docs/img/01-painel.png) | ![Chat](docs/img/03-chat.png) |
| **Ficha do equipamento** | **Equipamentos em uso** |
| ![Ficha](docs/img/04-ficha-do-equipamento.png) | ![Em uso](docs/img/02-equipamentos-em-uso.png) |
| **Previsão de toner** | **Peças e monitores** |
| ![Toner](docs/img/11-previsao-de-toner.png) | ![Peças](docs/img/12-pecas-e-monitores.png) |
| **Saúde dos computadores** | **Conferência do inventário** |
| ![Saúde](docs/img/13-saude-dos-pcs.png) | ![Conferência](docs/img/14-conferencia.png) |
| **Etiquetas com QR e código de barras** | **Tema escuro** |
| ![Etiquetas](docs/img/05-etiquetas.png) | ![Tema escuro](docs/img/09-tema-escuro.png) |

**No celular**

| Painel | Leitor de etiqueta | Ficha aberta pela leitura |
|---|---|---|
| <img src="docs/img/08-celular-painel.png" width="240"> | <img src="docs/img/06-celular-leitor.png" width="240"> | <img src="docs/img/07-celular-ficha.png" width="240"> |

<details>
<summary><b>Mais telas</b></summary>

| Relatórios | Ciclo de vida e renovação |
|---|---|
| ![Relatórios](docs/img/10-relatorios.png) | ![Ciclo de vida](docs/img/15-ciclo-de-vida.png) |
| **Resumo semanal por e-mail** | **Termo de responsabilidade gerado** |
| ![E-mail](docs/img/16-resumo-por-email.png) | ![Termo](docs/img/17-termo-gerado.png) |

</details>

## Arquitetura

```
  Navegador / celular (PWA)      Computadores (agente)      Impressoras
            │                           │                        │
            └────────── HTTPS ──────────┤                   SNMP │
                                        ▼                        │
                 ┌───────────── Servidor (Windows) ─────────────┐│
                 │  API REST (FastAPI) + controle de acesso     ◄┘
                 │  Regras de negócio · chat · coleta · termos  │
                 │  PostgreSQL + backup automático              │
                 └──────────────────────────────────────────────┘
```

| Camada | Tecnologias |
|---|---|
| Back-end | Python 3.11, FastAPI, SQL direto com psycopg 3 |
| Banco de dados | PostgreSQL |
| Front-end | HTML, CSS e JavaScript puros, PWA, tema claro e escuro, layout responsivo |
| Leitura de etiquetas | API de leitura do navegador e zxing (WebAssembly), sem internet |
| Coleta | PowerShell (WMI/CIM) e SNMP |
| Documentos | Modelos Word convertidos para PDF |
| Testes | pytest e Playwright |

## Evolução

O projeto começou como um programa de computador para uma pessoa só e virou um sistema
web para vários usuários ao mesmo tempo, inclusive no celular.

| v01 · App de mesa | v02 · Web + celular | v04 · Novo visual | v05 · Etiquetas QR |
|---|---|---|---|
| ![v01](docs/img/evo-v01-app-de-mesa.png) | ![v02](docs/img/evo-v02-pwa-celular.png) | ![v04](docs/img/evo-v04-novo-visual.png) | ![v05](docs/img/evo-v05-etiqueta-qr.png) |

O que entrou em cada versão está em **[docs/HISTORICO.md](docs/HISTORICO.md)**.

## Código-fonte

O código-fonte não está público porque o sistema foi desenvolvido para uso interno de
uma empresa. Este repositório mostra o projeto, as telas e as decisões técnicas.

Posso apresentar o código e fazer uma demonstração ao vivo em processos seletivos. Basta
entrar em contato.

## Autor

**Fabio Borel**, assistente de T.I. e estudante de Engenharia da Computação na
Universidade Veiga de Almeida (UVA).

Projeto desenvolvido com auxílio de ferramentas de IA.

Veja também o projeto **[Assinatura Local](https://github.com/fabioborelti-sys/assinatura-local)**,
uma plataforma de assinatura eletrônica de documentos pelo celular.
