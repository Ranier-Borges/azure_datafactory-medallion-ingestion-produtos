# Azure Data Factory – Pipeline de Ingestão Bronze (Produtos)

Este repositório documenta o processo de ingestão de dados utilizando o **Azure Data Factory (ADF)**, seguindo o padrão arquitetural **Medallion**.  
O objetivo é ingerir um arquivo de produtos, armazená-lo no Data Lake e convertê-lo para o formato **Parquet**, preparando a base para camadas Silver e Gold.

---

## 📌 Arquitetura Geral

A ingestão segue o fluxo:

1. **Fonte**: Arquivo Excel/CSV contendo dados de produtos  
2. **Bronze**: Armazenamento bruto no Azure Data Lake  
3. **ADF Pipeline**: Copy Activity para transformar o arquivo em Parquet  
4. **Destino**: `bronze/output/produtos.parquet`

---

## 🗂 Estrutura do Data Lake

/bronze
/produtos.xlsx
/output
produtos.parquet


---

## ⚙️ Componentes Criados no Azure Data Factory

### 🔹 Datasets
- `ds_excel_produtos` – origem (Excel)
- `ds_csv_produtos` – origem alternativa (CSV)
- `ds_parquet_produtos_bronze` – destino (Parquet)

### 🔹 Linked Service
- `ls_adls_portfolio` – conexão com o Azure Data Lake

### 🔹 Pipeline
- `pl_ingestao_produtos_bronze`
  - Atividade: **Copy Data**

---

## 🖼 Prints do Processo

1. **Dataset Excel/CSV configurado**  
   - Caminho do arquivo  
   - Delimiter (se CSV)  
   - First row as header  
   <img width="1914" height="856" alt="image" src="https://github.com/user-attachments/assets/a76c9cf5-3c2c-4cf9-9df5-ebf8d98138ad" />

    <img width="1904" height="908" alt="image" src="https://github.com/user-attachments/assets/6e285bbb-7d1d-419e-8997-6ed719d8bd82" />

2. **Dataset Parquet configurado**  
   - Container: bronze  
   - Diretório: output  
   - Arquivo: produtos.parquet  
   - Compressão: none ou snappy
     <img width="1916" height="782" alt="image" src="https://github.com/user-attachments/assets/2fe87c19-3b39-464f-9332-b72fc60934d3" />


3. **Copy Activity – Source e Sink**  
   - Source: dataset de origem  
   - Sink: dataset Parquet  

4. **Execução da Pipeline (Run Succeeded)**  
   - Print da tela de execução bem-sucedida  
<img width="1915" height="538" alt="image" src="https://github.com/user-attachments/assets/a81d5897-db84-4df9-ba9b-fcf6a0267d02" />

5. **Arquivo gerado no Data Lake**  
   - `bronze/output/produtos.parquet`
<img width="1905" height="907" alt="image" src="https://github.com/user-attachments/assets/a22b2df6-d167-49d8-a813-903ca581e99a" />

---

## 💡 Insights e Aprendizados

Durante o desenvolvimento, alguns pontos importantes foram descobertos:

### ✔ 1. Excel criptografado não é suportado pelo ADF
Mesmo sem senha, arquivos Excel podem conter **metadados de criptografia**.  
Isso gera o erro: EncryptedExcelIsNotSupported


Solução: recriar o arquivo ou usar CSV.

---

### ✔ 2. CSV é mais simples e mais confiável
O formato CSV evita:
- criptografia invisível  
- compressão ZIP  
- cabeçalhos inválidos  
- incompatibilidades do Excel  

E funciona perfeitamente no ADF.

---

### ✔ 3. O ADF não cria pastas automaticamente
A pasta `output` deve existir antes da execução.  
Caso contrário, o arquivo Parquet não aparece.

---

### ✔ 4. Execuções “Succeeded” podem não gerar arquivo
Mesmo com sucesso, o Sink pode não escrever se:
- o arquivo de origem falhar silenciosamente  
- o dataset estiver apontando para o storage errado  
- a compressão alterar o nome do arquivo

---

### ✔ 5. Storage Explorer pode demorar para atualizar
O arquivo pode aparecer alguns segundos depois da execução.

---

## 🚀 Próximos Passos

Com a camada Bronze pronta, você pode evoluir o projeto:

- Criar a **camada Silver** (limpeza, padronização, tipos)  
- Criar a **camada Gold** (tabelas analíticas)  
- Criar pipelines parametrizadas  
- Criar triggers automáticos  
- Implementar Dataflows para transformação  
- Criar dashboards no Power BI usando o Parquet  

---

## 📬 Autor

Projeto desenvolvido por **Ranier**, com apoio da IA para documentação e estruturação.



