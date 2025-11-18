
Segue o MD enxuto, no mesmo padrão das atividades anteriores, para você salvar (por exemplo) como:

`fase_1_diagnostico/docs/Fase_2_Execucao_Atividade_2_4_manifest_unificado.md`

---

````markdown
# Fase 2 – Execução da Atividade 2.4  
## Construção do manifest_unificado.csv

Esta atividade usa o resultado da Atividade 2.3:

- `dados/manifestos_final/manifest_arquivos_classificados.csv`

como base para criar o **manifesto unificado** do data lake da Etapa 1.

O objetivo é produzir um arquivo:

- `dados/manifestos_final/manifest_unificado.csv`

com todas as colunas necessárias para:

- acompanhar o estado de cada arquivo no pipeline (local → MinIO → refinado → derivado);
- orientar a migração futura para os buckets `maringa-*`.

Nesta atividade, o foco é **somente local**. A sincronização com o MinIO será feita em atividade posterior, quando o servidor estiver em execução.

---

## 2.4.1. Notebook da Atividade 2.4

O Agente deve criar o notebook:

```text
fase_1_diagnostico/analise/Fase2_04_manifest_unificado.ipynb
````

Este notebook:

1. Lê `manifest_arquivos_classificados.csv`.
    
2. Enrica cada linha com campos de controle de pipeline.
    
3. Gera `manifest_unificado.csv` em `dados/manifestos_final/`.
    
4. Produz resumos agregados para validação (dominio, forno, status_pipeline).
    

---

## 2.4.2. Entrada oficial

Entrada única da atividade:

```text
fase_1_diagnostico/dados/manifestos_final/manifest_arquivos_classificados.csv
```

Este arquivo é o catálogo em nível de arquivo, já contendo, no mínimo:

- `path_local`
    
- `pasta_raiz`
    
- `pasta_raiz_canonica`
    
- `nome_arquivo`
    
- `extensao`
    
- `tamanho_bytes`
    
- `data_modificacao`
    
- `hash_sha256`
    
- `dominio`
    
- `forno`
    
- `granularidade`
    
- (opcionais) colunas vindas do manifest_intermediario:
    
    - `fonte_manifesto`
        
    - `qtd_arquivos_reportada`
        
    - `descricao_manifesto`
        
- `observacoes_*` (dominio / forno / granularidade)
    

O notebook deve tratar este arquivo como a **fonte de verdade por arquivo**.

---

## 2.4.3. Novas colunas do manifest_unificado

O Agente deve criar, no DataFrame carregado, as seguintes colunas adicionais:

1. `id_arquivo`
    
    - Identificador único por linha.
        
    - Pode ser:
        
        - um índice sequencial estável, ou
            
        - um hash derivado de `path_local` + `hash_sha256`.
            
    - Critério: deve ser reprodutível se o inventário não mudar.
        
2. `origem_fisica`
    
    - Situação atual da cópia “de trabalho” que o pipeline usa:
        
        - valor inicial padronizado: `local`  
            (todos os arquivos estão, por enquanto, somente em `dados_iniciais`).
            
3. `bucket_minio`
    
    - Nome do bucket em que o arquivo será armazenado no MinIO.
        
    - Nesta fase, pode ser preenchido com o valor alvo, mesmo que ainda não exista fisicamente no MinIO:
        
        - `maringa-raw` para todos os arquivos brutos desta etapa.
            
    - Na migração real, esta coluna será usada para confirmar que o objeto foi criado.
        
4. `path_minio`
    
    - Caminho lógico alvo no bucket MinIO, seguindo o padrão:  
        `<dominio>/<forno>/<granularidade>/<ano>/<mes>/<nome_arquivo>`
        
    - Nesta atividade, é aceitável:
        
        - deixar `<ano>/<mes>` como `0000/00` ou outro placeholder quando o período ainda não estiver disponível;
            
        - registrar esse fato em `observacoes_pipeline`.
            
    - Exemplo de preenchimento inicial sem período real:
        
        - `supervisorio/F4/minuto/0000/00/supervisorio_F4_2023-01.csv`
            
5. `status_pipeline`
    
    - Estado atual do arquivo dentro do pipeline.
        
    - Nesta fase, todos devem ser inicializados com:
        
        - `raw_local`
            
    - Valores futuros (não usados aqui, mas previstos):
        
        - `raw_minio`, `refined`, `derived`.
            
6. `fonte_manifesto`
    
    - Se já existir em `manifest_arquivos_classificados.csv`, apenas preservar.
        
    - Caso contrário, o Agente pode:
        
        - preencher com `consolidado_2_3` ou similar, indicando que a linha veio do inventário enriquecido.
            
7. `observacoes_pipeline`
    
    - Campo texto livre para observações específicas sobre o pipeline, por exemplo:
        
        - “path_minio com ano/mês placeholder”;
            
        - “granularidade nao_definida”;
            
        - “forno nao_definido”.
            

O restante das colunas deve ser carregado e mantido tal como está no arquivo de entrada.

---

## 2.4.4. Escrita do manifest_unificado

Após criar as colunas adicionais, o Agente deve:

1. Ordenar opcionalmente o DataFrame para legibilidade:
    
    - por `dominio`, `forno`, `pasta_raiz_canonica`, `nome_arquivo`.
        
2. Gravar o arquivo resultante em:
    

```text
fase_1_diagnostico/dados/manifestos_final/manifest_unificado.csv
```

3. (Opcional, mas desejável)  
    Gravar uma versão em formato colunar:
    

```text
fase_1_diagnostico/dados/manifestos_final/manifest_unificado.parquet
```

O `manifest_unificado.csv` será o arquivo de referência para:

- medir completude por domínio e período;
    
- planejar a migração para o MinIO (Fase 3);
    
- alimentar eventuais ferramentas externas (NotebookLM, etc.).
    

---

## 2.4.5. Resumos e validação

O notebook deve produzir, ao final, resumos agregados para validação rápida pelo Owner:

1. Contagem por `dominio`:
    
    - tabela com `dominio`, `qtd_arquivos`, `tamanho_total_bytes`.
        
2. Contagem por `forno` (incluindo `nao_definido`):
    
    - tabela com `forno`, `qtd_arquivos`, `tamanho_total_bytes`.
        
3. Contagem por `status_pipeline`:
    
    - nesta fase, deve mostrar 100% em `raw_local`.
        
4. Amostras:
    
    - algumas linhas (head) por `pasta_raiz_canonica`, incluindo as novas colunas (`id_arquivo`, `bucket_minio`, `path_minio`, `status_pipeline`), para inspeção.
        

Esses resumos são apenas para validação; não precisam ser salvos em arquivo separado.

---

## 2.4.6. Checklist da Atividade 2.4

-  Notebook `analise/Fase2_04_manifest_unificado.ipynb` criado.
    
-  `manifest_arquivos_classificados.csv` lido corretamente.
    
-  Colunas adicionais criadas:
    
    -  `id_arquivo`
        
    -  `origem_fisica`
        
    -  `bucket_minio`
        
    -  `path_minio`
        
    -  `status_pipeline`
        
    -  `fonte_manifesto` (preservada ou preenchida)
        
    -  `observacoes_pipeline`
        
-  Arquivo `dados/manifestos_final/manifest_unificado.csv` gerado.
    
-  (Opcional) `manifest_unificado.parquet` gerado.
    
-  Resumos por `dominio`, `forno` e `status_pipeline` produzidos e revisados pelo Owner.
    

Quando esta checklist estiver concluída, considera-se encerrada a construção local do `manifest_unificado`.  
A próxima atividade, em Fase 2 ou já como transição para a Fase 3, será:

- subir o `manifest_unificado` para o bucket `maringa-manifests` no MinIO,
    
- e a partir dele planejar a primeira movimentação controlada para `maringa-raw`.
    

```


```