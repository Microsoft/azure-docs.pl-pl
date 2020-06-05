---
title: Analizowanie i monitorowanie dryfowania danych w zestawach DataSet (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Tworzenie zestawów danych Azure Machine Learning monitorów (wersja zapoznawcza), monitorowanie odniesień między danymi i Konfigurowanie alertów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: 15cfa56f718290af3ae5fb87aadab70016cc8594
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84430243"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Wykrywanie dryfowania danych (wersja zapoznawcza) w zestawach DataSet
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak utworzyć Azure Machine Learning monitory zestawu danych (wersja zapoznawcza), monitorować przedryfowanie danych i statystyczne zmiany w zestawach i skonfigurować alerty.

Za pomocą monitorów Azure Machine Learning zestawu danych można:
* **Analizuj dryf danych** , aby zrozumieć, jak zmienia się w miarę upływu czasu.
* **Monitoruj dane modelu** pod kątem różnic między szkoleniem i obsługujące zestawy danych.
* **Monitoruj nowe dane** pod kątem różnic między dowolnym bazowym i docelowym zestawem danych.
* **Profile funkcji w danych** , aby śledzić, jak właściwości statystyczne zmieniają się z upływem czasu.
* **Konfigurowanie alertów dotyczących dryfowania danych** w celu wczesnych ostrzeżeń dotyczących potencjalnych problemów. 

Metryki i szczegółowe informacje są dostępne za pomocą zasobu [usługi Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) skojarzonego z obszarem roboczym Azure Machine Learning.

> [!Important]
> Należy pamiętać, że przechodzenie danych monitorowania za pomocą zestawu SDK jest dostępne we wszystkich wersjach, podczas gdy monitorowanie dryfowania danych za pomocą programu Studio w sieci Web jest tylko w wersji Enterprise.

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć i korzystać z monitorów zestawu danych, potrzebne są:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.
* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).
* [Zestaw Azure Machine Learning SDK dla języka Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), który obejmuje pakiet usługi Azure DataSets.
* Dane ze strukturą (tabelaryczną) z sygnaturą czasową określoną w ścieżce pliku, nazwie pliku lub kolumnie w danych.

## <a name="what-is-data-drift"></a>Co to jest dryfowanie danych?

W kontekście uczenia maszynowego, dryfowanie danych to zmiana danych wejściowych modelu, które prowadzą do obniżenia wydajności modelu. Jest to jedna z najważniejszych przyczyn, które pogorszą się w miarę upływu czasu, dlatego monitorowanie dryfowania danych pomaga wykrywać problemy z wydajnością modelu.

Przyczyny dryfowania danych obejmują: 

- Zmiany procesu nadrzędnego, takie jak "zastępowany czujnik", zmieniają jednostki miary z cali na centymetry. 
- Problemy z jakością danych, takie jak uszkodzenie czujnika, zawsze odczytu 0.
- Naturalne dryfowanie danych, takie jak przeciętne wprowadzanie temperatury przy użyciu sezonów.
- Zmień zależność między funkcjami lub covariate Shift. 

Za pomocą Azure Machine Learningych monitorów zestawu danych można skonfigurować alerty, które pomagają w wykrywaniu dryfowania danych w zestawach DataSets z upływem czasu. 

### <a name="dataset-monitors"></a>Monitory zestawu danych 

