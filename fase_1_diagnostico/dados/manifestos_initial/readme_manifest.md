# Manifesto inicial dos dados (inventário)

Este manifesto lista todos os arquivos encontrados em `dados_iniciais`, com metadados mínimos,
heurísticas leves de identificação de forno e variável, e uma sondagem rápida para CSVs (n_cols, timestamp).

## Colunas do `manifest_index.csv`
- file_id: hash curto do caminho relativo (chave estável para referência).
- relative_path: caminho relativo ao diretório `GRUPO MARINGÁ/FASE 1 - DIAGNÓSTICO/Dados`.
- file_name: nome do arquivo.
- ext: extensão do arquivo.
- size_mb: tamanho em megabytes.
- last_modified: timestamp de modificação (ISO 8601).
- type_guess: tipo inferido (csv, xlsx, txt, pdf, md, dicionario, supervisorio, other).
- forno_guess: tentativa de extrair F1/F2/F3/F4/F5 a partir do caminho/nome.
- variable_guess: tentativa de mapear a variável (Consumo, Corrida, Granulometria, etc.).
- n_cols: número de colunas inferido (apenas CSV; amostra nrows=50).
- has_timestamp: yes/no/uncertain (apenas CSV; heurística por nome/parse).
- timestamp_column: nome da coluna de tempo detectada (se houver).
- encoding_used: encoding usado na leitura amostral do CSV (se aplicável).
- notes: observações (ex.: erro ao ler CSV).

Próximas etapas sugeridas:
- Etapa B: gerar amostras `sample_data/*.csv` e esquemas em texto/markdown (sem JSON), para leitura direta pelo NotebookLM.
- Etapa C: consolidar dicionários de dados existentes em `data_dictionary_merged.md`.
- Etapa D: auditoria rápida de qualidade (nulos, tipos inconsistentes, outliers simples) e flags no manifesto.
