---
title: Optymalizacja Azure Data Lake Storage Gen2 pod kątem wydajności | Microsoft Docs
description: Dowiedz się, jak zoptymalizować Azure Data Lake Storage Gen2 pod kątem wydajności. Pozyskiwanie danych, struktura zestawu danych i nie tylko.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: f0f64d910d03e42008c5fe6fef28a5b9c0917abd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97814469"
---
# <a name="optimize-azure-data-lake-storage-gen2-for-performance"></a>Optymalizowanie Azure Data Lake Storage Gen2 na potrzeby wydajności

Azure Data Lake Storage Gen2 obsługuje wysoką przepływność na potrzeby analizy i przenoszenia danych intensywnie korzystających z operacji we/wy.  W Data Lake Storage Gen2 przy użyciu całej dostępnej przepływności — ilość danych, które można odczytać lub zapisać na sekundę — jest ważna, aby uzyskać najlepszą wydajność.  Można to osiągnąć, wykonując jednocześnie wiele operacji odczytu i zapisu.

![Data Lake Storage Gen2 wydajność](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 można skalować w celu zapewnienia odpowiedniej przepływności dla wszystkich scenariuszy analizy. Domyślnie konto Data Lake Storage Gen2 zapewnia wystarczającą przepływność w konfiguracji domyślnej, aby zaspokoić potrzeby szerokiej kategorii przypadków użycia. W przypadkach, w których klienci mają domyślnie uruchomiony limit, konto Data Lake Storage Gen2 można skonfigurować w celu zapewnienia większej przepływności, kontaktując się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/faq/).

## <a name="data-ingestion"></a>Wprowadzanie danych

W przypadku pozyskiwania danych z systemu źródłowego do Data Lake Storage Gen2 należy pamiętać, że sprzęt źródłowy, źródłowy sprzęt sieciowy lub połączenie sieciowe, które Data Lake Storage Gen2 może być wąskim gardłem.  

