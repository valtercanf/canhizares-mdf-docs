# Canhizares MDF — Project Timeline & Tasks

## [PHASE 1] INITIAL SETUP & CORE LOGIC
- [x] Verified Python environment (py command)
- [x] Implemented Core MDF Calculation Logic (mdf_calculator.py)
- [x] Created Basic Data Structure for Projects
- [x] Implemented Total Budget Display

## [PHASE 2] WEB INTERFACE FOUNDATION
- [x] Created Flask Backend (app.py)
- [x] Created Frontend HTML (index.html)
- [x] Implemented Modern Dark Slate Theme (style.css)
- [x] Connected Frontend to Backend Logic

## [PHASE 3] DYNAMIC PRICING & INVENTORY
- [x] Created prices.json Database
- [x] Created Inventory Page (inventory.html)
- [x] Implemented Dynamic Price Lookup
- [x] Updated Logic to Support Multiple Thicknesses (3mm - 25mm)
- [x] Research & Integrated Average Market Prices

## [PHASE 4] ADVANCED COMPONENT CONFIGURATION
- [x] Implemented Detailed Drawer Configuration (Front, Body, Bottom)
- [x] Implemented Shelf Configuration
- [x] Refactored Calculator to Material-ID based aggregation

## [PHASE 5] CONSUMABLES & DETAILS
- [x] Implemented Glue Calculation (150g/m²)
- [x] Implemented Edge Banding Calculation (Perimeter Match)
- [x] Added Consumables to Database
- [x] Enhanced Parts List to show Glue & Tape

## [PHASE 6] SERRALHERIA (METALWORK) - V3.0
- [x] Added Metalon (Tube) Support (Shape, Size, Wall)
- [x] Added Steel Sheet Support (Type, Thickness)
- [x] Split Input Form (Marcenaria vs Serralheria)
- [x] Updated Inventory Filter

## [PHASE 7] UI/UX REFINEMENT
- [x] Switch to Compact 3-Column Form Layout
- [x] Implemented Accordion/Toggle Sections
- [x] Fixed "Calculando..." Button State
- [x] Expanded Color Options (Branco, Preto, Amadeirado, Colorido)

## [PHASE 8] PRICING ENGINE & EXPORT
- [x] Renamed "Total" to "Cost Price"
- [x] Added Metalwork Statistics (Meters/Area)
- [x] Implemented Labor & Profit Calculation
- [x] Designed "Organic" Dashboard for Results
- [x] Implemented Robust Excel Export (/api/export_excel)
    - Includes: Material Subtotal, Labor, Profit, Final Price
    - Professional Formatting (Currency, Widths)

## [PHASE 9] INTELLIGENT WEB SCRAPING & MATRIX VIEW
- [x] Migrated Inventory to Full Supplier Matrix View (Arauco, Duratex, etc.)
- [x] Integrated Real-time Scraper (BeautifulSoup4 + Requests)
- [x] Implemented Intelligent Brand Detection
    - Scraper now parses page titles/meta to automatically update the correct supplier column
- [x] Added Google Shopping Search Button for easy price discovery
- [x] Implemented Spinner/Loading state for Web Updates
- [x] Created Discovery Script (inject_links.py) for initial URL population

## [PHASE 10] RECENT UPDATES (Contexto Atual)

- [x] Analyze current implementation <!-- id: 0 -->
    - [x] Review `web/app.py` <!-- id: 1 -->
    - [x] Review `web/templates/index.html` (or relevant template) <!-- id: 2 -->
    - [x] **Testes de Sistema** (Atual)
    - [x] Criar plano de testes (`TEST_PLAN.md`)
    - [x] Implementar script de teste unificado (`run_system_tests.py`)
    - [x] Testar cálculo de MDF
    - [x] Testar cálculo de fita de borda
    - [x] Testar cálculo de ferragens
    - [x] Testar integridade do `prices.json`
- [x] **Documentação**
    - [x] Atualizar `README.md` com novas features
    - [x] Atualizar `walkthrough.md` com resultados dos testes

