---
title: Tworzenie projektu bazy danych U-SQL — Azure Data Lake
description: Dowiedz się, jak utworzyć bazę danych U-SQL przy użyciu Azure Data Lake Tools for Visual Studio.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 706457a602e20dd37e64e5f389948b351ac8ebcf
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219245"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Użyj projektu bazy danych U-SQL, aby utworzyć bazę danych U-SQL dla Azure Data Lake

Baza danych U-SQL zapewnia strukturalne widoki danych bez struktury i zarządzane dane strukturalne w tabelach. Zapewnia również ogólny system wykazu metadanych służący do organizowania danych strukturalnych i kodu niestandardowego. Baza danych to koncepcja, która grupuje te powiązane obiekty razem.

Dowiedz się więcej o usłudze [U-SQL Database i języku definicji danych (DDL)](/u-sql/data-definition-language-ddl-statements).

Projekt bazy danych U-SQL to typ projektu w programie Visual Studio, który ułatwia deweloperom szybkie i łatwe tworzenie i wdrażanie baz danych U-SQL oraz zarządzanie nimi.

## <a name="create-a-u-sql-database-project"></a>Tworzenie projektu bazy danych U-SQL

Azure Data Lake Tools for Visual Studio dodany nowy szablon projektu o nazwie "U-SQL Database" po wersji 2.3.3000.0. Aby utworzyć projekt U-SQL, wybierz pozycję **plik > nowy > projekt**. Projekt U-SQL Database można znaleźć w obszarze **Azure Data Lake > węzeł U-SQL**.

![Data Lake Tools for Visual Studio — Tworzenie projektu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png)

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>Tworzenie obiektów bazy danych U-SQL przy użyciu projektu bazy danych

Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL. Wybierz pozycję **dodaj > nowy element**. Wszystkie nowe obsługiwane typy obiektów można znaleźć w kreatorze **dodawania nowego elementu** .

W przypadku obiektu niebędącego zestawem (na przykład funkcji zwracającej tabelę) tworzony jest nowy skrypt U-SQL po dodaniu nowego elementu. Możesz rozpocząć tworzenie instrukcji DDL dla tego obiektu w edytorze.

Dla obiektu zestawu narzędzie zapewnia przyjazny dla użytkownika Edytor interfejsu użytkownika, który pomaga zarejestrować zestaw i wdrożyć pliki DLL oraz inne dodatkowe pliki. Poniższe kroki pokazują, jak dodać definicję obiektu zestawu do projektu bazy danych U-SQL:

