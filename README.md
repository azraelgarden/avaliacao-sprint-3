# Avaliação Sprint 3 - Análise sentimental de artigos com SpaCy
<p align="center"><img src = "https://www.um.edu.mt/__data/assets/image/0011/348149/varieties/banner.jpg"></p>

## 📜 Descrição do Projeto
Projeto avaliativo da Compasso-UOL, que possui o intuito de avaliar e pontuar:

- I. Extrair 10 artigos jornalísticos;
- II. A partir de cada artigo, armazenar os textos no mongoDB;
- III. Criar uma nuvem de palavras para cada artigo;
- IV. Listar verbos e entidades utilizadas;
- V. Analisar sentimentos das frases dos artigos;
- VI. Armazenar resultados das análises no mongoDB;

## 🗒 Objetivo do Projeto
Capturar sentimentos de uma base de dados do Twitter, e classificar os sentimentos dos artigos jornalísticos de 50 anos atrás da [Folha de S.Paulo](https://www1.folha.uol.com.br/especial/2017/ha-50-anos/).

## 📑 Artigos analisados
| idx | Título                                                                       | URL                                                                                                                                                    |
|-----|------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1   | 1971: Prefeito revoga decreto que previa quarteirão verde no Jardim Paulista | [Clique aqui!](https://www1.folha.uol.com.br/banco-de-dados/2021/05/1971-prefeito-revoga-decreto-que-previa-quarteirao-verde-no-jardim-paulista.shtml) |
| 2   | 1971: Governador diz conversar com prefeito de São Paulo diariamente         | [Clique aqui!](https://www1.folha.uol.com.br/banco-de-dados/2021/04/1971-governador-diz-conversar-com-prefeito-de-sao-paulo-diariamente.shtml)          |
| 3   | 1971: Vicente Matheus é escolhido para ser diretor de futebol do Corinthians | [Clique aqui!](https://www1.folha.uol.com.br/banco-de-dados/2021/04/1971-vicente-matheus-e-escolhido-para-ser-diretor-de-futebol-do-corinthians.shtml) |
| 4   | 1971: Vaticano afirma que países pobres recebem 'quantia ridícula' de ajuda  | [Clique aqui!](https://www1.folha.uol.com.br/banco-de-dados/2021/04/1971-vaticano-afirma-que-paises-pobres-recebem-quantia-ridicula-de-ajuda.shtml)    |
| 5   | 1971: Índia fala em matança e ameaça intervir para ajudar Bangladesh         | [Clique aqui!](https://www1.folha.uol.com.br/banco-de-dados/2021/04/1971-india-fala-em-matanca-e-ameaca-intervir-para-ajudar-bangladesh.shtml)         |
| 6   | 1971: Nixon cede a clamor ao libertar condenado por massacre em My Lai       | [Clique aqui!](https://www1.folha.uol.com.br/banco-de-dados/2021/04/1971-nixon-cede-a-clamor-ao-libertar-condenado-por-massacre-em-my-lai.shtml)       |
| 7   | 1971: Piloto italiano morre após Ferrari explodir em batida na Argentina     | [Clique aqui!](https://www1.folha.uol.com.br/banco-de-dados/2021/01/1971-piloto-italiano-morre-apos-ferrari-explodir-em-batida-na-argentina.shtml)     |
| 8   | 1971: A 'grande cidade' Corinthians vai às urnas; 30 mil podem votar         | [Clique aqui!](https://www1.folha.uol.com.br/banco-de-dados/2021/03/1971-a-grande-cidade-corinthians-vai-as-urnas-30-mil-podem-votar.shtml)            |
| 9   | 1971: Crise entre militares na cúpula do governo argentino se agrava         | [Clique aqui!](https://www1.folha.uol.com.br/banco-de-dados/2021/03/1971-crise-entre-militares-na-cupula-do-governo-argentino-se-agrava.shtml)         |
| 10  | 1971: Primeiro-ministro do Canadá se casa em segredo                         | [Clique aqui!](https://www1.folha.uol.com.br/banco-de-dados/2021/03/1971-primeiro-ministro-do-canada-se-casa-em-segredo.shtml)                         |

## 🔗 Funções criadas e breve descrição

| idx | Nome da função             | Descrição da função                                                      |
|-----|----------------------------|--------------------------------------------------------------------------|
| 1   | tratamento_texto(input)    | Trata o texto 'parsed' do HTML, re-ajustando os espaços entre as TAGS    |
| 2   | cria_titulo(input)         | Captura o título de cada artigo                                          |
| 3   | cria_artigo(input)         | Captura os textos (artigo) de cada artigo                                |
| 4   | baixa_site(input)          | Baixa por completo o site selecionado                                    |
| 5   | transforma_json(posx, aux) | Transforma em duas posições de dados em json - {posx:aux}                |
| 6   | cria_db(input)             | Cria uma conexão no banco, apenas passando o argumento para cada coleção |
| 7   | mostrar_nuvem(input)       | Mostra uma nuvem de palavras para invocação                              |
| 8   | cria_doc(input)            | Captura o texto por completo e trata em documentos de NLP                |
| 9   | cria_doc_tratado(input)    | Transforma o texto e válida stopwords e caracteres válidos               |
| 10  | trata_texto(doc)           | Criador de stopwords e caracteres válidos                                |

## 🏷 Modelos de classificações
- **MultinomialNB**: baseado no algoritmo Naive Bayes é definido para a distribuição multinomial com finalidade de classificações de textos;
- **RandomForestClassifier**: dispõe de técnicas de perturbação e combinação com escopo de aleatoridade ao classificador, objetivo de previsão média dos classificadores individuais. (Resultados mais precisos em relação ao MultinomialNB);

## 😛 Tabela de resultado: título por sentimento
| idx | Título                                                                       | Sentimento |
|-----|------------------------------------------------------------------------------|------------|
| 1   | 1971: Prefeito revoga decreto que previa quarteirão verde no Jardim Paulista | Neutro     |
| 2   | 1971: Governador diz conversar com prefeito de São Paulo diariamente         | Negativo   |
| 3   | 1971: Vicente Matheus é escolhido para ser diretor de futebol do Corinthians | Positivo   |
| 4   | 1971: Vaticano afirma que países pobres recebem 'quantia ridícula' de ajuda  | Neutro     |
| 5   | 1971: Índia fala em matança e ameaça intervir para ajudar Bangladesh         | Neutro     |
| 6   | 1971: Nixon cede a clamor ao libertar condenado por massacre em My Lai       | Neutro     |
| 7   | 1971: Piloto italiano morre após Ferrari explodir em batida na Argentina     | Neutro     |
| 8   | 1971: A 'grande cidade' Corinthians vai às urnas; 30 mil podem votar         | Neutro     |
| 9   | 1971: Crise entre militares na cúpula do governo argentino se agrava         | Negativo   |
| 10  | 1971: Primeiro-ministro do Canadá se casa em segredo                         | Neutro     |

## 🤔 Possíveis novas avaliações
- Baseando-se nos sentimentos, o modelo deve criar uma frase negativa, positiva e neutra;
- Classificar sentimentos de contexto por input de usuário;
- Listar palavras próximas ao contexto ou possíveis palavras com os mesmos sentimentos;
- Classificar verbos: passado, presente e futuro;
- Analisar o contexto, e o modelo deve retornar uma imagem com algo parecido;

## 📦 Pré-requisitos
- Python versão 3;
- SpaCy;
- SkLearn;
- PyMongo;
- NumPy;
- Pandas;
- bs4;
- MatplotLib;
- WordCloud;

## 🚀 Tecnologias utilizadas
- Linux Ubuntu 20.04;
- Jupyter Notebook;
- MongoDB Server & Client;
- GitHub;

## 👨‍💻 Autor
- <b>[João Victor Palhares Barbosa](https://github.com/vicpb)</b>
