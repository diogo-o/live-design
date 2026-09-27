# DMO Prototype — Estado Atual
*Última atualização: 2026-09-28*

## 1. Arquitetura de Consistência (IMPLEMENTADA)
O prototype usa um kit de 3 peças que torna o drift estruturalmente impossível:

| Peça | Ficheiro | Papel |
|---|---|---|
| Tokens + componentes | `0_ASSET_CANONICAL_DESIGN_SYSTEM.css` | Única fonte de cores/fonts/spacing/classes `dmo-*` via `var(--...)` |
| Shell CSS | `0_ASSET_SHELL.css` | Estilos do header/nav |
| Shell JS | `0_ASSET_SHELL.js` | Injeta header/nav em `<div data-dmo-shell>`; lê `data-dmo-page` e `data-dmo-area` |

### Separação USER/ADMIN
- **USER**: `jobon`, `controlo`, `boquilhas` (3 tabs de topo)
- **ADMIN**: `admin` (ambiente separado, `data-dmo-area="admin"`)
- Ferramentas **nunca** aparece como tab de topo (é flow contextual)

## 2. Inventário de Páginas

### Páginas de App (com shell)
| Ficheiro | data-dmo-page | data-dmo-area | Shell | Propósito |
|---|---|---|---|---|
| `20_JOB_ON_01_...job-on.html` | jobon | — | ✅ | Job On consulta (calendário + lista) |
| `20_JOB_ON_02_...job-on-folha.html` | jobon | — | ✅ | Job On folha criar/editar/duplicar + ToolPicker |
| `21_CONTROLO_01_...controlo.html` | controlo | — | ✅ | Controlo landing / resumo |
| `21_CONTROLO_02_...folha.html` | controlo | — | ✅ | Folha de Controlo (CM/BQ/MF/PU/CS) |
| `21_CONTROLO_03_...definicoes.html` | controlo | — | ✅ | Definições do Controlo |
| `22_PESO_OPERADOR_01_...peso-operador.html` | controlo | — | ✅ | Peso criar/editar |
| `22_PESO_OPERADOR_03_...folha-peso.html` | controlo | — | ✅ | Folha de Peso (CREATE/APPROVE) |
| `23_PESO_RESPONSAVEL_01_...peso-responsavel.html` | controlo | — | ✅ | Aprovações + Comparação evento |
| `24_PEGAMENTOS_01_...pegamentos.html` | controlo | — | ✅ | Pegamentos |
| `31_BOQUILHAS_01_...boquilhas.html` | boquilhas | — | ✅ | Boquilhas workspace + histórico |
| `13_ADMIN_01_...admin.html` | admin | admin | ✅ | Admin users/templates |
| `resumo.html` | controlo | — | ✅ | Resumo do controlo |
| `tool-create.html` | — | — | ✅ | Criar/editar ferramenta |
| `index.html` | — | — | ✅ | Entry page (redirect) |

### Páginas sem Shell (pré-auth ou print)
| Ficheiro | Propósito |
|---|---|
| `12_LOGIN_01_...login.html` | Login (2 identificadores mutuamente exclusivos) |
| `22_PESO_OPERADOR_02_...PRINT_peso.html` | Print de peso |

## 3. Design System Canónico

### Classes `dmo-*` Disponíveis
- **Botões**: `.dmo-button`, `.dmo-icon-button`
- **Formulários**: `.dmo-field`, `.dmo-form-grid`
- **Cartões**: `.dmo-card`
- **Badges**: `.dmo-pill` (variantes: `.active`, `.pending`, `.approved`, `.rejected`, `.inactive`)
- **Tabelas**: `.dmo-table-wrap`, `.dmo-table`, `.dmo-num`
- **Modais**: `.dmo-modal-backdrop`, `.dmo-modal`, `.dmo-modal-head`, `.dmo-modal-body`, `.dmo-modal-foot`
- **Toast**: `.dmo-toast`
- **Calendário**: `.dmo-calendar__head`, `.dmo-calendar__week`, `.dmo-calendar__grid`, `.dmo-calendar__day`, `.dmo-calendar__nav`, `.dmo-calendar__nav-btn`
- **Layout helpers**: `.dmo-page-viewport`, `.dmo-page-main`, `.dmo-page-head`, `.dmo-stack`, `.dmo-card-pad`, `.dmo-section-head`, `.dmo-grid-2/3/4`, `.dmo-summary-item`, `.dmo-actions`, `.dmo-hint`, `.dmo-config-status`, `.dmo-recipient-list`, `.dmo-recipient-tag`
- **Spacing utilities**: `.dmo-flex`, `.dmo-items-end`, `.dmo-flex-wrap`, `.dmo-gap-2/3`, `.dmo-mt-2/3/4`
- **Outros**: `.dmo-divider`, `.dmo-toast`