- [x] **Bugfix: Frontend Broken**
    - [x] Investigar rota `/api/calculate` em `web/app.py`
    - [x] Verificar compatibilidade com retorno do `mdf_calculator.py`
    - [x] Testar interface visualmente (Cache Buster aplicado)

- [x] **Debug: Vercel Deployment**
    - [x] Verificar `vercel.json` (Rotas e Builds) -> Atualizado para rota explícita
    - [x] Verificar caminhos de arquivos (`data/prices.json`) -> Alerta sobre FileSystem Read-Only
    - [x] Verificar logs de erro (se possível/simular) -> Rota estática corrigida

- [x] **Bugfix: Vercel Timeout/IO Error**
    - [x] Modificar `mdf_calculator.py` para usar `/tmp` exportação Excel (Vercel Read-Only)
    - [x] Atualizar `app.py` para servir arquivos de `/tmp`

- [x] **Otimização: Vercel Cloud Native**
    - [x] Atualizar `logger.py` para usar `sys.stdout` (Logs estruturados no console)
    - [x] Remover tentativas de escrita em disco em produção
    - [x] **Bugfix: Vercel Timeout/IO Error**
    - [x] Otimizar `mdf_calculator.py` com `skip_excel=True` (Evitar IO em requests de cálculo)
    - [x] Atualizar Frontend (`script.js`) para suportar fluxo de download separado
    - [x] Desacoplar botão "Baixar Excel" da resposta da API de cálculo
    - [x] Versionamento de cache `v=2.4`

- [x] **Bugfix: Missing Data in API**
    - [x] Corrigir `app.py` para incluir `doors` no dicionário `components` (Bug Crítico encontrado)
    - [x] Validar sanitização de inputs no `app.py` (Script JS envia Tipos Corretos)
    - [x] Criar teste de reprodução (Validado via Análise Estática)

- [x] **Frontend: Fix Inputs & UX**
    - [x] **Bugfix:** Adicionar coleta de `doors` no `script.js` (Submit Handler) - *Causa Raiz encontrada*
    - [x] **UX:** Implementar Modal de Confirmação antes do cálculo ("Você preencheu tudo?")
    - [x] **Bugfix:** Corrigir CSS/Z-Index do Modal (Botões não clicáveis) - *Movido para body*
    - [x] **Safety:** Adicionar Timeout no `fetch` para evitar "loading infinito" visual
    - [x] **Bugfix:** Corrigir CSS/Z-Index do Modal (Botões não clicáveis) - *Movido para body*
    - [x] **Safety:** Adicionar Timeout no `fetch` para evitar "loading infinito" visual
    - [x] Versionamento de cache `v=2.6`
    - [x] **Revert:** Remover Modal de Confirmação (Solicitação Usuário) - *Bugfixes mantidos*

- [x] **Restoration: Local/Desktop Mode**
    - [x] **Action:** Reverter código para commit `0ac8059` (Pré-Vercel)
    - [x] **Bugfix:** Manter correção de `KeyError: product`
    - [x] **Status:** Código restaurado para versão estável local
    - [x] **Bugfix:** Corrigir "Calculando..." infinito ao adicionar gavetas (Erro JS silencioso)







- [x] Design changes <!-- id: 4 -->
    - [x] Create implementation plan <!-- id: 5 -->
- [x] **V8: UI Refactor & Handles Preparation** <!-- id: 8 -->
    - [x] Modularize "Drawers" and "Shelves" in `index.html` (Card Design) <!-- id: 9 -->
    - [x] Add "Handle" toggle and options to Drawer UI <!-- id: 10 -->
    - [x] Update `script.js` to collect handle data <!-- id: 11 -->
    - [x] Refinar UI do Estoque
    - [x] Corrigir bugs nos filtros ("Todas", "Marcenaria", "Miscelânia", "Serralheria")
    - [x] Mover lógica JS para escopo global para evitar erros
    - [x] Adicionar categoria "Serralheria"
    - [x] Atualizar badges de contagem
- [x] Pesquisa de Preços (Fev 2026)
    - [x] Pesquisar preços de MDF, Fitas, Ferragens e Serralheria
    - [x] Atualizar `data/prices.json` com valores de mercado
