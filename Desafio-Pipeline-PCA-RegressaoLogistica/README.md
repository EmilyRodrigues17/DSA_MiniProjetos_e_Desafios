# Pipeline com PCA e Regressão Logística
<p align = "center">
    <img src="../imagens/DSA.png"/>
</p>

Este desafio foi proposto durante o curso de [Big Data Real-Time Analytics com Python e Spark](https://www.datascienceacademy.com.br/course?courseid=analise-de-dados-com-python) da Data Science Academy.

## **Introdução do Desafio:**
Neste desafio é pré-definido dois dataframes com dados randômicos (<b>df_treino e df_teste</b>).<br>
Com isso é proposto um estudo na [documentação](https://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html) do <b>Scikit-Learn</b> para a criação de um Pipeline. <br>
Um Pipeline de maneira resumida é um conjunto de modelos concatenados, no caso do desafio especificamente o PCA (para redução de dimensionalidade) com Regressão Logística (para classificação).

## **Resolução do Desafio:**
Após ver a documentação do Pipeline e entender como se aplica ao desafio, usei os seguintes comandos para a resolução:

```python
pipe = Pipeline([('PCA', PCA()),('Regressão', LogisticRegression())])
pipe.fit(df_treino_novo, df_treino['target'])
```
O resultado foi:
```python
previsão = pipe.predict(df_teste_novo)
    array([ True,  True,  True])
```

