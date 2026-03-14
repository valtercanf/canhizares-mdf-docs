# Canhizares MDF — Sistema de Orçamento (Móveis Planejados & Serralheria)

Bem-vindo ao sistema de orçamento **Canhizares MDF**.
Este projeto visa calcular custos, precificação e detalhamento de materiais para marcenaria (MDF) e serralheria (Metalon/Chapa).

## Status Atual
Sistema **v5.6** — completo, funcional e auditado. Backend Python modular, interface web premium e exportação Excel detalhada.

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
| Phase 14 | Preparação Local & Testes de Sistema | ✅ Concluído |
| Phase 15 | Refinamento Serralheria & Correção de Inputs | ✅ Concluído |
| Phase 16 | Pintura Eletrostática na Serralheria | ✅ Concluído |
| Phase 17 | Refinamento UI Pintura Premium | ✅ Concluído |
| Phase 18 | Ferramenta de Sincronização de Emergência | ✅ Concluído |
| Phase 19 | Migração para Supabase (Database Persistente) | ✅ Concluído |
| Phase 20 | Refatoração Modular & Agrupamento Visual | ✅ Concluído |
| Phase 21 | Estruturas Flexíveis & Engrosso de Tampo | ✅ Concluído |
| Phase 23 | Padronização de Versão (v5) | ✅ Concluído |
| Phase 24 | Hotfix Cálculo & Modo Offline (Local) | ✅ Concluído |
| **Phase 25** | **Auditoria Matemática & Detalhamento Completo** | **✅ Concluído** |

> Para o detalhamento completo de cada fase, acesse [docs/task.md](docs/task.md).

---

## Funcionalidades Implementadas
- **Cálculo de MDF:** Otimização para diferentes espessuras (3mm-25mm) e cores.
- **Módulos Flexíveis:** Box (Base+Topo separados), Open Top, Tampo, Base e Peça Avulsa.
- **Base/Topo Inteligente:** Detecção automática — se tampo existe, o topo do box é omitido.
- **Rodapé Automático:** Gerado para módulos box (50mm × perímetro).
- **Engrosso de Tampo:** Tiras de engrosso com dimensões auditadas (lateral ao longo do comprimento, frente/trás ao longo da profundidade).
- **Gavetas Decompostas:** Frente/Traseira, Laterais e Fundo como peças individuais.
- **Contra-frete Automático:** Peça estrutural gerada por gaveta (usa material da estrutura).
- **Detalhamento 100%:** TODOS os itens do cálculo aparecem na descrição: MDF, ferragens, fita, cola, parafusos, sapatas, corrediças, puxadores.
- **Automatização de Fornecedores:** Seleção manual ou automática do fornecedor.
- **Cálculo de Insumos:** Cola (área), Fita de Borda (perímetro) e Parafusos automáticos.
- **Serralheria V5.0:** Tubos, Ferro Trefilado e Chapas com **Pintura Eletrostática**.
- **Precificação Profissional:** Custo Material + Mão de Obra + Lucro = Preço de Venda.
- **Exportação Excel:** Relatório com detalhamento + custos por fornecedor.

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
│       ├── pricing.js          # Precificação e exportação
│       ├── ui.js               # DOM e componentes dinâmicos
│       ├── api.js              # Requisições HTTP
│       └── validation.js       # Validação de formulário
├── execution/
│   ├── mdf_calculator.py       # Orquestrador central de cálculo
│   └── calculators/            # Módulos especializados
│       ├── mdf_structure.py    # Estrutura (Box, Tampo, Open Top, Rodapé, Engrosso)
│       ├── drawers.py          # Gavetas (Frente/Traseira, Laterais, Contra-frete)
│       ├── doors.py            # Portas (Dobradiças, Puxadores)
│       ├── shelves.py          # Prateleiras/Divisórias
│       ├── metalwork.py        # Serralheria (Tubos, Chapas, Pintura)
│       ├── costs.py            # Custos e geração de Excel
│       └── utils.py            # Utilitários (preços, materiais)
├── data/
│   ├── prices.json             # Base de preços e materiais
│   └── history.json            # Histórico de cálculos
├── reports/                    # Planilhas de referência (SOMA MATERIAL.xlsx)
├── tests/                      # Testes automatizados
└── docs/
    ├── task.md                 # Timeline do projeto
    └── walkthrough.md          # Registro por sessão
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
*Última atualização: 14/03/2026 — Sistema V5.6 com cálculos auditados e detalhamento completo*
