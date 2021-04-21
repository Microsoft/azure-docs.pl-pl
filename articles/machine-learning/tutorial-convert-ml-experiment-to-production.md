---
title: Konwertowanie kodu notesu na skrypty języka Python
titleSuffix: Azure Machine Learning
description: Przekróć eksperymentalne notesy uczenia maszynowego w kod gotowy do produkcji przy użyciu szablonu kodu MLOpsPython. Następnie możesz przetestować, wdrożyć i zautomatyzować ten kod.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 37778bc096c9089e3706907fcdd6b9c816cc5fbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817490"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Samouczek: konwertowanie eksperymentów uczenia maszynowego na produkcyjny kod języka Python

Z tego samouczka dowiesz się, jak konwertować notesy Juptyer na skrypty języka Python, aby ułatwić testowanie i automatyzację przy użyciu szablonu kodu MLOpsPython i Azure Machine Learning. Zazwyczaj ten proces jest używany do eksperymentowania/trenowania kodu z notesu programu Juptyer i konwertowania go na skrypty języka Python. Skrypty te mogą być następnie używane do testowania i automatyzacji ci/CD w środowisku produkcyjnym. 

Projekt uczenia maszynowego wymaga eksperymentowania, w którym hipotezy są testowane przy użyciu elastycznych narzędzi, takich jak Jupyter Notebook przy użyciu rzeczywistych zestawów danych. Gdy model jest gotowy do produkcji, kod modelu powinien zostać umieszczony w repozytorium kodu produkcyjnego. W niektórych przypadkach kod modelu musi zostać przekonwertowany na skrypty języka Python, aby można je było umieścić w repozytorium kodu produkcyjnego. W tym samouczku opisano zalecane podejście do eksportowania kodu eksperymentowania do skryptów języka Python.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Czyszczenie kodu niemądowego
> * Refakto Jupyter Notebook kodu do funkcji
> * Tworzenie skryptów języka Python dla powiązanych zadań
> * Tworzenie testów jednostkowych

## <a name="prerequisites"></a>Wymagania wstępne

