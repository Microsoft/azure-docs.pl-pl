---
title: Analizowanie i monitorowanie dryfu danych w zestawach danych (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Tworzenie zestawów danych usługi Azure Machine Learning monitorów (podgląd), monitorowanie dryfu danych w zestawach danych i konfigurowanie alertów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/04/2019
ms.openlocfilehash: e49c621d92a8aa604b5f95291c5d80c0141f41dd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682721"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Wykrywanie dryftu danych (podgląd) w zestawach danych
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak utworzyć monitory zestawu danych usługi Azure Machine Learning (wersja zapoznawcza), monitorować dryf danych i zmiany statystyczne w zestawach danych oraz konfigurować alerty.

Za pomocą monitorów zestawu danych usługi Azure Machine Learning można:
* **Analizuj dryf w danych,** aby zrozumieć, jak zmienia się wraz z czasem.
* **Monitoruj dane modelu** pod kątem różnic między zestawami danych szkoleniowych i obsługujących.
* **Monitoruj nowe dane** pod kątem różnic między dowolnym zestawem danych bazowych i docelowych.
* **Funkcje profilu w danych,** aby śledzić, jak właściwości statystyczne zmieniają się w czasie.
* **Skonfiguruj alerty dotyczące dryfu danych** dla wczesnych ostrzeżeń o potencjalnych problemach. 

Metryki i szczegółowe informacje są dostępne za pośrednictwem zasobu [usługi Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) skojarzonego z obszarem roboczym usługi Azure Machine Learning.

> [!Important]
> Należy pamiętać, że monitorowanie dryfu danych za pomocą SDK jest dostępne we wszystkich wersjach, podczas gdy dane monitorowania dryfują przez studio w internecie jest tylko Enterprise edition.

## <a name="prerequisites"></a>Wymagania wstępne

Do tworzenia monitorów zestawu danych i pracy z nią potrzebne są:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree) już dziś.
* [Obszar roboczy usługi Azure Machine Learning](how-to-manage-workspace.md).
* [Zainstalowano zestaw SDK usługi Azure Machine Learning dla języka Python,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)który zawiera pakiet zestawów danych azureml.
* Dane strukturalne (tabelaryczne) z sygnaturą czasową określoną w ścieżce pliku, nazwie pliku lub kolumnie w danych.

## <a name="what-is-data-drift"></a>Co to jest dryft danych?

W kontekście uczenia maszynowego dryft danych jest zmiana danych wejściowych modelu, która prowadzi do obniżenia wydajności modelu. Jest to jeden z głównych powodów, dla których dokładność modelu pogarsza się w czasie, dzięki czemu monitorowanie dryfu danych pomaga wykrywać problemy z wydajnością modelu.

Przyczyny dryfu danych obejmują: 

- Zmiany w procesie wyższego rzędu, takie jak wymiana czujnika, który zmienia jednostki miary z cali na centymetry. 
- Problemy z jakością danych, takie jak uszkodzony czujnik zawsze odczytuje 0.
- Naturalny dryf w danych, taki jak średnia zmiana temperatury wraz z porami roku.
- Zmiana relacji między elementami lub zmiana współzmienna. 

Za pomocą monitorów zestawu danych usługi Azure Machine Learning można skonfigurować alerty, które pomagają w wykrywaniu dryfu danych w zestawach danych w czasie. 

### <a name="dataset-monitors"></a>Monitory zestawu danych 

