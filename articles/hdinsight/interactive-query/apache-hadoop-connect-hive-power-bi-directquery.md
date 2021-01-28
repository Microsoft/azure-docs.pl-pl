---
title: Wyświetlanie interakcyjnych zapytań dotyczących danych Hive przy użyciu Power BI w usłudze Azure HDInsight
description: Używanie Power BI firmy Microsoft do wizualizacji interakcyjnych zapytań dotyczących danych Hive z usługi Azure HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 7f249bb0e81bf3a371b8743a304ef49baffaed7a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941384"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Wizualizowanie interakcyjnych zapytań Apache Hive danych za pomocą usługi Microsoft Power BI przy użyciu zapytania bezpośredniego w usłudze HDInsight

W tym artykule opisano sposób łączenia Power BI firmy Microsoft z klastrami zapytań interakcyjnych usługi Azure HDInsight i wizualizacji danych Apache Hive przy użyciu zapytania bezpośredniego. Podany przykład ładuje dane z `hivesampletable` tabeli programu Hive do Power BI. `hivesampletable`Tabela programu Hive zawiera dane użycia telefonu komórkowego. Następnie można wykreślić dane użycia na mapie światowej:

![Usługa HDInsight Power BI raport mapy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Można użyć [sterownika Apache Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) do zaimportowania za pośrednictwem uniwersalnego łącznika odbc w Power BI Desktop. Nie jest to jednak zalecane w przypadku obciążeń analizy biznesowej, które podano nieinteraktywny charakter aparatu zapytań programu Hive. Łącznik [interakcyjnych zapytań usługi HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) i [Łącznik Apache Spark usługi HDInsight](/power-bi/spark-on-hdinsight-with-direct-connect) są lepszymi opcjami dotyczącymi wydajności.

## <a name="prerequisites"></a>Wymagania wstępne
Przed przejściem do tego artykułu należy dysponować następującymi elementami:

* **Klaster usługi HDInsight**. Klaster może być klastrem usługi HDInsight z Apache Hive lub nowo wydaną interaktywny klaster zapytań. Aby utworzyć klastry, zobacz [Tworzenie klastra](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Kopię można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Ładowanie danych z usługi HDInsight

`hivesampletable`Tabela programu Hive zawiera wszystkie klastry usługi HDInsight.

1. Uruchom program Power BI Desktop.

2. Na pasku menu Przejdź do **strony głównej**  >  **Pobierz dane**  >  **więcej...**.

    ![Usługa HDInsight Power BI uzyskać więcej danych](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. W oknie **pobieranie danych** wprowadź w polu wyszukiwania pozycję **HDInsight** .  

4. W wynikach wyszukiwania wybierz pozycję **zapytanie interaktywne usługi HDInsight**, a następnie wybierz pozycję **Połącz**.  Jeśli nie widzisz **interakcyjnego zapytania HDInsight**, musisz zaktualizować Power BI Desktop do najnowszej wersji.

5. Wybierz pozycję **Kontynuuj** , aby zamknąć okno dialogowe **łączenie z usługą innej firmy** .

6. W oknie **zapytanie interaktywne usługi HDInsight** wprowadź następujące informacje, a następnie wybierz przycisk **OK**:

    |Właściwość | Wartość |
    |---|---|
    |Serwer |Wprowadź nazwę klastra, na przykład *myiqcluster.azurehdinsight.NET*.|
    |baza danych |Wprowadź **wartość domyślną** dla tego artykułu.|
    |Tryb łączności danych |Wybierz opcję **DirectQuery** dla tego artykułu.|

    ![Zapytanie interaktywne usługi HDInsight Power BI połączenia DirectQuery](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Wprowadź poświadczenia HTTP, a następnie wybierz pozycję **Połącz**. Domyślna nazwa użytkownika to **admin**.

8. W oknie **Nawigator** w okienku po lewej stronie wybierz pozycję **hivesampletale**.

9. Wybierz pozycję **Załaduj** z okna głównego.

    ![Zapytanie interaktywne usługi HDInsight Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Wizualizowanie danych na mapie

Kontynuuj z ostatniej procedury.

1. W okienku wizualizacje wybierz pozycję **Mapa**, a następnie ikonę globusa. Mapa ogólna zostanie wyświetlona w oknie głównym.

    ![Power BI dostosowuje Raport usługi HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. W okienku pola wybierz pozycję **Country** i **devicemake**. Mapa świata z punktami danych pojawia się w oknie głównym po kilku chwilach.

3. Rozwiń mapę.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób wizualizacji danych z usługi HDInsight przy użyciu programu Microsoft Power BI.  Aby uzyskać więcej informacji na temat wizualizacji danych, zobacz następujące artykuły:

* [Wizualizuj Apache Hive dane przy użyciu programu Microsoft Power BI za pomocą ODBC w usłudze Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Użyj programu Apache Zeppelin do uruchamiania zapytań Apache Hive w usłudze Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Łączenie programu Excel z usługą HDInsight przy użyciu Sterownik Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z Apache Hadoop przy użyciu Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Nawiązywanie połączenia z usługą Azure HDInsight i uruchamianie zapytań Apache Hive przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Użyj narzędzia Azure HDInsight do Visual Studio Code](../hdinsight-for-vscode.md).
* [Przekazywanie danych do usługi HDInsight](./../hdinsight-upload-data.md).