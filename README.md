# Plano de Modernização da Área de Engenharia de Dados

## Documento Técnico Expandido (Versão Detalhada)

Período do projeto: **Abril até Dezembro (ano corrente)**\
Escopo: Modernização completa da estrutura de dados, migração de
sistemas legados e criação de um Data Lake + Data Warehouse escalável na
AWS.

------------------------------------------------------------------------

# 1. Objetivo Estratégico

O objetivo deste plano é transformar o ambiente atual, que hoje é
fortemente dependente de aplicações monolíticas e bancos transacionais
isolados, em uma **plataforma moderna de dados** capaz de:

-   Escalar conforme o crescimento do negócio
-   Reduzir dependência de sistemas legados
-   Permitir integração com novas aplicações
-   Reduzir custo operacional no médio prazo
-   Permitir criação de dashboards, análises e modelos analíticos
-   Centralizar dados em um único repositório confiável

------------------------------------------------------------------------

# 8. Estimativa Realista de Custos AWS (Atualizada)

A estimativa anterior considerava apenas os serviços principais (S3,
ECS, Athena e Glue). No entanto, para um ambiente completo de engenharia
de dados em produção, é necessário considerar também:

-   Custos de rede (VPC, NAT Gateway, tráfego interno)
-   Custos de logs (CloudWatch)
-   DNS e subdomínios (Route 53)
-   Certificados SSL (ACM)
-   Transferência de dados
-   Requisições S3 (não apenas armazenamento)
-   EFS com uso contínuo
-   Containers rodando 24h (Airflow, Kafka, DBT)

------------------------------------------------------------------------

## 8.1 Infraestrutura Base (Custos que normalmente não aparecem na primeira estimativa)

  ------------------------------------------------------------------------
  Serviço         Descrição             Estimativa Mensal
  --------------- --------------------- ----------------------------------
  VPC + tráfego   Comunicação entre     US\$ 40 -- 80
  interno         ECS, S3, Athena e     
                  containers            

  NAT Gateway     Necessário para       US\$ 60 -- 120
                  containers acessarem  
                  internet              

  CloudWatch Logs Logs do Airflow,      US\$ 30 -- 70
                  Kafka, ECS e          
                  pipelines             

  Transferência   Tráfego entre         US\$ 20 -- 50
  de dados        serviços AWS          
  interna                               

  Route 53        Domínio para Airflow  US\$ 10 -- 25
  (zona + DNS +   e Kafka               
  subdomínios)                          

  Certificados    HTTPS para acesso aos US\$ 5 -- 15
  SSL (ACM)       serviços              
  ------------------------------------------------------------------------

**Subtotal Infraestrutura Base: US\$ 170 -- 300 / mês**

------------------------------------------------------------------------

## 8.2 Camada de Processamento (Airflow + Kafka + DBT)

  -----------------------------------------------------------------------------
  Serviço              Descrição             Estimativa Mensal
  -------------------- --------------------- ----------------------------------
  ECS Cluster          Execução dos          US\$ 60 -- 120
                       containers            

  Airflow (rodando     Orquestração dos      US\$ 80 -- 140
  24h)                 pipelines             

  Kafka container      Streaming e ingestão  US\$ 60 -- 120
                       de dados              

  DBT container        Transformações SQL    US\$ 20 -- 50

  Instâncias           Balanceamento, acesso US\$ 50 -- 120
  auxiliares           e rede                
  (EC2/ALB/SG/Proxy)                         
  -----------------------------------------------------------------------------

**Subtotal Processamento: US\$ 270 -- 550 / mês**

------------------------------------------------------------------------

## 8.3 Armazenamento e Data Lake

Considerando crescimento de 30GB até 100GB até dezembro.

  Serviço                            Descrição                           Estimativa Mensal
  ---------------------------------- ----------------------------------- -------------------
  S3 (armazenamento principal)       Data Lake + Iceberg                 US\$ 5 -- 15
  Requisições S3 (leitura/escrita)   Pipelines e consultas               US\$ 10 -- 30
  EFS                                Compartilhamento entre containers   US\$ 30 -- 60
  Snapshots / backups                Segurança de dados                  US\$ 10 -- 25

**Subtotal Armazenamento: US\$ 55 -- 130 / mês**

------------------------------------------------------------------------

## 8.4 Camada Analítica (Consultas e Catálogo)

  Serviço                     Descrição                    Estimativa Mensal
  --------------------------- ---------------------------- -------------------
  Athena                      Consultas SQL no Data Lake   US\$ 40 -- 120
  Glue Catalog                Metadados das tabelas        US\$ 10 -- 20
  Processamento incremental   Atualizações frequentes      US\$ 20 -- 50

**Subtotal Analytics: US\$ 70 -- 190 / mês**

------------------------------------------------------------------------

## 8.5 Estimativa Total Realista

  Categoria                               Custo Mensal Estimado
  --------------------------------------- -----------------------
  Infraestrutura base                     US\$ 170 -- 300
  Processamento (Airflow + Kafka + DBT)   US\$ 270 -- 550
  Armazenamento (S3 + EFS + backup)       US\$ 55 -- 130
  Camada analítica (Athena + Glue)        US\$ 70 -- 190

### Total estimado mensal realista

**US\$ 565 -- US\$ 1.170 / mês**

------------------------------------------------------------------------

## 8.6 Estimativa de custo até dezembro (período do projeto)

  Período              Custo estimado
  -------------------- ---------------------------------------------------
  Abril - Maio         US\$ 300 -- 500 (infraestrutura inicial)
  Junho - Julho        US\$ 450 -- 700 (data lake + primeiros pipelines)
  Agosto - Setembro    US\$ 600 -- 900 (Iceberg + ingestões completas)
  Outubro - Novembro   US\$ 650 -- 1.050 (migração total dos dados)
  Dezembro             US\$ 650 -- 1.170 (ambiente final em produção)

------------------------------------------------------------------------

## 8.7 Justificativa Financeira da Arquitetura

Mesmo com custo mensal entre US\$ 600 e US\$ 900, essa arquitetura é
financeiramente vantajosa porque:

-   Evita custo alto de soluções prontas (Snowflake, Databricks, etc.)
-   Permite escalar apenas quando necessário
-   Reduz dependência de sistemas legados
-   Permite crescimento até 500GB+ sem necessidade de reconstrução
-   Permite evolução futura para arquitetura em tempo real

------------------------------------------------------------------------

**Documento atualizado com estimativa realista de custos AWS**
