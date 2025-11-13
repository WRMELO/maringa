# Plano de Ação para Análise de Completude dos Dados

## Etapas e Tarefas

### 1. Consolidar Inventário de Arquivos Existentes
- Identificar e listar todos os arquivos disponíveis nas pastas relevantes.
- Verificar a consistência dos nomes e formatos dos arquivos.
- Gerar um inventário consolidado com informações como:
  - Nome do arquivo.
  - Localização.
  - Tamanho.
  - Data de modificação.

### 2. Identificar Completude por Ano e Forno
- Para cada ano, identificar quais fornos possuem dados disponíveis.
- Categorizar os dados por tipo (Consumo, Corridas, Informações Diárias, etc.).
- Gerar uma tabela de presença/ausência de dados por [Ano × Forno × Tipo].

### 3. Revisar Dicionários de Dados para Campos Essenciais
- Analisar os dicionários de dados disponíveis (DIC_*).
- Identificar campos críticos para cada tipo de estudo (physics based, supervisionado, não supervisionado).
- Marcar campos relacionados à variável de interesse (distância ponta–fundo).

### 4. Calcular Cobertura Temporal (CT) por Segmento
- Para cada família de dados, calcular:
  - Datas mínima e máxima.
  - Percentual de dias com dados disponíveis.
- Gerar gráficos e tabelas de cobertura temporal por [Forno × Família × Período].

### 5. Analisar Completude de Campos Essenciais (CC)
- Verificar o preenchimento dos campos críticos identificados.
- Calcular o percentual de valores não nulos por [Forno × Período].
- Identificar campos com baixa completude e avaliar impacto.

### 6. Estudar Integridade Relacional (IR) entre Fontes
- Definir chaves de ligação entre as famílias de dados.
- Calcular indicadores de integridade relacional, como:
  - Percentual de registros conectados entre fontes.
  - Identificação de “ilhas de dados”.

### 7. Consolidar Resultados por Tipo de Estudo
- Organizar os resultados de completude por tipo de estudo:
  - Physics based.
  - Supervisionado.
  - Não supervisionado.
- Classificar segmentos em: Apto, Limitado ou Inadequado.

### 8. Calcular Índice Final de Completude e Gerar Conclusões
- Calcular o Índice de Grau de Completude por segmento.
- Destacar segmentos com alta completude para protótipos iniciais.
- Relacionar lacunas com impacto na modelagem da variável de interesse.

---

## Acompanhamento

Este plano será atualizado conforme o andamento das tarefas. Cada etapa será marcada como concluída ao final de sua execução.