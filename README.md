# Projeto: "Dataset epidemia de Monkeypox 2022" 
<br>
<p align="center">
<img src="http://img.shields.io/static/v1?label=STATUS&message=CONCLUIDO&color=GREEN&style=for-the-badge"/>
</p>

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/monkeypox1.png" />
</div>
<p align="center" style="font-size: 6px">
Fonte: <a href="https://www.monkeypox.global.health/">Global Health - a Data Science Iniciative</a>
</p>
<br>


## 🛠️ Para contribuir com o projeto<img height="25" src="https://colab.research.google.com/img/colab_favicon_256px.png" />

<p align="center">
<img src="http://img.shields.io/static/v1?label=SCRIPT&message=FINALIZADO&color=YELLOW&style=for-the-badge"/>
</p>


- Clone o repositório
```
git clone https://github.com/irenacosta/monkeypoxPySpark.git
```

## 🔖 Dataset sobre a epidemia do vírus Monkeypox:

<p style="font-size: 16px">Disponibilizado pela <a href="https://github.com/globaldothealth/monkeypox">Global Health</a>, o dataset escolhido tem como última atualização a data de 23 de setembro de 2022. Após essa data, a organização escolheu não mais seguir com o projeto de compilação dos dados gerais enviados pelos países e segue agora apenas divulgando o número de casos registrados nos países. </p>

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
print(f'A dimensão do Dataframe é: {(row,col)}')
print(f'O Dataframe possui o total de {row} linhas e {col} colunas')
```
<p> A dimensão do Dataframe é: (69639, 36) </p>
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

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/panorama%20geral%20colunas.png"/>
</div>

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

## 📋 Pivotando colunas essenciais de storytelling:

```
monkeypoxPivotSA = monkeypoxdf.groupBy("Symptoms").pivot("Age").count().show()
monkeypoxPivotSA
```

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/pivot%20symptoms%20age.png"/>
</div>

```
monkeypoxPivotSG = monkeypoxdf.groupBy("Symptoms").pivot("Gender").count().show()
monkeypoxPivotSG
```

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/pivot%20symtoms%20gender.png" width="500px" height="500px"/>
</div>

```
monkeypoxPivot = monkeypoxdf.groupBy("Country").pivot("Gender").count().show()
monkeypoxPivot
```
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/pivot%20country%20gender.png" width="500px" height="500px"/>
</div>

```
df2 = monkeypoxdf.withColumn("Sexo+Idade",create_map(
        lit("Gender"),col("Gender"),
        lit("Age"),col("Age")
        )).drop("Gender","Age") \
        .withColumn("Sintomas+Hospitalizado+Isolado",create_map(
        lit("Symptoms"),col("Symptoms"),
        lit("Hospitalised (Y/N/NA)"),col("Hospitalised (Y/N/NA)"),
        lit("Isolated (Y/N/NA)"),col("Isolated (Y/N/NA)")
        )).drop("Symptoms","Hospitalised (Y/N/NA)","Isolated (Y/N/NA)") \
        .withColumn("Viajou?+Local_visitado",create_map(
            lit("Travel_history (Y/N/NA)"), col("Travel_history (Y/N/NA)"),
            lit("Travel_history_location"), col("Travel_history_location"),
            lit("Travel_history_country"), col("Travel_history_country")
        )).drop("Country_ISO3","Date_onset","Travel_history (Y/N/NA)","Travel_history_location","Travel_history_country") \
        .drop("Date_hospitalisation","Date_isolation","Outcome","Contact_comment","Contact_ID","Contact_location",
                "Travel_history_entry","Travel_history_start", \
              "Genomics_Metadata","Confirmation_method","Source","Source_II","Source_III","Source_IV","Source_V",
              "Source_VI","Source_VII","Date_entry","Date_death","Date_last_modified")
