---
layout:     post
title:      "Write while code - API"
subtitle:   "Organizando dotfiles."
date:       2017-04-08 11:27:02
author:     "Quatroka"
header-img: "https://i.imgur.com/koMcnli.jpg"
comments : true
categories: ["write-while-code", "API"]
---

> 08/04/2017 12:34

Recentemente me candidatei a uma vaga Estágio em Desenvolvimento
 na empresa **Dress & Go**, e entre uma das tasks era desenvolver
 um web service que fizesse a busca pelo nome do local na Google
 Places API e armazenasse os resultados.

## Preparando o ambiente

Vamos começar montando o ambiente virtual e instalando os
 pacotes necessários:

1.Primeiro virtualenv de python 3:

    mkvirtualenv -a ~/workspace/dress-go-challenge/plus-API -p python plus-API

2.Em uma pesquisa rápida na documentação da Google, encontro
 uma versão da API em python:

    pip install -U googlemaps

3.Usarei o micro-framework flask para criarmos a nossa API do
 sistema:

    pip install -U flask

4.E para salvar todas as versões necessárias dos pacotes que
 fazem parte desse projeto, vamos guarda-los em um requirements.txt:
 
    pip freeze > requirements.txt

> Obs.: Para que a API do google funcione é necessário uma
 KEY, que pode ser adquirida no Google API Console. Nesse
 projeto colocarei todas a referencias a esta KEY no arquivo
 _key.py_ com o conteudo: KEY='_MY_KEY_'.

## Entendendo com funciona a API

Tudo pronto. Vamos primeiro fazer alguns teste com a API de
 python para ver como ela funciona, através do exemplos que
 eles fornecem na documentação.

test1.py
```python
import googlemaps
from key import KEY

gmaps = googlemaps.Client(key=KEY)

# Place result
place_result = gmaps.places('Dress & GO')
print(place_result)

# Retorna isso
{'html_attributions': [], 'results': [{'formatted_address': 'R. Santa Justina, 352 - Vila Olimpia, São Paulo - SP, 04545-041, Brazil', 'geometry': {'location': {'lat': -23.5949847, 'lng': -46.6764695}, 'viewport': {'northeast': {'lat': -23.5937742197085, 'lng': -46.67508536970849}, 'southwest': {'lat': -23.5964721802915, 'lng': -46.67778333029149}}}, 'icon': 'https://maps.gstatic.com/mapfiles/place_api/icons/generic_business-71.png', 'id': 'e89f32a7476e28fc4481caf9a2c116e896645eb8', 'name': 'Dress & Go', 'opening_hours': {'open_now': True, 'weekday_text': []}, 'photos': [{'height': 600, 'html_attributions': ['<a href="https://maps.google.com/maps/contrib/115318623214896388034/photos">Dress &amp; Go</a>'], 'photo_reference': 'CoQBdwAAANaiGGbvm5Oq2WnTB_ZhKruzy5cPR_K6D755XnS19hTXEPdn8D-PgdcJSg93aR2TCh5AooeX14P0HANtiyupClor34TXUVrelQmxw2_2tn1OZ0WWvZIV8xvcP5ZQSuBJW619tEGpcbbMV3C_CexXL1taBFHfVuR9F2lTf6cllQ2dEhDZkrqvFhfhrpPuvsa5VF8SGhScVTX89aiZiVwpEykxfMmGZG35cA', 'width': 1440}], 'place_id': 'ChIJNZZ3QVFXzpQRzj3sZ9iA8gg', 'rating': 3.5, 'reference': 'CmRRAAAAAuyPKiME0oscpcMWzNJI-wMy2jSDx8Z_9qLoobjQZpqF4FyHqJFjcHoJod9-MfLZznKD4ZyZ9OSNs7EGq-bJ0kJeuCqGuMHH1sUv0NYTHSofKGYW_-TGLUZYFQ7d4JHjEhC5jIrg-WA481O0w3Z_Zm7dGhR0-VBIX5USZqQwbIRrrV5nXpASaQ', 'types': ['point_of_interest', 'establishment']}], 'status': 'OK'}
```

Já dá para ter uma noção de como os dados chegarão. Para organizar melhor as coisas vamos fazer um pequeno roadmap cobrindo tudo:

1. Model

    1.1 Create table

    1.2 Save a place

    1.3 Retrive one place

    1.4 Retrive all places

    1.5 Delete a place

2. Controllers

    2.1 Format one place to save

