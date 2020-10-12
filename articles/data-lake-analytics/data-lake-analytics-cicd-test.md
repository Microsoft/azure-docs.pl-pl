---
title: Jak przetestować kod Azure Data Lake Analytics
description: Dowiedz się, jak dodać przypadki testowe dla języka U-SQL i rozszerzonego kodu C# dla Azure Data Lake Analytics.
services: data-lake-analytics
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: how-to
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: 44426598daf1808ef0aee233968b04d2dc7c165f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87129920"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Przetestuj kod Azure Data Lake Analytics

Azure Data Lake udostępnia język [U-SQL](data-lake-analytics-u-sql-get-started.md) . Skrypt U-SQL łączy deklaratywne SQL z niezbędną C# do przetwarzania danych w dowolnej skali. W tym dokumencie dowiesz się, jak utworzyć przypadki testowe dla kodu U-SQL i rozszerzonego języka C# zdefiniowanego przez użytkownika (UDO).

## <a name="test-u-sql-scripts"></a>Testowanie skryptów U-SQL

Skrypt U-SQL jest kompilowany i zoptymalizowany pod kątem uruchamiania kodu wykonywalnego na platformie Azure lub na komputerze lokalnym. Proces kompilowania i optymalizacji traktuje cały skrypt U-SQL jako całość. Dla każdej instrukcji nie można wykonać tradycyjnego testu jednostkowego. Jednak przy użyciu zestawu SDK testów U-SQL i lokalnego uruchomienia zestawu SDK można wykonywać testy na poziomie skryptów.

### <a name="create-test-cases-for-u-sql-script"></a>Utwórz przypadki testowe dla skryptu U-SQL

Azure Data Lake Tools for Visual Studio pozwala utworzyć przypadki testowe skryptu U-SQL.

1. Kliknij prawym przyciskiem myszy skrypt U-SQL w Eksplorator rozwiązań, a następnie wybierz pozycję **Utwórz test jednostkowy**.

