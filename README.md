# Boas Práticas com DynamoDB: Passo a Passo

Este guia apresenta um passo a passo para aplicar boas práticas ao trabalhar com o Amazon DynamoDB.

## 1. Introdução ao DynamoDB

*   **Entenda o que é DynamoDB:**  O DynamoDB é um banco de dados NoSQL totalmente gerenciado, que oferece desempenho rápido e previsível com escalabilidade contínua.
*   **DB Relacional x DB Não Relacional:** Compreenda as diferenças fundamentais entre bancos de dados relacionais (SQL) e não relacionais (NoSQL).  Entenda quando o DynamoDB é uma escolha adequada em relação a um banco de dados relacional.
    *   **Relacional (SQL):** Dados estruturados, esquemas rígidos, ACID (Atomicidade, Consistência, Isolamento, Durabilidade), ideal para transações complexas e relatórios.
    *   **Não Relacional (NoSQL):** Dados semi-estruturados ou não estruturados, esquemas flexíveis, CAP (Consistência, Disponibilidade, Tolerância a Partição), ideal para escalabilidade, alta disponibilidade e dados com evolução rápida.

## 2. Componentes e Boas Práticas com DynamoDB

*   **Tabelas, Itens e Atributos:**
    *   **Tabela:**  Uma coleção de itens.
    *   **Item:**  Um conjunto de atributos. Similar a uma linha em uma tabela relacional.
    *   **Atributo:**  Um elemento de dados dentro de um item.
*   **Chave Primária:**
    *   **Partition Key (Chave de Partição):** Usada para distribuir os dados entre as partições.  A escolha de uma boa Partition Key é crucial para escalabilidade e desempenho.
    *   **Sort Key (Chave de Ordenação):** Usada para ordenar os itens dentro de uma partição.  Opcional.
*   **Boas Práticas:**
    *   **Escolha uma Partition Key com alta cardinalidade:** Uma Partition Key com muitos valores distintos garante uma distribuição uniforme dos dados entre as partições.
    *   **Evite "hot partitions":**  Não direcione todas as requisições para uma única partição.  Distribua as requisições uniformemente.
    *   **Considere Global Secondary Indexes (GSIs):** Crie GSIs para permitir consultas eficientes usando atributos diferentes da chave primária.
    *   **Monitore o consumo de capacidade:** Acompanhe o consumo de unidades de leitura e escrita provisionadas para evitar throttling.
    *   **Use batch operations:**  Agrupe múltiplas operações (GetItem, PutItem, DeleteItem) em lote para reduzir o número de requisições e melhorar o desempenho.
    *   **Otimize as consultas:** Utilize o operador `KeyConditionExpression` para filtrar os dados com base na Partition Key e, se aplicável, na Sort Key.

## 3. Pensando Fora da Caixa no DynamoDB

*   **Modelagem de Dados Não Relacional:**  A modelagem de dados no DynamoDB é diferente da modelagem relacional. Concentre-se nos padrões de acesso aos dados, não apenas na estrutura dos dados.
*   **Denormalização:**  Considere a denormalização de dados para reduzir o número de requisições necessárias para recuperar informações.  Repita dados em múltiplos itens se isso melhorar o desempenho.
*   **Agregação:** Pré-calcule e armazene agregações de dados para evitar cálculos demorados em tempo real.
*   **Padrões de Modelagem:** Explore padrões de modelagem comuns do DynamoDB, como:
    *   **Adjacency List:** Para modelar relacionamentos hierárquicos.
    *   **Composite Keys:** Para combinar múltiplos atributos em uma única chave primária.
    *   **Sparse Indexes:** Para criar índices que indexam apenas um subconjunto dos itens.

## 4. Mãos à Obra: Observando a Arquitetura da Prática

*   **Arquitetura Orientada a Eventos:** DynamoDB se integra bem com arquiteturas orientadas a eventos.  Use o DynamoDB Streams para capturar mudanças nos dados e acionar outras funções ou serviços.
*   **Microserviços:** O DynamoDB é uma boa escolha para microserviços devido à sua escalabilidade e isolamento.  Cada microserviço pode ter sua própria tabela DynamoDB.
*   **Caching:** Use um cache (como o DynamoDB Accelerator - DAX) para reduzir a latência de leitura.

## 5. Configurando o AWS e Criando uma Tabela

*   **Configurar a AWS CLI:** Configure a AWS CLI (Command Line Interface) com suas credenciais para interagir com os serviços da AWS a partir da linha de comando.
*   **Criar uma Tabela:** Use a AWS CLI ou o console da AWS para criar uma tabela DynamoDB.  Defina a chave primária (Partition Key e Sort Key, se necessário) e o provisionamento de capacidade.

**Exemplo de criação de tabela com AWS CLI:**

```bash
aws dynamodb create-table \
    --table-name MinhaTabela \
    --attribute-definitions \
        AttributeName=ID,AttributeType=S \
    --key-schema \
        AttributeName=ID,KeyType=HASH \
    --provisioned-throughput \
        ReadCapacityUnits=5,WriteCapacityUnits=5
```

## 6. Inserindo Itens na Tabela

*   **Inserir Dados:** Use a AWS CLI ou a SDK do DynamoDB para inserir itens na tabela.

**Exemplo de inserção de item com AWS CLI:**

```bash
aws dynamodb put-item \
    --table-name MinhaTabela \
    --item '{"ID": {"S": "item1"}, "Nome": {"S": "Produto A"}, "Preco": {"N": "25.00"}}'
```

## 7. Criando Index

*   **Criar Global Secondary Index (GSI):** Use a AWS CLI ou o console da AWS para criar um GSI.  Defina a chave do índice (Partition Key e Sort Key, se necessário) e os atributos a serem projetados no índice.

**Exemplo de criação de GSI com AWS CLI:**

```bash
aws dynamodb update-table \
    --table-name MinhaTabela \
    --global-secondary-index-updates "[
        {
            \"Create\": {
                \"IndexName\": \"IndicePorNome\",
                \"KeySchema\": [
                    {
                        \"AttributeName\": \"Nome\",
                        \"KeyType\": \"HASH\"
                    }
                ],
                \"Projection\": {
                    \"ProjectionType\": \"ALL\"
                },
                \"ProvisionedThroughput\": {
                    \"ReadCapacityUnits\": 5,
                    \"WriteCapacityUnits\": 5
                }
            }
        }
    ]"
```

## 8. Finalizando o Projeto

*   **Revisão:** Revise o projeto para garantir que as boas práticas do DynamoDB foram aplicadas corretamente.
*   **Testes:** Realize testes para verificar o desempenho e a escalabilidade do projeto.
*   **Documentação:** Documente a arquitetura, o modelo de dados e as decisões tomadas durante o desenvolvimento.
*   **Otimização:** Otimize continuamente o projeto com base nos resultados dos testes e no monitoramento do consumo de capacidade.
