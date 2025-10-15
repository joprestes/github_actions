# 🧪 Playwright + GitHub Actions — Guia Passo a Passo (do Zero)

Este README ensina você (e seus alunos do **Projeto Crescer – Qualidade de Software**) a:

1. **Criar um repositório do zero no GitHub**
2. **Inicializar um projeto de testes com Playwright** respondendo ao **assistente interativo da CLI**
3. **Executar os testes localmente** com `npx playwright test`
4. **Rodar os testes no GitHub Actions** (com parâmetro para filtrar por tag)
5. **Publicar o relatório de testes no GitHub Pages**

---

## ✅ Pré-requisitos

- Conta no **GitHub**
- **Git** instalado (`git --version`)
- **Node.js 18+** instalado (`node -v`)
  > ⚠️ Se estiver com Node 16.x, atualize para a versão 18 ou superior:
  ```bash
  nvm install 18
  nvm use 18
  ```

---

## 🏗️ Estrutura do Projeto

Após seguir o passo a passo, sua estrutura deve ficar assim:

```
github_actions/
├── .github/
│   └── workflows/
│       └── playwright.yml
├── .vscode/                      👈 Configurações do VS Code
│   ├── settings.json
│   └── extensions.json
├── docs/                         👈 Documentação técnica do workflow
│   └── github_actions_explicado.md
├── tests/                        👈 Testes Playwright
│   └── example.spec.ts
├── playwright.config.ts
├── package.json
└── README.md
```

---

## 1) Criar o repositório no GitHub (do zero)

1. Acesse **https://github.com/new**
2. **Repository name:** `github_actions`
3. **Visibility:** `Public`
4. **⚠️ Importante:** _Não_ selecione criar README, .gitignore ou licença automático
5. Clique em **Create repository**
6. Copie a **URL HTTPS** do repositório (ex: `https://github.com/joprestes/github_actions.git`)

---

## 2) Preparar o projeto local e conectar ao GitHub

No terminal:

```bash
mkdir github_actions && cd github_actions
git init
git remote add origin https://github.com/{{seu_usuario}}/github_actions.git
```

Verifique com `git remote -v` para confirmar.

---

## 3) Inicializar projeto Node e instalar Playwright

```bash
npm init -y
npm create playwright@latest
```

### 3.2 Respostas recomendadas no assistente (CLI)

| Pergunta CLI                                 | Resposta sugerida | Observação                                 |
| -------------------------------------------- | ----------------- | ------------------------------------------ |
| Do you want to use TypeScript or JavaScript? | **JavaScript**    | melhor para didática                       |
| Where to put your end‑to‑end tests?          | **tests**         | fácil de lembrar                           |
| Add a GitHub Actions workflow?               | **Yes**           | já gera `.github/workflows/playwright.yml` |
| Install Playwright browsers?                 | **Yes**           | necessário pro CI e local                  |
| Install dependencies?                        | **Yes**           | deixa tudo pronto                          |
| Generate example tests?                      | **Yes**           | ajuda a validar logo `npx playwright test` |

Após isso, sua estrutura ficará algo como:

```
.github/
  workflows/
    playwright.yml
tests/
  example.spec.ts
playwright.config.ts
package.json
...
```

---

## 4) Primeiro commit e push

```bash
git add .
git commit -m "feat: projeto inicial com Playwright e workflow do GitHub Actions"
git branch -M main
git push -u origin main
```

---

## 5) Executar testes localmente

```bash
npx playwright test
npx playwright show-report
```

---

## 6) Rodar no GitHub Actions (workflow com tag e deploy)

Use este `playwright.yml` no caminho `.github/workflows/playwright.yml`.

Para entender **cada linha desse arquivo**, veja a documentação completa em:
👉 [docs/github_actions_explicado.md](docs/github_actions_explicado.md)

---

## 7) Ativar o GitHub Pages (uma vez)

1. Vá em **Settings → Pages**
2. Em **Build and deployment**, selecione **Source: GitHub Actions**
3. Salve
4. Após um funcionamento correto do workflow, o relatório estará disponível em:

```
https://joprestes.github.io/github_actions/
```

---

## 8) Execução manual com tag pelo UI

1. Vá em **Actions** → selecione **Playwright CI**
2. Clique em **Run workflow**
3. No campo **Tag de execução**, coloque algo como `@smoke` (ou deixe vazio)
4. Clique **Run workflow**
5. Aguarde; relatório será publicado no GitHub Pages

---

## 9) Problemas comuns

- “divergent branches” ao `git pull` → use `git pull --rebase origin main`
- Workflow não dispara → verifique se `.github/workflows/playwright.yml` existe e foi enviado para `main`
- Relatório não aparece no Pages → confirme em **Settings → Pages** que a fonte é **GitHub Actions**
- Navegadores faltando no CI → garanta `npx playwright install --with-deps` antes dos testes
- Instabilidade em testes → adicionar `--retries=2` ou configurar no `playwright.config.ts`

---

## 🧷 Resumo de comandos úteis

```bash
git init
git remote add origin https://github.com/joprestes/github_actions.git

npm init -y
npm create playwright@latest

npx playwright test
npx playwright show-report

git add .
git commit -m "feat: projeto inicial com Playwright e workflow do GitHub Actions"
git branch -M main
git push -u origin main
```

---

## 📘 Documentação Técnica do Workflow

Quer entender o que **cada parte do arquivo `.yml` faz**?
➡️ Veja: [docs/github_actions_explicado.md](docs/github_actions_explicado.md)

Esse documento explica de forma **simples e passo a passo** o conteúdo do arquivo `playwright.yml`, ideal para quem está começando com GitHub Actions.

> 💡 **Para estudo:**
> O arquivo [`playwright_original.yml`](.github/workflows/playwright_original.yml) mostra a versão gerada automaticamente pelo Playwright, antes das personalizações feitas no projeto.