- [x] **V9: Backend Pricing Logic** <!-- id: 13 -->
    - [x] Implement Handle Pricing in `mdf_calculator.py` <!-- id: 14 -->
    - [x] Update `prices.json` or Script with Handle Costs <!-- id: 15 -->
    - [x] Verify Calculation with Handle costs <!-- id: 16 -->
- [x] **V10: Screw Calculation Logic** <!-- id: 35 -->
    - [x] Update `prices.json` with Screw prices <!-- id: 36 -->
    - [x] Implement Screw Calculation in `mdf_calculator.py` <!-- id: 37 -->
    - [x] Verify Screw Calculation <!-- id: 38 -->
- [x] **V11: Verification** <!-- id: 39 -->
    - [x] Check Output in Web Interface <!-- id: 40 -->
    - [x] Verify Dynamic Pricing (Code Test) <!-- id: 45 -->
- [x] **V12: Leveling Feet Logic** <!-- id: 41 -->
    - [x] Add "Sapata Niveladora" to `prices.json` <!-- id: 42 -->
    - [x] Implement logic in `mdf_calculator.py` (Min 4, +2/90cm) <!-- id: 43 -->
    - [x] Verify calculation <!-- id: 44 -->
- [x] **V13: Door Module (Portas)** <!-- id: 46 -->
    - [x] Add "Dobradiça" to `prices.json` <!-- id: 47 -->
    - [x] Create Door UI Section (HTML/JS) <!-- id: 48 -->
    - [x] Implement Backend Logic (Doors + Hinges + Screws) <!-- id: 49 -->
    - [x] Verify Door/Hinge Calculation <!-- id: 50 -->
    - [x] Fix UI- [x] Nest Doors module within Marcenaria section
- [x] Standardize Estrutura Metálica card style
- [x] Fix extra closing div tags (HTML balancing)
- [x] Fix drawer remove button (add missing `removeDrawerRow` function)
- [x] Standardize Gavetas/Portas/Prateleiras UI design
  - [x] Drawer template: flex layout, circular remove button, rgba backgrounds
  - [x] Shelves section: matching container style, divider, font sizes
  - [x] Auto-create default row on toggle for Doors (like Drawers)id: 16 -->
    - [x] Replace "Add MDF" button with minimalist "+" icon <!-- id: 17 -->
    - [x] Center align the add button <!-- id: 18 -->
- [x] Refactor for Per-MDF Dimensions (V4.1) <!-- id: 19 -->
    - [x] Update UI: Move H/W/D inputs into MDF Row <!-- id: 20 -->
    - [x] Update JS: Collect H/W/D per row <!-- id: 21 -->
    - [x] Update Backend: Logic to calculate parts for EACH module row <!-- id: 22 -->
    - [x] Update Backend: Aggregation of materials across modules <!-- id: 23 -->
    - [x] Verify: Test with multiple modules of different sizes <!-- id: 24 -->


- [x] Refine MDF Color UI (Compact Mode) <!-- id: 25 -->
    - [x] Update `index.html`: Change radio group to grid layout <!-- id: 26 -->
    - [x] Update `style.css`: Add grid styles for radio group <!-- id: 27 -->
- [x] Separate Metalwork section from Woodwork section <!-- id: 28 -->
    - [x] Update `index.html`: Move Metalwork div outside of Woodwork div <!-- id: 29 -->
- [x] Update Drawer Module Logic <!-- id: 30 -->
    - [x] Update `index.html`: Add Width input and Hardware select <!-- id: 31 -->
    - [x] Update `script.js`: Collect new Drawer inputs <!-- id: 32 -->
    - [x] Update `mdf_calculator.py`: Implement new width and hardware logic <!-- id: 33 -->
    - [x] Verify: Test with new drawer parameters <!-- id: 34 -->
