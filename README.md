# Previsão de Inadimplência — Praso

Projeto da disciplina **CIN0144 — Aprendizado de Máquina e Ciência de Dados** (Centro de Informática, UFPE). O objetivo é construir um sistema de previsão de inadimplência para a **Praso**, uma startup B2B que concede crédito a pequenos varejistas (compre agora, pague depois). O modelo estima o risco de um cliente ficar inadimplente, dando à empresa uma base quantitativa para suas decisões de política de crédito.

A **métrica oficial** do projeto é o **ROC-AUC**: o objetivo de negócio é *ordenar* os clientes por risco, não classificá-los a partir de um threshold fixo.

---

## Arquitetura de dois modelos

O problema foi modelado como dois sistemas complementares, refletindo dois momentos distintos da jornada do cliente:

| | **Modelo 1 — Aplicação** | **Modelo 2 — Comportamental** |
|---|---|---|
| Quando se aplica | No cadastro (cliente novo) | Cliente ativo (com histórico) |
| Features | Apenas cadastrais (Serasa, Receita Federal/CNAE, iFood, Google Maps) | Cadastrais + histórico agregado de pedidos e pagamentos |
| Clientes | 3.000 | 664 (subconjunto ativo) |
| Taxa de inadimplência | 31,3% | 20,8% |
| Modelo final | Regressão Logística (L1, `C=0,368`) | Regressão Logística (L2, `C=0,005`) |
| ROC-AUC | ~0,77 (validação cruzada) | 0,7861 (hold-out) |

O **Modelo 1** cobre o *cold-start* (clientes sem histórico); o **Modelo 2** entra em ação conforme o cliente acumula pedidos, incorporando o sinal comportamental — que, pela ablação, agrega **+0,051 de AUC** sobre as features cadastrais. Juntos, formam a política de crédito completa: avaliação na entrada e acompanhamento ao longo do relacionamento.

---

## Principais achados

- **Parcimônia vence complexidade.** A Regressão Logística superou Random Forest e XGBoost em ambos os modelos, em AUC e estabilidade. A estrutura dos dados (amostra pequena, poucas features de sinal forte) favorece modelos lineares regularizados.
- **Arquitetura anti-leakage.** Transformações determinísticas ficam no pré-processamento; transformações estatísticas (agrupamento de raras, imputação, escalonamento) são ajustadas dentro de cada fold da validação cruzada, via `Pipeline` + `ColumnTransformer`.
- **Recência sem vazamento.** Features de recência relativas a um snapshot global foram rejeitadas (codificam *quando* a inadimplência ocorreu) e substituídas por `cadencia_media`, ancorada no histórico do próprio cliente.
- **Explicabilidade (SHAP).** A frequência de atraso (`prop_pedidos_atrasados`) supera a magnitude do atraso como sinal de risco — a regularidade do comportamento de pagamento importa mais que a severidade pontual.

---

## Estrutura do repositório

```
ML_Inadimplencia_Praso/
├── notebooks/
│   ├── eda/              # análise exploratória (aplicação e comportamental)
│   ├── preprocessing/    # limpeza, feature engineering e geração dos datasets
│   ├── modeling/         # treino, tunagem e comparação dos modelos
│   └── explainability/   # XAI / SHAP e análise de erros
└── outputs/
    └── figures/          # figuras geradas na EDA
```

---

## Ambiente

O desenvolvimento foi feito inteiramente no **Google Colab**, com os dados e artefatos hospedados no **Google Drive**. Por isso, este repositório não inclui instruções de execução local: os notebooks foram pensados para rodar no Colab, montando o Drive no início de cada um. O GitHub é usado para versionamento e apresentação do projeto.

Principais bibliotecas: `pandas`, `scikit-learn`, `xgboost`, `shap`, `matplotlib` e `seaborn`.

---

## Integrantes

- José Guilherme Teixeira Nunes — `jgtn`
- Jean Lucas de Barros Dias — `jlbd`
- Janderson Santana de Freitas — `jsf6`
- Lucas Matheus Soares Feliciano — `lmsf`
