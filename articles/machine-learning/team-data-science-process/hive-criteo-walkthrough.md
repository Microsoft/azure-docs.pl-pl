---
title: Korzystanie z klastra usługi Azure HDInsight Hadoop na zestawie danych o pojemności 1 TB — proces nauki o danych zespołowych
description: Korzystanie z procesu nauki o danych zespołu w scenariuszu end-to-end wykorzystującym klaster programu HDInsight Hadoop do tworzenia i wdrażania modelu przy użyciu dużego (1 TB) publicznie dostępnego zestawu danych
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1198d3cc7ccc0013e7c894488027d8e162470247
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677601"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Proces nauki o danych zespołu w działaniu — przy użyciu klastra hadoop usługi Azure HDInsight w zestawie danych o pojemności 1 TB

W tym przewodniku pokazano, jak używać procesu nauki o danych zespołu w scenariuszu end-to-end z [klastra Usługi Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) do przechowywania, eksplorowania, inżyniera funkcji i w dół przykładowych danych z jednego z publicznie dostępnych zestawów danych [Criteo.](https://labs.criteo.com/downloads/download-terabyte-click-logs/) Używa usługi Azure Machine Learning do tworzenia modelu klasyfikacji binarnej na podstawie tych danych. Pokazuje również, jak opublikować jeden z tych modeli jako usługę sieci Web.

Istnieje również możliwość użycia notesu IPython do wykonywania zadań przedstawionych w tym instruktażu. Użytkownicy, którzy chcieliby wypróbować to podejście, powinni zapoznać się z [instruktażem Criteo przy użyciu tematu połączenia HIVE ODBC.](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Opis zestawu danych Criteo
Dane Criteo to zestaw danych przewidywania kliknięć, który jest 370 GB gzip skompresowanych plików TSV (~ 1,3 TB nieskompresowane), zawierający ponad 4,3 miliarda rekordów. Pochodzi z 24 dni od kliknięcia danych udostępnionych przez [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Dla wygody analityków danych, dane dostępne nam do eksperymentowania został rozpakowany.

Każdy rekord w tym zestawie danych zawiera 40 kolumn:

* pierwsza kolumna to kolumna etykiety, która wskazuje, czy użytkownik kliknie wartość **dodaną** (wartość 1), czy nie kliknie jednej kolumny (wartość 0)
* następne 13 kolumn jest numerycznych, a
* ostatnie 26 to kolumny kategoryczne

Kolumny są anonimizowane i używają serii wyliczonych nazw: "Col1" (dla kolumny etykiety) do "Col40" (dla ostatniej kolumny kategorycznej).

Oto fragment pierwszych 20 kolumn dwóch obserwacji (wierszy) z tego zestawu danych:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

Brakuje wartości w kolumnach liczbowych i kategorycznych w tym zestawie danych. Opisano prostą metodę obsługi brakujących wartości. Dodatkowe szczegóły danych są badane podczas przechowywania ich w tabelach hive.

**Definicja:** *Współczynnik klikalności (CTR):* Ta metryka jest procentem kliknięć w danych. W tym zestawie danych Criteo CTR wynosi około 3,3% lub 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Przykłady zadań przewidywania
W tym instruktażu rozwiązano dwa przykładowe problemy z przewidywaniem:

1. **Klasyfikacja binarna**: Przewiduje, czy użytkownik kliknął add:

   * Klasa 0: Bez kliknięcia
   * Klasa 1: Kliknij
2. **Regresja:** przewiduje prawdopodobieństwo kliknięcia reklamy na podstawie funkcji użytkownika.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Konfigurowanie klastra hdinsight Hadoop do nauki o danych
> [!NOTE]
> Ten krok jest zazwyczaj zadanie **administratora.**

Skonfiguruj środowisko analizy danych platformy Azure do tworzenia rozwiązań analizy predykcyjnej za pomocą klastrów HDInsight w trzech krokach:

1. [Utwórz konto magazynu:](../../storage/common/storage-account-create.md)To konto magazynu służy do przechowywania danych w usłudze Azure Blob Storage. Dane używane w klastrach HDInsight są przechowywane w tym miejscu.
2. [Dostosuj klastry usługi Azure HDInsight Hadoop do nauki o danych:](customize-hadoop-cluster.md)Ten krok tworzy klaster Usługi Azure HDInsight Hadoop z 64-bitowym programem Anaconda Python 2.7 zainstalowanym we wszystkich węzłach. Istnieją dwa ważne kroki (opisane w tym temacie) do wykonania podczas dostosowywania klastra HDInsight.

   * Połącz konto magazynu utworzone w kroku 1 z klastrem HDInsight podczas jego tworzenia. To konto magazynu służy do uzyskiwania dostępu do danych, które mogą być przetwarzane w klastrze.
   * Włącz dostęp zdalny do węzła głównego klastra po jego utworzeniu. Zapamiętaj poświadczenia dostępu zdalnego określone w tym miejscu (inne niż poświadczenia określone podczas tworzenia klastra): wykonaj następujące procedury.
3. [Tworzenie obszaru roboczego usługi Azure Machine Learning Studio (klasycznego):](../studio/create-workspace.md)ten obszar roboczy usługi Azure Machine Learning jest używany do tworzenia modeli uczenia maszynowego po początkowej eksploracji danych i próbkowaniu w dół w klastrze HDInsight.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Pozyskiwanie i korzystanie z danych ze źródła publicznego
Dostęp do zestawu danych [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) można uzyskać, klikając link, akceptując warunki użytkowania i podając nazwę. Migawka jest pokazana tutaj:

![Akceptowanie warunków Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Kliknij **przycisk Kontynuuj, aby pobrać,** aby dowiedzieć się więcej o zestawie danych i jego dostępności.

Dane znajdują się w lokalizacji magazynu obiektów wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/ [blob platformy Azure:](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) . "Wasb" odnosi się do lokalizacji usługi Azure Blob Storage.

1. Dane w tym magazynie obiektów blob platformy Azure składa się z trzech podfolderów rozpakowanych danych.

   1. Podfolder *raw/count/* zawiera pierwsze 21 dni danych\_- od\_dnia 00 do dnia 20
   2. Podfolder *raw/train/* składa się z jednego\_dnia danych, dzień 21
   3. Podfolder *raw/test/* składa się z dwóch\_dni danych,\_dnia 22 i dnia 23
2. Surowe dane gzip są również dostępne w głównym *folderze raw /* jako day_NN.gz, gdzie NN przechodzi od 00 do 23.

Alternatywne podejście do dostępu, eksplorowania i modelowania tych danych, które nie wymaga żadnych lokalnych pobierania jest wyjaśnione w dalszej części tego przewodnika podczas tworzenia tabel hive.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Zaloguj się do węzła główny klastra
Aby zalogować się do węzła główny klastra, użyj [witryny Azure Portal,](https://ms.portal.azure.com) aby zlokalizować klaster. Kliknij ikonę Słoń HDInsight po lewej stronie, a następnie kliknij dwukrotnie nazwę klastra. Przejdź do karty **Konfiguracja,** kliknij dwukrotnie ikonę POŁĄCZ u dołu strony i wprowadź poświadczenia dostępu zdalnego po wyświetleniu monitu, przekierując cię do węzła głównej klastra.

Oto jak wygląda typowy pierwszy login do węzła główny klastra:

![Zaloguj się do klastra](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Po lewej stronie znajduje się "Hadoop Command Line", który jest naszym koniem pociągm do eksploracji danych. Zwróć uwagę na dwa przydatne adresy URL - "Hadoop Yarn Status" i "Hadoop Name Node". Adres URL stanu przędzy pokazuje postęp zadania, a adres URL węzła nazwy zawiera szczegółowe informacje na temat konfiguracji klastra.

Teraz jesteś skonfigurowany i gotowy do rozpoczęcia pierwszej części przewodnika: eksploracja danych przy użyciu gałęzi i przygotowanie danych do usługi Azure Machine Learning.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a>Tworzenie bazy danych i tabel hive
Aby utworzyć tabele gałęzi dla naszego zestawu danych Criteo, otwórz ***wiersz polecenia Hadoop*** na pulpicie węzła głównego i wprowadź katalog Hive, wprowadzając polecenie

    cd %hive_home%\bin

> [!NOTE]
> Uruchom wszystkie polecenia hive w tym instruktażu z wiersza wiersza wiązki/katalogu. Automatycznie zajmuje się wszelkimi problemami ze ścieżką. Można użyć terminów "Wiersz katalogu hive", "Hive bin/ directory prompt" i "Hadoop Command Line" zamiennie.
>
> [!NOTE]
> Aby wykonać dowolną kwerendę hive, zawsze można użyć następujących poleceń:
>
>        cd %hive_home%\bin
>        hive

Po ponownym łączeniu hive pojawia się z "gałąź >"znak, wystarczy wyciąć i wkleić kwerendę, aby go wykonać.

Poniższy kod tworzy bazę danych "criteo", a następnie generuje cztery tabele:

* *tabela do generowania liczby* zbudowany\_w dniach\_00 do dnia 20,
* *tabelę do wykorzystania jako zestaw* danych\_pociągu zbudowany w dniu 21.
* dwie *tabele do użycia jako zestawy danych testowych* utworzone odpowiednio w dniu\_22 i\_23.

Podziel zestaw danych testu na dwie różne tabele, ponieważ jeden z dni jest świętem. Celem jest ustalenie, czy model może wykryć różnice między urlopem a nieaktualnością ze współczynnika klikalności.

Przykładowy&#95;[&#95;tworzenia&#95;&#95;bazy danych criteo&#95;i&#95;&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) jest wyświetlany tutaj dla wygody:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Wszystkie te tabele są zewnętrzne, dzięki czemu można wskazać ich lokalizacji usługi Azure Blob Storage (wasb).

**Istnieją dwa sposoby wykonywania dowolnej kwerendy hive:**

* **Korzystanie z wiersza polecenia Hive REPL**: Pierwszym z nich jest wydanie polecenia "gałąź" oraz skopiowanie i wklejenie kwerendy w wierszu polecenia Hive REPL:

        cd %hive_home%\bin
        hive

     Teraz w wierszu polecenia REPL wykonuje go wycinanie i wklejanie kwerendy.
* **Zapisywanie zapytań do pliku i wykonywanie polecenia:** Drugi jest zapisanie kwerend do pliku '.hql'[(przykładowy&#95;gałęzi&#95;utworzyć&#95;criteo&#95;bazy danych&#95;i&#95;tables.hql),](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)a następnie wydać następujące polecenie, aby wykonać kwerendę:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Potwierdź tworzenie bazy danych i tabeli
Następnie potwierdź utworzenie bazy danych za pomocą następującego polecenia z monitu o pojemnik/katalog hive:

        hive -e "show databases;"

Daje to:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Potwierdza to utworzenie nowej bazy danych "criteo".

Aby zobaczyć, jakie tabele zostały utworzone, po prostu wystaw polecenie w tym miejscu z wiersza katalogu Bin/katalogu Hive:

        hive -e "show tables in criteo;"

Następnie należy zobaczyć następujące dane wyjściowe:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="data-exploration-in-hive"></a><a name="exploration"></a>Eksploracja danych w uli
Teraz możesz przystąpić do eksploracji podstawowych danych w Hive. Należy rozpocząć od zliczania liczby przykładów w tabelach danych pociągu i testu.

### <a name="number-of-train-examples"></a>Liczba przykładów pociągów
Zawartość [przykładowej&#95;liczby&#95;&#95;&#95;tabeli pociągów&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) są pokazane tutaj:

        SELECT COUNT(*) FROM criteo.criteo_train;

Daje to:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Alternatywnie można również wydać następujące polecenie z wiersza wiersza katalogu Bin/Hive:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Liczba przykładów testów w dwóch testowych zbiorach danych
Teraz zliczaj liczbę przykładów w dwóch testowych zestawach danych. Zawartość [próbki&#95;&#95;&#95;&#95;badania criteo&#95;&#95;dnia&#95;22&#95;tabeli&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) są tutaj:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Daje to:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Jak zwykle można również wywołać skrypt z wiersza bin/ katalogu hive, wydając polecenie:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Na koniec należy sprawdzić liczbę przykładów testów w zestawie\_danych testu na podstawie dnia 23.

Polecenie w tym celu jest podobne do pokazanego (patrz [przykładowa&#95;liczba&#95;&#95;ula&#95;criteo&#95;test&#95;dnia&#95;23&#95;examples.hql):](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Daje to:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Rozkład etykiet w zestawie danych pociągu
Dystrybucja etykiet w zestawie danych pociągu jest interesująca. Aby to zobaczyć, pokaż zawartość [próbki&#95;&#95;&#95;etykiety criteo&#95;&#95;dystrybucji&#95;pociągu&#95;table.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql)

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Daje to rozkład etykiet:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Odsetek etykiet dodatnich wynosi około 3,3% (zgodnie z oryginalnym zestawem danych).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Rozkłady histogramów niektórych zmiennych liczbowych w zestawie danych pociągu
Możesz użyć natywnej funkcji "histogramu\_liczbowego" hive, aby dowiedzieć się, jak wygląda rozkład zmiennych liczbowych. Oto zawartość [próbki&#95;&#95;&#95;criteo&#95;histogram&#95;numeric.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql)

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Daje to następujące właściwości:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

Widok poprzeczne — rozbić połączenie w hive służy do tworzenia danych wyjściowych podobnych do SQL zamiast zwykłej listy. W tej tabeli pierwsza kolumna odpowiada środkowi pojemnika, a druga częstotliwością pojemnika.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Przybliżone percentyle niektórych zmiennych liczbowych w zestawie danych pociągu
Również interesujące dla zmiennych liczbowych jest obliczanie przybliżonych percentyli. Rodo na rodzimy "percentyl\_ok" robi to za nas. Zawartość [próbki&#95;&#95;&#95;criteo&#95;przybliżoną&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) to:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Daje to:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Rozkład percentyli jest ściśle związany z rozkładem histogramu dowolnej zmiennej numerycznej zwykle.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Znajdowanie liczby unikatowych wartości dla niektórych kolumn kategorii w zestawie danych pociągu
Kontynuując eksplorację danych, znajdź, dla niektórych kolumn kategorycznych, liczbę unikatowych wartości, które przyjmują. Aby to zrobić, pokaż zawartość [próbki&#95;ula&#95;criteo&#95;unikatowe wartości&#95;&#95;kategoricals.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql)

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Daje to:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Col15 ma unikalne wartości 19M! Używanie naiwnych technik, takich jak "kodowanie na gorąco" do kodowania takich zmiennych kategorycznych o wysokiej wymiarze, nie jest możliwe. W szczególności wyjaśniono i zademonstrowano potężną, solidną technikę o nazwie [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) do skutecznego rozwiązania tego problemu.

Na koniec przyjrzyj się liczbie unikatowych wartości dla niektórych innych kolumn kategorycznych. Zawartość [próbki&#95;&#95;&#95;criteo&#95;unikatowe wartości&#95;&#95;wielu&#95;kategoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) to:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Daje to:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Ponownie należy zauważyć, że z wyjątkiem Col20, wszystkie inne kolumny mają wiele unikatowych wartości.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Współsygnowanie par zmiennych kategorii w zestawie danych pociągu

Rozkłady liczb par zmiennych kategorii są również interesujące. Można to ustalić za pomocą kodu w [przykładzie&#95;ul&#95;criteo&#95;sparowane&#95;kategoryczne&#95;counts.hql:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql)

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Odwróć kolejność liczy przez ich wystąpienie i spojrzeć na górę 15 w tym przypadku. To daje nam:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a>Próbkowanie w dół zestawów danych dla usługi Azure Machine Learning
Po zbadaniu zestawów danych i zademonstrowaniu, jak wykonać ten typ eksploracji dla dowolnych zmiennych (w tym kombinacji), próbkowanie zestawów danych w dół, dzięki czemu można skompilować modele w usłudze Azure Machine Learning. Przypomnijmy, że głównym celem problemu jest: biorąc pod uwagę zestaw przykładowych atrybutów (wartości obiektów z Col2 - Col40), przewidzieć, czy Col1 jest 0 (bez kliknięcia) lub 1 (kliknięcie).

Aby pobrać próbkę zestawu danych pociągu i testu do 1% oryginalnego rozmiaru, należy użyć natywnej funkcji RAND(). Następny skrypt, [przykładowy&#95;ul&#95;criteo&#95;downsample&#95;pociągu&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) robi to dla zestawu danych pociągu:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Daje to:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

Przykładowy&#95;[ula&#95;criteo&#95;test&#95;&#95;&#95;dzień&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) robi to dla danych testowych, dzień\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Daje to:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Na koniec przykładowy&#95;[ula&#95;criteo&#95;test&#95;&#95;&#95;dzień&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) robi to dla danych testowych, dzień\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Daje to:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Dzięki temu możesz przystąpić do korzystania z naszych próbkowanych zestawów danych w dół i testowania do tworzenia modeli w usłudze Azure Machine Learning.

Istnieje końcowy ważny składnik przed przejściem do usługi Azure Machine Learning, która dotyczy tabeli zliczania. W następnej podsekcji tabela zliczania jest szczegółowo omówiona.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a>Krótka dyskusja na temat tabeli liczby
Jak widać, kilka zmiennych kategorycznych ma wysoką wymiarowość. W instruktażu przedstawiono zaawansowaną technikę o nazwie [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) do kodowania tych zmiennych w wydajny, niezawodny sposób. Więcej informacji na temat tej techniki znajduje się w linku.

>[!NOTE]
>W tym instruktażu fokus jest na użyciu tabel zliczania do tworzenia kompaktowych reprezentacji wysokiej wymiarowych elementów kategorii. Nie jest to jedyny sposób kodowania funkcji kategorycznych; Aby uzyskać więcej informacji na temat innych technik, zainteresowani użytkownicy mogą sprawdzić [jeden hot-kodowania](https://en.wikipedia.org/wiki/One-hot) i [funkcji mieszania](https://en.wikipedia.org/wiki/Feature_hashing).
>

Aby utworzyć tabele zliczania danych na podstawie danych zliczania, należy użyć danych w folderze raw/count. W sekcji modelowania użytkownicy są pokazane, jak tworzyć te tabele zliczania dla funkcji kategorycznych od podstaw lub alternatywnie do korzystania z wstępnie utworzonej tabeli zliczania dla ich eksploracji. W tym, co następuje, gdy "wstępnie utworzone tabele zliczania" są określane jako używane tabele zliczania, które zostały dostarczone. Szczegółowe instrukcje dotyczące uzyskiwania dostępu do tych tabel znajdują się w następnej sekcji.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a>Tworzenie modelu za pomocą usługi Azure Machine Learning
Nasz proces tworzenia modelu w usłudze Azure Machine Learning wykonuje następujące kroki:

1. [Pobierz dane z tabel hive do usługi Azure Machine Learning](#step1)
2. [Tworzenie eksperymentu: czyszczenie danych i uczynienie ich funkcją z tabelami zliczania](#step2)
3. [Tworzenie, szkolenie i ocena modelu](#step3)
4. [Ocena modelu](#step4)
5. [Publikowanie modelu jako usługi sieci Web](#step5)

Teraz możesz przystąpić do tworzenia modeli w usłudze Azure Machine Learning studio. Nasze dane próbkowane w dół są zapisywane jako tabele gałęzi w klastrze. Użyj modułu **Danych importu** usługi Azure Machine Learning, aby odczytać te dane. Poświadczenia dostępu do konta magazynu tego klastra są podane w dalszej części.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a>Krok 1: Pobierz dane z tabel hive do usługi Azure Machine Learning przy użyciu modułu Importuj dane i wybierz je dla eksperymentu uczenia maszynowego
Zacznij od wybrania **+NOWY** -> **EKSPERYMENT** -> **Pusty eksperyment**. Następnie w polu **Wyszukiwania** w lewym górnym rogu wyszukaj hasło "Importuj dane". Przeciągnij i upuść moduł **Importuj dane** na kanwie eksperymentu (środkowa część ekranu), aby użyć modułu do dostępu do danych.

Tak wygląda **importowanie danych** podczas uzyskiwania danych z tabeli Gałąź:

![Importowanie danych pobiera dane](./media/hive-criteo-walkthrough/i3zRaoj.png)

W przypadku modułu **Importuj dane** wartości parametrów, które są podane w grafice, są tylko przykładami wartości, które należy podać. Oto ogólne wskazówki dotyczące wypełniania parametrów ustawionych dla modułu **Importuj dane.**

1. Wybierz "Zapytanie gałęzi" dla **źródła danych**
2. W polu **kwerendy bazy danych hive** wystarczy prosty\_\_select *\_\_z <nazwa bazy danych.nazwa tabeli> .
3. **Hcatalog server URI**: Jeśli klaster jest "abc",\/to jest to po prostu: https: /abc.azurehdinsight.net
4. **Nazwa konta użytkownika Hadoop**: Nazwa użytkownika wybrana w momencie uruchomienia klastra. (NIE nazwa użytkownika dostępu zdalnego!)
5. **Hasło do konta użytkownika Hadoop**: Hasło do nazwy użytkownika wybranej w momencie uruchomienia klastra. (NIE hasło dostępu zdalnego!)
6. **Lokalizacja danych wyjściowych:** wybierz "Azure"
7. **Nazwa konta usługi Azure Storage:** konto magazynu skojarzone z klastrem
8. **Klucz konta usługi Azure Storage:** klucz konta magazynu skojarzonego z klastrem.
9. **Nazwa kontenera platformy Azure:** Jeśli nazwa klastra to "abc", zwykle jest to po prostu "abc".

Po zakończeniu **importu danych** podczas uzyskiwania danych (na module pojawi się zielony znacznik), zapisz te dane jako zestaw danych (z wybraną nazwą). Jak to wygląda:

![Importowanie danych zapisywania danych](./media/hive-criteo-walkthrough/oxM73Np.png)

Kliknij prawym przyciskiem myszy port wyjściowy modułu **Importuj dane.** Spowoduje to **wyświetlenie** opcji Zapisz jako zestaw danych i opcji **Wizualizuj.** Opcja **Wizualizuj,** jeśli zostanie kliknięta, wyświetla 100 wierszy danych wraz z prawym panelem, który jest przydatny w przypadku niektórych statystyk podsumowania. Aby zapisać dane, po prostu wybierz **pozycję Zapisz jako zestaw danych** i postępuj zgodnie z instrukcjami.

Aby wybrać zapisany zestaw danych do użycia w eksperymencie uczenia maszynowego, zlokalizuj zestawy danych za pomocą pola **Wyszukiwania** pokazanego na poniższym rysunku. Następnie po prostu wpisz nazwę, którą częściowo nadano zestawowi danych, aby uzyskać do niego dostęp, i przeciągnij zestaw danych na panel główny. Upuszczanie go na panel główny wybiera go do użycia w modelowaniu uczenia maszynowego.

![Przeciąganie zestawu danych na panel główny](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Zrób to zarówno dla pociągu, jak i zestawów danych testowych. Należy również pamiętać o używaniu nazwy bazy danych i nazw tabel, które zostały nadane w tym celu. Wartości użyte na rysunku służą wyłącznie do celów ilustracyjnych.**
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a>Krok 2: Tworzenie eksperymentu w usłudze Azure Machine Learning w celu przewidywania kliknięć / bez kliknięć
Nasz eksperyment usługi Azure Machine Learning Studio (klasyczny) wygląda następująco:

![Eksperyment uczenia maszynowego](./media/hive-criteo-walkthrough/xRpVfrY.png)

Teraz zbadaj kluczowe składniki tego eksperymentu. Najpierw przeciągnij zapisane zestawy danych pociągu i testuj je na kanwę eksperymentu.

#### <a name="clean-missing-data"></a>Czyszczenie brakujących danych
**Moduł Clean Missing Data** wykonuje to, co sugeruje jego nazwa: czyści brakujące dane w sposób, który może być określony przez użytkownika. Zajrzyj do tego modułu, aby to zobaczyć:

![Czyszczenie brakujących danych](./media/hive-criteo-walkthrough/0ycXod6.png)

W tym miejscu należy zastąpić wszystkie brakujące wartości wartością 0. Istnieją również inne opcje, które można zobaczyć, patrząc na listy rozwijane w module.

#### <a name="feature-engineering-on-the-data"></a>Inżynieria funkcji na danych
Mogą istnieć miliony unikatowych wartości dla niektórych kategorycznych funkcji dużych zestawów danych. Używanie naiwnych metod, takich jak kodowanie jedno-gorące do reprezentowania takich wysokowymiarowych funkcji kategorycznych, jest całkowicie niewykonalne. W tym przewodniku pokazano, jak używać funkcji zliczania przy użyciu wbudowanych modułów usługi Azure Machine Learning do generowania kompaktowych reprezentacji tych zmiennych kategorycznych o wysokiej wymiarach. Efektem końcowym jest mniejszy rozmiar modelu, krótszy czas szkolenia i metryki wydajności, które są porównywalne z przy użyciu innych technik.

##### <a name="building-counting-transforms"></a>Transformacje zliczania budynków
Aby utworzyć funkcje zliczania, należy użyć modułu **Transform inwentaryzacji kompilacji,** który jest dostępny w usłudze Azure Machine Learning. Moduł wygląda następująco:

![Kompilacja zliczanie](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![właściwości modułu Build Counting Transform moduł](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> W polu **Zlicz kolumny** wprowadź kolumny, na których chcesz wykonać liczbę. Zazwyczaj są to (jak wspomniano) kolumny kategoryczne o wysokim wymiarze. Pamiętaj, że zestaw danych Criteo zawiera 26 kolumn kategorycznych: od Col15 do Col40. Tutaj możesz liczyć na wszystkie z nich i dać swoje indeksy (od 15 do 40 oddzielone przecinkami, jak pokazano).
>

Aby użyć modułu w trybie MapReduce (odpowiednie dla dużych zestawów danych), należy uzyskać dostęp do klastra HDInsight Hadoop (ten używany do eksploracji funkcji może być ponownie użyty w tym celu), jak również) i jego poświadczenia. Poprzednie dane liczbowe ilustrują wygląd wypełnionych wartości (zastąp wartości podane do ilustracji wartościami istotnymi dla własnego przypadku użycia).

![Parametry modułu](./media/hive-criteo-walkthrough/05IqySf.png)

Na powyższej ilustracji pokazano, jak wprowadzić lokalizację wejściowego obiektu blob. Ta lokalizacja ma dane zarezerwowane dla tabel licznika budynków.

Po zakończeniu pracy tego modułu zapisz transformację na później, klikając prawym przyciskiem myszy moduł i wybierając opcję **Zapisz jako przekształć:**

![Opcja "Zapisz jako transformację"](./media/hive-criteo-walkthrough/IcVgvHR.png)

W naszej architekturze eksperymentu pokazanej powyżej zestaw danych "ytransform2" odpowiada dokładnie zapisanemu przekształceniu liczby. W pozostałej części tego eksperymentu zakłada się, że czytelnik użył build **inwentaryzacji transformowania** modułu na niektórych danych do generowania zliczeń, a następnie można użyć tych zliczeń do generowania funkcji liczenia w pociągu i testowych zestawów danych.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Wybieranie funkcji licznika do uwzględnienia jako części zestawów danych pociągu i testów
Gdy liczba transformacji gotowe, użytkownik może wybrać, jakie funkcje do uwzględnienia w ich zestawów danych pociągu i testowania przy użyciu **modułu Modyfikuj liczbę parametrów tabeli.** Dla kompletności, ten moduł jest pokazany tutaj. Ale w trosce o prostotę nie używaj go w naszym eksperymencie.

![Modyfikowanie parametrów tabeli zliczania](./media/hive-criteo-walkthrough/PfCHkVg.png)

W takim przypadku, jak widać, log-kursy mają być używane i wstecz off kolumny jest ignorowana. Można również ustawić parametry, takie jak próg kosza na śmieci, ile pseudo-wcześniejszych przykładów, aby dodać do wygładzania i czy używać dowolnego szumu laplacian, czy nie. Wszystkie te są zaawansowane funkcje i należy zauważyć, że wartości domyślne są dobrym punktem wyjścia dla użytkowników, którzy są nowicjuszami w tego typu generowania funkcji.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformacja danych przed wygenerowaniem funkcji zliczania
Teraz skupiamy się na ważnym punkcie na przekształcaniu naszych danych pociągu i testów przed faktycznym generowaniem funkcji zliczania. Istnieją dwa **moduły skryptu wykonywanie języka R** używane przed przekształcenie count jest stosowany do naszych danych.

![Wykonywanie modułów skryptu R](./media/hive-criteo-walkthrough/aF59wbc.png)

Oto pierwszy skrypt R:

![Pierwszy skrypt R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Ten skrypt R zmienia nazwę naszych kolumn na nazwy "Col1" na "Col40". Dzieje się tak, ponieważ transformacja liczby oczekuje nazw tego formatu.

Drugi skrypt R równoważy rozkład między klasami dodatnimi i ujemnymi (odpowiednio klasy 1 i 0) przez próbkowanie w dół klasy ujemnej. Skrypt języka R pokazuje, jak to zrobić:

![Drugi skrypt R](./media/hive-criteo-walkthrough/91wvcwN.png)

W tym prostym skrypcie\_\_R "współczynnik neg pos neg" służy do ustawiania kwoty równowagi między klasami dodatnimi i ujemnymi. Jest to ważne, ponieważ poprawa nierównowagi klasy zwykle ma korzyści wydajności dla problemów z klasyfikacją, gdzie rozkład klasy jest wypaczony (należy przypomnieć, że w tym przypadku masz 3,3% klasy dodatniej i 96,7% negatywnej klasy).

##### <a name="applying-the-count-transformation-on-our-data"></a>Stosowanie transformacji liczby na naszych danych
Na koniec można użyć **Zastosuj transformacja** moduł, aby zastosować transformacje liczby w naszych zestawów danych pociągu i testu. Ten moduł pobiera zapisane przekształcenie liczby jako jedno wejście i zestawów danych pociągu lub testu jako inne dane wejściowe i zwraca dane z funkcjami zliczania. Jest pokazany tutaj:

![Zastosuj moduł transformacji](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Fragment tego, jak wyglądają funkcje liczenia
To jest pouczające, aby zobaczyć, jak funkcje liczyć wyglądać w naszym przypadku. Oto fragment tego:

![Zliczanie funkcji](./media/hive-criteo-walkthrough/FO1nNfw.png)

Ten fragment pokazuje, że dla kolumn, na które liczysz, otrzymujesz liczby i kursy dziennika oprócz wszelkich istotnych backoffs.

Teraz możesz przystąpić do tworzenia modelu usługi Azure Machine Learning przy użyciu tych przekształconych zestawów danych. W następnej sekcji pokazano, jak można to zrobić.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a>Krok 3: Tworzenie, szkolenie i ocena modelu

#### <a name="choice-of-learner"></a>Wybór ucznia
Najpierw musisz wybrać ucznia. Użyj dwuklasowego drzewa decyzyjnego wzmocnionego jako nasz uczeń. Oto domyślne opcje dla tego ucznia:

![Dwie klasy wzmocnione parametry drzewa decyzyjnego](./media/hive-criteo-walkthrough/bH3ST2z.png)

W przypadku eksperymentu wybierz wartości domyślne. Wartości domyślne są znaczące i dobrym sposobem na szybkie wyniki badań wydajności. Można poprawić wydajność przez zamiatanie parametrów, jeśli zdecydujesz się raz masz linii bazowej.

#### <a name="train-the-model"></a>Uczenie modelu
Do szkolenia po prostu wywołać **moduł modelu pociągu.** Dwa dane wejściowe do niego są dwie klasy boosted uczenia się drzewa decyzyjnego i nasz zestaw danych pociągu. Jest to pokazane tutaj:

![Moduł modelu pociągu](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Ocena modelu
Po uzyskaniu przeszkolonego modelu, można przystąpić do punktacji na zestaw danych testu i ocenić jego wydajność. Należy to zrobić za pomocą modułu **Score Model** pokazanego na poniższym rysunku, wraz z modułem **Oceń model:**

![Moduł Score Model (Generowanie wyników przez model)](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a>Krok 4: Ocena modelu
Na koniec należy przeanalizować wydajność modelu. Zazwyczaj dla dwóch klas (binarnych) problemów klasyfikacji, dobrym środkiem jest AUC. Aby wizualizować tę krzywą, należy połączyć moduł **Score Model** z modułem **Oceń model.** Kliknięcie przycisku **Wizualizuj** w module **Ocena modelu** daje grafikę podobną do następującej:

![Ocena modelu modułu BDT](./media/hive-criteo-walkthrough/0Tl0cdg.png)

W binarnych (lub dwóch klas) problemów klasyfikacji, dobrą miarą dokładności przewidywania jest obszar pod krzywą (AUC). W poniższej sekcji przedstawiono nasze wyniki przy użyciu tego modelu w naszym zestawie danych testowych. Kliknij prawym przyciskiem myszy port wyjściowy modułu **Oceń model,** a następnie **wizualizuj**.

![Wizualizuj moduł Oceny modelu](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a>Krok 5: Publikowanie modelu jako usługi sieci Web
Możliwość publikowania modelu usługi Azure Machine Learning jako usług sieci web z minimalnym zamieszaniem jest cenną funkcją do udostępniania go powszechnie dostępne. Gdy to zrobisz, każdy może nawiązywać połączenia z usługą sieci web z danych wejściowych, które potrzebują prognoz, a usługa sieci web używa modelu do zwrócenia tych prognoz.

Najpierw zapisz nasz przeszkolony model jako obiekt Trained Model, klikając prawym przyciskiem myszy moduł **Modelu pociągu** i używając opcji **Zapisz jako model przeszkolony.**

Następnie utwórz porty wejściowe i wyjściowe dla naszej usługi internetowej:

* port wejściowy pobiera dane w takiej samej formie jak dane, które są potrzebne do
* port wyjściowy zwraca etykiety punktowane i skojarzone prawdopodobieństwa.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Zaznaczanie kilku wierszy danych dla portu wejściowego
Jest to wygodne, aby użyć zastosuj moduł **transformacji SQL,** aby wybrać tylko 10 wierszy, aby służyć jako dane portu wejściowego. Wybierz tylko te wiersze danych dla naszego portu wejściowego za pomocą kwerendy SQL pokazanej tutaj:

![Dane portu wejściowego](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Usługa sieci Web
Teraz możesz przystąpić do uruchomienia małego eksperymentu, który może służyć do publikowania naszej usługi internetowej.

#### <a name="generate-input-data-for-webservice"></a>Generowanie danych wejściowych dla usługi sieci Web
Jako krok zerowy, ponieważ tabela zliczania jest duża, należy wziąć kilka wierszy danych testowych i wygenerować dane wyjściowe z niego za pomocą funkcji count. To dane wyjściowe mogą służyć jako format danych wejściowych dla naszej usługi internetowej, jak pokazano poniżej:

![Tworzenie danych wejściowych BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> W przypadku formatu danych wejściowych należy użyć wyjścia modułu **Count Featurizer.** Po zakończeniu tego eksperymentu, zapisz dane wyjściowe z **Count Featurizer** modułu jako zestaw danych. Ten zestaw danych jest używany dla danych wejściowych w udziale sieci web.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Eksperyment oceniania publikowania usług sieci web
Po pierwsze, zasadnicza struktura jest **wynik model** moduł, który akceptuje nasz obiekt modelu przeszkolonych i kilka wierszy danych wejściowych, które zostały wygenerowane w poprzednich krokach przy użyciu **Count Featurizer** modułu. Użyj "Wybierz kolumny w zestawie danych", aby rzutować etykiety ocenione i prawdopodobieństwa wyniku.

![Wybieranie kolumn w zestawie danych](./media/hive-criteo-walkthrough/kRHrIbe.png)

Zwróć uwagę, jak **wybierz kolumny w** module Zestaw danych może służyć do "filtrowania" danych z zestawu danych. Zawartość jest wyświetlana tutaj:

![Filtrowanie za pomocą modułu Wybierz kolumny w zestawie danych](./media/hive-criteo-walkthrough/oVUJC9K.png)

Aby uzyskać niebieskie porty wejściowe i wyjściowe, wystarczy kliknąć **przycisk Przygotuj obsługę sieci Web** w prawym dolnym rogu. Uruchomienie tego eksperymentu pozwala nam również opublikować usługę internetową: kliknij ikonę **PUBLIKUJ USŁUGĘ SIECI WEB** w prawym dolnym rogu, pokazaną tutaj:

![Publikowanie usługi sieci Web](./media/hive-criteo-walkthrough/WO0nens.png)

Po opublikowaniu usługi webservice przekieruj ją do strony, która wygląda w ten sposób:

![Pulpit nawigacyjny usługi internetowej](./media/hive-criteo-walkthrough/YKzxAA5.png)

Zwróć uwagę na dwa łącza do usług internetowych po lewej stronie:

* Usługa **REQUEST/RESPONSE** (lub RRS) jest przeznaczona dla pojedynczych prognoz i jest tym, co zostało wykorzystane w tym warsztacie.
* Usługa **WYKONYWANIA PARTII** (BES) jest używana do prognozowania partii i wymaga, aby dane wejściowe używane do tworzenia prognoz znajdują się w usłudze Azure Blob Storage.

Kliknięcie na link **REQUEST/RESPONSE** przeniesie nas na stronę, która daje nam wstępnie puszkowany kod w języku C#, python i R. Ten kod może być wygodnie używany do wykonywania połączeń z usługą sieci web. Klucz interfejsu API na tej stronie musi być używany do uwierzytelniania.

Wygodnie jest skopiować ten kod języka python do nowej komórki w notesie IPython.

Oto segment kodu języka Python z poprawnym kluczem interfejsu API.

![Kod Języka Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

Domyślny klucz interfejsu API został zastąpiony kluczem interfejsu API naszej usługi sieci Web. Kliknięcie **przycisku Uruchom** w tej komórce w notesie IPython daje następującą odpowiedź:

![Odpowiedź IPython](./media/hive-criteo-walkthrough/KSxmia2.png)

Dla dwóch przykładów testów pytanych o w python skrypt JSON framework, można uzyskać z powrotem odpowiedzi w postaci "Ocenione etykiety, ocenione prawdopodobieństwa". W takim przypadku wybrano wartości domyślne, które zapewnia kod w puszkach (0 dla wszystkich kolumn liczbowych i ciąg "value" dla wszystkich kolumn kategorii).

Podsumowując, w naszym przewodniku pokazano, jak obsługiwać zestaw danych na dużą skalę przy użyciu usługi Azure Machine Learning. Rozpoczęto od terabajta danych, skonstruowano model przewidywania i wdrożono go jako usługę sieci web w chmurze.

