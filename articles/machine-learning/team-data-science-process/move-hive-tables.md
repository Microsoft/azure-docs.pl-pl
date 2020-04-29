---
title: Tworzenie tabel programu Hive i ładowanie danych z usługi BLOB Storage — proces nauki danych zespołu
description: Zapytania Hive umożliwiają tworzenie tabel programu Hive i ładowanie danych z usługi Azure Blob Storage. Partycjonowanie tabel programu Hive i użycie formatu zoptymalizowanych wierszy kolumnowy (ORC) w celu zwiększenia wydajności zapytań.
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
ms.openlocfilehash: 625d9d5c5ecf095d4acbff625754b2065f184536
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79251663"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Tworzenie tabel programu Hive i ładowanie danych z usługi Azure Blob Storage

W tym artykule przedstawiono ogólne zapytania programu Hive, które tworzą tabele Hive i ładują dane z usługi Azure Blob Storage. Niektóre wskazówki są również udostępniane na partycjonowanie tabel programu Hive oraz przy użyciu formatu zoptymalizowanych wierszy kolumnowy (ORC) w celu zwiększenia wydajności zapytań.

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że masz:

* Utworzono konto usługi Azure Storage. Jeśli potrzebujesz instrukcji, zobacz [Informacje o kontach usługi Azure Storage](../../storage/common/storage-introduction.md).
* Zainicjowano obsługę niestandardowego klastra Hadoop z usługą HDInsight.  Jeśli potrzebujesz instrukcji, zobacz [Konfigurowanie klastrów w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Włączono dostęp zdalny do klastra, zalogowano się i otwarto konsolę wiersza polecenia usługi Hadoop. Jeśli potrzebujesz instrukcji, zobacz [Zarządzanie klastrami Apache Hadoop](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Przekazywanie danych do usługi Azure Blob Storage
Jeśli utworzono maszynę wirtualną platformy Azure, postępując zgodnie z instrukcjami podanymi w temacie [Konfigurowanie maszyny wirtualnej platformy Azure na potrzeby zaawansowanej analizy](../../machine-learning/data-science-virtual-machine/overview.md), ten plik skryptu powinien zostać pobrany do katalogu *\\C:\>\\Users\\\\\<Name Documents, User Science scripts* na maszynie wirtualnej. Te zapytania Hive wymagają tylko podania schematu danych i konfiguracji magazynu obiektów blob platformy Azure w odpowiednich polach, które mają być gotowe do przesłania.

Załóżmy, że dane dla tabel programu Hive znajdują się w **nieskompresowanym** formacie tabelarycznym i że dane zostały przekazane do domyślnego (lub do dodatkowego) kontenera konta magazynu używanego przez klaster usługi Hadoop.

Jeśli chcesz mieć na celu podwyższenie poziomu **danych o podróży z NYC taksówkami**, musisz:

* **Pobierz** pliki danych o podróży z 24 [NYC taksówkami](https://www.andresmh.com/nyctaxitrips) (12 plików podróży i 12-tańszych plików),
* **Rozpakuj** wszystkie pliki do plików CSV, a następnie
* **Przekaż** je do domyślnych (lub odpowiednich kontenerów) konta usługi Azure Storage; Opcje tego konta są wyświetlane w temacie [Korzystanie z usługi Azure Storage przy użyciu klastrów usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) . Proces przekazywania plików CSV do kontenera domyślnego na koncie magazynu można znaleźć na tej [stronie](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Jak przesłać zapytania Hive
Zapytania Hive można przesyłać przy użyciu:

* [Przesyłanie zapytań programu Hive przy użyciu wiersza polecenia usługi Hadoop w węzła głównego klastra Hadoop](#headnode)
* [Przesyłanie zapytań programu Hive przy użyciu edytora Hive](#hive-editor)
* [Przesyłanie zapytań programu Hive za pomocą poleceń Azure PowerShell](#ps)

Zapytania Hive są podobne do języka SQL. Jeśli znasz program SQL, możesz znaleźć [gałąź dla użytkowników SQL ściągawka arkusz](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) .

Podczas przesyłania zapytania programu Hive można także kontrolować miejsce docelowe danych wyjściowych z zapytań Hive, niezależnie od tego, czy znajdują się na ekranie, czy do pliku lokalnego w węźle głównym lub w obiekcie blob platformy Azure.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Przesyłanie zapytań programu Hive przy użyciu wiersza polecenia usługi Hadoop w węzła głównego klastra Hadoop
Jeśli zapytanie programu Hive jest złożone, przesłanie go bezpośrednio w węźle głównym klastra Hadoop zazwyczaj prowadzi do szybszego wyłączania niż przesyłanie go za pomocą edytora Hive lub skryptów Azure PowerShell.

Zaloguj się do węzła głównego klastra Hadoop, Otwórz wiersz polecenia Hadoop na pulpicie węzła głównego, a następnie wprowadź polecenie `cd %hive_home%\bin`.

Istnieją trzy sposoby przesyłania zapytań programu Hive w wierszu polecenia usługi Hadoop:

* wprost
* Używanie plików ". HQL"
* za pomocą konsoli poleceń programu Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Prześlij zapytania programu Hive bezpośrednio w wierszu polecenia usługi Hadoop.
Można uruchomić polecenie takie jak `hive -e "<your hive query>;` , aby przesłać proste zapytania Hive bezpośrednio w wierszu polecenia usługi Hadoop. Oto przykład, w którym czerwona Skrzynka zawiera polecenie, które przesyła zapytanie Hive, a zielony prostokąt zawiera dane wyjściowe zapytania programu Hive.

![Polecenie przesłania zapytania Hive z danymi wyjściowymi z zapytania programu Hive](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Prześlij zapytania programu Hive w plikach ". HQL"
Gdy zapytanie programu Hive jest bardziej skomplikowane i ma wiele wierszy, edytowanie zapytań w wierszu polecenia lub w konsoli poleceń Hive nie jest praktyczne. Alternatywą jest użycie edytora tekstu w węźle głównym klastra Hadoop w celu zapisania zapytań programu Hive w pliku "HQL" w katalogu lokalnym węzła głównego. Następnie zapytanie Hive w pliku ". HQL" można przesłać przy użyciu `-f` argumentu w następujący sposób:

    hive -f "<path to the '.hql' file>"

![Zapytanie programu Hive w pliku ". HQL"](./media/move-hive-tables/run-hive-queries-3.png)

**Pomiń drukowanie ekranu stanu postępu zapytań programu Hive**

Domyślnie po przesłaniu zapytania programu Hive w wierszu polecenia usługi Hadoop postęp zadania mapy/zmniejszania jest drukowany na ekranie. Aby pominąć drukowanie ekranu mapy/zmniejszyć postęp zadania, można użyć argumentu `-S` ("S" w Wielkiej litery) w wierszu polecenia w następujący sposób:

    hive -S -f "<path to the '.hql' file>"
    hive -S -e "<Hive queries>"

#### <a name="submit-hive-queries-in-hive-command-console"></a>Prześlij zapytania programu Hive w konsoli poleceń Hive.
Możesz również najpierw wprowadzić konsolę poleceń Hive, uruchamiając polecenie `hive` w wierszu polecenia usługi Hadoop, a następnie przesyłając zapytania programu Hive w konsoli poleceń Hive. Oto przykład. W tym przykładzie dwa czerwone pola podświetlą polecenia służące do wprowadzania konsoli poleceń Hive i zapytanie Hive przesłane w konsoli poleceń Hive odpowiednio. Zielony prostokąt wyróżni dane wyjściowe zapytania programu Hive.

![Otwórz konsolę poleceń Hive i wprowadź polecenie, Wyświetl dane wyjściowe zapytania Hive](./media/move-hive-tables/run-hive-queries-2.png)

Poprzednie przykłady bezpośrednio wyprowadzają wyniki zapytania programu Hive na ekranie. Dane wyjściowe można również zapisać w pliku lokalnym w węźle głównym lub w obiekcie blob platformy Azure. Następnie możesz użyć innych narzędzi, aby przeanalizować dane wyjściowe zapytań programu Hive.

**Wyniki zapytania programu Hive do pliku lokalnego.**
Aby wyprowadzić wyniki zapytania programu Hive do katalogu lokalnego w węźle głównym, należy przesłać zapytanie programu Hive w wierszu polecenia usługi Hadoop w następujący sposób:

    hive -e "<hive query>" > <local path in the head node>

W poniższym przykładzie dane wyjściowe zapytania programu Hive są zapisywane w pliku `hivequeryoutput.txt` w katalogu. `C:\apps\temp`

![Dane wyjściowe zapytania programu Hive](./media/move-hive-tables/output-hive-results-1.png)

**Wyniki zapytania programu Hive dla danych wyjściowych do obiektu blob platformy Azure**

Możesz również wyprowadzać wyniki zapytania programu Hive do obiektu blob platformy Azure w ramach domyślnego kontenera klastra Hadoop. Zapytanie programu Hive jest następujące:

    insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

W poniższym przykładzie dane wyjściowe zapytania programu Hive są zapisywane w katalogu `queryoutputdir` obiektów BLOB w kontenerze domyślnym klastra Hadoop. W tym miejscu wystarczy podać nazwę katalogu bez nazwy obiektu BLOB. Błąd jest zgłaszany w przypadku podania nazw katalogów i obiektów blob, takich `wasb:///queryoutputdir/queryoutput.txt`jak.

![Dane wyjściowe zapytania programu Hive](./media/move-hive-tables/output-hive-results-2.png)

W przypadku otwarcia domyślnego kontenera klastra usługi Hadoop przy użyciu Eksplorator usługi Azure Storage można zobaczyć dane wyjściowe zapytania programu Hive, jak pokazano na poniższej ilustracji. Możesz zastosować filtr (wyróżniony przez czerwoną ramkę), aby pobrać obiekt BLOB z określonymi literami w nazwach.

![Eksplorator usługi Azure Storage pokazujący dane wyjściowe zapytania programu Hive](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Przesyłanie zapytań programu Hive przy użyciu edytora Hive
Można również użyć konsoli zapytania (edytora Hive), wprowadzając adres URL formularza *https:\//\<Nazwa klastra usługi Hadoop>. azurehdinsight.net/Home/HiveEditor* w przeglądarce internetowej. Musisz zalogować się za pomocą tej konsoli, aby w tym miejscu musisz mieć poświadczenia klastra usługi Hadoop.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Przesyłanie zapytań programu Hive za pomocą poleceń Azure PowerShell
Aby przesłać zapytania Hive, można także użyć programu PowerShell. Aby uzyskać instrukcje, zobacz [przesyłanie zadań programu Hive przy użyciu programu PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Tworzenie bazy danych i tabel programu Hive
Zapytania programu Hive są udostępniane w [repozytorium GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) i można z niego pobrać.

Oto zapytanie programu Hive tworzące tabelę programu Hive.

    create database if not exists <database name>;
    CREATE EXTERNAL TABLE if not exists <database name>.<table name>
    (
        field1 string,
        field2 int,
        field3 float,
        field4 double,
        ...,
        fieldN string
    )
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
    STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Poniżej przedstawiono opisy pól, które należy podłączyć i inne konfiguracje:

* Nazwa bazy danych: Nazwa bazy danych, którą chcesz utworzyć. ** \<\>** Jeśli chcesz tylko użyć domyślnej bazy danych, można pominąć zapytanie "*CREATE DATABASE...*".
* Nazwa tabeli: Nazwa tabeli, która ma zostać utworzona w ramach określonej bazy danych. ** \<\>** Jeśli chcesz użyć domyślnej bazy danych, tabela może być bezpośrednio określona przez * \<nazwę\> tabeli* bez \<nazwy\>bazy danych.
* separator pola: separator, który ogranicza pola w pliku danych do przekazania do tabeli programu Hive. ** \<\>**
* Separator wierszy: separator, który ogranicza wiersze w pliku danych. ** \<\>**
* Lokalizacja magazynu: Lokalizacja usługi Azure Storage w celu zapisania danych tabel programu Hive. ** \<\>** Jeśli nie określisz *\>lokalizacji przechowywania \<lokalizacji*, baza danych i tabele są domyślnie przechowywane w kontenerze */magazynie/* w katalogu domyślnym dla klastra Hive. Jeśli chcesz określić lokalizację magazynu, lokalizacja przechowywania musi znajdować się w domyślnym kontenerze dla bazy danych i tabel. Ta lokalizacja musi być określana jako lokalizacja względem domyślnego kontenera klastra w formacie *"wasb:///\<Directory 1>/"* lub *"\<wasb:///Directory 1>/\<Directory 2>/"* itp. Po wykonaniu zapytania, katalogi względne są tworzone w domyślnym kontenerze.
* **TBLPROPERTIES ("Skip. Header. line. Count" = "1")**: Jeśli plik danych zawiera wiersz nagłówka, należy dodać tę właściwość **na końcu** zapytania *CREATE TABLE* . W przeciwnym razie wiersz nagłówka jest ładowany jako rekord do tabeli. Jeśli plik danych nie ma wiersza nagłówka, ta konfiguracja może zostać pominięta w zapytaniu.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Ładowanie danych do tabel programu Hive
Oto zapytanie programu Hive ładujące dane do tabeli programu Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

* **ścieżka do\>danych obiektu BLOB: Jeśli plik BLOB, który ma zostać przekazany do tabeli programu Hive, znajduje się w domyślnym kontenerze klastra usługi HDInsight Hadoop, ścieżka do danych obiektu BLOB powinna mieć format "wasb://Directory w tym kontenerze>/nazwa pliku obiektu BLOB>". \<** * \<\> * *\<\<* Plik BLOB może również znajdować się w dodatkowym kontenerze klastra usługi HDInsight Hadoop. W takim przypadku * \<\> ścieżka do danych obiektu BLOB* powinna mieć format *"\<wasb://Container Name>\<nazwa konta magazynu>. blob.Core.Windows.NET/\<pliku BLOB Name>"*.

  > [!NOTE]
  > Dane obiektów BLOB do przekazania do tabeli programu Hive musi znajdować się w domyślnym lub dodatkowym kontenerze konta magazynu dla klastra Hadoop. W przeciwnym razie zapytanie o *dane ładowania* nie powiedzie się, ponieważ nie może uzyskać dostępu do danych.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Tematy zaawansowane: partycjonowana tabela i przechowywanie danych Hive w formacie ORC
Jeśli dane są duże, partycjonowanie tabeli jest przydatne w przypadku zapytań, które wymagają tylko skanowania kilku partycji tabeli. Na przykład rozsądne jest Partycjonowanie danych dziennika witryny sieci Web według dat.

Oprócz partycjonowania tabel programu Hive warto również przechowywać dane Hive w formacie ORC (zoptymalizowane wiersze kolumnowy). Aby uzyskać więcej informacji na temat formatowania ORC, zobacz <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">Używanie plików ORC poprawia wydajność, gdy program Hive odczytuje, zapisuje i przetwarza dane</a>.

### <a name="partitioned-table"></a>Partycjonowana tabela
Oto zapytanie Hive, które tworzy partycjonowaną tabelę i ładuje do niej dane.

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
    (field1 string,
    ...
    fieldN string
    )
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
         lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
    LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
        PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Podczas wykonywania zapytania dotyczącego tabel partycjonowanych zaleca się dodanie warunku partycji na **początku** `where` klauzuli, co zwiększa efektywność wyszukiwania.

    select
        field1, field2, ..., fieldN
    from <database name>.<partitioned table name>
    where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Przechowywanie danych Hive w formacie ORC
Nie można bezpośrednio ładować danych z magazynu obiektów BLOB do tabel programu Hive, które są przechowywane w formacie ORC. Poniżej przedstawiono kroki, które należy wykonać, aby załadować dane z obiektów blob platformy Azure do tabel programu Hive przechowywanych w formacie ORC.

Utwórz zewnętrzną tabelę **przechowywaną jako textfile** i Załaduj dane z magazynu obiektów BLOB do tabeli.

        CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
            lines terminated by '<line separator>' STORED AS TEXTFILE
            LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

        LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

Utwórz wewnętrzną tabelę z tym samym schematem co tabela zewnętrzna w kroku 1, z tym samym ogranicznikiem pola i Zapisz dane programu Hive w formacie ORC.

        CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
        (
            field1 string,
            field2 int,
            ...
            fieldN date
        )
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

Wybierz dane z tabeli zewnętrznej w kroku 1 i Wstaw do tabeli ORC

        INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

> [!NOTE]
> Jeśli * \<\>Nazwa bazy danych tabeli textfile.\< zewnętrzna nazwa\> tabeli textfile* zawiera partycje, w kroku 3 `SELECT * FROM <database name>.<external textfile table name>` polecenie wybiera zmienną partycji jako pole w zwracanym zestawie danych. Wstawianie go do * \<nazwy\>bazy danych.\< Nazwa\> tabeli Orc* kończy się niepowodzeniem od * \<nazwy\>\< bazy danych. \> Nazwa tabeli Orc* nie zawiera zmiennej partycji jako pola w schemacie tabeli. W takim przypadku należy wybrać pola, które mają zostać wstawione do * \<nazwy\>bazy danych.\< \> Nazwa tabeli Orc* w następujący sposób:
>
>

        INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
           SELECT field1, field2, ..., fieldN
           FROM <database name>.<external textfile table name>
           WHERE <partition variable>=<partition value>;

Podczas używania następującego zapytania można bezpiecznie porzucić * \<nazwę\> tabeli zewnętrznych plików tekstowych* , gdy wszystkie dane zostały wstawione do * \<nazwy\>bazy danych.\< \>Nazwa tabeli Orc*:

        DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Po wykonaniu tej procedury należy mieć tabelę z danymi w formacie ORC gotowym do użycia.  
