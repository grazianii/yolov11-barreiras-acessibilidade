# Detecção de Barreiras de Acessibilidade em Ambientes Acadêmicos com YOLOv11

## 1. Sobre o projeto

Este repositório contém o notebook desenvolvido para o treinamento, validação e avaliação de um modelo de detecção de objetos baseado na arquitetura YOLOv11, com foco na identificação de elementos relacionados à acessibilidade em ambientes universitários.

O projeto tem como objetivo identificar estruturas que podem representar obstáculos ou recursos de acessibilidade, contribuindo para estudos de análise automatizada de ambientes e identificação de barreiras físicas.

O modelo foi desenvolvido utilizando o YOLOv11m, da biblioteca Ultralytics, e executado por meio do Google Colab, com integração ao Google Drive para armazenamento do dataset, dos pesos do modelo e dos resultados experimentais.

## 2. Dataset

O notebook foi executado considerando o dataset disponibilizado no Kaggle:

**Campus Accessibility Barriers Detection**

https://www.kaggle.com/datasets/rodrigograziani/campus-accessibility-barriers-detection

O conjunto de dados está organizado no formato YOLO Object Detection, contendo imagens e respectivas anotações divididas em três subconjuntos:

- Treinamento (`train`): utilizado para ajustar os parâmetros do modelo.
- Validação (`val`): utilizado para acompanhar o desempenho durante o treinamento.
- Teste (`test`): utilizado para avaliar o modelo após o treinamento.

### Classes utilizadas

O modelo considera quatro classes:

| ID | Classe | Descrição |
|---|---|---|
| 0 | curb | Guias e desníveis elevados |
| 1 | stairs | Escadas e degraus |
| 2 | handrail | Corrimãos e estruturas de apoio |
| 3 | ramp | Rampas e superfícies inclinadas |

### Estrutura do dataset

O dataset segue a seguinte organização:

```text
dataset_final/
├── images/
│   ├── train/
│   ├── val/
│   └── test/
│
├── labels/
│   ├── train/
│   ├── val/
│   └── test/
│
└── data.yaml
```

As anotações seguem o padrão YOLO, contendo o identificador da classe e as coordenadas normalizadas das caixas delimitadoras (bounding boxes).

## 3. Tecnologias e ferramentas

As principais tecnologias utilizadas no desenvolvimento foram:

- Python
- Google Colab
- Google Drive
- YOLOv11m
- Ultralytics
- PyTorch
- PyYAML

O treinamento utiliza GPU quando disponível no ambiente do Google Colab. Caso contrário, o notebook seleciona a CPU, embora o processamento possa levar mais tempo.

## 4. Configurações do treinamento

O modelo utilizado é o YOLOv11m, inicializado a partir dos pesos pré-treinados disponibilizados pela Ultralytics.

As principais configurações são:

| Parâmetro | Configuração |
|---|---|
| Arquitetura | YOLOv11m |
| Épocas | 100 |
| Tamanho da imagem | 640 × 640 |
| Batch size | Automático em GPU; 8 em CPU |
| Patience | 20 épocas |
| Seed | 42 |
| Otimizador | Configuração padrão da Ultralytics |
| Dispositivo | GPU, quando disponível; caso contrário, CPU |

O treinamento utiliza os subconjuntos de treinamento e validação. Após sua conclusão, o melhor checkpoint (`best.pt`) é utilizado na avaliação final sobre o conjunto de teste.

## 5. Execução do notebook

A execução foi planejada para ocorrer no Google Colab, utilizando o Google Drive como local de armazenamento dos arquivos necessários.

### 5.1. Preparação do ambiente

1. Abra o notebook no Google Colab.
2. Conecte sua conta Google.
3. Disponibilize o arquivo compactado `dataset_final.zip` na raiz do Google Drive.
4. Execute as células na sequência apresentada no notebook.

O arquivo do dataset deve estar disponível no seguinte caminho:

```text
Meu Drive/
└── dataset_final.zip
```

O notebook realiza a instalação das dependências, montagem do Google Drive, verificação do dispositivo de processamento, extração do dataset e preparação dos arquivos necessários para o treinamento e avaliação.

