modelos
Modelos físico‑informados e de aprendizado em metalurgia conseguem boa qualidade mesmo em cenários de “small data”, desde que o projeto trate de forma rigorosa a governança, a qualidade e a representatividade dos dados de processo. No contexto da Maringá Ferro Ligas – sem medição direta da distância do eletrodo em fornos de ferro‑liga – isso exige combinar modelos **physic‑based** de forno/submerged arc furnace, dados operacionais consolidados (RAW) e técnicas de ML/RL amostralmente eficientes.[sciencedirect+10](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)

Para a realidade da Maringá Ferro Ligas, o “número mínimo ótimo” de dados não é um valor fixo, mas um patamar por tipo de modelo que torna o ganho marginal de coletar mais dados pequeno em relação ao custo, desde que os dados passem pelo diagnóstico e estejam bem governados. Em metalurgia, casos industriais em aço mostram que resultados robustos vêm de combinar **qualidade** de dados (curados, rastreáveis, contextualizados) com tamanhos de amostra que variam de dezenas (modelos físicos) a milhões de registros de sensores (não supervisionado / RL).[](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​​​

---

## 1. Contexto: dados, eletrodos e fornos de ferro‑liga

Submerged arc furnaces (SAF) e fornos elétricos de ferro‑liga são sistemas altamente não lineares, onde a distância/imersão do eletrodo afeta a distribuição de corrente, o aquecimento Joule, a penetração de arco e a cinética de fusão e redução. Medições diretas e contínuas da posição do eletrodo em relação à carga são, em geral, inviáveis devido ao ambiente hostil (altas temperaturas, poeira, escória, radiação), e a operação depende de medições indiretas (tensão, corrente, potência, impedância, temperatura, composição de gás) e de modelos elétricos/magnético‑térmicos.[royalsocietypublishing+6](https://royalsocietypublishing.org/rsos/article/4/9/170313/93519/Pragmatic-analysis-of-the-electric-submerged-arc)​

No projeto de transição digital, isso se traduz em três desafios:

- Diagnosticar a qualidade e a disponibilidade dos dados históricos de operação do forno (corridas, campanhas, eventos).[academic.oup+3](https://academic.oup.com/nsr/article/10/7/nwad125/7147579)​
    
- Unificar esses dados em um repositório RAW rastreável (MinIO) e replicável (Docker) para análises de qualidade, features e protótipos.
    
- Definir “quanto de dado é suficiente” para cada tipo de modelo (physic‑based, supervisionado, não supervisionado e RL) diante da falta de medição direta da distância do eletrodo.[tohoku.elsevierpure+4](https://tohoku.elsevierpure.com/en/publications/machine-learning-strategies-for-small-sample-size-in-materials-sc/)​
    

---

## 2. Modelos physic‑based para fornos de ferro‑liga

Modelos physic‑based (primeiros princípios) descrevem balanços de massa e energia, escoamento de fluidos, campos eletromagnéticos e transferência de calor em SAF e EAF, e são amplamente usados para analisar eletrodos, escória e eficiência energética em ferro‑ligas. Esses modelos permitem:[academia+7](https://www.academia.edu/60855452/Electrode_Erosion_in_Submerged_Arc_Furnaces)​

- Representar explicitamente a geometria do forno, o banho de escória, a imersão do eletrodo e o campo eletromagnético, mesmo sem medir diretamente a distância em operação.[pmc.ncbi.nlm.nih+3](https://pmc.ncbi.nlm.nih.gov/articles/PMC11190561/)​
    
- Estudar sensibilidade do processo à profundidade do eletrodo, condutividade elétrica/térmica da escória, composição de carga e regime de potência.[imim+5](https://www.imim.pl/files/archiwum/Vol4_2017/54.pdf)​
    

Em vários estudos de CFD e MHD de SAF em ferroníquel e ferro‑silício, a calibração se baseia em um conjunto limitado de medidas industriais: temperaturas pontuais, balanços de energia, medições de tensão/corrente, análises de escória/metal e poucas campanhas instrumentadas.[ieeexplore.ieee+6](https://ieeexplore.ieee.org/iel8/6287639/6514899/10630793.pdf)​

**Requisitos de dados para calibrar modelos physic‑based (no cenário Maringá)**

Mesmo sem medir a distância do eletrodo, é possível calibrar modelos que “fecham” a física usando dados indiretos:

- Entradas por corrida ou janela operacional:
    
    - Potência ativa e reativa, tensão, corrente, fator de potência, ajustes de eletrodo (subida/descida, consumo de eletrodo).
        
    - Composição de carga (minério, redutor, fundentes, retorno), peso e sequência de carregamento.
        
    - Temperaturas: medições pontuais em gás ou escória (quando existirem), temperaturas em pontos de casco, dados de sistema de refrigeração.[sciencedirect+5](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)​
        
- Saídas:
    
    - Composição química do metal/ligas, rendimento, consumo específico de energia, taxa de produção, eventualmente emissões gasosas.[royalsocietypublishing+5](https://royalsocietypublishing.org/rsos/article/4/9/170313/93519/Pragmatic-analysis-of-the-electric-submerged-arc)​
        

Estudos em fornos de ferro‑liga e EAF típicos mostram que:

- 10–30 condições/momentos experimentais bem caracterizados são usados para ajustar parâmetros como condutividade da escória, coeficientes de troca térmica e perfis de perda de calor.[nature+4](https://www.nature.com/articles/s41598-021-96085-1)​
    
- Mais 5–10 casos independentes são reservados para validação cruzada dos resultados (por exemplo, comparar perfis previstos de temperatura/energia com dados industriais).[academia+2](https://www.academia.edu/60855452/Electrode_Erosion_in_Submerged_Arc_Furnaces)​
    

**Resumo numérico – physic‑based (cenário sem medição de distância do eletrodo)**

- Mínimo viável: cerca de 15 corridas/campanhas por regime principal (por exemplo, tipo de liga + faixa de potência), com dados de energia, carga e composição suficientemente completos para fechar balanços.[ieeexplore.ieee+6](https://ieeexplore.ieee.org/iel8/6287639/6514899/10630793.pdf)​
    
- Ótimo prático: 30–40 corridas bem medidas por regime, permitindo calibração robusta e validação em condições “novas” (como variações de carga ou receita).[pmc.ncbi.nlm.nih+4](https://pmc.ncbi.nlm.nih.gov/articles/PMC11190561/)​
    

---

## 3. Modelos supervisionados: qualidade, consumo e variáveis latentes

Modelos supervisionados (regressão e classificação) são usados em siderurgia e ferro‑ligas para prever composição final, consumo específico, probabilidade de defeitos, emissões e indicadores de eficiência, com bons resultados mesmo em small data. Nesses casos, a distância efetiva do eletrodo entra como variável latente implícita nas medidas de processo (tensão, corrente, impedância, ruído elétrico, assinatura de potência) mais dados metalúrgicos.[nature+15](https://www.nature.com/articles/s41524-023-01000-z)​

Revisões em “small data ML” e casos industriais aço/metalurgia mostram que:

- Modelos de regressão de propriedades ou desempenho podem alcançar boa acurácia com 100–300 amostras, quando há forte engenharia de features e incorporação de conhecimento físico.[adsabs.harvard+6](http://ui.adsabs.harvard.edu/abs/2023npjCM...9...42X/abstract)​
    
- Em casos industriais de aciaria, projetos de qualidade preditiva usam de 10³ a 10⁴ heats/corridas para treinar modelos mais flexíveis, como redes neurais.[d-nb+4](https://d-nb.info/1257929453/34)​
    

No contexto da Maringá, features supervisionadas podem incluir:

- Entradas:
    
    - Séries de potência, corrente, tensão, fator de potência, tempo em diferentes níveis de potência, consumo de eletrodo.[open.uct+5](https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf)​
        
    - Composições de alimentação, granulometria, umidade, temperatura de carga, sequenciamento.
        
    - Condições ambiente e manutenção (estado de revestimento, trocas de eletrodos, limpeza de forno).
        
- Saídas:
    
    - Composição de liga, rendimento metálico, consumo específico de energia, indicadores de estabilidade de operação, “OK/NOK” de qualidade.[onlinelibrary.wiley+4](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​
        

**Resumo numérico – supervisionado (para Maringá)**

- Regressão (consumo, qualidade, composição):
    
    - Mínimo viável: ~200–300 corridas bem registradas por família de ligas, com entradas e saídas completas.[sciencedirect+5](https://www.sciencedirect.com/science/article/pii/S2352847824001552)​
        
    - Ótimo prático: 1000–3000 corridas por família de ligas, faixa em que as curvas de aprendizado de modelos industriais de aço tendem a estabilizar.[research.rug+5](https://research.rug.nl/files/568069032/An_Industry_4.0_example_real_time_quality_control_for_steel_based_mass_production_using_Machine_Learning_on_non_invasive_sensor_data.pdf)​
        
- Classificação (OK/NOK, defeitos, regimes instáveis):
    
    - Mínimo viável: 500–1000 exemplos rotulados, assegurando dezenas de casos na classe minoritária (falha, desvio).[eds.yildiz+5](https://eds.yildiz.edu.tr/AjaxTool/GetArticleByPublishedArticleId?PublishedArticleId=7378)​
        
    - Ótimo prático: alguns milhares de amostras rotuladas, sobretudo em problemas com múltiplas classes (vários tipos de defeito ou regimes).[pmc.ncbi.nlm.nih+3](https://pmc.ncbi.nlm.nih.gov/articles/PMC11285775/)​
        

Mesmo sem medir diretamente a distância do eletrodo, a combinação de sinais elétricos e variáveis metalúrgicas permite que o modelo aprenda correlações com variáveis latentes relacionadas à posição/imersão, desde que o volume e a qualidade dos dados sejam adequados.[imim+5](https://www.imim.pl/files/archiwum/Vol4_2017/54.pdf)​

---

## 4. Não supervisionados: perfis de operação e anomalias

Modelos não supervisionados (clusterização, redução de dimensionalidade, detecção de anomalias) são particularmente adequados para:

- Identificar regimes distintos de operação de forno (por exemplo, regimes com eletrodo mais imerso ou mais raso, ainda que não observados diretamente).[oaepublish+4](https://www.oaepublish.com/articles/jmi.2024.85)​
    
- Detectar padrões de anomalia em sinais de potência, corrente, harmônicos e flutuações que indiquem instabilidade do arco ou do banho.[github+2](https://github.com/jonathanwvd/awesome-industrial-datasets)​
    

Na prática industrial, esses métodos trabalham com grandes volumes de séries temporais de sensores:

- Casos em siderurgia usam de 10⁴ a 10⁶ registros de sensores para agrupar regimes de processo e detectar anomalias em linhas de produção.[d-nb+5](https://d-nb.info/1257929453/34)​
    
- Em aplicações de monitoramento, meses de dados contínuos são comuns, o que permite capturar variação de turno, manutenção e sazonalidade.[sciencedirect+3](https://www.sciencedirect.com/science/article/pii/S2352847824001552)​
    

No projeto Maringá, as variáveis alvo podem incluir:

- Séries de potência, corrente, tensões fase‑fase, fatores de potência, harmônicos, ruídos e eventos de ajuste automático de eletrodo.[open.uct+5](https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf)​
    
- Temperaturas, vazões de refrigeração, pressões e variáveis ambientais disponíveis.
    

**Resumo numérico – não supervisionado (para Maringá)**

- Clusterização de regimes (operacionais, “perfis de eletrodo” implícitos):
    
    - Mínimo viável: ~10⁴ pontos de série temporal (por exemplo, leituras por minuto em algumas semanas de operação) por forno/tipo de produto.[adsabs.harvard+5](http://ui.adsabs.harvard.edu/abs/2023npjCM...9...42X/abstract)​
        
    - Ótimo prático: 10⁵–10⁶ pontos, cobrindo vários meses e múltiplas campanhas, o que melhora a qualidade dos clusters e a robustez dos “perfis de operação”.[research.rug+4](https://research.rug.nl/files/568069032/An_Industry_4.0_example_real_time_quality_control_for_steel_based_mass_production_using_Machine_Learning_on_non_invasive_sensor_data.pdf)​
        
- Detecção de anomalias:
    
    - Mínimo viável: ~10⁵ pontos, permitindo estimar a distribuição do comportamento “normal”.[oaepublish+2](https://www.oaepublish.com/articles/jmi.2024.85)​
        
    - Ótimo prático: ordem de 10⁶ pontos ou mais, tipicamente de 3–12 meses, incorporando diferentes condições externas e de manutenção.[github+3](https://github.com/jonathanwvd/awesome-industrial-datasets)​
        

Aqui, a ausência de medição direta de distância do eletrodo é parcialmente compensada pela riqueza das assinaturas de processo, que se refletem em padrões elétricos e térmicos que os algoritmos de clusterização capturam.[royalsocietypublishing+4](https://royalsocietypublishing.org/rsos/article/4/9/170313/93519/Pragmatic-analysis-of-the-electric-submerged-arc)​

---

## 5. Reinforcement Learning (RL) seguro em processos metalúrgicos

O uso de RL em processos metalúrgicos é emergente, com foco em controle ótimo e seguro (safe RL) em ambientes onde testar políticas diretamente na planta é arriscado. Casos documentados incluem controle de planicidade em laminação a frio e outros processos industriais onde RL off‑line é treinado sobre dados históricos e modelos de processo.[acm+9](https://dl.acm.org/doi/10.1016/j.ins.2023.119684)​

Em RL para fornos de ferro‑liga, potenciais usos incluem:

- Ajuste de setpoints de potência, perfil de subida/descida dos eletrodos e sequência de carregamento, com objetivo de minimizar consumo específico, emissões ou instabilidade elétrica.[research.aalto+5](https://research.aalto.fi/files/104297935/1_s2.0_S0020025523003158_main.pdf)​
    
- Aprendizagem de políticas que respeitem restrições de segurança e limites operacionais, mitigando riscos de arco instável, desgaste excessivo de eletrodo ou danos ao refratário.[sciencedirect+6](https://www.sciencedirect.com/science/article/abs/pii/S0020025523012690)​
    

Literatura de RL industrial e metalúrgico mostra que:

- Abordagens online “puras” exigiriam enormes quantidades de interações e são em geral inviáveis em processos críticos.[pure.tudelft+5](https://pure.tudelft.nl/ws/portalfiles/portal/142954982/dissertation.pdf)​
    
- Métodos off‑line e híbridos (modelo + dados) reduzem a necessidade de dados reais, mas ainda requerem milhares de transições históricas para aprender políticas confiáveis.[sciencedirect+5](https://www.sciencedirect.com/science/article/pii/S0166361522001452)​
    

**Resumo numérico – RL (para Maringá, priorizando segurança)**

- Histórico real (off‑line):
    
    - Mínimo viável: ~10³ transições estado–ação–recompensa por regime (por exemplo, combinações de tipo de liga e faixa de potência), bem registradas e com boa cobertura de ações “normais”.[diva-portal+5](https://www.diva-portal.org/smash/get/diva2:1750818/FULLTEXT01.pdf)​
        
    - Ótimo prático: 10⁴ transições por regime, correspondendo a meses de operação, o que reduz overfitting e melhora a estabilidade das políticas off‑line.[acm+5](https://dl.acm.org/doi/10.1016/j.compind.2022.103748)​
        
- Simulação / gêmeo digital:
    
    - Após calibrar um modelo physic‑based com cerca de 30–40 corridas reais (seção 2), é recomendável gerar 10⁴–10⁵ episódios sintéticos para treinar o agente RL com segurança, restringindo o espaço de ações ao envelope fisicamente plausível.[sciencedirect+7](https://www.sciencedirect.com/science/article/abs/pii/S0020025523002815)​
        

Sem medir diretamente a distância do eletrodo, o estado do agente deve incluir sinais elétricos, variáveis de processo e, possivelmente, estados latentes aprendidos (por autoencoders ou modelos supervisionados) que resumam a “posição efetiva” do eletrodo.[academia+5](https://www.academia.edu/60855452/Electrode_Erosion_in_Submerged_Arc_Furnaces)​

---

## 6. Tabela‑resumo de mínimos viáveis e ótimos

Valores em ordem de grandeza, por tipo de modelo, no contexto da Maringá Ferro Ligas, assumindo dados diagnosticados e limpos (RAW + Jupyter) e ausência de medição direta de distância do eletrodo:

|Tipo de modelo|Escopo típico no projeto|Mínimo viável de dados|Nível “ótimo” prático|Fontes|
|---|---|---|---|---|
|Physic‑based (forno / SAF, balanços)|Calibrar parâmetros físicos (condutividade de escória, perdas, perfis térmicos) a partir de corridas do forno|~15 corridas/campanhas bem medidas por regime operacional (tipo de liga + faixa de potência) [sciencedirect+4](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)​|30–40 corridas/campanhas bem medidas por regime, incluindo casos de validação [sciencedirect+4](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)​|[sciencedirect+6](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)​|
|Supervisionado – regressão|Previsão de consumo específico, composição, rendimento|200–300 corridas por família de ligas, com entradas/saídas completas [nature+5](https://www.nature.com/articles/s41524-023-01000-z)​|1000–3000 corridas por família de ligas [onlinelibrary.wiley+5](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​|[onlinelibrary.wiley+10](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​|
|Supervisionado – classificação|OK/NOK, defeitos, regimes instáveis|500–1000 exemplos rotulados (com dezenas na classe minoritária) [arxiv+5](https://arxiv.org/pdf/2102.09382v1.pdf)​|Vários milhares de exemplos rotulados, sobretudo em múltiplas classes [research.rug+3](https://research.rug.nl/files/568069032/An_Industry_4.0_example_real_time_quality_control_for_steel_based_mass_production_using_Machine_Learning_on_non_invasive_sensor_data.pdf)​|[arxiv+6](https://arxiv.org/pdf/2102.09382v1.pdf)​|
|Não supervisionado – clusterização|Descobrir regimes de operação e perfis de forno|~10⁴ pontos de série temporal por equipamento/regime (semanas de dados) [onlinelibrary.wiley+5](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​|10⁵–10⁶ pontos (meses de operação) [oaepublish+4](https://www.oaepublish.com/articles/jmi.2024.85)​|[onlinelibrary.wiley+6](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)​|
|Não supervisionado – anomalia|Detecção de eventos instáveis, padrões anômalos em potência/corrente|~10⁵ pontos de série temporal [oaepublish+2](https://www.oaepublish.com/articles/jmi.2024.85)​|≥10⁶ pontos cobrindo 3–12 meses [oaepublish+3](https://www.oaepublish.com/articles/jmi.2024.85)​|[oaepublish+3](https://www.oaepublish.com/articles/jmi.2024.85)​|
|RL off‑line + modelo|Otimizar setpoints / sequência de operação com segurança|~10³ transições estado–ação–recompensa por regime em dados históricos [sciencedirect+5](https://www.sciencedirect.com/science/article/pii/S0166361522001452)​|10⁴ transições históricas + 10⁴–10⁵ episódios sintéticos via gêmeo digital [acm+7](https://dl.acm.org/doi/10.1016/j.ins.2023.119684)​|[acm+9](https://dl.acm.org/doi/10.1016/j.ins.2023.119684)​|

---

## 7. Ilustrações conceituais úteis

Embora não haja imagens internas aqui, algumas figuras típicas da literatura podem ser usadas como referência visual em relatórios do projeto:

- Diagramas 3D de CFD/MHD de fornos SAF, mostrando eletrodos, banho de escória e linhas de corrente, úteis para explicar como a imersão do eletrodo (não medida diretamente) afeta a distribuição de corrente e o aquecimento Joule.[nature+4](https://www.nature.com/articles/s41598-021-96085-1)​
    
- Esquemas de circuitos elétricos equivalentes de fornos de arco submerso, ilustrando como tensão, corrente e impedância podem ser usados como proxies para a distância efetiva do eletrodo.[ieeexplore.ieee+3](https://ieeexplore.ieee.org/iel8/6287639/6514899/10630793.pdf)​
    
- Gráficos de curvas de aprendizado (erro vs. tamanho do conjunto de treino) em problemas de materiais e metalurgia, mostrando saturação de ganho de desempenho em torno das faixas “ótimas” de dados discutidas.[pubs.aip+5](https://pubs.aip.org/aip/jap/article/128/5/054901/1063225/Small-data-materials-design-with-machine-learning)​
    

Esses elementos visuais, combinados com a arquitetura RAW + Docker do projeto, ajudam a comunicar para as partes interessadas que o foco não é apenas “ter muitos dados”, mas atingir faixas de quantidade aliadas a qualidade, rastreabilidade e conhecimento de processo – fundamentais para modelos confiáveis em ferro‑ligas.[academic.oup+3](https://academic.oup.com/nsr/article/10/7/nwad125/7147579)​

Adicionar a seguimento

Verificar fontes

1. [https://www.sciencedirect.com/science/article/pii/S0307904X16303043](https://www.sciencedirect.com/science/article/pii/S0307904X16303043)
2. [https://royalsocietypublishing.org/rsos/article/4/9/170313/93519/Pragmatic-analysis-of-the-electric-submerged-arc](https://royalsocietypublishing.org/rsos/article/4/9/170313/93519/Pragmatic-analysis-of-the-electric-submerged-arc)
3. [https://www.academia.edu/60855452/Electrode_Erosion_in_Submerged_Arc_Furnaces](https://www.academia.edu/60855452/Electrode_Erosion_in_Submerged_Arc_Furnaces)
4. [https://tohoku.elsevierpure.com/en/publications/machine-learning-strategies-for-small-sample-size-in-materials-sc/](https://tohoku.elsevierpure.com/en/publications/machine-learning-strategies-for-small-sample-size-in-materials-sc/)
5. [https://ieeexplore.ieee.org/iel8/6287639/6514899/10630793.pdf](https://ieeexplore.ieee.org/iel8/6287639/6514899/10630793.pdf)
6. [https://www.imim.pl/files/archiwum/Vol4_2017/54.pdf](https://www.imim.pl/files/archiwum/Vol4_2017/54.pdf)
7. [https://academic.oup.com/nsr/article/10/7/nwad125/7147579](https://academic.oup.com/nsr/article/10/7/nwad125/7147579)
8. [https://pmc.ncbi.nlm.nih.gov/articles/PMC11190561/](https://pmc.ncbi.nlm.nih.gov/articles/PMC11190561/)
9. [https://www.nature.com/articles/s41524-023-01000-z](https://www.nature.com/articles/s41524-023-01000-z)
10. [https://d-nb.info/1257929453/34](https://d-nb.info/1257929453/34)
11. [http://ui.adsabs.harvard.edu/abs/2023npjCM...9...42X/abstract](http://ui.adsabs.harvard.edu/abs/2023npjCM...9...42X/abstract)
12. [https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf](https://open.uct.ac.za/bitstream/11427/14048/1/thesis_ebe_1980_stewart_alistair_bruce.pdf)
13. [https://www.sciencedirect.com/science/article/pii/S2352847824001552](https://www.sciencedirect.com/science/article/pii/S2352847824001552)
14. [https://onlinelibrary.wiley.com/doi/10.1002/srin.201700098](https://onlinelibrary.wiley.com/doi/10.1002/srin.201700098)
15. [https://www.nature.com/articles/s41598-021-96085-1](https://www.nature.com/articles/s41598-021-96085-1)
16. [https://pmc.ncbi.nlm.nih.gov/articles/PMC11285775/](https://pmc.ncbi.nlm.nih.gov/articles/PMC11285775/)
17. [https://pubs.aip.org/aip/jap/article/128/5/054901/1063225/Small-data-materials-design-with-machine-learning](https://pubs.aip.org/aip/jap/article/128/5/054901/1063225/Small-data-materials-design-with-machine-learning)
18. [https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813](https://onlinelibrary.wiley.com/doi/full/10.1002/srin.202100813)
19. [https://research.rug.nl/files/568069032/An_Industry_4.0_example_real_time_quality_control_for_steel_based_mass_production_using_Machine_Learning_on_non_invasive_sensor_data.pdf](https://research.rug.nl/files/568069032/An_Industry_4.0_example_real_time_quality_control_for_steel_based_mass_production_using_Machine_Learning_on_non_invasive_sensor_data.pdf)
20. [https://eds.yildiz.edu.tr/AjaxTool/GetArticleByPublishedArticleId?PublishedArticleId=7378](https://eds.yildiz.edu.tr/AjaxTool/GetArticleByPublishedArticleId?PublishedArticleId=7378)
21. [https://www.sciencedirect.com/science/article/abs/pii/S095741742101085X](https://www.sciencedirect.com/science/article/abs/pii/S095741742101085X)
22. [https://arxiv.org/pdf/2102.09382v1.pdf](https://arxiv.org/pdf/2102.09382v1.pdf)
23. [https://www.oaepublish.com/articles/jmi.2024.85](https://www.oaepublish.com/articles/jmi.2024.85)
24. [https://github.com/jonathanwvd/awesome-industrial-datasets](https://github.com/jonathanwvd/awesome-industrial-datasets)
25. [https://dl.acm.org/doi/10.1016/j.ins.2023.119684](https://dl.acm.org/doi/10.1016/j.ins.2023.119684)
26. [https://research.aalto.fi/files/104297935/1_s2.0_S0020025523003158_main.pdf](https://research.aalto.fi/files/104297935/1_s2.0_S0020025523003158_main.pdf)
27. [https://www.sciencedirect.com/science/article/abs/pii/S0020025523012690](https://www.sciencedirect.com/science/article/abs/pii/S0020025523012690)
28. [https://www.sciencedirect.com/science/article/abs/pii/S0020025523002815](https://www.sciencedirect.com/science/article/abs/pii/S0020025523002815)
29. [https://www.semanticscholar.org/paper/Sample-efficient-reinforcement-learning-with-hybrid-Zheng-Jia/c80ca58148e0bfc065c3b8fad9859e31a36e6007](https://www.semanticscholar.org/paper/Sample-efficient-reinforcement-learning-with-hybrid-Zheng-Jia/c80ca58148e0bfc065c3b8fad9859e31a36e6007)
30. [https://pure.tudelft.nl/ws/portalfiles/portal/142954982/dissertation.pdf](https://pure.tudelft.nl/ws/portalfiles/portal/142954982/dissertation.pdf)
31. [https://www.sciencedirect.com/science/article/pii/S0166361522001452](https://www.sciencedirect.com/science/article/pii/S0166361522001452)
32. [https://www.diva-portal.org/smash/get/diva2:1750818/FULLTEXT01.pdf](https://www.diva-portal.org/smash/get/diva2:1750818/FULLTEXT01.pdf)
33. [https://dl.acm.org/doi/10.1016/j.compind.2022.103748](https://dl.acm.org/doi/10.1016/j.compind.2022.103748)
34. [https://aaltodoc.aalto.fi/items/185cfa25-3d4f-45b9-8be5-f3d1c2001c72](https://aaltodoc.aalto.fi/items/185cfa25-3d4f-45b9-8be5-f3d1c2001c72)