1. Dodaj odwołania do projektu C#, który zawiera UDO/UDAG/UDF dla projektu bazy danych U-SQL.

   ![Data Lake Tools for Visual Studio — Dodawanie odwołania](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png)

   ![Data Lake Tools for Visual Studio — Dodawanie odwołania projektu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2. W widoku Projekt zestawu wybierz przywoływany zestaw z menu rozwijanego **Utwórz zestaw z odwołania** .

   ![Data Lake Tools for Visual Studio — Tworzenie zestawu z odwołania](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3. Dodaj **zarządzane zależności** i **dodatkowe pliki** , jeśli istnieją. Po dodaniu dodatkowych plików narzędzie używa ścieżki względnej, aby upewnić się, że można znaleźć zestawy zarówno na komputerze lokalnym, jak i na maszynie kompilacji.

@_DeployTempDirectory to wstępnie zdefiniowana zmienna, która wskazuje narzędzie do folderu danych wyjściowych kompilacji. W folderze danych wyjściowych kompilacji każdy zestaw ma podfolder o nazwie z nazwą zestawu. Wszystkie biblioteki DLL i dodatkowe pliki znajdują się w tym podfolderze.

## <a name="build-a-u-sql-database-project"></a>Kompilowanie projektu bazy danych U-SQL

Dane wyjściowe kompilacji dla projektu bazy danych U-SQL są pakietem wdrożeniowym bazy danych U-SQL o nazwie z sufiksem `.usqldbpack` . `.usqldbpack`Pakiet jest plikiem ZIP, który zawiera wszystkie instrukcje języka DDL w jednym skrypcie U-SQL w folderze **DDL** oraz wszystkie biblioteki DLL i dodatkowe pliki dla zestawów w folderze **tymczasowym** .

Dowiedz się więcej [na temat tworzenia projektu bazy danych U-SQL przy użyciu wiersza polecenia programu MSBuild i zadania kompilacji Azure DevOps Services](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Wdrażanie bazy danych U-SQL

Pakiet. usqldbpack można wdrożyć na koncie lokalnym lub koncie Azure Data Lake Analytics przy użyciu programu Visual Studio lub zestawu SDK wdrożenia.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Wdrażanie bazy danych U-SQL w programie Visual Studio

Bazę danych U-SQL można wdrożyć za pomocą projektu bazy danych U-SQL lub pakietu usqldbpack w programie Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Wdrażanie za pomocą projektu bazy danych U-SQL

1. Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL, a następnie wybierz polecenie **Wdróż**.

1. W **Kreatorze wdrażania U-SQL Database**wybierz **konto ADLA** , do którego chcesz wdrożyć bazę danych. Obsługiwane są zarówno konta lokalne, jak i konta ADLA.

1. **Źródło bazy danych** jest wypełniane automatycznie i wskazuje na pakiet. usqldbpack w folderze danych wyjściowych kompilacji projektu.

1. Wprowadź nazwę w polu **Nazwa bazy danych** , aby utworzyć bazę danych. Jeśli baza danych o takiej samej nazwie już istnieje na docelowym koncie Azure Data Lake Analytics, wszystkie obiekty, które są zdefiniowane w projekcie bazy danych, zostaną utworzone bez ponownego tworzenia bazy danych.

1. Aby wdrożyć bazę danych U-SQL, wybierz pozycję **Prześlij**. Wszystkie zasoby (zestawy i dodatkowe pliki) są przekazywane, a zadanie U-SQL zawierające wszystkie instrukcje języka DDL jest przesyłane.

   ![Data Lake Tools for Visual Studio — wdrażanie projektu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake Tools for Visual Studio — Kreator wdrażania projektu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Wdrażanie za pomocą pakietu wdrożeniowego bazy danych U-SQL

1. Otwórz **Eksplorator serwera**. Następnie rozwiń **konto Azure Data Lake Analytics** , do którego chcesz wdrożyć bazę danych.

1. Kliknij prawym przyciskiem myszy pozycję **bazy danych U-SQL**, a następnie wybierz polecenie **Wdróż bazę danych**.

1. Ustaw **Źródło bazy danych** na ścieżkę do pakietu wdrożeniowego bazy danych U-SQL (plik usqldbpack).

1. Wprowadź **nazwę bazy danych** , aby utworzyć bazę danych. Jeśli istnieje baza danych o takiej samej nazwie, która już istnieje na docelowym koncie Azure Data Lake Analytics, wszystkie obiekty, które są zdefiniowane w projekcie bazy danych są tworzone bez ponownego tworzenia bazy danych.

   ![Data Lake Tools for Visual Studio — wdrażanie pakietu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

   ![Data Lake Tools for Visual Studio — Kreator wdrażania pakietu bazy danych U-SQL](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>Wdrażanie bazy danych U-SQL przy użyciu zestawu SDK

`PackageDeploymentTool.exe` zapewnia programowanie i interfejsy wiersza polecenia, które ułatwiają wdrażanie baz danych U-SQL. Zestaw SDK jest zawarty w [pakiecie NuGet zestawu SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), który znajduje się w lokalizacji `build/runtime/PackageDeploymentTool.exe` .

[Dowiedz się więcej o zestawie SDK i sposobach konfigurowania potoku ciągłej integracji i ciągłego wdrażania bazy danych U-SQL](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Odwoływanie się do projektu bazy danych U-SQL

Projekt U-SQL może odwoływać się do projektu bazy danych U-SQL. Odwołanie dotyczy dwóch obciążeń:

- *Kompilacja projektu*: należy skonfigurować odwołania do środowisk baz danych przed skompilowaniem skryptów U-SQL.
- *Lokalne uruchomienie w odniesieniu do konta (lokalnego)*: środowiska bazy danych, do których istnieją odwołania, są wdrażane na koncie (na podstawie lokalnego projektu) przed wykonaniem skryptu U-SQL. [Dowiedz się więcej o lokalnych uruchomieniach i różnicach między (lokalnymi maszyną) a kontem (lokalnym-Project)](data-lake-analytics-data-lake-tools-local-run.md).

### <a name="how-to-add-a-u-sql-database-reference"></a>Jak dodać odwołanie do bazy danych U-SQL

1. Kliknij prawym przyciskiem myszy projekt U-SQL w **Eksplorator rozwiązań**, a następnie wybierz polecenie **Dodaj odwołanie u-SQL Database.**..

    ![Data Lake Tools for Visual Studio — Dodawanie odwołania do projektu bazy danych](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Skonfiguruj odwołanie do bazy danych z projektu bazy danych U-SQL w bieżącym rozwiązaniu lub w pliku pakietu bazy danych U-SQL.

3. Podaj nazwę bazy danych.

    ![Kreator dodawania odwołania projektu bazy danych Data Lake Tools for Visual Studio](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Następne kroki

- [Jak skonfigurować potok ciągłej integracji/ciągłego wdrażania dla Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Jak przetestować kod Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
