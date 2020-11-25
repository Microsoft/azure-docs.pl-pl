---
title: Azure Data Lake Storage Gen2 wprowadzenie
description: Zapoznaj się z wprowadzeniem do Azure Data Lake Storage Gen2. Poznaj kluczowe funkcje. Przejrzyj obsługiwane funkcje magazynu obiektów blob, integracje usług platformy Azure i platformy.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 5f2f3cfc5ccbdd6a3d3d3ede5bb39a3f6f548b19
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913100"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Wprowadzenie do usługi Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 to zestaw funkcji przeznaczonych do analizy danych Big Data, opartych na [usłudze Azure Blob Storage](storage-blobs-introduction.md). 

Data Lake Storage Gen2 zbieżność możliwości [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) z usługą Azure Blob Storage. Na przykład Data Lake Storage Gen2 zapewnia semantykę systemu plików, zabezpieczenia na poziomie plików i skalowanie. Ponieważ te możliwości są oparte na usłudze BLOB Storage, można również uzyskać niski koszt magazynu warstwowego z możliwościami wysokiej dostępności i odzyskiwania po awarii.

## <a name="designed-for-enterprise-big-data-analytics"></a>Zaprojektowana na potrzeby analizy danych Big Data w przedsiębiorstwie

Data Lake Storage Gen2 sprawia, że usługa Azure Storage jest podstawą do tworzenia jezior danych przedsiębiorstwa na platformie Azure. Zaprojektowana od początku do obsługi wielu petabajtów informacji przy zachowaniu setek gigabitowej przepływności, Data Lake Storage Gen2 umożliwia łatwe zarządzanie ogromnymi ilościami danych.

Podstawową częścią Data Lake Storage Gen2 jest dodanie [hierarchicznej przestrzeni nazw](data-lake-storage-namespace.md) do magazynu obiektów BLOB. Hierarchiczna przestrzeń nazw organizuje obiekty/pliki w hierarchię katalogów w celu zapewnienia wydajnego dostępu do danych. Wspólna konwencja nazewnictwa magazynu obiektów używa ukośników w nazwie do naśladowania hierarchicznej struktury katalogów. Ta struktura zacznie być prawdziwa z Data Lake Storage Gen2. Operacje, takie jak zmiana nazwy lub usuwanie katalogu, stają się pojedynczą niepodzielnymi operacjami metadanych w katalogu. Nie trzeba wyliczać i przetwarzać wszystkich obiektów, które współużytkują prefiks nazwy katalogu.

Data Lake Storage Gen2 kompiluje w usłudze BLOB Storage i zwiększa wydajność, zarządzanie i bezpieczeństwo w następujący sposób:

-   **Wydajność** jest zoptymalizowana, ponieważ nie trzeba kopiować ani przekształcać danych jako wymaganie wstępne do analizy. W porównaniu do płaskiej przestrzeni nazw w usłudze BLOB Storage, hierarchiczna przestrzeń nazw znacznie zwiększa wydajność operacji zarządzania katalogami, co zwiększa ogólną wydajność zadań.

-   **Zarządzanie** jest łatwiejsze, ponieważ pliki można organizować i manipulować nimi za poorednictwem katalogów i podkatalogów.

-   **Zabezpieczenia** są wymuszane, ponieważ można DEFINIOWAĆ uprawnienia POSIX do katalogów lub pojedynczych plików.

Ponadto Data Lake Storage Gen2 jest bardzo opłacalne, ponieważ jest ona oparta na [usłudze Azure Blob Storage](storage-blobs-introduction.md). Dodatkowe funkcje dodatkowo obniżają łączny koszt posiadania na potrzeby analizy danych Big Data na platformie Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Najważniejsze funkcje Data Lake Storage Gen2

