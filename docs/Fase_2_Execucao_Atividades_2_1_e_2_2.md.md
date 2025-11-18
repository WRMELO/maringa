Perfeito, vou atuar como Planejador operacional agora: detalhar exatamente **o que o Agente deve fazer**, sem código, começando pelas primeiras atividades da Fase 2.

Vou te entregar um texto em Markdown que você pode salvar, por exemplo, como:

`Fase_2_Execucao_Atividades_2_1_e_2_2.md`

---

````markdown
# Fase 2 – Execução das Atividades 2.1 e 2.2  
## Foco: Inventário de `dados_iniciais` e Harmonização dos Manifestos

Este documento orienta o **Agente de Código** na execução prática das primeiras etapas da Fase 2:

- **Atividade 2.1** – Inventário completo da pasta `dados_iniciais`.
- **Atividade 2.2** – Leitura e harmonização dos manifestos já existentes.

> Importante: Nenhuma dessas atividades exige MinIO rodando.  
> Podem ser executadas apenas com o notebook ligado e o ambiente `ds-base` ativo.

---

## 2.1 – Inventário de `dados_iniciais` (Atividade 2.1)

### 2.1.1. Objetivo

Gerar um inventário estruturado de **todos os arquivos** presentes em:

```text
MARINGA/
  fase_1_diagnostico/
    dados/
      dados_iniciais/
        Consumo Fornos/
        Corridas/
        Dicionário/
        Eletrodo/
        Informações Diária/
        Supervisório Forno 4/
        Supervisório Forno 5/
````

Esse inventário será a base para:

- cruzar com os manifestos antigos;

- montar o `manifest_unificado` na sequência.

### 2.1.2. Local e nome do notebook

O Agente deve criar um notebook com o seguinte caminho e nome:

```text
fase_1_diagnostico/analise/Fase2_01_inventario_dados_iniciais.ipynb
```

Esse notebook faz **apenas** o inventário de arquivos em `dados_iniciais`.

### 2.1.3. Operações que o notebook deve executar

No ambiente `conda ds-base`:

1. Definir o diretório base do projeto:

    - `MARINGA/fase_1_diagnostico` como raiz lógica do trabalho.

2. Definir o diretório de dados a ser inventariado:

    - `dados/dados_iniciais/` (sempre relativo à raiz `fase_1_diagnostico`).

3. Fazer uma **varredura recursiva** em `dados/dados_iniciais/`, incluindo todas as subpastas, coletando para cada arquivo:

    - Caminho relativo ao diretório `fase_1_diagnostico`:

        - `path_local`  
            Ex.:  
            `dados/dados_iniciais/Supervisório Forno 4/supervisorio_F4_2023-01.csv`

    - Pasta-raiz imediata dentro de `dados_iniciais`:

        - `pasta_raiz`  
            Valores esperados:  
            `Consumo Fornos`, `Corridas`, `Dicionário`, `Eletrodo`, `Informações Diária`, `Supervisório Forno 4`, `Supervisório Forno 5`.

    - Nome do arquivo:

        - `nome_arquivo`  
            Ex.: `supervisorio_F4_2023-01.csv`

    - Extensão:

        - `extensao`  
            Ex.: `.csv`, `.xlsx`, `.xls`, `.txt`.

    - Tamanho:

        - `tamanho_bytes` (tamanho em bytes do arquivo).

    - Datas de sistema:

        - `data_modificacao` (timestamp de última modificação do arquivo, em formato ISO 8601, por exemplo: `2025-11-18T14:32:05`).

    - (Opcional, mas desejável) Identificador de integridade:

        - `hash_sha256` ou `hash_md5` do arquivo, se o cálculo não for muito pesado.  
            Se o cálculo for considerado caro neste momento, o Agente pode:

            - incluir a coluna, preenchendo `null` e registrar em comentário que o hash será preenchido posteriormente.

4. Montar um DataFrame com essas colunas, uma linha por arquivo encontrado.

5. Ordenar o inventário por:

    - `pasta_raiz`,

    - `nome_arquivo` (ou `path_local`), para legibilidade.

### 2.1.4. Saída esperada

O notebook deve gerar um arquivo CSV com o inventário:

```text
fase_1_diagnostico/dados/manifestos_inicial/inventario_dados_iniciais.csv
```

Com as colunas mínimas:

- `path_local`

- `pasta_raiz`

- `nome_arquivo`

- `extensao`

- `tamanho_bytes`

- `data_modificacao`

- (opcional) `hash_sha256` ou `hash_md5`

Além disso, o notebook deve:

- Exibir um **resumo** ao final (por exemplo: contagem de arquivos por `pasta_raiz`).

- Conter uma célula de texto inicial explicando que esse inventário será usado na Fase 2 para construir o `manifest_unificado`.

### 2.1.5. Checklist Atividade 2.1

- Notebook `analise/Fase2_01_inventario_dados_iniciais.ipynb` criado no repositório.

- Varredura recursiva de `dados/dados_iniciais/` implementada.

- Arquivo `dados/manifestos_inicial/inventario_dados_iniciais.csv` gerado com as colunas mínimas.

- Resumo (contagem por `pasta_raiz`) visualizado e validado pelo Owner.

Quando todos estes itens estiverem marcados, a Atividade 2.1 estará concluída.

---

## 2.2 – Leitura e harmonização dos manifestos existentes (Atividade 2.2)

### 2.2.1. Objetivo

Integrar e harmonizar os manifestos já existentes (da fase anterior de trabalho), preparando uma base intermediária que será cruzada com o `inventario_dados_iniciais`.

Arquivos relevantes (nomes e paths podem ser ajustados conforme o repositório real, mas a ideia é esta):

- `fase_1_diagnostico/dados/manifestos_inicial/manifest_index.csv`

- `fase_1_diagnostico/dados/manifestos_inicial/index_resumo_por_pasta.csv`  
    (ou outro local onde `index_resumo_por_pasta.csv` esteja; o Agente deve confirmar o path real).

### 2.2.2. Local e nome do notebook

O Agente deve criar um segundo notebook para esta atividade:

```text
fase_1_diagnostico/analise/Fase2_02_manifestos_existentes.ipynb
```

Este notebook faz **apenas**:

- leitura dos manifestos existentes,

- harmonização básica de colunas,

- geração de um `manifest_intermediario.csv`.

### 2.2.3. Operações que o notebook deve executar

1. Definir o diretório base:

    - `fase_1_diagnostico` como raiz lógica.

2. Ler os manifestos existentes:

    - `manifest_index.csv`

    - `index_resumo_por_pasta.csv`

    O Agente deve:

    - anotar no próprio notebook os caminhos efetivamente utilizados, por exemplo:

        - `dados/manifestos_inicial/manifest_index.csv`

        - `dados/analise/index_resumo_por_pasta.csv`

3. Inspecionar as colunas de cada manifesto e documentar (em células de texto ou print):

    - nomes das colunas encontradas;

    - tipos/semântica aparente (por exemplo: coluna que indica pasta, contagem de arquivos, comentários etc.).

4. Definir um **conjunto mínimo de colunas harmonizadas** para o `manifest_intermediario`, por exemplo:

    - `fonte_manifesto`

        - valor `manifest_index` ou `index_resumo_por_pasta` para indicar de onde veio a linha.

    - `pasta_raiz`

        - quando existir; caso contrário, inferir o melhor possível (por exemplo, a partir de uma coluna de caminho ou nome da pasta).

    - `path_local_base`

        - padrão ou prefixo de caminho que o manifesto se refere (por exemplo, `dados/dados_iniciais/Supervisório Forno 4/`).

    - `descricao_manifesto`

        - texto livre que esteja nos manifestos descrevendo a pasta ou grupo de arquivos.

    - `qtd_arquivos_reportada`

        - quando o manifesto trouxer contagem de arquivos por pasta.

    - `observacoes_manifesto`

        - campos de observação/comentário que já existirem.

    Observação: este é um nível **macro**, por pasta ou agrupamento, não ainda linha-a-linha por arquivo individual.

5. Montar um DataFrame `manifest_intermediario` combinando as informações dos dois manifestos originais:

    - uma linha por “entrada” de manifesto (por exemplo, uma pasta ou grupo, conforme a estrutura dos arquivos).

    - com as colunas harmonizadas definidas no passo anterior.

6. Salvar esse `manifest_intermediario` em:

```text
fase_1_diagnostico/dados/manifestos_inicial/manifest_intermediario.csv
```

### 2.2.4. Saída esperada

- Arquivo `manifest_intermediario.csv` que:

  - possua a coluna `fonte_manifesto` identificando a origem;

  - possua `pasta_raiz` e/ou `path_local_base` que possam ser cruzados com o inventário de arquivos da Atividade 2.1;

  - traga qualquer informação relevante de contagem, descrição ou observações dos manifestos originais.

- Notebook `Fase2_02_manifestos_existentes.ipynb` deve conter:

  - breve explicação textual (células Markdown) do que foi feito;

  - exibição de algumas linhas de cada manifesto original;

  - exibição de algumas linhas do `manifest_intermediario` final.

### 2.2.5. Checklist Atividade 2.2

- Notebook `analise/Fase2_02_manifestos_existentes.ipynb` criado.

- Caminhos reais de `manifest_index.csv` e `index_resumo_por_pasta.csv` confirmados e documentados no notebook.

- Estrutura das colunas de cada manifesto original inspecionada e registrada.

- Modelo de colunas harmonizadas definido (`fonte_manifesto`, `pasta_raiz`, `path_local_base`, etc.).

- Arquivo `dados/manifestos_inicial/manifest_intermediario.csv` gerado.

- Amostras do `manifest_intermediario` visualizadas e validadas pelo Owner.

Quando essa checklist estiver concluída, teremos:

- Inventário físico dos arquivos (`inventario_dados_iniciais.csv`).

- Panorama consolidado dos manifestos antigos (`manifest_intermediario.csv`).

Com isso, a próxima atividade (2.3) poderá então tratar da **atribuição de `dominio`, `forno` e `granularidade`** para cada arquivo, usando esses dois insumos.

```

---

Se você der o ok, na próxima rodada eu avanço como Planejador para a **Atividade 2.3** (como o Agente deve, em um terceiro notebook, usar o inventário + manifest_intermediario para preencher `dominio`, `forno` e `granularidade` arquivo a arquivo).
```
