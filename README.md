
# Plano de Modernização em Engenharia de Dados

## Estrutura Atual e Pontos Críticos
- **Sistema Legado CRM**: Monolítico (PHP backend, HTML/CSS frontend, RDS MySQL 15GB). Sem pipelines de dados, insights limitados a queries ad-hoc, silos de dados isolados, performance engarrafada em crescimento.
- **Novo Sistema**: Modular (Node.js backend, Next.js frontend, MySQL 15GB). Ainda sem data lake/pipes, dependente de MySQL para analytics, integração manual com CRMs externos e arquivos locais.
- **Limitações Sem Eng. Dados**: Ausência total de pipes ETL/ELT, streaming ou orquestração; delays em decisões por processamento lento/duplicatas; perda de insights (comportamento cliente, tendências); data quality baixa sem transformações.

## Motivações para Infra Sugerida (AWS Modern Data Stack)
- **Escalabilidade & Performance**: De monolítico MySQL para data lake S3/Iceberg (até 10x queries mais rápidas, linear scaling GB-PB).
- **Flexibilidade**: Camadas medalhão (bronze/silver/gold), open formats (Iceberg), decoupled storage/compute.
- **Eficiência**: Airflow ECS para orquestração, Kafka streaming real-time, dbt SQL transforms; 50% redução custos, 3x velocity desenvolvimento.
- **Insights & Inovação**: Pipes dept com gabarito (tabelas acessadas/sensíveis/views/freqs), data marts gold para BI/ML.
- **Integração**: Fontes externas via Glue jobs; migração MySQL→Iceberg nativa.

## Arquitetura Proposta
- **Core**: S3 data lake, Iceberg/Glue Catalog warehouse, Athena queries.
- **Orquestração**: Airflow 3.1.8 ECS Fargate + EFS; Kafka container streaming; dbt transforms.
- **Suporte**: Route53 domínios, EC2/ALB/SG proxies.
- Migração: 30GB inicial → 100GB; pipes fontes externas.

## Gabarito Casos de Uso por Departamento
- Documentação ambiente (0), tabelas acessadas (1), mais dados/sensíveis.
- Views existentes/necessárias/refatoráveis/usadas.
- Freqs: updates tabelas/CRM/dashs.

## Cronograma (Abril-Dez 2026)

| Fase | Prazo | Entregas | Dependências |
|------|-------|----------|--------------|
| 1 | Abr-Mai | Avaliação, setup Airflow/Kafka/EFS, PoC migração | AWS setup |
| 2 | Jun-Jul | Migração MySQLs→Iceberg bronze/silver, pipes externos | Fase 1 |
| 3 | Ago-Set | DAGs Airflow, Kafka streaming, marts iniciais | Silver ok |
| 4 | Out-Nov | Gold marts dept, escalada 100GB, Athena otimizada | Pipes |
| 5 | Dez | Ajustes, docs, treinamento | Testes |

## Estimativa Custos AWS Mensal (us-east-1, 100GB)

| Serviço | Detalhe | Custo (USD) |
|---------|---------|-------------|
| ECS Fargate | Airflow/dbt/Kafka (2 vCPU, 4GB) | 400 |
| EFS | 100GB provisioned | 30 |
| S3 | 100GB IA storage + requests | 2.3 |
| Glue | Catalog + jobs migração | 50 |
| Athena | 10TB scans | 50 |
| Route53/EC2/ALB | Domínios/proxies | 40 |
| Iceberg/SG outros | - | 91.5 |
| **Total** | - | **663.8** |

*Otimize com Savings Plans. Brasil ~1.2x.*
