
## 1.1. Objetivo da Fase 1

Esta fase define **como o data lake será materializado fisicamente em MinIO** e como ele se conecta ao ambiente de trabalho `conda ds-base`.

Objetivos específicos:

1. Definir a **estrutura física** do data lake no MinIO (buckets e convenções de diretórios).
2. Definir a **ponte** entre o repositório local `MARINGA` e o MinIO (o que sai de `dados_iniciais` e para onde vai).
3. Definir os **parâmetros de infraestrutura** mínimos que o Agente de Código deverá respeitar ao instalar e configurar o MinIO.
4. Deixar clara a **sequência de atividades** que transforma a pasta local em um data lake RAW organizado.

O resultado da Fase 1 é um **MinIO operacional**, com buckets criados e prontos para receber os dados, e um documento de referência com todas as decisões de infraestrutura.

---

## 1.2. Situação atual e ponto de partida

O repositório local está organizado da seguinte forma (relevante para a Etapa 1):

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

1. A pasta `fase_1_diagnostico/dados/dados_iniciais/` é a **fonte oficial** dos dados brutos para a Etapa 1.
    
2. Nenhum arquivo em `dados_iniciais` deve ser alterado ou renomeado pelo Agente de Código.
    
    - Eles são o espelho da entrega original.
        
3. As subpastas de `dados_iniciais` serão mapeadas para domínios lógicos do data lake (ver seção 1.4).
    

Os diretórios `manifestos_inicial` e `manifestos_final` contêm manifestos já existentes da primeira tentativa; eles serão utilizados posteriormente na **Fase 2** (catálogo e manifesto unificado), mas já são reconhecidos como parte da arquitetura.

---

## 1.3. Decisões de infraestrutura MinIO e ambiente ds-base

### 1.3.1. Papel do MinIO

Decisões:

1. MinIO será o **repositório central** do data lake da Etapa 1.
    
2. Todos os arquivos relevantes para diagnóstico e modelagem migrarão, em cópia, do diretório local `dados_iniciais` para buckets no MinIO.
    
3. O MinIO será acessado a partir do ambiente `conda ds-base` por meio de bibliotecas cliente (Python), sem acessos diretos ao sistema de arquivos do servidor MinIO a partir dos notebooks.
    

### 1.3.2. Papel do ambiente conda `ds-base`

Decisões:

1. O ambiente `conda ds-base` será o **ambiente padrão de trabalho** para:
    
    - notebooks de diagnóstico;
        
    - scripts de ingestão e organização no MinIO;
        
    - scripts de leitura e análise de completude.
        
2. O Agente de Código deverá:
    
    - garantir que o ambiente `ds-base` esteja criado e ativável;
        
    - instalar as bibliotecas necessárias para comunicação com MinIO (por exemplo, cliente Python do MinIO) e manipulação de dados (Pandas, Polars ou equivalente).
        
3. O servidor MinIO pode rodar em outro contexto (por exemplo, processo dedicado ou container), desde que seja acessível via URL pelo ambiente `ds-base`.
    
    - A decisão de “como” instalar (Docker, binário, serviço do sistema) será detalhada em instruções técnicas específicas, mas os **parâmetros lógicos** serão padronizados nesta fase.
        

### 1.3.3. Parâmetros lógicos a serem padronizados

Independente da forma de instalação, o MinIO deverá expor:

- Um **endpoint HTTP** acessível localmente (por exemplo, `http://localhost:9000` para API).
    
- Um **par de credenciais** específicas para o projeto MARINGA, que serão armazenadas de forma segura em configuração local do usuário (e não no código-fonte).
    
- Um conjunto de **buckets padronizados** definidos na seção 1.4.
    

Esses parâmetros serão descritos em um arquivo de configuração (por exemplo, um `.md` ou `.env` fora do controle de versão, conforme você decidir posteriormente), que o Agente de Código deverá respeitar.

---

## 1.4. Desenho dos buckets e convenções de diretórios

### 1.4.1. Buckets do projeto MARINGA

