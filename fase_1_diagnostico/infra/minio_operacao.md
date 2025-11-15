# Operação do MinIO em Docker

## Pré-requisitos

1. Docker Engine + Docker Compose Plugin instalados e ativos.
2. Arquivo `infra/env/minio_credentials.env` contendo pelo menos `MINIO_ROOT_USER` e `MINIO_ROOT_PASSWORD` (já fora do Git).

## Inicialização do servidor

Os comandos devem ser executados a partir da raiz do repositório:

```bash
cd fase_1_diagnostico/infra/minio
docker compose up -d
```

- API: `http://localhost:${MINIO_SERVER_PORT:-9000}`
- Console: `http://localhost:${MINIO_SERVER_CONSOLE_PORT:-9001}`

## Finalização

```bash
cd fase_1_diagnostico/infra/minio
docker compose down
```

## Monitoramento

```bash
cd fase_1_diagnostico/infra/minio
docker compose logs -f
```

## Buckets oficiais

- `maringa-raw` – aterro inicial para ingestão de arquivos exatamente como recebidos.
- `maringa-refined` – dados após limpeza/normalização básica destinada aos analistas.
- `maringa-derived` – agregados, métricas ou artefatos produzidos pelas rotinas de diagnóstico.
- `maringa-manifests` – manifestos auxiliares (capítulos, índices e documentação gerada automaticamente).

Os buckets acima foram criados e verificados no notebook `fase_1_diagnostico/2ª tentativa/notebook/TESTE1.ipynb` usando o cliente MinIO local (`localhost:9000`).