### Shell
- `0_ASSET_SHELL.js`: array NAV com USER (jobon/controlo/boquilhas) e ADMIN (admin)
- Label da tab jobon = **"Planeamento"** (fonte única)
- Injeção via `data-dmo-area` e `data-dmo-page`

## 4. Decisões Fechadas (NÃO voltar atrás)

| Tema | Decisão |
|---|---|
| **Boquilhas movimentos** | 3 movimentos: `saida`, `entrada`, `entrada_sem_reparacao`. Sem lifecycle (abrir/fechar). Sem Início/Irreparável. |
| **Calote no Peso** | Referência visual apenas. **Nunca** entra na fórmula do peso do vidro. Calculador geométrico na UI (mm → cm³ → g) marcado `// DEMO ONLY`. |
| **Entrada no Resumo** | Seleção explícita de produção (dropdown decrescente). **Nunca** auto-abre a mais recente. |
| **Comparação histórica** | Emparelhamento automático por identidade de cavidade (query-time). Sem UI de emparelhamento manual. |
| **Roles/perfis** | Não existem. Só funções concedidas (create/approve). UI mostra/esconde ações, não muda de página. |
| **Master/owner** | Não existe. Entidades associam-se por id e função no fluxo. |
| **Ferramentas** | Contextual (ToolPicker), sem tab de topo. |
| **Perfil de demo** | Todas as funções disponíveis. Sem switcher. |
| **Redirect após Job On** | Guardar **NÃO** redireciona para Controlo. Fica no Job On read-only + botão explícito "Abrir Resumo no Controlo". |
| **Demo loop** | Job On guardado aparece na consulta (calendário + tabela) e Boquilhas recebe aviso contextual de candidata a associar. Sem auto-associação. |

## 5. Violações Conhecidas (Dívida Técnica)

### P0 — Estruturais
- **Nenhuma** (todas as P0 foram resolvidas)

### P1 — Higiene CSS (a limpar durante rebuilds por página)
- `@media` de reflow em páginas legacy: `12_LOGIN`, `13_ADMIN`, `22_PESO_01`, `22_PESO_02_PRINT`, `23_PESO_RESPONSAVEL`, `24_PEGAMENTOS`, `31_BOQUILHAS`
- Inline styles em páginas legacy: `13_ADMIN`, `22_PESO_01`, `22_PESO_03`, `23_PESO_RESPONSAVEL`, `24_PEGAMENTOS`, `31_BOQUILHAS`
- Ficheiros CSS mortos (mantidos como evidência, deslinkados): `beta-shell.css`, `styles.css`, `0_ASSET_JOB_ON_REDESIGN.css`
- Ficheiros JS mortos: `beta-shell.js`, `0_ASSET_JOB_ON_REDESIGN.js`, `app.js`

### P2 — Tech Debt Registado
- Nomes de ficheiros com "OPERADOR"/"RESPONSAVEL" (modelo antigo de roles) — não renomear agora para não partir links
- Modo via query string (`?mode=approve`) na folha-peso — na app real será por funções concedidas
- `.beta-light-*` / `.beta-production-rail` continuam a ser a gramática de layout do Job On — consolidar mais tarde

## 6. Lacunas Funcionais (O que Falta Construir)

### Job On
- ✅ Consulta (calendário + lista + painel de linhas)
- ✅ Folha criar/editar/duplicar com ToolPicker
- ✅ Demo loop fechado (guardar → consulta → aviso Boquilhas)

### Controlo
- ✅ Resumo landing
- ✅ Peso (folha-peso CREATE/APPROVE)
- ✅ Folha genérica (CM/BQ/MF/PU/CS com OK/NOK + MCaliper + ciclo completo)
- ✅ Definições (base directory, routing email, templates)
- ⏳ **Comparação evento** (re-medição durante produção — decisões Manter/Colocar de parte por CM) — **FALTA**
- ⏳ **Pegamentos** (já existe página, mas validar regras: eixos Costura/Contra costura, ±0.20, alertas não bloqueiam)
- ⏳ **Histórico** (resumos concluídos em leitura)
- ⏳ **Aprovações** (pending list, filtros, decisões explícitas)

