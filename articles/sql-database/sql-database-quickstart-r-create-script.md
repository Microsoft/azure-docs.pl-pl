---
title: Tworzenie i Uruchamianie prostych skryptów języka R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Uruchom proste skrypty języka R w Azure SQL Database Machine Learning Services (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bb6cb6d86933166d2427788d697d9cd38cf04bf0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81460175"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Szybki Start: Tworzenie i Uruchamianie prostych skryptów języka R w Azure SQL Database Machine Learning Services (wersja zapoznawcza)

W tym przewodniku szybki start utworzysz i uruchomisz zestaw skryptów języka R przy użyciu Machine Learning Services (w języku R) w Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Baza danych Azure SQL Database](sql-database-single-database-get-started.md) z [regułą zapory na poziomie serwera](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) z włączonym językiem R.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

Ten przykład używa procedury składowanej [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) , aby otoczyć poprawnie sformułowany skrypt języka R.

## <a name="run-a-simple-script"></a>Uruchamianie prostego skryptu

Aby uruchomić skrypt języka R, przekazanie go jako argumentu do procedury składowanej systemu [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

W poniższych krokach opisano uruchamianie tego przykładowego skryptu języka R w usłudze SQL Database:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Otwórz program **SQL Server Management Studio** i nawiąż połączenie z usługą SQL Database.

   Jeśli potrzebujesz pomocy przy nawiązywaniu połączenia, zobacz [Szybki Start: użyj SQL Server Management Studio, aby nawiązać połączenie i wysłać zapytanie do bazy danych Azure SQL](sql-database-connect-query-ssms.md).

1. Przekaż kompletny skrypt języka R do [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedury składowanej.

   Skrypt jest przesyłany przez `@script` argument. Wszystkie elementy wewnątrz `@script` argumentu muszą być prawidłowym kodem R.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    a <- 1
    b <- 2
    c <- a/b
    d <- a*b
    print(c(c, d))
    '
    ```

   Jeśli wystąpią błędy, może to być spowodowane tym, że publiczna wersja zapoznawcza usług Machine Learning Services (z językiem R) nie jest włączona dla usługi SQL Database. Zobacz powyższe [wymagania wstępne](#prerequisites) .

   > [!NOTE]
   > Jeśli jesteś administratorem, możesz uruchomić kod zewnętrzny automatycznie. Można udzielić uprawnienia innym użytkownikom przy użyciu polecenia:
   <br>**Przyznaj do** * \<nazwy użytkownika\>* wykonywanie dowolnego skryptu zewnętrznego.

2. Obliczany jest prawidłowy wynik, a funkcja `print` R zwraca wynik do okna **komunikatów** .

   Powinien on wyglądać podobnie do tego.

    **Wyniki**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Uruchamianie skryptu Hello worldowego

Typowy przykładowy skrypt to ten, który po prostu wyprowadza ciąg "Hello world". Uruchom następujące polecenie.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Dane wejściowe tej procedury składowanej obejmują:

| | |
|-|-|
| @language | definiuje rozszerzenie języka do wywołania, w tym przypadku R |
| @script | definiuje polecenia przesłane do środowiska uruchomieniowego języka R. Cały skrypt języka R musi być ujęty w ten argument, jako tekst w formacie Unicode. Można również dodać tekst do zmiennej typu **nvarchar** , a następnie wywołać zmienną |
| @input_data_1 | dane zwrócone przez zapytanie, przekazana do środowiska uruchomieniowego języka R, które zwraca dane do SQL Server jako ramki danych |
|Z ZESTAWAMI WYNIKÓW | klauzula definiuje schemat zwracanej tabeli danych dla SQL Server, dodając "Hello world" jako nazwę kolumny ( **int** ) dla typu danych |

Polecenie wyprowadza następujący tekst:

| Witaj, świecie |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Korzystanie z danych wejściowych i wyjściowych

Domyślnie [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) akceptuje pojedynczy zestaw danych jako dane wejściowe, co jest zazwyczaj podawane w postaci prawidłowego zapytania SQL. Następnie zwraca pojedynczą ramkę danych języka R jako dane wyjściowe.

Na razie Użyjmy domyślnych zmiennych wejściowych i wyjściowych [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql): **InputDataSet** i **OutputDataSet**.

1. Utwórz małą tabelę danych testowych.

    ```sql
    CREATE TABLE RTestData (col1 INT NOT NULL)
    
    INSERT INTO RTestData
    VALUES (1);
    
    INSERT INTO RTestData
    VALUES (10);
    
    INSERT INTO RTestData
    VALUES (100);
    GO
    ```

1. Użyj `SELECT` instrukcji, aby zbadać tabelę.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Wyniki**

    ![Zawartość tabeli RTestData](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Uruchom następujący skrypt języka R. Pobiera dane z tabeli przy użyciu `SELECT` instrukcji, przekazuje je za pośrednictwem środowiska uruchomieniowego języka R i zwraca dane jako ramkę danych. `WITH RESULT SETS` Klauzula definiuje schemat zwracanej tabeli danych dla SQL Database, dodając nazwę kolumny *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Wyniki**

    ![Dane wyjściowe skryptu języka R, który zwraca dane z tabeli](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Teraz Zmieńmy nazwy zmiennych wejściowych i wyjściowych. Domyślnymi nazwami zmiennych wejściowych i wyjściowych są **InputDataSet** i **OutputDataSet**, ten skrypt zmienia nazwy na **SQL_in** i **SQL_out**:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Należy pamiętać, że w języku R jest rozróżniana wielkość liter. Zmienne wejściowe i wyjściowe używane w skrypcie języka R (**SQL_out**, **SQL_in**) muszą być zgodne z wartościami zdefiniowanymi w `@input_data_1_name` i `@output_data_1_name`, włącznie z wielkością liter.

   > [!TIP]
   > Tylko jeden wejściowy zestaw danych może być przekazywany jako parametr i można zwrócić tylko jeden zestaw danych. Jednak można wywoływać inne zestawy danych z wewnątrz kodu języka R i można zwrócić dane wyjściowe innych typów oprócz zestawu danych. Możesz także dodać słowo kluczowe OUTPUT do dowolnego parametru, aby był zwracany z wynikami.

1. Możesz również generować wartości tylko przy użyciu skryptu języka R bez danych wejściowych (`@input_data_1` jest to ustawienie puste).

   Poniższy skrypt wyświetla tekst "Hello" i "World".

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    mytextvariable <- c("hello", " ", "world");
    OutputDataSet <- as.data.frame(mytextvariable);
    '
        , @input_data_1 = N''
    WITH RESULT SETS(([Col1] CHAR(20) NOT NULL));
    ```

    **Wyniki**

    ![Wyniki zapytania używającego @script jako danych wejściowych](./media/sql-database-quickstart-r-create-script/r-data-generated-output.png)

## <a name="check-r-version"></a>Sprawdzanie wersji języka R

Jeśli chcesz zobaczyć, która wersja języka R jest zainstalowana w bazie danych SQL, uruchom następujący skrypt.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'print(version)';
GO
```

Funkcja `print` języka R zwraca wersję do okna **Komunikaty**. W poniższym przykładzie danych wyjściowych można zobaczyć, że SQL Database w tym przypadku ma zainstalowany język R w wersji 3.4.4.

**Wyniki**

```text
STDOUT message(s) from external script:
                   _
platform       x86_64-w64-mingw32
arch           x86_64
os             mingw32
system         x86_64, mingw32
status
major          3
minor          4.4
year           2018
month          03
day            15
svn rev        74408
language       R
version.string R version 3.4.4 (2018-03-15)
nickname       Someone to Lean On
```

## <a name="list-r-packages"></a>Pakiety języka R

Firma Microsoft udostępnia wiele pakietów języka R wstępnie instalowanych z usługami Machine Learning Services w usłudze SQL Database.

Aby wyświetlić listę zainstalowanych pakietów języka R, w tym informacje o wersji, zależnościach, licencji i ścieżce biblioteki, uruchom następujący skrypt.

```SQL
EXEC sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License", "LibPath")]);'
WITH result sets((
            Package NVARCHAR(255)
            , Version NVARCHAR(100)
            , Depends NVARCHAR(4000)
            , License NVARCHAR(1000)
            , LibPath NVARCHAR(2000)
            ));
```

Dane wyjściowe pochodzą z `installed.packages()` w języku R i są zwracane jako zestaw wyników.

**Wyniki**

![Zainstalowane pakiety w języku R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Następne kroki

Aby utworzyć model uczenia maszynowego przy użyciu języka R w SQL Database, postępuj zgodnie z tym przewodnikiem Szybki Start:

> [!div class="nextstepaction"]
> [Tworzenie i uczenie modelu predykcyjnego w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-quickstart-r-train-score-model.md)

Aby uzyskać więcej informacji na Azure SQL Database Machine Learning Services z językiem R (wersja zapoznawcza), zobacz następujące artykuły.

- [Azure SQL Database Machine Learning Services z językiem R (wersja zapoznawcza)](sql-database-machine-learning-services-overview.md)
- [Zapisuj zaawansowane funkcje języka R w Azure SQL Database przy użyciu Machine Learning Services (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
- [Pracuj z danymi języka R i SQL w Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-machine-learning-services-data-issues.md)