Decisões (congeladas):

1. Será criado um conjunto de buckets específicos para o projeto, com os seguintes nomes lógicos:
    
    - `maringa-raw`
        
        - Recebe os arquivos brutos, copiados de `dados_iniciais`, preservando o conteúdo original.
            
    - `maringa-refined`
        
        - Recebe arquivos já padronizados (camada refinada por domínio).
            
    - `maringa-derived`
        
        - Recebe agregações, tabelões e outros produtos analíticos.
            
    - `maringa-manifests`
        
        - Recebe manifestos unificados, índices e metadados gerados a partir das Fases 2 e 3.
            
2. Todo desenvolvimento posterior (Fases 2 a 5) assume que esses quatro buckets existem e estão acessíveis.
    

### 1.4.2. Convenção de diretórios dentro dos buckets

Dentro de cada bucket, será adotado o seguinte padrão de caminho lógico:

```text
<dominio>/<forno>/<granularidade>/<ano>/<mes>/<arquivo>
```

Onde:

- `dominio` – representa o tipo lógico de dado:
    
    - `consumo_fornos`
        
    - `corridas`
        
    - `dicionario`
        
    - `eletrodo`
        
    - `informacoes_diarias`
        
    - `supervisorio`
        
    - outros, se forem identificados depois.
        
- `forno` – identificador padronizado do forno:
    
    - `F1`, `F2`, `F3`, `F4`, `F5`, ou `todos` quando o arquivo não for específico de um forno.
        
- `granularidade` – referência à granularidade original ou derivada do arquivo:
    
    - `corrida`, `minuto`, `hora`, `dia`, `meta` (para dicionários e documentação).
        
- `ano` e `mes` – obtidos a partir da informação de período coberto pelo arquivo (quando possível).
    

Exemplo de caminhos esperados em `maringa-raw`:

- `supervisorio/F4/minuto/2023/01/supervisorio_F4_2023-01.csv`
    
- `consumo_fornos/F5/hora/2023/01/consumo_F5_2023-01.csv`
    
- `corridas/F4/corrida/2023/01/corridas_F4_2023-01.xlsx`
    
- `dicionario/todos/meta/0000/00/dicionario_campos.xlsx`
    

### 1.4.3. Mapeamento inicial das pastas locais para domínios

Mapeamento conceitual (a ser detalhado na Fase 2):

- `Consumo Fornos/` → domínio `consumo_fornos`
    
- `Corridas/` → domínio `corridas`
    
- `Dicionário/` → domínio `dicionario`
    
- `Eletrodo/` → domínio `eletrodo`
    
- `Informações Diária/` → domínio `informacoes_diarias`
    
- `Supervisório Forno 4/` → domínio `supervisorio`, forno `F4`
    
- `Supervisório Forno 5/` → domínio `supervisorio`, forno `F5`
    

Este mapeamento será utilizado pelo Agente de Código para definir o `dominio` e `forno` ao migrar arquivos para `maringa-raw`.

---

## 1.5. Sequência de atividades da Fase 1

Esta subseção define a sequência de atividades para orientar o trabalho do Agente de Código e do próprio Owner.

A Fase 1 termina quando:

- MinIO estiver instalado e operacional;
    
- os buckets definidos na seção 1.4 tiverem sido criados;
    
- existir uma primeira cópia organizada dos arquivos brutos em `maringa-raw`.
    

### Atividade 1 – Preparar o ambiente `conda ds-base`

Responsável: Agente de Código (com validação do Owner).

Passos conceituais:

1. Verificar se o ambiente `ds-base` existe; criar se necessário.
    
2. Garantir que o ambiente contém:
    
    - biblioteca cliente para MinIO;
        
    - bibliotecas básicas para manipulação de dados (Pandas/Polars);
        
    - qualquer outra dependência mínima acordada.
        
3. Registrar, em documento próprio, o comando de ativação do ambiente e a lista de pacotes mínimos.
    

Resultado esperado:

