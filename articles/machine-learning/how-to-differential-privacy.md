---
title: Różnicowa Ochrona prywatności instrukcje-to-SmartNoise (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak stosować różnice w zakresie ochrony prywatności dla Azure Machine Learning modeli przy użyciu bibliotek Open Source SmartNoise.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 01/21/2020
ms.openlocfilehash: 62a002569696da4ef18e7bd967f027eb8247ef65
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681408"
---
# <a name="use-differential-privacy-in-azure-machine-learning-preview"></a>Używanie różnicowej ochrony prywatności w Azure Machine Learning (wersja zapoznawcza)

Dowiedz się, jak stosować różnice w zakresie ochrony prywatności dla Azure Machine Learning modeli przy użyciu bibliotek Open Source SmartNoise języka Python.

Różnicowa Ochrona prywatności jest standardową definicją prywatności. Systemy, które są zgodne z tą definicją prywatności, zapewniają mocne gwarancje dotyczące szerokiego zakresu ataków związanych z odtwarzaniem i ponownym identyfikacją danych, w tym ataków przez źródłami ataków, którzy posiadają informacje pomocnicze. Dowiedz się więcej o tym, [jak działa różnicowa prywatność](./concept-differential-privacy.md).


## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.
- [Python 3](https://www.python.org/downloads/)

## <a name="install-smartnoise-python-libraries"></a>Instalowanie bibliotek SmartNoise Python

### <a name="standalone-installation"></a>Instalacja autonomiczna

Biblioteki są przeznaczone do pracy z rozproszonych klastrów Spark i mogą być instalowane podobnie jak każdy inny pakiet.

W poniższych instrukcjach przyjęto założenie, że `python` `pip` polecenia i są mapowane na `python3` i `pip3` .

Użyj narzędzia PIP, aby zainstalować [pakiety SmartNoise języka Python](https://pypi.org/project/opendp-smartnoise/).

`pip install opendp-smartnoise`

Aby sprawdzić, czy pakiety są zainstalowane, Uruchom wiersz polecenia języka Python i wpisz:

```python
import opendp.smartnoise.core
import opendp.smartnoise.sql
```

Jeśli Importy zakończą się pomyślnie, biblioteki są zainstalowane i gotowe do użycia.

### <a name="docker-image-installation"></a>Instalacja obrazu platformy Docker

Możesz również użyć pakietów SmartNoise z platformą Docker.

Pobierz `opendp/smartnoise` obraz, aby użyć bibliotek wewnątrz kontenera Docker, który zawiera Spark, Jupyter i przykładowy kod.

```sh
docker pull opendp/smartnoise:privacy
```

Po pobraniu obrazu Uruchom serwer Jupyter:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run opendp/smartnoise:privacy
```

Spowoduje to uruchomienie serwera Jupyter na porcie z `8989` `localhost` hasłem `pass@word99` . Przy założeniu, że użyto wiersza polecenia powyżej do uruchomienia kontenera o nazwie `smartnoise-privacy` , można otworzyć Terminal bash na serwerze Jupyter, uruchamiając polecenie:

```sh
docker exec -it smartnoise-run bash
```

Wystąpienie platformy Docker czyści wszystkie Stany po zamknięciu, więc utracisz wszystkie notesy utworzone w uruchomionym wystąpieniu. Aby to naprawić, można powiązać folder lokalny z kontenerem po jego uruchomieniu:

```sh
docker run --rm -p 8989:8989 --name smartnoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/smartnoise:privacy
```

Wszystkie notesy utworzone w folderze *Moje notesy* będą przechowywane w lokalnym systemie plików.

## <a name="perform-data-analysis"></a>Wykonywanie analizy danych

Aby przygotować różnicowe wydanie prywatne, należy wybrać źródło danych, statystykę i niektóre parametry prywatności wskazujące poziom ochrony prywatności.  

Ten przykład odwołuje się do elementu California Public use Data (PUMS), reprezentującego anonimowe rekordy demograficzne obywateli:

```python
import os
import sys
import numpy as np
import opendp.smartnoise.core as sn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

W tym przykładzie obliczamy średnią i wariancję wieku.  Używamy łącznej `epsilon` liczby 1,0 (Epsilon to nasze parametry prywatności, rozkładając nasz budżet ochrony prywatności na dwie ilości, które chcemy obliczyć. Dowiedz się więcej o [metrykach ochrony prywatności](concept-differential-privacy.md#differential-privacy-metrics).

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = sn.dp_variance(data = sn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

Wyniki wyglądają podobnie do poniższych:

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

Istnieje kilka ważnych rzeczy, na które należy zwrócić uwagę na ten przykład.  Najpierw `Analysis` obiekt reprezentuje wykres przetwarzania danych.  W tym przykładzie średnia i Wariancja są obliczane z tego samego węzła źródłowego.  Można jednak dołączyć bardziej złożone wyrażenia, które łączą dane wejściowe z danymi wyjściowymi w dowolny sposób.

Wykres analityczny obejmuje `data_upper` i `data_lower` metadane, określając dolną i górną granicę dla wieku.  Te wartości służą do precyzyjnej kalibracji szumu w celu zapewnienia poufności różnicowej.  Te wartości są również używane w przypadku niektórych elementów odstających lub brakujących wartości.

Na koniec wykres analityczny śledzi łączny czas spędzony przez budżet ochrony prywatności.

Biblioteka służy do redagowania bardziej złożonych wykresów analitycznych, z kilkoma mechanizmami, statystykami i funkcjami narzędzi:

| Statystyki    | Mechanizmów | Narzędzia  |
| ------------- |------------|------------|
| Liczba         | Gaussa   | Rzutowanie       |
| Histogram     | Geometrycznej  | Ustalania poziomu   |
| Średnia          | Laplace    | Przekształcanie   |
| Kwantyle     |            | Filtr     |
| Sum           |            | Przypisywanie |
| WARIANCJA/Kowariancja |      | Przekształcanie  |

Aby uzyskać więcej informacji, zobacz [Notes analizy danych](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/basic_data_analysis.ipynb) .

## <a name="approximate-utility-of-differentially-private-releases"></a>Przybliżone narzędzie różnicowych wersji prywatnych

Ze względu na to, że różnicowa Ochrona prywatności jest przeprowadzana przez kalibrację hałasu, narzędzie wydań może się różnić w zależności od ryzyka dla prywatności.  Ogólnie rzecz biorąc, hałas wymagany do ochrony każdej osoby jest nieznaczny, ponieważ rozmiary próbek rosną duże, ale są one przeciążone w przypadku wydań przeznaczonych dla jednej osoby.  Analitycy mogą przejrzeć informacje o dokładności wydania, aby określić, jak użyteczna jest wersja:

```python
with sn.Analysis() as analysis:
    # load data
    data = sn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = sn.dp_mean(data = sn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

Wynik tej operacji powinien wyglądać podobnie do poniższego:

```text
Age accuracy is: 0.2995732273553991
```

Ten przykład oblicza średnią, jak powyżej, i używa `get_accuracy` funkcji, aby zażądać dokładności o `alpha` 0,05. `alpha`Wartość z 0,05 reprezentuje interwał 95%, w tym wydanej wartości będą należeć do raportowanych granic dokładności o 95% czasu.  W tym przykładzie raportowana dokładność to 0,3, co oznacza, że wydana wartość będzie się znajdować w przedziale od szerokości 0,6, około 95% czasu.  Ta wartość nie jest poprawna, ponieważ nie jest to możliwe jako Słupek błędu, ponieważ wydana wartość będzie się znajdować poza raportowanym zakresem dokładności według stawki określonej przez `alpha` , a wartości spoza zakresu mogą być poza dowolnym kierunkiem.

Analitycy mogą wykonywać zapytania `get_accuracy` dotyczące różnych wartości `alpha` , aby uzyskać wąskie lub szersze interwały zaufania, bez ponoszenia dodatkowych kosztów związanych z ochroną prywatności.

## <a name="generate-a-histogram"></a>Generowanie histogramu

Wbudowana `dp_histogram` Funkcja tworzy zróżnicowane histogramy prywatne na dowolnym z następujących typów danych:

- Zmienna ciągła, w której zestaw cyfr musi być podzielony na pojemniki
- Zmienna logiczna lub dichotomous, która może przyjmować tylko dwie wartości
- Zmienna kategorii, w której różne kategorie są wyliczane jako ciągi

Oto przykład `Analysis` określania pojemników dla histogramu ciągłego zmiennej:

```python
income_edges = list(range(0, 100000, 10000))

with sn.Analysis() as analysis:
    data = sn.Dataset(path = data_path, column_names = var_names)

    income_histogram = sn.dp_histogram(
            sn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Ze względu na to, że osoby te są oddzielone od pojemników histogramu, koszt prywatności jest naliczany tylko raz na histogram, nawet jeśli histogram zawiera wiele pojemników.

Aby uzyskać więcej informacji na temat histogramów, zobacz [Notes histogramów](https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/histograms.ipynb).

## <a name="generate-a-covariance-matrix"></a>Generowanie macierzy kowariancji

SmartNoise oferuje trzy różne funkcje z `dp_covariance` funkcją:

- Kowariancja między dwoma wektorami
- Macierz kowariancji macierzy
- Macierz wzajemnych wariancji pary macierzy

Oto przykład obliczeń skalarnej kowariancji:

```python
with sn.Analysis() as analysis:
    wn_data = sn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = sn.dp_covariance(
      left = sn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = sn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

Aby uzyskać więcej informacji, zobacz [Notes Kowariancja](
https://github.com/opendifferentialprivacy/smartnoise-samples/blob/master/analysis/covariance.ipynb)

## <a name="next-steps"></a>Następne kroki

- Poznaj [przykładowe notesy SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-samples/tree/master/analysis).