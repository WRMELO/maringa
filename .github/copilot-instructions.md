# Diretrizes para Agentes de IA neste Repositório

## Visão Geral do Projeto
Este repositório contém dados e análises relacionados ao diagnóstico de processos industriais, com foco em ferro-ligas. A estrutura do projeto é organizada em várias pastas principais:

- **`analise/`**: Contém resumos e relatórios analíticos.
- **`bibliografia/`**: Inclui manifestos e documentação de capítulos de referência.
- **`dados/`**: Armazena dados brutos organizados por categorias, como consumo, corridas e informações diárias.
- **`notebook/`**: Contém notebooks Jupyter para tratamento e análise de dados.

## Fluxos de Trabalho Importantes

### 1. Geração de Manifestos
- Arquivos PDF na pasta `bibliografia/` são processados para gerar dois arquivos principais:
  - `manifest_capitulos.csv`: Um manifesto em formato CSV com informações detalhadas sobre os capítulos.
  - `README.md`: Um arquivo Markdown com uma tabela resumida dos capítulos.
- Scripts relevantes para este fluxo estão no notebook `notebook/tratamento_do_handbook.ipynb`.

### 2. Análise de Dados
- Dados brutos estão organizados em subpastas dentro de `dados/dados_iniciais/`.
- Cada subpasta contém arquivos CSV com informações específicas, como consumo e corridas.
- Notebooks na pasta `notebook/` são usados para processar e analisar esses dados.

## Padrões e Convenções

### Estrutura de Dados
- Arquivos CSV seguem uma nomenclatura consistente: `<ano>_<Fase>_<Tipo>.csv`.
  - Exemplo: `2018_F2_Consumo.csv` (dados de consumo da fase 2 de 2018).
- Dados são organizados por ano e fase para facilitar a análise temporal.

### Notebooks
- Notebooks Jupyter são usados para processamento e análise de dados.
- Cada notebook deve ser bem documentado, com células Markdown explicando o propósito de cada etapa.

### Documentação
- A pasta `bibliografia/` contém um manifesto (`manifest_capitulos.csv`) e um README com informações detalhadas sobre os capítulos disponíveis.
- Sempre atualize o manifesto e o README ao adicionar ou modificar arquivos PDF.

## Integrações e Dependências
- O projeto utiliza bibliotecas Python como `pandas` e `fitz` para manipulação de dados e PDFs.
- Certifique-se de instalar as dependências antes de executar os notebooks:
  ```bash
  pip install pandas PyMuPDF
  ```

## Exemplos de Uso

### Processar PDFs e Gerar Manifesto
1. Navegue até a pasta `notebook/`.
2. Abra o notebook `tratamento_do_handbook.ipynb`.
3. Execute as células para processar os PDFs na pasta `bibliografia/` e gerar os arquivos `manifest_capitulos.csv` e `README.md`.

### Analisar Dados de Consumo
1. Localize os arquivos na pasta `dados/dados_iniciais/Consumo Fornos/`.
2. Use um notebook ou script Python para carregar os dados com `pandas`.
3. Realize análises específicas, como tendências de consumo ao longo do tempo.

## Boas Práticas
- Mantenha a consistência na nomenclatura de arquivos e pastas.
- Documente todas as alterações no manifesto e no README.
- Valide os dados antes de realizar análises para evitar erros.

---

Para dúvidas ou melhorias, consulte os responsáveis pelo projeto ou atualize este arquivo com novas diretrizes.