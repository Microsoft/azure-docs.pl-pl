---
title: Azure Data Lake Storage Gen1 — dostrajanie wydajności
description: Dowiedz się, jak korzystać z całej dostępnej przepływności w Azure Data Lake Storage Gen1 jest ważne, aby uzyskać najlepszą wydajność, wykonując jednocześnie wiele operacji odczytu i zapisu.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: d18440b27d9429a2638a58be40e1ec583b9a85ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88190242"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Dostrajanie Azure Data Lake Storage Gen1 na potrzeby wydajności

Data Lake Storage Gen1 obsługuje wysoką przepływność na potrzeby analizy i przenoszenia danych intensywnie korzystających z operacji we/wy. W Data Lake Storage Gen1 przy użyciu całej dostępnej przepływności — ilość danych, które można odczytać lub zapisać na sekundę — jest ważna, aby uzyskać najlepszą wydajność. Można to osiągnąć, wykonując jednocześnie wiele operacji odczytu i zapisu.

![Data Lake Storage Gen1 wydajność](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 można skalować w celu zapewnienia odpowiedniej przepływności dla całego scenariusza analizy. Domyślnie konto Data Lake Storage Gen1 zapewnia automatycznie wystarczającą przepływność, aby sprostać wymaganiom szerokiej kategorii przypadków użycia. W przypadkach, w których klienci mają domyślnie uruchomiony limit, można skonfigurować konto Data Lake Storage Gen1, aby zapewnić większą przepływność, kontaktując się z pomocą techniczną firmy Microsoft.

## <a name="data-ingestion"></a>Wprowadzanie danych

W przypadku pozyskiwania danych z systemu źródłowego do Data Lake Storage Gen1 należy pamiętać, że sprzęt źródłowy, sprzęt sieciowy i połączenie sieciowe, które mają Data Lake Storage Gen1 może być wąskim gardłem.

![Data Lake Storage Gen1 wydajność](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Ważne jest, aby upewnić się, że te czynniki nie wpływają na przeniesienie danych.

### <a name="source-hardware"></a>Sprzęt źródłowy

Niezależnie od tego, czy używasz lokalnych maszyn i maszyn wirtualnych na platformie Azure, wybierz odpowiedni sprzęt. W przypadku sprzętu dysku źródłowego Preferuj dysków SSD HDD i wybieraj sprzęt dyskowy z szybszymi wrzecionami. W przypadku sprzętowego sprzętu sieciowego Użyj najszybszych kart sieciowych. Na platformie Azure zalecamy korzystanie z maszyn wirtualnych usługi Azure D14, które mają odpowiednie zaawansowane sprzęty dyskowe i sieciowe.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Łączność sieciowa z Data Lake Storage Gen1

Połączenie sieciowe między danymi źródłowymi a Data Lake Storage Gen1 może czasami stanowić wąskie gardło. Jeśli dane źródłowe są lokalne, należy rozważyć użycie dedykowanego linku z [usługą Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) . Jeśli dane źródłowe są na platformie Azure, wydajność będzie najlepsza, gdy dane będą znajdować się w tym samym regionie platformy Azure co konto Data Lake Storage Gen1.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurowanie narzędzi pozyskiwania danych w celu uzyskania maksymalnej przetwarzanie równoległe

Po rozwiązaniu problemów ze źródłem zasobów sprzętowych i połączeń sieciowych można skonfigurować narzędzia pozyskiwania. Poniższa tabela zawiera podsumowanie najważniejszych ustawień dla kilku popularnych narzędzi do pozyskiwania i zawiera szczegółowe artykuły dotyczące dostrajania wydajności. Aby dowiedzieć się więcej na temat tego, którego narzędzia użyć w danym scenariuszu, odwiedź ten [artykuł](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios).

| Narzędzie          | Ustawienia | Więcej szczegółów                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, ConcurrentFileCount | [Powiązań](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy    | Jednostki Azure Data Lake Analytics | [Powiązań](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| Pomocą distcp            | -m (mapowanie) | [Powiązań](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies | [Powiązań](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS. Azure. Block. size,-m (Maper) | [Powiązań](https://docs.microsoft.com/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)        |

## <a name="structure-your-data-set"></a>Struktura zestawu danych

Gdy dane są przechowywane w Data Lake Storage Gen1, rozmiar pliku, liczba plików i struktura folderów mają wpływ na wydajność. W poniższej sekcji opisano najlepsze rozwiązania w tych obszarach.

### <a name="file-size"></a>Rozmiar pliku

Zwykle aparaty analityczne, takie jak HDInsight i Azure Data Lake Analytics, mają narzuty za pliki. Jeśli dane są przechowywane jako wiele małych plików, może to negatywnie wpłynąć na wydajność.

Ogólnie rzecz biorąc, Organizuj dane w pliki o większym rozmiarze, aby zapewnić lepszą wydajność. Jako zasada elementu kciuka Organizuj zestawy danych w plikach z 256 MB lub większych. W niektórych przypadkach, takich jak obrazy i dane binarne, nie można przetwarzać ich równolegle. W takich przypadkach zaleca się przechowywanie poszczególnych plików w obszarze 2 GB.

Czasami potoki danych mają ograniczoną kontrolę nad danymi pierwotnymi, które mają wiele małych plików. Zaleca się, aby proces "gotowania" generował większe pliki do użycia w aplikacjach podrzędnych.

### <a name="organize-time-series-data-in-folders"></a>Organizuj dane szeregów czasowych w folderach

W przypadku obciążeń Hive i ADLA, oczyszczanie partycji danych szeregów czasowych może pomóc niektórym kwerendom odczytywać tylko podzbiór danych, co zwiększa wydajność.

Te potoki, które pobierają dane szeregów czasowych, często umieszczają pliki z strukturalną nazewnictwem plików i folderów. Poniżej przedstawiono typowy przykład, który jest widoczny dla danych, które są uporządkowane według daty: *\dataset\yyyy\mm\dd\ datafile_YYYY_MM_DD. tsv*.

Zwróć uwagę, że informacje o dacie i godzinie są wyświetlane zarówno jako foldery, jak i w nazwie pliku.

W przypadku daty i godziny następujący wzorzec jest typowy: *\dataset\yyyy\mm\dd\hh\mm\ datafile_YYYY_MM_DD_HH_mm. tsv*.

Po ponownym wyborze z folderu i organizacji plików należy zoptymalizować dla większych rozmiarów plików i rozsądnej liczby plików w poszczególnych folderach.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optymalizowanie zadań intensywnie korzystających z operacji we/wy na obciążeniach Hadoop i Spark w usłudze HDInsight

Zadania należą do jednej z następujących trzech kategorii:

* **Intensywność procesora CPU.** Te zadania mają długotrwałe czasy obliczeń z minimalnymi liczbami operacji we/wy. Przykłady obejmują zadania uczenia maszynowego i przetwarzania języka naturalnego.
* **Duże ilości pamięci.** Te zadania korzystają z dużej ilości pamięci. Przykłady obejmują zadania analizy PageRank i w czasie rzeczywistym.
* **Intensywna operacja we/wy.** Te zadania spędzają większość czasu na wykonywanie operacji we/wy. Typowym przykładem jest zadanie kopiowania, które wykonuje tylko operacje odczytu i zapisu. Inne przykłady obejmują zadania przygotowywania danych, które odczytują wiele danych, wykonują transformację danych, a następnie zapisują dane z powrotem do magazynu.

Poniższe wskazówki dotyczą wyłącznie zadań intensywnie korzystających z operacji we/wy.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Ogólne zagadnienia dotyczące klastra usługi HDInsight

* **Wersje usługi HDInsight.** Aby uzyskać najlepszą wydajność, użyj najnowszej wersji usługi HDInsight.
* **Regionach.** Umieść konto Data Lake Storage Gen1 w tym samym regionie, w którym znajduje się klaster usługi HDInsight.

Klaster HDInsight An składa się z dwóch węzłów głównych i niektórych węzłów procesu roboczego. Każdy węzeł procesu roboczego zapewnia określoną liczbę rdzeni i pamięci, która jest określana przez typ maszyny wirtualnej. Podczas uruchamiania zadania, PRZĘDZa to negocjowanie zasobów, który przydziela dostępną pamięć i rdzenie do tworzenia kontenerów. Każdy kontener uruchamia zadania, które są konieczne do ukończenia zadania. Kontenery są uruchamiane równolegle w celu szybkiego przetwarzania zadań. W związku z tym wydajność można zwiększyć, uruchamiając jako możliwy do tego wiele kontenerów równoległych.

W klastrze usługi HDInsight znajdują się trzy warstwy, które można dostrajać, aby zwiększyć liczbę kontenerów i korzystać z całej dostępnej przepływności.

* **Warstwa fizyczna**
* **Warstwa PRZĘDZy**
* **Warstwa obciążenia**

### <a name="physical-layer"></a>Warstwa fizyczna

**Uruchom klaster z większą liczbą węzłów i/lub maszynami wirtualnymi o większej wielkości.** Większy klaster umożliwi Ci uruchamianie więcej kontenerów PRZĘDZy, jak pokazano na poniższej ilustracji.

![Data Lake Storage Gen1 wydajność](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Używaj maszyn wirtualnych z większą przepustowością sieci.** Przepustowość sieci może stanowić wąskie gardło, jeśli przepustowość sieci jest mniejsza niż Data Lake Storage Gen1 przepływność. Różne maszyny wirtualne będą miały różne rozmiary przepustowości sieci. Wybierz typ maszyny wirtualnej, który ma największą możliwą przepustowość sieci.

### <a name="yarn-layer"></a>Warstwa PRZĘDZy

**Używaj mniejszych kontenerów PRZĘDZy.** Zmniejsz rozmiar każdego kontenera PRZĘDZy, aby utworzyć więcej kontenerów z taką samą ilością zasobów.

![Data Lake Storage Gen1 wydajność](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

W zależności od obciążenia będzie zawsze wymagany minimalny rozmiar kontenera PRZĘDZy. W przypadku wybrania zbyt małego kontenera zadania będą działać w przypadku problemów z brakiem pamięci. Zazwyczaj kontenery PRZĘDZy nie powinny być mniejsze niż 1 GB. Często widzisz kontenery PRZĘDZy 3 GB. W przypadku niektórych obciążeń może zajść potrzeba większego kontenera PRZĘDZy.

**Zwiększ liczbę rdzeni na kontener PRZĘDZy.** Zwiększ liczbę rdzeni przypisanych do każdego kontenera, aby zwiększyć liczbę zadań równoległych, które są uruchamiane w każdym kontenerze. Działa to w przypadku aplikacji takich jak Spark, które uruchamiają wiele zadań na kontener. W przypadku aplikacji, takich jak Hive, w których działa pojedynczy wątek w każdym kontenerze, lepiej jest umieścić więcej kontenerów, a nie więcej rdzeni na kontener.

### <a name="workload-layer"></a>Warstwa obciążenia

**Użyj wszystkich dostępnych kontenerów.** Ustaw liczbę zadań, które mają być równe lub większe niż liczba dostępnych kontenerów, tak aby wszystkie zasoby były używane.

![Data Lake Storage Gen1 wydajność](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Zadania zakończone niepowodzeniem są kosztowne.** Jeśli każde zadanie zawiera dużą ilość danych do przetworzenia, wówczas niepowodzenie zadania skutkuje kosztowną ponowną próbą. W związku z tym lepiej jest utworzyć więcej zadań, z których każdy przetwarza niewielką ilość danych.

Oprócz ogólnych wytycznych, każda aplikacja ma inne parametry dostępne do dostrajania dla tej konkretnej aplikacji. W poniższej tabeli przedstawiono niektóre parametry i linki umożliwiające rozpoczęcie pracy z dostrajaniem wydajności dla każdej aplikacji.

| Obciążenie               | Parametr do ustawiania zadań                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Platforma Spark w usłudze HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>NUM-wykonawcy</li><li>Wykonawca pamięci</li><li>Program wykonujący rdzenie</li></ul> |
| [Usługa Hive w usłudze HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>Hive. tez. Container. size</li></ul>         |
| [MapReduce w usłudze HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>MapReduce. map. Memory</li><li>MapReduce. job. Maps</li><li>MapReduce. Zmniejsz ilość pamięci</li><li>MapReduce. job. Zmniejsz</li></ul> |
| [Usługa Storm w usłudze HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Liczba procesów roboczych</li><li>Liczba wystąpień programu wykonującego elementu Spout</li><li>Liczba wystąpień programu wykonującego Piorun </li><li>Liczba zadań elementu Spout</li><li>Liczba zadań obiektu Piorun</li></ul>|

## <a name="see-also"></a>Zobacz też

* [Omówienie Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Rozpoczynanie pracy z usługą Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
