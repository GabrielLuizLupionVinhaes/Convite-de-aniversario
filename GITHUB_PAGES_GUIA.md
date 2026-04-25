# 📄 GitHub Pages — Guia de Referência

> Documento criado para consulta rápida antes e durante o desenvolvimento de projetos hospedados no GitHub Pages.

---

## O que é o GitHub Pages?

GitHub Pages é um serviço **gratuito de hospedagem de sites estáticos** diretamente a partir de um repositório GitHub. Ele serve arquivos HTML, CSS e JavaScript sem nenhum servidor de execução (runtime).

URL padrão gerada: `https://seu-usuario.github.io/nome-do-repositorio`

---

## ✅ O que você PODE fazer

### Hospedagem & Acesso
- Hospedar sites estáticos (HTML, CSS, JS puro)
- Domínio customizado (ex: `meusite.com`) com suporte completo
- HTTPS automático e gratuito (incluindo domínio customizado)
- Repositórios públicos no plano gratuito ✅ | Privados exigem plano pago

### Tecnologias & Frameworks Compatíveis

| Tipo | Exemplos | Como funciona |
|---|---|---|
| **HTML/CSS/JS puro** | Qualquer projeto estático | Funciona nativamente, só enviar os arquivos |
| **Jekyll** | Blogs, documentações | Suporte nativo — GitHub builda automaticamente |
| **React, Vue, Angular** | SPAs | Exportar estático + GitHub Actions |
| **Next.js** | Sites dinâmicos | `output: 'export'` + GitHub Actions |
| **Astro, Hugo, Eleventy** | Sites modernos | GitHub Actions para build |

### Dados & Conteúdo no Client-Side
- Arquivos JSON estáticos servidos como "banco de dados" (`/data/info.json`)
- Consumo de APIs externas via `fetch()` (limitado por CORS — veja abaixo)
- `localStorage` / `sessionStorage` para persistência local no navegador
- `IndexedDB` para dados mais complexos no cliente

---

## ❌ O que você NÃO pode fazer

### Servidor & Backend
- ❌ Nenhum código server-side (PHP, Python, Node.js, Ruby rodando no servidor)
- ❌ Nenhum banco de dados (MySQL, PostgreSQL, MongoDB, etc.)
- ❌ Nenhuma lógica de autenticação própria no servidor
- ❌ Sem WebSockets nativos no servidor

### Roteamento (Problema com SPAs)
- ❌ Rotas do tipo `/sobre`, `/contato` **retornam 404 ao dar F5**
- ✅ **Solução 1 (fácil):** Usar roteamento por hash — `/#/sobre`
- ✅ **Solução 2 (avançada):** Criar um `404.html` que redireciona para o `index.html` via `sessionStorage`

### Uso Comercial Proibido
- ❌ E-commerce, lojas virtuais, transações financeiras
- ❌ SaaS (Software como Serviço) comercial
- ❌ Coletar senhas, cartões de crédito ou dados sensíveis
- ✅ Monetização leve é permitida (links de doação, Patreon, Pix, etc.)

---

## 📏 Limites e Cotas

| Item | Limite |
|---|---|
| Tamanho do repositório | Recomendado até **1 GB** |
| Tamanho do site publicado | Máximo de **1 GB** |
| Arquivo individual | Aviso em >50 MB, bloqueio em >**100 MB** |
| Banda mensal | **100 GB/mês** (soft limit) |
| Builds por hora (branch direto) | **10 builds/hora** |
| Builds via GitHub Actions | **Sem limite de builds/hora** |
| Timeout do deploy | **10 minutos** |
| Sites por conta | **1 site de usuário** + 1 por repositório |

> **Nota:** Os limites de banda e tamanho são "soft limits" — o GitHub pode entrar em contato se você exceder muito, mas não bloqueia automaticamente na primeira vez.

---

## ⚠️ Armadilhas Comuns (Gotchas)

### 1. CORS ao usar APIs externas
Ao chamar uma API externa via `fetch()`, o navegador pode bloquear a resposta se a API não permitir sua origem.
- ✅ APIs públicas com CORS habilitado funcionam normalmente
- ❌ APIs sem CORS bloqueiam a requisição no navegador
- ✅ Para seus próprios dados, use arquivos JSON no repositório — sem CORS

### 2. Subdiretório muda o caminho base
Se o site está em `usuario.github.io/meu-projeto/`, os caminhos relativos mudam.
- Configure o `base` no framework (ex: `basePath` no Next.js, `base` no Vite)

### 3. Sem HTTPS misto
O GitHub Pages serve por HTTPS. Recursos `http://` são bloqueados pelo navegador.
- Sempre use `https://` em todas as referências externas.

### 4. Cache agressivo
Após um deploy, pode levar alguns minutos para as mudanças aparecerem no browser.

### 5. Sem redirecionamentos no servidor
Não há `.htaccess` ou configuração de servidor — redirecionamentos precisam ser feitos via JavaScript.

---

## 🔌 APIs Externas Compatíveis

| API | Uso | CORS |
|---|---|---|
| Open-Meteo | Clima | ✅ Gratuita |
| JSONPlaceholder | Dados de teste | ✅ |
| GitHub REST API | Dados do repositório | ✅ (limitado sem token) |
| Supabase | Banco de dados no cliente | ✅ SDK funciona no browser |
| Firebase / Firestore | Backend serverless | ✅ SDK funciona no browser |
| EmailJS | Envio de e-mail pelo cliente | ✅ Sem backend necessário |

> **Dica:** **Supabase** e **Firebase** são ótimas escolhas se o projeto precisar de persistência de dados real — fornecem backend gerenciado acessível diretamente do frontend.

---

## 🧰 Estrutura Recomendada

### Site simples (HTML + CSS + JS puro)
```
repositorio/
├── index.html
├── style.css
├── script.js
└── assets/
    └── imagens...
```
→ Só fazer push na branch `main` e ativar o Pages nas configurações do repositório.

### Projeto com React/Vue/Vite + GitHub Actions
```yaml
# .github/workflows/deploy.yml
name: Deploy to GitHub Pages
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm install
      - run: npm run build
      - uses: actions/deploy-pages@v4
```

### "Banco de dados" estático com JSON
```
repositorio/
└── data/
    ├── projetos.json
    └── config.json
```
→ Carregado via `fetch('/data/projetos.json')` — funciona perfeitamente.

---

## 📋 Checklist pré-projeto

- [ ] O projeto precisa de backend? → Usar Firebase/Supabase ou repensar a arquitetura
- [ ] Vai usar framework (React/Vue/Vite)? → Configurar GitHub Actions
- [ ] Vai ter roteamento multi-página? → Usar hash routing ou hack do `404.html`
- [ ] Vai consumir APIs externas? → Verificar se têm CORS habilitado
- [ ] Vai ter arquivos grandes (vídeos/imagens)? → Usar CDN externo
- [ ] Precisa de domínio customizado? → Configurar DNS + CNAME no repositório