df2.printSchema()
df2.show(truncate=False)
```

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/pivot%20colunas%20afins.png" />
</div>

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

<br>
<br>
<hr/>

## 📋 Principais insights:

- O dataset traz uma porcentagem muito elevada de atributos nulos nas principais colunas;
- Optou-se por incluir a característica presença do vazio no processo de análise das informações contidas em colunas-chave do dataframe;
- Não se pode afirmar se a população de casos do vírus acontece mais entre homens ou mulheres pois 96,4% da coluna Gender apresenta-se com informação vazia.
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fico%20pizza%20sexo%20-%20todos.png" />
</div>

#### 📋 Principal insight do vazio nas colunas:
```
newmonkeypoxdf_Null1=["Status", "Localizacao", "Cidade", "Pais", "Cod_ISO3","Idade", "Sexo", "Sintomas","Hospitalizado","Viajou"]
newmonkeypoxdf1.select([count(when(isnan(c) | col(c).isNull(), c)).alias(c) for c in newmonkeypoxdf_Null1]).show()
```

<p align="center" style="font-size: 8px">
Gráfico em linha mostrando o nível de altura do vazio em todas as colunas do dataframe:
</p>
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fica%20linha%20peso%20do%20vazio%20colunas%20-%20todas.png" />
</div>
<p align="center" style="font-size: 8px">
Gráfico em barra mostrando a altura do vazio em todas as colunas do dataframe:
</p>
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fico%20barra%20peso%20do%20vazio%20colunas%20-%20todas.png" />
</div>
<p align="center" style="font-size: 8px">
Gráfico em linha mostrando o nível de altura das principais colunas do dataframe:
</p>
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fica%20linha%20peso%20vazio%20colunas%20-%20principais.png" />
</div>

#### 📋 Principal insight da coluna Age:
```
monkeypoxAgedf1 = [{"id":"1","Faixa_etaria":"null","Classificacao":"null","Quantidade":66574},
                    {"id":"2","Faixa_etaria":"20-69","Classificacao":"adulto","Quantidade":616},
                    {"id":"3","Faixa_etaria":"15-64","Classificacao":"indeterminado","Quantidade":275},
                    {"id":"4","Faixa_etaria":"15-64","Classificacao":"indeterminado","Quantidade":275},
                    {"id":"5","Faixa_etaria":"20-59","Classificacao":"adulto","Quantidade":244},
                    {"id":"6","Faixa_etaria":"15-74","Classificacao":"indeterminado","Quantidade":240},
                    {"id":"7","Faixa_etaria":"20-64","Classificacao":"adulto","Quantidade":225},
                    {"id":"8","Faixa_etaria":"15-84","Classificacao":"indeterminado","Quantidade":187},
                    {"id":"9","Faixa_etaria":"15-69","Classificacao":"indeterminado","Quantidade":184},
                    {"id":"10","Faixa_etaria":"30-34","Classificacao":"adulto","Quantidade":75},
                    {"id":"11","Faixa_etaria":"20-44","Classificacao":"adulto","Quantidade":60},
                    {"id":"12","Faixa_etaria":"25-29","Classificacao":"adulto","Quantidade":58},
                    {"id":"13","Faixa_etaria":"20-99","Classificacao":"adulto","Quantidade":57},
                    {"id":"14","Faixa_etaria":"0-69","Classificacao":"indeterminado","Quantidade":54},
                    {"id":"15","Faixa_etaria":"35-39","Classificacao":"adulto","Quantidade":46},
                    {"id":"16","Faixa_etaria":"18-61","Classificacao":"indeterminado","Quantidade":44},
                    {"id":"17","Faixa_etaria":"40-44","Classificacao":"adulto","Quantidade":42},
                    {"id":"18","Faixa_etaria":"1-69","Classificacao":"indeterminado","Quantidade":37},
                    {"id":"19","Faixa_etaria":"19-59","Classificacao":"indeterminado","Quantidade":36},
                    {"id":"20","Faixa_etaria":"0-59","Classificacao":"indeterminado","Quantidade":34},
                    {"id":"21","Faixa_etaria":"23-50","Classificacao":"adulto","Quantidade":32}]

monkeypoxAgedf1 = spark.createDataFrame(monkeypoxAgedf1)

monkeypoxAgedf1.groupBy('Classificacao').sum('Quantidade').show(20)
```
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fico%20pizza%20-%20faixa%20eta%CC%81ria.png"/>
</div>

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fico%20pizza%20faixa%20eta%CC%81ria%20-%20adulto%20e%20indeterminado.png"/>
</div>

#### 📋 Principal insight da coluna Gender:
```
monkeypoxdf.createOrReplaceTempView("MonkeypoxRank3")
spark.sql(
    """SELECT Gender,
        Count(Country) AS total_Sexo
    FROM MonkeypoxRank3
    GROUP BY Gender
    ORDER BY Count(Country) DESC"""
).show()
```
```
monkeypoxGender5=spark.createDataFrame([("67120","2442","33")],["Null","male","female"])
monkeypoxGender5.show()
```
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fico%20pizza%20sexo%20-%20todos.png"/>
</div>
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fico%20pizza%20sexo%20-%20sem%20null.png"/>
</div>

#### 📋 Principal insight da coluna Symptoms:
```
monkeypoxdf.createOrReplaceTempView("MonkeypoxRank4")
spark.sql("select Symptoms, count(Country) as count_Country from MonkeypoxRank4 " +
          "group by Symptoms having count_Country >= 1 " + 
          "order by count_Country desc").show(10)
```
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fico%20pizza%20sintomas%20-%20todos%20os%2010%20mais.png"/>
</div>
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fico%20pizza%20sintomas%20-%2010%20mais%20sem%20null.png"/>
</div>

```
monkeypoxSymptoms3=spark.createDataFrame([("1","None","None","69375"),("2","genital ulcer lesions","Genitália","30"),
                    ("3","oral ulcer","Boca","17"),("4","genital ulcers","Genitália","17"),("5","fever","Febre","17"),
                    ("6","ulcerative lesions","Pele","16"),("7","rash","Pele","13"),("8","skin lesions","Pele","7"),
                    ("9","skin lesions","Pele","5"),("10","ulcerative lesions","Pele","5"),("11","vesicular rash","Pele","5"),
                    ("12","genital ulcers", "Genitália","5"),("13","fever","Febre","4"),("14","skin lesions","Pele","4")],          
                    ["ID","Sintoma","Grupo_Sintoma","Total_Grupo_Sintoma"])
monkeypoxSymptoms3.show()
```

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fico%20pizza%20grupo%20sintomas%20-%20todos.png"/>
</div>
<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/img/gra%CC%81fico%20pizza%20grupo%20sintomas%20-%20sem%20null.png"/>
</div>
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

