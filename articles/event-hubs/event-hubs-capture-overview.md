---
title: Przechwytywanie zdarzeń przesyłania strumieniowego — Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera omówienie funkcji przechwytywania, która umożliwia przechwytywanie zdarzeń przesyłanych strumieniowo za pomocą usługi Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1b79db7a7f8d0fe03b21e005ef696d5fe55ac0a1
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613411"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Przechwyć zdarzenia za pomocą usługi Azure Event Hubs na platformie Azure Blob Storage lub Azure Data Lake Storage
Usługa Azure Event Hubs umożliwia automatyczne przechwytywanie danych przesyłanych strumieniowo w Event Hubs w usłudze [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) lub w ramach wybranego konta [Azure Data Lake Storage generacji 1 lub generacji 2](https://azure.microsoft.com/services/data-lake-store/) , z dodatkową elastycznością określania interwału czasu lub rozmiaru. Konfigurowanie przechwytywania jest szybkie, nie ma kosztów administracyjnych do uruchomienia i jest automatycznie skalowane przy użyciu [jednostek przepływności](event-hubs-scalability.md#throughput-units)Event Hubs. Przechwytywanie Event Hubs jest najprostszym sposobem ładowania danych przesyłanych strumieniowo na platformę Azure i umożliwia skoncentrowanie się na przetwarzaniu danych, a nie na przechwytywaniu danych.

> [!NOTE]
> Konfigurowanie przechwytywania Event Hubs do użycia Azure Data Lake Storage **Gen 2** jest takie samo, jak skonfigurowanie go do korzystania z BLOB Storage platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie przechwytywania Event Hubs](event-hubs-capture-enable-through-portal.md). 

Przechwytywanie Event Hubs umożliwia przetwarzanie potoków w czasie rzeczywistym i opartych na partiach w tym samym strumieniu. Oznacza to, że można tworzyć rozwiązania, które zwiększają się wraz z potrzebami. Bez względu na to, czy tworzysz systemy oparte na partiach, z uwzględnieniem przyszłego przetwarzania w czasie rzeczywistym, czy chcesz dodać wydajną ścieżkę zimną do istniejącego rozwiązania w czasie rzeczywistym, Event Hubs przechwytywanie ułatwia pracę z danymi przesyłanymi strumieniowo.

> [!IMPORTANT]
> Konto magazynu docelowego (Azure Storage lub Azure Data Lake Storage) musi znajdować się w tej samej subskrypcji co centrum zdarzeń. 

## <a name="how-event-hubs-capture-works"></a>Jak działa przechwytywanie Event Hubs

