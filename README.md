# Arquitetura Completa da Solução SimpleOrder na Microsoft Azure

## Visão Geral do Projeto

**Nome da Aplicação:** SimpleOrder  
**Objetivo da Migração:** Modernizar a aplicação SimpleOrder, que atualmente opera com uma arquitetura monolítica LAMP (Linux, Apache, MySQL, PHP) em um único servidor local, migrando para a Microsoft Azure para obter os seguintes benefícios:
- **Escalabilidade**: Suporte a aumento de carga com recursos de escalabilidade automática.
- **Segurança**: Implementação de autenticação avançada, criptografia e gestão de segredos.
- **Análise e Armazenamento de Dados**: Capacidade de gerar relatórios e insights dos dados com ferramentas de análise e armazenamento adequados.
- **Inteligência Artificial**: Uso de IA para personalização e recomendações de produtos.

## Benefícios e Melhorias da Migração para Azure

1. **Escalabilidade e Alta Disponibilidade**  
   Com o uso do **Azure Kubernetes Service (AKS)** e **Azure SQL Database**, a aplicação poderá escalar automaticamente conforme a demanda, sem depender de um único servidor físico. O AKS permite orquestração de contêineres, facilitando o gerenciamento e escalabilidade dos microserviços.

2. **Segurança**  
   A aplicação utilizará **Azure Active Directory (AAD)** para autenticação centralizada e **Azure Key Vault** para gestão segura de segredos e chaves. Essa infraestrutura de segurança elimina vulnerabilidades de acesso, implementando autenticação multifator e políticas de segurança.

3. **Armazenamento de Dados e Relatórios**  
   O uso de **Azure SQL Database** e **Azure Cosmos DB** permite um armazenamento mais robusto e flexível, com capacidade para gerar relatórios detalhados e acessar dados rapidamente para tomada de decisão. O **Azure Synapse Analytics** facilita a análise de dados e geração de insights.

4. **Inteligência Artificial para Recomendação de Produtos**  
   Com **Azure Machine Learning**, a aplicação poderá oferecer recomendações de produtos baseadas em histórico de pedidos, melhorando a experiência do cliente e promovendo produtos de forma personalizada.

---

## Diagrama de Arquitetura

```plaintext
                        ┌──────────────────────────────┐
                        │        GitHub Actions        │
                        │       (CI/CD Pipeline)       │
                        └─────────────┬────────────────┘
                                      │
                           ┌──────────▼───────────┐
                           │      Build &         │
                           │ Push Docker Images   │
                           │ to Azure Container   │
                           │ Registry (ACR)       │
                           └──────────┬───────────┘
                                      │
                        ┌─────────────▼──────────────┐
                        │    Deploy Docker Images    │
                        │   to AKS (Azure Kubernetes │
                        │   Service)                 │
                        └─────────────┬──────────────┘
                                      │
                        ┌─────────────▼──────────────┐
                        │     Azure Front Door       │
                        │     (Traffic Mgmt)         │
                        └─────────────┬──────────────┘
                                      │
                           ┌──────────▼───────────┐
                           │   Azure Load Balancer│
                           │  (App Balancing)     │
                           └──────────┬───────────┘
                                      │
                    ┌─────────────────▼─────────────────┐
                    │         Application Layer         │
                    │       (Azure Kubernetes Service)  │
                    └───────────────────────────────────┘
                                      │
        ┌─────────────────────────────┼───────────────────────────────┐
        │                             │                               │
┌───────▼────────┐            ┌───────▼─────────┐          ┌─────────▼────────┐
│Order Management│            │   Inventory      │          │     Reporting    │
│  Microservice  │            │   Microservice   │          │    Microservice  │
└────────────────┘            └──────────────────┘          └──────────────────┘
        │                             │                               │
        │                             │                               │
  ┌─────▼───────┐               ┌─────▼───────┐              ┌───────▼────────┐
  │Azure SQL    │               │Azure SQL    │              │ Azure Cosmos    │
  │Database     │               │Database     │              │ DB / Table      │
  └─────────────┘               └─────────────┘              │ Storage         │
        │                             │                      │ (Reporting)     │
  Tabelas:                      Tabelas:                      Tabelas:
   - orders                       - inventory                   - reports
     (id, cliente_id,             - produtos                   - log_reports
     produto_id,                    (id, nome, preco)          - analytics_data
     quantidade,                                                 
     data_pedido)                                            
   - clientes                                              
     (id, nome, email)                          

        ┌─────────────────────────────┬───────────────────────────────┐
                                      │
                        ┌─────────────▼───────────────┐
                        │        Azure Key Vault      │
                        │      (Secrets Management)   │
                        └─────────────┬───────────────┘
                                      │
                        ┌─────────────▼───────────────┐
                        │      Azure Active Directory │
                        │      (Auth Management)      │
                        └─────────────┬───────────────┘
                                      │
               ┌──────────────────────▼────────────────────────┐
               │             Data & Analytics Layer            │
               └───────────────────────────────────────────────┘
                                      │
                        ┌─────────────▼───────────────┐
                        │     Azure Synapse Analytics │
                        │      (Data Analysis)        │
                        └─────────────┬───────────────┘
                                      │
                        ┌─────────────▼───────────────┐
                        │      Azure Machine Learning │
                        │   (Product Recommendations) │
                        └─────────────────────────────┘
                     
                     ┌────────────────────────────┐
                     │     Message Broker         │
                     │  Azure Service Bus / Event │
                     │ Hub for async messaging    │
                     └────────────────────────────┘
```

