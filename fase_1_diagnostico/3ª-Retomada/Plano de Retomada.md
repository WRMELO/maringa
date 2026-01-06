
Sim — **altera** o Painel de controle, por dois motivos objetivos:

1. **Status real mudou**: o Gate 0 comprovou que **MinIO/Docker/buckets/credenciais existem e estão UP**, mas **sem objetos** (logo, “migração/publicação” não aconteceu).
    
2. **Prioridade contratual**: o caminho até o **Relatório RAW (entregável 1)** passa a ser **análise local do RAW + catálogo já existente**, e tudo que for “continuidade” (transferência para MinIO, refino, derivados, modelagem) fica **explicitamente condicionado ao aceite da Maringá**.
    

Abaixo está o **Painel de controle atualizado e congelado**, agora chamado **Plano de Retomada (v1.0)**.

---

## Plano de Retomada (v1.0) — Painel de controle Fases 0–5

Legenda de status:

- **CONFIRMADO**: evidência no Gate 0 / arquivos encontrados / serviço validado
    
- **NÃO INICIADO**
    
- **POSTERGADO (ACEITE MARINGÁ)**: só após aprovação formal de continuidade
    

|ID|Fase|Atividade|Status|Dependências|Saída esperada|Observação|
|--:|:-:|:--|:--|:--|:--|:--|
|0.1|0|Consolidação conceitual (SSOT/MinIO/camadas/granularidade)|CONFIRMADO|—|Doc base|Fase 0 fechada|
|0.2|0|Escopo Etapa 1 (Relatório RAW)|CONFIRMADO|—|Diretriz|Entregável 1 travado por contrato|
|G0|1|Auditoria técnica read-only (infra + buckets + artefatos)|CONFIRMADO|—|Relatório Gate 0|Docker UP, MinIO UP, buckets vazios|
|1.2|1|Subir/validar MinIO (serviço operacional)|CONFIRMADO|—|MinIO UP|Container `maringa-minio`|
|1.3|1|Buckets oficiais criados|CONFIRMADO|1.2|4 buckets|`raw/refined/derived/manifests` existem|
|1.4|1|Validar acesso via cliente (listar buckets)|CONFIRMADO|1.2–1.3|Evidência de listagem|Listagem OK; **0 objetos**|
|1.1|1|Ambiente `ds-base` conforme doc (conda)|**INCONCLUSIVO**|—|Evidência de ativação e libs|No Gate 0 houve cliente MinIO funcional, mas não ficou travado que foi via conda `ds-base`|
|1.5|1|Planejar migração inicial RAW→MinIO|POSTERGADO (ACEITE MARINGÁ)|Aceite|Plano de migração|Migração é continuidade|
|2.1|2|Inventário `dados_iniciais`|CONFIRMADO|—|`inventario_dados_iniciais.csv`|Confirmado presente|
|2.2|2|Harmonização manifestos legados|CONFIRMADO|—|`manifest_intermediario.csv`|Confirmado presente|
|2.3|2|Classificação domínio/forno/granularidade|CONFIRMADO|2.1–2.2|`manifest_arquivos_classificados.csv`|Confirmado presente|
|2.4|2|Manifesto unificado local|CONFIRMADO|2.3|`manifest_unificado.csv`|Confirmado presente|
|2.5|2|Publicar manifesto no MinIO (`maringa-manifests`)|POSTERGADO (ACEITE MARINGÁ)|Aceite|Objeto no MinIO|Bucket está vazio hoje|
|2.6|2|Atualização incremental `status_pipeline`|POSTERGADO (ACEITE MARINGÁ)|Aceite|Manifesto com lineage|Depende de publicar/migrar|
|**R1**|**2**|**Validar catálogo ↔ filesystem (coerência de paths e existência)**|**NÃO INICIADO**|2.4|Relatório de inconsistências|**Próximo passo imediato** para o Relatório RAW|
|**R2**|**3**|**Diagnóstico de leitura e qualidade do RAW por domínio (sem refino)**|**NÃO INICIADO**|R1|Tabelas de QA por domínio|Entra no Relatório RAW; não depende de MinIO|
|3.1|3|Consumo Fornos (delimitador/schema aprofundado)|NÃO INICIADO|R2|Evidência técnica + exceções|Pode ser parte do R2 se necessário|
|3.2|3|Padronização refinada (refined)|POSTERGADO (ACEITE MARINGÁ)|Aceite|Tabelas refinadas|Continuidade|
|3.3|3|Registro RAW→REFINED no manifesto|POSTERGADO (ACEITE MARINGÁ)|Aceite|lineage + paths|Depende de MinIO e refino|
|4.1|4|Agregações minuto→hora (derived)|POSTERGADO (ACEITE MARINGÁ)|Aceite|tabelas horárias|Continuidade|
|4.2|4|“Tabelões” derivados|POSTERGADO (ACEITE MARINGÁ)|Aceite|tabelões|Continuidade|
|**R3**|**5**|**Relatório RAW (qualidade + utilidade) — Entregável 1**|**NÃO INICIADO**|R1–R2|Relatório `.md`|**Objetivo imediato do contrato**|
|G1|5|Gate contratual: submissão e aceite de continuidade|NÃO INICIADO|R3|Aceite formal|Trava para fases pós-RAW|

---

## O que mudou “definitivamente” ao congelar o Plano de Retomada

- **Migração/publicação no MinIO** (2.5, 1.5, 2.6 e tudo de refino/derivados) ficou **POSTERGADA (ACEITE MARINGÁ)**.
    
- Entraram três itens explícitos para cumprir o contrato sem depender do MinIO:
    
    - **R1** (catálogo ↔ filesystem),
        
    - **R2** (diagnóstico QA do RAW por domínio),
        
    - **R3** (Relatório RAW — entregável 1),
        
    - e o **G1** (aceite para continuar).
        

## Próximo passo imediato (agora)

Executar **R1**: validação “manifest_unificado ↔ arquivos reais em dados_iniciais”, gerando um relatório objetivo de inconsistências (se houver).

Se você disser “vamos em R1”, eu escrevo a orientação para o agente (no Cursor) criar o notebook específico de R1 (também com execução assistida e correção a cada interação).