# Anexo 01 — Arquitetura proposta (visão simples)

## Visão geral
- Conteúdo estático do portal (páginas, imagens, PDFs) via **CloudFront + S3**
- APIs do portal via **API Gateway + Lambda**
- Banco transacional via **Aurora Serverless v2**

## Diagrama (Mermaid)
```mermaid
flowchart LR
  U[Clientes B2B - Farmacias] --> CF[CloudFront]
  CF --> S3[S3 - Portal e Midias]
  CF --> APIGW[API Gateway]
  APIGW --> L[Lambda]
  L --> DB[Aurora Serverless v2]