### Diferenças:

1.  **Mercado Regulamentado**:

    -   É estabelecido por leis ou regulamentos governamentais.
    -   As empresas são obrigadas a cumprir limites de emissão de carbono.
    -   Exemplos incluem o Sistema de Comércio de Emissões da UE e o Cap-and-Trade na Califórnia.
2.  **Mercado Voluntário**:

    -   Funciona sem imposições legais.
    -   Empresas ou indivíduos participam para atingir metas de sustentabilidade ou responsabilidade social.
    -   Os créditos de carbono são comprados para compensar emissões voluntariamente.

### Setor Excluído:

De acordo com a proposta de regulação do mercado de carbono brasileiro, um setor que foi excluído da regulação, permanecendo no segmento voluntário, é o **setor de agricultura**. Este setor ainda pode participar de iniciativas voluntárias para compensação de carbono.Descrição dos Componentes da Arquitetura
----------------------------------------

1.  **Pipeline CI/CD (GitHub Actions)**\
    Configurado para construção e implantação automática de imagens Docker:

    -   **Build and Push Docker Images**: Constrói as imagens Docker para cada microserviço e envia para o *Azure Container Registry (ACR)*.
    -   **Deploy to AKS**: Implanta as imagens no *AKS*, gerando uma arquitetura de contêiner escalável e resiliente.
2.  **Application Layer (AKS)**

    -   **Azure Kubernetes Service (AKS)**: Gerencia os contêineres dos microserviços PHP:
        -   **Order Management Microservice**: Responsável por gerenciar pedidos, conectado ao *Azure SQL Database*.
        -   **Inventory Microservice**: Gerencia o estoque, com acesso ao *Azure SQL Database*.
        -   **Reporting Microservice**: Gera relatórios, com armazenamento em *Azure Cosmos DB* ou *Azure Table Storage*.
3.  **Bancos de Dados e Estruturas de Tabelas**

    -   **Order Management**:

        -   Banco de Dados: *Azure SQL Database*
        -   Tabelas:
            -   `orders` (id, cliente_id, produto_id, quantidade, data_pedido)
            -   `clientes` (id, nome, email)
    -   **Inventory**:

        -   Banco de Dados: *Azure SQL Database*
        -   Tabelas:
            -   `inventory` (id, produto_id, quantidade, data_atualizacao)
            -   `produtos` (id, nome, preco)
    -   **Reporting**:

        -   Banco de Dados: *Azure Cosmos DB* ou *Azure Table Storage*
        -   Tabelas:
            -   `reports`
            -   `log_reports`
            -   `analytics_data`
4.  **Segurança e Autenticação**

    -   **Azure Key Vault**: Armazena credenciais e segredos com segurança.
    -   **Azure Active Directory**: Fornece autenticação centralizada e segura, com suporte a autenticação multifator.
5.  **Data & Analytics Layer**

    -   **Azure Synapse Analytics**: Oferece análise dos dados para gerar relatórios e insights.
    -   **Azure Machine Learning**: Permite gerar recomendações de produtos baseadas em IA.
6.  **Message Broker (Comunicação Assíncrona)**

    -   **Azure Service Bus ou Event Hub**: Facilita a comunicação entre microserviços, melhorando a escalabilidade e resiliência da aplicação.

* * * * *

Essa documentação detalha todos os elementos necessários para migrar a aplicação SimpleOrder para a Azure, proporcionando uma solução moderna, escalável e segura.