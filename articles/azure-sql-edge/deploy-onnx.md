---
title: Wdrażanie i wykonywanie prognoz przy użyciu ONNX
titleSuffix: SQL machine learning
description: Dowiedz się, jak szkolić model, przekonwertować go na ONNX, wdrożyć go w usłudze Azure SQL Edge lub wystąpieniu zarządzanym Azure SQL (wersja zapoznawcza), a następnie uruchomić natywne przewidywania danych przy użyciu przekazanego modelu ONNX.
keywords: Wdróż program SQL Edge
ms.prod: sql
ms.technology: machine-learning
ms.topic: quickstart
author: dphansen
ms.author: davidph
ms.date: 10/13/2020
ms.openlocfilehash: 755111b2fc48ec119c30d09f2e51b9db6c333848
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653214"
---
# <a name="deploy-and-make-predictions-with-an-onnx-model-and-sql-machine-learning"></a>Wdrażanie i Tworzenie prognoz przy użyciu modelu ONNX i programu SQL Machine Learning

W tym przewodniku szybki start dowiesz się, jak nauczyć model, przekonwertować go na ONNX, wdrożyć go w usłudze [Azure SQL Edge](onnx-overview.md) lub [wystąpieniu zarządzanym Azure SQL (wersja zapoznawcza)](../azure-sql/managed-instance/machine-learning-services-overview.md), a następnie uruchomić natywne przewidywania danych przy użyciu przekazanego modelu ONNX.

Ten przewodnik Szybki Start jest oparty na **scikit — uczenie** i używa [zestawu danych dla obudowy Boston](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.load_boston.html).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Jeśli używasz usługi Azure SQL Edge i nie wdrożono modułu usługi Azure SQL Edge, postępuj zgodnie z instrukcjami [wdrażania programu SQL Edge przy użyciu Azure Portal](deploy-portal.md).

* Zainstaluj [Azure Data Studio](/sql/azure-data-studio/download).

* Zainstaluj pakiety języka Python, które są odpowiednie dla tego przewodnika Szybki Start:

  1. Otwórz [Nowy Notes](/sql/azure-data-studio/sql-notebooks) połączony z jądrem Python 3. 
  1. Kliknij pozycję **Zarządzaj pakietami**
  1. Na karcie **zainstalowane** poszukaj następujących pakietów języka Python na liście zainstalowanych pakietów. Jeśli którykolwiek z tych pakietów nie jest zainstalowany, wybierz kartę **Dodaj nową** , Wyszukaj pakiet, a następnie kliknij przycisk **Instaluj**.
     - **scikit-learn**
     - **numpy**
     - **onnxmltools**
     - **onnxruntime**
     - **pyodbc**
     - **setuptools**
     - **skl2onnx**
     - **sqlalchemy**

* Dla każdego poniższego składnika skryptu wprowadź go w komórce w notesie Azure Data Studio i uruchom komórkę.

## <a name="train-a-pipeline"></a>Uczenie potoku

Podziel zestaw danych, aby używać funkcji do przewidywania wartości środkowej w domu.

```python
import numpy as np
import onnxmltools
import onnxruntime as rt
import pandas as pd
import skl2onnx
import sklearn
import sklearn.datasets

from sklearn.datasets import load_boston
boston = load_boston()
boston

df = pd.DataFrame(data=np.c_[boston['data'], boston['target']], columns=boston['feature_names'].tolist() + ['MEDV'])
 
target_column = 'MEDV'
 
# Split the data frame into features and target
x_train = pd.DataFrame(df.drop([target_column], axis = 1))
y_train = pd.DataFrame(df.iloc[:,df.columns.tolist().index(target_column)])

print("\n*** Training dataset x\n")
print(x_train.head())

print("\n*** Training dataset y\n")
print(y_train.head())
```

**Dane wyjściowe**:

```text
*** Training dataset x

        CRIM    ZN  INDUS  CHAS    NOX     RM   AGE     DIS  RAD    TAX  \
0  0.00632  18.0   2.31   0.0  0.538  6.575  65.2  4.0900  1.0  296.0
1  0.02731   0.0   7.07   0.0  0.469  6.421  78.9  4.9671  2.0  242.0
2  0.02729   0.0   7.07   0.0  0.469  7.185  61.1  4.9671  2.0  242.0
3  0.03237   0.0   2.18   0.0  0.458  6.998  45.8  6.0622  3.0  222.0
4  0.06905   0.0   2.18   0.0  0.458  7.147  54.2  6.0622  3.0  222.0

    PTRATIO       B  LSTAT  
0     15.3  396.90   4.98  
1     17.8  396.90   9.14  
2     17.8  392.83   4.03  
3     18.7  394.63   2.94  
4     18.7  396.90   5.33  

*** Training dataset y

0    24.0
1    21.6
2    34.7
3    33.4
4    36.2
Name: MEDV, dtype: float64
```

