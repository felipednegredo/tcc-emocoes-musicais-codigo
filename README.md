# Detecção de Emoções em Músicas com Fingerprinting e Análise Tempo–Frequência

[![Python](https://img.shields.io/badge/Python-3.x-blue.svg)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebooks-orange.svg)](https://jupyter.org/)
[![Dataset](https://img.shields.io/badge/Dataset-DEAM-green.svg)](https://cvml.unige.ch/databases/DEAM/)
[![Área](https://img.shields.io/badge/MIR-Music%20Emotion%20Recognition-purple.svg)](#)

Repositório dos notebooks e artefatos experimentais do Trabalho de Conclusão de Curso **“Detecção de Emoções em Músicas com base em Fingerprinting e Análise Tempo–Frequência”**.

O projeto investiga se representações compactas derivadas de **fingerprinting acústico**, extraídas a partir da **Short-Time Fourier Transform (STFT)** e de picos espectrais, podem contribuir para tarefas de **Music Emotion Recognition (MER)**. A proposta não busca substituir descritores acústicos tradicionais, mas avaliar em quais cenários os fingerprints funcionam como representação **interpretável, compacta e complementar** ao baseline `openSMILE`.

---

## Visão geral

O pipeline experimental compara três famílias de representação:

1. **openSMILE** — descritores acústicos tradicionais usados como baseline.
2. **Fingerprints acústicos** — representações `Rank` e `Band Rank`, baseadas em picos espectrais extraídos da STFT.
3. **Fusão openSMILE + fingerprints** — cenários combinando descritores tradicionais e atributos de fingerprinting.

As avaliações foram organizadas em três formulações de tarefa:

- regressão contínua de **valence** e **arousal**;
- classificação multiclasse por quadrantes emocionais;
- classificação binária **One-vs-Rest** por quadrante.

---

## Base experimental

A base utilizada é a **DEAM — Database for Emotion Analysis using Music**, com anotações contínuas de emoção no espaço **valence–arousal**.

| Item | Configuração |
|---|---|
| Dataset | DEAM |
| Total de músicas | 1.802 |
| Total de blocos utilizados | 6.506 |
| Duração dos blocos | até 10 s |
| Início da análise | 15 s |
| Alvos emocionais | `valence`, `arousal` |
| Validação | `GroupKFold(5)` agrupado por `song_id` |
| Objetivo do agrupamento | impedir que blocos da mesma música apareçam simultaneamente em treino e validação |

---

## Principais resultados do TCC

Os resultados indicam que o `openSMILE` continua sendo a representação mais estável na maioria das tarefas. Os fingerprints isolados não superam o baseline de forma geral, mas apresentam contribuição complementar em cenários específicos, principalmente em **arousal**.

| Cenário | Resultado observado |
|---|---|
| Regressão com `openSMILE` | baseline mais robusto e estável |
| Fingerprints isolados | desempenho inferior ao baseline na maior parte dos cenários |
| Fusão `openSMILE + fingerprints` em arousal | redução de RMSE de 4,1% |
| Fusão `openSMILE + fingerprints` em arousal | aumento de R² de 0,5779 para 0,6119 |
| Fusão na classificação multiclasse | Macro-F1 de 0,4680 para 0,4826 |
| Interpretação geral | complementaridade seletiva, mais forte em tarefas associadas à energia emocional |

A leitura crítica do trabalho é que os fingerprints capturam bem padrões locais de energia, saliência espectral, frequência e atividade por banda, o que favorece a dimensão **arousal**. Já **valence** permanece mais difícil por depender de aspectos musicais de nível mais alto, como harmonia, modo, progressões, textura e contexto musical.

---

## Organização do repositório

```text
.
├── README.md
├── TCC II/
│   ├── TCC_STFT.ipynb
│   └── TCC_Fingerprint.ipynb
└── TCC III/
    ├── Binária/
    ├── Multiclasse/
    ├── Regressão/
    └── Técnicas_Fingerprint/
        ├── TCC_Fingerprint_Rank.ipynb
        └── TCC_Fingerprint_Band_Rank.ipynb
```

### TCC II

Fase inicial do projeto, com notebooks voltados à construção do pipeline base:

| Notebook | Função |
|---|---|
| `TCC_STFT.ipynb` | processamento inicial do áudio, segmentação e exportação de blocos |
| `TCC_Fingerprint.ipynb` | geração de espectrogramas, mapas de constelação, picos espectrais e hashes inspirados em fingerprinting acústico |

### TCC III

Fase final de experimentação, validação e análise dos resultados:

| Pasta | Função |
|---|---|
| `TCC III/Regressão/` | experimentos de regressão para `valence` e `arousal` |
| `TCC III/Multiclasse/` | classificação direta dos quadrantes emocionais |
| `TCC III/Binária/` | classificadores binários `One-vs-Rest` por quadrante |
| `TCC III/Técnicas_Fingerprint/` | geração e análise das representações de fingerprinting |

---

## Representações de fingerprinting

### `Rank`

Notebook principal:

```text
TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Rank.ipynb
```

Representação baseada no ranqueamento dos principais picos espectrais por bloco.

Configurações principais:

| Parâmetro | Valor |
|---|---|
| `RANDOM_STATE` | 42 |
| `AUDIO_START_SEC` | 15.0 |
| `BLOCK_SIZE_SEC` | 10.0 |
| `BLOCK_STEP_SEC` | 10.0 |
| `TOP_K` | 30 |
| `N_FFT_GLOBAL` | 2048 |
| `HOP_LENGTH_GLOBAL` | 1024 |
| `WINDOW_TYPE` | `hann` |
| `SR_TARGET` | 44100 Hz |

Saída consolidada esperada:

```text
fingerprint_rank.parquet
```

### `Band Rank`

Notebook principal:

```text
TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Band_Rank.ipynb
```

Representação que divide o espectro em bandas e extrai os principais picos dentro de cada faixa de frequência.

Bandas utilizadas:

| Banda | Faixa de frequência |
|---|---|
| `low` | 20–250 Hz |
| `mid` | 250–2000 Hz |
| `high` | 2000–8000 Hz |
| `very_high` | 8000 Hz–Nyquist |

Configurações principais:

| Parâmetro | Valor |
|---|---|
| `TOPK_PER_BAND` | 10 |
| `N_FFT_GLOBAL` | 2048 |
| `HOP_LENGTH_GLOBAL` | 1024 |
| `WINDOW_TYPE` | `hann` |
| `SR_TARGET` | 44100 Hz |

Saídas consolidadas esperadas:

```text
fingerprint_band_rank.parquet
fingerprint_band_rank_raw_peaks.parquet
```

---

## Pipeline metodológico

O fluxo geral dos experimentos é:

```text
Áudio DEAM
   ↓
Segmentação temporal em blocos de 10 s
   ↓
STFT por bloco
   ↓
Extração de picos espectrais
   ↓
Geração das representações Rank e Band Rank
   ↓
Limpeza de atributos e remoção de colunas de vazamento
   ↓
Modelagem: regressão, multiclasse e One-vs-Rest
   ↓
Comparação com openSMILE
   ↓
Fusão openSMILE + fingerprints
   ↓
Análise estatística e interpretação pela taxonomia de Panda et al.
```

---

## Controle de vazamento de dados

Como uma mesma música pode gerar vários blocos temporais, o projeto utiliza validação agrupada por `song_id`.

Essa decisão é importante porque impede que blocos muito parecidos da mesma faixa musical sejam divididos entre treino e validação, o que produziria estimativas artificialmente otimistas.

Regras adotadas:

- usar `GroupKFold(5)` agrupado por `song_id`;
- remover `valence`, `arousal`, `quadrant` e `quadrant_label` da matriz de atributos antes do treinamento supervisionado;
- manter rótulos emocionais apenas para rastreabilidade, análise e avaliação;
- evitar split aleatório por linha individual quando há múltiplos blocos da mesma música.

---

## Tecnologias utilizadas

| Categoria | Bibliotecas / ferramentas |
|---|---|
| Linguagem | Python 3.x |
| Notebooks | Jupyter / Google Colab |
| Processamento de áudio | `librosa`, `scipy` |
| Dados | `pandas`, `numpy`, `pyarrow`, `fastparquet` |
| Modelagem | `scikit-learn`, `PyCaret` |
| Visualização | `plotly`, `kaleido` |
| Paralelização e progresso | `joblib`, `tqdm` |
| Formato dos artefatos | Parquet, CSV, HTML, PNG |

---

## Como executar

### 1. Clonar o repositório

```bash
git clone https://github.com/felipednegredo/tcc-emocoes-musicais-codigo.git
cd tcc-emocoes-musicais-codigo
```

### 2. Criar ambiente virtual

```bash
python -m venv .venv
```

Ativar no Windows:

```bash
.venv\Scripts\activate
```

Ativar no Linux/macOS:

```bash
source .venv/bin/activate
```

### 3. Instalar dependências principais

```bash
pip install -U numpy pandas scipy librosa scikit-learn plotly kaleido pyarrow fastparquet tqdm joblib
```

Se for reproduzir os notebooks com AutoML/experimentos via PyCaret:

```bash
pip install -U pycaret
```

### 4. Configurar os caminhos locais

Antes de executar os notebooks, ajuste as variáveis de caminho conforme o local da base DEAM no seu ambiente.

Exemplos de variáveis usadas nos notebooks:

```python
BASE_DIR = pathlib.Path(r"C:\dev\python\TCC Ajustado\Dados")
AUDIO_DIR = BASE_DIR / "audio"
FINGERPRINT_DIR = BASE_DIR / "fingerprint_band_rank"
```

No Google Colab, ajuste o `ROOT` para a pasta montada no Google Drive.

### 5. Ordem sugerida de execução

1. `TCC II/TCC_STFT.ipynb`
2. `TCC II/TCC_Fingerprint.ipynb`
3. `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Rank.ipynb`
4. `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Band_Rank.ipynb`
5. Notebooks de modelagem em:
   - `TCC III/Regressão/`
   - `TCC III/Multiclasse/`
   - `TCC III/Binária/`

---

## Artefatos gerados

Os principais artefatos produzidos pelo pipeline são arquivos Parquet com granularidade por bloco ou por pico espectral.

| Artefato | Descrição |
|---|---|
| `fingerprint_rank.parquet` | atributos consolidados da representação Rank por bloco |
| `fingerprint_band_rank.parquet` | atributos por banda de frequência agregados por bloco |
| `fingerprint_band_rank_raw_peaks.parquet` | picos brutos por banda, em granularidade mais detalhada |
| tabelas de métricas | resultados de regressão, multiclasse e One-vs-Rest |
| figuras e HTMLs | visualizações de espectrogramas, constelações, rankings e matrizes |

Os arquivos derivados da base DEAM e artefatos pesados não devem ser versionados diretamente no GitHub.

---

## Interpretação dos resultados

A principal conclusão do projeto é que o fingerprinting acústico possui valor como representação **complementar**, mas não como substituto direto de descritores acústicos consolidados.

Em termos práticos:

- `openSMILE` representa melhor um conjunto amplo de características acústicas tradicionais;
- fingerprints capturam padrões locais de saliência espectral e distribuição de energia;
- a fusão é mais promissora quando a emoção depende de atividade, intensidade e energia, como em **arousal**;
- `valence` permanece mais difícil por depender de relações musicais mais abstratas, como harmonia, modo, progressão e contexto.

Essa leitura também dialoga com a taxonomia de Panda et al., pois as features de fingerprinting cobrem melhor aspectos próximos de dinâmica, saliência espectral e altura local, mas representam menos conceitos musicais de alto nível, como textura, forma musical e expressividade.

---

## Limitações

- O projeto não avalia robustez típica de fingerprinting clássico, como resistência a ruído, compressão, mudança de pitch ou alteração de tempo.
- Os fingerprints foram adaptados para análise emocional, não para identificação musical.
- A comparação principal é experimental e interpretativa, não uma tentativa de superar o estado da arte em MER.
- A dimensão `valence` apresenta maior dificuldade, principalmente pela limitação das features de baixo nível em capturar atributos musicais abstratos.

---

## Trabalhos futuros

Possíveis extensões do projeto:

- testar representações harmônicas e tonais mais explícitas;
- incorporar descritores de modo, progressão harmônica, textura e forma musical;
- avaliar modelos temporais que preservem sequência entre blocos;
- explorar fusões com embeddings de áudio modernos;
- comparar fingerprints com representações auto-supervisionadas;
- validar a abordagem em outras bases de MER, como EmoMusic, PMEmo ou MER500.

---

## Referência acadêmica

```bibtex
@monography{negredo2026fingerprinting,
  author  = {Negredo, Felipe de},
  title   = {Detecção de Emoções em Músicas com base em Fingerprinting e Análise Tempo-Frequência},
  school  = {Universidade do Vale do Itajaí},
  year    = {2026},
  address = {Itajaí, SC},
  note    = {Trabalho Técnico-científico de Conclusão de Curso em Ciência da Computação}
}
```

---

## Autor

**Felipe de Negredo**  
Curso de Ciência da Computação — Universidade do Vale do Itajaí  
Área: Processamento de Sinais / Music Information Retrieval / Music Emotion Recognition
