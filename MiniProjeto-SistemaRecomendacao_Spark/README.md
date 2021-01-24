# Sistema de Recomendação com Spark MLLib
<p align = "center">
    <img src="../imagens/DSA.png"/>
</p>

Este é um dos Mini-Projetos proposto durante o curso de [Big Data Real-Time Analytics com Python e Spark](https://www.datascienceacademy.com.br/course?courseid=analise-de-dados-com-python) da Data Science Academy.

Versões utilizadas: <b>Java JDK 11</b> e <b>Apache Spark 2.4.2</b>
## **Introdução:**
O objetivo deste notebook é mostrar como aplicar um algoritmo de Sistema de Recomendação e calcular o "Affinity Score", a fim de descobrir se um produto deve ou não deve ser recomendado a um usuário.

<b>Conceitos importantes:</b>

* ALS = Alternating Least Squares, que é um algoritmo para sistema de recomendação que otimiza a loss function (taxa de erro do modelo) e funciona muito bem em ambientes paralelizados.
* Hiperparâmetros da ALS: rank (parametro para otimizar o algoritmo) e maxIter (numero maximo de iterações).
* A base de dados contém três colunas: user (identificação do usuário), item (identificação do produto) e rating (avaliação).


## **O que foi feito:**

* <b>Importando o algoritmo ALS</b>
```python
from pyspark.ml.recommendation import ALS
```
* <b>Carregando os dados e realizando as alterações nas variaveis [user, item, rating]</b>
```python
ratingsRDD = sc.textFile('data/user-item.txt')

# convertendo a coluna user e item para inteiro e a coluna rating para float
ratingsRDD2 = ratingsRDD.map(lambda line: line.split(',')).map(lambda line:(int(line[0]), int(line[1]), float(line[2])))

# criando um dataframe
ratingsDF = spSession.createDataFrame(ratingsRDD2, ["user", "item", "rating"])

ratingsDF.show()

+----+----+------+
|user|item|rating|
+----+----+------+
|1001|9001|  10.0|
|1001|9002|   1.0|
|1001|9003|   9.0|
|1002|9001|   3.0|
|1002|9002|   5.0|
|1002|9003|   1.0|
|1002|9004|  10.0|
|1003|9001|   2.0|
|1003|9002|   6.0|
|1003|9003|   2.0|
|1003|9004|   9.0|
|1003|9005|  10.0|
|1003|9006|   8.0|
|1003|9007|   9.0|
|1004|9001|   9.0|
|1004|9002|   2.0|
|1004|9003|   8.0|
|1004|9004|   3.0|
|1004|9010|  10.0|
|1004|9011|   9.0|
+----+----+------+
```
* <b>Contruindo o Modelo</b>
```python
als = ALS(rank = 10, maxIter = 5)
modelo = als.fit(ratingsDF)
```

* <b>Criando um dataset de teste</b>
```python
testeDF = spSession.createDataFrame([(1001, 9003),(1001,9004),(1001,9005)], ["user", "item"])
```

* <b>Criando as previsões</b>
```python
previsoes = (modelo.transform(testeDF).collect())

previsoes

[Row(user=1001, item=9004, prediction=-0.6358490586280823),
 Row(user=1001, item=9005, prediction=-2.2901651859283447),
 Row(user=1001, item=9003, prediction=9.001792907714844)]
```

O que podemos tirar como conclusão a partir das respostas:

1) Se o sistema ofertar o produto <b>9004</b> e <b>9005</b> para o usuário 1001 é provável que o usuário não compre/adquira o produto pois o Affinity Score está <b>negativo.</b>
2) Já com o produto <b>9003</b> como o Affinity Score é <b>positivo</b> é bem provável que o usuário compre/adquira esse produto.