### 5.2. Utilização de um modelo já treinado

O repositório também disponibiliza o arquivo `best.pt`, correspondente aos melhores pesos obtidos no treinamento do modelo.

Caso o usuário deseje utilizar esse checkpoint, sem executar novamente o treinamento, é possível armazená-lo no Google Drive na seguinte estrutura:

```text
Meu Drive/
└── resultados_yolov11/
    └── weights/
        └── best.pt
```

Nesse cenário:

1. Disponibilize o arquivo `best.pt` no caminho indicado.
2. Monte o Google Drive no notebook.
3. Pule as células responsáveis pelo treinamento do modelo.
4. Execute as etapas posteriores que carregam o checkpoint e realizam a avaliação no conjunto de teste.

Dessa forma, o modelo já treinado poderá ser utilizado diretamente, evitando o custo computacional e o tempo necessários para uma nova execução do treinamento.

### 5.3. Treinamento de um novo modelo

Também é possível executar o notebook sem utilizar o arquivo `best.pt` disponibilizado neste repositório.

Para isso:

1. Disponibilize o dataset `dataset_final.zip` na raiz do Google Drive.
2. Execute as células de preparação do ambiente e do dataset.
3. Execute as células responsáveis pelo treinamento do YOLOv11m.
4. Aguarde a conclusão do treinamento.
5. Utilize o checkpoint `best.pt` gerado pela própria execução nas etapas posteriores de avaliação.

Nesse cenário, não é necessário disponibilizar o checkpoint previamente treinado no Google Drive. Deve-se excluir ou ignorar a célula específica que carrega o `best.pt` externo, mantendo o fluxo de carregamento do checkpoint produzido pelo próprio treinamento.

O novo modelo será treinado a partir dos pesos pré-treinados `yolo11m.pt`, utilizando as imagens e anotações do dataset.

## 6. Avaliação do modelo

Após o treinamento ou carregamento do checkpoint existente, o notebook realiza a avaliação do modelo utilizando exclusivamente o subconjunto de teste (`test`).

São consideradas as seguintes métricas:

- **Precision:** proporção das detecções realizadas pelo modelo que estão corretas.
- **Recall:** proporção dos objetos reais que foram identificados pelo modelo.
- **mAP@0.5:** média da precisão média considerando IoU de 0,5.
- **mAP@0.5:0.95:** média da precisão média considerando diferentes limiares de IoU, entre 0,5 e 0,95.

Também são geradas informações complementares para análise do desempenho, incluindo métricas por classe e representações gráficas disponibilizadas pela biblioteca Ultralytics.

## 7. Resultados gerados

Os resultados são armazenados no Google Drive, dentro da pasta `resultados_yolov11`.

Entre os principais arquivos e diretórios gerados estão:

```text
resultados_yolov11/
├── treinamento_yolov11m/
│   ├── weights/
│   │   ├── best.pt
│   │   └── last.pt
│   ├── results.csv
│   └── confusion_matrix.png
│
├── teste_mackenzie/
│   └── gráficos e resultados da avaliação
│
├── configuracao_experimento.json
├── contagens_dataset.json
├── resumo_treinamento.json
└── metricas_teste_mackenzie.json
```

O arquivo `best.pt` representa o checkpoint selecionado como melhor modelo durante o treinamento, enquanto `last.pt` corresponde aos pesos salvos ao final da execução.

Os arquivos JSON registram informações sobre a configuração do experimento, as contagens do dataset, o resumo do treinamento e as métricas obtidas na avaliação final.

## 8. Considerações finais

Este projeto reúne o processo de preparação dos dados, treinamento, validação e avaliação de um modelo de detecção de objetos voltado à identificação de elementos de acessibilidade em ambientes universitários.

A disponibilização do notebook e dos pesos treinados permite tanto a reprodução dos experimentos quanto a utilização direta do modelo já desenvolvido, oferecendo flexibilidade para novas avaliações, ajustes e trabalhos futuros relacionados à acessibilidade e à visão computacional.