Utwórz potok, aby nauczyć model LinearRegression. Można również użyć innych modeli regresji.

```python
from sklearn.compose import ColumnTransformer
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import Pipeline
from sklearn.preprocessing import RobustScaler

continuous_transformer = Pipeline(steps=[('scaler', RobustScaler())])

# All columns are numeric - normalize them
preprocessor = ColumnTransformer(
    transformers=[
        ('continuous', continuous_transformer, [i for i in range(len(x_train.columns))])])

model = Pipeline(
    steps=[
        ('preprocessor', preprocessor),
        ('regressor', LinearRegression())])

# Train the model
model.fit(x_train, y_train)
```

Sprawdź dokładność modelu, a następnie Oblicz wynik R2 i średni błąd kwadratowy.

```python
# Score the model
from sklearn.metrics import r2_score, mean_squared_error
y_pred = model.predict(x_train)
sklearn_r2_score = r2_score(y_train, y_pred)
sklearn_mse = mean_squared_error(y_train, y_pred)
print('*** Scikit-learn r2 score: {}'.format(sklearn_r2_score))
print('*** Scikit-learn MSE: {}'.format(sklearn_mse))
```

**Dane wyjściowe**:

```text
*** Scikit-learn r2 score: 0.7406426641094094
*** Scikit-learn MSE: 21.894831181729206
```

## <a name="convert-the-model-to-onnx"></a>Konwertuj model na ONNX

Przekonwertuj typy danych na obsługiwane typy danych SQL. Ta konwersja będzie również wymagana dla innych ramek danych.

```python
from skl2onnx.common.data_types import FloatTensorType, Int64TensorType, DoubleTensorType

def convert_dataframe_schema(df, drop=None, batch_axis=False):
    inputs = []
    nrows = None if batch_axis else 1
    for k, v in zip(df.columns, df.dtypes):
        if drop is not None and k in drop:
            continue
        if v == 'int64':
            t = Int64TensorType([nrows, 1])
        elif v == 'float32':
            t = FloatTensorType([nrows, 1])
        elif v == 'float64':
            t = DoubleTensorType([nrows, 1])
        else:
            raise Exception("Bad type")
        inputs.append((k, t))
    return inputs
```

Korzystając z programu `skl2onnx` , przekonwertuj model LinearRegression na format ONNX i Zapisz go lokalnie.

```python
# Convert the scikit model to onnx format
onnx_model = skl2onnx.convert_sklearn(model, 'Boston Data', convert_dataframe_schema(x_train), final_types=[('variable1',FloatTensorType([1,1]))])
# Save the onnx model locally
onnx_model_path = 'boston1.model.onnx'
onnxmltools.utils.save_model(onnx_model, onnx_model_path)
```

## <a name="test-the-onnx-model"></a>Testowanie modelu ONNX

Po przeprowadzeniu konwersji modelu do formatu ONNX należy posłużyć do oceny modelu w celu niewielkiego obniżenia wydajności.

> [!NOTE]
> Środowisko uruchomieniowe ONNX używa wartości zmiennoprzecinkowych zamiast podwójnej precyzji, więc możliwe jest użycie małych niezgodności.

```python
import onnxruntime as rt
sess = rt.InferenceSession(onnx_model_path)

y_pred = np.full(shape=(len(x_train)), fill_value=np.nan)

for i in range(len(x_train)):
    inputs = {}
    for j in range(len(x_train.columns)):
        inputs[x_train.columns[j]] = np.full(shape=(1,1), fill_value=x_train.iloc[i,j])

    sess_pred = sess.run(None, inputs)
    y_pred[i] = sess_pred[0][0][0]

onnx_r2_score = r2_score(y_train, y_pred)
onnx_mse = mean_squared_error(y_train, y_pred)

print()
print('*** Onnx r2 score: {}'.format(onnx_r2_score))
print('*** Onnx MSE: {}\n'.format(onnx_mse))
print('R2 Scores are equal' if sklearn_r2_score == onnx_r2_score else 'Difference in R2 scores: {}'.format(abs(sklearn_r2_score - onnx_r2_score)))
print('MSE are equal' if sklearn_mse == onnx_mse else 'Difference in MSE scores: {}'.format(abs(sklearn_mse - onnx_mse)))
print()
```

**Dane wyjściowe**:

```text
*** Onnx r2 score: 0.7406426691136831
*** Onnx MSE: 21.894830759270633

R2 Scores are equal
MSE are equal
```

## <a name="insert-the-onnx-model"></a>Wstaw model ONNX

Przechowuj model w usłudze Azure SQL Edge lub wystąpieniu zarządzanym Azure SQL w `models` tabeli w bazie danych `onnx` . W parametrach połączenia Określ **adres serwera**, **nazwę użytkownika** i **hasło**.

