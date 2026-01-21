# Anexo 03 — Matriz simples de comparação (custos e operação)

> Comparação qualitativa (sem números), adequada para decisão rápida.

| Item | Modelo tradicional (servidor fixo) | Proposta (AWS) | Efeito esperado |
|---|---|---|---|
| Site/arquivos (imagens, PDFs) | Servidor web e storage anexado | S3 + CloudFront | Reduz custo fixo e tráfego no backend |
| APIs do portal | Instância ligada 24/7 | API Gateway + Lambda | Paga por uso; escala em pico; menos ociosidade |
| Banco de dados | Banco provisionado para pico | Aurora Serverless v2 | Ajusta capacidade; reduz ociosidade em baixa |
| Operação | Patch, monitoramento e manutenção de servidor | Serviços gerenciados | Menos horas operacionais e menor risco |
