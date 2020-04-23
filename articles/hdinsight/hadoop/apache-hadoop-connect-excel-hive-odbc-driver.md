---
title: Sterownik programu Excel & Apache Hadoop ze sterownikiem open database connectivity (ODBC) — Azure HDInsight
description: Dowiedz się, jak skonfigurować i używać sterownika Microsoft Hive ODBC dla programu Excel do wykonywania zapytań o dane w klastrach usługi HDInsight z programu Microsoft Excel.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/22/2020
ms.openlocfilehash: 5aefd2c344565bf6dcb384996c42c1bc30e7291d
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024935"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Łączenie programu Excel z usługą Apache Hadoop w usłudze Azure HDInsight przy użyciu sterownika Microsoft Hive ODBC

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Rozwiązanie Big Data firmy Microsoft integruje składniki analizy biznesowej firmy Microsoft (BI) z klastrami Apache Hadoop wdrożonymi w programie HDInsight. Przykładem jest możliwość łączenia programu Excel z magazynem danych hive klastra Hadoop. Połącz się za pomocą sterownika odbc (OdBC) funkcji Microsoft Hive Open Database Connectivity (ODBC).

Dane skojarzone z klastrem HDInsight z programu Excel można połączyć z dodatkiem Microsoft Power Query dla programu Excel. Aby uzyskać więcej informacji, zobacz [Łączenie programu Excel z programem HDInsight za pomocą dodatku Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego artykułu musisz mieć następujące elementy:

* Klaster Hadoop HDInsight. Aby go utworzyć, zobacz [Wprowadzenie do usługi Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Stacja robocza z pakietem Office 2010 Professional Plus lub nowszym lub Excel 2010 lub nowsza.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalowanie sterownika ODBC firmy Microsoft Hive

Pobierz i zainstaluj [sterownik OdBC firmy Microsoft Hive](https://www.microsoft.com/download/details.aspx?id=40886). Wybierz wersję, która pasuje do wersji aplikacji, w której będziesz używać sterownika ODBC.  W tym artykule sterownik jest używany w programie Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Tworzenie źródła danych Apache Hive ODBC

W poniższych krokach pokazano, jak utworzyć źródło danych ODBC hive.

1. W systemie Windows przejdź do **pozycji Narzędzia administracyjne systemu Windows > > źródła danych ODBC (32-bitowe)/(64-bitowe)**.  Ta akcja powoduje otwarcie okna **Administrator źródła danych ODBC.**

    ![Administrator źródła danych OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Konfigurowanie sieci DSN przy użyciu administratora źródła danych ODBC")

1. Na karcie **Nazwa DSN użytkownika** wybierz pozycję **Dodaj,** aby otworzyć okno **Utwórz nowe źródło danych.**

1. Wybierz pozycję **Sterownik ODBC gałęzi firmy Microsoft,** a następnie wybierz pozycję **Zakończ,** aby otworzyć okno **Instalatora dsn sterownika ODBC sterownika odbc firmy Microsoft.**

1. Wpisz lub wybierz poniższe wartości:

   | Właściwość | Opis |
   | --- | --- |
   |  Data Source Name (Nazwa źródła danych) |Nadaj nazwę źródła danych. |
   |  Hostami |Wprowadź polecenie `HDInsightClusterName.azurehdinsight.net`. Na przykład `myHDICluster.azurehdinsight.net`. Uwaga: `HDInsightClusterName-int.azurehdinsight.net` jest obsługiwana tak długo, jak maszyna wirtualna klienta jest równorzędna z tą samą siecią wirtualną. |
   |  Port |Użyj portu **443**. (Ten port został zmieniony z 563 na 443). |
   |  baza danych |Użyj **domyślnego**pliku . |
   |  Mechanism (Mechanizm) |Wybieranie **usługi HDInsight systemu Windows Azure** |
   |  Nazwa użytkownika |Wprowadź nazwę użytkownika HTTP klastra HDInsight. Domyślna nazwa użytkownika to **admin**. |
   |  Hasło |Wprowadź hasło użytkownika klastra HDInsight. Zaznacz pole wyboru **Zapisz hasło (zaszyfrowane).**|

1. Opcjonalnie: wybierz **opcje zaawansowane...**  

   | Parametr | Opis |
   | --- | --- |
   |  Użyj kwerendy natywnej |Po wybraniu sterownika ODBC NIE próbuje przekonwertować TSQL na HiveQL. Będziesz go używać tylko wtedy, gdy jesteś w 100% pewien, że przesyłasz czyste oświadczenia HiveQL. Podczas łączenia się z programem SQL Server lub usługą Azure SQL Database należy pozostawić ją niezaznaczoną. |
   |  Wiersze pobrane na blok |Podczas pobierania dużej liczby rekordów, dostrajanie tego parametru może być wymagane w celu zapewnienia optymalnej wydajności. |
   |  Domyślna długość kolumny ciągu, Długość kolumny binarnej, Skala kolumny dziesiętnej |Długości i precyzje typu danych mogą mieć wpływ na sposób zwracania danych. Powodują one nieprawidłowe informacje do zwrotu z powodu utraty precyzji i lub obcinania. |

    ![Zaawansowane opcje konfiguracji DSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Zaawansowane opcje konfiguracji DSN")

1. Wybierz **opcję Testuj,** aby przetestować źródło danych. Gdy źródło danych jest poprawnie skonfigurowane, wynik testu pokazuje **sukces!**

1. Wybierz **przycisk OK,** aby zamknąć okno Test.  

1. Wybierz **przycisk OK,** aby zamknąć okno **Instalatora dsn sterownika ODBC sterownika odbc firmy Microsoft.**  

1. Wybierz **przycisk OK,** aby zamknąć okno **Administrator źródła danych ODBC.**  

## <a name="import-data-into-excel-from-hdinsight"></a>Importowanie danych do programu Excel z usługi HDInsight

W poniższych krokach opisano sposób importowania danych z tabeli gałęzi do skoroszytu programu Excel przy użyciu źródła danych ODBC utworzonego w poprzedniej sekcji.

1. Otwórz nowy lub istniejący skoroszyt w programie Excel.

2. Na karcie **Dane** przejdź do pozycji **Pobierz dane** > **z innych źródeł** > **z odbc,** aby uruchomić okno **OdBC.**

    ![Kreator połączenia danych programu Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Kreator połączenia danych programu Excel")

3. Z listy rozwijanej wybierz nazwę źródła danych utworzoną w ostatniej sekcji, a następnie wybierz przycisk **OK**.

4. Przy pierwszym użyciu zostanie otwarte okno dialogowe **sterownika ODBC.** Wybierz **pozycję Windows** z lewego menu. Następnie wybierz pozycję **Połącz,** aby otworzyć okno **Nawigator.**

5. W **obszarze Navigator**przejdź do**pozycji Domyślna** > **liczba poułek** **HIVE** > , a następnie wybierz pozycję **Załaduj**. Trwa kilka chwil, zanim dane zostanie zaimportowane do programu Excel.

    ![Nawigator HDInsight Excel Hive ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "Nawigator HDInsight Excel Hive ODBC")

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się, jak używać sterownika Microsoft Hive ODBC do pobierania danych z usługi HDInsight do programu Excel. Podobnie można pobrać dane z usługi HDInsight do bazy danych SQL. Możliwe jest również przesyłanie danych do usługi HDInsight. Aby dowiedzieć się więcej, zobacz:

* [Wizualizuj dane gałęzi Apache za pomocą usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizuj dane gałęzi zapytań interaktywnych za pomocą usługi Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Łączenie programu Excel z apache Hadoop przy użyciu dodatku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Połącz się z usługą Azure HDInsight i uruchom kwerendy hive Apache przy użyciu narzędzi usługi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
