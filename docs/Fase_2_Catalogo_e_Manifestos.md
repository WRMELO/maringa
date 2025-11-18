
---

````markdown
# Fase 2 – Catálogo e Manifestos do Data Lake

Esta fase define como construir o **catálogo oficial** do data lake da Etapa 1 (projeto MARINGA), a partir:

- dos arquivos já entregues em `dados_iniciais/`, e  
- dos manifestos existentes (`manifest_index.csv`, `index_resumo_por_pasta.csv`),  
- agora com MinIO já instalado e buckets criados na Fase 1.

O resultado final da Fase 2 será um **manifesto unificado** que descreve, de forma consistente, todos os arquivos relevantes para o diagnóstico, servindo de base para as próximas fases (refino por domínio, agregações horárias e análise de completude).

---

## 2.1. Objetivos da Fase 2

Objetivos específicos:

1. Consolidar os manifestos existentes em um **manifesto unificado**, com modelo lógico único.
2. Descrever cada arquivo relevante com informações padronizadas:
   - localização (local e/ou MinIO);
   - domínio lógico;
   - forno associado (quando aplicável);
   - granularidade (corrida, minuto, hora, dia, meta);
   - período coberto (início/fim);
   - status no pipeline (raw / refinado / derivado).
3. Estabelecer o **catálogo oficial** que permitirá:
   - planejar a migração de `dados_iniciais` para `maringa-raw` de forma controlada;
   - dirigir a criação das tabelas refinadas por domínio (Fase 3);
   - orientar a análise de completude (Fase 5).

Ao final da Fase 2, o projeto terá uma visão única e estruturada de “o que existe” antes de qualquer transformação mais profunda.

---

## 2.2. Pontos de partida

Ponto de partida no repositório local:

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
      manifestos_inicial/
      manifestos_final/
    infra/
      minio/
        data/
        docker-compose.yml
      env/
        minio_credentials.env
````

Situação consolidada da Fase 1:

- MinIO operacional via Docker em `infra/minio`.
    
- Buckets criados:
    
    - `maringa-raw`
        
    - `maringa-refined`
        
    - `maringa-derived`
        
    - `maringa-manifests`
        

Nesta Fase 2, o foco é **catálogo e manifestos**. A migração física dos arquivos de `dados_iniciais` para `maringa-raw` será guiada por este catálogo.

---

## 2.3. Modelo lógico do Manifesto Unificado

Será criado um arquivo de manifesto unificado, com o nome sugerido:

- `manifest_unificado.csv` (ou `.parquet`, dependendo da escolha técnica),
    
- armazenado tanto em:
    
    - uma pasta local, por exemplo: `fase_1_diagnostico/dados/manifestos_final/`, e
        
    - no bucket `maringa-manifests` no MinIO.
        

### 2.3.1. Colunas mínimas do manifesto

Colunas definidas (podem ser ampliadas depois, mas estas são obrigatórias):

- `id_arquivo`  
    Identificador único do arquivo no catálogo (pode ser um hash, um ID numérico ou um caminho canônico).
    
- `origem_fisica`  
    Onde o arquivo está fisicamente:
    
    - `local` (para `dados_iniciais`),
        
    - `minio` (para objetos já no data lake).
        
- `path_local`  
    Caminho completo relativo ao repositório, quando o arquivo estiver em `dados_iniciais/` ou outra pasta local.  
    Exemplo:  
    `dados/dados_iniciais/Supervisório Forno 4/supervisorio_F4_2023-01.csv`
    
- `bucket_minio`  
    Nome do bucket MinIO onde o arquivo está (ou estará) armazenado. Por exemplo:  
    `maringa-raw`, `maringa-refined`, etc.  
    Pode ser vazio se o arquivo ainda não foi migrado.
    
- `path_minio`  
    Caminho lógico dentro do bucket MinIO, seguindo o padrão:  
    `<dominio>/<forno>/<granularidade>/<ano>/<mes>/<arquivo>`  
    Pode ser vazio enquanto o arquivo ainda estiver apenas em `dados_iniciais`.
    
- `dominio`  
    Domínio lógico de dados:
    
    - `consumo_fornos`
        
    - `corridas`
        
    - `dicionario`
        
    - `eletrodo`
        
    - `informacoes_diarias`
        
    - `supervisorio`
        
    - outros, se identificados depois.
        
- `forno`  
    Forno associado:
    
    - `F1`, `F2`, `F3`, `F4`, `F5`, `todos` ou `nao_aplicavel`.
        
- `granularidade`  
    Granularidade principal do conteúdo:
    
    - `corrida`, `minuto`, `hora`, `dia`, `meta`.
        
