---
title: Dodaj pakiet języka R do Machine Learning Services (wersja zapoznawcza)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W tym artykule wyjaśniono, jak zainstalować pakiet języka R, który nie jest jeszcze zainstalowany w programie Azure SQL Database Machine Learning Services (wersja zapoznawcza).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b42dba59e061eee0472d39f324b7474d7d829310
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85250727"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Dodaj pakiet języka R do Azure SQL Database Machine Learning Services (wersja zapoznawcza)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule wyjaśniono, jak dodać pakiet języka R do Azure SQL Database Machine Learning Services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj pulpit [R](https://www.r-project.org) i [RStudio](https://www.rstudio.com/products/rstudio/download/) na komputerze lokalnym. Język R jest dostępny dla systemów Windows, macOS i Linux. W tym artykule przyjęto założenie, że używasz systemu Windows.

- Ten artykuł zawiera przykład użycia [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) lub [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) do uruchamiania skryptu języka R w Azure SQL Database. Skrypty języka R można uruchamiać przy użyciu innych narzędzi do zarządzania bazami danych lub zapytań, ale w tym przykładzie założono Azure Data Studio lub SSMS.
   
> [!NOTE]
> Nie można zainstalować pakietu przez uruchomienie skryptu języka R przy użyciu **sp_execute_external_script** w Azure Data Studio lub SSMS. Pakiety można instalować i usuwać tylko przy użyciu wiersza polecenia R i RStudio zgodnie z opisem w tym artykule. Po zainstalowaniu pakietu można uzyskać dostęp do funkcji pakietu w skrypcie języka R przy użyciu **sp_execute_external_script**.

## <a name="list-r-packages"></a>Pakiety języka R

Firma Microsoft udostępnia wiele pakietów języka R wstępnie zainstalowanych z Machine Learning Services w Azure SQL Database.
Listę zainstalowanych pakietów języka R można wyświetlić, uruchamiając następujące polecenie w Azure Data Studio lub SSMS.

1. Otwórz Azure Data Studio lub SSMS i Połącz się z Azure SQL Database.

