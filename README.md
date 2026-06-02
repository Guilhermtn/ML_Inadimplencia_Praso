# Previsão de Inadimplência — Praso

Projeto acadêmico de Machine Learning para prever a probabilidade de inadimplência de varejistas no marketplace B2B da Praso.

## Modelos

- **Aplicação:** avalia clientes novos no momento do cadastro, usando dados públicos, da Serasa, do iFood e do Google Maps.
- **Comportamental:** avalia clientes ativos, combinando os dados anteriores com o histórico de pedidos.

Métrica principal: **ROC-AUC**.

## Estrutura

- `notebooks/` — EDA, pré-processamento, modelagem e análise
- `src/` — código modular reutilizável
- `outputs/` — gráficos e relatórios gerados
- `data/` — dados não incluídos por privacidade
