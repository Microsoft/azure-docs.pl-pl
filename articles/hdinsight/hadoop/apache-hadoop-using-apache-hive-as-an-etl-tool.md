---
title: Używanie Apache Hive jako narzędzia ETL — Azure HDInsight
description: Użyj Apache Hive do wyodrębniania, przekształcania i ładowania danych (ETL) w usłudze Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be331f36a6305b05ce83a2b2d5fdfb73a154ce3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77623117"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Użyj Apache Hive jako narzędzia wyodrębniania, przekształcania i ładowania (ETL)

Zazwyczaj należy oczyścić i przekształcić przychodzące dane przed załadowaniem ich do miejsca docelowego odpowiedniego do analizy. Operacje wyodrębniania, przekształcania i ładowania (ETL) są używane do przygotowywania danych i ładowania ich do miejsca docelowego danych.  Apache Hive w usłudze HDInsight może odczytywać dane bez struktury, przetwarzać je w razie potrzeby, a następnie ładować dane do magazynu danych relacyjnych na potrzeby systemów pomocy technicznej. W tym podejściu dane są wyodrębniane ze źródła i przechowywane w skalowalnym magazynie, takim jak obiekty blob usługi Azure Storage lub Azure Data Lake Storage. Dane są następnie przekształcane przy użyciu sekwencji zapytań programu Hive i są ostatecznie umieszczane w gałęzi w ramach przygotowania do ładowania zbiorczego do docelowego magazynu danych.

## <a name="use-case-and-model-overview"></a>Przegląd przypadków użycia i modelu

Na poniższej ilustracji przedstawiono przegląd przypadku użycia i modelu dla automatyzacji ETL. Dane wejściowe są przekształcane w celu wygenerowania odpowiednich danych wyjściowych.  Podczas tej transformacji dane mogą zmieniać kształt, typ danych i język nawet.  Procesy ETL umożliwiają konwersję z układu brytyjskiego na metrykę, zmienianie stref czasowych i poprawianie dokładności odpowiednio wyrównane z istniejącymi danymi w miejscu docelowym.  Procesy ETL mogą również łączyć nowe dane z istniejącymi danymi, aby aktualizować raporty lub uzyskiwać dokładniejsze informacje o istniejących danych.  Aplikacje, takie jak narzędzia i usługi raportowania, mogą następnie korzystać z tych danych w żądanym formacie.

![Apache Hive jako architektura ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Usługa Hadoop jest zwykle używana w procesach ETL, które zaimportują ogromną liczbę plików tekstowych (takich jak CSV) lub mniejszą, ale często zmieniają liczbę plików tekstowych.  Hive jest doskonałym narzędziem służącym do przygotowywania danych przed załadowaniem ich do miejsca docelowego danych.  Program Hive umożliwia utworzenie schematu w formacie CSV i użycie języka przypominającego SQL w celu wygenerowania programów MapReduce, które współdziałają z danymi.

Typowymi krokami korzystania z programu Hive w celu wykonania ETL są następujące:

1. Załaduj dane do Azure Data Lake Storage lub Blob Storage platformy Azure.
2. Utwórz bazę danych magazynu metadanych (przy użyciu Azure SQL Database) do użycia przez gałąź Hive w celu przechowywania schematów.
3. Utwórz klaster usługi HDInsight i połącz go z magazynem danych.
4. Zdefiniuj schemat, który ma być stosowany podczas odczytu danych w magazynie danych:

    ```
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

Można użyć programu Hive do wyprowadzania danych do różnych elementów docelowych, takich jak:

* Relacyjna baza danych, taka jak SQL Server lub Azure SQL Database.
* Magazyn danych, taki jak Azure SQL Data Warehouse.
* Excel.
* Azure Table i BLOB Storage.
* Aplikacje lub usługi, które wymagają, aby dane były przetwarzane do określonych formatów lub jako pliki, które zawierają określone typy struktury informacji.
* Magazyn dokumentów JSON, taki jak [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

## <a name="considerations"></a>Zagadnienia do rozważenia

Model ETL jest zazwyczaj używany, gdy chcesz:

* Ładowanie danych strumienia lub dużych ilości danych z częściową strukturą lub bez struktury ze źródeł zewnętrznych do istniejącej bazy danych lub systemu informacji.
* Wyczyść, Przekształć i sprawdź poprawność danych przed ich załadowaniem, na przykład za pomocą więcej niż jednego przebiegu transformacji przez klaster.
* Generowanie raportów i wizualizacji, które są regularnie aktualizowane. Na przykład jeśli generowanie raportu trwa zbyt długo, możesz zaplanować uruchamianie raportu w nocy. Aby automatycznie uruchomić zapytanie programu Hive, można użyć [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) i programu PowerShell.

Jeśli obiekt docelowy danych nie jest bazą danych, można wygenerować plik w odpowiednim formacie w ramach zapytania, na przykład wolumin CSV. Następnie można zaimportować ten plik do programu Excel lub Power BI.

Jeśli trzeba wykonać kilka operacji na danych w ramach procesu ETL, należy rozważyć sposób zarządzania nimi. Jeśli operacje są kontrolowane przez program zewnętrzny, a nie jako przepływ pracy w ramach rozwiązania, należy zdecydować, czy niektóre operacje można wykonywać równolegle, a także wykryć, kiedy każde zadanie zostało zakończone. Korzystanie z mechanizmu przepływu pracy, takiego jak Oozie w ramach usługi Hadoop, może być łatwiejsze niż próba organizowania sekwencji operacji przy użyciu skryptów zewnętrznych lub programów niestandardowych. Aby uzyskać więcej informacji na temat Oozie, zobacz [przepływy pracy i zadania](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>Następne kroki

* [ETL na dużą skalę](apache-hadoop-etl-at-scale.md)
* [Operacjonalizować potoku danych](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
