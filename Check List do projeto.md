## Painel de controle do plano (Fases 0–5) — checklist única

Pelo estado que você descreveu, **somente a Fase 0 está concluída (tarefas 0.1 e 0.2)**. Todo o restante está **definido em documento**, mas **não executado**.

Placar (em atividades): **21 no total; 2 feitas; 19 faltando**.

### Checklist (uma linha por atividade)

|ID|Fase|Atividade|Status|Dependências / pré-requisitos|Entradas (mínimas)|Saídas / artefatos esperados|
|--:|:--|:--|:--|:--|:--|:--|
|0.1|0|Registrar princípios SSOT como data lake (MinIO + catálogo; RAW/REFINADA/DERIVADOS; granularidades)|FEITO|—|Discussões/decisões do projeto|Documento de referência (Fase 0) atualizado|
|0.2|0|Vincular o plano à Etapa 1 (Relatório de Completude e Usabilidade; não é modelo final de ML)|FEITO|—|Decisão do escopo da Etapa 1|Documento de referência (Fase 0) com objetivo/escopo explícitos|
|1.1|1|Preparar ambiente `conda ds-base` (criar/validar pacotes mínimos)|NÃO INICIADO|Acesso ao conda; decisões de pacotes mínimos|Ambiente local; repo `MARINGA/`|`ds-base` pronto e ativável para notebooks/scripts do projeto|
|1.2|1|Instalar e subir servidor MinIO (método definido; endpoint; credenciais fora do repo)|NÃO INICIADO|DECISÃO OWNER: método (docker/binário/serviço)|Máquina/ambiente; parâmetros lógicos|MinIO operacional e acessível via URL|
|1.3|1|Criar buckets (`maringa-raw`, `maringa-refined`, `maringa-derived`, `maringa-manifests`)|NÃO INICIADO|1.2 concluída|Conexão/credenciais MinIO|Buckets criados e listáveis|
|1.4|1|Validar comunicação `ds-base` ↔ MinIO (listar buckets; criar/apagar objeto teste)|NÃO INICIADO|1.1 + 1.2 + 1.3|`ds-base` ativo; MinIO no ar|Evidência de teste (notebook/script) e confirmação leitura/escrita|
|1.5|1|Planejar migração inicial `dados_iniciais` → `maringa-raw` (dry-run; regras de path/granularidade)|NÃO INICIADO|1.4 concluída (recomendado)|Estrutura local `dados_iniciais/`; convenções de path|Diretrizes de migração + definição de “modo simulação”|
|2.1|2|Inventário local de `dados_iniciais` → `inventario_dados_iniciais.csv`|NÃO INICIADO|— (MinIO não é obrigatório aqui)|`fase_1_diagnostico/dados/dados_iniciais/`|`dados/manifestos_inicial/inventario_dados_iniciais.csv`|
|2.2|2|Harmonizar manifestos existentes → `manifest_intermediario.csv`|NÃO INICIADO|— (MinIO não é obrigatório aqui)|`manifestos_inicial/` e `manifestos_final/`|`dados/manifestos_inicial/manifest_intermediario.csv`|
|2.3|2|Classificar domínio/forno/granularidade (regras) e gerar base classificada|NÃO INICIADO|2.1 + 2.2|`inventario_dados_iniciais.csv` + `manifest_intermediario.csv`|`manifest_arquivos_classificados.csv` (insumo formal para 3.1)|
|2.4|2|Construir `manifest_unificado` local (status inicial `raw_local`; minio vazio/placeholder)|NÃO INICIADO|2.3|Base classificada|`dados/manifestos_final/manifest_unificado.csv`|
|2.5|2|Publicar `manifest_unificado` no MinIO (`maringa-manifests`) e validar leitura|NÃO INICIADO|1.4 + 2.4|MinIO OK; `manifest_unificado.csv` local|Objeto no `maringa-manifests` + evidência de leitura via notebook|
|2.6|2|Atualização incremental do `status_pipeline` conforme migração/refino/derivados avançarem|NÃO INICIADO|2.5 (e início de migração/refino)|`manifest_unificado` versionado|`manifest_unificado` atualizado com `raw_minio/refined/derived` + paths|
|3.1|3|Definir/diagnosticar tabela lógica por domínio e granularidade (iniciar por Consumo Fornos: delimitador/schema)|NÃO INICIADO|2.3 (recomendado: 2.4)|`manifest_arquivos_classificados.csv` + `dados_iniciais/`|Notebook `Fase3_01...ipynb` + `consumo_fornos_schema_diagnostico.csv`|
|3.2|3|Padronização de colunas/tipos/timestamps/unidades nas tabelas refinadas por domínio|NÃO INICIADO|3.1|Esquemas/diagnósticos por domínio|Tabelas refinadas por domínio (arquivos padronizados)|
|3.3|3|Registro no manifesto do que foi refinado (lineage RAW→REFINADO; paths em `maringa-refined`)|NÃO INICIADO|3.2 + (MinIO OK)|Tabelas refinadas geradas|`manifest_unificado` com `status_pipeline=refined` + `path_minio` preenchido|
|4.1|4|Agregações minuto→hora (média/min/max/std; n_valid; cobertura; flags)|NÃO INICIADO|3.2 (dados em minuto refinados)|Tabelas refinadas em minuto|Tabelas horárias derivadas (por forno/domínio)|
|4.2|4|Construção de 1–2 “tabelões” derivados por hora e registro no manifesto|NÃO INICIADO|4.1|Tabelas horárias derivadas|“Tabelões” em `maringa-derived` + `manifest_unificado` com `derived`|
|5.1|5|Métricas de completude por fonte/tabela (cobertura temporal; % preenchimento campos essenciais)|NÃO INICIADO|3.2 (mínimo)|Tabelas refinadas + critérios de campos essenciais|Tabelas/relatórios de completude por fonte/forno/período|
|5.2|5|Métricas de completude cruzada (integração entre fontes via tabelões)|NÃO INICIADO|4.2|Tabelões por hora/corrida|Métricas de interseção (horas/corridas “utilizáveis”) por tipo de modelo|
|5.3|5|Relatório final da Etapa 1 (texto corrido + gráficos/tabelas)|NÃO INICIADO|5.1 + 5.2|Resultados de completude|Relatório `.md` + figuras exportadas|

### Contagem final

- Atividades totais: **21**
    
- Concluídas: **2** (0.1, 0.2)
    
- Restantes: **19**
    

Se você quiser que eu já “trave” o caminho crítico para terminar (sem mudar nada do plano), ele fica: **1.1→1.4**, **2.1→2.5**, **3.1**, **3.2**, **4.1→4.2**, **5.1→5.3**.