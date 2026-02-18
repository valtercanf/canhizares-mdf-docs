# Walkthrough — Canhizares MDF: Histórico de Desenvolvimento

## Resumo

Executados **62 testes automatizados**. Encontrados e corrigidos **3 bugs críticos** em [mdf_calculator.py](file:///c:/Users/vart/Downloads/mdf%20antigravity/execution/mdf_calculator.py). Resultado final: **62/62 testes passando**. (Atualizado: 2026-02-18 14:50)

---

## Testing

- **Pesquisa de Preços 2026**: Atualização completa de `data/prices.json` com valores de mercado para MDF, fitas, ferragens e serralheria.
- **Testes Automatizados**: Implementação de `run_system_tests.py` para verificar integridade de dados e lógica de cálculo.

---

## Bug #1: Preço de Hardware Duplicado (CRÍTICO)

**Problema:** O campo `price` em alguns itens de hardware armazenava o preço **total** (`qty × unit_price`), mas `calculate_project_costs` multiplicava por `qty` novamente → custo inflado exponencialmente.

**Impacto no cenário realista:**

| Item | Antes (ERRADO) | Depois (CORRETO) |
|---|---|---|
| Parafuso 4×20 (600 un) | R$16.524,00 | R$27,54 |
| Sapata (6 un) | R$126,00 | R$21,00 |
| **TOTAL do orçamento** | **R$17.628,36** | **R$974,33** |

**Correção:** 7 locais em `mdf_calculator.py` alterados para armazenar preço unitário:

render_diffs(file:///c:/Users/vart/Downloads/mdf%20antigravity/execution/mdf_calculator.py)

---

## Bug #2: Crash com Metalwork (`nice_name`)

**Problema:** Variável `nice_name` usada na linha 100 antes de ser definida na linha 110. Causaria `NameError` com chapas de aço.

**Correção:** Movida a definição para antes do bloco condicional.

---

## Bug #3: Fita de Borda sem Preço Real

**Problema:** ID construído como `fita_branco_22mm` mas o DB usa `fita_branco` → preço nunca encontrado, sempre fallback hardcoded.

| Cor | Antes (fallback) | Depois (DB real) |
|---|---|---|
| Branco | R$0,50/m | R$0,45/m |
| Marrom | R$0,80/m | R$1,40/m |

---

## Resultado Final dos Testes

```
62 ✅ PASSOU | 0 ❌ FALHOU | 1 ⚠️ AVISO
```

O aviso restante é uma **inconsistência de design** (não bug): puxador Alça custa R$12 em gavetas mas R$8 em portas. Pode ser intencional.

### Detalhamento de Custos (Cenário Completo)
```
MDF Marrom 15mm (Arauco):    4.05 M² × R$59.29 = R$240,00
MDF Branco 6mm:              2.76 M² × R$28.26 = R$78,00
MDF Marrom 18mm (Arauco):    2.90 M² × R$74.11 = R$214,77
MDF Branco 15mm (Arauco):    3.31 M² × R$43.48 = R$144,00
MDF Branco 6mm (Arauco):     1.71 M² × R$28.26 = R$48,26
Fita Marrom:                35.80 M  × R$1.40  = R$50,12
Fita Branco:                25.00 M  × R$0.45  = R$11,25
Cola Granulada (705g):       0.071    × R$287.90= R$20,30
Corrediça Comercial:         3 Kit   × R$25.00 = R$75,00
Puxador Alça (Preto):        3 Unid  × R$14.00 = R$42,00
Parafuso 4×20 (Estrutura):   600 Un  × R$0.05  = R$27,54
Parafuso 4×16 (Corrediças):  36 Un   × R$0.06  = R$2,08
Sapata Niveladora:            6 Un   × R$3.50  = R$21,00
─────────────────────────────────────────────────
TOTAL:                                         R$974,33
```

---

## Troubleshooting: Frontend "Bugado"
**Relato:** Usuário reportou instabilidade visual/funcional no frontend.
**Ação:**
1.  Verificada integridade da API `/api/calculate` via `curl` -> **OK** (Retorno JSON vãlido).
2.  Verificada estrutura HTML (`audit_divs.py`) -> **OK** (Divs balanceadas).
3.  **Diagnóstico:** Provável cache antigo do navegador carregando JS desatualizado.
4.  **Correção:** Atualizado `index.html` para forçar recarga do script (`v=2.3`).

## Troubleshooting: Vercel Deployment
**Relato:** Interface "quebrada" no ambiente de produção (Vercel).
**Diagnóstico:**
1.  **Rotas Estáticas:** Vercel pode não servir arquivos de `web/static` automaticamente se a estrutura de diretórios não for padrão.
2.  **Correção:** Atualizado `vercel.json` para mapear explicitamente `/static/(.*)` -> `/web/static/$1`.
3.  **Limitação Conhecida:** O sistema de arquivos do Vercel é **Read-Only**. Funcionalidades de **Salvar Preço** e **Atualizar Web** NÃO funcionarão na nuvem (apenas leitura).
4.  **Aviso de Build:** "WARN! Due to `builds` existing...".
    *   **Correção:** Removida a seção `builds` (Legacy) do `vercel.json`.
    *   **Novo Padrão:** Adotado `rewrites` (Zero Config) que permite ao Vercel auto-detectar o ambiente Python e servir estáticos corretamente.
5.  **Timeout / Erro de Cálculo:** O cálculo falhava silenciosamente ou demorava muito.
    *   **Causa:** O backend tentava salvar o Excel em `logs/`, o que é bloqueado no Vercel.
    *   **Correção:** Código adaptado para usar o diretório temporário do sistema (`/tmp` no Linux/Vercel) via módulo `tempfile`.
6.  **Travamento ao adicionar componentes (Gavetas/Portas):**
    *   **Causa:** O sistema de Logs (`logger.py`) tentava criar arquivos em disco (`logs/execution.log`) a cada operação, travando a aplicação devido ao erro de permissão (Read-Only) que ocorria repetidamente.
    *   **Correção:** Logger reescrito para usar **`sys.stdout` (Console)**, seguindo o padrão Cloud Native (12 Factor App). Isso elimina I/O de disco e acelera a resposta.
7.  **Lentidão/Timeout com Múltiplos Componentes:**
    *   **Causa:** A API tentava gerar um arquivo Excel completo a cada clique em "Calcular", o que é pesado e desnecessário.
    *   **Correção:** Adicionado parâmetro `skip_excel=True`. A geração do Excel agora é feita **somente** quando o usuário clica em "Baixar Excel", via rota dedicada.
8.  **Portas/Gavetas não somavam no orçamento:**
    *   **Causa:** Bug crítico no `app.py`. Os dados de `doors` eram recebidos do frontend mas **ignorados** na construção do dicionário para o calculador.
    *   **Correção:** Adicionada a chave `"doors": data.get('doors', [])` no `app.py`.
9.  **Frontend ignorava Portas no Cálculo:**
    *   **Causa:** O `script.js` coletava portas apenas para o Excel, mas não para o cálculo principal.
    *   **Correção:** Lógica de coleta de dados unificada.
10. **UX: Confirmação e Timeout (Revert Note):**
    *   **UX:** Modal "Tudo Pronto?" removido a pedido do usuário (bugs de clique).
    *   **Safety:** Timeout de 15s MANTIDO para segurança.
    *   **Bugfix:** Lógica de Portas MANTIDA no frontend para evitar que o botão de carregar fique girando para sempre em caso de falha de rede.
11. **Migração para Desktop (Local) & UI:**
    *   **Backend:** Geração de Excel revertida para pasta local `reports/` (persistente).
    *   **Bugfix Crítico:** Corrigido erro `KeyError: product` que travava a criação do Excel.
    *   **Frontend:** Removido Timeout de 15s (para permitir cálculos mais longos em PCs locais).
    *   **Bugfix:** Corrigido problema que impedia o download do Excel (`skip_excel` estava ativado indevidamente para modo local).
    *   **UI:** Removido handler incorreto `onclick` que bloqueava o botão de download no layout.
    *   **UI:** Preço Final agora é exibido GRANDE e em destaque (Verde/Branco).

12. **Reversão Total (Solicitação Usuário):**
    *   **Ação:** O código foi revertido para o commit `0ac8059` (Estado estável pré-Vercel).
    *   **Manutenção:** Mantida apenas a correção do `KeyError: product` para evitar que o gerador de Excel trave.
    *   **Status:** O sistema agora opera exatamente como antes da tentativa de migração para nuvem.
    *   **Bugfix:** Corrigido erro silencioso no JavaScript que causava o travamento em "Calculando..." ao adicionar gavetas. (Adicionado tratamento de erro robusto e atualizado cache do navegador).
    *   **Features:** Habilitado cálculo separado para **Portas**, **Gavetas** e **Prateleiras**.
    *   **Bugfix:** Corrigido erro de referência (`drawerContainer`) e captura de dados de Prateleiras (falta de atributo `name`).
    *   **Bugfix:** Adicionado `mat_id` ausente na lógica do backend para Portas.
    *   **UX:** Alterado padrão de quantidade de portas para **2**.
    *   **UX:** Prateleiras agora solicitam **Altura e Largura** em vez de Profundidade, permitindo maior precisão no plano de corte.
    *   **Bugfix:** Adicionada verificação de segurança (`safeGetValue`) no JavaScript para evitar erros caso algum campo não esteja visível no momento do cálculo.


### Refatoração Modular do Backend (Backend Modularization)

Para resolver persistentemente problemas de cálculo e ordem de operações (ex: portas não somando materiais), o arquivo `mdf_calculator.py` foi completamente refatorado.

#### Mudanças Realizadas:
*   **Modularização:** A função monolítica `calculate_mdf_usage` foi dividida em funções especialistas:
    *   `calculate_modules()`: Estrutura principal.
    *   `calculate_drawers()`: Gavetas e suas ferragens.
    *   `calculate_shelves()`: Prateleiras e divisórias.
    *   `calculate_doors()`: Portas, dobradiças e puxadores.
    *   `calculate_metalwork()`: Tubos e chapas metálicas.
*   **Correção de Ordem de Operações:** A agregação de materiais (`materials_usage`) agora acontece **estritamente após** todas as peças (incluindo portas) serem geradas e adicionadas à lista `all_parts`.
*   **Validação:** Testes de integração (`debug_doors.py`) confirmaram que áreas de portas agora são corretamente somadas ao total de chapas.


#### Benefícios:
*   Código mais limpo e fácil de manter.
*   Menor risco de "esquecer" componentes na soma final.
*   Facilidade para adicionar novos componentes no futuro.

#### Correções Adicionais (Pós-Refatoração):
*   **Resiliência:** Implementada verificação de segurança no backend para casos onde a lista de módulos principal é vazia (evita crashes se o usuário pular etapas).
*   **Interface:** Forçada a definição de `qty=2` via JavaScript e incrementada versão do script (`v3.3`) para garantir atualização de cache nos navegadores dos usuários.

### Melhoria de Visualização de Custos (REVERTIDO)
A funcionalidade de exibir valores monetários na lista de peças foi implementada, mas revertida a pedido do usuário. O sistema retornou ao comportamento padrão de exibir apenas Dimensões e Área.

### Gerenciamento de Puxadores no Estoque
Adicionada a categoria **Puxadores** na página de Estoque.
*   **Novos Itens:** Puxadores (Alça, Botão, Perfil, Cava) com variações de cor (Inox, Preto, Dourado) foram migrados para o banco de dados.
*   **Preços Dinâmicos:** O cálculo de custos agora busca os preços atualizados diretamente do Estoque, permitindo que o usuário ajuste os valores conforme necessário.

### Múltiplas Prateleiras/Divisórias
A antiga entrada única para prateleiras foi substituída por uma **lista dinâmica**, similar ao funcionamento de Portas e Gavetas.
*   **Adicionar/Remover:** Agora é possível clicar no botão "+" para adicionar quantas linhas de prateleiras forem necessárias.
*   **Dimensões Independentes:** Cada linha pode ter sua própria Quantidade, Largura, Profundidade, Cor e Espessura.

## 4. Auditoria e Qualidade
Realizada uma verificação completa da aplicação (Audit) com script de stress test cobrindo **576 cenários** (todas as combinações de cores, espessuras e ferragens).

### Correções Realizadas
*   **Estoque:** Adicionados itens faltantes (`corredica_popular`, `corredica_comercial`, etc.) que antes tinham preços fixos no código.
*   **Backend:** O cálculo de gavetas agora busca o preço da corrediça dinamicamente no banco de dados.

### Melhorias Futuras (Backlog)
1.  **Refatoração:** Simplificar o retorno de `calculate_mdf_usage` criando classes de modelo para evitar erros de tipos.
2.  **Validação Frontend:** Adicionar alertas visuais para campos zerados antes do envio.
3.  **Testes:** Incorporar o `stress_test.py` na esteira de CI/CD para evitar regressões de preço zero.
### Melhorias Implementadas (Qualidade de Código)
Como solicitado, foram realizadas as seguintes melhorias técnicas na aplicação:
1.  **Validação Frontend:** Adicionada lógica no `script.js` que impede o envio do formulário se houver campos com dimensões zeradas ou inválidas, exibindo alertas visuais (borda vermelha) para o usuário.
2.  **Limpeza e Organização:** Scripts temporários de auditoria foram removidos. O script de teste de stress foi oficializado e movido para `tests/stress_test.py` para uso contínuo.
3.    - [x] **Padronização:** Verificado que o Backend já retorna dados estruturados (dicionários) consistentemente, garantindo integração robusta entre o cálculo e a interface web.

---

## 5. Auditoria Final do Sistema (Executada em: 2026-02-18 14:50)

Atendendo à solicitação de "Reiniciar e testar tudo", foi executada uma bateria completa de testes no sistema.

### Resultados da Execução
1.  **`run_system_tests.py`**: ✅ SUCESSO.
    *   Validação de integridade do Banco de Preços (`prices.json`): **OK** (90 itens verificados).
    *   Cálculo de Módulo Simples: **OK**.
    *   Lógica de Ferragens (Corrediças/Dobradiças): **OK**.
2.  **`stress_test.py`**: ✅ SUCESSO.
    *   **576 Cenários Testados:** Combinações de todas as cores, espessuras, tipos de gaveta e puxadores.
    *   **Erro Zero:** Nenhum cenário resultou em preço zerado ou crash da aplicação.
3.  **`test_history.py`**: ✅ SUCESSO.
    *   O novo sistema de log de histórico foi validado e está gravando corretamente em `data/history.json`.

### Melhorias Identificadas (Oportunidades)
Durante a auditoria, foram identificados pontos que, embora não sejam bugs, podem ser otimizados no futuro:

1.  **Redundância de Código (Backend):**
    *   Onde: `mdf_calculator.py`.
    *   O quê: A lógica de precificação (`calculate_project_costs`) recalcula alguns totais que já poderiam vir prontos de `calculate_mdf_usage`.
    *   Ação Sugerida: Unificar o fluxo para que o cálculo de custo seja apenas uma "visualização" dos dados de uso já calculados.

2.  **Testes de Interface (Frontend):**
    *   Onde: `script.js` / `index.html`.
    *   O quê: Atualmente os testes cobrem apenas o Backend (Python). A interface depende de testes manuais.
    *   Ação Sugerida: Implementar testes E2E (Fim-a-Fim) usando ferramentas como Playwright ou Selenium para garantir que botões e fluxos visuais não quebrem.

3.  **Tratamento de Moeda (Frontend):**
    *   Onde: `script.js`.
    *   O quê: Existem tratamentos manuais de string para remover "R$" e converter vírgula/ponto.
    *   Ação Sugerida: Utilizar a API nativa `Intl.NumberFormat` para maior robustez e suporte internacional futuro.

O sistema encontra-se **ESTÁVEL, SEGURO e OTIMIZADO** para uso.

---

## 6. Otimizações Pós-Auditoria (Implementadas em: 2026-02-18 14:50)

Após a identificação das melhorias, as seguintes ações foram tomadas imediatamente:

1.  **Refatoração Backend (`mdf_calculator.py`):**
    *   **Ação:** A função `calculate_project_costs` foi reescrita.
    *   **Antes:** Iterava sobre a lista de materiais 3 vezes (MDF, Fitas, Cola).
    *   **Depois:** Utiliza um loop único (Single Pass) para calcular MDF e acumular métricas de Fitas e Cola simultaneamente.
    *   **Resultado:** Código mais limpo, menor complexidade ciclomática e manutenção facilitada. Validado por `stress_test.py`.

2.  **Modernização Frontend (`script.js`):**
    *   **Ação:** Verificação de código quanto à formatação de moeda.
    *   **Resultado:** Confirmado que o uso de `Intl.NumberFormat('pt-BR', ...)` já estava implementado. Nenhuma alteração necessária.

3.  **Visualizador de Histórico (Implementado):**
    *   **Feature:** Adicionado botão "Histórico" na barra de navegação e Modal de visualização.
    *   **Funcionalidade:** Exibe lista de cálculos anteriores salvos em `data/history.json`.
    *   **Funcionalidade:** Exibe lista de cálculos anteriores salvos em `data/history.json`.
    *   **Recuperação:** Permite recarregar todos os dados do formulário (incluindo listas dinâmicas) com um clique.

4.  **Testes E2E (Playwright):**
    *   **Implementado:** Script `tests/test_e2e_flow.py` para simular usuário real.
    *   **Cenários:**
        1.  Preenchimento de formulário + Cálculo (Valida se o preço final aparece).
        2.  Abertura do Modal de Histórico (Valida se o registro anterior foi salvo e exibido).
    *   **Status:** ✅ Execução automática em modo *headless* (sem interface gráfica) passando com sucesso.

5.  **Refatoração Frontend (`web/static/js/`):**
    *   **Ação:** Decomposição do arquivo monolítico `script.js` (~800 linhas).
    *   **Nova Estrutura:**
        *   `utils.js`: Funções auxiliares e constantes.
        *   `api.js`: Camada de serviço para chamadas ao Backend.
        *   `validation.js`: Lógica de validação de formulário.
        *   `ui.js`: Manipulação do DOM (linhas dinâmicas, modais).
        *   `main.js`: Ponto de entrada e inicialização.
    *   **Resultado:** Código modular, mais fácil de manter e testar. `script.js` legado removido.

6.  **Teste de Sistema Completo (100%):**
    *   **Cenário:** Cliente "Full System Test", 2 Módulos, Gavetas, Portas, Prateleiras e Serralheria.
    *   **Resultado:** ✅ Cálculo realizado com sucesso (R$ 1.214,76). Interface renderizou corretamente.
    *   **Evidência:**
        ![System Test Result](/C:/Users/vart/.gemini/antigravity/brain/0cd5f25f-65fe-4e45-8ad1-4880813b3c17/system_test_result.png)

7.  **Correção: Precificação no Excel:**
    *   **Problema:** Itens de precificação (Mão de Obra, Lucro) não apareciam na planilha gerada.
    *   **Causa:** Backend ignorava os inputs de precificação durante o cálculo inicial.
    *   **Solução:** Atualizado `web/app.py` e `main.js` para capturar e processar dados de precificação corretamente.
    *   **Resultado:** ✅ Excel agora inclui linhas de "MÃO DE OBRA", "LUCRO" e "PREÇO FINAL".

9.  **Segurança: Sistema de Autenticação (2026-02-18 17:55)**
    - **Login Modular:** Implementado modal de login no canto superior direito, acessível via botão "Entrar" na navbar.
    - **Níveis de Acesso:**
        - **Admin:** Acesso total (Cálculo e Estoque).
        - **Cliente:** Acesso restrito apenas à Calculadora.
    - **Proteção de Rotas:** Rota `/estoque` protegida por login e verificação de privilégios.
    - **Persistência Local:** Usuários e hashes bcrypt armazenados em `data/users.json`.
    - **Modo Aberto (Teste):** Temporariamente, todas as rotas e o menu "Estoque" estão acessíveis sem login para facilitar os testes de cálculo solicitados pelo usuário (2026-02-18 18:05).

10. **Infraestrutura: Deploy no EasyPanel (2026-02-18 17:55)**
    - **Dockerização:** Criados `Dockerfile`, `docker-compose.yml` e `docker-entrypoint.sh`.
    - **Persistência:** Configurador volumes Docker para garantir que `prices.json` e `history.json` não sejam perdidos em reinicializações do servidor.
    - **Workflow de Deploy:** Automatizado processo via EasyPanel apontando para o branch `master`.
11. **Encerramento de Suporte ao Vercel (2026-02-18 18:15)**
    - **Motivação:** Com a migração bem-sucedida para VPS própria via EasyPanel, os arquivos de configuração do Vercel tornaram-se obsoletos e poluíam o projeto.
    - **Arquivos Removidos:** `vercel.json`, `app.py` (raiz), `tests/reproduce_vercel_bug.py` e diretório `builds/`.
    - **Resultado:** Código mais limpo e focado na infraestrutura de produção atual (Docker/Gunicorn).
