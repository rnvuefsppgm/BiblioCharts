# BiblioCharts

**Bibliometric Figure Generator**


Aplicação web para **análise bibliométrica e geração de figuras em nível de publicação Q1**. Ela recebe as exportações das principais bases científicas, une e deduplica os registros, e gera automaticamente gráficos prontos para artigos, além do diagrama PRISMA.

Tudo roda **no navegador, sem instalação e sem servidor**. Nenhum dado sai do seu computador.

---

## Sumário

1. [O que o app faz](#o-que-o-app-faz)
2. [Como abrir](#como-abrir)
3. [Tutorial passo a passo](#tutorial-passo-a-passo)
4. [Bases e formatos suportados](#bases-e-formatos-suportados)
5. [Figuras geradas](#figuras-geradas)
6. [Exportação das figuras e dos dados](#exportação-das-figuras-e-dos-dados)
7. [Como funciona a deduplicação](#como-funciona-a-deduplicação)
8. [Estrutura do repositório](#estrutura-do-repositório)
9. [Dicas e solução de problemas](#dicas-e-solução-de-problemas)
10. [Licença](#licença)

---

## O que o app faz

| Etapa | O que acontece |
|---|---|
| **Ingestão** | Lê exportações de Web of Science, Scopus, Dimensions, OpenAlex, Lens.org, PubMed e Cochrane Library, ou um arquivo único já exportado pelo Bibliometrix. |
| **Padronização** | Converte cada registro para o esquema de **59 colunas do Bibliometrix** (AU, TI, SO, PY, TC, C1, DE, DI, SR…). Nomes de autores viram `SOBRENOME INICIAIS` em todas as bases, para que o mesmo autor seja reconhecido entre elas. |
| **Filtro temporal** | Restringe o corpus por ano de publicação (campo PY), com detecção automática do intervalo. |
| **Deduplicação** | Remove registros repetidos entre bases usando DOI, similaridade de título e autor + ano. Indica em quais bases cada documento apareceu. |
| **Métricas** | Calcula produção anual, produtividade de autores (contagem bruta e fracionada), instituições, periódicos, países, redes de coautoria internacional e citações. |
| **Figuras** | Gera 7 figuras configuráveis (Fig 0 a Fig 6) e o diagrama de fluxo **PRISMA**. |
| **Exportação** | Exporta figuras em PNG, JPEG ou SVG vetorial, de 96 a 1200 dpi, e o dataset consolidado em XLSX. |

---

## Como abrir

**Opção 1, mais simples:** baixe o arquivo [`bibliometric_app.html`](bibliometric_app.html) e dê dois cliques nele. Ele abre no seu navegador padrão (Chrome, Edge ou Firefox recentes).

**Opção 2, código separado:** abra [`src/index.html`](src/index.html). É a mesma aplicação, com HTML, CSS e JavaScript em arquivos separados para facilitar a leitura e a manutenção.

> É preciso conexão com a internet na primeira abertura, para carregar as bibliotecas Chart.js, D3, TopoJSON, SheetJS e as fontes. Os seus dados nunca são enviados para nenhum servidor.

---

## Tutorial passo a passo

### Passo 1 · Exporte os dados das bases

Faça a busca em cada base e exporte **todos os campos**, incluindo referências citadas quando a base oferecer. Veja na seção [Bases e formatos suportados](#bases-e-formatos-suportados) o formato recomendado para cada uma.

### Passo 2 · Escolha o modo de carregamento

A página **Data Ingestion** tem dois caminhos.

**A) Load Bibliometric Dataset.** Use quando você já tem **um único arquivo** exportado do Bibliometrix (`.xlsx`, `.csv` ou `.bib`). Arraste o arquivo para a área tracejada. Os dados são processados na hora e as figuras ficam liberadas. Esse caminho não faz deduplicação.

**B) Stage 1: Ingestion & Deduplication.** Use quando você tem **exportações brutas de uma ou mais bases**.

1. Arraste os arquivos para a zona correspondente. Cada zona aceita vários arquivos.
   - **Scopus**
   - **Web of Science**
   - **Other databases**: Dimensions, OpenAlex, Lens.org, PubMed e Cochrane. A base é detectada automaticamente.
2. Depois do processamento, cada arquivo mostra a base detectada e quantos registros foram lidos, por exemplo `Dimensions · 250`.

### Passo 3 · Configure o filtro de anos (opcional)

No painel **Year Filter (PY field)**:

- Clique em **Auto-detect range** para descobrir o menor e o maior ano presentes nos arquivos. O filtro é ativado automaticamente.
- Ou digite o **Start year** e o **End year** e marque **Apply filter**.
- **Keep records without PY** decide se registros sem ano ficam no corpus.

### Passo 4 · Escolha a estratégia de deduplicação

Por padrão os três critérios ficam ativos: **DOI matching**, **Title similarity** e **Authors + Year**. Desmarque algum se quiser um critério mais conservador.

### Passo 5 · Processe

Clique em **Process Files**. Uma barra de progresso mostra as quatro etapas: leitura, metadados, detecção de duplicatas e mesclagem. Ao final aparecem:

- Cartões com registros por base, registros filtrados por ano, duplicatas removidas e tamanho do dataset final.
- Uma **prévia** dos primeiros 50 registros, com a coluna **DB origin** indicando a base, ou bases, de cada documento.
- Os botões de exportação do dataset.
- O **Dataset Summary**: documentos, autores, periódicos, período, total de citações e média de citações por documento.

### Passo 6 · Ajuste as configurações globais

Logo abaixo do resumo, em **Global Settings**:

- **Top N** de autores, instituições, periódicos e documentos citados, de 5 a 50.
- **Font Size** das figuras.
- **Export Resolution**: 96, 150, 300, 450, 600 ou 1200 dpi. Para publicação use 300 dpi ou mais.
- **Color Palette**: Blue, Teal/Green, Purple ou Warm.
- **Export Format**: PNG ou JPEG.

Clique em **Generate All Figures**.

### Passo 7 · Explore e refine cada figura

Use o menu lateral para abrir cada figura. Cada página tem um painel **Settings** próprio, com Top N, tamanho de fonte, largura e altura dos painéis e paleta de cores independente para cada painel. O gráfico se atualiza na hora.

### Passo 8 · Exporte

Use os botões acima de cada figura para baixar painéis individuais ou combinados. Veja a seção [Exportação](#exportação-das-figuras-e-dos-dados).

### Passo 9 · Monte o diagrama PRISMA

Abra **PRISMA Diagram** no menu, em **Tools**:

1. Clique em **adicionar base** para cada base pesquisada e informe o nome, a string de busca e o número de registros recuperados.
2. Adicione as **etapas de filtragem**: duplicatas, triagem por título e resumo, elegibilidade e assim por diante, com a fase e o número de registros excluídos.
3. Os totais são recalculados automaticamente. Gere o diagrama e baixe em SVG ou PNG.

> Dica: os números do Stage 1 (registros por base, filtrados por ano e duplicatas removidas) servem diretamente para preencher as primeiras caixas do PRISMA.

---

## Bases e formatos suportados

| Base | Formatos aceitos | Como exportar |
|---|---|---|
| **Web of Science** (WoS/WoK) | Plain text `.txt`, tab-delimited `.txt`, Excel `.xls/.xlsx`, BibTeX, RIS | Export → *Plain text file*, *Tab delimited*, *Excel* ou *BibTeX*, com **Full Record and Cited References** |
| **Scopus** | CSV, BibTeX, RIS | Export → CSV ou BibTeX, todos os campos |
| **Dimensions** | CSV, Excel `.xlsx`, BibTeX | Export → *Export results for bibliometric mapping* ou *Export to BibTeX* |
| **OpenAlex** | CSV do site, JSON da API | openalex.org → Export CSV, ou resposta de `api.openalex.org/works?...` salva como `.json` |
| **OpenAlex API** (via openalexR) | CSV/XLSX gravado no R | `oa_fetch()` e depois `write.csv()`, ou `bibliometrix::convert2df(..., dbsource = "openalex_api")` |
| **Lens.org** | CSV, RIS, BibTeX | Export → CSV (Scholarly Works), todos os campos |
| **PubMed** | MEDLINE/PubMed `.txt` ou `.nbib`, CSV | Save → formato *PubMed* ou *MEDLINE*, ou CSV |
| **Cochrane Library** | Plain text `.txt`, RIS | Export selected citations → *Plain text* ou *RIS* |
| **Bibliometrix** | `.xlsx`, `.csv` já no esquema de 59 colunas | Aceito nos dois modos de carregamento |

Os arquivos de texto são identificados **pelo conteúdo**, não pela extensão. As planilhas são identificadas **pelos nomes das colunas**.

---

## Figuras geradas

| Figura | Conteúdo |
|---|---|
| **Fig 0 · Annual Scientific Production** | Publicações por ano com curva acumulada (painel A) e boxplots da distribuição por década (painel B). |
| **Fig 1 · Authors Productivity** | Autores mais produtivos: número bruto de artigos (esquerda) e contribuição fracionada, em que cada artigo vale 1 dividido pelo número de autores (direita). |
| **Fig 2 · Authors' Production over Time** | Linha do tempo horizontal dos autores principais. O tamanho da bolha é o número de artigos no ano e a cor é o total de citações por ano. Tem controle do intervalo de anos exibido. |
| **Fig 3 · Institutions Productivity** | Ranking das instituições mais produtivas, a partir das afiliações (C1). |
| **Fig 4 · Journal / Source Productivity** | Periódicos com maior número de artigos. |
| **Fig 5 · Country Collaboration Map** | Mapa-múndi com a produção por país e as linhas de coautoria internacional. O parâmetro **Min. Edges** define o número mínimo de colaborações para desenhar uma ligação. |
| **Fig 6 · Most Globally Cited Documents** | Três painéis: total de citações (TC), citações por ano e TC normalizado pela média do corpus. |
| **PRISMA Flow Diagram** | Diagrama de fluxo da revisão sistemática, edição bibliométrica. |

---

## Exportação das figuras e dos dados

**Figuras**

- **PNG** e **JPEG** na resolução escolhida em *Export Resolution*.
- **SVG** vetorial, ideal para editar no Inkscape, Illustrator ou PowerPoint sem perda de qualidade.
- **Combined PNG / Combined SVG**: junta os painéis de uma figura (A, B, C) em uma única imagem, com rótulos de painel e legenda.
- Botões específicos por painel, como *SVG Left*, *SVG Right*, *SVG TC*, *SVG TC/yr* e *SVG Norm*.

**Dados do Stage 1**

| Botão | Arquivo gerado |
|---|---|
| **Download XLSX** | Dataset deduplicado nas 59 colunas do Bibliometrix. Pode ser lido no R com `bibliometrix`. |
| **XLSX + DB Origin** | Mesmo dataset com a coluna `DB_Origin`, por exemplo `Scopus`, `WOS` ou `Scopus/WOS/Dimensions`. |
| **Duplicates Audit Log** | Cada registro removido e o motivo da remoção, para auditoria e para o PRISMA. |

---

## Como funciona a deduplicação

Um registro é considerado duplicado quando atende a **qualquer** um dos critérios ativos:

1. **DOI idêntico**, depois de normalizado. Só vale quando os dois registros têm DOI.
2. **Título com similaridade Jaro-Winkler de 92% ou mais** e mesmo ano de publicação.
3. **Mesma impressão digital**, formada pelo sobrenome do primeiro autor, ano e primeiras palavras do título, **e** título com similaridade de 80% ou mais.

Quando há duplicata, o registro mantido segue a prioridade **Scopus, depois WoS, depois as demais bases**. As bases de origem de todas as cópias ficam registradas na coluna `DB_Origin`.

---

## Estrutura do repositório

```
BiblioCharts/
├── bibliometric_app.html   # aplicação completa em um único arquivo (basta abrir)
├── src/                    # mesma aplicação com o código separado
│   ├── index.html          # estrutura das páginas
│   ├── css/styles.css      # estilos
│   └── js/app.js           # leitura das bases, deduplicação, métricas, figuras e exportação
└── README.md               # este tutorial
```

Bibliotecas usadas, carregadas por CDN: [Chart.js](https://www.chartjs.org/), [D3](https://d3js.org/), [TopoJSON](https://github.com/topojson/topojson) e [SheetJS](https://sheetjs.com/).

---

## Dicas e solução de problemas

- **"No records could be parsed"**: confira se o arquivo é a exportação original da base, sem edição no Excel. Arquivos `.RData` não são lidos; exporte do R como CSV ou XLSX.
- **Mapa vazio na Fig 5**: o mapa depende do campo de afiliação (C1). Exportações sem afiliação, como o CSV do PubMed, não geram países.
- **Autor aparece duplicado**: verifique a grafia na exportação original. O app padroniza para `SOBRENOME INICIAIS`, mas não consegue unir grafias realmente diferentes do sobrenome.
- **Figuras para revista**: use 300 dpi ou mais, ou exporte em SVG.
- **Arquivos grandes**: dezenas de milhares de registros funcionam, mas o processamento pode levar alguns segundos. Prefira Chrome ou Edge.

---

## Licença

Distribuído sob a licença [CC0 1.0 Universal](LICENSE).
