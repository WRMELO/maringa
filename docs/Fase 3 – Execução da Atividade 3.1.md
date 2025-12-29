# Fase 3 – Execução da Atividade 3.1  

## Diagnóstico e parsing correto do domínio Consumo Fornos

Esta atividade inicia a Fase 3 com foco em **refino por domínio**, começando por:

- **Consumo Fornos**

Ela NÃO muda ainda a estrutura definitiva de tabelas, mas:

1. Resolve o problema de **delimitador** nos arquivos de consumo.
2. Verifica encoding básico.
3. Documenta o esquema (colunas, tipos, datas) de forma consistente.
4. Prepara o caminho para a geração da tabela refinada de consumo em etapa posterior.

---

## 3.1.1. Notebook da Atividade 3.1

O Agente deve criar (ou continuar, se já existir) o notebook:

```text
fase_1_diagnostico/analise/Fase3_01_diagnostico_consumo_fornos.ipynb
````

Este notebook tem escopo EXCLUSIVO no domínio **Consumo Fornos**.

---

## 3.1.2. Entradas oficiais

O notebook deve usar:

1. O catálogo em nível de arquivo:

```text
fase_1_diagnostico/dados/manifestos_final/manifest_arquivos_classificados.csv
```

2. A raiz de dados:

```text
fase_1_diagnostico/dados/dados_iniciais/
```

Filtrando apenas linhas de `manifest_arquivos_classificados.csv` com:

- `pasta_raiz_canonica == "Consumo Fornos"`

- (opcional) `dominio == "consumo_fornos"` como checagem adicional.

---

## 3.1.3. Objetivos da Atividade 3.1

Para o domínio **Consumo Fornos**:

1. **Detectar e ajustar o delimitador correto** dos arquivos CSV (ex.: `;` em vez de `,`).

2. Confirmar a **consistência de colunas** entre arquivos (mesma estrutura ou variações).

3. Registrar possíveis problemas:

    - colunas únicas gigantes (indicando parsing errado),

    - encoding suspeito,

    - nomes de colunas irregulares.

4. Produzir um **relatório técnico estruturado** que servirá de base para:

    - definir a tabela refinada (`consumo_fornos_refined`) em etapa posterior.

---

## 3.1.4. Passos que o notebook deve executar

### Passo 1 – Selecionar os arquivos de Consumo Fornos

- Carregar `manifest_arquivos_classificados.csv`.

- Filtrar somente linhas com:

  - `pasta_raiz_canonica == "Consumo Fornos"`.

Criar um DataFrame `consumo_manifest` com pelo menos:

- `path_local`

- `nome_arquivo`

- `extensao`

- `tamanho_bytes`

- `data_modificacao`

- `hash_sha256`

- `dominio`

- `forno`

- `granularidade` (atualmente deve estar `nao_definida` ou similar)

- demais colunas úteis.

Mostrar um resumo:

- quantidade de arquivos de consumo,

- tamanho total,

- lista de `nome_arquivo`.

### Passo 2 – Diagnóstico de delimitador

Para um subconjunto representativo (por exemplo, 2 arquivos por forno ou por ano) e depois, se viável, para todos:

1. Testar leitura com diferentes `sep`:

    - `,`

    - `;`

2. Verificar:

    - número de colunas resultantes,

    - se a primeira linha parece header,

    - se há uma “coluna única gigante”.

Critério de delimitador “aceito”:

- leitura que produza **mais de 1 coluna**,

- header com nomes “razoáveis” (não uma string inteira colada),

- número de linhas > 0.

O notebook deve:

- registrar, para cada arquivo testado, qual `sep` funcionou melhor;

- se possível, convergir para uma regra única (por exemplo, “todos os arquivos de consumo usam `sep=";"`”).

Se algum arquivo divergir (delimitador diferente), registrar isso em uma tabela de “exceções”.

### Passo 3 – Leitura consolidada com delimitador correto (amostra)

Definir, no notebook, a **regra oficial de delimitador** para Consumo Fornos, por exemplo:

- `sep=";"` e `decimal=","` (se for o caso; se não estiver claro, manter apenas `sep` correto e observar os tipos).

Aplicar essa regra a um conjunto de arquivos (idealmente todos, ou pelo menos um por ano/forno) e:

- concatenar em um DataFrame de amostra `consumo_amostra`.

- mostrar:

  - `consumo_amostra.head()`,

  - `consumo_amostra.dtypes`,

  - contagem de colunas.

Registrar:

- colunas que parecem ser data/hora (nomes típicos: `Data`, `Hora`, etc.),

- colunas que parecem ser valores de consumo (ex.: `Consumo_kWh`, `MWh`, etc., conforme o que aparecer).

### Passo 4 – Esquema preliminar

A partir de `consumo_amostra`, o notebook deve produzir um **esquema preliminar** com:

- nome_da_coluna

- dtype_inferido (tipo do Pandas)

- observacao_esquema (por exemplo: “parece ser data”, “parece consumo em kWh”, “flag booleana”, etc.)

Salvar esse esquema em:

```text
fase_1_diagnostico/dados/analise/consumo_fornos_schema_diagnostico.csv
```

Este arquivo é apenas de diagnóstico, não é ainda a tabela refinada.

### Passo 5 – Relatório interno (Markdown no próprio notebook)

No final do notebook, adicionar uma célula Markdown com uma síntese:

- delimitador adotado para o domínio Consumo Fornos;

- se há exceções de arquivos com delimitador diferente;

- número de colunas e principais campos identificados;

- eventuais problemas:

  - colunas com tudo nulo,

  - colunas duplicadas,

  - diferenças entre anos ou fornos.

---

## 3.1.5. Saídas esperadas da Atividade 3.1

1. Notebook:

```text
fase_1_diagnostico/analise/Fase3_01_diagnostico_consumo_fornos.ipynb
```

com:

- lógica para:

  - filtrar arquivos de Consumo Fornos,

  - testar delimitadores,

  - ler amostras com o delimitador correto,

  - gerar esquema de diagnóstico,

  - registrar observações.

2. Arquivo de esquema:

```text
fase_1_diagnostico/dados/analise/consumo_fornos_schema_diagnostico.csv
```

contendo:

- `nome_coluna`

- `dtype_inferido`

- `observacao_esquema`

3. Evidência no notebook de que:

- o delimitador está corretamente definido para a maior parte (ou todos) os arquivos;

- quaisquer exceções foram listadas em uma tabela.

---

## 3.1.6. Checklist da Atividade 3.1

- `consumo_manifest` criado filtrando `pasta_raiz_canonica == "Consumo Fornos"`.

- Delimitador testado com múltiplos `sep` e regra oficial definida.

- Leitura de amostra com delimitador correto, com `head()` e `dtypes` revisados.

- Arquivo `dados/analise/consumo_fornos_schema_diagnostico.csv` gerado.

- Relatório em Markdown no final do notebook, descrevendo:

  - delimitador final,

  - exceções,

  - panorama do esquema.

Quando essa checklist estiver concluída e validada pelo Owner, a Atividade 3.1 estará encerrada.  
A etapa seguinte será definir, com base nesse diagnóstico, a estrutura da **tabela refinada de Consumo Fornos** (camada `maringa-refined`) e o processo de ingestão desses dados para o MinIO.

```

Quando você salvar esse `.md`, pode mandar para o Agente algo como:

> “Agente, leia `docs/Fase_3_Execucao_Atividade_3_1_diagnostico_consumo_fornos.md` e implemente a Atividade 3.1 no notebook `Fase3_01_diagnostico_consumo_fornos.ipynb`, conforme especificado.”
```
