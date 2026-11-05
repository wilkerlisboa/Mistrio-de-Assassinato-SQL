# O misterio do Crime em SQL City
Wilker Lisboa Soares da Silva

## Introdução
Houve um **Assaassinato em SQL City**, o detetive me entregou um relatorio da cena do crime, porém acabei perdendo, tenho lembranças que o crime foi um **Assaassinato** que ocorreu em algum momento de **15 de Janeiro de 2018** na cidade.

### Objetivo
Preciso recuperar o relatorio da cena do crime no banco de dados do departamento de polícia para ajudar o detetive encontrar o criminoso.

### Desenvolvimento
No banco de dados da departamento da policia se ultiliza o SQLITE, preciso encontrar as tabelas desse banco de dados, vou ultilizar o seguinte comando.

    > SELECT name FROM sqlite_master WHERE type = 'table';

Resultado:
|   Nome           | 
|----------------|
|crime_scene_report|
|drivers_license|
|facebook_event_checkin|
|interview|
|get_fit_now_member|
|get_fit_now_check_in|
|solution|
|income|
|person|

### Estrutura das tabelas

Após receber o resultado dos nome das tabelas preciso entender como as estruturas delas funciona, realizar isso na ordem mostrada anteriormente, para isso vou realizar o seguinte comando: 

    > SELECT sql FROM sqlite_master WHERE name = 'crime_scene_report';

Esse comando basta mudar o nome da tabela e mostrar a estrutura de cada, a partir daqui mostra apenas as estruturas das tabelas.

- **Tabela:  crime_scene_report**

Resultado:
|   sql          | 
|----------------|
|CREATE TABLE crime_scene_report (date interger, type text, description text, city text)|


- **Tabela: drivers_license**

Resultado:
|   sql          | 
|----------------|
|CREATE TABLE drivers_license ( id integer PRIMARY KEY, age integer, height integer, eye_color text,
|hair_color text, gender text, plate_number text, car_make text, car_model text )|


- **Tabela: facebook_event_checkin**

Resultado:
|   sql          | 
|----------------|
|CREATE TABLE facebook_event_checkin ( person_id integer, event_id integer, event_name text, 
|date integer, FOREIGN KEY (person_id) REFERENCES person(id) )|

- **Tabela: interview**

Resultado:
|   sql          | 
|----------------|
|CREATE TABLE interview ( person_id integer, transcript text, FOREIGN KEY (person_id) REFERENCES person(id) )|

- **tabela: get_fit_now_member**

Resultado:
|   sql          | 
|----------------|
|CREATE TABLE get_fit_now_member ( id text PRIMARY KEY, person_id integer, name text,
membership_start_date integer, membership_status text, FOREIGN KEY (person_id) REFERENCES person(id) )|

- **Tabela: get_fit_now_check_in**

Resultado:
|   sql          | 
|----------------|
|CREATE TABLE get_fit_now_check_in ( membership_id text, check_in_date integer, check_in_time integer,
check_out_time integer, FOREIGN KEY (membership_id) REFERENCES get_fit_now_member(id) )|

- **Tabela: solution**

Resultado:
|   sql          | 
|----------------|
|CREATE TABLE solution ( user integer, value text )|

- **Tabela: income**

Resultado:
|   sql          | 
|----------------|
|CREATE TABLE income (ssn CHAR PRIMARY KEY, annual_income integer)|

- **Tabela: person**

Resultado:
|   sql          | 
|----------------|
|
CREATE TABLE person (id integer PRIMARY KEY, name text, license_id integer, address_number integer,
 address_street_name text, ssn CHAR REFERENCES income (ssn), FOREIGN KEY (license_id) 
 REFERENCES drivers_license (id))|


### Verificando os Dados da Tabelas
Aqui foi o ultilizado o seguinte comando para obter as informações dentro da tabela

    > SELECT *FROM Nome da Tabela;

esse e um exemplo de comando a ser passado no banco de dados do departamento de policia, porém esse comando retorna tudo que esta na tabela, e aqui procuramos algo especifico, como **Assaassinato**. **15 de Janeiro de 2018** e **SQL City**, foi criado esse filtro:

    > SELECT *FROM nome da tabela WHERE Date = Ano/Mes/DIa AND City = "Nome da Cidade";

Então o comando a ser o usado na base de dados do departamento de policia foi:

    > SELECT *FROM crime_scene_report WHERE Date = 20180115 AND city = "SQL City";


Resultado:
|   Date | Type  | Description | City |
|--------|-------|-------------|------|
|20180115|Assault|Hamilton: Lee, do you yield? Burr: You shot him in the side! Yes he yields!| SQL City|
|20180115|Assault|Report Not Found| SQL City|
|20180115|Murder|Security footage shows that there were 2 witnesses. The first witness lives at thelast house on "Northwestern Dr". The second witness, named Annabel, lives somewhere on "Franklin Ave".| SQL City|

A partir desse filtro conseguir obter a seguinte informações:

- **1° Testemunha** mora na ultima casa na "Northwestern Dr"
- **2° Testemunha** se chama Annabel e mora em algum lugar na "Franklin Ave"
    
### Testemunhas
Motivo de ir atras das Testemunha e tentar encontrar mais informações sobre eles como o nome da primeira testemunha, e depois ir atras de documentos ou entrevista de cada uma dela para entender mais sobre o caso.

Ao realizar a pesquisa de um filtra no endereço Franklin Ave ultilizando o seguinte comando:

    > SELECT *FROM person WHERE address_street_name ='Franklin Ave'

Resultado:
| id  | name | license_id | address_number | address_street_name | ssn |
|-----|------|------------|----------------|---------------------|-----|
|12207|Wilmer Wolever|509484|139|Franklin Ave|636825374|
|16371|Annabel Miller|490173|103|Franklin Ave|318771143|
|17683|Johnnie Schee|968887|1277|Franklin Ave|815977821|

A base de dados resultou numa pesquisa de 45 informações de pessoas diferente ao analisar todas foi encontrado a 2° testemunha ate então tinhamos apenas o primeiro nome agora, realizar a buscar na tabela de **facebook_event_checkin** usando o seguinte comando:

    > SELECT *FROM facebook_event_checkin where person_id = 16371

Resultado:
| person_id  | event_id | event_name | Date |
|-----|------|------------|----------------|
|16371|4719|A Turnê Funky Grooves|20180115|