- `periodo_inicio`  
    Data/hora (ou data) de início do período coberto pelo arquivo (quando for possível inferir).
    
- `periodo_fim`  
    Data/hora (ou data) de fim do período coberto pelo arquivo.
    
- `status_pipeline`  
    Situação do arquivo no pipeline:
    
    - `raw_local` (somente em `dados_iniciais`);
        
    - `raw_minio` (já copiado para `maringa-raw`);
        
    - `refined` (arquivo refinado em `maringa-refined`);
        
    - `derived` (artefato analítico em `maringa-derived`).
        
- `fonte_manifesto`  
    Indica de qual manifesto original a entrada veio:
    
    - `manifestos_inicial`
        
    - `manifestos_final`
        
    - `gerado_automaticamente` (quando fruto de varredura nova).
        
- `observacoes`  
    Campo texto livre para observações relevantes (inconsistências, anomalias, anotações do Owner).
    

---

## 2.4. Atividades práticas da Fase 2

As atividades abaixo são direcionadas ao **Agente de Código**, mas descritas em linguagem de Planejador.

### 2.4.1. Atividade 2.1 – Inventário local de `dados_iniciais`

**Responsável:** Agente de Código  
**Status:** - [ ] Pendente

Objetivo: ter um inventário completo dos arquivos em `dados_iniciais`, com seus caminhos físicos.

Tarefas:

-  Ler recursivamente a pasta:
    
    - `fase_1_diagnostico/dados/dados_iniciais/`
        
-  Gerar um primeiro inventário técnico:
    
    - lista de arquivos com:
        
        - `path_local`
            
        - tamanho do arquivo
            
        - data de modificação
            
-  Salvar esse inventário como:
    
    - `dados/manifestos_inicial/inventario_dados_iniciais.csv` (ou nome equivalente).
        

Este inventário será usado para cruzar com os manifestos já existentes.

---

### 2.4.2. Atividade 2.2 – Leitura e harmonização dos manifestos existentes

**Responsável:** Agente de Código  
**Status:** - [ ] Pendente

Objetivo: integrar `manifest_index.csv` e `index_resumo_por_pasta.csv` em uma base intermediária.

Tarefas:

-  Ler os arquivos existentes em `dados/manifestos_inicial/` e/ou `dados/manifestos_final/`:
    
    - `manifest_index.csv`
        
    - `index_resumo_por_pasta.csv`
        
-  Padronizar nomenclatura de colunas relevantes (por exemplo, campos que indiquem pasta, tipo de dado, período aproximado).
    
-  Gerar uma tabela intermediária, por exemplo:
    
    - `dados/manifestos_inicial/manifest_intermediario.csv`  
        com, no mínimo:
        
    - referência ao caminho/pasta;
        
    - indicação de domínio lógico (se já existir no manifesto);
        
    - eventuais anotações originais.
        

Esta tabela intermediária será usada para cruzar com o inventário local.

---

### 2.4.3. Atividade 2.3 – Definição e codificação de domínio, forno e granularidade

**Responsável:** Planejador (regra) + Agente de Código (implementação)  
**Status:** - [ ] Pendente

Regras conceituais a serem aplicadas:

- Mapeamento de pastas para `dominio`:
    
    - `Consumo Fornos/` → `consumo_fornos`
        
    - `Corridas/` → `corridas`
        
    - `Dicionário/` → `dicionario`
        
    - `Eletrodo/` → `eletrodo`
        
    - `Informações Diária/` → `informacoes_diarias`
        
    - `Supervisório Forno 4/` → `supervisorio`
        
    - `Supervisório Forno 5/` → `supervisorio`
        
- Mapeamento de pastas para `forno`:
    
    - `Supervisório Forno 4/` → `F4`
        
    - `Supervisório Forno 5/` → `F5`
        
    - `Consumo Fornos/` e `Corridas/` podem conter arquivos separados por forno ou agrupados:
        
        - regra: inferir `forno` a partir do nome do arquivo quando houver, caso contrário usar `todos` e refinar depois.
            
- Mapeamento inicial para `granularidade`:
    
    - Supervisório → `minuto` (por padrão; pode ser refinado se algum arquivo já vier em hora).
        
    - Corridas → `corrida`
        
    - Consumo Fornos → em geral `hora` ou `dia`; o Agente deve usar o nome/estrutura do arquivo para inferir, registrando incertezas em `observacoes`.
        
    - Dicionário → `meta`
        
    - Informações Diária → `dia`
        
    - Eletrodo → depende da estrutura (corrida, dia ou evento); inicialmente, registrar como `nao_definido` quando não for óbvio, com anotação em `observacoes`.
        

