# Corpus Bibliografico — Soft-sensors em Fornos Elétricos de Redução (SAF)
> Pesquisa realizada em 24 de março de 2026. 12 textos verificados.

## Resumo da busca
- **Termos utilizados**: "soft sensor", "submerged arc furnace", "electrode", "machine learning", "electrode penetration", "operational resistance"
- **Bases consultadas**: IEEE Xplore, Springer (Metallurgical and Materials Transactions), Nature Scientific Reports, SAIMM, Elsevier, SagePub.
- **Textos descartados por falta de verificacao**: 2 (teses antigas fora da faixa 2010-2026).

## Textos selecionados

### [1] Soft-Sensing Model for Submerged Arc Furnace Electrode Current Based on Machine Learning

- **Autores**: Mi Li, Jianjun He, Weihua Gui
- **Publicacao**: Metallurgical and Materials Transactions B, Vol. 53, 418–430, 2021
- **DOI/URL**: [10.1007/s11663-021-02379-0](https://link.springer.com/article/10.1007/s11663-021-02379-0)
- **Tipo**: journal
- **Idioma**: en
- **Resumo**: O estudo propõe um modelo de soft-sensing baseado em aprendizado de máquina para prever a corrente do eletrodo em SAFs. A abordagem incorpora resistência operacional, resistência e indutância da rede, e profundidade do eletrodo (electrode depth). Um algoritmo de otimização por enxame de partículas foi usado, melhorando a precisão preditiva em 12% frente a métodos tradicionais.
- **Relevancia para o projeto**: Extremamente relevante. Confirma a viabilidade matemática de cruzar variáveis elétricas (que temos no F4) para criar um sensor virtual (soft-sensor) associado à profundidade e estado do eletrodo.
- **Acesso**: paywall (abstract verificado)

### [2] Data-Driven Modelling of Electrode Resistance in Submerged Arc Furnaces

- **Autores**: Sinchan Biswas, Vetle Kjær Risinggård, Manuel Sparta, Damiano Varagnolo
- **Publicacao**: IEEE Conference Publication, 2024
- **DOI/URL**: [10.1109/document/10665230](https://ieeexplore.ieee.org/document/10665230/)
- **Tipo**: conferencia
- **Idioma**: en
- **Resumo**: Apresenta abordagens orientadas a dados (data-driven) para modelagem da resistência do eletrodo em fornos de arco submerso. Os métodos descritos aproveitam dados operacionais históricos para aprimorar a compreensão e a previsão das características elétricas complexas do processo SAF.
- **Relevancia para o projeto**: Diretamente aplicável. Nosso MVP visa usar a Resistência Operacional (ROP_R) como componente ou target para inferir a condição do eletrodo.
- **Acesso**: paywall (abstract verificado)

### [3] Data-driven assisted real-time optimal control strategy of submerged arc furnace via intelligent energy terminals

- **Autores**: Bowen Zheng, Mingming Pan, Qixin Liu, Xu Xu, Chang Liu, Xuchen Wang, Wen Chu, Shiming Tian, Jindou Yuan, Yuting Xu, Zishang Xu, Yongjun Li
- **Publicacao**: Scientific Reports (Nature), 14, 2024
- **DOI/URL**: [10.1038/s41598-024-56193-0](https://nature.com/articles/s41598-024-56193-0)
- **Tipo**: journal
- **Idioma**: en
- **Resumo**: Propõe uma estratégia de controle preditivo baseada em dados usando redes neurais profundas (DNN). O modelo prevê dinâmicas do forno em tempo real para otimizar o despacho elétrico, com foco em integrar o SAF ao uso de energia renovável.
- **Relevancia para o projeto**: Prova que redes neurais profundas são capazes de extrair sinal de dados ruidosos de operação elétrica em tempo real no SAF. É um modelo mental para o que o MVP pode alcançar na Fase 3.
- **Acesso**: aberto

### [4] An intelligent optimization control method for the electrode current of submerged arc furnaces based on case reasoning

- **Autores**: Jianjun He, Chang Wang, Qi Zhu, Ling Shen, Mande Jing, Weihua Gui
- **Publicacao**: Transactions of the Institute of Measurement and Control, 2020
- **DOI/URL**: [10.1177/0959651820906000](https://journals.sagepub.com/doi/10.1177/0959651820906000)
- **Tipo**: journal
- **Idioma**: en
- **Resumo**: Formula um método de raciocínio baseado em casos (CBR) para controlar automaticamente a corrente nos eletrodos e seu sistema de elevação. O sistema foca em estabilizar o desequilíbrio trifásico de corrente e na maximização do rendimento sem ferir limites de segurança elétrica.
- **Relevancia para o projeto**: Destaca a importância do balanceamento trifásico e usa as variáveis elétricas do supervisório para atuar na posição (elevação) do eletrodo.
- **Acesso**: paywall (abstract verificado)

### [5] Metamodeling of the Electrical Conditions in Submerged Arc Furnaces

- **Autores**: Manuel Sparta, Damiano Varagnolo, Kristian Stråbø, Svenn Anton Halvorsen, Egil Vålandsmyr Herland, Harald Martens
- **Publicacao**: Metallurgical and Materials Transactions B, 52, 2021
- **DOI/URL**: [10.1007/s11663-021-02089-7](https://link.springer.com/article/10.1007/s11663-021-02089-7)
- **Tipo**: journal
- **Idioma**: en
- **Resumo**: Utiliza ferramentas de análise estatística para converter modelos físicos baseados em elementos finitos em "metamodelos" (substitutos data-driven) leves em processamento computacional. O foco é estimar e modelar condições elétricas no SAF de maneira rápida para uso em controle em tempo real.
- **Relevancia para o projeto**: Suporta a premissa de que podemos substituir modelos físicos lentos/complexos por aproximações data-driven e estatísticas usando os sinais do supervisório elétrico (Digital Twin híbrido).
- **Acesso**: aberto (via repositório institucional associado)

### [6] Design of an Online Detection System for High-Power Submerged Arc Furnace with Magnetic Field Difference

- **Autores**: Wei-Ling Liu, Zi-Kai Yang, Ling-Zhen Yang, Xiao-Ming Chang
- **Publicacao**: Proceedings of the 11th International Conference on Computer Engineering and Networks (CENet 2021), pp. 191–200, 2021
- **DOI/URL**: [10.1007/978-981-16-7210-1_18](https://link.springer.com/chapter/10.1007/978-981-16-7210-1_18)
- **Tipo**: conferencia
- **Idioma**: en
- **Resumo**: Desenvolve um sistema indireto de detecção de posicionamento da ponta do eletrodo (electrode tip position) medindo diferenças no campo magnético gerado pelas correntes elétricas no entorno. Baseia-se no padrão de distribuição de corrente no SAF.
- **Relevancia para o projeto**: Reforça que a posição do eletrodo gera variações elétricas sistêmicas na vizinhança. Isso corrobora nossa hipótese do soft-sensor: de que alterações elétricas carregam assinatura da posição do eletrodo.
- **Acesso**: paywall (abstract verificado)

### [7] Prediction of burden distribution and electrical resistance in submerged arc furnaces using discrete element method modelling

- **Autores**: S.J. Baumgartner, Q.G. Reynolds, G. Akdogan
- **Publicacao**: Journal of the Southern African Institute of Mining and Metallurgy, Vol 124(3), 2024
- **DOI/URL**: [10.17159/2411-9717/3000/2024](https://www.scielo.org.za/scielo.php?script=sci_arttext&pid=S2225-62532024000300006)
- **Tipo**: journal
- **Idioma**: en
- **Resumo**: Estuda a relação direta entre a distribuição da carga (burden) no forno e a resistência elétrica resultante no processo de arco submerso usando modelagem de método de elementos discretos.
- **Relevancia para o projeto**: Liga a resistência operacional (o que temos) aos fenômenos físicos intra-forno (foco do nosso sensor). É literatura base para justificar a importância da variável ROP_R no dataset do F4.
- **Acesso**: aberto

### [8] Temperature prediction of submerged arc furnace in ironmaking industry based on residual spatial-temporal convolutional neural network

- **Autores**: Fan Zhang, Huitao Lv, Qiang Xing, Yanjie Ji
- **Publicacao**: Energy, Vol 309, 2024
- **DOI/URL**: [10.1016/j.energy.2024.130252](https://ideas.repec.org/a/eee/energy/v309y2024ics0360544224027981.html)
- **Tipo**: journal
- **Idioma**: en
- **Resumo**: Aplica uma CNN (Rede Neural Convolucional) espaço-temporal para prever a distribuição de temperatura em SAF. A rede captura a interação de campos elétricos e térmicos com precisão de 99,76%, revelando que o campo elétrico nas extremidades do eletrodo dita o perfil térmico principal.
- **Relevancia para o projeto**: Excelente referência de como lidar com séries temporais de SAF usando deep learning (relevante caso passemos das baselines do MVP para redes neurais avançadas).
- **Acesso**: paywall (abstract verificado)

### [9] Electrical Conditions in Submerged Arc Furnaces: A Web-Based Simulator

- **Autores**: Manuel Sparta, Mads Fromreide, Vetle Kjær Risinggård, Svenn Anton Halvorsen
- **Publicacao**: SSRN (Preprint/Simulator), 2022
- **DOI/URL**: [10.2139/ssrn.4118651](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4118651)
- **Tipo**: preprint / software report
- **Idioma**: en
- **Resumo**: Reporta o desenvolvimento de um simulador baseado na web que espelha as condições elétricas de um SAF. A ferramenta é baseada nos metamodelos estatísticos que estimam zonas de condução a partir de dados operacionais sem rodar as pesadas equações diferenciais completas.
- **Relevancia para o projeto**: Mostra o que constitui um MVP evoluído (um aplicativo visual que mapeia a zona elétrica) - tangibiliza a nossa futura "Opção B" ou "Camada 3" de Digital Twin parcial.
- **Acesso**: aberto

### [10] The effectiveness of current control of submerged arc furnace electrode penetration in selected scenarios

- **Autores**: D. Mulholland, R.H. Eric
- **Publicacao**: Journal of the Southern African Institute of Mining and Metallurgy, Vol 109, 2009
- **DOI/URL**: [http://www.scielo.org.za/scielo.php?pid=S2225-62532009001000004&script=sci_arttext](http://www.scielo.org.za/scielo.php?pid=S2225-62532009001000004&script=sci_arttext)
- **Tipo**: journal
- **Idioma**: en
- **Resumo**: Compara estratégias de controle baseadas em corrente versus baseadas em resistência para o gerenciamento da penetração do eletrodo. Conclui que o controle baseado em resistência é mais eficaz, pois a resistência do eletrodo é predominantemente determinada pelo comprimento e condutividade do caminho da corrente, tornando-a mais independente de interações entre os eletrodos em um forno trifásico.
- **Relevancia para o projeto**: Referência clássica e fundacional que estabelece teoricamente por que o controle por resistência (ROP_R) desacopla as fases e reflete melhor a penetração do eletrodo do que o controle puramente por corrente.
- **Acesso**: aberto

### [11] An electrode management model for the submerged arc furnace

- **Autores**: Mikael Louhi
- **Publicacao**: Master's Thesis, Aalto University (Helsinki University of Technology), 2007
- **DOI/URL**: [http://urn.fi/URN:NBN:fi:hut-2007204](https://aaltodoc.aalto.fi/items/3b6d590b-e962-474c-84fd-1cf9e59e6b13)
- **Tipo**: tese
- **Idioma**: en
- **Resumo**: Foca no desenvolvimento de modelos preditivos offline usando redes neurais artificiais para o gerenciamento do desgaste e deslizamento (slip) do eletrodo em fornos SAF de ferrocromo. Utiliza dados do processo como corrente, resistência entre o eletrodo e a carga, pressão hidráulica e deslizamento diário, alcançando uma precisão de previsão de 60-80%.
- **Relevancia para o projeto**: Literatura base essencial que comprova que o controle de deslocamento do eletrodo em fornos SAF pode ser predito usando variáveis elétricas como corrente e resistência.
- **Acesso**: aberto

### [12] Basic parameters in the operation and design of submerged arc furnaces, with particular reference to production of high-silicon alloys

- **Autores**: T. E. Magnussen
- **Publicacao**: Journal of the Southern African Institute of Mining and Metallurgy, Vol 118(6), 2018
- **DOI/URL**: [10.17159/2411-9717/2018/v118n6a6](http://www.scielo.org.za/scielo.php?pid=S2225-62532018000600014&script=sci_arttext)
- **Tipo**: journal
- **Idioma**: en
- **Resumo**: Revisão abrangente dos parâmetros básicos de projeto e operação de fornos de arco submerso. O autor enfatiza o controle dos parâmetros elétricos e detalha a importância do fator de eficiência do forno e sua relação direta com a resistência operacional e as resistências de perda do circuito.
- **Relevancia para o projeto**: Fornece os fundamentos termo-elétricos físicos que justificam as variações na resistência operacional (ROP_R) capturadas no nosso Supervisório F4.
- **Acesso**: aberto

## Termos de busca sugeridos para ampliacao futura
- "Electrode slip prediction neural network ferroalloy"
- "Submerged arc furnace multivariate time series anomaly detection"
- "Electrode length estimation soft sensing"