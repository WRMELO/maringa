## Projeto: Transição Digital orientada a Dados para Maringá Ferro Ligas

O projeto em curso é uma iniciativa de transição digital aplicada ao contexto industrial da Maringá Ferro Ligas, com foco em transformar dados operacionais e de processo (vindos de sistemas, planilhas e chão de fábrica) em uma base confiável para análises e, em seguida, para o desenvolvimento de modelos preditivos que apoiem a operação. A lógica central é simples: antes de discutir modelos, precisamos garantir que os dados existentes são utilizáveis, rastreáveis e representativos do processo real.

## Qual é o problema que estamos endereçando

O problema não começa no algoritmo; ele começa na realidade das fontes: dados distribuídos em múltiplos sistemas e formatos, com lacunas, atrasos, inconsistências, duplicidades e diferenças de governança entre áreas. Por isso, o objetivo imediato do projeto é executar um diagnóstico estruturado do “existente” (fontes, disponibilidade e qualidade), produzir um relatório de lacunas e riscos, e somente então avançar para protótipos e modelos.

## Como estamos nos preparando para solucioná-lo

A preparação está sendo feita em duas frentes, em paralelo:

1. **Metodologia de diagnóstico (processo):** seguimos um plano de trabalho em etapas (planejamento e alinhamento; mapeamento e coleta; análise de qualidade; diagnóstico de riscos; relatório e recomendações), com responsabilidades claras entre empresa e consultoria, e com execução organizada por semanas do projeto.
    
2. **Infraestrutura de trabalho (ambiente técnico):** estamos estruturando um “laboratório” reprodutível para ingestão e análise, baseado em contêineres Docker, com um repositório RAW em MinIO para armazenar os arquivos brutos (CSV/Parquet) sem alteração, e um ambiente Python/Jupyter para rodar análises de qualidade e protótipos. Isso reduz dependência do ambiente do cliente, mantém rastreabilidade do dado original e facilita reprocessamentos futuros.
    

## Estratégia que estamos usando (o “como” do projeto)

A estratégia adotada combina governança de dados com engenharia de dados leve e análise técnica:

- **Diagnosticar antes de modelar:** a primeira entrega relevante é o diagnóstico de qualidade e disponibilidade, porque qualquer modelo treinado sobre dados inconsistentes vira um “amplificador de erro”.
    
- **Avaliar qualidade por dimensões reconhecidas:** a análise considera dimensões clássicas de Data Quality (ex.: completude, acurácia, consistência, integridade, atualidade, unicidade e relevância) e começa por uma janela de histórico representativa (por exemplo, meses recentes), antes de escalar para todo o acervo.
    
- **RAW como fonte de verdade e reprocessamento:** os extratos recebidos são armazenados no MinIO “como vieram”, preservando a origem e permitindo repetir a limpeza/transformação sempre que uma abordagem melhor for definida.
    
- **Arquitetura portátil e controlada:** Docker/compose organiza os serviços (MinIO, Jupyter e, se necessário, um banco para metadados) em rede interna, garantindo repetibilidade e facilidade de evolução do pipeline.
    
- **Fechamento da Fase 1 com recomendações acionáveis:** o diagnóstico culmina em relatório com lacunas, riscos e priorização de correções, servindo de base para a fase seguinte de prototipagem e modelagem.
    