Tarefas:

-  Aplicar essas regras ao inventário local + manifesto intermediário, gerando colunas:
    
    - `dominio`
        
    - `forno`
        
    - `granularidade`
        

---

### 2.4.4. Atividade 2.4 – Construção do Manifesto Unificado local

**Responsável:** Agente de Código  
**Status:** - [ ] Pendente

Objetivo: consolidar tudo em um único arquivo `manifest_unificado`.

Tarefas:

-  Juntar:
    
    - inventário local de arquivos;
        
    - manifesto intermediário;
        
    - regras de domínio/forno/granularidade;
        
-  Construir a tabela `manifest_unificado` com todas as colunas da seção 2.3.1:
    
    - `id_arquivo`
        
    - `origem_fisica` (inicialmente `local`)
        
    - `path_local`
        
    - `bucket_minio` (inicialmente vazio)
        
    - `path_minio` (inicialmente vazio)
        
    - `dominio`
        
    - `forno`
        
    - `granularidade`
        
    - `periodo_inicio` (preencher quando for possível; deixar vazio ou nulo quando desconhecido)
        
    - `periodo_fim`
        
    - `status_pipeline` (inicialmente `raw_local`)
        
    - `fonte_manifesto`
        
    - `observacoes`
        
-  Salvar este manifesto em:
    
    - `fase_1_diagnostico/dados/manifestos_final/manifest_unificado.csv` (ou `.parquet`).
        

Neste momento, o catálogo ainda é “local”, sem obrigar que os arquivos já estejam no MinIO.

---

### 2.4.5. Atividade 2.5 – Registro do Manifesto Unificado no MinIO

**Responsável:** Agente de Código  
**Status:** - [ ] Pendente

Objetivo: garantir que o manifesto unificado também esteja disponível dentro do data lake.

Tarefas:

-  Subir uma cópia de `manifest_unificado.csv` para o bucket `maringa-manifests`, em um caminho como:
    
    - `catalogo/manifest_unificado/manifest_unificado_v1.csv`
        
-  Registrar no próprio `manifest_unificado` as informações:
    
    - `bucket_minio` e `path_minio` para o próprio arquivo de manifesto (auto-referência).
        
-  Validar via notebook que o arquivo aparece corretamente no MinIO.
    

Resultado:

- O catálogo passa a fazer parte do próprio data lake.
    

---

### 2.4.6. Atividade 2.6 – Atualização incremental do `status_pipeline`

Esta atividade é a ponte para a próxima fase (migração e refino).

**Responsável:** Agente de Código (com decisões adicionais do Planejador na próxima fase)  
**Status:** - [ ] Em aberto

Diretriz:

- À medida que arquivos forem sendo migrados de `dados_iniciais` para `maringa-raw`, e depois para versões refinadas em `maringa-refined`, o Agente deverá:
    
    - atualizar `status_pipeline` para:
        
        - `raw_minio` quando o arquivo estiver copiado para `maringa-raw`;
            
        - `refined` quando existir uma versão refinada associada;
            
        - `derived` para artefatos analíticos;
            
    - preencher `bucket_minio` e `path_minio` correspondentes.
        

Essa atualização incremental será detalhada na próxima fase, mas já fica previsto que o `manifest_unificado` será a fonte única de verdade sobre o estado de cada arquivo.

---

## 2.5. Checklist de conclusão da Fase 2

-  Inventário completo de `dados_iniciais` gerado (`inventario_dados_iniciais.csv`).
    
-  Manifestos existentes (`manifest_index.csv`, `index_resumo_por_pasta.csv`) lidos e harmonizados.
    
-  Regras de domínio/forno/granularidade aplicadas a todos os arquivos.
    
-  `manifest_unificado.csv` criado em `dados/manifestos_final/` com o modelo lógico definido.
    
-  Cópia do `manifest_unificado` armazenada em `maringa-manifests` no MinIO.
    
-  Validação via notebook de que o `manifest_unificado` é legível a partir do ambiente `ds-base`.
    

Quando todos esses itens estiverem marcados como concluídos, considera-se que a **Fase 2 – Catálogo e Manifestos** está finalizada, permitindo avançar para a Fase 3 (camada refinada por domínio).

```

Quando o Agente de Código tiver executado essa fase e você marcar a checklist, eu sigo como Planejador para a próxima etapa: **Fase 3 – Camada refinada por domínio**, usando justamente esse `manifest_unificado` como guia.
```