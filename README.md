# 👷 Safety Helmet Detection com YOLOv8 (Estratégia de Fusão)

Este projeto apresenta uma solução de Visão Computacional para detecção de EPIs (Capacetes de Segurança) em canteiros de obras. O projeto foi desenvolvido utilizando a arquitetura **YOLOv8** e uma estratégia de **Ensemble (Fusão de Modelos)** para atender aos requisitos de detectar novas classes sem perder a capacidade de identificar objetos do cotidiano.



[Image of Construction Worker and Dog]
 ## 🎯 O Desafio

O objetivo deste projeto era atender aos seguintes requisitos:
1. Utilizar a rede **YOLO** via Google Colab.
2. Realizar a rotulagem de dados ou utilizar um dataset pré-existente.
3. Realizar **Transfer Learning** para detectar pelo menos **2 novas classes** (neste caso: `Helmet` e `Head`).
4. **Requisito Crítico:** O modelo final deve detectar as novas classes E manter a detecção das classes originais (ex: Pessoas, Cachorros, Carros) treinadas previamente no dataset COCO.

## 💡 A Solução: O Problema do "Esquecimento Catastrófico"

Durante o desenvolvimento, foi identificado um problema comum em Redes Neurais:

* **Tentativa 1 (Treino Padrão):** Ao realizar o *fine-tuning* do modelo YOLO apenas com o dataset de capacetes, a rede aprendeu muito bem a identificar capacetes, mas sofreu de **Esquecimento Catastrófico**. Ela "esqueceu" o que eram cachorros, carros e outros objetos do dataset original (COCO), pois os pesos da rede foram drasticamente alterados para focar apenas no novo dataset.
* **A Dificuldade:** Para resolver isso da maneira tradicional, seria necessário baixar o dataset COCO completo (mais de 100 mil imagens), misturá-lo com o dataset de capacetes e retreinar tudo do zero. Isso exigiria muito tempo e poder computacional (GPU), inviável para um ambiente rápido como o Colab gratuito.

### 🚀 A Estratégia Adotada: Fusão de Modelos (Inference Ensemble)

Para atender ao desafio de forma **simples, eficiente e sem retreinar o mundo inteiro**, adotei uma abordagem de **Fusão em Tempo de Execução**:

O sistema carrega dois modelos simultaneamente na memória:
1. **Modelo Especialista (`best.pt`):** Treinado por mim apenas com o dataset de capacetes (Classes: `Helmet`, `Head`).
2. **Modelo Generalista (`yolov8n.pt`):** O modelo original da Ultralytics, que já sabe detectar 80 classes do mundo real (Pessoas, Animais, Veículos).

Um script Python intercepta a imagem, passa pelos dois modelos, e **funde os resultados visualmente**.

**Vantagens desta abordagem:**
* ✅ **Baixo Custo:** Treinamento extremamente rápido (apenas 25-50 épocas em um dataset pequeno).
* ✅ **Precisão:** Mantém a precisão original do YOLO para objetos comuns.
* ✅ **Flexibilidade:** Atende perfeitamente ao requisito de detectar o "novo" e o "velho" simultaneamente.

## 🛠️ Tecnologias Utilizadas

* **Python 3.x**
* **Google Colab** (GPU T4)
* **Ultralytics YOLOv8**
* **OpenCV** (para manipulação de imagem e desenho das bounding boxes)
* **Git** (para clonagem de datasets)

## 📂 Dataset

O dataset utilizado para o treinamento das classes de segurança foi obtido publicamente:
* **Fonte:** [Safety-Helmet-Detection (GitHub)](https://github.com/jomarkow/Safety-Helmet-Detection)
* **Classes:**
    * `0`: Helmet (Capacete)
    * `1`: Head (Cabeça / Sem capacete)

## ⚙️ Como Executar

O projeto está contido inteiramente no arquivo `treinamento.ipynb`.

1. **Configuração:** O script instala automaticamente a biblioteca `ultralytics`.
2. **Treinamento:** O código baixa o dataset e treina um modelo `yolov8n` focado exclusivamente nas classes de segurança.
3. **Inferência (O Pulo do Gato):**
    * Execute a célula final de "SISTEMA DE FUSÃO".
    * Faça o upload de uma imagem de teste (ex: uma obra com um cachorro ou um veículo passando).
    * O sistema desenhará:
        * **Caixas Azuis:** Detecções do modelo especialista (Capacete).
        * **Caixas Verdes:** Detecções do modelo generalista (Cachorro, Pessoa, etc.).

## 📊 Resultados

O modelo final é capaz de identificar um operário usando capacete (pelo treino novo) e, na mesma cena, identificar um animal ou veículo (pelo conhecimento prévio), comprovando a eficácia da estratégia de fusão.

---
*Desenvolvido como parte do desafio de Visão Computacional com YOLO.*
