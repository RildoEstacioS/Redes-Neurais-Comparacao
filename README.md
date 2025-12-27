# Comparação de Modelos de Aprendizado de Máquina para Classificação de Tipo e Qualidade de Pavimento Utilizando Dados de Sensores Veiculares

Projeto desenvolvido para a disciplina de Redes Neurais, com foco em comparação de modelos de aprendizado de máquina clássicos e redes LSTM aplicados à classificação de **tipo de pavimento** e **qualidade da via** utilizando dados do conjunto Passive Vehicular Sensors (PVS).
---

## 💡 Visão geral

Este repositório implementa uma arquitetura em dois estágios (cascata):

1. **Estágio 1 – Tipo de pavimento**  
   Classificação do tipo de pavimento (por exemplo, asfalto, paralelepípedo, terra) a partir de leituras de sensores embarcados em veículos.

2. **Estágio 2 – Qualidade da via**  
   Classificação da qualidade da via (boa, regular, ruim) utilizando as mesmas features de sensores **mais** o tipo de pavimento **previsto** no estágio anterior como feature adicional. 

Modelos comparados: **Random Forest**, **Multi-Layer Perceptron (MLP)**, **Support Vector Machine (SVM)** e **Long Short-Term Memory (LSTM)** em ambas as tarefas. 
---

## 🎯 Objetivos

- Classificar o tipo de pavimento usando dados de sensores veiculares do dataset PVS. 
- Classificar a qualidade da via em três níveis (boa, regular, ruim) a partir dos mesmos sinais de sensores.
- Comparar o desempenho de RF, MLP, SVM e LSTM em cada tarefa, analisando acurácia, precisão, recall e F1-score. 
- Avaliar a contribuição da arquitetura em cascata, onde a predição do pavimento é utilizada como entrada adicional para o modelo de qualidade. 

---

## 📊 Conjunto de dados

- **Fonte:** Passive Vehicular Sensors (PVS), disponibilizado publicamente (Kaggle), com dados de três veículos, três motoristas e nove cenários de condução. 
- **Atributos:** leituras de acelerômetros em diferentes posições/eixos, timestamp e rótulos de tipo de pavimento e indicadores de qualidade da via.

### Pré-processamento

- Criação de `target_pavimento` (3 classes) a partir dos rótulos originais de pavimento. 
- Criação de `target_qualidade` (0 = boa, 1 = regular, 2 = ruim) a partir de colunas binárias `good_road_left`, `regular_road_left` e `bad_road_left`. 
- Escalonamento das features e divisão em conjuntos de treino, validação e teste (p.ex. 80/20). 
- Geração de janelas temporais (sequências) para os modelos LSTM.

---

## 🤖 Modelos implementados

- **Random Forest (RF)**  
  - Utilizado em pavimento e qualidade, com ajuste de número de árvores e profundidade.  
  - Apresenta alta acurácia e bom equilíbrio entre desempenho e custo computacional. 

- **Multi-Layer Perceptron (MLP)**  
  - Rede neural feedforward com duas camadas ocultas.  
  - Captura relações não lineares entre os sinais de sensores e os rótulos. 

- **Support Vector Machine (SVM)**  
  - Utiliza kernel radial (RBF) para tratar fronteiras de decisão não lineares.  
  - Obteve desempenho competitivo, especialmente na tarefa de tipo de pavimento. 

- **Long Short-Term Memory (LSTM)**  
  - Rede recorrente aplicada a sequências temporais de sensores.  
  - Projetada para explorar dependências temporais e melhorar a detecção de trechos em condição ruim. 

---

## 🧱 Arquitetura em dois estágios (cascata)

1. **Estágio 1 – Pavimento**  
   - Entrada: features de sensores processadas.  
   - Saída: rótulos previstos de tipo de pavimento para cada amostra, por modelo (RF, MLP, SVM, LSTM).

2. **Estágio 2 – Qualidade da via**  
   - Entrada: mesmas features de sensores + coluna(s) de tipo de pavimento **previsto**.  
   - Saída: qualidade da via (boa, regular, ruim).  

**Motivação:** a percepção de irregularidades depende do tipo de pavimento; incorporar a predição do pavimento na entrada ajuda o modelo de qualidade a capturar essa relação.  

---

## 📁 Estrutura do repositório (sugerida)

```
.
├── Pavimento/           # Scripts e artefatos da tarefa de tipo de pavimento
│   ├── data/            # Dados específicos (splits, features, etc.)
│   ├── models/          # Pesos/modelos salvos (RF, MLP, SVM, LSTM)
│   └── *.py             # EDA, pré-processamento, treinamento e avaliação
│
├── Qualidade/           # Scripts e artefatos da tarefa de qualidade
│   ├── data/            # Dados específicos (splits, datasets com pavimento previsto)
│   ├── models/          # Pesos/modelos salvos (RF, MLP, SVM, LSTM)
│   └── *.py             # Criação de target_qualidade, cascata e treinamento
│
├── data/                # Dados brutos ou link para o PVS (se permitido)
│   └── PVS_9/           # Arquivos originais ou organizados do dataset
│
├── results/             # Matrizes de confusão, métricas, tabelas e gráficos
│
├── article/             # Artigo em LaTeX baseado no template SBC
│   └── *.tex
│
├── presentation/        # Slides da apresentação
│   └── *.pptx
│
└── README.md
```

Estrutura inspirada em templates de projetos de ciência de dados e machine learning para facilitar reprodutibilidade e organização.

---

## 🚀 Como executar

### 1. Clonar o repositório

```
git clone https://github.com/SEU_USUARIO/SEU_REPOSITORIO.git
cd SEU_REPOSITORIO
```

### 2. Criar ambiente e instalar dependências

```
python -m venv .venv
source .venv/bin/activate  # Linux/macOS
# .venv\Scripts\activate   # Windows

pip install -r requirements.txt
```

(As dependências incluem, por exemplo, `numpy`, `pandas`, `scikit-learn`, `tensorflow`/`keras`.)

### 3. Configurar caminhos de dados

- Ajustar `DATAPATH` e `PROJECT_ROOT` no arquivo de configuração (por exemplo, `_1_config.py`) para apontar para a pasta onde o PVS está armazenado. 

### 4. Rodar pipeline de pavimento

- Executar os scripts da pasta `Pavimento/` na ordem definida (EDA → pré-processamento → split → treinamento dos modelos RF/MLP/SVM/LSTM). 

### 5. Rodar pipeline de qualidade (cascata)

- Executar os scripts da pasta `Qualidade/` que:  
  - criam `target_qualidade`,  
  - montam os datasets com pavimento previsto,  
  - treinam e avaliam RF, MLP, SVM e LSTM para qualidade. 

### 6. Analisar resultados

- Consultar a pasta `results/` para ver as métricas comparativas (acurácia, F1, matrizes de confusão) usadas no artigo e na apresentação. 