1. Uruchom następujące polecenie:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
    OutputDataSet <- data.frame(installed.packages()[,c("Package", "Version", "Depends", "License")]);'
    WITH RESULT SETS((
                Package NVARCHAR(255)
                , Version NVARCHAR(100)
                , Depends NVARCHAR(4000)
                , License NVARCHAR(1000)
                ));
    ```

Dane wyjściowe powinny wyglądać podobnie do poniższego.

**Wyniki**

![Zainstalowane pakiety w języku R](./media/machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Dodaj pakiet z sqlmlutils

Jeśli konieczne jest użycie pakietu, który nie jest jeszcze zainstalowany w Azure SQL Database, można go zainstalować za pomocą [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** to pakiet zaprojektowany w celu ułatwienia użytkownikom współdziałania Microsoft Azure z bazami danych sql i SQL Server i wykonywania kodu języka r lub Python w języku SQL z poziomu klienta języka r lub Python. Obecnie tylko wersja języka R **sqlmlutils** jest obsługiwana w Azure SQL Database.

W poniższym przykładzie zostanie zainstalowany pakiet **[glue](https://cran.r-project.org/web/packages/glue/)** , który umożliwia formatowanie i interpolację ciągów. Te kroki instalują **sqlmlutils** i **RODBCext** (wymaganie wstępne dla **sqlmlutils**), a następnie dodają pakiet **przyklejania** .

### <a name="install-sqlmlutils"></a>Zainstaluj **sqlmlutils**

1. Pobierz najnowszy plik zip **sqlmlutils** z https://github.com/Microsoft/sqlmlutils/tree/master/R/dist na komputer lokalny. Nie musisz rozpakować pliku.

1. Otwórz **wiersz polecenia** i uruchom następujące polecenia, aby zainstalować **RODBCext** i **sqlmlutils** na komputerze lokalnym. Zastąp pełną ścieżkę do pobranego pliku zip **sqlmlutils** (przykład założono, że plik znajduje się w folderze dokumentów).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Widoczne dane wyjściowe powinny być podobne do następujących.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Jeśli wystąpi błąd, "R" nie jest rozpoznawany jako polecenie wewnętrzne lub zewnętrzne, program wykonywalny lub plik wsadowy, prawdopodobnie oznacza to, że ścieżka do R.exe nie jest uwzględniona w zmiennej środowiskowej **Path** w systemie Windows. Możesz dodać ścieżkę do zmiennej środowiskowej lub przejdź do folderu w wierszu polecenia (na przykład `cd C:\Program Files\R\R-3.5.3\bin` ), a następnie ponów próbę wykonania tego polecenia.

### <a name="add-the-package"></a>Dodaj pakiet

1. Otwórz program RStudio i utwórz nowy plik **skryptu języka R**. 

1. Użyj następującego kodu R, aby zainstalować pakiet **glue** przy użyciu **sqlmlutils**. Zastąp własne informacje o połączeniu Azure SQL Database.

    ```R
    library(sqlmlutils)
    connection <- connectionInfo(
    server= "yourserver.database.windows.net",
    database = "yourdatabase",
    uid = "yoursqluser",
    pwd = "yoursqlpassword")
    
    sql_install.packages(connectionString = connection, pkgs = "glue", verbose = TRUE, scope = "PUBLIC")
    ```

    > [!TIP]
    > **Zakresem** może być **Public** lub **Private**. Zakres publiczny jest przydatny dla administratora bazy danych w celu zainstalowania pakietów, których mogą używać wszyscy użytkownicy. Zakres prywatny sprawia, że pakiet jest dostępny tylko dla użytkownika, który go zainstaluje. Jeśli nie określisz zakresu, domyślnym zakresem będzie zakres **PRYWATNY**.

### <a name="verify-the-package"></a>Weryfikowanie pakietu

Sprawdź, czy pakiet **glue** został zainstalowany, uruchamiając następujący skrypt języka R w RStudio. Użyj tego samego **połączenia** , które zostało zdefiniowane w poprzednim kroku.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Wyniki**

![Zawartość tabeli RTestData](./media/machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Korzystanie z pakietu

Po zainstalowaniu pakietu można go użyć w skrypcie języka R za pomocą **sp_execute_external_script**.

1. Otwórz Azure Data Studio lub SSMS i Połącz się z Azure SQL Database.

1. Uruchom następujące polecenie:

    ```sql
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    library(glue)
    
    name <- "Fred"
    age <- 50
    anniversary <- as.Date("2020-06-14")
    text <- glue(''My name is {name}, '',
    ''my age next year is {age + 1}, '',
    ''my anniversary is {format(anniversary, "%A, %B %d, %Y")}.'')
    
    print(text)
    ';
    ```

    Na karcie **komunikaty** zostanie wyświetlony następujący wynik.

    **Wyniki**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Usuń pakiet

Jeśli chcesz usunąć pakiet, uruchom następujący skrypt języka R w RStudio. Użyj tego samego **połączenia** , które zostało zdefiniowane wcześniej.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Innym sposobem instalacji pakietu języka R w celu Azure SQL Database jest przekazanie pakietu języka R z strumienia bajtów przy użyciu instrukcji **Create External Library** T-SQL. Zobacz [Tworzenie biblioteki z strumienia bajtów](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) w dokumentacji [tworzenia biblioteki zewnętrznej](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Machine Learning Services Azure SQL Database przy użyciu języka R (wersja zapoznawcza), zobacz następujące artykuły.

- [Azure SQL Database Machine Learning Services z językiem R (wersja zapoznawcza)](machine-learning-services-overview.md)
- [Zapisuj zaawansowane funkcje języka R w Azure SQL Database przy użyciu Machine Learning Services (wersja zapoznawcza)](machine-learning-services-functions.md)
- [Pracuj z danymi języka R i SQL w Azure SQL Database Machine Learning Services (wersja zapoznawcza)](machine-learning-services-data-issues.md)
