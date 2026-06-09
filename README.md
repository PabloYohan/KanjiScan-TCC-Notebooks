# KanjiScan — TCC Notebooks

Repositório com os notebooks do Trabalho de Conclusão de Curso (TCC) **KanjiScan**: um sistema de reconhecimento de caracteres japoneses escritos à mão usando Redes Neurais Convolucionais (CNN).

## Visão Geral

O projeto treina um modelo CNN para classificar **215 caracteres japoneses** nas seguintes categorias:

| Categoria | Descrição |
|---|---|
| Hiragana | Silabário padrão + variantes com dakuten/handakuten + formas pequenas (ゃ, ゅ, ょ, っ) |
| Katakana | Silabário padrão + formas pequenas |
| Kanji N5 | Kanjis do nível JLPT N5 (日, 本, 語, 人, ...) |

**Dataset:** [ETL Character Database](http://etlcdb.db.tohoku.ac.jp/) (ETL1, ETL6, ETL8B, ETL9B) — ~195.000 imagens, mínimo de 161 amostras por classe.

**Ambiente de execução:** Google Colab.

## Estrutura dos Notebooks

### 1. `TCC_Dataset_prepare.ipynb` — Preparação do Dataset

Extrai e organiza as imagens do ETL Character Database no formato PyTorch `ImageFolder`:

```
dataset_processed/images/
├── あ/    (362 imagens)
├── い/    (362 imagens)
├── ...
└── 黒/    (362 imagens)
```

**Fluxo:**
1. Define os 215 caracteres-alvo
2. Percorre os datasets ETL1, ETL6, ETL8B e ETL9B filtrando apenas os caracteres-alvo
3. Gera `metadata.csv` com mapeamento de caracteres → pastas → contagens
4. Copia e renomeia as imagens no formato `{etl}_{folder}_{idx:05d}{ext}`
5. Gera `processed_metadata.csv` com distribuição final por classe
6. Compacta e exporta o dataset para o Google Drive

### 2. `Training.ipynb` — Treinamento do Modelo

Pipeline completo de treinamento da CNN:

1. **Configuração** — hiperparâmetros, augmentações, splits treino/validação/teste
2. **Arquitetura CNN** — definição e inspeção da rede
3. **Treinamento** — loop com checkpointing do melhor modelo
4. **Avaliação** — acurácia, relatório por classe (precisão/recall/F1), matriz de confusão
5. **Análise de erros** — visualização dos exemplos mais confundidos
6. **Exportação ONNX** — converte o modelo `.pth` para ONNX para inferência multiplataforma
7. **Pré-processamento** — pipelines v1 e v2, incluindo binarização de Otsu e robustez a rotações
8. **Exportação** — salva modelos e resultados no Google Drive

**Artefatos gerados:**
- `best_model.pth` — checkpoint do melhor modelo PyTorch
- `model.onnx` — modelo exportado para ONNX
- Gráficos de loss/acurácia por época
- Matriz de confusão e exemplos de erros

## Dependências

Os notebooks rodam no Google Colab, que já provê as principais bibliotecas:

```
torch, torchvision
scikit-learn
opencv-python
pandas, numpy
matplotlib
onnx, onnxruntime
tqdm
```

## Como Usar

1. Faça upload dos notebooks no [Google Colab](https://colab.research.google.com/)
2. Monte o Google Drive quando solicitado (os artefatos são salvos em `MyDrive/`)
3. Execute `TCC_Dataset_prepare.ipynb` para gerar o dataset processado
4. Execute `Training.ipynb` para treinar o modelo

> O ETL Character Database precisa estar disponível no ambiente antes de executar a preparação do dataset.

## Licença

MIT License — Copyright 2026 Pablo Yohan
