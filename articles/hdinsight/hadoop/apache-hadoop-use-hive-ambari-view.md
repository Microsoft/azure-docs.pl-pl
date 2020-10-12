---
title: Korzystanie z widoku programu Apache Ambari Hive z Apache Hadoop w usłudze Azure HDInsight
description: Dowiedz się, jak przesyłać zapytania programu Hive za pomocą widoku programu Hive z przeglądarki sieci Web. Widok Hive jest częścią Ambari internetowego interfejsu użytkownika dostarczonego z klastrem usługi HDInsight opartego na systemie Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: cc04a891bb32ede6c7bb72a339b728642cf343ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86207816"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Korzystanie z widoku programu Hive narzędzia Apache Ambari z usługą Apache Hadoop w usłudze HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak uruchamiać zapytania programu Hive za pomocą widoku Hive w programie Apache Ambari. Widok Hive umożliwia tworzenie, optymalizowanie i uruchamianie zapytań programu Hive z poziomu przeglądarki sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster usługi Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

1. Na [Azure Portal](https://portal.azure.com/)wybierz swój klaster.  Instrukcje można znaleźć w temacie Wyświetlanie [i wyświetlanie klastrów](../hdinsight-administer-use-portal-linux.md#showClusters) . Klaster zostanie otwarty w nowym widoku portalu.

1. W obszarze **pulpity nawigacyjne klastra**wybierz pozycję **widoki Ambari**. Po wyświetleniu monitu o uwierzytelnienie Użyj `admin` nazwy konta i hasła klastra, które zostały podane podczas tworzenia klastra. Możesz również przejść do `https://CLUSTERNAME.azurehdinsight.net/#/main/views` `CLUSTERNAME` okna przeglądarki, gdzie jest nazwą klastra.

1. Z listy widoki wybierz pozycję __Widok Hive__.

    ![Ambari wybierz widok Apache Hive Apache](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    Strona widok programu Hive jest podobna do następującej:

    ![Obraz arkusza zapytania dla widoku programu Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. Na karcie __zapytanie__ Wklej następujące instrukcje HiveQL do arkusza:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    Te instrukcje wykonują następujące czynności:

    |Instrukcja | Opis |
    |---|---|
    |USUŃ TABELĘ|Usuwa tabelę i plik danych, jeśli tabela już istnieje.|
    |TWORZENIE TABELI ZEWNĘTRZNEJ|Tworzy nową tabelę "zewnętrzna" w gałęzi Hive. Tabele zewnętrzne przechowują tylko definicję tabeli w programie Hive. Dane pozostaną w oryginalnej lokalizacji.|
    |FORMAT WIERSZA|Pokazuje, w jaki sposób dane są formatowane. W takim przypadku pola w każdym dzienniku są oddzielone spacją.|
    |PRZECHOWYWANE JAKO LOKALIZACJA TEXTFILE|Pokazuje, gdzie są przechowywane dane i czy są przechowywane jako tekst.|
    |SELECT|Wybiera liczbę wszystkich wierszy, w których kolumna T4 zawiera wartość [ERROR].|

   > [!IMPORTANT]  
   > Pozostaw __Domyślnie__wybraną __bazę danych__ . Przykłady w tym dokumencie wykorzystują domyślną bazę danych dołączoną do usługi HDInsight.

1. Aby uruchomić zapytanie, wybierz pozycję **Wykonaj** poniżej arkusza. Przycisk zmieni kolor na pomarańczowy, a tekst zostanie zmieniony na **zatrzymany**.

1. Po zakończeniu zapytania na karcie **wyniki** zostaną wyświetlone wyniki operacji. Następujący tekst jest wynikiem zapytania:

    ```output
    loglevel       count
    [ERROR]        3
    ```

    Karta **Dziennik** służy do wyświetlania informacji rejestrowania utworzonych przez zadanie.

   > [!TIP]  
   > Pobierz lub Zapisz wyniki z okna dialogowego rozwijanej **Akcje** na karcie  **wyniki** .

### <a name="visual-explain"></a>Wyjaśnienie wizualizacji

Aby wyświetlić wizualizację planu zapytania, wybierz kartę **wyjaśnienie wizualizacji** poniżej arkusza.

Widok **objaśnienia wizualnego** zapytania może pomóc w zrozumieniu przepływu złożonych zapytań.

### <a name="tez-ui"></a>Interfejs użytkownika tez

Aby wyświetlić interfejs użytkownika tez dla zapytania, wybierz kartę **interfejsu użytkownika tez** pod arkuszem.

> [!IMPORTANT]  
> Tez nie jest używany do rozpoznawania wszystkich zapytań. Można rozwiązać wiele zapytań bez używania tez.

## <a name="view-job-history"></a>Wyświetlanie historii zadań

Na karcie __zadania__ zostanie wyświetlona historia zapytań programu Hive.

![Historia karty Apache Hive widoku zadań](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Tabele bazy danych

Karta __tabele__ służy do pracy z tabelami w bazie danych programu Hive.

![Obraz karty Apache Hive tabele](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Zapisane zapytania

Na karcie **zapytanie** można opcjonalnie zapisywać zapytania. Po zapisaniu zapytania można użyć go ponownie z karty __zapisane zapytania__ .

![Karta Zapisane zapytania w widokach Apache Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Zapisane zapytania są przechowywane w domyślnym magazynie klastra. Zapisane zapytania można znaleźć pod ścieżką `/user/<username>/hive/scripts` . Są one przechowywane jako pliki w postaci zwykłego tekstu `.hql` .
>
> Jeśli usuniesz klaster, ale zachowasz magazyn, możesz pobrać zapytania za pomocą narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) lub Data Lake Storage Explorer (z witryny [Azure Portal](https://portal.azure.com)).

## <a name="user-defined-functions"></a>Funkcje zdefiniowane przez użytkownika

Gałąź można rozciągnąć za poorednictwem funkcji zdefiniowanych przez użytkownika (UDF). Użyj formatu UDF, aby zaimplementować funkcje lub logikę, które nie są łatwo modelowane w HiveQL.

Zadeklaruj i Zapisz zestaw UDF przy użyciu karty **UDF** w górnej części widoku programu Hive. Te UDF mogą być używane z **edytorem zapytań**.

![Wyświetlanie karty UDF w widoku Apache Hive](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Przycisk **Wstaw UDF** pojawia się u dołu **edytora zapytań**. Ten wpis wyświetla listę rozwijaną UDF zdefiniowaną w widoku Hive. Wybranie opcji UDF dodaje do zapytania instrukcje HiveQL, aby włączyć funkcję UDF.

Na przykład jeśli zdefiniowano UDF o następujących właściwościach:

* Nazwa zasobu: myudfs

* Ścieżka zasobu:/myudfs.jar

* Nazwa UDF: myawesomeudf

* Nazwa klasy UDF: com. myudfs. awesome

Za pomocą przycisku **Wstaw UDF** jest wyświetlany wpis o nazwie **myudfs**, z inną listą rozwijaną dla każdego elementu UDF zdefiniowanego dla tego zasobu. W tym przypadku jest to **myawesomeudf**. Po wybraniu tego wpisu na początku zapytania zostanie dodana następująca wartość:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Następnie można użyć w zapytaniu UDF. Na przykład `SELECT myawesomeudf(name) FROM people;`.

Aby uzyskać więcej informacji na temat używania UDF z usługą Hive w usłudze HDInsight, zobacz następujące artykuły:

* [Używanie języka Python z usługami Apache Hive i Apache chlewnej w usłudze HDInsight](python-udf-hdinsight.md)
* [Używanie formatu UDF języka Java z Apache Hive w usłudze HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Ustawienia programu Hive

Można zmienić różne ustawienia programu Hive, takie jak zmiana aparatu wykonywania dla programu Hive z tez (wartość domyślna) na MapReduce.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać ogólne informacje na temat usługi Hive w usłudze HDInsight:

* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Korzystanie z klienta Apache Beeline z usługą Apache Hive](apache-hadoop-use-hive-beeline.md)
