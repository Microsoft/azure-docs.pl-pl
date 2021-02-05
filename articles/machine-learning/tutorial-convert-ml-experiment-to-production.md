---
title: Konwertowanie kodu notesu na skrypty języka Python
titleSuffix: Azure Machine Learning
description: Przekształcenie eksperymentalnych notesów uczenia maszynowego do kodu gotowego do produkcji przy użyciu szablonu kodu MLOpsPython. Następnie można testować, wdrażać i automatyzować ten kod.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: cdfeb2fdeefabb0d2d4af2fb63222adda5d023fb
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576028"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Samouczek: konwertowanie eksperymentów ML do kodu w języku Python produkcji

W tym samouczku dowiesz się, jak konwertować notesy Juptyer na skrypty języka Python, aby zapewnić ich testowanie i automatyzację przy użyciu szablonu kodu MLOpsPython i Azure Machine Learning. Zazwyczaj ten proces służy do podejmowania eksperymentów/kodu szkoleniowego z notesu Juptyer i konwertowania go na skrypty języka Python. Te skrypty mogą następnie służyć do testowania i automatyzacji ciągłej integracji/ciągłego wdrażania w środowisku produkcyjnym. 

Projekt uczenia maszynowego wymaga eksperymentu, w którym są testowane z użyciem narzędzi agile, takich jak Jupyter Notebook przy użyciu rzeczywistych zestawów danych. Gdy model jest gotowy do produkcji, kod modelu powinien być umieszczony w repozytorium kodu produkcyjnego. W niektórych przypadkach kod modelu musi być konwertowany do skryptów języka Python, które mają zostać umieszczone w repozytorium kodu produkcyjnego. Ten samouczek obejmuje zalecane podejście do eksportowania kodu eksperymentu do skryptów języka Python.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Czysty nieistotny kod
> * Refaktoryzacja Jupyter Notebook kodu do funkcji
> * Tworzenie skryptów języka Python dla zadań pokrewnych
> * Tworzenie testów jednostkowych

## <a name="prerequisites"></a>Wymagania wstępne

