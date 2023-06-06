# DW-Estoque
Neste Projeto utilizei uma modelagem modelo constelação, para a criação de um DW para monitoramento de estoque, é uma leitura rápida e dinâmica!


Gostaria de apresentar a vocês um projeto de Data Warehouse, que eu fiz utilizando a ferramenta 
SQL Server Integration Services (SSIS), foi um projeto baseado em um modelo constelação para controle de estoque.
Requisitos do Projeto 02

Stakeholder:  João Lopes

Contexto:
“Temos um banco de dados que armazena as informações dos produtos que entraram e saíram do nosso supermercado, atualmente a gerência se reúne com informações em planilhas, as vezes as informações não batem com o que literalmente temos em estoque nas lojas. Gostaríamos de ter essa informação em D-1 para podermos avaliar a situação do nosso estoque e traçar estratégias de compras com os nossos fornecedores”.

Vamos começar com a modelagem de dados! Baseados nos requisitos do nosso stakeholder, vamos criar uma modelagem em modelo de constelação:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/87fd09bf-248d-4b8c-bfd9-858350f0044d)

As dimensões sub-categoria, categoria, fornecedor, produto e tempo, são dimensões conformadas, pois se relacionam com mais de uma fato. No banco de dados SQL Server, vamos criar dois novos databases, stage e Data Warehouse, também vamos criar um catálogo de dados no SQL Agent, para automatizar as cargas ao final dos processos de ETL. Configurando as origens:
Carga da tabela categoria para stage:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/3d4d5e44-659f-423a-8f0d-9899a8fe4554)

Carga da tabela produto para stage:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/a90e2dd4-0838-4b40-9f69-f4bed71013b7)

Carga da tabela subcategoria para stage:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/6c1e7463-8def-43d9-bda4-fe9c4dc43445)

Carga da tabela fornecedor para stage:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/5af3f9f7-d01b-4b62-ba64-adc69ebe867e)

Carga da tabela lote para stage:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/1db378f2-23a0-43b6-ac25-039d43e67d79)

Carga da tabela loja para stage:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/92229621-4477-461a-98e4-f7ca8826c6d8)

Todas as tabelas usamos os componentes “Origem OLE DB” e “Destino OLE DB”, agora vamos para as tabelas de entrada e saída!
Carga da tabela entrada para stage, para a tabela entrada vamos utilizar o lookup, para fazer os Joins com as outras tabelas, de inicio só temos a FKLOTE e através dela vamos fazer os Joins para obter mais informações, como a quantidade vendida, o custo e o total, informações da tabela item_entrada, vamos começar os Joins por ela!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/9144c0d7-6b67-430d-a525-4aa6948af143)

No Classificar vamos ordenar pelo “id_entrada” e “fkid_entrada” e vamos seguir a mesma lógica para as tabelas produto, fornecedor, categoria e subcategoria! Já temos o id do produto vamos usá-lo para efetuar o Join e trazer os ids do fornecedor e da subcategoria!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/bf404b72-61c4-4c10-aba9-82b3fc81ca8e)

Trazendo as chaves:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/e2bb13d5-23b1-45e4-af82-331c318e050b)

Vamos fazer um Join com a subcategoria e trazer o id da tabela categoria e carregar para stage:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/0c03c940-5a42-40c4-aa75-67b744bd9445)

Obtendo a chave:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/4ba12d21-d3c4-42b5-b3bc-33ea645e7892)

Carregando para Stage:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/f92d252d-bfbb-40cd-961e-7e071dba42e5)

Agora vamos fazer a carga da tabela saída, seguindo a mesma lógica vamos fazer um Join com a tabela item_saida para trazer informações da tabela loja e a data de saída!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/1a994fa6-a9e8-4b68-9499-e11639a66e71)

Vamos fazer o Join na tabela de produtos para obter as chaves da subcategoria e do fornecedor!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/2dd894e2-9400-4711-a1e5-67103efc5824)

Por fim vamos fazer um Join na tabela de subcategoria e trazer a chave da categoria, e carregar para stage!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/4eefd1b9-ee40-4746-a27d-63736ce48ab2)

