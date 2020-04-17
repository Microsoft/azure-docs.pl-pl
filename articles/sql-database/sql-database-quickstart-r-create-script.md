---
title: Tworzenie i uruchamianie prostych skryptów języka R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Uruchamianie prostych skryptów języka R w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza).
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460175"
---
# <a name="quickstart-create-and-run-simple-r-scripts-in-azure-sql-database-machine-learning-services-preview"></a>Szybki start: tworzenie i uruchamianie prostych skryptów języka R w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)

W tym przewodniku Szybki start utworzysz i uruchomisz zestaw skryptów języka R przy użyciu usług uczenia maszynowego (z R) w bazie danych SQL azure.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Baza danych SQL platformy Azure](sql-database-single-database-get-started.md) z [regułą zapory na poziomie serwera](sql-database-server-level-firewall-rule.md)
- [Usługi uczenia maszynowego](sql-database-machine-learning-services-overview.md) z włączoną funkcją języka R.
- [Sql Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

W tym przykładzie użyto procedury składowanej [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) do zawijania poprawnie sformułowanego skryptu języka R.

## <a name="run-a-simple-script"></a>Uruchamianie prostego skryptu

Aby uruchomić skrypt R, przekażesz go jako argument do procedury składowanej w [systemie, sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

W poniższych krokach zostanie uruchomiony ten przykładowy skrypt języka R w bazie danych SQL:

```r
a <- 1
b <- 2
c <- a/b
d <- a*b
print(c(c, d))
```

1. Otwórz program **SQL Server Management Studio** i nawiąż połączenie z usługą SQL Database.

   Jeśli potrzebujesz pomocy w nawiązywania połączenia, zobacz [Szybki start: Łączenie i wykonywanie zapytań o bazę danych SQL i wykonywanie zapytań o kwerendę .](sql-database-connect-query-ssms.md)

1. Przekaż pełny skrypt języka R do [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedury składowanej.

   Skrypt jest przekazywany przez `@script` argument. Wszystko wewnątrz `@script` argumentu musi być prawidłowy kod R.

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

   Jeśli wystąpią błędy, może to być spowodowane tym, że publiczna wersja zapoznawcza usług Machine Learning Services (z językiem R) nie jest włączona dla usługi SQL Database. Zobacz [Wymagania wstępne](#prerequisites) powyżej.

   > [!NOTE]
   > Jeśli jesteś administratorem, możesz automatycznie uruchomić kod zewnętrzny. Za pomocą polecenia można udzielić uprawnień innym użytkownikom:
   <br>**Przyznaj wykonanie dowolnego skryptu zewnętrznego do** * \<nazwy użytkownika\>*.

2. Obliczany jest prawidłowy `print` wynik, a funkcja R zwraca wynik do okna **Wiadomości.**

   To powinno wyglądać mniej więcej tak.

    **Wyniki**

    ```text
    STDOUT message(s) from external script:
    0.5 2
    ```

## <a name="run-a-hello-world-script"></a>Uruchamianie skryptu Hello World

Typowy przykładowy skrypt jest taki, który po prostu wyprowadza ciąg "Hello World". Uruchom następujące polecenie.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'OutputDataSet<-InputDataSet'
    , @input_data_1 = N'SELECT 1 AS hello'
WITH RESULT SETS(([Hello World] INT));
GO
```

Dane wejściowe do tej procedury składowanej obejmują:

| | |
|-|-|
| @language | definiuje rozszerzenie języka, aby wywołać, w tym przypadku R |
| @script | definiuje polecenia przekazywane do środowiska wykonawczego R. Cały skrypt R musi być ujęty w tym argumie, jako tekst Unicode. Można również dodać tekst do zmiennej typu **nvarchar,** a następnie wywołać zmienną |
| @input_data_1 | dane zwracane przez kwerendę, przekazywane do środowiska wykonawczego języka R, które zwraca dane do programu SQL Server jako ramki danych |
|Z ZESTAWAMI WYNIKÓW | klauzula definiuje schemat tabeli zwróconych danych dla programu SQL Server, dodając "Hello World" jako nazwę kolumny, **int** dla typu danych |

Polecenie wyprowadza następujący tekst:

| Witaj, świecie |
|-------------|
| 1 |

## <a name="use-inputs-and-outputs"></a>Korzystanie z danych wejściowych i wyjściowych

Domyślnie [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) akceptuje pojedynczy zestaw danych jako dane wejściowe, które zazwyczaj są podasz w postaci prawidłowej kwerendy SQL. Następnie zwraca pojedynczą ramkę danych R jako dane wyjściowe.

Na razie użyjmy domyślnych zmiennych wejściowych i wyjściowych [sp_execute_external_script:](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) **InputDataSet** i **OutputDataSet**.

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

1. Instrukcja `SELECT` służy do wykonywania zapytań o tabelę.
  
    ```sql
    SELECT *
    FROM RTestData
    ```

    **Wyniki**

    ![Zawartość tabeli RTestData](./media/sql-database-quickstart-r-create-script/select-rtestdata.png)

1. Uruchom następujący skrypt języka R. Pobiera dane z tabeli przy `SELECT` użyciu instrukcji, przekazuje je przez środowisko uruchomieniowe języka R i zwraca dane jako ramkę danych. Klauzula `WITH RESULT SETS` definiuje schemat tabeli zwróconych danych dla bazy danych SQL, dodając nazwę kolumny *NewColName*.

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'OutputDataSet <- InputDataSet;'
        , @input_data_1 = N'SELECT * FROM RTestData;'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    **Wyniki**

    ![Dane wyjściowe skryptu języka R, który zwraca dane z tabeli](./media/sql-database-quickstart-r-create-script/r-output-rtestdata.png)

1. Teraz zmieńmy nazwy zmiennych wejściowych i wyjściowych. Domyślne nazwy zmiennych wejściowych i wyjściowych to **InputDataSet** i **OutputDataSet**, ten skrypt zmienia nazwy na **SQL_in** i **SQL_out:**

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N' SQL_out <- SQL_in;'
        , @input_data_1 = N' SELECT 12 as Col;'
        , @input_data_1_name = N'SQL_in'
        , @output_data_1_name = N'SQL_out'
    WITH RESULT SETS(([NewColName] INT NOT NULL));
    ```

    Należy zauważyć, że R jest rozróżniana wielkość liter. Zmienne wejściowe i wyjściowe używane w skrypcie R (**SQL_out** **,** `@input_data_1_name` SQL_in `@output_data_1_name`) muszą być zgodne z wartościami zdefiniowanymi z i , w tym przypadek.

   > [!TIP]
   > Tylko jeden wejściowy zestaw danych może być przekazywany jako parametr i można zwrócić tylko jeden zestaw danych. Jednak można wywoływać inne zestawy danych z wewnątrz kodu języka R i można zwrócić dane wyjściowe innych typów oprócz zestawu danych. Możesz także dodać słowo kluczowe OUTPUT do dowolnego parametru, aby był zwracany z wynikami.

1. Można również wygenerować wartości tylko przy`@input_data_1` użyciu skryptu R bez danych wejściowych ( jest ustawiona na pustą).

   Poniższy skrypt wyprowadza tekst "hello" i "world".

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

Funkcja `print` języka R zwraca wersję do okna **Komunikaty**. W poniższym przykładzie danych wyjściowych widać, że baza danych SQL w tym przypadku ma zainstalowaną wersję języka R 3.4.4.

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

Aby wyświetlić listę pakietów języka R, w tym informacje o wersji, zależnościach, licencji i ścieżce biblioteki, uruchom następujący skrypt.

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

Dane wyjściowe `installed.packages()` pochodzi z w R i jest zwracany jako zestaw wyników.

**Wyniki**

![Zainstalowane pakiety w języku R](./media/sql-database-quickstart-r-create-script/r-installed-packages.png)

## <a name="next-steps"></a>Następne kroki

Aby utworzyć model uczenia maszynowego przy użyciu języka R w bazie danych SQL, wykonaj ten szybki start:

> [!div class="nextstepaction"]
> [Tworzenie i szkolenie modelu predykcyjnego w języku R za pomocą usług azure sql database machine learning services (wersja zapoznawcza)](sql-database-quickstart-r-train-score-model.md)

Aby uzyskać więcej informacji na temat usług Azure SQL Database Machine Learning Services z R (wersja zapoznawcza), zobacz następujące artykuły.

- [Usługi azure SQL Database Machine Learning services z r (wersja zapoznawcza)](sql-database-machine-learning-services-overview.md)
- [Pisanie zaawansowanych funkcji języka R w bazie danych SQL usługi Azure przy użyciu usług uczenia maszynowego (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
- [Praca z danymi języka R i SQL w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-machine-learning-services-data-issues.md)
