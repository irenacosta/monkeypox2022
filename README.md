# Projeto: "Insights sobre o banco de dados público da Monkeypox"
<br>
<p align="center">
<img src="http://img.shields.io/static/v1?label=STATUS&message=EM%20REFORMULACAO&color=GREEN&style=for-the-badge"/>
</p>

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/monkeypox.png" />
</div>
<p align="center" style="font-size: 6px">
Fonte: <a href="https://www.monkeypox.global.health/">Global Health - a Data Science Iniciative</a>
</p>
<br>


## 🛠️ Para contribuir com o projeto<img height="25" src="https://colab.research.google.com/img/colab_favicon_256px.png" />

<p align="center">
<img src="http://img.shields.io/static/v1?label=SCRIPTS&message=EM%20ATUALIZACAO&color=YELLOW&style=for-the-badge"/>
</p>
<p align="center">
<img src="http://img.shields.io/static/v1?label=QUERIES&message=EM%20DESENVOLVIMENTO&color=YELLOW&style=for-the-badge"/>
</p>


- Clone o repositório
```
git clone https://github.com/irenacosta/monkeypoxPySpark.git
```

## 🔖 Dataset sobre a epidemia do vírus Monkeypox:

<p style="font-size: 16px">Disponibilizado pela <a href="https://www.monkeypox.global.health/">Global Health</a>, o dataset escolhido tem como última atualização a data de 23 de setembro de 2022. Após essa data, a organização escolheu não mais seguir com o projeto de compilação dos dados gerais enviados pelos países e segue agora apenas divulgando o número de casos registrados nos países. </p>

```
monkeypoxdf.printSchema()
```

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/monkeypoxschema.png" width="500px" height="500px"/>
</div>

```
col = len(monkeypoxdf.columns)
col
```

```
row = monkeypoxdf.count()
row
```

```
print(f'A dimensáo do Dataframe é: {(row,col)}')
print(f'O Dataframe possui o total de {row} linhas e {col} colunas')
```
<p> A dimensáo do Dataframe é: (69639, 36) </p>
<p> O Dataframe possui o total de 69639 linhas e 36 colunas </p>

## 📋 Panorama geral do estado da arte das principais colunas dataframe (Status, País, Idade, Sexo e Sintomas):

```
monkeypoxdf.agg(
    f.count('Status').alias('Status_count'),
    f.countDistinct('Status').alias('Status_distinct'),
    f.count('Country').alias('Country_count'),
    f.countDistinct('Country').alias('Country_distinct'),
    f.count('Age').alias('Age_count'),
    f.countDistinct('Age').alias('Age_distinct'), 
    f.count('Gender').alias('Gender_count'),
    f.countDistinct('Gender').alias('Gender_distinct'),
    f.count('Symptoms').alias('Symptoms_count'),
    f.countDistinct('Symptoms').alias('Symptoms_distinct')
).show()
```
```
monkeypoxdf.groupBy("Status").agg(countDistinct('Country')) \
    .show(truncate=False)
```
```
monkeypoxdf.groupBy("Status").agg(countDistinct('Age')) \
    .show(truncate=False)
```
```
monkeypoxdf.groupBy("Status").agg(countDistinct('Gender')) \
    .show(truncate=False)
```
<br>
<hr/>


## 📉 Alguns scripts de análises explanatórias:

```
from pyspark.sql.functions import datediff,col
 
monkeypoxDatedif = monkeypoxdf.withColumn("Diferenca_em_dias", datediff(col("Date_confirmation"),col("Date_onset"))) \
    .drop("Symptoms","Hospitalised (Y/N/NA)","Isolated (Y/N/NA)","Travel_history (Y/N/NA)","Travel_history_location", \
          "Travel_history_country","Date_hospitalisation","Date_isolation","Outcome","Contact_comment", \
          "Contact_ID","Contact_location","Travel_history_entry","Travel_history_start", \
          "Genomics_Metadata","Confirmation_method","Source","Source_II","Source_III", \
          "Source_IV","Source_V","Source_VI","Source_VII","Date_entry","Date_death","Date_last_modified").show(truncate=False)
monkeypoxDatedif
```

```
monkeypoxdf.createOrReplaceTempView("MonkeypoxRank1")
spark.sql("select Country, count(Status) as count_Status from MonkeypoxRank1 " +
          "group by Country having count_Status >= 1 " + 
          "order by count_Status desc").show(20)
```
```
monkeypoxFaixa = spark.createDataFrame([("Crianças","0 a 12 anos"),("Adolescentes","13 a 19 anos"),
                 ("Adultos","20 a 95 anos")],["Classificação","Faixa etária"])
monkeypoxFaixa.show()

monkeypoxdf.createOrReplaceTempView("MonkeypoxAgeRank")
spark.sql("select Age, count(Country) as count_Age from MonkeypoxAgeRank " +
          "group by Age having count_Age >= 1 " + 
          "order by count_Age desc").show(20)
```
<p font-size: 10px> Faixa etária adotada seguindo a prática de classificaçao etária em estudos científicos com dados de doenças x idade em datasets que apresentam inconsistência na catalogação dos registros da idade de pacientes </p>

```
monkeypoxAgedf=spark.createDataFrame([("1","null","null",66574),("2","20-69","adulto",616),("3","15-64",
                "indeterminado",275),("4","15-64","indeterminado",275),("5","20-59","adulto",244),
                ("6","15-74","indeterminado",240),("7","20-64","adulto",225), ("8","15-84","indeterminado",187),
                ("9","15-69","indeterminado",184),("10","30-34","adulto",75),("11","20-44","adulto",60),
                ("12","25-29","adulto",58),("13","20-99","adulto",57),("14","0-69","indeterminado",54),
                ("15","35-39","adulto",46),("16","18-61","indeterminado",44),("17","40-44","adulto",42),
                ("18","1-69","indeterminado",37),("19","19-59","indeterminado",36),("20","0-59","indeterminado",34),
                ("21","23-50","adulto",32)],["ID","Faixa_etaria","Classificacao","Quantidade"])
monkeypoxAgedf.show(20)
```
```
newmonkeypoxdf_Null1=["Status", "Localizacao", "Cidade", "Pais", "Cod_ISO3","Idade", "Sexo", "Sintomas","Hospitalizado","Viajou"]
newmonkeypoxdf1.select([count(when(isnan(c) | col(c).isNull(), c)).alias(c) for c in newmonkeypoxdf_Null1]).show()
```


<br>
<br>
<hr/>

## 📋 Principais insights:



## ⚖️Licença
MIT License

Copyright (c), 2022. IRENA OLIVEIRA DA COSTA.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

<br>

