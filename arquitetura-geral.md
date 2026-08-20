# Arquitetura Geral – Projeto de Ingestão de Produtos no Azure Data Factory

Este documento descreve a arquitetura geral utilizada para construir o fluxo de ingestão da camada **Bronze** do Data Lake, seguindo o padrão **Medallion Architecture**.  
O objetivo é estabelecer uma base sólida para ingestão, transformação e disponibilização de dados para análises futuras.

---

## 🏛 Visão Geral da Arquitetura

A solução utiliza os seguintes componentes principais:

- **Azure Data Factory (ADF)** – Orquestração e ingestão de dados
- **Azure Data Lake Storage Gen2 (ADLS)** – Armazenamento estruturado em camadas
- **Datasets e Linked Services** – Conexões e representações dos dados
- **Copy Activity** – Responsável pela movimentação e transformação dos dados
- **Formato Parquet** – Armazenamento otimizado para leitura analítica

A arquitetura segue o padrão Medallion: Bronze → Silver → Gold


Neste projeto, implementamos a camada **Bronze**, responsável por armazenar dados brutos ou minimamente processados.

---

## 📂 Estrutura do Data Lake

A organização dos dados segue boas práticas de governança:

/bronze
produtos.xlsx 
/output
produtos.parquet


- **bronze/**: camada de ingestão bruta  
- **output/**: destino dos arquivos transformados  
- **produtos.parquet**: arquivo final gerado pela pipeline

---

## ⚙️ Componentes do Azure Data Factory

### 🔹 Linked Service
Conexão com o Data Lake:

- `ls_adls_portfolio`

### 🔹 Datasets
Representações dos dados:

- `ds_excel_produtos` – origem em Excel  
- `ds_parquet_produtos_bronze` – destino em Parquet  

### 🔹 Pipeline
Fluxo de ingestão:

- `pl_ingestao_produtos_bronze`
  - Atividade: **Copy Data**

---

## 🔄 Fluxo da Pipeline

1. **Leitura do arquivo de origem**  
   - Excel contendo dados de produtos

2. **Transformação mínima**  
   - Conversão para Parquet  
   - Compressão opcional (snappy ou none)

3. **Gravação no Data Lake**  
   - Caminho: `bronze/output/produtos.parquet`

4. **Validação da execução**  
   - Status: Succeeded  
   - Verificação do arquivo no container

---

## 🧩 Decisões Técnicas Importantes

### ✔ Uso de CSV como alternativa ao Excel
O ADF não suporta arquivos Excel criptografados, mesmo sem senha.  
CSV elimina esse problema e simplifica a ingestão.

### ✔ Criação manual da pasta `output`
O ADF não cria diretórios automaticamente.  
A pasta deve existir antes da execução.

### ✔ Compressão Snappy altera o nome do arquivo
Quando habilitada, o arquivo pode ser gerado como:

- `produtos.snappy.parquet`
- `produtos_000000.snappy.parquet`

Por isso, a compressão foi ajustada conforme necessidade.

### ✔ Delay na visualização do arquivo no portal
O Azure pode levar alguns segundos para exibir novos blobs.

---

## 🖼 Prints recomendados para documentação

Coloque estes prints na pasta `docs/imagens/`:

- Dataset Excel/CSV configurado  
- Dataset Parquet configurado  
- Copy Activity (Source e Sink)  
- Execução da pipeline (Succeeded)  
- Arquivo Parquet no Data Lake  

---

## 🚀 Próximos Passos da Arquitetura

Com a camada Bronze concluída, podemos evoluir para:

### **Silver**
- Padronização de tipos  
- Normalização de datas  
- Limpeza de valores nulos  
- Tratamento de categorias  

### **Gold**
- Tabelas analíticas  
- Métricas de produto  
- Dashboards no Power BI  

---

## 📬 Autor

Documentação criada por **Ranier**, com apoio da IA para estruturação e clareza técnica.