Można utworzyć monitor zestawu danych do wykrywania i ostrzegania o dryfowaniu danych na nowych danych w zestawie danych, analizowania danych historycznych pod kątem dryfu i profilowania nowych danych w czasie. Algorytm dryfu danych zapewnia ogólną miarę zmian w danych i wskazanie, które funkcje są odpowiedzialne za dalsze badania. Monitory zestawu danych generują szereg innych metryk, profilując nowe dane w zestawie `timeseries` danych. Niestandardowe alerty można skonfigurować na wszystkich metrykach generowanych przez monitor za pośrednictwem [usługi Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Monitory zestawu danych mogą służyć do szybkiego wychwytywania problemów z danymi i skracają czas debugowania problemu przez zidentyfikowanie prawdopodobnych przyczyn.  

Pod względem koncepcyjnym istnieją trzy podstawowe scenariusze konfigurowania monitorów zestawów danych w usłudze Azure Machine Learning.

Scenariusz | Opis
---|---
Monitorowanie danych obsługujących model w celu odpływu z danych szkoleniowych modelu | Wyniki z tego scenariusza można interpretować jako monitorowanie serwera proxy dla dokładności modelu, biorąc pod uwagę, że dokładność modelu ulega pogorszeniu, jeśli obsługujących danych dryfuje z danych szkoleniowych.
Monitorowanie zestawu danych szeregów czasowych dla dryfu z poprzedniego okresu. | Ten scenariusz jest bardziej ogólny i może służyć do monitorowania zestawów danych zaangażowanych nadrzędnych lub niższego rzędu tworzenia modelu.  Docelowy zestaw danych musi mieć kolumnę sygnatury czasowej, podczas gdy zestaw danych linii bazowej może być dowolnym zestawem danych tabelarycznym, który ma funkcje wspólne z docelowym zestawem danych.
Przeprowadzanie analiz na podstawie danych z przeszłości. | Ten scenariusz może służyć do zrozumienia danych historycznych i informowania decyzji w ustawieniach dla monitorów zestawu danych.

## <a name="how-dataset-can-monitor-data"></a>Jak zestaw danych może monitorować dane

Za pomocą usługi Azure Machine Learning dryf danych jest monitorowany za pośrednictwem zestawów danych. Aby monitorować dryft danych, określa się zestaw danych linii bazowej — zwykle zestaw danych szkoleniowych dla modelu. Docelowy zestaw danych — zwykle dane wejściowe modelu — jest porównywany w czasie z zestawem danych linii bazowej. To porównanie oznacza, że docelowy zestaw danych musi mieć określoną kolumnę sygnatury czasowej.

### <a name="set-the-timeseries-trait-in-the-target-dataset"></a>Ustawianie `timeseries` cechy w docelowym zestawie danych

Docelowy zestaw danych musi `timeseries` mieć ustawioną cechę, określając kolumnę sygnatury czasowej z kolumny w danych lub kolumny wirtualnej wywodzącej się ze wzorca ścieżki plików. Można to zrobić za pośrednictwem zestawu SDK języka Python lub usługi Azure Machine Learning studio. Aby dodać `timeseries` cechę do zestawu danych, należy określić kolumnę reprezentującą sygnaturę czasową "drobnoziarnistą". Jeśli dane są podzielone na strukturę folderów z informacjami o czasie, takimi jak "{yyy/MM/dd}", można utworzyć kolumnę wirtualną za pomocą ustawienia wzorca ścieżki i ustawić ją jako sygnaturę czasową "gruboziarnistego", aby poprawić znaczenie funkcji szeregów czasowych. 

#### <a name="python-sdk"></a>Zestaw SDK dla języka Python

[`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) Metoda [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) klasy definiuje kolumnę sygnatury czasowej dla zestawu danych. 

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

Pełny przykład użycia `timeseries` cech zestawów danych można znaleźć w [przykładowym notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) lub dokumentacji zestawu danych [SDK](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

#### <a name="azure-machine-learning-studio"></a>Studio uczenia maszynowego Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Jeśli tworzysz zestaw danych przy użyciu usługi Azure Machine Learning studio, upewnij się, że ścieżka do danych zawiera informacje o sygnatury czasowej, uwzględnij wszystkie podfoldery z danymi i ustaw format partycji. 

W poniższym przykładzie wszystkie dane w podfolderze *NoaAIsdFlorida/2019* są pobierane, a format partycji określa rok, miesiąc i dzień sygnatury czasowej. 

[![Format partycji](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

W **ustawieniach schematu** określ kolumnę sygnatury czasowej z kolumny wirtualnej lub rzeczywistej w określonym zestawie danych:

![Znacznik czasu](./media/how-to-monitor-datasets/timestamp.png)

## <a name="dataset-monitor-settings"></a>Ustawienia monitora zestawu danych

Po utworzeniu zestawu danych z określonymi ustawieniami sygnatury czasowej można skonfigurować monitor zestawu danych.

Różne ustawienia monitora zestawu danych są podzielone na trzy grupy: **Podstawowe informacje, Ustawienia monitora** i **Ustawienia zasypywania**.

### <a name="basic-info"></a>Podstawowe informacje

Ta tabela zawiera podstawowe ustawienia używane dla monitora zestawu danych.

| Ustawienie | Opis | Porady | Modyfikowalny | 
| ------- | ----------- | ---- | ------- | 
| Nazwa | Nazwa monitora zestawu danych. | | Nie |
| Zestaw danych linii bazowej | Tabelaryczny zestaw danych, który będzie używany jako linia bazowa do porównywania docelowego zestawu danych w czasie. | Zestaw danych linii bazowej musi mieć funkcje wspólne z docelowym zestawem danych. Ogólnie rzecz biorąc linia bazowa powinna być ustawiona na zestaw danych szkoleniowych modelu lub wycinek docelowego zestawu danych. | Nie |
| Docelowy zestaw danych | Tabelaryczny zestaw danych z określoną kolumną sygnatury czasowej, która będzie analizowana pod kątem dryfu danych. | Docelowy zestaw danych musi mieć funkcje wspólne z zestawem danych linii bazowej i powinien być zestawem `timeseries` danych, do którego dołączane są nowe dane. Dane historyczne w docelowym zestawie danych mogą być analizowane lub można monitorować nowe dane. | Nie | 
| Częstotliwość | Częstotliwość, która będzie używana do planowania zadania potoku i analizowania danych historycznych, jeśli jest uruchomiony zasyp. Opcje obejmują codziennie, co tydzień lub co miesiąc. | Dostosuj to ustawienie, aby uwzględnić porównywalny rozmiar danych do linii bazowej. | Nie | 
| Funkcje | Lista funkcji, które będą analizowane pod kątem dryfu danych w czasie. | Ustaw funkcję wyjściową modelu do pomiaru dryfu koncepcji. Nie należy dołączać funkcji, które naturalnie dryfują w czasie (miesiąc, rok, indeks itp.). Po dostosowaniu listy funkcji można uzupełniać i istniejący monitor dryfu danych. | Tak | 
| Docelowy zasób obliczeniowy | Cel obliczeniowy usługi Azure Machine Learning do uruchamiania zadań monitorowania zestawu danych. | | Tak | 

### <a name="monitor-settings"></a>Ustawienia monitora

Te ustawienia są dla zaplanowanego potoku monitora zestawu danych, który zostanie utworzony. 

| Ustawienie | Opis | Porady | Modyfikowalny | 
| ------- | ----------- | ---- | ------- |
| Włączanie | Włączanie lub wyłączanie harmonogramu w potoku monitora zestawu danych | Wyłącz harmonogram analizowania danych historycznych z ustawieniem zasypki. Można go włączyć po utworzeniu monitora zestawu danych. | Tak | 
| Opóźnienie | Czas w godzinach, potrzebny jest do uzyskania danych w zestawie danych. Na przykład jeśli trwa trzy dni dla danych, aby uzyskać w sql db zestaw danych hermetyzuje, ustawić opóźnienie do 72. | Nie można zmienić po utworzeniu monitora zestawu danych | Nie | 
| Adresy e-mail | Adresy e-mail do alertów na podstawie przekroczenie progu procentowego dryfu danych. | Wiadomości e-mail są wysyłane za pośrednictwem usługi Azure Monitor. | Tak | 
| Próg | Próg procentowy dryfu danych dla alertów e-mail. | Dalsze alerty i zdarzenia można ustawić na wiele innych metryk w zasobie skojarzonej z usługą Application Insights obszaru roboczego. | Tak | 

### <a name="backfill-settings"></a>Ustawienia zasypywania

Te ustawienia służą do uruchamiania wypełnienia zasypami danych z przeszłości dla metryk dryfu danych.

| Ustawienie | Opis | Porady |
| ------- | ----------- | ---- |
| Data rozpoczęcia | Data rozpoczęcia zadania wypełniania. | | 
| Data końcowa | Data zakończenia zadania zasypowania. | Data zakończenia nie może być większa niż 31*jednostek częstotliwości czasu od daty rozpoczęcia. Na istniejącym monitorze zestawu danych metryki mogą być wypełniane w celu analizowania danych historycznych lub zastępowania metryk zaktualizowanymi ustawieniami. |

## <a name="create-dataset-monitors"></a>Tworzenie monitorów zestawu danych 

Tworzenie monitorów zestawu danych do wykrywania i alertów o dryfowaniu danych w nowym zestawie danych za pomocą studia usługi Azure Machine Learning lub zestawu SDK języka Python. 

### <a name="azure-machine-learning-studio"></a>Studio uczenia maszynowego Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

Aby skonfigurować alerty na monitorze zestawu danych, obszar roboczy zawierający zestaw danych, dla którego chcesz utworzyć monitor, musi mieć możliwości wersji Enterprise. 

Po potwierdzeniu funkcji obszaru roboczego przejdź do strony głównej studia i wybierz kartę Zestawy danych po lewej stronie. Wybierz monitory zestawu danych.

![Lista monitorów](./media/how-to-monitor-datasets/monitor-list.png)

Kliknij przycisk **+Utwórz monitor** i kontynuuj za pomocą kreatora, klikając przycisk **Dalej**.

![Kreatora](./media/how-to-monitor-datasets/wizard.png)

Wynikowy monitor zestawu danych pojawi się na liście. Wybierz go, aby przejść do strony szczegółów tego monitora.

### <a name="from-python-sdk"></a>Od python SDK

Szczegółowe informacje można znaleźć w [dokumentacji referencyjnej zestawu SDK języka Python na temat dryfu danych.](/python/api/azureml-datadrift/azureml.datadrift) 

W poniższym przykładzie pokazano, jak utworzyć monitor zestawu danych przy użyciu zestawu SDK języka Python

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

Pełny przykład konfigurowania zestawu `timeseries` danych i detektora dryfu danych można znaleźć w naszym [przykładowym notesie](https://aka.ms/datadrift-notebook).

## <a name="understanding-data-drift-results"></a>Opis wyników dryfu danych

Monitor danych tworzy dwie grupy wyników: Przegląd dryfu i Szczegóły funkcji. Poniższa animacja przedstawia dostępne wykresy monitora dryfu na podstawie wybranej operacji i metryki. 

![Wideo demo](./media/how-to-monitor-datasets/video.gif)

### <a name="drift-overview"></a>Przegląd driftu

Sekcja **Przegląd driftu** zawiera szczegółowe informacje na temat wielkości dryfu danych i które funkcje należy dokładniej zbadać. 

| Metryka | Opis | Porady | 
| ------ | ----------- | ---- | 
| Wielkość dryfu danych | Podane jako wartość procentowa między bazowym i docelowym zestawem danych w czasie. W zakresie od 0 do 100, gdzie 0 wskazuje identyczne zestawy danych i 100 wskazuje, że usługa azure machine learning dryfowania danych można całkowicie odróżnić dwa zestawy danych. | Hałas w precyzyjnym odmierzonym procentu jest oczekiwany ze względu na techniki uczenia maszynowego wykorzystywane do generowania tej wielkości. | 
| Wkład driftu według funkcji | Udział każdej operacji w docelowym zestawie danych do zmierzonej wielkości dryfu. |  Ze względu na zmianę współzmienną podstawowa dystrybucja funkcji nie musi się zmieniać, aby mieć stosunkowo duże znaczenie funkcji. | 

Poniższa ilustracja jest przykładem wykresów widocznych w wynikach **przeglądu Drift** w studiu usługi Azure Machine Learning, wynikających z zasypki [zintegrowanych danych powierzchni NOAA.](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) Próbowano, `stationName contains 'FLORIDA'`a w styczniu 2019 r. wykorzystano jako bazowy zestaw danych, a wszystkie dane za 2019 r. wykorzystano jako cel.
 
![Przegląd driftu](./media/how-to-monitor-datasets/drift-overview.png)

### <a name="feature-details"></a>Szczegóły obiektu

Sekcja **Szczegóły funkcji** zawiera wgląd na poziomie funkcji w zakresie zmian w dystrybucji wybranej funkcji, a także inne statystyki w czasie. 

Docelowy zestaw danych jest również profilowane w czasie. Statystyczna odległość między rozkładem bazowym każdej operacji jest porównywana z docelowym zbiorem danych w czasie, który jest koncepcyjnie podobny do wielkości dryfu danych, z wyjątkiem tego statystycznej odległości dla pojedynczej operacji. Min, max i średnia są również dostępne. 

W studiu usługi Azure Machine Learning, jeśli klikniesz na punkt danych na wykresie rozkład wyświetlanej funkcji dostosuje się odpowiednio. Domyślnie pokazuje rozkład zestawu danych linii bazowej i rozkład ostatniego uruchomienia tej samej funkcji. 

Te metryki można również pobrać w SDK `get_metrics()` języka `DataDriftDetector` Python za pomocą metody na obiekcie. 

#### <a name="numeric-features"></a>Funkcje numeryczne 

Funkcje liczbowe są profilowane w każdym uruchomieniu monitora zestawu danych. Poniższe informacje są udostępniane w studio usługi Azure Machine Learning. Gęstość prawdopodobieństwa jest wyświetlana dla rozkładu.

| Metryka | Opis |  
| ------ | ----------- |  
| Odległość Wasserstein | Minimalna ilość pracy, aby przekształcić rozkład linii bazowej w dystrybucję docelową. |
| Średnia wartość | Średnia wartość operacji. |
| Wartość minimalna | Minimalna wartość operacji. |
| Wartość maksymalna | Maksymalna wartość operacji. |

![Szczegóły operacji numeryczne](./media/how-to-monitor-datasets/feature-details.png)

#### <a name="categorical-features"></a>Funkcje kategoryczne 

Funkcje liczbowe są profilowane w każdym uruchomieniu monitora zestawu danych. Poniższe informacje są udostępniane w studio usługi Azure Machine Learning. Histogram jest pokazany dla dystrybucji.

| Metryka | Opis |  
| ------ | ----------- |  
| Odległość euklidesowa | Odległość geometryczna między rozkładami linii bazowej i docelowej. |
| Unikalne wartości | Liczba unikatowych wartości (kardynalność) operacji. |


![Szczegóły funkcji kategorycznie](./media/how-to-monitor-datasets/feature-details2.png)

## <a name="metrics-alerts-and-events"></a>Metryki, alerty i zdarzenia

Metryki można wyszukiwać w zasobie [usługi Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) skojarzone z obszarem roboczym uczenia maszynowego. Który daje dostęp do wszystkich funkcji usługi Application Insights, w tym skonfigurować dla niestandardowych reguł alertów i grup akcji, aby wyzwolić akcję, takich jak e-mail/SMS/Push/Voice lub funkcji platformy Azure. Szczegółowe informacje można znaleźć w pełnej dokumentacji usługi Application Insights. 

Aby rozpocząć, przejdź do witryny Azure Portal i wybierz stronę **Przegląd** obszaru roboczego.  Skojarzony zasób usługi Application Insights znajduje się po prawej stronie:

[![Azure Portal — omówienie](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Wybierz pozycję Dzienniki (Analytics) w obszarze Monitorowanie w lewym okienku:

![Omówienie szczegółowych informacji o aplikacjach](./media/how-to-monitor-datasets/ai-overview.png)

Metryki monitora zestawu danych `customMetrics`są przechowywane jako . Po skonfigurowaniu monitora zestawu danych można napisać i uruchomić kwerendę w celu ich wyświetlenia:

[![Zapytanie analizy dzienników](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Po zidentyfikowaniu metryk w celu skonfigurowania reguł alertów utwórz nową regułę alertu:

![Nowa reguła alertu](./media/how-to-monitor-datasets/alert-rule.png)

Można użyć istniejącej grupy akcji lub utworzyć nową, aby zdefiniować akcję, która ma zostać podjęta po spełnieniu określonych warunków:

![Nowa grupa akcji](./media/how-to-monitor-datasets/action-group.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ograniczenia i znane problemy:

* Zakres czasu zadań zasypywania jest ograniczony do 31 interwałów ustawienia częstotliwości monitora. 
* Ograniczenie 200 funkcji, chyba że nie określono listy funkcji (wszystkie używane funkcje).
* Rozmiar obliczeń musi być wystarczająco duży, aby obsłużyć dane. 
* Upewnij się, że zestaw danych ma dane w dacie rozpoczęcia i zakończenia dla danego uruchomienia monitora.
* Monitory zestawu danych będą działać tylko na zestawach danych, które zawierają 50 wierszy lub więcej. 

Kolumny lub funkcje w zestawie danych są klasyfikowane jako kategoryczne lub liczbowe na podstawie warunków przedstawionych w poniższej tabeli. Jeśli funkcja nie spełnia tych warunków — na przykład kolumna ciągu typu z >100 unikatowych wartości - funkcja jest usuwana z naszego algorytmu dryfu danych, ale nadal jest profilowana. 

| Typ operacji | Typ danych | Warunek | Ograniczenia | 
| ------------ | --------- | --------- | ----------- |
| Podzielone na kategorie | sznurek, bool, int, pływak | Liczba unikatowych wartości w operacji jest mniejsza niż 100 i mniej niż 5% liczby wierszy. | Null jest traktowany jako własną kategorię. | 
| Numeryczne | int, pływak | Wartości w operacji są typu danych liczbowych i nie spełniają warunku dla operacji kategorycznej. | Funkcja porzucona, jeśli >15% wartości ma wartość null. | 

## <a name="next-steps"></a>Następne kroki

* Przejdź do [studia usługi Azure Machine Learning](https://ml.azure.com) lub [notesu języka Python,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) aby skonfigurować monitor zestawu danych.
* Zobacz, jak skonfigurować dryf danych w [modelach wdrożonych w usłudze Azure Kubernetes](how-to-monitor-data-drift.md).
* Skonfiguruj monitory dryfu zestawu danych z [siatką zdarzeń](how-to-use-event-grid.md). 
