# TCC: Reconhecimento de Emoções Musicais via Fingerprinting Espectral

Este repositório contém notebooks e experimentos relacionados à extração de assinaturas espectrais (fingerprints) e ao reconhecimento de emoções musicais.

---

## TCC II

Fase de desenvolvimento do pipeline básico: processamento espectral e geração de fingerprints.

- `TCC_STFT.ipynb`: processamento inicial com STFT, divisão em blocos e exportação para Parquet.
- `TCC_Fingerprint.ipynb`: detecção de picos, construção de constelações e geração de hashes (abordagem tipo Shazam).

Os notebooks acima geram artefatos em `ai_ready/` para uso em treinamentos de modelos.

---

## TCC III

Fase de experimentação e avaliação dos modelos (classificação binária, multiclasse e regressão), além de técnicas avançadas de fingerprint.

- `TCC III/Binária/`: experimentos de classificação binária.
- `TCC III/Multiclasse/`: experimentos de classificação multiclasse.
- `TCC III/Regressão/`: experimentos de regressão sobre Valence/Arousal.
- `TCC III/Técnicas_Fingerprint/`: notebooks adicionais sobre variações e análise de banda (`TCC_Fingerprint_Band_Rank.ipynb`, `TCC_Fingerprint_Rank.ipynb`).

*Obs.: as pastas do TCC III organizam os diferentes protocolos de avaliação e as variantes do modelo.*

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
