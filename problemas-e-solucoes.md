# Problemas e Soluções – Ingestão Bronze (Produtos)

Este documento registra os principais problemas encontrados durante o desenvolvimento da ingestão Bronze no Azure Data Factory, bem como as soluções aplicadas.  
O objetivo é facilitar futuras manutenções, evitar retrabalho e servir como referência técnica para outros projetos.

---

## ⚠️ 1. Erro: EncryptedExcelIsNotSupported

### 🧩 Problema
Ao tentar ingerir o arquivo `produtos.xlsx`, o ADF retornou o erro: Encrypted excel file 'produtos.xlsx' is not supported


Mesmo sem senha, o Excel continha **metadados de criptografia interna**, comuns em arquivos gerados por versões modernas do Office.

### ✅ Solução
Criar um novo arquivo Excel limpo:

1. Abrir o arquivo original  
2. Selecionar tudo (Ctrl+A)  
3. Copiar (Ctrl+C)  
4. Colar em uma nova planilha  
5. Salvar como `.xlsx` novamente  

Ou, alternativamente:

👉 **Converter para CSV**, que elimina qualquer criptografia.

---

## ⚠️ 2. Pasta `output` inexistente no Data Lake

### 🧩 Problema
O ADF não cria diretórios automaticamente.  
A pipeline executava com sucesso, mas **nenhum arquivo Parquet era gerado**.

### ✅ Solução
Criar manualmente a pasta: bronze/output


Após isso, a pipeline passou a escrever corretamente o arquivo Parquet.

---

## ⚠️ 3. Execução “Succeeded” sem gerar arquivo

### 🧩 Problema
Mesmo com status “Succeeded”, o arquivo Parquet não aparecia no container.

### Causas possíveis:
- Arquivo Excel ainda criptografado  
- Dataset apontando para storage errado  
- Compressão Snappy alterando o nome do arquivo  
- Delay na atualização do portal do Azure

### ✅ Solução
- Recriar o arquivo Excel ou usar CSV  
- Validar o caminho do dataset Parquet  
- Ajustar compressão para `none` quando necessário  
- Atualizar o portal após alguns segundos

---

## ⚠️ 4. Compressão Snappy alterando o nome do arquivo

### 🧩 Problema
Com Snappy habilitado, o arquivo gerado não era exatamente `produtos.parquet`.

Exemplos possíveis:
- `produtos.snappy.parquet`
- `produtos_000000.snappy.parquet`
- `part-0000.snappy.parquet`

### ✅ Solução
Definir compressão como: none


Assim o arquivo é gerado exatamente como configurado.

---

## ⚠️ 5. Delay na visualização do arquivo no portal

### 🧩 Problema
O arquivo Parquet demorava alguns segundos para aparecer no container, mesmo após execução bem-sucedida.

### 🧠 Causa
O Azure Blob Storage pode atrasar a atualização visual, especialmente quando:
- o container estava vazio  
- o arquivo é pequeno  
- há replicação interna acontecendo  

### ✅ Solução
Aguardar alguns segundos e clicar em **Refresh**.

---

## ⚠️ 6. Dataset apontando para o storage errado

### 🧩 Problema
O arquivo não aparecia porque o usuário estava verificando outro storage account.

### 🧠 Causa
Datasets podem apontar para diferentes storages via Linked Services.

### ✅ Solução
Confirmar o storage correto: storageportfolioadls → bronze/output


---

## ✔️ Conclusão

Todos os problemas encontrados foram resolvidos com ajustes simples, mas essenciais:

- recriação do arquivo de origem  
- uso de CSV  
- criação manual de diretórios  
- validação de caminhos  
- ajuste de compressão  
- atenção ao storage correto  

Com isso, a ingestão Bronze está totalmente funcional e pronta para evoluir para Silver e Gold.

---

## 📬 Autor

Documentação criada por **Ranier**, com apoio da IA para organização e clareza técnica.





