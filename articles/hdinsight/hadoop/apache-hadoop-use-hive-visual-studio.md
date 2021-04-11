---
title: Apache Hive & Data Lake Tools for Visual Studio — Azure HDInsight
description: Dowiedz się, jak za pomocą narzędzi Data Lake Tools for Visual Studio uruchamiać zapytania Apache Hive za pomocą Apache Hadoop w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 4512c9d9fdb66713ba24fbf30278e5d5dbb2ae23
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863755"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Uruchamianie zapytań Apache Hive przy użyciu wtyczki Data Lake Tools for Visual Studio

Dowiedz się, jak Apache Hive wykonywać zapytania za pomocą narzędzi Data Lake Tools for Visual Studio. Narzędzia Data Lake umożliwiają łatwe tworzenie, przesyłanie i monitorowanie zapytań Hive w celu Apache Hadoop w usłudze Azure HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Aby uzyskać informacje na temat tworzenia tego elementu, zobacz [Tworzenie klastra Apache Hadoop w usłudze Azure HDInsight przy użyciu szablonu Menedżer zasobów](./apache-hadoop-linux-tutorial-get-started.md).

* [Program Visual Studio](https://visualstudio.microsoft.com/vs/). Kroki opisane w tym artykule korzystają z programu Visual Studio 2019.

* Narzędzia HDInsight Tools for Visual Studio lub Azure Data Lake Tools for Visual Studio. Aby uzyskać informacje na temat instalowania i konfigurowania narzędzi, zobacz [Install Data Lake Tools for Visual Studio](apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="run-apache-hive-queries-using-the-visual-studio"></a>Uruchamianie zapytań Apache Hive przy użyciu programu Visual Studio

Masz dwie opcje umożliwiające utworzenie i uruchomienie zapytań Hive:

* Utwórz zapytania ad hoc.
* Tworzenie aplikacji Hive.

### <a name="create-an-ad-hoc-hive-query"></a>Tworzenie zapytania Hive ad hoc

Zapytania ad hoc mogą być wykonywane w trybie **wsadowym** lub **interaktywnym** .

1. Uruchom **program Visual Studio** i wybierz pozycję **Kontynuuj bez kodu**.

2. W **Eksplorator serwera** kliknij prawym przyciskiem myszy pozycję **Azure**, wybierz pozycję **Połącz z Microsoft Azure subskrypcją...**, a następnie ukończ proces logowania.

3. Rozwiń węzeł **HDInsight**, kliknij prawym przyciskiem myszy klaster, w którym chcesz uruchomić zapytanie, a następnie wybierz polecenie **Napisz zapytanie Hive**.

4. Wprowadź następujące zapytanie programu Hive:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Wybierz pozycję **Wykonaj**. Tryb wykonywania jest domyślnie **interaktywny**.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png" alt-text="Wykonywanie interakcyjnego zapytania Hive, Visual Studio" border="true":::

6. Aby uruchomić to samo zapytanie w trybie **wsadowym** , przełącz listę rozwijaną **interaktywnie** do **Batch**. Przycisk wykonywania zmienia się z **Execute** na **Submit**.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/visual-studio-batch-query.png" alt-text="Przesyłanie zapytania programu Hive w usłudze Batch, Visual Studio" border="true":::

    Edytor Hive obsługuje funkcję IntelliSense. Narzędzia Data Lake Tools for Visual Studio obsługują ładowanie zdalnych metadanych podczas edycji skryptu Hive. Na przykład, jeśli wpiszesz `SELECT * FROM` , IntelliSense wyświetla wszystkie sugerowane nazwy tabel. Po określeniu nazwy tabeli funkcja IntelliSense wyświetla nazwy kolumn. Narzędzia obsługują większość instrukcji DML programu Hive, podzapytań i wbudowanych sterowników UDF. Funkcja IntelliSense zasugeruje tylko metadane klastra zaznaczonego na pasku narzędzi usługi HDInsight.

7. Na pasku narzędzi zapytania (obszar poniżej karty zapytania i powyżej tekstu zapytania) wybierz pozycję **Prześlij** lub wybierz rozwijaną strzałkę obok pola **Prześlij** i wybierz pozycję **Zaawansowane** z listy rozwijanej. Jeśli wybierzesz ostatnią opcję,

8. W przypadku wybrania opcji przesyłania zaawansowanego Skonfiguruj **nazwę zadania**, **argumenty**, **dodatkowe konfiguracje** i **katalog stanu** w oknie dialogowym **Prześlij skrypt** . Następnie wybierz pozycję **Prześlij**.

    :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/vs-tools-submit-jobs-advanced.png" alt-text="Okno dialogowe przesyłania skryptu, zapytanie Hive usługi HDInsight Hadoop" border="true":::

### <a name="create-a-hive-application"></a>Tworzenie aplikacji Hive

Aby uruchomić zapytanie programu Hive przez utworzenie aplikacji Hive, wykonaj następujące kroki:

1. Otwórz **program Visual Studio**.

2. W oknie **uruchamiania** wybierz pozycję **Utwórz nowy projekt**.

3. W oknie **Tworzenie nowego projektu** w polu **Wyszukaj szablony** wprowadź wartość *Hive*. Następnie wybierz pozycję **aplikacja Hive** i wybierz pozycję **dalej**.

4. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu**, wybierz lub Utwórz **lokalizację** dla nowego projektu, a następnie wybierz pozycję **Utwórz**.

5. Otwórz plik **Script. HQL** , który jest tworzony przy użyciu tego projektu, i wklej go w następujących instrukcjach HiveQL:

    ```hql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Te instrukcje wykonują następujące czynności:

    * `DROP TABLE`: Usuwa tabelę, jeśli istnieje.

    * `CREATE EXTERNAL TABLE`: Tworzy nową tabelę "External" w gałęzi. Tabele zewnętrzne przechowują wyłącznie definicję tabeli w programie Hive. (Dane pozostaną w oryginalnej lokalizacji).

        > [!NOTE]  
        > Tabele zewnętrzne powinny być używane, gdy oczekuje się, że dane podstawowe mają być aktualizowane przez zewnętrzne źródło, takie jak zadanie MapReduce lub usługa platformy Azure.
        >
        > Porzucenie tabeli **zewnętrznej nie powoduje usunięcia danych** , tylko definicji tabeli.

    * `ROW FORMAT`: Informuje Hive o sposobie formatowania danych. W takim przypadku pola w każdym dzienniku są oddzielone spacją.

    * `STORED AS TEXTFILE LOCATION`: Instruuje gałąź, że dane są przechowywane w *przykładowym katalogu danych* i są przechowywane jako tekst.

    * `SELECT`: Wybiera liczbę wszystkich wierszy, w których kolumna `t4` zawiera wartość `[ERROR]` . Ta instrukcja zwraca wartość `3` , ponieważ trzy wiersze zawierają tę wartość.

    * `INPUT__FILE__NAME LIKE '%.log'`: Instruuje gałąź, aby zwracała tylko dane z plików kończących się na. log. Ta klauzula ogranicza wyszukiwanie do pliku *Sample. log* , który zawiera dane.

6. Na pasku narzędzi pliku zapytania (który ma podobny wygląd do paska narzędzi zapytania ad hoc) wybierz klaster usługi HDInsight, który ma być używany dla tego zapytania. Następnie zmień **interaktywnie** na **Batch** (w razie potrzeby) i wybierz pozycję **Prześlij** , aby uruchomić instrukcje jako zadanie Hive.

   Zostanie wyświetlone **Podsumowanie zadania programu Hive** zawierające informacje o uruchomionym zadaniu. Użyj linku **Odśwież** , aby odświeżyć informacje o zadaniu do momentu, aż **stan zadania** zmieni się na **zakończone**.

   :::image type="content" source="./media/apache-hadoop-use-hive-visual-studio/hdinsight-job-summary.png" alt-text="Ukończono podsumowanie zadania Hive, aplikacja Hive, program Visual Studio" border="true":::

7. Wybierz pozycję **dane wyjściowe zadania** , aby wyświetlić dane wyjściowe tego zadania. Zostanie wyświetlona `[ERROR] 3` , która jest wartością zwracaną przez to zapytanie.

### <a name="additional-example"></a>Dodatkowy przykład

Poniższy przykład opiera się na `log4jLogs` tabeli utworzonej w poprzedniej procedurze, [Tworzenie aplikacji Hive](#create-a-hive-application).

1. W **Eksplorator serwera** kliknij prawym przyciskiem myszy klaster i wybierz polecenie **Napisz zapytanie Hive**.

2. Wprowadź następujące zapytanie programu Hive:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Te instrukcje wykonują następujące czynności:

    * `CREATE TABLE IF NOT EXISTS`: Tworzy tabelę, jeśli jeszcze nie istnieje. Ponieważ `EXTERNAL` słowo kluczowe nie jest używane, ta instrukcja tworzy tabelę wewnętrzną. Tabele wewnętrzne są przechowywane w magazynie danych programu Hive i są zarządzane przez program Hive.

        > [!NOTE]  
        > W przeciwieństwie do `EXTERNAL` tabel, upuszczenie wewnętrznej tabeli spowoduje również usunięcie danych źródłowych.

    * `STORED AS ORC`: Przechowuje dane w formacie Orc ( *zoptymalizowany wiersz kolumnowy* ). ORC to wysoce zoptymalizowany i wydajny format służący do przechowywania danych programu Hive.

    * `INSERT OVERWRITE ... SELECT`: Wybiera wiersze z `log4jLogs` tabeli, która zawiera `[ERROR]` , a następnie wstawia dane do `errorLogs` tabeli.

3. W razie potrzeby zmień **interaktywną** pozycję **Batch** , a następnie wybierz pozycję **Prześlij**.

4. Aby sprawdzić, czy zadanie utworzyło tabelę, przejdź do **Eksplorator serwera** i rozwiń węzeł **Azure**  >  **HDInsight**. Rozwiń klaster usługi HDInsight, a następnie rozwiń węzeł **bazy danych Hive**  >  **default**. Zostanie wyświetlona tabela **errorLogs** i tabela **log4jLogs** .

## <a name="next-steps"></a>Następne kroki

Jak widać, narzędzia HDInsight Tools for Visual Studio zapewniają łatwy sposób pracy z zapytaniami programu Hive w usłudze HDInsight.

* Aby uzyskać ogólne informacje o usłudze Hive w usłudze HDInsight, zobacz [co to jest Apache Hive i HiveQL w usłudze Azure HDInsight?](hdinsight-use-hive.md)

* Aby uzyskać informacje o innych sposobach pracy z usługą Hadoop w usłudze HDInsight, zobacz [Korzystanie z MapReduce w Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)

* Aby uzyskać więcej informacji na temat narzędzi HDInsight Tools for Visual Studio, zobacz[Używanie narzędzi Data Lake Tools for Visual Studio do nawiązywania połączenia z usługą Azure HDInsight i uruchamiania zapytań Apache Hive](apache-hadoop-visual-studio-tools-get-started.md)