3. Views

    3.1 View all places

    3.2 View one place

## Os primeiros métodos

> 08/04/2017 16:22:07

Vamos começar criando o teste e a função para pegar um dado de um lugar:

tests/test_places.py
```python
# -*- coding: utf-8 -*-
""" Test file for Google Places API. """
from unittest import TestCase, main
from places import get_place_data


class TestGoogleMaps(TestCase):
    """ Test class for Google Places API. """


    def test_get_place_data(self):
        """ Test for get_place_data_method. """
        data = get_place_data('Dress & Go')
        self.assertEqual('Dress & Go', data['results'][0]['name'])
        self.assertEqual('OK', data['status'])
        self.assertEqual(-46.6764695, data['results'][0]['geometry']['location']['lng'])
        self.assertEqual(-23.5949847, data['results'][0]['geometry']['location']['lat'])


if __name__ == '__main__':
    main()
```
places.py
```python
# -*- coding: utf-8 -*-
""" File to functions of Google Places API. """
import googlemaps
from key import KEY


def get_place_data(place):
    """ Get Google Place data of a place paramter.

        Recive place paramter(string), create a googlemaps
        client, request your data, and returns your response. """
    gmaps = googlemaps.Client(key=KEY)
    place_result = gmaps.places(place)
    return place_result

```

Agora que temos nos "buscador", precisamos criar um lugar
 para armazena-lo. Vamos usar o sqlite. Vamos criar um setup
 file para a criação dele.

setup_db.py
```python
# -*- coding: utf-8 -*-
""" Setup file to create sqlite db. """
import sqlite3

CONN = sqlite3.connect('places.db')
CURSOR = CONN.cursor()

CURSOR.execute('CREATE TABLE places( id INTEGER PRIMARY KEY AUTOINCREMENT, \
                                     local_name CHAR(100) NOT NULL, \
                                     full_address  CHAR(500) NOT NULL,\
                                     latitude REAL NOT NULL, \
                                     longitude REAL NOT NULL, \
                                     place_id CHAR(100) NOT NULL )')

CONN.close()
```

Temos os dados e temos o local para armazenar os dados, porém
 não temos os dados formatados e muito menos um método para
 armazena-los. Vamos começar pelos dados:

test/test_controllers.py
```python
# -*- coding: utf-8 -*-
""" Test file for controllers. """
from unittest import TestCase, main
from places import get_place_data
from controllers import filter_essential_data


class TestControllers(TestCase):
    """ Test class for controllers. """


    def test_filter_essential_data(self):
        """ Test for filter_essential_data method. """
        data = get_place_data('Dress & Go')
        expect = {
            'local_name': 'Dress & Go',
            'full_address': 'R. Santa Justina, 352 - Vila Olimpia, ' \
            'São Paulo - SP, 04545-041, Brazil',
            'latitude': -23.5949847,
            'longitude': -46.6764695,
            'place_id': 'ChIJNZZ3QVFXzpQRzj3sZ9iA8gg'
            }
        self.assertEqual(expect, filter_essential_data(data))


if __name__ == '__main__':
    main()
```

controllers.py
```python
# -*- coding: utf-8 -*-
""" Controller file for all remaining methods. """


def filter_essential_data(place_data):
    """ Recieve a data and returns your essential data. """
    essential_data = {}
    essential_data['local_name'] = place_data['results'][0]['name']
    essential_data['full_address'] = place_data['results'][0]['formatted_address']
    essential_data['latitude'] = place_data['results'][0]['geometry']['location']['lat']
    essential_data['longitude'] = place_data['results'][0]['geometry']['location']['lng']
    essential_data['place_id'] = place_data['results'][0]['place_id']
    return essential_data

```

Agora o método para salvar os dados no database:

models.py
```python
# -*- coding: utf-8 -*-
""" Models file for all remaining methods. """
import sqlite3

def save_place(place):
    """ Recieve a place and insert on database. """
    conn = sqlite3.connect('places.db')
    cursor = conn.cursor()

    sql = 'INSERT INTO places(local_name, full_address, latitude, longitude, place_id)' \
          'VALUES(?, ?, ?, ?, ?)'

    cursor.execute(sql, (place['local_name'],
                         place['full_address'],
                         place['latitude'],
                         place['longitude'],
                         place['place_id']
                        )
                  )
    conn.commit()
    conn.close()
```

> 08/04/2017 19:02:23

