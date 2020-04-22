---
title: Co nowego w wydaniu?
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o najnowszych aktualizacjach usługi Azure Machine Learning oraz zestawach SDK języka Python przygotowujących uczenie maszynowe i dane.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 03/10/2020
ms.openlocfilehash: 2739e960c4bc6d35ac9ad757e80ed620e5dc04f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684640"
---
# <a name="azure-machine-learning-release-notes"></a>Informacje o wersji usługi Azure Machine Learning

W tym artykule dowiedz się więcej o wersjach usługi Azure Machine Learning.  Aby uzyskać pełną zawartość referencyjną sdk, odwiedź główną stronę [**sdk usługi**](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) Azure Machine Learning dla języka Python.

Zobacz [listę znanych problemów,](resource-known-issues.md) aby dowiedzieć się więcej o znanych błędach i obejściach.

## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.3.0

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Dodano dodatkowe dane telemetryczne dotyczące operacji po szkoleniu.
    + Przyspiesza automatyczne szkolenie ARIMA, korzystając z warunkowej sumy kwadratów (CSS) szkolenia dla serii długości dłuższej niż 100. Należy zauważyć, że używana długość jest przechowywana jako stała ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/w klasie TimeSeriesInternal w /src/azureml-automl-core/azureml/automl/core/shared/constants.py
    + Poprawiono rejestrowanie przez użytkowników przebiegów prognozowania, teraz więcej informacji o aktualnie uruchomionej fazie zostanie wyświetlonych w dzienniku
    + Niedozwolone target_rolling_window_size być ustawione na wartości mniejsze niż 2
  + **azureml-automl-runtime**
    + Poprawiono komunikat o błędzie wyświetlany po znalezieniu zduplikowanych znaczników czasu.
    + Niedozwolone target_rolling_window_size być ustawione na wartości mniejsze niż 2.
    + Naprawiono błąd imputacji opóźnienia. Problem był spowodowany niewystarczającą liczbą obserwacji potrzebnych do sezonowego rozkładu serii. Dane "de-sezonowe" są używane do obliczania funkcji częściowej autokorelacji (PACF) w celu określenia długości opóźnienia.
    + Włączone dostosowanie celu kolumny featurization do prognozowania zadań przez featurization config. Numeryczne i kategoryczne jako cel kolumny do prognozowania zadań są teraz obsługiwane.
    + Włączone dostosowanie do dostosowywania featurization kolumny upuszczania do prognozowania zadań przez featurization config.
    + Włączone dostosowywanie imputacji do prognozowania zadań przez featurization config. Przypisanie stałej wartości dla kolumny docelowej i średniej, mediany, most_frequent i przypisania wartości stałej dla danych szkoleniowych są teraz obsługiwane.
  + **azureml-contrib-pipeline-steps**
    + Zaakceptuj nazwy obliczeń ciągów, które mają być przekazywane do ParallelRunConfig
  + **azureml-core**
    +  Dodano interfejs API Environment.clone(new_name) w celu utworzenia kopii obiektu Środowisko
    +  Environment.docker.base_dockerfile akceptuje ścieżkę plików. Jeśli będzie w stanie rozpoznać plik, zawartość zostanie odczytana do base_dockerfile właściwości środowiska
    + Automatyczne resetowanie wzajemnie wykluczające się wartości dla base_image i base_dockerfile, gdy użytkownik ręcznie ustawia wartość w pliku Environment.docker
    +  Zestaw danych: naprawiono niepowodzenie pobierania zestawu danych, jeśli ścieżka danych zawierająca znaki unicode
    +  Zestaw danych: ulepszony mechanizm buforowania instalacji zestawu danych w celu zachowania minimalnego zapotrzebowania na miejsce na dysku w usłudze Azure Machine Learning Compute, co pozwala uniknąć bezużyteczności węzła i spowodować anulowanie zadania
    + Dodano flagę user_managed w RSection, która wskazuje, czy środowisko jest zarządzane przez użytkownika lub przez usługę AzureML.
    + Zestaw danych: dodajemy indeks dla kolumny timeseries podczas uzyskiwania dostępu do zestawu danych timeseries jako pandas dataframes, który jest używany do przyspieszenia dostępu do timeseries na podstawie dostępu do danych.  Wcześniej indeks otrzymał taką samą nazwę jak kolumna sygnatury czasowej, myląc użytkowników, o których jest kolumna rzeczywista sygnatura czasowa i który jest indeksem. Teraz nie nadajemy żadnej konkretnej nazwy indeksu, ponieważ nie powinien on być używany jako kolumna. 
  + **azureml-dataprep**
    + Naprawiono problem z uwierzytelnianiem zestawu danych w suwerennej chmurze
    + Naprawiono `Dataset.to_spark_dataframe` błąd dla zestawów danych utworzonych z magazynów danych Usługi Azure PostgreSQL
  + **azureml-interpret**
    + Dodano globalne wyniki do wizualizacji, jeśli lokalne wartości ważności są rzadkie
    + Zaktualizowano interpretację azureml w celu użycia interpret-community 0.9.*
    + Naprawiono błąd podczas pobierania wyjaśnień, który miał rzadkie dane ewaluacyjne
    + Dodano obsługę rzadkiego formatu obiektu objaśnienia w AutoML
  + **azureml-pipeline-core**
    + Obsługa obliczeń jako celu obliczeniowego w potokach
  + **azureml-train-automl-client**
    + Dodano dodatkowe dane telemetryczne dotyczące operacji po szkoleniu.
    + Naprawiono regresję we wczesnym zatrzymywaniu
    + Przestarzały azureml.dprep.Dataflow jako prawidłowy typ danych wejściowych.
    +  Zmiana domyślnego limitu czasu eksperymentu automl na 6 dni.
  + **azureml-train-automl-runtime**
    + Dodano dodatkowe dane telemetryczne dotyczące operacji po szkoleniu.
    + dodano obsługę rozrzedzony automl e2e
  + **azureml-opendatasets**
    + Dodano dodatkowe dane telemetryczne dla monitora usługi.
    + Włącz frontdoor dla obiektu blob, aby zwiększyć stabilność 
## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.2.0

+ **Fundamentalne zmiany**
  + Obsługa upuść pythona 2.7

+ **Poprawki i ulepszenia błędów**
  + **azure-cli-ml**
    + Dodaje "--subscription-id" `az ml model/computetarget/service` do poleceń w wierszu polecenia
    + Dodawanie pomocy technicznej w zakresie przekazywania vault_url klucza zarządzanego przez klienta(CMK), key_name i key_version wdrożenia usługi ACI
  + **azureml-automl-core** 
    + Włączone niestandardowe przypisanie z stałą wartością zarówno dla zadań prognozowania danych X, jak i y.
    + Naprawiono błąd związany z wyświetlaniem komunikatów o błędach dla użytkownika.    
  + **azureml-automl-runtime**
    + Naprawiono błąd, który powodował prognozowanie w zestawach danych, zawierające ziarna zawierające tylko jeden wiersz
    + Zmniejszono ilość pamięci wymaganą przez zadania prognozowania.
    + Dodano lepsze komunikaty o błędach, jeśli kolumna czasu ma nieprawidłowy format.
    + Włączone niestandardowe przypisanie z stałą wartością zarówno dla zadań prognozowania danych X, jak i y.
  + **azureml-core**
    + Dodaj obsługę ładowania ServicePrincipal ze zmiennych środowiskowych: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID i AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Wprowadzono nowy `support_multi_line` parametr `Dataset.Tabular.from_delimited_files`do :`support_multi_line=False`Domyślnie ( ), wszystkie podziały wierszy, w tym te w wartościach pól cytowanych, będą interpretowane jako podział rekordu. Odczytywanie danych w ten sposób jest szybsze i bardziej zoptymalizowane pod kątem równoległego wykonywania na wielu rdzeniach procesora. Może to jednak spowodować po cichu tworzenie większej liczby rekordów z niewyrównane wartości pól. Należy to ustawić, `True` gdy rozdzielane pliki są znane zawierają podziały wierszy cytowany.
    + Dodano możliwość rejestrowania ADLS Gen2 w wierszu polecenia uczenia maszynowego usługi Azure
    + Zmieniono nazwę parametru "fine_grain_timestamp" na "timestamp" i parametr "coarse_grain_timestamp" na "partition_timestamp" dla metody with_timestamp_columns() w Zestawie danych tabeli, aby lepiej odzwierciedlić użycie parametrów.
    + Zwiększono maksymalną długość nazwy eksperymentu do 255.
  + **azureml-interpret**
    + Zaktualizowano interpretację azureml do interpretowania społeczności 0.7.*
  + **azureml-sdk**
    + Zmiana na zależności z kompatybilną wersją Tilde dla obsługi poprawek w wersji wstępnej i stabilne.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.1.5

+ **Deprecation funkcji**
  + **Python 2.7**
    + Ostatnia wersja do obsługi python 2.7

+ **Fundamentalne zmiany**
  + **Przechowywanie wersji semantycznych 2.0.0**
    + Począwszy od wersji 1.1 Azure ML Python SDK przyjmuje semantyczne przechowywanie wersji 2.0.0. [Przeczytaj więcej tutaj](https://semver.org/). Wszystkie kolejne wersje będą zgodne z nowym schematem numeracji i kontraktem na przechowywanie wersji semantycznych. 

+ **Poprawki i ulepszenia błędów**
  + **azure-cli-ml**
    + Zmień nazwę polecenia interfejsu wiersza polecenia punktu końcowego z 'az ml endpoint aks' na 'az ml endpoint realtime' dla spójności.
    + aktualizowanie instrukcji instalacji interfejsu wiersza polecenia dla stabilnej i eksperymentalnej gałęzi INTERFEJSU WIERSZA POLECENIA
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu wygenerowania zalecenia i zostało udostępnione w rdzeniu sdk.
  + **azureml-automl-core**
    + Włączono wnioskowanie trybu wsadowego (jednokrotne wielokrotne robienie wielu wierszy) dla modeli automl ONNX
    + Poprawiono wykrywanie częstotliwości w zestawach danych, brakowało danych lub zawierało nieregularne punkty danych
    + Dodano możliwość usuwania punktów danych niezgodnych z dominującą częstotliwością.
    + Zmieniono dane wejściowe konstruktora, aby wyświetlić listę opcji, aby zastosować opcje imputacji dla odpowiednich kolumn.
    + Poprawiono rejestrowanie błędów.
  + **azureml-automl-runtime**
    + Naprawiono błąd zrzucanym błędem, jeśli w zestawie testowym pojawiło się ziarno, którego nie było w zestawie treningowym.
    + Usunięto wymóg y_query podczas oceniania usługi prognozowania
    + Naprawiono błąd z prognozowaniem, gdy zestaw danych zawiera krótkie ziarna z długimi przerwami czasowymi.
    + Naprawiono błąd, gdy horyzont automatycznego max jest włączony, a kolumna daty zawiera daty w postaci ciągów. Dodano odpowiednie komunikaty o konwersji i błędach, gdy konwersja do tej pory nie jest możliwa
    + Używanie natywnych NumPy i SciPy do serializacji i deserializacji danych pośrednich dla FileCacheStore (używanego do lokalnych uruchomień automl)
    + Naprawiono błąd, który powodował, że nieudane przebiegi podrzędne mogły utknąć w stanie Uruchamianie.
    + Zwiększona szybkość featurization.
    + Naprawiono kontrolę częstotliwości podczas oceniania, teraz zadania prognozowania nie wymagają ścisłej równoważności częstotliwości między pociągiem a zestawem testów.
    + Zmieniono dane wejściowe konstruktora, aby wyświetlić listę opcji, aby zastosować opcje imputacji dla odpowiednich kolumn.
    + Naprawiono błędy związane z wyborem typu opóźnienia.
    + Naprawiono błąd niesklasyfikowany wywoływany w zestawach danych, mający ziarna z pojedynczym wierszem
    + Naprawiono błąd z powolnością wykrywania częstotliwości.
    + Naprawia błąd w obsłudze wyjątków AutoML, który spowodował, że rzeczywista przyczyna niepowodzenia szkolenia została zastąpiona przez AttributeError.
  + **azureml-cli-wspólne**
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu wygenerowania zalecenia i zostało udostępnione w rdzeniu sdk.
  + **azureml-contrib-mir**
    + Dodaje funkcje w klasie MirWebservice, aby pobrać token dostępu
    + Domyślnie używaj auth tokenu dla usługi MirWebservice podczas wywołania MirWebservice.run() — odświeżanie tylko wtedy, gdy wywołanie zakończy się niepowodzeniem
    + Wdrożenie usługi sieci Web Mir wymaga teraz odpowiedniego narzędzia Skus [Standard_DS2_v2, Standard_F16, Standard_A2_v2] zamiast odpowiednio [Ds2v2, A2v2 i F16].
  + **azureml-contrib-pipeline-steps**
    + Opcjonalny parametr side_inputs dodany do ParallelRunStep. Ten parametr może służyć do instalowania folderu w kontenerze. Obecnie obsługiwane typy to DataReference i PipelineData.
    + Parametry przekazywane w ParallelRunConfig mogą być zastąpione przez przekazywanie parametrów potoku teraz. Nowe parametry potoku obsługiwane aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count i aml_process_count_per_node są już częścią wcześniejszej wersji).
  + **azureml-core**
    + Wdrożone usługi AzureML Webservices `INFO` będą teraz domyślnie rejestrować. Można to kontrolować, `AZUREML_LOG_LEVEL` ustawiając zmienną środowiskową w wdrożonej usłudze.
    + Python SDK używa usługi odnajdywania do używania punktu końcowego "api" zamiast "potoków".
    + Zamiana na nowe trasy we wszystkich połączeniach SDK
    + Zmienia routing wywołań do Usługi Manaagement Do nowej ujednoliconej struktury
      + Upubliczniona metoda aktualizacji obszaru roboczego.
      + Dodano parametr image_build_compute w metodzie aktualizacji obszaru roboczego, aby umożliwić użytkownikowi aktualizowanie obliczeń kompilacji obrazu
    +  Dodano komunikaty o deprecjacji do starego przepływu pracy profilowania. Poprawiono limity procesora i pamięci profilowania
    + Dodano RSection jako część środowiska do uruchamiania zadań R
    +  Dodano sprawdzanie `Dataset.mount` poprawności, aby podnieść błąd, gdy źródło zestawu danych nie jest dostępny lub nie zawiera żadnych danych.
    + Dodano `--grant-workspace-msi-access` jako dodatkowy parametr dla interfejsu wiersza polecenia magazynu danych do rejestrowania kontenera obiektów blob platformy Azure, który pozwoli na zarejestrowanie kontenera obiektów blob, który znajduje się za siecią wirtualną
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu wygenerowania zalecenia i zostało udostępnione w rdzeniu sdk.
    + Naprawiono błąd w aks.py _deploy
    + Sprawdza poprawność integralności modeli przekazywanych, aby uniknąć awarii magazynu dyskretnego.
    + Użytkownik może teraz określić wartość klucza eru podczas ponownego generowania kluczy dla usług sieci web.
    + Naprawiono błąd, który powodował, że wielkie litery nie mogły być używane jako nazwa wejściowa zestawu danych
  + **azureml-defaults**
    + `azureml-dataprep`zostanie zainstalowany w ramach `azureml-defaults`. Nie jest już wymagane ręczne instalowanie dataprep[bezpiecznik] na obiekty docelowe obliczeń do montażu zestawów danych.
  + **azureml-interpret**
    + Zaktualizowano interpretację azureml do interpretowania społeczności 0.6.*
    + Zaktualizowano interpretację azureml w zależności od interpret-community 0.5.0
    + Dodano wyjątki w stylu azureml do azureml-interpret
    + Poprawiono serializację DeepScoringExplainer dla modeli keras
  + **azureml-mlflow**
    + Dodaj obsługę suwerennych chmur do azureml.mlflow
  + **azureml-pipeline-core**
    + Notes oceniania partii potoku używa teraz parallelrunstep
    + Naprawiono błąd, który powodował, że wyniki PythonScriptStep mogły być niepoprawnie ponownie używane pomimo zmiany listy argumentów
    + Dodano możliwość ustawiania typu kolumn podczas wywoływania metod parse_* na`PipelineOutputFileDataset`
  + **azureml-pipeline-kroki**
    + Przeniesiono `AutoMLStep` `azureml-pipeline-steps` do paczki. Przestarzałe `AutoMLStep` `azureml-train-automl-runtime`w obrębie .
    + Dodano przykład dokumentacji dla zestawu danych jako wejście PythonScriptStep
  + **azureml-tensorboard**
    + zaktualizowana tablica azureml-tensorboard do obsługi tensorflow 2.0
    + Pokaż poprawny numer portu podczas korzystania z niestandardowego portu Tensorboard w wystąpieniu obliczeniowym
  + **azureml-train-automl-client**
    + Naprawiono błąd, który powodował, że niektóre pakiety mogły być instalowane w niepoprawnych wersjach na zdalnych działach.
    + Naprawiono błąd FeaturizationConfig, który filtrował niestandardowy config featurization.
  + **azureml-train-automl-runtime**
    + Naprawiono błąd z wykrywaniem częstotliwości w trybie zdalnym
    + Przeniesiono `AutoMLStep` `azureml-pipeline-steps` w pakiecie. Przestarzałe `AutoMLStep` `azureml-train-automl-runtime`w obrębie .
  + **azureml-train-core**
    + Obsługa PyTorch w wersji 1.4 w Estymatorze PyTorch
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.1.2rc0 (wersja wstępna)

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Włączono wnioskowanie trybu wsadowego (jednokrotne wielokrotne robienie wielu wierszy) dla modeli automl ONNX
    + Poprawiono wykrywanie częstotliwości w zestawach danych, brakowało danych lub zawierało nieregularne punkty danych
    + Dodano możliwość usuwania punktów danych niezgodnych z dominującą frequrncy.
  + **azureml-automl-runtime**
    + Naprawiono błąd zrzucanym błędem, jeśli w zestawie testowym pojawiło się ziarno, którego nie było w zestawie treningowym.
    + Usunięto wymóg y_query podczas oceniania usługi prognozowania
  + **azureml-contrib-mir**
    + Dodaje funkcje w klasie MirWebservice, aby pobrać token dostępu
  + **azureml-core**
    + Wdrożone usługi AzureML Webservices `INFO` będą teraz domyślnie rejestrować. Można to kontrolować, `AZUREML_LOG_LEVEL` ustawiając zmienną środowiskową w wdrożonej usłudze.
    + Napraw iterację, `Dataset.get_all` aby zwrócić wszystkie zestawy danych zarejestrowane w obszarze roboczym.
    + Popraw komunikat o błędzie, `path` gdy nieprawidłowy typ jest przekazywany do argumentu interfejsów API tworzenia zestawu danych.
    + Python SDK używa usługi odnajdywania do używania punktu końcowego "api" zamiast "potoków".
    + Zamiana na nowe trasy we wszystkich połączeniach SDK
    + Zmienia routing wywołań do Usługi Manaagement Do nowej ujednoliconej struktury
      + Upubliczniona metoda aktualizacji obszaru roboczego.
      + Dodano parametr image_build_compute w metodzie aktualizacji obszaru roboczego, aby umożliwić użytkownikowi aktualizowanie obliczeń kompilacji obrazu
    +  Dodano komunikaty o deprecjacji do starego przepływu pracy profilowania. Poprawiono limity procesora i pamięci profilowania
  + **azureml-interpret**
    + aktualizacja azureml-interpret do interpretowania społeczności 0.6.*
  + **azureml-mlflow**
    + Dodaj obsługę suwerennych chmur do azureml.mlflow
  + **azureml-pipeline-kroki**
    + Przeniesiono `AutoMLStep` do `azureml-pipeline-steps package`. Przestarzałe `AutoMLStep` `azureml-train-automl-runtime`w obrębie .
  + **azureml-train-automl-client**
    + Naprawiono błąd, który powodował, że niektóre pakiety mogły być instalowane w niepoprawnych wersjach na zdalnych działach.
  + **azureml-train-automl-runtime**
    + Naprawiono błąd z wykrywaniem częstotliwości w trybie zdalnym
    + Przeniesiono `AutoMLStep` do `azureml-pipeline-steps package`. Przestarzałe `AutoMLStep` `azureml-train-automl-runtime`w obrębie .
  + **azureml-train-core**
    + Przeniesiono `AutoMLStep` do `azureml-pipeline-steps package`. Przestarzałe `AutoMLStep` `azureml-train-automl-runtime`w obrębie .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.1.1rc0 (wersja wstępna)

