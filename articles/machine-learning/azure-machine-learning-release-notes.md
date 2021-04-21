---
title: Co nowego w wersji?
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o najnowszych aktualizacjach Azure Machine Learning oraz o zestawach SDK języka Python do uczenia maszynowego i przetwarzania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 02/18/2021
ms.openlocfilehash: 1de495253dacac5aeab7dcff95f74aeed11782a8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750738"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning informacji o wersji

Ten artykuł zawiera informacje na temat Azure Machine Learning wersji.  Aby uzyskać pełną zawartość referencyjną zestawu SDK, odwiedź Azure Machine Learning z główną stroną referencyjną [**zestawu SDK dla języka Python.**](/python/api/overview/azure/ml/intro)

__Kanał informacyjny RSS__: otrzymuj powiadomienie, gdy ta strona zostanie zaktualizowana przez skopiowanie i wklejenie następującego adresu URL w czytniku kanałów informacyjnych: `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes%22&locale=en-us`


## <a name="2021-04-19"></a>2021-04-19

### <a name="azure-machine-learning-sdk-for-python-v1270"></a>Azure Machine Learning SDK dla języka Python w wersji 1.27.0
+ **Poprawki błędów i ulepszenia**
  + **azureml-core**
    + Dodano możliwość zastąpienia domyślnej wartości limitu czasu przekazywania artefaktu za pośrednictwem zmiennej środowiskowej "AZUREML_ARTIFACTS_DEFAULT_TIMEOUT".
    + Usunięto usterkę, w przypadku której ustawienia platformy Docker w obiekcie Environment w pliku ScriptRunConfig nie były przestrzegane.
    + Zezwalaj na partycjonowanie zestawu danych podczas kopiowania go do miejsca docelowego.
    + Dodano tryb niestandardowy do pliku OutputDatasetConfig w celu umożliwienia przekazywania utworzonych zestawów danych w potokach za pośrednictwem funkcji linku. Te ulepszenia obsługi wprowadzono w celu włączenia partycjonowania tabelaryowego dla usługi PRS.
    + Dodano nowy typ obliczeniowy KubernetesCompute do elementu azureml-core.
  + **azureml-pipeline-core**
    + Dodanie trybu niestandardowego do pliku OutputDatasetConfig i umożliwienie użytkownikowi przekazania utworzonych zestawów danych w potokach za pośrednictwem funkcji linku. Miejsca docelowe ścieżki plików obsługują symbole zastępcze. Obsługują one ulepszenia wprowadzone w celu włączenia partycjonowania tabelaryowego dla usługi PRS.
    + Dodanie nowego typu obliczeniowego KubernetesCompute do platformy azureml-core.
  + **azureml-pipeline-steps**
    + Dodanie nowego typu obliczeniowego KubernetesCompute do platformy azureml-core.
  + **azureml-synapse**
    + Aktualizowanie adresu URL interfejsu użytkownika platformy Spark w widżecie usługi azureml Synapse
  + **azureml-train-automl-client**
    + Cechator STL dla zadania prognozowania używa teraz bardziej niezawodnego wykrywania sezonowości na podstawie częstotliwości szeregów czasu.
  + **azureml-train-core**
    + Usunięto usterkę w przypadku, gdy ustawienia platformy Docker w obiekcie Environment nie były przestrzegane.
    + Dodanie nowego typu obliczeniowego KubernetesCompute do elementu azureml-core.


## <a name="2021-04-05"></a>2021-04-05

### <a name="azure-machine-learning-sdk-for-python-v1260"></a>Azure Machine Learning SDK dla języka Python w wersji 1.26.0
+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Rozwiązano problem, który oznaczał, że modele natywne są zalecane w przypadku przebiegów autoMLStep i nie powiodły się z funkcjami opóźnień lub kroczących okien. Te modele nie będą zalecane w przypadku ustawienia docelowych opóźnień lub docelowego rozmiaru okna kroczącego.
    +  Zmieniono dane wyjściowe konsoli podczas przesyłania uruchomienia autoML w celu pokazania linku portalu do uruchomienia.
  + **azureml-core**
    + Dodano tryb HDFS w dokumentacji.
    + Dodano obsługę w celu zrozumienia partycji zestawu danych plików na podstawie struktury globu.
    + Dodano obsługę aktualizowania rejestru kontenerów skojarzonego z obszarem roboczym usługi AzureML.
    + Przestarzałe atrybuty środowiska w obszarze DockerSection — "enabled", "shared_volume" i "arguments" są teraz częścią funkcji DockerConfiguration w funkcji RunConfiguration.
    + Zaktualizowana dokumentacja klonowania interfejsu wiersza polecenia potoku
    + Zaktualizowano adresy URI portalu w celu dołączyć dzierżawę do uwierzytelniania
    + Usunięto nazwę eksperymentu z uruchomionych interfejsów URI, aby uniknąć przekierowań 
    + Zaktualizowano eksperyment URO, aby używać identyfikatora eksperymentu.
    + Poprawki błędów dotyczące dołączania zdalnych zasobów obliczeniowych za pomocą interfejsu wiersza polecenia usługi AzureML.
    + Zaktualizowano UWIERZYTELNIANIA portalu w celu dołączyć dzierżawę do uwierzytelniania.
    + Zaktualizowano identyfikator URI eksperymentu w celu użycia identyfikatora eksperymentu.
  + **azureml-interpret**
    + Zaktualizowano narzędzie azureml-interpret do korzystania z wersji interpret-community 0.17.0
  + **azureml-opendatasets**
    + Sprawdzanie poprawności typu daty rozpoczęcia i zakończenia danych wejściowych oraz wskazanie błędu, jeśli nie jest to typ data/data/data/data.
  + **azureml-parallel-run**
    + [Funkcja eksperymentalna] Dodaj parametr do parallelRunConfig, jeśli zostanie określony, wejściowych zestawów danych zostaną podzielone na mini partie przez `partition_keys` klucze określone przez niego. Wymaga ona partycjonowania wszystkich wejściowych zestawów danych.
  + **azureml-pipeline-steps**
    + Poprawka usterki — obsługa path_on_compute podczas przekazywania konfiguracji zestawu danych jako pobierania.
    + Wycofaj RScriptStep na rzecz używania funkcji CommandStep do uruchamiania skryptów języka R w potokach. 
    + Wycofaj krok EstimatorStep na rzecz używania funkcji CommandStep do uruchamiania trenowania uczenia maszynowego (w tym trenowania rozproszonego) w potokach.
  + **azureml-sdk**
    + Aktualizacja python_requires do wersji < 3.9 dla zestawu azureml-sdk
  + **azureml-train-automl-client**
    +  Zmieniono dane wyjściowe konsoli podczas przesyłania uruchomienia autoML w celu pokazania linku portalu do uruchomienia.
  + **azureml-train-core**
    + Wycofano atrybuty "enabled", "shared_volume" i "arguments" usługi DockerSection na rzecz używania funkcji DockerConfiguration z poleceniem ScriptRunConfig.
    +  Użyj Azure Open Datasets dla zestaw danych MNIST
    + Komunikaty o błędach funkcji Hyperdrive zostały zaktualizowane.


## <a name="2021-03-22"></a>2021-03-22

### <a name="azure-machine-learning-sdk-for-python-v1250"></a>Azure Machine Learning SDK dla języka Python w wersji 1.25.0
+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    +  Zmieniono dane wyjściowe konsoli podczas przesyłania uruchomienia autoML w celu pokazania linku portalu do uruchomienia.
  + **azureml-core**
    + Rozpoczyna obsługę aktualizowania rejestru kontenerów dla obszaru roboczego w zestawie SDK i interfejsie wiersza polecenia
    + Wycofano atrybuty "enabled", "shared_volume" i "arguments" usługi DockerSection na rzecz używania funkcji DockerConfiguration z poleceniem ScriptRunConfig.
    + Zaktualizowana dokumentacja klonowania interfejsu wiersza polecenia potoku
    + Zaktualizowano adresy URI portalu w celu dołączyć dzierżawę do uwierzytelniania
    + Usunięto nazwę eksperymentu z uruchomionych interfejsów URI, aby uniknąć przekierowań
    + Zaktualizowano eksperyment URO, aby używać identyfikatora eksperymentu.
    + Poprawki usterek dotyczące dołączania zdalnych zasobów obliczeniowych przy użyciu polecenia az cli
    + Zaktualizowano UWIERZYTELNIANIA portalu w celu dołączyć dzierżawę do uwierzytelniania.
    + Dodano obsługę w celu zrozumienia partycji zestawu danych plików na podstawie struktury globu.
  + **azureml-interpret**
    + Zaktualizowano narzędzie azureml-interpret w celu używania programu interpret-community 0.17.0
  + **azureml-opendatasets**
    + Wejściowa data rozpoczęcia i typ daty zakończenia weryfikacji i wskazanie błędu, jeśli nie jest to typ daty/godziny.
  + **azureml-pipeline-core**
    + Poprawka usterki — obsługa path_on_compute podczas przekazywania konfiguracji zestawu danych jako pobierania.
  + **azureml-pipeline-steps**
    + Poprawka usterki — obsługa path_on_compute podczas przekazywania konfiguracji zestawu danych jako pobierania.
    + Wycofaj krok RScriptStep na rzecz używania funkcji CommandStep do uruchamiania skryptów języka R w potokach. 
    + Wycofaj krok EstimatorStep na rzecz używania funkcji CommandStep do uruchamiania trenowania uczenia maszynowego (w tym trenowania rozproszonego) w potokach.
  + **azureml-train-automl-runtime**
    +  Zmieniono dane wyjściowe konsoli podczas przesyłania uruchomienia autoML w celu pokazania linku portalu do uruchomienia.
  + **azureml-train-core**
    + Wycofano atrybuty "enabled", "shared_volume" i "arguments" usługi DockerSection na rzecz używania funkcji DockerConfiguration z poleceniem ScriptRunConfig.
    + Użyj Azure Open Datasets dla zestaw danych MNIST
    + Komunikaty o błędach funkcji Hyperdrive zostały zaktualizowane.


## <a name="2021-03-31"></a>2021-03-31
### <a name="azure-machine-learning-studio-notebooks-experience-march-update"></a>Azure Machine Learning Studio Notebooks Experience (aktualizacja z marca)
+ **Nowe funkcje**
  + Renderuj wolumin CSV/TSV. Użytkownicy będą mogli renderować pliki TSV/CSV w formacie siatki, aby ułatwić analizę danych. 
  + Uwierzytelnianie za pomocą logowania jednokrotnego dla wystąpienia obliczeniowego. Użytkownicy mogą teraz łatwo uwierzytelniać dowolne nowe wystąpienia obliczeniowe bezpośrednio w interfejsie użytkownika notesu, co ułatwia uwierzytelnianie i używanie zestawów Azure SDK bezpośrednio w usłudze AzureML. 
  + Metryki wystąpienia obliczeniowego. Użytkownicy będą mogli wyświetlać metryki obliczeniowe, takie jak użycie procesora CPU i pamięć, za pośrednictwem terminalu.
  + Szczegóły pliku. Użytkownicy mogą teraz zobaczyć szczegóły pliku, w tym czas ostatniej modyfikacji i rozmiar pliku, klikając 3 kropki obok pliku.

+ **Poprawki błędów i ulepszenia**
  + Ulepszone czasy ładowania stron.
  + Lepsza wydajność.
  + Zwiększona szybkość i niezawodność jądra.
  + Zyskaj pionową powierzchnię, trwale przenosząc okienko pliku notesu w górę
  + Linki można teraz klikać w terminalu
  + Zwiększona wydajność funkcji IntelliSense


## <a name="2021-03-08"></a>2021-03-08