## Começando com Flask

Já temos todos os métodos pronto para criamos a nossa 
 primeira url na API, para isso usaremos o Flask. Importaremos
 tudo o que produzimos e utilizaremos em um unico método
 chamado _create_place_:

run.py
```python
from flask import Flask, jsonify, request
from places import get_place_data
from controllers import filter_essential_data
from models import save_place

APP = Flask(__name__)

@APP.route('/api/places', methods=['POST'])
def create_place():
    """ Insert a place on database.

        Receive a json with place value, get Googla Places
        data of value, filter the necessary information of
        that and insert on database. Return the data inserted. """
    if request.method == 'POST':
        place = request.json['place']
        place = get_place_data(place)
        place = filter_essential_data(place)
        save_place(place)
        return jsonify(place)


if __name__ == '__main__':  
    APP.run(debug=True, port=5000)

```

> Para ficar melhor o vizualização das urls que iremos produzir,
> criei um arquivo que contém toda a estrutura das urls da API.

Agora temos que iniciar o servidor do Flask:

    python run.py

Para conferir se está funcionando, podemos executar o comando curl desta maneira:

    curl -H "Content-Type: application/json" -X POST -d '{ "place": "Google BR" }' http://localhost:5000/api/places
 
> Obs.: Atenção ao cabeçalho da request: é um arquivo json!
> Uma outra alternativa é usar o app Postman, ele agiliza bastante este processo.

Um dos requisitos a ser feito é a criação de um arquivo de log.
 Para fazer isso é bem prático, apenas algumas linhas a mais
 antes da inicialização do flask, para que ele saiba que tem
 que armazenar os logs:

run.py
```python
if __name__ == '__main__':
    logger = logging.getLogger('werkzeug') # Pega o log criado pelo Werkzurg
    handler = logging.FileHandler('access.log') # Diz onde salva-lo
    logger.addHandler(handler) # Adiciona os logs no arquivo
    APP.logger.addHandler(handler) # Acrescenta os logs no app
    APP.run(debug=True, port=5000)
```

--- 

## Organizando as coisas

Antes de avançar está na hora de colocar os arquivos nos
 lugares certos, atualizar suas importações, e arrumar alguns
 erros na documentação, porque acumular problemas só gera
 uma coisa: mais problemas!  A nova estrutura do projeto:

    plus-API/
        controllers/
            files...
        docs/
            files...
        models/
            files...
        tests/
            tests...
        views/
            tests...
        run.py
        more some files...

Uma coisa que se deve ter um pouco de atenção é em relação ao
 a nova modularição que temos que fazer para ter esta estrutura:
 os Blueprints! Eles são uma espécie de métodos auxiliares para
 podermos dizer que alguns arquivos e suas rotas funcionem como
 módulos. Vamos às modificações:

views/views.py
```python
""" Blueprint for views of app """
from flask import jsonify, request, Blueprint
from controllers.places import get_place_data
from controllers.controllers import filter_essential_data
from models.models import save_place

PLACES = Blueprint('places', __name__) # Aqui dizemos que o arquivo é um módulo

@PLACES.route('/api/places', methods=['POST'])
def create_place():
    """ Insert a place on database.

        Receive a json with place value, get Googla Places
        data of value, filter the necessary information of
        that and insert on database."""
    if request.method == 'POST':
        place = request.json['place']
        place = get_place_data(place)
        place = filter_essential_data(place)
        save_place(place)
        return jsonify(place)
```

run.py
```python
""" Main file to start the project """
import logging
from flask import Flask, request, Blueprint
from views.views import PLACES

APP = Flask(__name__)

# Blueprints
APP.register_blueprint(PLACES) # Registramos o módulo na aplicação.

if __name__ == '__main__':
    logger = logging.getLogger('werkzeug')
    handler = logging.FileHandler('access.log')
    logger.addHandler(handler)
    APP.logger.addHandler(handler)
    APP.run(debug=True, port=5000)
```

> 09/04/2017 14:07:02

## Criando os primeiros GETs

Agora que temos dados em nossa aplicação temos que ter uma
 forma de regata-las. Nessa API teremos dois GETs: Um que
 retorna todos os places do banco e outra que retorna somente
 os dados de um place solicitado. Vamos ao mais simples, buscar
 todos os dados juntos. Para isso basta acrescentar uma view
 e um novo método para resgatar todos estes dados. E em seguida
 o GET de um único place, que é uma variação do outro, porém
 com um parâmetro na requisição:

