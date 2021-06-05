# Avaliação Sprint 3

## Pré requisitos

* Python3
* pip
* Mongo DB
* BeautifulSoup
* Sua IDE/Editor de texto favorito

## Criando uma conexão com o MongoDB

* Criamos uma conexão com o mongoDB, para armazenar os artigos retirados das urls

* Devemos importar o MongoClient do pymongo

```py
from pymongo import MongoClient
```

* Vamos fazer a conexão com o banco de dados
```py
client = MongoClient("localhost", 27017)
artigos_db = client["Artigos"]
artigos = artigos_db["Coleção_artigos"]
```

## Pegando os paragrafos dos artigos da url

* Vamos criar uma lista com todas as urls que iremos utilizar

```py
urls = ['<<URLS AQUI>>']
```

* Após isso, vamos criar uma lista vazia para incluir os artigos dentro dela

```py
lista_artigos = []
```

### Scraping dos artigos

* Temos que percorrer a lista de urls, para que em cada url, seja retirado os pargrafos de cada artigo e as tags html

* Após isso, terá de incluir cada artigo na lista de artigos
```py
lista_artigos = []
for url in urls:
    response = urlopen(url)
    html = response.read().decode('utf8')

    soup = BeautifulSoup(html, 'html.parser')

    paragrafos_artigo = soup.find('div', {'class': 'c-news__body'}).findAll('p')
    # Retirando as tags html dos artigos
    # As tags html, são substituidas por uma string vazia
    paragrafos_artigo = [str(paragrafo).replace('<p>','') for paragrafo in paragrafos_artigo]
    paragrafos_artigo = [str(paragrafo).replace('</p>','') for paragrafo in paragrafos_artigo]

    lista_artigos.append({'Paragrafos': paragrafos_artigo})
```

### Salvando os artigos no banco

* Vamos usar o comando insert_one para salvar os artigos na nossa coleção

```py
artigos.insert_one({'Artigos': lista_artigos})
```
* Com isso, podemos recuperar os artigos direto do mongoDB

