# RELATÓRIO DE IMPLEMENTAÇÃO DE SERVIÇOS AWS  
**Data:** 21/01/2026  
**Empresa:** Abstergo Industries  — Distribuidora B2B para Farmácias  
**Responsável:** Daniel Correa  

## Resumo Executivo  
A Abstergo Industries opera como distribuidora e atende outras farmácias por meio de um portal B2B. O ponto central desta proposta é substituir custos fixos (infraestrutura ligada o tempo todo) por um modelo de **pagamento conforme uso**, reduzindo despesas imediatas e simplificando a operação.

As três implementações sugeridas atacam diretamente as principais fontes de gasto em plataformas desse tipo: (1) entrega de conteúdo e arquivos do portal, (2) processamento de APIs e integrações, e (3) banco de dados transacional superdimensionado para picos. O ganho será acompanhado por métricas objetivas (custo por requisição/pedido, tráfego em cache, consumo médio do banco) e por controles de governança (limites e alertas de orçamento).

## Introdução  
Este relatório descreve a proposta de implementação de serviços na AWS para a Abstergo Industries, uma empresa fictícia que atua como **distribuidora** e atende outras farmácias por meio de um **portal B2B** (catálogo, pedidos, documentos e rotinas de integração).

O objetivo do projeto é **selecionar 3 serviços AWS** com foco em **redução imediata de custos**, priorizando:  
1) diminuição de infraestrutura fixa (servidores ligados sem necessidade),  
2) redução do custo de entrega de conteúdo (imagens, PDFs e páginas do portal),  
3) simplificação do ambiente para reduzir horas de operação/manutenção.

## Descrição do Projeto  
O projeto foi organizado em 3 etapas. Cada etapa contempla uma escolha de serviço com objetivo direto de economia e aplicação prática no portal B2B.

### Etapa 1  
- **Nome da ferramenta:** Amazon S3 + Amazon CloudFront  
- **Foco da ferramenta:** Hospedagem e entrega de conteúdo do portal com menor custo operacional e menor necessidade de servidor dedicado.  
- **Descrição de caso de uso:**  
  O portal B2B possui grande volume de conteúdo estático (páginas, imagens de produtos, tabelas de preço em PDF, documentos para clientes e materiais de apoio). Em vez de manter servidores apenas para servir esse material, o conteúdo será armazenado no **Amazon S3** e entregue ao usuário final pelo **CloudFront** (CDN), com cache distribuído.

  **Benefício esperado (custos):**  
  - redução de gasto com infraestrutura dedicada para servir páginas/arquivos;  
  - menor consumo do backend por efeito de cache (menos chamadas e menos tráfego saindo da origem);  
  - melhora de desempenho percebido no portal, reduzindo chamados de suporte e retrabalho.

  **Impacto financeiro esperado (como medir):**  
  - % de requisições atendidas por cache (hit ratio) e redução de tráfego na origem;  
  - volume mensal (GB) entregue via CloudFront versus origem;  
  - queda de custo de hosting de conteúdo (eliminando servidores para conteúdo estático).

### Etapa 2  
- **Nome da ferramenta:** AWS Lambda + Amazon API Gateway  
- **Foco da ferramenta:** Substituir servidor de API sempre ligado por execução sob demanda (paga pelo uso), mantendo escalabilidade automática nos horários de pico.  
- **Descrição de caso de uso:**  
  As funcionalidades do portal B2B (login, consulta de catálogo/preço, criação e consulta de pedidos, regras comerciais, cálculo de frete e integrações) serão expostas por APIs no **API Gateway** e processadas por funções **AWS Lambda**.

  **Por que isso reduz custo de forma direta:**  
  - elimina custo de instâncias 24/7 quando a demanda real oscila (principalmente fora do horário comercial);  
  - reduz necessidade de superdimensionamento para aguentar picos;  
  - diminui manutenção de servidor (atualizações, patches, monitoramento de OS).

  **Exemplos práticos de rotinas:**  
  - criação de pedido com validação de limite/condição comercial;  
  - atualização de status de pedido e rotinas de notificação;  
  - integração com ERP/faturamento via webhooks e eventos.

  **Impacto financeiro esperado (como medir):**  
  - custo por 1.000 requisições de API;  
  - custo médio por pedido processado (métrica simples e objetiva para o financeiro);  
  - redução do custo fixo anterior (compute sempre ligado).

### Etapa 3  
- **Nome da ferramenta:** Amazon Aurora Serverless v2 (RDS)  
- **Foco da ferramenta:** Banco relacional com escala automática para reduzir gasto em períodos de baixa, mantendo segurança e consistência transacional.  
- **Descrição de caso de uso:**  
  Para uma distribuidora, o núcleo do sistema é transacional: clientes, contratos, tabela de preço por perfil, pedidos, itens, faturamento e rastreabilidade. A escolha de um banco relacional gerenciado atende essa necessidade, e o **Aurora Serverless v2** ajusta capacidade conforme a demanda (aumenta em picos e reduz em baixa).

  **Benefício esperado (custos):**  
  - menor custo por evitar banco provisionado no máximo o tempo todo;  
  - menor custo operacional por ser gerenciado (backup e manutenção com menos esforço);  
  - melhor equilíbrio entre desempenho e gasto em períodos de sazonalidade.

  **Impacto financeiro esperado (como medir):**  
  - capacidade média utilizada versus capacidade máxima/contratada (ociosidade reduzida);  
  - custo mensal do banco correlacionado com volume de pedidos;  
  - redução de gastos com administração de banco (horas operacionais).

## Riscos e Medidas de Controle  
- **Risco de custo inesperado por má configuração (ex.: tráfego excessivo, loops de integração):**  
  **Medida:** criação de **AWS Budgets com alertas** e limites por ambiente (homologação/produção), além de revisão mensal do consumo.  
- **Risco de picos de acesso afetarem performance:**  
  **Medida:** uso de cache (CloudFront), ajustes de limites e monitoramento de latência nas APIs; definição de capacidade mínima no Aurora Serverless v2 conforme padrão de uso.  
- **Risco de indisponibilidade por falhas de integração:**  
  **Medida:** tratamento de erros nas funções, registros de eventos e monitoramento contínuo (logs e alarmes).

## Conclusão  
As três escolhas (S3+CloudFront, Lambda+API Gateway e Aurora Serverless v2) foram feitas porque atacam o principal custo inicial em plataformas B2B: **infraestrutura fixa ociosa**.

O resultado esperado é:  
- **redução de custo imediato** ao substituir servidores permanentes por serviços sob demanda;  
- **redução de custos indiretos** (menos manutenção e menor complexidade operacional);  
- **melhor experiência do cliente** no portal, principalmente na entrega de imagens e documentos, reduzindo suporte.

Recomenda-se acompanhar mensalmente as métricas propostas (custo por pedido, cache hit ratio, consumo médio do banco) e manter controles de orçamento para evitar variações inesperadas.

## Anexos  
1. Arquitetura proposta (Portal → CloudFront → S3 / API Gateway → Lambda → Aurora Serverless)  
2. Lista de funcionalidades do portal B2B (mínimo viável)  
3. Matriz de comparação de custos (servidor fixo vs. sob demanda)  


**Assinatura do Responsável pelo Projeto:**  
Daniel Correa