- [x] **Fix: Separate & Enable Drawers/Shelves/Doors**
    - [x] **Frontend:** Implementar coleta de dados de **Portas** (`script.js`)
    - [x] **Frontend:** Adicionar `doors` ao payload JSON (`script.js`)
    - [x] **Backend:** Mapear `doors` no `app.py` para o calculador
    - [x] **Verify:** Criar teste de integração com Gavetas, Prateleiras e Portas
    - [x] **Verify:** Validar se o cálculo de preço muda com a adição desses itens
    - [x] **Bugfix:** Corrigir `ReferenceError: drawerContainer` no `script.js`
    - [x] **Bugfix:** Corrigir captura de dados de Prateleiras (Inputs sem atributo `name`)
    - [x] **Bugfix:** Corrigir cálculo de Portas (Falta de `mat_id` no backend causava erro)
    - [x] **UX:** Alterar quantidade padrão de Portas para **2**
    - [x] **UX:** Alterar input de Prateleiras para **Altura e Largura** (Substituindo Profundidade)
    - [x] **Bugfix:** Implementar `safeGetValue` no `script.js` para evitar erro de elemento nulo (`Cannot read properties of null`)
    - [x] **Bugfix:** Implementar `safeGetValue` no `script.js` para evitar erro de elemento nulo (`Cannot read properties of null`)

- [x] **Refactor: Modularize Backend Logic** (Solicitado pelo usuário)
    - [x] **Goal:** Fragmentar `calculate_mdf_usage` em funções menores para garantir isolamento e correção.
    - [x] **Functions:**
        - `calculate_modules(modules, colors, thickness)`
        - `calculate_drawers(components, primary_module)`
        - `calculate_shelves(components, primary_module)`
        - `calculate_doors(components, primary_module)`
        - `calculate_metalwork(components)`
    - [x] **Integration:** Reconstruir `calculate_mdf_usage` para chamar essas funções e agregar corretamente `parts`, `hardware` e `materials`.
    - [x] **Verify:** Reutilizar `debug_doors.py` e `debug_shelves.py` (recriar) para validar o novo fluxo.

- [x] **Bugfix:** Portas não calculando / Valor padrão incorreto.
    - [x] **Cause:** Cache do navegador mantendo versão antiga do `script.js` e `index.html`.
    - [x] **Fix:** Atualizado `index.html` para carregar `script.js?v=3.3`.
    - [x] **Hardening:** Implementado *fallback* no backend para `primary_mod` caso a lista de módulos venha vazia.
    - [x] **UI:** Forçado valor padrão `2` via JavaScript (`script.js`) na criação da linha de porta.

- [ ] **Feature:** Exibir Valor (R$) no detalhamento de peças. (REVERTIDO A PEDIDO DO USUÁRIO)
    - [x] **Backend:** Implementado cálculo de custo por peça (Proporcional à área ou valor unitário). (REMOVIDO)
    - [x] **Frontend:** Adicionada coluna "Valor (R$)" na tabela de resultados. (REMOVIDO)
    - [x] **Bugfix:** Valores zerados corrigidos. (REMOVIDO JUNTO DA FEATURE)

- [x] **Feature:** Gerenciamento de Puxadores no Estoque.
    - [x] **DB:** Migrar `prices.json` para incluir Puxadores (Alça, Botão, Perfil, Cava) com variações de cor.
    - [x] **Frontend:** Adicionar categoria "Puxadores" na página de Estoque (`inventory.html`).
    - [x] **Backend:** Atualizar `mdf_calculator.py` para buscar preços de puxadores do banco de dados (substituir valores hardcoded).

- [x] **Feature:** Múltiplas Prateleiras/Divisórias. (Em: 2026-02-18 14:06)
    - [x] **Frontend:** Converter inputs de prateleira para lista dinâmica (Adicionar/Remover linhas).
    - [x] **Backend:** Atualizar `calculate_shelves` para processar lista de itens.

- [ ] **Audit:** Verificação Completa da Aplicação.
    - [x] **Mapeamento:** Listar todas as opções de materiais, espessuras e ferragens do Frontend (`index.html`).
    - [x] **Verificação Cruzada:** Checar se todos os itens mapeados existem em `prices.json`.
    - [x] **Teste de Stress:** Executar script que simula cálculos com todas as combinações possíveis.
