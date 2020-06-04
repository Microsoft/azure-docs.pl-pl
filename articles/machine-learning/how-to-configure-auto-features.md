---
title: Cechowania in AutoML eksperymenty
titleSuffix: Azure Machine Learning
description: Dowiedz się, jakie ustawienia cechowania Azure Machine Learning ofertami i jak Inżynieria funkcji jest obsługiwana w zautomatyzowanych eksperymentach ml.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 87cb5714a1f4b362e2f73879d93f5739f0fc20cf
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84324287"
---
# <a name="featurization-with-automated-machine-learning"></a>Cechowania przy użyciu automatycznej uczenia maszynowego

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym przewodniku dowiesz się, jakie ustawienia cechowania są oferowane i jak dostosować je do [zautomatyzowanych eksperymentów usługi Machine Learning](concept-automated-ml.md).

Inżynieria funkcji to proces polegający na użyciu znajomości domeny danych w celu utworzenia funkcji, które pomagają algorytmom ML uczyć się. W Azure Machine Learning są stosowane techniki skalowania i normalizacji danych w celu ułatwienia inżynierii funkcji. Wspólnie te techniki i inżynieria funkcji są określane jako cechowania w zautomatyzowanym uczeniu maszynowym, AutoML, eksperymentów.

W tym artykule założono, że znasz już sposób konfigurowania eksperymentu AutoML. Aby uzyskać szczegółowe informacje, zobacz następujące artykuły:

* Aby zapoznać się z pierwszym doświadczeniem w kodzie: [Skonfiguruj zautomatyzowane eksperymenty ml przy użyciu zestawu SDK języka Python](how-to-configure-auto-train.md).
* W przypadku małych i nieprawidłowych kodów: [Tworzenie, przeglądanie i wdrażanie zautomatyzowanych modeli uczenia maszynowego za pomocą programu Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md)

## <a name="configure-featurization"></a>Konfigurowanie cechowania