Można utworzyć monitor zestawu danych, który będzie wykrywać i generować dane dotyczące oddzielania danych dla nowych danych w zestawie danych, analizować dane historyczne dotyczące dryfowania i profilować nowe dane w czasie. Algorytm dryfowania danych zawiera ogólną miarę zmian danych i wskazanie, które funkcje są odpowiedzialne za dalsze badanie. Monitory zestawu danych tworzą wiele innych metryk przez profilowanie nowych danych w `timeseries` zestawie danych. Niestandardowe alerty można skonfigurować na wszystkich metrykach wygenerowanych przez Monitor za pomocą [usługi Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Za pomocą monitorów zestawu danych można szybko przechwycić problemy z danymi i skrócić czas debugowania problemu, identyfikując możliwe przyczyny.  

Koncepcyjnie istnieją trzy podstawowe scenariusze konfigurowania monitorów zestawów danych w Azure Machine Learning.

Scenariusz | Opis
---|---
Monitorowanie obsługi danych przez model w celu oddzielenia od danych szkoleniowych modelu | Wyniki z tego scenariusza można interpretować jako monitorowanie serwera proxy dla dokładności modelu, pod warunkiem, że poprzednia dokładność modelu jest niezależna od danych szkoleniowych.
Monitorowanie zestawu danych szeregów czasowych dla dryfu z poprzedniego okresu. | Ten scenariusz jest bardziej ogólny i może służyć do monitorowania zestawów danych będących w modelu nadrzędnym lub podrzędnym kompilowania modeli.  Docelowy zestaw danych musi mieć kolumnę sygnatur czasowych, natomiast bazowy zestaw danych może być dowolnym tabelarycznym zestawem danych, który zawiera funkcje wspólne z docelowym zestawem danych.
Wykonywanie analizy danych przeszłych. | Ten scenariusz może służyć do zrozumienia danych historycznych i podejmowania decyzji dotyczących ustawień dla monitorów zestawu danych.

## <a name="how-dataset-can-monitor-data"></a>Jak zestaw danych może monitorować dane

Za pomocą Azure Machine Learning, dryfowanie danych jest monitorowane za pośrednictwem zestawów DataSet. Aby monitorować do dryfowania danych, bazowy zestaw danych — zwykle jest to zestaw danych szkoleniowych dla modelu — jest określony. Docelowy zestaw danych — zwykle dane wejściowe modelu — jest porównywany z upływem czasu do zestawu danych bazowych. To porównanie oznacza, że docelowy zestaw danych musi mieć określoną kolumnę sygnatur czasowych.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Ustawianie `timeseries` cech w docelowym zestawie danych

Docelowy zestaw danych musi mieć `timeseries` ustawioną cechę, określając kolumnę sygnatury czasowej w kolumnie dane lub kolumnę wirtualną pochodną wzorca ścieżki plików. Można to zrobić za pomocą zestawu SDK języka Python lub Azure Machine Learning Studio. Kolumna reprezentująca znacznik czasu "drobnoziarniste" musi być określona, aby można było dodać `timeseries` cechy do zestawu danych. Jeśli dane są partycjonowane w strukturze folderów wraz z informacjami o czasie, takimi jak "{RRRR/MM/DD}", można utworzyć kolumnę wirtualną za pomocą ustawienia wzorca ścieżki i ustawić ją jako sygnaturę "grube ziarno", aby zwiększyć ważność funkcji szeregów czasowych. 

#### <a name="python-sdk"></a>Zestaw SDK dla języka Python

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) Metoda Class określa kolumnę sygnatury czasowej dla zestawu danych. 

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