- Ambiente `ds-base` pronto para executar notebooks e scripts que interajam com MinIO.
    

### Atividade 2 – Instalar e subir o servidor MinIO

Responsável: Agente de Código (com decisão do Owner sobre o método de instalação).

Passos conceituais:

1. Escolher método de instalação:
    
    - execúvel local;
        
    - container;
        
    - outro método suportado.
        
2. Configurar o serviço MinIO para:
    
    - expor endpoint de API acessível localmente (por exemplo, `http://localhost:9000`);
        
    - definir usuário e senha do projeto MARINGA (sem expor essas credenciais no repositório).
        
3. Validar:
    
    - o serviço responde no endpoint configurado;
        
    - é possível listar/ criar buckets usando um cliente (por exemplo, via script de teste em `ds-base`).
        

Resultado esperado:

- Servidor MinIO em execução, com acesso confirmado a partir do ambiente `ds-base`.
    

### Atividade 3 – Criar os buckets do projeto

Responsável: Agente de Código.

Passos conceituais:

1. Conectar ao MinIO com as credenciais definidas.
    
2. Criar os buckets:
    
    - `maringa-raw`
        
    - `maringa-refined`
        
    - `maringa-derived`
        
    - `maringa-manifests`
        
3. Registrar a criação e eventuais configurações adicionais (políticas de acesso, versionamento, etc.) em um documento de infraestrutura.
    

Resultado esperado:

- Buckets do projeto criados e disponíveis para uso nas Fases 2, 3 e 4.
    

### Atividade 4 – Validar a comunicação entre `ds-base` e MinIO

Responsável: Agente de Código.

Passos conceituais:

1. A partir de um notebook ou script rodando no ambiente `ds-base`, executar um teste que:
    
    - liste os buckets disponíveis;
        
    - crie e apague um objeto de teste em `maringa-raw`.
        
2. Documentar o teste e o resultado (por exemplo, em um arquivo `.md` de “Teste de Conectividade MinIO”).
    

Resultado esperado:

- Confirmado que o ambiente `ds-base` consegue ler/escrever no MinIO.
    

### Atividade 5 – Planejar a migração inicial de `dados_iniciais` para `maringa-raw`

Responsável: Planejador (esta fase) e Agente de Código (execução nas próximas fases).

Decisão:

- A Fase 1 **não executa ainda** a migração completa, mas define **como** ela deve ser feita em alto nível, para uso na Fase 2.
    

Diretrizes:

1. A migração deve:
    
    - preservar o conteúdo dos arquivos;
        
    - inferir domínio e forno a partir do caminho local (seção 1.4.3);
        
    - organizar o path no MinIO conforme `<dominio>/<forno>/<granularidade>/<ano>/<mes>/`.
        
2. A decisão da granularidade (`corrida`, `minuto`, `hora`, `dia`, `meta`) pode ser tomada inicialmente por regra simples (nome da pasta / arquivo), e refinada depois na Fase 2.
    
3. Antes de qualquer migração real:
    
    - deve existir um “modo de simulação” (dry-run) que apenas liste o que **seria** copiado e para onde.
        

Resultado esperado:

- Regras de migração definidas conceitualmente, prontas para serem implementadas com segurança na Fase 2.
    

---

## 1.6. Entregáveis da Fase 1

Ao concluir a Fase 1, deverão existir:

1. Servidor MinIO instalado e operacional.
    
2. Ambiente `conda ds-base` preparado e testado para se comunicar com MinIO.
    
3. Buckets do projeto criados:
    
    - `maringa-raw`
        
    - `maringa-refined`
        
    - `maringa-derived`
        
    - `maringa-manifests`
        
4. Documento de infraestrutura descrevendo:
    
    - endpoint do MinIO;
        
    - procedimentos de inicialização/parada;
        
    - comandos básicos de teste.
        
5. Diretrizes conceituais de migração de `dados_iniciais` para `maringa-raw`, incluindo:
    
    - mapeamento de pastas locais para domínios;
        
    - padrão de path interno nos buckets.
        
