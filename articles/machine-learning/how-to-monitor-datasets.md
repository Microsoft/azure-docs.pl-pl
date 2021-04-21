---
title: Wykrywanie dryfu danych w zestawach danych (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować wykrywanie dryfu danych w usłudze Azure Learning. Tworzenie monitorów zestawów danych (wersja zapoznawcza), monitorowanie dryfu danych i konfigurowanie alertów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml, contperf-fy21q2
ms.openlocfilehash: 95fb2dfeea98b988eaeaea43efc4ea44fd6e33fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770313"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Wykrywanie dryfu danych (wersja zapoznawcza) w zestawach danych

Dowiedz się, jak monitorować dryf danych i ustawiać alerty, gdy dryf jest wysoki.  

Monitory Azure Machine Learning zestawów danych (wersja zapoznawcza) mogą:
* **Analizowanie dryfu danych w celu** zrozumienia ich zmian w czasie.
* **Monitoruj dane modelu** pod względem różnic między trenowaniem a obsługującymi zestawami danych.  Rozpocznij od [zebrania danych modelu z wdrożonych modeli.](how-to-enable-data-collection.md)
* **Monitoruj nowe dane** pod względem różnic między dowolnym punktem odniesienia i docelowym zestawem danych.
* **Funkcje profilu w danych do** śledzenia zmian właściwości statystycznych w czasie.
* **Skonfiguruj alerty dotyczące dryfu danych w** celu wczesnego ostrzeżeń o potencjalnych problemach. 
* **[Utwórz nową wersję zestawu danych,](how-to-version-track-datasets.md)** gdy określisz, że dane zbyt bardzo się oddalają.

Do utworzenia monitora jest używany zestaw danych usługi [Azure Machine Learning.](how-to-create-register-datasets.md) Zestaw danych musi zawierać kolumnę znacznika czasu.

Metryki dryfu danych można wyświetlać za pomocą zestawu SDK języka Python lub w Azure Machine Learning studio.  Inne metryki i szczegółowe informacje są dostępne za pośrednictwem zasobu [usługi Azure Application Insights](../azure-monitor/app/app-insights-overview.md) skojarzonego z Azure Machine Learning roboczym.

> [!IMPORTANT]
> Wykrywanie dryfu danych dla zestawów danych jest obecnie dostępne w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Do tworzenia monitorów zestawów danych i pracy z nich potrzebne są:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) już dziś.
* Obszar [Azure Machine Learning obszaru roboczego.](how-to-manage-workspace.md)
* Zainstalowany [Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/install), który zawiera pakiet azureml-datasets.
* Dane ze strukturą (tabelaryka) z sygnaturą czasową określoną w ścieżce pliku, nazwie pliku lub kolumnie w danych.

## <a name="what-is-data-drift"></a>Co to jest dryf danych?

Dryf danych jest jednym z większej przyczyny pogorszenia dokładności modelu w czasie.  W przypadku modeli uczenia maszynowego dryf danych to zmiana danych wejściowych modelu, która prowadzi do spadku wydajności modelu.  Monitorowanie dryfu danych pomaga wykrywać te problemy z wydajnością modelu.

Przyczyny dryfu danych obejmują:

- Zmiany procesu nadrzędnego, takie jak wymiana czujnika, który zmienia jednostki miary z cala na cm. 
- Problemy z jakością danych, takie jak uszkodzony czujnik, zawsze odczytuje 0.
- Naturalny dryf danych, na przykład średnia temperatura zmienia się wraz z pory roku.
- Zmiana w relacji między cechami lub kowariantna zmiana. 

Azure Machine Learning upraszcza wykrywanie dryfu przez obliczanie pojedynczej metryki abstrakcję złożoności porównywanych zestawów danych.  Te zestawy danych mogą mieć setki funkcji i dziesiątki tysięcy wierszy. Po wykryciu dryfu możesz przejść do szczegółów, które funkcje powodują dryf.  Następnie należy sprawdzić metryki na poziomie funkcji, aby debugować i izolować główną przyczynę dryfu.

Takie podejście od góry do dołu ułatwia monitorowanie danych zamiast tradycyjnych technik opartych na zasadach. Techniki oparte na zasadach, takie jak dozwolony zakres danych lub dozwolone unikatowe wartości, mogą być czasochłonne i podatne na błędy.