1. Utwórz nowy projekt testowy lub Wstaw przypadek testowy do istniejącego projektu testowego.

   ![Data Lake Tools for Visual Studio — Tworzenie konfiguracji projektu testowego U-SQL](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>Zarządzanie testowym źródłem danych

Podczas testowania skryptów U-SQL należy przetestować pliki wejściowe. Aby zarządzać danymi testowymi, w **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt U-SQL i wybierz polecenie **Właściwości**. Możesz wprowadzić źródło w **testowym źródle danych**.

![Data Lake Tools for Visual Studio — Skonfiguruj źródło danych testu projektu](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Po wywołaniu `Initialize()` interfejsu w zestawie SDK testów U-SQL w katalogu roboczym projektu testowego zostanie utworzony tymczasowy folder główny danych lokalnych. Wszystkie pliki i foldery w folderze źródła danych testowych są kopiowane do tymczasowego lokalnego folderu głównego danych przed uruchomieniem przypadków testowych skryptu U-SQL. Można dodać więcej folderów testów źródła danych, dzieląc ścieżkę folderu danych testowych średnikiem.

### <a name="manage-the-database-environment-for-testing"></a>Zarządzanie środowiskiem bazy danych na potrzeby testowania

Jeśli skrypty U-SQL używają lub zapytania z obiektami bazy danych U-SQL, musisz zainicjować środowisko bazy danych przed uruchomieniem przypadków testowych U-SQL. Takie podejście może być konieczne podczas wywoływania procedur składowanych. `Initialize()`Interfejs zestawu SDK testów u-SQL ułatwia wdrażanie wszystkich baz danych, do których odwołuje się projekt u-SQL, do tymczasowego folderu głównego danych lokalnych w katalogu roboczym projektu testowego.

Aby uzyskać więcej informacji o sposobach zarządzania odwołaniami projektu bazy danych U-SQL dla projektu U-SQL, zobacz [odwołanie do projektu bazy danych u-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).

### <a name="verify-test-results"></a>Weryfikuj wyniki testu

`Run()`Interfejs zwraca wynik wykonania zadania. *0* oznacza sukces, a *1* oznacza błąd. Możesz również użyć funkcji potwierdzenia języka C# do zweryfikowania danych wyjściowych.

### <a name="run-test-cases-in-visual-studio"></a>Uruchamianie przypadków testowych w programie Visual Studio

Projekt testu skryptu U-SQL jest oparty na strukturze testów jednostkowych języka C#. Po skompilowaniu projektu wybierz pozycję **Testuj**  >  **Windows**  >  **Eksplorator testów**systemu Windows. Można uruchamiać przypadki testowe z **Eksploratora testów**. Alternatywnie kliknij prawym przyciskiem myszy plik CS w teście jednostkowym i wybierz polecenie **Uruchom testy**.

## <a name="test-c-udos"></a>Testowanie udo C#

### <a name="create-test-cases-for-c-udos"></a>Utwórz przypadki testowe dla udo C#

Możesz użyć struktury testów jednostkowych języka C# do testowania zdefiniowanych przez użytkownika operatorów języka C# (udo). Podczas testowania udo należy przygotować odpowiednie obiekty **IRowset** jako dane wejściowe.

Istnieją dwa sposoby tworzenia obiektu **IRowset** :

- Załaduj dane z pliku, aby utworzyć **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Użyj danych z kolekcji danych do utworzenia **IRowset**:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Weryfikuj wyniki testu

Po wywołaniu funkcji UDO można sprawdzić wyniki za pomocą weryfikacji wartości schematu i zestawu wierszy przy użyciu funkcji Assert języka C#. Do rozwiązania można dodać **projekt testu jednostkowego języka U-SQL udo** . W tym celu wybierz pozycję **plik > nowy > projekt** w programie Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Uruchamianie przypadków testowych w programie Visual Studio

Po skompilowaniu projektu wybierz pozycję **Testuj**  >  **Windows**  >  **Eksplorator testów**systemu Windows. Można uruchamiać przypadki testowe z **Eksploratora testów**. Alternatywnie kliknij prawym przyciskiem myszy plik CS w teście jednostkowym i wybierz polecenie **Uruchom testy**.

## <a name="run-test-cases-in-azure-pipelines"></a>Uruchom przypadki testowe w Azure Pipelines<a name="run-test-cases-in-azure-devops"></a>

Zarówno **projekty testowe skryptów U-SQL** , jak i **projekty testowe udo języka c#** dziedziczą projekty testów jednostkowych c#. [Zadanie testowe programu Visual Studio](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) w Azure Pipelines może uruchamiać te przypadki testowe.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>Uruchom przypadki testowe U-SQL w Azure Pipelines

W przypadku testu U-SQL upewnij się, że załadowano `CPPSDK` na komputerze kompilacji, a następnie Przekaż `CPPSDK` ścieżkę do programu `USqlScriptTestRunner(cppSdkFolderFullPath: @"")` .

#### <a name="what-is-cppsdk"></a>Co to jest CPPSDK?

CPPSDK to pakiet, który zawiera Microsoft Visual C++ 14 i Windows SDK 10.0.10240.0. Ten pakiet zawiera środowisko, które jest potrzebne przez środowisko uruchomieniowe języka U-SQL. Ten pakiet można pobrać pod folderem instalacji Azure Data Lake Tools for Visual Studio:

- W przypadku programu Visual Studio 2015 jest w obszarze `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- W przypadku programu Visual Studio 2017 jest w obszarze `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- W przypadku programu Visual Studio 2019 jest w obszarze `C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>Przygotuj CPPSDK w agencie kompilacji Azure Pipelines

Najbardziej typowym sposobem przygotowania zależności CPPSDK w Azure Pipelines jest następująca:

1. Folder zip, który zawiera biblioteki CPPSDK.

1. Zaewidencjonuj plik zip w systemie kontroli źródła. Plik. zip gwarantuje Zaewidencjonuj wszystkie biblioteki w folderze CPPSDK, aby pliki nie były ignorowane ze względu na `.gitignore` plik.

1. Rozpakuj plik zip w potoku kompilacji.

1. Wskaż `USqlScriptTestRunner` ten folder niespakowany na komputerze kompilacji.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>Uruchamianie przypadków testowych UDO języka C# w Azure Pipelines

W przypadku testu UDO języka C# Pamiętaj, aby odwoływać się do następujących zestawów, które są konieczne do udo.

- Microsoft. Analytics. interfejsy
- Microsoft. Analytics. Types
- Microsoft. Analytics. testu jednostkowego

Jeśli odwołujesz się do nich za pomocą [pakietu NuGet Microsoft. Azure. datalake. USQL. Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/), upewnij się, że zostało dodane zadanie przywracania NuGet w potoku kompilacji.

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania dla Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
- [Korzystanie z projektu bazy danych U-SQL do tworzenia bazy danych U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
