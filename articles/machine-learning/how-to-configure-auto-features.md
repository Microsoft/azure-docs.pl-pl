---
title: Cechowania in AutoML eksperymenty
titleSuffix: Azure Machine Learning
description: Dowiedz się, jakie ustawienia cechowania Azure Machine Learning ofertami i jak Inżynieria funkcji jest obsługiwana w zautomatyzowanych eksperymentach ML.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 94595bac2febdef1d3739703f0fa49c9ef15f218
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166624"
---
# <a name="featurization-in-automated-machine-learning"></a>Cechowania w zautomatyzowanym uczeniu maszynowym

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym przewodniku dowiesz się:

- Jakie ustawienia cechowania Azure Machine Learning oferty.
- Jak dostosować te funkcje dla [zautomatyzowanych eksperymentów usługi Machine Learning](concept-automated-ml.md).

*Inżynieria funkcji* to proces używania znajomości domeny danych w celu tworzenia funkcji, które pomagają algorytmom uczenia maszynowego (ml) do lepszego uczenia się. W Azure Machine Learning są stosowane techniki skalowania i normalizacji danych, które ułatwiają inżynierom funkcji. Wspólnie te techniki i inżynieria funkcji są nazywane *cechowania* w zautomatyzowanym uczeniu maszynowym lub *AutoML*.

W tym artykule przyjęto założenie, że już wiesz, jak skonfigurować eksperyment AutoML. Informacje o konfiguracji można znaleźć w następujących artykułach:

