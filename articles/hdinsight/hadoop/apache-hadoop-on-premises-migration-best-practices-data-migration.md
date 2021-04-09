---
title: 'Migracja danych: Apache Hadoop lokalne do usługi Azure HDInsight'
description: Informacje o najlepszych rozwiązaniach dotyczących migracji danych dotyczących migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cdb028bcd608aa7a19a7c83e5e2c1129386928a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939787"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania dotyczące migracji danych

W tym artykule przedstawiono zalecenia dotyczące migracji danych do usługi Azure HDInsight. Jest ona częścią serii, która oferuje najlepsze rozwiązania ułatwiające Migrowanie lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrowanie danych lokalnych na platformę Azure

Dostępne są dwie główne opcje migracji danych ze środowiska lokalnego do platformy Azure:

* Transferowanie danych za pośrednictwem sieci przy użyciu protokołu TLS
    * Za pośrednictwem Internetu — można przesyłać dane do usługi Azure Storage za pośrednictwem zwykłego połączenia internetowego przy użyciu jednego z kilku narzędzi, takich jak Eksplorator usługi Azure Storage, AzCopy, Azure PowerShell i interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [przeniesienie danych do i z usługi Azure Storage](../../storage/common/storage-choose-data-transfer-solution.md).

    * Express Route-ExpressRoute to usługa platformy Azure, która umożliwia tworzenie prywatnych połączeń między centrami danych firmy Microsoft i infrastrukturą lokalną lub międzylokalizacyjną. Połączenia ExpressRoute nie przechodzą przez publiczny Internet i oferują wyższe opóźnienia niż typowe połączenia przez Internet. Aby uzyskać więcej informacji, zobacz [Tworzenie i modyfikowanie obwodu usługi ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).

    * Urządzenie Data Box transfer danych online — Data Box Edge i Data Box Gateway to produkty transferu danych online, które działają jako bramy magazynu sieciowego do zarządzania danymi między lokacjami i platformą Azure. Data Box Edge, lokalne urządzenie sieciowe, transferuje dane do i z platformy Azure i korzysta z obliczeń brzegowych opartych na sztucznej analizie (AI), aby przetwarzać dane. Data Box Gateway jest urządzeniem wirtualnym z możliwościami bramy magazynu. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą Azure Data Box — transfer w trybie online](../../databox-online/index.yml).

