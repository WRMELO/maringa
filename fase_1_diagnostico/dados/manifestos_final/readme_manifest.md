# Manifesto de dados — versão persistente

Este manifesto cataloga os arquivos em `dados_iniciais`, com colunas:
- file_name, relative_path, ext, type_guess, size_mb, last_modified
- forno_guess (detecção robusta, pega F* em qualquer posição do caminho)
- variable_guess (palavras-chave de domínio)
- n_cols, n_rows (para CSV)
- timestamp_column, has_timestamp, first_ts, last_ts, timespan_days (parse sem warnings via detecção BR/US/ISO)
- encoding_used (para CSV)
- linked_dictionary, link_confidence (matching por similaridade ao XLSX mais próximo)