+ **Poprawki i ulepszenia błędów**
  + **azure-cli-ml**
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu wygenerowania zalecenia i zostało udostępnione w rdzeniu sdk.
  + **azureml-automl-core**
    + Poprawiono rejestrowanie błędów.
  + **azureml-automl-runtime**
    + Naprawiono błąd z prognozowaniem, gdy zestaw danych zawiera krótkie ziarna z długimi przerwami czasowymi.
    + Naprawiono błąd, gdy horyzont automatycznego max jest włączony, a kolumna daty zawiera daty w postaci ciągów. Dodaliśmy właściwą konwersję i rozsądny błąd, jeśli konwersja do tej pory nie jest możliwa
    + Używanie natywnych NumPy i SciPy do serializacji i deserializacji danych pośrednich dla FileCacheStore (używanego do lokalnych uruchomień automl)
    + Naprawiono błąd, który powodował, że nieudane przebiegi podrzędne mogły utknąć w stanie Uruchamianie.
  + **azureml-cli-wspólne**
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu wygenerowania zalecenia i zostało udostępnione w rdzeniu sdk.
  + **azureml-core**
    + Dodano `--grant-workspace-msi-access` jako dodatkowy parametr dla interfejsu wiersza polecenia magazynu danych do rejestrowania kontenera obiektów blob platformy Azure, który pozwoli na zarejestrowanie kontenera obiektów blob, który znajduje się za siecią wirtualną
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu wygenerowania zalecenia i zostało udostępnione w rdzeniu sdk.
    + Naprawiono błąd w aks.py _deploy
    + Sprawdza poprawność integralności modeli przekazywanych, aby uniknąć awarii magazynu dyskretnego.
  + **azureml-interpret**
    + dodano wyjątki w stylu azureml do azureml-interpret
    + Poprawiono serializację DeepScoringExplainer dla modeli keras
  + **azureml-pipeline-core**
    + Notes oceniania partii potoku używa teraz parallelrunstep
  + **azureml-pipeline-kroki**
    + Przeniesiono `AutoMLStep` `azureml-pipeline-steps` w pakiecie. Przestarzałe `AutoMLStep` `azureml-train-automl-runtime`w obrębie .
  + **azureml-contrib-pipeline-steps**
    + Opcjonalny parametr side_inputs dodany do ParallelRunStep. Ten parametr może służyć do instalowania folderu w kontenerze. Obecnie obsługiwane typy to DataReference i PipelineData.
  + **azureml-tensorboard**
    + zaktualizowana tablica azureml-tensorboard do obsługi tensorflow 2.0
  + **azureml-train-automl-client**
    + Naprawiono błąd FeaturizationConfig, który filtrował niestandardowy config featurization.
  + **azureml-train-automl-runtime**
    + Przeniesiono `AutoMLStep` `azureml-pipeline-steps` w pakiecie. Przestarzałe `AutoMLStep` `azureml-train-automl-runtime`w obrębie .
  + **azureml-train-core**
    + Obsługa PyTorch w wersji 1.4 w Estymatorze PyTorch
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.1.0rc0 (wersja wstępna)

+ **Fundamentalne zmiany**
  + **Przechowywanie wersji semantycznych 2.0.0**
    + Począwszy od wersji 1.1 Azure ML Python SDK przyjmuje semantyczne przechowywanie wersji 2.0.0. [Przeczytaj więcej tutaj](https://semver.org/). Wszystkie kolejne wersje będą zgodne z nowym schematem numeracji i kontraktem na przechowywanie wersji semantycznych. 
  
+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-runtime**
    + Zwiększona szybkość featurization.
    + Naprawiono kontrolę częstotliwości podczas punktacji, teraz w zadaniach prognozowania nie wymagamy ścisłej równoważności częstotliwości między pociągiem a zestawem testów.
  + **azureml-core**
    + Użytkownik może teraz określić wartość klucza eru podczas ponownego generowania kluczy dla usług sieci web.
  + **azureml-interpret**
    + Zaktualizowano interpretację azureml w zależności od interpret-community 0.5.0
  + **azureml-pipeline-core**
    + Naprawiono błąd, który powodował, że wyniki PythonScriptStep mogły być niepoprawnie ponownie używane pomimo zmiany listy argumentów
  + **azureml-pipeline-kroki**
    + Dodano przykład dokumentacji dla zestawu danych jako wejście PythonScriptStep
  + **azureml-contrib-pipeline-steps**
    + Parametry przekazywane w ParallelRunConfig mogą być zastąpione przez przekazywanie parametrów potoku teraz. Nowe parametry potoku obsługiwane aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count i aml_process_count_per_node są już częścią wcześniejszej wersji).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.85

+ **Nowe funkcje**
  + **azureml-core**
    + Pobierz bieżące ograniczenie użycia i przydziału dla zasobów AmlCompute w danym obszarze roboczym i subskrypcji
  
  + **azureml-contrib-pipeline-steps**
    + Włącz użytkownikowi przekazywanie tabelaryczny zestaw danych jako wynik pośredni z poprzedniego kroku do równoległego kroku

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-runtime**
    + Usunięto wymaganie y_query kolumny w żądaniu do wdrożonej usługi prognozowania. 
    + "y_query" został usunięty z sekcji żądania usługi notebooka Dominick's Orange Juice.
    + Naprawiono błąd uniemożliwiający prognozowanie w wdrożonych modelach, działający na zestawach danych z kolumnami daty i godziny.
    + Dodano współczynnik korelacji Matthews jako metrykę klasyfikacji, zarówno dla klasyfikacji binarnej, jak i wieloklasowej.
  + **azureml-contrib-interpret**
    + Usunięte objaśnienia tekstu z azureml-contrib-interpret jako wyjaśnienie tekstu został przeniesiony do repozytorium tekstu interpret-text, który zostanie wydany wkrótce.
  + **azureml-core**
    + Zestaw danych: użycia zestawu danych plików nie zależy już od numpy i pandas do zainstalowania w python env.
    + Zmieniono LocalWebservice.wait_for_deployment(), aby sprawdzić stan lokalnego kontenera platformy Docker przed próbą ping jego punktu końcowego kondycji, znacznie skracając czas potrzebny do zgłoszenia nieudanego wdrożenia.
    + Naprawiono inicjowanie właściwości wewnętrznej używanej w LocalWebservice.reload(), gdy obiekt usługi jest tworzony na podstawie istniejącego wdrożenia przy użyciu konstruktora LocalWebservice().
    + Edytowany komunikat o błędzie w celu wyjaśnienia.
    + Dodano nową metodę o nazwie get_access_token() do usługi AksWebservice, która zwróci obiekt AksServiceAccessToken, który zawiera token dostępu, odświeżyć po sygnaturze czasowej, wygaśnięcia sygnatury czasowej i typu tokenu. 
    + Przestarzała istniejąca metoda get_token() w U. AksWebservice, ponieważ nowa metoda zwraca wszystkie informacje zwracane przez tę metodę.
    + Zmodyfikowano dane wyjściowe polecenia get-access-token usługi az ml. Zmieniono nazwę tokenu, aby uzyskaćdlaken i refreshBy odświeżyćPo. Dodano właściwości expiryOn i tokenType.
    + Poprawiono get_active_runs
  + **azureml-explain-model**
    + zaktualizowano shap do 0.33.0 i interpret-community do 0.4.*
  + **azureml-interpret**
    + zaktualizowano shap do 0.33.0 i interpret-community do 0.4.*
  + **azureml-train-automl-runtime**
    + Dodano współczynnik korelacji Matthews jako metrykę klasyfikacji, zarówno dla klasyfikacji binarnej, jak i wieloklasowej.
    + Przestarzałe flagi przetwarzania wstępnego z kodu i zastąpione featurization -featurization jest domyślnie włączony

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.83

+ **Nowe funkcje**
  + Zestaw danych: Dodaj `on_error` `out_of_range_datetime` dwie `to_pandas_dataframe` opcje i aby zakończyć się niepowodzeniem, gdy dane mają wartości błędów zamiast wypełniać je `None`.
  + Obszar roboczy: `hbi_workspace` Dodano flagę obszarów roboczych z poufnymi danymi, która umożliwia dalsze szyfrowanie i wyłącza zaawansowaną diagnostykę obszarów roboczych. Dodaliśmy również obsługę przynoszenia własnych kluczy dla skojarzonego wystąpienia `cmk_keyvault` usługi `resource_cmk_uri` Cosmos DB, określając parametry i parametry podczas tworzenia obszaru roboczego, który tworzy wystąpienie usługi Cosmos DB w ramach subskrypcji podczas inicjowania obsługi administracyjnej obszaru roboczego. [Czytaj więcej tutaj.](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-cosmos-db)

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-runtime**
    + Naprawiono regresję, która powodowało wywoływanie typerora podczas uruchamiania autodlapu w wersjach języka Python poniżej 3.5.4.
  + **azureml-core**
    + Naprawiono `datastore.upload_files` błąd, który powodował, `./` że ścieżka względna, która nie zaczynała się od niej, nie była w stanie być używana.
    + Dodano komunikaty o cofnięciu dla wszystkich ścieżek kodowych klasy Image
    + Poprawiono konstrukcję adresu URL zarządzania modelami dla regionu Mooncake.
    + Rozwiązaliśmy problem polegający na tym, że nie można było spakować modeli korzystających z source_dir dla usługi Azure Functions.    
    + Dodano opcję [environment.build_local()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) w celu wypchnięcia obrazu do rejestru kontenerów obszaru roboczego usługi AzureML
    + Zaktualizowano zestaw SDK, aby używać nowej biblioteki tokenów w usłudze Azure Synapse w sposób zgodny z powrotem.
  + **azureml-interpret**
    + Naprawiono błąd, który powodował, że brak był zwracany, gdy nie było żadnych wyjaśnień do pobrania. Teraz zgłasza wyjątek, pasujące zachowanie gdzie indziej.
  + **azureml-pipeline-kroki**
    + Niedozwolone `DatasetConsumptionConfig` `Estimator`przekazywanie `inputs` s `Estimator` do 's `EstimatorStep`parametr, gdy będzie używany w .
  + **azureml-sdk**
    + Dodano klienta AutoML do pakietu azureml-sdk, umożliwiając zdalne automl działa do przesłania bez instalowania pełnego pakietu AutoML.
  + **azureml-train-automl-client**
    + Poprawiono wyrównanie na wyjściu konsoli dla uruchomień automl
    + Naprawiono błąd, który powodował, że na zdalnym porównaniu może być zainstalowana nieprawidłowa wersja pand.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.81

+ **Poprawki i ulepszenia błędów**
  + **azureml-contrib-interpret**
    + odroczyć zależność shap do interpretacji społeczności z azureml-interpretować
  + **azureml-core**
    + Cel obliczeniowy można teraz określić jako parametr odpowiednich obiektów konfiguracji wdrażania. Jest to w szczególności nazwa obiektu docelowego obliczeń do wdrożenia, a nie obiektu SDK.
    + Dodano informacje CreatedBy do obiektów modelu i usługi. Można uzyskać dostęp <var>za pośrednictwem .created_by
    + Naprawiono containerImage.run(), który nie poprawnie konfigurował port HTTP kontenera platformy Docker.
    + Opcja `azureml-dataprep` Opcjonalna dla `az ml dataset register` polecenia cli
    + Naprawiono błąd, który `TabularDataset.to_pandas_dataframe` powodował, że niepoprawnie wracał do alternatywnego czytnika i drukował ostrzeżenie.
  + **azureml-explain-model**
    + odroczyć zależność shap do interpretacji społeczności z azureml-interpretować
  + **azureml-pipeline-core**
    + Dodano nowy `NotebookRunnerStep`krok potoku , aby uruchomić notes lokalny jako krok w potoku.
    + Usunięto przestarzałe funkcje get_all dla opublikowanych elementów, harmonogramów i pipelineendpoints
  + **azureml-train-automl-client**
    + Rozpoczęto deprecjacja data_script jako danych wejściowych do automl.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.79

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Usunięto featurizationConfig do zalogowania
      + Zaktualizowano rejestrowanie, aby rejestrować tylko "auto"/"off"/"customized".
  + **azureml-automl-runtime**
    + Dodano wsparcie dla pand. Seria i pandy. Kategoryczne wykrywanie typu danych kolumny. Wcześniej obsługiwane tylko numpy.ndarray
      + Dodano powiązane zmiany kodu, aby poprawnie obsługiwać klasyczny dtype.
    + Udoskonalono interfejs funkcji prognozy: parametr y_pred został opcjonalny. -Docstrings zostały ulepszone.
  + **azureml-contrib-dataset**
    + Naprawiono błąd, który powodował, że nie można było zamontować oznaczonych zestawów danych.
  + **azureml-core**
    + Poprawka błędu `Environment.from_existing_conda_environment(name, conda_environment_name)`dla . Użytkownik może utworzyć wystąpienie środowiska, które jest dokładną repliką środowiska lokalnego
    + Zmieniono metody zestawów danych `include_boundary=True` związanych z szeregami czasowymi na domyślnie.
  + **azureml-train-automl-client**
    + Naprawiono błąd, który powodował, że wyniki sprawdzania poprawności nie były drukowane, gdy dane wyjściowe pokazu są ustawione na false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.76

+ **Fundamentalne zmiany**
  + Problemy z uaktualnieniem usługi Azureml-Train-AutoML
    + Uaktualnienie do azureml-train-automl>=1.0.76 z azureml-train-automl<1.0.76 może spowodować częściowe instalacje, powodując niektóre importy automl zakończyć się niepowodzeniem. Aby rozwiązać ten problem, można uruchomić https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmdskrypt konfiguracji znajdujący się w pliku . Lub jeśli używasz pip bezpośrednio można:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + lub można odinstalować starą wersję przed uaktualnieniem
      + "pip odinstalować azureml-train-automl"
      + "pip zainstalować azureml-train-automl"

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-runtime**
    + AutoML będzie teraz uwzględniać zarówno klasy true, jak i false podczas obliczania uśrednionych metryk skalarnych dla zadań klasyfikacji binarnej.
    + Przeniesiono kod uczenia maszynowego i szkolenia w usłudze AzureML-AutoML-Core do nowego pakietu AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Podczas `to_pandas_dataframe` wywoływania oznaczonego zestawu danych z opcją pobierania można teraz określić, czy należy zastąpić istniejące pliki, czy nie.
    + Podczas `keep_columns` wywoływania lub `drop_columns` powoduje, że czasochł, etykieta lub kolumna obrazu są usuwane, odpowiednie możliwości zostaną usunięte dla zestawu danych, jak również.
    + Naprawiono błąd z funkcją ładowania pytorch dla zadania wykrywania obiektów.
  + **azureml-contrib-interpret**
    + Usunięto widżet pulpitu nawigacyjnego objaśnień z azureml-contrib-interpret, zmieniony pakiet, aby odwołać się do nowego w interpret_community
    + Zaktualizowana wersja interpret-community do 0.2.0
  + **azureml-core**
    + Poprawa wydajności `workspace.datasets`.
    + Dodano możliwość rejestrowania usługi Azure SQL Database Datastore przy użyciu uwierzytelniania nazwy użytkownika i hasła
    + Napraw ładowanie RunConfigurations ze ścieżek względnych.
    + Podczas `keep_columns` wywoływania lub `drop_columns` powoduje, że kolumna timeseries jest odrzucana, odpowiednie możliwości zostaną usunięte dla zestawu danych, jak również.
  + **azureml-interpret**
    + zaktualizowana wersja interpret-community do 0.2.0
  + **azureml-pipeline-kroki**
    + Udokumentowane obsługiwane wartości `runconfig_pipeline_params` dla kroków potoku uczenia maszynowego platformy Azure.
  + **azureml-pipeline-core**
    + Dodano opcję interfejsu wiersza polecenia, aby pobrać dane wyjściowe w formacie json dla poleceń potoku.
  + **azureml-train-automl**
    + Podziel azureml-train-automl na 2 pakiety, pakiet klienta AzureML-Train-AutoML-Client i pakiet szkoleniowy ML AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Dodano cienkiego klienta do przesyłania eksperymentów AutoML bez konieczności instalowania żadnych zależności uczenia maszynowego lokalnie.
    + Naprawiono rejestrowanie automatycznie wykrywanych opóźnień, rozmiarów okien tocznych i maksymalnych horyzontów w zdalnych biegach.
  + **azureml-train-automl-runtime**
    + Dodano nowy pakiet AutoML do izolowania składników uczenia maszynowego i środowiska wykonawczego od klienta.
  + **azureml-contrib-train-rl**
    + Dodano wsparcie w nauce wzmocnienia w SDK.
    + Dodano obsługę AmlWindowsCompute w RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.74

  + **Funkcje podglądu**
    + **azureml-contrib-dataset**
      + Po zaimportowaniu zestawu danych azureml-contrib można wywołać `Dataset.Labeled.from_json_lines` zamiast `._Labeled` utworzyć zestaw danych z etykietą.
      + Podczas `to_pandas_dataframe` wywoływania oznaczonego zestawu danych z opcją pobierania można teraz określić, czy należy zastąpić istniejące pliki, czy nie.
      + Podczas `keep_columns` wywoływania lub `drop_columns` powoduje, że szeregi czasowe, etykieta lub kolumna obrazu są odrzucane, odpowiednie możliwości zostaną usunięte dla zestawu danych, jak również.
      + Naprawiono problemy z ładowarką `dataset.to_torchvision()`PyTorch podczas wywoływania .

+ **Poprawki i ulepszenia błędów**
  + **azure-cli-ml**
    + Dodano profilowanie modelu do interfejsu wiersza polecenia podglądu.
    + Poprawki zmiany podziału w usłudze Azure Storage powodujące niepowodzenie interfejsu wiersza polecenia usługi AzureML.
    + Dodano typ modułu równoważenia obciążenia do mlc dla typów AKS
  + **azureml-automl-core**
    + Naprawiono błąd, który powodował wykrywanie maksymalnego horyzontu w szeregach czasowych, brakujących wartości i wielu ziaren.
    + Naprawiono błąd podczas generowania podziałów sprawdzania poprawności krzyżowej.
    + Zastąp tę sekcję komunikatem w formacie znaczników, który ma być wyświetlany w informacjach o wersji: -Ulepszona obsługa krótkich ziaren w zestawach danych prognozowania.
    + Naprawiono błąd podczas maskowania niektórych informacji o użytkowniku podczas rejestrowania. -Ulepszone rejestrowanie błędów podczas prognozowania przebiegów.
    + Dodawanie psutil jako zależności conda do automatycznie generowanego pliku wdrożenia yml.
  + **azureml-contrib-mir**
    + Poprawki zmiany podziału w usłudze Azure Storage powodujące niepowodzenie interfejsu wiersza polecenia usługi AzureML.
  + **azureml-core**
    + Naprawia błąd, który powodował, że modele wdrożone w usłudze Azure Functions tworzyły 500s.
    + Naprawiono błąd, który powodował, że plik amlignore nie był stosowany do migawek.
    + Dodano nowy interfejs API amlcompute.get_active_runs, który zwraca generator do uruchamiania i kolejkowane uruchamia na danym amlcompute.
    + Dodano typ modułu równoważenia obciążenia do mlc dla typów AKS.
    + Dodano parametr bool append_prefix do download_files w run.py i download_artifacts_from_prefix w artifacts_client. Ta flaga służy do selektywnego spłaszczenia ścieżki pliku pochodzenia, więc tylko nazwa pliku lub folderu jest dodawana do output_directory
    + Rozwiąż problem `run_config.yml` z deserializacją w przypadku użycia zestawu danych.
    + Podczas `keep_columns` wywoływania lub `drop_columns` powoduje, że kolumna szeregów czasowych jest odrzucana, odpowiednie możliwości zostaną usunięte dla zestawu danych, jak również.
  + **azureml-interpret**
    + Zaktualizowano wersję społeczności interpretacyjnej do wersji 0.1.0.3
  + **azureml-train-automl**
    + Rozwiązaliśmy problem polegający na tym, że automl_step mogły nie drukować problemów z sprawdzaniem poprawności.
    + Naprawiono register_model, aby zakończyć się pomyślnie, nawet jeśli w środowisku modelu brakuje zależności lokalnie.
    + Naprawiono błąd, który powodował, że niektóre zdalne przebiegi nie były włączone docker.
    + Dodaj rejestrowanie wyjątku, który powoduje, że uruchomienie lokalne zakończy się niepowodzeniem przedwcześnie.
  + **azureml-train-core**
    + Należy wziąć pod uwagę resume_from przebiegów w obliczaniu automatycznego dostrajania hiperparametryczne najlepsze przebiegi podrzędne.
  + **azureml-pipeline-core**
    + Stała obsługa parametrów w konstrukcji argumentu rurociągu.
    + Dodano opis potoku i parametr yaml typu kroku.
    + Nowy format yaml dla kroku potoku i dodano ostrzeżenie o deprecation dla starego formatu.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Środowisko internetowe

