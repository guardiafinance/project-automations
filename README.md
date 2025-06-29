# Reusable GitHub Actions Workflows

Este diretório contém workflows reutilizáveis que podem ser utilizados em múltiplos repositórios para automatizar processos de desenvolvimento.

## 📦 Workflows Disponíveis

### 1. **reusable-label-sync.yml** - Sincronização de Labels
Sincroniza labels do repositório baseado em arquivo de configuração.

**Como usar:**
```yaml
jobs:
  sync-labels:
    uses: guardiafinance/project-automations/.github/workflows/reusable-label-sync.yml@main
    with:
      labels_config_path: ".github/labeling/labels.yml"  # Opcional
      delete_other_labels: true                          # Opcional
      dry_run: false                                     # Opcional
```

**Inputs:**
- `labels_config_path`: Caminho para arquivo de configuração de labels (padrão: `.github/labeling/labels.yml`)
- `delete_other_labels`: Remove labels não definidos no config (padrão: `true`)
- `dry_run`: Executa sem fazer alterações (padrão: `false`)

---

### 2. **reusable-labeler.yml** - Labeling Automático
Aplica labels automaticamente em issues e pull requests.

**Como usar:**
```yaml
jobs:
  auto-labeler:
    uses: guardiafinance/project-automations/.github/workflows/reusable-labeler.yml@main
    with:
      issue_labeler_config: ".github/labeling/issue-labeler.yml"    # Opcional
      pr_labeler_config: ".github/labeling/pr-labeler.yml"         # Opcional
      pr_reviewers_config: ".github/reviewers/pr-reviewers.yml"    # Opcional
      enable_size_labeling: true                                   # Opcional
      size_config: '{"0": "XS", "10": "S", "30": "M", "100": "L", "500": "XL", "1000": "XXL"}'  # Opcional
```

**Inputs:**
- `issue_labeler_config`: Config para labeling de issues
- `pr_labeler_config`: Config para labeling de PRs  
- `pr_reviewers_config`: Config para atribuição de reviewers
- `enable_size_labeling`: Habilita labels de tamanho de PR
- `size_config`: Configuração dos tamanhos de PR em JSON

---

### 3. **reusable-project-automations.yml** - Automações de Projeto
Gerencia automaticamente o movimento de issues e PRs no GitHub Projects.

**Como usar:**
```yaml
jobs:
  project-automations:
    uses: guardiafinance/project-automations/.github/workflows/reusable-project-automations.yml@main
    with:
      organization: "guardiafinance"              # Obrigatório
      project_id: "13"                          # Obrigatório
      issue_opened_status: "BACKLOG"            # Opcional - Status para issues abertas
      issue_reopened_status: "TO REFINEMENT"   # Opcional - Status para issues reabertas
      issue_closed_status: "DONE"              # Opcional - Status para issues fechadas
      issue_rejected_status: "REJECTED"        # Opcional - Status para issues rejeitadas
      pr_opened_status: "CODE REVIEW"          # Opcional - Status para PRs abertos
      pr_closed_status: "DONE"                 # Opcional - Status para PRs fechados
      rejected_label: "rejected ❌"            # Opcional - Label para items rejeitados
      move_related_issues: true                # Opcional - Mover issues relacionadas
    secrets:
      gh_project_token: ${{ secrets.GH_PROJECT_TOKEN }}  # Obrigatório
```

**Inputs obrigatórios:**
- `organization`: Nome da organização GitHub
- `project_id`: ID do GitHub Project

**Inputs opcionais (com valores padrão):**
- `issue_opened_status`: Status quando issues são abertas (padrão: `"BACKLOG"`)
- `issue_reopened_status`: Status quando issues são reabertas (padrão: `"TO REFINEMENT"`)
- `issue_closed_status`: Status quando issues são fechadas (padrão: `"DONE"`)
- `issue_rejected_status`: Status quando issues são rejeitadas (padrão: `"REJECTED"`)
- `pr_opened_status`: Status quando PRs são abertos (padrão: `"CODE REVIEW"`)
- `pr_closed_status`: Status quando PRs são fechados (padrão: `"DONE"`)
- `rejected_label`: Nome do label para items rejeitados (padrão: `"rejected ❌"`)
- `move_related_issues`: Se deve mover issues relacionadas (padrão: `true`)