### Boquilhas
- ✅ Workspace com 3 movimentos (saida/entrada/entrada_sem_reparacao)
- ✅ Histórico com filtros
- ✅ Definições (reparadores, mapa máquina→reparador)
- ⏳ Validar vocabulário de movimentos (confirmar que não há "Irreparável" ou "Início")

### Ferramentas
- ✅ `tool-create.html` (criar/editar ferramenta)
- ✅ ToolPicker contextual no Job On
- ⏳ Validar que `tool-create.html` não gera IDs (usa pool fixo `DEMO-TOOL-001..008`)

### Admin
- ✅ Users, Templates
- ⏳ Remover "Aplicações" e "Auditoria" do mockup antigo (Master §5 manda remover)

### Login
- ✅ Rebuild com 2 identificadores mutuamente exclusivos
- ⏳ Validar que não há messaging "test environment" fingido

## 7. Fila de Trabalho Pendente

### Prioridade Alta
1. **Comparação evento** (Controlo) — re-medição durante produção, decisões Manter/Colocar de parte
2. **Validar Pegamentos** — confirmar regras de eixos e alertas
3. **Validar Boquilhas** — confirmar vocabulário de movimentos
4. **Admin rebuild** — remover Aplicações/Auditoria do mockup antigo

### Prioridade Média
5. **Rebuilds por página** — limpar `@media`/inline styles legacy (módulo a módulo, sem pressa)
6. **Histórico do Controlo** — resumos concluídos em leitura
7. **Aprovações do Controlo** — pending list, filtros, decisões

### Prioridade Baixa
8. **Consolidar `.beta-light-*`** — migrar para classes `dmo-*` canónicas
9. **Renomear ficheiros** — remover "OPERADOR"/"RESPONSAVEL" dos nomes (quando não partir links)

## 8. Commits Locais Pendentes

Repo local, sem remote. **Nunca force-push.**

```
feat(shell): split USER and ADMIN navigation areas per design master
feat(design-system): add dmo-num and dmo-form-grid token-based classes
feat(design-system): extract shared layout helpers to canonical tokens
chore(pages): drop duplicated helper style blocks from new pages
chore(settings): canonicalize settings helper classes
fix(demo-loop): Job On -> Consulta -> Boquilhas with explicit association
fix(boquilhas): replace dynamic BQ trace IDs with fixed pool
feat(login): rebuild login with identifier-type routing
```

## 9. Regras Absolutas do Prototype

- **PROIBIDO:** `@media` / `@container` / reflow (layout fixo 1366×768)
- **PROIBIDO:** `localStorage` / `IndexedDB` (só `sessionStorage` para demo)
- **PROIBIDO:** fórmulas industriais em JS (marcar `// DEMO ONLY` se for visualização)
- **PROIBIDO:** inventar classes `dmo-*` novas — se faltar, **parar e perguntar**
- **PROIBIDO:** escrever markup de header/nav inline (usar `<div data-dmo-shell>`)
- **PROIBIDO:** cores/fonts/spacing inline (sempre `var(--...)`)
- **PROIBIDO:** geração de IDs em JS (usar pools fixos ou IDs escritos no HTML)
- **PROIBIDO:** fetch/XHR/API endpoints (zero backend no prototype)
- **PROIBIDO:** auto-seleção de ferramentas ou produções
- **PROIBIDO:** redirect automático entre módulos (mudar de módulo = ação explícita da pessoa)

## 10. Hierarquia de Autoridade

Quando há conflito entre fontes:
1. **Design Master** (`DMO Beta — Frontend Design Master`) → visual, navegação, componentes
2. **KB funcional** (`DOCUMENT_FLOW`, `IDENTITY_RELATIONS`, `MODULE_FLOW`, `How the App Works`) → regras de domínio
3. **Prototype existente** → o que já está construído (reutilizar, não reinventar)
4. **Legacy Access / Excel / PDFs** → evidência apenas, **nunca** template
5. **Briefs específicos** de cada módulo → detalhe funcional

**Regra de ouro:** Se algo no prototype contradiz a KB ou o Master, **para e pergunta**. Nunca assume.

---

*Este ficheiro deve ser atualizado sempre que o estado do prototype mudar significativamente.*
