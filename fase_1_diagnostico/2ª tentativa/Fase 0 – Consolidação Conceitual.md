
# SSOT como Data Lake e Organização Inicial dos Dados

### 0.1. Contexto da Etapa 1

Esta Fase 0 formaliza as decisões conceituais para a **Etapa 1 – Diagnóstico / Completude dos Dados** do projeto MARINGA (fornos elétricos).

Objetivo desta etapa:

- Avaliar **completude, cobertura temporal e usabilidade** dos dados disponíveis para:
  - modelos **physics-based**;
  - modelos **supervisionados**;
  - modelos **não supervisionados**;
  - com foco, mas não exclusividade, na variável **distância da ponta do eletrodo**.
- Entregar um **Relatório de Completude e Usabilidade dos Dados**, em formato Markdown, base para decisões de modelagem em fases posteriores.

Nesta fase **não** será construído o modelo final de Machine Learning; o foco é garantir que a base de dados e a arquitetura estejam sólidas.

---

### 0.2. Decisão central: SSOT como Data Lake (MinIO + Catálogo)

Decisões congeladas:

1. A **Single Source of Truth (SSOT)** será concebida como um **data lake**, e não como uma tabela única monolítica.
2. A SSOT será composta por:
   - **MinIO** como repositório de objetos (arquivos CSV, Parquet, XLSX, etc.);  
   - um **catálogo de manifestos** (arquivos de índice) que descrevem tudo o que está armazenado;
   - uma camada de **tabelas refinadas** e **derivados** (agregações e “tabelões”) construídos de forma planejada.
3. A unidade de verdade não é mais “uma tabela final”, mas sim:
   - o conjunto de arquivos organizados e versionados no MinIO;  
   - mais o catálogo que os descreve.

Implicação: qualquer análise de completude e qualquer modelo futuro **derivam** desse data lake, em vez de tentar forçar todos os dados diretamente em uma única tabela “final”.

---

### 0.3. Camadas lógicas do data lake

Ficam definidas as seguintes camadas lógicas (independentes da implementação física):

1. **RAW**  
   - Arquivos **exatamente como recebidos** do cliente ou do diretório de origem.  
   - Nenhuma modificação de conteúdo, apenas organização em pastas.  

2. **REFINADA**  
   - Arquivos/tabelas por **domínio** (supervisório, corridas, consumo de fornos, laboratório, medições de eletrodo, etc.) com:
     - nomes de colunas padronizados;
     - tipos de dados corrigidos;
     - timestamps normalizados;
     - chaves lógicas bem definidas (forno, corrida, timestamp, etc.).
   - Esta camada é a base para o diagnóstico de completude.

3. **DERIVADOS**  
   - Agregações e visões para análise e modelagem, por exemplo:
     - séries **horárias** derivadas de dados em minuto;
     - “tabelões” por forno-hora ou por corrida;
     - indicadores pré-calculados de operação e qualidade.
   - São produtos analíticos, não substituem as camadas RAW/REFINADA.

---

### 0.4. Organização atual dos dados iniciais (ponto de partida local)

Os dados entregues para a Etapa 1 estão, neste momento, organizados no repositório local:

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
````

Decisões:

1. A pasta `fase_1_diagnostico/dados/dados_iniciais/` é o **ponto de partida** para a ingestão no data lake.
    
2. As subpastas de `dados_iniciais` serão mapeadas para **domínios lógicos** do data lake:
    
    - `Consumo Fornos` → domínio de consumo/energia dos fornos.
        
    - `Corridas` → domínio de corridas / produção.
        
    - `Dicionário` → dicionários de dados e metainformação.
        
    - `Eletrodo` → medições relacionadas ao eletrodo (incluindo distância da ponta, quando disponível).
        
    - `Informações Diária` → consolidações diárias / relatórios operacionais.
        
    - `Supervisório Forno 4` → dados de supervisório do Forno 4 (tempo contínuo, granularidade minuto).
        
    - `Supervisório Forno 5` → dados de supervisório do Forno 5 (tempo contínuo, granularidade minuto).
        
3. As pastas `manifestos_inicial` e `manifestos_final` contêm os **índices** e **inventários** da primeira fase e serão utilizados como base para construir um **manifesto unificado** na Fase 2.
    

---

### 0.5. Granularidades oficiais

Definimos as seguintes granularidades de referência para o projeto:

- **Corrida** – unidade de processo metalúrgico (especialmente para corridas e laboratório).
    
- **Minuto** – granularidade nativa típica dos dados de supervisório.
    
- **Hora** – **granularidade padrão de diagnóstico e comparação entre fontes**, usada para:
    
    - cruzar supervisório, consumo de energia e demais variáveis de processo;
        
    - calcular métricas de completude cruzada.
        
- **Dia** – quando necessário para consolidações de alto nível (por exemplo, gráficos de cobertura por dia).
    

Decisão importante:

