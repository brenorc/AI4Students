## Oracle 23ai Vector Search

# 🎯 **Objetivos**

Demonstrar de forma prática como utilizar a funcionalidade de busca vetorial do Oracle 23c AI Vector Search.

O que você aprenderá:

- Criar e configurar um banco de dados autônomo (Autonomous Database) no Oracle Cloud Infrastructure (OCI).
- Utilizar a funcionalidade de busca vetorial para otimizar consultas e análises em PDFs.
- Explorar as vantagens do Oracle 23c AI na integração de dados relacionais e semânticos.

### _**Aproveite sua experiência na Oracle Cloud!**_


## 📌 Introdução

>**Com o Oracle 23c AI, o AI Vector Search integra vetores ao Oracle Database, eliminando bancos especializados e evitando a fragmentação de dados. Essa tecnologia permite buscas por similaridade combinadas com SQL simples, potencializando modelos de linguagem (LLMs) com contexto adicional. Inclui novo tipo de dado vetorial, índices específicos e extensões SQL para análises avançadas diretamente no banco de dados.** 

![Semantic](images/semantic.png)

Referência: [Announcing Oracle Database 23ai : General Availability
](https://blogs.oracle.com/database/post/oracle-23ai-now-generally-available)

### ➡️ **O que é Vector Database?**

> Um banco de dados vetorial é qualquer banco de dados que pode armazenar e gerenciar embeddings vetoriais de forma nativa e lidar com os dados não estruturados que eles descrevem, como documentos, imagens, vídeos ou áudios.


### ➡️ **Como o Oracle AI Vector Search revoluciona a busca de dados semânticos no Oracle Database?**

> O Oracle AI Vector Search permite criar tabelas com o novo tipo de dado VECTOR, armazenando vetores diretamente no banco de dados. É possível inserir vetores com INSERT, carregar dados usando SQL Loader ou Data Pump, e criar índices para otimizar buscas em embeddings. Novas funções SQL, como as de cálculo de distância vetorial, facilitam a análise de vetores, enquanto operadores adicionais permitem criar, converter e descrever vetores, além de realizar chunking e embeddings de dados de forma eficiente.

### **Recursos e Suporte**:
- **Documentação da Oracle Cloud**: [Getting started with vectors in 23ai](https://blogs.oracle.com/coretec/post/getting-started-with-vectors-in-23ai)
- **Tutoriais**: [Oracle Database 23ai - Oracle AI Vector Search & Retrieval Augmented Generation (RAG) with Oracle APEX](https://www.linkedin.com/pulse/oracle-database-23ai-ai-vector-search-retrieval-augmented-rao-bqkcf/)

## 1️⃣ Validação de Região

Faça o login no Oracle Cloud Infrastructure (OCI) e valide se a região de Chicago se encontra disponível para uso.

   ![Validate Region](images/validate-region.png " ")

> **Caso a região de Chicago não esteja disponível, clique em manage region (caso já esteja disponível pule para a sessão 2 - Criação de Autonomous Database)** 
![Manage Region](images/manage-region.png)

Busque por **US Midwest (Chicago)** e clique no botão subscribe. O processo de subscrição pode levar alguns minutos, aguarde para dar sequência ao workshop. Clique no ícone escrito **Oracle Cloud** no canto esquerdo e faça logoff e login para validar se a região já foi subscrita.

![Subscribe Region](images/subscribe-region.png)

## 2️⃣ Criação de Autonomous Database

Clique no menu de hambúrger do canto superior esquerdo da tela, na sequência navegue até a página de gestão de autonomous databases.

![Autonomous Acess](images/autonomous-acess.png)

Na página de gestão de Autonomous Databases, clique em create autonomous database. Selecione **ATP ou ADW**
  
![Create Autonomous](images/create-autonomous.png)

Escolha a versão 23ai para o banco de dados:

![Create 23AI](images/create-23ai.png)

Coloque a senha **WORKSHOPsec2019##** , escolha **secure access from everywhere** e clique em **Create Autonomous Database**:
<br><br>

![Secure Acess](images/secure-acess.png)

Aguarde até a conclusão da criação: 
- Ícone amarelo = criando; 
- Ícone verde = pronto para uso;
![Yellow ADW](images/yellow-adw.png)
![Green ADW](images/green-adw.png)

## 3️⃣ Configurando o Autonomous Database

Clique no ícone chamado database actions e SQL:
![Database Actions](images/database-actions.png)

Caso seja requisitado, o usuário é **admin** e a senha é a **fornecida na criação do autonomous database da etapa anteiror.**
Feche todos os tutoriais que aparecerão na página.
Copie, cole e execute os comandos abaixo:


    <copy>  
        --Criação de credencial
    BEGIN
        DBMS_CLOUD.CREATE_CREDENTIAL(
            credential_name => 'OBJ_STORE_CRED',
            username => 'oracleidentitycloudservice/CAIO.OLIVEIRA@ORACLE.COM',
            password => 'teste'
        );
    END;
    /

    --Download de Modelo onnx do object storage para diretorio autonomous
    BEGIN
    dbms_cloud.get_object(
    credential_name => 'OBJ_STORE_CRED'
    , object_uri => 'https://objectstorage.us-chicago-1.oraclecloud.com/p/zlrcQmv79yuagr-zVNipnBVtyzRQV4Lj_qypuKu0Wr7_Zbmnbh55C3a-sxmq_nKX/n/idajmumkp9ca/b/embedding-model/o/all_MiniLM_L12_v2.onnx'
    , directory_name => 'DATA_PUMP_DIR'
    , file_name => 'intfloatmodelsmall.onnx'
    );
    END;
    /

    --Criação de usuário/schema para import do modelo e criação da aplicação
    create user demo identified by "WORKSHOPsec2019##";
    grant dwrole to demo;
    grant unlimited tablespace to demo;
    grant read, write on directory data_pump_dir to demo;

    --Import de modelo onnx para autonomous
    EXECUTE dbms_vector.load_onnx_model('DATA_PUMP_DIR', 'intfloatmodelsmall.onnx', 'demo.doc_model', JSON('{"function" : "embedding", "embeddingOutput" : "embedding" , "input": {"input": ["DATA"]}}'));
    commit;

    </copy>

Execute conforme indicado abaixo:

![Execute Code](images\execute-code.png)

### Extra: Para testar se o modelo de embedding funcionou, você pode usar a consulta abaixo:

    SELECT TO_VECTOR(VECTOR_EMBEDDING(demo.doc_model USING 'Raça CAASO' as data)) AS embedding;

## 👥 Agradecimentos

- **Autores** - Caio Oliveira
- **Autores Contribuintes** - Breno Comin, Isabelle Anjos, Gabriela Miyazima, Aristotelles Serra
- **Última Atualização Por/Data** - Setembro 2025

## 🛡️ Declaração de Porto Seguro (Safe Harbor)

O tutorial apresentado tem como objetivo traçar a orientação dos nossos produtos em geral. É destinado somente a fins informativos e não pode ser incorporado a um contrato. Ele não representa um compromisso de entrega de qualquer tipo de material, código ou funcionalidade e não deve ser considerado em decisões de compra. O desenvolvimento, a liberação, a data de disponibilidade e a precificação de quaisquer funcionalidades ou recursos descritos para produtos da Oracle estão sujeitos a mudanças e são de critério exclusivo da Oracle Corporation.

Esta é a tradução de uma apresentação em inglês preparada para a sede da Oracle nos Estados Unidos. A tradução é realizada como cortesia e não está isenta de erros. Os recursos e funcionalidades podem não estar disponíveis em todos os países e idiomas. Caso tenha dúvidas, entre em contato com o representante de vendas da Oracle. 
