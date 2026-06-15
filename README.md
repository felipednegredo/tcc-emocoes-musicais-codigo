# Detecção de Emoções em Músicas com Fingerprinting e Análise Tempo–Frequência

[![Python](https://img.shields.io/badge/Python-3.x-blue.svg)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebooks-orange.svg)](https://jupyter.org/)
[![Dataset](https://img.shields.io/badge/Dataset-DEAM-green.svg)](https://cvml.unige.ch/databases/DEAM/)
[![Área](https://img.shields.io/badge/MIR-Music%20Emotion%20Recognition-purple.svg)](#)
[![Status](https://img.shields.io/badge/status-academic%20research-lightgrey.svg)](#)

Repositório de códigos, notebooks e artefatos experimentais do Trabalho Técnico-científico de Conclusão de Curso:

> **Detecção de Emoções em Músicas com base em Fingerprinting e Análise Tempo–Frequência**  
> Autor: **Felipe de Negredo**  
> Curso de Ciência da Computação — Universidade do Vale do Itajaí  
> Área: Processamento de Sinais / Music Information Retrieval / Music Emotion Recognition

---

## Sumário

- [Sobre o projeto](#sobre-o-projeto)
- [Pergunta de pesquisa](#pergunta-de-pesquisa)
- [Resumo técnico](#resumo-técnico)
- [Principais resultados](#principais-resultados)
- [Organização do repositório](#organização-do-repositório)
- [Organização local dos dados](#organização-local-dos-dados)
- [Notebooks principais](#notebooks-principais)
- [Pipeline experimental](#pipeline-experimental)
- [Representações de fingerprinting](#representações-de-fingerprinting)
- [Controle de vazamento de dados](#controle-de-vazamento-de-dados)
- [Como executar](#como-executar)
- [Artefatos gerados](#artefatos-gerados)
- [Interpretação dos resultados](#interpretação-dos-resultados)
- [Limitações](#limitações)
- [Trabalhos futuros](#trabalhos-futuros)
- [Referência acadêmica](#referência-acadêmica)

---

## Sobre o projeto

Este projeto investiga o uso de **fingerprinting acústico** como representação compacta e interpretável para tarefas de **Music Emotion Recognition (MER)**.

A proposta utiliza a **Short-Time Fourier Transform (STFT)** para decompor o sinal musical em tempo–frequência e, a partir dela, extrai picos espectrais que são organizados em representações de fingerprinting. Essas representações são comparadas com descritores acústicos tradicionais extraídos por `openSMILE`, utilizados como baseline.

O objetivo central não é substituir o `openSMILE` nem superar o estado da arte em MER, mas avaliar se os fingerprints acústicos podem fornecer informação **complementar** para a predição de emoções musicais no espaço **valence–arousal**.

---

## Pergunta de pesquisa

A questão prática investigada neste repositório é:

> Representações compactas baseadas em picos espectrais e fingerprinting acústico conseguem contribuir para o reconhecimento de emoções em músicas quando comparadas ou combinadas com descritores acústicos tradicionais?

A resposta encontrada no TCC é **parcialmente sim**: os fingerprints isolados não superam o baseline de forma geral, mas apresentam contribuição complementar em cenários específicos, principalmente na dimensão **arousal** e em algumas formulações de classificação.

---

## Resumo técnico

| Item | Descrição |
|---|---|
| Área | Music Information Retrieval / Music Emotion Recognition |
| Dataset | DEAM — Database for Emotion Analysis using Music |
| Amostras | 1.802 músicas |
| Unidade experimental | blocos temporais de até 10 s |
| Total de blocos | 6.506 blocos |
| Modelo emocional | valence–arousal |
| Baseline | `openSMILE` |
| Proposta | fingerprints acústicos extraídos de picos espectrais via STFT |
| Representações | `Rank`, `Band Rank`, `Raw Peaks` |
| Tarefas avaliadas no TCC | regressão, classificação multiclasse e One-vs-Rest |
| Validação | `GroupKFold(5)` agrupado por `song_id` |
| Métricas principais | RMSE, MAE, R², Macro-F1, F1 positivo e acurácia balanceada |

---

## Principais resultados

Os resultados indicam que o `openSMILE` permanece como a representação mais estável na maioria das tarefas. No entanto, a fusão com fingerprints apresentou ganhos seletivos.

| Cenário | Resultado observado |
|---|---|
| `openSMILE` isolado | representação mais robusta e estável na maioria das tarefas |
| Fingerprints isolados | desempenho inferior ao baseline na maior parte dos cenários |
| Fusão `openSMILE + fingerprints` em `arousal` | redução de RMSE de 4,1% |
| Fusão `openSMILE + fingerprints` em `arousal` | aumento de R² de 0,5779 para 0,6119 |
| Fusão na classificação multiclasse | Macro-F1 de 0,4680 para 0,4826 |
| Classificação One-vs-Rest | ganhos seletivos em quadrantes específicos |

A principal conclusão experimental é que os fingerprints têm maior utilidade como representação **complementar**, especialmente para aspectos ligados a energia, intensidade, saliência espectral e atividade acústica, que se relacionam mais diretamente com **arousal**.

---

## Organização do repositório

A estrutura abaixo mostra os **notebooks reais versionados no repositório**. Ela preserva o histórico do desenvolvimento do TCC: primeiro a construção do pipeline de STFT e fingerprinting no **TCC II**, depois a preparação do baseline, a geração das features finais e as análises interpretativas no **TCC III**.

```text
.
├── README.md
│
├── TCC II/
│   ├── TCC_STFT.ipynb
│   └── TCC_Fingerprint.ipynb
│
└── TCC III/
    ├── 00_construir_opensmile_blocks.ipynb
    │
    ├── Técnicas_Fingerprint/
    │   ├── TCC_Fingerprint_Rank.ipynb
    │   └── TCC_Fingerprint_Band_Rank.ipynb
    │
    └── Analises/
        └── Analise_Pandas_Fingerprint_Panda_Taxonomia.ipynb
```

### Leitura rápida por finalidade

| Caminho | Papel no projeto | Quando usar |
|---|---|---|
| `README.md` | documentação geral do projeto | primeiro contato com o repositório |
| `TCC II/TCC_STFT.ipynb` | exploração inicial da STFT, blocos temporais e estrutura dos dados | entender a base tempo–frequência do projeto |
| `TCC II/TCC_Fingerprint.ipynb` | protótipo inicial de constelações, picos espectrais e hashes | entender a origem da abordagem de fingerprinting |
| `TCC III/00_construir_opensmile_blocks.ipynb` | construção dos blocos e artefatos relacionados ao baseline `openSMILE` | preparar a comparação com descritores acústicos tradicionais |
| `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Rank.ipynb` | geração da representação `Rank` por picos espectrais globais | produzir `fingerprint_rank.parquet` |
| `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Band_Rank.ipynb` | geração da representação `Band Rank` e dos picos brutos por banda | produzir `fingerprint_band_rank.parquet` e `fingerprint_band_rank_raw_peaks.parquet` |
| `TCC III/Analises/Analise_Pandas_Fingerprint_Panda_Taxonomia.ipynb` | análise interpretativa das features de fingerprinting pela taxonomia de Panda et al. | explicar a cobertura conceitual das features e a diferença entre `arousal` e `valence` |

### Organização por etapa experimental

| Etapa | Notebook / artefato relacionado | Processamento | Saída esperada |
|---|---|---|---|
| 1. Exploração tempo–frequência | `TCC II/TCC_STFT.ipynb` | STFT, blocos e inspeção do sinal | blocos e visualizações iniciais |
| 2. Protótipo de fingerprinting | `TCC II/TCC_Fingerprint.ipynb` | constelações, picos e hashes | protótipos e artefatos iniciais |
| 3. Baseline acústico | `TCC III/00_construir_opensmile_blocks.ipynb` | preparação dos blocos `openSMILE` | artefatos de baseline por bloco |
| 4. Fingerprint Rank | `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Rank.ipynb` | ranqueamento dos principais picos globais | `fingerprint_rank.parquet` |
| 5. Fingerprint Band Rank | `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Band_Rank.ipynb` | extração de picos por banda de frequência | `fingerprint_band_rank.parquet` |
| 6. Raw Peaks | `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Band_Rank.ipynb` | preservação granular dos picos por banda | `fingerprint_band_rank_raw_peaks.parquet` |
| 7. Análise conceitual | `TCC III/Analises/Analise_Pandas_Fingerprint_Panda_Taxonomia.ipynb` | mapeamento das features na taxonomia de Panda et al. | tabelas e gráficos interpretativos |

---

## Organização local dos dados

Os dados da DEAM e os artefatos pesados **não devem ser versionados no GitHub**. Os notebooks esperam uma estrutura local semelhante a esta:

```text
Dados/
├── audio/                         # áudios da base DEAM
├── valence.csv                    # anotações temporais de valence
├── arousal.csv                    # anotações temporais de arousal
│
├── opensmile/
│   ├── opensmile_blocks.parquet
│   ├── opensmile_blocks_inventory.csv
│   ├── opensmile_taxonomy_features.csv
│   └── opensmile_taxonomy_summary.csv
│
├── fingerprint_rank/
│   └── fingerprint_rank.parquet
│
├── fingerprint_band_rank/
│   ├── fingerprint_band_rank.parquet
│   ├── fingerprint_band_rank_raw_peaks.parquet
│   └── songs/                     # saídas opcionais por música
│
└── resultados/
    ├── tabelas/
    ├── figuras/
    └── modelos/
```

Essa separação mantém o repositório leve e evita versionar arquivos derivados, áudios, Parquets grandes, modelos treinados ou imagens exportadas.

---

## Notebooks principais

### TCC II

| Notebook | Objetivo |
|---|---|
| `TCC II/TCC_STFT.ipynb` | processar áudio, explorar STFT e preparar blocos temporais |
| `TCC II/TCC_Fingerprint.ipynb` | gerar mapas de constelação, picos espectrais e hashes inspirados em fingerprinting acústico |

### TCC III

| Notebook | Objetivo | Saída / contribuição principal |
|---|---|---|
| `TCC III/00_construir_opensmile_blocks.ipynb` | construir a base por blocos com descritores `openSMILE` | baseline acústico e inventários de features |
| `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Rank.ipynb` | extrair picos globais por bloco e gerar ranking espectral | `fingerprint_rank.parquet` |
| `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Band_Rank.ipynb` | extrair picos por banda de frequência e picos brutos | `fingerprint_band_rank.parquet` e `fingerprint_band_rank_raw_peaks.parquet` |
| `TCC III/Analises/Analise_Pandas_Fingerprint_Panda_Taxonomia.ipynb` | analisar a cobertura das features de fingerprinting segundo Panda et al. | gráficos e tabelas interpretativas para o TCC |

---

## Pipeline experimental

```text
Áudio DEAM
   ↓
Remoção / ignorar trecho inicial de 15 s
   ↓
Segmentação em blocos de até 10 s
   ↓
STFT por bloco
   ↓
Extração de picos espectrais
   ↓
Geração das representações Rank, Band Rank e Raw Peaks
   ↓
Preparação do baseline openSMILE por bloco
   ↓
Limpeza de atributos e remoção de colunas de vazamento
   ↓
Validação agrupada por song_id
   ↓
Modelagem supervisionada avaliada no TCC
   ├── regressão contínua de valence/arousal
   ├── classificação multiclasse por quadrantes
   └── classificação binária One-vs-Rest
   ↓
Comparação entre openSMILE, fingerprints e fusão
   ↓
Análise estatística e interpretação conceitual
```

---

## Representações de fingerprinting

### `Rank`

Representação baseada no ranqueamento dos principais picos espectrais de cada bloco.

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

Saída principal:

```text
fingerprint_rank.parquet
```

### `Band Rank`

Representação baseada na divisão do espectro em bandas de frequência e extração dos principais picos dentro de cada banda.

| Banda | Faixa de frequência |
|---|---|
| `low` | 20–250 Hz |
| `mid` | 250–2000 Hz |
| `high` | 2000–8000 Hz |
| `very_high` | 8000 Hz–Nyquist |

| Parâmetro | Valor |
|---|---|
| `TOPK_PER_BAND` | 10 |
| `N_FFT_GLOBAL` | 2048 |
| `HOP_LENGTH_GLOBAL` | 1024 |
| `WINDOW_TYPE` | `hann` |
| `SR_TARGET` | 44100 Hz |

Saídas principais:

```text
fingerprint_band_rank.parquet
fingerprint_band_rank_raw_peaks.parquet
```

---

## Controle de vazamento de dados

Como uma música pode gerar vários blocos temporais, a validação precisa impedir que blocos da mesma música apareçam simultaneamente em treino e validação.

Por isso, os experimentos finais usam:

```python
GroupKFold(n_splits=5)
```

com agrupamento por:

```python
groups = song_id
```

Regras metodológicas importantes:

- nunca dividir treino e validação por linha individual quando há múltiplos blocos da mesma música;
- remover `valence`, `arousal`, `quadrant` e `quadrant_label` da matriz `X` antes do treinamento;
- manter rótulos emocionais apenas para rastreabilidade e avaliação;
- usar o mesmo protocolo de validação ao comparar `openSMILE`, fingerprints e fusões;
- documentar qualquer diferença entre matriz bruta, matriz limpa e inventário de features.

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

Windows:

```bash
.venv\Scripts\activate
```

Linux/macOS:

```bash
source .venv/bin/activate
```

### 3. Instalar dependências principais

```bash
pip install -U numpy pandas scipy librosa scikit-learn plotly kaleido pyarrow fastparquet tqdm joblib
```

Para notebooks que usam PyCaret:

```bash
pip install -U pycaret
```

### 4. Configurar caminhos locais

Ajuste nos notebooks as variáveis que apontam para a base DEAM e para as saídas locais.

Exemplo:

```python
import pathlib

BASE_DIR = pathlib.Path(r"C:\dev\python\TCC Ajustado\Dados")
AUDIO_DIR = BASE_DIR / "audio"
VALENCE_PATH = BASE_DIR / "valence.csv"
AROUSAL_PATH = BASE_DIR / "arousal.csv"
```

Para o Google Colab, monte o Google Drive e ajuste o `ROOT` para a pasta correspondente.

### 5. Ordem sugerida de execução

| Ordem | Caminho | Finalidade |
|---|---|---|
| 1 | `TCC II/TCC_STFT.ipynb` | exploração e preparação inicial |
| 2 | `TCC II/TCC_Fingerprint.ipynb` | protótipo de fingerprinting |
| 3 | `TCC III/00_construir_opensmile_blocks.ipynb` | preparar os blocos e inventários do baseline `openSMILE` |
| 4 | `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Rank.ipynb` | gerar `fingerprint_rank.parquet` |
| 5 | `TCC III/Técnicas_Fingerprint/TCC_Fingerprint_Band_Rank.ipynb` | gerar `fingerprint_band_rank.parquet` e `raw_peaks` |
| 6 | `TCC III/Analises/Analise_Pandas_Fingerprint_Panda_Taxonomia.ipynb` | gerar análises interpretativas pela taxonomia de Panda et al. |

---

## Artefatos gerados

| Artefato | Descrição | Versionar? |
|---|---|---|
| `opensmile_blocks.parquet` | blocos com descritores acústicos `openSMILE` usados como baseline | não |
| `opensmile_blocks_inventory.csv` | inventário das features `openSMILE` | opcional, se pequeno |
| `opensmile_taxonomy_features.csv` | mapeamento das features `openSMILE` em categorias conceituais | opcional, se pequeno |
| `opensmile_taxonomy_summary.csv` | resumo da taxonomia `openSMILE` | opcional, se pequeno |
| `fingerprint_rank.parquet` | atributos consolidados da representação Rank por bloco | não |
| `fingerprint_band_rank.parquet` | atributos por banda de frequência agregados por bloco | não |
| `fingerprint_band_rank_raw_peaks.parquet` | picos brutos por banda | não |
| tabelas de métricas | resultados de regressão, multiclasse e One-vs-Rest | depende do tamanho |
| figuras do TCC | visualizações finais usadas no relatório | opcional |
| modelos treinados | modelos gerados nos experimentos | não |
| HTMLs interativos | gráficos Plotly exportados | opcional / externo |

Recomendação: manter no GitHub apenas notebooks, scripts, documentação e pequenos arquivos de configuração. Artefatos grandes devem ficar em armazenamento externo.

---

## Interpretação dos resultados

A leitura final do TCC é que o fingerprinting acústico funciona melhor como uma fonte complementar de informação.

Em termos práticos:

- `openSMILE` captura um conjunto amplo e consolidado de descritores acústicos;
- fingerprints representam saliência espectral, energia por banda e padrões locais de frequência;
- `arousal` tende a se beneficiar mais dessas informações por estar associado a energia, intensidade e atividade acústica;
- `valence` permanece mais difícil, pois depende de relações musicais mais abstratas, como harmonia, modo, progressão e contexto;
- a fusão é mais relevante quando os atributos adicionam informação que o baseline não representa completamente.

Essa interpretação também se conecta à taxonomia de Panda et al.: as features de fingerprinting cobrem melhor aspectos próximos de dinâmica, saliência, frequência e altura local, mas cobrem menos textura, forma musical e técnicas expressivas.

---

## Limitações

- Os fingerprints foram adaptados para reconhecimento emocional, não para identificação musical clássica.
- O projeto não avalia robustez a ruído, compressão, mudança de pitch ou alteração de tempo.
- Os fingerprints isolados não superam o `openSMILE` na maior parte dos cenários.
- A dimensão `valence` continua sendo a mais difícil.
- A base experimental depende da organização local da DEAM e de artefatos derivados que não são distribuídos neste repositório.

---

## Trabalhos futuros

Possíveis extensões:

- adicionar descritores harmônicos e tonais mais explícitos;
- representar modo, progressão harmônica, textura e forma musical;
- testar modelos temporais que preservem sequência entre blocos;
- comparar fingerprints com embeddings modernos de áudio;
- avaliar novas estratégias de fusão;
- validar o pipeline em outras bases de MER, como EmoMusic, PMEmo ou MER500.

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