Strona docelowa obszaru [https://ml.azure.com](https://ml.azure.com) roboczego współpracy w została ulepszona i przemianowana na studio usługi Azure Machine Learning (wersja zapoznawcza).

W studio można szkolić, testować, wdrażać i zarządzać zasobami usługi Azure Machine Learning, takimi jak zestawy danych, potoki, modele, punkty końcowe i inne.

Dostęp do następujących narzędzi do tworzenia stron internetowych ze studia:

| Narzędzie oparte na sieci Web | Opis | Wersja |
|-|-|-|
| Maszyna wirtualna notesu (wersja zapoznawcza) | W pełni zarządzana stacja robocza oparta na chmurze | Podstawowe & Enterprise |
| [Zautomatyzowane uczenie maszynowe](tutorial-first-experiment-automated-ml.md) (wersja zapoznawcza) | Brak środowiska kodu do automatyzacji tworzenia modeli uczenia maszynowego | Enterprise |
| [Projektant](concept-designer.md) (wersja zapoznawcza) | Narzędzie do modelowania uczenia maszynowego przeciągania i upuszczania, wcześniej znane jako projektant | Enterprise |


### <a name="azure-machine-learning-designer-enhancements"></a>Ulepszenia projektanta usługi Azure Machine Learning

+ Dawniej znany jako interfejs wizualny 
+    11 nowych [modułów,](algorithm-module-reference/module-reference.md) w tym rekomendatorów, klasyfikatorów i narzędzi szkoleniowych, w tym inżynierii funkcji, sprawdzania poprawności krzyżowej i przekształcania danych.

### <a name="r-sdk"></a>Zestaw SDK języka R 
 
Analitycy danych i deweloperzy sztucznej inteligencji używają [sdk usługi Azure Machine Learning dla języka R](tutorial-1st-r-experiment.md) do tworzenia i uruchamiania przepływów pracy uczenia maszynowego za pomocą usługi Azure Machine Learning.

Zestaw SDK usługi Azure Machine `reticulate` Learning dla języka R używa pakietu do powiązania z zestawem SDK języka Python. Przez powiązanie bezpośrednio z Pythonem zestaw SDK dla języka R umożliwia dostęp do podstawowych obiektów i metod zaimplementowanych w module SDK języka Python z dowolnego środowiska języka R, które wybierzesz.

Główne możliwości SDK obejmują:

+    Zarządzanie zasobami w chmurze na potrzeby monitorowania, rejestrowania i organizowania eksperymentów uczenia maszynowego.
+    Trenuj modele przy użyciu zasobów w chmurze, w tym szkolenia z modeli przyspieszanych przez procesor GPU.
+    Wdrażanie modeli jako usługi sieci web w przypadku wystąpień kontenerów platformy Azure (ACI) i usługi Azure Kubernetes Service (AKS).

Pełną dokumentację można znaleźć w [witrynie internetowej pakietu.](https://azure.github.io/azureml-sdk-for-r)

### <a name="azure-machine-learning-integration-with-event-grid"></a>Integracja usługi Azure Machine Learning z siatką zdarzeń 

Usługa Azure Machine Learning jest teraz dostawcą zasobów dla usługi Event Grid, można skonfigurować zdarzenia uczenia maszynowego za pośrednictwem witryny Azure portal lub interfejsu wiersza polecenia platformy Azure. Użytkownicy mogą tworzyć zdarzenia do ukończenia uruchamiania, rejestracji modelu, wdrożenia modelu i dryfu danych wykrytych. Zdarzenia te mogą być kierowane do programów obsługi zdarzeń obsługiwanych przez siatkę zdarzeń do użycia. Zobacz [schemat](https://docs.microsoft.com/azure/event-grid/event-schema-machine-learning)zdarzeń uczenia maszynowego, pojęcia i artykuły [samouczka,](https://docs.microsoft.com/azure/machine-learning/concept-event-grid-integration) [aby](https://docs.microsoft.com/azure/machine-learning/how-to-use-event-grid) uzyskać więcej informacji.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.72

+ **Nowe funkcje**
  + Dodano monitory zestawu danych za pośrednictwem pakietu [**azureml-datadrift,**](https://docs.microsoft.com/python/api/azureml-datadrift) co pozwala na monitorowanie zestawów danych szeregów czasowych dla dryfu danych lub innych zmian statystycznych w czasie. Alerty i zdarzenia mogą być wyzwalane, jeśli zostanie wykryty dryf lub zostaną spełnione inne warunki na danych. Szczegółowe informacje można znaleźć w [naszej dokumentacji.](https://aka.ms/datadrift)
  + Ogłaszając dwie nowe wersje (również określane jako zamiennie jednostka SKU) w usłudze Azure Machine Learning. W tej wersji można teraz utworzyć obszar roboczy usługi Basic lub Enterprise Azure Machine Learning. Wszystkie istniejące obszary robocze zostaną domyślnie włączone do wersji Basic i można przejść do witryny Azure portal lub studio, aby uaktualnić obszar roboczy w dowolnym momencie. Obszar roboczy Basic lub Enterprise można utworzyć z witryny Azure Portal. Przeczytaj [naszą dokumentację,](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace) aby dowiedzieć się więcej. Z SDK, wersja obszaru roboczego można określić za pomocą właściwości "sku" obiektu obszaru roboczego.
  + Udostępniliśmy również ulepszenia usługi Azure Machine Learning Compute — można teraz wyświetlać metryki klastrów (takie jak węzły całkowite, uruchomione węzły, całkowity przydział podstawowy) w usłudze Azure Monitor, oprócz wyświetlania dzienników diagnostycznych do debugowania. Ponadto można również wyświetlić aktualnie uruchomione lub umieszczone w kolejce uruchomienia klastra i szczegóły, takie jak adresy IP różnych węzłów w klastrze. Można je wyświetlić w portalu lub przy użyciu odpowiednich funkcji w SDK lub CLI.

  + **Funkcje podglądu**
    + Udostępniamy obsługę w wersji zapoznawczej szyfrowania dysków lokalnego dysku SSD w usłudze Azure Machine Learning Compute. Aby uzyskać białą listę subskrypcji, należy podnieść bilet pomocy technicznej, aby uzyskać białą listę subskrypcji, aby korzystać z tej funkcji.
    + Publiczna wersja zapoznawcza wnioskowania wsadowego usługi Azure Machine Learning. Wnioskowanie wsadowe usługi Azure Machine Learning jest przeznaczone do dużych zadań wnioskowania, które nie są zależne od czasu. Wnioskowanie wsadowe zapewnia ekonomiczne wnioskowanie obliczeń skalowania, z niezrównaną przepływnością dla aplikacji asynchronicznych. Jest zoptymalizowany pod kątem wysokiej przepływności, fire-and-forget wnioskowania w dużych zbiorach danych.
    + [**azureml-contrib-dataset**](https://docs.microsoft.com/python/api/azureml-contrib-dataset)
        + Włączone funkcje dla oznaczonego zestawu danych
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Poprawki i ulepszenia błędów**
  + **azure-cli-ml**
    + CLI obsługuje teraz opakowanie modelu.
    + Dodano zestaw danych CLI. Aby uzyskać więcej informacji:`az ml dataset --help`
    + Dodano obsługę wdrażania i pakowania obsługiwanych modeli (ONNX, scikit-learn i TensorFlow) bez inferencji.
    + Dodano flagę zastępowania dla wdrażania usług (ACI i AKS) w zestawie SDK i CLI. Jeśli jest podana, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, utworzy nową usługę.
    + Modele mogą być rejestrowane w dwóch nowych frameworkach, Onnx i Tensorflow. - Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
  + **azureml-automl-core**
    + Szkolenie iteracji będzie działać w procesie podrzędnym tylko wtedy, gdy są ustawiane ograniczenia środowiska uruchomieniowego.
    + Dodano barierkę do prognozowania zadań, aby sprawdzić, czy określony max_horizon spowoduje problem z pamięcią na danym komputerze, czy nie. Jeśli tak się stanie, zostanie wyświetlony komunikat o barierce.
    + Dodano obsługę złożonych częstotliwości, takich jak 2 lata i 1 miesiąc. -Dodano zrozumiały komunikat o błędzie, jeśli nie można określić częstotliwości.
    + Dodaj azureml-defaults do automatycznie generowane conda env, aby rozwiązać błąd wdrożenia modelu
    + Zezwalaj na konwertowanie danych pośrednich w potoku `AutoMLStep`usługi Azure Machine Learning na tabelaryczny zestaw danych i użycie w pliku .
    + Zaimplementowano aktualizację celu kolumny do przesyłania strumieniowego.
    + Zaimplementowano aktualizację parametrów transformatora dla Imputer i HashOneHotEncoder do przesyłania strumieniowego.
    + Dodano bieżący rozmiar danych i minimalny wymagany rozmiar danych do komunikatów o błędach sprawdzania poprawności.
    + Zaktualizowano minimalny wymagany rozmiar danych dla krzyżowego sprawdzania poprawności, aby zagwarantować co najmniej dwie próbki w każdym krotnie sprawdzania poprawności.
  + **azureml-cli-wspólne**
    + CLI obsługuje teraz opakowanie modelu.
    + Modele mogą być rejestrowane w dwóch nowych frameworkach, Onnx i Tensorflow.
    + Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
  + **azureml-contrib-gbdt**
    + naprawiono kanał zwalniający notebooka
    + Dodano ostrzeżenie dla celu obliczeniowego poza AmlCompute, które nie obsługujemy
    + Dodano LightGMB Estimator do pakietu azureml-contrib-gbdt
  + [**azureml-core**](https://docs.microsoft.com/python/api/azureml-core)
    + CLI obsługuje teraz opakowanie modelu.
    + Dodaj ostrzeżenie o cofnięciu dla przestarzałych interfejsów API zestawu danych. Zobacz powiadomienie o zmianie https://aka.ms/tabular-datasetinterfejsu API zestawu danych pod adresem .
    + Zmień [`Dataset.get_by_id`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) nazwę i wersję rejestracji, jeśli zestaw danych jest zarejestrowany.
    + Napraw błąd, który ScriptRunConfig z zestawem danych jako argument nie może być wielokrotnie używany do przesyłania przebiegu eksperymentu.
    + Zestawy danych pobrane podczas przebiegu będą śledzone i można je zobaczyć [`run.get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--) na stronie szczegółów uruchamiania lub przez wywołanie po zakończeniu uruchamiania.
    + Zezwalaj na konwertowanie danych pośrednich w potoku [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep)usługi Azure Machine Learning na tabelaryczny zestaw danych i użycie w pliku .
    + Dodano obsługę wdrażania i pakowania obsługiwanych modeli (ONNX, scikit-learn i TensorFlow) bez inferencji.
    + Dodano flagę zastępowania dla wdrażania usług (ACI i AKS) w zestawie SDK i CLI. Jeśli jest podana, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, utworzy nową usługę.
    +  Modele mogą być rejestrowane w dwóch nowych frameworkach, Onnx i Tensorflow. Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
    + Dodano nowy magazyn danych dla usługi Azure Database dla mysql. Dodano przykład użycia usługi Azure Database for MySQL w datatransferstep w potokach usługi Azure Machine Learning.
    + Dodano funkcje dodawania i usuwania tagów z eksperymentów Dodano funkcje usuwania tagów z przebiegów
    + Dodano flagę zastępowania dla wdrażania usług (ACI i AKS) w zestawie SDK i CLI. Jeśli jest podana, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, utworzy nową usługę.
  + [**azureml-datadrift**](https://docs.microsoft.com/python/api/azureml-datadrift)
    + Przeniesiony `azureml-contrib-datadrift` z do`azureml-datadrift`
    + Dodano obsługę monitorowania zestawów danych szeregów czasowych dla dryfu i innych miar statystycznych
    + Nowe `create_from_model()` metody `create_from_dataset()` i [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) do klasy. Metoda `create()` zostanie przestarzała.
    + Dostosowania do wizualizacji w pythonie i interfejsie użytkownika w studio usługi Azure Machine Learning.
    + Obsługa cotygodniowego i miesięcznego planowania monitorów, oprócz codziennych monitorów zestawu danych.
    + Obsługa zasypywania metryk monitorowania danych w celu analizowania danych historycznych dla monitorów zestawu danych.
    + Różne poprawki błędów
  + [**azureml-pipeline-core**](https://docs.microsoft.com/python/api/azureml-pipeline-core)
    + azureml-dataprep nie jest już potrzebne do przesłania potoku usługi Azure Machine Learning uruchomić z pliku potoku. `yaml`
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Dodaj azureml-defaults do automatycznie generowane conda env, aby rozwiązać błąd wdrożenia modelu
    + Automatyczne szkolenie zdalne automl zawiera teraz azureml domyślne, aby umożliwić ponowne wykorzystanie env szkolenia do wnioskowania.
  + **azureml-train-core**
    + Dodano obsługę PyTorch 1.3 w [`PyTorch`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch) estymatorze

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interfejs wizualny (wersja zapoznawcza)

+ Interfejs wizualny usługi Azure Machine Learning (wersja zapoznawcza) został przebudowany w celu uruchomienia [w potokach usługi Azure Machine Learning.](concept-ml-pipelines.md) Potoki (wcześniej znane jako eksperymenty) w interfejsie wizualnym są teraz w pełni zintegrowane z podstawowym doświadczeniem usługi Azure Machine Learning.
  + Ujednolicone środowisko zarządzania za pomocą zasobów SDK
  + Przechowywanie wersji i śledzenie modeli interfejsów wizualnych, potoków i punktów końcowych
  + Przeprojektowany interfejs użytkownika
  + Dodano wdrożenie wnioskowania wsadowego
  + Dodano obsługę usługi Azure Kubernetes Service (AKS) dla obiektów docelowych obliczeń wnioskowania
  + Nowy przepływ tworzenia potoku kroków Pythona
  + Nowa [strona docelowa](https://ml.azure.com) dla narzędzi do tworzenia wizualnych

+ **Nowe moduły**
  + Zastosuj operację matematyczną
  + Stosowanie transformacji SQL
  + Wartości klipu
  + Podsumowywanie danych
  + Importowanie z bazy danych SQL

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.69

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Ograniczenie objaśnień modelu do najlepszego uruchomienia, a nie obliczeń objaśnień dla każdego uruchomienia. Wprowadzenie tej zmiany zachowania dla lokalnych, zdalnych i ADB.
    + Dodano obsługę objaśnień modelu na żądanie dla interfejsu użytkownika
    + Dodano psutil jako zależność `automl` i zawarte psutil jako zależność conda w amlcompute.
    + Naprawiono błąd z powodu opóźnień heurystycznych i kroczących rozmiarów okien na zestawach danych prognozowania, z których niektóre serie mogły powodować błędy algebry liniowej
      + Dodano wydruk dla heurystycznie określonych parametrów w przebiegach prognozowania.
  + **azureml-contrib-datadrift**
    + Dodano ochronę podczas tworzenia metryk wyjściowych, jeśli dryf poziomu zestawu danych nie znajduje się w pierwszej sekcji.
  + **azureml-contrib-interpret**
    + azureml-contrib-explain-model package został przemianowany na azureml-contrib-interpret
  + **azureml-core**
    + Dodano interfejs API do wyrejestrowania zestawów danych. `dataset.unregister_all_versions()`
    + azureml-contrib-explain-model pakiet został zmieniony na azureml-contrib-interpret.
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core)**
    + Dodano interfejs API do wyrejestrowania zestawów danych. Dataset. [unregister_all_versions()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Dodano interfejs API zestawu danych, aby sprawdzić czas zmiany danych. `dataset.data_changed_time`.
    + Możliwość korzystania `FileDataset` z `TabularDataset` danych wejściowych `EstimatorStep`i `HyperDriveStep` wprowadzania danych do `PythonScriptStep`, i w potoku usługi Azure Machine Learning
    + `FileDataset.mount` Poprawiono wydajność folderów z dużą liczbą plików
    + Możliwość korzystania z [zestawu danych plików](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset) i zestawów danych [tabelaryczne](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset) jako danych wejściowych do [języka PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)i [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) w potoku usługi Azure Machine Learning.
    + Wydajność zestawu danych plików. [mount()](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) został ulepszony dla folderów z dużą liczbą plików
    + Dodano adres URL do znanych zaleceń dotyczących błędów w szczegółach uruchamiania.
    + Naprawiono błąd w run.get_metrics, w którym żądania nie powiodły się, gdyby bieg miał zbyt wiele ściągnięcia dzieci
    + Naprawiono błąd w [run.get_metrics,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) w którym żądania nie powiodły się, gdyby bieg miał zbyt wiele ściągnięcia dzieci
    + Dodano obsługę uwierzytelniania w klastrze Arcadia.
    + Tworzenie obiektu Eksperyment pobiera lub tworzy eksperyment w obszarze roboczym usługi Azure Machine Learning do śledzenia historii uruchamiania. Identyfikator eksperymentu i czas zarchiwizacji są wypełniane w eksperymencie obiektu podczas tworzenia. Przykład: eksperyment = Eksperyment(obszar roboczy, "Nowy eksperyment") experiment_id = experiment.id archive() i reactivate() to funkcje, które można wywołać w eksperymencie, aby ukryć i przywrócić eksperyment z pokazywania w UX lub zwracanego domyślnie w wywołaniu eksperymentów z listą. Jeśli nowy eksperyment zostanie utworzony o takiej samej nazwie jak eksperyment zarchiwizowany, można zmienić nazwę zarchiwizowanego eksperymentu podczas ponownej aktywacji, przekazując nową nazwę. Może istnieć tylko jeden aktywny eksperyment z daną nazwą. Przykład: eksperyment1 = Eksperyment(obszar roboczy, "Aktywny eksperyment") eksperyment1.archive() # Utwórz nowy aktywny eksperyment o tej samej nazwie co zarchiwizowana. eksperyment2. = Eksperyment(obszar roboczy, "Aktywny eksperyment") experiment1.reactivate(new_name="Poprzedni aktywny eksperyment") Statywna list() w eksperymencie może mieć filtr nazw i filtr ViewType. ViewType wartości są "ACTIVE_ONLY", "ARCHIVED_ONLY" i "ALL" Przykład: archived_experiments = Experiment.list(obszar roboczy, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="Pierwszy eksperyment", view_type="ALL")
    + Obsługa środowiska do wdrażania modelu i aktualizacji usługi
  + **azureml-datadrift**
    + Atrybut show klasy DataDriftDector nie będzie już obsługiwał opcjonalnego argumentu "with_details". Atrybut show będzie prezentować tylko współczynnik dryftu danych i udział dryfu danych kolumn charakterystycznych.
    + DataDriftDetector atrybut "get_output" zmiany zachowania:
      + Parametr wejściowy start_time, end_time są opcjonalne, a nie obowiązkowe;
      + Wprowadzanie określonych start_time i/lub end_time z określonym run_id w tym samym wywoływaniu spowoduje wyjątek błędu wartości, ponieważ wzajemnie się wykluczają
      + Przy wprowadzeniu określonych start_time i/lub end_time zostaną zwrócone tylko wyniki zaplanowanych przebiegów;
      + Parametr "daily_latest_only" jest przestarzały.
    + Obsługa pobierania danych opartych na danych drift wyjść.
  + **azureml-explain-model**
    + Zmienia nazwę pakietu AzureML-explain-model na AzureML-interpret, zachowując stary pakiet dla zgodności z powrotem na razie
    + naprawiono `automl` błąd z surowymi objaśnieniami ustawionymi na zadanie klasyfikacji zamiast regresji domyślnie przy pobieraniu z ExplanationClient
    + Dodaj obsługę `ScoringExplainer` do utworzenia bezpośrednio za pomocą`MimicWrapper`
  + **azureml-pipeline-core**
    + Zwiększona wydajność podczas tworzenia dużych rurociągów
  + **azureml-train-core**
    + Dodano obsługę TensorFlow 2.0 w Estymatorze TensorFlow
  + **azureml-train-automl**
    + Tworzenie obiektu [Eksperyment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment) pobiera lub tworzy eksperyment w obszarze roboczym usługi Azure Machine Learning do śledzenia historii uruchamiania. Identyfikator eksperymentu i czas zarchiwizacji są wypełniane w eksperymencie obiektu podczas tworzenia. Przykład:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [archive()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#archive--) i [reactivate()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) to funkcje, które można wywołać w eksperymencie w celu ukrycia i przywrócenia eksperymentu z pokazywania w UX lub zwracanego domyślnie w eksperymentach z wywołaniem listy. Jeśli nowy eksperyment zostanie utworzony o takiej samej nazwie jak eksperyment zarchiwizowany, można zmienić nazwę zarchiwizowanego eksperymentu podczas ponownej aktywacji, przekazując nową nazwę. Może istnieć tylko jeden aktywny eksperyment z daną nazwą. Przykład:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Metoda statyczna [list()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) w eksperymencie może mieć filtr nazw i filtr ViewType. ViewType wartości są "ACTIVE_ONLY", "ARCHIVED_ONLY" i "ALL". Przykład:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Obsługa przy użyciu środowiska do wdrażania modelu i aktualizacji usługi.
  + **[azureml-datadrift](https://docs.microsoft.com/python/api/azureml-datadrift)**
    + Atrybut show [klasy DataDriftDetector](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) nie będzie już obsługiwał opcjonalnego argumentu "with_details". Atrybut show będzie prezentować tylko współczynnik dryftu danych i udział dryfu danych kolumn charakterystycznych.
    + DataDriftDetector, funkcja [get_output]https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) zmienia zachowanie:
      + Parametr wejściowy start_time, end_time są opcjonalne, a nie obowiązkowe;
      + Wprowadzanie określonych start_time i/lub end_time z określonym run_id w tym samym wywoływaniu spowoduje wyjątek błędu wartości, ponieważ wzajemnie się wykluczają;
      + Przy wprowadzeniu określonych start_time i/lub end_time zostaną zwrócone tylko wyniki zaplanowanych przebiegów;
      + Parametr "daily_latest_only" jest przestarzały.
    + Obsługa pobierania danych opartych na danych drift wyjść.
  + **azureml-explain-model**
    + Zmienia nazwę pakietu AzureML-explain-model na azureml-interpret, zachowując stary pakiet dla zgodności z powrotem na razie.
    + Naprawiono błąd AutoML z surowymi objaśnieniami ustawionymi na zadanie klasyfikacji zamiast regresji domyślnie przy pobieraniu z ExplanationClient.
    + Dodaj obsługę [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py) do utworzenia bezpośrednio za pomocą MimicWrapper
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Poprawiono wydajność tworzenia dużych potoków.
  + **[azureml-train-core](https://docs.microsoft.com/python/api/azureml-train-core)**
    + Dodano obsługę TensorFlow 2.0 w Estymatorze [TensorFlow.](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow)
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Uruchomienie nadrzędne nie zakończy się niepowodzeniem, gdy iteracja instalacji nie powiodła się, ponieważ aranżacja już się nim zajmuje.
    + Dodano obsługę lokalnego docker i local-conda dla eksperymentów AutoML
    + Dodano obsługę lokalnego docker i local-conda dla eksperymentów AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nowe środowisko sieci Web (wersja zapoznawcza) dla obszarów roboczych usługi Azure Machine Learning

Karta Eksperyment w [nowym portalu obszaru roboczego](https://ml.azure.com) została zaktualizowana, aby analitycy danych mogli monitorować eksperymenty w bardziej wydajny sposób. Możesz zapoznać się z następującymi funkcjami:
+ Eksperymentowanie z metadanymi w celu łatwego filtrowania i sortowania listy eksperymentów
+ Uproszczone i wydajne strony szczegółów eksperymentu, które pozwalają wizualizować i porównywać przebiegi
+ Nowy projekt do uruchamiania stron szczegółów, aby zrozumieć i monitorować przebiegi treningowe

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.65

  + **Nowe funkcje**
    + Dodano wyselekcjonowane środowiska. Te środowiska zostały wstępnie skonfigurowane z bibliotekami dla typowych zadań uczenia maszynowego i zostały wstępnie skompilowane i buforowane jako obrazy platformy Docker w celu szybszego wykonania. Są one wyświetlane domyślnie na liście środowiska workspace z prefiksem "AzureML".
    + Dodano wyselekcjonowane środowiska. Te środowiska zostały wstępnie skonfigurowane z bibliotekami dla typowych zadań uczenia maszynowego i zostały wstępnie skompilowane i buforowane jako obrazy platformy Docker w celu szybszego wykonania. Są one wyświetlane domyślnie na liście środowiska [obszaru roboczego](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29)z prefiksem "AzureML".

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Dodano obsługę konwersji ONNX dla ADB i HDI

+ **Funkcje podglądu**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Obsługiwane BERT i BiLSTM jako tekst featurizer (tylko podgląd)
    + Obsługiwane dostosowywanie featurization dla celów kolumnowych i parametrów transformatora (tylko wersja zapoznawcza)
    + Obsługiwane nieprzetworzone objaśnienia, gdy użytkownik włącza objaśnienie modelu podczas szkolenia (tylko wersja zapoznawcza)
    + Dodano proroka do `timeseries` prognozowania jako rurociągu do pociągu (tylko wersja zapoznawcza)

  + **azureml-contrib-datadrift**
    + Pakiety przeniesione z azureml-contrib-datadrift do azureml-datadrift; `contrib` pakiet zostanie usunięty w przyszłej wersji

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Wprowadzono featurizationConfig do AutoMLConfig i AutoMLBaseSettings
    + Wprowadzono featurizationConfig do [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) i AutoMLBaseSettings
      + Zastąp cel kolumny dla featurization z danym typem kolumny i obiektu
      + Zastępowanie parametrów transformatora
    + Dodano komunikat o cofnięciu dla explain_model() i retrieve_model_explanations()
    + Dodano Proroka jako rurociąg do pociągu (tylko podgląd)
    + Dodano komunikat o cofnięciu dla explain_model() i retrieve_model_explanations().
    + Dodano Proroka jako rurociąg do pociągu (tylko podgląd).
    + Dodano obsługę automatycznego wykrywania opóźnień docelowych, rozmiaru okna tocznego i maksymalnego horyzontu. Jeśli jeden z target_lags, target_rolling_window_size lub max_horizon jest ustawiony na "auto", heurystyka zostanie zastosowana do oszacowania wartości odpowiedniego parametru na podstawie danych szkoleniowych.
    + Poprawiono prognozowanie w przypadku, gdy zestaw danych zawiera jedną kolumnę ziarna, to ziarno jest typu liczbowego i istnieje luka między pociągiem a zestawem testowym
    + Naprawiono komunikat o błędzie dotyczący zduplikowanego indeksu w zdalnym przebiegu w zadaniach prognozowania
    + Poprawiono prognozowanie w przypadku, gdy zestaw danych zawiera jedną kolumnę ziarna, to ziarno jest typu liczbowego i istnieje luka między pociągiem a zestawem testów.
    + Naprawiono komunikat o błędzie dotyczący zduplikowanego indeksu w zdalnym przebiegu w zadaniach prognozowania.
    + Dodano barierkę, aby sprawdzić, czy zestaw danych jest niezrównoważony, czy nie. Jeśli tak jest, na konsoli zostanie zapisana wiadomość o barierkach.
  + **azureml-core**
    + Dodano możliwość pobierania adresu URL sygnatury dostępu Współdzielonego do modelowania w magazynie za pośrednictwem obiektu modelu. Przykład: model.get_sas_url()
    + Wprowadzenie `run.get_details()['datasets']` w celu uzyskania zestawów danych skojarzonych z przesłanym przebiegiem
    + Dodaj `Dataset.Tabular.from_json_lines_files` interfejs API, aby utworzyć zestaw tabelaryczne z plików JSON Lines. Aby dowiedzieć się więcej o tych danych tabelaryczne w https://aka.ms/azureml-data plikach JSON Lines na TabularDataset, odwiedź dokumentację.
    + Dodano dodatkowe pola rozmiaru maszyny Wirtualnej (dysk systemu operacyjnego, liczba procesorów graficznych) do funkcji supported_vmsizes ()
    + Dodano dodatkowe pola do funkcji list_nodes (), aby pokazać bieg, prywatny i publiczny adres IP, port itp.
    + Możliwość określenia nowego pola podczas inicjowania obsługi administracyjnej klastra --remotelogin_port_public_access które można ustawić na włączone lub wyłączone w zależności od tego, czy chcesz pozostawić port SSH otwarty lub zamknięty w momencie tworzenia klastra. Jeśli nie określisz, usługa będzie inteligentnie otworzyć lub zamknąć port w zależności od tego, czy wdrażasz klaster wewnątrz sieci wirtualnej.
  + **azureml-explain-model**
  + **[azureml-core](https://docs.microsoft.com/python/api/azureml-core/azureml.core)**
    + Dodano możliwość pobierania adresu URL sygnatury dostępu Współdzielonego do modelowania w magazynie za pośrednictwem obiektu modelu. Przykład: model. [get_sas_url()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Wprowadzenie run. [get_details](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['zestawy danych'], aby uzyskać zestawy danych skojarzone z przesłanym przebiegiem
    + Dodaj `Dataset.Tabular`interfejs API . [from_json_lines_files(),](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) aby utworzyć zestaw tabelaryczny z plików JSON Lines. Aby dowiedzieć się więcej o tych danych tabelaryczne w https://aka.ms/azureml-data plikach JSON Lines na TabularDataset, odwiedź dokumentację.
    + Dodano dodatkowe pola rozmiaru maszyny Wirtualnej (dysk systemu operacyjnego, liczba procesorów graficznych) do funkcji [supported_vmsizes()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Dodano dodatkowe pola do funkcji [list_nodes(),](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) aby pokazać bieg, prywatny i publiczny adres IP, port itp.
    + Możliwość określenia nowego pola podczas [inicjowania obsługi administracyjnej](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) `--remotelogin_port_public_access` klastra, które można ustawić na włączone lub wyłączone w zależności od tego, czy chcesz pozostawić port SSH otwarty lub zamknięty w momencie tworzenia klastra. Jeśli nie określisz, usługa będzie inteligentnie otworzyć lub zamknąć port w zależności od tego, czy wdrażasz klaster wewnątrz sieci wirtualnej.
  + **azureml-explain-model**
    + Ulepszona dokumentacja dla wyników objaśnień w scenariuszu klasyfikacji.
    + Dodano możliwość przesyłania przewidywanych wartości y w objaśnieniu przykładów oceny. Odblokowuje bardziej przydatne wizualizacje.
    + Dodano właściwość explainer do MimicWrapper, aby umożliwić uzyskanie podstawowej MimicExplainer.
  + **azureml-pipeline-core**
    + Dodano notebooka do opisania modułu, modułuVersion i modulestep
  + **azureml-pipeline-kroki**
    + Dodano RScriptStep do obsługi skryptu R uruchamianego za pośrednictwem potoku AML.
    + Poprawiono analizowanie parametrów metadanych w usłudze AzureBatchStep, które powodowało komunikat o błędzie "przypisanie parametru SubscriptionId nie jest określone".
  + **azureml-train-automl**
    + Obsługiwane training_data, validation_data, label_column_name weight_column_name jako format wprowadzania danych
    + Dodano komunikat o cofnięciu dla explain_model() i retrieve_model_explanations()
  + **[azureml-pipeline-core](https://docs.microsoft.com/python/api/azureml-pipeline-core)**
    + Dodano [notes](https://aka.ms/pl-modulestep) do opisania [modułu,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.module(class)) [modułuVersion](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.moduleversion) i [modulestep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-kroki](https://docs.microsoft.com/python/api/azureml-pipeline-steps)**
    + Dodano [RScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) do obsługi skryptu R uruchamianego za pośrednictwem potoku AML.
    + Poprawiono analizowanie parametrów metadanych w [usłudze AzureBatchStep,](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep) które powodowało komunikat o błędzie "przypisanie parametru SubscriptionId nie jest określone".
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Obsługiwane training_data, validation_data, label_column_name weight_column_name jako format wprowadzania danych.
    + Dodano komunikat o cofnięciu dla [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) i [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.62

+ **Nowe funkcje**
  + Wprowadzono `timeseries` cechę na TabularDataset. Ta cecha umożliwia łatwe filtrowanie sygnatury czasowej danych zestaw danych tabular, takich jak biorąc wszystkie dane między zakresem czasu lub najnowszych danych. Aby dowiedzieć `timeseries` się więcej na ten temat cecha na TabularDataset, odwiedź dokumentację https://aka.ms/azureml-data lub https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb przykładowy notes.
  + Włączono szkolenie z tabulardatset i FileDataset. https://aka.ms/dataset-tutorial Odwiedź przykładowy notes.

  + **azureml-train-core**
      + Dodano `Nccl` `Gloo` i wsparcie w estymatorze PyTorch

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Przestarzałe ustawienie AutoML "lag_length" i OpóźnienieTransformator.
    + Poprawiono poprawne sprawdzanie poprawności danych wejściowych, jeśli są one określone w formacie przepływ danych
    + Zmodyfikowano fit_pipeline.py, aby wygenerować wykres json i przesłać do artefaktów.
    + Renderowane wykres w `userrun` `Cytoscape`obszarze za pomocą .
  + **azureml-core**
    + Ponownie przeanalizowano obsługę wyjątków w kodzie ADB i wprowadzać zmiany zgodnie z nową obsługą błędów
    + Dodano automatyczne uwierzytelnianie MSI dla maszyn wirtualnych notesu.
    + Naprawia błąd, w którym uszkodzone lub puste modele mogą zostać przekazane z powodu nieudanych ponownych prób.
    + Naprawiono błąd, który powodował, `DataReference` że nazwa zmieniała się po `DataReference` zmianie trybu (np. podczas wywoływania `as_upload`, `as_download`lub `as_mount`).
    + Zrób `mount_point` `target_path` i `FileDataset.mount` opcjonalnie dla i `FileDataset.download`.
    + Wyjątek, że kolumna sygnatury czasowej nie można odnaleźć zostanie wyrzucona, jeśli czas seriali związanych interfejsu API jest wywoływana bez kolumny fine sygnatury czasowej przypisane lub przypisane kolumny sygnatury czasowej są usuwane.
    + Kolumny szeregowe czasowe powinny być przypisane z kolumną, której typem jest Data, w przeciwnym razie oczekiwany jest wyjątek
    + Kolumna czasowa, przypisująca interfejs API "with_timestamp_columns", może przyjąć nazwę kolumny z określoną sygnaturą czasową Brak/grubostronną sygnaturę czasową, która wyczyści wcześniej przypisane kolumny sygnatury czasowej.
    + Wyjątek zostanie wyrzucony, gdy albo gruboziarniste ziarnem lub drobnoziarnista kolumna sygnatury czasowej zostanie upuszczona ze wskazaniem dla użytkownika, że upuszczenie może być wykonane po wykluczeniu kolumny sygnatury czasowej na liście upuszczania lub wywołania with_time_stamp z wartością Brak, aby zwolnić kolumny sygnatury czasowej
    + Wyjątek zostanie wyrzucony, gdy kolumna z gruboziarnistym lub drobnoziarnista kolumna sygnatury czasowej nie zostanie uwzględniona na liście kolumn keep ze wskazaniem dla użytkownika, że zachowanie można wykonać po uwzględnieniu kolumny sygnatury czasowej na liście kolumn keep lub wywołaniu with_time_stamp z wartością Brak, aby zwolnić kolumny sygnatury czasowej.
    + Dodano rejestrowanie rozmiaru zarejestrowanego modelu.
  + **azureml-explain-model**
    + Naprawiono ostrzeżenie wydrukowane na konsoli, gdy nie zainstalowano pakietu python "pakującego": "Używając starszej niż obsługiwana wersja lightgbm, uaktualnij ją do wersji większej niż 2.2.1"
    + Poprawiono wyjaśnienie modelu pobierania z dzieleniem na fragmenty dla globalnych wyjaśnień z wieloma funkcjami
    + Poprawiono objaśnienie mimów brakujące przykłady inicjowania na wyjściu wyjaśnienie
    + Naprawiono błąd niezmienny na właściwości zestawu podczas przesyłania z klientem objaśnienia przy użyciu dwóch różnych typów modeli
    + Dodano get_raw param do punktacji explainer .explain(), więc jeden punktacji explainer może zwrócić zarówno inżynierii i surowe wartości.
  + **azureml-train-automl**
    + Wprowadzono publiczne interfejsy API z AutoML `automl` do obsługi wyjaśnień z explain SDK — nowszy sposób obsługi wyjaśnień AutoML przez oddzielenie automl featurization i wyjaśnić SDK — zintegrowane nieprzetworzone objaśnienia z azureml wyjaśnić SDK dla modeli AutoML.
    + Usuwanie azureml-defaults ze zdalnych środowisk szkoleniowych.
    + Zmieniono domyślną lokalizację magazynu pamięci podręcznej z FileCacheStore na jeden na AzureFileCacheStore jeden dla automl na ścieżce kodu Usługi Azure Databricks.
    + Poprawiono poprawne sprawdzanie poprawności danych wejściowych, jeśli są one określone w formacie przepływ danych
  + **azureml-train-core**
    + Cofnięte source_directory_data_store deprecation.
    + Dodano możliwość zastąpienia wersji pakietu zainstalowanego azureml.
    + Dodano obsługę dockerfile w `environment_definition` parametrze w estymatorach.
    + Uproszczone rozproszone parametry treningowe w estymatorach.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nowe środowisko sieci Web (wersja zapoznawcza) dla obszarów roboczych usługi Azure Machine Learning
Nowe środowisko internetowe umożliwia analitykom danych i inżynierom danych ukończenie kompleksowego cyklu życia uczenia maszynowego od przygotowywania i wizualizacji danych do szkoleń i wdrażania modeli w jednej lokalizacji.

![Interfejs użytkownika obszaru roboczego usługi Azure Machine Learning (wersja zapoznawcza)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Najważniejsze cechy programu:**

Korzystając z tego nowego interfejsu usługi Azure Machine Learning, możesz teraz:
+ Zarządzanie notesami lub łączenie się z jupyterem
+ [Uruchamianie zautomatyzowanych eksperymentów ml](tutorial-first-experiment-automated-ml.md)
+ [Tworzenie zestawów danych z plików lokalnych, magazynów danych, & plików internetowych](how-to-create-register-datasets.md)
+ Eksploruj & przygotowywać zestawy danych do tworzenia modelu
+ Monitorowanie dryfu danych dla swoich modeli
+ Wyświetlanie najnowszych zasobów z pulpitu nawigacyjnego

W tej wersji obsługiwane są następujące przeglądarki: Chrome, Firefox, Safari i Microsoft Edge Preview.

**Znane problemy:**

1. Odśwież przeglądarkę, jeśli zobaczysz "Coś poszło nie tak! Błąd ładowania plików fragmentów" podczas wdrażania jest w toku.

1. Nie można usunąć ani zmienić nazwy pliku w notesach i plikach. Podczas publicznego podglądu można użyć interfejsu użytkownika jupyter lub terminalu w maszynie wirtualnej notesu do wykonywania operacji aktualizacji plików. Ponieważ jest to zamontowany sieciowy system plików, wszystkie zmiany wprowadzone na maszynie Wirtualnej notesu są natychmiast odzwierciedlane w obszarze roboczym notesu.

1. Aby SSH do maszyny Wirtualnej notesu:
   1. Znajdź klucze SSH, które zostały utworzone podczas konfigurowania maszyny Wirtualnej. Można też znaleźć klucze w obszarze roboczym usługi Azure Machine Learning > otwórz kartę Oblicza > znajdź maszynę wirtualną notesu na liście > otworzyć jej właściwości: skopiuj klucze z okna dialogowego.
   1. Zaimportuj te publiczne i prywatne klucze SSH do komputera lokalnego.
   1. Użyj ich do SSH do maszyny Wirtualnej notesu.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.60

+ **Nowe funkcje**
  + Wprowadzono zestaw danych filedataset, który odwołuje się do pojedynczych lub wielu plików w magazynach danych lub publicznych adresach URL. Pliki mogą mieć dowolny format. Zestaw danych filedaset umożliwia pobieranie lub montowanie plików na komputerze. Aby dowiedzieć się więcej o https://aka.ms/file-datasetFileDataset, odwiedź stronę .
  + Dodano obsługę yaml potoku dla języka PythonScript Step, Adla Step, Databricks Step, DataTransferStep i AzureBatch Step

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + AutoArima jest teraz sugerowanym potokiem tylko do podglądu.
    + Ulepszone raportowanie błędów w prognozowaniu.
    + Usprawniono rejestrowanie przy użyciu wyjątków niestandardowych zamiast ogólnych w zadaniach prognozowania.
    + Usunięto czek na max_concurrent_iterations jest mniejsza niż całkowita liczba iteracji.
    + Modele AutoML zwracają teraz automlexceptions
    + Ta wersja zwiększa wydajność wykonywania zautomatyzowanych uruchomień lokalnych uczenia maszynowego.
  + **azureml-core**
    + Wprowadzenie dataset.get_all(workspace), który zwraca słownik `TabularDataset` i `FileDataset` obiekty wpisane przez ich nazwę rejestracji.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Wprowadzenie `parition_format` jako `Dataset.Tabular.from_delimited_files` argument `Dataset.Tabular.from_parquet.files`i . Informacje o partycji każdej ścieżki danych zostaną wyodrębnione do kolumn na podstawie określonego formatu. "{column_name}" tworzy kolumnę ciągu, a "{column_name:yyy/MM/dd/HH/mm/ss}" tworzy kolumnę datetime, w której "yyyy", "MM", "dd", "HH", "mm" i "ss" są używane do wyodrębniania roku, miesiąca, dnia, godziny, minuty i sekundy dla typu datetime. partition_format powinien rozpoczynać się od pozycji pierwszego klucza partycji do końca ścieżki pliku. Na przykład, biorąc pod uwagę ścieżkę '.. /USA/2019/01/01/data.csv' gdzie partycja jest według kraju i godziny, partition_format="/{Kraj}/{PartitionDate:yyy/MM/dd}/data.csv' tworzy kolumnę ciągów "Kraj" o wartości "USA" i kolumnę datatime "PartitionDate" o wartości "2019-01-01".
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Wprowadzenie `partition_format` jako `Dataset.Tabular.from_delimited_files` argument `Dataset.Tabular.from_parquet.files`i . Informacje o partycji każdej ścieżki danych zostaną wyodrębnione do kolumn na podstawie określonego formatu. "{column_name}" tworzy kolumnę ciągu, a "{column_name:yyy/MM/dd/HH/mm/ss}" tworzy kolumnę datetime, w której "yyyy", "MM", "dd", "HH", "mm" i "ss" są używane do wyodrębniania roku, miesiąca, dnia, godziny, minuty i sekundy dla typu datetime. partition_format powinien rozpoczynać się od pozycji pierwszego klucza partycji do końca ścieżki pliku. Na przykład, biorąc pod uwagę ścieżkę '.. /USA/2019/01/01/data.csv' gdzie partycja jest według kraju i godziny, partition_format="/{Kraj}/{PartitionDate:yyy/MM/dd}/data.csv' tworzy kolumnę ciągów "Kraj" o wartości "USA" i kolumnę datatime "PartitionDate" o wartości "2019-01-01".
    + `to_csv_files`i `to_parquet_files` metody zostały `TabularDataset`dodane do . Metody te umożliwiają `TabularDataset` konwersję `FileDataset` między a i a, konwertując dane na pliki określonego formatu.
    + Automatycznie loguj się do podstawowego rejestru obrazów podczas zapisywania pliku Dockerfile wygenerowanego przez Model.package().
    + "gpu_support" nie jest już konieczne; AML automatycznie wykrywa i używa rozszerzenia docker nvidia, gdy jest ono dostępne. Zostanie on usunięty w przyszłej wersji.
    + Dodano obsługę tworzenia, aktualizowania i używania PipelineDrafts.
    + Ta wersja zwiększa wydajność wykonywania zautomatyzowanych uruchomień lokalnych uczenia maszynowego.
    + Użytkownicy mogą wysyłać zapytania z metryk z historii uruchamiania według nazwy.
    + Usprawniono rejestrowanie przy użyciu wyjątków niestandardowych zamiast ogólnych w zadaniach prognozowania.
  + **azureml-explain-model**
    + Dodano feature_maps parametr do nowego MimicWrapper, dzięki czemu użytkownicy mogą uzyskać surowe wyjaśnienia funkcji.
    + Przesyłanie zestawu danych jest teraz domyślnie wyłączone w celu przesłania objaśnień i można je ponownie włączyć za pomocą upload_datasets=True
    + Dodano parametry filtrowania "is_law" do listy objaśnień i funkcji pobierania.
    + Dodaje `get_raw_explanation(feature_maps)` metodę do obiektów objaśnień globalnych i lokalnych.
    + Dodano wersję sprawdzić lightgbm z wydrukowanym ostrzeżeniem, jeśli poniżej obsługiwanej wersji
    + Zoptymalizowane użycie pamięci podczas objaśnień przetwarzania wsadowego
    + Modele AutoML zwracają teraz automlexceptions
  + **azureml-pipeline-core**
    + Dodano obsługę tworzenia, aktualizowania i używania PipelineDrafts — może służyć do obsługi modyfikowalnych definicji potoków i używania ich interaktywnie do uruchamiania
  + **azureml-train-automl**
    + Stworzono funkcję instalowania określonych wersji pytorcha obsługującego gpu :::no-loc text="cuda"::: v1.1.0, zestawu narzędzi 9.0, transformatorów pytorch, która jest wymagana do włączenia BERT/ XLNet w zdalnym środowisku uruchomieniowym python.
  + **azureml-train-core**
    + Wczesna awaria niektórych błędów definicji przestrzeni hiperparametryczne bezpośrednio w sdk zamiast po stronie serwera.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.14
+ **Poprawki i ulepszenia błędów**
  + Włączono zapisywanie do ADLS/ADLSGen2 przy użyciu nieprzetworzonej ścieżki i poświadczeń.
  + Naprawiono błąd, `include_path=True` który powodował, że nie działał dla `read_parquet`.
  + Naprawiono `to_pandas_dataframe()` błąd spowodowany przez wyjątek "Nieprawidłowa wartość właściwości: hostSecret".
  + Naprawiono błąd, który powodował, że nie można było odczytać plików na DBFS w trybie Iskra.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.57
+ **Nowe funkcje**
  + Włączone `TabularDataset` do korzystania przez AutomatedML. Aby dowiedzieć `TabularDataset`się https://aka.ms/azureml/howto/createdatasetswięcej o , odwiedź .

+ **Poprawki i ulepszenia błędów**
  + **automl-client-core-nativeclient**
    + Naprawiono błąd, wywoływany, gdy etykiety szkoleniowe i/lub sprawdzania poprawności (y i y_valid) są dostarczane w postaci pandas dataframe, ale nie jako numpy tablicy.
    + Zaktualizowano interfejs, `RawDataContext` aby utworzyć a `AutoMLBaseSettings` wymagać tylko danych i obiektu.
    +  Zezwalaj użytkownikom automl na upuszczanie serii szkoleniowych, które nie są wystarczająco długie podczas prognozowania. - Zezwalaj użytkownikom AutoML na upuszczanie ziaren z zestawu testów, który nie istnieje w zestawie szkoleniowym podczas prognozowania.
  + **azure-cli-ml**
    + Teraz można zaktualizować certyfikat TLS/SSL dla punktu końcowego oceniania wdrożonego w klastrze AKS zarówno dla wygenerowanego przez firmę Microsoft, jak i dla certyfikatu klienta.
  + **azureml-automl-core**
    + Naprawiono błąd w automl, który powodował, że wiersze z brakującymi etykietami nie były poprawnie usuwane.
    + Ulepszone rejestrowanie błędów w automl; pełne komunikaty o błędach będą teraz zawsze zapisywane w pliku dziennika.
    + AutoML zaktualizował swoje `azureml-defaults`przypinanie pakietu, aby uwzględnić , `azureml-explain-model`i `azureml-dataprep`. AutoML nie będzie już ostrzegał o niezgodności pakietu (z wyjątkiem `azureml-train-automl` pakietu).
    + Naprawiono błąd, `timeseries` który powodował, że podziały cv były nierówne, co powodowało niepowodzenie obliczeń pojemników.
    + Podczas uruchamiania iteracji zespołu dla typu szkolenia krzyżowego sprawdzania poprawności, jeśli skończyło się problemy z pobraniem modeli przeszkolonych w całym zestawie danych, mieliśmy niespójność między wagi modelu i modeli, które były podawane do zespołu głosowania.
    + Naprawiono błąd, wywoływany, gdy etykiety szkoleniowe i/lub sprawdzania poprawności (y i y_valid) są dostarczane w postaci pandas dataframe, ale nie jako numpy tablicy.
    + Naprawiono błąd związany z zadaniami prognozowania, gdy w kolumnach logicznych tabel wejściowych napotkano opcję Brak.
    + Zezwalaj użytkownikom automl na upuszczanie serii szkoleniowych, które nie są wystarczająco długie podczas prognozowania. - Zezwalaj użytkownikom AutoML na upuszczanie ziaren z zestawu testów, który nie istnieje w zestawie szkoleniowym podczas prognozowania.
  + **azureml-core**
    + Naprawiono błąd podczas zamawiania parametrów blob_cache_timeout.
    + Dodano zewnętrzne dopasowanie i przekształcanie typów wyjątków do błędów systemu.
    + Dodano obsługę wpisów tajnych magazynu kluczy dla zdalnych przebiegów. Dodaj klasę azureml.core.keyvault.Keyvault, aby dodać, uzyskać i wyświetlić listę wpisów tajnych z keyvault skojarzonego z obszarem roboczym. Obsługiwane operacje to:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(nazwa, wartość)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(nazwa)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Dodatkowe metody uzyskiwania domyślnego keyvault i uzyskiwania wpisów tajnych podczas zdalnego uruchamiania:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(nazwa)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Dodano dodatkowe parametry zastępowania do polecenia interfejsu wiersza polecenia submit-hyperdrive.
    + Zwiększ niezawodność wywołań interfejsu API rozwijanie ponownych prób do typowych wyjątków biblioteki żądań.
    + Dodaj obsługę przesyłania przebiegów z przesłanego przebiegu.
    + Naprawiono wygasający problem tokenu SAS w FileWatcher, który powodował, że pliki przestały być przekazywane po wygaśnięciu ich początkowego tokenu.
    + Obsługiwane importowanie plików HTTP csv/tsv w zestawie danych python SDK.
    + Przestarzała metoda Workspace.setup(). Komunikat ostrzegawczy wyświetlany użytkownikom sugeruje użycie create() lub get()/from_config().
    + Dodano Environment.add_private_pip_wheel(), który umożliwia przekazywanie prywatnych niestandardowych pakietów `whl`python do obszaru roboczego i bezpiecznie używając ich do tworzenia/materializacji środowiska.
    + Teraz można zaktualizować certyfikat TLS/SSL dla punktu końcowego oceniania wdrożonego w klastrze AKS zarówno dla wygenerowanego przez firmę Microsoft, jak i dla certyfikatu klienta.
  + **azureml-explain-model**
    + Dodano parametr, aby dodać identyfikator modelu do objaśnień podczas przekazywania.
    + Dodano `is_raw` tagowanie do wyjaśnień w pamięci i przesłać.
    + Dodano pytorch wsparcie i testy dla pakietu azureml-explain-model.
  + **azureml-opendatasets**
    + Obsługa wykrywania i rejestrowania środowiska automatycznego testowania.
    + Dodano klasy, aby uzyskać populację USA według hrabstwa i zip.
  + **azureml-pipeline-core**
    + Dodano właściwość etykiety do definicji portów wejściowych i wyjściowych.
  + **azureml-telemetry**
    + Naprawiono nieprawidłową konfigurację telemetrii.
  + **azureml-train-automl**
    + Naprawiono błąd, który powodował błąd podczas wykonywania konfiguracji, błąd nie był rejestrowany w polu "błędy" dla przebiegu instalacji i dlatego nie był przechowywany w nadrzędnym uruchomieniu "błędy".
    + Naprawiono błąd w automl, który powodował, że wiersze z brakującymi etykietami nie były poprawnie usuwane.
    + Zezwalaj użytkownikom automl na upuszczanie serii szkoleniowych, które nie są wystarczająco długie podczas prognozowania.
    + Zezwalaj użytkownikom automl na upuszczanie ziaren z zestawu testów, które nie istnieją w zestawie szkoleniowym podczas prognozowania.
    + Teraz AutoMLStep `automl` przechodzi przez config do wewnętrznej bazy, aby uniknąć problemów ze zmianami lub dodatkami nowych parametrów konfiguracji.
    + Automatyczna barierka danych AUTOML jest teraz w publicznej wersji zapoznawczej. Użytkownik zobaczy raport Data Guardrail (dla zadań klasyfikacji/regresji) po szkoleniu, a także będzie mógł uzyskać do niego dostęp za pośrednictwem interfejsu API SDK.
  + **azureml-train-core**
    + Dodano podparcie palnika 1.2 w Estymatorze PyTorch.
  + **azureml-widgets**
    + Ulepszone wykresy macierzy pomyłek do treningu klasyfikacji.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.12
+ **Nowe funkcje**
  + Listy ciągów mogą być teraz przekazywane `read_*` jako dane wejściowe do metod.

+ **Poprawki i ulepszenia błędów**
  + Wydajność została `read_parquet` znacznie zwiększona podczas pracy w spark.
  + Naprawiono błąd, który `column_type_builder` powodował niepowodzenie w przypadku pojedynczej kolumny z niejednoznacznymi formatami dat.

### <a name="azure-portal"></a>Azure Portal
+ **Funkcja podglądu**
  + Przesyłanie strumieniowe plików dziennika i danych wyjściowych jest teraz dostępne dla stron szczegółów uruchamiania. Pliki będą przesyłać strumieniowo aktualizacje w czasie rzeczywistym, gdy przełącznik podglądu jest włączony.
  + Możliwość ustawiania przydziału na poziomie obszaru roboczego jest zwalniana w wersji zapoznawczej. Przydziały AmlCompute są przydzielane na poziomie subskrypcji, ale teraz umożliwiamy rozdzielenie tego przydziału między obszary robocze i przydzielenie go do sprawiedliwego udostępniania i nadzoru. Wystarczy kliknąć na **usages +quotas** blade w lewym pasku nawigacyjnym obszaru roboczego i wybierz kartę **Konfigurowanie przydziałów.**

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.55

+ **Nowe funkcje**
  + Uwierzytelnianie oparte na tokenie jest teraz obsługiwane dla wywołań do punktu końcowego oceniania wdrożonego w uaŚ. Będziemy nadal obsługiwać bieżące uwierzytelnianie oparte na kluczu, a użytkownicy mogą korzystać z jednego z tych mechanizmów uwierzytelniania naraz.
  + Możliwość rejestrowania magazynu obiektów blob, który znajduje się za siecią wirtualną (VNet) jako magazyn danych.

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Naprawia błąd, w którym rozmiar sprawdzania poprawności dla podziałów CV jest mały i powoduje złe przewidywane vs prawdziwe wykresy regresji i prognozowania.
    + Rejestrowanie zadań prognozowania na zdalnych uruchomieniach ulepszone, teraz użytkownik jest wyposażony w kompleksowy komunikat o błędzie, jeśli uruchomienie nie powiodło się.
    + Naprawiono `Timeseries` błędy, jeśli flaga przetwarzania wstępnego jest True.
    + Niektóre komunikaty o błędach sprawdzania poprawności danych prognozowania były bardziej użyteczne.
    + Mniejsze zużycie pamięci przez automl uruchamia przez upuszczanie i/lub z opóźnieniem ładowania zestawów danych, zwłaszcza między spawnami procesowymi
  + **azureml-contrib-explain-model**
    + Dodano flagę model_task do objaśnień, aby umożliwić użytkownikowi zastąpienie domyślnej logiki automatycznego wnioskowania dla typu modelu
    + Zmiany widget: Automatycznie instaluje się z `contrib`, nie więcej `nbextension` zainstalować / włączyć - wyjaśnienie wsparcia tylko globalne znaczenie funkcji (np Permutative)
    + Zmiany na desce rozdzielczej: `beeswarm` `beeswarm` - Wykresy box i wykresy skrzypcowe oprócz fabuły na stronie podsumowania - Znacznie szybsze rerendering fabuły na "Top -k" zmiana suwaka - pomocny komunikat wyjaśniający, jak top-k jest obliczany - Przydatne konfigurowalne wiadomości zamiast wykresów, gdy dane nie są podane
  + **azureml-core**
    + Dodano model.package() metoda tworzenia obrazów platformy Docker i Dockerfiles, które hermetyzują modeli i ich zależności.
    + Zaktualizowano lokalne usługi sieci web, aby zaakceptować InferenceConfigs zawierające obiekty środowiska.
    + Naprawiono model.register() produkujący nieprawidłowe modele, gdy '.' (dla bieżącego katalogu) jest przekazywana jako parametr model_path.
    + Dodaj Run.submit_child, funkcja dubluje Experiment.submit określając jednocześnie uruchomić jako element nadrzędny przesłanego uruchomienia podrzędnego.
    + Obsługa opcji konfiguracji z pliku Model.register w pliku Run.register_model.
    + Możliwość uruchamiania zadań JAR w istniejącym klastrze.
    + Teraz obsługuje parametry instance_pool_id i cluster_log_dbfs_path.
    + Dodano obsługę przy użyciu środowiska obiektu podczas wdrażania modelu do usługi sieci Web. Środowisko obiekt może być teraz dostarczony jako część InferenceConfig obiektu.
    + Dodaj mapowanie appinsifht dla nowych regionów - centralus - westus - northcentralus
    + Dodano dokumentację dla wszystkich atrybutów we wszystkich klasach magazynu danych.
    + Dodano parametr blob_cache_timeout do `Datastore.register_azure_blob_container`.
    + Dodano metody save_to_directory i load_from_directory do azureml.core.environment.Environment.
    + Dodano polecenia "az ml environment download" i "az ml environment register" do interfejsu wiersza polecenia.
    + Dodano metodę Environment.add_private_pip_wheel.
  + **azureml-explain-model**
    + Dodano śledzenie zestawu danych do objaśnień za pomocą usługi Zestawu danych (wersja zapoznawcza).
    + Zmniejszono domyślny rozmiar partii podczas przesyłania strumieniowego globalnych wyjaśnień z 10k do 100.
    + Dodano flagę model_task do objaśnień, aby umożliwić użytkownikowi zastąpienie domyślnej logiki wnioskowania automatycznego dla typu modelu.
  + **azureml-mlflow**
    + Naprawiono błąd w pliku mlflow.azureml.build_image, w którym katalogi zagnieżdżone są ignorowane.
  + **azureml-pipeline-kroki**
    + Dodano możliwość uruchamiania zadań JAR w istniejącym klastrze usługi Azure Databricks.
    + Dodano parametry instance_pool_id i cluster_log_dbfs_path dla kroku DatabricksStep.
    + Dodano obsługę parametrów potoku w kroku DatabricksStep.
  + **azureml-train-automl**
    + Dodano `docstrings` dla plików związanych z ensemble.
    + Zaktualizowano dokumenty do bardziej `max_cores_per_iteration` odpowiedniego języka dla`max_concurrent_iterations`
    + Rejestrowanie zadań prognozowania na zdalnych uruchomieniach ulepszone, teraz użytkownik jest wyposażony w kompleksowy komunikat o błędzie, jeśli uruchomienie nie powiodło się.
    + Usunięto get_data `automlstep` z notesu potoku.
    + Uruchomiono `dataprep` `automlstep`obsługę w pliku .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.10

+ **Nowe funkcje**
  + Teraz można zażądać wykonania określonych inspektorów (np. histogramu, wykresu punktowego itp.) na określonych kolumnach.
  + Dodano argument równoległości `append_columns`do . Jeśli True, dane zostaną załadowane do pamięci, ale wykonanie będzie działać równolegle; jeśli False, wykonanie będzie przesyłania strumieniowego, ale jednowątkowe.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.53

+ **Nowe funkcje**
  + Zautomatyzowane uczenie maszynowe obsługuje teraz szkolenia modeli ONNX na zdalnym docelowym obliczeniu
  + Usługa Azure Machine Learning umożliwia teraz wznowienie szkolenia z poprzedniego uruchomienia, punktu kontrolnego lub plików modelu.
    + Dowiedz się, jak [używać estymatorów do wznawiania treningów z poprzedniego biegu](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Poprawki i ulepszenia błędów**
  + **automl-client-core-nativeclient**
    + Napraw błąd dotyczący utraty typów kolumn po transformacji (połączony błąd);
    + Zezwalaj y_query na typ obiektu zawierający brak(y) na początku (#459519).
  + **azure-cli-ml**
    + Polecenia CLI "model deploy" i "service update" akceptują teraz parametry, pliki konfiguracyjne lub kombinację tych dwóch. Parametry mają pierwszeństwo przed atrybutami w plikach.
    + Opis modelu można teraz zaktualizować po rejestracji
  + **azureml-automl-core**
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualny najnowszy).
    + Dodanie obsługi estymatorów NimbusML & rurociągów do wykorzystania w estymatorach AutoML.
    + Naprawianie błędu w procedurze selekcji Ensemble, który niepotrzebnie powiększał wynikowy zespół, nawet jeśli wyniki pozostały stałe.
    + Włącz ponowne użycie niektórych featurizations całej podziałów CV do prognozowania zadań. Przyspiesza to czas wykonywania instalacji uruchamiany przez mniej więcej współczynnik n_cross_validations dla kosztownych featurizations jak lagi i okna toczne.
    + Rozwiązanie problemu, jeśli czas jest poza pandas obsługiwane zakres czasu. Teraz podnieść DataException jeśli czas jest krótszy niż pd. Sygnatura czasowa.min lub większa niż pd. Sygnatura czasowa.max
    + Prognozowanie umożliwia teraz różne częstotliwości w zestawach pociągów i testów, jeśli można je wyrównać. Na przykład można wyrównać "kwartalne rozpoczęcie stycznia" i "kwartalne rozpoczęcie października".
    + Właściwość "parametry" został dodany do TimeSeriesTransformer.
    + Usuń stare klasy wyjątków.
    + W zadaniach prognozowania `target_lags` parametr akceptuje teraz pojedynczą wartość całkowitą lub listę liczby całkowitej. Jeśli podano całkowitą, zostanie utworzone tylko jedno opóźnienie. Jeśli zostanie udostępniona lista, zostaną pobrane unikatowe wartości opóźnień. target_lags=[1, 2, 2, 4] spowoduje opóźnienie jednego, 2 i 4 okresów.
    + Napraw błąd dotyczący utraty typów kolumn po transformacji (połączony z błędem);
    + W `model.forecast(X, y_query)`, zezwalaj y_query jako typ obiektu zawierający brak(y) na początku (#459519).
    + Dodawanie oczekiwanych `automl` wartości do danych wyjściowych
  + **azureml-contrib-datadrift**
    +  Ulepszenia przykładowego notesu, w tym przełączanie do zestawów azureml-opendatasets zamiast zestawów azureml-contrib-opendatasets i poprawy wydajności podczas wzbogacania danych
  + **azureml-contrib-explain-model**
    + Poprawiono argument przekształceń dla objaśnienia LIME dla surowego znaczenia funkcji w pakiecie azureml-contrib-explain-model
    + Dodano segmentacje do objaśnień obrazu w objaśnieniu obrazów dla pakietu AzureML-contrib-explain-model
    + Dodaj scipy sparse wsparcie dla LimeExplainer
    + Dodano `batch_size` do mimic `include_local=False`explainer, gdy , do przesyłania strumieniowego globalnych wyjaśnień w partiach, aby poprawić czas realizacji DecisionTreeExplainableModel
  + **azureml-contrib-featureinżynienie**
    + Poprawka do wywoływania set_featurizer_timeseries_params(): zmiana typu dict i sprawdzanie `timeseries` wartości null - Dodaj notes dla featurizer
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualny najnowszy).
  + **azureml-core**
    + Dodano możliwość dołączania magazynów danych DBFS do interfejsu wiersza polecenia usługi AzureML
    + Naprawiono błąd podczas przekazywania danych w `target_path` magazynie danych, w którym tworzony jest pusty folder, jeśli został uruchomiony z`/`
    + Naprawiono błąd w serwisie.Fixed `deepcopy` issue in ServicePrincipalAuthentication.
    + Dodano polecenia "az ml environment show" i "az ml environment list" do interfejsu wiersza polecenia.
    + Środowiska obsługują teraz określanie base_dockerfile jako alternatywy dla już utworzonego base_image.
    + Nieużywane ustawienie RunConfiguration auto_prepare_environment zostało oznaczone jako przestarzałe.
    + Opis modelu można teraz zaktualizować po rejestracji
    + Poprawka błędu: Model i image delete teraz zawiera więcej informacji na temat pobierania obiektów nadrzędnych, które zależą od nich, jeśli usunięcie nie powiedzie się z powodu zależności nadrzędnej.
    + Naprawiono błąd, który drukował pusty czas trwania wdrożeń, które występują podczas tworzenia obszaru roboczego dla niektórych środowisk.
    + Ulepszone wyjątki błędów tworzenia obszaru roboczego. W taki sposób, że użytkownicy nie widzą "Nie można utworzyć obszaru roboczego. Nie można znaleźć..." jako komunikat i zamiast tego zobacz rzeczywisty błąd tworzenia.
    + Dodaj obsługę uwierzytelniania tokenów w usługach sieci web usługi AKS.
    + Dodaj `get_token()` metodę `Webservice` do obiektów.
    + Dodano obsługę interfejsu wiersza polecenia do zarządzania zestawami danych uczenia maszynowego.
    + `Datastore.register_azure_blob_container`teraz opcjonalnie `blob_cache_timeout` przyjmuje wartość (w sekundach), która konfiguruje parametry instalacji bluzki, aby włączyć wygaśnięcie pamięci podręcznej dla tego magazynu danych. Wartość domyślna nie jest limit czasu, czyli po odczytaniu obiektu blob, pozostanie w lokalnej pamięci podręcznej, dopóki zadanie nie zostanie zakończone. Większość zadań preferuje to ustawienie, ale niektóre zadania muszą odczytywać więcej danych z dużego zestawu danych, niż mieszczą się w ich węzłach. W przypadku tych zadań dostrajanie tego parametru pomoże im odnieść sukces. Należy uważać podczas dostrajania tego parametru: ustawienie zbyt niskiej wartości może spowodować niską wydajność, ponieważ dane używane w epoce mogą wygasnąć przed ponownym użyciem. Oznacza to, że wszystkie odczyty będą wykonywane z magazynu obiektów blob (tj. sieci) zamiast lokalnej pamięci podręcznej, co negatywnie wpływa na czas szkolenia.
    + Opis modelu można teraz poprawnie zaktualizować po rejestracji
    + Usunięcie modelu i obrazu zawiera teraz więcej informacji o obiektach nadrzędnych, które są od nich zależne, co powoduje niepowodzenie usuwania
    + Zwiększ wykorzystanie zasobów zdalnych przebiegów za pomocą pliku azureml.mlflow.
  + **azureml-explain-model**
    + Poprawiono argument przekształceń dla objaśnienia LIME dla surowego znaczenia funkcji w pakiecie azureml-contrib-explain-model
    + dodaj scipy sparse wsparcie dla LimeExplainer
    + dodano otokę objaśnienia liniowego kształtu, a także inny poziom do objaśnienia tabelaryczne do objaśnienia modeli liniowych
    + dla mimic explainer w wyjaśnieniu biblioteki modelu, naprawiony błąd, gdy include_local = False dla danych rozrzedzonych
    + dodawanie oczekiwanych `automl` wartości do danych wyjściowych
    + stałe znaczenie funkcji permutacji, gdy argument przekształceń dostarczony, aby uzyskać znaczenie obiektu nieprzetworzonego
    + dodane `batch_size` do mimic `include_local=False`explainer, gdy , do przesyłania strumieniowego globalnych wyjaśnień w partiach w celu poprawy czasu wykonania DecisionTreeExplainableModel
    + dla biblioteki wyjaśniającej modelu, stałe objaśnienia blackbox, w których pandas dataframe input jest wymagane do przewidywania
    + Naprawiono błąd, który `explanation.expected_values` czasami zwracał pływak, a nie listę z spławikiem.
  + **azureml-mlflow**
    + Poprawa wydajności mlflow.set_experiment(experiment_name)
    + Napraw błąd w użyciu InteractiveLoginAuthentication dla mlflow tracking_uri
    + Zwiększ wykorzystanie zasobów zdalnych przebiegów za pomocą pliku azureml.mlflow.
    + Ulepszanie dokumentacji pakietu azureml-mlflow
    + Błąd poprawki, w którym plik mlflow.log_artifacts("my_dir") zapisywał artefakty w obszarze "my_dir/<> ścieżek artefaktów" zamiast "<ścieżek artefaktów>"
  + **azureml-opendatasets**
    + Pin `pyarrow` `opendatasets` do starych wersji (<0.14.0) z powodu problemu z pamięcią nowo wprowadzone tam.
    + Przenieś azureml-contrib-opendatasets do azureml-opendatasets.
    + Zezwalaj na rejestrowanie otwartych klas zestawów danych w obszarze roboczym usługi Azure Machine Learning i bezproblemowo korzystaj z funkcji zestawu danych AML.
    + Poprawić NoaAIsdWeather znacznie wzbogacić wydajność w wersji non-SPARK.
  + **azureml-pipeline-kroki**
    + DBFS Datastore jest teraz obsługiwany dla wejść i wyjść w DatabricksStep.
    + Zaktualizowano dokumentację usługi Azure Batch Step w odniesieniu do danych wejściowych/wyjściowych.
    + W usłudze AzureBatchStep zmieniono *delete_batch_job_after_finish* wartość domyślną na *true*.
  + **azureml-telemetry**
    +  Przenieś azureml-contrib-opendatasets do azureml-opendatasets.
    + Zezwalaj na rejestrowanie otwartych klas zestawów danych w obszarze roboczym usługi Azure Machine Learning i bezproblemowo korzystaj z funkcji zestawu danych AML.
    + Poprawić NoaAIsdWeather znacznie wzbogacić wydajność w wersji non-SPARK.
  + **azureml-train-automl**
    + Zaktualizowano dokumentację dotyczącą get_output, aby odzwierciedlić rzeczywisty typ zwrotu i dostarczyć dodatkowych uwag dotyczących pobierania właściwości klucza.
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualny najnowszy).
    + dodawanie oczekiwanych `automl` wartości do danych wyjściowych
  + **azureml-train-core**
    + Ciągi są teraz akceptowane jako cel obliczeniowy dla automatycznego dostrajania hiperparametryczne
    + Nieużywane ustawienie RunConfiguration auto_prepare_environment zostało oznaczone jako przestarzałe.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.9

+ **Nowe funkcje**
  + Dodano obsługę odczytu pliku bezpośrednio z adresu URL http lub https.

+ **Poprawki i ulepszenia błędów**
  + Poprawiono komunikat o błędzie podczas próby odczytu zestawu danych parkietu ze źródła zdalnego (który nie jest obecnie obsługiwany).
  + Naprawiono błąd podczas zapisywania do formatu pliku Parkietu w ADLS Gen 2 i aktualizowania nazwy kontenera ADLS Gen 2 w ścieżce.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interfejs wizualny
+ **Funkcje podglądu**
  + Dodano moduł "Wykonaj skrypt R" w interfejsie wizualnym.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.48

+ **Nowe funkcje**
  + **azureml-opendatasets**
    + **azureml-contrib-opendatasets** jest teraz dostępny jako **azureml-opendatasets**. Stary pakiet może nadal działać, ale zaleca się korzystanie z **azureml-opendatasets posuwając** się do przodu, aby uzyskać bogatsze możliwości i ulepszenia.
    + Ten nowy pakiet umożliwia rejestrowanie otwartych zestawów danych jako zestaw danych w obszarze roboczym usługi Azure Machine Learning i korzystanie z funkcji, które oferuje zestaw danych.
    + Zawiera również istniejące funkcje, takie jak korzystanie z otwartych zestawów danych, takich jak pandas/SPARK dataframes i sprzężenia lokalizacji dla niektórych zestaw danych, takich jak pogoda.

+ **Funkcje podglądu**
    + HyperDriveConfig można teraz zaakceptować obiekt potoku jako parametr do obsługi dostrajania hiperparametryczne przy użyciu potoku.

+ **Poprawki i ulepszenia błędów**
  + **azureml-train-automl**
    + Naprawiono błąd dotyczący utraty typów kolumn po transformacji.
    + Naprawiono błąd, aby umożliwić y_query jako typ obiektu zawierający brak(y) na początku.
    + Naprawiono błąd w procedurze selekcji zespołu, który niepotrzebnie powiększał wynikowy zespół, nawet jeśli wyniki pozostały stałe.
    + Naprawiono błąd z ustawieniami whitelist_models i blacklist_models w AutoMLStep.
    + Rozwiązano ten problem, który uniemożliwiał użycie przetwarzania wstępnego, gdy automl był używany w kontekście potoków usługi Azure ML.
  + **azureml-opendatasets**
    + Przeniesiono zestawy danych azureml-contrib-open do zestawów azureml-opendatasets.
    + Dozwolone otwarte klasy zestawów danych, które mają być zarejestrowane w obszarze roboczym usługi Azure Machine Learning i bezproblemowo korzystać z funkcji zestawu danych AML.
    + Ulepszony NoaIsdWeather znacznie wzbogacił wydajność w wersji innej niż SPARK.
  + **azureml-explain-model**
    + Zaktualizowano dokumentację online dla obiektów interpretability.
    + Dodano `batch_size` do mimic `include_local=False`explainer, gdy , do przesyłania strumieniowego globalnych wyjaśnień w partiach, aby poprawić czas wykonywania DecisionTreeExplainableModel dla biblioteki wyjaśniającej modelu.
    + Naprawiono błąd, który `explanation.expected_values` powodował, że czasami zwracał pływak, a nie listę z spławikiem.
    + Dodano oczekiwane `automl` wartości do wytłumaczenia dla objaśnienia mimic w objaśnieniu biblioteki modelu.
    + Poprawiono znaczenie funkcji permutacji, gdy argument przekształceń dostarczony, aby uzyskać znaczenie obiektu nieprzetworzonego.
  + **azureml-core**
    + Dodano możliwość dołączania magazynów danych DBFS w pliku wiersza polecenia azureml.
    + Rozwiązaliśmy problem z przesyłaniem danych w `target_path` magazynie `/`danych, w którym tworzony jest pusty folder, jeśli został uruchomiony z .
    + Włączone porównanie dwóch zestawów danych.
    + Model i image delete teraz zawiera więcej informacji na temat pobierania obiektów nadrzędnych, które zależą od nich, jeśli usunięcie nie powiedzie się z powodu zależności nadrzędnej.
    + Przestarzałe ustawienie nieużywanej konfiguracji RunConfiguration w auto_prepare_environment.
  + **azureml-mlflow**
    + Ulepszone wykorzystanie zasobów zdalnych uruchomień korzystających z pliku azureml.mlflow.
    + Ulepszono dokumentację pakietu azureml-mlflow.
    + Naprawiono błąd, który powodował, że plik mlflow.log_artifacts("my_dir") zapisywał artefakty w obszarze "my_dir/ścieżki artefaktów" zamiast "ścieżki artefaktów".
  + **azureml-pipeline-core**
    + Parametr hash_paths dla wszystkich kroków potoku jest przestarzały i zostanie usunięty w przyszłości. Domyślnie zawartość source_directory jest mieszana (z wyjątkiem plików wymienionych w .amlignore lub .gitignore)
    + Dalsze ulepszanie modułu i modułuStep do obsługi modułów specyficznych dla typu obliczeniowego, aby przygotować się do integracji RunConfiguration i innych zmian w celu odblokowania użycia modułu określonego typu obliczeniowego w potokach.
  + **azureml-pipeline-kroki**
    + AzureBatchStep: Ulepszona dokumentacja w odniesieniu do danych wejściowych/wyjść.
    + AzureBatchStep: Zmieniono wartość domyślną delete_batch_job_after_finish na true.
  + **azureml-train-core**
    + Ciągi są teraz akceptowane jako cel obliczeniowy dla automatycznego dostrajania hiperparametry.
    + Przestarzałe ustawienie nieużywanej konfiguracji RunConfiguration w auto_prepare_environment.
    + Przestarzałe `conda_dependencies_file_path` `pip_requirements_file_path` parametry i `conda_dependencies_file` `pip_requirements_file` na korzyść i odpowiednio.
  + **azureml-opendatasets**
    + Poprawić NoaAIsdWeather znacznie wzbogacić wydajność w wersji non-SPARK.

### <a name="azure-machine-learning-data-prep-sdk-v118"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.8

+ **Nowe funkcje**
 + Obiekty przepływu danych mogą być teraz iterowane, tworząc sekwencję rekordów. Zobacz dokumentację dla `Dataflow.to_record_iterator`.
  + Obiekty przepływu danych mogą być teraz iterowane, tworząc sekwencję rekordów. Zobacz dokumentację dla `Dataflow.to_record_iterator`.

+ **Poprawki i ulepszenia błędów**
 + Zwiększono niezawodność zestawu SDK DataPrep.
 + Ulepszona obsługa pandas DataFrames z indeksami kolumn bez ciągu.
 + Poprawiono wydajność `to_pandas_dataframe` w zestawach danych.
 + Naprawiono błąd, który powodował, że wykonanie zestawu danych w trybie Spark nie powiodło się podczas uruchamiania w środowisku wielowęzłowym.
  + Zwiększono niezawodność zestawu SDK DataPrep.
  + Ulepszona obsługa pandas DataFrames z indeksami kolumn bez ciągu.
  + Poprawiono wydajność `to_pandas_dataframe` w zestawach danych.
  + Naprawiono błąd, który powodował, że wykonanie zestawu danych w trybie Spark nie powiodło się podczas uruchamiania w środowisku wielowęzłowym.

## <a name="2019-07-01"></a>2019-07-01

### <a name="azure-machine-learning-data-prep-sdk-v117"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.7

Wycofaliśmy zmianę, która poprawiła wydajność, ponieważ powodowała problemy dla niektórych klientów korzystających z usługi Azure Databricks. Jeśli wystąpił problem na platformie Azure Databricks, można uaktualnić do wersji 1.1.7 przy użyciu jednej z poniższych metod:
1. Uruchom ten skrypt, aby uaktualnić:`%sh /home/ubuntu/databricks/python/bin/pip install azureml-dataprep==1.1.7`
2. Odtwórz ponownie klaster, który zainstaluje najnowszą wersję zestawu SDK przygotowania danych.

## <a name="2019-06-25"></a>2019-06-25

### <a name="azure-machine-learning-sdk-for-python-v1045"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.45

+ **Nowe funkcje**
  + Dodaj model zastępczy drzewa decyzyjnego, aby naśladować objaśnienie w pakiecie azureml-explain-model
  + Możliwość określenia wersji CUDA, która ma być zainstalowana na obrazach wnioskujących. Obsługa CUDA 9.0, 9.1 i 10.0.
  + Informacje o podstawowych obrazach szkoleniowych usługi Azure ML są teraz dostępne w [repozytorium GitHub kontenerów usługi Azure ML](https://github.com/Azure/AzureML-Containers) i [usłudze DockerHub](https://hub.docker.com/_/microsoft-azureml)
  + Dodano obsługę interfejsu wiersza polecenia dla harmonogramu potoku. Uruchom "az ml pipeline -h", aby dowiedzieć się więcej
  + Dodano niestandardowy parametr obszaru nazw Kubernetes do konfiguracji wdrażania usług sieci Web usługi AKS i interfejsu wiersza polecenia.
  + Przestarzały parametr hash_paths dla wszystkich kroków potoku
  + Model.register obsługuje teraz rejestrowanie wielu pojedynczych plików `child_paths` jako jednego modelu przy użyciu parametru.

+ **Funkcje podglądu**
    + Punktacji explainers można teraz opcjonalnie zapisać conda i pip informacji dla bardziej niezawodne serializacji i deserializacji.
    + Poprawka błędu dla automatycznego selektora funkcji.
    + Zaktualizowano plik mlflow.azureml.build_image do nowego interfejsu API, poprawiono błędy udostępniane przez nową implementację.

+ **Poprawki i ulepszenia błędów**
  + Usunięto `paramiko` zależność z azureml-core. Dodano ostrzeżenia o wycofanie dla starszych obliczeń docelowych dołączyć metody.
  + Poprawa wydajności run.create_children
  + W mimic explainer z klasyfikatorem binarnym należy ustalić kolejność prawdopodobieństw, gdy prawdopodobieństwo nauczyciela jest używane do skalowania wartości kształtu.
  + Ulepszona obsługa błędów i komunikat dla automatycznego uczenia maszynowego.
  + Naprawiono problem limitu czasu iteracji dla automatycznego uczenia maszynowego.
  + Poprawiono wydajność transformacji szeregów czasowych dla zautomatyzowanego uczenia maszynowego.

## <a name="2019-06-24"></a>2019-06-24

### <a name="azure-machine-learning-data-prep-sdk-v116"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.6

+ **Nowe funkcje**
  + Dodano funkcje podsumowania`SummaryFunction.TOPVALUES`dla wartości`SummaryFunction.BOTTOMVALUES`górnych ( ) i dolnej ( ).

+ **Poprawki i ulepszenia błędów**
  + Znacząco poprawiła wydajność `read_pandas_dataframe`.
  + Naprawiono błąd, `get_profile()` który powodowałby niepowodzenie przepływu danych wskazującego na pliki binarne.
  + Udostępniane `set_diagnostics_collection()` w celu umożliwienia programmatic włączanie/wyłączanie kolekcji telemetrii.
  + Zmieniono zachowanie `get_profile()`pliku . Wartości NaN są teraz ignorowane dla min, mean, std i sum, co jest zgodne z zachowaniem pand.


## <a name="2019-06-10"></a>2019-06-10

### <a name="azure-machine-learning-sdk-for-python-v1043"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.43

+ **Nowe funkcje**
  + Usługa Azure Machine Learning zapewnia teraz pierwszorzędną obsługę popularnego uczenia maszynowego i struktury analizy danych Scikit-learn. Za pomocą [ `SKLearn` estymatora,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)użytkownicy mogą łatwo szkolić i wdrażać modele Scikit-learn.
    + Dowiedz się, jak [uruchomić dostrajanie hiperparametryczne za pomocą scikit-learn za pomocą HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb).
  + Dodano obsługę tworzenia ModuleStep w potokach wraz z klasami Module i ModuleVersion do zarządzania jednostkami obliczeniowymi wielokrotnego użytku.
  + Usługi sieci web ACI obsługują teraz trwałe scoring_uri za pośrednictwem aktualizacji. scoring_uri zmieni się z IP na FQDN. Etykietę nazwy DNS dla nazwy FQDN można skonfigurować, ustawiając dns_name_label na deploy_configuration.
  + Nowe funkcje automatycznego uczenia maszynowego:
    + STL featurizer do prognozowania
    + KMeans klastrowanie jest włączona dla funkcji zamiatanie
  + AmlCompute Zatwierdzenia Kontyngentu po prostu stał się szybszy! Teraz zautomatyzowaliśmy proces zatwierdzania żądań przydziału w granicach progu. Aby uzyskać więcej informacji na temat funkcjonowania przydziałów, dowiedz się, [jak zarządzać przydziałami](https://docs.microsoft.com/azure/machine-learning/how-to-manage-quotas).

+ **Funkcje podglądu**
    + Integracja ze [śledzeniem MLflow](https://mlflow.org) 1.0.0 za pośrednictwem pakietu azureml-mlflow[(przykładowe notesy).](https://aka.ms/azureml-mlflow-examples)
    + Prześlij notes Jupyter jako bieg. [Dokumentacja referencyjna interfejsu API](https://docs.microsoft.com/python/api/azureml-contrib-notebook/azureml.contrib.notebook?view=azure-ml-py)
    + Publiczny podgląd [detektora dryfu danych](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector(class)) za pośrednictwem pakietu azureml-contrib-datadrift[(przykładowe notesy).](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/monitor-models/data-drift) Dryf danych jest jednym z głównych powodów, dla których dokładność modelu pogarsza się w czasie. Dzieje się tak, gdy dane obsługiwane do modelowania w produkcji różni się od danych, które model został przeszkolony na. Detektor dryfu danych AML pomaga klientowi monitorować dryft danych i wysyła alerty po wykryciu dryfu.

+ **Fundamentalne zmiany**

+ **Poprawki i ulepszenia błędów**
  + RunConfiguration load and save supports specifying a full file path with full back-compat for previous behavior.RunConfiguration load and save supports specifying a full file path with full back-compat for previous behavior.
  + Dodano buforowanie w ServicePrincipalAuthentication, domyślnie wyłączone.
  + Włącz rejestrowanie wielu wykresów pod tą samą nazwą metryki.
  + Klasa modelu jest teraz poprawnie importowana`from azureml.core import Model`z pliku azureml.core ( ).
  + W krokach `hash_path` potoku parametr jest teraz przestarzały. Nowe zachowanie polega na mieszaniu kompletnych source_directory, z wyjątkiem plików wymienionych w .amlignore lub .gitignore.
  + W pakietach `get_all` rurociągów, różne i `get_all_*` metody `list` zostały `list_*`przestarzałe na rzecz i , odpowiednio.
  + azureml.core.get_run nie wymaga już zaimportowania klas przed zwróceniem oryginalnego typu uruchomienia.
  + Rozwiązało błąd, który powodował, że niektóre wywołania aktualizacji usługi WebService nie wyzwalały aktualizacji.
  + Limit czasu oceniania w usługach sieci web usługi AKS powinien wynosić od 5ms do 300000ms. Max dozwolone scoring_timeout_ms do punktacji wniosków został przecięły z 1 min do 5 min.
  + LocalWebservice obiekty `scoring_uri` mają `swagger_uri` teraz i właściwości.
  + Przeniesione dane wyjściowe tworzenia katalogu i dane wyjściowe katalogu przekazać z procesu użytkownika. Włączono uruchamianie sdk historii uruchamiania w każdym procesie użytkownika. Powinno to rozwiązać niektóre problemy z synchronizacją doświadczane przez rozproszone przebiegi szkoleniowe.
  + Nazwa dziennika azureml napisane z nazwy procesu użytkownika będzie teraz zawierać nazwę procesu (tylko dla szkolenia rozproszonego) i PID.

### <a name="azure-machine-learning-data-prep-sdk-v115"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.5

+ **Poprawki i ulepszenia błędów**
  + W przypadku interpretowanych wartości datetime, które mają 2-cyfrowy format roku, zakres ważnych lat został zaktualizowany, aby dopasować ją do wersji majowej systemu Windows. W latach 1930-2029 zmieniono zakres.
  + Podczas czytania w pliku `handleQuotedLineBreaks=True` `\r` i ustawienie , będą traktowane jako nowy wiersz.
  + Naprawiono błąd, `read_pandas_dataframe` który powodował niepowodzenie w niektórych przypadkach.
  + Poprawiono `get_profile`wydajność .
  + Poprawiono komunikaty o błędach.

## <a name="2019-05-28"></a>2019-05-28

### <a name="azure-machine-learning-data-prep-sdk-v114"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.4

+ **Nowe funkcje**
  + Teraz można użyć następujących funkcji języka wyrażeń, aby wyodrębnić i przeanalizować wartości datetime do nowych kolumn.
    + `RegEx.extract_record()`wyodrębnia elementy datetime do nowej kolumny.
    + `create_datetime()`tworzy obiekty datetime z oddzielnych elementów datetime.
  + Podczas `get_profile()`wywoływania można teraz zobaczyć, że kolumny kwantyfikowane są oznaczone jako (est.), aby wyraźnie wskazać, że wartości są przybliżeniami.
  + Teraz można użyć ** globbing podczas odczytu z usługi Azure Blob Storage.
    + Przykład.`dprep.read_csv(path='https://yourblob.blob.core.windows.net/yourcontainer/**/data/*.csv')`

+ **Poprawki błędów**
  + Naprawiono błąd związany z odczytywaniem pliku parkietu ze źródła zdalnego (azure blob).

## <a name="2019-05-14"></a>2019-05-14

### <a name="azure-machine-learning-sdk-for-python-v1039"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.39
+ **Zmiany**
  + Uruchom konfigurację auto_prepare_environment opcja jest przestarzała, a automatyczne przygotowanie staje się wartością domyślną.

## <a name="2019-05-08"></a>2019-05-08

### <a name="azure-machine-learning-data-prep-sdk-v113"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.3

+ **Nowe funkcje**
  + Dodano obsługę odczytu z bazy danych PostgresSQL, wywołując read_postgresql lub przy użyciu magazynu danych.
    + Zobacz przykłady w przewodnikach insektowych:
      + [Notes pozyskiwania danych](https://aka.ms/aml-data-prep-ingestion-nb)
      + [Notes magazynu danych](https://aka.ms/aml-data-prep-datastore-nb)

+ **Poprawki i ulepszenia błędów**
  + Naprawiono problemy z konwersją typu kolumny:
  + Teraz poprawnie konwertuje kolumnę logiczną lub numeryczną na kolumnę logiczną.
  + Teraz nie zakończy się niepowodzeniem podczas próby ustawienia kolumny daty jako typu daty.
  + Ulepszone typy JoinType i towarzysząca mu dokumentacja referencyjna. Podczas łączenia dwóch przepływów danych można teraz określić jeden z następujących typów sprzężenia:
    + BRAK, MECZ, WEWNĘTRZNY, NIEZRÓWNANY, LEFTANTI, LEFTOUTER, NIEDOŚCIGNIONY, RIGHTANTI, RIGHTOUTER, FULLANTI, FULL.
  + Ulepszone wnioskowanie typu danych w celu rozpoznania większej liczby formatów dat.

## <a name="2019-05-06"></a>2019-05-06

### <a name="azure-portal"></a>Azure Portal

W witrynie Azure portal możesz teraz:
+ Tworzenie i uruchamianie zautomatyzowanych eksperymentów ml
+ Utwórz maszynę wirtualną notesu, aby wypróbować przykładowe notesy Jupyter lub własne.
+ Zupełnie nowa sekcja Tworzenia (Wersja zapoznawcza) w obszarze roboczym usługi Azure Machine Learning, która obejmuje automatyczne uczenie maszynowe, interfejs wizualny i hostowane maszyny wirtualne notesu
    + Automatyczne tworzenie modelu przy użyciu automatycznego uczenia maszynowego
    + Użyj interfejsu wizualnego przeciągania i upuszczania, aby przeprowadzić eksperymenty
    + Tworzenie maszyny Wirtualnej notesu w celu eksplorowania danych, tworzenia modeli i wdrażania usług.
+ Aktualizacja wykresu na żywo i metryki w raportach uruchamiania i uruchamianiu stron szczegółów
+ Zaktualizowano przeglądarkę plików dla dzienników, wyjść i migawek na stronach Szczegółów uruchamiania.
+ Nowe i ulepszone środowisko tworzenia raportów na karcie Eksperymenty.
+ Dodano możliwość pobrania pliku config.json ze strony Przegląd obszaru roboczego usługi Azure Machine Learning.
+ Obsługa tworzenia obszaru roboczego usługi Azure Machine Learning z obszaru roboczego usługi Azure Databricks.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.33
+ **Nowe funkcje**
  + Metoda _Workspace.create_ akceptuje teraz domyślne konfiguracje klastrów dla klastrów procesora CPU i GPU.
  + Jeśli tworzenie obszaru roboczego nie powiedzie się, zasoby zależne są czyszczone.
  + Domyślna jednostka SKU rejestru kontenerów platformy Azure została przełączona na podstawową.
  + Usługa Azure Container Registry jest tworzony leniwie, gdy jest to konieczne do uruchomienia lub tworzenia obrazu.
  + Obsługa środowisk dla przebiegów szkoleniowych.

### <a name="notebook-virtual-machine"></a>Maszyna wirtualna notesu 

Użyj maszyny Wirtualnej notesu jako bezpiecznego, gotowego do organizacji środowiska hostingowego dla notesów Jupyter, w którym możesz programować eksperymenty uczenia maszynowego, wdrażać modele jako punkty końcowe sieci web i wykonywać wszystkie inne operacje obsługiwane przez zestaw Azure Machine Learning SDK przy użyciu języka Python.Zapewnia kilka możliwości:
+ [Szybko rozdziel wstępnie skonfigurowaną maszynę wirtualną notesu](tutorial-1st-experiment-sdk-setup.md) z najnowszą wersją zestawu SDK usługi Azure Machine Learning i powiązanych pakietów.
+ Program Access jest zabezpieczony za pomocą sprawdzonych technologii, takich jak HTTPS, uwierzytelnianie i autoryzacja usługi Azure Active Directory.
+ Niezawodne przechowywanie notesów i kodu w chmurze na koncie magazynu obiektów blob usługi Azure Machine Learning Workspace.Możesz bezpiecznie usunąć maszynę wirtualną notebooka bez utraty pracy.
+ Preinstalowane przykładowe notesy do eksplorowania i eksperymentowania z funkcjami usługi Azure Machine Learning.
+ Pełne możliwości dostosowywania maszyn wirtualnych platformy Azure, dowolnego typu maszyny wirtualnej, dowolnych pakietów, dowolnych sterowników. 

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Wersja zestawowa SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.33 została wydana.

+ Modele przyspieszane sprzętowo usługi Azure ML w [układach FPGA](how-to-deploy-fpga-web-service.md) są ogólnie dostępne.
  + Teraz można [użyć pakietu azureml-accel-models](how-to-deploy-fpga-web-service.md) do:
    + Szkolenie ciężarów obsługiwanej sieci neuronowej głębokiej (ResNet 50, ResNet 152, DenseNet-121, VGG-16 i SSD-VGG)
    + Korzystanie z nauki transferu z obsługiwanym DNN
    + Zarejestruj model za pomocą usługi zarządzania modelami i konteneryzuj model
    + Wdrażanie modelu na maszynie wirtualnej platformy Azure przy obliczu umowy FPGA w klastrze usługi Azure Kubernetes (AKS)
  + Wdrażanie kontenera na urządzeniu serwera [usługi Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview)
  + Ocena danych za pomocą punktu końcowego gRPC za pomocą tego [przykładu](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

+ Przesączanie funkcji, aby umożliwić dynamiczne dodawanie :::no-loc text="featurizers"::: w celu optymalizacji wydajności. Nowość :::no-loc text="featurizers":::: osadzanie pracy, waga dowodów, kodowanie docelowe, kodowanie celu tekstowego, odległość klastra
+ Inteligentne CV do obsługi pociągu / prawidłowe podziały wewnątrz zautomatyzowanego ML
+ Niewiele zmian optymalizacji pamięci i poprawa wydajności środowiska uruchomieniowego
+ Poprawa wydajności w objaśnieniu modelu
+ Konwersja modelu ONNX dla uruchomienia lokalnego
+ Dodano obsługę podpróbkowania
+ Inteligentne zatrzymywanie, gdy nie zdefiniowano kryteriów wyjścia
+ Zespoły ułożone

+ Prognozowanie szeregów czasowych
  + Nowa funkcja prognozy prognozy
  + Teraz można użyć sprawdzania poprawności krzyżowej pochodzenia stopniowego na danych szeregów czasowych
  + Dodano nowe funkcje do konfigurowania opóźnień szeregów czasowych
  + Dodano nowe funkcje do obsługi funkcji agregacji okien tocznych
  + Nowe wykrywanie holiday i featurizer, gdy kod kraju jest zdefiniowany w ustawieniach eksperymentu

+ Azure Databricks
  + Włączone prognozowanie szeregów czasowych i możliwości tłumaczenia modelu
  + Teraz możesz anulować i wznowić (kontynuować) zautomatyzowane eksperymenty uczenia maszynowego
  + Dodano obsługę przetwarzania wielordzeniowego

### <a name="mlops"></a>MlOps ( mlops )
+ **Wdrażanie lokalne & debugowanie do oceniania kontenerów**<br/> Teraz można wdrożyć model ml lokalnie i szybko iterować na pliku oceniania i zależności, aby upewnić się, że zachowują się zgodnie z oczekiwaniami.

+ **Wprowadzono inferenceConfig & Model.deploy()**<br/> Wdrożenie modelu obsługuje teraz określanie folderu źródłowego ze skryptem wejścia, tak samo jak RunConfig.  Ponadto wdrożenie modelu zostało uproszczone do jednego polecenia.

+ **Śledzenie odwołań git**<br/> Klienci od pewnego czasu żądają podstawowych funkcji integracji Git, ponieważ pomaga to w utrzymaniu kompleksowej ścieżki inspekcji. Zaimplementowaliśmy śledzenie między głównymi jednostkami w usłudze Azure ML dla metadanych związanych z git (repozytorium, zatwierdzenie, czysty stan). Te informacje będą zbierane automatycznie przez SDK i CLI.

+ **Profilowanie modelu & usługi sprawdzania poprawności**<br/> Klienci często skarżą się na trudności w prawidłowym rozmiarze obliczeń związanych z ich usługą wnioskowania. Dzięki naszej usłudze profilowania modelu klient może dostarczyć przykładowe dane wejściowe, a my przedstawimy 16 różnych konfiguracji procesora / pamięci, aby określić optymalną wielkość dla wdrożenia.

+ **Przynieś własny obraz bazowy do wnioskowania**<br/> Innym częstym zarzutem była trudność w przejściu od eksperymentów do wnioskowania zależności udostępniania RE. Dzięki naszej nowej możliwości udostępniania obrazów podstawowych możesz teraz ponownie wykorzystać obrazy podstawowe eksperymentów, zależności i wszystkie, aby do tego wnioskować. Powinno to przyspieszyć wdrożenia i zmniejszyć lukę od wewnętrznej do zewnętrznej pętli.

+ **Ulepszone środowisko generowania schematu swaggera**<br/> Nasza poprzednia metoda generowania swagger była podatna na błędy i niemożliwa do zautomatyzowania. Mamy nowy sposób generowania schematów swagger z dowolnej funkcji Pythona za pośrednictwem dekoratorów. Mamy open-source ten kod i nasz protokół generowania schematu nie jest powiązany z platformą Azure ML.

+ **Narzędzie cli usługi Azure ML jest ogólnie dostępne (GA)**<br/> Modele można teraz wdrożyć za pomocą jednego polecenia interfejsu wiersza polecenia. Otrzymaliśmy wspólne opinie klientów, że nikt nie wdraża modelu ML z notesu Jupyter. [**Zaktualizowano dokumentację referencyjną interfejsu wiersza polecenia.**](https://aka.ms/azmlcli)


## <a name="2019-04-22"></a>2019-04-22

Wersja zestawu SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.30 została wydana.

Wprowadzono, [`PipelineEndpoint`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?view=azure-ml-py) aby dodać nową wersję opublikowanego potoku przy zachowaniu tego samego punktu końcowego.

## <a name="2019-04-17"></a>2019-04-17

### <a name="azure-machine-learning-data-prep-sdk-v112"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.2

Uwaga: Zestaw SDK języka Python `numpy` `pandas` prep danych nie będzie już instalował się i pakiety. Zobacz [zaktualizowane instrukcje instalacji](https://github.com/Microsoft/AMLDataPrepDocs).

+ **Nowe funkcje**
  + Teraz można użyć transformacji przestawnej.
    + Przewodnik: Notes [przestawny](https://aka.ms/aml-data-prep-pivot-nb)
  + Teraz można używać wyrażeń regularnych w funkcjach natywnych.
    + Przykłady:
      + `dflow.filter(dprep.RegEx('pattern').is_match(dflow['column_name']))`
      + `dflow.assert_value('column_name', dprep.RegEx('pattern').is_match(dprep.value))`
  + Teraz można `to_upper`  używać `to_lower`  i funkcji w języku wyrażeń.
  + Teraz w profilu danych można zobaczyć liczbę unikatowych wartości każdej kolumny.
  + W przypadku niektórych często używanych kroków czytnika można teraz przekazać w argumie. `infer_column_types` Jeśli jest ustawiona na `True`, Data Prep spróbuje wykryć i automatycznie przekonwertować typy kolumn.
    + `inference_arguments`jest teraz przestarzała.
  + Teraz możesz `Dataflow.shape`zadzwonić .

+ **Poprawki i ulepszenia błędów**
  + `keep_columns` teraz akceptuje dodatkowy opcjonalny argument, `validate_column_exists`który `keep_columns` sprawdza, czy wynik będzie zawierać kolumny.
  + Wszystkie kroki czytelnika (które odczytywane z `verify_exists`pliku) akceptują teraz dodatkowy opcjonalny argument .
  + Poprawiono wydajność odczytu z ram danych pand i uzyskiwania profili danych.
  + Naprawiono błąd, który powodował, że krojenie pojedynczego kroku z przepływu danych nie powiodło się przy jednym indeksie.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Teraz można ponownie przesłać istniejące uruchomienie skryptu w istniejącym klastrze obliczeniowym zdalnym.
  + Teraz można uruchomić opublikowany potok z nowymi parametrami na karcie Potoki.
  + Uruchom szczegóły teraz obsługuje nową przeglądarkę plików migawka. Migawkę katalogu można wyświetlić podczas przesyłania określonego przebiegu. Można również pobrać notes, który został przesłany do rozpoczęcia biegu.
  + Teraz można anulować nadrzędne uruchamia z witryny Azure portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.23

+ **Nowe funkcje**
  + Zestaw SDK usługi Azure Machine Learning obsługuje teraz język Python 3.7.
  + Estymatory DNN usługi Azure Machine Learning zapewniają teraz wbudowaną obsługę wielu wersji. Na przykład `TensorFlow`  estymator akceptuje `framework_version` teraz parametr, a użytkownicy mogą określić wersję "1.10" lub "1.12". Aby uzyskać listę wersji obsługiwanych przez bieżącą wersję `get_supported_versions()` SDK, należy wywołać żądaną klasę framework (na przykład `TensorFlow.get_supported_versions()`).
  Aby uzyskać listę wersji obsługiwanych przez najnowszą wersję SDK, zobacz [dokumentację DNN Estymator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn?view=azure-ml-py).

### <a name="azure-machine-learning-data-prep-sdk-v111"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.1

+ **Nowe funkcje**
  + Można odczytać wiele źródeł Datastore/DataPath/DataReference przy użyciu read_* przekształceń.
  + Można wykonać następujące operacje na kolumnach, aby utworzyć nową kolumnę: dzielenie, piętro, modulo, moc, długość.
  + Przygotowanie danych jest teraz częścią pakietu diagnostyki usługi Azure ML i domyślnie rejestruje informacje diagnostyczne.
    + Aby wyłączyć tę funkcję, ustaw tę zmienną środowiskową na true: DISABLE_DPREP_LOGGER

+ **Poprawki i ulepszenia błędów**
  + Ulepszona dokumentacja kodu dla często używanych klas i funkcji.
  + Naprawiono błąd w auto_read_file, który nie powodował odczytu plików programu Excel.
  + Dodano opcję zastępowanie folderu w read_pandas_dataframe.
  + Poprawiono wydajność instalacji zależności dotnetcore2 i dodano obsługę Fedory 27/28 i Ubuntu 1804.
  + Poprawiono wydajność odczytu z obiektów blob platformy Azure.
  + Wykrywanie typu kolumny obsługuje teraz kolumny typu Long.
  + Naprawiono błąd, który powodował wyświetlanie niektórych wartości dat jako znaczniki czasu zamiast obiektów randki języka Python.
  + Naprawiono błąd, który powodował, że niektóre liczby typów były wyświetlane jako dublety zamiast liczby całkowite.


## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.21

+ **Nowe funkcje**
  + Metoda *azureml.core.Run.create_children* umożliwia tworzenie wielu uruchomień podrzędnych z małym opóźnieniem za pomocą jednego wywołania.

### <a name="azure-machine-learning-data-prep-sdk-v110"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.1.0

+ **Fundamentalne zmiany**
  + Koncepcja pakietu przygotowania danych została przestarzała i nie jest już obsługiwana. Zamiast utrwalać wiele przepływów danych w jednym pakiecie, można utrwalić przepływy danych indywidualnie.
    + Przewodnik: [Otwieranie i zapisywanie notesu przepływów danych](https://aka.ms/aml-data-prep-open-save-dataflows-nb)

+ **Nowe funkcje**
  + Przygotowanie danych może teraz rozpoznawać kolumny, które pasują do określonego typu semantycznego i odpowiednio podzielić. Obecnie obsługiwane typy STypes obejmują: adres e-mail, współrzędne geograficzne (szerokość & długość geograficzna), adresy IPv4 i IPv6, numer telefonu w USA i kod pocztowy USA.
    + Przewodnik inaucyjnie: [Notes typów semantycznych](https://aka.ms/aml-data-prep-semantic-types-nb)
  + Przygotowawcze do danych obsługuje teraz następujące operacje generowania kolumny wynikowej z dwóch kolumn liczbowych: odejmowanie, pomnożenie, dzielenie i modulo.
  + Można wywołać `verify_has_data()` przepływ danych, aby sprawdzić, czy przepływ danych będzie produkować rekordy, jeśli wykonywane.

+ **Poprawki i ulepszenia błędów**
  + Można teraz określić liczbę pojemników używanych w histogramie dla profili kolumn liczbowych.
  + Transformacja `read_pandas_dataframe` wymaga teraz DataFrame mieć ciąg- lub bajt-typed nazwy kolumn.
  + Naprawiono błąd `fill_nulls` w transformacji, w którym wartości nie były poprawnie wypełniane, jeśli brakuje kolumny.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.18

 + **Zmiany**
   + Pakiet azureml-tensorboard zastępuje azureml-contrib-tensorboard.
   + W tej wersji można skonfigurować konto użytkownika w zarządzanym klastrze obliczeniowym (amlcompute), podczas tworzenia go. Można to zrobić, przekazując te właściwości w konfiguracji inicjowania obsługi administracyjnej. Więcej informacji można znaleźć w [dokumentacji referencyjnej SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.0.17

+ **Nowe funkcje**
  + Teraz obsługuje dodawanie dwóch kolumn liczbowych w celu wygenerowania kolumny wynikowej przy użyciu języka wyrażenia.

+ **Poprawki i ulepszenia błędów**
  + Poprawiono dokumentację i sprawdzanie parametrów pod kątem random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.0.16

+ **Poprawka**
  + Rozwiązano problem z uwierzytelnianiem jednostki usługi, który był spowodowany zmianą interfejsu API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.17

+ **Nowe funkcje**

  + Usługa Azure Machine Learning zapewnia teraz pierwszorzędową obsługę popularnej struktury DNN Chainer. Za [`Chainer`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) pomocą klasy użytkownicy mogą łatwo szkolić i wdrażać modele Chainer.
    + Dowiedz się, jak [prowadzić szkolenia rozproszone z ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/distributed-chainer/distributed-chainer.ipynb)
    + Dowiedz się, jak [uruchomić dostrajanie hiperparametryczne za pomocą funkcji Chainer za pomocą hyperdrive](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Potoki usługi Azure Machine Learning dodano możliwość wyzwalania potoku uruchomić na podstawie modyfikacji magazynu danych. Notes [harmonogramu potoku](https://aka.ms/pl-schedule) jest aktualizowany w celu zaprezentowania tej funkcji.

+ **Poprawki i ulepszenia błędów**
  + Dodaliśmy obsługę potoków usługi Azure Machine Learning do ustawiania właściwości source_directory_data_store do żądanego magazynu danych (takiego jak magazyn obiektów blob) w [witrynach RunConfigurations,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) które są dostarczane do [pythonscriptstep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) Domyślnie kroki używają magazynu plików platformy Azure jako zapasowego magazynu danych, który może napotkać problemy z ograniczaniem przepustowości, gdy jednocześnie wykonywana jest duża liczba kroków.

### <a name="azure-portal"></a>Azure Portal

+ **Nowe funkcje**
  + Nowe środowisko edytora tabel przeciągania i upuszczania dla raportów. Użytkownicy mogą przeciągnąć kolumnę ze studni do obszaru tabeli, w którym zostanie wyświetlony podgląd tabeli. Kolumny można zmieniać.
  + Przeglądarka nowych plików dzienników
  + Łącza do przebiegów eksperymentów, obliczeń, modeli, obrazów i wdrożeń z karty Działania

### <a name="azure-machine-learning-data-prep-sdk-v1015"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.0.15

+ **Nowe funkcje**
  + Przygotowanie danych obsługuje teraz zapisywanie strumieni plików z przepływu danych. Zapewnia również możliwość manipulowania nazwami strumienia plików w celu utworzenia nowych nazw plików.
    + Przewodnik poradnik: [Praca z notesem strumieni plików](https://aka.ms/aml-data-prep-file-stream-nb)

+ **Poprawki i ulepszenia błędów**
  + Poprawiono wydajność t-Digest w dużych zestawach danych.
  + Przygotowanie danych obsługuje teraz odczyt danych z datapath.
  + Jedno kodowanie na gorąco działa teraz na kolumnach logicznych i liczbowych.
  + Inne różne poprawki błędów.

## <a name="2019-02-11"></a>2019-02-11

### <a name="azure-machine-learning-sdk-for-python-v1015"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.15

+ **Nowe funkcje**
  + Potoki usługi Azure Machine Learning dodały usługę AzureBatchStep[(notebook),](https://aka.ms/pl-azbatch)funkcję HyperDriveStep (notes) i funkcję planowania opartego na czasie[(notes).](https://aka.ms/pl-schedule)
  +  DataTranferStep zaktualizowany do pracy z usługą Azure SQL Server i bazy danych platformy Azure dla postgreSQL ([notebook](https://aka.ms/pl-data-trans)).

+ **Zmiany**
  + Przestarzały `PublishedPipeline.get_published_pipeline` na `PublishedPipeline.get`rzecz .
  + Przestarzały `Schedule.get_schedule` na `Schedule.get`rzecz .

### <a name="azure-machine-learning-data-prep-sdk-v1012"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.0.12

+ **Nowe funkcje**
  + Przygotowanie danych obsługuje teraz odczytywanie z bazy danych SQL platformy Azure przy użyciu magazynu danych.

+ **Zmiany**
  + Poprawiono wydajność pamięci niektórych operacji na dużych danych.
  + `read_pandas_dataframe()`teraz `temp_folder` wymaga określenia.
  + Właściwość `name` `ColumnProfile` na została przestarzała `column_name` - użyj zamiast tego.

## <a name="2019-01-28"></a>2019-01-28

### <a name="azure-machine-learning-sdk-for-python-v1010"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.10

+ **Zmiany**:
  + Zestaw Azure ML SDK nie ma już pakietów interfejsu azure-cli jako zależności. W szczególności zależności azure-cli-core i azure-cli-profile zostały usunięte z azureml-core. Są to zmiany wpływające na użytkownika:
      + Jeśli wykonujesz "az login", a następnie za pomocą azureml-sdk, zestaw SDK wykona logowanie kodu przeglądarki lub urządzenia jeszcze raz. Nie będzie używać żadnych poświadczeń stanu utworzonego przez "az login".
    + W przypadku uwierzytelniania interfejsu WIERSZA POLECENIA platformy Azure, na przykład przy użyciu "logowania az", należy użyć klasy _azureml.core.authentication.AzureCliAuthentication._ W przypadku uwierzytelniania interfejsu WIERSZA POLECENIA platformy Azure należy _zainstalować platformę Azure-Cli_ w środowisku języka Python, w którym zainstalowano azureml-sdk.
    + Jeśli robisz "az login" przy użyciu jednostki usługi dla automatyzacji, zalecamy użycie _azureml.core.authentication.ServicePrincipalAuthentication_ klasy, jak azureml-sdk nie będzie używać stanu poświadczeń utworzonych przez azure CLI.

+ **Poprawki błędów:** Ta wersja zawiera głównie drobne poprawki błędów

### <a name="azure-machine-learning-data-prep-sdk-v108"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.0.8

+ **Poprawki błędów**
  + Poprawiono wydajność uzyskiwania profili danych.
  + Naprawiono drobne błędy związane z raportowaniem błędów.

### <a name="azure-portal-new-features"></a>Portal Azure: nowe funkcje
+ Nowe środowisko przeciągania i upuszczania wykresów dla raportów. Użytkownicy mogą przeciągnąć kolumnę lub atrybut ze studni do obszaru wykresu, w którym system automatycznie wybierze odpowiedni typ wykresu dla użytkownika na podstawie typu danych. Użytkownicy mogą zmienić typ wykresu na inne odpowiednie typy lub dodać dodatkowe atrybuty.

    Obsługiwane typy wykresów:
    - Wykres liniowy
    - Histogram
    - Skumulowany wykres słupkowy
    - Wykres pudełkowy
    - Wykres punktowy
    - Wykres bąbelkowy
+ Portal dynamicznie generuje teraz raporty dla eksperymentów. Gdy użytkownik przesyła przebieg do eksperymentu, raport zostanie automatycznie wygenerowany z zarejestrowanych metryk i wykresów, aby umożliwić porównanie w różnych przebiegów.

## <a name="2019-01-14"></a>2019-01-14

### <a name="azure-machine-learning-sdk-for-python-v108"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.8

+ **Poprawki błędów:** Ta wersja zawiera głównie drobne poprawki błędów

### <a name="azure-machine-learning-data-prep-sdk-v107"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.0.7

+ **Nowe funkcje**
  + Ulepszenia magazynu danych (udokumentowane w [instrukcjach obsługi magazynu danych)](https://aka.ms/aml-data-prep-datastore-nb)
    + Dodano możliwość odczytu i zapisu do udziału plików azure i ADLS magazynów danych w skalowaniu w górę.
    + Podczas korzystania z magazynów danych, Data Prep obsługuje teraz przy użyciu uwierzytelniania jednostkowego usługi zamiast uwierzytelniania interaktywnego.
    + Dodano obsługę adresów URL wasb i wasbs.

## <a name="2019-01-09"></a>2019-01-09

### <a name="azure-machine-learning-data-prep-sdk-v106"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.0.6

+ **Poprawki błędów**
  + Naprawiono błąd z odczytem z publicznych czytelnych kontenerów obiektów blob platformy Azure na platformie Spark

## <a name="2018-12-20"></a>2018-12-20

### <a name="azure-machine-learning-sdk-for-python-v106"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.6
+ **Poprawki błędów:** Ta wersja zawiera głównie drobne poprawki błędów

### <a name="azure-machine-learning-data-prep-sdk-v104"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 1.0.4

+ **Nowe funkcje**
  + `to_bool`funkcja umożliwia teraz przekonwertowanie niedopasowanych wartości na wartości błędów. Jest to nowe domyślne zachowanie `to_bool` `set_column_types`niezgodności dla i , podczas gdy poprzednie domyślne zachowanie było konwertować niedopasowane wartości do False.
  + Podczas `to_pandas_dataframe`wywoływania istnieje nowa opcja interpretowania wartości null/brakujących w kolumnach liczbowych jako NaN.
  + Dodano możliwość sprawdzenia typu zwracanego niektórych wyrażeń, aby zapewnić spójność typu i zakończyć się niepowodzeniem.
  + Teraz można `parse_json` wywołać analizowanie wartości w kolumnie jako obiekty JSON i rozwijać je do wielu kolumn.

+ **Poprawki błędów**
  + Naprawiono błąd, `set_column_types` który uległ awarii w Pythonie 3.5.2.
  + Naprawiono błąd, który uległ awarii podczas łączenia się z magazynem danych przy użyciu obrazu AML.

+ **Aktualizacje**
  * [Przykładowe notesy](https://aka.ms/aml-data-prep-notebooks) dotyczące samouczków, studiów przypadku i poradniki.

## <a name="2018-12-04-general-availability"></a>2018-12-04: Ogólna dostępność

Usługa Azure Machine Learning jest teraz ogólnie dostępna.

### <a name="azure-machine-learning-compute"></a>Środowisko obliczeniowe usługi Azure Machine Learning
W tej wersji ogłaszamy nowe zarządzane środowisko obliczeniowe za pośrednictwem [usługi Azure Machine Learning Compute.](how-to-set-up-training-targets.md#amlcompute) Ten cel obliczeniowy zastępuje obliczenia usługi Azure Batch AI dla usługi Azure Machine Learning.

Ten cel obliczeniowy:
+ Służy do szkolenia modelu i wnioskowania/oceniania partii
+ Jest obliczana jedno- i wielowęzłowa
+ Czy zarządzanie klastrem i planowanie zadań dla użytkownika
+ Domyślnie skaluje automatycznie
+ Obsługa zasobów procesora CPU i GPU
+ Umożliwia korzystanie z maszyn wirtualnych o niskim priorytecie w celu obniżenia kosztów

Obliczenia usługi Azure Machine Learning można tworzyć w języku Python, przy użyciu witryny Azure portal lub interfejsu wiersza polecenia. Musi zostać utworzony w obszarze obszaru roboczego i nie można go dołączyć do żadnego innego obszaru roboczego. Ten obiekt docelowy obliczeń używa kontenera platformy Docker do uruchomienia i pakiety zależności do replikowania tego samego środowiska we wszystkich węzłach.

> [!Warning]
> Zalecamy utworzenie nowego obszaru roboczego w celu użycia danych obliczeniowych usługi Azure Machine Learning. Istnieje zdalna szansa, że użytkownicy próbujący utworzyć obliczenia usługi Azure Machine Learning z istniejącego obszaru roboczego mogą zobaczyć błąd. Istniejące obliczenia w obszarze roboczym powinny nadal działać bez zmian.

### <a name="azure-machine-learning-sdk-for-python-v102"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 1.0.2
+ **Fundamentalne zmiany**
  + W tej wersji usuwamy obsługę tworzenia maszyny wirtualnej z usługi Azure Machine Learning. Nadal można dołączyć istniejącą maszynę wirtualną w chmurze lub zdalny serwer lokalny.
  + Usuwamy również obsługę usługi BatchAI, z których wszystkie powinny być teraz obsługiwane za pośrednictwem usługi Azure Machine Learning Compute.

+ **Nowy**
  + W przypadku potoków uczenia maszynowego:
    + [EstimatorStep (EstimatorStep)](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimator_step.estimatorstep?view=azure-ml-py)
    + [HyperDriveStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyper_drive_step.hyperdrivestep?view=azure-ml-py)
    + [Krok mpistep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.mpi_step.mpistep?view=azure-ml-py)


+ **Zaktualizowano**
  + W przypadku potoków uczenia maszynowego:
    + [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) akceptuje teraz runconfig
    + [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) teraz kopiuje do iz źródła danych SQL
    + Planowanie funkcji w zestawie SDK w celu tworzenia i aktualizowania harmonogramów uruchamiania opublikowanych potoków

<!--+ **Bugs fixed**-->

### <a name="azure-machine-learning-data-prep-sdk-v052"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 0.5.2
+ **Fundamentalne zmiany**
  * `SummaryFunction.N`zmieniono nazwę `SummaryFunction.Count`na .

+ **Poprawki**
  * Użyj najnowszego tokenu uruchamiania AML podczas odczytywania i zapisywania w magazynach danych w zdalnych uruchomieniach. Wcześniej, jeśli token uruchamiania AML jest aktualizowany w języku Python, środowisko uruchomieniowe przygotowania danych nie zostanie zaktualizowane za pomocą zaktualizowanego tokenu uruchamiania AML.
  * Dodatkowe jaśniejsze komunikaty o błędach
  * to_spark_dataframe() nie upaść, gdy Platforma `Kryo` Spark używa serializacji
  * Inspektor zliczania wartości może teraz wyświetlić ponad 1000 unikatowych wartości
  * Random Split nie kończy się niepowodzeniem, jeśli oryginalny przepływ danych nie ma nazwy

+ **Więcej informacji**
  * [Zestaw SDK przygotowywania danych usługi Azure Machine Learning](https://aka.ms/data-prep-sdk)

### <a name="docs-and-notebooks"></a>Dokumenty i notesy
+ Rurociągi ML
  + Nowe i zaktualizowane notesy do rozpoczynania pracy z potokami, zakresem partii i przykładami transferu stylu:https://aka.ms/aml-pipeline-notebooks
  + Dowiedz się, jak [utworzyć pierwszy potok](how-to-create-your-first-pipeline.md)
  + Dowiedz się, jak [uruchamiać prognozy partii przy użyciu potoków](how-to-use-parallel-run-step.md)
+ Cel obliczeniowy usługi Azure Machine Learning
  + [Przykładowe notesy](https://aka.ms/aml-notebooks) są teraz aktualizowane w celu użycia nowych zarządzanych obliczeń.
  + [Dowiedz się więcej o tych obliczeniach](how-to-set-up-training-targets.md#amlcompute)

### <a name="azure-portal-new-features"></a>Portal Azure: nowe funkcje
+ Tworzenie typów [obliczeniowych usługi Azure Machine Learning](how-to-set-up-training-targets.md#amlcompute) i zarządzanie nimi w portalu.
+ Monitorowanie użycia przydziału i [przydziału żądań](how-to-manage-quotas.md) dla obliczeń usługi Azure Machine Learning.
+ Wyświetlanie stanu klastra obliczeniowego usługi Azure Machine Learning w czasie rzeczywistym.
+ Dodano obsługę sieci wirtualnej dla tworzenia usługi Azure Machine Learning Compute i Azure Kubernetes Service.
+ Uruchom ponownie opublikowane potoki z istniejącymi parametrami.
+ Nowe [zautomatyzowane wykresy uczenia maszynowego](how-to-understand-automated-ml.md) dla modeli klasyfikacji (wzrost, zyski, kalibracja, wykres ważności funkcji z wytłumaczeniem modelu) i modele regresji (resztki i wykres ważności funkcji z wyjaśnieniem modelu).
+ Potoki można wyświetlać w witrynie Azure portal




## <a name="2018-11-20"></a>2018-11-20

### <a name="azure-machine-learning-sdk-for-python-v0180"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 0.1.80

+ **Fundamentalne zmiany**
  * obszar nazw *azureml.train.widgets* został przeniesiony do *azureml.widgets*.
  * *azureml.core.compute.AmlCompute* przestarzałe następujące klasy - *azureml.core.compute.BatchAICompute* i *azureml.core.compute.DSVMCompute*. Ta ostatnia klasa zostanie usunięta w kolejnych wersjach. Klasa AmlCompute ma teraz łatwiejszą definicję i po prostu potrzebuje vm_size i max_nodes i automatycznie skaluje klaster od 0 do max_nodes po przesłaniu zadania. Nasze [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/training) zostały zaktualizowane o te informacje i powinny podać przykłady użycia. Mamy nadzieję, że podoba Ci się to uproszczenie i wiele bardziej ekscytujących funkcji, które pojawią się w późniejszym wydaniu!

### <a name="azure-machine-learning-data-prep-sdk-v051"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 0.5.1

Dowiedz się więcej o sdku przygotowania danych, czytając [dokumenty referencyjne](https://aka.ms/data-prep-sdk).
+ **Nowe funkcje**
   * Utworzono nową interfejsu wiersza polecenia DataPrep w celu wykonywania pakietów DataPrep i wyświetlania profilu danych dla zestawu danych lub przepływu danych
   * Przeprojektowany interfejs API SetColumnType w celu poprawy użyteczności
   * Zmieniono nazwę smart_read_file na auto_read_file
   * Teraz zawiera pochylenie i kurtozę w profilu danych
   * Może pobierać próbki z próbkowaniem stratified
   * Może odczytywać z plików zip zawierających pliki CSV
   * Można podzielić zestawy danych wierszy mądry z random split (na przykład, do zestawów test-train)
   * Można uzyskać wszystkie typy danych kolumny z przepływu danych lub profilu danych, dzwoniąc`.dtypes`
   * Może uzyskać liczbę wierszy z przepływu danych lub profilu danych, wywołując`.row_count`

+ **Poprawki**
   * Poprawiono długą do podwójnej konwersji
   * Naprawiono potwierdzenia po każdej kolumnie dodawania
   * Naprawiono błąd z problemem fuzzygrouping, który w niektórych przypadkach nie wykrywał grup.
   * Naprawiono funkcję sortowania w celu zapewnienia poszanowania wielokolumnowej kolejności sortowania
   * Stałe i/lub wyrażenia podobne do `pandas` sposobu ich obsługi
   * Poprawiono odczyt ze ścieżki dbfs
   * Komunikaty o błędach były bardziej zrozumiałe
   * Teraz nie kończy się niepowodzeniem podczas odczytu na zdalnym celu obliczeniowym przy użyciu tokenu AML
   * Teraz już nie zawodzi na Linux DSVM
   * Teraz nie ulega już awarii, gdy wartości niebędące ciągami znajdują się w predykatach ciągu
   * Teraz obsługuje błędy potwierdzenia, gdy przepływ danych powinien zakończyć się niepowodzeniem poprawnie
   * Teraz obsługuje lokalizacje magazynu montowane dbutils na platformie Azure Databricks

## <a name="2018-11-05"></a>2018-11-05

### <a name="azure-portal"></a>Azure Portal
Portal Azure dla usługi Azure Machine Learning ma następujące aktualizacje:
  * Nowa karta **Potoki** dla opublikowanych potoków.
  * Dodano obsługę dołączania istniejącego klastra HDInsight jako celu obliczeniowego.

### <a name="azure-machine-learning-sdk-for-python-v0174"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 0.1.74

+ **Fundamentalne zmiany**
  * *Workspace.compute_targets, magazyny danych, eksperymenty, obrazy, modele i *usługi internetowe* są właściwości zamiast metod. Na przykład zastąp *workspace.compute_targets()* *obszarem roboczym.compute_targets*.
  * *Run.get_context* przestarzałe *Run.get_submitted_run*. Ta ostatnia metoda zostanie usunięta w kolejnych wersjach.
  * *PipelineData* klasa oczekuje teraz obiektu magazynu danych jako parametr, a nie datastore_name. Podobnie *Pipeline* akceptuje default_datastore, a nie default_datastore_name.

+ **Nowe funkcje**
  * Przykładowy [notes](https://github.com/Azure/MachineLearningNotebooks/tree/master/pipeline/pipeline-mpi-batch-prediction.ipynb) usługi Azure Machine Learning pipelines używa teraz kroków MPI.
  * Widżet RunDetails dla notesów Jupyter jest aktualizowany w celu wyświetlenia wizualizacji potoku.

### <a name="azure-machine-learning-data-prep-sdk-v040"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 0.4.0

+ **Nowe funkcje**
  * Liczba typów dodana do profilu danych
  * Liczba wartości i histogram jest już dostępny
  * Więcej percentyli w profilu danych
  * Mediana jest dostępna w polu Podsumowanie
  * Python 3.7 jest teraz obsługiwany
  * Po zapisaniu przepływu danych, który zawiera magazyny danych do pakietu DataPrep, informacje magazynu danych zostaną utrwalone jako część pakietu DataPrep
  * Pisanie do magazynu danych jest teraz obsługiwane

+ **Naprawiony**
  * 64-bitowe niepodpisane przepełnienia liczby całkowitej są teraz obsługiwane poprawnie w systemie Linux
  * Poprawiono niepoprawną etykietę tekstową dla plików tekstowych w smart_read
  * Typ kolumny ciągów jest teraz wyświetlany w widoku metryk
  * Liczba typów jest teraz stała, aby wyświetlić ValueKinds mapowane na pojedynczy FieldType zamiast poszczególnych
  * Write_to_csv nie kończy się niepowodzeniem, gdy ścieżka jest dostarczana jako ciąg
  * W przypadku korzystania z funkcji Zamień pozostawienie pustego pola "znajdź" nie zakończy się niepowodzeniem

## <a name="2018-10-12"></a>2018-10-12

### <a name="azure-machine-learning-sdk-for-python-v0168"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 0.1.68

+ **Nowe funkcje**
  * Obsługa wielu dzierżaw podczas tworzenia nowego obszaru roboczego.

+ **Błędy**
  * Nie trzeba już przypinać wersji biblioteki pynacl podczas wdrażania usługi sieci web.

### <a name="azure-machine-learning-data-prep-sdk-v030"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 0.3.0

+ **Nowe funkcje**
  * Dodano metodę transform_partition_with_file(script_path), która pozwala użytkownikom przejść w ścieżce pliku Pythona do wykonania

## <a name="2018-10-01"></a>2018-10-01

### <a name="azure-machine-learning-sdk-for-python-v0165"></a>Zestaw SDK usługi Azure Machine Learning dla języka Python w wersji 0.1.65
[Wersja 0.1.65](https://pypi.org/project/azureml-sdk/0.1.65) zawiera nowe funkcje, więcej dokumentacji, poprawki błędów i więcej [przykładowych notesów.](https://aka.ms/aml-notebooks)

Zobacz [listę znanych problemów,](resource-known-issues.md) aby dowiedzieć się więcej o znanych błędach i obejściach.

+ **Fundamentalne zmiany**
  * Workspace.experiments, Workspace.models, Workspace.compute_targets, Workspace.images, Workspace.web_services return dictionary, poprzednio zwrócona lista. Zobacz dokumentację interfejsu API [azureml.core.Workspace.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py)

  * Automatyczne uczenie maszynowe usunęło znormalizowany błąd kwadratowy z metryk podstawowych.

+ **Hyperdrive**
  * Różne poprawki błędów HyperDrive dla Bayesian, ulepszenia wydajności dla wywołań metryk.
  * Aktualizacja Tensorflow 1.10 z 1.9
  * Optymalizacja obrazu platformy Docker pod kątem rozruchu na zimno.
  * Zadania zgłaszają teraz poprawny stan, nawet jeśli zakończą pracę z kodem błędu innym niż 0.
  * Sprawdzanie poprawności atrybutu RunConfig w pliku SDK.
  * Obiekt uruchamiania hyperdrive obsługuje anulowanie podobne do zwykłego przebiegu: nie ma potrzeby przekazywania żadnych parametrów.
  * Ulepszenia widżetu w celu utrzymania stanu wartości rozwijanych dla rozproszonych przebiegów i uruchomień HyperDrive.
  * TensorBoard i inne pliki dziennika obsługują naprawione dla serwera parametrów.
  * Obsługa mpi firmy Intel(R) po stronie usługi.
  * Poprawka do dostrajania parametrów dla poprawki przebiegu rozproszonego podczas sprawdzania poprawności w BatchAI.
  * Menedżer kontekstu identyfikuje teraz wystąpienie podstawowe.

+ **Środowisko usługi Azure portal**
  * log_table() i log_row() są obsługiwane w szczegółach przebiegu.
  * Automatycznie twórz wykresy dla tabel i wierszy z 1, 2 lub 3 kolumnami liczbowymi i opcjonalną kolumną kategoryczną.

+ **Zautomatyzowane uczenie maszynowe**
  * Ulepszona obsługa błędów i dokumentacja
  * Naprawiono problemy z wydajnością pobierania właściwości uruchamiania.
  * Naprawiono błąd podczas wykonywania.
  * Naprawiono :::no-loc text="ensembling"::: problemy z iteracją.
  * Naprawiono błąd w zawieszaniu treningu na MAC OS.
  * Downsampling makro średnia krzywa PR/ROC w scenariuszu weryfikacji niestandardowej.
  * Usunięto dodatkową logikę indeksu.
  * Usunięto filtr z get_output interfejsu API.

+ **Potoki**
  * Dodano metodę Pipeline.publish(), aby opublikować potok bezpośrednio, bez konieczności uruchamiania wykonania.
  * Dodano metodę PipelineRun.get_pipeline_runs(), aby pobrać przebiegi potoku, które zostały wygenerowane z opublikowanego potoku.

+ **Projekt Brainwave**
  * Zaktualizowano obsługę nowych modeli SI dostępnych w układach FPGA.

### <a name="azure-machine-learning-data-prep-sdk-v020"></a>Zestaw SDK do przygotowania danych usługi Azure Machine Learning w wersji 0.2.0
[Wersja 0.2.0](https://pypi.org/project/azureml-dataprep/0.2.0/) zawiera następujące funkcje i poprawki błędów:

+ **Nowe funkcje**
  * Obsługa kodowania na jednym gorącym
  * Wsparcie dla transformacji kwantylowe

+ **Naprawiony:**
  * Współpracuje z dowolną wersją Tornado, nie ma potrzeby obniżania wersji Tornado
  * Wartość zlicza się dla wszystkich wartości, a nie tylko dla trzech najlepszych

## <a name="2018-09-public-preview-refresh"></a>2018-09 (Publiczne odświeżanie wersji zapoznawczej)

Nowa, odświeżona wersja usługi Azure Machine Learning: Dowiedz się więcej o tej wersji:https://azure.microsoft.com/blog/what-s-new-in-azure-machine-learning-service/


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z omówieniem usługi [Azure Machine Learning](overview-what-is-azure-ml.md).
