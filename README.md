# IPCA Inflation Analysis — Brazil (BACEN/SGS 433)

**Geração:** 26 de Outubro de 2025  
**Fonte de Dados:** Variação mensal do IPCA (%, mensal) — índice oficial de inflação ao consumidor (IBGE), via SGS 433.

---

## 📖 Visão Geral
Este repositório contém uma análise de séries temporais focada na inflação brasileira, medida pelo Índice Nacional de Preços ao Consumidor Amplo (IPCA). O projeto abrange desde a ingestão e limpeza dos dados até a aplicação de modelos avançados de previsão, com o objetivo de entender o comportamento histórico da inflação e projetar tendências futuras (2025-2026).

**Contexto Importante:** A análise histórica (1980-2025) revelou um período de hiperinflação pré-1994 que distorce significativamente os modelos preditivos atuais. Por isso, a modelagem foi estrategicamente **fatiada para o período pós-Plano Real (a partir de 01/01/1995)**, garantindo que as previsões sejam baseadas em um regime econômico estável e relevante para o cenário atual.

---

## 📂 Estrutura do Projeto
* **`time_series_analysis_inflation.ipynb`**: Notebook principal contendo todo o fluxo de trabalho:
    * Importação e tratamento de dados.
    * Visualização da série temporal completa e fatiada.
    * Decomposição da série (Tendência, Sazonalidade, Resíduos).
    * Testes de estacionariedade (ADF).
    * Modelagem preditiva com múltiplos algoritmos.
* **`03_Inflacao_BR.csv`**: Arquivo de dados brutos (necessário para execução).

---

## ⚙️ Metodologia e Modelagem

### 1. Pré-processamento
* **Limpeza e Indexação:** Conversão da coluna de datas para `datetime` e definição como índice do DataFrame.
* **Fatiamento (Slicing):** Seleção do período a partir de **Janeiro de 1995** para isolar a estabilidade econômica pós-Plano Real e evitar o ruído da hiperinflação passada.
* **Divisão Treino/Teste:** Separação dos últimos 12 meses (Out/2024 - Set/2025) para validação dos modelos.

### 2. Análise Exploratória
* **Decomposição Aditiva:** Identificação de uma tendência não linear (random walk) e uma sazonalidade anual clara, além de heteroscedasticidade nos resíduos.
* **Autocorrelação (ACF/PACF):** Confirmação da não-estacionariedade (decadência lenta no ACF e corte no lag 1 do PACF), indicando a necessidade de diferenciação (d=1).

### 3. Modelos Implementados
O projeto compara o desempenho de diferentes abordagens para prever os próximos 12 meses de inflação:

1.  **SARIMA (Auto-ARIMA):**
    * *Configuração:* `(1, 1, 1)x(0, 1, 1, 12)`.
    * *Resultado:* RMSE de 23.78. O modelo capturou a sazonalidade, mas falhou em generalizar a tendência recente, apresentando alta autocorrelação nos resíduos.

2.  **Holt-Winters (Exponential Smoothing):**
    * *Configuração:* Modelo Aditivo.
    * *Resultado:* RMSE de 29.64. O modelo mais simplista, que não conseguiu capturar a dinâmica complexa da inflação, resultando em erros sistemáticos.

3.  **Decision Tree Regressor:**
    * *Configuração:* `max_depth=5`, utilizando *lags* como features.
    * *Resultado:* RMSE de 36.06. Pior desempenho entre os testados, indicando que uma árvore simples não é suficiente para essa série temporal.

4.  **LSTM (Long Short-Term Memory):**
    * *Configuração:* Rede neural recorrente simples.
    * *Resultado:* RMSE de 35.88. Embora promissor, o modelo simples não superou o SARIMA nesta configuração inicial.

5.  **Prophet (Facebook):**
    * *Configuração:* Sazonalidade aditiva.
    * *Resultado:* Previsões geradas para o período de 2024-2025, com identificação automática de pontos de mudança de tendência.

---

## 🚀 Como Executar
1.  Clone este repositório.
2.  Instale as dependências listadas abaixo.
3.  Certifique-se de que o arquivo `03_Inflacao_BR.csv` esteja no mesmo diretório.
4.  Execute o notebook `time_series_analysis_inflation.ipynb`.

### Requisitos
```bash
pip install pandas numpy matplotlib seaborn statsmodels scikit-learn prophet pmdarima tensorflow
