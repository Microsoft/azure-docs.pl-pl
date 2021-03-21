---
title: Używanie Apache Hive jako narzędzia ETL — Azure HDInsight
description: Użyj Apache Hive do wyodrębniania, przekształcania i ładowania danych (ETL) w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: e2b76fbc2afaad465773860ff45f18261edad138
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933436"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Użyj Apache Hive jako narzędzia wyodrębniania, przekształcania i ładowania (ETL)

Zazwyczaj należy oczyścić i przekształcić przychodzące dane przed załadowaniem ich do miejsca docelowego odpowiedniego do analizy. Operacje wyodrębniania, przekształcania i ładowania (ETL) są używane do przygotowywania danych i ładowania ich do miejsca docelowego danych.  Apache Hive w usłudze HDInsight może odczytywać dane bez struktury, przetwarzać je w razie potrzeby, a następnie ładować dane do magazynu danych relacyjnych na potrzeby systemów pomocy technicznej. W tym podejściu dane są wyodrębniane ze źródła. Następnie przechowywane w magazynie dostosowywalnym, takim jak obiekty blob usługi Azure Storage lub Azure Data Lake Storage. Dane są następnie przekształcane przy użyciu sekwencji zapytań programu Hive. Następnie przemieszczono w gałęzi przygotowania do ładowania zbiorczego do docelowego magazynu danych.

## <a name="use-case-and-model-overview"></a>Przegląd przypadków użycia i modelu

Na poniższej ilustracji przedstawiono przegląd przypadku użycia i modelu dla automatyzacji ETL. Dane wejściowe są przekształcane w celu wygenerowania odpowiednich danych wyjściowych.  Podczas tej transformacji dane zmieniają kształt, typ danych i język nawet.  Procesy ETL umożliwiają konwersję z układu brytyjskiego na metrykę, zmienianie stref czasowych i poprawianie dokładności odpowiednio wyrównane z istniejącymi danymi w miejscu docelowym. Procesy ETL mogą również łączyć nowe dane z istniejącymi danymi, aby aktualizować raporty lub uzyskiwać dokładniejsze informacje o istniejących danych. Aplikacje, takie jak narzędzia i usługi raportowania, mogą następnie korzystać z tych danych w żądanym formacie.

![Apache Hive jako architektura ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Usługa Hadoop jest zwykle używana w procesach ETL, które zaimportują ogromną liczbę plików tekstowych (takich jak CSV). Lub mniejsza, ale często zmieniana liczba plików tekstowych lub obie.  Hive jest doskonałym narzędziem służącym do przygotowywania danych przed załadowaniem ich do miejsca docelowego danych.  Program Hive umożliwia utworzenie schematu w formacie CSV i użycie języka przypominającego SQL w celu wygenerowania programów MapReduce, które współdziałają z danymi.

Typowymi krokami korzystania z usługi Hive do wykonania ETL są następujące:

1. Załaduj dane do Azure Data Lake Storage lub Blob Storage platformy Azure.
2. Utwórz bazę danych magazynu metadanych (przy użyciu Azure SQL Database) do użycia przez gałąź Hive w celu przechowywania schematów.
3. Utwórz klaster usługi HDInsight i połącz go z magazynem danych.
4. Zdefiniuj schemat, który ma być stosowany podczas odczytu danych w magazynie danych:

    ```hql
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Przekształć dane i załaduj je do miejsca docelowego.  Istnieje kilka sposobów na korzystanie z gałęzi podczas transformacji i ładowania:

    * Wykonywanie zapytań i przygotowywanie danych przy użyciu usługi Hive i zapisywanie ich jako woluminu CSV w Azure Data Lake Storage lub Azure Blob Storage.  Następnie użyj narzędzia takiego jak SQL Server Integration Services (SSIS), aby uzyskać te CSV i załadować dane do docelowej relacyjnej bazy danych, takiej jak SQL Server.
    * Wykonywanie zapytań dotyczących danych bezpośrednio z programu Excel lub C# przy użyciu sterownika Hive ODBC.
    * Za pomocą platformy [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) Odczytaj przygotowane pliki płaskiego woluminu CSV i załaduj je do docelowej relacyjnej bazy danych.

## <a name="data-sources"></a>Źródła danych

Źródła danych są zazwyczaj danymi zewnętrznymi, które można dopasować do istniejących danych w magazynie danych, na przykład:

* Dane mediów społecznościowych, pliki dzienników, czujniki i aplikacje, które generują pliki danych.
* Zestawy danych uzyskane od dostawców, takie jak statystyki pogodowe lub numery sprzedaży dostawców.
* Dane przesyłane strumieniowo przechwycone, filtrowane i przetwarzane przez odpowiednie narzędzie lub platformę.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Wyjściowe elementy docelowe

Można użyć programu Hive do wyprowadzania danych do różnych rodzajów obiektów docelowych, takich jak:

* Relacyjna baza danych, taka jak SQL Server lub Azure SQL Database.
* Magazyn danych, taki jak Azure Synapse Analytics.
* Excel.
* Azure Table i BLOB Storage.
* Aplikacje lub usługi, które wymagają, aby dane były przetwarzane do określonych formatów lub jako pliki, które zawierają określone typy struktury informacji.
* Magazyn dokumentów JSON, taki jak Azure Cosmos DB.

## <a name="considerations"></a>Zagadnienia do rozważenia

Model ETL jest zazwyczaj używany, gdy chcesz:

`*` Ładowanie danych strumienia lub dużych ilości danych z częściową strukturą lub bez struktury ze źródeł zewnętrznych do istniejącej bazy danych lub systemu informacji.
`*` Wyczyść, Przekształć i sprawdź poprawność danych przed ich załadowaniem, na przykład za pomocą więcej niż jednego przebiegu transformacji przez klaster.
`*` Generowanie raportów i wizualizacji, które są regularnie aktualizowane. Na przykład jeśli generowanie raportu trwa zbyt długo, możesz zaplanować uruchamianie raportu w nocy. Aby automatycznie uruchomić zapytanie programu Hive, można użyć [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) i programu PowerShell.

Jeśli obiekt docelowy danych nie jest bazą danych, można wygenerować plik w odpowiednim formacie w ramach zapytania, na przykład wolumin CSV. Następnie można zaimportować ten plik do programu Excel lub Power BI.

Jeśli trzeba wykonać kilka operacji na danych w ramach procesu ETL, należy rozważyć sposób zarządzania nimi. Przy użyciu operacji kontrolowanych przez program zewnętrzny, a nie jako przepływ pracy w ramach rozwiązania, należy zdecydować, czy niektóre operacje mogą być wykonywane równolegle. I do wykrywania czasu ukończenia każdego zadania. Korzystanie z mechanizmu przepływu pracy, takiego jak Oozie w ramach usługi Hadoop, może być łatwiejsze niż próba organizowania sekwencji operacji przy użyciu skryptów zewnętrznych lub programów niestandardowych.

## <a name="next-steps"></a>Następne kroki

* [ETL na dużą skalę](apache-hadoop-etl-at-scale.md)
* [`Operationalize a data pipeline`](../hdinsight-operationalize-data-pipeline.md)