W Azure Machine Learning monitory zestawów danych są wykorzystywane do wykrywania dryfu danych i ostrzegania o nich.
  
### <a name="dataset-monitors"></a>Monitory zestawów danych 

Monitor zestawu danych umożliwia:

* Wykrywanie dryfu danych i alerty dotyczące dryfu danych w nowych danych w zestawie danych.
* Analizowanie danych historycznych pod celu analizy dryfu.
* Profilowanie nowych danych w czasie.

Algorytm dryfu danych zapewnia ogólną miarę zmian danych i wskazanie, które funkcje są odpowiedzialne za dalsze badanie. Monitory zestawów danych dają wiele innych metryk przez profilowanie nowych danych w zestawie `timeseries` danych. 

Niestandardowe alerty można skonfigurować dla wszystkich metryk wygenerowanych przez monitor za pośrednictwem usługi [Azure Application Insights](../azure-monitor/app/app-insights-overview.md). Monitory zestawów danych mogą służyć do szybkiego wychwytowania problemów z danymi i skrócenia czasu debugowania problemu przez zidentyfikowanie prawdopodobnych przyczyn.  

Koncepcyjnie istnieją trzy podstawowe scenariusze konfigurowania monitorów zestawów danych w Azure Machine Learning.

Scenariusz | Opis
---|---
Monitorowanie danych obsługujących model pod celu odejmowania ich od danych treningowych | Wyniki z tego scenariusza można interpretować jako monitorowanie serwera proxy dokładności modelu, ponieważ dokładność modelu spada, gdy dane obsługujące oddalają się od danych treningowych.
Monitoruj zestaw danych szeregów czasu pod celu monitorowania dryfu w poprzednich okresach. | Ten scenariusz jest bardziej ogólny i może służyć do monitorowania zestawów danych związanych z tworzeniem modeli nadrzędnych lub dalszych.  Docelowy zestaw danych musi mieć kolumnę znacznika czasu. Zestaw danych linii bazowej może być dowolnym tabelaryznym zestawem danych, który ma funkcje wspólne z docelowym zestawem danych.
Wykonywanie analizy na podstawie przeszłych danych. | Ten scenariusz może służyć do zrozumienia danych historycznych i podejmowania decyzji w ustawieniach monitorów zestawów danych.

Monitory zestawów danych zależą od następujących usług platformy Azure.

|Usługa platformy Azure  |Opis  |
|---------|---------|
| *Dataset* | Drift używa Machine Learning danych do pobierania danych treningowych i porównywania danych w celu trenowania modelu.  Generowanie profilu danych służy do generowania niektórych zgłoszonych metryk, takich jak min, maksimum, odrębne wartości, liczba odrębnych wartości. |
| *Potok i obliczenia usługi AzureML* | Zadanie obliczania dryfu jest hostowane w potoku azureml.  Zadanie jest wyzwalane na żądanie lub zgodnie z harmonogramem w celu uruchomienia w obliczeniach skonfigurowanych w czasie tworzenia monitora dryfu.
| *Application Insights*| Dryf emituje metryki do Application Insights należących do obszaru roboczego uczenia maszynowego.
| *Azure Blob Storage*| Dryf emituje metryki w formacie JSON do usługi Azure Blob Storage.

### <a name="baseline-and-target-datasets"></a>Bazowe i docelowe zestawy danych 

Monitorujesz [zestawy danych usługi Azure Machine Learning pod](how-to-create-register-datasets.md) celu dryfu danych. Podczas tworzenia monitora zestawu danych będziesz odwoływać się do:
* Zestaw danych linii bazowej — zazwyczaj zestaw danych trenowania dla modelu.
* Docelowy zestaw danych — zwykle dane wejściowe modelu — jest porównywany z czasem z zestawem danych linii bazowej. To porównanie oznacza, że docelowy zestaw danych musi mieć określoną kolumnę znacznika czasu.

Monitor porówna zestawy danych bazowych i docelowych.

## <a name="create-target-dataset"></a>Tworzenie docelowego zestawu danych

