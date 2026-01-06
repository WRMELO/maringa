# Relatório de Adequações e Transformações para Avaliação do RAW (R2)

## Escopo e guardrails
- Somente leitura de `dados_iniciais`, sem publicação/migração para MinIO e sem criação de camadas refined/derived.
- Saídas mantidas em `3ª-Retomada/outputs/R2`.
- Kernel `ds-base`, notebook `RETOMADA_R2_diagnostico_raw_por_dominio.ipynb` executado integralmente.

## Principais adequações implementadas
- **Fallback de encoding**: tentativa sequencial `utf-8` → `latin-1` → `cp1252` com `errors='replace'` para garantir leitura de todos os CSV.
- **Detecção de delimitador**: heurística simples (contagem de `,`, `;`, `	`, `|`) por arquivo, armazenada em `r2_parse_audit.csv`.
- **Tratamento de cabeçalhos com aspas duplicadas** (domínio `Supervisorio Forno 4`): leitura com `quoting=csv.QUOTE_NONE` para preservar colunas e evitar quebra de parsing.
- **Normalização de datas**:
  - Limpeza de aspas em colunas temporais (`str.strip('"')`).
  - Conversão com `pd.to_datetime(..., errors='coerce', dayfirst=False, utc=True)` para strings ISO (`YYYY-MM-DDTHH:MM:SS...-04:00`), evitando inversões dia/mês.
  - Cálculo de `min_date` e `max_date` por arquivo (armazenados em `r2_parse_audit.csv`) e consolidação por domínio em `r2_date_coverage.csv`.
- **Barra de progresso**: `tqdm` texto em todos os loops de auditoria.
- **Impressão de amostras**: `head(20)` para cada DataFrame relevante conforme critério do plano.
- **Limpeza de outputs anteriores**: uso de `nbconvert --ClearOutputPreprocessor` para remover widgets e evitar erro de renderer.

## Transformações efetivas nos dados (em memória, não persistidas)
- Conversão de strings de data/hora para `datetime64[ns, UTC]` apenas para fins de auditoria de cobertura temporal.
- Contagem de linhas/colunas e registro de colunas identificadas por arquivo.
- Nenhuma gravação altera os arquivos originais; apenas metadados e relatórios foram escritos em `outputs/R2`.

## Artefatos gerados/atualizados
- `r2_file_index.csv`: índice de 145 arquivos manifestados com checagem de presença em `RAW_DIR`.
- `r2_parse_audit.csv`: 124 CSV com status `ok`, delimitador, encoding usado, colunas, min/max de datas quando aplicável.
- `r2_domain_summary.csv`, `r2_forno_summary.csv`: agregações por domínio/forno.
- `r2_date_coverage.csv`: cobertura temporal por domínio.
- `r2_summary.json` e `r2_report.md`: sumário geral da execução.

## Caso específico: Supervisorio Forno 4
- Problema identificado: cabeçalho com aspas duplicadas e parsing prévio retornando datas incorretas (1969/1970).
- Ajuste aplicado: `quoting=csv.QUOTE_NONE` + limpeza de aspas + parsing `utc=True, dayfirst=False`.
- Resultado correto:
  - `F4_2024_1S.csv`: min 2024-01-01 04:00:00+00:00; max 2024-07-01 03:58:00+00:00.
  - `F4_2024_2S.csv`: min 2023-09-29 04:00:00+00:00; max 2025-01-02 03:58:00+00:00.
  - Cobertura consolidada do domínio: 2023-09-29 04:00:00+00:00 a 2025-01-02 03:58:00+00:00.

## Conclusão sobre padronização
- **Consistência de leitura**: Após fallback de encoding, detecção de delimitador e tratamento de aspas, todos os 124 CSV foram lidos com sucesso (`ok=124`, `fail=0`).
- **Datas**: Há heterogeneidade de formatos (ISO com offset, uso de aspas), mas o parsing parametrizado garantiu min/max coerentes. Contudo, a coexistência de offsets e a ausência de timezone explícito padronizado indicam necessidade de normalização posterior.
- **Estrutura**: Predominância de CSV delimitados por vírgula; alguns domínios trazem cabeçalhos/campos com aspas duplicadas. Não há schema único global.
- **Para Data Science**: O sistema é auditável e os dados estão legíveis após ajustes leves, mas não estão totalmente padronizados. Recomendam-se etapas adicionais de normalização (timezone único, nomenclatura de colunas, tipos explícitos) antes de modelagem ou integração cruzada.

## Recomendações imediatas
- Congelar a lógica de leitura (encoding fallback, delimitador, quoting) para reuso em pipelines posteriores.
- Padronizar timezone em UTC já na ingestão; registrar formato e origem do offset.
- Criar dicionário de campos por domínio para harmonizar nomes e unidades antes de análises ou modelos.