```python
import pyodbc

server = '' # SQL Server IP address
username = '' # SQL Server username
password = '' # SQL Server password

# Connect to the master DB to create the new onnx database
connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=master;UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(connection_string, autocommit=True)
cursor = conn.cursor()

database = 'onnx'
query = 'DROP DATABASE IF EXISTS ' + database
cursor.execute(query)
conn.commit()

# Create onnx database
query = 'CREATE DATABASE ' + database
cursor.execute(query)
conn.commit()

# Connect to onnx database
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string, autocommit=True)
cursor = conn.cursor()

table_name = 'models'

# Drop the table if it exists
query = f'drop table if exists {table_name}'
cursor.execute(query)
conn.commit()

# Create the model table
query = f'create table {table_name} ( ' \
    f'[id] [int] IDENTITY(1,1) NOT NULL, ' \
    f'[data] [varbinary](max) NULL, ' \
    f'[description] varchar(1000))'
cursor.execute(query)
conn.commit()

# Insert the ONNX model into the models table
query = f"insert into {table_name} ([description], [data]) values ('Onnx Model',?)"

model_bits = onnx_model.SerializeToString()

insert_params  = (pyodbc.Binary(model_bits))
cursor.execute(query, insert_params)
conn.commit()
```

## <a name="load-the-data"></a>Ładowanie danych

Załaduj dane do bazy danych SQL.

Najpierw Utwórz dwie tabele, **funkcje** i elementy **docelowe**, aby przechowywać podzestawy zestawu danych dla obudowy Boston.

* **Funkcja** zawiera wszystkie dane używane do przewidywania wartości docelowej, mediana. 
* **Element docelowy** zawiera wartość mediany dla każdego rekordu w zestawie danych. 

```python
import sqlalchemy
from sqlalchemy import create_engine
import urllib

db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"

conn = pyodbc.connect(db_connection_string)
cursor = conn.cursor()

features_table_name = 'features'

# Drop the table if it exists
query = f'drop table if exists {features_table_name}'
cursor.execute(query)
conn.commit()

# Create the features table
query = \
    f'create table {features_table_name} ( ' \
    f'    [CRIM] float, ' \
    f'    [ZN] float, ' \
    f'    [INDUS] float, ' \
    f'    [CHAS] float, ' \
    f'    [NOX] float, ' \
    f'    [RM] float, ' \
    f'    [AGE] float, ' \
    f'    [DIS] float, ' \
    f'    [RAD] float, ' \
    f'    [TAX] float, ' \
    f'    [PTRATIO] float, ' \
    f'    [B] float, ' \
    f'    [LSTAT] float, ' \
    f'    [id] int)'

cursor.execute(query)
conn.commit()

target_table_name = 'target'

# Create the target table
query = \
    f'create table {target_table_name} ( ' \
    f'    [MEDV] float, ' \
    f'    [id] int)'

x_train['id'] = range(1, len(x_train)+1)
y_train['id'] = range(1, len(y_train)+1)

print(x_train.head())
print(y_train.head())
```

Na koniec użyj, `sqlalchemy` Aby wstawić `x_train` i `y_train` Pandas ramki danych do tabel `features` i `target` , odpowiednio. 

```python
db_connection_string = 'mssql+pyodbc://' + username + ':' + password + '@' + server + '/' + database + '?driver=ODBC+Driver+17+for+SQL+Server'
sql_engine = sqlalchemy.create_engine(db_connection_string)
x_train.to_sql(features_table_name, sql_engine, if_exists='append', index=False)
y_train.to_sql(target_table_name, sql_engine, if_exists='append', index=False)
```

Teraz można wyświetlić dane w bazie danych programu.

## <a name="run-predict-using-the-onnx-model"></a>Uruchamianie przewidywania przy użyciu modelu ONNX

Model w języku SQL umożliwia uruchamianie natywnego przewidywania danych przy użyciu przekazanego modelu ONNX.

> [!NOTE]
> Zmień jądro notesu na SQL w celu uruchomienia pozostałej komórki.

```sql
USE onnx

DECLARE @model VARBINARY(max) = (
        SELECT DATA
        FROM dbo.models
        WHERE id = 1
        );

WITH predict_input
AS (
    SELECT TOP (1000) [id]
        , CRIM
        , ZN
        , INDUS
        , CHAS
        , NOX
        , RM
        , AGE
        , DIS
        , RAD
        , TAX
        , PTRATIO
        , B
        , LSTAT
    FROM [dbo].[features]
    )
SELECT predict_input.id
    , p.variable1 AS MEDV
FROM PREDICT(MODEL = @model, DATA = predict_input, RUNTIME=ONNX) WITH (variable1 FLOAT) AS p;
```

## <a name="next-steps"></a>Następne kroki

* [Machine Learning i AI z ONNX w usłudze SQL Edge](onnx-overview.md)
* [Machine Learning Services w wystąpieniu zarządzanym usługi Azure SQL (wersja zapoznawcza)](../azure-sql/managed-instance/machine-learning-services-overview.md)
