# Avaliação sprint 3

## Proposta:

    - Fazer analize de sentimento com spacy: OK
    - Web scrap de artigos jornalisticos: OK
     - WordCloud: OK
      - Armazenar o texto no mongodb: OK
      - Verbos do artigo: OK
      - NER: OK
      - Análise de sentimento das frases: OK
      - Armazenar a analize no mongodb: OK
 
## Entrega 
    
   - Com os links dos artigos colocados em um .txt, eu os percorri gravando seu titulo e conteudo no bando de dados,com uma função eu percorri os textos dos artigos
tirando as stopwords assim podendo plotar a cloud com mais foco nas palavras mais frequentes de cada texto.
   - Toda a parte de scraping dos dados + identificação e exibição das NER e dos verbos + wordcloud foram feitas com um FOR percorrendo o arquivo .txt com todos os links
   - A partir do csv disponibilizado, extrai duas colunas e as tratei da mesma forma que o texto dos artigos. A partir do tratamento, pude fazer o treinamento e o teste do modelo com acurácia de 0.82%.
   - A parte de análise de sentimento das frases foram feitas com um FOR percorrendo o arquivo.txt 
   - Gravei os dados de cada teste no banco no fim do código.

