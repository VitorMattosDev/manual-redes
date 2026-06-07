# Manual de Redes de Computadores

Quarto book (HTML + PDF) publicado no GitHub Pages. Foco em redes ópticas
(FTTx/PON) e rádio rural, do básico ao avançado.

**📖 Leia online:** https://VitorMattosDev.github.io/manual-redes

## Pré-requisitos

- [Quarto](https://quarto.org) ≥ 1.5
- TeX (via `quarto install tinytex`) — necessário **mesmo só para o HTML**,
  porque as figuras TikZ compilam por `pdflatex` → `dvisvgm`.

## Setup local (uma vez)

```bash
quarto install tinytex
TLMGR=$(command -v tlmgr || find "$HOME" -name tlmgr -type f 2>/dev/null | head -n1)
"$TLMGR" install standalone pgf pgfplots dvisvgm xcolor amsmath amsfonts

# prepor o bin do TinyTeX ao PATH (a extensão TikZ chama pdflatex/dvisvgm pelo nome)
# Linux/macOS:
export PATH="$HOME/.TinyTeX/bin/$(ls $HOME/.TinyTeX/bin):$PATH"
# Windows (Git Bash):
# export PATH="$HOME/AppData/Roaming/TinyTeX/bin/windows:$PATH"
```

> A pasta `_extensions/danmackinlay/tikz/` está com **patches locais**.
> **Não** rode `quarto add`/`quarto update` nela — copie de um manual existente
> se ela não estiver presente.

## Preview

```bash
quarto preview
```

## Publicação no GitHub Pages

1. Crie o repositório no GitHub e faça o push da `main`.
2. **Antes do primeiro deploy**, crie o branch `gh-pages` órfão (o publish action
   aborta se ele não existir):

   ```bash
   git push origin $(git commit-tree $(git hash-object -t tree /dev/null) -m 'init gh-pages'):refs/heads/gh-pages
   ```

3. Em **Settings → Pages**, aponte para o branch `gh-pages`.
4. O workflow `.github/workflows/publish.yml` faz render + deploy a cada push na
   `main` (já instala TinyTeX, pacotes LaTeX e `chrome-headless-shell` para o
   mermaid no PDF).

## Estrutura

```
_quarto.yml            # config do book + filtros TikZ + formatos
index.qmd              # apresentação + mapa de pré-requisitos (mermaid)
styles.css             # tema (1ª linha = marcador SCSS obrigatório)
CLAUDE.md              # instruções para o Claude Code
capitulos/             # 59 capítulos + apêndices (config, regulatório) + glossário + referências
_extensions/danmackinlay/tikz/   # extensão TikZ COM patches locais
.github/workflows/publish.yml
```
