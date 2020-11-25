---
title: Zarządzanie zestawami U-SQL w potoku ciągłej integracji/ciągłego wdrażania — Azure Data Lake
description: Poznaj najlepsze rozwiązania dotyczące zarządzania zestawami języka U-SQL w potoku ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps.
author: liudan66
ms.author: liud
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/30/2018
ms.openlocfilehash: e88616f45c69d33234aa35333e0d82ad8cc59bb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015285"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Najlepsze rozwiązania dotyczące zarządzania zestawami U-SQL w potoku ciągłej integracji/ciągłego wdrażania

W tym artykule dowiesz się, jak zarządzać kodem źródłowym zestawu U-SQL przy użyciu nowo wprowadzonego projektu bazy danych U-SQL. Dowiesz się również, jak skonfigurować potok ciągłej integracji i wdrażania (CI/CD) na potrzeby rejestracji zestawu przy użyciu usługi Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Użyj projektu bazy danych U-SQL, aby zarządzać kodem źródłowym zestawu

[Projekt bazy danych u-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md) to typ projektu w programie Visual Studio, który ułatwia deweloperom szybkie i łatwe tworzenie i wdrażanie baz danych U-SQL oraz zarządzanie nimi. Można zarządzać wszystkimi obiektami bazy danych U-SQL (z wyjątkiem poświadczeń) za pomocą projektu bazy danych U-SQL.

Aby zarządzać kodem źródłowym zestawu języka C# i skryptami języka c-SQL rejestracji zestawu, użyj:

- Projekt bazy danych u-SQL w celu zarządzania rejestrowaniem skryptów U-SQL.
- Biblioteka klas (dla aplikacji U-SQL) do zarządzania kodem źródłowym C# i zależnościami dla operatorów zdefiniowanych przez użytkownika, funkcji i agregatorów (udo, UDF i UDAGs).
- Projekt bazy danych U-SQL do odwoływania się do projektu biblioteki klas.

Projekt bazy danych U-SQL może odwoływać się do biblioteki klas (dla aplikacji U-SQL). Zestawy zarejestrowane w bazie danych U-SQL można tworzyć za pomocą odwołania do kodu źródłowego C# z tej biblioteki klas (dla aplikacji U-SQL).

Wykonaj następujące kroki, aby utworzyć projekty i dodać odwołania.

