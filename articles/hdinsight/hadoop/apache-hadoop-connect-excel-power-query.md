---
title: Łączenie programu Excel z Apache Hadoop przy użyciu Power Query — usługa Azure HDInsight
description: Dowiedz się, jak korzystać ze składników analizy biznesowej i korzystać z Power Query dla programu Excel, aby uzyskiwać dostęp do danych przechowywanych w usłudze Hadoop w usłudze HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 13862e642c6a91fe6f3c635df2efde91672ecbad
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866815"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Łączenie programu Excel z Apache Hadoop przy użyciu Power Query

Jedną z kluczowych funkcji rozwiązania Microsoft Big Data jest integracja składników Microsoft Business Intelligence (BI) z klastrami Apache Hadoop w usłudze Azure HDInsight. Podstawowym przykładem jest możliwość łączenia programu Excel z kontem usługi Azure Storage zawierającym dane skojarzone z klastrem Hadoop przy użyciu dodatku Microsoft Power Query dla programu Excel. W tym artykule omówiono sposób konfigurowania i używania Power Query do wykonywania zapytań dotyczących danych skojarzonych z klastrem usługi Hadoop zarządzanym za pomocą usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Stacja robocza z systemem operacyjnym Windows 10, 7, Windows Server 2008 R2 lub nowszym.
* Microsoft 365 Apps dla przedsiębiorstw, Office 2016, Office 2013 Professional Plus, Excel 2013 Standalone lub Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Zainstaluj program Microsoft Power Query

Power Query może importować dane wyjściowe lub wygenerowane przez zadanie usługi Hadoop uruchomione w klastrze usługi HDInsight.

W programie Excel 2016 Power Query został zintegrowany z wstążką danych w sekcji Pobieranie & transformacji. W przypadku starszych wersji programu Excel Pobierz Microsoft Power Query dla programu Excel z [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?LinkID=286689) i zainstaluj go.

## <a name="import-hdinsight-data-into-excel"></a>Importowanie danych usługi HDInsight do programu Excel

Dodatek Power Query dla programu Excel ułatwia importowanie danych z klastra usługi HDInsight do programu Excel, w którym narzędzia analizy biznesowej, takie jak PowerPivot i Power Map, mogą służyć do sprawdzania, analizowania i prezentowania danych.

1. Uruchom program Excel.

1. Utwórz nowy pusty skoroszyt.

1. Wykonaj następujące kroki w oparciu o wersję programu Excel:

   * Excel 2016

     * Wybierz pozycję > **dane**  >  **Pobierz dane**  >  **z platformy Azure**  >  **z usługi Azure HDInsight (HDFS)**.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png" alt-text="HDI. PowerQuery. SelectHdiSource. 2016" border="true":::

   * Excel 2013/2010

     * Wybierz **Power Query**  >  **z platformy Azure**  >  **z Microsoft Azure HDInsight**.

       :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png" alt-text="HDI. PowerQuery. SelectHdiSource" border="true":::

       **Uwaga:** Jeśli nie widzisz menu **Power Query** , przejdź do pozycji   >  **Opcje** pliku  >  **Dodatki** i wybierz opcję **Dodatki COM** w polu rozwijanym **Zarządzaj** w dolnej części strony. Wybierz przycisk **Przejdź...** i sprawdź, czy pole dla dodatku dla Power Query dla programu Excel zostało zaznaczone.

       **Uwaga:** Power Query umożliwia również Importowanie danych z systemu plików HDFS przez wybranie **z innych źródeł**.

1. W oknie dialogowym **Azure HDInsight (HDFS)** w polu tekstowym **nazwa konta lub adres URL** wprowadź nazwę konta usługi Azure Blob Storage skojarzonego z klastrem. Następnie wybierz przycisk **OK**. To konto może być domyślnym kontem magazynu lub połączonym kontem magazynu.  Format to `https://StorageAccountName.blob.core.windows.net/` .

1. W polu **klucz konta** wprowadź klucz dla konta usługi BLOB Storage, a następnie wybierz pozycję **Połącz**. (Należy wprowadzić informacje o koncie tylko podczas pierwszego dostępu do tego magazynu).

1. W okienku **Nawigator** po lewej stronie edytora zapytań kliknij dwukrotnie nazwę kontenera magazynu obiektów BLOB skojarzoną z klastrem. Domyślnie nazwa kontenera jest taka sama jak nazwa klastra.

1. Znajdź **HiveSampleData.txt** w kolumnie **Nazwa** (ścieżka folderu to **... /Hive/Warehouse/hivesampletable/**), a następnie wybierz pozycję **Binary** po lewej stronie HiveSampleData.txt. HiveSampleData.txt jest dostarczany ze wszystkimi klastrami. Opcjonalnie możesz użyć własnego pliku.

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png" alt-text="Zaimportuj dane programu Excel w programie HDI" border="true":::

1. Jeśli chcesz, możesz zmienić nazwy kolumn. Gdy wszystko będzie gotowe, wybierz pozycję **zamknij & obciążenie**.  Dane zostały załadowane do skoroszytu:

    :::image type="content" source="./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png" alt-text="HDI zaimportowaną tabelę programu Excel dla dodatku PowerShell" border="true":::

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób używania Power Query do pobierania danych z usługi HDInsight do programu Excel. Podobnie można pobrać dane z usługi HDInsight do Azure SQL Database. Możliwe jest również przekazywanie danych do usługi HDInsight. Więcej informacji można znaleźć w następujących artykułach:

* [Wizualizuj Apache Hive dane za pomocą usługi Microsoft Power BI w usłudze Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Wizualizowanie interakcyjnych zapytań danych Hive za pomocą Power BI w usłudze Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Użyj programu Apache Zeppelin do uruchamiania zapytań Apache Hive w usłudze Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z usługą HDInsight przy użyciu Sterownik Microsoft Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Nawiązywanie połączenia z usługą Azure HDInsight i uruchamianie zapytań Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Użyj narzędzia Azure HDInsight do Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do usługi HDInsight](./../hdinsight-upload-data.md).
