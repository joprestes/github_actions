# 🧪 Playwright + GitHub Actions — Guia Passo a Passo (do Zero)

Este README ensina você (e seus alunos do **Projeto Crescer – Qualidade de Software**) a:

1. **Criar um repositório do zero no GitHub**
2. **Inicializar um projeto de testes com Playwright**
3. **Executar os testes localmente**
4. **Rodar os testes no GitHub Actions**
5. **Publicar o relatório de testes no GitHub Pages**
6. **Usar tags (@smoke, @regression) nos testes**

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
│   ├── github_actions_explicado.md
│   ├── publicando_relatorio_github_pages.md
│   └── usando_tags_nos_testes.md
├── tests/                        👈 Testes Playwright
│   └── example.spec.ts
├── playwright.config.ts
├── package.json
└── README.md
```

---

<details>
<summary><h2>1️⃣ Criar o repositório no GitHub (do zero)</h2></summary>

1. Acesse **https://github.com/new**
2. **Repository name:** `github_actions`
3. **Visibility:** `Public`
4. ⚠️ **Importante:** _Não_ selecione criar README, .gitignore ou licença automático
5. Clique em **Create repository**
6. Copie a **URL HTTPS** do repositório (ex: `https://github.com/joprestes/github_actions.git`)

</details>

---

<details>
<summary><h2>2️⃣ Preparar o projeto local e conectar ao GitHub</h2></summary>

```bash
mkdir github_actions && cd github_actions
git init
git remote add origin https://github.com/{{seu_usuario}}/github_actions.git
```

Verifique com `git remote -v` para confirmar.

</details>

---

<details>
<summary><h2>3️⃣ Inicializar projeto Node e instalar Playwright</h2></summary>

```bash
npm init -y
npm create playwright@latest
```

### 💬 Respostas recomendadas no assistente (CLI)

| Pergunta CLI                                 | Resposta sugerida | Observação                                 |
| -------------------------------------------- | ----------------- | ------------------------------------------ |
| Do you want to use TypeScript or JavaScript? | **JavaScript**    | melhor para iniciantes                     |
| Where to put your end‑to‑end tests?          | **tests**         | fácil de lembrar                           |
| Add a GitHub Actions workflow?               | **Yes**           | já gera `.github/workflows/playwright.yml` |
| Install Playwright browsers?                 | **Yes**           | necessário pro CI e local                  |
| Install dependencies?                        | **Yes**           | deixa tudo pronto                          |
| Generate example tests?                      | **Yes**           | ajuda a validar logo `npx playwright test` |

</details>

---

<details>
<summary><h2>4️⃣ Primeiro commit e push</h2></summary>

```bash
git add .
git commit -m "feat: projeto inicial com Playwright e workflow do GitHub Actions"
git branch -M main
git push -u origin main
```

</details>

---

<details>
<summary><h2>5️⃣ Executar testes localmente</h2></summary>

```bash
npx playwright test
npx playwright show-report
```

</details>

---

<details>
<summary><h2>6️⃣ Rodar no GitHub Actions (workflow com tag e deploy)</h2></summary>

Use este `playwright.yml` no caminho `.github/workflows/playwright.yml`.

Para entender **cada linha desse arquivo**, veja a documentação completa em:
👉 [docs/github_actions_explicado.md](docs/github_actions_explicado.md)

</details>

---

<details>
<summary><h2>7️⃣ Ativar o GitHub Pages (uma vez)</h2></summary>

1. Vá em **Settings → Pages**
2. Em **Build and deployment**, selecione **Source: GitHub Actions**
3. Salve
4. Após um funcionamento correto do workflow, o relatório estará disponível em:

```
https://seu-usuario.github.io/github_actions/
```

</details>

---

<details>
<summary><h2>🏷️ Usando tags nos testes Playwright</h2></summary>

As tags permitem **filtrar execuções** tanto localmente quanto no GitHub Actions.
Você pode marcar seus testes assim:

```ts
test("@smoke valida título da página", async ({ page }) => {
  await page.goto("https://playwright.dev/");
  await expect(page).toHaveTitle(/Playwright/);
});
```

E rodar apenas os testes marcados:

```bash
npx playwright test --grep "@smoke"
```

📖 Veja o guia completo em
➡️ [docs/usando_tags_nos_testes.md](docs/usando_tags_nos_testes.md)

</details>

---

<details>
<summary><h2>🧰 Problemas comuns</h2></summary>

- “divergent branches” ao `git pull` → use `git pull --rebase origin main`
- Workflow não dispara → verifique se `.github/workflows/playwright.yml` existe e foi enviado para `main`
- Relatório não aparece no Pages → confirme em **Settings → Pages** que a fonte é **GitHub Actions**
- Navegadores faltando no CI → garanta `npx playwright install --with-deps` antes dos testes
- Instabilidade em testes → adicionar `--retries=2` ou configurar no `playwright.config.ts`

</details>

---

## 📘 Documentação Técnica do Workflow

Quer entender o que **cada parte do arquivo `.yml` faz**?
➡️ Veja: [docs/github_actions_explicado.md](docs/github_actions_explicado.md)

Esse documento explica de forma **simples e passo a passo** o conteúdo do arquivo `playwright.yml`, ideal para quem está começando com GitHub Actions.

> 💡 **Para estudo:**
> O arquivo [`playwright_original.yml`](.github/workflows/playwright_original.yml) mostra a versão gerada automaticamente pelo Playwright, antes das personalizações feitas no projeto.

---

## ✨ Créditos

Este material foi criado por **Joelma Prestes** como parte do curso
**Projeto Crescer – Qualidade de Software**, com o objetivo de ensinar de forma prática e acessível o uso de **Playwright** e **GitHub Actions**.

> 💡 Material didático livre para uso educacional, com a devida menção à autora.
