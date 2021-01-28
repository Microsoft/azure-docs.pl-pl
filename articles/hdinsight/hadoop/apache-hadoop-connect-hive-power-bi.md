---
title: Wizualizowanie Apache Hive danych za pomocą Power BI — usługa Azure HDInsight
description: Dowiedz się, jak za pomocą programu Microsoft Power BI wizualizować dane Hive przetwarzane przez usługę Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: a5732c2dc0a92bd5727eeff39a529630e45683d7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946676"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Wizualizowanie danych programu Apache Hive przy użyciu usługi Microsoft Power BI za pomocą interfejsu ODBC w usłudze Azure HDInsight

Dowiedz się, jak połączyć Power BI Desktop firmy Microsoft z usługą Azure HDInsight za pomocą ODBC i wizualizować Apache Hive danych.

> [!IMPORTANT]
> Można użyć sterownika programu Hive ODBC do zaimportowania za pośrednictwem uniwersalnego łącznika ODBC w Power BI Desktop. Nie jest to jednak zalecane w przypadku obciążeń analizy biznesowej, które podano nieinteraktywny charakter aparatu zapytań programu Hive. [Interaktywny łącznik zapytań usługi HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) i [Łącznik usługi HDInsight Spark](/power-bi/spark-on-hdinsight-with-direct-connect) są lepszymi wyborami dla ich wydajności.

W tym artykule załadujesz dane z tabeli programu `hivesampletable` Hive do Power BI. Tabela programu Hive zawiera dane użycia telefonu komórkowego. Następnie można wykreślić dane użycia na mapie światowej:

![Usługa HDInsight Power BI raport mapy](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Informacje dotyczą również nowego typu klastra [interakcyjnych zapytań](../interactive-query/apache-interactive-query-get-started.md) . Aby dowiedzieć się, jak nawiązać połączenie z interakcyjnym zapytaniem usługi HDInsight przy użyciu zapytania bezpośredniego, zobacz [Wizualizacja interaktywne zapytania Hive danych w usłudze Microsoft Power BI przy użyciu zapytania bezpośredniego w usłudze HDInsight Azure](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed przejściem do tego artykułu należy dysponować następującymi elementami:

* Klaster usługi HDInsight. Klaster może być klastrem usługi HDInsight z użyciem programu Hive lub nowo wydaną interaktywny klaster zapytań. Aby utworzyć klastry, zobacz [Tworzenie klastra](apache-hadoop-linux-tutorial-get-started.md).

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/). Kopię można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Tworzenie źródła danych ODBC usługi Hive

Zobacz [Tworzenie źródła danych ODBC Hive](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Ładowanie danych z usługi HDInsight

Tabela programu Hive **hivesampletable** zawiera wszystkie klastry usługi HDInsight.

1. Uruchom program Power BI Desktop.

1. W górnym menu Przejdź do **strony głównej**  >  **Pobierz dane**  >  **więcej...**.

    ![Usługa HDInsight Excel Power BI otworzyć dane](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. W oknie dialogowym **pobieranie danych** wybierz pozycję **inne** po lewej stronie, wybierz pozycję **ODBC** z prawej strony, a następnie wybierz pozycję **Połącz** u dołu.

1. W oknie dialogowym **z programu ODBC** wybierz nazwę źródła danych utworzoną w ostatniej sekcji z listy rozwijanej. Następnie wybierz przycisk **OK**.

1. Do pierwszego użycia zostanie otwarte okno dialogowe **sterownika ODBC** . Z menu po lewej stronie wybierz opcję **domyślne lub niestandardowe** . Następnie wybierz pozycję **Połącz** , aby otworzyć okno **Nawigator**.

1. W oknie dialogowym **Nawigator** rozwiń węzeł **ODBC > Hive > domyślnie** wybierz pozycję **hivesampletable**, a następnie wybierz pozycję **Załaduj**.

## <a name="visualize-data"></a>Wizualizowanie danych

Kontynuuj z ostatniej procedury.

1. W okienku wizualizacje wybierz pozycję **Mapa**, czyli ikonę globusa.

    ![Power BI dostosowuje Raport usługi HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. W okienku **pola** wybierz pozycję **Country** i **devicemake**. Dane są wyświetlane na mapie.

1. Rozwiń mapę.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób wizualizacji danych z usługi HDInsight przy użyciu Power BI.  Więcej informacji można znaleźć w następujących artykułach:

* [Łączenie programu Excel z usługą HDInsight przy użyciu Sterownik Microsoft Hive ODBC](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Łączenie programu Excel z Apache Hadoop przy użyciu Power Query](apache-hadoop-connect-excel-power-query.md).
* [Wizualizowanie interakcyjnych zapytań Apache Hive danych za pomocą usługi Microsoft Power BI przy użyciu zapytania bezpośredniego](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)