1. Utwórz bibliotekę klas (dla aplikacji U-SQL), wybierając kolejno pozycje **plik**  >  **Nowy**  >  **projekt**. Projekt znajduje się w **Azure Data Lake > węzła U-SQL** .

   ![Data Lake Tools for Visual Studio — Tworzenie projektu biblioteki klas C#](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)

1. Dodaj zdefiniowany przez użytkownika kod C# w bibliotece klas (dla aplikacji U-SQL).

1. Utwórz projekt U-SQL, wybierając pozycję **plik**  >  **Nowy**  >  **projekt**. Projekt znajduje się w węźle **Azure Data Lake**  >  **U-SQL** .

   ![Data Lake Tools for Visual Studio — Tworzenie projektu bazy danych U-SQL](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)

1. Dodaj odwołanie do projektu biblioteki klas C# dla projektu bazy danych U-SQL.

   ![Data Lake Tools for Visual Studio — Dodawanie odwołania](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png)

   ![Data Lake Tools for Visual Studio — Dodawanie odwołania projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

1. Utwórz skrypt zestawu w projekcie bazy danych U-SQL, klikając prawym przyciskiem myszy projekt i wybierając polecenie **Dodaj nowy element**.

   ![Data Lake Tools for Visual Studio — Dodawanie skryptu zestawu](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Otwórz skrypt zestawu w widoku projektu zestawu. Wybierz przywoływany zestaw z menu rozwijanego **Utwórz zestaw z odwołania** .

   ![Data Lake Tools for Visual Studio — Tworzenie zestawu z odwołania](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

1. Dodaj **zarządzane zależności** i **dodatkowe pliki**, jeśli istnieją. Po dodaniu dodatkowych plików narzędzie używa ścieżki względnej, aby upewnić się, że można znaleźć zestawy na komputerze lokalnym i na maszynie kompilacji w późniejszym czasie.

**\@ _DeployTempDirectory** w oknie edytora u dołu jest wstępnie zdefiniowaną zmienną, która wskazuje narzędzie do folderu danych wyjściowych kompilacji. W folderze danych wyjściowych kompilacji każdy zestaw ma podfolder o nazwie z nazwą zestawu. Wszystkie biblioteki DLL i dodatkowe pliki znajdują się w tym podfolderze.

## <a name="build-a-u-sql-database-project"></a>Kompilowanie projektu bazy danych U-SQL

Dane wyjściowe kompilacji dla projektu bazy danych U-SQL to pakiet wdrożeniowy usługi U-SQL Database. Nazwa jest nazywana sufiksem `.usqldbpack` . `.usqldbpack`Pakiet jest plikiem ZIP, który zawiera wszystkie instrukcje języka DDL w jednym skrypcie U-SQL w folderze DDL. Wszystkie skompilowane pliki. dll i dodatkowe pliki dla zestawów znajdują się w folderze temp.

## <a name="deploy-a-u-sql-database"></a>Wdrażanie bazy danych U-SQL

`.usqldbpack`Pakiet można wdrożyć na koncie lokalnym lub koncie Azure Data Lake Analytics. Użyj programu Visual Studio lub zestawu SDK wdrażania.

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Wdrażanie bazy danych U-SQL w programie Visual Studio

Bazę danych U-SQL można wdrożyć przy użyciu projektu bazy danych U-SQL lub `.usqldbpack` pakietu w programie Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Wdrażanie przy użyciu projektu bazy danych U-SQL

1. Kliknij prawym przyciskiem myszy projekt bazy danych U-SQL, a następnie wybierz polecenie **Wdróż**.

1. W kreatorze **wdrażania U-SQL Database** wybierz **konto ADLA** , do którego chcesz wdrożyć bazę danych. Obsługiwane są zarówno konta lokalne, jak i konta ADLA.

1. **Źródło bazy danych** jest wypełniane automatycznie. Wskazuje na pakiet. usqldbpack w folderze danych wyjściowych kompilacji projektu.

1. Wprowadź nazwę w polu **Nazwa bazy danych** , aby utworzyć bazę danych. Jeśli baza danych o takiej samej nazwie już istnieje na docelowym koncie Azure Data Lake Analytics, wszystkie obiekty, które są zdefiniowane w projekcie bazy danych, zostaną utworzone bez ponownego tworzenia bazy danych.

1. Aby wdrożyć bazę danych U-SQL, wybierz pozycję **Prześlij**. Przekazywane są wszystkie zasoby, takie jak zestawy i dodatkowe pliki. Zostanie przesłane zadanie U-SQL zawierające wszystkie instrukcje języka DDL.

   ![Data Lake Tools for Visual Studio — wdrażanie projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

   ![Data Lake Tools for Visual Studio — Kreator wdrażania projektu bazy danych U-SQL](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Wdrażanie bazy danych U-SQL w usłudze Azure DevOps

`PackageDeploymentTool.exe` zapewnia programowanie i interfejsy wiersza polecenia, które ułatwiają wdrażanie baz danych U-SQL. Zestaw SDK jest zawarty w [pakiecie NuGet zestawu SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), który znajduje się w lokalizacji `build/runtime/PackageDeploymentTool.exe` .

W usłudze Azure DevOps można użyć zadania wiersza polecenia i tego zestawu SDK, aby skonfigurować potok automatyzacji dla odświeżania bazy danych U-SQL. [Dowiedz się więcej o zestawie SDK i sposobach konfigurowania potoku ciągłej integracji i ciągłego wdrażania bazy danych U-SQL](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj potok ciągłej integracji/ciągłego wdrażania dla Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Przetestuj kod Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Uruchom skrypt U-SQL na komputerze lokalnym](data-lake-analytics-data-lake-tools-local-run.md)
