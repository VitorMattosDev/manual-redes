# CLAUDE.md — Manual de Redes de Computadores

Projeto **Quarto book** (HTML + PDF) publicado no **GitHub Pages**, no mesmo
esquema dos *Manuais de Ciências*. Foco em **redes ópticas (FTTx/PON)** e
**rádio em áreas rurais**, do básico ao avançado.

---

## 0. BOOTSTRAP DO TOOLCHAIN (rodar ANTES do primeiro `quarto preview`)

As figuras são em **TikZ** e cada uma compila por `pdflatex` → `dvisvgm`.
Isso exige TeX instalado **mesmo só para gerar o HTML**. Faça o bootstrap por
conta própria caso o usuário não tenha feito:

```bash
# 1) instalar TinyTeX via Quarto
quarto install tinytex

# 2) achar o tlmgr (o install do TinyTeX NÃO o coloca no PATH da sessão)
TLMGR=$(command -v tlmgr || find "$HOME" -name tlmgr -type f 2>/dev/null | head -n1)

# 3) instalar os pacotes LaTeX usados pelas figuras
"$TLMGR" install standalone pgf pgfplots dvisvgm xcolor amsmath amsfonts
```

### ⚠️ PATH do TinyTeX é o blocker recorrente
A extensão chama `pdflatex`/`dvisvgm` **pelo nome**, e `quarto install tinytex`
**não** coloca o bin do TinyTeX no `PATH` da sessão. Sintoma: figura não
renderiza e `tikz.lua` falha com `imgdata` nil (por volta da linha 587).
**Não é pacote faltando — é PATH.** Antes de qualquer `quarto preview`/`render`,
preponha o bin do TinyTeX ao PATH:

- **Linux/macOS:** `export PATH="$HOME/.TinyTeX/bin/$(ls $HOME/.TinyTeX/bin):$PATH"`
- **Windows:** `export PATH="$HOME/AppData/Roaming/TinyTeX/bin/windows:$PATH"`

Só então rode `quarto preview` — aí o primeiro preview já renderiza as figuras.

---

## 1. FIGURAS TikZ — extensão com PATCHES LOCAIS

- A extensão fica em `_extensions/danmackinlay/tikz/` e está **com patches locais**
  (do kit `figuras-tikz-kit.zip` / `FIGURAS.md`).
- **NUNCA** rode `quarto add danmackinlay/tikz` nem `quarto update` — isso baixa o
  upstream **sem os patches** e quebra tudo. Se a pasta `_extensions/` não existir
  neste repo, **copie-a de um manual existente** (ex.: Manual de Física), não baixe.
- No `_quarto.yml`: filtro `danmackinlay/tikz` **ANTES** de `quarto`, e
  `tikz: svg-engine: dvisvgm`.

### Convenção de figura (sempre assim)
```markdown
::: {#fig-olt-splitter}
```{.tikz}
%%| filename: olt-splitter
%%| alt: Diagrama da OLT até o splitter na CTO
\begin{tikzpicture}
  % ... usar estilos prontos: curva, destaque, auxiliar, eixo, ponto, vetor
  % ... cores: manualblue, manualred, manualgreen, manualyellow, manualgray
\end{tikzpicture}
```
Legenda da figura vem AQUI, antes do fechamento.
:::
```
Referencie no texto com `@fig-olt-splitter`.

---

## 2. styles.css É UMA CAMADA SCSS

`styles.css` está listado em `theme: [cosmo, styles.css]`. No Quarto ≥ 1.9 ele é
tratado como **camada SCSS** e **exige um marcador de camada na primeira linha**;
sem ele TODO o render quebra com *"doesn't contain at least one layer boundary"*.
O arquivo já começa com:

```scss
/*-- scss:rules --*/
```

Mantenha esse marcador no topo. Não remova.

---

## 3. MERMAID NO PDF — chrome-headless-shell

O `index.qmd` tem um grafo **mermaid** (mapa de pré-requisitos). No render do PDF
no runner Ubuntu, o mermaid **trava** por falta de Chromium headless. A correção
já está no `.github/workflows/publish.yml`: um step
`run: quarto install chrome-headless-shell` **antes** do passo de render/publish.
(A ferramenta correta é **chrome-headless-shell**, não chromium.)

---

## 4. PUBLICAÇÃO NO GITHUB PAGES — bootstrap do branch gh-pages (1x)

`quarto-actions/publish@v2` **aborta se o branch `gh-pages` ainda não existe** no
remoto (erro circular), e `quarto publish gh-pages --no-render` também se recusa a
criá-lo. **Antes do primeiro push**, crie o branch órfão vazio uma única vez:

```bash
git push origin $(git commit-tree $(git hash-object -t tree /dev/null) -m 'init gh-pages'):refs/heads/gh-pages
```

Depois é só disparar o workflow normalmente. (Em Settings → Pages, aponte para o
branch `gh-pages`.)

---

## 5. COMMITS PELO BASH (especialmente no Windows)

Ao commitar pela ferramenta Bash, use flags `-m "..."` simples (uma por parágrafo).
**NÃO** use here-string do PowerShell (`@'...'@`) dentro do Bash — o `@` vaza para a
mensagem do commit e obriga um `git commit --amend` pra corrigir.

---

## 6. Estilo de conteúdo

- Português do Brasil, didático, do básico ao avançado.
- Sempre relacionar a teoria com a prática de um **provedor (ISP) de fibra + rádio
  rural**: exemplos com OLT, ONT, CTO, DIO, splitter, power budget, PPPoE, BGP etc.
- Cada capítulo: introdução curta → conceitos → diagrama TikZ quando ajudar →
  exemplo prático do provedor → resumo/checklist no fim.
- Termos técnicos no glossário (`capitulos/glossario.qmd`).