- [x] **Improvement:** Backend Refactoring.
    - [x] **Standardize:** Alterar `calculate_mdf_usage` para retornar apenas um dicionário estruturado (Já estava feito).
    - [x] **Cleanup:** Remover arquivos temporários de auditoria.

- [x] **Improvement:** Frontend Validation.
    - [x] **JavaScript:** Adicionar verificação antes do submit para alertar campos com valor 0 ou vazios.
    - [x] **UX:** Destacar campos inválidos com borda vermelha.

- [x] **Improvement:** Testes.
    - [x] **Official Script:** Tornar `stress_test.py` um script de teste permanente (`tests/stress_test.py`).

    - [x] **Sanitize:** Remover menções desnecessárias.

- [x] **Feature:** Calculation History Log. (Em: 2026-02-18 14:32)
    - [x] **Design:** Definir estrutura do JSON (`data/history.json`).
    - [x] **Backend:** Implementar salvamento automático após cada cálculo em `web/app.py`.
- [x] **Cleanup:** Remove old/unused files. (Em: 2026-02-18 14:32)
- [x] **Full Audit:** Reiniciar e testar tudo (Solicitado pelo usuário). (Em: 2026-02-18 14:50)
    - [x] **Review:** Verificar cobertura dos testes atuais.
    - [x] **Execute:** Rodar `run_system_tests.py`, `stress_test.py` e `test_history.py`.
    - [x] **Report:** Identificar melhorias e problemas (Ver `walkthrough.md`).

- [x] **Improvement:** Backend Optimization (Redundancy). (Em: 2026-02-18 14:50)
    - [x] **Refactor:** Simplificar `calculate_project_costs` em `mdf_calculator.py` para usar dados pré-calculados.
    
- [x] **Improvement:** Frontend Modernization (Currency). (Em: 2026-02-18 14:50)
    - [x] **Refactor:** Substituir formatação manual por `Intl.NumberFormat` no `script.js` (Já implementado).

## [PHASE 11] HISTORY VIEWER (Done)
- [x] **Backend:** Implementar endpoint `GET /api/history` em `app.py`.
- [x] **Frontend UI:** Adicionar botão "Histórico" e Modal em `index.html`.
- [x] **Frontend Logic:** Implementar `loadHistory()` e `renderHistory()` em `script.js`.
- [x] **Integration:** Conectar o botão "Carregar" para repopular o formulário.
- [x] **Verify:** Testar fluxo de Salvar -> Listar -> Carregar.

## [PHASE 12] FRONTEND E2E TESTS (Done)
- [x] **Setup:** Configurar Playwright (Python).
- [x] **Script:** Criar `tests/test_e2e_flow.py` (Preencher formulário, calcular, validar resultado).
- [x] **Execute:** Rodar teste em modo headless (Playwright).
- [x] **Verify:** Testar fluxo de cálculo e histórico ✅

## [PHASE 13] FRONTEND REFACTORING (Done)
- [x] **Analyze:** Identificar blocos lógicos em `script.js` (UI, Calc, Validation).
- [x] **Split:** Separar em arquivos modulares.
    - [x] `utils.js` (Helpers)
    - [x] `ui.js` (DOM Logic)
    - [x] `api.js` (Fetch Service)
    - [x] `validation.js` (Form Validator)
    - [x] `main.js` (Entry Point)
- [x] **Verify:** Garantir que tudo continua funcionando (Playwright Passing ✅).
- [x] **System Test:** Teste de 100% (Drawers, Doors, Metalwork). `tests/test_full_system.py` validado com sucesso (Cálculo OK).
- [x] **Fix:** Precificação no Excel (Mão de Obra/Lucro) corrigida. Backend agora recebe dados e gera linhas corretamente.
- [x] **Melhoria (2026-02-18 15:52):** Validação de precificação antes de exportar Excel. Criado `pricing.js` modular. `main.js` refatorado com `collectFormData()`. Excel agora é gerado dinamicamente com dados atuais do formulário.

