# Heroku Deploy

Instruções de como fazer deploy do seu projeto Java/Spring no Heroku.

Com seu projeto finalizado:

1. Criar script SQL para rodar no PostgreSQL e preparação dos arquivos: application.properties
1. system.properties
3. Verificar se o Heroku Cli está instalado em seu computador
4. Fazer o deploy no Heroku.com



## Passos

### 1. Criar script SQL para rodar no PostgreSQL e preparação dos arquivos: application.properties

Crie 3 perfis de projeto:

```
application.properties
application-test.properties
application-dev.properties
application-prod.properties

```

#### application.properties

```
spring.profiles.active=${APP_PROFILE:test}
spring.jpa.open-in-view=false
```

#### application-test.properties (geralmente utiliza-se o H2)

```
# Dados de conexão com o banco H2
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.username=sa
spring.datasource.password=

# Configuração do cliente web do banco H2
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# Configuração para mostrar o SQL no console
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

#### application-dev.properties (PostgreSQL instalado local/sua máquina)

```
# Somente para criar o create.sql
#spring.jpa.properties.javax.persistence.schema-generation.create-source=metadata
#spring.jpa.properties.javax.persistence.schema-generation.scripts.action=create
#spring.jpa.properties.javax.persistence.schema-generation.scripts.create-target=create.sql
#spring.jpa.properties.hibernate.hbm2ddl.delimiter=;
# fim

spring.datasource.url=jdbc:postgresql://localhost:5432/dsmovie
spring.datasource.username=postgres
spring.datasource.password=root

spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
spring.jpa.hibernate.ddl-auto=none
```

Neste contém um conjunto de instruções comentadas, descomente, salve e rode sua aplicação. <br>
Irá criar um arquivo create.sql na raíz do seu projeto contendo todo o script SQL do seu banco.<br>
Utilize-o para criar seu banco no PostgreSQL local ou no Heroku.

Atenção: Deixar as 4 primeiras linhas comentadas, somente descomente para criar o Script SQL.


#### application-prod.properties

Esta configuração da variavel ${DATABASE_URL} será utilizada no heroku.

```
spring.datasource.url=${DATABASE_URL}
```

### 2. system.properties

Crie o arquivo system.properties na raíz do seu projeto. É obrigatório para identificar o java utilizado.

```
java.runtime.version=17
```

### 3. Verificar se o Heroku Cli está instalado em seu computador

Instale o Heroki Cli em seu computador.

Verificar a versão do Heroku Cli

```
heroku -v
```
  
### 4. Fazer o deploy no Heroku

Roteiro:
- Criar app no Heroku
- Provisionar banco Postgres
- Definir variável APP_PROFILE=prod
- Conectar ao banco via pgAdmin
- Criar seed do banco

#### Criar app no heroku

Acesse o link ``` https://heroku.com/ ```

Clique no botão NEW e escolha a opção  ``` Create new app ```

![Image](https://# "Novo projeto")

Informe um nome único, pode deixar a região  ``` United States ```  e clique em ``` Create App ``` 

Seu App no heroku será criado (Obs: Heroku permite criar 5 apps free, mais será necessário comprar um plano.)

#### Provisionar banco Postgres

Agora, agora com seu App criado, clique em ``` Resources ```  para provisionar um banco de dados PostgreSQL.

![Image](https://# "Resources")

Em Add-ons: pesquise por PostgreSQL e selecione o ``` Heroku Postgres ```. Será apresentado para selecionar o plano. Escolha o pano Free.

![Image](https://# "Resources")

Pronto. Neste momento seu banco de dados PostgreSQL no heroku foi criado/provisionado

#### Definir variável APP_PROFILE=prod

Será necessário realizar uma configuração no seu banco.

Clique na opção  ``` Settings ```.

Na sessão ``` Config Vars ``` clique em  ``` Reveal Config Vars ``` para mostrar as variáveis de ambiente.

Neste momento contém somente a variável   ``` DATABASE_URL ```
Adicione mais uma variável ``` APP_PROFILE ``` com valor ``` prod ```

Desta forma quando for fazer o deploy do seu projeto, o Heroku saberá qual ambiente deverá pegar as configurações do seu projeto.


#### Conectar ao banco via pgAdmin

No passo anterior, onde contém as variáveis de ambiente do Heroku, na variável ``` DATABASE_URL ``` contém um link de conexão ao banco postgres no Heroku.

Você irá utilizar este link para conectar seu pgAdmin.

Link composição: ``` postgres://nome_usuario:senha_usuario@host:porta/nome_banco ```

Acesse seu pgAdmin localmente.
- Clique botão direito em Servers > Server.
- Na aba GENERAL, preencha o campo NAME com nome do seu banco.
- Na aba CONNECTION, preencha os campos HOST, PORT, MAINTENANCE DATABASE(Nome do seu banco), USERNAME e PASSWORD
- Na aba ADVANCED, preencha o campo DB RESTRICTION (Nome do seu banco)
- Salve.

#### Criar seed do banco

Com a configuração do PostgreSQL Heroku configurado localmente, execute o script SQL para criar toda a estrutura de tabelas do seu projeto.