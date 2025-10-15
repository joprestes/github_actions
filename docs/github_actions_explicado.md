# 🧩 Entendendo o Workflow do GitHub Actions — passo a passo

Este guia explica **linha por linha** o que faz o arquivo `.github/workflows/playwright.yml`.

O objetivo é te ajudar a **entender a lógica de execução** de um pipeline no GitHub Actions, sem precisar ter experiência prévia com YAML ou CI/CD.

---

## 🧾 1. Nome do workflow

```yaml
name: Playwright CI
```

👉 Define o **nome que aparecerá na aba “Actions”** do GitHub.  
Neste caso, chama-se *“Playwright CI”*, porque ele roda os testes automatizados.

---

## ⚡ 2. Quando o workflow é disparado

```yaml
on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]
  workflow_dispatch:
    inputs:
      tag:
        description: "Tag de execução (ex.: @smoke, @regression). Deixe vazio para rodar tudo."
        required: false
        default: ""
```

📖 Significa que o workflow será executado em três situações:
1. **push** → toda vez que alguém fizer `git push` na branch `main`  
2. **pull_request** → quando for aberto ou atualizado um PR para `main`  
3. **workflow_dispatch** → permite **executar manualmente** pela interface do GitHub, com um campo opcional chamado `tag` (para filtrar testes)

---

## 🔐 3. Permissões

```yaml
permissions:
  contents: write
  pages: write
  id-token: write
```

Essas permissões dizem o que o workflow pode fazer:
- `contents: write` → permite criar commits (por exemplo, para subir o relatório)  
- `pages: write` → permite publicar no GitHub Pages  
- `id-token: write` → usada para autenticação segura durante o deploy

---

## ⏸️ 4. Controle de concorrência

```yaml
concurrency:
  group: "pages"
  cancel-in-progress: false
```

👉 Garante que **não rodem várias execuções do Pages ao mesmo tempo**.  
Se um pipeline ainda estiver rodando, o próximo espera terminar.

---

## 💻 5. Definição do job

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
```

- **`jobs`** → representa um grupo de etapas (steps).  
- **`test`** → é o nome do job.  
- **`runs-on`** → define o ambiente em que o job será executado (aqui: Linux com Ubuntu).

---

## 🧱 6. Etapas (steps)

Cada **step** é um comando ou ação.

---

### 🪄 6.1 Checkout do código

```yaml
- name: Checkout
  uses: actions/checkout@v4
```

Baixa o código do repositório para o ambiente do GitHub Actions.  
Sem isso, o workflow não teria acesso ao código-fonte.

---

### ⚙️ 6.2 Configurar Node.js

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: "lts/*"
```

Instala o **Node.js** (versão LTS mais recente) para rodar os testes.

---

### 📦 6.3 Instalar dependências

```yaml
- name: Install dependencies
  run: npm ci
```

Roda o comando `npm ci`, que instala as dependências do projeto conforme o `package-lock.json`.

---

### 🌐 6.4 Instalar navegadores do Playwright

```yaml
- name: Install Playwright browsers
  run: npx playwright install --with-deps
```

Instala os navegadores (Chromium, Firefox, WebKit) que o Playwright precisa.

---

### 🧪 6.5 Executar os testes

```yaml
- name: Run Playwright tests (all or by tag)
  run: |
    if [ -n "${{ github.event.inputs.tag }}" ]; then
      echo "Running only tag: ${{ github.event.inputs.tag }}"
      npx playwright test --grep "${{ github.event.inputs.tag }}"
    else
      echo "Running full suite"
      npx playwright test
    fi
```

Executa os testes.  
Se o usuário informar uma **tag** na interface do GitHub (ex.: `@smoke`), só aqueles cenários são executados.  
Caso contrário, roda toda a suíte.

---

### 📁 6.6 Salvar relatório como artefato

```yaml
- name: Upload HTML report as artifact
  uses: actions/upload-artifact@v4
  with:
    name: playwright-report
    path: playwright-report
    if-no-files-found: error
```

Salva o relatório HTML gerado pelo Playwright como **artefato de build**, disponível para download direto no GitHub Actions.

---

### 🌍 6.7 Preparar e publicar no GitHub Pages

```yaml
- name: Setup Pages
  uses: actions/configure-pages@v5

- name: Upload report to Pages artifact
  uses: actions/upload-pages-artifact@v3
  with:
    path: ./playwright-report

- name: Deploy to GitHub Pages
  id: deployment
  uses: actions/deploy-pages@v4
```

Esses três passos configuram o GitHub Pages e publicam o conteúdo do relatório (`playwright-report/`) como um site estático.

No final, o relatório fica acessível em:  
👉 [https://joprestes.github.io/github_actions/](https://joprestes.github.io/github_actions/)

---

## 🧠 Resumo rápido

| Etapa | O que faz | Ferramenta |
|-------|------------|-------------|
| Checkout | Baixa o código do repo | actions/checkout |
| Setup Node.js | Instala Node | actions/setup-node |
| Install deps | Instala libs | npm ci |
| Install browsers | Prepara navegadores | playwright |
| Run tests | Executa testes | npx playwright test |
| Upload artifact | Salva relatório | upload-artifact |
| Deploy Pages | Publica relatório | deploy-pages |

---

## 💬 Dica final

Você pode adaptar esse mesmo workflow para **outros frameworks de teste**, como Cypress ou Jest — basta ajustar o comando de execução (`npx playwright test`) e o caminho do relatório.

---

✍️ **Autoria:** Joelma Prestes Prestes Ferreira
📚 Projeto Crescer – Qualidade de Software
