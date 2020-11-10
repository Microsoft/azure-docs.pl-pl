---
title: Co nowego w wersji?
titleSuffix: Azure Machine Learning
description: Poznaj najnowsze aktualizacje Azure Machine Learning oraz zestawy SDK języka Python dotyczące uczenia maszynowego i przygotowywania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jmartens
author: j-martens
ms.date: 09/10/2020
ms.openlocfilehash: 5054451b181223d3d6deece6812358cfd08b1e30
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445085"
---
# <a name="azure-machine-learning-release-notes"></a>Informacje o wersji Azure Machine Learning

W tym artykule dowiesz się więcej na temat wydań Azure Machine Learning.  Aby uzyskać pełną zawartość referencyjną SDK, odwiedź stronę referencyjną [**głównego zestawu sdk Azure Machine Learning dla języka Python**](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) .

Zapoznaj się z [listą znanych problemów](resource-known-issues.md) , aby dowiedzieć się więcej o znanych usterkach i obejść.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Etykiety danych dla segmentacji wystąpienia obrazu (Adnotacja z wielokątem) (wersja zapoznawcza)

Typ projektu segmentacji wystąpienia obrazu (adnotacje wielokątne) w ramach etykietowania danych jest teraz dostępny, aby użytkownicy mogli rysować wielokąty i dodawać do nich adnotacje wokół konturów obiektów w obrazach. Użytkownicy będą mogli przypisywać klasy i wielokąta do każdego obiektu, który jest interesujący w obrazie.

Dowiedz się więcej o [etykietowaniu segmentacji wystąpień obrazu](how-to-label-images.md).


## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.17.0
+ **nowe przykłady**
  + Nowe przykładowe repozytorium społecznościowe jest dostępne na https://github.com/Azure/azureml-examples
+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Rozwiązano problem polegający na tym, że get_output może zgłosić XGBoostError.
  + **azureml-automl-runtime**
    + Funkcja oparta na czasie/kalendarzu utworzona przez AutoML będzie teraz mieć prefiks.
    + Naprawiono IndexError w trakcie szkolenia StackEnsemble dla zestawów danych klasyfikacji o dużej liczbie klas i Podpróbkowanie włączone.
    + Rozwiązano problem polegający na tym, że przewidywania VotingRegressor mogą być niedokładne po dopasowaniu modelu.
  + **azureml-core**
    + Dodano dodatkowe szczegóły dotyczące relacji między konfiguracją wdrożenia AKS i pojęciami dotyczącymi usługi Azure Kubernetes.
    + Klient może korzystać z zestawu SDK połączonej usługi, aby połączyć obszar roboczy Synapse z obszarem roboczym AML. CRUD są obsługiwane.
    + Obsługa etykiet klienta środowiska. Użytkownik może etykietować środowiska i odwoływać się do nich za pomocą etykiety.
  + **azureml-dataprep**
    + Lepszy komunikat o błędzie podczas korzystania aktualnie z nieobsługiwanej platformy Spark z Scala 2,12.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Pakiet Azure-Wyjaśnij-model jest oficjalnie przestarzały
  + **azureml-mlflow**
    + Rozwiązano błąd w mlflow. Projects. Run w odniesieniu do zaplecza Azure, gdzie finalizowanie stanu nie zostało prawidłowo obsłużone.
  + **azureml-pipeline-core**
    + Dodaj obsługę tworzenia, wyświetlania i pobierania harmonogramu potoku w oparciu o jeden punkt końcowy potoku.
    +  Ulepszona dokumentacja PipelineData.as_dataset z nieprawidłowym przykładem użycia — użycie PipelineData.as_dataset nieprawidłowego spowoduje teraz wyrzucanie wartości Valueexception
    + Zmieniono Notes potoków HyperDriveStep, aby zarejestrować najlepszy model w PipelineStep bezpośrednio po uruchomieniu HyperDriveStep.
  + **azureml-pipeline-steps**
    + Zmieniono Notes potoków HyperDriveStep, aby zarejestrować najlepszy model w PipelineStep bezpośrednio po uruchomieniu HyperDriveStep.
  + **azureml-train-automl-client**
    + Rozwiązano problem polegający na tym, że get_output może zgłosić XGBoostError.


## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.16.0
+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + AKSWebservice i AKSEndpoints obsługują teraz limity zasobów procesora i pamięci na poziomie poniżej. Te opcjonalne limity mogą być używane przez ustawienie `--cpu-cores-limit` i `--memory-gb-limit` flagi w odpowiednich wywołaniach interfejsu wiersza polecenia
  + **azureml-core**
    + Przypinanie głównych wersji bezpośrednich zależności programu Azure Core
    + AKSWebservice i AKSEndpoints obsługują teraz limity zasobów procesora i pamięci na poziomie poniżej. Więcej informacji na temat [zasobów i limitów Kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)
    + Zaktualizowano run.log_table, aby umożliwić rejestrowanie pojedynczych wierszy.
    + Dodano metodę statyczną `Run.get(workspace, run_id)` w celu pobrania przebiegu tylko przy użyciu obszaru roboczego 
    + Dodano metodę wystąpienia w `Workspace.get_run(run_id)` celu pobrania przebiegu w obszarze roboczym
    + Wprowadzenie do właściwości polecenia w konfiguracji uruchamiania spowoduje, że użytkownicy będą mogli przesłać polecenie zamiast argumentów & skryptu.
  + **azureml-interpret**
    + Poprawiono zachowanie flagi is_raw klienta na platformie Azure — interpretowanie
  + **Azure-SDK**
    + `azureml-sdk` oficjalnie Obsługuj środowisko Python 3,8.
  + **azureml-train-core**
    + Dodawanie środowiska nadzorowanego w środowisku TensorFlow 2,3
    + Wprowadzenie do właściwości polecenia w konfiguracji uruchamiania spowoduje, że użytkownicy będą mogli przesłać polecenie zamiast argumentów & skryptu.
  + **azureml-widgets**
    + Przeprojektowany interfejs dla widżetu uruchamiania skryptu.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.15.0
+ **Poprawki i ulepszenia błędów**
  + **Azure-contrib — interpretacja**
    + Wyjaśniono, że wyjaśnienie WAPNa zostało przeniesione z platformy Azure-contrib-Interpretuj do interpretowania — pakiet Community i objaśnienie obrazu usunięte z pakietu Azure-contrib-interpreter
    + Pulpit nawigacyjny wizualizacji został usunięty z pakietu platformy Azure-contrib-interpreter, wyjaśnienie klient został przeniesiony do programu Azure — Interpretuj pakiet i został uznany za przestarzały w usłudze Azure-contrib-Interpretuj pakiet i notesy zaktualizowane w celu odzwierciedlenia ulepszonego
    + Popraw opisy pakietów PyPi dla platformy Azure-interpretacji, uczenia maszynowego — wyjaśnienie-model, uczenie maszynowe-contrib — interpretowanie i Azure tensorboard
  + **azureml-contrib-notebook**
    + Przypnij zależność nbcovert do < 6, aby Papermill 1. x nadal działała.
  + **azureml-core**
    + Dodano parametry do konstruktora TensorflowConfiguration i MpiConfiguration, aby umożliwić bardziej usprawnione inicjowanie atrybutów klasy bez konieczności od użytkownika ustawiania poszczególnych atrybutów. Dodano klasę PyTorchConfiguration do konfigurowania zadań rozproszonego PyTorch w programie ScriptRunConfig.
    + Przypnij wersję platformy Azure-zarządzanie-zasób, aby naprawić błąd uwierzytelniania.
    + Obsługa Triton bez wdrażania kodu
    + katalogi wyjściowe określone w Run.start_logging () będą teraz śledzone w przypadku korzystania z funkcji run w scenariuszach interaktywnych. Śledzone pliki będą widoczne na ML Studio po wywołaniu metody Run. Complete ()
    + Kodowanie plików można teraz określić podczas tworzenia zestawu danych za pomocą `Dataset.Tabular.from_delimited_files` i `Dataset.Tabular.from_json_lines_files` przez przekazanie `encoding` argumentu. Obsługiwane kodowania to "UTF8", "iso88591", "Latin1", "ASCII", UTF16 "," UTF32 "," utf8bom "i" windows1252 ".
    + Poprawka błędu, gdy obiekt środowiska nie jest przekazaniem do konstruktora ScriptRunConfig.
    + Zaktualizowano przebieg. Cancel (), aby zezwolić na anulowanie lokalnego uruchomienia z innego komputera.
  + **azureml-dataprep**
    +  Rozwiązano problemy z limitem czasu instalowania zestawu danych.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Popraw opisy pakietów PyPi dla platformy Azure-interpretacji, uczenia maszynowego — wyjaśnienie-model, uczenie maszynowe-contrib — interpretowanie i Azure tensorboard
  + **azureml-interpret**
    + Pulpit nawigacyjny wizualizacji został usunięty z pakietu platformy Azure-contrib-interpreter, wyjaśnienie klient został przeniesiony do programu Azure — Interpretuj pakiet i został uznany za przestarzały w usłudze Azure-contrib-Interpretuj pakiet i notesy zaktualizowane w celu odzwierciedlenia ulepszonego
    + pakiet Azure-interpreter został zaktualizowany do programu w zależności od interpretera 0.15.0 Community
    + Popraw opisy pakietów PyPi dla platformy Azure-interpretacji, uczenia maszynowego — wyjaśnienie-model, uczenie maszynowe-contrib — interpretowanie i Azure tensorboard
  + **Azure — potok — rdzeń**
    +  Problem ze stałym potokiem z `OutputFileDatasetConfig` miejscem, w którym system może przestać odpowiadać `register_on_complete` , gdy jest wywoływana z `name` parametrem ustawionym na nazwę istniejącego zestawu danych.
  + **azureml-pipeline-steps**
    + Usunięto stare notesy datakostek.
  + **azureml-tensorboard**
    + Popraw opisy pakietów PyPi dla platformy Azure-interpretacji, uczenia maszynowego — wyjaśnienie-model, uczenie maszynowe-contrib — interpretowanie i Azure tensorboard
  + **azureml-train-automl-runtime**
    + Pulpit nawigacyjny wizualizacji został usunięty z pakietu platformy Azure-contrib-interpreter, wyjaśnienie klient został przeniesiony do programu Azure — Interpretuj pakiet i został uznany za przestarzały w usłudze Azure-contrib-Interpretuj pakiet i notesy zaktualizowane w celu odzwierciedlenia ulepszonego
  + **azureml-widgets**
    + Pulpit nawigacyjny wizualizacji został usunięty z pakietu platformy Azure-contrib-interpreter, wyjaśnienie klient został przeniesiony do programu Azure — Interpretuj pakiet i został uznany za przestarzały w usłudze Azure-contrib-Interpretuj pakiet i notesy zaktualizowane w celu odzwierciedlenia ulepszonego

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.14.0
+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Profilowanie siatki zostało usunięte z zestawu SDK i nie jest już obsługiwane.
  + **azureml-accel-models**
    + pakiet Azure-akceleracja-models obsługuje teraz Tensorflow 2. x
  + **azureml-automl-core**
    + Dodano obsługę błędów w get_output dla przypadków, gdy lokalne wersje Pandas/skryptu sklearn nie pasują do tych używanych podczas szkolenia
  + **azureml-automl-runtime**
    + Naprawiono usterkę, w której iteracje AutoArima kończą się niepowodzeniem z obiektem predykcyjnym i komunikatem: "Wystąpił błąd dyskretny podczas przewidywania".
  + **Azure-Common — interfejs wiersza polecenia**
    + Profilowanie siatki zostało usunięte z zestawu SDK i nie jest już obsługiwane.
  + **Azure contrib — serwer**
    + Opis aktualizacji dla strony Przegląd pakietu dla PyPi.
  + **azureml-core**
    + Profilowanie siatki zostało usunięte z zestawu SDK i nie jest już obsługiwane.
    + Zmniejsz liczbę komunikatów o błędach, jeśli pobieranie obszaru roboczego nie powiedzie się.
    + Nie pokazuj ostrzeżenia w przypadku niepowodzenia pobierania metadanych
    + Nowy krok Kusto i element docelowy obliczeń Kusto.
    + Zaktualizuj dokument dla parametru SKU. Usuń jednostkę SKU w funkcji aktualizacji obszaru roboczego w interfejsie wiersza polecenia i zestawie SDK.
    + Opis aktualizacji dla strony Przegląd pakietu dla PyPi.
    + Zaktualizowana dokumentacja środowisk platformy Azure.
    + Uwidacznianie ustawień zasobów zarządzanych przez usługę dla obszaru roboczego AML w zestawie SDK.
  + **azureml-dataprep**
    + Włącz uprawnienie EXECUTE dla plików dla instalacji zestawu danych.
  + **azureml-mlflow**
    + Zaktualizowana dokumentacja dotycząca MLflowi Azure i przykłady notesu 
    + Nowa obsługa projektów MLflow z zapleczem usługi Azure
    + Obsługa rejestru modelu MLflow
    + Dodano obsługę RBAC dla operacji AzureML-MLflow 
    
  + **azureml-pipeline-core**
    + Ulepszona dokumentacja metod PipelineOutputFileDataset.parse_ *.
    + Nowy krok Kusto i element docelowy obliczeń Kusto.
    + Podana Właściwość Swaggerurl dla jednostki punktu końcowego potoku za pośrednictwem tego użytkownika może zobaczyć definicję schematu dla opublikowanego punktu końcowego potoku.
  + **azureml-pipeline-steps**
    + Nowy krok Kusto i element docelowy obliczeń Kusto.
  + **azureml-telemetry**
    + Opis aktualizacji dla strony Przegląd pakietu dla PyPi.
  + **Azure — szkolenie**
    + Opis aktualizacji dla strony Przegląd pakietu dla PyPi.
  + **azureml-train-automl-client**
    + Dodano obsługę błędów w get_output dla przypadków, gdy lokalne wersje Pandas/skryptu sklearn nie pasują do tych używanych podczas szkolenia
  + **azureml-train-core**
    + Opis aktualizacji dla strony Przegląd pakietu dla PyPi.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.13.0
+ **Funkcje w wersji zapoznawczej**
  + **Azure — rdzeń** Dzięki nowym wyjściowym zestawom danych możesz pisać z powrotem do magazynu w chmurze, w tym obiektów blob, ADLS Gen 1, ADLS Gen 2 i. Można skonfigurować lokalizację danych wyjściowych, sposób wyprowadzania danych wyjściowych (za pośrednictwem instalacji lub przekazywania), czy rejestrować dane wyjściowe na potrzeby późniejszego ponownego użycia i udostępniania i przekazywania danych pośrednich między krokami potoku. Pozwala to na odtwarzalność, udostępnianie, uniemożliwia duplikowanie danych i skutkuje wydajnością kosztów i korzyściami związanymi z produktywnością. [Dowiedz się, jak z niej korzystać](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py)
    
+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Dodano validated_ {platform} _requirements.txt pliku do przypinania wszystkich zależności PIP dla AutoML.
    + Ta wersja obsługuje modele większe niż 4 GB.
    + Uaktualnione zależności AutoML: `scikit-learn` (teraz 0.22.1), `pandas` (teraz 0.25.1), `numpy` (teraz 1.18.2).
  + **azureml-automl-runtime**
    + Ustaw horovod dla DNN text, aby zawsze używać kompresji FP16.
    + Ta wersja obsługuje modele większe niż 4 GB.
    + Rozwiązano problem polegający na tym, że AutoML kończy się niepowodzeniem z ImportError: nie można `RollingOriginValidator` zaimportować nazwy
    + Uaktualnione zależności AutoML: `scikit-learn` (teraz 0.22.1), `pandas` (teraz 0.25.1), `numpy` (teraz 1.18.2).
  + **Azure-contrib-automl-DNN — prognozowanie**
    + Uaktualnione zależności AutoML: `scikit-learn` (teraz 0.22.1), `pandas` (teraz 0.25.1), `numpy` (teraz 1.18.2).
  + **azureml-contrib-fairness**
    + Podaj krótki opis usługi Uczenie maszynowe contrib.
  + **azureml-contrib-pipeline-steps**
    + Dodano komunikat wskazujący, że ten pakiet jest przestarzały, a w zamian użytkownik powinien używać etapów z potokiem.
  + **azureml-core**
    + Dodano polecenie klucza listy dla obszaru roboczego.
    + Dodaj parametry tagów w zestawie SDK obszaru roboczego i interfejsie wiersza polecenia.
    + Rozwiązano problem polegający na tym, że przesyłanie podrzędnego uruchomienia z zestawem danych zakończy się niepowodzeniem z powodu błędu `TypeError: can't pickle _thread.RLock objects` .
    + Dodawanie page_count domyślnego/dokumentacji dla listy modeli ().
    + Zmodyfikuj zestaw SDK&interfejsu wiersza polecenia, aby wykonać adbworkspace parametr, a następnie Dodaj łącze/Odłącz obszar roboczy.
    + Usuń usterkę w zestawie danych. Aktualizacja, która spowodowała aktualizację najnowszej wersji zestawu danych, nie została wywołana w dniu. 
    + Usuń usterkę w Dataset.get_by_name, która będzie zawierała Tagi dla najnowszej wersji zestawu danych, nawet w przypadku pobrania określonej starszej wersji.
  + **azureml-interpret**
    + Dodano wyniki prawdopodobieństwa do objaśnień oceny oceniania na platformie Azure w oparciu o parametr shap_values_output z oryginalnego objaśnienia.
  + **azureml-pipeline-core**
    + Ulepszona `PipelineOutputAbstractDataset.register` Dokumentacja.
  + **azureml-train-automl-client**
    + Uaktualnione zależności AutoML: `scikit-learn` (teraz 0.22.1), `pandas` (teraz 0.25.1), `numpy` (teraz 1.18.2).
  + **azureml-train-automl-runtime**
    + Uaktualnione zależności AutoML: `scikit-learn` (teraz 0.22.1), `pandas` (teraz 0.25.1), `numpy` (teraz 1.18.2).
  + **azureml-train-core**
    + Użytkownicy muszą teraz podać prawidłowy hyperparameter_sampling ARG podczas tworzenia HyperDriveConfig. Ponadto Dokumentacja programu HyperDriveRunConfig została edytowana w celu informowania użytkowników o zaniechaniu HyperDriveRunConfig.
    + Przywracanie domyślnej wersji PyTorch do 1,4.
    + Dodawanie obrazów PyTorch 1,6 & Tensorflow 2,2 i środowiska nadzorowanego.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Środowisko Azure Machine Learning Studio notesy (Aktualizacja z sierpnia)
+ **Nowe funkcje**
  + Nowa strona docelowa wprowadzenia 
  
+ **Funkcje w wersji zapoznawczej**
    + Zbierz funkcje w notesach. Dzięki funkcji [zbierania](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) użytkownicy mogą łatwo czyścić notesy za pomocą programu, dzięki czemu program zbierający korzysta z zautomatyzowanej analizy zależności Twojego notesu, gwarantując, że istotny kod jest przechowywany, ale usuwając wszelkie nieistotne elementy.

