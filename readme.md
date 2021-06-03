# Avaliação Sprint 3

## Execução
- Com base no trabalho: https://www.kaggle.com/leandrodoze/sentiment-analysis-in-portuguese e seu material https://github.com/minerandodados/mdrepo/raw/master/script-analise-sentimentos-live.zip usar o SPACY para fazer a análise de sentimento
- Extrair o texto de 10 artigos jornalísticos - dica: https://www1.folha.uol.com.br/especial/2017/ha-50-anos/
- A partir de cada texto extraído:
    * armazenar o texto no mongodb
    * criar uma nuvem de palavras, onde quanto maior o tamanho da imagem da palavra, maior sua frequência no texto (tirar as stopwords)
    * listar todos os verbos utilizados
    * identificar e listar todas entidades nomeadas
    * possibilitar a análise de sentimento das frases do texto
    * armazenar a análise no mongodb

## Entrega
- Com os links dos artigos colocados em um .txt, eu os percorri gravando seu titulo e conteudo no bando de dados, com uma função eu percorri os textos dos artigos
tirando as palavras irrelevantes e criando uma nova coluna sem as stopwords assim podendo plotar a cloud com mais foco nas palavras que mais aparecem.
- (sobre a parte da analise de sentimentos ainda em desenvolvimento)