-   **Dostęp zgodny** z usługą Hadoop: Data Lake Storage Gen2 umożliwia zarządzanie danymi i uzyskiwanie do nich dostępu tak samo jak w przypadku [rozproszony system plików Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nowy [Sterownik ABFS](data-lake-storage-abfs-driver.md) (używany do uzyskiwania dostępu do danych) jest dostępny w ramach wszystkich środowisk Apache Hadoop. Te środowiska obejmują [usługi Azure HDInsight](../../hdinsight/index.yml)*,* [Azure Databricks](/azure/databricks/)i [Azure Synapse Analytics](/azure/synapse-analytics).

-   **Nadzbiór uprawnień systemu POSIX**: model zabezpieczeń dla Data Lake Gen2 obsługuje uprawnienia ACL i POSIX oraz niezbędny stopień szczegółowości Data Lake Storage Gen2. Ustawienia można skonfigurować za poorednictwem Eksplorator usługi Storage lub platform, takich jak Hive i Spark.

-   **Koszt ekonomiczny**: Data Lake Storage Gen2 oferuje niską wydajność magazynu i transakcji. Funkcje, takie jak [cykl życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md) , optymalizują koszty jako przejścia danych w całym cyklu życia.

-   **Zoptymalizowany sterownik**: Sterownik ABFS jest [zoptymalizowany pod](data-lake-storage-abfs-driver.md) kątem analizy danych Big Data. Odpowiednie interfejsy API REST są przyłączone do punktu końcowego `dfs.core.windows.net` .

### <a name="scalability"></a>Skalowalność

Usługa Azure Storage jest skalowalna poprzez projektowanie, czy dostęp odbywa się za pośrednictwem interfejsów Data Lake Storage Gen2 lub BLOB Storage. Jest w stanie przechowywać i obsługiwać *wiele eksabajtowej danych*. Ta ilość miejsca w magazynie jest dostępna z przepływem wydajności mierzonym w gigabitach na sekundę (GB/s) na wysokim poziomie operacji wejścia/wyjścia na sekundę (IOPS). Przetwarzanie jest wykonywane przy opóźnieniu o bliskich stałych żądaniach, które są mierzone na poziomach usługi, konta i pliku.

### <a name="cost-effectiveness"></a>Opłacalność

Ponieważ Data Lake Storage Gen2 jest oparty na usłudze Azure Blob Storage, pojemność magazynu i koszty transakcji są mniejsze. W przeciwieństwie do innych usług magazynu w chmurze, nie musisz przenosić ani przekształcać danych przed ich przeanalizą. Aby uzyskać więcej informacji o cenach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage).

Ponadto funkcje takie jak [hierarchiczna przestrzeń nazw](data-lake-storage-namespace.md) znacząco zwiększają ogólną wydajność wielu zadań analitycznych. To ulepszenie wydajności oznacza, że wymaga mniejszej mocy obliczeniowej, aby przetwarzać tę samą ilość danych, co skutkuje niższym całkowitym kosztem posiadania (TCO) dla kompleksowego zadania analizy.

### <a name="one-service-multiple-concepts"></a>Jedna usługa, wiele koncepcji

Ponieważ Data Lake Storage Gen2 jest oparta na usłudze Azure Blob Storage, wiele koncepcji może opisywać te same, udostępnione elementy.

Poniżej przedstawiono jednostki równoważne, zgodnie z opisem różnych koncepcji. O ile nie określono inaczej, te jednostki są bezpośrednio synonimem:

| Pojęcie                                | Organizacja najwyższego poziomu | Organizacja niższego poziomu                                            | Kontener danych |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Obiekty blob — magazyn obiektów ogólnego przeznaczenia | Kontener              | Katalog wirtualny (tylko SDK — nie zapewnia manipulowania niepodzielnego) | Obiekt blob           |
| Azure Data Lake Storage Gen2 — magazyn analizy          | Kontener            | Katalog                                                           | Plik           |

## <a name="supported-blob-storage-features"></a>Obsługiwane funkcje usługi Blob Storage

Funkcje magazynu obiektów blob, takie jak [rejestrowanie diagnostyczne](../common/storage-analytics-logging.md), [warstwy dostępu](storage-blob-storage-tiers.md)i [zasady zarządzania cyklem życia BLOB Storage](storage-lifecycle-management-concepts.md) są dostępne dla Twojego konta. 

Aby uzyskać listę obsługiwanych funkcji usługi BLOB Storage, zobacz [BLOB Storage funkcje dostępne w Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Obsługiwane integracje usług platformy Azure

Data Lake Storage Gen2 obsługuje kilka usług platformy Azure. Można ich używać do pozyskiwania danych, wykonywania analiz i tworzenia reprezentacji wizualnych. Aby uzyskać listę obsługiwanych usług platformy Azure, zobacz [usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Obsługiwane platformy open source

Kilka platform Open Source obsługuje Data Lake Storage Gen2. Aby uzyskać pełną listę, zobacz [platformy typu "open source" obsługujące Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Zobacz też

- [Znane problemy z Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Dostęp z wieloprotokołem do Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)