+ **Poprawki i ulepszenia błędów**
  + Poprawa szybkości i niezawodności
  + Naprawione błędy trybu ciemnego
  + Naprawione błędy przewijania danych wyjściowych
  + Przykładowe wyszukiwanie przeszukuje teraz całą zawartość wszystkich plików w Azure Machine Learning przykładowym repozytorium notesów
  + Można teraz uruchamiać wielowierszowe komórki R
  + "Zaufanie zawartości tego pliku" jest teraz sprawdzane po raz pierwszy
  + Ulepszone okno dialogowe rozwiązywania konfliktów z nową opcją "Utwórz kopię"
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.12.0

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Dodaj parametry image_name i image_label do modelu. Package (), aby umożliwić zmianę nazwy skompilowanego obrazu pakietu.
  + **azureml-automl-core**
    + AutoML zgłasza nowy kod błędu z, gdy zawartość jest modyfikowana podczas odczytu.
  + **azureml-automl-runtime**
    + Dodano alerty dla użytkownika, gdy dane zawierają brakujące wartości, ale cechowania jest wyłączone.
    + Naprawiono błędy przebiegu podrzędnego, gdy dane zawierają NaN, a cechowania jest wyłączone.
    + AutoML zgłasza nowy kod błędu z, gdy zawartość jest modyfikowana podczas odczytu.
    + Zaktualizowano normalizację dla metryk prognozowania przez ziarno.
    + Ulepszone Obliczanie prognozy quantiles, gdy funkcje lookback są wyłączone.
    + Stała obsługa logicznej klasy rozrzedzonej podczas obliczania objaśnień po AutoML.
  + **azureml-core**
    + Nowa metoda `run.get_detailed_status()` zawiera teraz szczegółowe wyjaśnienie bieżącego stanu przebiegu. Obecnie pokazywane jest tylko wyjaśnienie `Queued` stanu.
    + Dodaj parametry image_name i image_label do modelu. Package (), aby umożliwić zmianę nazwy skompilowanego obrazu pakietu.
    + Nowa metoda `set_pip_requirements()` umożliwiająca jednoczesne ustawienie całej sekcji PIP [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?preserve-view=true&view=azure-ml-py) .
    + Włącz rejestrowanie ADLS Gen2 magazynu danych bez poświadczeń.
    + Ulepszony komunikat o błędzie podczas próby pobrania lub zainstalowania nieprawidłowego typu zestawu danych.
    + Aktualizacja przykładowego notesu filtru zestawu danych szeregów czasowych z wieloma przykładami partition_timestamp, które zapewniają optymalizację filtru.
    + Zmień zestaw SDK i interfejs wiersza polecenia w celu akceptowania identyfikatora subskrypcji, zasobów, obszaru roboczegoname, peConnectionName jako parametrów zamiast ArmResourceId podczas usuwania połączenia z prywatnym punktem końcowym.
    + Eksperymentalny dekoratora zawiera nazwę klasy ułatwiającą identyfikację.
    + Opisy zasobów wewnątrz modeli nie są już automatycznie generowane na podstawie przebiegu.
  + **azureml-datadrift**
    + Oznacz create_from_model interfejs API w DataDriftDetector jako przestarzały.
  + **azureml-dataprep**
    + Ulepszony komunikat o błędzie podczas próby pobrania lub zainstalowania nieprawidłowego typu zestawu danych.
  + **azureml-pipeline-core**
    + Rozwiązano błąd podczas deserializacji wykresu potoku zawierającego zarejestrowane zestawy danych.
  + **azureml-pipeline-steps**
    + RScriptStep obsługuje RSection z platformy Azure. Core. Environment.
    + Usunięto parametr passthru_automl_config z `AutoMLStep` publicznego interfejsu API i został przekonwertowany na parametr tylko wewnętrzny.
  + **azureml-train-automl-client**
    + Usunięto lokalne, asynchroniczne środowisko zarządzane z AutoML. Wszystkie uruchomienia lokalne zostaną uruchomione w środowisku, w którym uruchomiono uruchomienie.
    + Rozwiązano problemy z migawką podczas przesyłania AutoML uruchomionych bez skryptów dostarczonych przez użytkownika.
    + Naprawiono błędy przebiegu podrzędnego, gdy dane zawierają NaN, a cechowania jest wyłączone.
  + **azureml-train-automl-runtime**
    + AutoML zgłasza nowy kod błędu z, gdy zawartość jest modyfikowana podczas odczytu.
    + Rozwiązano problemy z migawką podczas przesyłania AutoML uruchomionych bez skryptów dostarczonych przez użytkownika.
    + Naprawiono błędy przebiegu podrzędnego, gdy dane zawierają NaN, a cechowania jest wyłączone.
  + **azureml-train-core**
    + Dodano obsługę określania opcji PIP (na przykład--Extra-index-URL) w pliku wymagań PIP przekazaną do [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator?preserve-view=true&view=azure-ml-py) parametru through `pip_requirements_file` .


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.11.0

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Nie przeszedł struktury modelu i struktury modelu, która nie została przeniesiona w obiekcie Run w ścieżce rejestracji modelu interfejsu wiersza polecenia
    + Popraw tożsamość amlcompute interfejsu wiersza polecenia, aby wyświetlić identyfikator dzierżawy i Identyfikator podmiotu zabezpieczeń 
  + **azureml-train-automl-client**
    + Dodano get_best_child () do AutoMLRun w celu pobrania najlepszego przebiegu podrzędnego dla AutoML uruchomionego bez pobierania skojarzonego modelu.
    + Dodano obiekt ModelProxy, który umożliwia przewidywalność lub prognozę do uruchomienia w środowisku zdalnego szkolenia bez lokalnego pobierania modelu.
    + Nieobsłużone wyjątki w AutoML teraz wskazują na stronę HTTP o znanych problemach, gdzie można znaleźć więcej informacji o błędach.
  + **azureml-core**
    + Nazwy modeli mogą składać się z od 255 znaków.
    + Typ obiektu zwrotnego Environment.get_image_details () został zmieniony. `DockerImageDetails` zamienionej klasy `dict` , szczegóły obrazu są dostępne z nowych właściwości klasy. Zmiany są zgodne z poprzednimi wersjami.
    + Naprawianie usterki dla Environment.from_pip_requirements () w celu zachowania struktury zależności
    + Naprawiono usterkę, w której log_list się nie powieść, jeśli na tej samej liście uwzględniono liczbę int i Double.
    + Podczas włączania prywatnego linku w istniejącym obszarze roboczym należy pamiętać, że jeśli istnieją obiekty docelowe obliczeń skojarzone z obszarem roboczym, te elementy docelowe nie będą działały, jeśli nie znajdują się w tej samej sieci wirtualnej co prywatny punkt końcowy obszaru roboczego.
    + Wykonane `as_named_input` jako opcjonalne w przypadku używania zestawów danych w eksperymentach i dodanych `as_mount` i `as_download` do `FileDataset` . Nazwa wejściowa zostanie wygenerowana automatycznie `as_mount` , jeśli lub `as_download` jest wywoływana.
  + **azureml-automl-core**
    + Nieobsłużone wyjątki w AutoML teraz wskazują na stronę HTTP o znanych problemach, gdzie można znaleźć więcej informacji o błędach.
    + Dodano get_best_child () do AutoMLRun w celu pobrania najlepszego przebiegu podrzędnego dla AutoML uruchomionego bez pobierania skojarzonego modelu.
    + Dodano obiekt ModelProxy, który umożliwia przewidywania lub prognozę do uruchomienia w środowisku zdalnego szkolenia bez lokalnego pobierania modelu.
  + **azureml-pipeline-steps**
    + Dodano `enable_default_model_output` `enable_default_metrics_output` flagi i do `AutoMLStep` . Flagi te mogą służyć do włączania/wyłączania domyślnych danych wyjściowych.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.10.0

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + W przypadku korzystania z AutoML, jeśli ścieżka jest przenoszona do obiektu AutoMLConfig i jeszcze nie istnieje, zostanie automatycznie utworzona.
    + Użytkownicy mogą teraz określić częstotliwość szeregów czasowych na potrzeby prognozowania zadań przy użyciu `freq` parametru.
  + **azureml-automl-runtime**
    + W przypadku korzystania z AutoML, jeśli ścieżka jest przenoszona do obiektu AutoMLConfig i jeszcze nie istnieje, zostanie automatycznie utworzona.
    + Użytkownicy mogą teraz określić częstotliwość szeregów czasowych na potrzeby prognozowania zadań przy użyciu `freq` parametru.
    + Funkcja prognozowania AutoML obsługuje teraz ocenę kroczącą, która odnosi się do przypadków użycia, że długość testu lub zestawu walidacji jest większa niż horyzont wejściowy, a znana wartość y_pred jest używana jako kontekst prognozowania.
  + **azureml-core**
    + Komunikaty ostrzegawcze będą drukowane, jeśli nie pobrano żadnych plików z magazynu danych w ramach uruchomienia.
    + Dodano dokumentację `skip_validation` do programu `Datastore.register_azure_sql_database method` .
    + Użytkownicy muszą przeprowadzić uaktualnienie do zestawu SDK v 1.10.0 lub nowszego, aby utworzyć zatwierdzony przez siebie prywatny punkt końcowy. Obejmuje to zasób Notes, który można wykorzystać za siecią wirtualną.
    + Uwidocznij NotebookInfo w odpowiedzi na obszar roboczy pobierania.
    + Zmiany w celu wyświetlenia listy obiektów docelowych obliczeń i pobrania obiektu docelowego obliczeń powiodło się w uruchomieniu zdalnym. Funkcje zestawu SDK umożliwiające pobieranie elementów docelowych obliczeniowych i obszarów roboczych obszaru roboczego listy będą teraz działać w zdalnych uruchomieniach.
    + Dodaj komunikaty o zaniechaniu do opisów klas dla klas platformy Azure. Core. Image.
    + Zgłoś wyjątek i oczyść obszar roboczy i zasoby zależne, jeśli Tworzenie prywatnego punktu końcowego nie powiedzie się.
    + Obsługa uaktualnienia SKU obszaru roboczego w ramach metody aktualizacji obszaru roboczego.
  + **azureml-datadrift**
    + Zaktualizuj wersję matplotlib z 3.0.2 do 3.2.1, aby obsługiwała środowisko Python 3,8.
  + **azureml-dataprep**
    + Dodano obsługę źródeł danych adresu URL sieci Web `Range` z `Head` żądaniem lub. 
    + Ulepszona stabilność instalacji i pobierania zestawu danych plików.
  + **azureml-train-automl-client**
    + Rozwiązano problemy związane z usuwaniem `RequirementParseError` z setuptools.
    + Użyj platformy Docker zamiast Conda dla lokalnych przebiegów przesłanych przy użyciu "compute_target =" Local ""
    + Czas trwania iteracji wydrukowany w konsoli został poprawiony. Poprzednio czas trwania iteracji był czasami drukowany jako czas zakończenia przebiegu minus godzina utworzenia. Został skorygowany do czasu zakończenia przebiegu minus godzina rozpoczęcia przebiegu.
    + W przypadku korzystania z AutoML, jeśli ścieżka jest przenoszona do obiektu AutoMLConfig i jeszcze nie istnieje, zostanie automatycznie utworzona.
    + Użytkownicy mogą teraz określić częstotliwość szeregów czasowych na potrzeby prognozowania zadań przy użyciu `freq` parametru.
  + **azureml-train-automl-runtime**
    + Ulepszone dane wyjściowe konsoli w przypadku niepowodzenia najlepszych wyjaśnień modelu.
    + Zmieniono nazwę parametru wejściowego "backlist_models" na "blocked_models".
      + Zmieniono nazwę parametru wejściowego "whitelist_models" na "allowed_models".
    + Użytkownicy mogą teraz określić częstotliwość szeregów czasowych na potrzeby prognozowania zadań przy użyciu `freq` parametru.

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.9.0

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Zamieniono get_model_path () na zmienną środowiskową AZUREML_MODEL_DIR w AutoML automatycznie wygenerowany skrypt oceniania. Dodano również telemetrię do śledzenia błędów podczas inicjowania ().
    + Usunięto możliwość określenia `enable_cache` jako części AutoMLConfig
    + Naprawiono usterkę, której uruchomienie może zakończyć się niepowodzeniem z błędami usługi podczas określonych przebiegów prognozowania
    + Ulepszona obsługa błędów wokół określonych modeli podczas `get_output`
    + Nawiązane połączenie z fitted_model. Dopasuj (X, y) dla klasyfikacji przy użyciu funkcji t Transformer
    + Włączono dostosowany do przepełniania do przodu do zadań prognozowania
    + Zamiast parametrów prognozowania w formacie DICT zostanie użyta Nowa Klasa ForecastingParameters
    + Ulepszone miejsce docelowe automatycznego wykrycia opóźnienia
    + Dodano ograniczoną dostępność wielowęzłowych, rozproszonych cechowania wieloprocesorowych z BERT
  + **azureml-automl-runtime**
    + Prophet teraz jest dodatkiem model sezonowości zamiast mnożenia.
    + Rozwiązano problem polegający na tym, że w przypadku krótkich ziaren, które częstotliwości różnią się od siebie długich ziaren, spowoduje to niepowodzenie uruchomienia.
  + **Azure-contrib-automl-DNN-Vision**
    + Zbieranie statystyk systemu/procesora GPU i średnich dzienników na potrzeby szkoleń i oceniania
  + **Azure-contrib-Mir**
    + Dodano obsługę flagi Enable-App-Insights w ManagedInferencing
  + **azureml-core**
    + Sprawdź poprawność parametru do tych interfejsów API, umożliwiając Pominięcie sprawdzania poprawności, gdy źródło danych nie jest dostępne z bieżącego obliczenia.
      + TabularDataset.time_before (end_time, include_boundary = true, Validate = true)
      + TabularDataset.time_after (start_time, include_boundary = true, Validate = true)
      + TabularDataset.time_recent (time_delta, include_boundary = true, Validate = true)
      + TabularDataset.time_between (start_time, end_time, include_boundary = true, Validate = true)
    + Dodano obsługę filtrowania platformy dla listy modeli i dodano przykład NCD AutoML do tyłu notesu
    + W przypadku Datastore.register_azure_blob_container i Datastore.register_azure_file_share (tylko opcje obsługujące token SAS) Zaktualizowaliśmy ciągi dokumentów dla `sas_token` pola, aby uwzględnić minimalne wymagania dotyczące uprawnień dla typowych scenariuszy odczytu i zapisu.
    + Przestarzałe _with_auth param w ws.get_mlflow_tracking_uri ()
  + **azureml-mlflow**
    + Dodawanie obsługi wdrażania lokalnych modeli file://za pomocą AzureML-MLflow
    + Przestarzałe _with_auth param w ws.get_mlflow_tracking_uri ()
  + **Azure — opendatasets**
    + Ostatnio opublikowane zestawy danych Covid-19 są teraz dostępne z zestawem SDK
  + **azureml-pipeline-core**
    + Ostrzeżenie o wylogowaniu, gdy wartość "Azure-Defaults" nie jest uwzględniona w ramach zależności typu PIP
    + Popraw renderowanie notatek.
    + Dodano obsługę podziałów wierszy w cudzysłowie podczas analizowania rozdzielanych plików na PipelineOutputFileDataset.
    + Klasa PipelineDataset jest przestarzała. Aby uzyskać więcej informacji, zobacz https://aka.ms/dataset-deprecation. Dowiedz się, jak używać zestawu danych z potokiem, zobacz https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline-steps**
    + Aktualizacje doc dotyczące kroków potoku.
    +  Dodano obsługę w programie ParallelRunConfig `load_yaml()` , aby użytkownicy mogli definiować środowiska wbudowane w resztę konfiguracji lub w osobnym pliku
  + **Azure-pociąg-automl-Client**.
    + Usunięto możliwość określenia `enable_cache` jako części AutoMLConfig
  + **azureml-train-automl-runtime**
    + Dodano ograniczoną dostępność wielowęzłowych, rozproszonych cechowania z BERT.
    + Dodano obsługę błędów dla niezgodnych pakietów w programie ADB na podstawie zautomatyzowanych przebiegów uczenia maszynowego.
  + **azureml-widgets**
    + Aktualizacje dokumentów dla programu Azure — elementy widget.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.8.0
  
  + **Funkcje w wersji zapoznawczej**
    + Uczenie maszynowe **— contrib — sprawiedliwość** `azureml-contrib-fairness`Pakiet zapewnia integrację między oceną godziwą i nieuczciwymi rozwiązaniami zaradczymi [Fairlearn](https://fairlearn.github.io) i Azure Machine Learning Studio. W szczególności pakiet włącza pulpity nawigacyjne oceny atrakcyjności modelu do przekazania w ramach uruchomienia platformy Azure i pojawiają się w Azure Machine Learning Studio

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Obsługa pobierania dzienników kontenera init.
    + Dodano nowe polecenia interfejsu CLI do zarządzania ComputeInstance
  + **azureml-automl-core**
    + Użytkownicy mogą teraz włączać iteracje kompletności stosu dla zadań szeregów czasowych z ostrzeżeniem, że potencjalnie overfit.
    + Dodano nowy typ wyjątku użytkownika, który jest zgłaszany w przypadku naruszenia zawartości magazynu pamięci podręcznej
  + **azureml-automl-runtime**
    + Nie można już włączyć czyszczenia równoważenia klasy, jeśli użytkownik wyłączy cechowania.  
  + **Azure-contrib-itp**
    + Typ obliczania zestawu CmAk jest obsługiwany. Możesz dołączyć własny klaster AKS do obszaru roboczego na potrzeby zadania szkoleniowego.
  + **azureml-contrib-notebook**
    + Udoskonalenia dokumentacji dotyczącej pakietu Azure-contrib-Notes.
  + **azureml-contrib-pipeline-steps**
    + Udoskonalenia dokumentacji pakietu Azure-contrib--Pipeline-etaps.
  + **azureml-core**
    + Dodawanie set_connection, get_connection, list_connections, delete_connection funkcji dla klienta do działania w ramach zasobu połączenia z obszarem roboczym
    + Dokumentacja aktualizacji pakietu azureml-Coore/Azure. Exceptions.
    + Dokumentacja aktualizacji pakietu Azure Core.
    + Aktualizacja doc klasy ComputeInstance.
    + Udoskonalenia dokumentacji dotyczącej pakietu azureml-Core/Azure. Core. COMPUTE.
    + Udoskonalenia dokumentacji dotyczącej klas związanych z usługą WebService w rdzeniu Azure.
    + Obsługa danych profilowania wybranych przez użytkownika
    + Dodano Właściwość expand i page_count dla interfejsu API listy modeli
    + Rozwiązano problem polegający na tym, że usunięcie właściwości overwrite spowoduje niepowodzenie przesyłania w wyniku błędu deserializacji.
    + Naprawiono niespójną strukturę folderów podczas pobierania lub instalowania odwołania FileDataset do pojedynczego pliku.
    + Ładowanie zestawu danych plików Parquet to_spark_dataframe jest teraz szybsze i obsługuje wszystkie Parquet i Spark SQL DataTypes.
    + Obsługa pobierania dzienników kontenera init.
    + Uruchomienia AutoML są teraz oznaczone jako podrzędne uruchomienia kroku przebiegu równoległego.
  + **azureml-datadrift**
    + Udoskonalenia dokumentacji dotyczącej pakietu Azure-contrib-Notes.
  + **azureml-dataprep**
    + Ładowanie zestawu danych plików Parquet to_spark_dataframe jest teraz szybsze i obsługuje wszystkie Parquet i Spark SQL DataTypes.
    + Lepsza obsługa pamięci dla problemu OutOfMemory dla to_pandas_dataframe.
  + **azureml-interpret**
    + Uaktualniono usługę Azure — Interpretuj, aby użyć interpretera — Community w wersji 0,12. *
  + **azureml-mlflow**
    + Udoskonalenia dokumentacji dotyczącej programu Azure mlflow.
    + Dodaje obsługę rejestru modelu AML z MLFlow.
  + **Azure — opendatasets**
    + Dodano obsługę języka Python 3,8
  + **azureml-pipeline-core**
    + Zaktualizowano `PipelineDataset` dokumentację, aby wyczyścić ją jako wewnętrzną klasę.
    + ParallelRunStep aktualizacje, aby akceptować wiele wartości dla jednego argumentu, na przykład: "--group_column_names", "Kol1", "Col2", "Col3"
    + Usunięto wymaganie passthru_automl_config dla pośredniego użycia danych z AutoMLStep w potokach.
  + **azureml-pipeline-steps**
    + Udoskonalenia dokumentacji pakietu Azure-Pipeline-etaps.
    + Usunięto wymaganie passthru_automl_config dla pośredniego użycia danych z AutoMLStep w potokach.
  + **azureml-telemetry**
    + Udoskonalenia dokumentacji dotyczącej uczenia maszynowego.
  + **azureml-train-automl-client**
    + Naprawiono usterkę, która `experiment.submit()` została wywołana dwukrotnie na `AutoMLConfig` obiekcie, w wyniku czego wystąpił inne zachowanie.
    + Użytkownicy mogą teraz włączać iteracje kompletności stosu dla zadań szeregów czasowych z ostrzeżeniem, że potencjalnie overfit.
    + Zmieniono zachowanie przebiegu AutoML w celu wywołania UserErrorException, jeśli usługa zgłasza błąd użytkownika
    + Naprawia usterkę, która spowodowała wystąpienie azureml_automl. log w celu wygenerowania lub braku dzienników podczas przeprowadzania eksperymentu automl na zdalnym miejscu docelowym obliczeń.
    + W przypadku zestawów danych klasyfikacji z niezrównoważonymi klasami stosujemy równoważenie wagi, jeśli funkcja Sweeper zapewnią, że w przypadku danych z próbką, równoważenie wagi poprawi wydajność zadania klasyfikacji przez określony próg.
    + Uruchomienia AutoML są teraz oznaczone jako podrzędne uruchomienia kroku przebiegu równoległego.
  + **azureml-train-automl-runtime**
    + Zmieniono zachowanie przebiegu AutoML w celu wywołania UserErrorException, jeśli usługa zgłasza błąd użytkownika
    + Uruchomienia AutoML są teraz oznaczone jako podrzędne uruchomienia kroku przebiegu równoległego.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.7.0

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Ukończono usuwanie profilowania modelu z Mir contrib przez czyszczenie poleceń interfejsu wiersza polecenia i zależności pakietów, profilowanie modelu jest dostępne na rdzeń.
    + Uaktualnia minimalną wersję interfejsu wiersza polecenia platformy Azure do 2.3.0
  + **azureml-automl-core**
    + Lepszy komunikat o wyjątku na cechowania kroku fit_transform () z powodu niestandardowych parametrów transformatora.
    + Dodaj obsługę wielu języków dla modeli transformatora głębokiego, takich jak BERT, w zautomatyzowanej ML.
    + Usuń przestarzały parametr lag_length z dokumentacji.
    + Ulepszona dokumentacja dotycząca parametrów prognozowania. Parametr lag_length był przestarzały.
  + **azureml-automl-runtime**
    + Naprawiono błąd wywoływany, gdy jedna z kolumn kategorii jest pusta w czasie prognozowania/testowania.
    + Usuń błędy uruchamiania wykonywane, gdy funkcje lookback są włączone, a dane zawierają krótkie ziarna.
    + Rozwiązano problem ze zduplikowanym komunikatem o błędzie indeksu czasu, gdy spowolnienia lub stopniowe okna zostały ustawione na wartość "Auto".
    + Rozwiązano problem z modelami Prophet i ARIMA w zestawach danych, które zawierają funkcje lookback.
    + Dodano obsługę dat przed 1677-09-21 lub po 2262-04-11 w kolumnach innych niż data i godzina w zadaniach prognozowania. Ulepszone komunikaty o błędach.
    + Ulepszona dokumentacja dotycząca parametrów prognozowania. Parametr lag_length był przestarzały.
    + Lepszy komunikat o wyjątku na cechowania kroku fit_transform () z powodu niestandardowych parametrów transformatora.
    + Dodaj obsługę wielu języków dla modeli transformatora głębokiego, takich jak BERT, w zautomatyzowanej ML.
    + Operacje pamięci podręcznej, które powodują, że niektóre OSErrors spowodują wystąpienie błędu użytkownika.
    + Dodano testy, aby upewnić się, że dane szkoleniowe i weryfikacyjne mają taką samą liczbę i zestaw kolumn
    + Rozwiązano problem z automatycznie generowanym skryptem oceniania AutoML, gdy dane zawierają znaki cudzysłowu
    + Włączanie wyjaśnień dla modeli AutoML Prophet i modelu z modelem Prophet.
    + W przypadku ostatniego problemu z klientem Wykryto usterkę na żywo w celu założenia, że komunikaty są rejestrowane w ramach równoważenia klasy, nawet jeśli logika równoważenia klasy nie jest prawidłowo włączona. Usuwanie tych dzienników/komunikatów za pomocą tego żądania ściągnięcia.
  + **Azure-Common — interfejs wiersza polecenia**
    + Ukończono usuwanie profilowania modelu z Mir contrib przez czyszczenie poleceń interfejsu wiersza polecenia i zależności pakietów, profilowanie modelu jest dostępne na rdzeń.
  + **azureml-contrib-reinforcementlearning**
    + Narzędzie testowania obciążenia
  + **azureml-core**
    + Dokumentacja dotycząca zmian w dokumentacji Script_run_config. PR
    + Naprawia usterkę z drukowaniem danych wyjściowych uruchomienia polecenia Run-Pipeline
    + Udoskonalenia dokumentacji azureml-Core/Azure. Data
    + Rozwiązuje problem podczas pobierania konta magazynu przy użyciu polecenia HDFS getconf
    + Ulepszona dokumentacja register_azure_blob_container i register_azure_file_share
  + **azureml-datadrift**
    + Ulepszona implementacja do wyłączania i włączania monitorów dryfowania zestawu danych
  + **azureml-interpret**
    + W wyjaśnieniu klient usuwa NaNs lub inf przed serializacji JSON przy przekazywaniu z artefaktów
    + Aktualizowanie do najnowszej wersji programu interpretuje społeczność, aby zwiększyć błędy pamięci dla globalnych wyjaśnień z wieloma funkcjami i klasami
    + Dodaj true_ys opcjonalny parametr do wyjaśnienia przekazywanie, aby włączyć dodatkowe funkcje w interfejsie użytkownika programu Studio
    + Poprawa wydajności download_model_explanations () i list_model_explanations ()
    + Małe dostosowania do notesów, aby pomóc w debugowaniu
  + **Azure — opendatasets**
    + Azure opendatasets potrzebuje programu Azure-preprodukcyjny w wersji 1.4.0 lub nowszej. Dodano ostrzeżenie w przypadku wykrycia niższej wersji
  + **azureml-pipeline-core**
    + Ta zmiana umożliwia użytkownikowi dostarczenie opcjonalnej runconfig do moduleVersion podczas wywoływania modułu. Publish_python_script.
    + Konto węzła Enable może być parametrem potoku w ParallelRunStep w usłudze Azure. Pipeline. kroki
  + **azureml-pipeline-steps**
    + Ta zmiana umożliwia użytkownikowi dostarczenie opcjonalnej runconfig do moduleVersion podczas wywoływania modułu. Publish_python_script.
  + **azureml-train-automl-client**
    + Dodaj obsługę wielu języków dla modeli transformatora głębokiego, takich jak BERT, w zautomatyzowanej ML.
    + Usuń przestarzały parametr lag_length z dokumentacji.
    + Ulepszona dokumentacja dotycząca parametrów prognozowania. Parametr lag_length był przestarzały.
  + **azureml-train-automl-runtime**
    + Włączanie wyjaśnień dla modeli AutoML Prophet i modelu z modelem Prophet.
    + Dokumentacja aktualizacji dla pakietów Azure-pociąg-automl-*.
  + **azureml-train-core**
    + Obsługa TensorFlow w wersji 2,1 w PyTorch szacowania
    + Ulepszenia dotyczące pakietu Azure — uczenie rdzeniowe.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.6.0

+ **Nowe funkcje**
  + **azureml-automl-runtime**
    + Funkcja prognozowania AutoML obsługuje teraz prognozowanie klientów wykraczających poza wstępnie określoną wartość Max-Horizon bez ponownego uczenia modelu. Gdy miejsce docelowe prognozy jest późniejsze w przyszłości niż określony maksymalny horyzont, funkcja REGLINX () będzie nadal przekazywać punkty do późniejszej daty przy użyciu trybu operacji cyklicznej. Ilustracja nowej funkcji znajduje się w sekcji "prognozowanie dalej niż maksymalny horyzont" w notesie "prognozowanie-Prognoza-funkcja" w [folderze](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)".
  
  + **azureml-pipeline-steps**
    + ParallelRunStep jest teraz wydawany i jest częścią pakietu platformy **Azure-Pipeline-etap** . Istniejący ParallelRunStep w pakiecie **Azure-contrib-Pipeline-etaps** jest przestarzały. Zmiany z publicznej wersji zapoznawczej:
      + Dodano `run_max_try` opcjonalny konfigurowalny parametr w celu kontrolowania maksymalnego wywołania metody Run dla danej partii, wartość domyślna to 3.
      + Żadne PipelineParameters nie są już generowane automatycznie. Następujące konfigurowalne wartości można ustawić jako PipelineParameter jawnie.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + Wartość domyślna dla process_count_per_node jest zmieniana na 1. Użytkownik powinien dostosować tę wartość w celu zwiększenia wydajności. Najlepszym rozwiązaniem jest ustawienie liczby węzłów procesora GPU lub procesorów.
      + ParallelRunStep nie wprowadza żadnych pakietów, użytkownik musi uwzględnić pakiety **Azure-Core** i Azure- **[Pandas, bezpiecznik]** w definicji środowiska. Jeśli w user_managed_dependencies jest używany niestandardowy obraz platformy Docker, użytkownik musi zainstalować Conda na obrazie.
      
+ **Zmiany powodujące niezgodność**
  + **azureml-pipeline-steps**
    + Zaniechano używania usługi Azure. dprep. przepływu danych jako prawidłowego typu danych wejściowych dla AutoMLConfig
  + **azureml-train-automl-client**
    + Zaniechano używania usługi Azure. dprep. przepływu danych jako prawidłowego typu danych wejściowych dla AutoMLConfig

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Rozwiązano problem polegający na tym, że ostrzeżenie można wydrukować w ramach `get_output` tego monitu o obniżenie poziomu klienta.
    + Zaktualizowany komputer Mac, aby polegał na cudatoolkit = 9.0, ponieważ nie jest jeszcze dostępny w wersji 10.
    + Usuwanie ograniczeń dotyczących modeli Prophet i xgboost podczas uczenia się w przypadku obliczeń zdalnych.
    + Ulepszone rejestrowanie w AutoML
    + Ulepszono obsługę błędów niestandardowych cechowania podczas prognozowania zadań.
    + Dodano funkcję zezwalającą użytkownikom na dołączanie funkcji do generowania prognoz.
    + Aktualizacje komunikatu o błędzie w celu poprawnego wyświetlenia błędu użytkownika.
    + Obsługa cv_split_column_names, które mają być używane z training_data
    + Aktualizowanie rejestrowania komunikatu o wyjątku i traceback.
  + **azureml-automl-runtime**
    + Włącz guardrails do prognozowania brakujących wartości przypisywania.
    + Ulepszone rejestrowanie w AutoML
    + Dodano szczegółowe obsłudze błędów dla wyjątków przygotowywania danych
    + Usuwanie ograniczeń dotyczących modeli phrophet i xgboost podczas uczenia się w przypadku obliczeń zdalnych.
    + `azureml-train-automl-runtime` i `azureml-automl-runtime` mają zaktualizowane zależności dla `pytorch` , `scipy` i `cudatoolkit` . teraz obsługujemy `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` i `cudatoolkit==10.1.243` .
    + Ulepszono obsługę błędów niestandardowych cechowania podczas prognozowania zadań.
    + Ulepszono mechanizm wykrywania częstotliwości zestawu danych prognozowania.
    + Rozwiązano problem dotyczący szkolenia modelu PROPHET na niektórych zestawach danych.
    + Ulepszono funkcję automatycznego wykrywania maksymalnego horyzontu podczas prognozowania.
    + Dodano funkcję zezwalającą użytkownikom na dołączanie funkcji do generowania prognoz.
    +  Dodaje funkcje w funkcji prognozowania, aby umożliwić dostarczanie prognoz poza przeszkolonym horyzontem bez ponownego szkolenia modelu prognozowania.
    + Obsługa cv_split_column_names, które mają być używane z training_data
  + **Azure-contrib-automl-DNN — prognozowanie**
    + Ulepszone rejestrowanie w AutoML
  + **Azure-contrib-Mir**
    + Dodano obsługę usług systemu Windows w ManagedInferencing
    + Usuń stare MIRe przepływy pracy, takie jak Attach MIR COMPUTE, SingleModelMirWebservice Class-Clean out Profiling w contrib-Mir Package
  + **azureml-contrib-pipeline-steps**
    + Drobna poprawka dla pomocy technicznej YAML
    + ParallelRunStep jest ogólnie dostępna — Azure. contrib. Pipeline. kroki zawierają powiadomienie o zaniechaniu i są przenoszone do programu Azure. Pipeline. kroki
  + **azureml-contrib-reinforcementlearning**
    + Narzędzie testowania obciążenia RL
    + RL szacowania ma inteligentne wartości domyślne
  + **azureml-core**
    + Usuń stare MIRe przepływy pracy, takie jak Attach MIR COMPUTE, SingleModelMirWebservice Class-Clean out Profiling w contrib-Mir Package
    + Naprawiono informacje podane użytkownikowi w przypadku niepowodzenia profilowania: uwzględniono identyfikator żądania i treść komunikatu, który będzie bardziej zrozumiały. Dodano nowy przepływ pracy profilowania do profilowania Runners
    + Ulepszony tekst błędu w przypadku niepowodzenia wykonywania zestawu danych.
    + Dodano obsługę interfejsu wiersza polecenia prywatnego linku do obszaru roboczego.
    + Dodano opcjonalny parametr `invalid_lines` do `Dataset.Tabular.from_json_lines_files` , który umożliwia określenie sposobu obsługi wierszy zawierających nieprawidłowy kod JSON.
    + W kolejnej wersji zostanie wycofane tworzenie obliczeń w oparciu o uruchomienie. Zalecamy utworzenie rzeczywistego klastra Amlcompute jako trwałego obiektu docelowego obliczeń i użycie nazwy klastra jako elementu docelowego obliczeń w konfiguracji przebiegu. Zobacz przykładowy Notes: aka.ms/amlcomputenb
    + Ulepszone komunikaty o błędach w przypadku niepowodzenia wykonywania zestawu danych.
  + **azureml-dataprep**
    + Ostrzeżenie o konieczności uaktualnienia wersji pyarrow.
    + Ulepszona obsługa błędów i komunikat zwrócony w przypadku niepowodzenia wykonania przepływu danych.
  + **azureml-interpret**
    + Dokumentacja aktualizacji pakietu do interpretacji z usługą Azure.
    + Stałe pakiety do interpretacji i notesy, które mają być zgodne z najnowszą aktualizacją skryptu sklearn
  + **Azure — opendatasets**
    + Zwróć brak, gdy nie zostaną zwrócone żadne dane.
    + Zwiększ wydajność to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Szybka poprawka dla ParallelRunStep, gdzie ładowanie z YAML zostało przerwane
    + ParallelRunStep jest udostępniona do ogólnej dostępności — Azure. contrib. Pipeline. kroki zawierają powiadomienie o zaniechaniu i są przenoszone do programu Azure. Pipeline. kroki — nowe funkcje obejmują: 1. Zestawy danych jako PipelineParameter 2. Nowy parametr run_max_retry 3. Konfigurowalna nazwa pliku wyjściowego append_row
  + **azureml-pipeline-steps**
    + Przestarzały element Azure. dprep. przepływu danych jest prawidłowym typem danych wejściowych.
    + Szybka poprawka dla ParallelRunStep, gdzie ładowanie z YAML zostało przerwane
    + ParallelRunStep jest udostępniona do ogólnej dostępności — Azure. contrib. Pipeline. kroki zawierają powiadomienie o zaniechaniu i są przenoszone do programu Azure. Pipeline. kroki — nowe funkcje obejmują:
      + Zestawy danych jako PipelineParameter
      + Nowy run_max_retry parametrów
      + Konfigurowalna nazwa pliku wyjściowego append_row
  + **azureml-telemetry**
    + Aktualizowanie rejestrowania komunikatu o wyjątku i traceback.
  + **azureml-train-automl-client**
    + Ulepszone rejestrowanie w AutoML
    + Aktualizacje komunikatu o błędzie w celu poprawnego wyświetlenia błędu użytkownika.
    + Obsługa cv_split_column_names, które mają być używane z training_data
    + Przestarzały element Azure. dprep. przepływu danych jest prawidłowym typem danych wejściowych.
    + Zaktualizowany komputer Mac, aby polegał na cudatoolkit = 9.0, ponieważ nie jest jeszcze dostępny w wersji 10.
    + Usuwanie ograniczeń dotyczących modeli phrophet i xgboost podczas uczenia się w przypadku obliczeń zdalnych.
    + `azureml-train-automl-runtime` i `azureml-automl-runtime` mają zaktualizowane zależności dla `pytorch` , `scipy` i `cudatoolkit` . teraz obsługujemy `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` i `cudatoolkit==10.1.243` .
    + Dodano funkcję zezwalającą użytkownikom na dołączanie funkcji do generowania prognoz.
  + **azureml-train-automl-runtime**
    + Ulepszone rejestrowanie w AutoML
    + Dodano szczegółowe obsłudze błędów dla wyjątków przygotowywania danych
    + Usuwanie ograniczeń dotyczących modeli phrophet i xgboost podczas uczenia się w przypadku obliczeń zdalnych.
    + `azureml-train-automl-runtime` i `azureml-automl-runtime` mają zaktualizowane zależności dla `pytorch` , `scipy` i `cudatoolkit` . teraz obsługujemy `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` i `cudatoolkit==10.1.243` .
    + Aktualizacje komunikatu o błędzie w celu poprawnego wyświetlenia błędu użytkownika.
    + Obsługa cv_split_column_names, które mają być używane z training_data
  + **azureml-train-core**
    + Dodano nowy zestaw wyjątków określonych dla konkretnych dysków. Program Azure. uczenie. na dysku spowoduje teraz zgłoszenie szczegółowych wyjątków.
  + **azureml-widgets**
    + Widżety Azure nie są wyświetlane w JupyterLab
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.5.0

+ **Nowe funkcje**
  + **Funkcje w wersji zapoznawczej**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning jest w trakcie korzystania z wersji zapoznawczej dla nauki wzmacniania przy użyciu struktury [Ray](https://ray.io) . `ReinforcementLearningEstimator`Umożliwia szkolenie agentów nauki wzmacniania między procesorami GPU i procesorami obliczeniowymi w Azure Machine Learning.

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Naprawia przypadkowo pozostawiony w powyższym dzienniku ostrzeżeń w poprzednim żądaniu ściągnięcia. Dziennik został użyty do debugowania i przypadkowo został pozostawiony w tle.
    + Poprawka usterki: informowanie klientów o częściowej awarii podczas profilowania
  + **azureml-automl-core**
    + Przyspiesz model Prophet/AutoArima w prognozie AutoML, włączając równoległe Dopasowywanie dla szeregów czasowych, gdy zestawy danych mają wiele szeregów czasowych. Aby można było skorzystać z tej nowej funkcji, zaleca się ustawienie "max_cores_per_iteration =-1" (czyli użycie wszystkich dostępnych rdzeni procesora) w AutoMLConfig.
    + Naprawiono błąd podczas drukowania guardrails w interfejsie konsoli
    + Naprawiono komunikat o błędzie dla experimentation_timeout_hours
    + Przestarzałe modele Tensorflow dla AutoML.
  + **azureml-automl-runtime**
    + Naprawiono komunikat o błędzie dla experimentation_timeout_hours
    + Naprawiono niesklasyfikowany wyjątek podczas próby deserializacji z magazynu pamięci podręcznej
    + Przyspiesz model Prophet/AutoArima w prognozie AutoML, włączając równoległe Dopasowywanie dla szeregów czasowych, gdy zestawy danych mają wiele szeregów czasowych.
    + Naprawiono prognozowanie z włączonym przewijanym oknem w zestawach danych, gdzie test/zestaw prognoz nie zawiera jednego z ziaren z zestawu szkoleniowego.
    + Ulepszona obsługa brakujących danych
    + Rozwiązano problem z interwałami przewidywania podczas prognozowania zestawów danych zawierających szeregi czasowe, które nie są wyrównane w czasie.
    + Dodano lepszą weryfikację kształtu danych dla zadań prognozowania.
    + Ulepszone wykrywanie częstotliwości.
    + Ten komunikat o błędzie został utworzony w przypadku, gdy nie można wygenerować zginania krzyżowego składania zadań do prognozowania.
    + Napraw interfejs konsoli, aby poprawnie wydrukować brakującą wartość Guardrail.
    + Wymuszanie sprawdzania elementu DataType na cv_split_indices danych wejściowych w AutoMLConfig.
  + **Azure-Common — interfejs wiersza polecenia**
    + Poprawka usterki: informowanie klientów o częściowej awarii podczas profilowania
  + **Azure-contrib-Mir**
    + Dodaje klasę Azure. contrib. Mir. RevisionStatus, która przekazuje informacje dotyczące aktualnie wdrożonej poprawki MIR i najnowszej wersji określonej przez użytkownika. Ta klasa jest zawarta w obiekcie MirWebservice w atrybucie "deployment_status".
    + Włącza aktualizację usług WebService typu MirWebservice i jej klasy podrzędnej SingleModelMirWebservice.
  + **azureml-contrib-reinforcementlearning**
    + Dodano obsługę usługi Ray 0.8.3
    + AmlWindowsCompute obsługuje tylko Azure Files jako magazyn zainstalowany
    + Zmieniono nazwę health_check_timeout na health_check_timeout_seconds
    + Naprawiono niektóre opisy klas/metod.
  + **azureml-core**
    + Włączono konwersje WASB > obiektów BLOB w chmurach Azure Government i Chiny.
    + Naprawia usterkę, aby umożliwić rolom czytnika użycie poleceń AZ ml Run interfejsu wiersza polecenia w celu uzyskania informacji o uruchamianiu
    + Usunięto zbędne rejestrowanie w trakcie zdalnego uruchomienia usługi Azure ML z wejściowymi zestawami danych.
    + RCranPackage obsługuje teraz parametr "Version" dla wersji pakietu CRAN.
    + Poprawka usterki: informowanie klientów o częściowej awarii podczas profilowania
    + Dodano obsługę zmiennoprzecinkową w stylu europejskim dla platformy Azure Core.
    + Włączono funkcje prywatnego linku obszaru roboczego w zestawie SDK usługi Azure ml.
    + Podczas tworzenia TabularDataset za pomocą `from_delimited_files` , można określić, czy puste wartości mają być ładowane jako None, czy jako pusty ciąg poprzez ustawienie argumentu logicznego `empty_as_string` .
    + Dodano obsługę zmiennoprzecinkową w stylu europejskim dla zestawów danych.
    + Ulepszono komunikaty o błędach podczas instalacji zestawu danych.
  + **azureml-datadrift**
    + Zapytanie o wyniki dryfowania danych z zestawu SDK miało usterkę nieodróżniającą minimalnej, maksymalnej i średniej metryk funkcji, co spowodowało zduplikowane wartości. Ta usterka została naprawiona przez umieszczenie prefiksu docelowej lub odniesienia do nazw metryk. Przed: zduplikowana minimalna, maksymalna, średnia. Po: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + Poprawa obsługi zapisów z ograniczonym środowiskiem Python w przypadku zapewnienia zależności .NET wymaganych do dostarczania danych.
    + Naprawiono tworzenie przepływu danych w pliku z wiodącymi pustymi rekordami.
    + Dodano opcje obsługi błędów dla `to_partition_iterator` podobnej do `to_pandas_dataframe` .
  + **azureml-interpret**
    + Zmniejszono limity długości ścieżki, aby zmniejszyć prawdopodobieństwo przekroczenia limitu systemu Windows
    + Poprawka dla wyjaśnień rozrzedzonych utworzonych za pomocą narzędzia do demetrii przy użyciu wieloskładnikowego modelu.
  + **azureml-opendatasets**
    + Rozwiązywanie problemów z kolumnami MNIST ręcznie są analizowane jako ciąg, który powinien być typem int.
  + **Azure — potok — rdzeń**
    + Zezwalanie na regenerate_outputs przy użyciu modułu osadzonego w ModuleStep.
  + **azureml-train-automl-client**
    + Przestarzałe modele Tensorflow dla AutoML.
    + Naprawa użytkowników Zezwalaj na wyświetlanie nieobsługiwanych algorytmów w trybie lokalnym
    + Poprawki doc do AutoMLConfig.
    + Wymuszanie sprawdzania elementu DataType na cv_split_indices danych wejściowych w AutoMLConfig.
    + Rozwiązano problem z niepowodzeniem uruchamiania AutoML w show_output
  + **azureml-train-automl-runtime**
    + Naprawianie błędu w iteracjach kompletnych, które uniemożliwiają pomyślne rozpoczęcie operacji pobierania modelu.
  + **azureml-train-core**
    + Napraw literówka w klasie Azure. uczenie. DNN. Nccl.
    + Obsługa PyTorch w wersji 1,5 w PyTorch szacowania
    + Usuń problem polegający na tym, że nie można pobrać obrazu struktury w Azure Government regionie podczas korzystania z platformy szkoleniowej szacowania

  
## <a name="2020-05-04"></a>2020-05-04
**Nowe środowisko notesu**

Teraz możesz tworzyć, edytować i udostępniać Notesy i pliki uczenia maszynowego bezpośrednio w środowisku sieci Web programu Studio Azure Machine Learning. Możesz użyć wszystkich klas i metod dostępnych w [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) z wewnątrz tych notesów Rozpocznij [tutaj](./how-to-run-jupyter-notebooks.md)

**Wprowadzono nowe funkcje:**

+ Udoskonalony edytor (Edytor Monako) używany przez VS Code 
+ Udoskonalenia interfejsu użytkownika/środowiska
+ Pasek narzędzi komórki
+ Nowy pasek narzędzi notesu i kontrolki obliczeniowe
+ Pasek stanu notesu 
+ Wbudowane przełączanie jądra
+ Obsługa języka R
+ Ulepszenia ułatwień dostępu i lokalizacji
+ Paleta poleceń
+ Dodatkowe skróty klawiaturowe
+ Automatyczne
+ Zwiększona wydajność i niezawodność

Uzyskaj dostęp do następujących narzędzi autorskich opartych na sieci Web z programu Studio:
    
| Narzędzie oparte na sieci Web  |     Opis  |
|---|---|
| Notesy ML Studio platformy Azure   |     Pierwsze tworzenie w klasie plików notesu i obsługa wszystkich operacji dostępnych w zestawie SDK usługi Azure ML w języku Python. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.4.0

+ **Nowe funkcje**
  + Klastry AmlCompute obsługują teraz Konfigurowanie tożsamości zarządzanej w klastrze w momencie aprowizacji. Wystarczy określić, czy chcesz użyć tożsamości przypisanej do systemu lub tożsamości przypisanej do użytkownika, i przekazać identityId dla tej ostatniej. Następnie można skonfigurować uprawnienia dostępu do różnych zasobów, takich jak Storage lub ACR, w taki sposób, aby tożsamość obliczeń była używana do bezpiecznego dostępu do danych, zamiast podejścia opartego na tokenach, które AmlCompute już dziś. Zapoznaj się z naszym odwołaniem do zestawu SDK, aby uzyskać więcej informacji o parametrach.
  

+ **Zmiany powodujące niezgodność**
  + Klastry AmlCompute obsługują funkcję w wersji zapoznawczej wokół tworzenia opartego na uruchomieniach, które planujemy zaniechania w dwóch tygodniach. Możesz kontynuować tworzenie trwałych obiektów docelowych obliczeń jako zawsze przy użyciu klasy Amlcompute, ale konkretna metoda określania identyfikatora "Amlcompute" jako elementu docelowego obliczeń w konfiguracji uruchomieniowej nie będzie obsługiwana w najbliższej przyszłości. 

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-runtime**
    + Włącz obsługę typu niezmieszanego podczas obliczania liczby unikatowych wartości w kolumnie.
  + **azureml-core**
    + Ulepszona stabilność podczas czytania z usługi Azure Blob Storage przy użyciu TabularDataset.
    + Ulepszona Dokumentacja dla `grant_workspace_msi` parametru `Datastore.register_azure_blob_store` .
    + Naprawiono usterkę z `datastore.upload` do obsługi `src_dir` argumentu kończącego się znakiem `/` lub `\` .
    + Dodano komunikat o błędzie z możliwością działania podczas próby przekazania do magazynu danych usługi Azure Blob Storage, który nie ma klucza dostępu ani tokenu SAS.
  + **azureml-interpret**
    + Dodano górną granicę do rozmiaru pliku dla danych wizualizacji na przekazanych wyjaśnień.
  + **azureml-train-automl-client**
    + Jawnie sprawdzaj, czy label_column_name & weight_column_name parametry AutoMLConfig mają być typu String.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep obsługuje teraz zestaw danych jako parametr potoku. Użytkownik może skonstruować potok z przykładowym zestawem danych i może zmienić wejściowy zestaw danych tego samego typu (plik lub tabelaryczny) dla nowego uruchomienia potoku.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.3.0

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Dodano dodatkową telemetrię dla operacji po szkoleniu.
    + Przyspiesza automatyczne uczenie ARIMA przy użyciu warunkowego podsumowania kwadratów (CSS) dla serii o długości większej niż 100. Użyta długość jest przechowywana jako stała ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/w klasie TimeSeriesInternal w/src/azureml-automl-Core/azureml/automl/Core/Shared/Constants.py
    + Ulepszone rejestrowanie przebiegów prognozowania zostało udoskonalone. teraz więcej informacji na temat aktualnie uruchomionej fazy zostanie wyświetlony w dzienniku
    + Niedozwolone target_rolling_window_size ustawione na wartości mniejsze niż 2
  + **azureml-automl-runtime**
    + Ulepszony komunikat o błędzie wyświetlany, gdy zostanie znaleziony duplikaty sygnatur czasowych.
    + Niedozwolone target_rolling_window_size ustawione na wartości mniejsze niż 2.
    + Naprawiono błąd opóźnienia podczas przypisywania. Przyczyną tego problemu jest niewystarczająca liczba obserwacji, które są konieczne do rozdzielenia serii. "Niesezonowe" dane są używane do obliczania częściowej funkcji autokorelacji (PACF) w celu określenia długości zwłoki.
    + Włączono Dostosowywanie cechowania cel kolumny do prognozowania zadań według cechowania konfiguracji. Obsługiwane są wartości liczbowe i kategorii jako cel kolumn dla prognozowania zadań.
    + Włączono Dostosowywanie cechowania kolumn do prognozowania według cechowania konfiguracji.
    + Włączono dostosowanie przypisywania do zadań prognozowania według konfiguracji cechowania. Obecnie obsługiwane są wartości stałe przynoszące do kolumny docelowej i średniego, średnika, most_frequent i wartości stałej przypisywania danych szkoleniowych.
  + **azureml-contrib-pipeline-steps**
    + Akceptuj nazwy obliczeń ciągów, które mają zostać przesłane do ParallelRunConfig
  + **azureml-core**
    +  Dodano interfejs API Environment. klonowania (new_name), aby utworzyć kopię obiektu środowiska
    +  Ker.base_dockerfile Environment.docakceptuje ścieżki. Jeśli możliwe jest rozpoznanie pliku, zawartość zostanie odczytana do base_dockerfile właściwości środowiska
    + Automatycznie Resetuj wzajemnie wykluczające się wartości dla base_image i base_dockerfile, gdy użytkownik ręcznie ustawi wartość w Environment.docKer
    + Dodano flagę user_managed w RSection, która wskazuje, czy środowisko jest zarządzane przez użytkownika, czy przez usługę Azure.
    + DataSet: błąd pobierania ustalonego zestawu danych, jeśli ścieżka danych zawiera znaki Unicode.
    + Zestaw danych: udoskonalony mechanizm buforowania instalacji zestawu danych w celu uwzględnienia minimalnego wymagania dotyczącego miejsca na dysku w Azure Machine Learning obliczeniach, które uniemożliwiają nieużywanie węzła i spowodowanie anulowania zadania.
    + Zestaw danych: dodajemy indeks kolumny szeregów czasowych, gdy uzyskujesz dostęp do zestawu danych szeregów czasowych jako Pandas Dataframe, która jest używana do przyspieszenia dostępu do danych na podstawie szeregów czasowych.  Wcześniej indeks miał taką samą nazwę jak kolumna sygnatur czasowych, przez pomyłkę użytkowników, która jest rzeczywistą kolumną sygnatury czasowej i jest indeksem. Nie należy teraz podawać żadnej nazwy do indeksu, ponieważ nie powinna ona być używana jako kolumna. 
    + Zestaw danych: problem z uwierzytelnianiem ustalonego zestawu danych w chmurze suwerennej.
    + DataSet: Naprawiono `Dataset.to_spark_dataframe` błąd dla zestawów danych utworzonych przy użyciu usługi Azure PostgreSQL datastores.
  + **azureml-interpret**
    + Dodano globalne oceny do wizualizacji, jeśli wartości ważności lokalnej są rozrzedzone
    + Zaktualizowano usługę Azure-Interpretuj pod kątem używania interpretacji — społeczność 0,9. *
    + Rozwiązano problem z pobieraniem wyjaśnień, które miały rozrzedzone dane oceny
    + Dodano obsługę formatu rozrzedzonego obiektu wyjaśnienia w AutoML
  + **azureml-pipeline-core**
    + Obsługa ComputeInstance jako celu obliczeń w potokach
  + **azureml-train-automl-client**
    + Dodano dodatkową telemetrię dla operacji po szkoleniu.
    + Naprawiono na wczesne zatrzymanie regresji
    + Przestarzały element Azure. dprep. przepływu danych jest prawidłowym typem danych wejściowych.
    +  Zmiana domyślnego czasu eksperymentu AutoML na sześć dni.
  + **azureml-train-automl-runtime**
    + Dodano dodatkową telemetrię dla operacji po szkoleniu.
    + dodano kompleksową pomoc techniczną AutoML
  + **Azure — opendatasets**
    + Dodano dodatkową telemetrię dla monitora usługi.
    + Włącz drzwi do przodu dla obiektu BLOB, aby zwiększyć stabilność 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.2.0

+ **Zmiany powodujące niezgodność**
  + Porzuć wsparcie dla języka Python 2,7

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Dodaje "--Subscription-ID" do `az ml model/computetarget/service` poleceń w interfejsie wiersza polecenia
    + Dodawanie obsługi przekazywania klucza zarządzanego przez klienta (CMK) vault_url, key_name i key_version wdrożenia ACI
  + **azureml-automl-core** 
    + Włączono dostosowane przypisywanie z wartością stałą dla zadań prognozowania danych X i y.
    + Rozwiązano problem związany z wyświetlaniem komunikatów o błędach do użytkownika.    
  + **azureml-automl-runtime**
    + Rozwiązano problem związany z prognozą na zestawach danych, zawierający ziarna z tylko jednym wierszem
    + Zmniejszono ilość pamięci wymaganej przez zadania prognozowania.
    + Dodano lepsze komunikaty o błędach, jeśli kolumna czasu ma niepoprawny format.
    + Włączono dostosowane przypisywanie z wartością stałą dla zadań prognozowania danych X i y.
  + **azureml-core**
    + Dodano obsługę ładowania elementu serviceprincipal ze zmiennych środowiskowych: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID i AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Wprowadza nowy parametr `support_multi_line` do `Dataset.Tabular.from_delimited_files` : domyślnie ( `support_multi_line=False` ), wszystkie podziały wierszy, łącznie z wartościami pól w cudzysłowie, będą interpretowane jako podział rekordu. Odczytywanie danych w ten sposób jest szybsze i bardziej zoptymalizowane pod kątem równoległego wykonywania na wielu rdzeniach procesora. Jednak może to spowodować w trybie dyskretnym tworzenie większej liczby rekordów z niewyrównanymi wartościami pól. Ta wartość powinna być ustawiona na `True` , gdy pliki rozdzielane mają zawierać podziały wierszy w cudzysłowie.
    + Dodano możliwość rejestrowania ADLS Gen2 w interfejsie wiersza polecenia Azure Machine Learning
    + Zmieniono nazwę parametru "fine_grain_timestamp" na "timestamp" i parametr "coarse_grain_timestamp" na "partition_timestamp" dla metody with_timestamp_columns () w TabularDataset, aby lepiej odzwierciedlić użycie parametrów.
    + Zwiększona Maksymalna długość nazwy eksperymentu do 255.
  + **azureml-interpret**
    + Zaktualizowano usługę Azure — Interpretuj ją pod kątem interpretacji — Community 0,7. *
  + **Azure-SDK**
    + Zmiana na zależności ze zgodną wersją tyldy dla obsługi poprawek w wersji wstępnej i stabilnej.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.1.5

+ **Przestarzałe funkcje**
  + **Python 2,7**
    + Ostatnia wersja do obsługi języka Python 2,7

+ **Zmiany powodujące niezgodność**
  + **Semantyczne przechowywanie wersji 2.0.0**
    + Począwszy od wersji 1,1 usługi Azure ML Python SDK przyjmuje 2.0.0 wersji semantycznej. [Przeczytaj więcej tutaj](https://semver.org/). Wszystkie kolejne wersje będą zgodne z nowym schematem numeracji i kontraktem wersji semantycznej. 

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Zmień nazwę polecenia CLI punktu końcowego z "AZ ml Endpoint AKS" na "AZ ml Endpoint Time", aby zapewnić spójność.
    + Aktualizowanie instrukcji instalacji interfejsu wiersza polecenia dla stabilnego i eksperymentalnego interfejsu wiersza polecenia
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu utworzenia rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
  + **azureml-automl-core**
    + Włączono wnioskowanie w trybie wsadowym (pobierając wiele wierszy raz) dla modeli AutoML ONNX
    + Ulepszone wykrywanie częstotliwości zestawów danych, Brak danych lub nieregularne punkty danych
    + Dodano możliwość usuwania punktów danych, które nie są zgodne z częstotliwością dominującą.
    + Zmieniono dane wejściowe konstruktora, aby lista opcji została zastosowana do odpowiednich kolumn.
    + Ulepszono rejestrowanie błędów.
  + **azureml-automl-runtime**
    + Rozwiązano problem z błędem zgłoszonym, jeśli ziarna nie było obecne w zestawie szkoleniowym w zestawie testów
    + Usunięto wymaganie y_query podczas oceniania usługi prognozowania
    + Rozwiązano problem związany z prognozą, gdy zestaw danych zawiera krótkie ziarna z długimi odstępami czasu.
    + Rozwiązano problem, gdy jest włączona funkcja Automax, a kolumna Date zawiera daty w postaci ciągów. Podczas konwersji na datę nie jest możliwe dodanie odpowiednich błędów konwersji i komunikatów o błędach
    + Używanie natywnych NumPy i SciPy do serializacji i deserializacji danych pośrednich dla plik filecachestore (używany do lokalnego uruchamiania AutoML)
    + Naprawiono usterkę, która może spowodować zablokowanie nieprawidłowych przebiegów podrzędnych.
    + Zwiększona prędkość cechowania.
    + Rozwiązano sprawdzanie częstotliwości podczas oceniania, a teraz zadania prognozowania nie wymagają dokładnej równoważności częstotliwości między zestawem uczenia i testem.
    + Zmieniono dane wejściowe konstruktora, aby lista opcji została zastosowana do odpowiednich kolumn.
    + Naprawione błędy związane z wyborem typu zwłoki.
    + Rozwiązano niesklasyfikowany błąd zgłoszony w zestawach danych, zawierający ziarna z pojedynczym wierszem
    + Rozwiązano problem z niespowolnieniem wykrywania częstotliwości.
    + Naprawia usterkę w obsłudze wyjątków AutoML, która spowodowała, że rzeczywista przyczyna niepowodzenia szkolenia została zastąpiona przez AttributeError.
  + **Azure-Common — interfejs wiersza polecenia**
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu utworzenia rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
  + **Azure-contrib-Mir**
    + Dodaje funkcje klasy MirWebservice w celu pobrania tokenu dostępu
    + Używaj uwierzytelniania tokenów dla MirWebservice domyślnie podczas MirWebservice. Run () tylko do wywołania odświeżenia, jeśli wywołanie zakończy się niepowodzeniem
    + Wdrożenie usługi sieci Web Mir wymaga teraz odpowiednich jednostek SKU [Standard_DS2_v2, Standard_F16, Standard_A2_v2] zamiast [Ds2v2, A2v2 i F16].
  + **azureml-contrib-pipeline-steps**
    + Dodano side_inputs parametru opcjonalnego do ParallelRunStep. Ten parametr może służyć do instalowania folderu w kontenerze. Obecnie obsługiwane typy to DataReference i PipelineData.
    + Parametry przekazywane w ParallelRunConfig mogą zostać zastąpione przez przekazanie teraz parametrów potoku. Obsługiwane są nowe parametry potoku aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count i aml_process_count_per_node są już częścią starszej wersji).
  + **azureml-core**
    + Wdrożone usługi WebService będą teraz domyślnie `INFO` rejestrowane. Może to być kontrolowane przez ustawienie `AZUREML_LOG_LEVEL` zmiennej środowiskowej w wdrożonej usłudze.
    + Zestaw SDK języka Python używa usługi odnajdywania do użycia punktu końcowego "API" zamiast "potoków".
    + Zamień na nowe trasy we wszystkich wywołaniach zestawu SDK.
    + Zmieniono routing wywołań do ModelManagementService na nową ujednoliconą strukturę.
      + Udostępniona Metoda aktualizacji obszaru roboczego jest publicznie dostępna.
      + Dodano parametr image_build_compute w metodzie Update obszaru roboczego, aby zezwolić użytkownikom na aktualizowanie obliczeń dla kompilacji obrazu.
    + Dodano komunikaty o zaniechaniu do starego przepływu pracy profilowania. Stałe profilowania procesora i pamięci.
    + Dodano RSection jako część środowiska do uruchamiania zadań języka R.
    + Dodano weryfikację do `Dataset.mount` zgłaszania błędu, gdy źródło zestawu danych nie jest dostępne lub nie zawiera żadnych danych.
    + Dodano `--grant-workspace-msi-access` jako dodatkowy parametr interfejsu wiersza polecenia magazynu danych służącego do rejestrowania kontenera obiektów blob platformy Azure, który umożliwi rejestrację kontenera obiektów BLOB znajdującego się za siecią wirtualną.
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu utworzenia rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
    + Rozwiązano problem w aks.py _deploy.
    + Sprawdza integralność przekazywanych modeli, aby uniknąć cichych awarii magazynu.
    + Użytkownik może teraz określić wartość klucza uwierzytelniania podczas ponownego generowania kluczy dla usług WebService.
    + Naprawiono usterkę, w której wielkie litery nie mogą być używane jako nazwa wejściowa zestawu danych.
  + **Azure — ustawienia domyślne**
    + `azureml-dataprep` program zostanie teraz zainstalowany w ramach programu `azureml-defaults` . Nie jest już wymagane ręczne instalowanie programu przygotowywania danych [bezpiecznik] w celach obliczeniowych.
  + **azureml-interpret**
    + Zaktualizowano usługę Azure — Interpretuj ją pod kątem interpretacji — Community 0,6. *
    + Zaktualizowano interpretację usługi Azure 0.5.0
    + Dodano wyjątki w stylu programu Azure do interpretacji Azure
    + Stała Serializacja DeepScoringExplainer dla modeli keras
  + **azureml-mlflow**
    + Dodawanie obsługi dla Niesuwerennych chmur do usługi Azure. mlflow
  + **azureml-pipeline-core**
    + Notes oceniania partii potoków używa teraz ParallelRunStep
    + Naprawiono usterkę, w której wyniki PythonScriptStep mogą być nieprawidłowo używane ponownie pomimo zmiany listy argumentów
    + Dodano możliwość ustawiania typu kolumn podczas wywoływania parse_ * metod na `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Przeniesiono `AutoMLStep` do `azureml-pipeline-steps` pakietu. Przestarzałe w `AutoMLStep` programie `azureml-train-automl-runtime` .
    + Dodano przykład dokumentacji dla zestawu danych jako dane wejściowe PythonScriptStep
  + **azureml-tensorboard**
    + Zaktualizowano usługę Azure tensorboard w celu obsługi tensorflow 2,0
    + Pokaż poprawny numer portu przy użyciu niestandardowego portu Tensorboard w wystąpieniu obliczeniowym
  + **azureml-train-automl-client**
    + Rozwiązano problem polegający na tym, że niektóre pakiety mogą zostać zainstalowane w nieprawidłowych wersjach w zdalnych uruchomieniach.
    + Rozwiązano problem z zastępowaniem FeaturizationConfig, który filtruje niestandardową konfigurację cechowania.
  + **azureml-train-automl-runtime**
    + Rozwiązano problem z wykrywaniem częstotliwości w zdalnych uruchomieniach
    + Przeniesiono `AutoMLStep` w `azureml-pipeline-steps` pakiecie. Przestarzałe w `AutoMLStep` programie `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Obsługa PyTorch w wersji 1,4 w PyTorch szacowania
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK dla języka Python v 1.1.2 RC0 (wersja wstępna)

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Włączono wnioskowanie w trybie wsadowym (pobierając wiele wierszy raz) dla modeli AutoML ONNX
    + Ulepszone wykrywanie częstotliwości zestawów danych, Brak danych lub nieregularne punkty danych
    + Dodano możliwość usuwania punktów danych, które nie są zgodne z częstotliwością dominującą.
  + **azureml-automl-runtime**
    + Rozwiązano problem z błędem zgłoszonym, jeśli ziarna nie było obecne w zestawie szkoleniowym w zestawie testów
    + Usunięto wymaganie y_query podczas oceniania usługi prognozowania
  + **Azure-contrib-Mir**
    + Dodaje funkcje klasy MirWebservice w celu pobrania tokenu dostępu
  + **azureml-core**
    + Wdrożone usługi WebService będą teraz domyślnie `INFO` rejestrowane. Może to być kontrolowane przez ustawienie `AZUREML_LOG_LEVEL` zmiennej środowiskowej w wdrożonej usłudze.
    + Napraw iterację w `Dataset.get_all` celu zwrócenia wszystkich zestawów danych zarejestrowanych w obszarze roboczym.
    + Ulepsz komunikat o błędzie, gdy nieprawidłowy typ jest przekazaniem do `path` argumentu interfejsów API tworzenia zestawu danych.
    + Zestaw SDK języka Python używa usługi odnajdywania do użycia punktu końcowego "API" zamiast "potoków".
    + Zamień na nowe trasy we wszystkich wywołaniach zestawu SDK
    + Zmienia routing wywołań do ModelManagementService na nową ujednoliconą strukturę
      + Udostępniona Metoda aktualizacji obszaru roboczego jest publicznie dostępna.
      + Dodano parametr image_build_compute w metodzie aktualizacji obszaru roboczego, aby zezwolić użytkownikom na aktualizowanie obliczeń dla kompilacji obrazu
    +  Dodano komunikaty o zaniechaniu do starego przepływu pracy profilowania. Stałe profilowania CPU i limity pamięci
  + **azureml-interpret**
    + Aktualizacja programu Azure — Interpretuj do interpretacji — Community 0,6. *
  + **azureml-mlflow**
    + Dodawanie obsługi dla Niesuwerennych chmur do usługi Azure. mlflow
  + **azureml-pipeline-steps**
    + Przeniesiono `AutoMLStep` do `azureml-pipeline-steps package` . Przestarzałe w `AutoMLStep` programie `azureml-train-automl-runtime` .
  + **azureml-train-automl-client**
    + Rozwiązano problem polegający na tym, że niektóre pakiety mogą zostać zainstalowane w nieprawidłowych wersjach w zdalnych uruchomieniach.
  + **azureml-train-automl-runtime**
    + Rozwiązano problem z wykrywaniem częstotliwości w zdalnych uruchomieniach
    + Przeniesiono `AutoMLStep` do `azureml-pipeline-steps package` . Przestarzałe w `AutoMLStep` programie `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Przeniesiono `AutoMLStep` do `azureml-pipeline-steps package` . Przestarzałe w `AutoMLStep` programie `azureml-train-automl-runtime` .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK dla języka Python v 1.1.1 RC0 (wersja wstępna)

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu utworzenia rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
  + **azureml-automl-core**
    + Ulepszono rejestrowanie błędów.
  + **azureml-automl-runtime**
    + Rozwiązano problem związany z prognozą, gdy zestaw danych zawiera krótkie ziarna z długimi odstępami czasu.
    + Rozwiązano problem, gdy jest włączona funkcja Automax, a kolumna Date zawiera daty w postaci ciągów. Dodaliśmy poprawną konwersję i rozsądny błąd, jeśli konwersja na datę nie jest możliwa
    + Używanie natywnych NumPy i SciPy do serializacji i deserializacji danych pośrednich dla plik filecachestore (używany do lokalnego uruchamiania AutoML)
    + Naprawiono usterkę, która może spowodować zablokowanie nieprawidłowych przebiegów podrzędnych.
  + **Azure-Common — interfejs wiersza polecenia**
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu utworzenia rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
  + **azureml-core**
    + Dodano `--grant-workspace-msi-access` jako dodatkowy parametr interfejsu wiersza polecenia magazynu danych służącego do rejestrowania kontenera obiektów blob platformy Azure, który umożliwi rejestrację kontenera obiektów BLOB znajdującego się za siecią wirtualną
    + Profilowanie pojedynczego wystąpienia zostało naprawione w celu utworzenia rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
    + Rozwiązano problem w aks.py _deploy
    + Sprawdza integralność przekazywanych modeli, aby uniknąć cichych awarii magazynu.
  + **azureml-interpret**
    + dodano wyjątki w stylu programu Azure do interpretacji Azure
    + stała Serializacja DeepScoringExplainer dla modeli keras
  + **Azure — potok — rdzeń**
    + Notes oceniania partii potoków używa teraz ParallelRunStep
  + **azureml-pipeline-steps**
    + Przeniesiono `AutoMLStep` w `azureml-pipeline-steps` pakiecie. Przestarzałe w `AutoMLStep` programie `azureml-train-automl-runtime` .
  + **azureml-contrib-pipeline-steps**
    + Dodano side_inputs parametru opcjonalnego do ParallelRunStep. Ten parametr może służyć do instalowania folderu w kontenerze. Obecnie obsługiwane typy to DataReference i PipelineData.
  + **azureml-tensorboard**
    + Zaktualizowano usługę Azure tensorboard w celu obsługi tensorflow 2,0
  + **azureml-train-automl-client**
    + Rozwiązano problem z zastępowaniem FeaturizationConfig, który filtruje niestandardową konfigurację cechowania.
  + **azureml-train-automl-runtime**
    + Przeniesiono `AutoMLStep` w `azureml-pipeline-steps` pakiecie. Przestarzałe w `AutoMLStep` programie `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Obsługa PyTorch w wersji 1,4 w PyTorch szacowania
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.1.0 RC0 (wersja wstępna)

+ **Zmiany powodujące niezgodność**
  + **Semantyczne przechowywanie wersji 2.0.0**
    + Począwszy od wersji 1,1 usługi Azure ML Python SDK przyjmuje 2.0.0 wersji semantycznej. [Przeczytaj więcej tutaj](https://semver.org/). Wszystkie kolejne wersje będą zgodne z nowym schematem numeracji i kontraktem wersji semantycznej. 
  
+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-runtime**
    + Zwiększona prędkość cechowania.
    + Rozwiązano sprawdzanie częstotliwości podczas oceniania, a teraz w zadaniach prognozowania nie jest wymagana ścisła równoważność częstotliwości między zestawem pociągm i testowym.
  + **azureml-core**
    + Użytkownik może teraz określić wartość klucza uwierzytelniania podczas ponownego generowania kluczy dla usług WebService.
  + **azureml-interpret**
    + Zaktualizowano interpretację usługi Azure 0.5.0
  + **azureml-pipeline-core**
    + Naprawiono usterkę, w której wyniki PythonScriptStep mogą być nieprawidłowo używane ponownie pomimo zmiany listy argumentów
  + **azureml-pipeline-steps**
    + Dodano przykład dokumentacji dla zestawu danych jako dane wejściowe PythonScriptStep
  + **azureml-contrib-pipeline-steps**
    + Parametry przekazywane w ParallelRunConfig mogą zostać zastąpione przez przekazanie teraz parametrów potoku. Obsługiwane są nowe parametry potoku aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count i aml_process_count_per_node są już częścią starszej wersji).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.85

+ **Nowe funkcje**
  + **azureml-core**
    + Pobierz bieżące podstawowe ograniczenie użycia i przydziału dla zasobów AmlCompute w danym obszarze roboczym i subskrypcji
  
  + **azureml-contrib-pipeline-steps**
    + Zezwól użytkownikowi na przekazanie tabelarycznego zestawu danych jako wynik pośredni z poprzedniego kroku do parallelrunstep

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-runtime**
    + Usunięto wymaganie kolumny y_query w żądaniu do wdrożonej usługi prognozowania. 
    + "Y_query" został usunięty z sekcji żądania usługi notesu pomarańczowego Dominick.
    + Naprawiono usterkę uniemożliwiającą prognozowanie wdrożonych modeli, działającą na zestawach danych z kolumnami daty i godziny.
    + Dodano współczynnik korelacji Matthews jako metrykę klasyfikacji dla klasyfikacji binarnej i wieloklasowej.
  + **Azure-contrib — interpretacja**
    + Usunięto wyjaśnienie tekstu z platformy Azure-contrib-interpretuj jako wyjaśnienie tekstu zostało przeniesione do repozytorium interpretacji tekstu, które zostanie wkrótce udostępnione.
  + **azureml-core**
    + DataSet: użycie zestawu danych dla plików nie jest już zależne od numpy i Pandas do zainstalowania w ENV.
    + Zmieniono LocalWebservice.wait_for_deployment () w celu sprawdzenia stanu lokalnego kontenera platformy Docker przed podjęciem próby wysłania polecenia ping do punktu końcowego kondycji, znacznie skracając czas potrzebny na zgłoszenie niepowodzenia wdrożenia.
    + Naprawiono inicjalizację wewnętrznej właściwości używanej w LocalWebservice. reload (), gdy obiekt usługi jest tworzony na podstawie istniejącego wdrożenia przy użyciu konstruktora LocalWebservice ().
    + Edytowano komunikat o błędzie w celu wyjaśnienia.
    + Dodano nową metodę o nazwie get_access_token () do AksWebservice, która zwróci obiekt AksServiceAccessToken, który zawiera token dostępu, Odśwież po sygnaturze czasowej, wygasa przy użyciu sygnatury czasowej i typu tokenu. 
    + Przestarzała istniejąca Metoda get_token () w AksWebservice, ponieważ nowa metoda zwraca wszystkie informacje zwracane przez tę metodę.
    + Zmodyfikowano dane wyjściowe polecenia AZ ml Service Get-Access-token. Zmieniono nazwę tokenu na accessToken i refreshBy na refreshAfter. Dodano właściwości expiryOn i TokenType.
    + Stały get_active_runs
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Zaktualizowano kształt 0.33.0 i Interpretuj — społeczność do 0,4. *
  + **azureml-interpret**
    + Zaktualizowano kształt 0.33.0 i Interpretuj — społeczność do 0,4. *
  + **azureml-train-automl-runtime**
    + Dodano współczynnik korelacji Matthews jako metrykę klasyfikacji dla klasyfikacji binarnej i wieloklasowej.
    + Wycofaj flagę preprocesora z kodu i zamieniono ją na cechowania-cechowania jest domyślnie włączona

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.83

+ **Nowe funkcje**
  + Zestaw danych: Dodaj dwie opcje `on_error` i `out_of_range_datetime` w `to_pandas_dataframe` celu niepowodzenia, jeśli dane zawierają wartości błędów zamiast wypełniania `None` .
  + Obszar roboczy: dodano `hbi_workspace` flagę dla obszarów roboczych z danymi poufnymi, które umożliwiają dalsze szyfrowanie i wyłącza zaawansowaną diagnostykę w obszarze roboczym. Dodaliśmy również obsługę tworzenia własnych kluczy dla skojarzonego wystąpienia Cosmos DB, określając `cmk_keyvault` `resource_cmk_uri` Parametry i w przypadku utworzenia obszaru roboczego, co powoduje utworzenie wystąpienia Cosmos DB w subskrypcji podczas aprowizacji obszaru roboczego. [Przeczytaj więcej tutaj.](./concept-data-encryption.md#azure-cosmos-db)

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-runtime**
    + Naprawiono regresję, która spowodowała wygenerowanie TypeError podczas uruchamiania AutoML w wersjach języka Python poniżej 3.5.4.
  + **azureml-core**
    + Stała usterka w programie `datastore.upload_files` była ścieżką względną, która nie została uruchomiona z programem, `./` nie można jej użyć.
    + Dodano komunikaty o zaniechaniu dla wszystkich ścieżek kodu klasy obrazu
    + Naprawiono konstrukcję adresu URL Zarządzanie modelami dla regionu 21Vianet platformy Azure w Chinach.
    + Rozwiązano problem polegający na tym, że modele używające source_dir nie mogły zostać spakowane dla Azure Functions.    
    + Dodano opcję do [Environment.build_local ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) w celu wypchnięcia obrazu do rejestru kontenerów obszaru roboczego platformy Azure
    + Zaktualizowano zestaw SDK w taki sposób, aby korzystał z nowej biblioteki tokenów na platformie Azure Synapse w sposób zgodny ze sobą.
  + **azureml-interpret**
    + Naprawiono usterkę, która nie została zwrócona w przypadku braku wyjaśnień dostępnych do pobrania. Teraz zgłasza wyjątek, zgodne zachowanie w innym miejscu.
  + **azureml-pipeline-steps**
    + Niedozwolone przekazanie `DatasetConsumptionConfig` parametru s `Estimator` do `inputs` , gdy `Estimator` zostanie użyty w `EstimatorStep` .
  + **Azure-SDK**
    + Dodano klienta AutoML do pakietu Azure-SDK, co umożliwia przesłanie zdalnych przebiegów AutoML bez instalowania pełnego pakietu AutoML.
  + **azureml-train-automl-client**
    + Skorygowane wyrównanie danych wyjściowych z konsoli dla uruchomień AutoML
    + Rozwiązano problem polegający na tym, że w zdalnej amlcompute można zainstalować niepoprawną wersję programu Pandas.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.81

+ **Poprawki i ulepszenia błędów**
  + **Azure-contrib — interpretacja**
    + Odłóż zależność kształtu do interpretowania przez społeczność od platformy Azure — Interpretuj
  + **azureml-core**
    + Element docelowy obliczeń można teraz określić jako parametr do odpowiednich obiektów konfiguracji wdrożenia. Jest to w odróżnieniu od nazwy elementu docelowego obliczeń, który ma zostać wdrożony, a nie obiektu zestawu SDK.
    + Dodano informacje CreatedBy do modelu i obiektów usługi. Dostęp through.created_by
    + Naprawiono ContainerImage. Run (), który nieprawidłowo konfiguruje port HTTP kontenera Docker.
    + Ustaw jako `azureml-dataprep` Opcjonalnie `az ml dataset register` polecenie interfejsu wiersza polecenia
    + Naprawiono usterkę, w której `TabularDataset.to_pandas_dataframe` nastąpiło nieprawidłowe powracanie do alternatywnego czytnika i wydrukowanie ostrzeżenia.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Odłóż zależność kształtu do interpretowania przez społeczność od platformy Azure — Interpretuj
  + **azureml-pipeline-core**
    + Dodano nowy krok potoku w `NotebookRunnerStep` celu uruchomienia lokalnego notesu w ramach potoku.
    + Usunięto przestarzałe funkcje get_all dla PublishedPipelines, harmonogramów i PipelineEndpoints
  + **azureml-train-automl-client**
    + Rozpoczęto przestarzałość data_script jako dane wejściowe do AutoML.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.79

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Usunięto featurizationConfig do zarejestrowania
      + Zaktualizowano rejestrowanie w celu zarejestrowania tylko "Auto"/"off"/"dostosowany".
  + **azureml-automl-runtime**
    + Dodano obsługę Pandas. Seria i Pandas. Kategorii do wykrywania typu danych kolumny. Poprzednio obsługiwane tylko numpy. ndarray
      + Dodano powiązane zmiany kodu w celu poprawnego obsłużenia kategorii dtype.
    + Ulepszono interfejs funkcji prognozowania: parametr y_pred został wykonany jako opcjonalny. -Docstrings zostały ulepszone.
  + **azureml-contrib-dataset**
    + Naprawiono usterkę, w której nie można zainstalować zestawów danych z etykietami.
  + **azureml-core**
    + Poprawka usterki dla programu `Environment.from_existing_conda_environment(name, conda_environment_name)` . Użytkownik może utworzyć wystąpienie środowiska, które jest dokładną repliką środowiska lokalnego
    + Zmieniono domyślne metody zestawów danych związanych z serią czasową `include_boundary=True` .
  + **azureml-train-automl-client**
    + Rozwiązano problem polegający na tym, że wyniki walidacji nie są drukowane, gdy dla opcji Pokaż dane wyjściowe ustawiono wartość FAŁSZ.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.76

+ **Zmiany powodujące niezgodność**
  + Problemy z aktualizacją uczenia maszynowego — AutoML
    + Uaktualnianie do usługi Azure-uczenie-automl>= 1.0.76 z usługi Azure-pociąg-automl<1.0.76 może spowodować niepowodzenie instalacji częściowych. Aby rozwiązać ten problem, można uruchomić skrypt Instalatora znajdujący się pod adresem https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . Lub jeśli używasz narzędzia PIP bezpośrednio, możesz:
      + "Instalacja PIP--upgrade Azure-pociąg-automl"
      + "Narzędzie pip install--ignore-Install-automl-Client"
    + lub można odinstalować starą wersję przed uaktualnieniem
      + "PIP Uninstall-pociąg-automl"
      + "pip install Azure-pociąg-automl"

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-runtime**
    + Funkcja AutoML będzie teraz uwzględniać zarówno klasy prawdziwe, jak i fałszywe w przypadku obliczania średniej metryk skalarnych dla zadań klasyfikacji binarnej.
    + Przeniesiono kod uczenia maszynowego i szkoleniowego w usłudze Azure AutoML-Core do nowego pakietu Azure-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Podczas wywoływania `to_pandas_dataframe` zestawu danych z etykietą przy użyciu opcji Pobierz można teraz określić, czy zastąpić istniejące pliki, czy nie.
    + Po wywołaniu `keep_columns` lub `drop_columns` w wyniku porzucenia szeregów czasowych, etykiet lub kolumny obrazu, odpowiednie możliwości zostaną również porzucone dla zestawu danych.
    + Rozwiązano problem z programem ładującym pytorch na potrzeby zadania wykrywania obiektu.
  + **Azure-contrib — interpretacja**
    + Usunięto widżet pulpitu nawigacyjnego wyjaśnienie z platformy Azure-contrib-interpreter, zmieniony pakiet, aby odwołać się do nowego elementu w interpret_community
    + Zaktualizowana wersja interpretera — społeczność 0.2.0
  + **azureml-core**
    + Zwiększ wydajność programu `workspace.datasets` .
    + Dodano możliwość rejestrowania Azure SQL Database datastore przy użyciu nazwy użytkownika i uwierzytelniania hasła
    + Poprawka do ładowania RunConfigurations ze ścieżek względnych.
    + Podczas wywoływania `keep_columns` lub `drop_columns` w wyniku porzucenia kolumny szeregów czasowych odpowiednie możliwości zostaną porzucone również dla zestawu danych.
  + **azureml-interpret**
    + zaktualizowana wersja interpretera — społeczność 0.2.0
  + **azureml-pipeline-steps**
    + Udokumentowane obsługiwane wartości dla `runconfig_pipeline_params` kroków potoku usługi Azure Machine Learning.
  + **azureml-pipeline-core**
    + Dodano opcję interfejsu wiersza polecenia w celu pobrania danych wyjściowych w formacie JSON dla poleceń potoku.
  + **Uczenie maszynowe — automl**
    + Podziel usługę Azure AutoML na dwa pakiety, pakiet klienta Azure-Learning-AutoML-Client i pakiet uczenia maszynowego Azure — uczenie-AutoML — środowisko uruchomieniowe
  + **azureml-train-automl-client**
    + Dodano klienta zubożonego do przesyłania eksperymentów AutoML bez konieczności lokalnego instalowania żadnych zależności usługi Machine Learning.
    + Stałe rejestrowanie automatycznie wykrytych spowolnienia, stopniowe rozmiary okien i maksymalne Horizons w zdalnych uruchomieniach.
  + **azureml-train-automl-runtime**
    + Dodano nowy pakiet AutoML w celu wyodrębnienia składników uczenia maszynowego i środowiska uruchomieniowego z klienta programu.
  + **Azure-contrib-pociąg-RL**
    + Dodano obsługę uczenia wzmacniania w zestawie SDK.
    + Dodano obsługę AmlWindowsCompute w zestawie RL SDK.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.74

  + **Funkcje w wersji zapoznawczej**
    + **azureml-contrib-dataset**
      + Po zaimportowaniu Azure-contrib-DataSet można wywołać `Dataset.Labeled.from_json_lines` zamiast programu, `._Labeled` Aby utworzyć zestaw danych z etykietą.
      + Podczas wywoływania `to_pandas_dataframe` zestawu danych z etykietą przy użyciu opcji Pobierz można teraz określić, czy zastąpić istniejące pliki, czy nie.
      + Po wywołaniu `keep_columns` lub `drop_columns` w wyniku porzucenia szeregów czasowych, etykiet lub kolumny obrazu, odpowiednie możliwości zostaną również porzucone dla zestawu danych.
      + Rozwiązano problemy z modułem ładującym PyTorch podczas wywoływania `dataset.to_torchvision()` .

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Dodano profilowanie modelu do interfejsu wiersza polecenia w wersji zapoznawczej.
    + Naprawia istotną zmianę w usłudze Azure Storage powodującą awarię interfejsu wiersza polecenia platformy Azure.
    + Dodano typ Load Balancer do MLC dla typów AKS
  + **azureml-automl-core**
    + Rozwiązano problem z wykrywaniem maksymalnego poziomu w szeregach czasowych, mających brakujące wartości i wiele ziaren.
    + Rozwiązano problem z błędami podczas generowania podziałów wzajemnego sprawdzania poprawności.
    + Zastąp tę sekcję komunikatem w formacie promocji, aby pojawić się w informacjach o wersji:-Ulepszona obsługa krótkich ziaren w zestawach danych prognozowanie.
    + Rozwiązano problem związany z maską niektórych informacji o użytkowniku podczas rejestrowania. -Ulepszone rejestrowanie błędów podczas prognozowania przebiegów.
    + Dodawanie psutil jako zależności Conda do automatycznie generowanego pliku wdrożenia YML.
  + **Azure-contrib-Mir**
    + Naprawia istotną zmianę w usłudze Azure Storage powodującą awarię interfejsu wiersza polecenia platformy Azure.
  + **azureml-core**
    + Naprawia usterkę, która spowodowała wdrożenie modeli wdrożonych na Azure Functions w celu utworzenia rozwiązania 500S.
    + Rozwiązano problem polegający na tym, że plik amlignore nie został zastosowany do migawek.
    + Dodano nowy amlcompute.get_active_runs interfejsu API, który zwraca generator dla uruchomionych i umieszczonych w kolejce przebiegów w danym amlcompute.
    + Dodano Load Balancer typ do MLC dla typów AKS.
    + Dodano append_prefix parametru bool do download_files w run.py i download_artifacts_from_prefix w artifacts_client. Ta flaga służy do selektywnego spłaszczania ścieżki źródła, więc do output_directory dodawany jest tylko nazwa pliku lub folderu.
    + Rozwiązano problem z deserializacjim dotyczący `run_config.yml` użycia zestawu danych.
    + Podczas wywoływania `keep_columns` lub `drop_columns` w wyniku porzucenia kolumny szeregów czasowych odpowiednie możliwości zostaną porzucone również dla zestawu danych.
  + **azureml-interpret**
    + Zaktualizowano interpreter — wersja społecznościowa do 0.1.0.3
  + **Uczenie maszynowe — automl**
    + Rozwiązano problem polegający na tym, że automl_step mogą nie drukować problemów z walidacją.
    + Naprawiono pomyślne register_model, nawet jeśli w środowisku modelu brakuje lokalnie zależności.
    + Rozwiązano problem polegający na tym, że niektóre zdalne uruchomienia nie zostały włączone.
    + Dodanie rejestrowania wyjątku powodującego przedwczesne uruchomienie lokalnego uruchomienia.
  + **azureml-train-core**
    + Należy wziąć pod uwagę resume_from uruchomienia w obliczu zautomatyzowanego przebiegu strojenia najlepszych parametrów.
  + **azureml-pipeline-core**
    + Obsługa stałych parametrów w konstrukcji argumentu potoku.
    + Dodano parametr YAML potoku i typ kroku.
    + Nowy format YAML dla kroku potoku i dodano ostrzeżenie o zaniechaniu dla starego formatu.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Środowisko sieci Web

Strona docelowa współpracy obszaru roboczego w programie [https://ml.azure.com](https://ml.azure.com) została ulepszona i oznaczana jako Azure Machine Learning Studio.

Z poziomu programu Studio można nauczyć, testować i wdrażać Azure Machine Learning zasoby, takie jak zestawy danych, potoki, modele, punkty końcowe itd., oraz zarządzać nimi.

Uzyskaj dostęp do następujących narzędzi autorskich opartych na sieci Web z programu Studio:

| Narzędzie oparte na sieci Web | Opis | 
|-|-|-|
| Maszyna wirtualna w notesie (wersja zapoznawcza) | W pełni zarządzana stacja robocza oparta na chmurze | 
| [Automatyczne Uczenie maszynowe](tutorial-first-experiment-automated-ml.md) (wersja zapoznawcza) | Brak środowiska kodu do automatyzacji tworzenia modelu uczenia maszynowego | 
| [Projektant](concept-designer.md) | Narzędzie do modelowania i upuszczania w usłudze Machine Learning, znane wcześniej jako interfejs wizualizacji | 


### <a name="azure-machine-learning-designer-enhancements"></a>Udoskonalenia projektanta Azure Machine Learning

+ Dawniej znany jako interfejs wizualizacji 
+    11 nowe [moduły](algorithm-module-reference/module-reference.md) , w tym zalecenia, klasyfikatory i narzędzia szkoleniowe, w tym Inżynieria funkcji, wzajemne sprawdzanie poprawności i Przekształcanie danych.

### <a name="r-sdk"></a>Zestaw SDK języka R 
 
Analitycy danych i deweloperzy AI używają [zestawu SDK Azure Machine Learning dla języka R](tutorial-1st-r-experiment.md) do kompilowania i uruchamiania przepływów pracy uczenia maszynowego przy użyciu Azure Machine Learning.

Zestaw Azure Machine Learning SDK dla języka R używa `reticulate` pakietu do powiązania z zestawem Python SDK. Dzięki powiązaniu bezpośrednio z językiem Python zestaw SDK dla języka R umożliwia dostęp do podstawowych obiektów i metod zaimplementowanych w zestawie Python SDK z dowolnego wybranego środowiska języka R.

Główne możliwości zestawu SDK obejmują:

+    Zarządzanie zasobami w chmurze na potrzeby monitorowania, rejestrowania i organizowania eksperymentów uczenia maszynowego.
+    Uczenie modeli przy użyciu zasobów w chmurze, w tym szkoleń modelu procesora GPU.
+    Wdróż modele jako usługi WebService na Azure Container Instances (ACI) i Azure Kubernetes Service (AKS).

Zapoznaj się z [witryną internetową pakietu](https://azure.github.io/azureml-sdk-for-r) , aby uzyskać pełną dokumentację.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integrację z usługą Event Grid 

Azure Machine Learning jest teraz dostawcą zasobów dla Event Grid, można skonfigurować zdarzenia uczenia maszynowego za pomocą Azure Portal lub interfejsu wiersza polecenia platformy Azure. Użytkownicy mogą tworzyć zdarzenia na potrzeby ukończenia przebiegu, rejestracji modelu, wdrożenia modelu i dryfowania danych. Te zdarzenia mogą być kierowane do programów obsługi zdarzeń obsługiwanych przez Event Grid do użycia. Więcej informacji można znaleźć w artykule [schemat](../event-grid/event-schema-machine-learning.md) zdarzeń usługi Machine Learning i artykuły dotyczące [samouczków](how-to-use-event-grid.md) .

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.72

+ **Nowe funkcje**
  + Dodano monitory zestawu danych za pośrednictwem pakietu [**Azure-datadryfing**](/python/api/azureml-datadrift) , co pozwala na monitorowanie zestawów danych szeregów czasowych na potrzeby dryfowania lub innych zmian statystycznych w czasie. Alerty i zdarzenia mogą być wyzwalane w przypadku wykrycia dryfu lub spełnienia innych warunków dotyczących danych. Zapoznaj się z [naszą dokumentacją](how-to-monitor-datasets.md) , aby uzyskać szczegółowe informacje.
  + Ogłaszamy dwie nowe wersje (nazywane także zamiennie jednostkami SKU) w Azure Machine Learning. W tej wersji możesz teraz utworzyć obszar roboczy Azure Machine Learning w warstwie Podstawowa lub Enterprise. Wszystkie istniejące obszary robocze zostaną domyślnie przeszukane w wersji podstawowa. Możesz też przejść do Azure Portal lub do programu Studio, aby uaktualnić obszar roboczy w dowolnym momencie. Możesz utworzyć obszar roboczy podstawowy lub Enterprise na podstawie Azure Portal. Zapoznaj się z [naszą dokumentacją](./how-to-manage-workspace.md) , aby dowiedzieć się więcej. W zestawie SDK wersja obszaru roboczego można określić przy użyciu właściwości "SKU" obiektu obszaru roboczego.
  + Wprowadzono również usprawnienia Azure Machine Learning obliczeń — teraz można wyświetlać metryki dla klastrów (takie jak łączna liczba węzłów, uruchomione węzły, łączny przydział rdzeni) w Azure Monitor, oprócz wyświetlania dzienników diagnostycznych na potrzeby debugowania. Ponadto można także wyświetlić uruchomione lub umieszczone w kolejce uruchomienia w klastrze oraz szczegóły, takie jak adresy IP różnych węzłów w klastrze. Można je wyświetlić w portalu lub za pomocą odpowiednich funkcji w zestawie SDK lub interfejsie wiersza polecenia.

  + **Funkcje w wersji zapoznawczej**
    + Firma Microsoft udostępnia w wersji zapoznawczej obsługę szyfrowania dysków lokalnego dysku SSD w programie Azure Machine Learning COMPUTE. Zgłoś bilet pomocy technicznej, aby umożliwić korzystanie z tej funkcji w ramach subskrypcji.
    + Publiczna wersja zapoznawcza Azure Machine Learning wnioskowania partii. Azure Machine Learning wnioskowanie wsadowe odwołuje się do dużych zadań wnioskowania, które nie są zależne od czasu. Wnioskowanie wsadowe zapewnia ekonomiczne skalowanie obliczeniowe z zastosowaniem niezrównanej przepływności dla aplikacji asynchronicznych. Jest zoptymalizowany pod kątem wysokiej przepływności, pożaru i zapomnienia w przypadku dużych kolekcji danych.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
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
  + **Azure — interfejs wiersza polecenia**
    + Interfejs wiersza polecenia obsługuje teraz pakowanie modeli.
    + Dodano interfejs wiersza polecenia zestawu danych. Aby uzyskać więcej informacji: `az ml dataset --help`
    + Dodano obsługę wdrażania i pakowania obsługiwanych modeli (ONNX, scikit — uczenie i TensorFlow) bez wystąpienia InferenceConfig.
    + Dodano flagę zastępowania dla wdrożenia usługi (ACI i AKS) w zestawie SDK i interfejsie wiersza polecenia. Jeśli ta wartość jest określona, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, program utworzy nową usługę.
    + Modele można zarejestrować przy użyciu dwóch nowych platform, Onnx i Tensorflow. — Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
  + **azureml-automl-core**
    + Szkolenie iteracji zostanie uruchomione w procesie podrzędnym tylko wtedy, gdy są ustawiane ograniczenia środowiska uruchomieniowego.
    + Dodano Guardrail do prognozowania zadań, aby sprawdzić, czy określony max_horizon spowoduje problem z pamięcią na danym komputerze. W takim przypadku zostanie wyświetlony komunikat Guardrail.
    + Dodano obsługę złożonych częstotliwości, takich jak dwa lata i jeden miesiąc. -Dodano zrozumiały komunikat o błędzie, jeśli nie można ustalić częstotliwości.
    + Dodaj usługę Azure-default do automatycznie generowanej ENV Conda, aby rozwiązać błąd wdrażania modelu
    + Zezwalaj na konwertowanie pośrednich danych w potoku Azure Machine Learning, które mają być konwertowane na tabelaryczny zestaw danych i używane w programie `AutoMLStep` .
    + Zaimplementowana aktualizacja celu kolumny na potrzeby przesyłania strumieniowego.
    + Zaimplementowana aktualizacja parametrów transformatora dla programu Streamer i HashOneHotEncoder na potrzeby przesyłania strumieniowego.
    + Dodano bieżący rozmiar danych i minimalny wymagany rozmiar danych do komunikatów o błędach walidacji.
    + Zaktualizowano minimalny wymagany rozmiar danych na potrzeby weryfikacji krzyżowej w celu zagwarantowania co najmniej dwóch próbek w każdym złożeniu walidacji.
  + **Azure-Common — interfejs wiersza polecenia**
    + Interfejs wiersza polecenia obsługuje teraz pakowanie modeli.
    + Modele można zarejestrować przy użyciu dwóch nowych platform, Onnx i Tensorflow.
    + Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
  + **Azure-contrib-gbdt**
    + Naprawiono kanał wydania dla notesu
    + Dodano ostrzeżenie dla nieobsługiwanego elementu docelowego obliczeń AmlCompute
    + Dodano LightGMB szacowania do pakietu Azure-contrib-gbdt
  + [**azureml-core**](/python/api/azureml-core)
    + Interfejs wiersza polecenia obsługuje teraz pakowanie modeli.
    + Dodaj ostrzeżenie o zaniechaniu dla przestarzałych interfejsów API zestawu danych. Zobacz temat Zmień informacje o interfejsie API usługi DataSet pod adresem https://aka.ms/tabular-dataset .
    + Zmień [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) , aby zwracała nazwę i wersję rejestracji, jeśli zestaw danych jest zarejestrowany.
    + Usuń usterkę, która ScriptRunConfig z zestawem danych jako argumentem, nie można wielokrotnie użyć do przesłania uruchomienia eksperymentu.
    + Zestawy danych pobrane podczas przebiegu będą śledzone i mogą być widoczne na stronie Szczegóły uruchamiania lub przez wywołanie [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) po zakończeniu przebiegu.
    + Zezwalaj na konwertowanie pośrednich danych w potoku Azure Machine Learning, które mają być konwertowane na tabelaryczny zestaw danych i używane w programie [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Dodano obsługę wdrażania i pakowania obsługiwanych modeli (ONNX, scikit — uczenie i TensorFlow) bez wystąpienia InferenceConfig.
    + Dodano flagę zastępowania dla wdrożenia usługi (ACI i AKS) w zestawie SDK i interfejsie wiersza polecenia. Jeśli ta wartość jest określona, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, program utworzy nową usługę.
    +  Modele można zarejestrować przy użyciu dwóch nowych platform, Onnx i Tensorflow. Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
    + Dodano nowy magazyn danych dla Azure Database for MySQL. Dodano przykład używania Azure Database for MySQL w DataTransferStep w potokach Azure Machine Learning.
    + Dodano funkcje umożliwiające dodawanie i usuwanie tagów z eksperymentów dodanych do funkcji usuwania tagów z przebiegów
    + Dodano flagę zastępowania dla wdrożenia usługi (ACI i AKS) w zestawie SDK i interfejsie wiersza polecenia. Jeśli ta wartość jest określona, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, program utworzy nową usługę.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Przeniesiono z `azureml-contrib-datadrift` do `azureml-datadrift`
    + Dodano obsługę monitorowania zestawów danych szeregów czasowych dla dryfowania i innych środków statystycznych
    + Nowe metody `create_from_model()` i `create_from_dataset()` [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) klasy. `create()`Metoda zostanie uznana za przestarzałą.
    + Korekty wizualizacji w języku Python i interfejsie użytkownika w programie Azure Machine Learning Studio.
    + Obsługuj cotygodniowe i comiesięczne planowanie monitorów, a także codziennie dla monitorów zestawu danych.
    + Obsługa wypełniania metryk monitora danych w celu analizowania danych historycznych dla monitorów zestawu danych.
    + Różne poprawki błędów
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + usługi Azure datasubmit nie są już potrzebne do przesyłania Azure Machine Learning potoku z pliku potoku `yaml` .
  + [**Uczenie maszynowe — automl**](/python/api/azureml-train-automl-runtime/)
    + Dodaj usługę Azure-default do automatycznie generowanej ENV Conda, aby rozwiązać błąd wdrażania modelu
    + AutoML szkolenie zdalne obejmuje teraz usługę Azure-defaults, która umożliwia ponowne użycie ENV na potrzeby wnioskowania.
  + **azureml-train-core**
    + Dodano obsługę PyTorch 1,3 w [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) szacowania

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interfejs wizualny (wersja zapoznawcza)

+ Azure Machine Learning graficzny interfejs (wersja zapoznawcza) został przestawiony do uruchomienia na [potokach Azure Machine Learning](concept-ml-pipelines.md). Potoki (wcześniej znane jako eksperymenty) utworzone w interfejsie wizualizacji są teraz w pełni zintegrowane z podstawowymi Azure Machine Learning środowiska.
  + Ujednolicone środowisko zarządzania z zasobami zestawu SDK
  + Przechowywanie wersji i śledzenie dla modeli interfejsu wizualizacji, potoków i punktów końcowych
  + Przeprojektowany interfejs użytkownika
  + Dodano wdrożenie wnioskowania o partii
  + Dodano obsługę usługi Azure Kubernetes Service (AKS) dla celów obliczeniowych wnioskowania
  + Nowy przepływ pracy tworzenia potoku w języku Python
  + Nowa [Strona](https://ml.azure.com) docelowa dla narzędzi do tworzenia wizualnego

+ **Nowe moduły**
  + Zastosuj operacje matematyczne
  + Zastosuj transformację SQL
  + Przytnij wartości
  + Podsumowywanie danych
  + Importuj z SQL Database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.69

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Ograniczanie objaśnień modelu do najlepszego przebiegu zamiast obliczania wyjaśnień dla każdego przebiegu. Zmiana tego zachowania na Local, Remote i ADB.
    + Dodano obsługę wyjaśnień modelu na żądanie dla interfejsu użytkownika
    + Dodano psutil jako zależność `automl` i uwzględnione psutil jako zależność Conda w amlcompute.
    + Rozwiązano problem związany z algorytmem heurystycznego spowolnienia i oknem kroczącym dla danych prognozowania, który określa niektóre serie mogące spowodować błędy algebry liniowe
      + Dodano drukowanie dla parametrów z algorytmem heurystycznym określonych w przebiegach prognozowania.
  + **azureml-contrib-datadrift**
    + Dodano ochronę podczas tworzenia metryk danych wyjściowych, jeśli w pierwszej sekcji nie ma dryfu poziomu zestawu danych.
  + **Azure-contrib — interpretacja**
    + Nazwa pakietu "Azure-contrib-wyjaśnij" została zmieniona na "Azure-contrib-Interpretuj"
  + **azureml-core**
    + Dodano interfejs API służący do wyrejestrowywania zestawów danych. `dataset.unregister_all_versions()`
    + Nazwa pakietu "Azure-contrib-wyjaśnij" została zmieniona na "Azure-contrib-interpretacja".
  + **[azureml-core](/python/api/azureml-core)**
    + Dodano interfejs API służący do wyrejestrowywania zestawów danych. zestawu. [unregister_all_versions ()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Dodano interfejs API zestawu danych w celu sprawdzenia czasu zmiany danych. `dataset.data_changed_time`.
    + Możliwość użycia `FileDataset` i `TabularDataset` jako danych wejściowych do `PythonScriptStep` , `EstimatorStep` i `HyperDriveStep` w potoku Azure Machine Learning
    + `FileDataset.mount`Ulepszona wydajność programu dla folderów zawierających dużą liczbę plików
    + Możliwość korzystania z [FileDataset](/python/api/azureml-core/azureml.data.filedataset) i [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) jako danych wejściowych dla [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep), [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)i [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) w potoku Azure Machine Learning.
    + Wydajność FileDataset. Ulepszono [instalację ()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) w przypadku folderów zawierających dużą liczbę plików
    + Dodano adres URL do znanych zaleceń dotyczących błędów w szczegółach uruchomienia.
    + Naprawiono usterkę w run.get_metrics, w której żądania mogą zakończyć się niepowodzeniem, Jeśli uruchomienie miało zbyt wiele elementów podrzędnych
    + Naprawiono usterkę w [Run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) , w której żądania mogą zakończyć się niepowodzeniem, Jeśli uruchomienie miało zbyt wiele elementów podrzędnych
    + Dodano obsługę uwierzytelniania w klastrze Arcadia.
    + Utworzenie obiektu eksperymentu powoduje pobranie lub utworzenie eksperymentu w obszarze roboczym Azure Machine Learning na potrzeby śledzenia historii uruchamiania. Identyfikator eksperymentu i czas archiwizowany są wypełniane w obiekcie eksperymentu podczas tworzenia. Przykład: eksperyment = eksperyment (obszar roboczy, "nowy eksperyment") experiment_id = experiment.id Archive () i reactivate () są funkcjami, które mogą być wywoływane w ramach eksperymentu w celu ukrycia i przywrócenia eksperymentu z wyświetlania w środowisku użytkownika lub zwrócenia go domyślnie w wywołaniu eksperymentów z listą. Jeśli zostanie utworzony nowy eksperyment o takiej samej nazwie jak zarchiwizowany eksperyment, można zmienić nazwę zarchiwizowanego eksperymentu podczas ponownej aktywacji, przekazując nową nazwę. Może istnieć tylko jeden aktywny eksperyment o danej nazwie. Przykład: experiment1 = eksperyment (obszar roboczy, "aktywny eksperyment") experiment1. archiwalny () # Utwórz nowy aktywny eksperyment o takiej samej nazwie jak zarchiwizowany. experiment2. = Eksperyment (obszar roboczy, "aktywny eksperyment") experiment1. reactivate (new_name = "poprzednio aktywny eksperyment") lista metod statycznych () na Eksperymentie może przyjmować filtr nazw i filtr widoku. Wartości ViewType to "ACTIVE_ONLY", "ARCHIVED_ONLY" i "ALL" przykład: archived_experiments = eksperyment. list (Workspace, view_type = "ARCHIVED_ONLY") all_first_experiments = eksperyment. list (Workspace, Name = "pierwszy eksperyment", view_type = "wszystkie")
    + Obsługa użycia środowiska do wdrażania modelu i aktualizacji usługi
  + **azureml-datadrift**
    + Atrybut show klasy DataDriftDector nie obsługuje już opcjonalnego argumentu "with_details". Atrybut show będzie zawierał tylko współczynnik dryfu danych i udział danych w kolumnach funkcji.
    + DataDriftDetector zmiany zachowania atrybutu "get_output":
      + Start_time parametru wejściowego, end_time są opcjonalne zamiast obowiązkowe;
      + Dane wejściowe określonego start_time i/lub end_time z określonym run_id w tym samym wywołaniu spowodują wyjątek błędu wartości, ponieważ wykluczają się wzajemnie.
      + Według danych wejściowych start_time i/lub end_time zostaną zwrócone tylko wyniki zaplanowanego uruchomienia.
      + Parametr "daily_latest_only" jest przestarzały.
    + Obsługa pobierania danych wyjściowych z dryfem opartym na DataSet.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Zmienia nazwę pakietu usługi Uczenie maszynowe-Wyjaśnij, aby obtłumaczyć usługę Azure, zachowując stary pakiet pod kątem zgodności z poprzednimi wersjami
    + Naprawiono `automl` usterkę z nieprzetworzonymi wyjaśnieniami, które nie są domyślnie pobierane przy pobieraniu z ExplanationClient
    + Dodaj wsparcie `ScoringExplainer` do utworzenia bezpośrednio przy użyciu `MimicWrapper`
  + **azureml-pipeline-core**
    + Zwiększona wydajność tworzenia dużych potoków
  + **azureml-train-core**
    + Dodano obsługę TensorFlow 2,0 w TensorFlow szacowania
  + **Uczenie maszynowe — automl**
    + Utworzenie obiektu [eksperymentu](/python/api/azureml-core/azureml.core.experiment.experiment) powoduje pobranie lub utworzenie eksperymentu w obszarze roboczym Azure Machine Learning na potrzeby śledzenia historii uruchamiania. Identyfikator eksperymentu i czas archiwizowany są wypełniane w obiekcie eksperymentu podczas tworzenia. Przykład:

        ```py
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        Funkcja [Archive ()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) i [reactivate ()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) to funkcje, które mogą być wywoływane w ramach eksperymentu w celu ukrycia i przywrócenia eksperymentu z wyświetlania w środowisku użytkownika lub zwrócenia domyślnie w wywołaniu eksperymentów z listą. Jeśli zostanie utworzony nowy eksperyment o takiej samej nazwie jak zarchiwizowany eksperyment, można zmienić nazwę zarchiwizowanego eksperymentu podczas ponownej aktywacji, przekazując nową nazwę. Może istnieć tylko jeden aktywny eksperyment o danej nazwie. Przykład:

        ```py
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Lista metod statycznych [()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) na eksperymentie może przyjmować filtr nazw i filtru widoku. Wartości ViewType są "ACTIVE_ONLY", "ARCHIVED_ONLY" i "ALL". Przykład:

        ```py
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Obsługa użycia środowiska do wdrażania modelu i aktualizacji usługi.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + Atrybut show klasy [DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) nie obsługuje już opcjonalnego argumentu "with_details". Atrybut show będzie zawierał tylko współczynnik dryfu danych i udział danych w kolumnach funkcji.
    + DataDriftDetector funkcja [get_output] Python/API/azureml-datadrift/Azure. datadryf. DataDriftDetector. DataDriftDetector # Get-Output-Start-Time-none--End-Time-none--Run-ID-None-) zmiany zachowania:
      + Start_time parametru wejściowego, end_time są opcjonalne zamiast obowiązkowe;
      + Wejście start_time i/lub end_time z określonym run_id w tym samym wywołaniu spowoduje wyjątek błędu wartości, ponieważ wykluczają się wzajemnie.
      + Według danych wejściowych start_time i/lub end_time zostaną zwrócone tylko wyniki zaplanowanego uruchomienia.
      + Parametr "daily_latest_only" jest przestarzały.
    + Obsługa pobierania danych wyjściowych z dryfem opartym na DataSet.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Dodawanie obsługi [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer?view=azure-ml-py&preserve-view=true) do tworzenia bezpośrednio przy użyciu MimicWrapper
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Ulepszona wydajność tworzenia dużych potoków.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + Dodano obsługę TensorFlow 2,0 w [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) szacowania.
  + **[Uczenie maszynowe — automl](/python/api/azureml-train-automl-runtime/)**
    + Uruchomienie przebiegu nadrzędnego nie powiedzie się, jeśli iteracja instalacji nie powiodła się, ponieważ organizacja już ją obsłuży.
    + Dodano obsługę funkcji Local-Docker i lokalnego Conda na potrzeby eksperymentów AutoML
    + Dodano obsługę funkcji Local-Docker i lokalnego Conda na potrzeby eksperymentów AutoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nowe środowisko sieci Web (wersja zapoznawcza) dla Azure Machine Learning obszarów roboczych

Karta eksperymenty w [nowym portalu obszaru roboczego](https://ml.azure.com) została zaktualizowana, aby analityki danych mogli monitorować eksperymenty w bardziej wydajny sposób. Można zapoznać się z następującymi funkcjami:
+ Zaeksperymentowanie metadanych w celu łatwego filtrowania i sortowania listy eksperymentów
+ Uproszczone i wydajne strony szczegółów eksperymentów, które umożliwiają wizualizowanie i porównywanie przebiegów
+ Nowy projekt do uruchamiania stron szczegółów, aby zrozumieć i monitorować przebiegi szkoleniowe

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.65

  + **Nowe funkcje**
    + Dodano środowiska nadzorowane. Te środowiska zostały wstępnie skonfigurowane z bibliotekami dla typowych zadań uczenia maszynowego i zostały wstępnie skompilowane i zbuforowane jako obrazy platformy Docker w celu przyspieszenia wykonania. Są one domyślnie wyświetlane na liście środowisk obszaru roboczego z prefiksem "Azure".
    + Dodano środowiska nadzorowane. Te środowiska zostały wstępnie skonfigurowane z bibliotekami dla typowych zadań uczenia maszynowego i zostały wstępnie skompilowane i zbuforowane jako obrazy platformy Docker w celu przyspieszenia wykonania. Są one domyślnie wyświetlane na liście środowisk [obszaru roboczego](/python/api/azureml-core/azureml.core.workspace%28class%29)z prefiksem "Azure".

  + **Uczenie maszynowe — automl**
  + **[Uczenie maszynowe — automl](/python/api/azureml-train-automl-runtime/)**
    + Dodano obsługę konwersji ONNX dla ADB i HDI

+ **Funkcje w wersji zapoznawczej**
  + **Uczenie maszynowe — automl**
  + **[Uczenie maszynowe — automl](/python/api/azureml-train-automl-runtime/)**
    + Obsługiwane BERT i BiLSTM jako featurized tekstu (tylko wersja zapoznawcza)
    + Obsługiwane dostosowanie cechowania dla parametrów przeznaczenie kolumny i transformator (tylko wersja zapoznawcza)
    + Obsługiwane nieprzetworzone wyjaśnienia, gdy użytkownik włącza wyjaśnienie modelu podczas uczenia (tylko wersja zapoznawcza)
    + Dodano PROPHET do `timeseries` prognozowania jako potok do uczenia (tylko wersja zapoznawcza)

  + **azureml-contrib-datadrift**
    + Pakiety przechowywane z usługi Azure contrib-datadryfing do platformy Azure — datadryf; `contrib` pakiet zostanie usunięty w przyszłej wersji

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Wprowadzono FeaturizationConfig do AutoMLConfig i AutoMLBaseSettings
    + Wprowadzono FeaturizationConfig do [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) i AutoMLBaseSettings
      + Przesłoń cel kolumny dla cechowania z podaną kolumną i typem funkcji
      + Przesłoń parametry transformatora
    + Dodano komunikat o zaniechaniu dla explain_model () i retrieve_model_explanations ()
    + Dodano Prophet jako potok do uczenia (tylko wersja zapoznawcza)
    + Dodano komunikat o zaniechaniu dla explain_model () i retrieve_model_explanations ().
    + Dodano Prophet jako potok do uczenia (tylko wersja zapoznawcza).
    + Dodano obsługę automatycznego wykrywania spowolnienia docelowego, rozmiaru okna stopniowego i maksymalnego zakresu. Jeśli jedna z target_lags, target_rolling_window_size lub max_horizon jest ustawiona na wartość "Auto", heurystyka zostanie zastosowana do oszacowania wartości odpowiadającego parametru na podstawie danych szkoleniowych.
    + Stałe prognozowanie w przypadku, gdy zestaw danych zawiera jedną kolumnę ziarna, ten ziarno jest typu liczbowego i istnieje przerwy między zestawem pociągiem i testem
    + Rozwiązano komunikat o błędzie dotyczący zduplikowanego indeksu w trakcie wykonywania zadań prognozowania
    + Stałe prognozowanie w przypadku, gdy zestaw danych zawiera jedną kolumnę ziarna, ten ziarno jest typu liczbowego i istnieje przerwy między zestawem pociągiem i testowym.
    + Rozwiązano komunikat o błędzie dotyczący zduplikowanego indeksu w przebiegu zdalnym podczas prognozowania zadań.
    + Dodano Guardrail, aby sprawdzić, czy zestaw danych jest niezrównoważony. Jeśli tak jest, w konsoli zostanie zapisany komunikat Guardrail.
  + **azureml-core**
    + Dodano możliwość pobierania adresu URL sygnatury dostępu współdzielonego do modelu w magazynie za pomocą obiektu model. Np.: model.get_sas_url ()
    + Wprowadź `run.get_details()['datasets']` , aby uzyskać zestawy danych skojarzone z przesłanym przebiegiem
    + Dodaj interfejs API `Dataset.Tabular.from_json_lines_files` , aby utworzyć TabularDataset z plików wierszy JSON. Aby dowiedzieć się więcej o tych danych tabelarycznych w plikach wierszy JSON w TabularDataset, odwiedź [ten artykuł](how-to-create-register-datasets.md) w celu uzyskania dokumentacji.
    + Dodano dodatkowe pola rozmiaru maszyny wirtualnej (dysk systemu operacyjnego, liczba procesorów GPU) do funkcji supported_vmsizes ()
    + Dodano dodatkowe pola do funkcji list_nodes (), aby pokazać przebieg, prywatny i publiczny adres IP, port itd.
    + Możliwość określenia nowego pola podczas aprowizacji klastra — remotelogin_port_public_access, które można ustawić jako włączone lub wyłączone w zależności od tego, czy port SSH ma pozostać otwarty, czy zamknięty w momencie tworzenia klastra. Jeśli nie określisz go, usługa będzie inteligentnie otwierać lub zamykać port w zależności od tego, czy klaster jest wdrażany w sieci wirtualnej.
  + **Uczenie maszynowe — wyjaśnienie modelu**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + Dodano możliwość pobierania adresu URL sygnatury dostępu współdzielonego do modelu w magazynie za pomocą obiektu model. Np.: model. [get_sas_url ()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Wprowadź przebieg. [get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)["zestawy danych"], aby pobrać zestawy danych skojarzone z przesłanym przebiegiem
    + Dodaj interfejs API `Dataset.Tabular` .[ from_json_lines_files ()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) w celu utworzenia TabularDataset z plików wierszy JSON. Aby dowiedzieć się więcej o tych danych tabelarycznych w plikach wierszy JSON w TabularDataset, odwiedź stronę https://aka.ms/azureml-data w celu uzyskania dokumentacji.
    + Dodano dodatkowe pola rozmiaru maszyny wirtualnej (dysk systemu operacyjnego, liczba procesorów GPU) do funkcji [supported_vmsizes ()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Dodano dodatkowe pola do funkcji [list_nodes ()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) , aby pokazać przebieg, prywatny i publiczny adres IP, port itd.
    + Możliwość określenia nowego pola podczas [aprowizacji](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)  klastra, które można ustawić jako włączone lub wyłączone w zależności od tego, czy port SSH ma pozostać otwarty, czy zamknięty w momencie tworzenia klastra. Jeśli nie określisz go, usługa będzie inteligentnie otwierać lub zamykać port w zależności od tego, czy klaster jest wdrażany w sieci wirtualnej.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Ulepszona dokumentacja dotycząca wyników wyjaśnień w scenariuszu klasyfikacji.
    + Dodano możliwość przekazania przewidywanych wartości y w wyjaśnieniu dla przykładowych ocen. Odblokowuje więcej przydatnych wizualizacji.
    + Dodano Właściwość wyjaśniającej do MimicWrapper, aby umożliwić pobieranie bazowego MimicExplaineru.
  + **azureml-pipeline-core**
    + Dodano Notes do opisywania modułów, ModuleVersion i ModuleStep
  + **azureml-pipeline-steps**
    + Dodano RScriptStep do obsługi skryptu języka R za pośrednictwem potoku AML.
    + Stałe analizowanie parametrów metadanych w AzureBatchStep, które spowodowały, że komunikat o błędzie "przypisanie dla identyfikatora subskrypcji parametru nie jest określony".
  + **Uczenie maszynowe — automl**
    + Obsługiwane training_data, validation_data, label_column_name weight_column_name jako format danych wejściowych
    + Dodano komunikat o zaniechaniu dla explain_model () i retrieve_model_explanations ()
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Dodano [Notes](https://aka.ms/pl-modulestep) do opisywania [modułów](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModuleVersion i [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep).
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + Dodano [RScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) do obsługi skryptu języka R za pośrednictwem potoku AML.
    + Stałe analizowanie parametrów metadanych w [AzureBatchStep, które spowodowały komunikat o błędzie "przypisanie dla identyfikatora subskrypcji parametru nie jest określone".
  + **[Uczenie maszynowe — automl](/python/api/azureml-train-automl-runtime/)**
    + Obsługiwane training_data, validation_data, label_column_name weight_column_name jako format danych wejściowych.
    + Dodano komunikat o zaniechaniu dla [explain_model ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) i [retrieve_model_explanations ()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.62

+ **Nowe funkcje**
  + Wprowadza `timeseries`  cechę TabularDataset. Ta cecha umożliwia łatwe filtrowanie znaczników czasu dla danych TabularDataset, takich jak pobieranie wszystkich danych między zakresem czasu a najnowszymi danymi.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb przykład notesu.
  + Włączono szkolenia z TabularDataset i FileDataset. 

  + **azureml-train-core**
      + Dodano `Nccl` i `Gloo` obsłudze w PyTorch szacowania

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Zaniechano ustawienia AutoML "lag_length" i LaggingTransformer.
    + Naprawiono poprawną weryfikację danych wejściowych, jeśli są one określone w formacie przepływu danych
    + Zmodyfikowano fit_pipeline. PR, aby wygenerować kod JSON grafu i przekazać go do artefaktów.
    + Renderowanie wykresu w obszarze `userrun` using `Cytoscape` .
  + **azureml-core**
    + Oglądany obsługę wyjątków w kodzie ADB i Wprowadzaj zmiany zgodnie z nową obsługą błędów
    + Dodano automatyczne uwierzytelnianie MSI dla maszyn wirtualnych notesu.
    + Naprawia usterkę, w przypadku której można przekazać uszkodzone lub puste modele z powodu nieudanych ponownych prób.
    + Naprawiono usterkę, której `DataReference` Nazwa zmienia się w przypadku `DataReference` zmiany trybu (na przykład podczas wywoływania `as_upload` , `as_download` lub `as_mount` ).
    + Wprowadź `mount_point` i `target_path` Opcjonalnie dla `FileDataset.mount` i `FileDataset.download` .
    + Wyjątek, że nie można odnaleźć kolumny sygnatur czasowych, zostanie zgłoszony, jeśli interfejs API związany z połączeniami szeregowymi jest wywoływany bez przypisanej kolumny sygnatury czasowej lub są usuwane przypisane kolumny sygnatur czasowych.
    + Kolumny szeregów czasowych powinny być przypisane do kolumny, której typem jest Date, w przeciwnym razie oczekiwany jest wyjątek
    + Kolumny szeregów czasowych przypisujące interfejs API "with_timestamp_columns" może przyjmować nierówną wartość o wartości none/Gruba kolumna znaczników czasu, co spowoduje wyczyszczenie wcześniej przypisanych kolumn sygnatur czasowych.
    + Wyjątek zostanie wygenerowany, gdy kolumna Gruba lub szczegółowa sygnatura czasowa zostanie porzucona z oznaczeniem użytkownika, który może zostać wykonany po wykluczeniu kolumny sygnatury czasowej lub wywoływać with_time_stamp bez wartości none do kolumn zwolnienia sygnatury czasowej
    + Wyjątek zostanie wygenerowany, gdy kolumna bardzo gruba lub szczegółowa sygnatura czasowa nie zostanie uwzględniona w liście Zachowaj kolumny z oznaczeniem dla użytkownika, który będzie można wykonać po dołączeniu kolumny sygnatur czasowych w polu Zachowaj listę kolumn lub wywoływać with_time_stamp bez wartości none do kolumn Release timestamp.
    + Dodano rejestrowanie dla rozmiaru zarejestrowanego modelu.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Naprawiono ostrzeżenie w konsoli, gdy pakiet języka Python "pakowanie" nie jest zainstalowany: "Używanie starszej niż obsługiwanej wersji programu lightgbm, Uaktualnij do wersji nowszej niż 2.2.1"
    + Stałe wyjaśnienie modelu pobierania z fragmentowania dla globalnych wyjaśnień z wieloma funkcjami
    + Brak przykładów inicjalizacji w wyjaśnieniu danych wyjściowych dla nierozwiązanego śladu
    + Naprawiono niezmienny błąd na właściwościach ustawionych podczas przekazywania z wyjaśnieniem klienta przy użyciu dwóch różnych typów modeli
    + Dodano get_raw PARAM do programu do oceny objaśnień. Wyjaśnij (), dzięki czemu jeden z objaśnień oceniających może zwrócić zarówno wartości przetworzone, jak i pierwotne.
  + **Uczenie maszynowe — automl**
    + Wprowadzono publiczne interfejsy API z AutoML w celu obsługi wyjaśnień z `automl` zestawu SDK — nowszego sposobu obsługi wyjaśnień AutoML przez oddzielenie AutoML cechowania i wyjaśnienie zestawu SDK zintegrowanego z niestandardowym objaśnieniem z usługi Azure.
    + Usuwanie z usługi Azure Defaults z zdalnych środowisk szkoleniowych.
    + Zmieniono domyślną lokalizację magazynu pamięci podręcznej z plik filecachestore na jeden, aby AzureFileCacheStore jeden dla AutoML na Azure Databricks ścieżce kodu.
    + Naprawiono poprawną weryfikację danych wejściowych, jeśli są one określone w formacie przepływu danych
  + **azureml-train-core**
    + Przywrócono source_directory_data_store przestarzałe.
    + Dodano możliwość przesłonięcia wersji pakietu z zainstalowanym usługą Azure.
    + Dodano obsługę pliku dockerfile w `environment_definition` parametrze w szacowania.
    + Uproszczone dystrybuowanie parametrów szkoleniowych w szacowania.

         ```py
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nowe środowisko sieci Web (wersja zapoznawcza) dla Azure Machine Learning obszarów roboczych
Nowe środowisko internetowe umożliwia analitykom danych i inżynierom danych ukończenie kompleksowych cykli życia uczenia maszynowego z przygotowywanie i wizualizowania danych w celu uczenia i wdrażania modeli w jednej lokalizacji.

![Interfejs użytkownika obszaru roboczego Azure Machine Learning (wersja zapoznawcza)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Najważniejsze funkcje:**

Korzystając z tego nowego interfejsu Azure Machine Learning, możesz teraz:
+ Zarządzanie notesami lub łączenie się z usługą Jupyter
+ [Uruchamianie zautomatyzowanych eksperymentów ML](tutorial-first-experiment-automated-ml.md)
+ [Tworzenie zestawów danych z lokalnych plików, magazynów elementów, & plików sieci Web](how-to-create-register-datasets.md)
+ Eksplorowanie & Przygotowanie zestawów danych do tworzenia modelu
+ Monitoruj dryfowanie danych dla modeli
+ Wyświetlanie ostatnich zasobów z pulpitu nawigacyjnego

W tej wersji obsługiwane są następujące przeglądarki: Chrome, Firefox, Safari i Microsoft Edge Preview.

**Znane problemy:**

1. Odśwież przeglądarkę, Jeśli zobaczysz komunikat "coś poszło źle! Wystąpił błąd podczas ładowania plików fragmentu, gdy wdrożenie jest w toku.

1. Nie można usunąć pliku ani zmienić jego nazwy w notesach i plikach. W publicznej wersji zapoznawczej można użyć interfejsu użytkownika Jupyter lub terminalu w maszynie wirtualnej notesu do wykonywania operacji aktualizacji plików. Ze względu na to, że jest to zainstalowany system plików sieciowych, wszystkie zmiany wprowadzone w notesie maszyn wirtualnych zostaną natychmiast odzwierciedlone w obszarze roboczym Notes.

1. Aby SSH do maszyny wirtualnej notesu:
   1. Znajdź klucze SSH, które zostały utworzone podczas konfiguracji maszyny wirtualnej. Lub Znajdź klucze w obszarze roboczym Azure Machine Learning > Otwórz kartę obliczenia > zlokalizuj maszynę wirtualną Notes na liście > Otwórz jej właściwości: Skopiuj klucze z okna dialogowego.
   1. Zaimportuj te publiczne i prywatne klucze SSH do komputera lokalnego.
   1. Używaj ich do używania protokołu SSH w notesie maszyn wirtualnych.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.60

+ **Nowe funkcje**
  + Wprowadzono FileDataset, która odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Pliki mogą być w dowolnym formacie. FileDataset umożliwia pobieranie lub Instalowanie plików na potrzeby obliczeń. 
  + Dodano obsługę YAML potoku dla kroku PythonScript, krok adla, krok, etap, DataTransferStep i AzureBatch kroku

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + AutoArima jest teraz pożądanym potokiem tylko dla wersji zapoznawczej.
    + Ulepszone raportowanie błędów w przypadku prognozowania.
    + Ulepszone rejestrowanie przy użyciu wyjątków niestandardowych zamiast ogólnych w zadaniach prognozowania.
    + Usunięto sprawdzenie max_concurrent_iterations, aby było mniejsze niż łączna liczba iteracji.
    + Modele AutoML teraz zwracają AutoMLExceptions
    + Ta wersja zwiększa wydajność wykonywania zautomatyzowanych uruchomień lokalnych w usłudze Machine Learning.
  + **azureml-core**
    + Wprowadź Dataset.get_all (obszar roboczy), który zwraca słownik i obiekty podkluczy `TabularDataset` `FileDataset` według nazwy rejestracji.

    ```py
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Wprowadź `parition_format` jako argument do `Dataset.Tabular.from_delimited_files` i `Dataset.Tabular.from_parquet.files` . Informacje o partycji każdej ścieżki danych zostaną wyodrębnione do kolumn w oparciu o określony format. "{column_name}" tworzy kolumnę String, a "{column_name: RRRR/MM/DD/HH/mm/SS}" tworzy kolumnę datetime, gdzie "RRRR", "MM", "DD", "gg", "mm" i "SS" są używane do wyodrębniania wartości typu Year, month, Day, Hour, minute i Second. Partition_format powinna rozpoczynać się od pozycji pierwszego klucza partycji aż do końca ścieżki pliku. Na przykład dana ścieżka ".. /USA/2019/01/01/data.csv "gdzie partycja jest według kraju i godziny, partition_format ="/{Country}/{PartitionDate: RRRR/MM/DD}/data.csv "tworzy kolumnę ciągu" Country "z wartością" USA "i kolumną DateTime" PartitionDate "o wartości" 2019-01-01 ".
        ```py
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Wprowadź `partition_format` jako argument do `Dataset.Tabular.from_delimited_files` i `Dataset.Tabular.from_parquet.files` . Informacje o partycji każdej ścieżki danych zostaną wyodrębnione do kolumn w oparciu o określony format. "{column_name}" tworzy kolumnę String, a "{column_name: RRRR/MM/DD/HH/mm/SS}" tworzy kolumnę datetime, gdzie "RRRR", "MM", "DD", "gg", "mm" i "SS" są używane do wyodrębniania wartości typu Year, month, Day, Hour, minute i Second. Partition_format powinna rozpoczynać się od pozycji pierwszego klucza partycji aż do końca ścieżki pliku. Na przykład dana ścieżka ".. /USA/2019/01/01/data.csv "gdzie partycja jest według kraju i godziny, partition_format ="/{Country}/{PartitionDate: RRRR/MM/DD}/data.csv "tworzy kolumnę ciągu" Country "z wartością" USA "i kolumną DateTime" PartitionDate "o wartości" 2019-01-01 ".
    + `to_csv_files` metody i zostały `to_parquet_files` dodane do `TabularDataset` . Metody te umożliwiają konwersję między `TabularDataset` a i a `FileDataset` przez konwersję danych do plików o określonym formacie.
    + Podczas zapisywania pliku dockerfile wygenerowanego przez model () automatycznie loguj się do podstawowego rejestru obrazu.
    + "gpu_support" nie jest już konieczne; AML teraz automatycznie wykrywa i używa rozszerzenia platformy Docker firmy NVIDIA, gdy jest dostępne. Zostanie usunięta w przyszłej wersji.
    + Dodano obsługę tworzenia, aktualizowania i używania PipelineDrafts.
    + Ta wersja zwiększa wydajność wykonywania zautomatyzowanych uruchomień lokalnych w usłudze Machine Learning.
    + Użytkownicy mogą badać metryki z historii uruchamiania według nazwy.
    + Ulepszone rejestrowanie przy użyciu wyjątków niestandardowych zamiast ogólnych w zadaniach prognozowania.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Dodano parametr feature_maps do nowego MimicWrapper, dzięki czemu użytkownicy mogą uzyskiwać nieprzetworzone wyjaśnienia funkcji.
    + Przekazywanie zestawu danych jest teraz domyślnie wyłączone w celu przekazania wyjaśnień i można je ponownie włączyć przy użyciu upload_datasets = true
    + Dodano parametry filtrowania "is_law" do listy wyjaśnień i funkcji pobierania.
    + Dodaje metodę `get_raw_explanation(feature_maps)` do globalnych i lokalnych obiektów wyjaśnień.
    + Dodano Sprawdzanie wersji do lightgbm z wydrukowanym ostrzeżeniem, jeśli poniżej obsługiwanej wersji
    + Zoptymalizowane użycie pamięci podczas tworzenia wsadowych objaśnień
    + Modele AutoML teraz zwracają AutoMLExceptions
  + **azureml-pipeline-core**
    + Dodano obsługę tworzenia, aktualizowania i używania PipelineDrafts — może służyć do obsługi definicji potoku mutable i używania ich interaktywnie do uruchamiania
  + **Uczenie maszynowe — automl**
    + Utworzono funkcję w celu zainstalowania określonych wersji procesora GPU pytorch v 1.1.0, :::no-loc text="cuda"::: zestawu narzędzi 9,0, pytorch-transformatorów, które są wymagane do włączenia Bert/XLNet w środowisku uruchomieniowym języka Python.
  + **azureml-train-core**
    + Wczesna awaria niektórych z parametrów w zestawie SDK, a nie po stronie serwera.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning zestawu SDK 1.1.14 przygotowywania danych
+ **Poprawki i ulepszenia błędów**
  + Włączono funkcję zapisu do ADLS/ADLSGen2 przy użyciu nieprzetworzonej ścieżki i poświadczeń.
  + Naprawiono usterkę, która spowodowała, że `include_path=True` nie zadziałała `read_parquet` .
  + Naprawiono `to_pandas_dataframe()` błąd spowodowany przez wyjątek "Nieprawidłowa wartość właściwości: hostSecret".
  + Naprawiono usterkę polegającą na tym, że nie można odczytać plików na DBFS w trybie Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.57
+ **Nowe funkcje**
  + Włączone `TabularDataset` do użycia przez AutomatedML. Aby dowiedzieć się więcej `TabularDataset` , odwiedź stronę https://aka.ms/azureml/howto/createdatasets .

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Teraz można zaktualizować certyfikat TLS/SSL dla punktu końcowego oceniania wdrożonego w klastrze AKS zarówno dla dostawcy wygenerowanego przez firmę Microsoft, jak i dla certyfikatu klienta.
  + **azureml-automl-core**
    + Rozwiązano problem w AutoML, że wiersze z brakującymi etykietami nie zostały prawidłowo usunięte.
    + Ulepszono rejestrowanie błędów w AutoML; Pełne komunikaty o błędach będą teraz zawsze zapisywane w pliku dziennika.
    + AutoML zaktualizował Przypinanie pakietu do dołączenia `azureml-defaults` , `azureml-explain-model` , i `azureml-dataprep` . AutoML nie będzie już ostrzegał o niezgodności pakietów (z wyjątkiem `azureml-train-automl` pakietu).
    + Rozwiązano problem polegający na tym, że `timeseries`  podziały OKS mają nierówne rozmiary powodujące niepowodzenie obliczeń w pojemniku.
    + Podczas wykonywania iteracji zestawu danych dla typu szkolenia z weryfikacją krzyżową, jeśli zakończył się problemami z pobraniem modeli przeszkolonych na całym DataSet, mamy niespójność między modelami wag i modelami, które były karmione w zbiorze głosu.
    + Rozwiązano błąd wywoływany, gdy szkolenia i/lub etykiety sprawdzania poprawności (y i y_valid) są podane w postaci Pandas Dataframe, ale nie jako tablica numpy.
    + Rozwiązano problem z zadaniami prognozowania, gdy żaden z nich nie został napotkany w kolumnach logicznych tabel wejściowych.
    + Zezwól użytkownikom AutoML na porzucanie serii szkoleniowych, które nie są wystarczająco długie podczas prognozowania. -Zezwól użytkownikom AutoML na porzucanie ziaren z zestawu testów, który nie istnieje w zestawie szkoleniowym podczas prognozowania.
  + **azureml-core**
    + Rozwiązano problem z kolejnością parametrów blob_cache_timeout.
    + Dodano zewnętrzne Typy wyjątków dopasowania i transformacji do błędów systemowych.
    + Dodano obsługę Key Vault wpisów tajnych dla zdalnych uruchomień. Dodaj magazyn usługi Azure. Core.. kluczy, aby dodać, pobrać i wyświetlić wpisy tajne z magazynu kluczy skojarzonego z Twoim obszarem roboczym. Obsługiwane są następujące operacje:
      + azureml.core.workspace.Workspace.get_default_keyvault ()
      + azureml.core.keyvault.Keyvault.set_secret (nazwa, wartość)
      + azureml.core.keyvault.Keyvault.set_secrets (secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret (nazwa)
      + azureml.core.keyvault.Keyvault.get_secrets (secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets ()
    + Dodatkowe metody uzyskiwania domyślnego magazynu kluczy i uzyskiwania wpisów tajnych podczas zdalnego uruchomienia:
      + azureml.core.workspace.Workspace.get_default_keyvault ()
      + azureml.core.run.Run.get_secret (nazwa)
      + azureml.core.run.Run.get_secrets (secrets_list)
    + Dodano dodatkowe parametry przesłonięcia w celu przesłania polecenia CLI.
    + Zwiększ niezawodność wywołań interfejsu API, aby rozszerzać ponowną próbę na typowe wyjątki biblioteki żądań.
    + Dodano obsługę przesyłania przebiegów z przesłanego przebiegu.
    + Rozwiązano problem związany z wygaśnięciem tokenu sygnatury dostępu współdzielonego w FileWatcher, co spowodowało zatrzymanie przekazywania plików po wygaśnięciu tokenu początkowego.
    + Obsługiwane Importowanie plików CSV/TSV protokołu HTTP w zestawie danych Python SDK.
    + Zaniechano metody Workspace. Setup (). Komunikat ostrzegawczy pokazywany użytkownikom sugeruje użycie polecenia Create () lub Get ()/from_config ().
    + Dodano Environment.add_private_pip_wheel (), co umożliwia przekazywanie prywatnych niestandardowych pakietów języka Python `whl` do obszaru roboczego i bezpieczne używanie ich do kompilowania/zmaterializowania środowiska.
    + Teraz można zaktualizować certyfikat TLS/SSL dla punktu końcowego oceniania wdrożonego w klastrze AKS zarówno dla dostawcy wygenerowanego przez firmę Microsoft, jak i dla certyfikatu klienta.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Dodano parametr służący do dodawania identyfikatora modelu do wyjaśnień podczas przekazywania.
    + Dodano `is_raw` znakowanie do wyjaśnień w pamięci i przekazywanie.
    + Dodano obsługę pytorch i testy dla pakietu Azure-Wyjaśnij model.
  + **Azure — opendatasets**
    + Obsługa wykrywania i rejestrowania środowiska testowego.
    + Dodano klasy w celu uzyskania wypełniania do USA według powiatów i zip.
  + **azureml-pipeline-core**
    + Dodano Właściwość Label do definicji portów wejściowych i wyjściowych.
  + **azureml-telemetry**
    + Naprawiono niepoprawną konfigurację telemetrii.
  + **Uczenie maszynowe — automl**
    + Naprawiono usterkę dotyczącą niepowodzenia instalacji, błąd nie był rejestrowany w polu "błędy" dla uruchomienia Instalatora i dlatego nie został on zapisany w nadrzędnym przebiegu "Errors".
    + Rozwiązano problem w AutoML, że wiersze z brakującymi etykietami nie zostały prawidłowo usunięte.
    + Zezwól użytkownikom AutoML na porzucanie serii szkoleniowych, które nie są wystarczająco długie podczas prognozowania.
    + Zezwalaj użytkownikom AutoML na upuszczanie ziaren z zestawu testów, który nie istnieje w zestawie szkoleniowym podczas prognozowania.
    + Teraz AutoMLStep przechodzi przez `automl` konfigurację do zaplecza, aby uniknąć problemów ze zmianami lub dodatkami nowych parametrów konfiguracji.
    + AutoML Data Guardrail jest teraz w publicznej wersji zapoznawczej. Użytkownik zobaczy raport Guardrail danych (dla zadań klasyfikacji/regresji) po szkoleniu, a także będzie mógł uzyskać do niego dostęp za pomocą interfejsu API zestawu SDK.
  + **azureml-train-core**
    + Dodano obsługę Torch 1,2 w PyTorch szacowania.
  + **azureml-widgets**
    + Udoskonalone wykresy macierzowe z błędami na potrzeby szkoleń klasyfikacji.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning zestawu SDK 1.1.12 przygotowywania danych
+ **Nowe funkcje**
  + Listy ciągów mogą teraz być przesyłane jako dane wejściowe do `read_*` metod.

+ **Poprawki i ulepszenia błędów**
  + `read_parquet`Ulepszono wydajność programu w przypadku uruchamiania programu Spark.
  + Rozwiązano problem polegający na tym, że `column_type_builder` w przypadku pojedynczej kolumny z niejednoznacznymi formatami daty Wystąpił błąd.

### <a name="azure-portal"></a>Witryna Azure Portal
+ **Funkcja wersji zapoznawczej**
  + Przesyłanie strumieniowe plików dziennika i danych wyjściowych jest teraz dostępne dla stron szczegółów uruchamiania. Po włączeniu przełącznika podglądu pliki będą przesyłać strumieniowo aktualizacje w czasie rzeczywistym.
  + Możliwość ustawiania limitu przydziału na poziomie obszaru roboczego jest publikowana w wersji zapoznawczej. Przydziały AmlCompute są przydzielane na poziomie subskrypcji, ale teraz można rozpowszechnić ten przydział między obszarami roboczymi i przydzielić go do sprawiedliwego udostępniania i zarządzania. Po prostu kliknij blok **użycie i limity przydziału** na lewym pasku nawigacyjnym obszaru roboczego i wybierz kartę **Konfigurowanie przydziałów** . Musisz być administratorem subskrypcji, aby móc ustawiać przydziały na poziomie obszaru roboczego, ponieważ jest to operacja między obszarami roboczymi.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.55

+ **Nowe funkcje**
  + Uwierzytelnianie oparte na tokenach jest teraz obsługiwane dla wywołań punktu końcowego oceniania wdrożonego w AKS. Będziemy nadal obsługiwać bieżące uwierzytelnianie oparte na kluczach, a użytkownicy mogą korzystać z jednego z tych mechanizmów uwierzytelniania jednocześnie.
  + Możliwość zarejestrowania magazynu obiektów BLOB znajdującego się za siecią wirtualną (VNet) jako magazynem danych.

+ **Poprawki i ulepszenia błędów**
  + **azureml-automl-core**
    + Naprawia usterkę, w której rozmiar walidacji podziałów OKS jest mały i powoduje nieprawidłowe wykresy przewidywane a rzeczywiste dla regresji i prognozowania.
    + Rejestrowanie zadań prognozowania w trakcie zdalnego działania zostało ulepszone, teraz użytkownik jest dostarczany z kompleksowym komunikatem o błędzie, Jeśli uruchomienie nie powiodło się.
    + Naprawione błędy w `Timeseries` przypadku, gdy flaga preprocesora ma wartość true.
    + Niektóre komunikaty o błędach sprawdzania poprawności danych prognozowania są bardziej funkcjonalne.
    + Zmniejszone użycie pamięci przez AutoML jest uruchamiane przez porzucanie i/lub opóźnione ładowanie zestawów danych, w szczególności między zduplikowanymi procesami
  + **Azure-contrib-opis-model**
    + Dodano flagę model_task do objaśnień, aby umożliwić użytkownikowi przesłonięcie domyślnej automatycznej logiki wnioskowania dla typu modelu
    + Zmiany widżetu: program jest automatycznie instalowany przy użyciu programu `contrib` , nie ma więcej `nbextension` wyjaśnień dotyczących instalacji/włączania-obsługi z globalną ważnością funkcji (na przykład Permutative)
    + Zmiany w pulpicie nawigacyjnym: — Skrzynka i wykresy Violin poza `beeswarm` wykresem na stronie podsumowania — znacznie szybsze ponowne renderowanie `beeswarm` wykresu w przypadku zmiany suwaka "góra-k" — przydatny komunikat wyjaśniający, jak najwyższe-k jest obliczane — przydatne komunikaty w miejscu na wykresach, gdy dane nie zostały dostarczone
  + **azureml-core**
    + Dodano metodę model. Package () w celu utworzenia obrazów platformy Docker i wieloetapowe dockerfile, które hermetyzują modele i ich zależności.
    + Zaktualizowano lokalne usługi WebService, aby akceptować InferenceConfigs zawierające obiekty środowiska.
    + Naprawiono model. Register () podczas tworzenia nieprawidłowych modeli, gdy "." (dla bieżącego katalogu) jest przenoszona jako parametr model_path.
    + Dodawanie Run.submit_child, funkcja powoduje odbicie eksperymentu. Prześlij podczas określania uruchomienia jako elementu nadrzędnego przesłanego przebiegu podrzędnego.
    + Obsługa opcji konfiguracji z metody model. Register w Run.register_model.
    + Możliwość uruchamiania zadań JAR w istniejącym klastrze.
    + Teraz obsługiwane instance_pool_id i cluster_log_dbfs_path parametrów.
    + Dodano obsługę używania obiektu środowiska podczas wdrażania modelu w usłudze sieci Web. Obiekt środowiska można teraz dostarczyć jako część obiektu InferenceConfig.
    + Dodawanie mapowania appinsifht dla nowych regionów — środkowe i zachodnie-northcentralus
    + Dodano dokumentację dla wszystkich atrybutów we wszystkich klasach magazynu danych.
    + Dodano parametr blob_cache_timeout do `Datastore.register_azure_blob_container` .
    + Dodano metody save_to_directory i load_from_directory do programu Azure. Core. Environment. Environment.
    + Dodano polecenia "AZ ml Download Environment" i "polecenie" AZ ml Environment Register "do interfejsu CLI.
    + Dodano metodę Environment.add_private_pip_wheel.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Dodano śledzenie zestawu danych do wyjaśnień za pomocą usługi DataSet (wersja zapoznawcza).
    + Zmniejszono domyślny rozmiar wsadu podczas przesyłania globalnych wyjaśnień z 10 000 do 100.
    + Dodano flagę model_task do objaśnień, aby umożliwić użytkownikowi przesłonięcie domyślnej automatycznej logiki wnioskowania dla typu modelu.
  + **azureml-mlflow**
    + Naprawiono usterkę w mlflow.azureml.build_image, w której zagnieżdżone katalogi są ignorowane.
  + **azureml-pipeline-steps**
    + Dodano możliwość uruchamiania zadań JAR w istniejącym klastrze Azure Databricks.
    + Dodano instance_pool_id obsługi i cluster_log_dbfs_path parametrów dla kroku DatabricksStep.
    + Dodano obsługę parametrów potoku w kroku DatabricksStep.
  + **Uczenie maszynowe — automl**
    + Dodano `docstrings` dla plików związanych z kompletem danych.
    + Zaktualizowane dokumenty do bardziej odpowiedniego języka dla `max_cores_per_iteration` i `max_concurrent_iterations`
    + Rejestrowanie zadań prognozowania w trakcie zdalnego działania zostało ulepszone, teraz użytkownik jest dostarczany z kompleksowym komunikatem o błędzie, Jeśli uruchomienie nie powiodło się.
    + Usunięto get_data z `automlstep` notesu potoku.
    + Uruchomiono obsługę `dataprep` w `automlstep` .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning zestawu SDK 1.1.10 przygotowywania danych

+ **Nowe funkcje**
  + Teraz możesz poprosić o wykonanie określonych inspektorów (na przykład histogramu, wykresu punktowego itp.) w określonych kolumnach.
  + Dodano argument zrównoleglanie do elementu `append_columns` . Jeśli wartość jest równa true, dane zostaną załadowane do pamięci, ale wykonywanie zostanie uruchomione równolegle; w przypadku wartości false wykonywanie będzie przesyłane strumieniowo, ale pojedyncze wątki.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.53

+ **Nowe funkcje**
  + Zautomatyzowane Machine Learning teraz obsługuje modele szkoleń ONNX na zdalnym miejscu docelowym obliczeń
  + Azure Machine Learning teraz zapewnia możliwość wznowienia szkolenia z poprzedniego przebiegu, punktów kontrolnych lub plików modeli.
    + Dowiedz się [, jak za pomocą usługi szacowania wznowić szkolenia z poprzedniego przebiegu](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Poprawki i ulepszenia błędów**
  + **Azure — interfejs wiersza polecenia**
    + Polecenia CLI "model Deploy" i "Aktualizacja usługi" teraz akceptują parametry, pliki konfiguracji lub kombinację dwóch. Parametry mają pierwszeństwo przed atrybutami w plikach.
    + Opis modelu można teraz zaktualizować po rejestracji
  + **azureml-automl-core**
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualna Najnowsza wersja).
    + Dodawanie obsługi potoków NimbusML szacowania &, które mają być używane w AutoML szacowania.
    + Naprawianie usterki w procedurze wyboru kompletu, która niepotrzebnie zwiększa wynikowe zestawy, nawet jeśli wyniki pozostawały stałe.
    + Umożliwia ponowne użycie niektórych featurizations w ramach podziałów OKS na potrzeby prognozowania zadań. Przyspiesza to Uruchamianie Instalatora przez przekroczenie współczynnika n_cross_validations dla kosztownych featurizations, takich jak spowolnienia i stopniowające okna.
    + Rozwiązywanie problemu, jeśli czas jest poza Pandas obsługiwanym zakresem czasu. Teraz możemy podnieść wartość DataException, jeśli czas jest krótszy niż PD. Sygnatura czasowa. minimalna lub większa od PD. Timestamp. Max
    + Prognozowanie umożliwia teraz różne częstotliwości w zestawach uczenia i testu, jeśli można je wyrównać. Na przykład "kwartalne rozpoczynające się w styczniu" i "co kwartał zaczynają się w październiku" można wyrównać.
    + Właściwość "Parameters" została dodana do TimeSeriesTransformer.
    + Usuń stare klasy wyjątków.
    + W przypadku zadań prognozowania `target_lags` parametr przyjmuje teraz jedną wartość całkowitą lub listę liczb całkowitych. Jeśli podano liczbę całkowitą, zostanie utworzone tylko jedno opóźnienie. W przypadku podanej listy zostaną wykonane unikatowe wartości spowolnienia. target_lags = [1, 2, 2, 4] utworzy spowolnienia jeden, dwa i cztery okresy.
    + Usuń usterkę dotyczącą utraty typów kolumn po przekształceniu (połączona usterka);
    + W `model.forecast(X, y_query)` , zezwalaj y_query być typem obiektu zawierającym brak (s) na początku (#459519).
    + Dodaj oczekiwane wartości do `automl` danych wyjściowych
  + **azureml-contrib-datadrift**
    +  Ulepszenia przykładowego notesu, w tym przełączanie do platformy Uczenie maszynowe opendatasets zamiast platformy Azure-contrib-opendatasets i wydajności podczas wzbogacania danych
  + **Azure-contrib-opis-model**
    + Stałe przekształcenia — argument dla objaśniania WAPNa dla wagi nieprzetworzonej funkcji w usłudze Azure contrib-Wyjaśnij pakiet modelu
    + Dodano segmentacji do wyjaśnień obrazu w opisie obrazu dla pakietu Azure-contrib-Wyjaśnij-model
    + Dodaj obsługę rozrzedzenia scipy dla LimeExplainer
    + Dodano `batch_size` do programu objaśniający, kiedy w `include_local=False` przypadku przesyłania globalnych wyjaśnień w partiach w celu poprawienia czasu wykonywania DecisionTreeExplainableModel
  + **Azure-contrib-featureengineering**
    + Poprawka do wywoływania set_featurizer_timeseries_params (): DICT zmiana typu wartości i sprawdzanie wartości null — Dodawanie notesu dla `timeseries`  featurized
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualna Najnowsza wersja).
  + **azureml-core**
    + Dodano możliwość dołączania magazynów danych DBFS w interfejsie wiersza polecenia Azure
    + Rozwiązano problem z przekazaniem magazynu danych, w którym zostanie utworzony pusty folder, jeśli `target_path` został uruchomiony z `/`
    + Rozwiązano `deepcopy` problem w ServicePrincipalAuthentication.
    + Do interfejsu wiersza polecenia dodano poleceń "AZ ml Environment show" i "AZ ml Environment list".
    + Środowiska obsługują teraz Określanie base_dockerfile jako alternatywy dla już skompilowanego base_image.
    + Nieużywane ustawienie RunConfiguration auto_prepare_environment zostało oznaczone jako przestarzałe.
    + Opis modelu można teraz zaktualizować po rejestracji
    + Poprawka: model i usuwanie obrazu teraz zawiera więcej informacji na temat pobierania obiektów nadrzędnych, które są zależne od nich, jeśli usuwanie nie powiedzie się z powodu zależności między strumieniami.
    + Rozwiązano problem polegający na tym, że wydrukowany pusty czas trwania wdrożeń występujących podczas tworzenia obszaru roboczego dla niektórych środowisk.
    + Ulepszone wyjątki błędów dla tworzenia obszaru roboczego. Użytkownicy nie zobaczą "nie można utworzyć obszaru roboczego. Nie można znaleźć... " jako komunikat i zamiast tego pojawia się rzeczywisty błąd tworzenia.
    + Dodano obsługę uwierzytelniania tokenów w AKS WebServices.
    + Dodaj `get_token()` metodę do `Webservice` obiektów.
    + Dodano obsługę interfejsu wiersza polecenia do zarządzania zestawami danych uczenia maszynowego.
    + `Datastore.register_azure_blob_container` teraz opcjonalnie przyjmuje `blob_cache_timeout` wartość (w sekundach), która umożliwia skonfigurowanie parametrów instalacji blobfuse w celu włączenia wygasania pamięci podręcznej dla tego magazynu danych. Wartość domyślna nie jest przekroczeniem limitu czasu, na przykład gdy obiekt BLOB jest odczytywany, pozostaje w lokalnej pamięci podręcznej do momentu zakończenia zadania. Większość zadań preferuje to ustawienie, ale niektóre zadania muszą odczytywać więcej danych z dużego zestawu danych, niż mieści się na ich węzłach. W przypadku tych zadań dostrajanie tego parametru pomoże się pomyślnie. Weź pod uwagę, gdy dostrajasz ten parametr: ustawienie zbyt małej wartości może spowodować spadek wydajności, ponieważ dane używane w epokach mogły wygasnąć przed ponownym użyciem. Wszystkie operacje odczytu będą wykonywane z magazynu obiektów BLOB/sieci, a nie lokalnej pamięci podręcznej, co ma negatywny wpływ na czasy szkoleniowe.
    + Opis modelu można teraz prawidłowo zaktualizować po rejestracji
    + Usuwanie modelu i obrazu zapewnia teraz więcej informacji na temat obiektów nadrzędnych, które są od nich zależne, co powoduje niepowodzenie usuwania
    + Zwiększ wykorzystanie zasobów przez zdalne uruchomienia za pomocą usługi Azure. mlflow.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Stałe przekształcenia — argument dla objaśniania WAPNa dla wagi nieprzetworzonej funkcji w usłudze Azure contrib-Wyjaśnij pakiet modelu
    + Dodaj obsługę rozrzedzenia scipy dla LimeExplainer
    + dodano otokę objaśnienia liniowego kształtu, a także inny poziom do tabelarycznego objaśnienia w celu wyjaśnienia modeli liniowych
    + Aby uzyskać wyjaśnienie śladu w bibliotece modeli wyjaśnień, Naprawiono błąd podczas include_local = false dla rozrzedzonych danych wejściowych
    + Dodaj oczekiwane wartości do `automl` danych wyjściowych
    + stała ważność funkcji permutacji podczas dostarczania argumentu Transformations w celu uzyskania ważności funkcji surowej
    + dodano `batch_size` do programu objaśniający, kiedy w `include_local=False` przypadku przesyłania globalnych wyjaśnień w partiach w celu poprawienia czasu wykonywania DecisionTreeExplainableModel
    + Aby zapoznać się z biblioteką wyjaśniającą model, należy naprawić blackboxe, gdzie dane wejściowe Pandas Dataframe są wymagane do przewidywania
    + Naprawiono usterkę, gdy `explanation.expected_values` czasami zwróci wartość zmiennoprzecinkową zamiast listy z elementem zmiennoprzecinkowym.
  + **azureml-mlflow**
    + Poprawianie wydajności mlflow.set_experiment (experiment_name)
    + Naprawianie usterki w korzystaniu z InteractiveLoginAuthentication dla mlflow tracking_uri
    + Zwiększ wykorzystanie zasobów przez zdalne uruchomienia za pomocą usługi Azure. mlflow.
    + Ulepszanie dokumentacji pakietu Azure-mlflow
    + Poprawka błędu, gdzie mlflow.log_artifacts ("my_dir") zapisze artefakty w obszarze "my_dir/<artefaktów>" zamiast "<ścieżek artefaktów>"
  + **Azure — opendatasets**
    + Przypnij `pyarrow` `opendatasets` do starych wersji (<0.14.0) z powodu problemu z pamięcią nowo wprowadzonego w tym miejscu.
    + Przenieś usługę Azure-contrib-opendatasets do programu Azure opendatasets.
    + Zezwalaj na rejestrację otwartych klas DataSet w obszarze roboczym Azure Machine Learning i bezproblemowo korzystaj z możliwości zestawu danych AML.
    + Znacznie Zwiększ wydajność wzbogacania NoaaIsdWeather w wersji innej niż SPARK.
  + **azureml-pipeline-steps**
    + Magazyn danych DBFS jest teraz obsługiwany w przypadku wejść i wyjść w DatabricksStep.
    + Zaktualizowana dokumentacja Azure Batch kroku w odniesieniu do danych wejściowych/wyjściowych.
    + W AzureBatchStep, zmieniono wartość domyślną *delete_batch_job_after_finish* na *true*.
  + **azureml-telemetry**
    +  Przenieś usługę Azure-contrib-opendatasets do programu Azure opendatasets.
    + Zezwalaj na rejestrację otwartych klas DataSet w obszarze roboczym Azure Machine Learning i bezproblemowo korzystaj z możliwości zestawu danych AML.
    + Znacznie Zwiększ wydajność wzbogacania NoaaIsdWeather w wersji innej niż SPARK.
  + **Uczenie maszynowe — automl**
    + Zaktualizowana dokumentacja dotycząca get_output odzwierciedla faktyczny zwracany typ i udostępnia dodatkowe uwagi dotyczące pobierania właściwości klucza.
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (aktualna Najnowsza wersja).
    + Dodaj oczekiwane wartości do `automl` danych wyjściowych
  + **azureml-train-core**
    + Ciągi są teraz akceptowane jako element docelowy obliczeń dla zautomatyzowanego dostrajania parametrów
    + Nieużywane ustawienie RunConfiguration auto_prepare_environment zostało oznaczone jako przestarzałe.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning zestawu SDK 1.1.9 przygotowywania danych

+ **Nowe funkcje**
  + Dodano obsługę odczytywania pliku bezpośrednio z adresu URL http lub https.

+ **Poprawki i ulepszenia błędów**
  + Ulepszony komunikat o błędzie podczas próby odczytania zestawu danych Parquet ze źródła zdalnego (co nie jest obecnie obsługiwane).
  + Rozwiązano błąd podczas zapisu w formacie pliku Parquet w ADLS Gen 2 i Zaktualizowano nazwę kontenera ADLS generacji 2 w ścieżce.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interfejs wizualizacji
+ **Funkcje w wersji zapoznawczej**
  + Dodano moduł "Wykonaj skrypt języka R" w interfejsie wizualizacji.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.48

+ **Nowe funkcje**
  + **Azure — opendatasets**
    + **Azure-contrib-opendatasets** jest teraz dostępny jako **Azure-opendatasets**. Stary pakiet może nadal korzystać z programu, ale zalecamy użycie usługi **Azure-opendatasets** do przodu w celu uzyskania bogatszych możliwości i ulepszeń.
    + Ten nowy pakiet umożliwia zarejestrowanie otwartych zestawów danych jako zestaw DataSet w obszarze roboczym Azure Machine Learning i wykorzystanie funkcji oferowanych przez zestaw danych.
    + Obejmuje ona również istniejące funkcje, takie jak zużywanie otwartych zestawów danych jako Pandas/SPARK dataframes, a także przyłączania do lokalizacji dla niektórych elementów DataSet, takich jak pogoda.

+ **Funkcje w wersji zapoznawczej**
    + HyperDriveConfig może teraz akceptować obiekt potoku jako parametr obsługujący dostrajanie parametrów przy użyciu potoku.

+ **Poprawki i ulepszenia błędów**
  + **Uczenie maszynowe — automl**
    + Rozwiązano błąd dotyczący utraty typów kolumn po transformacji.
    + Rozwiązano błąd zezwalający y_query być typem obiektu zawierającym brak (s) na początku.
    + Rozwiązano problem związany z procedurą wyboru kompletną, która niepotrzebnie zwiększa wynikowe zestawy, nawet jeśli wyniki pozostawały stałe.
    + Rozwiązano problem z opcją Zezwalaj list_models i blokuj ustawienia list_models w AutoMLStep.
    + Rozwiązano problem uniemożliwiający użycie przetwarzania wstępnego, gdy AutoML mógłby zostać użyty w kontekście potoków usługi Azure ML.
  + **Azure — opendatasets**
    + Przeniesiono usługę Azure-contrib-opendatasets do programu Azure opendatasets.
    + Dozwolone są otwarte klasy zestawu danych do zarejestrowania w obszarze roboczym Azure Machine Learning i bezproblemowo wykorzystują możliwości zestawu danych AML.
    + Znacznie Ulepszono NoaaIsdWeather wzbogacanie wydajności w wersji innej niż SPARK.
  + **Uczenie maszynowe — wyjaśnienie modelu**
    + Zaktualizowana dokumentacja online dla obiektów interpretera.
    + Dodano `batch_size` do programu wyjaśniającego `include_local=False` , gdy, w przypadku przesyłania globalnych wyjaśnień w partiach w celu poprawienia czasu wykonywania DecisionTreeExplainableModel dla biblioteki modelowania.
    + Rozwiązano problem polegający na tym, że `explanation.expected_values` czasami zwracają wartość zmiennoprzecinkową, a nie listę z zmiennoprzecinkową.
    + Dodano oczekiwane wartości do `automl` danych wyjściowych dla objaśnień śladowych w bibliotece modeli wyjaśnień.
    + Stała ważność funkcji permutacji, gdy podano argument Transformations w celu uzyskania ważności funkcji RAW.
  + **azureml-core**
    + Dodano możliwość dołączania magazynów danych DBFS w interfejsie wiersza polecenia Azure.
    + Rozwiązano problem związany z przekazywaniem magazynu danych, w którym jest tworzony pusty folder, jeśli `target_path` został uruchomiony z `/` .
    + Włączono porównanie dwóch zestawów danych.
    + Usuwanie modelu i obrazu teraz zawiera więcej informacji na temat pobierania obiektów nadrzędnych, które są zależne od nich, jeśli usuwanie nie powiedzie się z powodu zależności między strumieniami.
    + Przestarzałe ustawienie RunConfiguration nie jest używane w auto_prepare_environment.
  + **azureml-mlflow**
    + Ulepszone wykorzystanie zasobów przez zdalne uruchomienia korzystające z platformy Azure. mlflow.
    + Ulepszona Dokumentacja pakietu Azure-mlflow.
    + Rozwiązano problem polegający na tym, że mlflow.log_artifacts ("my_dir") zapisze artefakty w obszarze "my_dir/artifact-Paths" zamiast "artefaktów".
  + **azureml-pipeline-core**
    + Parametr hash_paths dla wszystkich kroków potoku jest przestarzały i zostanie usunięty w przyszłości. Domyślnie zawartość source_directory jest skrótem (z wyjątkiem plików wymienionych w `.amlignore` lub `.gitignore` )
    + Kontynuuj ulepszanie modułów i ModuleStep w celu obsługi obliczeniowych modułów dla konkretnych typów, aby przygotować się do integracji RunConfiguration i innych zmian w celu odblokowania użycia modułu specyficznego dla typu obliczeń w potokach.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Ulepszona dokumentacja w odniesieniu do danych wejściowych/wyjściowych.
    + AzureBatchStep: Zmieniono wartość domyślną delete_batch_job_after_finish na true.
  + **azureml-train-core**
    + Ciągi są teraz akceptowane jako element docelowy obliczeń dla zautomatyzowanego dostrajania parametrów.
    + Przestarzałe ustawienie RunConfiguration nie jest używane w auto_prepare_environment.
    + Przestarzałe parametry `conda_dependencies_file_path` i `pip_requirements_file_path` `conda_dependencies_file` `pip_requirements_file` odpowiednio.
  + **Azure — opendatasets**
    + Znacznie Zwiększ wydajność wzbogacania NoaaIsdWeather w wersji innej niż SPARK.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning zestawu SDK dla języka Python v 1.0.33.

+ Usługa Azure ML Modele z przyspieszaniem sprzętowym w systemie [FPGA](how-to-deploy-fpga-web-service.md) jest ogólnie dostępna.
  + Teraz można [używać pakietu Azure-akceleracja-models](how-to-deploy-fpga-web-service.md) do:
    + Uczenie wag obsługiwanej sieci głębokiej neuronowych (ResNet 50, ResNet 152, DenseNet-121, VGG-16 i SSD-VGG)
    + Korzystanie z uczenia transferowego z obsługiwanym DNN
    + Zarejestruj model przy użyciu usługi Zarządzanie modelami i konteneryzowanie model
    + Wdrażanie modelu na maszynie wirtualnej platformy Azure przy użyciu FPGA w klastrze usługi Azure Kubernetes Service (AKS)
  + Wdrażanie kontenera na urządzeniu z serwerem [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md)
  + Poprowadź ocenę danych za pomocą punktu końcowego gRPC z tym [przykładem](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

+ Czyszczenie funkcji umożliwiające dynamiczne dodawanie :::no-loc text="featurizers"::: do optymalizacji wydajności. Nowość :::no-loc text="featurizers"::: : osadzanie pracy, Waga dowodów, kodowanie docelowe, kodowanie tekstu docelowego, odległość klastra
+ Inteligentna CV do obsługi pociągów i prawidłowych podziałów w ramach zautomatyzowanej ML
+ Niektóre zmiany optymalizacji pamięci i zwiększenie wydajności środowiska uruchomieniowego
+ Poprawa wydajności w wyjaśnieniu modelu
+ Konwersja modelu ONNX na potrzeby lokalnego uruchomienia
+ Dodano obsługę podpróbkowania
+ Inteligentne zatrzymywanie, gdy nie zdefiniowano kryteriów wyjścia
+ Skumulowane zestawy

+ Prognozowanie szeregów czasowych
  + Nowa funkcja prognozowania przewidywania
  + Teraz można korzystać z krzyżowego sprawdzania poprawności w danych szeregów czasowych
  + Dodano nowe funkcje w celu skonfigurowania spowolnienia szeregów czasowych
  + Dodano nowe funkcje do obsługi funkcji agregowania okna
  + Nowe wykrywanie świąt i featurized, gdy kod kraju jest zdefiniowany w ustawieniach eksperymentu

+ Azure Databricks
  + Włączono prognozowanie szeregów czasowych i explainabilty model/możliwości interpretacji
  + Możesz teraz anulować i wznowić (kontynuować) zautomatyzowane eksperymenty ML
  + Dodano obsługę przetwarzania wielordzeniowego

### <a name="mlops"></a>MLOps
+ **Debugowanie lokalnego & wdrożenia dla kontenerów oceniania**<br/> Teraz można wdrożyć model ML lokalnie i szybko wykonać iterację w pliku oceniania i zależnościach, aby upewnić się, że działają one zgodnie z oczekiwaniami.

+ **Wprowadzono InferenceConfig & model. deploy ()**<br/> Wdrożenie modelu obsługuje teraz Określanie folderu źródłowego ze skryptem wejścia, tak samo jak RunConfig.  Ponadto wdrożenie modelu zostało uproszczone do jednego polecenia.

+ **Śledzenie odwołań git**<br/> Klienci zażądali podstawowych możliwości integracji usługi git przez jakiś czas, ponieważ ułatwiają one obsługę kompletnego dziennika inspekcji. Zaimplementowano śledzenie dla głównych jednostek w usłudze Azure ML dla metadanych związanych z git (repozytorium, zatwierdzanie, czyszczenie stanu). Te informacje będą zbierane automatycznie przez zestaw SDK i interfejs wiersza polecenia.

+ **Profilowanie modelu & weryfikacja usługi**<br/> Klienci często omawiają trudności związane z prawidłowym rozmiarem obliczeń związanych z ich usługą wnioskowania. Dzięki naszej usłudze profilowania modelu klient może dostarczyć przykładowe dane wejściowe i profilować w 16 różnych konfiguracjach procesora/pamięci, aby określić optymalną zmianę rozmiarów wdrożenia.

+ **Przenoszenie własnego obrazu podstawowego do wnioskowania**<br/> Kolejną powszechną skargą była trudność w przeniesieniu się z eksperymentów w celu przeprowadzenia ponownego udostępniania. Dzięki naszej nowej funkcji udostępniania obrazów podstawowych można teraz ponownie wykorzystać podstawowe obrazy eksperymentów, zależności i wszystkie w przypadku wnioskowania. Powinno to przyspieszyć wdrożenia i zmniejszyć przerwy od wewnętrznego do pętli zewnętrznej.

+ **Ulepszone środowisko generowania schematu struktury Swagger**<br/> Nasza Poprzednia metoda generowania struktury Swagger była podatna na błędy i nie można jej zautomatyzować. Mamy nowy wbudowany sposób generowania schematów struktury Swagger z dowolnej funkcji języka Python za pośrednictwem dekoratory. Mamy ten kod, a nasz protokół generowania schematu nie jest połączony z platformą Azure ML.

+ **Azure ML CLI jest ogólnie dostępna (GA)**<br/> Modele można teraz wdrażać za pomocą jednego polecenia CLI. Mamy często zadawane Opinie klientów, że żaden z nich nie wdraża modelu ML z notesu Jupyter. [**Dokumentacja referencyjna interfejsu wiersza polecenia**](./reference-azure-machine-learning-cli.md) została zaktualizowana.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning zestawu SDK dla języka Python v 1.0.30.

[`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint?preserve-view=true&view=azure-ml-py)Wprowadzono, aby dodać nową wersję opublikowanego potoku podczas utrzymywania tego samego punktu końcowego.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Witryna Azure Portal
  + Teraz można ponownie przesłać istniejący skrypt uruchomiony w istniejącym zdalnym klastrze obliczeniowym.
  + Teraz można uruchomić opublikowany potok z nowymi parametrami na karcie potoki.
  + Szczegóły uruchamiania obsługują teraz nową przeglądarkę plików migawek. Możesz wyświetlić migawkę katalogu podczas przesyłania określonego uruchomienia. Możesz również pobrać Notes, który został przesłany w celu uruchomienia uruchomienia.
  + Teraz można anulować uruchomienia nadrzędne z Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.23

+ **Nowe funkcje**
  + Zestaw SDK Azure Machine Learning obsługuje teraz środowisko Python 3,7.
  + Azure Machine Learning DNN szacowania teraz zapewnia wbudowaną obsługę wielowersji. Na przykład `TensorFlow` szacowania teraz akceptuje `framework_version` parametr, a użytkownicy mogą określić wersję "1,10" lub "1,12". Aby zapoznać się z listą wersji obsługiwanych przez bieżącą wersję zestawu SDK, należy wywołać `get_supported_versions()` żądaną klasę struktury (na przykład `TensorFlow.get_supported_versions()` ).
  Listę wersji obsługiwanych przez najnowszą wersję zestawu SDK można znaleźć w [dokumentacji DNN szacowania](/python/api/azureml-train-core/azureml.train.dnn?preserve-view=true&view=azure-ml-py).

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.21

+ **Nowe funkcje**
  + Metoda *azureml.Core.Run.create_children* umożliwia tworzenie wielu elementów podrzędnych w przypadku małych opóźnień z pojedynczym wywołaniem.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.18

 + **Zmiany**
   + Pakiet Azure-tensorboard zastępuje usługę Azure-contrib-tensorboard.
   + W tej wersji można skonfigurować konto użytkownika w zarządzanym klastrze obliczeniowym (amlcompute) podczas jego tworzenia. Można to zrobić, przekazując te właściwości w konfiguracji aprowizacji. Więcej szczegółów można znaleźć w [dokumentacji referencyjnej zestawu SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning zestawu SDK 1.0.17 przygotowywania danych

+ **Nowe funkcje**
  + Program obsługuje teraz Dodawanie dwóch kolumn liczbowych w celu wygenerowania wynikowej kolumny przy użyciu języka wyrażeń.

+ **Poprawki i ulepszenia błędów**
  + Ulepszono sprawdzanie dokumentacji i parametrów dla random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning zestawu SDK 1.0.16 przygotowywania danych

+ **Poprawka błędu**
  + Rozwiązano problem z uwierzytelnianiem jednostki usługi, który został spowodowany przez zmianę interfejsu API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Zestaw Azure Machine Learning SDK dla języka Python v 1.0.17

+ **Nowe funkcje**
  + Azure Machine Learning teraz zapewnia obsługę pierwszej klasy dla popularnego łańcucha DNN Framework. Korzystanie z [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer?preserve-view=true&view=azure-ml-py) klas użytkownicy mogą łatwo przeszkolić i wdrożyć modele łańcucha.
    + Dowiedz się, jak [uruchomić szkolenie rozproszone z ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Dowiedz się, jak [uruchomić dostrajanie parametrów za pomocą łańcucha](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Potoki Azure Machine Learning dodane zdolność do wyzwalania uruchomienia potoku na podstawie modyfikacji magazynu danych. [Notes harmonogramu](https://aka.ms/pl-schedule) potoku został zaktualizowany w celu pokazania tej funkcji.

+ **Poprawki i ulepszenia błędów**
  + Dodaliśmy obsługę w potokach Azure Machine Learning, aby ustawić właściwość source_directory_data_store na żądany magazyn danych (np. Magazyn obiektów BLOB) w witrynie [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration?preserve-view=true&view=azure-ml-py) , które są dostarczane do [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?preserve-view=true&view=azure-ml-py). Zgodnie z domyślnymi krokami usługa Azure File Store jest używana jako zapasowy magazyn danych, co może powodować problemy z ograniczaniem wydajności w przypadku wykonywania dużej liczby kroków współbieżnie.

### <a name="azure-portal"></a>Witryna Azure Portal

+ **Nowe funkcje**
  + Nowe środowisko edytora tabeli przeciągnij i upuść dla raportów. Użytkownicy mogą przeciągać kolumnę od samego do obszaru tabeli, w którym zostanie wyświetlona wersja zapoznawcza tabeli. Kolumny można zmieniać.
  + Nowa przeglądarka plików dzienników
  + Linki do przebiegów eksperymentów, obliczenia, modele, obrazy i wdrożenia z poziomu karty działania

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z omówieniem usługi [Azure Machine Learning](overview-what-is-azure-ml.md).