W każdym automatycznym doświadczeniu uczenia maszynowego [Automatyczne skalowanie i techniki normalizacji](#featurization) są domyślnie stosowane do danych. Te techniki skalowania i normalizacji to typy cechowania, które pomagają *określonym* algorytmom, które są wrażliwe na funkcje w różnych skalach. Można jednak również włączyć dodatkowe cechowania, takie jak brakujące wartości, które nie **przypisywania, kodowania** i **transformacji**.

> [!NOTE]
> Zautomatyzowane kroki cechowania uczenia maszynowego (normalizacja funkcji, obsługa brakujących danych, konwertowanie tekstu na liczbowe itp.) staje się częścią modelu źródłowego. Korzystając z modelu dla prognoz, te same kroki cechowania stosowane podczas uczenia są automatycznie stosowane do danych wejściowych.

W przypadku eksperymentów skonfigurowanych z zestawem SDK można włączyć/wyłączyć ustawienie `featurization` i określić cechowania kroki, które powinny być używane dla eksperymentu. Jeśli używasz Azure Machine Learning Studio, zobacz How to enable cechowania [z tymi krokami](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

W poniższej tabeli przedstawiono zaakceptowane ustawienia dla `featurization` [klasy AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

|Konfiguracja cechowania | Opis|
------------- | ------------- |
|**`"featurization": 'auto'`**| Wskazuje, że w ramach przetwarzania wstępnego [guardrails danych i kroki cechowania](#featurization) są wykonywane automatycznie. **Ustawienie domyślne**.|
|**`"featurization": 'off'`**| Wskazuje, że kroki cechowania nie powinny być wykonywane automatycznie.|
|**`"featurization":`&nbsp;`'FeaturizationConfig'`**| Wskazuje dostosowany krok cechowania. [Dowiedz się, jak dostosować cechowania](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatyczne cechowania

Poniższa tabela zawiera podsumowanie technik, które są automatycznie stosowane do danych. Dzieje się tak w przypadku eksperymentów skonfigurowanych za pomocą zestawu SDK lub Studio. Aby wyłączyć to zachowanie, ustaw `"featurization": 'off'` w `AutoMLConfig` obiekcie.

> [!NOTE]
> Jeśli planujesz eksportować modele utworzone przez funkcję automl do [modelu ONNX](concept-onnx.md), w formacie ONNX są obsługiwane tylko opcje cechowania. Dowiedz się więcej o [konwertowaniu modeli na ONNX](concept-automated-ml.md#use-with-onnx). 

|Cechowania &nbsp; kroki| Opis |
| ------------- | ------------- |
|**Porzuć wysoką Kardynalność lub brak funkcji wariancji*** |Porzuć je z zestawów szkoleniowych i walidacji, w tym funkcji ze wszystkimi wartościami, które są takie same, jak w przypadku wszystkich wierszy lub o dużej kardynalności (na przykład skrótów, identyfikatorów lub GUID).|
|**Brak wartości w postaci kalkulacyjne*** |W przypadku funkcji liczbowych wartość jest wartością obliczaną ze średnimi wartościami w kolumnie.<br/><br/>W przypadku funkcji kategorii wartość jest równa liczbie wartości.|
|**Generuj dodatkowe funkcje*** |W przypadku funkcji DateTime: Year, month, Day, Day tygodnia, Day Year, Quarter, Week of Year, Hour, minute, Second.<br/><br/>W przypadku funkcji tekstowych: Częstotliwość okresu oparta na unigrams, bi-gramach i Tri-Character-Grams.|
|**Przekształcanie i kodowanie***|Funkcje liczbowe z kilkoma unikatowymi wartościami są przekształcane w funkcje kategorii.<br/><br/>Jednostronicowe kodowanie jest wykonywane dla niskiej kardynalności kategorii; w celu uzyskania dużej kardynalności kodowanie jedno-gorąca.|
|**Osadzanie wyrazów**|Tekst featurized, który konwertuje wektory tokenów tekstowych na wektory zdania przy użyciu modelu wstępnie przeszkolonego. Wektor osadzania każdego wyrazu w dokumencie jest agregowany w celu utworzenia wektora funkcji dokumentu.|
|**Kodowanie docelowe**|W przypadku funkcji kategorii odwzorowuje każdą kategorię ze średnią wartością docelową dla problemów z regresją oraz z prawdopodobieństwem klasy dla każdej klasy w przypadku problemów z klasyfikacją. Użycie wag opartych na częstotliwościach i w k-krotne sprawdzanie poprawności jest stosowane, aby zmniejszyć liczbę mapowań i szumów spowodowanych przez kategorie danych rozrzedzonych.|
|**Kodowanie obiektu docelowego tekstu**|W przypadku wprowadzania tekstu, skumulowany model liniowy z zbiorem słów jest używany do generowania prawdopodobieństwa każdej klasy.|
|**Waga dowodu (WoE)**|Oblicza WoE jako miarę korelacji kolumn kategorii z kolumną docelową. Jest ona obliczana jako dziennik proporcji prawdopodobieństwa klasy w klasie a. Ten krok powoduje wyjście z jednej kolumny funkcji liczbowej dla każdej klasy i eliminuje konieczność jawnego wyprowadzania brakujących wartości i odstającego traktowania.|
|**Odległość klastra**|Pociąga k-oznacza model klastra dla wszystkich kolumn liczbowych.  Wyprowadzaj nowe funkcje, jedną nową funkcję liczbową na klaster zawierający odległość poszczególnych próbek do centroida każdego klastra.|

## <a name="data-guardrails"></a>Guardrails danych

Guardrails danych ułatwia identyfikowanie potencjalnych problemów dotyczących danych (np. brakujących wartości, [nierównowagi klasy](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)) i ułatwia podejmowanie działań naprawczych w celu uzyskania lepszych wyników. 

Guardrails danych są stosowane 

* **W przypadku eksperymentów zestawu SDK**, gdy parametry `"featurization": 'auto'` lub `validation=auto` są określone w `AutoMLConfig` obiekcie.
* **W przypadku eksperymentów programu Studio**, gdy jest włączona *Funkcja automatycznej cechowania* .  

Możesz przejrzeć guardrails danych związanych z eksperymentem

* Przez ustawienie `show_output=True` podczas przesyłania eksperymentu za pomocą zestawu SDK języka Python.

* W programie Studio na karcie **dane guardrails** w zautomatyzowanym przebiegu ml.

### <a name="data-guardrail-states"></a>Stany Guardrail danych

W guardrails danych zostanie wyświetlony jeden z trzech stanów: **Passed**, **done**lub **alertów**.

|Stan| Opis |
|----|---- |
|**Przeniesione**| Nie wykryto żadnych problemów z danymi i nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika. |
|**Gotowe**| Zmiany zostały zastosowane do Twoich danych. Zachęcamy użytkowników do oceny zautomatyzowanych działań, które zostały wykonane w celu zapewnienia, że zmiany zostaną wyrównane z oczekiwanymi wynikami. |
|**Alerty**| Wykryto problem dotyczący danych, którego nie można rozwiązać. Zachęcamy użytkowników do poprawiania i rozwiązywania problemów.| 

W poniższej tabeli opisano aktualnie obsługiwane guardrails danych oraz skojarzone Stany, które użytkownicy mogą napotkać podczas przesyłania eksperymentu.

Guardrail|Stan|Warunek &nbsp; dla &nbsp; wyzwalacza
---|---|---
**Brak przypisywania wartości funkcji** |*Przeniesione* <br><br><br> *Gotowe*| Nie wykryto żadnych wartości funkcji w danych szkoleniowych. Dowiedz się więcej o [braku przypisywania wartości.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> W danych szkoleniowych zostały wykryte brakujące wartości funkcji.
**Obsługa funkcji wysokiej kardynalności** |*Przeniesione* <br><br><br> *Gotowe*| Dane wejściowe zostały przeanalizowane i nie wykryto żadnych funkcji wysokiej kardynalności. Dowiedz się więcej o [funkcji wykrywania dużej kardynalności.](#automatic-featurization) <br><br> W danych wejściowych zostały wykryte funkcje wysokiej kardynalności i zostały obsłużone.
**Obsługa podziału walidacji** |*Gotowe*| Konfiguracja walidacji została ustawiona na wartość "automatycznej", a dane szkoleniowe zawierają **mniej niż 20 000 wierszy**. <br> Każda iteracja przeszkolonego modelu została zweryfikowana za pomocą weryfikacji krzyżowej. Dowiedz się więcej o [danych sprawdzania poprawności.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) <br><br> Konfiguracja walidacji została ustawiona na wartość "Auto", a dane szkoleniowe zawierały **więcej niż 20 000 wierszy**. <br> Dane wejściowe zostały podzielone na zestaw danych szkoleniowych i zestaw danych walidacji w celu sprawdzenia poprawności modelu.
**Wykrywanie równoważenia klasy** |*Przeniesione* <br><br><br><br><br> *Alerty* | Dane wejściowe zostały przeanalizowane i wszystkie klasy są zrównoważone w danych szkoleniowych. Zestaw danych jest uznawany za zrównoważony, jeśli każda klasa ma dobrą reprezentację w zestawie danych, zgodnie z liczbą i stosunkiem próbek. <br><br><br> Wykryto niezrównoważone klasy w danych wejściowych. Aby naprawić odchylenia modelu, napraw problem z równoważeniem. Dowiedz się więcej o [niezrównoważonych danych.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
**Wykrywanie problemów z pamięcią** |*Przeniesione* <br><br><br><br> *Gotowe* |<br> Przeanalizowane wartości wybranych elementów {Horizon, lag, kroczących} i nie wykryto problemów braku związanych z pamięcią. Dowiedz się więcej na temat [konfiguracji prognozowania](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) szeregów czasowych. <br><br><br>Przeanalizowane zostały wartości wybrane w polu {Horizon, zwłoka, krocząca}, co może spowodować, że w Twoim doświadczeniu zabrakło pamięci. Konfiguracje okna zwłoki lub kroczącej zostały wyłączone.
**Wykrywanie częstotliwości** |*Przeniesione* <br><br><br><br> *Gotowe* |<br> Przeanalizowane serie czasowe i wszystkie punkty danych są wyrównane z wykrytą częstotliwością. <br> <br> Wykryto punkty danych, które nie są wyrównane z wykrytą częstotliwością. Te punkty danych zostały usunięte z zestawu danych. Dowiedz się więcej o [przygotowaniu danych na potrzeby prognozowania szeregów czasowych.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="customize-featurization"></a>Dostosuj cechowania

Możesz dostosować ustawienia cechowania, aby upewnić się, że dane i funkcje używane do uczenia modelu ML powodują odpowiednie przewidywania. 

Aby dostosować featurizations, określ  `"featurization": FeaturizationConfig` w `AutoMLConfig` obiekcie. Jeśli używasz programu Azure Machine Learning Studio dla eksperymentu, zapoznaj się z tematem [jak to zrobić](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Obsługiwane dostosowania obejmują:

|Dostosowywanie|Definicja|
|--|--|
|**Aktualizacja celu kolumny**|Przesłoń typ funkcji dla określonej kolumny.|
|**Aktualizacja parametru Transformer** |Zaktualizuj parametry dla określonej funkcji przekształcania. Obecnie obsługuje program obsługujący (średnia, najczęściej & Media) i HashOneHotEncoder.|
|**Upuszczanie kolumn** |Kolumny do usunięcia z featurized.|
|**Blokuj Transformatory**| Blokuj transformatory, które mają być używane w procesie cechowania.|

Utwórz obiekt FeaturizationConfig przy użyciu wywołań interfejsu API:
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```
## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak skonfigurować zautomatyzowane eksperymenty ML,

    * W przypadku klientów korzystających z kodu: [Skonfiguruj zautomatyzowane eksperymenty ml przy użyciu zestawu SDK Azure Machine Learning](how-to-configure-auto-train.md).
    * Klienci korzystający z małych i niewielkich kodów: [Twórz automatyczne eksperymenty uczenia maszynowego w programie Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Dowiedz się więcej o tym [, jak i gdzie wdrożyć model](how-to-deploy-and-where.md).

* Dowiedz się więcej o tym, [Jak szkolić model regresji z automatycznym uczeniem maszynowym](tutorial-auto-train-models.md) lub [jak korzystać z funkcji automatycznego uczenia maszynowego w ramach zasobu zdalnego](how-to-auto-train-remote.md).