- W przypadku środowiska pierwszego kodu: [Skonfiguruj zautomatyzowane eksperymenty ml przy użyciu zestawu SDK Azure Machine Learning dla języka Python](how-to-configure-auto-train.md).
- W przypadku środowiska o małym kodzie lub bez kodu: [Twórz, Przeglądaj i wdrażaj automatyczne modele uczenia maszynowego za pomocą programu Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Konfigurowanie cechowania

W każdym automatycznym doświadczeniu uczenia maszynowego [Automatyczne skalowanie i techniki normalizacji](#featurization) są domyślnie stosowane do danych. Techniki te są typami cechowania, które pomagają *określonym* algorytmom, które są wrażliwe na funkcje w różnych skali. Można jednak również włączyć dodatkowe cechowania, takie jak brakujące wartości, które nie *przypisywania*, *kodowania*i *transformacji*.

> [!NOTE]
> Kroki dla zautomatyzowanej uczenia maszynowego cechowania (takie jak normalizacja funkcji, obsługa brakujących danych lub konwertowanie tekstu na liczbowe) stają się częścią modelu źródłowego. Gdy używasz modelu do prognozowania, te same czynności cechowania, które są stosowane podczas szkolenia, są automatycznie stosowane do danych wejściowych.

W przypadku eksperymentów skonfigurowanych za pomocą zestawu SDK języka Python można włączyć lub wyłączyć ustawienie cechowania oraz dodatkowo określić kroki cechowania, które mają być używane dla eksperymentu. Jeśli używasz programu Azure Machine Learning Studio, zapoznaj się z [instrukcjami, aby włączyć cechowania](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

W poniższej tabeli przedstawiono zaakceptowane ustawienia dla `featurization` [klasy AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Konfiguracja cechowania | Opis|
------------- | ------------- |
|`"featurization": 'auto'`| Określa, że w ramach procesu wstępnego przetwarzania [guardrails danych i cechowania kroki](#featurization) są wykonywane automatycznie. Jest to ustawienie domyślne.|
|`"featurization": 'off'`| Określa, że kroków cechowania nie należy wykonywać automatycznie.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Określa, że mają być używane niestandardowe kroki cechowania. [Dowiedz się, jak dostosować cechowania](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatyczne cechowania

Poniższa tabela zawiera podsumowanie technik, które są automatycznie stosowane do danych. Te techniki są stosowane do eksperymentów skonfigurowanych za pomocą zestawu SDK lub Studio. Aby wyłączyć to zachowanie, ustaw `"featurization": 'off'` w `AutoMLConfig` obiekcie.

> [!NOTE]
> Jeśli planujesz eksportować modele utworzone przez AutoML do [modelu ONNX](concept-onnx.md), w formacie ONNX są obsługiwane tylko opcje cechowania oznaczone gwiazdką ("*"). Dowiedz się więcej o [konwertowaniu modeli na ONNX](concept-automated-ml.md#use-with-onnx).

|Cechowania &nbsp; kroki| Opis |
| ------------- | ------------- |
|**Porzuć wysoką Kardynalność lub brak funkcji wariancji*** |Porzuć te funkcje z poziomu szkoleń i zestawów walidacji. Dotyczy funkcji mających wszystkie brakujące wartości, o tej samej wartości we wszystkich wierszach lub o dużej kardynalności (na przykład skrótów, identyfikatorów lub identyfikatorów GUID).|
|**Brak wartości w postaci kalkulacyjne*** |W przypadku funkcji liczbowych można obliczyć wartości w kolumnie.<br/><br/>W przypadku funkcji kategorii wartość jest równa liczbie wartości.|
|**Generuj dodatkowe funkcje*** |W przypadku funkcji DateTime: Year, month, Day, Day tygodnia, Day Year, Quarter, Week of Year, Hour, minute, Second.<br><br> *W przypadku zadań prognozowania* są tworzone następujące dodatkowe funkcje: rok ISO, półroczny rok, miesiąc kalendarzowy jako ciąg, tydzień, dzień tygodnia jako ciąg, dzień kwartału, dzień roku, am/pm (0 Jeśli godzina jest wcześniejsza niż południe (12 PM), 1 (w przeciwnym razie), AM/PM jako ciąg, godzina dnia (12hr)<br/><br/>W przypadku funkcji tekstowych: Częstotliwość okresu oparta na unigrams, rozgramach i trigrams. Dowiedz się więcej o [tym, jak to zrobić za pomocą Bert.](#bert-integration)|
|**Przekształcanie i kodowanie***|Przekształć funkcje liczbowe, które mają kilka unikatowych wartości w funkcjach kategorii.<br/><br/>Kodowanie jednostronicowe jest używane w przypadku funkcji kategorii o niskiej kardynalności. Kodowanie jednostronicowe jest używane w przypadku funkcji kategorii wysoka Kardynalność.|
|**Osadzanie wyrazów**|Tekst featurized konwertuje wektory tokenów tekstowych na wektory zdania przy użyciu modelu przedniego. Wektor osadzania każdego wyrazu w dokumencie jest agregowany wraz z resztą w celu utworzenia wektora funkcji dokumentu.|
|**Kodowanie docelowe**|W przypadku funkcji kategorii ten krok mapuje każdą kategorię ze średnią wartością docelową dla problemów z regresją oraz do prawdopodobieństwa klasy dla każdej klasy w przypadku problemów z klasyfikacją. Wagi oparte na częstotliwościach i k-zgięcie krzyżowe są stosowane w celu zmniejszenia zamontowania mapowania i szumów spowodowanych przez kategorie danych rozrzedzonych.|
|**Kodowanie obiektu docelowego tekstu**|W przypadku wprowadzania tekstu, skumulowany model liniowy z zbiorem słów jest używany do generowania prawdopodobieństwa każdej klasy.|
|**Waga dowodu (WoE)**|Oblicza WoE jako miarę korelacji kolumn kategorii z kolumną docelową. WoE jest obliczany jako dziennik współczynnika prawdopodobieństwa w klasie a. Ten krok powoduje wygenerowanie jednej kolumny funkcji liczbowej dla każdej klasy i eliminuje konieczność jawnego dodawania brakujących wartości i odstającej funkcjonalności.|
|**Odległość klastra**|Pociąga k-oznacza model klastra dla wszystkich kolumn liczbowych. Tworzy *k* nowe funkcje (jedna nowa funkcja liczbowa na klaster), które zawierają odległość poszczególnych próbek do centroida każdego klastra.|

## <a name="data-guardrails"></a>Guardrails danych

*Guardrails danych* pomaga identyfikować potencjalne problemy związane z danymi (na przykład brakujące wartości lub [niezrównoważone klasy](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). Ułatwiają one także podejmowanie działań naprawczych w celu uzyskania lepszych wyników.

Guardrails danych są stosowane:

- **Dla eksperymentów zestawu SDK**: Kiedy parametry `"featurization": 'auto'` lub `validation=auto` są określone w `AutoMLConfig` obiekcie.
- **Eksperymenty dotyczące programu Studio**: gdy jest włączona funkcja automatycznej cechowania.

Możesz przejrzeć guardrails danych dla eksperymentu:

- Przez ustawienie `show_output=True` podczas przesyłania eksperymentu za pomocą zestawu SDK.

- W programie Studio na karcie **Guardrails danych** w zautomatyzowanym przebiegu ml.

### <a name="data-guardrail-states"></a>Stany Guardrail danych

Guardrails danych wyświetla jeden z trzech stanów:

|Stan| Opis |
|----|---- |
|**Przeniesione**| Nie wykryto żadnych problemów z danymi i nie jest wymagana żadna akcja. |
|**Gotowe**| Zmiany zostały zastosowane do Twoich danych. Zachęcamy do przeglądania działań naprawczych, które AutoML, aby upewnić się, że zmiany są wyrównane z oczekiwanymi wynikami. |
|**Alerty**| Wykryto problem dotyczący danych, ale nie można go rozwiązać. Zachęcamy do poprawiania i rozwiązywania problemów.|

### <a name="supported-data-guardrails"></a>Obsługiwane guardrails danych

W poniższej tabeli opisano aktualnie obsługiwane guardrails danych oraz powiązane Stany, które mogą zostać wyświetlone podczas przesyłania eksperymentu:

Guardrail|Stan|Warunek &nbsp; dla &nbsp; wyzwalacza
---|---|---
**Brak przypisywania wartości funkcji** |Przeniesione <br><br><br> Gotowe| Nie wykryto żadnych wartości funkcji w danych szkoleniowych. Dowiedz się więcej o [przypisywaniu brakujących wartości.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> W danych szkoleniowych wykryto brakujące wartości funkcji i zostały one przypisane.
**Obsługa funkcji wysokiej kardynalności** |Przeniesione <br><br><br> Gotowe| Dane wejściowe zostały przeanalizowane i nie wykryto żadnych funkcji wysokiej kardynalności. <br><br> W danych wejściowych wykryto funkcje wysokiej kardynalności i zostały obsłużone.
**Obsługa podziału walidacji** |Gotowe| Konfiguracja sprawdzania poprawności została ustawiona na `'auto'` , a dane szkoleniowe zawierają *mniej niż 20 000 wierszy*. <br> Każda iteracja przeszkolonego modelu została zweryfikowana za pomocą weryfikacji krzyżowej. Dowiedz się więcej o [danych sprawdzania poprawności](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data). <br><br> Konfiguracja walidacji została ustawiona na `'auto'` , a dane szkoleniowe zawierały *więcej niż 20 000 wierszy*. <br> Dane wejściowe zostały podzielone na zestaw danych szkoleniowych i zestaw danych walidacji w celu sprawdzenia poprawności modelu.
**Wykrywanie równoważenia klasy** |Przeniesione <br><br>Alerty <br><br>Gotowe | Dane wejściowe zostały przeanalizowane i wszystkie klasy są zrównoważone w danych szkoleniowych. Zestaw danych jest uznawany za zrównoważony, jeśli każda klasa ma dobrą reprezentację w zestawie danych, zgodnie z liczbą i stosunkiem próbek. <br><br><br> Wykryto niezrównoważone klasy w danych wejściowych. Aby naprawić odchylenia modelu, napraw problem z równoważeniem. Dowiedz się więcej o [niezrównoważonych danych](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data).<br><br><br> W danych wejściowych wykryto niezrównoważone klasy, a logika czyszczenia została określona w celu zastosowania równoważenia.
**Wykrywanie problemów z pamięcią** |Przeniesione <br><br><br><br> Gotowe |<br> Przeanalizowane wybrane wartości (horyzont, zwłoka, stopniowe okno) i nie wykryto problemów braku pamięci. Dowiedz się więcej na temat [konfiguracji prognozowania](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)szeregów czasowych. <br><br><br>Przeanalizowane zostały wybrane wartości (horyzont, zwłoka, stopniowe okno) i może to spowodować brak pamięci przez eksperyment. Konfiguracje opóźnienia lub przedziału czasu zostały wyłączone.
**Wykrywanie częstotliwości** |Przeniesione <br><br><br><br> Gotowe |<br> Przeanalizowane serie czasowe, a wszystkie punkty danych są wyrównane z wykrytą częstotliwością. <br> <br> Wykryto punkty danych, które nie są wyrównane z wykrytą częstotliwością. Te punkty danych zostały usunięte z zestawu danych. Dowiedz się więcej o [przygotowaniu danych na potrzeby prognozowania szeregów czasowych](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Dostosuj cechowania

Możesz dostosować ustawienia cechowania, aby upewnić się, że dane i funkcje, które są używane do uczenia modelu ML, powodują odpowiednie przewidywania.

Aby dostosować featurizations, określ  `"featurization": FeaturizationConfig` w `AutoMLConfig` obiekcie. Jeśli używasz programu Azure Machine Learning Studio dla eksperymentu, zapoznaj się z [artykułem jak to zrobić](how-to-use-automated-ml-for-ml-models.md#customize-featurization). Aby dostosować cechowania do prognozowania typów zadań, zapoznaj się z [tematem prognozowanie, jak to zrobić](how-to-auto-train-forecast.md#customize-featurization).

Obsługiwane dostosowania obejmują:

|Dostosowywanie|Definicja|
|--|--|
|**Aktualizacja celu kolumny**|Przesłoń automatyczne wykrywanie typu funkcji dla określonej kolumny.|
|**Aktualizacja parametru Transformer** |Zaktualizuj parametry dla określonej funkcji przekształcania. Obecnie obsługuje *program* obsługujący (Oredni, najczęstszy i średni) oraz *HashOneHotEncoder*.|
|**Upuszczanie kolumn** |Określa kolumny do porzucenia z featurized.|
|**Blokuj Transformatory**| Określa Transformatory blokowe, które mają być używane w procesie cechowania.|

Utwórz `FeaturizationConfig` Obiekt przy użyciu wywołań interfejsu API:

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

## <a name="bert-integration"></a>Integracja BERT 
[Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) jest używany w warstwie cechowania zautomatyzowanej ml. W tej warstwie wykrywamy, czy kolumna zawiera tekst wolny lub inne typy danych, takie jak sygnatury czasowe czy proste i cechowanie odpowiednio. W przypadku usługi BERT dostrajamy model przy użyciu etykiet dostarczonych przez użytkownika, a następnie osadzania dokumentów wyjściowych (w przypadku BERT są to ostatni ukryty stan skojarzony z specjalnym tokenem [CLS]) jako funkcje wraz z innymi funkcjami, takimi jak funkcje oparte na znacznikach czasowych (np. dzień tygodnia) lub liczby, które mają wiele typowych zestawów danych. 

Aby włączyć BERT, należy używać obliczeń procesora GPU na potrzeby szkoleń. Jeśli zostanie użyta wartość obliczeniowa procesora CPU, a nie BERT, AutoML spowoduje włączenie BiLSTM DNN featurized. Aby wywoływać BERT, należy ustawić "enable_dnn: true" w automl_settings i użyć obliczeń procesora GPU (np. vm_size = "STANDARD_NC6" lub większego procesora GPU). Przykład można znaleźć [w tym notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb).

AutoML wykonuje następujące czynności w przypadku BERT (należy pamiętać, że w automl_settings dla tych elementów należy ustawić "enable_dnn: true"):

1. Przetwarzanie wstępne, w tym tokenizacji wszystkich kolumn tekstowych (w podsumowaniu cechowania tego modelu zobaczysz transformator "StringCast"). Odwiedź [ten Notes](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb) , aby zobaczyć przykład sposobu tworzenia podsumowania cechowania modelu przy użyciu `get_featurization_summary()` metody.

```python
text_transformations_used = []
for column_group in fitted_model.named_steps['datatransformer'].get_featurization_summary():
    text_transformations_used.extend(column_group['Transformations'])
text_transformations_used
```

2. Łączy wszystkie kolumny tekstowe w pojedynczą kolumnę tekstową, dlatego w modelu końcowym zostanie wyświetlony tekst "StringConcatTransformer". 

> [!NOTE]
> Nasza implementacja BERT ogranicza łączną długość tekstu szkolenia do 128 tokenów. Oznacza to, że wszystkie kolumny tekstowe, gdy są połączone, powinny mieć maksymalnie 128 tokenów. W idealnym przypadku, jeśli istnieją wiele kolumn, każda kolumna powinna zostać oczyszczona w taki sposób, że ten warunek jest spełniony. Na przykład jeśli w danych znajdują się dwie kolumny tekstowe, obie kolumny tekstowe należy oczyścić do 64 tokenów (przy założeniu, że obie kolumny mają być równo reprezentowane w ostatniej połączonej kolumnie tekstowej) przed podawaniem danych do AutoML. W przypadku połączonych kolumn o długości >tokeny 128, warstwa tokenizatora BERT będzie obcinać dane wejściowe do tokenów 128.

3. W kroku czyszczenia funkcji AutoML porównuje BERT z linią bazową (zbiorem funkcji słowa) na próbkę danych i określa, czy BERT da ulepszenia dokładności. Jeśli ustali, że BERT wykonuje lepsze niż linia bazowa, AutoML następnie używa BERT dla cechowania tekstu jako optymalnej strategii cechowania i kontynuuje pracę z featurizingem całych danych. W takim przypadku zobaczysz "PretrainedTextDNNTransformer" w modelu końcowym.

BERT zazwyczaj działa dłużej niż większość innych featurizers. Może być przyspieszyło, zapewniając więcej obliczeń w klastrze. AutoML będzie dystrybuować szkolenia BERT w wielu węzłach, jeśli są dostępne (maksymalnie 8 węzłów). Można to zrobić przez ustawienie [max_concurrent_iterations](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) na wartość większą niż 1. W celu uzyskania lepszej wydajności zalecamy korzystanie z jednostek SKU z funkcjami RDMA (takimi jak "STANDARD_NC24r" lub "STANDARD_NC24rs_V3").

AutoML obecnie obsługuje około 100 języków i w zależności od języka zestawu danych AutoML wybiera odpowiedni model BERT. W przypadku danych niemieckich korzystamy z niemieckiego modelu BERT. W przypadku języka angielskiego używany jest model BERT w języku angielskim. W przypadku wszystkich innych języków używamy wielojęzykowego modelu BERT.

W poniższym kodzie jest wyzwalany niemiecki model BERT, ponieważ język DataSet został określony jako "DEU", kod języka 3 litery dla języka niemieckiego zgodnie z [klasyfikacją ISO](https://iso639-3.sil.org/code/deu):

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak skonfigurować zautomatyzowane eksperymenty ML:

    * W przypadku środowiska pierwszego kodu: [Skonfiguruj zautomatyzowane eksperymenty ml przy użyciu zestawu SDK Azure Machine Learning](how-to-configure-auto-train.md).
    * W przypadku środowiska z niską ilością kodu lub bez kodu: [Utwórz zautomatyzowane eksperymenty ml w Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Dowiedz się więcej o tym [, jak i gdzie wdrożyć model](how-to-deploy-and-where.md).

* Dowiedz się więcej o [sposobie uczenia modelu regresji przy użyciu funkcji automatycznego uczenia maszynowego](tutorial-auto-train-models.md) lub uczenia się przy [użyciu funkcji automatycznego uczenia maszynowego w ramach zasobu zdalnego](how-to-auto-train-remote.md).
