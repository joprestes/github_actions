# 🏷️ Usando tags nos testes Playwright

As **tags** permitem identificar e agrupar testes para diferentes finalidades, como _smoke_, _regression_ ou _sanity_.
No **Playwright**, não há uma sintaxe especial de anotação (como no Cucumber), então as tags são adicionadas **dentro do nome do teste** e filtradas com o comando `--grep`.

---

## 🧩 Exemplo básico

```ts
// @ts-check
import { test, expect } from "@playwright/test";

// 🔹 Este teste é marcado como @smoke
test("@smoke deve abrir a página inicial e verificar o título", async ({
  page,
}) => {
  await page.goto("https://playwright.dev/");
  await expect(page).toHaveTitle(/Playwright/);
});

// 🔹 Este é um teste normal (sem tag)
test("verifica link de instalação", async ({ page }) => {
  await page.goto("https://playwright.dev/");
  await page.getByRole("link", { name: "Get started" }).click();
  await expect(
    page.getByRole("heading", { name: "Installation" })
  ).toBeVisible();
});
```

---

## 🧪 Executando testes com tags

Para executar somente os testes que possuem uma tag específica, use o comando `--grep`:

```bash
npx playwright test --grep "@smoke"
```

Isso executará apenas os testes que contenham `@smoke` no título.

### 🎯 Exemplos adicionais

| Ação                               | Comando                                    |
| ---------------------------------- | ------------------------------------------ | --------- |
| Executar somente `@smoke`          | `npx playwright test --grep "@smoke"`      |
| Executar `@smoke` **ou** `@sanity` | `npx playwright test --grep "@smoke        | @sanity"` |
| Executar tudo **exceto** `@wip`    | `npx playwright test --grep-invert "@wip"` |

---

## ⚙️ Integração com GitHub Actions

Você pode permitir que a tag seja escolhida no momento da execução do workflow no GitHub Actions.

Exemplo de trecho no seu arquivo `.github/workflows/playwright.yml`:

```yaml
on:
  workflow_dispatch:
    inputs:
      tag:
        description: "Tag de execução (ex: @smoke, @regression)"
        required: false
        default: ""

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: lts/*
      - name: Install dependencies
        run: npm ci
      - name: Install Playwright Browsers
        run: npx playwright install --with-deps
      - name: Run Playwright tests
        run: npx playwright test --grep "${{ github.event.inputs.tag || '' }}"
```

💡 Assim, quando você clicar em **Run workflow** no GitHub, poderá digitar a tag desejada (por exemplo, `@smoke`) e apenas aqueles testes serão executados.

---

## 🧱 Boas práticas

- Use nomes claros e consistentes: `@smoke`, `@regression`, `@sanity`, `@api`, `@ui`.
- Mantenha sempre a tag **no início do título do teste**, para facilitar a leitura e o filtro.
- Combine tags e filtros para rodar subconjuntos de testes no CI/CD.

---

## 📘 Referências úteis

- [Documentação oficial do Playwright - Filtering tests](https://playwright.dev/docs/test-cli#--grep-regexp)
- [Guia: Configurando execução por tag no GitHub Actions](./github_actions_explicado.md)
- [Publicando relatórios de testes no GitHub Pages](./publicando_relatorio_github_pages.md)
