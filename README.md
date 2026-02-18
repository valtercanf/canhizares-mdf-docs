# Canhizares MDF — Sistema de Orçamento (Móveis Planejados & Serralheria)

Bem-vindo ao sistema de orçamento **Canhizares MDF**.
Este projeto visa calcular custos, precificação e detalhamento de materiais para marcenaria (MDF) e serralheria (Metalon/Chapa).

## Status Atual
O sistema está **completo e funcional**, incluindo backend Python, interface web moderna e exportação avançada de Excel.

---

## 📚 Documentação do Projeto

> Toda a evolução, tarefas e decisões técnicas do projeto estão documentadas nos arquivos abaixo.
> Clique nos links para acessar diretamente.

| Documento | Descrição | Link |
|---|---|---|
| **Timeline & Tasks** | Linha do tempo completa com todas as fases (1–13), tarefas concluídas e pendentes | [📋 task.md](docs/task.md) |
| **Walkthrough** | Registro detalhado de implementações, testes e evidências por sessão | [📖 walkthrough.md](docs/walkthrough.md) |

### Resumo das Fases

| Fase | Descrição | Status |
|---|---|---|
| Phase 1 | Setup inicial e lógica de cálculo MDF | ✅ Concluído |
| Phase 2 | Interface Web (Flask + HTML) | ✅ Concluído |
| Phase 3 | Precificação dinâmica e inventário | ✅ Concluído |
| Phase 4 | Configuração avançada de componentes | ✅ Concluído |
| Phase 5 | Consumíveis (Cola, Fita, Parafusos) | ✅ Concluído |
| Phase 6 | Serralheria V3.0 (Metalon + Chapa) | ✅ Concluído |
| Phase 7 | Refinamento UI/UX | ✅ Concluído |
| Phase 8 | Engine de Precificação + Export Excel | ✅ Concluído |
| Phase 9 | Web Scraping Inteligente + Matriz de Fornecedores | ✅ Concluído |
| Phase 10 | Bugfixes, Otimizações e Refatorações | ✅ Concluído |
| Phase 11 | Histórico de Cálculos (History Viewer) | ✅ Concluído |
| Phase 12 | Testes E2E com Playwright | ✅ Concluído |
| Phase 13 | Refatoração Frontend Modular + Precificação Excel | ✅ Concluído |

> Para o detalhamento completo de cada fase, acesse [docs/task.md](docs/task.md).

---

## Funcionalidades Implementadas
- **Cálculo de MDF:** Otimização para diferentes espessuras (3mm-25mm) e cores.
- **Cálculo de Insumos:** Cola (área), Fita de Borda (perímetro) e Parafusos automáticos.
- **Serralheria V4.0:** Cálculo completo de Tubos (Metalon) e Chapas de Aço (Fria/Quente).
- **Precificação Completa:** Custo Material + Mão de Obra + Lucro = Preço de Venda.
- **Exportação Excel:** Relatório detalhado com validação obrigatória dos campos de precificação.
- **Inventory Matrix (Fev 2026):** Base de dados atualizada com preços reais de mercado.
- **Sistema de Testes:** Suíte automatizada (`run_system_tests.py`) para validação de lógica e dados.
- **Busca Inteligente:** Integração com Google Shopping para encontrar referências de preço rapidamente.
- **Detecção de Marca:** Identificação automática da marca no site para atualizar a coluna correta na matriz.
- **Histórico de Cálculos:** Salva e restaura cálculos anteriores via modal.

## Como Rodar o Projeto

### Pré-requisitos
- Python 3.10+
- Bibliotecas: `flask`, `pandas`, `openpyxl`

### Instalação
```bash
pip install -r requirements.txt
```

### Execução (Local)
1.  Navegue até a pasta do projeto.
2.  Execute o servidor:
    ```bash
    py web/app.py
    ```
3.  Acesse no navegador: `http://localhost:5000`

## Estrutura de Arquivos Importantes

```
canhizares-mdf/
├── web/
│   ├── app.py                  # Backend Flask (Rotas e API)
│   ├── templates/
│   │   └── index.html          # Interface principal (Builder)
│   └── static/js/
│       ├── main.js             # Entry point do frontend
│       ├── pricing.js          # Módulo de validação e exportação de precificação
│       ├── ui.js               # Lógica de DOM e componentes dinâmicos
│       ├── api.js              # Serviço de requisições HTTP
│       └── validation.js       # Validação de formulário
├── execution/
│   └── mdf_calculator.py       # Lógica central de cálculo e geração de Excel
├── data/
│   ├── prices.json             # Base de dados de preços e materiais
│   └── history.json            # Histórico de cálculos realizados
├── tests/                      # Scripts de teste automatizado (Playwright + Pytest)
└── docs/
    ├── task.md                 # Timeline e tarefas do projeto
    └── walkthrough.md          # Registro detalhado por sessão
```

## Para Continuar o Desenvolvimento
Se você fechar o projeto e reabrir no futuro:
1.  **Leia [docs/task.md](docs/task.md)**: Para ver o status das tarefas e a linha do tempo completa.
2.  **Leia [docs/walkthrough.md](docs/walkthrough.md)**: Para entender o que foi feito em cada sessão.
3.  **Rode o servidor**: O código é a fonte da verdade.

## Sistema de Preços Web (Matriz)

O sistema agora permite o acompanhamento de preços de múltiplos fornecedores em tempo real.

### Como usar:
1.  **Página de Estoque:** Acesse `/estoque` no menu superior.
2.  **Busca de Links 🔍:** Clique na lupa ao lado do nome de um material para abrir o Google Shopping.
3.  **Configuração:** Copie a URL do produto e cole no campo **Link Ref.**
4.  **Atualização:** Clique em **Atualizar Web**.
    *   O robô visitará os sites e extrairá o preço.
    *   **Inteligência:** Ele identificará automaticamente a marca (Arauco, Duratex, etc.) e atualizará a coluna correta.

---
*Última atualização: 2026-02-18 — Gerado por AI Assistant*
