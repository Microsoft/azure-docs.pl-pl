---
title: Dodawanie pakietu języka R do usług uczenia maszynowego (wersja zapoznawcza)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W tym artykule wyjaśniono, jak zainstalować pakiet języka R, który nie jest jeszcze zainstalowany w usłudze Azure SQL Database Machine Learning Services (wersja zapoznawcza).
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
ms.openlocfilehash: ab066609bff773ceacb06be604e386eed5cdf7ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453340"
---
# <a name="add-an-r-package-to-azure-sql-database-machine-learning-services-preview"></a>Dodawanie pakietu języka R do usług azure sql database machine learning services (wersja zapoznawcza)

W tym artykule wyjaśniono, jak dodać pakiet języka R do usług Azure SQL Database Machine Learning Services (wersja zapoznawcza).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj [komputery R](https://www.r-project.org) i [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) na komputerze lokalnym. Język R jest dostępny dla systemów Windows, macOS i Linux. W tym artykule założono, że używasz systemu Windows.

- Ten artykuł zawiera przykład użycia [usługi Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) lub PROGRAMU SQL Server Management [Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) do uruchamiania skryptu języka R w bazie danych SQL azure. Skrypty języka R można uruchamiać przy użyciu innych narzędzi do zarządzania bazą danych lub zapytań, ale w tym przykładzie przyjęto założenie, że usługa Azure Data Studio lub SSMS.
   
> [!NOTE]
> Nie można zainstalować pakietu, uruchamiając skrypt języka R przy użyciu **sp_execute_external_script** w usłudze Azure Data Studio lub SSMS. Pakiety można instalować i usuwać tylko za pomocą wiersza polecenia R i RStudio, jak opisano w tym artykule. Po zainstalowaniu pakietu można uzyskać dostęp do funkcji pakietu w skrypcie języka R za pomocą **sp_execute_external_script**.

## <a name="list-r-packages"></a>Pakiety języka R

Firma Microsoft udostępnia szereg pakietów języka R wstępnie zainstalowanych z usługami uczenia maszynowego w bazie danych SQL platformy Azure.
Możesz wyświetlić listę zainstalowanych pakietów R, uruchamiając następujące polecenie w usłudze Azure Data Studio lub SSMS.

1. Otwórz usługę Azure Data Studio lub SSMS i połącz się z bazą danych SQL platformy Azure.

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

Dane wyjściowe powinny wyglądać podobnie do następującego.

**Wyniki**

![Zainstalowane pakiety w języku R](./media/sql-database-machine-learning-services-add-r-packages/r-installed-packages.png)

## <a name="add-a-package-with-sqlmlutils"></a>Dodawanie pakietu z sqlmlutils

Jeśli chcesz użyć pakietu, który nie jest jeszcze zainstalowany w bazie danych SQL Azure, możesz go zainstalować za pomocą [sqlmlutils](https://github.com/Microsoft/sqlmlutils). **sqlmlutils** to pakiet zaprojektowany, aby pomóc użytkownikom w interakcji z bazami danych SQL (SQL Server i Azure SQL Database) i wykonać kod Języka R lub Pythona w języku SQL z klienta języka R lub Python. Obecnie tylko wersja języka R **sqlmlutils** jest obsługiwana w usłudze Azure SQL Database.

W poniższym przykładzie zainstalujesz pakiet **[kleju,](https://cran.r-project.org/web/packages/glue/)** który może formatować i interpolować ciągi. Te kroki zainstalować **sqlmlutils** i **RODBCext** (warunek wstępny dla **sqlmlutils)** i dodać pakiet **kleju.**

### <a name="install-sqlmlutils"></a>Zainstaluj **sqlmlutils**

1. Pobierz najnowszy plik zip **sqlmlutils** z https://github.com/Microsoft/sqlmlutils/tree/master/R/dist komputera lokalnego. Nie musisz rozpakować pliku.

1. Otwórz **wiersz polecenia** i uruchom następujące polecenia, aby zainstalować **programy RODBCext** i **sqlmlutils** na komputerze lokalnym. Zastąp pełną ścieżkę do pobranego pliku zip **sqlmlutils** (w przykładzie założono, że plik znajduje się w folderze Dokumenty).
    
    ```console
    R -e "install.packages('RODBCext', repos='https://cran.microsoft.com')"
    R CMD INSTALL %UserProfile%\Documents\sqlmlutils_0.5.1.zip
    ```

    Dane wyjściowe, które widzisz, powinny być podobne do następujących.

    ```text
    In R CMD INSTALL
    * installing to library 'C:/Users/<username>/Documents/R/win-library/3.5'
    package sqlmlutils successfully unpacked and MD5 sums checked
    ```

    > [!TIP]
    > Jeśli zostanie wyświetlony błąd " "R" nie jest rozpoznawany jako polecenie wewnętrzne lub zewnętrzne, sprawny program lub plik wsadowy", prawdopodobnie oznacza to, że ścieżka do R.exe nie jest uwzględniona w zmiennej środowiskowej **PATH** w systemie Windows. Można dodać ścieżkę do zmiennej środowiskowej lub przejść do folderu `cd C:\Program Files\R\R-3.5.3\bin`w wierszu polecenia (na przykład), a następnie ponowić próbę wykonania polecenia.

### <a name="add-the-package"></a>Dodaj pakiet

1. Otwórz program RStudio i utwórz nowy plik **skryptu języka R**. 

1. Użyj następującego kodu R, aby zainstalować pakiet **kleju** za pomocą **sqlmlutils**. Zastąp własne informacje o połączeniu usługi Azure SQL Database.

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
    > **Zakres** może być **publiczny** lub **prywatny**. Zakres publiczny jest przydatny dla administratora bazy danych w celu zainstalowania pakietów, których mogą używać wszyscy użytkownicy. Zakres prywatny udostępnia pakiet tylko dla użytkownika, który go instaluje. Jeśli nie określisz zakresu, domyślnym zakresem będzie zakres **PRYWATNY**.

### <a name="verify-the-package"></a>Sprawdź pakiet

Sprawdź, czy pakiet **kleju** został zainstalowany, uruchamiając następujący skrypt języka R w RStudio. Użyj tego samego **połączenia,** które zostały zdefiniowane w poprzednim kroku.

```R
r<-sql_installed.packages(connectionString = connection, fields=c("Package", "Version", "Depends", "License"))
View(r)
```

**Wyniki**

![Zawartość tabeli RTestData](./media/sql-database-machine-learning-services-add-r-packages/r-verify-package-install.png)

### <a name="use-the-package"></a>Użyj pakietu

Po zainstalowaniu pakietu można go używać w skrypcie języka R za pośrednictwem **sp_execute_external_script**.

1. Otwórz usługę Azure Data Studio lub SSMS i połącz się z bazą danych SQL platformy Azure.

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

    Na karcie **Wiadomości** zobaczysz następujący wynik.

    **Wyniki**

    ```text
    My name is Fred, my age next year is 51, my anniversary is Sunday, June 14, 2020.
    ```

### <a name="remove-the-package"></a>Usuń pakiet

Jeśli chcesz usunąć pakiet, uruchom następujący skrypt języka R w RStudio. Użyj tego samego **połączenia** zdefiniowanego wcześniej.

```R
sql_remove.packages(connectionString = connection, pkgs = "glue", scope = "PUBLIC")
```

> [!TIP]
> Innym sposobem zainstalowania pakietu języka R do bazy danych SQL platformy Azure jest przekazanie pakietu Języka R ze strumienia bajtów przy użyciu instrukcji **CREATE EXTERNAL LIBRARY** T-SQL. Zobacz [Tworzenie biblioteki ze strumienia bajtów](/sql/t-sql/statements/create-external-library-transact-sql#create-a-library-from-a-byte-stream) w dokumentacji referencyjnej [TWORZENIE BIBLIOTEKI ZEWNĘTRZNEJ.](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usług Azure SQL Database Machine Learning Services z R (wersja zapoznawcza), zobacz następujące artykuły.

- [Usługi azure SQL Database Machine Learning services z r (wersja zapoznawcza)](sql-database-machine-learning-services-overview.md)
- [Pisanie zaawansowanych funkcji języka R w bazie danych SQL usługi Azure przy użyciu usług uczenia maszynowego (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
- [Praca z danymi języka R i SQL w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-machine-learning-services-data-issues.md)