- [Wygeneruj szablon MLOpsPython i](https://github.com/microsoft/MLOpsPython/generate) użyj `experimentation/Diabetes Ridge Regression Training.ipynb` `experimentation/Diabetes Ridge Regression Scoring.ipynb` notesów i . Te notesy są używane jako przykład konwersji z eksperymentowania do produkcji. Te notesy można znaleźć na stronie [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) .
- Zainstaluj system `nbconvert`. Postępuj zgodnie z instrukcjami instalacji w sekcji __Instalowanie programu nbconvert__ na [stronie](https://nbconvert.readthedocs.io/en/latest/install.html) Instalacja.

## <a name="remove-all-nonessential-code"></a>Usuwanie całego kodu nieoczywalnego

Kod napisany podczas eksperymentów jest przeznaczony tylko do celów eksploracyjne. W związku z tym pierwszym krokiem konwersji kodu eksperymentalnego na kod produkcyjny jest usunięcie tego nieścisłego kodu. Usunięcie nieścisłego kodu również sprawi, że będzie on bardziej konserwowalny. W tej sekcji usuniesz kod z `experimentation/Diabetes Ridge Regression Training.ipynb` notesu. Instrukcje drukowania kształtu i i wywoływania komórki są tylko `X` `y` do `features.describe` eksploracji danych i można je usunąć. Po usunięciu nieścisłego kodu powinien on wyglądać jak `experimentation/Diabetes Ridge Regression Training.ipynb` następujący kod bez znaczników markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg_model.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Refaktoryzacja kodu do funkcji

Po drugie należy refaktoryzować kod Jupyter w funkcje. Refaktoryzacja kodu w funkcje ułatwia testowanie jednostkowe i sprawia, że kod jest łatwiejszy w utrzymaniu. W tej sekcji refaktoryzuje się:

- Notes Diabetes Regression `experimentation/Diabetes Ridge Regression Training.ipynb` Training()
- Notes Ocenianie regresji diabetes Regression Scoring( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refaktoktoryzacja notesu Do trenowania regresji cukrzycowej w funkcje

W `experimentation/Diabetes Ridge Regression Training.ipynb` programie wykonaj następujące czynności:

1. Utwórz funkcję o nazwie `split_data` , aby podzielić ramkę danych na dane testowe i treningowe. Funkcja powinna przyjąć ramce danych jako parametr i `df` zwrócić słownik zawierający klucze i `train` `test` .

    Przenieś kod pod nagłówkiem *Split Data into Training and Validation Sets* (Podział danych na zestawy szkoleniowe i walidacje) do funkcji i zmodyfikuj `split_data` go, aby zwracał `data` obiekt .

1. Utwórz funkcję o nazwie `train_model` , która przyjmuje parametry i zwraca `data` `args` wytrenowany model.

    Przenieś kod pod nagłówkiem Training Model on Training Set (Model trenowania w zestawie *treningowym)* do funkcji i `train_model` zmodyfikuj go, aby zwrócić `reg_model` obiekt . Usuń `args` słownik. Wartości będą pochodzić z `args` parametru .

1. Utwórz funkcję o nazwie , która przyjmuje parametry i , a następnie oblicza model, a następnie zwraca słownik metryk dla `get_model_metrics` `reg_model` `data` wytrenowany model.

    Przenieś kod w obszarze *nagłówka Validate Model on Validation Set* (Weryfikuj model na zestawie walidacji) do funkcji i `get_model_metrics` zmodyfikuj go, aby zwrócić obiekt `metrics` .

Te trzy funkcje powinny być następujące:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Nadal w `experimentation/Diabetes Ridge Regression Training.ipynb` programie wykonaj następujące czynności:

1. Utwórz nową funkcję o nazwie `main` , która nie przyjmuje żadnych parametrów i nic nie zwraca.
1. Przenieś kod pod nagłówkiem "Load Data" (Załaduj dane) do `main` funkcji .
1. Dodaj wywołania dla nowo napisanych funkcji do `main` funkcji :
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Przenieś kod pod nagłówkiem "Save Model" (Zapisz model) do `main` funkcji .

Funkcja `main` powinna wyglądać podobnie do następującego kodu:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

Na tym etapie w notesie nie powinien być pozostały żaden kod, który nie znajduje się w funkcji innej niż instrukcje importu w pierwszej komórce.

Dodaj instrukcje, która wywołuje `main` funkcję .

```python
main()
```

Po refaktoryzacji `experimentation/Diabetes Ridge Regression Training.ipynb` powinien wyglądać jak następujący kod bez znaczników markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refaktoktoryzacja notesu oceniania regresji cukrzycy w funkcje

W `experimentation/Diabetes Ridge Regression Scoring.ipynb` programie wykonaj następujące czynności:

1. Utwórz nową funkcję o nazwie `init` , która nie przyjmuje żadnych parametrów i nie zwraca niczego.
1. Skopiuj kod z nagłówka "Load Model" (Załaduj model) do `init` funkcji .

Funkcja `init` powinna wyglądać podobnie do następującego kodu:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Po utworzeniu funkcji zastąp cały kod pod nagłówkiem `init` "Load Model" jednym wywołaniem funkcji `init` w następujący sposób:

```python
init()
```

W `experimentation/Diabetes Ridge Regression Scoring.ipynb` programie wykonaj następujące czynności:

1. Utwórz nową funkcję o nazwie , która przyjmuje parametry i oraz `run` zwraca słownik wyników w następujący `raw_data` `request_headers` sposób:

    ```python
    {"result": result.tolist()}
    ```

1. Skopiuj kod z nagłówków "Prepare Data" (Przygotowywanie danych) i "Score Data" (Wyeksuj dane) do `run` funkcji .

    Funkcja powinna wyglądać podobnie do następującego kodu (Pamiętaj, aby usunąć instrukcje ustawiające zmienne i , które będą używane później, gdy `run` `raw_data` funkcja zostanie `request_headers` `run` wywołana):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Po utworzeniu funkcji zastąp cały kod w nagłówkach "Prepare Data" (Przygotowywanie danych) i "Score Data" (Wyliczyj dane) `run` następującym kodem:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Poprzedni kod ustawia zmienne i wywołuje funkcję za pomocą funkcji oraz , a `raw_data` `request_header` następnie `run` `raw_data` `request_header` drukuje przewidywania.

Po refaktoryzacji `experimentation/Diabetes Ridge Regression Scoring.ipynb` program powinien wyglądać podobnie do następującego kodu bez znaczników markdown:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Łączenie powiązanych funkcji w plikach języka Python

Po trzecie, powiązane funkcje należy scalić z plikami języka Python, aby ułatwić ponowne używanie kodu. W tej sekcji będziesz tworzyć pliki języka Python dla następujących notesów:

- Notes Diabetes Regression Training (Trenowania regresji `experimentation/Diabetes Ridge Regression Training.ipynb` cukrzycy)
- Notes Do oceniania regresji diabetes w 1999 r. `experimentation/Diabetes Ridge Regression Scoring.ipynb` ()

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Tworzenie pliku języka Python dla notesu Diabetes Regression Training (Trenowania regresji cukrzycy)

Przekonwertuj notes na skrypt wykonywalny, uruchamiając następującą instrukcje w wierszu polecenia, która używa pakietu i `nbconvert` ścieżki `experimentation/Diabetes Ridge Regression Training.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

Po przekonwertowaniu notesu na `train.py` notes usuń wszelkie niepożądane komentarze. Zastąp wywołanie na końcu pliku wywołaniem warunkowym, `main()` jak w poniższym kodzie:

```python
if __name__ == '__main__':
    main()
```

Plik `train.py` powinien wyglądać podobnie do następującego kodu:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py` Można teraz wywołać z terminalu, `python train.py` uruchamiając .
Funkcje z `train.py` funkcji mogą być również wywoływane z innych plików.

Plik `train_aml.py` znaleziony w katalogu w `diabetes_regression/training` repozytorium MLOpsPython wywołuje funkcje zdefiniowane w pliku w kontekście Azure Machine Learning `train.py` eksperymentu. Funkcje mogą być również wywoływane w testach jednostkowych, które zostały uwzględnione w dalszej części tego przewodnika.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Tworzenie pliku języka Python dla notesu Diabetes Regression Scoring (Ocenianie regresji cukrzycy)

Zasłoń notes do skryptu wykonywalnego, uruchamiając następującą instrukcje w wierszu polecenia, która używa pakietu i `nbconvert` ścieżki `experimentation/Diabetes Ridge Regression Scoring.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

Po przekonwertowaniu notesu na `score.py` notes usuń wszelkie niepożądane komentarze. Plik `score.py` powinien wyglądać podobnie do następującego kodu:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

Zmienna `model` musi być globalna, aby była widoczna w całym skrypcie. Dodaj następującą instrukcje na początku `init` funkcji:

```python
global model
```

Po dodaniu poprzedniej instrukcji `init` funkcja powinna wyglądać podobnie do następującego kodu:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Tworzenie testów jednostkowych dla każdego pliku języka Python

Po czwarte, utwórz testy jednostkowe dla funkcji języka Python. Testy jednostkowe chronią kod przed regresjami funkcjonalnymi i ułatwiają ich konserwację. W tej sekcji otworzymy testy jednostkowe dla funkcji w programie `train.py` .

`train.py` Zawiera wiele funkcji, ale w tym samouczku utworzymy tylko jeden test jednostkowy dla funkcji przy użyciu struktury `train_model` Pytest. Pytest nie jest jedyną platformą testowania jednostkowego języka Python, ale jest jedną z najczęściej używanych. Aby uzyskać więcej informacji, odwiedź [witrynę Pytest](https://pytest.org).

Test jednostkowy zwykle zawiera trzy główne akcje:

- Rozmieszczanie obiektu — tworzenie i konfigurowanie niezbędnych obiektów
- Działanie na obiekcie
- Potwierdzanie, co jest oczekiwane

Test jednostkowy wywoła z pewnymi zakodowane danymi i argumentami i zweryfikuje, czy model działał zgodnie z oczekiwaniami, używając wynikowego wytrenowany model do przewidywania i porównywania tego przewidywania z oczekiwaną `train_model` `train_model` wartością.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak przekonwertować eksperyment na kod produkcyjny, zobacz następujące linki, aby uzyskać więcej informacji i wykonać kolejne kroki:

+ [MLOpsPython:](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md)tworzenie potoku CI/CD w celu trenowania, oceniania i wdrażania własnego modelu przy użyciu Azure Pipelines i Azure Machine Learning
+ [Monitorowanie przebiegów i metryk eksperymentów usługi Azure ML](./how-to-log-view-metrics.md)
+ [Monitorowanie i zbieranie danych z punktów końcowych usługi internetowej ML](./how-to-enable-app-insights.md)