Event Hubs to trwały bufor przechowywania danych telemetrycznych, podobny do dziennika rozproszonego. Klucz do skalowania w Event Hubs jest [modelem odbiorcy partycjonowanym](event-hubs-scalability.md#partitions). Każda partycja jest niezależnym segmentem danych i jest używana niezależnie. W miarę upływu czasu te dane są wyłączane w oparciu o konfigurowalny okres przechowywania. W związku z tym dany centrum zdarzeń nigdy nie jest "zbyt pełny".

Przechwytywanie Event Hubs umożliwia określenie własnego konta i kontenera usługi Azure Blob Storage, a także konta Azure Data Lake Storage, które są używane do przechowywania przechwyconych danych. Te konta mogą znajdować się w tym samym regionie, w którym znajduje się centrum zdarzeń lub w innym regionie, co zwiększa elastyczność funkcji przechwytywania Event Hubs.

Przechwycone dane są zapisywane w formacie [Apache Avro][Apache Avro] : kompaktowy, szybki format binarny, który zapewnia rozbudowane struktury danych z wbudowanym schematem. Ten format jest szeroko używany w ekosystemie Hadoop, Stream Analytics i Azure Data Factory. Więcej informacji na temat pracy z usługą Avro jest dostępnych w dalszej części tego artykułu.

### <a name="capture-windowing"></a>Okna przechwytywania

Przechwytywanie Event Hubs umożliwia skonfigurowanie okna do kontroli przechwytywania. W tym oknie jest minimalny rozmiar i czas konfiguracji z "pierwszą zasadą usługi WINS", co oznacza, że pierwszy wyzwalacz napotkał błąd operacji przechwytywania. Jeśli masz 15-minutowy, 100 MB okna przechwytywania i wysłano 1 MB na sekundę, okno rozmiaru wyzwala się przed przedziałem czasu. Każda partycja przechwytuje niezależnie i zapisuje ukończony blokowy obiekt BLOB w czasie przechwytywania o nazwie dla czasu, w którym napotkano Interwał przechwytywania. Konwencja nazewnictwa magazynu jest następująca:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Wartości dat są uzupełnione zerami; Przykładowa nazwa pliku może być:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

W przypadku, gdy obiekt BLOB usługi Azure Storage jest tymczasowo niedostępny, przechwytywanie Event Hubs zachowa dane dla okresu przechowywania danych skonfigurowanego w centrum zdarzeń i zapisze dane po ponownym udostępnieniu konta magazynu.

### <a name="scaling-to-throughput-units"></a>Skalowanie do jednostek przepływności

Ruch Event Hubs jest kontrolowany przez [jednostki przepływności](event-hubs-scalability.md#throughput-units). Pojedyncza jednostka przepływności zezwala na 1 MB na sekundę lub 1000 zdarzeń na sekundę i dwa razy większą ilość danych wychodzących. Standardowe Event Hubs można skonfigurować przy użyciu jednostek przepływności 1-20 i można zakupić więcej dzięki [żądaniu zwiększenia obsługi][support request]przydziału. Użycie wykraczające poza zakupione jednostki przepływności jest ograniczone. Event Hubs Capture kopiuje dane bezpośrednio z magazynu wewnętrznego Event Hubs, pomijając przydziały ruchu wychodzącego jednostek przepływności i zapisując ruch wychodzący dla innych czytników przetwarzania, takich jak Stream Analytics lub Spark.

Po skonfigurowaniu Event Hubs przechwytywania zostanie automatycznie uruchomione po wysłaniu pierwszego zdarzenia i kontynuowania działania. Aby ułatwić przetwarzanie w czasie podrzędnym, upewnij się, że proces działa, Event Hubs zapisuje puste pliki, gdy nie ma żadnych danych. Ten proces zapewnia przewidywalny erze i znacznik, który może dostarczyć strumieniowe procesory.

## <a name="setting-up-event-hubs-capture"></a>Konfigurowanie przechwytywania Event Hubs

Funkcję przechwytywania można skonfigurować w czasie tworzenia centrum zdarzeń przy użyciu [Azure Portal](https://portal.azure.com)lub szablonów Azure Resource Manager. Aby uzyskać więcej informacji, zobacz następujące artykuły:

- [Włączanie funkcji przechwytywania usługi Event Hubs przy użyciu witryny Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Tworzenie przestrzeni nazw usługi Event Hubs z centrum zdarzeń i włączanie funkcji przechwytywania przy użyciu szablonu usługi Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Eksplorowanie przechwyconych plików i praca z Avro

Przechwytywanie Event Hubs tworzy pliki w formacie Avro, jak określono w skonfigurowanym przedziale czasu. Te pliki można wyświetlić w dowolnym narzędziu, takim jak [Eksplorator usługi Azure Storage][Azure Storage Explorer]. Pliki można pobrać lokalnie, aby móc z nich korzystać.

Pliki tworzone przez funkcję przechwytywania Event Hubs mają następujący schemat Avro:

![Schemat Avro][3]

Łatwym sposobem eksplorowania plików Avro jest użycie [Narzędzia Avro][Avro Tools] w postaci jar. Możesz również użyć funkcji [drążenia Apache][Apache Drill] w celu uzyskania uproszczonego środowiska opartego na języku SQL lub [Apache Spark][Apache Spark] , aby wykonać złożone przetwarzanie rozproszone dla pozyskiwanych danych. 

### <a name="use-apache-drill"></a>Korzystanie z funkcji drążenia Apache

[Apache drążenie][Apache Drill] to "otwarty aparat zapytań SQL" "open source" do eksploracji danych Big Data ", który może wykonywać zapytania o dane strukturalne i częściowo strukturalne wszędzie tam, gdzie się znajduje. Aparat może działać jako autonomiczny węzeł lub jako ogromny klaster w celu uzyskania doskonałej wydajności.

Dostępna jest Natywna obsługa magazynu obiektów blob platformy Azure, która ułatwia wykonywanie zapytań dotyczących danych w pliku Avro, zgodnie z opisem w dokumentacji:

[Funkcja drążenia Apache: wtyczka Blob Storage platformy Azure][Apache Drill: Azure Blob Storage Plugin]

Aby łatwo zbadać przechwycone pliki, możesz utworzyć i wykonać maszynę wirtualną z włączoną funkcją drążenia oprogramowania Apache za pośrednictwem kontenera, aby uzyskać dostęp do usługi Azure Blob Storage:

https://github.com/yorek/apache-drill-azure-blob

Pełny kompletny przykład jest dostępny w repozytorium przesyłania strumieniowego w skali:

[Przesyłanie strumieniowe na dużą skalę: Przechwytywanie Event Hubs]

### <a name="use-apache-spark"></a>Użyj Apache Spark

[Apache Spark][Apache Spark] to "ujednolicony aparat analityczny do przetwarzania danych na dużą skalę". Obsługuje on różne języki, w tym SQL, i może łatwo uzyskać dostęp do usługi Azure Blob Storage. Istnieje kilka opcji uruchamiania Apache Spark na platformie Azure, a każda z nich zapewnia łatwy dostęp do usługi Azure Blob Storage:

- [HDInsight: pliki adresów w usłudze Azure Storage][HDInsight: Address files in Azure storage]
- [Azure Databricks: Magazyn obiektów blob platformy Azure][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](../aks/spark-job.md) 

### <a name="use-avro-tools"></a>Korzystanie z narzędzi Avro

[Narzędzia Avro][Avro Tools] są dostępne jako pakiet jar. Po pobraniu pliku JAR można zobaczyć schemat określonego pliku Avro, uruchamiając następujące polecenie:

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

To polecenie zwraca

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Możesz również użyć narzędzi Avro, aby skonwertować plik do formatu JSON i wykonać inne przetwarzanie.

Aby przeprowadzić bardziej zaawansowane przetwarzanie, Pobierz i zainstaluj Avro dla wybranej platformy. W czasie tego pisania dostępne są implementacje języków C, C++, C \# , Java, NodeJS, Perl, php, Python i Ruby.

Oprogramowanie Apache Avro zakończyło się Wprowadzenie przewodników dla [języków Java][Java] i [Python][Python]. Możesz również przeczytać artykuł [wprowadzenie do Event Hubs przechwytywania](event-hubs-capture-python.md) .

## <a name="how-event-hubs-capture-is-charged"></a>Jak jest naliczana opłata za przechwycenie Event Hubs

Przechwytywanie Event Hubs jest mierzone w jednostkach przepływności: jako opłata godzinowa. Opłata jest naliczana bezpośrednio proporcjonalnie do liczby jednostek przepływności zakupionych dla przestrzeni nazw. W miarę zwiększania i zmniejszania jednostek przepływności, Event Hubs przechwytywania liczników zwiększa się i zmniejsza w celu zapewnienia dopasowania wydajności. Liczniki występują wspólnie. Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

Przechwytywanie nie zużywa limitu przydziału ruchu wychodzącego, ponieważ jest rozliczany osobno. 

## <a name="integration-with-event-grid"></a>Integracja z usługą Event Grid 

Jako źródło można utworzyć Azure Event Grid subskrypcję z przestrzenią nazw Event Hubs. W poniższym samouczku pokazano, jak utworzyć Event Grid subskrypcję z centrum zdarzeń jako źródło i Azure Functions aplikację jako ujścia: [proces i migracja przechwyconej Event Hubs danych do usługi Azure Synapse Analytics przy użyciu Event Grid i Azure Functions](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Następne kroki
Przechwytywanie Event Hubs jest najprostszym sposobem na pobieranie danych na platformę Azure. Korzystając z Azure Data Lake, Azure Data Factory i usługi Azure HDInsight, możesz wykonać przetwarzanie wsadowe i inne analizy przy użyciu znanych narzędzi i platform wybieranych w dowolnej skali.

Dowiedz się, jak włączyć tę funkcję przy użyciu szablonu Azure Portal i Azure Resource Manager:

- [Włączanie usługi Event Hubs Capture za pomocą witryny Azure Portal](event-hubs-capture-enable-through-portal.md)
- [Włączanie przechwytywania Event Hubs przy użyciu szablonu Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://github.com/microsoft/AzureStorageExplorer/releases
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://downloads.apache.org/avro/stable/java/
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Przesyłanie strumieniowe na dużą skalę: Przechwytywanie Event Hubs]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