### <a name="azure-machine-learning-sdk-for-python-v1240"></a>Azure Machine Learning SDK dla języka Python w wersji 1.24.0
+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Usunięto importy zgodne z poprzednimi wersjami z . `azureml.automl.core.shared` Błędy nie znaleziono modułu w `azureml.automl.core.shared` przestrzeni nazw można rozwiązać, importując z elementu `azureml.automl.runtime.shared` .
  + **azureml-contrib-automl-dnn-vision**
    + Ujawniony model yolo wykrywania obiektów.
  + **azureml-contrib-dataset**
    + Dodano funkcję filtrowania tabelarowych zestawów danych według wartości kolumn i zestawów danych plików według metadanych.
  + **azureml-contrib-fairness**
    + Uwzględnij schemat JSON w pliku wheel dla `azureml-contrib-fairness`
  + **azureml-contrib-dostęp**
    + Po ustawieniu show_output wartości True podczas wdrażania modeli konfiguracja wnioskowania i konfiguracja wdrożenia zostaną powtórzone przed wysłaniem żądania do serwera.
  + **azureml-core**
    + Dodano funkcję filtrowania tabelarowych zestawów danych według wartości kolumn i zestawów danych plików według metadanych.
    + Wcześniej możliwe było, że użytkownicy utworzyli konfiguracje aprowizacji dla elementu ComputeTarget, które nie spełniały wymagań dotyczących siły hasła dla pola (tj. muszą zawierać co najmniej 3 z następujących znaków: 1 mała litera, 1 wielkie litery, 1 cyfra i 1 znak specjalny z `admin_user_password` następującego zestawu: ``\`~!@#$%^&*()=+_[]{}|;:./'",<>?`` ). Jeśli użytkownik utworzył konfigurację ze słabym hasłem i uruchomił zadanie przy użyciu tej konfiguracji, zadanie nie powiodłoby się w czasie wykonywania. Teraz wywołanie do `AmlCompute.provisioning_configuration` zrzuci komunikat z towarzyszącym komunikatem `ComputeTargetException` o błędzie wyjaśniający wymagania dotyczące siły hasła. 
    + Ponadto w niektórych przypadkach można było również określić konfigurację z ujemną liczbą maksymalnej liczby węzłów. Nie jest już możliwe. Teraz `AmlCompute.provisioning_configuration` zrzuci argument `ComputeTargetException` , jeśli argument jest `max_nodes` ujemną liczbą całkowitą.
    + Po ustawieniu show_output wartości True podczas wdrażania modeli zostanie wyświetlona konfiguracja wnioskowania i konfiguracja wdrożenia.
    + Po ustawieniu show_output na wartość True podczas oczekiwania na ukończenie wdrażania modelu zostanie wyświetlony postęp operacji wdrażania.
    + Zezwalaj na katalog konfiguracji uwierzytelniania AzureML określony przez klienta za pomocą zmiennej środowiskowej: AZUREML_AUTH_CONFIG_DIR
    + Wcześniej można było utworzyć konfigurację aprowizowania z minimalną licznikiem węzłów mniejszą niż maksymalna liczba węzłów. Zadanie zostanie uruchomione, ale nie będzie działać w czasie wykonywania. Ta usterka została naprawiona. Jeśli teraz spróbujesz utworzyć konfigurację aprowizowania przy użyciu `min_nodes < max_nodes` zestawu SDK, zostanie wyniesieny . `ComputeTargetException`
  + **azureml-interpret**
    + pulpit nawigacyjny z wyjaśnieniem rozrzednia nie pokazuje zagregowanych ważności funkcji dla rozrzedowanych wyjaśnień
    + zoptymalizowane użycie pamięci przez explanationClient w pakiecie azureml-interpret
  + **azureml-train-automl-client**
    +  Naprawiono show_output = False, aby zwrócić kontrolę do użytkownika podczas uruchamiania przy użyciu platformy Spark.

## <a name="2021-02-28"></a>2021-02-28
### <a name="azure-machine-learning-studio-notebooks-experience-february-update"></a>Azure Machine Learning notesów programu Studio (aktualizacja w lutym)
+ **Nowe funkcje**
  + [Terminal natywny (GA)](./how-to-access-terminal.md). Użytkownicy będą teraz mieć dostęp do zintegrowanego terminalu oraz do operacji Git za pośrednictwem zintegrowanego terminalu.
  + Fragmenty kodu notesu (wersja zapoznawcza). Typowe fragmenty kodu usługi Azure ML są teraz dostępne w zasięgu ręki. Przejdź do panelu fragmentów kodu, dostępnego za pośrednictwem paska narzędzi, lub aktywuj menu fragmentów kodu za pomocą kombinacji klawiszy Ctrl + spacja.  
  + [Skróty klawiaturowe](./how-to-run-jupyter-notebooks.md#useful-keyboard-shortcuts). Pełna parzystość ze skrótami klawiaturowymi dostępnymi w programie Jupyter. 
  + Wskazanie parametrów komórki. Pokazuje użytkownikom, które komórki w notesie są komórkami parametrów i mogą uruchamiać sparametryzowane notesy za pośrednictwem [papermill](https://github.com/nteract/papermill) w wystąpieniu obliczeniowym.
  + Menedżer sesji terminalu i jądra: użytkownicy będą mogli zarządzać wszystkimi jądrami i sesjami terminali uruchomionymi w ich obliczeniach.
  + Przycisk Udostępniania. Użytkownicy mogą teraz udostępnić dowolny plik w Eksploratorze plików notesu, klikając go prawym przyciskiem myszy i używając przycisku udostępniania.


+ **Poprawki błędów i ulepszenia**
  + Ulepszone czasy ładowania stron
  + Większa wydajność 
  + Zwiększona szybkość i niezawodność jądra
  + Dodano obracające się koło w celu pokazania postępu dla wszystkich [trwających operacji wystąpienia obliczeniowego.](./how-to-run-jupyter-notebooks.md#status-indicators)
  + Kliknij prawym przyciskiem myszy w Eksplorator plików. Kliknięcie prawym przyciskiem myszy dowolnego pliku spowoduje teraz otwarcie operacji na plikach. 


## <a name="2021-02-16"></a>2021-02-16

### <a name="azure-machine-learning-sdk-for-python-v1230"></a>Azure Machine Learning SDK dla języka Python w wersji 1.23.0
+ **Poprawki błędów i ulepszenia**
  + **azureml-core**
    + [Funkcja eksperymentalna] Dodawanie obsługi łączenia obszaru roboczego usługi Synapse z usługą AML jako połączonej usługi
    + [Funkcja eksperymentalna] Dodanie obsługi dołączania puli synapse spark do rozwiązania AML jako zasobów obliczeniowych
    + [Funkcja eksperymentalna] Dodanie obsługi dostępu do danych na podstawie tożsamości. Użytkownicy mogą rejestrować magazyn danych lub zestawy danych bez poświadczeń. W takim przypadku token usługi AAD użytkownika lub tożsamość zarządzana docelowego obiektu obliczeniowego będą używane do uwierzytelniania. Więcej informacji można znaleźć [tutaj](./how-to-identity-based-data-access.md).
  + **azureml-pipeline-steps**
    + [Funkcja eksperymentalna] Dodawania obsługi [rozwiązania SynapseSparkStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.synapsesparkstep)
  + **azureml-synapse**
    + [Funkcja eksperymentalna] Dodawania obsługi funkcji magic platformy Spark w celu uruchamiania interaktywnej sesji w puli usługi Synapse Spark.
+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-runtime**
    + W tej aktualizacji dodaliśmy gładsy holt exponential smoothing do przybornika prognozowania zestawu AutoML SDK. Biorąc pod uwagę szereg czasowy, najlepszy model jest wybierany przez [AICc (poprawione](https://otexts.com/fpp3/selecting-predictors.html#selecting-predictors) kryterium informacyjne Akaike) i zwracany.
    + Teraz autoML wygeneruje dwa pliki dziennika zamiast jednego. Instrukcje dziennika będą trafiać do jednego lub drugiego procesu w zależności od tego, w którym procesie została wygenerowana instrukcja dziennika.
    + Usuń niepotrzebne przewidywanie w próbce podczas trenowania modelu za pomocą krzyżowego sprawdzania poprawności. W niektórych przypadkach może to zmniejszyć czas trenowania modelu, szczególnie w przypadku modeli prognozowania szeregów czasowych.
  + **azureml-contrib-fairness**
    + Dodaj schemat JSON dla pulpitu nawigacyjnegoDyktura przekazywania.
  + **azureml-contrib-interpret**
    + Aktualizacja pliku README azureml-contrib-interpret odzwierciedla, że pakiet zostanie usunięty w następnej aktualizacji po wycofaniu od października. Zamiast tego użyj pakietu azureml-interpret
  + **azureml-core**
    + Wcześniej można było utworzyć konfigurację aprowizowania z minimalną liczbę węzłów mniejszą niż maksymalna liczba węzłów. Ten problem został rozwiązany. Jeśli teraz spróbujesz utworzyć konfigurację aprowizowania za `min_nodes < max_nodes` pomocą zestawu SDK, zostanie wyniesieny . `ComputeTargetException`
    +  Usunięto usterkę w wait_for_completion w amlCompute, która powodowała, że funkcja zwracała przepływ sterowania przed ukończeniem operacji
    + Funkcja Run.fail() jest teraz przestarzała, użyj funkcji Run.tag(), aby oznaczyć przebieg jako nieudany, lub użyj funkcji Run.cancel(), aby oznaczyć przebieg jako anulowany.
    + Pokaż komunikat o błędzie "Nazwa środowiska oczekiwano str, found", gdy {} podana nazwa środowiska nie jest ciągiem.
  + **azureml-train-automl-client**
    + Usunięto usterkę uniemożliwiającą anulowanie eksperymentów automatycznego Azure Databricks na klastrach.


## <a name="2021-02-09"></a>2021-02-09

### <a name="azure-machine-learning-sdk-for-python-v1220"></a>Azure Machine Learning SDK dla języka Python w wersji 1.22.0
+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Usunięto usterkę, w przypadku której do pliku conda yml dodano dodatkową zależność pip dla modeli obrazów.
  + **azureml-automl-runtime**
    + Usunięto usterkę, w przypadku której klasyczne modele prognozowania (np. autoaryma) mogły odbierać dane treningowe, w których wiersze z imputedami wartościami docelowymi nie były obecne. Narusza to kontrakt danych tych modeli. * Usunięto różne usterki z zachowaniem opóźnienia po wystąpieniu w operatorze opóźniania szeregów czasu. Wcześniej operacja opóźnienia po wystąpieniu nie oznaczała poprawnie wszystkich wierszy z imputedami, dlatego nie zawsze generowałaby poprawne wartości opóźnień wystąpienia. Rozwiązano również niektóre problemy ze zgodnością między operatorem opóźnienia i operatorem okna kroczącego z zachowaniem opóźnienia po wystąpieniu. Wcześniej operator okna kroczącego porzucał niektóre wiersze z danych treningowych, których w przeciwnym razie powinien użyć.
  + **azureml-core**
    + Dodanie obsługi uwierzytelniania tokenu przez odbiorców.
    + Dodaj `process_count` do [PyTorchConfiguration w](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) celu obsługi wieloprocesów wielowęzłowych zadań PyTorch.
  + **azureml-pipeline-steps**
    + [Krok polecenia jest](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.commandstep) teraz dostępny i nie jest już eksperymentalny.
    + [ParallelRunConfig:](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig)dodaj argument allowed_failed_count i allowed_failed_percent, aby sprawdzić próg błędu na poziomie mini partii. Próg błędu ma teraz 3 smaki:
       + error_threshold — liczba dozwolonych elementów wsadowych minizgodnych, które zakończyły się niepowodzeniem; 
       + allowed_failed_count — liczba dozwolonych mini partii, które zakończyły się niepowodzeniem; 
       + allowed_failed_percent — procent dozwolonych minisad, które zakończyły się niepowodzeniem. 
       
       Zadanie zostanie zatrzymane, jeśli przekroczy którekolwiek z nich. error_threshold jest wymagane, aby zachować zgodność z poprzednimi wersjami. Ustaw wartość na -1, aby ją zignorować.
    + Poprawiono obsługę białych znaków w nazwie AutoMLStep.
    + SkryptRunConfig jest teraz obsługiwany przez hyperDriveStep
  + **azureml-train-core**
    + Uruchomienia funkcji HyperDrive wywoływane z polecenia ScriptRun będą teraz traktowane jako przebieg podrzędny.
    + Dodaj `process_count` do [PyTorchConfiguration w](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) celu obsługi wieloprocesów wielowęzłowych zadań PyTorch.
  + **azureml-widgets**
    + Dodaj widżet ParallelRunStepDetails, aby zwizualizować stan elementu ParallelRunStep.
    + Umożliwia użytkownikom funkcji hyperdrive wyświetlanie dodatkowej osi na wykresie współrzędnych równoległych, która pokazuje wartość metryki odpowiadającą każdemu zestawowi hiperparametrów dla każdego uruchomienia podrzędnego.


 ## <a name="2021-01-31"></a>2021-01-31
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Azure Machine Learning Studio Notebooks Experience (styczniowa aktualizacja)
+ **Nowe funkcje**
  + Natywny edytor języka Markdown w usłudze AzureML. Użytkownicy mogą teraz natywnie renderować i edytować pliki markdown w programie AzureML Studio.
  + [Przycisk Uruchom dla skryptów (py, . R i .sh)](./how-to-run-jupyter-notebooks.md#run-a-notebook-or-python-script). Użytkownicy mogą teraz łatwo uruchamiać skrypty w językach Python, R i Bash w usłudze AzureML
  + [Eksplorator zmiennych .](./how-to-run-jupyter-notebooks.md#explore-variables-in-the-notebook) Zapoznaj się z zawartością zmiennych i ramek danych w wyskakującym panelu. Użytkownicy mogą łatwo sprawdzać typ danych, rozmiar i zawartość.
  + [Tabela zawartości](./how-to-run-jupyter-notebooks.md#navigate-with-a-toc). Przejdź do sekcji notesu wskazywanych przez nagłówki markdown.
  + Wyeksportuj notes jako Latex/HTML/Py. Tworzenie łatwych do udostępnienia plików notesów przez eksportowanie do elementu LaTex, HTML lub PY
  + Intellicode. Wyniki oparte na uchwyceniach uczenia maszynowego zapewniają ulepszone [środowisko inteligentnego autouzupełniania.](/visualstudio/intellicode/overview)

+ **Poprawki błędów i ulepszenia**
  + Ulepszone czasy ładowania stron
  + Większa wydajność 
  + Większa szybkość i niezawodność jądra
  

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Azure Machine Learning SDK dla języka Python w wersji 1.21.0
+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Naprawiono tekst pomocy interfejsu wiersza polecenia podczas korzystania z usługi AmlCompute z tożsamością przypisaną przez użytkownika
  + **azureml-contrib-automl-dnn-vision**
    + Przyciski wdrażania i pobierania staną się widoczne dla przebiegów usługi AutoML Vision, a modele można wdrażać lub pobierać podobnie jak inne przebiegi rozwiązania AutoML. Istnieją dwa nowe pliki (scoring_file_v_1_0_0.py i conda_env_v_1_0_0.yml), które zawierają skrypt do uruchamiania wnioskowania i plik yml w celu ponownego tworzenia środowiska conda. Nazwa pliku "model.pth" została również zmieniona tak, aby używać rozszerzenia ".pt".
  + **azureml-core**
    + Obsługa msi dla polecenia azure-cli-ml
    + Obsługa tożsamości zarządzanej przypisanej przez użytkownika.
    + Dzięki tej zmianie klienci powinni mieć możliwość podania tożsamości przypisanej przez użytkownika, która może służyć do pobierania klucza z magazynu kluczy klienta w celu szyfrowania danych magazynowanych.
    +  poprawka row_count=0 dla profilu bardzo dużych plików — napraw błąd w podwójnej konwersji wartości rozdzielanych z wypełnieniem odstępu
    + Usuwanie flagi eksperymentalnej dla wyjściowego zestawu danych (ga ga)
    + Aktualizacja dokumentacji dotyczącej pobierania określonej wersji modelu
    + Zezwalaj na aktualizowanie obszaru roboczego w trybie mieszanym w przypadku połączenia prywatnego
    + Poprawka usuwania dodatkowej rejestracji w sklepie danych dla funkcji uruchamiania wznawiania
    + Dodano obsługę interfejsu wiersza polecenia/zestawu SDK w celu aktualizowania podstawowej tożsamości przypisanej przez użytkownika obszaru roboczego
  + **azureml-interpret**
    + Zaktualizowano narzędzie azureml-interpret do programu interpret-community 0.16.0
    + optymalizacje pamięci dla klienta wyjaśnienia w pliku azureml-interpret
  + **azureml-train-automl-runtime**
    + Włączono przesyłanie strumieniowe dla przebiegów usługi ADB
  + **azureml-train-core**
    + Poprawka usuwania dodatkowej rejestracji w sklepie danych dla funkcji uruchamiania wznawiania
  + **azureml-widgets**
    + Klienci nie powinni widzieć zmian w istniejącej wizualizacji danych uruchamiania przy użyciu widżetu, a teraz będą mieli pomoc techniczną, jeśli opcjonalnie będą korzystać z hiperparametrów warunkowych.
    + Widżet uruchamiania użytkownika zawiera teraz szczegółowe wyjaśnienie, dlaczego przebieg jest w stanie w kolejce.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Azure Machine Learning SDK dla języka Python w wersji 1.20.0
+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + framework_version dodane w pliku OptimizationConfig. Będzie on używany podczas rejestrowania modelu za pomocą struktury MULTI.
  + **azureml-contrib-optimization**
    + framework_version dodane w pliku OptimizationConfig. Będzie on używany podczas rejestrowania modelu za pomocą struktury MULTI.
  + **azureml-pipeline-steps**
    + Wprowadzono krok CommandStep, który byłby poleceniem do przetwarzania. Polecenie może zawierać pliki wykonywalne, polecenia powłoki, skrypty itp.
  + **azureml-core**
    + Teraz tworzenie obszaru roboczego obsługuje tożsamość przypisaną przez użytkownika. Dodawanie obsługi interfejsu uai z zestawu SDK/interfejsu wiersza polecenia
    + Rozwiązano problem z service.reload() w celu pobrania zmian score.py we wdrożeniu lokalnym.
    + `run.get_details()` ma dodatkowe pole o nazwie "submittedBy", które wyświetla nazwę autora dla tego uruchomienia.
    + Edytowano dokumentację metody Model.register, aby wspomnieć o tym, jak zarejestrować model bezpośrednio z uruchomienia
    + Rozwiązano IOT-Server obsługi zmiany stanu połączenia.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Azure Machine Learning notesów programu Studio (grudniowa aktualizacja)
+ **Nowe funkcje**
  + Wyszukiwanie nazwy pliku użytkownika. Użytkownicy mogą teraz wyszukiwać wszystkie pliki zapisane w obszarze roboczym.
  + Obsługa znaczników Markdown obok siebie na komórkę notesu. W komórce notesu użytkownicy mogą teraz wyświetlać obok siebie renderowane składnie znaczników markdown i markdown.
  + Pasek stanu komórki. Pasek stanu wskazuje, w jakim stanie znajduje się komórka kodu, czy uruchomienie komórki powiodło się i jak długo trwało uruchomienie. 
   
+ **Poprawki błędów i ulepszenia**
  + Ulepszone czasy ładowania stron
  + Większa wydajność 
  + Zwiększona szybkość i niezawodność jądra

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK dla języka Python w wersji 1.19.0
+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Dodano eksperymentalną obsługę danych testowych do autoMLStep.
    + Dodano początkową podstawową implementację funkcji pozyskiwania zestawu testów.
    + Przeniesiono odwołania do sklearn.externals.joblib, aby zależeć bezpośrednio od joblib.
    + wprowadzenie nowego typu zadania autoML "image-instance-segmentation".
  + **azureml-automl-runtime**
    + Dodano początkową podstawową implementację funkcji pozyskiwania zestawu testów.
    + Gdy wszystkie ciągi w kolumnie tekstowej mają długość dokładnie 1 znaku, cechator tfIdf word-gram nie będzie działać, ponieważ jego tokenizator ignoruje ciągi z mniej niż 2 znakami. Bieżąca zmiana kodu pozwoli na obsługę tego przypadku użycia przez autoML.
    + wprowadzenie nowego typu zadania autoML "image-instance-segmentation".
  + **azureml-contrib-automl-dnn-nlp**
    + Początkowe ściągnięcie ściągnięcie dla nowego pakietu dnn-nlp
  + **azureml-contrib-automl-dnn-vision**
    + wprowadzenie nowego typu zadania rozwiązania AutoML "image-instance-segmentation".
  + **azureml-contrib-automl-pipeline-steps**
    + Ten nowy pakiet jest odpowiedzialny za tworzenie kroków wymaganych dla wielu modeli w scenariuszu trenowania/wnioskowania. — Kod train/inference jest również przesuowany do pakietu azureml.train.automl.runtime, dzięki czemu wszelkie przyszłe poprawki będą automatycznie dostępne za pośrednictwem wersji środowiska w trybie curated.
  + **azureml-contrib-dataset**
    + wprowadzenie nowego typu zadania rozwiązania AutoML "image-instance-segmentation".
  + **azureml-core**
    + Dodano początkową podstawową implementację funkcji pozyskiwania zestawu testów.
    + Naprawianie ostrzeżeń elementu xref dla dokumentacji w pakiecie azureml-core
    + Poprawki ciągu doc dla funkcji obsługi poleceń w zestawie SDK
    + Dodanie właściwości polecenia do właściwości RunConfiguration. Ta funkcja umożliwia użytkownikom uruchamianie rzeczywistego polecenia lub plików wykonywalnych na obliczeniach za pomocą zestawu AzureML SDK.
    + Użytkownicy mogą usunąć pusty eksperyment z danym identyfikatorem tego eksperymentu.
  + **azureml-dataprep**
    + Dodano obsługę zestawu danych dla platformy Spark sbudowaną za pomocą platformy Scala 2.12. To dodaje do istniejącej obsługi 2.11.
  + **azureml-mlflow**
    + AzureML-MLflow dodaje bezpieczne ochrony w skryptach zdalnych, aby uniknąć wcześniejszego zakończenia przesłanych przebiegów.
  + **azureml-pipeline-core**
    + Usunięto usterkę podczas ustawiania domyślnego potoku dla punktu końcowego potoku utworzonego za pomocą interfejsu użytkownika
  + **azureml-pipeline-steps**
    + Dodano eksperymentalną obsługę danych testowych do autoMLStep.
  + **azureml-tensorboard**
    + Naprawianie ostrzeżeń elementu xref dla dokumentacji w pakiecie azureml-core
  + **azureml-train-automl-client**
    + Dodano eksperymentalną obsługę danych testowych do autoMLStep.
    + Dodano początkową podstawową implementację funkcji pozyskiwania zestawu testów.
    + wprowadzenie nowego typu zadania autoML "image-instance-segmentation".
  + **azureml-train-automl-runtime**
    + Dodano początkową podstawową implementację funkcji pozyskiwania zestawu testów.
    + Napraw obliczenia nieprzetworzonych wyjaśnień najlepszego modelu rozwiązania AutoML, jeśli modele rozwiązania AutoML są trenowane przy użyciu validation_size automatycznego.
    + Przeniesiono odwołania do sklearn.externals.joblib, aby zależeć bezpośrednio od joblib.
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric() powinno zakończyć się szybciej
    + Ulepszono obsługę błędów w zestawie SDK usługi HyperDrive.
    +  Wycofane wszystkie klasy narzędzia do szacowania na rzecz używania polecenia ScriptRunConfig do konfigurowania przebiegów eksperymentu. Przestarzałe klasy obejmują:
        + MMLBase
        + Estymator
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + Wycofano użycie nccl i Gloo jako prawidłowych typów danych wejściowych dla klas narzędzia do szacowania na rzecz używania funkcji PyTorchConfiguration z scriptRunConfig.
    + Wycofane użycie mpi jako prawidłowego typu danych wejściowych dla klas narzędzia do szacowania na rzecz używania funkcji MpiConfiguration z poleceniem ScriptRunConfig.
    + Dodanie właściwości polecenia do polecenia runconfiguration. Ta funkcja umożliwia użytkownikom uruchamianie rzeczywistego polecenia lub plików wykonywalnych na obliczeniach za pomocą zestawu AzureML SDK.

    +  Wycofane wszystkie klasy narzędzia do szacowania na rzecz używania polecenia ScriptRunConfig do konfigurowania przebiegów eksperymentu. Przestarzałe klasy obejmują: + MMLBaseEstimator + Estimator + PyTorch + TensorFlow + Chainer + SKLearn
    + Wycofano użycie nccl i Gloo jako prawidłowego typu danych wejściowych dla klas narzędzia do szacowania na rzecz używania funkcji PyTorchConfiguration z poleceniem ScriptRunConfig. 
    + Wycofane użycie mpi jako prawidłowego typu danych wejściowych dla klas narzędzia do szacowania na rzecz używania funkcji MpiConfiguration z poleceniem ScriptRunConfig.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Azure Machine Learning Studio Notebooks Experience (listopadowa aktualizacja)
+ **Nowe funkcje**
   + Terminal natywny. Użytkownicy będą teraz mieć dostęp do zintegrowanego terminalu oraz do operacji Git za pośrednictwem [zintegrowanego terminalu.](./how-to-access-terminal.md)
  + Zduplikowany folder 
  + Lista rozwijana kosztów zasobów obliczeniowych 
  + Obliczanie trybu offline 

+ **Poprawki błędów i ulepszenia**
  + Ulepszone czasy ładowania stron
  + Większa wydajność 
  + Większa szybkość i niezawodność jądra
  + Przekazywanie dużych plików. Teraz możesz przekazać plik o rozmiarze >95 MB

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK dla języka Python w wersji 1.18.0
+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    +  Ulepszona obsługa krótkich szeregów czasu dzięki możliwości dopełnienia ich szumem gaussańskim.
  + **azureml-automl-runtime**
    + Zgłasza wyjątek ConfigException, jeśli kolumna DateTime ma wartość OutOfBoundsDatetime
    + Ulepszona obsługa krótkich szeregów czasu dzięki możliwości dopełnienia ich szumem gaussańskim.
    + Upewnienie się, że każda kolumna tekstowa może wykorzystać przekształcenie char-gram z zakresem n-gramowym na podstawie długości ciągów w tej kolumnie tekstowej
    + Udostępnianie pierwotnych wyjaśnień funkcji dla najlepszego trybu eksperymentów rozwiązania AutoML uruchomionych na lokalnych obliczeniach użytkownika
  + **azureml-core**
    + Przypnij pakiet: pyjwt, aby uniknąć ściągania przełomowych wersji w przyszłych wersjach.
    + Utworzenie eksperymentu spowoduje zwrócenie aktywnego lub ostatniego zarchiwizowanego eksperymentu o takiej samej nazwie, jeśli taki eksperyment istnieje lub nowy eksperyment.
    + Wywołanie get_experiment według nazwy zwróci aktywny lub ostatni zarchiwizowane eksperymenty z podaną nazwą.
    + Użytkownicy nie mogą zmienić nazwy eksperymentu podczas jego ponownego aktywowania.
    + Ulepszono komunikat o błędzie, aby zawierał potencjalne poprawki, gdy zestaw danych jest niepoprawnie przekazywany do eksperymentu (np. ScriptRunConfig). 
    + Ulepszono `OutputDatasetConfig.register_on_complete` dokumentację programu , aby uwzględnić zachowanie tego, co się stanie, gdy nazwa już istnieje.
    + Określanie nazw danych wejściowych i wyjściowych zestawu danych, które mogą kolidować ze wspólnymi zmiennymi środowiskowym, spowoduje teraz ostrzeżenie
    + Podczas rejestrowania magazynów danych należy `grant_workspace_access` ponownie określać wartość parametru. Ustaw go na , `True` aby uzyskać dostęp do danych za siecią wirtualną z Machine Learning Studio.
      [Dowiedz się więcej](./how-to-enable-studio-virtual-network.md)
    + Interfejs API połączonej usługi został udoskonalony. Zamiast dostarczać identyfikator zasobu, mamy 3 oddzielne parametry, sub_id, rg i nazwa zdefiniowane w konfiguracji.
    + Aby umożliwić klientom samodzielne rozwiązywanie problemów z uszkodzeniem tokenu, włącz synchronizację tokenów obszaru roboczego jako metodę publiczną.
    + Ta zmiana umożliwia korzystanie z pustego ciągu jako wartości dla script_param
  + **azureml-train-automl-client**
    +  Ulepszona obsługa krótkich szeregów czasu dzięki możliwości dopełnienia ich szumem gaussańskim.
  + **azureml-train-automl-runtime**
    + Throw ConfigException if a DateTime column has OutOfBoundsDatetime value (Wyjątek ConfigException, jeśli kolumna DateTime ma wartość OutOfBoundsDatetime)
    + Dodano obsługę dostarczania nieprzetworzonych wyjaśnień funkcji dla najlepszego modelu eksperymentów rozwiązania AutoML uruchomionych na lokalnych obliczeniach użytkownika
    + Ulepszona obsługa krótkich szeregów czasu dzięki możliwości dopełnienia ich szumem gaussańskim.
  + **azureml-train-core**
    + Ta zmiana umożliwia korzystanie z pustego ciągu jako wartości dla script_param
  + **azureml-train-restclients-hyperdrive**
    + Zmieniono readme, aby zaoferować więcej kontekstu
  + **azureml-widgets**
    + Dodanie obsługi ciągów do biblioteki wykresów/współrzędnych równoległych dla widżetu.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Etykietowanie danych dla segmentacji wystąpienia obrazu (adnotacja wielokąta) (wersja zapoznawcza)

Typ projektu segmentacji wystąpienia obrazu (adnotacje wielokąta) w etykietowaniu danych jest teraz dostępny, dzięki czemu użytkownicy mogą rysować wielokąty wokół obiektów na obrazach i dodawać do nich adnotacje. Użytkownicy będą mogli przypisać klasę i wielokąt do każdego obiektu, który będzie cię interesować na obrazie.

Dowiedz się więcej o [etykietowaniu wystąpienia obrazu.](how-to-label-images.md)



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK dla języka Python w wersji 1.17.0
+ **nowe przykłady**
  + Nowe repozytorium przykładów oparte na społeczności jest dostępne pod https://github.com/Azure/azureml-examples
+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Rozwiązano problem, który get_output zgłaszać błąd XGBoostError.
  + **azureml-automl-runtime**
    + Funkcje oparte na czasie/kalendarzu utworzone przez automl będą teraz mieć prefiks .
    + Naprawiono błąd IndexError występujący podczas trenowania usługi StackEnsemble dla zestawów danych klasyfikacji z włączoną dużą liczbą klas i próbkowania podrzędnego.
    + Rozwiązano problem, który mógł powodować, że przewidywania VotingRegressor były niedokładne po ponownego dopasowyniu modelu.
  + **azureml-core**
    + Dodano dodatkowe szczegóły dotyczące relacji między konfiguracją wdrożenia usługi AKS Azure Kubernetes Service pojęciami.
    + Obsługa etykiet klienta środowiska. Użytkownik może oznaczać środowiska etykietami i odwoływać się do nich za pomocą etykiet.
  + **azureml-dataprep**
    + Lepszy komunikat o błędzie podczas korzystania z obecnie nieobsługiwanej platformy Spark w wersji Scala 2.12.
  + **azureml-explain-model**
    + Pakiet azureml-explain-model jest oficjalnie przestarzały
  + **azureml-mlflow**
    + Rozwiązano usterkę w pliku mlflow.projects.run względem zaplecza azureml, która oznaczała, że stan finalizowania nie był prawidłowo obsługiwany.
  + **azureml-pipeline-core**
    + Dodawania obsługi tworzenia, tworzenia listy i uzyskania harmonogramu potoku na podstawie jednego punktu końcowego potoku.
    +  Ulepszono dokumentację PipelineData.as_dataset z nieprawidłowym przykładem użycia — nieprawidłowe użycie PipelineData.as_dataset spowoduje teraz wygenerowanie błędu ValueException
    + Zmieniono notes potoków HyperDriveStep w celu zarejestrowania najlepszego modelu w ramach rozwiązania PipelineStep bezpośrednio po uruchomieniu funkcji HyperDriveStep.
  + **azureml-pipeline-steps**
    + Zmieniono notes potoków HyperDriveStep w celu zarejestrowania najlepszego modelu w ramach rozwiązania PipelineStep bezpośrednio po uruchomieniu funkcji HyperDriveStep.
  + **azureml-train-automl-client**
    + Rozwiązano problem, który get_output zgłaszać błąd XGBoostError.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Azure Machine Learning Studio Notebooks Experience (aktualizacja z października)
+ **Nowe funkcje**
  + [Pełna obsługa sieci wirtualnej](./how-to-enable-studio-virtual-network.md)
  + [Tryb koncentracji uwagi](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Zapisywanie notesów Ctrl-S
  + Numery wiersza

+ **Poprawki błędów i ulepszenia**
  + Poprawa szybkości i niezawodności jądra
  + Aktualizacje interfejsu użytkownika widżetu Jupyter

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK dla języka Python w wersji 1.16.0
+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Usługi AKSWebservice i AKSEndpoints obsługują teraz limity zasobów procesora CPU i pamięci na poziomie zasobnika. Tych opcjonalnych limitów można użyć przez ustawienie `--cpu-cores-limit` flag i w odpowiednich `--memory-gb-limit` wywołaniach interfejsu wiersza polecenia
  + **azureml-core**
    + Przypinanie głównych wersji bezpośrednich zależności azureml-core
    + Usługi AKSWebservice i AKSEndpoints obsługują teraz limity zasobów procesora CPU i pamięci na poziomie zasobnika. Więcej informacji na temat [zasobów i limitów kubernetes](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)
    + Zaktualizowano run.log_table, aby umożliwić rejestrowane poszczególne wiersze.
    + Dodano `Run.get(workspace, run_id)` statyczną metodę do pobierania uruchomienia tylko przy użyciu obszaru roboczego 
    + Dodano metodę `Workspace.get_run(run_id)` wystąpienia w celu pobrania uruchomienia w obszarze roboczym
    + Wprowadzenie właściwości polecenia w konfiguracji uruchamiania, która umożliwi użytkownikom przesyłanie poleceń zamiast & argumentów.
  + **azureml-interpret**
    + naprawiono wyjaśnienie zachowania flagi is_raw w pliku azureml-interpret
  + **azureml-sdk**
    + `azureml-sdk` oficjalnie obsługuje język Python 3.8.
  + **azureml-train-core**
    + Dodawanie środowiska curated TensorFlow 2.3
    + Wprowadzenie właściwości polecenia w konfiguracji uruchamiania, która umożliwi użytkownikom przesyłanie poleceń zamiast & argumentów.
  + **azureml-widgets**
    + Przeprojektowany interfejs widżetu uruchamiania skryptu.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK dla języka Python w wersji 1.15.0
+ **Poprawki błędów i ulepszenia**
  + **azureml-contrib-interpret**
    + Objaśnienie DLA zostało przeniesione z pakietu azureml-contrib-interpret w celu usunięcia pakietu interpret-community i objaśnienia obrazów z pakietu azureml-contrib-interpret
    + pulpit nawigacyjny wizualizacji usunięty z pakietu azureml-contrib-interpret, klient wyjaśnienia przeniesiony do pakietu azureml-interpret i przestarzały w pakiecie azureml-contrib-interpret oraz notesach zaktualizowanych w celu odzwierciedlenia ulepszonego interfejsu API
    + naprawianie opisów pakietów pypi dla azureml-interpret, azureml-explain-model, azureml-contrib-interpret i azureml-tensorboard
  + **azureml-contrib-notebook**
    + Przypnij zależność nbcovert do < 6, aby papermill 1.x nadal działał.
  + **azureml-core**
    + Dodano parametry do konstruktora TensorflowConfiguration i MpiConfiguration, aby umożliwić bardziej usprawnione inicjowanie atrybutów klasy bez konieczności ustawienia poszczególnych atrybutów przez użytkownika. Dodano klasę PyTorchConfiguration do konfigurowania rozproszonych zadań PyTorch w pliku ScriptRunConfig.
    + Przypnij wersję narzędzia azure-mgmt-resource, aby naprawić błąd uwierzytelniania.
    + Obsługa wdrażania trytonu bez kodu
    + Katalogi wyjściowe określone w Run.start_logging() będą teraz śledzone podczas korzystania z uruchamiania w scenariuszach interaktywnych. Śledzone pliki będą widoczne w programie ML Studio po wywołaniu funkcji Run.complete()
    + Kodowanie pliku można teraz określić podczas tworzenia zestawu danych za `Dataset.Tabular.from_delimited_files` pomocą `Dataset.Tabular.from_json_lines_files` argumentu i przez przekazanie `encoding` argumentu . Obsługiwane kodowanie to "utf8", "iso88591", "latin1", "ascii", utf16", "utf32", "utf8bom" i "windows1252".
    + Poprawka usterki, gdy obiekt środowiska nie jest przekazywany do konstruktora ScriptRunConfig.
    + Zaktualizowano run.cancel(), aby umożliwić anulowanie lokalnego uruchomienia z innej maszyny.
  + **azureml-dataprep**
    +  Rozwiązano problemy z limitem czasu instalacji zestawu danych.
  + **azureml-explain-model**
    + naprawianie opisów pakietów pypi dla azureml-interpret, azureml-explain-model, azureml-contrib-interpret i azureml-tensorboard
  + **azureml-interpret**
    + pulpit nawigacyjny wizualizacji usunięty z pakietu azureml-contrib-interpret, klient wyjaśnienia przeniesiony do pakietu azureml-interpret i przestarzały w pakiecie azureml-contrib-interpret oraz notesach zaktualizowanych w celu odzwierciedlenia ulepszonego interfejsu API
    + Zaktualizowano pakiet azureml-interpret, aby był zależny od wersji interpret-community 0.15.0
    + naprawianie opisów pakietów pypi dla azureml-interpret, azureml-explain-model, azureml-contrib-interpret i azureml-tensorboard
  + **azureml-pipeline-core**
    +  Rozwiązano problem z potokiem, który pozwalał systemowi przestać odpowiadać po wywołaniu z parametrem ustawionym `OutputFileDatasetConfig` `register_on_complete` na `name` wcześniejszą nazwę zestawu danych.
  + **azureml-pipeline-steps**
    + Usunięto nieodświeżone notesy databricks.
  + **azureml-tensorboard**
    + Naprawiono opisy pakietów pypi dla azureml-interpret, azureml-explain-model, azureml-contrib-interpret i azureml-tensorboard
  + **azureml-train-automl-runtime**
    + pulpit nawigacyjny wizualizacji usunięty z pakietu azureml-contrib-interpret, klient wyjaśnienia przeniesiony do pakietu azureml-interpret i przestarzały w pakiecie azureml-contrib-interpret oraz notesach zaktualizowanych w celu odzwierciedlenia ulepszonego interfejsu API
  + **azureml-widgets**
    + pulpit nawigacyjny wizualizacji usunięty z pakietu azureml-contrib-interpret, klient wyjaśnienia przeniesiony do pakietu azureml-interpret i przestarzały w pakiecie azureml-contrib-interpret oraz notesach zaktualizowanych w celu odzwierciedlenia ulepszonego interfejsu API

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK dla języka Python w wersji 1.14.0
+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Profilowanie siatki zostało usunięte z zestawu SDK i nie jest już obsługiwane.
  + **azureml-accel-models**
    + Pakiet azureml-accel-models obsługuje teraz platformę Tensorflow 2.x
  + **azureml-automl-core**
    + Dodano obsługę błędów w get_output przypadku, gdy lokalne wersje pandas/sklearn nie są zgodne z wersjami używanymi podczas trenowania
  + **azureml-automl-runtime**
    + Usunięto usterkę, która występowała w przypadku niepowodzenia iteracji AutoArima z błędem PredictionException i komunikatem: "Wystąpił dyskretny błąd podczas przewidywania".
  + **azureml-cli-common**
    + Profilowanie siatki zostało usunięte z zestawu SDK i nie jest już obsługiwane.
  + **azureml-contrib-server**
    + Zaktualizuj opis pakietu dla strony przeglądu pypi.
  + **azureml-core**
    + Profilowanie siatki zostało usunięte z zestawu SDK i nie jest już obsługiwane.
    + Zmniejsz liczbę komunikatów o błędach w przypadku niepowodzenia pobierania obszaru roboczego.
    + Nie pokazuj ostrzeżenia w przypadku niepowodzenia pobierania metadanych
    + Nowy krok Kusto i docelowy obiekt obliczeniowy Kusto.
    + Aktualizacja dokumentu dla parametru SKU. Usuwanie sku w funkcji aktualizacji obszaru roboczego w interfejsie wiersza polecenia i zestawie SDK.
    + Zaktualizuj opis pakietu dla strony przeglądu pypi.
    + Zaktualizowano dokumentację środowisk AzureML.
    + Uwidocznij ustawienia zasobów zarządzanych przez usługę dla obszaru roboczego usługi AML w zestawie SDK.
  + **azureml-dataprep**
    + Włącz uprawnienie do wykonywania dla plików dla instalacji zestawu danych.
  + **azureml-mlflow**
    + Zaktualizowano dokumentację i przykłady notesów platformy AzureML MLflow 
    + Nowa obsługa projektów MLflow z zapleczami AzureML
    + Obsługa rejestru modeli MLflow
    + Dodano obsługę kontroli RBAC platformy Azure AzureML-MLflow operacji 
    
  + **azureml-pipeline-core**
    + Ulepszono dokumentację metod PipelineOutputFileDataset.parse_*.
    + Nowy krok Kusto i docelowy obiekt obliczeniowy Kusto.
    + Podano właściwość Swaggerurl dla jednostki pipeline-endpoint za pośrednictwem tego użytkownika, aby zobaczyć definicję schematu dla opublikowanego punktu końcowego potoku.
  + **azureml-pipeline-steps**
    + Nowy krok Kusto i docelowy obiekt obliczeniowy Kusto.
  + **azureml-telemetry**
    + Zaktualizuj opis pakietu dla strony przeglądu pypi.
  + **azureml-train**
    + Zaktualizuj opis pakietu dla strony przeglądu pypi.
  + **azureml-train-automl-client**
    + Dodano obsługę błędów w get_output przypadku, gdy lokalne wersje pandas/sklearn nie są zgodne z wersjami używanymi podczas trenowania
  + **azureml-train-core**
    + Zaktualizuj opis pakietu dla strony przeglądu pypi.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK dla języka Python w wersji 1.13.0
+ **Funkcje w wersji zapoznawczej**
  + **azureml-core** Dzięki nowej funkcji wyjściowych zestawów danych można zapisywać dane w magazynie w chmurze, w tym Blob, ADLS Gen 1, ADLS Gen 2 i FileShare. Możesz skonfigurować, gdzie mają być wyprowadzane dane, jak dane wyjściowe (za pośrednictwem instalacji lub przekazywania), czy rejestrować dane wyjściowe do ponownego użycia i udostępniania w przyszłości oraz bezproblemowo przekazywać dane pośrednie między krokami potoku. Zapewnia to odtwarzalność, udostępnianie, zapobiega duplikowaniu danych oraz zapewnia efektywność kosztową i zwiększa produktywność. [Dowiedz się, jak z niego korzystać](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)
    
+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Dodano validated_{platform}_requirements.txt do przypinania wszystkich zależności pip dla automl.
    + Ta wersja obsługuje modele większe niż 4 Gb.
    + Uaktualniono zależności autoML: `scikit-learn` (teraz 0.22.1), `pandas` (teraz 0.25.1) `numpy` (teraz 1.18.2).
  + **azureml-automl-runtime**
    + Ustaw układ horovod dla tekstowej kompresji DNN, aby zawsze używać kompresji fp16.
    + Ta wersja obsługuje modele większe niż 4 Gb.
    + Rozwiązano problem, który kończył się niepowodzeniem automatycznegoml z importError: nie można zaimportować nazwy `RollingOriginValidator` .
    + Uaktualniono zależności autoML: `scikit-learn` (teraz 0.22.1), `pandas` (teraz 0.25.1) `numpy` (teraz 1.18.2).
  + **azureml-contrib-automl-dnn-forecasting**
    + Uaktualniono zależności autoML: `scikit-learn` (teraz 0.22.1), `pandas` (teraz 0.25.1) `numpy` (teraz 1.18.2).
  + **azureml-contrib-fairness**
    + Podaj krótki opis azureml-contrib-fairness.
  + **azureml-contrib-pipeline-steps**
    + Dodano komunikat informujący, że ten pakiet jest przestarzały, a użytkownik powinien zamiast tego użyć instrukcji azureml-pipeline-steps.
  + **azureml-core**
    + Dodano polecenie klucza listy dla obszaru roboczego.
    + Dodaj parametr tags w zestawie SDK obszaru roboczego i interfejsie wiersza polecenia.
    + Usunięto usterkę, przez która przesyłanie podrzędnego uruchomienia za pomocą zestawu danych nie powiodło się z powodu `TypeError: can't pickle _thread.RLock objects` błędu .
    + Dodanie page_count domyślnej/dokumentacji dla funkcji Lista modeli().
    + Zmodyfikuj&SDK interfejsu wiersza polecenia, aby przyjmować parametr adbworkspace i dodać obszar roboczy adb lin/unlink runner.
    + Naprawiono usterkę w pliku Dataset.update, która powodowała, że najnowsza wersja zestawu danych była aktualizowana, a nie była wywoływana wersja aktualizacji zestawu danych. 
    + Naprawiono usterkę w Dataset.get_by_name, która pokazywała tagi dla najnowszej wersji zestawu danych nawet po pobraniu określonej starszej wersji.
  + **azureml-interpret**
    + Dodano dane wyjściowe prawdopodobieństwa do objaśnień oceniania shap w funkcji azureml-interpret na podstawie shap_values_output z oryginalnego objaśnienia.
  + **azureml-pipeline-core**
    + Ulepszona `PipelineOutputAbstractDataset.register` dokumentacja programu .
  + **azureml-train-automl-client**
    + Uaktualniono zależności autoML: `scikit-learn` (teraz 0.22.1), `pandas` (teraz 0.25.1) `numpy` (teraz 1.18.2).
  + **azureml-train-automl-runtime**
    + Uaktualniono zależności autoML: `scikit-learn` (teraz 0.22.1), `pandas` (teraz 0.25.1) `numpy` (teraz 1.18.2).
  + **azureml-train-core**
    + Użytkownicy muszą teraz podać prawidłową hyperparameter_sampling arg podczas tworzenia funkcji HyperDriveConfig. Ponadto dokumentacja funkcji HyperDriveRunConfig została edytowana w celu poinformowania użytkowników o cofaniu funkcji HyperDriveRunConfig.
    + Przywracanie domyślnej wersji PyTorch do wersji 1.4.
    + Dodanie PyTorch 1.6 & obrazów i środowiska wsadowego tensorflow 2.2.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Azure Machine Learning Studio Notebooks Experience (aktualizacja z sierpnia)
+ **Nowe funkcje**
  + Nowa strona docelowa Wprowadzenie 
  
+ **Funkcje w wersji zapoznawczej**
    + Funkcja Zbierz w notesach. Dzięki funkcji [Zbierz](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) użytkownicy mogą teraz łatwo czyścić notesy. Funkcja Gather korzysta z automatycznej analizy zależności notesu, zapewniając, że podstawowy kod jest przechowywany, ale usuwa wszystkie nieistotne elementy.

+ **Poprawki błędów i ulepszenia**
  + Poprawa szybkości i niezawodności
  + Usunięto usterki w trybie ciemnym
  + Usunięto usterki przewijania danych wyjściowych
  + Wyszukiwanie przykładowe wyszukuje teraz całą zawartość wszystkich plików w Azure Machine Learning przykładowych notesów
  + Można teraz uruchamiać wielo wierszowe komórki R
  + Opcja "Ufam zawartości tego pliku" jest teraz automatycznie sprawdzana po pierwszym
  + Ulepszone okno dialogowe rozwiązywania konfliktów z nową opcją "Kopiuj"
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK dla języka Python w wersji 1.12.0

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Dodaj image_name i image_label do funkcji Model.package(), aby umożliwić zmianę nazwy sbudowaną obrazu pakietu.
  + **azureml-automl-core**
    + Automatyczneml zgłasza nowy kod błędu z dataprep, gdy zawartość jest modyfikowana podczas odczytywania.
  + **azureml-automl-runtime**
    + Dodano alerty dla użytkownika, gdy dane zawierają brakujące wartości, ale cechowanie jest wyłączone.
    + Usunięto podrzędne błędy uruchamiania, gdy dane zawierają nan i cechowanie są wyłączone.
    + Automatyczneml zgłasza nowy kod błędu z dataprep, gdy zawartość jest modyfikowana podczas odczytywania.
    + Zaktualizowano normalizację prognozowania metryk według ziarna.
    + Ulepszone obliczanie kwantyli prognoz, gdy funkcje wyszukiwania są wyłączone.
    + Poprawiono obsługę rozrzedzianej macierzy bool podczas obliczania wyjaśnień po automatycznym ujrzeniuml.
  + **azureml-core**
    + Nowa metoda `run.get_detailed_status()` zawiera teraz szczegółowe wyjaśnienie bieżącego stanu uruchomienia. Obecnie wyświetlane jest tylko wyjaśnienie `Queued` stanu.
    + Dodaj image_name i image_label do funkcji Model.package(), aby umożliwić zmianę nazwy sbudowaną obrazu pakietu.
    + Nowa metoda `set_pip_requirements()` do ustawienia całej sekcji pip [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies) jednocześnie.
    + Włącz rejestrowanie magazynu danych bez ADLS Gen2 poświadczeń.
    + Ulepszono komunikat o błędzie podczas próby pobrania lub instalacji nieprawidłowego typu zestawu danych.
    + Zaktualizuj przykładowy notes filtru zestawu danych szeregów czasu, aby uzyskać więcej przykładów partition_timestamp, które zapewniają optymalizację filtru.
    + Zmień zestaw SDK i interfejs wiersza polecenia, aby akceptowały parametry subscriptionId, resourceGroup, workspaceName i peConnectionName zamiast ArmResourceId podczas usuwania połączenia z prywatnym punktem końcowym.
    + Experimental Decorator pokazuje nazwę klasy w celu ułatwienia identyfikacji.
    + Opisy zasobów wewnątrz modeli nie są już generowane automatycznie na podstawie uruchomienia.
  + **azureml-datadrift**
    + Oznacz create_from_model API w dataDriftDetector jako przestarzały.
  + **azureml-dataprep**
    + Ulepszono komunikat o błędzie podczas próby pobrania lub instalacji nieprawidłowego typu zestawu danych.
  + **azureml-pipeline-core**
    + Usunięto usterkę podczas deserializacji grafu potoku zawierającego zarejestrowane zestawy danych.
  + **azureml-pipeline-steps**
    + Krok RScriptStep obsługuje funkcję RSection z pliku azureml.core.environment.
    + Usunięto passthru_automl_config z publicznego interfejsu API i `AutoMLStep` przekonwertowano go na parametr tylko wewnętrzny.
  + **azureml-train-automl-client**
    + Usunięto lokalne asynchroniczne, zarządzane środowisko uruchamiane z autoML. Wszystkie uruchomienia lokalne będą uruchamiane w środowisku, z których uruchomiono przebieg.
    + Rozwiązano problemy z migawkami podczas przesyłania autoML bez skryptów dostarczonych przez użytkownika.
    + Usunięto podrzędne błędy uruchamiania, gdy dane zawierają elementy nan i cechowanie są wyłączone.
  + **azureml-train-automl-runtime**
    + AutoML zgłasza nowy kod błędu z dataprep, gdy zawartość jest modyfikowana podczas odczytywania.
    + Rozwiązano problemy z migawkami podczas przesyłania autoML bez skryptów dostarczonych przez użytkownika.
    + Usunięto podrzędne błędy uruchamiania, gdy dane zawierają elementy nan i cechowanie są wyłączone.
  + **azureml-train-core**
    + Dodano obsługę określania opcji pip (na przykład --extra-index-url) w pliku wymagań pip przekazanym do [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator) parametru `pip_requirements_file` through.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK dla języka Python w wersji 1.11.0

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Naprawiono platformę modelu i platformę modelu, które nie zostały przekazane w obiekcie run w ścieżce rejestracji modelu interfejsu wiersza polecenia
    + Naprawiono polecenie amlcompute identity show interfejsu wiersza polecenia w celu pokazania identyfikatora dzierżawy i identyfikatora podmiotu zabezpieczeń 
  + **azureml-train-automl-client**
    + Dodano get_best_child () do funkcji AutoMLRun w celu pobrania najlepszego podrzędnego uruchomienia dla uruchomienia rozwiązania AutoML bez pobierania skojarzonego modelu.
    + Dodano obiekt ModelProxy, który umożliwia uruchamianie prognozowania w zdalnym środowisku trenowania bez lokalnego pobierania modelu.
    + Nieobsługiwane wyjątki w uchwyceniu automatycznego oprogramowania wskazują teraz na znane problemy na stronie HTTP, na której można znaleźć więcej informacji o błędach.
  + **azureml-core**
    + Nazwy modeli mogą mieć 255 znaków.
    + Environment.get_image_details() typ obiektu zwracanego został zmieniony. `DockerImageDetails` klasa zastąpiona `dict` , szczegóły obrazu są dostępne z nowych właściwości klasy. Zmiany są zgodne z poprzednimi wersjami.
    + Naprawiono usterkę w Environment.from_pip_requirements() w celu zachowania struktury zależności
    + Usunięto usterkę, która log_list się niepowodzeniem, jeśli na tej samej liście znajdują się int i double.
    + Podczas włączania łącza prywatnego w istniejącym obszarze roboczym należy pamiętać, że jeśli z obszarem roboczym są skojarzone obiekty docelowe obliczeń, te obiekty docelowe nie będą działać, jeśli nie znajdują się za tą samą siecią wirtualną co prywatny punkt końcowy obszaru roboczego.
    + Ta opcja `as_named_input` jest opcjonalna w przypadku używania zestawów danych w eksperymentach oraz `as_mount` dodawana i `as_download` do `FileDataset` . Nazwa wejściowa zostanie wygenerowana automatycznie, jeśli `as_mount` zostanie `as_download` wywołana nazwa lub .
  + **azureml-automl-core**
    + Nieobsługiwane wyjątki w uchwyceniu automatycznego oprogramowania wskazują teraz na znane problemy na stronie HTTP, na której można znaleźć więcej informacji o błędach.
    + Dodano get_best_child () do funkcji AutoMLRun w celu pobrania najlepszego uruchomienia podrzędnego dla uruchomienia rozwiązania AutoML bez pobierania skojarzonego modelu.
    + Dodano obiekt ModelProxy, który umożliwia uruchamianie przewidywania lub prognozowania w zdalnym środowisku trenowania bez lokalnego pobierania modelu.
  + **azureml-pipeline-steps**
    + Dodano `enable_default_model_output` `enable_default_metrics_output` flagi i do . `AutoMLStep` Te flagi mogą służyć do włączania/wyłączania domyślnych danych wyjściowych.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK dla języka Python w wersji 1.10.0

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Jeśli w przypadku korzystania z automl ścieżka jest przekazywana do obiektu AutoMLConfig i jeszcze nie istnieje, zostanie ona utworzona automatycznie.
    + Użytkownicy mogą teraz określać częstotliwość szeregów czasu dla zadań prognozowania przy użyciu `freq` parametru .
  + **azureml-automl-runtime**
    + Jeśli w przypadku korzystania z automl ścieżka jest przekazywana do obiektu AutoMLConfig i jeszcze nie istnieje, zostanie ona utworzona automatycznie.
    + Użytkownicy mogą teraz określać częstotliwość szeregów czasu dla zadań prognozowania przy użyciu `freq` parametru .
    + Funkcja prognozowania automatycznego oprogramowania obsługuje teraz ocenę stopniową, która ma zastosowanie do przypadku użycia, że długość zestawu testowego lub walidacji jest większa niż horyzont wejściowy, a znana wartość y_pred jest używana jako kontekst prognozowania.
  + **azureml-core**
    + Komunikaty ostrzegawcze będą wyświetlane, jeśli żadne pliki nie zostały pobrane z magazynu danych w uruchomieniu.
    + Dodano dokumentację `skip_validation` dla do . `Datastore.register_azure_sql_database method`
    + Użytkownicy muszą uaktualnić zestaw SDK do wersji 1.10.0 lub nowszą w celu utworzenia automatycznie zatwierdzonego prywatnego punktu końcowego. Obejmuje to zasób notesu, który można wykorzystać za siecią wirtualną.
    + Uwidocznij informacje NotebookInfo w odpowiedzi na polecenie get workspace.
    + Zmiany w wywołaniach w celu listy docelowych obiektów obliczeniowych i uzyskania docelowego obiektu obliczeniowego w przypadku uruchomienia zdalnego zakończyły się powodzeniem. Funkcje zestawu SDK służące do uzyskania docelowego obiektu obliczeniowego i listy docelowych zasobów obliczeniowych obszaru roboczego będą teraz działać w zdalnych przebiegach.
    + Dodaj komunikaty o cofaniu wersji do opisów klas dla klas azureml.core.image.
    + Jeśli tworzenie prywatnego punktu końcowego obszaru roboczego nie powiedzie się, należy zgłosić wyjątek i wyczyścić obszar roboczy oraz zasoby zależne.
    + Obsługa uaktualniania wersji SKU obszaru roboczego w metodzie aktualizacji obszaru roboczego.
  + **azureml-datadrift**
    + Zaktualizuj wersję pakietu matplotlib z 3.0.2 do 3.2.1 w celu obsługi języka Python 3.8.
  + **azureml-dataprep**
    + Dodano obsługę internetowych źródeł danych adresu URL za pomocą `Range` lub `Head` żądania. 
    + Ulepszona stabilność instalacji i pobierania zestawu danych plików.
  + **azureml-train-automl-client**
    + Rozwiązano problemy związane z usuwaniem `RequirementParseError` z narzędzi setuptools.
    + Użyj platformy Docker zamiast conda dla przebiegów lokalnych przesłanych przy użyciu compute_target ='local'"
    + Poprawiono czas trwania iteracji wydrukowany w konsoli. Wcześniej czas trwania iteracji był czasami drukowany jako czas zakończenia działania minus czas tworzenia. Poprawiono go tak, aby był równy czasowi zakończenia uruchomienia minus czas rozpoczęcia uruchomienia.
    + Jeśli w przypadku korzystania z automl ścieżka zostanie przekazana do obiektu AutoMLConfig i jeszcze nie istnieje, zostanie ona utworzona automatycznie.
    + Użytkownicy mogą teraz określać częstotliwość szeregów czasu dla zadań prognozowania przy użyciu `freq` parametru .
  + **azureml-train-automl-runtime**
    + Ulepszono dane wyjściowe konsoli, gdy wyjaśnienia najlepszych modeli nie powiodły się.
    + Zmieniono nazwę parametru wejściowego na "blocked_models", aby usunąć poufny termin.
      + Zmieniono nazwę parametru wejściowego na "allowed_models", aby usunąć poufny termin.
    + Użytkownicy mogą teraz określać częstotliwość szeregów czasu dla zadań prognozowania przy użyciu `freq` parametru .

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK dla języka Python w wersji 1.9.0

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Zastąpiono get_model_path() zmienną AZUREML_MODEL_DIR środowiskowej w automatycznie wygenerowanym skrypcie oceniania autoML. Dodano również telemetrię do śledzenia błędów podczas init().
    + Usunięto możliwość określania `enable_cache` w ramach konfiguracji AutoMLConfig
    + Usunięto usterkę, która powodować, że przebiegi mogą się nie powieść z błędami usługi podczas określonych przebiegów prognozowania
    + Ulepszono obsługę błędów wokół określonych modeli podczas `get_output`
    + Naprawiono wywołanie do fitted_model.fit(X, y) dla klasyfikacji za pomocą transformatora y
    + Włączono dostosowany imputer wypełniania do przodu dla zadań prognozowania
    + Zamiast prognozowania parametrów w formacie dict zostanie użyta nowa klasa ForecastingParameters
    + Ulepszono autowysekcję opóźnienia docelowego
    + Dodano ograniczoną dostępność wielowęzłowej, rozproszonej cechowania z wieloma procesorami GPU za pomocą funkcji BERT
  + **azureml-automl-runtime**
    + Teraz zamiast mnożenia, modelowanie addytywne sezonowości jest addytywne.
    + Rozwiązano problem, który dotyczył sytuacji, gdy krótkie ziarna, z częstotliwościami różniącymi się od tych o długich ziarnach, powodować nieudane przebiegi.
  + **azureml-contrib-automl-dnn-vision**
    + Zbieranie statystyk systemowych/gpu i średnich dzienników na czas trenowania i oceniania
  + **azureml-contrib-dostęp**
    + Dodano obsługę flagi enable-app-insights w funkcji ManagedInferencing
  + **azureml-core**
    + Parametr weryfikacji dla tych interfejsów API umożliwia pominięcie walidacji, gdy źródło danych nie jest dostępne z bieżącego wystąpienia obliczeniowego.
      + TabularDataset.time_before(end_time, include_boundary=True, validate=True)
      + TabularDataset.time_after(start_time, include_boundary=True, validate=True)
      + TabularDataset.time_recent(time_delta, include_boundary=True, validate=True)
      + TabularDataset.time_between(start_time, end_time, include_boundary=True, validate=True)
    + Dodano obsługę filtrowania struktury dla listy modeli i dodano przykład NCD AutoML w notesie
    + W Datastore.register_azure_blob_container i Datastore.register_azure_file_share (tylko opcje, które obsługują token SAS), zaktualizowaliśmy ciągi doc dla pola, aby uwzględnić minimalne wymagania dotyczące uprawnień dla typowych scenariuszy odczytu `sas_token` i zapisu.
    + Wycofane _with_auth w funkcji ws.get_mlflow_tracking_uri()
  + **azureml-mlflow**
    + Dodanie obsługi wdrażania lokalnych modeli file:// za pomocą AzureML-MLflow
    + Wycofane _with_auth w funkcji ws.get_mlflow_tracking_uri()
  + **azureml-opendatasets**
    + Ostatnio opublikowane zestawy danych śledzenia covid-19 są teraz dostępne z zestawem SDK
  + **azureml-pipeline-core**
    + Ostrzeżenie o wylogowaniu, gdy wartość "azureml-defaults" nie jest uwzględniona w ramach zależności pip
    + Ulepszanie renderowania notek.
    + Dodano obsługę podziałów wierszy w cudzysłowy podczas analizowania rozdzielonych plików do zestawu PipelineOutputFileDataset.
    + Klasa PipelineDataset jest przestarzała. Aby uzyskać więcej informacji, zobacz https://aka.ms/dataset-deprecation. Aby dowiedzieć się, jak używać zestawu danych z potokiem, zobacz https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline-steps**
    + Aktualizacje dokumentu azureml-pipeline-steps.
    +  Dodano obsługę w pliku ParallelRunConfig dla użytkowników w celu definiowania środowisk w tekście w pozostałej części konfiguracji lub `load_yaml()` w oddzielnym pliku
  + **azureml-train-automl-client**.
    + Usunięto możliwość określania `enable_cache` w ramach autoMLConfig
  + **azureml-train-automl-runtime**
    + Dodano ograniczoną dostępność cechowania rozproszonego z wieloma węzłami i wieloma procesorami GPU za pomocą funkcji BERT.
    + Dodano obsługę błędów dla niezgodnych pakietów w przebiegach zautomatyzowanego uczenia maszynowego opartych na bazie ADB.
  + **azureml-widgets**
    + Aktualizacje dokumentu dla elementów azureml-widget.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK dla języka Python w wersji 1.8.0
  
  + **Funkcje w wersji zapoznawczej**
    + **azureml-contrib-fairness** Pakiet `azureml-contrib-fairness` ten zapewnia integrację między pakietem Fairlearn i [](https://fairlearn.github.io) fairness mitigation package open source fairness assessment and jego Azure Machine Learning studio. W szczególności pakiet umożliwia przekazywane pulpity nawigacyjne oceny uczciwości modelu jako część uruchomienia usługi AzureML i pojawiające się w Azure Machine Learning studio

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Obsługa uzyskiwania dzienników kontenera init.
    + Dodano nowe polecenia interfejsu wiersza polecenia do zarządzania usługą ComputeInstance
  + **azureml-automl-core**
    + Użytkownicy mogą teraz włączyć iterację zespołów stosów dla zadań szeregów czasowych z ostrzeżeniem, że może to potencjalnie przesłonić.
    + Dodano nowy typ wyjątku użytkownika, który jest wywoływany, jeśli zawartość magazynu pamięci podręcznej została naruszona
  + **azureml-automl-runtime**
    + Czyszczenie równoważenia klas nie będzie już włączone, jeśli użytkownik wyłączy cechowanie.  
  + **azureml-contrib-itp**
    + Typ obliczeniowy cmAks jest obsługiwany. Do obszaru roboczego w celu szkolenia możesz dołączyć własny klaster usługi AKS.
  + **azureml-contrib-notebook**
    + Ulepszenia dokumentu pakietu azureml-contrib-notebook.
  + **azureml-contrib-pipeline-steps**
    + Ulepszenia dokumentu pakietu azureml-contrib--pipeline-steps.
  + **azureml-core**
    + Dodaj set_connection, get_connection, list_connections, delete_connection, aby klient działał na zasobie połączenia obszaru roboczego
    + Aktualizacje dokumentacji pakietu azureml-coore/azureml.exceptions.
    + Aktualizacje dokumentacji pakietu azureml-core.
    + Aktualizacja doc klasy ComputeInstance.
    + Ulepszenia dokumentu pakietu azureml-core/azureml.core.compute.
    + Ulepszenia dokumentu dla klas związanych z usługami internetowymi w rdzeniu azureml-core.
    + Obsługa magazynu danych wybranego przez użytkownika do przechowywania danych profilowania
    + Dodano właściwość expand i page_count dla interfejsu API listy modeli
    + Usunięto usterkę, która powoduje, że usunięcie właściwości overwrite powoduje niepowodzenie przesłanego uruchomienia z powodu błędu deserializacji.
    + Naprawiono niespójną strukturę folderów podczas pobierania lub instalowanie zestawu FileDataset odwołującego się do jednego pliku.
    + Ładowanie zestawu danych plików parquet to_spark_dataframe jest teraz szybsze i obsługuje wszystkie typy danych parquet i Spark SQL.
    + Obsługa uzyskiwania dzienników kontenera init.
    + Przebiegi autoML są teraz oznaczone jako podrzędne przebiegi kroku uruchamiania równoległego.
  + **azureml-datadrift**
    + Ulepszenia dokumentu dotyczące pakietu azureml-contrib-notebook.
  + **azureml-dataprep**
    + Ładowanie zestawu danych plików parquet to_spark_dataframe jest teraz szybsze i obsługuje wszystkie typy danych parquet i Spark SQL.
    + Lepsza obsługa pamięci dla problemu OutOfMemory dla to_pandas_dataframe.
  + **azureml-interpret**
    + Uaktualniono narzędzie azureml-interpret w celu używania wersji 0.12 programu interpret-community.*
  + **azureml-mlflow**
    + Ulepszenia dokumentu azureml-mlflow.
    + Dodano obsługę rejestru modeli AML za pomocą uczenia maszynowego MLFlow.
  + **azureml-opendatasets**
    + Dodano obsługę języka Python 3.8
  + **azureml-pipeline-core**
    + Zaktualizowano `PipelineDataset` dokumentację klasy , aby było jasne, że jest to klasa wewnętrzna.
    + ParallelRunStep aktualizuje do akceptowania wielu wartości dla jednego argumentu, na przykład: "--group_column_names", "Col1", "Col2", "Col3"
    + Usunięto passthru_automl_config użycia danych pośrednich za pomocą funkcji AutoMLStep w potokach.
  + **azureml-pipeline-steps**
    + Ulepszenia dokumentu dotyczące pakietu azureml-pipeline-steps.
    + Usunięto passthru_automl_config użycia danych pośrednich za pomocą funkcji AutoMLStep w potokach.
  + **azureml-telemetry**
    + Ulepszenia dokumentu azureml-telemetry.
  + **azureml-train-automl-client**
    + Usunięto usterkę, `experiment.submit()` która powodować dwukrotne wywoływanie obiektu w wyniku innego `AutoMLConfig` zachowania.
    + Użytkownicy mogą teraz włączyć iterację zespołów stosów dla zadań szeregów czasowych z ostrzeżeniem, że może to potencjalnie przesłonić.
    + Zmieniono zachowanie uruchamiania autoML, aby zgłosić wyjątek UserErrorException, jeśli usługa zgłasza błąd użytkownika
    + Usunięto usterkę, która powodowała, że plik azureml_automl.log nie został wygenerowany lub brakuje dzienników podczas wykonywania eksperymentu rozwiązania AutoML na zdalnym docelowym obiektem obliczeniowym.
    + W przypadku zestawów danych klasyfikacji z niezrównoważonych klas zastosujemy równoważenie wagi, jeśli funkcja zamiatania ustali, że w przypadku danych próbkowanych równoważenie wagi zwiększa wydajność zadania klasyfikacji o określony próg.
    + Przebiegi autoML są teraz oznaczone jako przebieg podrzędny kroku uruchamiania równoległego.
  + **azureml-train-automl-runtime**
    + Zmieniono zachowanie uruchamiania autoML, aby zgłosić wyjątek UserErrorException, jeśli usługa zgłasza błąd użytkownika
    + Przebiegi autoML są teraz oznaczone jako przebieg podrzędny kroku uruchamiania równoległego.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK dla języka Python w wersji 1.7.0

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Ukończono usuwanie profilowania modelu z subskrypcji przez oczyszczenie poleceń interfejsu wiersza polecenia i zależności pakietów. Profilowanie modelu jest dostępne w rdzeniu.
    + Uaktualnia minimalna wersję interfejsu wiersza polecenia platformy Azure do wersji 2.3.0
  + **azureml-automl-core**
    + Lepszy komunikat o wyjątku w kroku cechowania fit_transform() z powodu niestandardowych parametrów transformatora.
    + Dodanie obsługi wielu języków dla modeli transformatora uczenia głębokiego, takich jak BERT w zautomatyzowanym uczeniu maszynowym.
    + Usuń przestarzały parametr lag_length z dokumentacji.
    + Ulepszono dokumentację parametrów prognozowania. Parametr lag_length został przestarzały.
  + **azureml-automl-runtime**
    + Naprawiono błąd, który był wyświetlany, gdy jedna z kolumn kategorii jest pusta w czasie prognozy/testu.
    + Napraw błędy uruchamiania, które zdarzają się, gdy funkcje wyszukiwania zwrotnego są włączone, a dane zawierają krótkie ziarno.
    + Rozwiązano problem z komunikatem o błędzie zduplikowanego indeksu czasu, gdy opóźnienia lub okna kroczące były ustawione na wartość "auto".
    + Rozwiązano problem z modelami amici i Arima w zestawach danych, które zawierały funkcje wyszukiwania zwrotnego.
    + Dodano obsługę dat przed 1677-09-21 lub po 2262-04-11 w kolumnach innych niż data i godzina w zadaniach prognozowania. Ulepszone komunikaty o błędach.
    + Ulepszono dokumentację parametrów prognozowania. Parametr lag_length był przestarzały.
    + Lepszy komunikat o wyjątku w kroku cechowania fit_transform() ze względu na niestandardowe parametry transformatora.
    + Dodanie obsługi wielu języków dla modeli transformatora uczenia głębokiego, takich jak BERT w zautomatyzowanym uczeniu maszynowym.
    + Operacje pamięci podręcznej, których wynikiem są niektóre błędy OSErrors, będą zgłaszać błąd użytkownika.
    + Dodano kontrole zapewniające, że dane trenowania i walidacji mają taką samą liczbę i zestaw kolumn
    + Rozwiązano problem z automatycznie wygenerowanym skryptem oceniania autoML, gdy dane zawierają znaki cudzysłowu
    + Włączanie objaśnień dla automl ujednokątnych modeli, które zawierają model chylić.
    + Najnowszy problem klienta ujawniał usterkę witryny na żywo, w której komunikaty są rejestrowane wraz z czyszczeniem równoważenia klas nawet wtedy, gdy logika równoważenia klas nie jest prawidłowo włączona. Usunięcie tych dzienników/komunikatów za pomocą tego pr.
  + **azureml-cli-common**
    + Ukończono usuwanie profilowania modelu z usługi contrib przez oczyszczenie poleceń interfejsu wiersza polecenia i zależności pakietów. Profilowanie modelu jest dostępne w rdzeniu.
  + **azureml-contrib-reinforcementlearning**
    + Narzędzie do testowania obciążenia
  + **azureml-core**
    + Zmiany dokumentacji w Script_run_config.py
    + Usunięto usterkę podczas drukowania danych wyjściowych interfejsu wiersza polecenia przesyłania potoku uruchamiania
    + Ulepszenia dokumentacji azureml-core/azureml.data
    + Rozwiązano problem z pobieraniem konta magazynu przy użyciu polecenia hdfs getconf
    + Ulepszona register_azure_blob_container i register_azure_file_share dokumentacji
  + **azureml-datadrift**
    + Ulepszona implementacja wyłączania i włączania monitorów dryfu zestawu danych
  + **azureml-interpret**
    + W kliencie wyjaśnienia usuń nans lub Infs przed serializacją JSON podczas przekazywania z artefaktów
    + Aktualizacja do najnowszej wersji społeczności interpret-community w celu poprawienia błędów braku pamięci w celu wyjaśnienia globalnego z wieloma funkcjami i klasami
    + Dodanie true_ys opcjonalnego parametru do przekazywania wyjaśnienia w celu włączenia dodatkowych funkcji w interfejsie użytkownika programu Studio
    + Poprawianie download_model_explanations() i list_model_explanations()
    + Niewielkie ulepszenia notesów, które mają pomóc w debugowaniu
  + **azureml-opendatasets**
    + Narzędzie azureml-opendatasets wymaga narzędzia azureml-dataprep w wersji 1.4.0 lub wyższej. Dodano ostrzeżenie w przypadku wykrycia niższej wersji
  + **azureml-pipeline-core**
    + Ta zmiana umożliwia użytkownikowi podanie opcjonalnej konfiguracji runconfig dla modułu moduleVersion podczas wywoływania modułu. Publish_python_script.
    + Włączenie konta węzła może być parametrem potoku w parallelrunstep w pliku azureml.pipeline.steps
  + **azureml-pipeline-steps**
    + Ta zmiana umożliwia użytkownikowi podanie opcjonalnej konfiguracji runconfig dla modułu moduleVersion podczas wywoływania modułu. Publish_python_script.
  + **azureml-train-automl-client**
    + Dodanie obsługi wielu języków dla modeli transformatora uczenia głębokiego, takich jak BERT w zautomatyzowanym uczeniu maszynowym.
    + Usuń przestarzały parametr lag_length z dokumentacji.
    + Ulepszono dokumentację parametrów prognozowania. Parametr lag_length został przestarzały.
  + **azureml-train-automl-runtime**
    + Włączanie objaśnień dla funkcji AutoML i poszepkiwu modeli zawierających model cykadowania.
    + Aktualizacje dokumentacji pakietów azureml-train-automl-*.
  + **azureml-train-core**
    + Obsługa tejsorFlow w wersji 2.1 w estymatorze PyTorch
    + Ulepszenia pakietu azureml-train-core.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK dla języka Python w wersji 1.6.0

+ **Nowe funkcje**
  + **azureml-automl-runtime**
    + Prognozowanie automatycznegoml obsługuje teraz prognozy klientów wykraczające poza wstępnie określony maksymalny horyzont bez ponownego trenowania modelu. Jeśli miejsce docelowe prognozy będzie bardziej w przyszłości niż określony maksymalny horyzont, funkcja forecast() będzie nadal generować prognozy punktowe do późniejszej daty przy użyciu trybu operacji cyklicznej. Aby uzyskać ilustrację nowej funkcji, zobacz sekcję "Prognozowanie dalej niż maksymalny horyzont" notesu "forecasting-forecast-function" w [folderze](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)."
  
  + **azureml-pipeline-steps**
    + Krok ParallelRunStep jest teraz wydany i jest częścią **pakietu azureml-pipeline-steps.** Istniejący krok ParallelRunStep w **pakiecie azureml-contrib-pipeline-steps** jest przestarzały. Zmiany z publicznej wersji zapoznawczej:
      + Dodano opcjonalny konfigurowalny parametr do sterowania maksymalnym wywołaniem metody uruchamiania dla dowolnej `run_max_try` partii, a wartość domyślna to 3.
      + Żadne pipelineParameters są już automatycznie wygenerowane. Następujące konfigurowalne wartości można jawnie ustawić jako PipelineParameter.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + Wartość domyślna process_count_per_node jest zmieniana na 1. Użytkownik powinien dostosować tę wartość w celu lepszej wydajności. Najlepszym rozwiązaniem jest ustawienie liczby procesorów GPU lub węzłów procesora CPU.
      + ParallelRunStep nie wprowadza żadnych pakietów, a użytkownik musi uwzględnić pakiety **azureml-core** i **azureml-dataprep[pandas, fuse]** w definicji środowiska. Jeśli niestandardowy obraz platformy Docker jest używany z user_managed_dependencies następnie użytkownik musi zainstalować na nim program conda.
      
+ **Fundamentalne zmiany**
  + **azureml-pipeline-steps**
    + Wycofane użycie pliku azureml.dprep.Dataflow jako prawidłowego typu danych wejściowych dla pliku AutoMLConfig
  + **azureml-train-automl-client**
    + Wycofane użycie pliku azureml.dprep.Dataflow jako prawidłowego typu danych wejściowych dla pliku AutoMLConfig

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Usunięto usterkę, która oznaczała, że podczas monitu użytkownika o obniżenie poziomu klienta może `get_output` zostać wydrukowane ostrzeżenie.
    + Zaktualizowano komputer Mac, aby polegał na zestawie cudatoolkit=9.0, ponieważ nie jest jeszcze dostępny w wersji 10.
    + Usuwanie ograniczeń dotyczących modeli węzłów i xgboost w przypadku szkolenia na zdalnych obliczeniach.
    + Ulepszone rejestrowanie w automl
    + Ulepszono obsługę błędów dla niestandardowej cechowania w zadaniach prognozowania.
    + Dodano funkcję, która umożliwia użytkownikom dołączanie funkcji z flagami w celu generowania prognoz.
    + Aktualizacje komunikatu o błędzie w celu poprawnego wyświetlenia błędu użytkownika.
    + Obsługa cv_split_column_names do training_data
    + Aktualizowanie rejestrowania komunikatu o wyjątku i śledzenia zwrotnego.
  + **azureml-automl-runtime**
    + Włącz bariery ochronne do prognozowania brakujących imputacji wartości.
    + Ulepszone rejestrowanie w automl
    + Dodano obsługę precyzyjnej obsługi błędów dla wyjątków dotyczących wstępnego przetwarzania danych
    + Usuwanie ograniczeń w modelach phroprop i xgboost w przypadku trenowana na zdalnych obliczeniach.
    + `azureml-train-automl-runtime` i `azureml-automl-runtime` zaktualizowali zależności `pytorch` dla , `scipy` i `cudatoolkit` . Obsługujemy teraz `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` i `cudatoolkit==10.1.243` .
    + Ulepszono obsługę błędów dla niestandardowej cechowania w zadaniach prognozowania.
    + Ulepszono mechanizm wykrywania częstotliwości zestawu danych prognozowania.
    + Rozwiązano problem z trenowaniem modelu w niektórych zestawach danych.
    + Ulepszono automatyczne wykrywanie maksymalnego horyzontu podczas prognozowania.
    + Dodano funkcję, która umożliwia użytkownikom dołączanie funkcji z lagami w celu generowania prognoz.
    +  Dodaje funkcje w funkcji prognozy, aby umożliwić dostarczanie prognoz poza wytrenowany horyzont bez ponownego trenowania modelu prognozowania.
    + Obsługa cv_split_column_names do training_data
  + **azureml-contrib-automl-dnn-forecasting**
    + Ulepszone rejestrowanie w uchu automl
  + **azureml-contrib-dostęp**
    + Dodano obsługę usług systemu Windows w programie ManagedInferencing
    + Usuń stare przepływy pracy JEDNOSTKI, takie jak dołączanie obliczeń JEDNOSTKI, klasa SingleModelMirWebservice — wyczyść profilowanie modelu umieszczone w pakiecie contrib-old
  + **azureml-contrib-pipeline-steps**
    + Drobna poprawka obsługi yaml
    + Narzędzie ParallelRunStep jest ogólnie dostępne — azureml.contrib.pipeline.steps ma powiadomienie o cofaniu świadczenia usługi i jest przenoszone do pliku azureml.pipeline.steps
  + **azureml-contrib-reinforcementlearning**
    + Narzędzie do testowania obciążenia listy RL
    + Estymator RL ma inteligentne wartości domyślne
  + **azureml-core**
    + Usuń stare przepływy pracy JEDNOSTKI, takie jak dołączanie mocy obliczeniowej JEDNOSTKI, klasa SingleModelMirWebservice — czyszczenie profilowania modelu umieszczone w pakiecie contrib-by
    + Naprawiono informacje podane użytkownikowi w przypadku niepowodzenia profilowania: uwzględniono identyfikator żądania i poprawiono treść komunikatu, aby był bardziej zrozumiały. Dodano nowy przepływ pracy profilowania do profilów wytyczających profilowanie
    + Ulepszono tekst błędu w przypadku niepowodzeń wykonywania zestawu danych.
    + Dodano obsługę interfejsu wiersza polecenia linku prywatnego obszaru roboczego.
    + Dodano opcjonalny parametr do parametru , który umożliwia określenie sposobu `invalid_lines` obsługi wierszy zawierających nieprawidłowy kod `Dataset.Tabular.from_json_lines_files` JSON.
    + W następnej wersji wycofamy proces tworzenia zasobów obliczeniowych opartych na uruchomieniu. Zalecamy utworzenie rzeczywistego klastra Amlcompute jako trwałego docelowego obiektu obliczeniowego i użycie nazwy klastra jako docelowego obiektu obliczeniowego w konfiguracji uruchamiania. Zobacz przykładowy notes tutaj: aka.ms/amlcomputenb
    + Ulepszono komunikaty o błędach w przypadku niepowodzeń wykonywania zestawu danych.
  + **azureml-dataprep**
    + Bardziej jawne ostrzeżenie dotyczące uaktualniania wersji programu pyarrow.
    + Ulepszona obsługa błędów i komunikat zwracany w przypadku niepowodzenia wykonania przepływu danych.
  + **azureml-interpret**
    + Aktualizacje dokumentacji pakietu azureml-interpret.
    + Naprawiono pakiety i notesy możliwości interpretacji, aby były zgodne z najnowszą aktualizacją sklearn
  + **azureml-opendatasets**
    + zwraca brak, gdy nie są zwracane żadne dane.
    + Zwiększenie wydajności to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Szybka poprawka dla parallelrunstep, gdzie ładowanie z YAML zostało przerwane
    + Krok ParallelRunStep jest ogólnie dostępny — azureml.contrib.pipeline.steps ma powiadomienie o cofaniu wersji i jest przenoszczony do pliku azureml.pipeline.steps — nowe funkcje obejmują: 1. Zestawy danych jako PipelineParameter 2. Nowy parametr run_max_retry 3. Konfigurowalna append_row wyjściowego
  + **azureml-pipeline-steps**
    + Przestarzałe azureml.dprep.Dataflow jako prawidłowy typ danych wejściowych.
    + Szybka poprawka rozwiązania ParallelRunStep, w której ładowanie z pliku YAML zostało przerwane
    + Narzędzie ParallelRunStep jest ogólnie dostępne — azureml.contrib.pipeline.steps ma powiadomienie o cofaniu wersji i jest przenoszone do pliku azureml.pipeline.steps — nowe funkcje obejmują:
      + Zestawy danych jako PipelineParameter
      + Nowy parametr run_max_retry
      + Konfigurowalna append_row wyjściowego
  + **azureml-telemetry**
    + Zaktualizuj rejestrowanie komunikatu o wyjątku i śledzenia.
  + **azureml-train-automl-client**
    + Ulepszone rejestrowanie w uchu automl
    + Aktualizacje komunikatu o błędzie w celu poprawnego wyświetlenia błędu użytkownika.
    + Obsługa cv_split_column_names do training_data
    + Przestarzałe azureml.dprep.Dataflow jako prawidłowy typ danych wejściowych.
    + Zaktualizowano komputery Mac, aby polegały na zestawie cudatoolkit=9.0, ponieważ nie jest on jeszcze dostępny w wersji 10.
    + Usuwanie ograniczeń w modelach phroprop i xgboost w przypadku trenowana na zdalnych obliczeniach.
    + `azureml-train-automl-runtime` i `azureml-automl-runtime` zaktualizowali zależności `pytorch` dla , `scipy` i `cudatoolkit` . Obsługujemy teraz `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` i `cudatoolkit==10.1.243` .
    + Dodano funkcję, która umożliwia użytkownikom dołączanie funkcji z lagami w celu generowania prognoz.
  + **azureml-train-automl-runtime**
    + Ulepszone rejestrowanie w uchu automl
    + Dodano obsługę precyzyjnej obsługi błędów dla wyjątków dotyczących wstępnego przetwarzania danych
    + Usuwanie ograniczeń w modelach phroprop i xgboost w przypadku trenowana na zdalnych obliczeniach.
    + `azureml-train-automl-runtime` i `azureml-automl-runtime` zaktualizowali zależności `pytorch` dla , `scipy` i `cudatoolkit` . Obsługujemy teraz `pytorch==1.4.0` , `scipy>=1.0.0,<=1.3.1` i `cudatoolkit==10.1.243` .
    + Aktualizacje komunikatu o błędzie w celu poprawnego wyświetlenia błędu użytkownika.
    + Obsługa cv_split_column_names do training_data
  + **azureml-train-core**
    + Dodano nowy zestaw wyjątków specyficznych dla usługi HyperDrive. Funkcja azureml.train.hyperdrive będzie teraz zgłaszać szczegółowe wyjątki.
  + **azureml-widgets**
    + Widżety AzureML nie są wyświetlane w aplikacji JupyterLab
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK dla języka Python w wersji 1.5.0

+ **Nowe funkcje**
  + **Funkcje w wersji zapoznawczej**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning wersji zapoznawczej dla uczenia przez wzmacnianie przy użyciu [struktury Ray.](https://ray.io) Umożliwia `ReinforcementLearningEstimator` trenowanie agentów uczenia przez wzmacnianie dla docelowych obliczeń procesora GPU i procesora CPU w Azure Machine Learning.

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Naprawia przypadkowo pozostawiony dziennik ostrzeżeń w poprzednim pnecie ściągnięcie. Dziennik został użyty do debugowania i przypadkowo został pozostawiony.
    + Poprawka usterki: informowanie klientów o częściowym niepowodzeniu podczas profilowania
  + **azureml-automl-core**
    + Przyspiesz model cygów/autoarima w prognozowaniu automatycznego przetwarzania danych, włączając równoległe dopasowywanie do szeregów czasu, gdy zestawy danych mają wiele szeregów czasu. Aby skorzystać z tej nowej funkcji, zaleca się ustawienie wartości "max_cores_per_iteration = -1" (czyli użycie wszystkich dostępnych rdzeni procesora CPU) w aplikacji AutoMLConfig.
    + Naprawiono błąd KeyError podczas drukowania barier w interfejsie konsoli
    + Naprawiono komunikat o błędzie experimentation_timeout_hours
    + Przestarzałe modele Tensorflow dla automatycznegoml.
  + **azureml-automl-runtime**
    + Naprawiono komunikat o błędzie experimentation_timeout_hours
    + Naprawiono niesklasyfikowany wyjątek podczas próby deserialiizacji z magazynu pamięci podręcznej
    + Przyspiesz model z autoarimą w prognozowaniu automatycznego przetwarzania danych, włączając równoległe dopasowywanie do szeregów czasu, gdy zestawy danych mają wiele szeregów czasu.
    + Naprawiono prognozowanie z włączonym oknem stopniowym w zestawach danych, w którym zestaw testowy/przewidywania nie zawierał jednego ziarna z zestawu treningowego.
    + Ulepszona obsługa brakujących danych
    + Rozwiązano problem z interwałami przewidywania podczas prognozowania w zestawach danych, zawierający szeregi czasowe, które nie są wyrównane w czasie.
    + Dodano lepszą walidację kształtu danych dla zadań prognozowania.
    + Ulepszono wykrywanie częstotliwości.
    + Utworzono lepszy komunikat o błędzie, jeśli nie można wygenerować krzyżowej walidacji dla zadań prognozowania.
    + Naprawienie interfejsu konsoli w celu poprawnego wydrukowania brakującej wartości.
    + Wymuszanie kontroli typu danych dla cv_split_indices danych wejściowych w pliku AutoMLConfig.
  + **azureml-cli-common**
    + Poprawka usterki: informowanie klientów o częściowym niepowodzeniu podczas profilowania
  + **azureml-contrib-dostęp**
    + Dodaje klasę azureml.contrib.the RevisionStatus, która przekazuje informacje o aktualnie wdrożonej poprawCE i najnowszej wersji określonej przez użytkownika. Ta klasa jest uwzględniona w obiekcie ClassWebservice w ramach atrybutu "deployment_status".
    + Włącza aktualizację dla usług sieci Web typuRowWebservice i jej klasy podrzędnej SingleModelMirWebservice.
  + **azureml-contrib-reinforcementlearning**
    + Dodano obsługę ray 0.8.3
    + AmlWindowsCompute obsługuje tylko Azure Files jako zainstalowany magazyn
    + Zmieniono nazwę health_check_timeout na health_check_timeout_seconds
    + Naprawiono niektóre opisy klas/metod.
  + **azureml-core**
    + Włączono konwersje wasb > blob w chmurach Azure Government i Chinach.
    + Naprawiono usterkę, która pozwalała rolam czytelnika na używanie poleceń interfejsu wiersza polecenia uruchamiania az ml w celu uzyskania informacji o uruchomieniu
    + Usunięto niepotrzebne rejestrowanie podczas zdalnych przebiegów usługi Azure ML z wejściowymi zestawami danych.
    + Pakiet RCranPackage obsługuje teraz parametr "version" dla wersji pakietu CRAN.
    + Poprawka usterki: informowanie klientów o częściowym niepowodzeniu podczas profilowania
    + Dodano obsługę zmiennoprzecinkową w stylu europejskiego dla platformy azureml-core.
    + Włączone funkcje linku prywatnego obszaru roboczego w zestawie Sdk usługi Azure ML.
    + Podczas tworzenia zestawu TabularDataset przy użyciu funkcji można określić, czy puste wartości mają być ładowane jako Brak, czy jako ciąg pusty, ustawiając `from_delimited_files` argument logiczny `empty_as_string` .
    + Dodano obsługę zmiennoprzecinkową w stylu europejskiego dla zestawów danych.
    + Ulepszone komunikaty o błędach dotyczące niepowodzeń instalacji zestawu danych.
  + **azureml-datadrift**
    + Zapytanie wyników dryfu danych z zestawu SDK miało usterkę, która nie rozróżniała metryk minimalnej, maksymalnej i średniej funkcji, co powoduje zduplikowanie wartości. Naprawiliśmy tę usterkę, poprzedzając element docelowy lub punkt odniesienia nazwami metryk. Przed: zduplikowane wartości minimalne, maksymalne, średnie. Po: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + Usprawnij obsługę pisania ograniczonych środowisk języka Python podczas zapewniania zależności .NET wymaganych do dostarczania danych.
    + Naprawiono tworzenie przepływu danych w pliku z wiodącymi pustymi rekordami.
    + Dodano opcje obsługi błędów dla opcji `to_partition_iterator` podobnych do `to_pandas_dataframe` .
  + **azureml-interpret**
    + Zmniejszenie limitów długości ścieżki wyjaśnienia w celu zmniejszenia prawdopodobieństwa przekroenia limitu systemu Windows
    + Poprawka usterki dla rozrzednych wyjaśnień utworzonych za pomocą objaśnienia naśladujących przy użyciu liniowego modelu zastępczego.
  + **azureml-opendatasets**
    + Rozwiązano problem z kolumnami MNIST analizowany jako ciąg, który powinien być typu int.
  + **azureml-pipeline-core**
    + Zezwalanie na używanie regenerate_outputs w przypadku korzystania z modułu osadzonego w module ModuleStep.
  + **azureml-train-automl-client**
    + Przestarzałe modele Tensorflow dla automatycznegoml.
    + Poprawka zezwalania użytkownikom na wyświetlanie nieobsługiwanych algorytmów w trybie lokalnym
    + Poprawki w chmurze dotyczące funkcji AutoMLConfig.
    + Wymuszanie kontroli typu danych dla cv_split_indices danych wejściowych w pliku AutoMLConfig.
    + Rozwiązano problem z niepowodzeniem przebiegów automatycznego show_output
  + **azureml-train-automl-runtime**
    + Usunięcie usterki w iteracjach zespołu, która uniemożliwiała pomyślne przeoczanie limitu czasu pobierania modelu.
  + **azureml-train-core**
    + Naprawienie literówki w klasie azureml.train.dnn.Nccl.
    + Obsługa programu PyTorch w wersji 1.5 w programie do szacowania PyTorch
    + Rozwiązano problem, który nie można pobrać obrazu struktury w regionie Azure Government podczas korzystania z narzędzia do szacowania struktury trenowania

  
## <a name="2020-05-04"></a>2020-05-04
**Nowe środowisko notesu**

Teraz możesz tworzyć, edytować i udostępniać notesy i pliki uczenia maszynowego bezpośrednio w internetowym interfejsie studio dla Azure Machine Learning. Możesz użyć wszystkich klas i metod dostępnych w zestawie SDK języka [Python Azure Machine Learning](/python/api/overview/azure/ml/intro) w tych notesach Wprowadzenie [tutaj](./how-to-run-jupyter-notebooks.md)

**Wprowadzono nowe funkcje:**

+ Ulepszony edytor (edytor Monaco) używany przez VS Code 
+ Ulepszenia interfejsu użytkownika/środowiska użytkownika
+ Pasek narzędzi komórki
+ Nowy pasek narzędzi notesu i kontrolki obliczeniowe
+ Pasek stanu notesu 
+ Przełączanie jądra wbudowanego
+ Obsługa R
+ Ulepszenia ułatwień dostępu i lokalizacji
+ Paleta poleceń
+ Dodatkowe skróty klawiaturowe
+ Autozapisywanie
+ Zwiększona wydajność i niezawodność

Uzyskaj dostęp do następujących internetowych narzędzi do tworzenia z programu Studio:
    
| Narzędzie internetowe  |     Opis  |
|---|---|
| Notesy usługi Azure ML Studio   |     Pierwsze w klasie tworzenie plików notesu i obsługa wszystkich operacji dostępnych w zestawie SDK języka Python usługi Azure ML. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK dla języka Python w wersji 1.4.0

+ **Nowe funkcje**
  + Klastry AmlCompute obsługują teraz konfigurowanie tożsamości zarządzanej w klastrze w czasie aprowizowania. Wystarczy określić, czy chcesz użyć tożsamości przypisanej przez system, czy tożsamości przypisanej przez użytkownika, i przekazać identyfikator identityId dla tej drugiej tożsamości. Następnie można skonfigurować uprawnienia dostępu do różnych zasobów, takich jak magazyn lub ACR, w taki sposób, aby tożsamość zasobów obliczeniowych jest używana do bezpiecznego uzyskiwania dostępu do danych, zamiast podejścia opartego na tokenach stosowanego obecnie przez usługę AmlCompute. Zapoznaj się z naszą odwołaniami do zestawu SDK, aby uzyskać więcej informacji na temat parametrów.
  

+ **Fundamentalne zmiany**
  + Klastry AmlCompute obsługiowały funkcję w wersji zapoznawczej wokół tworzenia opartego na uruchomieniu, która ma być przestarzała za dwa tygodnie. Można nadal tworzyć trwałe docelowe obiekty obliczeniowe jak zawsze przy użyciu klasy Amlcompute, ale konkretne podejście do określania identyfikatora "amlcompute" jako docelowego obiektu obliczeniowego w konfiguracji uruchamiania nie będzie obsługiwane w najbliższej przyszłości. 

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-runtime**
    + Włącz obsługę typu bezhashable podczas obliczania liczby unikatowych wartości w kolumnie.
  + **azureml-core**
    + Poprawiono stabilność podczas odczytywania z Azure Blob Storage przy użyciu zestawu danych TabularDataset.
    + Ulepszona dokumentacja `grant_workspace_msi` parametru dla `Datastore.register_azure_blob_store` .
    + Usunięto usterkę w `datastore.upload` funkcji w celu obsługi `src_dir` argumentu kończącego się na `/` lub `\` .
    + Dodano komunikat o błędzie z akcjami podczas próby przekazania Azure Blob Storage magazynu danych, który nie ma klucza dostępu ani tokenu SAS.
  + **azureml-interpret**
    + Dodano górną granicę do rozmiaru pliku dla danych wizualizacji w przekazanych wyjaśnieniach.
  + **azureml-train-automl-client**
    + Jawne sprawdzanie label_column_name & weight_column_name dla autoMLConfig jako ciąg typu.
  + **azureml-contrib-pipeline-steps**
    + ParallelRunStep obsługuje teraz zestaw danych jako parametr potoku. Użytkownik może skonstruować potok z przykładowym zestawem danych i może zmienić wejściowy zestaw danych tego samego typu (plik lub tabelaryk) dla nowego uruchomienia potoku.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK dla języka Python w wersji 1.3.0

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Dodano dodatkowe dane telemetryczne dotyczące operacji po trenowaniach.
    + Przyspiesza automatyczne trenowanie ARIMA przy użyciu warunkowej sumy kwadratów (CSS) do trenowania serii o długości dłuższej niż 100. Używana długość jest przechowywana jako stała ARIMA_TRIGGER_CSS_TRAINING_LENGTH w/w klasie TimeSeriesInternal w folderze /src/azureml-automl-core/azureml/automl/core/shared/constants.py
    + Ulepszono rejestrowanie przez użytkownika przebiegów prognozowania. Teraz więcej informacji na temat fazy, która jest aktualnie uruchomiona, będzie wyświetlana w dzienniku
    + Niedozwolone ustawienie target_rolling_window_size wartości mniejszej niż 2
  + **azureml-automl-runtime**
    + Ulepszono komunikat o błędzie wyświetlany po znalezioniu zduplikowanych znaczników czasu.
    + Niedozwolone ustawienie target_rolling_window_size wartości mniejszej niż 2.
    + Naprawiono błąd imputacji opóźnienia. Przyczyną problemu była niewystarczająca liczba obserwacji potrzebnych do sezonowego rozkładu serii. Dane "odsezonowane" są używane do obliczania częściowej funkcji automatycznej skorelacji (PACF) w celu określenia długości opóźnienia.
    + Włączono dostosowywanie cechowania celu kolumny na potrzeby prognozowania zadań przez konfigurację cechowania. Obsługiwane są wartości liczbowe i podzielone na wartości jako kolumny na potrzeby zadań prognozowania.
    + Włączono dostosowywanie cechowania kolumny upuszczania do prognozowania zadań przez konfigurację cechowania.
    + Włączono dostosowywanie imputacji do prognozowania zadań przez konfigurację cechowania. Obsługiwane są teraz imputacji wartości stałej dla kolumny docelowej i średniej, mediany, most_frequent i imputacji wartości stałej dla danych treningowych.
  + **azureml-contrib-pipeline-steps**
    + Akceptowanie nazw obliczeniowych ciągów, które mają być przekazywane do elementu ParallelRunConfig
  + **azureml-core**
    +  Dodano interfejs API Environment.clone(new_name) w celu utworzenia kopii obiektu Environment
    +  Environment.docker.base_dockerfile akceptuje ścieżka pliku. Jeśli uda się rozpoznać plik, zawartość zostanie odczytana do base_dockerfile środowiska
    + Automatyczne resetowanie wzajemnie wykluczających się wartości dla base_image i base_dockerfile, gdy użytkownik ręcznie ustawia wartość w Environment.docker
    + Dodano user_managed w sekcji RSection, która wskazuje, czy środowisko jest zarządzane przez użytkownika, czy przez usługę AzureML.
    + Zestaw danych: Naprawiono błąd pobierania zestawu danych, jeśli ścieżka danych zawiera znaki Unicode.
    + Zestaw danych: Ulepszony mechanizm buforowania instalacji zestawu danych w celu przestrzegania minimalnego wymagania dotyczącego miejsca na dysku w u Azure Machine Learning Compute, co pozwala uniknąć sytuacji, w której węzeł będzie bezużytelny i powoduje anulowanie zadania.
    + Zestaw danych: Dodajemy indeks dla kolumny szeregów czasowych, gdy uzyskujesz dostęp do zestawu danych szeregów czasowych jako ramce danych pandas, która służy do przyspieszania dostępu do danych opartych na szeregach czasowych.  Wcześniej indeksowi nadano taką samą nazwę jak kolumna znacznika czasu, co myliło użytkowników co do tego, która kolumna jest rzeczywistą kolumną znacznika czasu i która jest indeksem. Teraz nie nadajemy żadnej konkretnej nazwy indeksowi, ponieważ nie powinien być używany jako kolumna. 
    + Zestaw danych: Rozwiązano problem z uwierzytelnianiem zestawu danych w suwerennej chmurze.
    + Zestaw danych: `Dataset.to_spark_dataframe` Usunięto błąd dla zestawów danych utworzonych na podstawie magazynów danych usługi Azure PostgreSQL.
  + **azureml-interpret**
    + Dodano globalne wyniki do wizualizacji, jeśli lokalne wartości ważności są rozrzedniane
    + Zaktualizowano narzędzie azureml-interpret w celu używania programu interpret-community 0.9.*
    + Rozwiązano problem z pobieraniem wyjaśnienia, który miał rozrzedione dane oceny
    + Dodano obsługę rozrzednego formatu obiektu explanation w umacie AutoML
  + **azureml-pipeline-core**
    + Obsługa wystąpienia obliczeniowego jako docelowego obiektu obliczeniowego w potokach
  + **azureml-train-automl-client**
    + Dodano dodatkowe dane telemetryczne dotyczące operacji po trenowaniach.
    + Naprawiono regresję podczas wczesnego zatrzymywania
    + Przestarzałe azureml.dprep.Dataflow jako prawidłowy typ danych wejściowych.
    +  Zmiana domyślnego limit czasu eksperymentu autoML na sześć dni.
  + **azureml-train-automl-runtime**
    + Dodano dodatkowe dane telemetryczne dotyczące operacji po trenowaniach.
    + Dodano rozrzedną obsługę end-to-end autoML
  + **azureml-opendatasets**
    + Dodano dodatkową telemetrię dla monitora usług.
    + Włączanie usługi Front Door dla obiektu blob w celu zwiększenia stabilności 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK dla języka Python w wersji 1.2.0

+ **Fundamentalne zmiany**
  + Obsługa drop dla języka Python 2.7

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Dodaje polecenie "--subscription-id" do `az ml model/computetarget/service` poleceń w interfejsie wiersza polecenia
    + Dodawania obsługi przekazywania kluczy zarządzanych przez klienta (CMK) vault_url, key_name i key_version wdrażania usługi ACI
  + **azureml-automl-core** 
    + Włączono dostosowaną imputacji z wartością stałą dla zadań prognozowania danych X i y.
    + Rozwiązano problem z wyświetlaniem komunikatów o błędach użytkownikowi w programie .    
  + **azureml-automl-runtime**
    + Rozwiązano problem z prognozowaniem w zestawach danych, który zawierał ziarna z tylko jednym wierszem
    + Zmniejszenie ilości pamięci wymaganej przez zadania prognozowania.
    + Dodano lepsze komunikaty o błędach, jeśli kolumna czasu ma niepoprawny format.
    + Włączono dostosowaną imputacji z wartością stałą dla zadań prognozowania danych X i y.
  + **azureml-core**
    + Dodano obsługę ładowania wartości ServicePrincipal ze zmiennych AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID i AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + Wprowadzono nowy parametr do funkcji : Domyślnie ( ) wszystkie podziały wierszy, w tym te w wartościach pól w cudzysłowy, będą interpretowane `support_multi_line` `Dataset.Tabular.from_delimited_files` jako podział `support_multi_line=False` rekordu. Odczytywanie danych w ten sposób jest szybsze i bardziej zoptymalizowane pod kątem wykonywania równoległego na wielu rdzeniach procesora CPU. Jednak może to spowodować dyskretne tworzenie większej liczby rekordów z niedorównaną wartością pola. Należy go ustawić na wartość `True` , gdy rozdzielane pliki zawierają podziały wierszy w cudzysłowy.
    + Dodano możliwość rejestrowania ADLS Gen2 interfejsie wiersza Azure Machine Learning wiersza polecenia
    + Zmieniono nazwę parametru "fine_grain_timestamp" na "timestamp" i parametru "coarse_grain_timestamp" na "partition_timestamp" dla metody with_timestamp_columns() w metodzie TabularDataset, aby lepiej odzwierciedlać użycie parametrów.
    + Zwiększono maksymalną długość nazwy eksperymentu do 255.
  + **azureml-interpret**
    + Zaktualizowano narzędzie azureml-interpret do programu interpret-community 0.7.*
  + **azureml-sdk**
    + Zmiana na zależności za pomocą tyldy zgodnej wersji w celu obsługi poprawek w wersjach wstępnych i stabilnych.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK dla języka Python w wersji 1.1.5

+ **Feature deprecation (Cofanie funkcji)**
  + **Python 2.7**
    + Ostatnia wersja do obsługi języka Python 2.7

+ **Fundamentalne zmiany**
  + **Semantyczne przechowywanie wersji 2.0.0**
    + Począwszy od wersji 1.1 zestawu Sdk usługi Azure ML dla języka Python, 2.0.0 jest wsadem semantycznego wersjonowania. [Przeczytaj więcej tutaj.](https://semver.org/) Wszystkie kolejne wersje będą zgodne z nowym schematem numerowania i semantyczną umową wersjonacyjną. 

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Zmień nazwę polecenia interfejsu wiersza polecenia punktu końcowego z "az ml endpoint aks" na "az ml endpoint real time" w celu zachowania spójności.
    + aktualizowanie instrukcji instalacji interfejsu wiersza polecenia dla stabilnego i eksperymentalnego interfejsu wiersza polecenia gałęzi
    + Naprawiono profilowanie pojedynczego wystąpienia w celu uzyskania rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
  + **azureml-automl-core**
    + Włączono wnioskowanie w trybie wsadowym (trwanie wielu wierszy raz) dla modeli ONNX automatycznego języka
    + Ulepszono wykrywanie częstotliwości w zestawach danych, brak danych lub zawiera nieregularne punkty danych
    + Dodano możliwość usuwania punktów danych, które nie są zgodne z dominującą częstotliwością.
    + Zmieniono dane wejściowe konstruktora, aby uzyskać listę opcji w celu zastosowania opcji imputacji dla odpowiednich kolumn.
    + Rejestrowanie błędów zostało ulepszone.
  + **azureml-automl-runtime**
    + Rozwiązano problem z błędem zgłaszany, jeśli ziarno nie było obecne w zestawie treningowym, który pojawił się w zestawie testowym
    + Usunięto y_query podczas oceniania w usłudze prognozowania
    + Rozwiązano problem z prognozowaniem, gdy zestaw danych zawiera krótkie ziarna z długimi przerwami czasu.
    + Rozwiązano problem, który występuje, gdy automatycznie włączony jest maksymalny horyzont, a kolumna daty zawiera daty w postaci ciągów. Dodano właściwą konwersję i komunikaty o błędach dla sytuacji, gdy konwersja na datę nie jest możliwa
    + Używanie natywnych plików NumPy i SciPy do serializacji i deserializacji danych pośrednich dla fileCacheStore (używane w przypadku lokalnych przebiegów autoML)
    + Usunięto usterkę, która mogła zablokować nieudane przebiegi podrzędne w stanie Uruchomione.
    + Zwiększona szybkość cechowania.
    + Naprawiono sprawdzanie częstotliwości podczas oceniania, teraz zadania prognozowania nie wymagają ścisłej równoważności częstotliwości między trenem a zestawem testowym.
    + Zmieniono dane wejściowe konstruktora, aby uzyskać listę opcji w celu zastosowania opcji imputacji dla odpowiednich kolumn.
    + Usunięto błędy związane z wyborem typu opóźnienia.
    + Naprawiono niesklasyfikowany błąd zgłoszony w zestawach danych, który miał ziarno z pojedynczym wierszem
    + Rozwiązano problem z powolnością wykrywania częstotliwości.
    + Usuwa usterkę w obsłudze wyjątków autoML, która powodowała, że rzeczywista przyczyna niepowodzenia trenowania była zastępowana przez atrybut AttributeError.
  + **azureml-cli-common**
    + Naprawiono profilowanie pojedynczego wystąpienia w celu uzyskania rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
  + **azureml-contrib-dostęp**
    + Dodaje funkcje w klasie DoSiećWebservice w celu pobrania tokenu dostępu
    + Użyj domyślnie uwierzytelniania tokenu dla usługiWebservice podczas wywołania FunkcjiWebservice.run() — odświeżaj tylko w przypadku niepowodzenia wywołania
    + Wdrożenie usługi internetowej wymaga teraz odpowiednich sku [Standard_DS2_v2, Standard_F16, Standard_A2_v2] zamiast odpowiednio [Ds2v2, A2v2 i F16].
  + **azureml-contrib-pipeline-steps**
    + Opcjonalny parametr side_inputs dodany do parallelRunStep. Ten parametr może służyć do instalacji folderu w kontenerze. Obecnie obsługiwane typy to DataReference i PipelineData.
    + Parametry przekazane w pliku ParallelRunConfig można teraz nadpisać, przekazując parametry potoku. Nowe parametry potoku obsługiwane aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count i aml_process_count_per_node są już częścią wcześniejszej wersji).
  + **azureml-core**
    + Wdrożone usługi sieci Web AzureML będą teraz domyślnie `INFO` logować. Można to kontrolować, ustawiając zmienną `AZUREML_LOG_LEVEL` środowiskową w wdrożonej usłudze.
    + Zestaw SDK języka Python używa usługi odnajdywania do używania punktu końcowego "api" zamiast "pipelines".
    + Zamień na nowe trasy we wszystkich wywołaniach zestawu SDK.
    + Zmieniono routing wywołań do usługi ModelManagementService na nową ujednoliconą strukturę.
      + Publicznie dostępna metoda aktualizacji obszaru roboczego.
      + Dodano image_build_compute w metodzie aktualizacji obszaru roboczego, aby umożliwić użytkownikowi aktualizowanie obliczeń dla kompilacji obrazu.
    + Dodano komunikaty dotyczące cofania obsługi do starego przepływu pracy profilowania. Naprawiono limity procesora i pamięci profilowania.
    + Dodano element RSection jako część środowiska do uruchamiania zadań R.
    + Dodano walidację do metody , aby zgłaszać błąd, gdy źródło zestawu danych jest niedostępne lub `Dataset.mount` nie zawiera żadnych danych.
    + Dodano parametr jako dodatkowy parametr interfejsu wiersza polecenia magazynu danych do rejestrowania kontenera obiektów blob platformy Azure, który umożliwi zarejestrowanie kontenera obiektów blob, który znajduje się `--grant-workspace-msi-access` za siecią wirtualną.
    + Naprawiono profilowanie pojedynczego wystąpienia w celu uzyskania rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
    + Rozwiązano problem z aks.py _deploy.
    + Sprawdza integralność przekazywanych modeli, aby uniknąć niepowodzeń przechowywania w trybie dyskretnym.
    + Użytkownik może teraz określić wartość klucza uwierzytelniania podczas ponownego generowania kluczy dla usług sieci Web.
    + Usunięto usterkę, która wskazuje, że wielkie litery nie mogą być używane jako nazwa wejściowa zestawu danych.
  + **azureml-defaults**
    + `azureml-dataprep` Program zostanie teraz zainstalowany w ramach systemu `azureml-defaults` . Nie jest już wymagane ręczne instalowanie elementu data prep[fuse] na docelowych elementach obliczeniowych w celu zainstalowania zestawów danych.
  + **azureml-interpret**
    + Zaktualizowano narzędzie azureml-interpret do programu interpret-community 0.6.*
    + Zaktualizowano narzędzie azureml-interpret, aby było zależne od wersji interpret-community 0.5.0
    + Dodano wyjątki w stylu azureml do pliku azureml-interpret
    + Naprawiono serializację DeepScoringExplainer dla modeli Keras
  + **azureml-mlflow**
    + Dodawanie obsługi suwerennych chmur do pliku azureml.mlflow
  + **azureml-pipeline-core**
    + Notes oceniania wsadowego potoku używa teraz parallelrunstep
    + Usunięto usterkę, która powoduje, że wyniki pythonScriptStep mogły być nieprawidłowo ponownie używane pomimo zmiany listy argumentów
    + Dodano możliwość ustawienia typu kolumn podczas wywoływania metod parse_*. `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Przeniesiono `AutoMLStep` pakiet do pakietu `azureml-pipeline-steps` . W programie wywłaszczone `AutoMLStep` zostały przestarzałe. `azureml-train-automl-runtime`
    + Dodano przykład dokumentacji dla zestawu danych jako dane wejściowe PythonScriptStep
  + **azureml-tensorboard**
    + zaktualizowano narzędzie azureml-tensorboard w celu obsługi platformy tensorflow 2.0
    + Pokazywanie poprawnego numeru portu podczas korzystania z niestandardowego portu tensorboard w wystąpieniu obliczeniowym
  + **azureml-train-automl-client**
    + Rozwiązano problem, który mógł powodować, że niektóre pakiety można było instalować w nieprawidłowych wersjach w zdalnych przebiegach.
    + Rozwiązano problem z zastępowaniem pliku FeaturizationConfig, który filtruje niestandardową konfigurację cechowania.
  + **azureml-train-automl-runtime**
    + Rozwiązano problem z wykrywaniem częstotliwości w przebiegach zdalnych
    + Przeniesiono `AutoMLStep` pakiet `azureml-pipeline-steps` . Wycofane w `AutoMLStep` ramach `azureml-train-automl-runtime` .
  + **azureml-train-core**
    + Obsługa programu PyTorch w wersji 1.4 w programie do szacowania PyTorch
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK dla języka Python w wersji 1.1.2rc0 (wersja wstępna)

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Włączono wnioskowanie w trybie wsadowym (trwanie wielu wierszy raz) dla modeli ONNX automatycznego języka
    + Ulepszono wykrywanie częstotliwości w zestawach danych, brak danych lub zawiera nieregularne punkty danych
    + Dodano możliwość usuwania punktów danych, które nie są zgodne z dominującą częstotliwością.
  + **azureml-automl-runtime**
    + Rozwiązano problem z błędem zgłoszonym, jeśli ziarno nie było w zestawie treningowym wyświetlanym w zestawie testowym
    + Usunięto wymaganie y_query podczas oceniania w usłudze prognozowania
  + **azureml-contrib-dostęp**
    + Dodaje funkcje w klasie FunkcjiWebservice w celu pobrania tokenu dostępu
  + **azureml-core**
    + Wdrożone usługi sieci Web AzureML będą teraz domyślnie `INFO` logować. Można to kontrolować, ustawiając zmienną `AZUREML_LOG_LEVEL` środowiskową w wdrożonej usłudze.
    + Naprawienie iterowania w `Dataset.get_all` celu zwrócenia wszystkich zestawów danych zarejestrowanych w obszarze roboczym.
    + Ulepszenie komunikatu o błędzie, gdy nieprawidłowy typ jest przekazywany `path` do argumentu interfejsów API tworzenia zestawu danych.
    + Zestaw SDK języka Python używa usługi odnajdywania do używania punktu końcowego "api" zamiast "pipelines".
    + Zamiana na nowe trasy we wszystkich wywołaniach zestawu SDK
    + Zmienia routing wywołań do usługi ModelManagementService na nową ujednoliconą strukturę
      + Publicznie dostępna metoda aktualizacji obszaru roboczego.
      + Dodano image_build_compute w metodzie aktualizacji obszaru roboczego, aby umożliwić użytkownikowi aktualizowanie obliczeń dla kompilacji obrazu
    +  Dodano komunikaty o cofaniu obsługi do starego przepływu pracy profilowania. Naprawiono limity procesora i pamięci profilowania
  + **azureml-interpret**
    + aktualizacja pliku azureml-interpret do programu interpret-community 0.6.*
  + **azureml-mlflow**
    + Dodawanie obsługi suwerennych chmur do pliku azureml.mlflow
  + **azureml-pipeline-steps**
    + Przeniesiono `AutoMLStep` do . `azureml-pipeline-steps package` W programie wywłaszczone `AutoMLStep` zostały przestarzałe. `azureml-train-automl-runtime`
  + **azureml-train-automl-client**
    + Rozwiązano problem, który mógł powodować, że niektóre pakiety można było instalować w nieprawidłowych wersjach w zdalnych przebiegach.
  + **azureml-train-automl-runtime**
    + Rozwiązano problem z wykrywaniem częstotliwości w zdalnych przebiegach
    + Przeniesiono `AutoMLStep` do . `azureml-pipeline-steps package` W programie wywłaszczone `AutoMLStep` zostały przestarzałe. `azureml-train-automl-runtime`
  + **azureml-train-core**
    + Przeniesiono `AutoMLStep` do . `azureml-pipeline-steps package` W programie wywłaszczone `AutoMLStep` zostały przestarzałe. `azureml-train-automl-runtime`

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK dla języka Python w wersji 1.1.1rc0 (wersja wstępna)

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Naprawiono profilowanie pojedynczego wystąpienia w celu uzyskania rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
  + **azureml-automl-core**
    + Rejestrowanie błędów zostało ulepszone.
  + **azureml-automl-runtime**
    + Rozwiązano problem z prognozowaniem, gdy zestaw danych zawiera krótkie ziarna z długimi przerwami czasu.
    + Rozwiązano problem, który występuje, gdy automatyczne maksimum horyzontu jest włączone, a kolumna dat zawiera daty w postaci ciągów. Dodaliśmy odpowiednią konwersję i prawidłowy błąd, jeśli konwersja na datę nie jest możliwa
    + Używanie natywnych plików NumPy i SciPy do serializacji i deserializacji danych pośrednich dla fileCacheStore (używane dla lokalnych przebiegów autoML)
    + Usunięto usterkę, która mogła zablokować nieudane przebiegi podrzędne w stanie Uruchomiony.
  + **azureml-cli-common**
    + Naprawiono profilowanie pojedynczego wystąpienia w celu uzyskania rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
  + **azureml-core**
    + Dodano parametr jako dodatkowy parametr interfejsu wiersza polecenia magazynu danych do rejestrowania kontenera obiektów blob platformy Azure, który umożliwi zarejestrowanie kontenera obiektów blob, który znajduje się `--grant-workspace-msi-access` za siecią wirtualną
    + Naprawiono profilowanie pojedynczego wystąpienia w celu uzyskania rekomendacji i zostało udostępnione w podstawowym zestawie SDK.
    + Rozwiązano problem w programie aks.py _deploy
    + Sprawdza integralność przekazywanych modeli, aby uniknąć niepowodzeń przechowywania w trybie dyskretnym.
  + **azureml-interpret**
    + dodano wyjątki w stylu azureml do azureml-interpret
    + Naprawiono serializację DeepScoringExplainer dla modeli keras
  + **azureml-pipeline-core**
    + Notes oceniania wsadowego potoku używa teraz parallelRunStep
  + **azureml-pipeline-steps**
    + Przeniesiono `AutoMLStep` pakiet `azureml-pipeline-steps` . Wycofane w `AutoMLStep` ramach `azureml-train-automl-runtime` .
  + **azureml-contrib-pipeline-steps**
    + Opcjonalny parametr side_inputs dodany do parallelRunStep. Ten parametr może służyć do instalacji folderu w kontenerze. Obecnie obsługiwane typy to DataReference i PipelineData.
  + **azureml-tensorboard**
    + zaktualizowano narzędzie azureml-tensorboard w celu obsługi platformy tensorflow 2.0
  + **azureml-train-automl-client**
    + Rozwiązano problem z zastępowaniem pliku FeaturizationConfig, który filtruje niestandardową konfigurację cechowania.
  + **azureml-train-automl-runtime**
    + Przeniesiono `AutoMLStep` pakiet `azureml-pipeline-steps` . W programie wywłaszczone `AutoMLStep` zostały przestarzałe. `azureml-train-automl-runtime`
  + **azureml-train-core**
    + Obsługa programu PyTorch w wersji 1.4 w programie do szacowania PyTorch
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK dla języka Python w wersji 1.1.0rc0 (wersja wstępna)

+ **Fundamentalne zmiany**
  + **Semantyczne przechowywanie wersji 2.0.0**
    + Począwszy od wersji 1.1 zestawu Sdk usługi Azure ML dla języka Python, system przyjmuje semantyczną wersję 2.0.0. [Przeczytaj więcej tutaj.](https://semver.org/) Wszystkie kolejne wersje będą zgodne z nowym schematem numerowania i semantyczną umową wersjonacyjną. 
  
+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-runtime**
    + Zwiększona szybkość cechowania.
    + Naprawiono sprawdzanie częstotliwości podczas oceniania, teraz w zadaniach prognozowania nie wymagamy ścisłego równoważności częstotliwości między trenem a zestawem testowym.
  + **azureml-core**
    + Użytkownik może teraz określić wartość klucza uwierzytelniania podczas ponownego generowania kluczy dla usług sieci Web.
  + **azureml-interpret**
    + Zaktualizowano narzędzie azureml-interpret, aby było zależne od wersji interpret-community 0.5.0
  + **azureml-pipeline-core**
    + Usunięto usterkę, która powoduje, że wyniki pythonScriptStep mogły być nieprawidłowo ponownie używane pomimo zmiany listy argumentów
  + **azureml-pipeline-steps**
    + Dodano przykład dokumentacji dla zestawu danych jako dane wejściowe PythonScriptStep
  + **azureml-contrib-pipeline-steps**
    + Parametry przekazywane w pliku ParallelRunConfig można teraz nadpisać, przekazując parametry potoku. Nowe parametry potoku aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count i aml_process_count_per_node są już częścią wcześniejszej wersji).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.85

+ **Nowe funkcje**
  + **azureml-core**
    + Uzyskiwanie bieżącego podstawowego użycia i limitu przydziału dla zasobów usługi AmlCompute w danym obszarze roboczym i subskrypcji
  
  + **azureml-contrib-pipeline-steps**
    + Umożliwianie użytkownikowi przekazania tabelaryowego zestawu danych jako wyniku pośredniego z poprzedniego kroku do kroku parallelrunstep

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-runtime**
    + Usunięto wymaganie y_query w żądaniu do wdrożonej usługi prognozowania. 
    + Sekcja "y_query" została usunięta z sekcji żądania obsługi notesu Orange Tablet firmy Dominicka.
    + Usunięto usterkę uniemożliwiającą prognozowanie we wdrożonych modelach, która działa na zestawach danych z kolumnami daty i czasu.
    + Dodano współczynnik korelacji Matthewsa jako metrykę klasyfikacji dla klasyfikacji binarnej i wieloklasowej.
  + **azureml-contrib-interpret**
    + Usunięto objaśnienia tekstowe z pliku azureml-contrib-interpret jako wyjaśnienie tekstu, które zostało przeniesione do usługi interpret-text, która zostanie wkrótce wydana.
  + **azureml-core**
    + Zestaw danych: użycie zestawu danych plików nie zależy już od tego, czy pliki numpy i pandas mają być zainstalowane w środowisku języka Python.
    + Zmieniono LocalWebservice.wait_for_deployment() w celu sprawdzenia stanu lokalnego kontenera platformy Docker przed podjęciem próby ping do punktu końcowego kondycji, co znacznie skraca czas zgłaszania wdrożenia, które zakończyło się niepowodzeniem.
    + Naprawiono inicjowanie właściwości wewnętrznej używanej w funkcji LocalWebservice.reload(), gdy obiekt usługi został utworzony z istniejącego wdrożenia przy użyciu konstruktora LocalWebservice().
    + Edytowano komunikat o błędzie w celu wyjaśnienia.
    + Dodano nową metodę o nazwie get_access_token() do usługi AksWebservice, która zwraca obiekt AksServiceAccessToken, który zawiera token dostępu, odświeżanie po znaczniku czasu, wygaśnięcie znacznika czasu i typ tokenu. 
    + Wycofana istniejąca metoda get_token() w ucieku AksWebservice, ponieważ nowa metoda zwraca wszystkie informacje zwracane przez tę metodę.
    + Zmodyfikowano dane wyjściowe polecenia az ml service get-access-token. Zmieniono nazwę tokenu na accessToken i refreshBy na refreshAfter. Dodano właściwości expiryOn i tokenType.
    + Naprawiono get_active_runs
  + **azureml-explain-model**
    + Zaktualizowano shap do wersji 0.33.0 i interpret-community do wersji 0.4.*
  + **azureml-interpret**
    + Zaktualizowano shap do wersji 0.33.0 i interpret-community do wersji 0.4.*
  + **azureml-train-automl-runtime**
    + Dodano współczynnik korelacji Matthewsa jako metrykę klasyfikacji dla klasyfikacji binarnej i wieloklasowej.
    + Flaga przestarzałego przetwarzania wstępnego z kodu i zastąpiona cechą -featurization jest domyślnie włączona

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.83

+ **Nowe funkcje**
  + Zestaw danych: Dodaj dwie opcje i dla opcji , aby się nie powiodły, gdy dane mają wartości błędów `on_error` `out_of_range_datetime` zamiast `to_pandas_dataframe` wypełniać je wartością `None` .
  + Obszar roboczy: Dodano flagę dla obszarów roboczych z poufnymi danymi, która umożliwia dalsze szyfrowanie i `hbi_workspace` wyłącza zaawansowaną diagnostykę w obszarach roboczych. Dodaliśmy również obsługę dostarczania własnych kluczy dla skojarzonego wystąpienia usługi Cosmos DB przez określenie parametrów i podczas tworzenia obszaru roboczego, co powoduje utworzenie wystąpienia Cosmos DB w subskrypcji podczas aprowizowania obszaru `cmk_keyvault` `resource_cmk_uri` roboczego. [Przeczytaj więcej tutaj.](./concept-data-encryption.md#azure-cosmos-db)

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-runtime**
    + Naprawiono regresję, która powodowała, że błąd TypeError był wywoływany podczas uruchamiania automl w języku Python w wersji starszej niż 3.5.4.
  + **azureml-core**
    + Usunięto usterkę w ścieżce względnej, która nie rozpoczynała się od , `datastore.upload_files` `./` nie można było użyć.
    + Dodano komunikaty o cofaniu pracy dla wszystkich ścieżek kodu klasy obrazów
    + Naprawiono Zarządzanie modelami adresu URL dla Azure (Chiny) — 21Vianet regionu.
    + Rozwiązano problem, który source_dir nie mógł spakował modeli korzystających z Azure Functions.    
    + Dodano opcję [Environment.build_local() w celu](/python/api/azureml-core/azureml.core.environment.environment) wypychania obrazu do rejestru kontenerów obszaru roboczego usługi AzureML
    + Zaktualizowano zestaw SDK w celu używania nowej biblioteki tokenów w usłudze Azure Synapse w sposób zgodny z powrotem.
  + **azureml-interpret**
    + Usunięto usterkę, która zwracała błąd None, gdy nie było dostępnych wyjaśnień do pobrania. Teraz zgłasza wyjątek, dopasowując zachowanie w innym miejscu.
  + **azureml-pipeline-steps**
    + Niedozwolone przekazywanie s `DatasetConsumptionConfig` do `Estimator` `inputs` parametru , gdy będzie `Estimator` używany w `EstimatorStep` .
  + **azureml-sdk**
    + Dodano klienta rozwiązania AutoML do pakietu azureml-sdk, umożliwiając przesłać zdalne przebiegi rozwiązania AutoML bez instalowania pełnego pakietu AutoML.
  + **azureml-train-automl-client**
    + Poprawiono wyrównanie danych wyjściowych konsoli dla przebiegów autoML
    + Usunięto usterkę, która powodować, że nieprawidłowa wersja pandas może być instalowana na zdalnym amlcompute.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.81

+ **Poprawki błędów i ulepszenia**
  + **azureml-contrib-interpret**
    + defer shap dependency to interpret-community from azureml-interpret (odrocz zależność shap na interpretowanie społeczności na platformie azureml-interpret)
  + **azureml-core**
    + Docelowy obiekt obliczeniowy można teraz określić jako parametr dla odpowiednich obiektów konfiguracji wdrożenia. Jest to nazwa docelowego obiektu obliczeniowego do wdrożenia, a nie obiektu zestawu SDK.
    + Dodano informacje CreatedBy do obiektów Model i Service. Dostęp do through.created_by
    + Naprawiono błąd ContainerImage.run(), który nie był poprawnie skonfigurowany na porcie HTTP kontenera platformy Docker.
    + Poleć `azureml-dataprep` jako opcjonalne polecenie `az ml dataset register` interfejsu wiersza polecenia
    + Usunięto usterkę, `TabularDataset.to_pandas_dataframe` która niepoprawnie wracała do alternatywnego czytnika i drukowała ostrzeżenie.
  + **azureml-explain-model**
    + defer shap dependency to interpret-community from azureml-interpret (odrocz zależność shap na interpretowanie społeczności na platformie azureml-interpret)
  + **azureml-pipeline-core**
    + Dodano nowy krok potoku , aby `NotebookRunnerStep` uruchomić notes lokalny jako krok w potoku.
    + Usunięto przestarzałe get_all dla publishedPipelines, Schedules i PipelineEndpoints
  + **azureml-train-automl-client**
    + Rozpoczęto cofanie data_script jako dane wejściowe dla automatycznegoml.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.79

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Usunięto plik featurizationConfig, który ma być rejestrowany
      + Zaktualizowano rejestrowanie w celu rejestrowania tylko "auto"/"off"/"customized".
  + **azureml-automl-runtime**
    + Dodano obsługę pandas. Seria i pandas. Kategoryczne do wykrywania typu danych kolumny. Wcześniej obsługiwane były tylko numpy.ndarray
      + Dodano powiązane zmiany kodu w celu poprawnej obsługi typów kategorii.
    + Ulepszono interfejs funkcji prognozy: y_pred parametr został opcjonalny. —Ulepszono docstrings.
  + **azureml-contrib-dataset**
    + Usunięto usterkę, która oznaczała, że nie można było zamontowania oznaczonych zestawów danych.
  + **azureml-core**
    + Poprawka usterki dla `Environment.from_existing_conda_environment(name, conda_environment_name)` . Użytkownik może utworzyć wystąpienie środowiska, które jest dokładną repliką środowiska lokalnego
    + Zmieniono domyślne metody zestawów danych dotyczące `include_boundary=True` szeregów czasu.
  + **azureml-train-automl-client**
    + Rozwiązano problem, który pokazywał, że wyniki walidacji nie były wyświetlane, gdy dla pokazywania danych wyjściowych ustawiono wartość false.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.76

+ **Fundamentalne zmiany**
  + Problemy z uaktualnianiem rozwiązania Azureml-Train-AutoML
    + Uaktualnienie do wersji azureml-train-automl>=1.0.76 z narzędzia azureml-train-automl<1.0.76 może spowodować częściowe instalacje, powodując niepowodzenie niektórych importów rozwiązania AutoML. Aby rozwiązać ten problem, możesz uruchomić skrypt instalatora, który znajduje się na stronie https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . Jeśli używasz narzędzia pip bezpośrednio, możesz:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + lub można odinstalować starą wersję przed uaktualnieniem
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-runtime**
    + Podczas obliczania średnich metryk skalarnych dla zadań klasyfikacji binarnej autoML uwzględnia teraz zarówno klasy wartości true, jak i false.
    + Przeniesiono kod uczenia maszynowego i trenowania w środowisku AzureML-AutoML-Core do nowego pakietu AzureML-AutoML-Runtime.
  + **azureml-contrib-dataset**
    + Podczas wywoływania dla zestawu danych z etykietą opcji pobierania można teraz określić, czy zastąpić `to_pandas_dataframe` istniejące pliki.
    + Podczas wywoływania funkcji lub , która powoduje porzucanie szeregu czasowego, etykiety lub kolumny obrazu, odpowiednie możliwości również zostaną usunięte dla zestawu `keep_columns` `drop_columns` danych.
    + Rozwiązano problem z modułu ładującego pytorch dla zadania wykrywania obiektu.
  + **azureml-contrib-interpret**
    + Usunięto widżet wyjaśnienia pulpitu nawigacyjnego z elementu azureml-contrib-interpret, zmieniono pakiet w celu odwołania się do nowego w interpret_community
    + Zaktualizowano wersję programu interpret-community do 0.2.0
  + **azureml-core**
    + Poprawianie `workspace.datasets` wydajności .
    + Dodano możliwość rejestrowania magazynu Azure SQL Database danych przy użyciu uwierzytelniania za pomocą nazwy użytkownika i hasła
    + Poprawka ładowania konfiguracji RunConfiguration ze ścieżek względnych.
    + W przypadku wywoływania funkcji lub , która powoduje porzucanie kolumny szeregów czasu, odpowiednie możliwości również zostaną usunięte `keep_columns` `drop_columns` dla zestawu danych.
  + **azureml-interpret**
    + zaktualizowano wersję społeczności interpret-community do 0.2.0
  + **azureml-pipeline-steps**
    + Udokumentowane obsługiwane wartości kroków `runconfig_pipeline_params` potoku uczenia maszynowego platformy Azure.
  + **azureml-pipeline-core**
    + Dodano opcję interfejsu wiersza polecenia do pobierania danych wyjściowych w formacie JSON dla poleceń potoku.
  + **azureml-train-automl**
    + Podziel element AzureML-Train-AutoML na dwa pakiety: pakiet klienta AzureML-Train-AutoML-Client i pakiet szkoleniowy ML AzureML-Train-AutoML-Runtime
  + **azureml-train-automl-client**
    + Dodano klienta zuzenkiego do przesyłania eksperymentów automatycznego uczenia maszynowego bez konieczności instalowania lokalnie żadnych zależności uczenia maszynowego.
    + Naprawiono rejestrowanie automatycznie wykrytych opóźnień, rozmiarów okien stopniowych i maksymalnych horyzontów w zdalnych przebiegach.
  + **azureml-train-automl-runtime**
    + Dodano nowy pakiet AutoML w celu odizolowania składników uczenia maszynowego i środowiska uruchomieniowego od klienta.
  + **azureml-contrib-train-rl**
    + Dodano obsługę uczenia przez wzmacnianie w zestawie SDK.
    + Dodano obsługę AmlWindowsCompute w zestawie SDK listy RL.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.74

  + **Funkcje w wersji zapoznawczej**
    + **azureml-contrib-dataset**
      + Po zaimportowaniu zestawu danych azureml-contrib-dataset można wywołać element zamiast w celu `Dataset.Labeled.from_json_lines` `._Labeled` utworzenia zestawu danych z etykietą.
      + Podczas wywoływania dla zestawu danych z etykietami z opcją pobierania można teraz określić, czy zastąpić `to_pandas_dataframe` istniejące pliki.
      + Podczas wywoływania funkcji lub powoduje to porzucanie szeregu czasowego, etykiety lub kolumny obrazu, odpowiednie możliwości również zostaną usunięte dla zestawu `keep_columns` `drop_columns` danych.
      + Rozwiązano problemy z modułu ładującego PyTorch podczas wywoływania `dataset.to_torchvision()` .

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Dodano profilowanie modelu do interfejsu wiersza polecenia w wersji zapoznawczej.
    + Rozwiązaliśmy problem powodujący niepowodzenie zmiany powodującej niepowodzenie interfejsu wiersza polecenia usługi AzureML w usłudze Azure Storage.
    + Dodano Load Balancer mlc dla typów AKS
  + **azureml-automl-core**
    + Rozwiązano problem z wykrywaniem maksymalnych horyzontów w szeregach czasu, brakującymi wartościami i wieloma ziarnami.
    + Rozwiązano problem z błędami podczas generowania podziałów krzyżowej weryfikacji.
    + Zastąp tę sekcję komunikatem w formacie markdown, który będzie wyświetlany w informacjach o wersji: - Ulepszono obsługę krótkich ziarn w zestawach danych prognozowania.
    + Rozwiązano problem z maskami niektórych informacji o użytkowniku podczas rejestrowania. — Ulepszone rejestrowanie błędów podczas uruchomiń prognozowania.
    + Dodanie psutil jako zależności conda do automatycznie wygenerowanego pliku wdrożenia yml.
  + **azureml-contrib-dostęp**
    + Rozwiązaliśmy problem powodujący niepowodzenie zmiany powodującej niepowodzenie interfejsu wiersza polecenia usługi AzureML w usłudze Azure Storage.
  + **azureml-core**
    + Usunięto usterkę, która powodowała, że modele wdrożone Azure Functions wytworzyć 500.
    + Rozwiązaliśmy problem, który miał miejsce, gdy plik amlignore nie był stosowany do migawek.
    + Dodano nowy interfejs API amlcompute.get_active_runs który zwraca generator do uruchamiania i uruchamiania w kolejce przebiegów dla danej usługi amlcompute.
    + Dodano Load Balancer mlc dla typów AKS.
    + Dodano append_prefix bool do download_files w run.py i download_artifacts_from_prefix w artifacts_client. Ta flaga jest używana do selektywnego spłaszczania ścieżki pliku źródła, więc do pliku lub folderu jest dodawana tylko nazwa output_directory
    + Rozwiązano problem z deserializacji dla `run_config.yml` użycia zestawu danych.
    + Podczas wywoływania funkcji lub , która powoduje porzucanie kolumny szeregów czasu, odpowiednie możliwości również zostaną usunięte `keep_columns` `drop_columns` dla zestawu danych.
  + **azureml-interpret**
    + Zaktualizowano wersję interpret-community do 0.1.0.3
  + **azureml-train-automl**
    + Rozwiązano problem, który automl_step nie drukować problemów z walidacją.
    + Rozwiązano register_model się pomyślnie, nawet jeśli w środowisku modelu brakuje zależności lokalnie.
    + Rozwiązano problem, który dotyczył sytuacji, w której niektóre przebiegi zdalne nie były włączone dla platformy Docker.
    + Dodaj rejestrowanie wyjątku, który powoduje, że uruchomienie lokalne przedwcześnie się nie powiedzie.
  + **azureml-train-core**
    + Rozważ resume_from przebiegów w obliczeniach automatycznego dostrajania hiperparametrów najlepszych przebiegów podrzędnych.
  + **azureml-pipeline-core**
    + Naprawiono obsługę parametrów w konstrukcji argumentów potoku.
    + Dodano opis potoku i parametr yaml typu kroku.
    + Nowy format yaml dla kroku potoku i dodano ostrzeżenie o cofaniu pracy dla starego formatu.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Środowisko internetowe

Strona docelowa obszaru roboczego do współpracy na stronie została rozszerzona i została ponownie [https://ml.azure.com](https://ml.azure.com) Azure Machine Learning studio.

W programie Studio możesz trenować, testować i wdrażać zasoby Azure Machine Learning, takie jak zestawy danych, potoki, modele, punkty końcowe i nie tylko.

Uzyskaj dostęp do następujących internetowych narzędzi do tworzenia z programu Studio:

| Narzędzie internetowe | Opis | 
|-|-|-|
| Maszyna wirtualna notesu (wersja zapoznawcza) | W pełni zarządzana stacja robocza oparta na chmurze | 
| [Zautomatyzowane uczenie maszynowe](tutorial-first-experiment-automated-ml.md) (wersja zapoznawcza) | Brak doświadczenia w kodzie w zakresie automatyzowania tworzenia modeli uczenia maszynowego | 
| [Projektant](concept-designer.md) | Narzędzie do modelowania uczenia maszynowego typu "przeciągnij i upuść" wcześniej znane jako interfejs wizualny | 


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning ulepszenia projektanta

+ Wcześniej nazywany interfejsem wizualnym 
+    11 nowych [modułów,](algorithm-module-reference/module-reference.md) w tym polecających, klasyfikatorów i narzędzi szkoleniowych, w tym inżynierię cech, krzyżową walidację i przekształcanie danych.

### <a name="r-sdk"></a>Zestaw SDK języka R 
 
Naukowcy danych i deweloperzy AI używają zestawu SDK Azure Machine Learning dla języka [R,](https://github.com/Azure/azureml-sdk-for-r) aby tworzyć i uruchamiać przepływy pracy uczenia maszynowego za pomocą Azure Machine Learning.

Zestaw Azure Machine Learning SDK dla języka R używa `reticulate` pakietu do powiązania z zestawem SDK języka Python. Dzięki powiązaniu bezpośrednio z językem Python zestaw SDK dla języka R umożliwia dostęp do podstawowych obiektów i metod zaimplementowanych w zestawie SDK języka Python z dowolnego środowiska języka R.

Główne możliwości zestawu SDK obejmują:

+    Zarządzanie zasobami w chmurze na potrzeby monitorowania, rejestrowania i organizowania eksperymentów uczenia maszynowego.
+    Trenowanie modeli przy użyciu zasobów w chmurze, w tym trenowanie modelu przyspieszane przez procesor GPU.
+    Wdrażaj modele jako usługi internetowe na Azure Container Instances (ACI) i Azure Kubernetes Service (AKS).

Pełną [dokumentację można znaleźć w](https://azure.github.io/azureml-sdk-for-r) witrynie internetowej pakietu.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integracji z Event Grid 

Azure Machine Learning jest teraz dostawcą zasobów dla usługi Event Grid, zdarzenia uczenia maszynowego można skonfigurować za pomocą interfejsu Azure Portal wiersza polecenia platformy Azure. Użytkownicy mogą tworzyć zdarzenia służące do ukończenia uruchamiania, rejestracji modelu, wdrażania modelu i wykrycia dryfu danych. Te zdarzenia mogą być kierowane do obsługi zdarzeń obsługiwanych przez Event Grid do użycia. Zobacz schemat zdarzeń uczenia [maszynowego i](../event-grid/event-schema-machine-learning.md) [artykuły z samouczkami,](how-to-use-event-grid.md) aby uzyskać więcej informacji.

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.72

+ **Nowe funkcje**
  + Dodano monitory zestawów danych za pośrednictwem pakietu [**azureml-datadrift,**](/python/api/azureml-datadrift) co umożliwia monitorowanie zestawów danych szeregów czasowych pod zakresie dryfu danych lub innych zmian statystycznych w czasie. Alerty i zdarzenia mogą być wyzwalane, jeśli zostanie wykryty dryf lub zostaną spełnione inne warunki dotyczące danych. Szczegółowe [informacje można znaleźć w](how-to-monitor-datasets.md) naszej dokumentacji.
  + Ogłaszamy dwie nowe wersje (zamiennie określane jako SKU) w Azure Machine Learning. W tej wersji można teraz utworzyć podstawowy obszar roboczy lub obszar roboczy Azure Machine Learning Enterprise. Wszystkie istniejące obszary robocze będą domyślnie w wersji Podstawowa. W dowolnym momencie możesz przejść do witryny Azure Portal lub do programu Studio, aby uaktualnić obszar roboczy. Obszar roboczy w jęz. podstawowym lub przedsiębiorstwie można utworzyć z Azure Portal. Przeczytaj [naszą dokumentację,](./how-to-manage-workspace.md) aby dowiedzieć się więcej. Z zestawu SDK wersję obszaru roboczego można określić przy użyciu właściwości "sku" obiektu obszaru roboczego.
  + Ulepszyliśmy również usługę Azure Machine Learning Compute — oprócz wyświetlania dzienników diagnostycznych na potrzeby debugowania można teraz wyświetlać metryki dla klastrów (takich jak łączna liczba węzłów, uruchomione węzły, całkowity limit przydziału rdzeni) w programie Azure Monitor. Ponadto można również wyświetlić aktualnie uruchomione lub w kolejce uruchomienia w klastrze oraz szczegóły, takie jak ip różnych węzłów w klastrze. Można je wyświetlić w portalu lub przy użyciu odpowiednich funkcji w zestawie SDK lub interfejsie wiersza polecenia.

  + **Funkcje w wersji zapoznawczej**
    + Udostępniamy obsługę wersji zapoznawczej szyfrowania dysków lokalnych dysków SSD w Azure Machine Learning Compute. Zdaj bilet pomocy technicznej, aby zezwolić twojej subskrypcji na korzystanie z tej funkcji.
    + Publiczna wersja zapoznawcza Azure Machine Learning wnioskowania wsadowego. Azure Machine Learning wnioskowania wsadowego dotyczy dużych zadań wnioskowania, które nie są wrażliwe na czas. Wnioskowanie wsadowe zapewnia ekonomiczne skalowanie obliczeń wnioskowania z niezrównaną przepływnością dla aplikacji asynchronicznych. Jest ona zoptymalizowana pod kątem wysokiej przepływności, wnioskowania typu "fire-and-forget" w dużych kolekcjach danych.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + Włączone funkcje dla zestawu danych z etykietami
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

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Interfejs wiersza polecenia obsługuje teraz pakowanie modeli.
    + Dodano interfejs wiersza polecenia zestawu danych. Aby uzyskać więcej informacji: `az ml dataset --help`
    + Dodano obsługę wdrażania i pakowania obsługiwanych modeli (ONNX, scikit-learn i TensorFlow) bez wystąpienia inferenceConfig.
    + Dodano flagę zastępowania dla wdrożenia usługi (ACI i AKS) w zestawie SDK i interfejsie wiersza polecenia. Jeśli zostanie podany, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, program utworzy nową usługę.
    + Modele można rejestrować za pomocą dwóch nowych platform: Onnx i Tensorflow. — Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
  + **azureml-automl-core**
    + Trenowania iteracji będzie uruchamiany w procesie podrzędnym tylko wtedy, gdy są ustawione ograniczenia środowiska uruchomieniowego.
    + Dodano barierę kontrolną do prognozowania zadań w celu sprawdzenia, czy określona max_horizon spowoduje problem z pamięcią na danej maszynie. Jeśli tak, zostanie wyświetlony komunikat o barierze ochronnej.
    + Dodano obsługę częstotliwości złożonych, takich jak dwa lata i jeden miesiąc. - Dodano zrozumiały komunikat o błędzie, jeśli nie można określić częstotliwości.
    + Dodawanie wartości domyślnych azureml do automatycznie wygenerowanego pliku conda env w celu rozwiązania błędu wdrażania modelu
    + Zezwalaj na konwertowanie danych Azure Machine Learning potoku na tabelarowy zestaw danych i ich używać w programie `AutoMLStep` .
    + Zaimplementowano aktualizację celu kolumny na potrzeby przesyłania strumieniowego.
    + Zaimplementowano aktualizację parametrów transformatora dla imputer i hashonehotEncoder przesyłania strumieniowego.
    + Dodano bieżący rozmiar danych i minimalny wymagany rozmiar danych do komunikatów o błędach walidacji.
    + Zaktualizowano minimalny wymagany rozmiar danych dla walidacji krzyżowej, aby zagwarantować co najmniej dwie próbki w każdym z nich.
  + **azureml-cli-common**
    + Interfejs wiersza polecenia obsługuje teraz pakowanie modeli.
    + Modele można rejestrować za pomocą dwóch nowych platform: Onnx i Tensorflow.
    + Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
  + **azureml-contrib-gbdt**
    + naprawiono kanał wydania dla notesu
    + Dodano ostrzeżenie dotyczące docelowego obiektu obliczeniowego spoza obiektu obliczeniowego AmlCompute, które nie jest przez nas obsługuje
    + Dodano narzędzie do szacowania LightGMB do pakietu azureml-contrib-gbdt
  + [**azureml-core**](/python/api/azureml-core)
    + Interfejs wiersza polecenia obsługuje teraz pakowanie modeli.
    + Dodawania ostrzeżenia o wycofaniu przestarzałych interfejsów API zestawu danych. Zobacz Powiadomienie o zmianie interfejsu API zestawu danych na stronie https://aka.ms/tabular-dataset .
    + Zmień [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) , aby zwrócić nazwę i wersję rejestracji, jeśli zestaw danych jest zarejestrowany.
    + Naprawiono usterkę, która oznaczała, że polecenia ScriptRunConfig z zestawem danych jako argumentem nie można było wielokrotnie używać do przesyłania przebiegów eksperymentu.
    + Zestawy danych pobierane podczas uruchamiania będą śledzone i widoczne na stronie szczegółów uruchomienia lub przez wywołanie po [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) zakończeniu uruchomienia.
    + Zezwalaj na konwertowanie danych pośrednich Azure Machine Learning potoku na tabelarowy zestaw danych i ich używać w programie [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) .
    + Dodano obsługę wdrażania i pakowania obsługiwanych modeli (ONNX, scikit-learn i TensorFlow) bez wystąpienia aplikacji InferenceConfig.
    + Dodano flagę zastępowania dla wdrożenia usługi (ACI i AKS) w zestawie SDK i interfejsie wiersza polecenia. Jeśli zostanie podany, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, program utworzy nową usługę.
    +  Modele można rejestrować za pomocą dwóch nowych platform: Onnx i Tensorflow. Rejestracja modelu akceptuje przykładowe dane wejściowe, przykładowe dane wyjściowe i konfigurację zasobów dla modelu.
    + Dodano nowy magazyn danych dla Azure Database for MySQL. Dodano przykład użycia Azure Database for MySQL w kroku DataTransferStep w Azure Machine Learning Pipelines.
    + Dodano funkcję dodawania i usuwania tagów z eksperymentów Dodano funkcjonalność usuwania tagów z przebiegów
    + Dodano flagę zastępowania dla wdrożenia usługi (ACI i AKS) w zestawie SDK i interfejsie wiersza polecenia. Jeśli zostanie podany, zastąpi istniejącą usługę, jeśli usługa o nazwie już istnieje. Jeśli usługa nie istnieje, program utworzy nową usługę.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Przeniesiono z `azureml-contrib-datadrift` do `azureml-datadrift`
    + Dodano obsługę monitorowania zestawów danych szeregów czasu dla dryfu i innych miar statystycznych
    + Nowe metody `create_from_model()` i `create_from_dataset()` do klasy [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) . Metoda `create()` zostanie wycofana.
    + Korekty wizualizacji w języku Python i interfejsie użytkownika w Azure Machine Learning studio.
    + Obsługa cotygodniowego i comiesięcznego planowania monitorów w przypadku monitorów zestawów danych oprócz codziennego.
    + Obsługa wypełniania metryk monitora danych w celu analizowania danych historycznych dla monitorów zestawów danych.
    + Różne poprawki błędów
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + Narzędzie azureml-dataprep nie jest już potrzebne do przesyłania Azure Machine Learning potoku z pliku `yaml` potoku.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Dodawanie wartości domyślnych azureml do automatycznie wygenerowanego pliku conda env w celu rozwiązania błędu wdrażania modelu
    + Zdalne trenowanie automatycznego rozwiązaniaml zawiera teraz wartości domyślne azureml umożliwiające ponowne używanie trenowania na wnioskowanie.
  + **azureml-train-core**
    + Dodano obsługę PyTorch 1.3 w [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) estymatorze

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Interfejs wizualny (wersja zapoznawcza)

+ Interfejs Azure Machine Learning (wersja zapoznawcza) został zmodernizowany w celu uruchamiania w Azure Machine Learning [potokach.](concept-ml-pipelines.md) Potoki (wcześniej znane jako eksperymenty) w interfejsie wizualnym są teraz w pełni zintegrowane z podstawowym Azure Machine Learning użytkownika.
  + Ujednolicone środowisko zarządzania z zasobami zestawu SDK
  + Wersjonalizowanie i śledzenie modeli interfejsu wizualnego, potoków i punktów końcowych
  + Przeprojektowany interfejs użytkownika
  + Dodano wdrożenie wnioskowania wsadowego
  + Dodano Azure Kubernetes Service (AKS) dla docelowych obiektów obliczeniowych wnioskowania
  + Nowy przepływ pracy tworzenia potoku w kroku języka Python
  + Nowa [strona docelowa](https://ml.azure.com) narzędzi do tworzenia wizualizacji

+ **Nowe moduły**
  + Stosowanie operacji matematycznych
  + Stosowanie przekształcenia SQL
  + Obcinanie wartości
  + Podsumowuje dane
  + Importowanie z SQL Database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.69

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Ograniczenie objaśnień modelu do najlepszego uruchomienia, a nie obliczeń objaśnień dla każdego uruchomienia. Wprowadzenie tej zmiany zachowania dla lokalnych, zdalnych i ADB.
    + Dodano obsługę objaśnień modelu na żądanie dla interfejsu użytkownika
    + Dodano psutil jako zależność i dodano psutil jako zależność `automl` conda w amlcompute.
    + Rozwiązano problem z opóźnieniami heurystycznymi i kroczącymi rozmiarami okien w zestawach danych prognozowania, których szereg może powodować błędy algebry liniowej
      + Dodano drukowanie dla parametrów określonych heucystycznie w przebiegach prognozowania.
  + **azureml-contrib-datadrift**
    + Dodano ochronę podczas tworzenia metryk wyjściowych, jeśli dryf na poziomie zestawu danych nie znajduje się w pierwszej sekcji.
  + **azureml-contrib-interpret**
    + Nazwa pakietu azureml-contrib-explain-model została zmieniona na azureml-contrib-interpret
  + **azureml-core**
    + Dodano interfejs API do wyrejestrowywał zestawy danych. `dataset.unregister_all_versions()`
    + Nazwa pakietu azureml-contrib-explain-model została zmieniona na azureml-contrib-interpret.
  + **[azureml-core](/python/api/azureml-core)**
    + Dodano interfejs API do wyrejestrowywał zestawy danych. Dataset. [unregister_all_versions()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Dodano interfejs API zestawu danych w celu sprawdzenia czasu zmiany danych. `dataset.data_changed_time`.
    + Możliwość wprowadzania danych wejściowych i do `FileDataset` `TabularDataset` , i w `PythonScriptStep` `EstimatorStep` `HyperDriveStep` potoku Azure Machine Learning potoku
    + Poprawiono `FileDataset.mount` wydajność folderów z dużą liczbą plików
    + Możliwość wprowadzania danych Do [PythonScriptStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)i HyperDriveStep w potoku Azure Machine Learning [FileDataset i](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) [FileDataset.](/python/api/azureml-core/azureml.data.filedataset) [](/python/api/azureml-core/azureml.data.tabulardataset)
    + Wydajność zestawu danych FileDataset. [Ulepszono mount()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) dla folderów z dużą liczbą plików
    + Dodano adres URL do znanych zaleceń dotyczących błędów w szczegółach uruchomienia.
    + Usunięto usterkę w run.get_metrics gdzie żądania nie powiodły się, jeśli przebieg miał zbyt wiele dzieci
    + Usunięto usterkę w [run.get_metrics](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) gdzie żądania nie powiodły się, jeśli przebieg miał zbyt wiele dzieci
    + Dodano obsługę uwierzytelniania w klastrze Arcadia.
    + Utworzenie obiektu Eksperyment pobiera lub tworzy eksperyment w obszarze roboczym Azure Machine Learning do śledzenia historii uruchamiania. Identyfikator eksperymentu i zarchiwizowane czasy są wypełniane w obiekcie Eksperyment podczas tworzenia. Przykład: experiment = Experiment(workspace, "New Experiment") experiment_id = experiment.id archive() i reactivate() to funkcje, które można wywołać w eksperymencie w celu ukrycia i przywrócenia eksperymentu w interfejsie użytkownika lub zwrócenia go domyślnie w wywołaniu eksperymentu listy. Jeśli zostanie utworzony nowy eksperyment o takiej samej nazwie jak zarchiwizowany eksperyment, możesz zmienić nazwę zarchiwizowany eksperyment podczas jego ponownej aktywacji, przekazując nową nazwę. Może być tylko jeden aktywny eksperyment o danej nazwie. Przykład: experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # Create new active experiment with the same name as the archived (Eksperyment1.archive() # Utwórz nowy aktywny eksperyment o takiej samej nazwie jak zarchiwizowane. experiment2. = Experiment(workspace, "Active Experiment") experiment1.reactivate(new_name="Previous Active Experiment") Statyczna metoda list() w eksperymencie może przyjąć filtr nazwy i filtr ViewType. Wartości ViewType to "ACTIVE_ONLY", "ARCHIVED_ONLY" i "ALL". Przykład: archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + Obsługa wdrażania modelu i aktualizacji usługi przy użyciu środowiska
  + **azureml-datadrift**
    + Atrybut show klasy DataDriftDector nie będzie już obsługiwać opcjonalnego argumentu "with_details". Atrybut show będzie przedstawiać tylko współczynnik dryfu danych i udział dryfu danych kolumn cech.
    + Zmiany zachowania atrybutu DataDriftDetector "get_output":
      + Parametry wejściowe start_time, end_time opcjonalne, a nie obowiązkowe;
      + Dane wejściowe start_time i/lub end_time z określonym run_id w tym samym wywołaniach spowoduje wyjątek błędu wartości, ponieważ wzajemnie się wykluczają
      + Dzięki wejściowi start_time i/lub end_time zostaną zwrócone tylko wyniki zaplanowanych przebiegów.
      + Parametr "daily_latest_only" jest przestarzały.
    + Obsługa pobierania danych wyjściowych dryfu danych opartych na zestawie danych.
  + **azureml-explain-model**
    + Zmienia nazwę pakietu AzureML-explain-model na AzureML-interpret, zachowując na razie stary pakiet w celu zapewnienia zgodności z poprzednimi wersjami
    + usunięto `automl` usterkę z nieprzetworzoną objaśnieniami ustawioną na zadanie klasyfikacji zamiast regresji domyślnie podczas pobierania z klasy ExplanationClient
    + Dodawania obsługi funkcji `ScoringExplainer` do bezpośredniego tworzenia przy użyciu `MimicWrapper`
  + **azureml-pipeline-core**
    + Zwiększona wydajność tworzenia dużych potoków
  + **azureml-train-core**
    + Dodano obsługę programu TensorFlow 2.0 w programie TensorFlow Estimator
  + **azureml-train-automl**
    + Utworzenie obiektu [Eksperyment](/python/api/azureml-core/azureml.core.experiment.experiment) pobiera lub tworzy eksperyment w obszarze roboczym Azure Machine Learning do śledzenia historii uruchamiania. Identyfikator eksperymentu i zarchiwizowane czasy są wypełniane w obiekcie Eksperyment podczas tworzenia. Przykład:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        [funkcje archive()](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) i [reactivate()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) to funkcje, które można wywołać w eksperymencie w celu ukrycia i przywrócenia eksperymentu z pokazywania w interfejsie użytkownika lub zwracania go domyślnie w wywołaniu eksperymentów listy. Jeśli zostanie utworzony nowy eksperyment o takiej samej nazwie jak zarchiwizowane eksperymenty, możesz zmienić nazwę zarchiwizowanego eksperymentu podczas jego ponownej aktywacji, przekazując nową nazwę. Może być tylko jeden aktywny eksperyment o danej nazwie. Przykład:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        Metoda statyczna [list() w](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) eksperymencie może przyjąć filtr nazwy i filtr ViewType. Wartości ViewType to "ACTIVE_ONLY", "ARCHIVED_ONLY" i "ALL". Przykład:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Obsługa wdrażania modeli i aktualizacji usług przy użyciu środowiska.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + Atrybut show klasy [DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) nie będzie już obsługiował opcjonalnego argumentu "with_details". Atrybut show będzie przedstawiać tylko współczynnik dryfu danych i udział dryfu danych w kolumnach cech.
    + DataDriftDetector function [get_output]python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none--end-time-none--run-id-none-) — zmiany zachowania:
      + Parametry wejściowe start_time, end_time opcjonalne, a nie obowiązkowe;
      + Specyficzne dla start_time i/lub end_time z określonym run_id w tym samym wywołania spowoduje wyjątek błędu wartości, ponieważ wzajemnie się wykluczają;
      + Dane wejściowe specyficzne start_time i/lub end_time zwracane są tylko wyniki zaplanowanych przebiegów;
      + Parametr "daily_latest_only" jest przestarzały.
    + Obsługa pobierania danych wyjściowych dryfu danych opartych na zestawie danych.
  + **azureml-explain-model**
    + Dodanie obsługi obiektu [ScoringExplainer,](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer) który ma zostać utworzony bezpośrednio przy użyciu narzędzia MimicWrapper
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Zwiększona wydajność tworzenia dużych potoków.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + Dodano obsługę programu TensorFlow 2.0 w [estymatorze TensorFlow.](/python/api/azureml-train-core/azureml.train.dnn.tensorflow)
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Przebieg nadrzędny nie będzie już nieudany, gdy iteracja konfiguracji nie powiedzie się, ponieważ aranżacja już ją zajmuje.
    + Dodano obsługę lokalnych platform Docker i local-conda dla eksperymentów autoML
    + Dodano obsługę lokalnych platform Docker i local-conda dla eksperymentów autoML.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nowe środowisko sieci Web (wersja zapoznawcza) dla Azure Machine Learning roboczych

Karta Eksperyment w nowym portalu obszaru [roboczego](https://ml.azure.com) została zaktualizowana, dzięki czemu badacze danych mogą monitorować eksperymenty w bardziej performantny sposób. Możesz eksplorować następujące funkcje:
+ Metadane eksperymentu w celu łatwego filtrowania i sortowania listy eksperymentów
+ Uproszczone i performantowe strony szczegółów eksperymentu, które umożliwiają wizualizowanie i porównywanie przebiegów
+ Nowy projekt do uruchamiania stron szczegółów w celu zrozumienia i monitorowania przebiegów trenowania

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.65

  + **Nowe funkcje**
    + Dodano środowiska z curated. Te środowiska zostały wstępnie skonfigurowane przy użyciu bibliotek dla typowych zadań uczenia maszynowego oraz zostały wstępnie skompilowane i zapisane w pamięci podręcznej jako obrazy platformy Docker w celu szybszego wykonywania. Są one domyślnie wyświetlane na liście środowiska obszaru roboczego z prefiksem "AzureML".
    + Dodano środowiska z curated. Te środowiska zostały wstępnie skonfigurowane przy użyciu bibliotek dla typowych zadań uczenia maszynowego oraz zostały wstępnie skompilowane i zapisane w pamięci podręcznej jako obrazy platformy Docker w celu szybszego wykonywania. Są one domyślnie wyświetlane [na](/python/api/azureml-core/azureml.core.workspace%28class%29)liście środowiska obszaru roboczego z prefiksem "AzureML".

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Dodano obsługę konwersji ONNX dla ADB i HDI

+ **Funkcje w wersji zapoznawczej**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Obsługiwane funkcje BERT i BiLSTM jako do cechatora tekstu (tylko wersja zapoznawcza)
    + Obsługiwane dostosowywanie cech na potrzeby kolumn i parametrów transformatora (tylko wersja zapoznawcza)
    + Obsługiwane nieprzetworzone wyjaśnienia, gdy użytkownik włącza wyjaśnienie modelu podczas trenowania (tylko wersja zapoznawcza)
    + Dodano `timeseries` prognozowanie w trybie trenowania (tylko wersja zapoznawcza)

  + **azureml-contrib-datadrift**
    + Pakiety przeniesione z lokalizacji azureml-contrib-datadrift do lokalizacji azureml-datadrift; Pakiet `contrib` zostanie usunięty w przyszłej wersji

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Wprowadzono cechowanieConfig do autoMLConfig i AutoMLBaseSettings
    + Wprowadzono cechowanieConfig do [autoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) i AutoMLBaseSettings
      + Przesłoń cel kolumny na potrzeby cechowania przy użyciu danego typu kolumny i funkcji
      + Zastępowanie parametrów transformatora
    + Dodano komunikat o explain_model() i retrieve_model_explanations()
    + Dodano wytębniany potok (tylko wersja zapoznawcza)
    + Dodano komunikat o explain_model() i retrieve_model_explanations().
    + Dodano wytęchiwalny potok (tylko wersja zapoznawcza).
    + Dodano obsługę automatycznego wykrywania opóźnień docelowych, stopniowego rozmiaru okna i maksymalnego horyzontu. Jeśli jeden z target_lags, target_rolling_window_size lub max_horizon jest ustawiony na "auto", heurystyczne zostaną zastosowane do oszacowania wartości odpowiedniego parametru na podstawie danych treningowych.
    + Naprawiono prognozowanie w przypadku, gdy zestaw danych zawiera jedną kolumnę ziarna, to ziarno jest typu liczbowego i istnieje przerwa między zestawem trenowania i zestawem testowym
    + Naprawiono komunikat o błędzie zduplikowanego indeksu w zdalnym uruchomieniu zadań prognozowania
    + Naprawiono prognozowanie w przypadku, gdy zestaw danych zawiera jedną kolumnę ziarna, to ziarno jest typu liczbowego i istnieje przerwa między zestawem trenowania i zestawem testowym.
    + Naprawiono komunikat o błędzie zduplikowanego indeksu w zdalnym uruchomieniu w zadaniach prognozowania.
    + Dodano barierę kontrolną w celu sprawdzenia, czy zestaw danych jest niezrównoważony. Jeśli tak, komunikat o barierze ochronnej zostanie zapisany w konsoli.
  + **azureml-core**
    + Dodano możliwość pobierania adresu URL sygnatury dostępu współdzielonego w celu modelowania w magazynie za pośrednictwem obiektu modelu. Przykład: model.get_sas_url()
    + Wprowadzenie `run.get_details()['datasets']` do uzyskania zestawów danych skojarzonych z przesłanym przebiegiem
    + Dodaj interfejs `Dataset.Tabular.from_json_lines_files` API, aby utworzyć zestaw TabularDataset z plików wierszy JSON. Aby dowiedzieć się więcej na temat tych danych tabelarowych w plikach JSON Lines w obszarze TabularDataset, zapoznaj się z [tym artykułem,](how-to-create-register-datasets.md) aby uzyskać dokumentację.
    + Dodano dodatkowe pola rozmiaru maszyny wirtualnej (dysk systemu operacyjnego, liczba procesorów GPU) do supported_vmsizes ()
    + Dodano dodatkowe pola do list_nodes (), aby pokazać przebieg, prywatny i publiczny adres IP, port itp.
    + Możliwość określenia nowego pola podczas aprowizowania klastra — remotelogin_port_public_access które można włączyć lub wyłączyć w zależności od tego, czy port SSH ma być otwarty, czy zamknięty w czasie tworzenia klastra. Jeśli go nie określisz, usługa będzie inteligentnie otwierać lub zamykać port w zależności od tego, czy klaster jest wdrażany w sieci wirtualnej.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + Dodano możliwość pobierania adresu URL sygnatury dostępu współdzielonego w celu modelowania w magazynie za pośrednictwem obiektu modelu. Przykład: model. [get_sas_url()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + Wprowadzenie uruchomienia. [get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['zestawy danych'] w celu uzyskania zestawów danych skojarzonych z przesłanym przebiegiem
    + Dodaj interfejs API `Dataset.Tabular` .[ from_json_lines_files()](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) w celu utworzenia zestawu danych TabularDataset z plików JSON Lines. Aby dowiedzieć się więcej na temat tych danych tabelarowych w plikach JSON Lines w obszarze TabularDataset, odwiedź https://aka.ms/azureml-data stronę dokumentacji.
    + Dodano dodatkowe pola rozmiaru maszyny wirtualnej (dysk systemu operacyjnego, liczba procesorów GPU) [do funkcji supported_vmsizes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + Dodano dodatkowe pola [do list_nodes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) w celu pokazania uruchomienia, prywatnego i publicznego adresu IP, portu itp.
    + Możliwość określenia nowego pola [](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) podczas aprowizowania klastra, które można ustawić na włączone lub wyłączone w zależności od tego, czy port SSH ma być otwarty, czy zamknięty w czasie tworzenia klastra. Jeśli go nie określisz, usługa będzie inteligentnie otwierać lub zamykać port w zależności od tego, czy klaster jest wdrażany w sieci wirtualnej.
  + **azureml-explain-model**
    + Ulepszona dokumentacja dotycząca objaśnień danych wyjściowych w scenariuszu klasyfikacji.
    + Dodano możliwość przekazywania przewidywanych wartości y w objaśnieniu przykładów oceny. Odblokowuje bardziej przydatne wizualizacje.
    + Dodano właściwość objaśnienia do programu MimicWrapper, aby umożliwić pobieranie bazowego obiektu MimicExplainer.
  + **azureml-pipeline-core**
    + Dodano notes opisujący moduły Module, ModuleVersion i ModuleStep
  + **azureml-pipeline-steps**
    + Dodano krok RScriptStep do obsługi uruchamiania skryptu języka R za pośrednictwem potoku AML.
    + Naprawiono analizowanie parametrów metadanych w poleceniu AzureBatchStep, które powodowało komunikat o błędzie "Nie określono przypisania parametru SubscriptionId".
  + **azureml-train-automl**
    + Obsługiwane training_data, validation_data, label_column_name, weight_column_name jako format danych wejściowych
    + Dodano komunikat o explain_model() i retrieve_model_explanations()
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Dodano [notes opisujący](https://aka.ms/pl-modulestep) [moduły Module](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), [ModuleVersion i [ModuleStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + Dodano [krok RScriptStep do](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) obsługi uruchamiania skryptu języka R za pośrednictwem potoku AML.
    + Naprawiono analizowanie parametrów metadanych w poleceniu [AzureBatchStep, które powodowało błąd "Nie określono przypisania parametru SubscriptionId".
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Obsługiwane training_data, validation_data, label_column_name, weight_column_name jako format danych wejściowych.
    + Dodano komunikat o [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) i [retrieve_model_explanations()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-).


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.62

+ **Nowe funkcje**
  + Wprowadzono `timeseries`  cechę dla zestawu TabularDataset. Ta cecha umożliwia łatwe filtrowanie sygnatury czasowej danych w tabularDataset, na przykład między zakresem czasu a najnowszymi danymi.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb na przykładowy notes.
  + Włączono szkolenie za pomocą funkcji TabularDataset i FileDataset. 

  + **azureml-train-core**
      + Dodano `Nccl` `Gloo` obsługę i w estymatorze PyTorch

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Wycofaliśmy ustawienie autoML "lag_length" i LaggingTransformer.
    + Naprawiono prawidłową walidację danych wejściowych, jeśli są one określone w formacie przepływu danych
    + Zmodyfikowano plik fit_pipeline.py, aby wygenerować grafowy kod JSON i przekazać go do artefaktów.
    + Renderowany graf w `userrun` obszarze przy użyciu `Cytoscape` .
  + **azureml-core**
    + Ponownie omów obsługę wyjątków w kodzie ADB i wprowadzaj zmiany w pliku zgodnie z nową obsługą błędów
    + Dodano automatyczne uwierzytelnianie MSI dla maszyn wirtualnych notesu.
    + Usunięto usterkę, w przypadku której można było przekazać uszkodzone lub puste modele z powodu nieudanych ponownych prób.
    + Usunięto usterkę, która pozwalała zmieniać nazwę podczas zmiany trybu (na przykład podczas wywoływania `DataReference` `DataReference` funkcji , lub `as_upload` `as_download` `as_mount` ).
    + Dla `mount_point` i należy wprowadzić opcjonalne dla i `target_path` `FileDataset.mount` `FileDataset.download` .
    + Wyjątek, że nie można odnaleźć kolumny znacznika czasu zostanie odrzucony, jeśli interfejs API powiązany z szeregami czasowym zostanie wywołany bez przypisanej kolumny znacznika czasu lub zostaną usunięte przypisane kolumny znacznika czasu.
    + Kolumny szeregów czasu powinny być przypisane z kolumną, której typem jest Data, w przeciwnym razie oczekiwany jest wyjątek
    + Kolumny szeregów czasu przypisujące kolumny "with_timestamp_columns" interfejsu API mogą przyjmować wartość Brak zgrubną/zgrubną nazwą kolumny znacznika czasu, co spowoduje wyczyszczenie wcześniej przypisanych kolumn sygnatury czasowej.
    + Wyjątek zostanie zgłoszony, gdy zgrubna lub grubna kolumna znacznika czasu zostanie upuszczona ze wskazaniem dla użytkownika, że porzucanie może zostać wykonane po wykluczeniu kolumny znacznika czasu na liście upuszczania lub wywołaj kolumnę with_time_stamp z wartością Brak, aby zwolnić kolumny znaczników czasu
    + Wyjątek zostanie zgłoszony, gdy zgrubna lub grubna kolumna znacznika czasu nie jest uwzględniona na liście kolumn keep ze wskazaniem dla użytkownika, że zachowanie może zostać wykonane po uwzględnieniu kolumny znacznika czasu na liście zachowaj kolumnę lub wywołaj kolumnę with_time_stamp z wartością Brak, aby zwolnić kolumny sygnatury czasowej.
    + Dodano rejestrowanie dotyczące rozmiaru zarejestrowanego modelu.
  + **azureml-explain-model**
    + Naprawiono ostrzeżenie wyświetlane w konsoli, gdy nie zainstalowano "pakowania" pakietu języka Python: "Przy użyciu starszej niż obsługiwana wersja lightgbm uaktualnij do wersji większej niż 2.2.1"
    + Naprawiono wyjaśnienie modelu pobierania z fragmentami dla globalnych wyjaśnień z wieloma funkcjami
    + Naprawiono brak przykładów inicjalizacji w objaśnieniu danych wyjściowych dla objaśnień naśladujących
    + Naprawiono błąd niezmienny we właściwościach zestawu podczas przekazywania z klientem wyjaśnienia przy użyciu dwóch różnych typów modeli
    + Dodano get_raw do oceniania explainer.explain(), dzięki czemu jeden objaśnienia oceniania mogą zwracać zarówno wartości inżynierowane, jak i nieprzetworzone.
  + **azureml-train-automl**
    + Wprowadzono publiczne interfejsy API z rozwiązania AutoML do obsługi objaśnień z zestawu EXPLAIN SDK — nowszego sposobu obsługi wyjaśnień rozwiązania AutoML przez oddzielenie cech autoML i wyjaśnienie zestawu SDK — zintegrowana obsługa nieprzetworzonych wyjaśnień z zestawu SDK objaśnień azureml dla modeli `automl` AutoML.
    + Usuwanie azureml-defaults ze zdalnych środowisk szkoleniowych.
    + Zmieniono domyślną lokalizację magazynu pamięci podręcznej z fileCacheStore opartą na jednej na AzureFileCacheStore w przypadku automatycznego Azure Databricks ścieżki kodu.
    + Naprawiono prawidłową walidację danych wejściowych, jeśli są one określone w formacie przepływu danych
  + **azureml-train-core**
    + Wycofano source_directory_data_store wycofywania.
    + Dodano możliwość zastępowania wersji zainstalowanych pakietów azureml.
    + Dodano obsługę pliku dockerfile `environment_definition` w parametrze w estymatorach.
    + Uproszczone rozproszone parametry trenowania w estymatorach.

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Nowe środowisko sieci Web (wersja zapoznawcza) dla Azure Machine Learning roboczych
Nowe środowisko internetowe umożliwia badaczom danych i inżynierom danych ukończenie kompleksowego cyklu życia uczenia maszynowego, od wstępnego dobierania i wizualizowania danych, przez trenowanie i wdrażanie modeli w jednej lokalizacji.

![Azure Machine Learning obszaru roboczego (wersja zapoznawcza)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Najważniejsze funkcje:**

Za pomocą tego Azure Machine Learning interfejsu można teraz:
+ Zarządzanie notesami lub łączenie z programem Jupyter
+ [Uruchamianie eksperymentów zautomatyzowanego uczenia maszynowego](tutorial-first-experiment-automated-ml.md)
+ [Tworzenie zestawów danych z plików lokalnych, magazynów danych i & plików internetowych](how-to-create-register-datasets.md)
+ Eksplorowanie & przygotowania zestawów danych do utworzenia modelu
+ Monitorowanie dryfu danych dla modeli
+ Wyświetlanie ostatnich zasobów z pulpitu nawigacyjnego

Obecnie w tej wersji obsługiwane są następujące przeglądarki: Chrome, Firefox, Safari i Microsoft Edge zapoznawcza.

**Znane problemy:**

1. Odśwież przeglądarkę, jeśli zostanie wyświetlony błąd "Coś poszło nie tak! Błąd podczas ładowania plików fragmentów", gdy wdrażanie jest w toku.

1. Nie można usunąć pliku ani zmienić jego nazwy w notesach i plikach. W publicznej wersji zapoznawczej można używać interfejsu użytkownika programu Jupyter lub terminalu na maszynie wirtualnej notesu do wykonywania operacji aktualizacji plików. Ponieważ jest to zainstalowany sieciowy system plików, wszystkie zmiany wprowadzone na maszynie wirtualnej notesu są natychmiast odzwierciedlane w obszarze roboczym notesu.

1. Aby za pomocą SSH na maszynie wirtualnej notesu:
   1. Znajdź klucze SSH utworzone podczas konfigurowania maszyny wirtualnej. Możesz też znaleźć klucze w obszarze roboczym usługi Azure Machine Learning > otworzyć kartę Obliczenia, > znajdź maszynę wirtualną notesu na liście, > otworzyć jej właściwości: skopiuj klucze z okna dialogowego.
   1. Zaimportuj te publiczne i prywatne klucze SSH na komputer lokalny.
   1. Użyj ich do naśłodowego ssh do maszyny wirtualnej notesu.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.60

+ **Nowe funkcje**
  + Wprowadzono zestaw FileDataset, który odwołuje się do jednego lub wielu plików w magazynach danych lub publicznych adresach URL. Pliki mogą być w dowolnym formacie. Zestaw danych FileDataset zapewnia możliwość pobrania lub instalacji plików w obliczeniach. 
  + Dodano obsługę kodu Yaml potoku dla kroków PythonScript, Adla Step, Databricks Step, DataTransferStep i AzureBatch

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Funkcja AutoArima jest teraz sugerowanym potokiem tylko w wersji zapoznawczej.
    + Ulepszone raportowanie błędów prognozowania.
    + Ulepszono rejestrowanie przy użyciu wyjątków niestandardowych zamiast ogólnych w zadaniach prognozowania.
    + Usunięto sprawdzanie max_concurrent_iterations, aby było mniejsze niż łączna liczba iteracji.
    + Modele autoML zwracają teraz autoMLExceptions
    + Ta wersja zwiększa wydajność wykonywania lokalnych przebiegów zautomatyzowanego uczenia maszynowego.
  + **azureml-core**
    + Wprowadź Dataset.get_all(workspace), który zwraca słownik obiektów i z `TabularDataset` `FileDataset` kluczem z ich nazwą rejestracji.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + Wprowadź `parition_format` jako argument do i `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` . Informacje o partycji każdej ścieżki danych zostaną wyodrębnione do kolumn na podstawie określonego formatu. Element "{column_name}" tworzy kolumnę ciągu, a element "{column_name:yyyy/MM/dd/HH/mm/ss}" tworzy kolumnę daty/godziny, w której kolumny "yyyy", "MM", "dd", "HH", "mm" i "ss" są używane do wyodrębniania roku, miesiąca, dnia, godziny, minuty i sekundy dla typu data/godzina. Nazwa partition_format powinna rozpoczynać się od pozycji pierwszego klucza partycji do końca ścieżki pliku. Na przykład, biorąc pod uwagę ścieżkę ".. /USA/2019/01/01/data.csv', gdzie partycja jest według kraju i godziny, partition_format="/{Country}/{PartitionDate:yyyy/MM/dd}/data.csv" tworzy kolumnę ciągu "Country" z wartością "USA" i kolumną daty/godziny "PartitionDate" z wartością "2019-01-01".
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + Wprowadź `partition_format` jako argument do i `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_parquet.files` . Informacje o partycji każdej ścieżki danych zostaną wyodrębnione do kolumn na podstawie określonego formatu. Element "{column_name}" tworzy kolumnę ciągu, a element "{column_name:yyyy/MM/dd/HH/mm/ss}" tworzy kolumnę daty/godziny, gdzie "yyyy", "MM", "dd", "HH", "mm" i "ss" są używane do wyodrębniania roku, miesiąca, dnia, godziny, minuty i sekundy dla typu data/godzina. Nazwa partition_format powinna rozpoczynać się od pozycji pierwszego klucza partycji do końca ścieżki pliku. Na przykład, biorąc pod uwagę ścieżkę ".. /USA/2019/01/01/data.csv", gdzie partycja jest według kraju i godziny, partition_format="/{Kraj}/{PartitionDate:yyyy/MM/dd}/data.csv" tworzy kolumnę ciągu "Country" z wartością "USA" i kolumną daty/godziny "PartitionDate" z wartością "2019-01-01".
    + `to_csv_files` Dodano metody i `to_parquet_files` do metody `TabularDataset` . Te metody umożliwiają konwersję między a i przez konwersję danych `TabularDataset` na pliki w określonym `FileDataset` formacie.
    + Automatycznie loguj się do podstawowego rejestru obrazów podczas zapisywania pliku Dockerfile wygenerowanego przez pakiet Model.package().
    + "gpu_support" nie jest już konieczne; AML teraz automatycznie wykrywa rozszerzenie nvidia docker i używa go, gdy jest dostępne. Zostanie on usunięty w przyszłej wersji.
    + Dodano obsługę tworzenia, aktualizowania i używania potoku PipelineDrafts.
    + Ta wersja zwiększa wydajność wykonywania lokalnych przebiegów zautomatyzowanego uczenia maszynowego.
    + Użytkownicy mogą query metrics from run history by name (Użytkownicy mogą zapytania dotyczące metryk z historii uruchamiania według nazwy).
    + Ulepszono rejestrowanie przy użyciu wyjątków niestandardowych zamiast ogólnych w zadaniach prognozowania.
  + **azureml-explain-model**
    + Dodano feature_maps do nowego elementu MimicWrapper, dzięki czemu użytkownicy mogą uzyskać nieprzetworzone wyjaśnienia funkcji.
    + Przekazywanie zestawów danych jest teraz domyślnie wyłączone w celu wyjaśnienia przekazania i można je ponownie włączyć za pomocą upload_datasets=True
    + Dodano parametry filtrowania is_law "is_law" do listy wyjaśnień i funkcji pobierania.
    + Dodaje metodę `get_raw_explanation(feature_maps)` do globalnych i lokalnych obiektów objaśnień.
    + Dodano sprawdzanie wersji do lightgbm z drukowanym ostrzeżeniem, jeśli jest to obsługiwana wersja poniżej
    + Zoptymalizowane użycie pamięci podczas przetwarzania wsadowego objaśnień
    + Modele automatycznegoml zwracają teraz autoMLExceptions
  + **azureml-pipeline-core**
    + Dodano obsługę tworzenia, aktualizowania i używania potoku PipelineDrafts — może służyć do obsługi modyfikowalnych definicji potoków i używania ich interaktywnie do uruchamiania
  + **azureml-train-automl**
    + Utworzono funkcję instalowania określonych wersji pytorch z procesorem GPU w wersji 1.1.0, zestawu narzędzi 9.0 i transformatorów pytorch, które są wymagane do włączenia BERT/XLNet w zdalnym środowisku uruchomieniowym języka :::no-loc text="cuda"::: Python.
  + **azureml-train-core**
    + Wczesne błędy niektórych błędów definicji przestrzeni hiperparametrów bezpośrednio w zestawie SDK zamiast po stronie serwera.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning ZESTAW SDK do wstępnego przetwarzania danych w wersji 1.1.14
+ **Poprawki błędów i ulepszenia**
  + Włączono zapisywanie w usługach ADLS/ADLSGen2 przy użyciu nieprzetworzoną ścieżkę i poświadczenia.
  + Usunięto usterkę, która `include_path=True` powodowała, że nie działała dla . `read_parquet`
  + Usunięto `to_pandas_dataframe()` błąd spowodowany przez wyjątek "Nieprawidłowa wartość właściwości: hostSecret".
  + Usunięto usterkę, która pozwalała na to, że nie można było odczytać plików w systemie DBFS w trybie Spark.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.57
+ **Nowe funkcje**
  + Włączone, `TabularDataset` aby można było korzystać z usługi AutomatedML. Aby dowiedzieć się więcej na temat `TabularDataset` , odwiedź stronę https://aka.ms/azureml/howto/createdatasets .

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Teraz możesz zaktualizować certyfikat TLS/SSL dla punktu końcowego oceniania wdrożonego w klastrze usługi AKS zarówno dla certyfikatu wygenerowanego przez firmę Microsoft, jak i certyfikatu klienta.
  + **azureml-automl-core**
    + Rozwiązano problem z autoML, który dotyczył wierszy z brakującymi etykietami, które nie były prawidłowo usuwane.
    + Ulepszono rejestrowanie błędów w ujmowaniu automatycznym; Pełne komunikaty o błędach będą teraz zawsze zapisywane w pliku dziennika.
    + AutoML zaktualizowało przypinanie pakietu tak, aby `azureml-defaults` zawierało `azureml-explain-model` , i `azureml-dataprep` . Automatyczneml nie będzie już ostrzegać o niezgodności pakietów (z wyjątkiem `azureml-train-automl` pakietu).
    + Rozwiązaliśmy problem, który powodował, że podziały cv mają `timeseries`  nierówny rozmiar, co powodowało niepowodzenie obliczania pojemnika.
    + Jeśli podczas uruchamiania iteracji zespołowej dla typu trenowania Krzyżowa weryfikacja wystąpią problemy z pobraniem modeli przeszkolonych na całym zestawie danych, wystąpi niespójność między wagami modelu i modelami, które były podawane do zespołu głosujących.
    + Naprawiono błąd, który był wywoływany, gdy etykiety trenowania i/lub walidacji (y i y_valid) są dostarczane w postaci ramki danych pandas, ale nie jako tablica numpy.
    + Rozwiązano problem z zadaniami prognozowania, gdy w kolumnach logicznych tabel wejściowych wystąpił brak.
    + Zezwalaj użytkownikom autoML na upuszczanie serii treningowych, które nie są wystarczająco długie podczas prognozowania. — Zezwalaj użytkownikom autoML na upuszczanie ziarna z zestawu testowego, który nie istnieje w zestawie treningowym podczas prognozowania.
  + **azureml-core**
    + Rozwiązano problem z blob_cache_timeout kolejności parametrów.
    + Dodano dopasowanie zewnętrzne i przekształcanie typów wyjątków do błędów systemowych.
    + Dodano obsługę wpisów Key Vault tajnych dla przebiegów zdalnych. Dodaj klasę azureml.core.keyvault.Keyvault, aby dodać, pobrać i wyświetlić listę wpisów tajnych z magazynu kluczy skojarzonego z obszarem roboczym. Obsługiwane operacje to:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(nazwa, wartość)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(nazwa)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + Dodatkowe metody uzyskiwania domyślnego magazynu kluczy i uzyskiwania wpisów tajnych podczas zdalnego uruchamiania:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(nazwa)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + Dodano dodatkowe parametry przesłonięcia do polecenia interfejsu wiersza polecenia submit-hyperdrive.
    + Zwiększenie niezawodności wywołań interfejsu API polega na rozszerzaniu ponownych prób do wyjątków biblioteki typowych żądań.
    + Dodanie obsługi przesyłania przebiegów z przesłanego uruchomienia.
    + Rozwiązano problem z wygasającą tokenem sygnatury dostępu współdzielonego w fileWatcher, który powodował, że pliki przestają być przekazywane po wygaśnięciu ich tokenu początkowego.
    + Do obsługi importowania plików CSV/tsv protokołu HTTP w zestawie danych zestawu SDK języka Python.
    + Wycofana metoda Workspace.setup(). Komunikat ostrzegawczy dla użytkowników sugeruje użycie funkcji create() lub get()/from_config().
    + Dodano Environment.add_private_pip_wheel(), która umożliwia przekazywanie prywatnych niestandardowych pakietów języka Python do obszaru roboczego i bezpieczne używanie ich do `whl` kompilowania/materializacji środowiska.
    + Teraz możesz zaktualizować certyfikat TLS/SSL dla punktu końcowego oceniania wdrożonego w klastrze usługi AKS zarówno dla certyfikatu wygenerowanego przez firmę Microsoft, jak i certyfikatu klienta.
  + **azureml-explain-model**
    + Dodano parametr w celu dodania identyfikatora modelu do objaśnień podczas przekazywania.
    + Dodano `is_raw` tagowanie do objaśnień w pamięci i przekazywania.
    + Dodano obsługę i testy pytorch dla pakietu azureml-explain-model.
  + **azureml-opendatasets**
    + Obsługa wykrywania i rejestrowania środowiska automatycznego testowania.
    + Dodano klasy w celu uzyskania populacji STANÓW Zjednoczonych według hrabstwa i pliku zip.
  + **azureml-pipeline-core**
    + Dodano właściwość label do definicji portów wejściowych i wyjściowych.
  + **azureml-telemetry**
    + Naprawiono nieprawidłową konfigurację telemetrii.
  + **azureml-train-automl**
    + Usunięto usterkę, która oznaczała, że w przypadku niepowodzenia instalacji błąd nie był rejestrowany w polu "errors" dla uruchomienia instalatora i dlatego nie był przechowywany w nadrzędnym uruchomieniu "errors".
    + Rozwiązano problem z automl były wiersze z brakującymi etykietami nie zostały prawidłowo usunięte.
    + Zezwalaj użytkownikom autoML na upuszczanie serii treningowych, które nie są wystarczająco długie podczas prognozowania.
    + Zezwalaj użytkownikom autoML na upuszczanie ziarna z zestawu testowego, który nie istnieje w zestawie treningowym podczas prognozowania.
    + Teraz krok AutoMLStep przekazuje konfigurację do zaplecza, aby uniknąć problemów ze zmianami lub `automl` dodawaniem nowych parametrów konfiguracji.
    + Funkcja AutoML Data Guardrail jest teraz w publicznej wersji zapoznawczej. Użytkownik zobaczy raport Data Guardrail (dla zadań klasyfikacji/regresji) po zakończeniu trenowania i będzie mógł uzyskać do niego dostęp za pośrednictwem interfejsu API zestawu SDK.
  + **azureml-train-core**
    + Dodano obsługę torcha 1.2 w programie do szacowania PyTorch.
  + **azureml-widgets**
    + Ulepszone wykresy macierzy pomyłek do trenowania klasyfikacji.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning DATA Prep SDK 1.1.12
+ **Nowe funkcje**
  + Listy ciągów mogą być teraz przekazywane jako dane wejściowe do `read_*` metod.

+ **Poprawki błędów i ulepszenia**
  + Wydajność funkcji została `read_parquet` zwiększona podczas uruchamiania na sparkie.
  + Rozwiązano problem, który był niepowodzeniem w przypadku pojedynczej `column_type_builder` kolumny z niejednoznacznymi formatami dat.

### <a name="azure-portal"></a>Azure Portal
+ **Funkcja w wersji zapoznawczej**
  + Przesyłanie strumieniowe dzienników i plików wyjściowych jest teraz dostępne dla stron szczegółów uruchomienia. Pliki będą przesyłać strumieniowo aktualizacje w czasie rzeczywistym, gdy przełącznik podglądu jest włączony.
  + Możliwość ustawienia limitu przydziału na poziomie obszaru roboczego jest wydana w wersji zapoznawczej. Przydziały usługi AmlCompute są przydzielane na poziomie subskrypcji, ale teraz możemy rozdzielić ten limit przydziału między obszarami roboczymi i przydzielić go do sprawiedliwego udostępniania i zarządzania. Po prostu kliknij blok **Użycie i przydziały** na lewym pasku nawigacyjnym obszaru roboczego i wybierz **kartę Konfigurowanie przydziałów.** Aby móc ustawiać limity przydziału na poziomie obszaru roboczego, musisz być administratorem subskrypcji, ponieważ jest to operacja między obszarami roboczymi.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.55

+ **Nowe funkcje**
  + Uwierzytelnianie oparte na tokenach jest teraz obsługiwane dla wywołań wykonanych do punktu końcowego oceniania wdrożonego w u usługi AKS. Będziemy nadal obsługiwać bieżące uwierzytelnianie oparte na kluczach, a użytkownicy mogą jednocześnie korzystać z jednego z tych mechanizmów uwierzytelniania.
  + Możliwość zarejestrowania magazynu obiektów blob, który znajduje się za siecią wirtualną (VNet) jako magazynu danych.

+ **Poprawki błędów i ulepszenia**
  + **azureml-automl-core**
    + Usunięto usterkę, która powoduje, że rozmiar walidacji dla podziałów CV jest mały i skutkuje źle przewidywanymi i prawdziwymi wykresami regresji i prognozowania.
    + Ulepszono rejestrowanie zadań prognozowania na zdalnych przebiegach. Użytkownik ma teraz kompleksowy komunikat o błędzie, jeśli przebieg zakończył się niepowodzeniem.
    + Usunięto błędy `Timeseries` flagi if preprocess o wartości True.
    + Niektóre komunikaty o błędach weryfikacji danych prognozowania są bardziej actionable.
    + Mniejsze zużycie pamięci przez przebiegi automatycznego przetwarzania danych przez porzucanie i/lub ładowanie zestawów danych z opóźnieniem, szczególnie między zduplikowaniami procesów
  + **azureml-contrib-explain-model**
    + Dodano model_task do objaśnień, aby umożliwić użytkownikowi zastępowanie domyślnej logiki wnioskowania automatycznego dla typu modelu
    + Zmiany widżetu: są automatycznie instalowane za pomocą funkcji , koniec z instalowaniem/włączaniem — wyjaśnienie obsługi z globalną ważnością funkcji `contrib` `nbextension` (na przykład permutatywną)
    + Zmiany pulpitu nawigacyjnego: — wykresy skrzynkowe i wykresy odcłaniające oprócz wykresu na stronie podsumowania — znacznie szybsze rerenderowanie wykresu na suwaku "Top -k" — przydatny komunikat wyjaśniający sposób obliczania wartości `beeswarm` top-k — przydatne dostosowywalne komunikaty w miejsce wykresów, gdy nie podano danych `beeswarm`
  + **azureml-core**
    + Dodano metodę Model.package() w celu tworzenia obrazów platformy Docker i plików Dockerfile, które hermetyzują modele i ich zależności.
    + Zaktualizowano lokalne usługi internetowe tak, aby akceptowały konfiguracje wnioskowania zawierające obiekty środowiska.
    + Naprawiono błąd Model.register() tworzący nieprawidłowe modele, gdy "." (dla bieżącego katalogu) jest przekazywany jako model_path parametru.
    + Dodaj Run.submit_child, funkcja dubluje element Experiment.submit podczas określania uruchomienia jako elementu nadrzędnego przesłanego uruchomienia podrzędnego.
    + Obsługa opcji konfiguracji z pliku Model.register w Run.register_model.
    + Możliwość uruchamiania zadań JAR w istniejącym klastrze.
    + Teraz obsługa instance_pool_id i cluster_log_dbfs_path parametrów.
    + Dodano obsługę używania obiektu Environment podczas wdrażania modelu do usługi internetowej. Obiekt Environment można teraz dostarczać jako część obiektu InferenceConfig.
    + Dodawanie mapowania appinsifht dla nowych regionów — centralus — westus — northcentralus
    + Dodano dokumentację dla wszystkich atrybutów we wszystkich klasach magazynu danych.
    + Dodano blob_cache_timeout do parametru `Datastore.register_azure_blob_container` .
    + Dodano save_to_directory i load_from_directory do pliku azureml.core.environment.Environment.
    + Dodano polecenia "az ml environment download" i "az ml environment register" do interfejsu wiersza polecenia.
    + Dodano Environment.add_private_pip_wheel metody .
  + **azureml-explain-model**
    + Dodano śledzenie zestawów danych do sekcji Wyjaśnienia przy użyciu usługi Dataset (wersja zapoznawcza).
    + Zmniejszenie domyślnego rozmiaru partii w przypadku globalnych wyjaśnień przesyłania strumieniowego z 10 000 do 100.
    + Dodano model_task do objaśnień, aby umożliwić użytkownikowi przesłanianie domyślnej logiki automatycznego wnioskowania dla typu modelu.
  + **azureml-mlflow**
    + Usunięto usterkę w mlflow.azureml.build_image, w której zagnieżdżone katalogi są ignorowane.
  + **azureml-pipeline-steps**
    + Dodano możliwość uruchamiania zadań JAR w istniejących Azure Databricks klastra.
    + Dodano obsługę instance_pool_id i cluster_log_dbfs_path dla kroku DatabricksStep.
    + Dodano obsługę parametrów potoku w kroku DatabricksStep.
  + **azureml-train-automl**
    + Dodano `docstrings` wartość dla plików powiązanych z zestawem.
    + Zaktualizowano dokumenty do bardziej odpowiedniego języka dla `max_cores_per_iteration` i `max_concurrent_iterations`
    + Ulepszono rejestrowanie zadań prognozowania na zdalnych przebiegach. Użytkownik ma teraz kompleksowy komunikat o błędzie, jeśli przebieg zakończył się niepowodzeniem.
    + Usunięto get_data z notesu `automlstep` potoku.
    + Rozpoczęto obsługę `dataprep` w `automlstep` programie .

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning DATA Prep SDK 1.1.10

+ **Nowe funkcje**
  + Teraz możesz zażądać wykonania określonych inspektorów (na przykład histogramu, wykresu punktowego itp.) dla określonych kolumn.
  + Dodano argument parallelize do `append_columns` . W przypadku wartości True dane zostaną załadowane do pamięci, ale wykonywanie będzie uruchamiane równolegle; Jeśli ma wartość False, wykonywanie będzie przesyłane strumieniowo, ale jednowątkowe.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.53

+ **Nowe funkcje**
  + Zautomatyzowane Machine Learning obsługują teraz trenowania modeli ONNX na zdalnym docelowym obiektem obliczeniowym
  + Azure Machine Learning teraz możliwość wznowienia trenowania z poprzedniego uruchomienia, punktu kontrolnego lub plików modelu.
    + Dowiedz się, jak wznawiać trenowania z poprzedniego uruchomienia za pomocą [estymatorów](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Poprawki błędów i ulepszenia**
  + **azure-cli-ml**
    + Polecenia interfejsu wiersza polecenia "model deploy" i "service update" akceptują teraz parametry, pliki konfiguracji lub kombinację tych dwóch elementów. Parametry mają pierwszeństwo przed atrybutami w plikach.
    + Opis modelu można teraz zaktualizować po rejestracji
  + **azureml-automl-core**
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (bieżąca najnowsza wersja).
    + Dodanie obsługi estymatorów NimbusML w & do szacowania automatycznego.
    + Usunięcie usterki w procedurze wyboru Zespołów, która niepotrzebnie powiększała wynikową zespołów, nawet jeśli wyniki pozostały stałe.
    + Włącz ponowne używanie niektórych cech w podziałach CV do prognozowania zadań. Przyspiesza to czas uruchamiania konfiguracji przez w przybliżeniu czynnik n_cross_validations kosztownych cech, takich jak opóźnienia i okna kroczące.
    + Rozwiązywanie problemu, jeśli czas jest poza zakresem czasu obsługiwanym przez pandas. Teraz zgłaszamy błąd DataException, jeśli czas jest krótszy niż pd. Sygnatura czasowa.min lub większa niż pd. Timestamp.max
    + Prognozowanie umożliwia teraz różne częstotliwości w zestawach treningowych i testowych, jeśli można je wyrównać. Na przykład można dopasować "kwartalne od stycznia" i "kwartalne od października".
    + Właściwość "parameters" została dodana do obiektu TimeSeriesTransformer.
    + Usuń stare klasy wyjątków.
    + W zadaniach prognozowania parametr akceptuje teraz pojedynczą wartość całkowitą `target_lags` lub listę liczb całkowitych. Jeśli podano liczbę całkowitą, zostanie utworzone tylko jedno opóźnienie. Jeśli zostanie podano listę, zostaną podjęte unikatowe wartości opóźnień. target_lags= [1, 2, 2, 4] spowoduje opóźnienie jednego, dwóch i czterech okresów.
    + Naprawienie usterki dotyczącej utraty typów kolumn po przekształceniu (połączonej usterki);
    + W `model.forecast(X, y_query)` pliku y_query, aby był typem obiektu zawierającym none(s) na początku (#459519).
    + Dodawanie oczekiwanych wartości do `automl` danych wyjściowych
  + **azureml-contrib-datadrift**
    +  Ulepszenia przykładowego notesu, w tym przełączanie na zestawy danych azureml-opendataset zamiast azureml-contrib-opendataset i ulepszenia wydajności podczas wzbogacania danych
  + **azureml-contrib-explain-model**
    + Naprawiono argument przekształceń dla objaśnienia MODELU dla pierwotnej ważności funkcji w pakiecie azureml-contrib-explain-model
    + Dodano segmentacje do objaśnień obrazów w objaśnieniu obrazu dla pakietu AzureML-contrib-explain-model
    + Dodawania obsługi rozrzednika scipy dlaexplainer
    + Dodano do naśladujących objaśnienia, gdy , w celu przesyłania strumieniowego globalnych wyjaśnień w partiach w celu poprawienia czasu wykonywania `batch_size` `include_local=False` modelu DecisionTreeExplainableModel
  + **azureml-contrib-featureengineering**
    + Poprawka dla wywoływania set_featurizer_timeseries_params(): zmiana typu wartości dyktowania i sprawdzanie wartości null — dodawanie notesu dla `timeseries`  cechatora
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (najnowsza wersja).
  + **azureml-core**
    + Dodano możliwość dołączania magazynów danych DBFS w interfejsie wiersza polecenia usługi AzureML
    + Usunięto usterkę w przekazywaniu magazynu danych, która w przypadku rozpoczęcia pracy z tworzona `target_path` jest pusty folder `/`
    + Rozwiązano `deepcopy` problem z usługą ServicePrincipalAuthentication.
    + Dodano polecenia "az ml environment show" i "az ml environment list" do interfejsu wiersza polecenia.
    + Środowiska obsługują teraz określanie base_dockerfile jako alternatywę dla już base_image.
    + Nieużywane ustawienie RunConfiguration auto_prepare_environment zostało oznaczone jako przestarzałe.
    + Opis modelu można teraz zaktualizować po rejestracji
    + Poprawka usterki: Usuwanie modelu i obrazu zawiera teraz więcej informacji na temat pobierania obiektów nadrzędnych, które są od nich zależne, jeśli usuwanie zakończy się niepowodzeniem z powodu zależności nadrzędnej.
    + Usunięto usterkę, która oznaczała, że podczas tworzenia obszaru roboczego w niektórych środowiskach był drukowany pusty czas trwania wdrożeń.
    + Ulepszone wyjątki błędów podczas tworzenia obszaru roboczego. W ten sposób użytkownicy nie widzą informacji "Nie można utworzyć obszaru roboczego. Nie można odnaleźć..." jako komunikat i zamiast tego zobaczy rzeczywisty błąd tworzenia.
    + Dodanie obsługi uwierzytelniania tokenu w usługach internetowych usługi AKS.
    + Dodaj `get_token()` metodę do obiektów `Webservice` .
    + Dodano obsługę interfejsu wiersza polecenia do zarządzania zestawami danych uczenia maszynowego.
    + `Datastore.register_azure_blob_container` Teraz opcjonalnie przyjmuje wartość (w sekundach), która konfiguruje parametry instalacji systemu blobfuse w celu włączenia wygaśnięcia pamięci podręcznej `blob_cache_timeout` dla tego magazynu danych. Wartość domyślna to brak limitu czasu, na przykład gdy obiekt blob jest odczytywany, pozostanie w lokalnej pamięci podręcznej do czasu zakończeniu zadania. Większość zadań preferuje to ustawienie, ale niektóre zadania muszą odczytywać więcej danych z dużego zestawu danych, niż mieści się w ich węzłach. W przypadku tych zadań dostrajanie tego parametru pomoże im zakończyć się powodzeniem. Podczas dostrajania tego parametru należy zadbać: ustawienie zbyt niskiej wartości może spowodować niską wydajność, ponieważ dane używane w epoce mogą wygasnąć, zanim będą ponownie używane. Wszystkie odczyty będą wykonywane z magazynu obiektów blob/sieci, a nie z lokalnej pamięci podręcznej, co negatywnie wpływa na czas trenowania.
    + Opis modelu można teraz poprawnie zaktualizować po rejestracji
    + Usuwanie modelu i obrazu zawiera teraz więcej informacji o obiektach nadrzędnych, które od nich zależą, co powoduje niepowodzenie usuwania
    + Zwiększ wykorzystanie zasobów zdalnych przebiegów przy użyciu pliku azureml.mlflow.
  + **azureml-explain-model**
    + Naprawiono argument przekształceń dla objaśnienia MODELU dla pierwotnej ważności funkcji w pakiecie azureml-contrib-explain-model
    + dodanie obsługi rozrzednika scipy dlaexplainer
    + Dodano otokę objaśniawą liniową kształtu, a także kolejny poziom objaśnienia tabelaryowego do wyjaśniania modeli liniowych
    + for mimic explainer in explainer in explain model library, fixed error when include_local=False for sparse data input
    + dodawanie oczekiwanych wartości do `automl` danych wyjściowych
    + naprawiono ważność funkcji permutacji, gdy argument przekształceń został dostarczony w celu uzyskania pierwotnej ważności funkcji
    + Dodano do naśladujące objaśnienia, gdy , w celu przesyłania strumieniowego globalnych wyjaśnień w partiach w celu poprawienia czasu wykonywania `batch_size` `include_local=False` modelu DecisionTreeExplainableModel
    + w przypadku biblioteki objaśnień modelu naprawiono objaśnienia czarnej skrzynki, w przypadku których dane wejściowe ramki danych biblioteki pandas są wymagane do przewidywania
    + Usunięto usterkę, `explanation.expected_values` która czasami zwracała zmiennoprzecinkowy, a nie listę z zmiennoprzecinkowy.
  + **azureml-mlflow**
    + Poprawianie wydajności mlflow.set_experiment(experiment_name)
    + Naprawiono usterkę w używaniu interactiveLoginAuthentication dla interfejsu mlflow tracking_uri
    + Zwiększ wykorzystanie zasobów zdalnych przebiegów przy użyciu pliku azureml.mlflow.
    + Ulepszanie dokumentacji pakietu azureml-mlflow
    + Poprawka usterki, która mlflow.log_artifacts("my_dir") zapisywała artefakty w obszarze "my_dir/<artifact-paths>" zamiast "<artifact-paths>"
  + **azureml-opendatasets**
    + `pyarrow`Przypnij do starych wersji (<0.14.0) z powodu nowo wprowadzonego problemu z `opendatasets` pamięcią.
    + Przenieś zestawy danych azureml-contrib-opendataset do zestawu azureml-opendatasets.
    + Zezwalaj na rejestrowanie klas otwartego zestawu danych w Azure Machine Learning i bezproblemowe korzystanie z możliwości zestawu danych AML.
    + Znacząco zwiększ wydajność systemu NoaaIsdWeather w wersji spoza platformy SPARK.
  + **azureml-pipeline-steps**
    + Magazyn danych DBFS jest teraz obsługiwany dla danych wejściowych i wyjściowych w kroku DatabricksStep.
    + Zaktualizowano dokumentację Azure Batch Krok w odniesieniu do danych wejściowych/wyjściowych.
    + W kroku AzureBatchStep zmieniono *delete_batch_job_after_finish* wartość domyślną na *true*.
  + **azureml-telemetry**
    +  Przenieś zestawy danych azureml-contrib-opendataset do zestawu azureml-opendatasets.
    + Zezwalaj na rejestrowanie klas otwartego zestawu danych w Azure Machine Learning i bezproblemowe korzystanie z możliwości zestawu danych AML.
    + Znacząco zwiększ wydajność systemu NoaaIsdWeather w wersji spoza platformy SPARK.
  + **azureml-train-automl**
    + Zaktualizowano dokumentację get_output, aby odzwierciedlała rzeczywisty zwracany typ i zawierała dodatkowe uwagi dotyczące pobierania właściwości klucza.
    + Zaktualizuj zależność NimbusML do wersji 1.2.0 (najnowsza wersja).
    + dodawanie oczekiwanych wartości do `automl` danych wyjściowych
  + **azureml-train-core**
    + Ciągi są teraz akceptowane jako docelowy obiekt obliczeniowy zautomatyzowanego dostrajania hiperparametrów
    + Nieużywane ustawienie RunConfiguration auto_prepare_environment zostało oznaczone jako przestarzałe.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning DATA Prep SDK 1.1.9

+ **Nowe funkcje**
  + Dodano obsługę odczytywania pliku bezpośrednio z adresu URL http lub https.

+ **Poprawki błędów i ulepszenia**
  + Ulepszony komunikat o błędzie podczas próby odczytu zestawu danych Parquet ze źródła zdalnego (który nie jest obecnie obsługiwany).
  + Usunięto usterkę podczas zapisywania w formacie pliku Parquet w umacie ADLS Gen 2 i aktualizowania nazwy kontenera usługi ADLS Gen 2 w ścieżce.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Interfejs wizualny
+ **Funkcje w wersji zapoznawczej**
  + Dodano moduł "Execute R script" (Wykonaj skrypt języka R) w interfejsie wizualnym.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.48

+ **Nowe funkcje**
  + **azureml-opendatasets**
    + **Zestawy danych azureml-contrib-opendataset są** teraz dostępne jako **zestawy danych azureml-opendataset.** Stary pakiet może nadal działać, ale zalecamy używanie **zestawu azureml-opendatasets** w celu skorzystania z bardziej rozbudowanych możliwości i ulepszeń.
    + Ten nowy pakiet umożliwia rejestrowanie otwartych zestawów danych jako zestawu danych w Azure Machine Learning roboczym i korzystanie z dowolnych funkcji oferowanych przez zestaw danych.
    + Obejmuje również istniejące możliwości, takie jak korzystanie z otwartych zestawów danych, takich jak ramki danych pandas/SPARK, oraz sprzężenia lokalizacji dla niektórych zestawów danych, takich jak pogoda.

+ **Funkcje w wersji zapoznawczej**
    + Funkcja HyperDriveConfig może teraz akceptować obiekt potoku jako parametr do obsługi dostrajania hiperparametrów przy użyciu potoku.

+ **Poprawki błędów i ulepszenia**
  + **azureml-train-automl**
    + Usunięto usterkę w przypadku utraty typów kolumn po przekształceniu.
    + Usunięto usterkę, która y_query, że obiekt zawierał obiekty None(s) na początku.
    + Rozwiązano problem w procedurze wyboru zespołów, który niepotrzebnie powiększał wynikową zespołów, nawet jeśli wyniki pozostały stałe.
    + Rozwiązano problem z zezwalania na list_models i blokowania list_models w autoMLStep.
    + Rozwiązano problem uniemożliwiający użycie przetwarzania wstępnego, gdy automl było używane w kontekście potoków usługi Azure ML.
  + **azureml-opendatasets**
    + Przeniesiono ciąg azureml-contrib-opendatasets do zestawu azureml-opendatasets.
    + Umożliwia rejestrowanie klas otwartego zestawu danych w Azure Machine Learning i bezproblemowe korzystanie z możliwości zestawu danych AML.
    + Ulepszona wersja NoaaIsdWeather znacznie zwiększa wydajność w wersji spoza platformy SPARK.
  + **azureml-explain-model**
    + Zaktualizowano dokumentację online dotyczącą obiektów możliwości interpretacji.
    + Dodano wartość , aby naśladować objaśnienia w przypadku , aby przesyłać strumieniowo globalne wyjaśnienia w partiach, aby poprawić czas wykonywania `batch_size` `include_local=False` modelu DecisionTreeExplainableModel dla biblioteki objaśnień modelu.
    + Rozwiązano problem, który czasami `explanation.expected_values` zwracał zmiennoprzecinkowy, a nie listę z zmiennoprzecinkową.
    + Dodano oczekiwane wartości do `automl` danych wyjściowych dla objaśnienia naśladujących w bibliotece modeli objaśnienia.
    + Naprawiono ważność funkcji permutacji, gdy argument przekształceń został dostarczony w celu uzyskania pierwotnej ważności funkcji.
  + **azureml-core**
    + Dodano możliwość dołączania magazynów danych DBFS w interfejsie wiersza polecenia usługi AzureML.
    + Rozwiązano problem z przekazywaniem magazynu danych, który dotyczył tworzenia pustego folderu w `target_path` przypadku rozpoczęcia pracy z programem `/` .
    + Włączone porównanie dwóch zestawów danych.
    + Usuwanie modelu i obrazu zawiera teraz więcej informacji na temat pobierania obiektów nadrzędnych, które są od nich zależne, jeśli usuwanie zakończy się niepowodzeniem z powodu zależności nadrzędnej.
    + Wycofano nieużywane ustawienie RunConfiguration w auto_prepare_environment.
  + **azureml-mlflow**
    + Ulepszone wykorzystanie zasobów zdalnych przebiegów, które używają pliku azureml.mlflow.
    + Ulepszono dokumentację pakietu azureml-mlflow.
    + Rozwiązano problem, mlflow.log_artifacts ("my_dir") zapisywał artefakty w obszarze "my_dir/artifact-paths" zamiast "artifact-paths".
  + **azureml-pipeline-core**
    + Parametr hash_paths dla wszystkich kroków potoku jest przestarzały i zostanie usunięty w przyszłości. Domyślnie zawartość pliku jest source_directory skrótu (z wyjątkiem plików wymienionych w `.amlignore` lub `.gitignore` )
    + W dalszym ciągu ulepszaliśmy moduły Module i ModuleStep w celu obsługi modułów specyficznych dla typu obliczeń w celu przygotowania do integracji RunConfiguration i innych zmian w celu odblokowania użycia modułów specyficznych dla typu obliczeń w potokach.
  + **azureml-pipeline-steps**
    + AzureBatchStep: ulepszona dokumentacja dotycząca danych wejściowych/wyjściowych.
    + AzureBatchStep: zmieniono delete_batch_job_after_finish wartość domyślną na true.
  + **azureml-train-core**
    + Ciągi są teraz akceptowane jako docelowy obiekt obliczeniowy automatycznego dostrajania hiperparametrów.
    + Wycofano nieużywane ustawienie RunConfiguration w auto_prepare_environment.
    + Przestarzałe parametry `conda_dependencies_file_path` i odpowiednio na rzecz i `pip_requirements_file_path` `conda_dependencies_file` `pip_requirements_file` .
  + **azureml-opendatasets**
    + Znacznie ulepsz wydajność wzbogacania NoaaIsdWeather w wersji spoza platformy SPARK.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning zestaw SDK dla języka Python w wersji 1.0.33 został wydany.

+ Usługa Azure ML Modele z przyspieszaniem sprzętowym [układów FPGA](how-to-deploy-fpga-web-service.md) jest ogólnie dostępna.
  + Teraz możesz użyć [pakietu azureml-accel-models, aby:](how-to-deploy-fpga-web-service.md)
    + Szkolenie wag obsługiwanej głębokiej sieci neuronowej (ResNet 50, ResNet 152, DenseNet-121, VGG-16 i SSD-VGG)
    + Korzystanie z uczenia transferowego przy użyciu obsługiwanej technologii DNN
    + Rejestrowanie modelu w usłudze Zarządzanie modelami Service i konteneryzowanie modelu
    + Wdrażanie modelu na maszynie wirtualnej platformy Azure przy użyciu układu FPGA w klastrze Azure Kubernetes Service (AKS)
  + Wdrażanie kontenera na urządzeniu serwera [Azure Data Box Edge](../databox-online/azure-stack-edge-overview.md)
  + Ocena danych za pomocą punktu końcowego gRPC za pomocą tego [przykładu](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Zautomatyzowane uczenie maszynowe

+ Czyszczenie funkcji w celu umożliwienia dynamicznego dodawania w :::no-loc text="featurizers"::: celu optymalizacji wydajności. :::no-loc text="featurizers":::Nowość: osadzanie pracy, waga dowodów, kodowanie docelowe, kodowanie obiektu docelowego tekstu, odległość klastra
+ Inteligentne CV do obsługi podziałów trenowania/prawidłowych wewnątrz zautomatyzowanego uczenia maszynowego
+ Kilka zmian optymalizacji pamięci i poprawa wydajności środowiska uruchomieniowego
+ Poprawa wydajności w objaśnieniu modelu
+ Konwersja modelu ONNX dla uruchomienia lokalnego
+ Dodano obsługę próbkowania podrzędnego
+ Inteligentne zatrzymywanie, gdy nie zdefiniowano kryteriów zakończenia
+ Skumulowane zestawy

+ Prognozowanie szeregów czasowych
  + Nowa funkcja prognozowania
  + Teraz można używać sprawdzania krzyżowego pochodzenia stopniowego na danych szeregów czasowych
  + Dodano nowe funkcje do konfigurowania opóźnień szeregów czasu
  + Dodano nowe funkcje do obsługi funkcji agregowania kroczących okien
  + Wykrywanie nowych dni wolnych i do cechowanie, gdy kod kraju jest zdefiniowany w ustawieniach eksperymentu

+ Azure Databricks
  + Włączone prognozowanie szeregów czasu i możliwość wyjaśnienia modelu/możliwości interpretacji
  + Teraz możesz anulować i wznowić (kontynuować) eksperymenty zautomatyzowanego uczenia maszynowego
  + Dodano obsługę przetwarzania wielordzeniowego

### <a name="mlops"></a>MLOps
+ **Wdrażanie lokalne & debugowania dla kontenerów oceniania**<br/> Teraz możesz wdrożyć model uczenia maszynowego lokalnie i szybko iterować po pliku oceniania i zależnościach, aby upewnić się, że zachowują się one zgodnie z oczekiwaniami.

+ **Wprowadzono plik InferenceConfig & Model.deploy()**<br/> Wdrożenie modelu obsługuje teraz określanie folderu źródłowego za pomocą skryptu wprowadzania, tak samo jak w przypadku pliku RunConfig.  Ponadto wdrożenie modelu zostało uproszczone do pojedynczego polecenia.

+ **Śledzenie odwoływać się do usługi Git**<br/> Od pewnego czasu klienci żądają podstawowych możliwości integracji z usługą Git, ponieważ pomagają one w utrzymaniu pełnego dziennika inspekcji. Zaimplementowaliśmy śledzenie głównych jednostek w usłudze Azure ML dla metadanych związanych z usługą Git (repozytorium, zatwierdzenie, czysty stan). Te informacje będą zbierane automatycznie przez zestaw SDK i interfejs wiersza polecenia.

+ **Profilowanie modelu & usługi walidacji**<br/> Klienci często narzekają na trudności z prawidłowym rozmiarem zasobów obliczeniowych skojarzonych z ich usługą wnioskowania. Dzięki naszej usłudze profilowania modelu klient może udostępnić przykładowe dane wejściowe, a my będziemy profilować w 16 różnych konfiguracjach procesora/pamięci, aby określić optymalny rozmiar wdrożenia.

+ **Bring your own base image for inference (Bring your own base image for inference)**<br/> Inną częstą skargą była trudność w przechodzeniu z eksperymentowania do zależności udostępniania referencji wnioskowania. Dzięki naszej nowej funkcji udostępniania obrazów podstawowych możesz teraz ponownie używać obrazów podstawowych eksperymentowania, zależności i wszystkich tych obrazów do wnioskowania. Powinno to przyspieszyć wdrożenia i zmniejszyć przerwę między wewnętrzną i zewnętrzną pętlą.

+ **Ulepszone środowisko generowania schematów swagger**<br/> Nasza poprzednia metoda generowania swagger była podatna na błędy i nie była możliwa do zautomatyzowania. Mamy nowy sposób generowania schematów swagger z dowolnej funkcji języka Python za pośrednictwem dekoratorów. Ten kod jest typu open source, a protokół generowania schematów nie jest połączony z platformą Azure ML.

+ **Azure ML CLI jest ogólnie dostępna**<br/> Modele można teraz wdrażać za pomocą jednego polecenia interfejsu wiersza polecenia. Dostaliśmy typowe opinie klientów, że nikt nie wdraża modelu uczenia maszynowego z notesu Jupyter. Dokumentacja [**referencyjna interfejsu wiersza**](./reference-azure-machine-learning-cli.md) polecenia została zaktualizowana.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning zestaw SDK dla języka Python w wersji 1.0.30 został wydany.

Wprowadzono [`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint) parametr w celu dodania nowej wersji opublikowanego potoku przy zachowaniu tego samego punktu końcowego.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Możesz teraz ponownie przesłać istniejące uruchomienie skryptu w istniejącym zdalnym klastrze obliczeniowym.
  + Teraz możesz uruchomić opublikowany potok z nowymi parametrami na karcie Potoki.
  + Szczegóły uruchamiania obsługują teraz nową przeglądarkę plików migawki. Migawkę katalogu można wyświetlić podczas przesłania określonego uruchomienia. Możesz również pobrać przesłany notes, aby rozpocząć uruchamianie.
  + Teraz możesz anulować przebiegi nadrzędne z Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.23

+ **Nowe funkcje**
  + Zestaw AZURE MACHINE LEARNING SDK obsługuje teraz język Python 3.7.
  + Azure Machine Learning do szacowania WNN zapewniają teraz wbudowaną obsługę wielu wersji. Na przykład estymator akceptuje teraz parametr, a użytkownicy mogą określić wersję `TensorFlow` `framework_version` "1.10" lub "1.12". Aby uzyskać listę wersji obsługiwanych przez bieżącą wersję zestawu SDK, wywołaj wywołanie dla żądanej klasy `get_supported_versions()` platformy (na przykład `TensorFlow.get_supported_versions()` ).
  Aby uzyskać listę wersji obsługiwanych przez najnowszą wersję zestawu SDK, zobacz dokumentację narzędzia [do szacowania platformy DNN.](/python/api/azureml-train-core/azureml.train.dnn)

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.21

+ **Nowe funkcje**
  + Metoda *azureml.core.Run.create_children* umożliwia tworzenie wielu przebiegów podrzędnych z małym opóźnieniem za pomocą jednego wywołania.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.18

 + **Zmiany**
   + Pakiet azureml-tensorboard zastępuje ciąg azureml-contrib-tensorboard.
   + W tej wersji można skonfigurować konto użytkownika w zarządzanym klastrze obliczeniowym (amlcompute) podczas jego tworzenia. Można to zrobić, przekazując te właściwości w konfiguracji aprowizowania. Więcej szczegółów można znaleźć w dokumentacji [referencyjnej zestawu SDK](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--).

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning DATA Prep SDK 1.0.17

+ **Nowe funkcje**
  + Teraz obsługuje dodawanie dwóch kolumn liczbowych w celu wygenerowania wynikowej kolumny przy użyciu języka wyrażeń.

+ **Poprawki błędów i ulepszenia**
  + Ulepszono dokumentację i sprawdzanie parametrów dla random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning DATA Prep SDK 1.0.16

+ **Poprawka**
  + Rozwiązano problem z uwierzytelnianiem jednostki usługi, który był spowodowany przez zmianę interfejsu API.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK dla języka Python w wersji 1.0.17

+ **Nowe funkcje**
  + Azure Machine Learning zapewnia teraz najwyższej klasy obsługę popularnego łańcucha struktury sieci DNN. Użytkownicy [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer) klas mogą łatwo trenować i wdrażać modele chainer.
    + Dowiedz się, jak [uruchomić trenowania rozproszonego przy użyciu sieci ChainerMN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Dowiedz się, jak [uruchamiać dostrajanie hiperparametryczne za pomocą narzędzia Chainer przy użyciu usługi HyperDrive](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning Potoki dodano możliwość wyzwalania uruchomienia potoku na podstawie modyfikacji magazynu danych. Notes harmonogramu [potoku został](https://aka.ms/pl-schedule) zaktualizowany w celu zaprezentowania tej funkcji.

+ **Poprawki błędów i ulepszenia**
  + W potokach usługi Azure Machine Learning dodaliśmy obsługę ustawiania właściwości source_directory_data_store na żądany magazyn danych (taki jak magazyn obiektów blob) w konfiguracjach [RunConfiguration](/python/api/azureml-core/azureml.core.runconfig.runconfiguration) dostarczanych do kroku [PythonScriptStep.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) Domyślnie kroki używają magazynu plików platformy Azure jako zapasowego magazynu danych, co może powodować problemy z ograniczaniem przepustowości, gdy wiele kroków jest wykonywanych współbieżnie.

### <a name="azure-portal"></a>Azure Portal

+ **Nowe funkcje**
  + Nowe środowisko przeciągania i upuszczania edytora tabel dla raportów. Użytkownicy mogą przeciągać kolumnę ze obszaru obszaru tabeli do obszaru tabeli, w którym zostanie wyświetlony podgląd tabeli. Kolumny można zmienić.
  + Nowa przeglądarka plików dzienników
  + Linki do przebiegów eksperymentów, obliczeń, modeli, obrazów i wdrożeń z karty działań

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z omówieniem usługi [Azure Machine Learning](overview-what-is-azure-ml.md).