* Dostarczanie danych w trybie offline

    Urządzenie Data Box transfer danych w trybie offline — urządzenie Data Box, Data Box Disk i urządzenia Data Box Heavy umożliwiają Transferowanie dużych ilości danych na platformę Azure, gdy sieć nie jest opcją. Te urządzenia transferu danych w trybie offline są dostarczane między organizacją a centrum dane platformy Azure. Używają szyfrowania AES do ochrony danych podczas przesyłania i są one poddawane gruntownym procesom oczyszczania po przekazaniu, aby usunąć dane z urządzenia. Aby uzyskać więcej informacji na urządzenie Data Box urządzeniach transferu w trybie offline, zobacz [dokumentację Azure Data Box — transfer w trybie offline](../../databox/index.yml). Aby uzyskać więcej informacji na temat migracji klastrów Hadoop, zobacz [używanie Azure Data Box do migrowania z lokalnego magazynu systemu plików HDFS do usługi Azure Storage](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

Poniższa tabela ma przybliżony czas trwania transferu danych oparty na woluminie danych i przepustowości sieci. Użyj pola danych, jeśli oczekuje się, że migracja danych zajmie więcej niż trzy tygodnie.

|Ilość danych | Przepustowość sieci<br>z<br>**45 MB/s (T3)**|Przepustowość sieci<br>z<br>**100 Mb/s**|Przepustowość sieci<br>z<br>**1 Gb/s**|Przepustowość sieci<br>z<br>**10 Gb/s**|
|---|:---:|:---:|:---:|:---:|
|1 TB|2 dni|1 dzień| 2 godziny|14 minut|
|10 TB|22 dni|10 dni|1 dzień|2 godziny|
|35 TB|76 dni|34 dni|3 dni|8 godzin|
|80 TB|173 dni|78 dni|8 dni|19 godzin|
|100 TB|216 dni|97 dni|10 dni|1 dzień|
|200 TB|1 rok|194 dni|19 dni|2 dni|
|500 TB|3 lata|1 rok|49 dni|5 dni|
|1 PB|6 lat|3 lata|97 dni|10 dni|
|2 PB|12 lat|5 lat|194 dni|19 dni|

Narzędzia natywne dla platformy Azure, takie jak Apache Hadoop pomocą distcp, Azure Data Factory i AzureCp, mogą służyć do transferowania danych za pośrednictwem sieci. WANDisco narzędzi innych firm można także użyć w tym samym celu. Apache Kafka narzędzia MirrorMaker i Apache Sqoop mogą służyć do ciągłego transferu danych z systemów lokalnych do systemu Azure Storage.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z Apache Hadoop pomocą distcp

Pomocą distcp to projekt Apache, który używa zadania mapy MapReduce do transferowania danych, obsługi błędów i odzyskiwania po tych błędach. Przypisuje listę plików źródłowych do każdego zadania mapy. Następnie zadanie Mapa kopiuje wszystkie przypisane pliki do miejsca docelowego. Istnieje kilka technik, które mogą zwiększyć wydajność pomocą distcp.

### <a name="increase-the-number-of-mappers"></a>Zwiększ liczbę mapera

Pomocą distcp próbuje utworzyć zadania mapy, tak aby każda kopia była w przybliżeniu równa tej samej liczbie bajtów. Domyślnie zadania pomocą distcp używają 20 mapowania. Użycie większej liczby odwzorowań dla pomocą distcp (z parametrem 'm w wierszu polecenia) zwiększa równoległości podczas procesu transferu danych i zmniejsza długość transferu danych. Istnieją jednak dwie rzeczy, które należy wziąć pod uwagę podczas zwiększania liczby odwzorowań:

* Najniższy poziom szczegółowości pomocą distcp jest pojedynczym plikiem. Określenie liczby odwzorowania więcej niż liczba plików źródłowych nie jest pomocna i spowoduje marnowanie dostępnych zasobów klastra.

* Aby określić liczbę odwzorowań, należy wziąć pod uwagę dostępną pamięć przędzy w klastrze. Każde zadanie mapy jest uruchamiane jako kontener przędzy. Przy założeniu, że żadne inne duże obciążenia nie są uruchomione w klastrze, liczba odwzorowań może być określona przez następującą formułę: m = (liczba węzłów procesu roboczego \* przędzy pamięć dla każdego węzła procesu roboczego)/rozmiar kontenera przędzy. Jeśli jednak inne aplikacje używają pamięci, należy wybrać opcję użycia tylko części pamięci PRZĘDZy dla zadań pomocą distcp.

### <a name="use-more-than-one-distcp-job"></a>Użyj więcej niż jednego zadania pomocą distcp

Gdy rozmiar zestawu danych, który ma zostać przeniesiony, jest większy niż 1 TB, należy użyć więcej niż jednego zadania pomocą distcp. Użycie więcej niż jednego zadania ogranicza wpływ awarii. Jeśli jakieś zadanie zakończy się niepowodzeniem, należy ponownie uruchomić to konkretne zadanie, a nie wszystkie zadania.

### <a name="consider-splitting-files"></a>Rozważ rozdzielenie plików

Jeśli istnieje niewielka liczba dużych plików, rozważ ich rozdzielenie na fragmenty plików 256-MB, aby uzyskać więcej możliwości współbieżności z większą liczbą odwzorowań.

### <a name="use-the-strategy-command-line-parameter"></a>Użyj parametru wiersza polecenia "Strategia"

Rozważ użycie `strategy = dynamic` parametru w wierszu polecenia. Wartość domyślna `strategy` parametru to `uniform size` , w którym przypadku każda kopia mapy jest w przybliżeniu równa tej samej liczbie bajtów. Gdy ten parametr zostanie zmieniony na `dynamic` , plik listy jest podzielony na kilka "fragmentów plików". Liczba plików fragmentów jest wielokrotnością liczby map. Każde zadanie mapy ma przypisany jeden z plików fragmentów. Po przetworzeniu wszystkich ścieżek w fragmencie zostaje usunięty bieżący fragment i zostanie pobrany nowy fragment. Proces jest kontynuowany, dopóki nie będzie dostępnych więcej fragmentów. To podejście "dynamiczne" pozwala szybszym zadaniom mapy na korzystanie z większej liczby ścieżek niż wolniejsze i przyspieszanie zadań pomocą distcp.

### <a name="increase-the-number-of-threads"></a>Zwiększ liczbę wątków

Sprawdź, czy zwiększenie `-numListstatusThreads` parametru zwiększa wydajność. Ten parametr określa liczbę wątków do użycia na potrzeby kompilowania listy plików, a 40 jest wartością maksymalną.

### <a name="use-the-output-committer-algorithm"></a>Użyj algorytmu programu przekazującego dane wyjściowe

Sprawdź, czy przekazywanie parametru `-Dmapreduce.fileoutputcommitter.algorithm.version=2` poprawia wydajność pomocą distcp. Ten algorytm zatwierdzania danych wyjściowych ma optymalizacje dotyczące zapisywania plików wyjściowych w miejscu docelowym. Następujące polecenie ilustruje użycie różnych parametrów:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migracja metadanych

### <a name="apache-hive"></a>Apache Hive

Magazyn metadanych Hive można migrować za pomocą skryptów lub przy użyciu replikacji bazy danych.

#### <a name="hive-metastore-migration-using-scripts"></a>magazyn metadanych Hive migracji przy użyciu skryptów

1. Generowanie DDLs Hive z poziomu lokalnego magazyn metadanych Hive. Ten krok można wykonać przy użyciu [skryptu bash otoki](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Edytuj wygenerowany kod DDL, aby zamienić adres URL systemu HDFS na adresy URL WASB/ADLS/ABFS.
1. Uruchom zaktualizowany kod DDL w magazynie metadanych z klastra usługi HDInsight.
1. Upewnij się, że wersja magazyn metadanych Hive jest zgodna między środowiskiem lokalnym i chmurą.

#### <a name="hive-metastore-migration-using-db-replication"></a>Migracja magazyn metadanych Hive przy użyciu replikacji bazy danych

- Skonfiguruj replikację bazy danych między lokalną magazyn metadanych Hive DB i bazą danych magazynu metadanych usługi HDInsight.
- Aby zastąpić adres URL systemu HDFS przy użyciu adresów URL WASB/ADLS/ABFS, użyj narzędzia "Hive".

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- Eksportuj lokalne zasady Ranger do plików XML.
- Przekształć specyficzne dla konkretnych lokalizacji ścieżki oparte na systemie HDFS na WASB/ADLS za pomocą narzędzia, takiego jak XSLT.
- Zaimportuj zasady do usługi Ranger uruchomionej w usłudze HDInsight.

## <a name="next-steps"></a>Następne kroki

Przeczytaj następny artykuł z tej serii:

- [Najlepsze rozwiązania dotyczące zabezpieczeń i DevOps migracji w środowisku Azure HDInsight Hadoop lokalnym](apache-hadoop-on-premises-migration-best-practices-security-devops.md)