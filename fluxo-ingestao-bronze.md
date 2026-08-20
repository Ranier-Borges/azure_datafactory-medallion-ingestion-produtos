# Fluxo de Ingestão – Camada Bronze (Produtos)

Este documento descreve o fluxo completo da ingestão de dados para a camada **Bronze** do Data Lake, utilizando o **Azure Data Factory (ADF)**.  
O objetivo é registrar o passo a passo operacional, desde a chegada do arquivo até a geração do Parquet final.

---

## 🏁 1. Entrada do Arquivo no Data Lake

O processo inicia com o envio do arquivo de origem para o Data Lake:

### 📥 Arquivo de entrada
- `produtos.xlsx` **ou** `produtos.csv`

### 📂 Local de armazenamento
/bronze


O arquivo é armazenado sem transformações, representando o dado bruto.

---

## 🔗 2. Configuração dos Datasets no ADF

### 🔹 Dataset de origem
Dependendo do formato utilizado:

- `ds_excel_produtos` (Excel)
- `ds_csv_produtos` (CSV)

Configurações principais:
- Linked Service: `ls_adls_portfolio`
- Caminho: `/bronze/produtos.xlsx` ou `/bronze/produtos.csv`
- Delimiter (CSV): `,`
- First row as header: `True`

### 🔹 Dataset de destino
- `ds_parquet_produtos_bronze`
- Caminho: `/bronze/output/produtos.parquet`
- Compressão: `none` ou `snappy`

---

## 🔄 3. Execução da Pipeline

A pipeline responsável pela ingestão é:

### **`pl_ingestao_produtos_bronze`**

Ela contém uma única atividade:

### 🔸 **Copy Data**

#### **Source**
- Dataset: origem (Excel ou CSV)
- Configuração: leitura direta do arquivo bruto

#### **Sink**
- Dataset: destino Parquet
- Configuração: escrita no formato otimizado

#### **Mapping**
- Importação automática do schema
- Mapeamento direto das colunas

---

## 🧪 4. Validação da Execução

Após iniciar a pipeline:

- O ADF processa o arquivo de origem
- Converte para Parquet
- Escreve no destino configurado

### ✔ Status esperado
Run Succeeded


### ✔ Verificação manual
No Data Lake:
/bronze/output/produtos.parquet


---

## ⚠️ 5. Problemas Comuns e Como Foram Resolvidos

### ❗ Excel criptografado
O ADF não suporta arquivos Excel com criptografia interna.  
Solução: recriar o arquivo ou usar CSV.

### ❗ Pasta `output` inexistente
O ADF não cria diretórios automaticamente.  
Solução: criar manualmente no Data Lake.

### ❗ Compressão Snappy altera o nome do arquivo
Arquivos podem aparecer como:
- `produtos.snappy.parquet`
- `produtos_000000.snappy.parquet`

Solução: ajustar compressão conforme necessidade.

### ❗ Delay na visualização do blob
O portal pode demorar alguns segundos para exibir novos arquivos.

---

## 📈 6. Resultado Final

Ao final do fluxo, temos:

### 📦 Arquivo Parquet gerado
bronze/output/produtos.parquet


### 🧱 Camada Bronze concluída
O dado bruto está padronizado e pronto para transformações na camada Silver.

---

## 🚀 7. Próximos Passos

Com a ingestão Bronze funcionando, podemos evoluir para:

### Silver
- Limpeza  
- Padronização  
- Tipagem  
- Normalização  

### Gold
- Métricas  
- Agregações  
- Dashboards  

---

## 📬 Autor

Documentação criada por **Ranier**, com apoio da IA para estruturação técnica e clareza do fluxo.



