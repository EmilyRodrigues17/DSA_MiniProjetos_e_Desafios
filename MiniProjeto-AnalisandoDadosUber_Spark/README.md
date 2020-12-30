# Analisando Dados do Uber com Spark
<p align = "center">
    <img src="../imagens/DSA.png"/>
</p>

Este é um dos Mini-Projetos proposto durante o curso de [Big Data Real-Time Analytics com Python e Spark](https://www.datascienceacademy.com.br/course?courseid=analise-de-dados-com-python) da Data Science Academy.

Versões utilizadas: <b>Java JDK 11</b> e <b>Apache Spark 2.4.2</b>
## **Introdução:**
Para este mini-projeto foi utilizado esse conjunto de dados [Dataset](https://github.com/fivethirtyeight/uber-tlc-foil-response).<br><br>
<i>Esse conjunto de dados contém dados de mais de 4,5 milhões de captações Uber na cidade de Nova York de abril a setembro de 2014 e 14,3 milhões de captações Uber de janeiro a junho de 2015. Dados em nível de viagem sobre 10 outras empresas de veículos de aluguel (FHV) bem como dados agregados para 329 empresas de FHV, também estão incluídos. Todos os arquivos foram recebidos em 3 de agosto, 15 de setembro e 22 de setebro de 2015.</i> <br>

Com isso é proposto a resolução de 3 perguntas principais:
1) Quantos são e quais são as bases de carros do Uber (onde os carros ficam esperando passageiros)?
2) Qual o total de veículos que passaram pela base B02617?
3) Qual o total de corridas por base? Apresente de forma decrescente.

## **Resolução:**
Foram feitos alguns estudos do Spark principalmente sobre RDD (Resilient Distributed Datasets), operações de transformação e operações de ações.

* Quantos são e quais são as bases de carros do Uber (onde os carros ficam esperando passageiros)?<br>

<b>Para a quantidade de bases de carros: Foram 6 bases</b>
```python
rdd_uber_linhas.map(lambda line: line[0]).distinct().count() -1

resultado: 6
```
<b>Para visualização das bases:</b>
```python
rdd_uber_linhas.map(lambda line: line[0]).distinct().collect()

resultado: ['dispatching_base_number',
            'B02765',
            'B02682',
            'B02598',
            'B02512',
            'B02764',
            'B02617']
```

* Qual o total de veículos que passaram pela base B02617?
```python
rdd_uber_B02617 = rdd_uber_linhas.filter(lambda line: "B02617" in line)

rdd_uber_B02617.count()

resultado: 59
```
* Qual o total de corridas por base? Apresente de forma decrescente.
```python
uberRDD2 = sc.textFile("data/uber.csv").filter(lambda line: "base" not in line).map(lambda line:line.split(","))

uberRDD2.map(lambda kp: (kp[0], int(kp[3])) ).reduceByKey(lambda k,v: k + v).takeOrdered(10, key = lambda x: -x[1])

resultado: [('B02764', 1914449),
            ('B02617', 725025),
            ('B02682', 662509),
            ('B02598', 540791),
            ('B02765', 193670),
            ('B02512', 93786)]
```