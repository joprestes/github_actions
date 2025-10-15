# 🌐 Publicando o Relatório de Testes no GitHub Pages

Este documento explica, de forma detalhada e didática, **como o relatório HTML gerado pelo Playwright** é publicado automaticamente no **GitHub Pages**, tornando-o acessível publicamente após a execução do workflow.

---

## 🧩 1️⃣ O que é o GitHub Pages

O **GitHub Pages** é um recurso gratuito do GitHub que permite **hospedar sites estáticos** diretamente a partir de um repositório.
Neste projeto, usamos o Pages para **publicar o relatório de testes gerado pelo Playwright**.

> 💡 Em vez de baixar o relatório manualmente, o GitHub Pages cria um site com ele pronto para visualização.

Exemplo de URL:

```
https://joprestes.github.io/github_actions/
```

---

## ⚙️ 2️⃣ Como o pipeline faz a publicação

O arquivo `.github/workflows/playwright.yml` contém dois **jobs**:

1. **`test`** → executa os testes e gera o relatório.
2. **`deploy`** → publica o relatório no **GitHub Pages**.

### Estrutura resumida do workflow:

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npx playwright test
      - uses: actions/upload-artifact@v4
        with:
          name: playwright-report
          path: playwright-report/

  deploy:
    runs-on: ubuntu-latest
    needs: test
    if: always()
    steps:
      - uses: actions/download-artifact@v4
        with:
          name: playwright-report
          path: playwright-report
      - uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./playwright-report
          publish_branch: gh-pages
```

---

## 🧠 3️⃣ Explicando cada parte

### 🧩 `upload-artifact`

```yaml
- uses: actions/upload-artifact@v4
  with:
    name: playwright-report
    path: playwright-report/
```

Salva o relatório gerado como um **artefato temporário** dentro do workflow.
Esse artefato é usado no job seguinte (`deploy`).

---

### 🧩 `download-artifact`

```yaml
- uses: actions/download-artifact@v4
  with:
    name: playwright-report
    path: playwright-report
```

Recupera o artefato salvo anteriormente.
Ou seja, pega o relatório HTML e o disponibiliza para upload no Pages.

---

### 🚀 `peaceiris/actions-gh-pages`

```yaml
- uses: peaceiris/actions-gh-pages@v4
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./playwright-report
    publish_branch: gh-pages
```

Essa Action:

1. Cria (ou atualiza) a branch `gh-pages`
2. Publica nela o conteúdo da pasta `playwright-report`
3. Usa o token do próprio GitHub (`GITHUB_TOKEN`) para autenticação

Após isso, o **GitHub Pages** serve automaticamente os arquivos da branch `gh-pages`.

---

## 🧷 4️⃣ A branch `gh-pages` é criada automaticamente

Você **não precisa criar manualmente** a branch `gh-pages`.
A Action `peaceiris/actions-gh-pages` faz isso automaticamente na **primeira execução bem-sucedida** do pipeline.

> ⚠️ Certifique-se apenas de que o workflow rode pelo menos uma vez e o job `deploy` termine com sucesso.

---

## ⚙️ 5️⃣ Configurando o GitHub Pages no repositório

1. Vá até o seu repositório no GitHub
   👉 [https://github.com/joprestes/github_actions](https://github.com/joprestes/github_actions)

2. Acesse **Settings → Pages**

3. Em **Build and deployment**, configure:

   - **Source:** `Deploy from a branch`
   - **Branch:** `gh-pages`
   - **Folder:** `/ (root)`

4. Clique em **Save**

> 🔁 O GitHub cria (ou atualiza) o site automaticamente toda vez que o workflow roda.

---

## 🌍 6️⃣ Acessando o relatório publicado

Após a execução do workflow, o relatório estará disponível em:

👉 **https://joprestes.github.io/github_actions/**

> ⚠️ Pode levar de 1 a 2 minutos após o deploy para o Pages ficar ativo.

---

## 🧪 7️⃣ Testando o processo

Você pode disparar o workflow manualmente:

1. Vá até a aba **Actions**
2. Selecione o workflow **Playwright CI**
3. Clique em **Run workflow**
4. (Opcional) Informe uma **tag** de execução, como `@smoke`
5. Aguarde a execução e acesse o link do relatório

---

## 💬 8️⃣ Possíveis erros e soluções

| Problema                     | Causa                               | Solução                                    |
| ---------------------------- | ----------------------------------- | ------------------------------------------ |
| Página 404 no Pages          | Deploy ainda não processado         | Espere 1–2 min e atualize                  |
| Branch `gh-pages` não criada | Falha no job `deploy`               | Verifique logs do Actions                  |
| Relatório vazio              | Testes falharam ou pasta incorreta  | Confirme se `playwright-report` foi gerada |
| Permissão negada             | `GITHUB_TOKEN` ausente ou incorreto | Deixe o token padrão do Actions ativo      |

---

## 🧩 9️⃣ Dica para o README

Adicione no topo do seu `README.md` o link do relatório publicado:

```markdown
## 📊 Relatório mais recente

🔗 [Ver relatório de testes no GitHub Pages](https://joprestes.github.io/github_actions/)
```

---

## ✍️ Autor

**Jo Prestes**
Projeto Crescer — Qualidade de Software
📘 Aula: Integração Contínua com GitHub Actions e Playwright
