# django-heroku
Configuração mínima para hospedar um projeto Django no Heroku

## Criar o diretório do projeto
`mkdir [NOME DA PASTA]`

`cd [NOME DA PASTA]`

## Criar e ativar um ambiente virtual
`python -m venv venv`

`venv/bin/activate`

## Instalando o Django
`pip install django`

## Criar um projeto Django
`django-admin startproject [NOME DO PROJETO]`

## Criar um repositório Git
* git init 
* Crie um arquivo chamado `.gitignore` com o seguinte conteúdo:
```
# Arquivos da IDE (Pycharm)
.idea
# Banco de dados para fins de desenvolvimento
*.sqlite3
# Ambiente virtual
.venv
*pyc
```
`git add .`

`git commit -m 'First commit'`

## Escondendo as configurações
`pip install python-decouple`

* Criar o arquivo **.env** na pasta raiz e inserir as seguintes variáveis:
```
SECRET_KEY=[CHAVE SECRETA - ESTÁ NO ARQUIVO settings.py]
DEBUG=True
```

AVISO: Os parâmetros não deverão conter espaços ou aspas

### Settings.py
`from decouple import config`
`SECRET_KEY = config('SECRET_KEY')`
`DEBUG = config('DEBUG', default=False, cast=bool)`

Sobrescrever os valores anteriores por esses valores

## Configurando o banco de dados
`pip install dj-database-url`

### Settings.py
`from dj_database_url import parse as dburl`

Adicionar linha antes da variável *DATABASES*

`default_dburl = 'sqlite:///' + os.path.join(BASE_DIR, 'db.sqlite3')`

Substituir variável *DATABASES* por:

`DATABASES = {
    'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
}`


## Arquivos estáticos
`pip install dj-static`

### wsgi
`from dj_static import Cling`

`application = Cling(get_wsgi_application())`

### Settings.py
`STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')`

## Crie o arquivo requirements-dev.txt
`pip freeze > requirements-dev.txt`

## Crie o arquivo requirements.txt e referencie o arquivo anterior, além de duas dependências
```
-r requirements-dev.txt
gunicorn
psycopg2
```

## Criar o arquivo *Procfile* na raiz e adicionar a seguinte linha
`web: gunicorn XXX.wsgi --log-file -`

Mudar XXX para o nome do projeto. Ex: website.wsgi

## Criar o arquivo *runtime.txt* na raiz e adicionar a seguinte linha
* python-3.6.0

## Criar o app no Heroku
Você deve instalar o [heroku CLI tools](https://devcenter.heroku.com/articles/heroku-cli) 

`heroku apps:create [NOME DO APP]`

Lembre-se de guardar o endereço do app

## Configurando os hosts permitidos
Incluir o endereço do app na variável *ALLOWED_HOSTS* no arquivo *settings.py* - Apenas o domínio, sem protocolos ou barras

Exemplo:

`ALLOWED_HOSTS = ['app.herokuapp.com']`

## Instalar plugin de configuração do Heroku
`heroku plugins:install heroku-config`

### Enviando configurações do arquivo *.env* para o Heroku (Você deve estar na pasta do arquivo *.env*)
`heroku plugins:install heroku-config`

`heroku config:push -a [NOME DO APP]`

### Verificar as configurações
`heroku config`

## Publicando o app
`git add .`

`git commit -m 'Configuring the app'`

`git push heroku master`

## Criando o banco de dados
`heroku run python3 manage.py migrate`

## Criando o usuário do Django Admin
`heroku run python3 manage.py createsuperuser`

## EXTRAS
### Você pode precisar desativar o collectstatic
`heroku config:set DISABLE_COLLECTSTATIC=1`

### Alterando uma configuração específica
`heroku config:set DEBUG=True`