- [x] **Infra (2026-02-18 16:26):** Configurado GitHub Action (`sync-docs.yml`) para sincronizar automaticamente `README.md` e `docs/` para repositório público `canhizares-mdf-docs` a cada push no `master`.
- [x] **Auth (2026-02-18 17:55):** Sistema de login com modal (canto superior direito) e dois níveis de acesso.
- [x] **Infra (2026-02-18 17:55):** Configurado Dockerfile e docker-compose.yml para deploy no EasyPanel.
- [x] **Auth (2026-02-18 18:05):** Ativado "Modo Aberto" para testes (login visual mantido, mas acesso livre a todas as páginas).
- [x] **Cleanup (2026-02-18 18:15):** Removidos arquivos e dependências legadas do Vercel (`vercel.json`, entrypoint root, etc.) em favor do EasyPanel.
- [x] **Clarificação (2026-02-18 18:55):** Esclarecimento sobre a base de cálculo da margem de lucro (Matéria-prima + Mão de Obra).
## [PHASE 14] NEW VERSION & LOCAL TESTING (2026-02-21)
- [x] **Setup:** Preparar ambiente local e verificar dependências.
- [x] **Service:** Iniciar servidor Flask local (localhost:5000).
- [x] **Verify:** Rodar `run_system_tests.py` (3/3 passando).

## [PHASE 15] METALWORK REFINEMENT & INPUT BUGFIX (2026-02-21)
- [x] **Serralheria:** Implementar suporte a Ferro Trefilado.
- [x] **UI:** Ocultar dinamicamente o campo "Parede" para Trefilados.
- [x] **Bugfix:** Corrigir inputs de medida que não aceitavam dígitos/vírgulas em dispositivos móveis.
- [x] **Normalization:** Implementar tratamento de Decimais (Vírgula para Ponto) em todo o sistema.

## [PHASE 16] ELECTROSTATIC PAINTING (2026-02-21)
- [x] **Logic:** Criar `painting_calculator.py` com cálculo de área e custo dinâmico.
- [x] **DB:** Adicionar 10 novos materiais de pintura ao `prices.json`.
- [x] **UI:** Adicionar seção de Pintura no formulário de Serralheria.
- [x] **Inventory:** Criar aba "Pintura" no gerenciamento de estoque.
- [x] **Verify:** Validar cálculos via `test_painting_logic.py` (Área externa para tubos, Face dupla para chapas).

## [PHASE 17] PAINTING UI REFINEMENT (2026-02-21 21:10)
- [x] **UI:** Remover texto redundant "(Serralheria Only)" do título.
- [x] **UX:** Remover botão de slide (toggle) de ativação.
- [x] **Design:** Substituir dropdown por grid de cards interativos com efeitos de hover e seleção premium.
- [x] **Config:** Remover opção "Sem Pintura" e definir "Epóxi Híbrida Padrão" como seleção obrigatória inicial.
- [x] **Logic:** Adaptar `main.js` para coletar dados da grade de cards.

## [PHASE 18] EMERGENCY SYNC TOOL (2026-02-21 22:15)
- [x] **Backend:** Criar rota `/admin/import-data` para sobrescrever `prices.json`.
- [x] **Frontend:** Adicionar botão "Sync Emergência" na página de Estoque.
- [x] **UX:** Implementar lógica de upload de JSON com confirmação de segurança.
- [x] **Deploy:** Realizar push para o GitHub para permitir atualização no EasyPanel via Redeploy.

## [PHASE 19] MIGRATION TO SUPABASE (2026-02-22 09:50)
- [x] **Setup:** Configurar projeto no Supabase e definir schema das tabelas (Profiles, Materials, Prices, Calculations).
- [x] **Security:** Implementar Row Level Security (RLS) no banco de dados.
- [x] **Migration:** Criar script `migrate_to_supabase.py` para converter JSONs atuais para PostgreSQL.
- [x] **Integration:** Instalar biblioteca cliente e atualizar backend Flask (`app.py`) para CRUD via Supabase.
- [x] **Scalability:** Implementar tabelas de Clientes e Projetos para organização profissional.
- [x] **Storage:** Configurar Supabase Storage para armazenamento de anexos e desenhos técnicos.