**Secrets obrigatórios:**
- `gh_project_token`: Token com permissões de projeto

---

## 🚀 Configuração Rápida

### Para usar em outro repositório:

1. **Crie os arquivos de trigger:**

```yaml
# .github/workflows/labeler.yml
name: "Labeler"
on:
  issues:
    types: [opened, reopened, edited]
  pull_request_target:
    types: [opened, reopened, synchronize]

jobs:
  auto-labeler:
    uses: guardiafinance/project-automations/.github/workflows/reusable-labeler.yml@main
```

```yaml
# .github/workflows/project-automations.yml
name: Project automations
on:
  issues:
    types: [opened, reopened, closed]
  pull_request:
    types: [opened, reopened, closed]

jobs:
  project-automations:
    uses: guardiafinance/project-automations/.github/workflows/reusable-project-automations.yml@main
    with:
      organization: "sua-organizacao"
      project_id: "seu-project-id"
      # Opcional: customize os status do seu projeto
      issue_opened_status: "📋 Backlog"
      issue_reopened_status: "🔍 To Refinement"
      pr_opened_status: "👁️ Code Review" 
      pr_closed_status: "🎉 Done"
    secrets:
      gh_project_token: ${{ secrets.GH_PROJECT_TOKEN }}
```

2. **Configure as variáveis necessárias:**
   - `ORGANIZATION_NAME` (repository variable)
   - `GH_PROJECT_ID` (repository variable)  
   - `GH_PROJECT_TOKEN` (repository secret)

3. **Crie os arquivos de configuração:**
   - `.github/labeling/labels.yml`
   - `.github/labeling/issue-labeler.yml`
   - `.github/labeling/pr-labeler.yml`
   - `.github/reviewers/pr-reviewers.yml`

---

## 💡 Exemplos de Uso

### **Configuração Mínima (usa valores padrão):**
```yaml
jobs:
  project-automations:
    uses: guardiafinance/project-automations/.github/workflows/reusable-project-automations.yml@main
    with:
      organization: "guardiafinance"
      project_id: "13"
    secrets:
      gh_project_token: ${{ secrets.GH_PROJECT_TOKEN }}
```

### **Configuração Personalizada (com emojis):**
```yaml
jobs:
  project-automations:
    uses: guardiafinance/project-automations/.github/workflows/reusable-project-automations.yml@main
    with:
      organization: "guardiafinance"
      project_id: "13"
      issue_opened_status: "BACKLOG"
      issue_reopened_status: "TO REFINEMENT"
      issue_closed_status: "DONE"
      issue_rejected_status: "REJECTED"
      pr_opened_status: "CODE REVIEW"
      pr_closed_status: "DONE"
      rejected_label: "rejected ❌"
      move_related_issues: true
    secrets:
      gh_project_token: ${{ secrets.GH_PROJECT_TOKEN }}
```

---

## ✅ Benefícios dos Reusable Workflows

- **🔄 Reutilização**: Use os mesmos workflows em múltiplos repos
- **🛠️ Manutenibilidade**: Centralize lógica em um local
- **⚙️ Configurabilidade**: Inputs flexíveis para customização
- **🔒 Segurança**: Controle centralizado de permissões
- **📊 Consistência**: Padronização entre projetos

---

## 📝 Exemplo Completo

Veja os arquivos deste repositório como exemplo de implementação:
- `label-sync.yml` - Como usar o reusable para sync de labels
- `labeler.yml` - Como usar o reusable para labeling
- `project-automations.yml` - Como usar o reusable para automações de projeto 