- As bases em minuto **serão preservadas** na camada REFINADA.
    
- As bases horárias serão **derivadas** da camada em minuto, com cálculo explícito de:
    
    - média, min, max, desvio-padrão por hora;
        
    - número de minutos válidos por hora (`n_valid`);
        
    - cobertura (`n_valid / n_expected`);
        
    - flags de qualidade da hora.
        

---

### 0.6. Papel dos manifestos e dos critérios de campos essenciais

Documentos existentes:

- `manifest_index.csv` e `index_resumo_por_pasta.csv` (pasta de manifestos).
    
- `criterios_campos_essenciais.md` (definição dos campos essenciais para cada tipo de estudo).
    

Decisões:

1. Os arquivos de manifestos existentes serão a **base de verdade** para:
    
    - localizar arquivos físicos;
        
    - identificar domínio, forno e granularidade original de cada arquivo.
        
2. Na Fase 2, esses manifestos serão integrados em um **manifesto unificado**, com campos como:
    
    - `id_arquivo`, `dominio`, `fonte`, `forno`, `granularidade_original`, `periodo_inicio`, `periodo_fim`, `path_no_bucket`, `status_refino`, etc.
        
3. O documento `criterios_campos_essenciais.md` será a referência oficial para:
    
    - classificar colunas das tabelas refinadas em:
        
        - chaves lógicas;
            
        - variáveis de processo térmico;
            
        - variáveis de consumo;
            
        - variáveis de qualidade;
            
        - variáveis ligadas à distância da ponta do eletrodo;
            
    - definir quais campos **devem** ser avaliados em termos de completude para cada tipo de modelo (physics-based, supervisionado, não supervisionado).
        

---

### 0.7. Decisão sobre granularidade de análise de completude

- A análise de completude será feita em **dois níveis**:
    

1. **Por tabela / fonte (nível de domínio):**
    
    - Cobertura temporal por forno e por período (hora/dia/mês).
        
    - Percentual de preenchimento dos **campos essenciais** em cada tabela refinada.
        
2. **Cruzada entre fontes (nível integrado):**
    
    - Usando tabelas horárias derivadas e/ou tabelões, medir:
        
        - para quantas horas temos supervisório + consumo de energia;
            
        - para quantas corridas temos corridas + laboratório + supervisório;
            
        - qual fração do tempo é utilizável para:
            
            - modelos physics-based;
                
            - modelos supervisionados;
                
            - modelos não supervisionados.
                

---

### 0.8. Infraestrutura MinIO e ambiente `ds-base`

Situação atual:

- **MinIO ainda não está instalado**.
    
- O ambiente `conda ds-base` existe, mas **ainda não foi ativado/configurado** para este projeto.
    

Decisões:

1. O **data lake será implementado em MinIO**, com:
    
    - um bucket para dados RAW;
        
    - um bucket para dados REFINADOS;
        
    - um bucket para DERIVADOS;
        
    - e, se necessário, um bucket para manifestos/catalogação.
        
2. A interação com o data lake será feita a partir do ambiente `conda ds-base`, que será o ambiente padrão para:
    
    - notebooks de análise;
        
    - scripts de ingestão e refino;
        
    - bibliotecas Python de acesso ao MinIO.
        
3. A **Fase 1 – Organização Física do Data Lake no MinIO** irá detalhar:
    
    - como instalar e configurar o MinIO (servidor);
        
    - como conectar o ambiente `ds-base` ao MinIO;
        
    - como migrar os conteúdos de `dados_iniciais` para a camada RAW do data lake.
        

---

### 0.9. Orientações gerais ao Agente de Código (para fases futuras)

Quando o Agente de Código for ativado, ele deverá:

1. **Não alterar** os arquivos em `dados_iniciais` – eles são o espelho da entrega original do cliente.
    
2. Implementar scripts e notebooks que:
    
    - leiam os manifestos existentes;
        
    - gerem o manifesto unificado conforme o modelo da Fase 2;
        
    - migrem dados para o MinIO conforme a estrutura que será definida nas próximas fases;
        
    - construam as tabelas refinadas e derivadas seguindo este documento de decisões.
        
3. Respeitar a separação de camadas (RAW → REFINADA → DERIVADOS) e **nunca sobrescrever** dados de uma camada com artefatos de outra.
    

---

### 0.10. Conclusão da Fase 0

Com este documento, ficam **congeladas** as decisões conceituais da Fase 0:

- SSOT como data lake (MinIO + catálogo).
    
- Camadas RAW, REFINADA e DERIVADOS.
    
- Papel e localização dos dados iniciais.
    
- Uso de granularidade horária como eixo principal de diagnóstico, preservando minuto/corrida.
    
- Manifestos como base do catálogo.
    
- Uso de `criterios_campos_essenciais` como referência para campos essenciais.
    
- MinIO como infraestrutura a ser implantada na Fase 1, acessado via ambiente `ds-base`.
    