Carregando para stage:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/4c251e69-1f6e-47bb-a7f8-94961613bf8b)

Não podemos nos esquecer de mapear e criar as tabelas no banco stage para carregar somente as colunas que precisamos! E antes de executarmos as cargas vamos deixar um script SQL para truncar as tabelas! Em todas as cargas!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/9779c2bf-3099-4d57-b619-2d530a96f2e7)

Vamos iniciar as cargas para nosso Data Warehouse, importante lembrar que nossas dimensões produto e fornecedor serão slowly changing dimension! Vamos iniciar com a carga da tabela de subcategoria:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/061ff345-c088-4da4-9dcf-c134f62a9a89)

Não se esqueça de criar a tabela no banco DW e adicionar a surrogate key!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/bbcdaf66-f99e-48fb-bcef-410ab95fa98e)

Não se esqueça do mapeamento, para trazer somente as colunas que precisamos!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/4cb59dfd-202c-4d27-b717-3eaaa3bd3092)

Vamos fazer a carga da loja para o DW:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/c36cc192-43a4-4aea-b003-459ce2744571)

Não se esqueça de criar a tabela e a nossa surrogate key no banco DW! Vamos seguir essa mesma lógica para a tabela lote!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/4317785b-33bc-4ec1-937f-b191361218f3)

E para a tabela categoria!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/5f469fda-509b-469d-aab0-a696c18d8378)

Importante lembrar, sempre do banco stage para o DW! Antes de criarmos nossas SCD, vamos criar a nossa dim_tempo a partir de uma instrução SQL!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/f3a6939c-3942-462d-925d-830303bbbff6)

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/2850747e-ba42-4ae2-9fe5-e9f604d5ef7d)

Vamos criar nossas SCD’s (Slowly Changing Dimension), mas antes vamos deixar uma instrução SQL para truncar as outras dimensões, menos as nossas SCD’s e a dim_tempo! Por fim vamos as Slowly Changing Dimensions, vamos começar pela dimensão produto! Vamos analisar as colunas que precisamos para levar ao DW na nossa modelagem, e buscá-las por meio de um select:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/95488aa1-95e0-4939-8352-7cca24f0dc0b)

Vamos no destino configurar a criação da tabela e adicionar nossa SK e as datas de início e fim!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/8f324602-0410-497b-9bea-3c1c5bc82117)

Com a tabela criada não vamos mapear! Apenas cancelar, pois a tabela já está criada no banco e utilizar nosso componente “Dimensão de Alteração Lenta”, e configurá-la:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/f0303f03-2d79-4034-9cf4-7ba08ed80cf4)

Vamos avançar e definir nossas colunas como atributo histórico: 

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/b120ffe6-6824-4a52-9e6d-a5bc68d1e37b)

E definir a coluna de início da data e do fim da data:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/2279f469-4967-4e82-8838-d41a3012a6b3)

Desabilite esta caixinha:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/26395620-ebfb-443b-9eb1-1d8cdbbc31d4)

E pode finalizar, no fim ele irá criar um fluxo de dados para nossa carga!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/3061b8e7-c7c5-4572-ace0-0f51292092ec)

Vamos seguir a mesma lógica para a tabela de fornecedores! E agora vamos as nossas tabelas fatos! Vamos iniciar com a nossa Fato_entrada, importante lembrar que na Fato_entrada não tem a dimensão loja e na Fato_saida não tem a dimensão lote! Vamos iniciar a carga da fato, carregando apenas até o mês 05 usando uma instrução SQL:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/9a22593c-eaf4-4d7e-b3f7-4d0010fe7ccc)

Utilizando esse script na origem OLE DB. Vamos utilizar o componente “Pesquisa” que é um lookup do SSIS e buscar a SKLOTE no nosso DW:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/328be312-d03b-4e23-966c-839fa22d7826)

Na parte de colunas, vamos ligar as colunas FKLOTE com IDLOTE e trazer a SK:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/14c09bbe-791a-480a-9ff9-b79f868bf6d4)

Não se esqueça de redirecionar as linhas caso não tenha correspondência:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/a1b8281b-d078-4b01-be4e-49719ba8be15)

