# Avaliação Sprint 3

## Requisitos
- Pymongo
- beautifulSoup
- numpy
- sklearn
- spacy
- pandas
- wordCloud
- 
## Conexão MongoDB
- A partir da lib Pymongo, criamos uma conexao que sera responsável por criar o banco de dados e a coleção onde será guardado os dados de teste e resultados
```py
   from pymongo import MongoClient
```
```py
   client = MongoClient('')#colocar string de conexao desejada
   database = client['avaliacao3']
   colecao = database['artigos']
```

## Scraping
- com a tecnica de scraping, foi capaz de coletar dados da web para serem usados como dados de teste do nosso modelo de treino, salvando no banco logo após
```py
   def scraping(url):
     #raspando conteúdo
     response = urlopen(url)
     html = response.read().decode('utf8')

     #instanciando BeatifulSoup
     soup = BeautifulSoup(html, 'html.parser')

     #buscando os dados no html
     titulo = tratamento_inicial(soup.title.string)
     paragrafos_raw = tratamento_inicial(soup.find('div', {'class': 'c-news__body'}).getText())

     return {'titulo': titulo, 'paragrafos': paragrafos_raw}
```
```py
urls = open(<arquivo com urls>, 'r')

for url in urls:
  artigo = scraping(url)
  if not colecao.find_one(artigo):
    colecao.insert_one(artigo)
```

## Pandas
- Com a lib Pandas, foi capaz de criar dataframes para melhor visualização dos dados coletados

```py
   dataframe = pd.DataFrame(lista_paragrafos)
   dataframe = dataframe.rename(columns={0: 'conteudo_artigo_raw'})
```

![dataframe](https://user-images.githubusercontent.com/66642358/121042503-224fc700-c78a-11eb-9f05-0f77cd025a33.png)

## Spacy
- A lib spacy foi responsável por tratar os dados e tokenizar-los para futuramento serem usados para as wordclouds e tambem para os testes

```py
   lista = [stop_words(doc) for doc in nlp.pipe(lista_paragrafos, batch_size = 1000, n_process = -1)]
```
- foi possível também mostrar as entidades e verbos de cada artigo

```py
   for paragrafos in dataframe.tratado:
     doc = nlp(paragrafos)
     entidades = [ent.sent for ent in doc.ents if ent.label_ == 'PER']
     spacy.displacy.render(entidades, style='ent', jupyter=True)
```
```py
   verbos = []
   for paragrafo in dataframe.tratado:
     doc = nlp(str(paragrafo))
     verbo = [token.text for token in doc if token.pos_ == 'VERB']
     print(verbo)
     verbos.append(verbo)
```

## WordClouds
- Logo após os tratamentos, foi criado uma wordcloud para cada artigo coletado da web

```py
   def word_cloud(text):
       stopwords = set(STOPWORDS)
       wc = WordCloud(width=800, height=500, background_color="black", max_words=2000,stopwords=stopwords, contour_width=3, contour_color='green').generate(text)

       plt.figure(figsize=(15, 10))
       plt.imshow(wc, interpolation='bilinear')
       plt.axis("off")
       plt.show()
```

```py
   for paragrafos in dataframe.tratado:
      word_cloud(paragrafos)
```

## Tratando os dados de treino

- Será usado como dados de teste um dataset com tweets do twitter

```py
   tweets = pd.read_csv('/content/drive/MyDrive/datasets(csv)/Tweets_Mg.csv')
   df_tweets = tweets[['Text', 'Classificacao']]
   df_tweets.head()
   tweets_lower = (titulo.lower() for titulo in df_tweets['Text'])
   tweets_tratados = [stop_words(doc) for doc in nlp.pipe(tweets_lower, batch_size= 1000, n_process=-1)]
   df_tweets.insert(1, 'tratado', tweets_tratados)
```
- Mostrando os 5 primeiros tweets
![image](https://user-images.githubusercontent.com/66642358/121043754-4b248c00-c78b-11eb-87c8-5024372199b6.png)

- Wordcloud
![image](https://user-images.githubusercontent.com/66642358/121043823-5bd50200-c78b-11eb-864f-7682596daf08.png)

### Treinando um modelo com Sklearn

- A partir dos dados coletados de teste e treino, foi possivel treinar um modelo para nos dizer se o artigo é 'POSITIVO', 'NEGATIVOU' ou 'NEUTRO'

```py
   tweets_train = df_tweets['tratado'].values
   classificacao = df_tweets['Classificacao'].values

   from sklearn.feature_extraction.text import CountVectorizer
   vectorizer = CountVectorizer(analyzer= 'word')

   freq_tweets = vectorizer.fit_transform(tweets_train)
   
   np.random.seed(42)# setando uma seed estática

   model = MultinomialNB()
   model.fit(freq_tweets, classificacao)
   
   results = cross_val_predict(model, freq_tweets, classificacao, cv=10)
   print(len(results))# mostrando a predição
   
   accuracy = metrics.accuracy_score(classificacao, results) 
   print(f'{accuracy * 100}%')# acurácia do nosso modelo
   
   sentiments = ['Positivo', 'Negativo', 'Neutro']
   print(metrics.classification_report(classificacao, results, sentiments))# Plotando as medidas do modelo
```
![image](https://user-images.githubusercontent.com/66642358/121044234-bd956c00-c78b-11eb-866c-04d4bfe5930b.png)