Pełny przykład użycia `timeseries` cech zestawów danych można znaleźć w [przykładowym notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) lub [dokumentacji zestawu SDK zestawów danych](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

#### <a name="azure-machine-learning-studio"></a>Studio uczenia maszynowego Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Jeśli tworzysz zestaw danych przy użyciu Azure Machine Learning Studio, upewnij się, że ścieżka do danych zawiera informacje o znaczniku czasu, Uwzględnij wszystkie podfoldery z danymi i ustaw format partycji. 

W poniższym przykładzie jest podejmowane wszystkie dane w podfolderze *NoaaIsdFlorida/2019* , a format partycji Określa rok, miesiąc i dzień. 

[![Format partycji](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

W ustawieniach **schematu** należy określić kolumnę sygnatury czasowej z wirtualnej lub rzeczywistej kolumny w określonym zestawie danych:

![Znacznik czasu](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Ustawienia monitora zestawu danych

Po utworzeniu zestawu danych przy użyciu określonych ustawień sygnatury czasowej możesz skonfigurować Monitor zestawu danych.

Różne ustawienia monitora zestawu danych są podzielone na trzy grupy: **podstawowe informacje, ustawienia monitora** i **Ustawienia wypełniania**.

### <a name="basic-info"></a>Informacje podstawowe

Ta tabela zawiera podstawowe ustawienia używane do monitorowania zestawu danych.

| Ustawienie | Opis | Porady | Modyfikowalny | 
| ------- | ----------- | ---- | ------- | 
| Nazwa | Nazwa monitora zestawu danych. | | Nie |
| Bazowy zestaw danych | Tabelaryczny zestaw danych, który będzie używany jako linia bazowa do porównania docelowego zestawu danych w czasie. | Zestaw danych bazowych musi mieć wspólne funkcje z docelowym zestawem danych. Ogólnie rzecz biorąc, linia bazowa powinna być ustawiona na zestaw danych szkoleniowych modelu lub do wycinka docelowego zestawu danych. | Nie |
| Docelowy zestaw danych | Zestaw danych tabelarycznych z określoną kolumną sygnatur czasowych, który zostanie przeanalizowany pod kątem dryfowania danych. | Docelowy zestaw danych musi mieć wspólne funkcje z bazowym zestawem danych, który powinien być `timeseries` zestawem danych, do którego dodawane są nowe dane. Dane historyczne w docelowym zestawie danych mogą być analizowane lub można monitorować nowe dane. | Nie | 
| Częstotliwość | Częstotliwość, która zostanie użyta do zaplanowania zadania potoku i przeanalizowania danych historycznych w przypadku uruchamiania wypełniania. Dostępne opcje to codziennie, co tydzień lub co miesiąc. | Dostosuj to ustawienie, aby uwzględnić w linii bazowej porównywalny rozmiar danych. | Nie | 
| Funkcje | Lista funkcji, które będą analizowane pod kątem dryfowania danych w czasie. | Ustaw na funkcje wyjściowe modelu, aby zmierzyć dryf koncepcji. Nie należy dołączać funkcji, które naturalnie dryfuje w czasie (miesiąc, rok, indeks itp.). Po dostosowaniu listy funkcji można wypełniać i śledzić istniejący monitor dryfowania danych. | Tak | 
| Docelowy zasób obliczeniowy | Azure Machine Learning miejsce docelowe obliczeń, aby uruchomić zadania monitorowania zestawu danych. | | Tak | 

### <a name="monitor-settings"></a>Ustawienia monitora

Te ustawienia dotyczą potoku monitora harmonogramu zestawu danych, który zostanie utworzony. 

| Ustawienie | Opis | Porady | Modyfikowalny | 
| ------- | ----------- | ---- | ------- |
| Włącz | Włączanie lub wyłączanie harmonogramu dla potoku monitora zestawu danych | Wyłącz harmonogram, aby analizować dane historyczne przy użyciu ustawienia wypełniania. Można ją włączyć po utworzeniu monitora zestawu danych. | Tak | 
| Opóźnienie | Czas, w godzinach, pobiera dane do zestawu danych. Na przykład jeśli dane mają być dostarczone przez trzy dni w bazie danych bazy danych SQL, należy ustawić opóźnienie na 72. | Nie można zmienić po utworzeniu monitora zestawu danych | Nie | 
| Adresy e-mail | Adresy e-mail dla alertów na podstawie naruszenia progu procentu dryfu danych. | Wiadomości e-mail są wysyłane za poorednictwem Azure Monitor. | Tak | 
| Próg | Próg procentowy dryfu danych dla alertów e-mail. | Więcej alertów i zdarzeń można ustawić dla wielu innych metryk w skojarzonym zasobie Application Insightsm obszaru roboczego. | Tak | 

### <a name="backfill-settings"></a>Ustawienia wypełniania

Te ustawienia służą do uruchamiania wypełniania danych w przeszłości na potrzeby metryk dryfowania danych.

| Ustawienie | Opis | Porady |
| ------- | ----------- | ---- |
| Data rozpoczęcia | Data rozpoczęcia zadania wypełniania. | | 
| Data końcowa | Data zakończenia zadania wypełniania. | Data zakończenia nie może być dłuższa niż 31 * jednostek częstotliwości od daty rozpoczęcia. Na istniejącym monitorze zestawu danych metryki mogą być wypełniane, aby analizować dane historyczne lub zastępować metryki ze zaktualizowanymi ustawieniami. |

## <a name="create-dataset-monitors"></a>Tworzenie monitorów zestawu danych 

Tworzenie monitorów zestawu danych w celu wykrywania i generowania alertów dotyczących dryfowania danych w nowym zestawie danych przy użyciu programu Azure Machine Learning Studio lub zestawu Python SDK. 

### <a name="azure-machine-learning-studio"></a>Studio uczenia maszynowego Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Aby skonfigurować alerty na monitorze zestawu danych, obszar roboczy, który zawiera zestaw danych, dla którego chcesz utworzyć monitor, musi mieć funkcje wersji Enterprise. 

Po potwierdzeniu funkcjonalności obszaru roboczego przejdź do strony głównej programu Studio i wybierz kartę zestawy danych po lewej stronie. Wybierz monitory zestawu danych.

![Lista monitorów](./media/how-to-monitor-datasets/monitor-list.png)

Kliknij przycisk **+ Utwórz monitor** i Kontynuuj pracę kreatora, klikając przycisk **dalej**.

![Kreatora](./media/how-to-monitor-datasets/wizard.png)

Ten monitor zestawu danych zostanie wyświetlony na liście. Wybierz go, aby przejść do strony szczegółów tego monitora.

### <a name="from-python-sdk"></a>Z zestawu SDK języka Python

Aby uzyskać szczegółowe informacje, zobacz [dokumentację referencyjną języka Python SDK dotyczącą dryfowania danych](/python/api/azureml-datadrift/azureml.datadrift) . 

Poniższy przykład pokazuje, jak utworzyć monitor zestawu danych za pomocą zestawu SDK języka Python

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

Pełny przykład konfigurowania `timeseries` zestawu danych i wykrywania dryfowania danych można znaleźć w naszym [przykładowym notesie](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Zrozumienie wyników dryfowania danych

Monitor danych tworzy dwie grupy wyników: przegląd Dryfów i szczegółowe informacje o funkcji. Poniższa animacja pokazuje dostępne wykresy monitora dryfowego na podstawie wybranej funkcji i metryki. 

![Wideo demonstracyjne](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Przegląd dryfu

Sekcja **Przegląd dryfu** zawiera szczegółowe informacje o wielkości dryfowania danych oraz o tym, które funkcje powinny być poddane dalszej analizie. 

| Metric | Opis | Porady | 
| ------ | ----------- | ---- | 
| Wielkość dryfu danych | Wyrażony jako wartość procentowa między bazowym i docelowym zestawem danych w czasie. W zakresie od 0 do 100 gdzie 0 wskazuje identyczne zestawy danych i 100 wskazuje, że funkcja dryfowania danych Azure Machine Learning może całkowicie powiadomić o dwóch zestawach. | Oczekiwano szumu w precyzyjnym procencie mierzonym z powodu technik uczenia maszynowego używanego do generowania tego rozmiaru. | 
| Współtworzenie dryfu według funkcji | Udział każdej funkcji w docelowym zestawie danych do mierzonej wielkości dryfu. |  Ze względu na covariate Shift bazowa dystrybucja funkcji nie musi być konieczna do zmiany w celu zapewnienia stosunkowo dużej ważności funkcji. | 

Na poniższej ilustracji przedstawiono przykładowe wykresy widoczne w **omówieniu dryfu** w programie Azure Machine Learning Studio, wynikające z wypełniania [NOAA zintegrowanych danych powierzchniowych](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/). Dane były próbkowane do `stationName contains 'FLORIDA'` , z 2019 stycznia, używany jako zestaw danych bazowych i wszystkie dane 2019 używane jako element docelowy.
 
![Przegląd dryfu](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Szczegóły funkcji

Sekcja **Szczegóły funkcji** zawiera szczegółowe informacje na temat zmian w dystrybucji wybranej funkcji, a także inne dane statystyczne w miarę upływu czasu. 

Docelowy zestaw danych jest również profilowany w czasie. Odległość statystyczna między rozkładem linii bazowej każdej funkcji jest porównywana z docelowym zestawem danych w czasie, który jest koncepcyjnie podobny do wielkości dryfu danych, z wyjątkiem tego, że ta odległość statystyczna dotyczy pojedynczej funkcji. Dostępne są również wartości minimalna, maksymalna i średnia. 

W Azure Machine Learning Studio, jeśli klikniesz punkt danych na grafie, rozkład wyświetlanej funkcji zostanie odpowiednio dostosowany. Domyślnie pokazuje rozkład bazowego zestawu danych i najnowszą dystrybucję przebiegu tej samej funkcji. 

Te metryki można także pobrać w zestawie SDK języka Python za pomocą `get_metrics()` metody dla `DataDriftDetector` obiektu. 

#### <a name="numeric-features"></a>Funkcje liczbowe 

Funkcje liczbowe są profilowane w każdym przebiegu monitora zestawu danych. Poniższe elementy są dostępne w programie Azure Machine Learning Studio. Dla dystrybucji jest pokazywana gęstość prawdopodobieństwa.

| Metric | Opis |  
| ------ | ----------- |  
| Odległość Wasserstein | Minimalna ilość pracy, aby przekształcić dystrybucję bazową w dystrybucję docelową. |
| Wartość średnia | Średnia wartość funkcji. |
| Wartość minimalna | Minimalna wartość funkcji. |
| Wartość maksymalna | Maksymalna wartość funkcji. |

![Szczegóły funkcji — liczbowe](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Funkcje kategorii 

Funkcje liczbowe są profilowane w każdym przebiegu monitora zestawu danych. Poniższe elementy są dostępne w programie Azure Machine Learning Studio. Dla dystrybucji zostanie wyświetlony histogram.

| Metric | Opis |  
| ------ | ----------- |  
| Odległość euclidian | Odległość geometryczna między rozkładami bazowymi i docelowymi. |
| Unikatowe wartości | Liczba unikatowych wartości (Kardynalność) funkcji. |


![Szczegóły funkcji kategorii](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Metryki, alerty i zdarzenia

Metryki mogą być zapytania w ramach zasobu [usługi Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) skojarzonego z obszarem roboczym uczenia maszynowego. Zapewnia to dostęp do wszystkich funkcji Application Insights, w tym skonfigurować dla niestandardowych reguł alertów i grup akcji, aby wyzwolić akcję, taką jak wiadomość E-mail/SMS/wypychanie/głos lub funkcja platformy Azure. Aby uzyskać szczegółowe informacje, zapoznaj się z pełną dokumentacją Application Insights. 

Aby rozpocząć, przejdź do Azure Portal i wybierz stronę **omówienia** obszaru roboczego.  Skojarzony zasób Application Insights jest po prawej stronie:

[![Azure Portal — omówienie](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Wybierz pozycję Dzienniki (analiza) w obszarze monitorowanie w okienku po lewej stronie:

![Omówienie usługi Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

Metryki monitora zestawu danych są przechowywane jako `customMetrics` . Można napisać i uruchomić zapytanie po skonfigurowaniu monitora zestawu danych, aby je wyświetlić:

[![Zapytanie usługi log Analytics](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Po zidentyfikowaniu metryk w celu skonfigurowania reguł alertów Utwórz nową regułę alertu:

![Nowa reguła alertu](./media/how-to-monitor-datasets/alert-rule.png)

Możesz użyć istniejącej grupy akcji lub utworzyć nową, aby zdefiniować akcję, która ma zostać podjęta po spełnieniu warunków ustawionych:

![Nowa grupa akcji](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ograniczenia i znane problemy:

* Zakres czasu zadań z zadaniami wypełniania jest ograniczony do 31 interwałów ustawienia częstotliwości monitora. 
* Ograniczenie funkcji 200, chyba że lista funkcji nie zostanie określona (wszystkie używane funkcje).
* Rozmiar obliczeń musi być wystarczająco duży, aby można było obsłużyć dane. 
* Upewnij się, że zestaw danych zawiera dane w dniu rozpoczęcia i zakończenia danego monitora.
* Monitory zestawu danych będą działały tylko w zestawach, które zawierają 50 wierszy lub więcej. 

Kolumny lub funkcje w zestawie danych są klasyfikowane jako kategorii lub liczbowe w zależności od warunków w poniższej tabeli. Jeśli funkcja nie spełnia tych warunków — na przykład kolumna typu ciąg z >100 unikatowymi wartościami — funkcja zostanie porzucona z algorytmu dryfowania danych, ale jest nadal profilowana. 

| Typ funkcji | Typ danych | Warunek | Ograniczenia | 
| ------------ | --------- | --------- | ----------- |
| Podzielone na kategorie | String, bool, int, float | Liczba unikatowych wartości w funkcji jest mniejsza niż 100 i mniejsza niż 5% liczby wierszy. | Wartość null jest traktowana jako jej własna Kategoria. | 
| Porządkow | int, float | Wartości w funkcji mają typ danych liczbowych i nie spełniają warunku funkcji kategorii. | Funkcja została porzucona, jeśli >15% wartości ma wartość null. | 

## <a name="next-steps"></a>Następne kroki

* Przejdź do programu [Azure Machine Learning Studio](https://ml.azure.com) lub [notesu Python](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) , aby skonfigurować Monitor zestawu danych.
* Zobacz jak skonfigurować dryfowanie danych dla [modeli wdrożonych w usłudze Azure Kubernetes Service](how-to-monitor-data-drift.md).
* Skonfiguruj monitory przedryfania zestawu danych za pomocą [siatki zdarzeń](how-to-use-event-grid.md). 