Vamos puxar um “Unir Tudo” e uma “ColunaDerivada” na coluna derivada vamos tratar o dado que a pesquisa não achar e unir novamente no fluxo principal!
Tratando o dado:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/903b25f6-bc91-4ed0-a33c-aa0060cf289f)

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/45b78da5-22f0-42cd-9228-bd952d066efe)

Vamos repetir este processo para as outras dimensões (menos a loja), e ao final teremos o seguinte fluxo:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/6c2ca950-1f6e-47b8-975b-bb9bd8e1491e)

No final vamos mapear os dados deixando apenas nossa SK’s, e na criação vamos apagar todas as FK’s e deixar somente as SK’s! Ficando assim:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/20da90f3-cd4e-4e5c-a639-d0616f283efb)

Vamos repetir o processo para a Fato_saida, mas lembre-se na Fato_saida não temos a dimensão lote! Ao concluir o processo, vamos programar a carga incremental, na parte de fluxo de dados vamos utilizar um script SQL e criar uma variável!
Criando a Variavel:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/51ab96c9-b313-44f3-8ca9-91eb10a41efd)

No script SQL vamos colocar a seguinte instrução:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/254be164-a181-4537-b307-b4650a6aa387)

Não se esqueça de configurar a conexão e o resultado como linha única pois vamos armazenar essa instrução na nossa variável!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/87e37a6c-ba70-43e5-8ed1-3a9b78113776)

Vamos em conjunto de resultados e selecionar a nossa variável:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/a82b8a2f-2216-4b4d-9154-4c0bd7b68771)

Na origem da Fato_saida no Where do select vamos colocar uma “?” e passar nossa variável como parâmetro:
 
![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/b2bc8871-2a90-453c-9dd2-35f29ace6101)

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/e4e7c07e-0ed4-47a7-9922-2eb4b2cb0de4)

Pronto! Temos a carga incremental da Fato_saida! Vamos a carga incremental da Fato_entrada:
Vamos utilizar as mesmas configurações:


![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/753e25e4-9eb8-4519-83d9-e56c2eaddea2)

Criando a variável:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/a4d7d0f4-e70e-47cd-a152-7e45df96470c)

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/6533e57c-130e-43fe-8cc4-04056c7cb7ae)

Vamos passar nossa variável como parâmetro na nossa consulta de origem!

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/82399a00-180a-4f1f-a6c1-0ceaad86ddad)

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/7f1870b8-c32a-43e1-855f-df67b40ff513)

Agora vamos ao SQL Server automatizar nossos Jobs! Vamos fazer o deploy do nosso projeto, vamos escolher o servidor e programar as cargas no SQL Server!
Selecione o servidor e crie o caminho:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/f6d196ce-31a8-4bd3-ae2a-dcfa3d80088b)

As informações de onde está sendo implantado o projeto e só clicar em deploy:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/a45da101-f5da-4e0a-886b-5ca46e172808)

Pronto! Agora vamos ao SQL Server! 
Automatizando Cargas da Stage:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/4f2718da-ce42-4907-ad41-36e8ada15855)

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/57b124b6-c40a-452b-a353-d58cde24cc71)

Agendando os horários e os dias das cargas:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/3db5bb94-3d37-46d4-aef7-8cdb3166060c)

Automatizando Cargas do Data Warehouse:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/4e26282f-4f8d-4e31-b0a7-b9a7eecee5fc)

Etapas:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/5bfd018c-9be6-4683-9e74-e94dcbbb150a)

Agendando os horários e os dias das cargas:

![image](https://github.com/Miltonmoura011/DW-Estoque/assets/110420164/04e20c98-d87f-4f80-bfb2-460b27482a0e)

Agora temos nosso projeto totalmente automatizado, uma pequena observação no deploy, é interessante implantar o projeto de ETL em um servidor, pois um servidor pode ficar ligado mais horas do que sua máquina local! Obrigado por ler até aqui! Qualquer dúvida sobre o projeto pode me chamar!
https://www.linkedin.com/in/milton-moura/
