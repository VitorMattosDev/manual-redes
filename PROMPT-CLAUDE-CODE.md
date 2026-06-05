# PROMPT PARA O CLAUDE CODE — Manual de Redes de Computadores

> Cole este texto inteiro como primeira mensagem no Claude Code, dentro da pasta
> do repositório (que já contém o scaffold: `_quarto.yml`, `index.qmd`,
> `styles.css`, `CLAUDE.md`, `capitulos/` com stubs e o workflow).
> **Antes**, copie a pasta `_extensions/danmackinlay/tikz/` (com patches locais)
> de um manual existente — não baixe do upstream.

---

Você vai desenvolver um **Manual de Redes de Computadores** (Quarto book,
HTML + PDF, deploy no GitHub Pages), do básico ao avançado, em **português do
Brasil**, com foco na operação de um **provedor (ISP) de fibra óptica que também
atende por rádio em áreas rurais**. Leia o `CLAUDE.md` antes de tudo e siga-o à
risca — ele tem as armadilhas de toolchain, TikZ, SCSS, mermaid e publicação.

## Passo 0 — Bootstrap (faça você mesmo, não dependa de mim)
1. `quarto install tinytex`
2. Localize o `tlmgr` e instale: `standalone pgf pgfplots dvisvgm xcolor amsmath amsfonts`
3. **Preponha o bin do TinyTeX ao PATH** (a extensão TikZ chama `pdflatex`/`dvisvgm`
   pelo nome; sem isso o `tikz.lua` falha com `imgdata` nil ~linha 587).
4. Confirme que `_extensions/danmackinlay/tikz/` existe. Se não existir, **pare e
   me peça** para copiá-la de outro manual — **nunca** rode `quarto add`/`update`.
5. Rode `quarto preview` e confirme que o esqueleto renderiza com as figuras.

## Passo 1 — Conteúdo dos capítulos
Os 55 capítulos já existem como stubs em `capitulos/` (a estrutura está no
`_quarto.yml`). Substitua cada stub por conteúdo completo. **Trabalhe parte por
parte**, e ao final de cada parte rode `quarto preview` para validar render +
figuras antes de seguir. Faça **um commit por parte** (mensagem com `-m` simples).

### Padrão de cada capítulo
- Abertura curta (o que é, por que importa para o provedor).
- Conceitos do básico ao avançado, com profundidade crescente.
- Pelo menos **uma figura TikZ** quando um diagrama ajudar (topologia, caminho
  óptico OLT→splitter→CTO→ONT, zona de Fresnel, pilha de protocolos, etc.),
  usando a convenção do `CLAUDE.md` (`::: {#fig-...}` + bloco `{.tikz}` com
  `%%| filename:` e `%%| alt:`; estilos `curva/destaque/auxiliar/eixo/ponto/vetor`;
  cores `manualblue/red/green/yellow/gray`).
- Tabelas onde couber (ex.: atenuação por elemento, classes de splitter, janelas
  de transmissão, faixas de frequência de rádio).
- Uma caixa **"Na prática do provedor"** (use `::: {.callout-provedor}`) com o uso
  real: valores típicos de potência, exemplo de provisionamento, troubleshooting.
- **Resumo / checklist** ao final.
- Cálculos quando fizer sentido (orçamento de potência em dB, sub-redes/CIDR,
  cálculo de enlace de rádio, EIRP, zona de Fresnel).

### Profundidade nos fundamentos (Partes I, IV e V)
Trate os fundamentos como tópicos de primeira classe, não como aquecimento:
**OSI** (cap. 02) camada por camada com função, PDUs e exemplos; **TCP/IP e
encapsulamento** (cap. 03) mostrando o caminho de um pacote pela pilha; e o arco
de endereçamento da **Parte IV** — cabeçalho IPv4, classes/máscaras, e
principalmente **CIDR/VLSM/sumarização (cap. 16) com exercícios resolvidos passo
a passo** (cálculo de bits de rede/host, blocos, faixa utilizável, broadcast).
Em **transporte/aplicação** (Parte V), explique o 3-way handshake do TCP, portas,
e os protocolos de aplicação (DNS, DHCP, HTTP/HTTPS/TLS, SSH/SMTP/SNMP/NTP).
Sempre amarre ao provedor (ex.: planejamento de blocos para CGNAT, VLAN×sub-rede,
DNS recursivo do ISP).

### Ênfase no foco do provedor
Capriche também nas **Partes VI–X** (PON/FTTx, planta externa, medição
óptica, rádio rural e operação de ISP). Termos a cobrir com profundidade prática:
**Links, OLT, DIO, ONT/ONU, CTO/CEO, splitter (balanceado e desbalanceado),
rede primária/secundária, drop, cabo AS, power budget, OTDR, fusão, PPPoE, BNG,
RADIUS, CGNAT, BGP/ASN/IX, VLAN por cliente**. Use exemplos numéricos realistas
(ex.: orçamento GPON com splitter 1:8 e 1:8 em cascata, distâncias, dB por km,
perdas por conector/fusão/splitter).

### Glossário e referências
- Preencha `capitulos/glossario.qmd` com todas as siglas usadas.
- Adicione referências em `referencias.bib` e cite com `[@chave]`.

## Passo 2 — Validação final
- `quarto render` gerando **HTML e PDF** sem erros (atenção ao mermaid no PDF —
  o workflow já trata com `chrome-headless-shell`; localmente, se travar, é o
  Chromium headless).
- Cheque que todas as `@fig-...` e `@sec-...`/`[@ref]` resolvem.

## Passo 3 — Publicação
Siga o `CLAUDE.md`: se o branch `gh-pages` ainda não existir, crie o órfão vazio
**antes** do primeiro deploy, depois deixe o workflow publicar. Em
`_quarto.yml`/`README.md`, troque `SEU-USUARIO` pelo usuário real do GitHub.

---

**Comece agora pelo Passo 0 e, em seguida, desenvolva a Parte I inteira. Ao
terminar a Parte I, rode o preview, faça o commit e me avise antes de seguir para
a Parte II** (assim eu acompanho o tom e o nível de profundidade).