![Diagram przedstawiający czynniki, które należy wziąć pod uwagę podczas pozyskiwania danych z systemu źródłowego do Data Lake Storage Gen2.](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Ważne jest, aby upewnić się, że te czynniki nie wpływają na przeniesienie danych.

### <a name="source-hardware"></a>Sprzęt źródłowy

Niezależnie od tego, czy używasz lokalnych maszyn i maszyn wirtualnych na platformie Azure, należy starannie wybrać odpowiedni sprzęt. W przypadku sprzętu dysku źródłowego Preferuj dysków SSD HDD i wybieraj sprzęt dyskowy z szybszymi wrzecionami. W przypadku sprzętowego sprzętu sieciowego Użyj najszybszych kart sieciowych.  Na platformie Azure zalecamy korzystanie z maszyn wirtualnych usługi Azure D14, które mają odpowiednio zaawansowane sprzęty dyskowe i sieciowe.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Łączność sieciowa z Data Lake Storage Gen2

Połączenie sieciowe między danymi źródłowymi a Data Lake Storage Gen2 może czasami stanowić wąskie gardło. Jeśli dane źródłowe są lokalne, należy rozważyć użycie dedykowanego linku z [usługą Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/). Jeśli dane źródłowe są na platformie Azure, wydajność będzie najlepsza, gdy dane będą znajdować się w tym samym regionie platformy Azure co konto Data Lake Storage Gen2.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurowanie narzędzi pozyskiwania danych w celu uzyskania maksymalnej przetwarzanie równoległe

Po rozwiązaniu problemów ze źródłem zasobów sprzętowych i połączeń sieciowych można przystąpić do konfigurowania narzędzi do pozyskiwania. Poniższa tabela zawiera podsumowanie najważniejszych ustawień dla kilku popularnych narzędzi do pozyskiwania i zawiera szczegółowe artykuły dotyczące dostrajania wydajności.  Aby dowiedzieć się więcej na temat tego, którego narzędzia użyć w danym scenariuszu, odwiedź ten [artykuł](data-lake-storage-data-scenarios.md).

| Narzędzie               | Ustawienia | Więcej szczegółów                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| Pomocą distcp            | -m (mapowanie)   | [Link](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Link](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | FS. Azure. Block. size,-m (Maper)    |   [Link](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)        |

## <a name="structure-your-data-set"></a>Struktura zestawu danych

Gdy dane są przechowywane w Data Lake Storage Gen2, rozmiar pliku, liczba plików i struktura folderów mają wpływ na wydajność.  W poniższej sekcji opisano najlepsze rozwiązania w tych obszarach.  

### <a name="file-size"></a>Rozmiar pliku

Zwykle aparaty analityczne, takie jak HDInsight i Azure Data Lake Analytics, mają narzuty za pliki. Jeśli dane są przechowywane jako wiele małych plików, może to negatywnie wpłynąć na wydajność. Ogólnie rzecz biorąc, Organizuj dane w pliki o większym rozmiarze, aby uzyskać lepszą wydajność (od 256 do 100 GB rozmiaru). Niektóre aparaty i aplikacje mogą mieć problemy z wydajnym przetwarzaniem plików o rozmiarze większym niż 100 GB.

Czasami potoki danych mają ograniczoną kontrolę nad danymi nieprzetworzonymi, które zawierają wiele małych plików. Ogólnie rzecz biorąc, firma Microsoft zaleca, aby system miał pewien proces agregowania małych plików w celu ich użycia w aplikacjach podrzędnych.

### <a name="organizing-time-series-data-in-folders"></a>Organizowanie danych szeregów czasowych w folderach

W przypadku obciążeń platformy Hive oczyszczanie partycji danych szeregów czasowych może pomóc niektórym kwerendom odczytywać tylko podzbiór danych, które zwiększają wydajność.    

Te potoki, które pobierają dane szeregów czasowych, często umieszczają pliki z bardzo strukturalną nazewnictwem plików i folderów. Poniżej znajduje się bardzo typowy przykład dla danych, które są uporządkowane według daty:

*\DataSet\YYYY\MM\DD\ datafile_YYYY_MM_DD. tsv*

Zwróć uwagę, że informacje o dacie i godzinie są wyświetlane zarówno jako foldery, jak i w nazwie pliku.

W przypadku daty i godziny następujący wzorzec jest typowym wzorcem

*\DataSet\YYYY\MM\DD\HH\mm\ datafile_YYYY_MM_DD_HH_mm. tsv*

Po ponownym wyborze z folderu i organizacji plików należy zoptymalizować dla większych rozmiarów plików i rozsądnej liczby plików w poszczególnych folderach.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optymalizowanie zadań intensywnie korzystających z operacji we/wy na obciążeniach Hadoop i Spark w usłudze HDInsight

Zadania należą do jednej z następujących trzech kategorii:

* **Intensywność procesora CPU.**  Te zadania mają długotrwałe czasy obliczeń z minimalnymi liczbami operacji we/wy.  Przykłady obejmują zadania uczenia maszynowego i przetwarzania języka naturalnego.  
* **Duże ilości pamięci.**  Te zadania korzystają z dużej ilości pamięci.  Przykłady obejmują zadania analizy PageRank i w czasie rzeczywistym.  
* **Intensywna operacja we/wy.**  Te zadania spędzają większość czasu na wykonywanie operacji we/wy.  Typowym przykładem jest zadanie kopiowania, które wykonuje tylko operacje odczytu i zapisu.  Inne przykłady obejmują zadania przygotowywania danych, które odczytują dużo danych, wykonują transformację danych, a następnie zapisują dane z powrotem do magazynu.  

Poniższe wskazówki dotyczą wyłącznie zadań intensywnie korzystających z operacji we/wy.

## <a name="general-considerations"></a>Zagadnienia ogólne

Możesz mieć zadanie odczytujące lub zapisu do 100 MB w jednej operacji, ale bufor tego rozmiaru może naruszyć wydajność.
Aby zoptymalizować wydajność, spróbuj zachować rozmiar operacji we/wy z zakresu od 4 do 16 MB.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Ogólne zagadnienia dotyczące klastra usługi HDInsight

* **Wersje usługi HDInsight.** Aby uzyskać najlepszą wydajność, użyj najnowszej wersji usługi HDInsight.
* **Regionach.** Umieść konto Data Lake Storage Gen2 w tym samym regionie, w którym znajduje się klaster usługi HDInsight.  

Klaster HDInsight An składa się z dwóch węzłów głównych i niektórych węzłów procesu roboczego. Każdy węzeł procesu roboczego zapewnia określoną liczbę rdzeni i pamięci, która jest określana przez typ maszyny wirtualnej.  Podczas uruchamiania zadania, PRZĘDZa to negocjowanie zasobów, który przydziela dostępną pamięć i rdzenie do tworzenia kontenerów.  Każdy kontener uruchamia zadania, które są konieczne do ukończenia zadania.  Kontenery są uruchamiane równolegle w celu szybkiego przetwarzania zadań. W związku z tym wydajność można zwiększyć, uruchamiając jako możliwy do tego wiele kontenerów równoległych.

W klastrze usługi HDInsight znajdują się trzy warstwy, które można dostrajać, aby zwiększyć liczbę kontenerów i korzystać z całej dostępnej przepływności.  

* **Warstwa fizyczna**
* **Warstwa PRZĘDZy**
* **Warstwa obciążenia**

### <a name="physical-layer"></a>Warstwa fizyczna

**Uruchom klaster z większą liczbą węzłów i/lub maszynami wirtualnymi o większej wielkości.**  Większy klaster umożliwi Ci uruchamianie więcej kontenerów PRZĘDZy, jak pokazano na poniższej ilustracji.

![Diagram przedstawiający sposób, w jaki większy klaster umożliwi uruchamianie większej liczby kontenerów PRZĘDZy.](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Używaj maszyn wirtualnych z większą przepustowością sieci.**  Przepustowość sieci może stanowić wąskie gardło, jeśli przepustowość sieci jest mniejsza niż Data Lake Storage Gen2 przepływność.  Różne maszyny wirtualne będą miały różne rozmiary przepustowości sieci.  Wybierz typ maszyny wirtualnej, który ma największą możliwą przepustowość sieci.

### <a name="yarn-layer"></a>Warstwa PRZĘDZy

**Używaj mniejszych kontenerów PRZĘDZy.**  Zmniejsz rozmiar każdego kontenera PRZĘDZy, aby utworzyć więcej kontenerów z taką samą ilością zasobów.

![Diagram pokazujący wynik w przypadku zmniejszenia rozmiaru każdego kontenera PRZĘDZy, aby utworzyć więcej kontenerów.](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

W zależności od obciążenia będzie zawsze wymagany minimalny rozmiar kontenera PRZĘDZy. W przypadku wybrania zbyt małego kontenera zadania będą działać w przypadku problemów z brakiem pamięci. Zwykle kontenery PRZĘDZy nie powinny być mniejsze niż 1 GB. Typowym sposobem jest zobaczenie kontenerów PRZĘDZy WŁĄCZONĄ. W przypadku niektórych obciążeń może zajść potrzeba większego kontenera PRZĘDZy.  

**Zwiększ liczbę rdzeni na kontener PRZĘDZy.**  Zwiększ liczbę rdzeni przypisanych do każdego kontenera, aby zwiększyć liczbę zadań równoległych, które są uruchamiane w każdym kontenerze.  Działa to w przypadku aplikacji, takich jak platforma Spark, które uruchamiają wiele zadań na kontener.  W przypadku aplikacji, takich jak Hive uruchamiających pojedynczy wątek w każdym kontenerze, lepiej jest umieścić więcej kontenerów, a nie więcej rdzeni na kontener.

### <a name="workload-layer"></a>Warstwa obciążenia

**Użyj wszystkich dostępnych kontenerów.**  Ustaw liczbę zadań, która ma być równa lub większa niż liczba dostępnych kontenerów w celu wykorzystania wszystkich zasobów.

![Diagram przedstawiający użycie wszystkich kontenerów.](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Zadania zakończone niepowodzeniem są kosztowne.** Jeśli każde zadanie zawiera dużą ilość danych do przetworzenia, wówczas niepowodzenie zadania skutkuje kosztowną ponowną próbą.  W związku z tym lepiej jest utworzyć więcej zadań, z których każdy przetwarza niewielką ilość danych.

Oprócz ogólnych wytycznych, każda aplikacja ma inne parametry dostępne do dostrajania dla tej konkretnej aplikacji. W poniższej tabeli przedstawiono niektóre parametry i linki umożliwiające rozpoczęcie pracy z dostrajaniem wydajności dla każdej aplikacji.

| Obciążenie | Parametr do ustawiania zadań |
|----------|------------------------|
| [Platforma Spark w usłudze HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>NUM-wykonawcy</li><li>Wykonawca pamięci</li><li>Program wykonujący rdzenie</li></ul> |
| [Usługa Hive w usłudze HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>Hive. tez. Container. size</li></ul> |
| [MapReduce w usłudze HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>MapReduce. map. Memory</li><li>MapReduce. job. Maps</li><li>MapReduce. Zmniejsz ilość pamięci</li><li>MapReduce. job. Zmniejsz</li></ul> |
| [Usługa Storm w usłudze HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Liczba procesów roboczych</li><li>Liczba wystąpień programu wykonującego elementu Spout</li><li>Liczba wystąpień programu wykonującego Piorun </li><li>Liczba zadań elementu Spout</li><li>Liczba zadań obiektu Piorun</li></ul>|

## <a name="see-also"></a>Zobacz też
* [Omówienie Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