- Wygeneruj [szablon MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) i Użyj `experimentation/Diabetes Ridge Regression Training.ipynb` `experimentation/Diabetes Ridge Regression Scoring.ipynb` notesów i. Te notesy są używane jako przykład konwersji z eksperymentów do środowiska produkcyjnego. Te notesy można znaleźć pod adresem [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation) .
- Zainstaluj system `nbconvert`. Postępuj zgodnie z instrukcjami dotyczącymi instalacji w sekcji __Instalowanie nbconvert__ na stronie [instalacji](https://nbconvert.readthedocs.io/en/latest/install.html) .

## <a name="remove-all-nonessential-code"></a>Usuń cały nieistotny kod

Kod zapisany podczas eksperymentowania jest przeznaczony wyłącznie do celów badawczych. W związku z tym pierwszy krok konwersji kodu doświadczalnego na kod produkcyjny polega na usunięciu tego nieistotnego kodu. Usunięcie nieistotnego kodu spowoduje również zwiększenie utrzymania kodu. W tej sekcji usuniemy kod z `experimentation/Diabetes Ridge Regression Training.ipynb` notesu. Instrukcje drukowania kształtu `X` i `y` i wywołania komórki `features.describe` są przeznaczone tylko do eksploracji danych i można je usunąć. Po usunięciu nieistotnego kodu `experimentation/Diabetes Ridge Regression Training.ipynb` powinien wyglądać podobnie do następującego kodu:

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

Po drugie kod Jupyter musi zostać oddzielony do funkcji. Refaktoryzacja kodu do funkcji sprawia, że testy jednostkowe są łatwiejsze i zwiększają łatwość obsługi kodu. W tej sekcji zostanie Refaktoryzacja:

- Notes uczenia regresji pierścieniowej cukrzycą ( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- Notes oceniania regresji pierścieniowej cukrzycą ( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Refaktoryzacja cukrzycą — Notes szkolenia pierścieniowego do funkcji

W programie `experimentation/Diabetes Ridge Regression Training.ipynb` wykonaj następujące czynności:

1. Utwórz funkcję o nazwie, `split_data` Aby podzielić ramkę danych na testowanie i próbkowanie danych. Funkcja powinna przyjmować element Dataframe `df` jako parametr i zwracać słownik zawierający klucze `train` i `test` .

    Przenieś kod w polu *Podziel dane na nagłówek szkolenia i zestawy walidacji* do `split_data` funkcji i zmodyfikuj go, aby zwracał `data` obiekt.

1. Utwórz funkcję o nazwie `train_model` , która przyjmuje parametry `data` i `args` zwraca szkolony model.

    Przenieś kod objęty *modelem szkoleń dotyczących kursów w ramach szkolenia ustawionego* na `train_model` funkcję i zmodyfikuj go, aby zwracał `reg_model` obiekt. Usunięcie `args` słownika spowoduje, że wartości będą pochodzić z `args` parametru.

1. Utwórz funkcję o nazwie `get_model_metrics` , która pobiera parametry `reg_model` i `data` i szacuje model, a następnie zwraca słownik metryk dla nauczonego modelu.

    Przenieś kod w obszarze *model walidacji w polu Walidacja Ustaw* nagłówek do `get_model_metrics` funkcji i zmodyfikuj go, aby zwracał `metrics` obiekt.

Trzy funkcje powinny być następujące:

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

Nadal w programie `experimentation/Diabetes Ridge Regression Training.ipynb` wykonaj następujące czynności:

1. Utwórz nową funkcję o nazwie `main` , która nie przyjmuje parametrów i nie zwraca żadnej wartości.
1. Przenieś kod w nagłówku "Ładowanie danych" do `main` funkcji.
1. Dodaj wywołania dla nowo pisanych funkcji do `main` funkcji:
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
1. Przenieś kod w nagłówku "Zapisz model" do `main` funkcji.

`main`Funkcja powinna wyglądać podobnie do następującego kodu:

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

Na tym etapie nie powinien być pozostały żaden kod w notesie, który nie znajduje się w funkcji innej niż instrukcja importu w pierwszej komórce.

Dodaj instrukcję, która wywołuje `main` funkcję.

```python
main()
```

Po refaktoryzacji `experimentation/Diabetes Ridge Regression Training.ipynb` powinien wyglądać podobnie do następującego kodu bez promocji:

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

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Refaktoryzacja Cukrzycąa w notesie do funkcji

W programie `experimentation/Diabetes Ridge Regression Scoring.ipynb` wykonaj następujące czynności:

1. Utwórz nową funkcję o nazwie `init` , która nie przyjmuje parametrów i nie zwraca niczego.
1. Skopiuj kod w nagłówku "Ładowanie modelu" do `init` funkcji.

`init`Funkcja powinna wyglądać podobnie do następującego kodu:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Po `init` utworzeniu funkcji Zastąp cały kod pod nagłówkiem "Ładowanie modelu" pojedynczym wywołaniem do `init` w następujący sposób:

```python
init()
```

W programie `experimentation/Diabetes Ridge Regression Scoring.ipynb` wykonaj następujące czynności:

1. Utwórz nową funkcję o nazwie `run` , która przyjmuje `raw_data` i `request_headers` jako parametry i zwraca słownik wyników w następujący sposób:

    ```python
    {"result": result.tolist()}
    ```

1. Skopiuj kod w nagłówkach "przygotowanie danych" i "dane oceny" do `run` funkcji.

    `run`Funkcja powinna wyglądać podobnie do następującego kodu (należy pamiętać, aby usunąć instrukcje ustawiające zmienne `raw_data` i `request_headers` , które będą używane później podczas `run` wywoływania funkcji):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Po `run` utworzeniu funkcji Zastąp cały kod w nagłówkach "przygotowanie danych" i "dane oceny" następującym kodem:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Poprzedni kod ustawia zmienne `raw_data` i `request_header` , wywołuje `run` funkcję z i i `raw_data` `request_header` drukuje przewidywania.

Po refaktoryzacji `experimentation/Diabetes Ridge Regression Scoring.ipynb` powinien wyglądać podobnie do następującego kodu bez promocji:

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

Po trzecie, powiązane funkcje muszą zostać scalone z plikami języka Python, aby lepiej ułatwić ponowne użycie kodu. W tej sekcji utworzysz pliki języka Python dla następujących notesów:

- Notes uczenia regresji pierścieniowej cukrzycą ( `experimentation/Diabetes Ridge Regression Training.ipynb` )
- Notes oceniania regresji pierścieniowej cukrzycą ( `experimentation/Diabetes Ridge Regression Scoring.ipynb` )

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Utwórz plik w języku Python dla notesu szkoleniowego dla regresji cukrzycą

Przekonwertuj Notes na skrypt wykonywalny, uruchamiając następującą instrukcję w wierszu polecenia, który używa `nbconvert` pakietu i ścieżki `experimentation/Diabetes Ridge Regression Training.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Training.ipynb" --to script --output train
```

Po przekonwertowaniu notesu na `train.py` program Usuń niechciane Komentarze. Zastąp wywołanie `main()` na końcu pliku z wywołaniem warunkowym, takim jak poniższy kod:

```python
if __name__ == '__main__':
    main()
```

`train.py`Plik powinien wyglądać podobnie do następującego kodu:

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

`train.py` mogą teraz być wywoływane z terminalu przez uruchomienie `python train.py` .
Funkcje z `train.py` mogą również być wywoływane z innych plików.

`train_aml.py`Plik znaleziony w `diabetes_regression/training` katalogu w repozytorium MLOpsPython wywołuje funkcje zdefiniowane w `train.py` kontekście uruchomienia eksperymentu Azure Machine Learning. Funkcje mogą być również wywoływane w testach jednostkowych, omówione w dalszej części tego przewodnika.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Utwórz plik Python dla notesu oceniania regresji pierścieniowej cukrzycą

Przekonwertować Notes do skryptu wykonywalnego, uruchamiając następującą instrukcję w wierszu polecenia, który używa `nbconvert` pakietu i ścieżki `experimentation/Diabetes Ridge Regression Scoring.ipynb` :

```
jupyter nbconvert "Diabetes Ridge Regression Scoring.ipynb" --to script --output score
```

Po przekonwertowaniu notesu na `score.py` program Usuń niechciane Komentarze. `score.py`Plik powinien wyglądać podobnie do następującego kodu:

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

`model`Zmienna musi być globalna, aby była widoczna w całym skrypcie. Dodaj następującą instrukcję na początku `init` funkcji:

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

## <a name="create-unit-tests-for-each-python-file"></a>Utwórz testy jednostkowe dla każdego pliku języka Python

Czwarty, Utwórz testy jednostkowe dla funkcji języka Python. Testy jednostkowe chronią kod przed regresją funkcjonalną i ułatwiają ich obsługę. W tej sekcji utworzysz testy jednostkowe dla funkcji w programie `train.py` .

`train.py` zawiera wiele funkcji, ale utworzymy tylko jeden test jednostkowy dla `train_model` funkcji przy użyciu struktury Pytest w tym samouczku. Pytest nie jest jedyną strukturą testów jednostkowych w języku Python, ale jest jednym z najczęściej używanych. Aby uzyskać więcej informacji, odwiedź stronę [Pytest](https://pytest.org).

Test jednostkowy zwykle zawiera trzy główne akcje:

- Rozmieść obiekt — tworzenie i Konfigurowanie niezbędnych obiektów
- Działanie na obiekcie
- Potwierdź, co jest oczekiwane

Test jednostkowy będzie wywoływał `train_model` z niektórymi ustalonymi danymi i argumentami, a następnie sprawdza, czy działa `train_model` zgodnie z oczekiwaniami, korzystając z modelu, który jest przeszkolony, aby utworzyć prognozę i porównać ją z oczekiwaną wartością.

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

Teraz, po zrozumieniu sposobu konwersji z eksperymentu na kod produkcyjny, Skorzystaj z poniższych linków, aby uzyskać więcej informacji i następnych kroków:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): Kompiluj potok ciągłej integracji/ciągłego dostarczania, aby przeprowadzić uczenie, oszacować i wdrożyć własny model przy użyciu Azure Pipelines i Azure Machine Learning
+ [Monitoruj uruchomienia eksperymentów i metryki usługi Azure ML](./how-to-track-experiments.md)
+ [Monitorowanie i zbieranie danych z punktów końcowych usługi internetowej ML](./how-to-enable-app-insights.md)