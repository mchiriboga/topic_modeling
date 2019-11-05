# Segmentação de Tópicos de Reviews do Airbnb RJ

Esta análise é referente a segmentação dos principais assuntos das reviews do Airbnb Rio de Janeiro.

O dataset utilizado foi obtido de http://insideairbnb.com/get-the-data.html.
Especificamente, foi utilizado o arquivo [reviews.csv](http://data.insideairbnb.com/brazil/rj/rio-de-janeiro/2019-09-23/visualisations/reviews.csv)
que também está disponível na pasta [data](https://github.com/mchiriboga/topic_modelling/tree/master/data) deste repositório.

O problema em questão trata-se de um caso de aprendizado não supervisionado referente a PNL (Processamento de Linguagem Natural)
e tem como objetivo identificar os principais tópicos relacionados às reviews do Airbnb. Com esse intuito, foi utilizado o
algoritmo LDA (Latent Dirichlet Allocation), um modelo estatístico que, com base na frequência de palavras em um conjunto de
documentos, sugere tópicos relacionados aos conjuntos de palavras dominantes identificados. Assim como em clusterização, o
número de tópicos é um hiperparâmetro.

O processo para modelagem consistiu basicamente no:
- Carregamento dos Dados;
- Preprocessamento;
- Escolha do Número de Tópicos;
- Criação do Modelo; e
- Visualização e Interpretação dos Tópicos.

## Preprocessamento

Para o preprocessamento dos dados, foi utilizada a biblioteca spaCy, que disponibiliza funcionalidades de NLP, como tokenização e remoção de pontuação e stop words. Conforme identificado durante a análise exploratória dos dados, o dataset do Airbnb inclui reviews escritas em diversos idiomas, com Português, Inglês, e Espanhol compondo a maior parte. Portanto, a primeira escolha foi a de utilizar o modelo multi-idiomas do spaCy. Entretanto, verificou-se que este modelo não suporta a identificação de classes gramaticais ou stopwords, sendo limitado ao reconhecimento de entidades mencionadas. Portanto, optou-se por utilizar o modelo de lingua portuguesa, uma vez que esta consiste no idioma predominante das reviews. A análise para identificar tópicos para os demais idiomas pode ser feita de forma análoga, substituindo o modelo spaCy utilizado pelo do idioma desejado.

## Escolha de Hiperparâmetro e Criação do Modelo

Para a escolha do hiperparâmetro `num_topics`, foram treinados modelos com diferentes número de tópicos, e analisando o `coherence score`, o melhor modelo seria aquele treinado com `num_topics = 12`, uma vez que este foi o que apresentou o maior valor. Entretanto, avaliando a interpretabilidade dos tópicos identificados, esse modelo deixou a desejar, dado que no mesmo há uma sobreposição de temas em diferentes tópicos. Por este motivo, optou-se por selecionar um modelo com menor número de tópicos (`num_topics = 5`), já que esse ofereceu resultados mais interpretáveis e coerentes.

Uma vez escolhido o modelo, foram assinalados manualmente rótulos para cada um dos tópicos identificados, com base nos termos que os compõem. Em seguida, adicionou-se ao dataframe uma coluna (`main_topic`) referente ao tópico com maior probabilidade para cada uma das reviews, conforme identificado pelo modelo.

## Validação

Uma vez que não existem categorias pré-definidas para as reviews, não é possível medir a precisão do modelo desenvolvido. Entretanto, de forma subjetiva, é possível verificar que os tópicos identificados, tem relação com o conteúdo das reviews. Uma abordagem que permitiria obter uma métrica mais concreta para avaliação do modelo, ao custo de um maior tempo de desenvolvimento, seria usar uma parte das reviews para fazer o treinamento do modelo, enquanto a outra parte seria rotulada manualmente para posteriormente ser utilizada como set de validação, comparando os rótulos manuais com os sugeridos pelo modelo.

## Análises Futuras

Conforme mencionado anteriormente, a análise foi limitada ao idioma português, e no futuro, seria interessante incluir as reviews de outros idiomas. Uma opção para tal seria efetuar o preprocessamento utilizando os modelos correspondentes do spaCy para cada um dos referentes idiomas, e posteriormente utilizando [tradução automática](https://pypi.org/project/googletrans/) nas reviews preprocessadas para realizar a segmentação por tópico.

Ainda com relação ao preprocessamento, seria interessante avaliar mais a fundo formas de refiná-lo ainda mais, identificando termos que poderiam ser desconsiderados, de forma similar ao tratamento dado para as strings que substituem conteúdo ocultado pelo Airbnb (e.g. "website hidden by airbnb").

Outro ponto a ser avaliado seria o impacto de incluir bigrams e trigrams na modelagem, avaliando se os tópicos identificados podem se tornar mais relevantes e interpretáveis, e com menos sobreposição de termos.

Finalmente, para efeitos de validação, vale reforçar o ponto mencionado anteriormente relativo a separação dos dados utilizados para treinamento e validação. Para tal, seria necessário um esforço significativo para rotulação dos dados a serem utilizados para validação, mas com um impacto significativo no que se refere a obter uma avaliação do modelo com mais confiança.
