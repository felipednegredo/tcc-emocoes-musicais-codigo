# TCC: Reconhecimento de Emoções Musicais via Fingerprinting Espectral

Este projeto faz parte de um Trabalho de Conclusão de Curso (TCC) focado na análise e reconhecimento de emoções em músicas através de técnicas de processamento digital de sinais e aprendizado de máquina. O objetivo central é transformar clipes de áudio em representações compactas e robustas ("fingerprints") que preservem as características emocionais para treinamento de modelos de I.A.

---

## 🚀 Visão Geral do Pipeline

O fluxo de dados do projeto é dividido em três etapas principais, automatizadas via notebooks Python:

### 1. Processamento STFT (`TCC_STFT.ipynb`)
- **Entrada**: Arquivos de áudio brutos (Dataset DEAM).
- **Processamento**: 
    - Aplicação da Transformada de Fourier de Curto Termo (STFT).
    - Divisão da música em blocos temporais.
    - Sincronização com metadados de Valence/Arousal (VA).
- **Saída**: Arquivos Parquet compactados contendo as magnitudes espectrais, frequências e tempos de cada bloco.

### 2. Fingerprinting e Hashing (`TCC_Fingerprint.ipynb`)
- **Detecção de Picos**: Identificação dos pontos de maior energia no espectrograma (picos espectrais) usando filtros de densidade adaptativos.
- **Constelação**: Criação de um mapa de picos para cada bloco da música.
- **Geração de Hashes (Shazam-like)**:
    - Seleção de pontos de ancoragem e zonas de destino.
    - Criação de hashes estáveis baseados na relação temporal e de frequência entre picos.
    - Quantização para garantir robustez contra ruídos e distorções.
- **Visualização**: Geração de gráficos interativos (Plotly) comparando o espectrograma com o mapa de picos.

### 3. Preparação para I.A (`ai_ready/`)
- **Consolidação**: Os fingerprints são salvos em formato Parquet otimizado para consumo direto por modelos de Deep Learning.
- **Rótulos Emocionais**: Cada conjunto de hashes é acompanhado por seus valores de Valência e Arousal, permitindo o treinamento supervisionado em modelos de regressão ou classificação por quadrantes emocionais (Modelo de Russell).

---

## 📁 Estrutura de Arquivos

```text
tcc-emocoes-musicais-codigo/
├── TCC_STFT.ipynb         # Pipeline inicial de processamento de áudio
├── TCC_Fingerprint.ipynb    # Algoritmo de geração de fingerprints e hashes
├── README.md                # Documentação do projeto
└── .git/                    # Controle de versão
```

*Nota: Os dados processados (Parquets e HTMLs) são geralmente armazenados em diretórios externos (como Google Drive) devido ao grande volume.*

---

## 🛠️ Tecnologias Utilizadas

- **Linguagem**: Python 3.x
- **Processamento de Áudio**: `librosa`, `scipy`
- **Manipulação de Dados**: `pandas`, `numpy`, `pyarrow` (Parquet)
- **Visualização**: `plotly`, `kaleido`
- **Hashing**: `hashlib` (Blake2b)
- **Progresso**: `tqdm`

---

## 🧠 Conceitos Chave

- **Modelo de Russell (Valence/Arousal)**: As emoções são mapeadas em um espaço bidimensional onde a *Valência* indica o quão positiva/negativa é a emoção e o *Arousal* indica o nível de excitação/energia.
- **Fingerprint Espectral**: Técnica que extrai "assinaturas" únicas de áudio, permitindo identificar padrões musicais mesmo com variações de qualidade.
- **Snappy Compression**: Utilizado nos arquivos Parquet para equilibrar velocidade de leitura e espaço em disco.

---

## 📖 Como Executar

1. **Dependências**:
   ```bash
   pip install librosa pandas plotly tqdm pyarrow fastparquet kaleido scipy
   ```
2. **Execução**:
   - Execute o `TCC_STFT.ipynb` para processar os áudios e gerar os blocos iniciais.
   - Execute o `TCC_Fingerprint.ipynb` para gerar os mapas de picos e os hashes finais para a I.A.
   - Verifique a pasta de saída definida (`OUT_ROOT`) para os arquivos `ai_ready/` e os relatórios de visualização.

---
**Autor**: Felipe Negredo
**Contexto**: Pesquisa acadêmica sobre Music Information Retrieval (MIR).
