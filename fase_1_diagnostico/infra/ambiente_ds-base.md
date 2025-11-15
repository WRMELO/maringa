# Ambiente conda `ds-base`

## Ativação

```bash
conda activate ds-base
```

## Python

- Versão: 3.11.14 (`conda run -n ds-base python --version`)

## Pacotes principais

| Pacote | Versão | Observação |
| --- | --- | --- |
| minio | 7.2.18 | Cliente para conexão com o servidor MinIO |
| pandas | 2.3.3 | Manipulação tabular |
| polars | 1.35.1 | Alternativa de alto desempenho para dados tabulares |
| notebook | 7.4.7 | Execução de notebooks Jupyter |

> Utilize `conda run -n ds-base <comando>` quando estiver fora do shell ativado.
