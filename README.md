# MIQR-CC ERCP Image Classification

Este repositório contém o pipeline completo para o desenvolvimento, treino, avaliação e interpretabilidade de modelos de Deep Learning para a classificação de imagens de CPRE (Colangiopancreatografia Retrógrada Endoscópica) no dataset **MIQR-CC**.

O objetivo do projeto é classificar as imagens em 4 classes clínicas principais:
* **Biliary Leaks** (Fugas Biliares)
* **Lithiasis** (Litíase / Cálculos)
* **Normal** (Normal)
* **Stricture** (Estenoses / Apertos)

---

## 📂 Estrutura do Repositório

```text
├── data/
│   ├── dataset/          # Imagens originais do dataset
│   ├── processed/        # Imagens pré-processadas (com CLAHE) e splits em CSV
│   └── metadata.csv      # Metadados das imagens
├── notebooks/
│   ├── 01_exploratory_data_analysis.ipynb     # Análise Exploratória de Dados (EDA)
│   ├── 02_image_preprocessing.ipynb           # Pré-processamento e split do dataset
│   ├── 03_model_densenet.ipynb                # Treino e Grad-CAM da DenseNet121
│   ├── 03_convnext-tiny.ipynb                 # Treino e Grad-CAM do ConvNeXt Tiny
│   ├── 03_efficientnet_b7_training.ipynb      # Treino e Grad-CAM do EfficientNet-B7
│   ├── 03_resnet50_training.ipynb             # Treino e Grad-CAM do ResNet50
│   └── 03_swin_transformer_training.ipynb     # Treino e Grad-CAM do Swin Transformer Tiny
├── models/                                    # Ficheiros PNG com gráficos e heatmaps
├── requirements.txt                           # Dependências do projeto
└── README.md                                  # Este ficheiro de instruções
```

---

##  Configuração do Ambiente de Execução

Recomenda-se a utilização de um ambiente virtual Python (`venv`) para garantir a consistência das dependências.

### 1. Criar e Ativar o Ambiente Virtual

No terminal da pasta raiz do projeto:

**Em Windows (PowerShell):**
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

**Em macOS/Linux:**
```bash
python3 -m venv venv
source venv/bin/activate
```

### 2. Instalar as Dependências

Com o ambiente virtual ativo, executar o seguinte comando para instalar as bibliotecas necessárias (incluindo PyTorch, MONAI, timm, e WandB):

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

---

##  Fluxo de Execução dos Notebooks

Os notebooks devem ser executados na seguinte ordem sequencial:

### 1. Exploração e Análise de Dados
* **Notebook:** `notebooks/01_exploratory_data_analysis.ipynb`
* **Objetivo:** Compreender a distribuição de classes, dimensões das imagens e identificar o forte desequilíbrio do dataset.

### 2. Pré-Processamento e Geração dos Splits
* **Notebook:** `notebooks/02_image_preprocessing.ipynb`
* **Objetivo:** Aplicar equalização de histograma adaptativa (CLAHE) para melhorar o contraste das imagens médicas e gerar as partições de treino, validação e teste (`train_split.csv`, `val_split.csv`, `test_split.csv`) guardadas em `data/processed/`.

### 3. Treino e Avaliação dos Modelos
* **Notebooks:**
  * `notebooks/03_model_densenet.ipynb` (DenseNet121 — 512×512)
  * `notebooks/03_convnext-tiny.ipynb` (ConvNeXt Tiny — 224×224)
  * `notebooks/03_efficientnet_b7_training.ipynb` (EfficientNet-B7 — 224×224)
  * `notebooks/03_resnet50_training.ipynb` (ResNet50 — 224×224)
  * `notebooks/03_swin_transformer_training.ipynb` (Swin Transformer Tiny — 224×224)
* **Objetivo:** Treinar cada arquitetura com regularização avançada (Dropout, Focal Loss, Class Weights) e a estratégia de **Fine-Tuning em 2 Fases** (Backbone congelado na Fase 1 e descongelado na Fase 2 com Learning Rate reduzido). Cada notebook inclui avaliação no conjunto de teste independente e geração de mapas Grad-CAM.

---

##  Monitorização de Experiências (WandB)

Todos os treinos e avaliações estão integrados com o **Weights & Biases (WandB)**. Para sincronizar as experiências no workspace de equipa partilhado:

1. Autenticar no terminal com a API Key:
   ```bash
   wandb login
   ```
2. As experiências serão enviadas automaticamente para o projeto `saraantunesferreirapinto-universidade-do-minho` na respetiva entidade de equipa.
3. Para execução local em modo offline, definir a variável de ambiente:
   ```bash
   $env:WANDB_MODE="offline" # Windows
   export WANDB_MODE="offline" # macOS/Linux
   ```

---

## Métricas e Interpretabilidade 

Cada notebook de treino contém as seguintes validações e saídas:
* **Métricas Robustas:** Matriz de Confusão, F1-Score Macro e Curva AUC-ROC calculadas diretamente no conjunto de teste independente.
* **Probabilidades Clínicas:** Tabela com a distribuição de probabilidades para cada uma das 4 classes.
* **Grad-CAM Visual Overlays:** Geração automática do mapa de calor sobreposto na imagem original, identificando visualmente os focos de atenção da rede neural para a tomada de decisão diagnóstica (por exemplo, focando-se na presença de cálculos na litíase ou em estreitamentos nas estenoses).
