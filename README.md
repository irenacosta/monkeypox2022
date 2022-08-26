# Projeto "Monkeypox e os reflexos dessa história em dados"
<br>
<p align="center">
<img src="http://img.shields.io/static/v1?label=STATUS&message=EM%20ANDAMENTO&color=GREEN&style=for-the-badge"/>
</p>

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/confirmedmonkeypox.png" />
</div>
<p align="center" style="font-size: 6px">
Fonte: <a href="https://www.monkeypox.global.health/">Global Health - a Data Science Iniciative</a>
</p>
<br>


## Para usar os scripts do projeto no ambiente do Google Colab<img height="25" src="https://colab.research.google.com/img/colab_favicon_256px.png" />

- Clone o repositório
```
git clone ......
```

- Faça upload do dataset em seu Google Drive

- Conectar o Google Drive ao Colab
```
from google.colab import drive
drive.mount('/content/drive/MyDrive/[local_do_arquivo]')
```
- Instalar dependências
```
!apt-get install openjdk-8-jdk-headless -qq > /dev/null
!wget -q https://archive.apache.org/dist/spark/spark-2.4.4/spark-2.4.4-bin-hadoop2.7.tgz
!tar xf spark-2.4.4-bin-hadoop2.7.tgz
!pip install -q findspark
```
- Configurar as variáveis do ambiente
```
import os
os.environ["JAVA_HOME"] = "/usr/lib/jvm/java-8-openjdk-amd64"
os.environ["SPARK_HOME"] = "/content/spark-2.4.4-bin-hadoop2.7"

import findspark
findspark.init('spark-2.4.4-bin-hadoop2.7')
```

## 🔖 Dataset sobre a epidemia do vírus Monkeypox:

<p style="font-size: 16px">Disponibilizado pela <a href="https://www.monkeypox.global.health/">Global Health</a>, o dataset escolhido tem como última atualização a data de 22 de agosto de 2022 e traz informações acerca do atual panorama da epidemia de varíola dos macacos distribuídas em 36 colunas.</p>

```
monkeypoxdf.printSchema()
```

<div align="center">
<img src="https://github.com/irenacosta/monkeypoxPySpark/blob/main/confirmedmonkeypox.png" />
</div>


## 📋 Perguntas norteadoras do storytelling em dados:

<ul style="list-style: square;">
    <li>O cálculo ......?</li>
    <li>O ápice ......?</li>
    <li>O fator....?</li>
    <li>Há uma comunicação entre......?</li>
    <li>Por que as .....?</li>
    <li>Um ranking .....</li>
    <li>Como ......?</li>
    <li>Qual produto.....?</li>
    <li>Que episódio....?</li>
    <li>Que núcleo ......?</li>
    <li>Há um episódio ......?</li>
</ul>

<br>
<hr/>




## 📉 Algumas análises (scripts, queries e gráficos):
<div align="center">
  <img src="assets/img/monkeypox1.png" />
</div>

<div align="center">
<img src="assets/img/monkeypox2.png" />
</div>

<div align="center">
<img src="assets/img/monkeypox3.png" />
</div>

<div align="center">
<img src="assets/img/monkeypox4.png" />
</div>

<div align="center">
<img src="assets/img/monkeypox5.png" />
</div>

<br>
<br>
<hr/>

## 🛠️ Tecnologias e ferramentas utilizadas:

<div align="center">
    
   <img src="assets/img/techs.png" />

</div>

<br>
<hr/>

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