Docelowy zestaw danych wymaga zestawu cech, określając kolumnę znacznika czasu na podstawie kolumny w danych lub kolumny wirtualnej pochodzącej ze wzorca ścieżki `timeseries` plików. Utwórz zestaw danych ze znacznikiem czasu za pomocą zestawu [SDK języka Python](#sdk-dataset) lub [Azure Machine Learning studio](#studio-dataset). Aby dodać cechę do zestawu danych, należy określić kolumnę reprezentującą "znacznik `timeseries` czasu". Jeśli dane są podzielone na partycje w strukturze folderów z informacjami o czasie, takimi jak "{yyyy/MM/dd}", utwórz kolumnę wirtualną za pośrednictwem ustawienia wzorca ścieżki i ustaw ją jako "sygnaturę czasową partycji", aby zwiększyć ważność funkcji szeregów czasu.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

Metoda [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) klasy [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)  definiuje kolumnę sygnatury czasowej dla zestawu danych.

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

> [!TIP]
> Pełny przykład użycia cech zestawów danych można znaleźć w przykładzie notesu lub dokumentacji zestawu `timeseries` [SDK zestawów danych](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-). [](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb)

# <a name="studio"></a>[Studio](#tab/azure-studio)

<a name="studio-dataset"></a>

Jeśli tworzysz zestaw danych przy użyciu Azure Machine Learning studio, upewnij się, że ścieżka do danych zawiera informacje o sygnaturze czasowej, dołącz wszystkie podfoldery z danymi i ustaw format partycji.

W poniższym przykładzie są brane wszystkie dane z podfolderu *NoaaIsdFoldida/2019,* a format partycji określa rok, miesiąc i dzień znacznika czasu.

[![Format partycji](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

W **ustawieniach** Schematu określ kolumnę znacznika czasu z kolumny wirtualnej lub rzeczywistej w określonym zestawie danych:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Ustawianie znacznika czasu":::

Jeśli dane są podzielone na partycje według daty, jak ma to miejsce w tym przypadku, możesz również określić partition_timestamp.  Umożliwia to wydajniejsze przetwarzanie dat.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Sygnatura czasowa partycji":::

---

## <a name="create-dataset-monitor"></a>Tworzenie monitora zestawu danych

Utwórz monitor zestawu danych w celu wykrywania dryfu danych i ostrzegania o dryfie danych w nowym zestawie danych.  Użyj zestawu [SDK języka Python](#sdk-monitor) lub [Azure Machine Learning studio](#studio-monitor).

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> Aby uzyskać szczegółowe informacje, zobacz dokumentację referencyjną zestawu [SDK języka Python dotyczącą dryfu](/python/api/azureml-datadrift/azureml.datadrift) danych. 

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

> [!TIP]
> Pełny przykład konfigurowania zestawu danych i detektora dryfu danych można znaleźć `timeseries` w naszym [przykładzie notesu](https://aka.ms/datadrift-notebook).


# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="studio-monitor"></a>

1. Przejdź do [strony głównej programu Studio.](https://ml.azure.com)
1. Wybierz **kartę Zestawy** danych po lewej stronie. 
1. Wybierz pozycję **Monitory zestawu danych.**
   ![Lista monitorów](./media/how-to-monitor-datasets/monitor-list.png)

1. Kliknij przycisk **+Utwórz monitor** i kontynuuj pracę kreatora, klikając przycisk **Dalej.**  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Kreator tworzenia monitora":::

* **Wybierz docelowy zestaw danych**.  Docelowy zestaw danych to tabelarowy zestaw danych z określoną kolumną znacznika czasu, który będzie analizowany pod celu dryfu danych. Docelowy zestaw danych musi mieć cechy wspólne z bazowym zestawem danych i powinien być zestawem danych, do którego są `timeseries` dołączane nowe dane. Dane historyczne w docelowym zestawie danych można analizować lub monitorować nowe dane.

* **Wybierz zestaw danych linii bazowej.**  Wybierz tabelarowy zestaw danych, który będzie używany jako punkt odniesienia do porównywania docelowego zestawu danych w czasie.  Podstawowy zestaw danych musi mieć funkcje wspólne z docelowym zestawem danych.  Wybierz zakres czasu, aby użyć wycinka docelowego zestawu danych, lub określ oddzielny zestaw danych do użycia jako punkt odniesienia.

* **Monitoruj ustawienia**.  Te ustawienia są dla potoku zaplanowanego monitora zestawu danych, który zostanie utworzony. 

    | Ustawienie | Opis | Porady | Modyfikowalny | 
    | ------- | ----------- | ---- | ------- |
    | Nazwa | Nazwa monitora zestawu danych. | | Nie |
    | Funkcje | Lista funkcji, które będą analizowane pod uwagę dryfowania danych w czasie. | Ustaw na funkcje wyjściowe modelu, aby zmierzyć dryf koncepcji. Nie należy uwzględniać funkcji, które naturalnie dryfają w czasie (miesiąc, rok, indeks itp.). Po dostosowaniu listy funkcji można wypełnić i istniejący monitor dryfu danych. | Tak | 
    | Docelowy zasób obliczeniowy | Azure Machine Learning docelowy obiekt obliczeniowy do uruchamiania zadań monitorowania zestawu danych. | | Tak | 
    | Włącz | Włączanie lub wyłączanie harmonogramu w potoku monitora zestawu danych | Wyłącz harmonogram, aby analizować dane historyczne za pomocą ustawienia wypełniania. Można ją włączyć po utworzeniu monitora zestawu danych. | Tak | 
    | Częstotliwość | Częstotliwość, która będzie używana do planowania zadania potoku i analizowania danych historycznych w przypadku uruchomienia wypełniania. Opcje obejmują codziennie, co tydzień lub co miesiąc. | Każdy przebieg porównuje dane w docelowym zestawie danych zgodnie z częstotliwością: <li>Codziennie: porównaj ostatni pełny dzień w docelowym zestawie danych z punktem odniesienia <li>Co tydzień: porównaj ostatni pełny tydzień (od poniedziałku do niedzieli) w docelowym zestawie danych z punktem odniesienia <li>Co miesiąc: porównaj ostatni pełny miesiąc w docelowym zestawie danych z punktem odniesienia | Nie | 
    | Opóźnienie | Czas (w godzinach) na dotarcie danych do zestawu danych. Jeśli na przykład dane dotrą do bazy danych SQL DB hermetyzowane przez zestaw danych, ustaw opóźnienie na 72. | Nie można zmienić po utworzeniu monitora zestawu danych | Nie | 
    | Adresy e-mail | Adresy e-mail do alertów na podstawie progu procentowego dryfu danych. | Wiadomości e-mail są wysyłane za pośrednictwem Azure Monitor. | Tak | 
    | Próg | Próg procentowy dryfu danych dla alertów e-mail. | Dalsze alerty i zdarzenia można ustawić dla wielu innych metryk w skojarzonym z obszarem roboczym Application Insights zasobów. | Tak |

Po zakończeniu pracy kreatora wynikowy monitor zestawu danych pojawi się na liście. Wybierz go, aby przejść do strony szczegółów tego monitora.

---

## <a name="understand-data-drift-results"></a>Informacje o wynikach dryfu danych

W tej sekcji przedstawiono wyniki monitorowania zestawu danych, które znajdują się na **stronie** Monitory zestawu danych zestawów danych w  /   programie Azure Studio.  Na tej stronie możesz zaktualizować ustawienia, a także przeanalizować istniejące dane w określonym okresie.  

Zacznij od szczegółowych informacji najwyższego poziomu dotyczących wielkości dryfu danych i najważniejszych cech, które należy zbadać.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Omówienie dryfu":::


| Metric | Opis | 
| ------ | ----------- | 
| Wielkość dryfu danych | Procent dryfu między punktem odniesienia i docelowym zestawem danych w czasie. Zakres od 0 do 100, 0 oznacza identyczne zestawy danych, a 100 oznacza, że model dryfu danych Azure Machine Learning może całkowicie odróżnić te dwa zestawy danych. Szum w mierzonej dokładnej wartości procentowej jest oczekiwany ze względu na techniki uczenia maszynowego używane do generowania tej wielkości. | 
| Najważniejsze funkcje dryfujące | Przedstawia funkcje z zestawu danych, które najbardziej oddalają się i w związku z tym najbardziej przyczyniają się do metryki Drift Magnitude (Wielkość dryfu). Ze względu na kowariantną zmianę, podstawowy rozkład funkcji nie musi zmieniać się, aby mieć stosunkowo wysoką ważność funkcji. |
| Próg | Wielkość dryfu danych poza ustawiony próg spowoduje wyzwolenie alertów. Można to skonfigurować w ustawieniach monitora. | 

### <a name="drift-magnitude-trend"></a>Trend wielkości dryfu

Zobacz, czym różni się zestaw danych od docelowego zestawu danych w określonym przedziale czasu.  Im bliżej 100%, tym więcej jest tych dwóch zestawów danych.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Trend wielkości dryfu":::

### <a name="drift-magnitude-by-features"></a>Wielkość dryfu według cech

Ta sekcja zawiera szczegółowe informacje na poziomie funkcji dotyczące zmiany dystrybucji wybranej funkcji, a także inne statystyki w czasie.

Docelowy zestaw danych jest również profilowany w czasie. Statystyczna odległość między rozkładem bazowym każdej funkcji jest porównywana z docelowym zestawem danych w czasie.  Koncepcyjnie jest to podobne do wielkości dryfu danych.  Jednak ta statystyczna odległość dotyczy poszczególnych cech, a nie wszystkich funkcji. Dostępne są również opcje Minimum, Maksimum i Średnia.

W Azure Machine Learning studio na wykresie kliknij pasek, aby wyświetlić szczegóły poziomu funkcji dla tej daty. Domyślnie jest wyświetlony rozkład zestawu danych punktu odniesienia i rozkład najnowszego uruchomienia tej samej funkcji.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Wielkość dryfu według cech":::

Te metryki można również pobrać w zestawie SDK języka Python za `get_metrics()` pomocą metody w `DataDriftDetector` obiekcie .

### <a name="feature-details"></a>Szczegóły funkcji

Na koniec przewiń w dół, aby wyświetlić szczegóły poszczególnych funkcji.  Użyj listy rozwijanej powyżej wykresu, aby wybrać funkcję, a ponadto wybierz metrykę, którą chcesz wyświetlić.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Wykres cech liczbowych i porównanie":::

Metryki na wykresie zależą od typu funkcji.

* Cechy liczbowe

    | Metric | Opis |  
    | ------ | ----------- |  
    | Odległość Wassersteina | Minimalna ilość pracy w celu przekształcenia rozkładu punktu odniesienia w dystrybucję docelową. |
    | Wartość średnia | Średnia wartość funkcji. |
    | Wartość minimalna | Minimalna wartość funkcji. |
    | Wartość maksymalna | Maksymalna wartość funkcji. |

* Cechy podzielone na kategorii
    
    | Metric | Opis |  
    | ------ | ----------- |  
    | Odległość euklidesowa     |  Obliczane dla kolumn kategorii. Odległość euklidesowa jest obliczana na dwóch wektorach generowanych na podstawie rozkładu empirycznego tej samej kolumny kategorii z dwóch zestawów danych. 0 wskazuje, że rozkłady empiryczne nie różnią się.  Im więcej wartości odbiega od wartości 0, tym bardziej ta kolumna oddala się. Trendy można obserwować na podstawie wykresu szeregów czasu tej metryki i mogą być pomocne w odkrywaniu funkcji dryfowania.  |
    | Unikatowe wartości | Liczba unikatowych wartości (kardynalność) funkcji. |

Na tym wykresie wybierz pojedynczą datę, aby porównać rozkład funkcji między wartością docelową a tą datą dla wyświetlanej funkcji. W przypadku cech liczbowych pokazuje to dwa rozkłady prawdopodobieństwa.  Jeśli funkcja jest liczbowa, zostanie pokazany wykres słupkowy.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Wybierz datę do porównania z datą docelową":::

## <a name="metrics-alerts-and-events"></a>Metryki, alerty i zdarzenia

Zapytania dotyczące metryk można wiązać z zasobem [usługi Azure Application Insights](../azure-monitor/app/app-insights-overview.md) skojarzonym z obszarem roboczym uczenia maszynowego. Masz dostęp do wszystkich funkcji usługi Application Insights w tym do skonfigurowania niestandardowych reguł alertów i grup akcji w celu wyzwolenia akcji, takiej jak poczta e-mail/wiadomość SMS/wypychanie/głos lub funkcja platformy Azure. Szczegółowe informacje można znaleźć w Application Insights dokumentacji. 

Aby rozpocząć, przejdź do [Azure Portal](https://portal.azure.com) i wybierz stronę Przegląd **obszaru roboczego.**  Skojarzony zasób Application Insights znajduje się po prawej stronie:

[![Azure Portal — omówienie](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Wybierz pozycję Dzienniki (analiza) w obszarze Monitorowanie w okienku po lewej stronie:

![Omówienie usługi Application Insights](./media/how-to-monitor-datasets/ai-overview.png)

Metryki monitora zestawu danych są przechowywane jako `customMetrics` . Po skonfigurowaniu monitora zestawu danych możesz napisać i uruchomić zapytanie, aby je wyświetlić:

[![Zapytanie usługi Log Analytics](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Po zidentyfikowaniu metryk w celu skonfigurowania reguł alertów utwórz nową regułę alertu:

![Nowa reguła alertu](./media/how-to-monitor-datasets/alert-rule.png)

Możesz użyć istniejącej grupy akcji lub utworzyć nową, aby zdefiniować akcję, która ma zostać podjęta, gdy zostaną spełnione określone warunki:

![Nowa grupa akcji](./media/how-to-monitor-datasets/action-group.png)


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ograniczenia i znane problemy dotyczące monitorów dryfu danych:

* Zakres czasu podczas analizowania danych historycznych jest ograniczony do 31 interwałów ustawienia częstotliwości monitora. 
* Ograniczenie do 200 funkcji, chyba że lista funkcji nie zostanie określona (wszystkie używane funkcje).
* Rozmiar obliczeniowy musi być wystarczająco duży, aby obsłużyć dane.
* Upewnij się, że zestaw danych zawiera dane w ciągu daty rozpoczęcia i zakończenia dla danego uruchomienia monitora.
* Monitory zestawów danych będą działać tylko w przypadku zestawów danych, które zawierają co najmniej 50 wierszy.
* Kolumny lub funkcje w zestawie danych są klasyfikowane jako podzielone na kategorii lub liczbowe na podstawie warunków z poniższej tabeli. Jeśli funkcja nie spełnia tych warunków — na przykład kolumna typu ciąg z 100 unikatowymi wartościami >— funkcja zostanie porzucona z naszego algorytmu dryfu danych, ale nadal jest profilowana. 

    | Typ funkcji | Typ danych | Warunek | Ograniczenia | 
    | ------------ | --------- | --------- | ----------- |
    | Podzielone na kategorie | string, bool, int, float | Liczba unikatowych wartości w funkcji jest mniejsza niż 100 i mniejsza niż 5% liczby wierszy. | Wartość null jest traktowana jako własną kategorię. | 
    | Numeryczne | int, float | Wartości w funkcji mają typ danych liczbowych i nie spełniają warunku dla funkcji kategorii. | Funkcja jest porzucana, >15% wartości ma wartość null. | 

* Jeśli utworzono monitor dryfu danych, ale  nie widzisz danych na stronie Monitory zestawu danych w Azure Machine Learning studio, spróbuj wykonać następujące czynności.

    1. Sprawdź, czy wybrano właściwy zakres dat w górnej części strony.  
    1. Na karcie **Monitory zestawów** danych wybierz link eksperymentu, aby sprawdzić stan uruchomienia.  Ten link znajduje się po prawej stronie tabeli.
    1. Jeśli uruchomienie zakończyło się pomyślnie, sprawdź dzienniki sterowników, aby zobaczyć, ile metryk zostało wygenerowanych lub czy są jakieś komunikaty ostrzegawcze.  Znajdź dzienniki sterowników na **karcie Dane wyjściowe i dzienniki** po kliknięciu eksperymentu.

* Jeśli funkcja zestawu SDK `backfill()` nie generuje oczekiwanych danych wyjściowych, może to być spowodowane problemem z uwierzytelnianiem.  Podczas tworzenia wystąpienia obliczeniowego do przekazania do tej funkcji nie należy używać funkcji `Run.get_context().experiment.workspace.compute_targets` .  Zamiast tego należy [użyć elementu ServicePrincipalAuthentication,](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) takiego jak poniżej, aby utworzyć obliczenia, które są przekazywać do tej `backfill()` funkcji: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* Z modułu zbierającego dane modelu do konta usługi Blob Storage może upłynieć (ale zwykle mniej niż) 10 minut. W skrypcie lub notesie poczekaj 10 minut, aby upewnić się, że poniższe komórki zostaną uruchomione.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>Następne kroki

* Przejdź do strony [Azure Machine Learning studio](https://ml.azure.com) lub [notesu języka Python,](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) aby skonfigurować monitor zestawu danych.
* Zobacz, jak skonfigurować dryf danych dla [modeli wdrożonych w Azure Kubernetes Service](./how-to-enable-data-collection.md).
* Skonfiguruj monitory dryfu zestawu danych za pomocą [siatki zdarzeń](how-to-use-event-grid.md).