views/views.py
```python
@PLACES.route('/api/places', methods=['GET'])
def places_get_all():
    """ Receive all places in database. """
    if request.method == 'GET':
        return jsonify(get_all_places())


@PLACES.route('/api/places/<string:place>', methods=['GET'])
def places_get_one(place):
    """ Receive one place of database.

        Receive a place paramter in url and get
        your data in database. """
    place = place.replace('-', ' ')
    if request.method == 'GET':
        return jsonify(get_one_place(place))
```

models/models.py
```python
def get_all_places():
    """ Retrieve a list of all places on database. """
    places = {'places': []}

    conn = sqlite3.connect('places.db')
    cursor = conn.cursor()

    sql = 'SELECT local_name, full_address, latitude, longitude, place_id FROM places'

    for place in cursor.execute(sql):
        place_modal = {}
        place_modal['local_name'] = place[0]
        place_modal['full_address'] = place[1]
        place_modal['latitude'] = place[2]
        place_modal['longitude'] = place[3]
        place_modal['place_id'] = place[4]
        places['places'].append(place_modal)

    conn.close()
    return places


def get_one_place(place):
    """ Retrieve a list of all places on database. """
    places = {'places': []}

    conn = sqlite3.connect('places.db')
    cursor = conn.cursor()

    sql = 'SELECT local_name, full_address, latitude, longitude, place_id ' \
          'FROM places WHERE local_name LIKE ?'
    param = '%' + place + '%'

    for place in cursor.execute(sql, (param,)):
        place_modal = {}
        place_modal['local_name'] = place[0]
        place_modal['full_address'] = place[1]
        place_modal['latitude'] = place[2]
        place_modal['longitude'] = place[3]
        place_modal['place_id'] = place[4]
        places['places'].append(place_modal)

    conn.close()
    return places['places'][0]
```

## E por último o DELETE

O delete fica fácil uma vez que ele é igual ao __get_one_place__
 alterando somente a query executada no método escrito no
 model. Vamos lá terminar isso:

views/views.py
```python
@PLACES.route('/api/places/<string:place>', methods=['DELETE'])
def places_delete_one(place):
    """ Delete one place on database.

        Receive a place paramter in url and delete
        your repective place in database. """
    place = place.replace('-', ' ')
    if request.method == 'DELETE':
        delete_one_place(place)
        return jsonify({'Deleted': place})
```

models/models.py
```python
def delete_one_place(place):
    """ Delete one place on database. """
    conn = sqlite3.connect('places.db')
    cursor = conn.cursor()

    param = '%' + place + '%'
    sql = 'DELETE FROM places WHERE local_name LIKE ?'
    cursor.execute(sql, (param,))
    conn.commit()

    conn.close()
```

## Quando você acha que terminou...

Estava revisando o projeto e percebi que tem algumas falhas
 e estas falhas ainda é possivel arrumar. A primeira delas 
 são os testes, está faltando alguns testes(para ser mais 
 direto eu não encontrei uma formar de testar algumas partes),
 a segunda falha são os _status code_, eles são parte
 fundamental da API e não os fiz, e fazendo-os, terei uma
 maneira de criar os testes que preciso. E por último e não
 menos importante: As páginas de erro 404 e mais uma vez os
 status codes estão aparecendo como necessários no sistema.
 _Here we go!_. Como as alterações foram muitas porém básicas
 deixo os códigos restantes para vizualização no próprio
 repositório do projeto.

:)

---
Links:

>[Dress Go Challenge-Plus API](https://github.com/quatroka/dress-go-challenge/tree/master/plus-API)
>
>[Desafio](https://github.com/quatroka/dress-go-challenge/blob/master/plus-API/Desafio_API.pdf)
>
>[Google Places API](https://developers.google.com/places/)
>
>[Google API Console](https://console.developers.google.com/)
>
>[Google Maps API Python Documentation](https://googlemaps.github.io/google-maps-services-python/docs/2.4.6/) 
>
>[Flask](http://flask.pocoo.org/)
>
>[Roadmap](https://github.com/quatroka/dress-go-challenge/blob/master/plus-API/docs/roadmap.md)
>
>[Urls Structure](https://github.com/quatroka/dress-go-challenge/blob/master/plus-API/docs/urls_structure.md)
>
>[Postman](https://www.getpostman.com/)
>
>[Status code](https://http.cat/)
