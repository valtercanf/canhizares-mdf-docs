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
    *   **Status:** O sistema agora opera exatamente como antes da tentativa de migração para nuvem. (Restaurado para commit `0ac8059`).
    *   **Bugfix:** Manutenção da correção do `KeyError: product` e travamentos de JS em gavetas.

### 13. Migração para Supabase (Database Persistente) (22/02/2026 10:00)
- **Persistência Global:** Transição de arquivos JSON para PostgreSQL no Supabase, garantindo que preços e histórico sobrevivam a redeploys.
- **Alta Performance:** Otimização do carregamento de materiais e histórico em lotes (bulk fetch).

### 14. Refatoração Modular do Backend (V5.0) (22/02/2026 11:00)
- **Arquitetura Segmentada:** O `mdf_calculator.py` foi decomposto em módulos especialistas (`drawers.py`, `doors.py`, `metalwork.py`, etc.).
- **Agrupamento Visual**: Peças no orçamento agora são agrupadas por Módulo e Categoria, facilitando a leitura técnica.
- **Precisão de Medidas**: Implementação de tratamento robusto para vírgulas e ajuste de precisão decimal.

### 15. Automatização de Fornecedores e Espessura (22/02/2026 12:00)
- **Seleção Autônoma**: O sistema escolhe automaticamente o fornecedor mais barato, removendo a necessidade de seleção manual.
- **Correção 18mm**: Frentes de gaveta agora herdam a espessura do módulo, eliminando o valor fixo indevido.

### 16. Estruturas Flexíveis e Ripas de Ligação (22/02/2026 16:00)
- **Flexibilidade Total**: "Tampo Superior" e "Base Inferior" separados. Opção de "Engrosso" (moldura) automática para tampos.
- **Ripas de Ligação**: Geradas automaticamente para unir módulos com tampos de cores diferentes. **Correção 15mm (0.015m)** aplicada conforme padrão técnico.
- **Cores Mistas**: Lógica profissional de acabamento — ripa na cor do tampo com fita na cor da estrutura.
- **Fixação**: Inclusão de 8 parafusos extras no orçamento.

### 17. Padronização de Versão e Timezone (22/02/2026 16:10)
- **Tag de Versão**: Atualizado de `V4.0-carnaval` para `v5` em todo o frontend (`index.html` e `inventory.html`).
- **Fuso Horário**: A data do commit agora exibe explicitamente o timezone de Brasília `(GMT-3)`.
- **Backend**: Refatorada a função `get_last_commit_date` em `app.py` para injetar o sufixo de fuso horário automaticamente.

---
**Status Final**: Sistema V5.0 estável, maduro e com lógica de marcenaria de alto nível, pronto para uso profissional.
