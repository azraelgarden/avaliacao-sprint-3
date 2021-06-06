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

    # Extraindo o texto de cada paragrafo dos artigos, sem as tags html
    paragrafos_artigo = [paragrafo.text for paragrafo in soup.find('div', {'class': 'c-news__body'}).findAll('p')]

    lista_artigos.append({'Paragrafos': paragrafos_artigo})
```

### Salvando os artigos no banco de dados

* Vamos usar o comando insert_one para salvar os artigos na nossa coleção

```py
artigos.insert_one({'Artigos': lista_artigos})
```
* Com isso, podemos recuperar os artigos direto do mongoDB

## Criando um dataset com os artigos da coleção do MongoDB

* Vamos retirar os paragrafos dos artigos direto do mongoDB e armazena-los em uma variavel

* Vamos utilizar o método find() para recuperar os artigos e criar um loop para que os paragrafos sejam transformados em um dataframe

```py
paragrafos_artigos = artigos.find({'Artigos': lista_artigos}) # Retirando os paragrafos direto do mongo
for paragrafo in paragrafos_artigos:
    # Os paragrafos são transformados em dataframe
    df_paragrafos = pd.DataFrame(paragrafo['Artigos'])
```

## Retirando as stopwords

* As stopwords são palavras que não interferem no sentido do texto, então vamos criar uma função para retira-las

* Primeiro, vamos criar uma lista que iremos armazenar nossos artigos processados (sem as stopwords)

```py
artigos_processados = []
```

* Vamos importar a bibliotéca spacy, caso não tiver ela instalado, pode utilizar o comando:
```
pip install spacy
```

* Agora vamos criar a função retira_stop_words

```py
def retira_stop_words(artigos):
    palavras_tokens = []

    # Transforma cada palavra do artigo em token
    nlp = spacy.load("pt_core_news_sm") # nlp em português
    doc = nlp(str(artigos))

    for palavra_token in doc:
        palavra = not palavra_token.is_stop and palavra_token.is_alpha

        if palavra:
            # Adiciona na lista e deixa a palavra em minusculo
            palavras_tokens.append(palavra_token.text.lower())
    
    return ' '.join(palavras_tokens)
```

* Agora temos que percorrer nossa coluna de paragrafos do dataframe e executar a função, armazenando cada resultado na lista de artigos processados

```py
for paragrafos in df_paragrafos.Paragrafos:
    artigos_processados.append(retira_stop_words(paragrafos))
```

* Para vizualizarmos o resultado, vamos criar uma nova coluna chamada de Paragrafos_processados e colocar os textos sem as stopwords

```py
df_paragrafos["Paragrafos_processados"] = artigos_processados
df_paragrafos.head()
```

## Obtendo os verbos e as entidades dos artigos

* Em primeiro lugar, vamos criar uma lista para verbos e outra para entidades

```py
lista_verbos = []
lista_entidades = []
```

### Verbos

* Para obter os verbos, vamos utilizar o método Navigating the parse tree, do spacy

* Vamos importar os verbos do spacy

```py
from spacy.symbols import VERB
```

* A nossa variaveldoc, agora será a nossa lista_artigos

* Após isso, vamos criar um laço de repetição, onde para cada palavra no doc, iremos checar se é um verbo. Se sim, ele irá checar se ja existe esse verbo na lista, se não ele irá adiciona-lo na nossa lista de verbos

* Depois disso, vamos criar uma coleção no mongo chamada verbos, e adicionar nossa lista de verbos no mongoDB

Coleção verbos
```py
verbos_artigos = artigos_db["Coleção_verbos"]
```
Procurando os verbos e adicionando na lista
```py
# Listando os verbos
lista_verbos = []
nlp = spacy.load("pt_core_news_sm") # nlp em português
doc = nlp(str(lista_artigos))

for palavra in doc:
    if palavra.head.pos == VERB:
        if palavra.head.orth_ not in lista_verbos:
            # Transforma em string e adiciona na lista de verbos
            lista_verbos.append(str(palavra.head))

lista_verbos
verbos_artigos.insert_one({'Verbos': lista_verbos})
```

### Entidades

* O spacy ja consegue procurar as entidades com o comando doc.ents. Porém, o spacy vai reconhecer simbolos como '[', '\' e etc.

* Para adicionar apenas as entidades na nossa lista, precisamos de um laço de repetição onde:
    * Checa se tem a entidade na lista
    * Se o tamanho da entidade for maior qu 2 caracteres, adicionar na lista

```py
# Listando as entidades
lista_entidades = []
doc = nlp(str(lista_artigos))

for palavra in doc.ents:
    if palavra.orth_ not in lista_entidades:
        if len(palavra) > 2:
            lista_entidades.append(palavra.orth_)

```

* Depois disso, vamos repetir o procedimento de criar uma coleção para entidade e adicionar no mongoDB

```py
verbos_entidades = artigos_db["Coleção_entidades"]
```

## Núvem de palavras

* Para criar a núvem de palavras, vamos importar a biblioteca wordcloud e matplotlib

```py
from wordcloud import WordCloud
import matplotlib.pyplot as plt
```

* Vamos criar uma função que recebe os artigos como parametro para gerar a núvem de palavras

* Nessa função, vamos definir uma váriavel para ser nossa núvem de palavras, que irá ter o tamanho da imagem e o tamanho da fonte

* Nessa função, também vamos definir o tamanho da figura a ser plotada e que também apenas aparece a núvem, sem o plano cartesiano

```py
def gerar_nuvem_palavras(artigos):
    todas_palavras = ''.join([text for text in artigos])
    nuvem_palavras = WordCloud(width=800, 
                            height=500,
                            max_font_size=110,
                            collocations=False).generate(todas_palavras)
                            
    plt.figure(figsize=(10,7)) # Tamanho da figura
    plt.imshow(nuvem_palavras)
    plt.axis("off") # Plota apenas a imagem, fora do plano cartesiano
    plt.show()
```

* Em seguida, vamos criar um laço de repetição que irá de 0 a 10 (temos 10 artigos), para que em cada artigo sem as stopwords, seja plotado um gráfico. Ou seja, teremos 10 gráficos

```py
for i in range(0, 10):
    paragrafo_artigo = str(df_paragrafos.Paragrafos_processados[i])
    gerar_nuvem_palavras(paragrafo_artigo)
```




