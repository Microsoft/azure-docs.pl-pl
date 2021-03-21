---
title: Wykrywanie dryfowania danych w zestawach DataSet (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak skonfigurować wykrywanie dryfowania danych w usłudze Azure Learning. Tworzenie zestawów danych monitorów (wersja zapoznawcza), monitorowanie dryfowania i Konfigurowanie alertów.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml, contperf-fy21q2
ms.openlocfilehash: 5a3d16445c5a4276f07f4ed502b9830a10c4ff72
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518912"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Wykrywanie dryfowania danych (wersja zapoznawcza) w zestawach DataSet

Dowiedz się, jak monitorować dryf danych i ustawiać alerty, gdy dryf jest wysoki.  

Za pomocą monitorów zestawu danych Azure Machine Learning (wersja zapoznawcza) można:
* **Analizuj dryf danych** , aby zrozumieć, jak zmienia się w miarę upływu czasu.
* **Monitoruj dane modelu** pod kątem różnic między szkoleniem i obsługujące zestawy danych.  Zacznij od [zebrania danych modelu ze wdrożonych modeli](how-to-enable-data-collection.md).
* **Monitoruj nowe dane** pod kątem różnic między dowolnym bazowym i docelowym zestawem danych.
* **Profile funkcji w danych** , aby śledzić, jak właściwości statystyczne zmieniają się z upływem czasu.
* **Konfigurowanie alertów dotyczących dryfowania danych** w celu wczesnych ostrzeżeń dotyczących potencjalnych problemów. 
* **[Utwórz nową wersję zestawu danych] (instrukcje-to-Version-Track-DataSet)** podczas określania zbyt dużej ilości danych.

[Zestaw danych usługi Azure Machine Learning](how-to-create-register-datasets.md) jest używany do tworzenia monitora. Zestaw danych musi zawierać kolumnę sygnatur czasowych.

Metryki dotyczące dryfu danych można wyświetlić za pomocą zestawu SDK języka Python lub w programie Azure Machine Learning Studio.  Inne metryki i szczegółowe dane są dostępne za pomocą zasobu [usługi Azure Application Insights](../azure-monitor/app/app-insights-overview.md) skojarzonego z obszarem roboczym Azure Machine Learning.

> [!IMPORTANT]
> Wykrywanie dryfowania danych dla zestawów DataSet jest obecnie w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć i korzystać z monitorów zestawu danych, potrzebne są:
* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.
* [Obszar roboczy Azure Machine Learning](how-to-manage-workspace.md).
* [Zestaw Azure Machine Learning SDK dla języka Python](/python/api/overview/azure/ml/install), który obejmuje pakiet usługi Azure DataSets.
* Dane ze strukturą (tabelaryczną) z sygnaturą czasową określoną w ścieżce pliku, nazwie pliku lub kolumnie w danych.

## <a name="what-is-data-drift"></a>Co to jest dryfowanie danych?

Dryfowanie danych to jedna z najważniejszych przyczyn pogorszenia jakości modelu z upływem czasu.  W przypadku modeli uczenia maszynowego dryfowanie danych jest zmianą danych wejściowych modelu, które prowadzą do obniżenia wydajności modelu.  Dryfowanie danych monitorowania pomaga wykryć te problemy z wydajnością modelu.

Przyczyny dryfowania danych obejmują:

- Zmiany procesu nadrzędnego, takie jak "zastępowany czujnik", zmieniają jednostki miary z cali na centymetry. 
- Problemy z jakością danych, takie jak uszkodzenie czujnika, zawsze odczytu 0.
- Naturalne dryfowanie danych, takie jak przeciętne wprowadzanie temperatury przy użyciu sezonów.
- Zmień zależność między funkcjami lub covariate Shift. 

Azure Machine Learning upraszcza wykrywanie dryfu przez Obliczanie pojedynczej metryki abstrakcji złożoności porównywanych zestawów danych.  Te zestawy danych mogą mieć setki funkcji i dziesiątki tysięcy wierszy. Po wykryciu dryfu przejdziesz do szczegółów, które funkcje powodują dryf.  Następnie należy sprawdzić metryki poziomu funkcji w celu debugowania i izolowania głównej przyczyny dryfowania.

To podejście w dół ułatwia monitorowanie danych zamiast tradycyjnych technik opartych na regułach. Techniki oparte na regułach, takie jak dozwolony zakres danych lub dozwolone unikatowe wartości, mogą być czasochłonne i podatne na błędy.

W Azure Machine Learning używasz monitorów zestawu danych do wykrywania i generowania alertów dotyczących dryfowania danych.
  
### <a name="dataset-monitors"></a>Monitory zestawu danych 

Za pomocą monitora zestawu danych można:

* Wykrywaj i Ostrzegaj o odniesieniu danych w nowych danych w zestawie danych.
* Analizuj dane historyczne dotyczące dryfowania.
* Profilowanie nowych danych w czasie.

Algorytm dryfowania danych zawiera ogólną miarę zmian danych i wskazanie, które funkcje są odpowiedzialne za dalsze badanie. Monitory zestawu danych tworzą wiele innych metryk przez profilowanie nowych danych w `timeseries` zestawie danych. 

Niestandardowe alerty można skonfigurować na wszystkich metrykach wygenerowanych przez Monitor za pomocą [usługi Azure Application Insights](../azure-monitor/app/app-insights-overview.md). Za pomocą monitorów zestawu danych można szybko przechwycić problemy z danymi i skrócić czas debugowania problemu, identyfikując możliwe przyczyny.  

Koncepcyjnie istnieją trzy podstawowe scenariusze konfigurowania monitorów zestawów danych w Azure Machine Learning.

Scenariusz | Opis
---|---
Monitorowanie obsługi danych przez model na potrzeby dryfowania danych szkoleniowych | Wyniki z tego scenariusza mogą być interpretowane jako monitorowanie serwera proxy dla dokładności modelu, ponieważ obniżanie poziomu dokładności modelu w przypadku, gdy dane dotyczące uczenia są uznawane za.
Monitoruj zestaw danych szeregów czasowych dla dryfu z poprzedniego okresu. | Ten scenariusz jest bardziej ogólny i może służyć do monitorowania zestawów danych będących w modelu nadrzędnym lub podrzędnym kompilowania modeli.  Docelowy zestaw danych musi mieć kolumnę sygnatur czasowych. Zestaw danych bazowych może być dowolnym tabelarycznym zestawem danych, który zawiera funkcje wspólne z docelowym zestawem danych.
Wykonaj analizę danych z przeszłości. | Ten scenariusz może służyć do zrozumienia danych historycznych i podejmowania decyzji dotyczących ustawień dla monitorów zestawu danych.

Monitory zestawu danych są zależne od następujących usług platformy Azure.

|Usługa platformy Azure  |Opis  |
|---------|---------|
| *Zestawu* | Funkcja dryf używa Machine Learning zestawów danych, aby pobierać dane szkoleniowe i porównywać dane w celu szkolenia modeli.  Generowanie profilu danych jest używane do generowania niektórych raportowanych metryk, takich jak minimalna, maksymalna, różne wartości, liczba unikatowych wartości. |
| *Potok i obliczenia w usłudze Azure* | Zadanie obliczania dryfu jest hostowane w potoku Azure.  Zadanie jest wyzwalane na żądanie lub według harmonogramu do uruchomienia w ramach obliczeń skonfigurowanych na czas tworzenia monitora dryfu.
| *Application Insights*| Dryf emituje metryki do Application Insights należące do obszaru roboczego uczenia maszynowego.
| *Azure Blob Storage*| Dryf emituje metryki w formacie JSON do magazynu obiektów blob platformy Azure.

### <a name="baseline-and-target-datasets"></a>Bazowe i docelowe zestawy danych 

Możesz monitorować [zestawy danych usługi Azure Machine Learning](how-to-create-register-datasets.md) pod kątem dryfowania. Podczas tworzenia monitora zestawu danych należy odwołać się do:
* Bazowy zestaw danych — zazwyczaj zestaw danych szkoleniowych dla modelu.
* Docelowy zestaw danych — zwykle dane wejściowe modelu — są porównywane z upływem czasu do zestawu danych bazowych. To porównanie oznacza, że docelowy zestaw danych musi mieć określoną kolumnę sygnatur czasowych.

Monitor będzie porównywać bazowe i docelowe zestawy danych.

## <a name="create-target-dataset"></a>Utwórz docelowy zestaw danych

Docelowy zestaw danych wymaga `timeseries` zestawu cech dla niego, określając kolumnę sygnatury czasowej w kolumnie dane lub kolumnę wirtualną pochodną wzorca ścieżki plików. Utwórz zestaw danych z sygnaturą czasową za pomocą [zestawu SDK języka Python](#sdk-dataset) lub [Azure Machine Learning Studio](#studio-dataset). Kolumna reprezentująca znacznik czasu musi być określona, aby można było dodać `timeseries` cechy do zestawu danych. Jeśli dane są partycjonowane w strukturze folderów wraz z informacjami o czasie, takimi jak "{RRRR/MM/DD}", Utwórz kolumnę wirtualną za pomocą ustawienia wzorca ścieżki i ustaw ją jako "sygnatura czasowa partycji", aby zwiększyć ważność funkcji szeregów czasowych.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

[`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)Metoda klasy [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) definiuje kolumnę sygnatury czasowej dla zestawu danych.

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
> Pełny przykład użycia `timeseries` cech zestawów danych można znaleźć w [przykładowym notesie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) lub [dokumentacji zestawu SDK zestawów danych](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

# <a name="studio"></a>[Studio](#tab/azure-studio)

<a name="studio-dataset"></a>

Jeśli tworzysz zestaw danych przy użyciu Azure Machine Learning Studio, upewnij się, że ścieżka do danych zawiera informacje o znaczniku czasu, Uwzględnij wszystkie podfoldery z danymi i ustaw format partycji.

W poniższym przykładzie jest podejmowane wszystkie dane w podfolderze *NoaaIsdFlorida/2019* , a format partycji Określa rok, miesiąc i dzień.

[![Format partycji](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

W ustawieniach **schematu** należy określić kolumnę sygnatury czasowej z wirtualnej lub rzeczywistej kolumny w określonym zestawie danych:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Ustawianie sygnatury czasowej":::

Jeśli dane są partycjonowane według daty, tak jak tutaj, można również określić partition_timestamp.  Umożliwia to wydajniejsze przetwarzanie dat.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Sygnatura czasowa partycji":::

---

## <a name="create-dataset-monitor"></a>Utwórz monitor zestawu danych

Utwórz monitor zestawu danych służący do wykrywania i generowania alertów dotyczących dryfowania danych w nowym zestawie danych.  Użyj [zestawu SDK języka Python](#sdk-monitor) lub [Azure Machine Learning Studio](#studio-monitor).

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> Aby uzyskać szczegółowe informacje, zobacz [dokumentację referencyjną języka Python SDK dotyczącą dryfowania danych](/python/api/azureml-datadrift/azureml.datadrift) . 

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

> [!TIP]
> Pełny przykład konfigurowania `timeseries` zestawu danych i wykrywania dryfowania danych można znaleźć w naszym [przykładowym notesie](https://aka.ms/datadrift-notebook).


# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="studio-monitor"></a>

1. Przejdź do [strony głównej Studio](https://ml.azure.com).
1. Wybierz kartę **zestawy danych** po lewej stronie. 
1. Wybierz **Monitory zestawu danych**.
   ![Lista monitorów](./media/how-to-monitor-datasets/monitor-list.png)

1. Kliknij przycisk **+ Utwórz monitor** i Kontynuuj pracę kreatora, klikając przycisk **dalej**.  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Kreator tworzenia monitora":::

* **Wybierz docelowy zestaw danych**.  Docelowy zestaw danych jest tabelarycznym zestawem danych z określoną kolumną sygnatur czasowych, która zostanie przeanalizowana pod kątem dryfowania danych. Docelowy zestaw danych musi mieć wspólne funkcje z bazowym zestawem danych, który powinien być `timeseries` zestawem danych, do którego dodawane są nowe dane. Dane historyczne w docelowym zestawie danych mogą być analizowane lub można monitorować nowe dane.

* **Wybierz zestaw danych punktu odniesienia.**  Wybierz tabelaryczny zestaw danych, który ma być używany jako linia bazowa dla porównania docelowego zestawu danych w czasie.  Zestaw danych bazowych musi mieć wspólne funkcje z docelowym zestawem danych.  Wybierz zakres czasu, aby użyć wycinka docelowego zestawu danych, lub określ oddzielny zestaw danych, który ma być używany jako linia bazowa.

* **Ustawienia monitorowania**.  Te ustawienia dotyczą potoku monitora harmonogramu zestawu danych, który zostanie utworzony. 

    | Ustawienie | Opis | Porady | Modyfikowalny | 
    | ------- | ----------- | ---- | ------- |
    | Nazwa | Nazwa monitora zestawu danych. | | Nie |
    | Funkcje | Lista funkcji, które będą analizowane pod kątem dryfowania danych w czasie. | Ustaw na funkcje wyjściowe modelu, aby zmierzyć dryf koncepcji. Nie dołączaj funkcji, które naturalnie dryfuje w czasie (miesiąc, rok, indeks itp.). Po dostosowaniu listy funkcji można wypełniać i śledzić istniejący monitor dryfowania danych. | Tak | 
    | Docelowy zasób obliczeniowy | Azure Machine Learning miejsce docelowe obliczeń, aby uruchomić zadania monitorowania zestawu danych. | | Tak | 
    | Włącz | Włączanie lub wyłączanie harmonogramu dla potoku monitora zestawu danych | Wyłącz harmonogram, aby analizować dane historyczne przy użyciu ustawienia wypełniania. Można ją włączyć po utworzeniu monitora zestawu danych. | Tak | 
    | Częstotliwość | Częstotliwość, która zostanie użyta do zaplanowania zadania potoku i przeanalizowania danych historycznych w przypadku uruchamiania wypełniania. Dostępne opcje to codziennie, co tydzień lub co miesiąc. | Każde uruchomienie porównuje dane w docelowym zestawie danych zgodnie z częstotliwością: <li>Codziennie: Porównaj najnowszy pełny dzień w docelowym zestawie danych z punktem odniesienia <li>Tydzień: porównanie ostatniego pełnego tygodnia (poniedziałek-niedziela) w docelowym zestawie danych z punktem odniesienia <li>Miesięcznie: porównanie ostatniego pełnego miesiąca w docelowym zestawie danych z punktem odniesienia | Nie | 
    | Opóźnienie | Czas, w godzinach, pobiera dane do zestawu danych. Na przykład jeśli dane mają być dostarczone przez trzy dni w bazie danych bazy danych SQL, należy ustawić opóźnienie na 72. | Nie można zmienić po utworzeniu monitora zestawu danych | Nie | 
    | Adresy e-mail | Adresy e-mail dla alertów na podstawie naruszenia progu procentu dryfu danych. | Wiadomości e-mail są wysyłane za poorednictwem Azure Monitor. | Tak | 
    | Próg | Próg procentowy dryfu danych dla alertów e-mail. | Więcej alertów i zdarzeń można ustawić dla wielu innych metryk w skojarzonym zasobie Application Insightsm obszaru roboczego. | Tak |

Po zakończeniu pracy kreatora, wynikający z monitora zestawu danych pojawi się na liście. Wybierz go, aby przejść do strony szczegółów tego monitora.

---

## <a name="understand-data-drift-results"></a>Zrozumienie wyników dryfowania danych

W tej sekcji przedstawiono wyniki monitorowania zestawu danych, które znajdują **się na**  /  stronie **Monitory zestawu danych DataSet** w programie Azure Studio.  Możesz zaktualizować ustawienia, a także przeanalizować istniejące dane dla określonego okresu na tej stronie.  

Zacznij od wglądu w dane najwyższego poziomu do wielkości dryfu danych oraz wyróżniania funkcji do dalszej analizy.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Przegląd dryfu":::


| Metric | Opis | 
| ------ | ----------- | 
| Wielkość dryfu danych | Procent dryfu między bazowym i docelowym zestawem danych w czasie. W zakresie od 0 do 100, 0 wskazuje identyczne zestawy danych i 100 wskazuje, Azure Machine Learning model dryfowania danych może całkowicie odróżnić te dwa zestawy. Oczekiwano szumu w precyzyjnym procencie mierzonym z powodu technik uczenia maszynowego używanego do generowania tego rozmiaru. | 
| Najważniejsze funkcje przedryfania | Pokazuje funkcje z zestawu danych, które zostały odpięte i w związku z tym przyczyniają się do metryki wartości dryf. Ze względu na covariate Shift bazowa dystrybucja funkcji nie musi być konieczna do zmiany w celu zapewnienia stosunkowo dużej ważności funkcji. |
| Próg | Rozmiar dryfu danych poza ustawionym progiem spowoduje wyzwolenie alertów. Można to skonfigurować w ustawieniach monitora. | 

### <a name="drift-magnitude-trend"></a>Trend wielkości dryfu

Zobacz, jak zestaw danych różni się od docelowego zestawu danych w określonym przedziale czasu.  Bliżej 100%, tym większa jest różnica między dwoma zestawami danych.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Trend wielkości dryfu":::

### <a name="drift-magnitude-by-features"></a>Wielkość dryfu według funkcji

Ta sekcja zawiera szczegółowe informacje na temat zmian w dystrybucji wybranej funkcji, a także inne dane statystyczne w miarę upływu czasu.

Docelowy zestaw danych jest również profilowany w czasie. Odległość statystyczna między rozkładem linii bazowej każdej funkcji jest porównywana z docelowym zestawem danych w czasie.  Koncepcyjnie jest to podobne do wielkości dryfu danych.  Jednak ta odległość statystyczna dotyczy pojedynczej funkcji, a nie wszystkich funkcji. Dostępne są również wartości minimalna, maksymalna i średnia.

W Azure Machine Learning Studio kliknij pasek na wykresie, aby wyświetlić szczegóły poziomu funkcji dla tej daty. Domyślnie zostanie wyświetlona dystrybucja zestawu danych punktu odniesienia i Najnowsza dystrybucja przebiegu tej samej funkcji.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Wielkość dryfu według funkcji":::

Te metryki można także pobrać w zestawie SDK języka Python za pomocą `get_metrics()` metody dla `DataDriftDetector` obiektu.

### <a name="feature-details"></a>Szczegóły funkcji

Na koniec przewiń w dół, aby wyświetlić szczegółowe informacje dotyczące poszczególnych funkcji.  Użyj listy rozwijanej powyżej wykresu, aby wybrać funkcję, a następnie wybierz metrykę, którą chcesz wyświetlić.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Wykres funkcji liczbowej i porównanie":::

Metryki na wykresie zależą od typu funkcji.

* Funkcje liczbowe

    | Metric | Opis |  
    | ------ | ----------- |  
    | Odległość Wasserstein | Minimalna ilość pracy, aby przekształcić dystrybucję bazową w dystrybucję docelową. |
    | Wartość średnia | Średnia wartość funkcji. |
    | Wartość minimalna | Minimalna wartość funkcji. |
    | Wartość maksymalna | Maksymalna wartość funkcji. |

* Funkcje kategorii
    
    | Metric | Opis |  
    | ------ | ----------- |  
    | Odległość euclidian     |  Obliczone dla kolumn kategorii. Odległość Euclidean jest obliczana na dwóch wektorach wygenerowanych na podstawie rozkładu empirycznego tej samej kolumny kategorii z dwóch zestawów danych. wartość 0 oznacza, że nie ma różnicy w rozkładach empirycznych.  Im więcej odbędzie się od 0, tym większa kolumna jest przechylać. Trendy mogą być zauważalne z wykresu szeregowego czasu tej metryki i mogą być pomocne w odwróceniu funkcji dryfowania.  |
    | Unikatowe wartości | Liczba unikatowych wartości (Kardynalność) funkcji. |

Na tym wykresie wybierz pojedynczą datę, aby porównać dystrybucję funkcji między obiektem docelowym a tą datą dla wyświetlanej funkcji. W przypadku funkcji liczbowych pokazuje dwa rozkłady prawdopodobieństwa.  Jeśli funkcja jest liczbowa, zostanie wyświetlony wykres słupkowy.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Wybierz datę do porównania z elementem docelowym":::

## <a name="metrics-alerts-and-events"></a>Metryki, alerty i zdarzenia

Metryki mogą być zapytania w ramach zasobu [usługi Azure Application Insights](../azure-monitor/app/app-insights-overview.md) skojarzonego z obszarem roboczym uczenia maszynowego. Masz dostęp do wszystkich funkcji Application Insights, w tym konfiguracji niestandardowych reguł alertów i grup akcji, aby wyzwolić akcję, taką jak wiadomość E-mail/SMS/wypychanie/głos lub funkcja platformy Azure. Aby uzyskać szczegółowe informacje, zobacz kompletną dokumentację Application Insights. 

Aby rozpocząć, przejdź do [Azure Portal](https://portal.azure.com) i wybierz stronę **omówienia** obszaru roboczego.  Skojarzony zasób Application Insights jest po prawej stronie:

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

Ograniczenia i znane problemy dotyczące monitorów dryfowania danych:

* Zakres czasu podczas analizowania danych historycznych jest ograniczony do 31 interwałów ustawienia częstotliwości monitora. 
* Ograniczenie funkcji 200, chyba że lista funkcji nie zostanie określona (wszystkie używane funkcje).
* Rozmiar obliczeń musi być wystarczająco duży, aby można było obsłużyć dane.
* Upewnij się, że zestaw danych zawiera dane w dniu rozpoczęcia i zakończenia danego monitora.
* Monitory zestawu danych będą działały tylko w zestawach, które zawierają 50 wierszy lub więcej.
* Kolumny lub funkcje w zestawie danych są klasyfikowane jako kategorii lub liczbowe w zależności od warunków w poniższej tabeli. Jeśli funkcja nie spełnia tych warunków — na przykład kolumna typu ciąg z >100 unikatowymi wartościami — funkcja zostanie porzucona z algorytmu dryfowania danych, ale jest nadal profilowana. 

    | Typ funkcji | Typ danych | Warunek | Ograniczenia | 
    | ------------ | --------- | --------- | ----------- |
    | Podzielone na kategorie | String, bool, int, float | Liczba unikatowych wartości w funkcji jest mniejsza niż 100 i mniejsza niż 5% liczby wierszy. | Wartość null jest traktowana jako jej własna Kategoria. | 
    | Porządkow | int, float | Wartości w funkcji mają typ danych liczbowych i nie spełniają warunku funkcji kategorii. | Funkcja została porzucona, jeśli >15% wartości ma wartość null. | 

* Po utworzeniu monitora dryfowania danych, ale nie na stronie **Monitory zestawu danych** w programie Azure Machine Learning Studio, spróbuj wykonać poniższe czynności.

    1. Sprawdź, czy w górnej części strony wybrano prawy zakres dat.  
    1. Na karcie **Monitory zestawu danych** wybierz łącze eksperyment, aby sprawdzić stan uruchomienia.  Ten link znajduje się po prawej stronie tabeli.
    1. Jeśli uruchomienie zakończyło się pomyślnie, Sprawdź dzienniki sterowników, aby zobaczyć, ile metryk zostało wygenerowanych, lub jeśli istnieją komunikaty ostrzegawcze.  Na karcie **dane wyjściowe i dzienniki** Znajdź dzienniki sterowników po kliknięciu eksperymentu.

* Jeśli funkcja SDK nie `backfill()` generuje oczekiwanych danych wyjściowych, może to być spowodowane problemem z uwierzytelnianiem.  Podczas tworzenia obliczeń do przekazania do tej funkcji nie należy używać `Run.get_context().experiment.workspace.compute_targets` .  Zamiast tego należy użyć [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) , takich jak następujące, aby utworzyć obliczenia, które są przekazywane do tej `backfill()` funkcji: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* Z modułu zbierającego dane modelu może upłynąć do (ale zazwyczaj mniej niż) 10 minut na dostarczenie danych na koncie usługi BLOB Storage. W skrypcie lub w notesie odczekaj 10 minut, aby upewnić się, że poniższe komórki zostaną uruchomione.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>Następne kroki

* Przejdź do programu [Azure Machine Learning Studio](https://ml.azure.com) lub [notesu Python](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) , aby skonfigurować Monitor zestawu danych.
* Zobacz jak skonfigurować dryfowanie danych dla [modeli wdrożonych w usłudze Azure Kubernetes Service](./how-to-enable-data-collection.md).
* Skonfiguruj monitory przedryfania zestawu danych za pomocą [siatki zdarzeń](how-to-use-event-grid.md).