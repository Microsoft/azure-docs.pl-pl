---
title: Używanie języka R z usługami uczenia maszynowego do wykonywania zapytań o bazę danych (wersja zapoznawcza)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W tym artykule pokazano, jak używać skryptu języka R z usługami uczenia maszynowego bazy danych SQL platformy Azure, aby połączyć się z bazą danych SQL platformy Azure i zbadać ją przy użyciu instrukcji Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 23dc784586e85b8cbdc816ac5f4d54556fc1ffb7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456995"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Szybki start: używanie języka R z usługami uczenia maszynowego do wykonywania zapytań o bazę danych SQL platformy Azure (wersja zapoznawcza)

W tym przewodniku Szybki start używasz języka R z usługami uczenia maszynowego do łączenia się z bazą danych SQL platformy Azure i używania instrukcji T-SQL do wykonywania zapytań o dane.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Baza danych SQL platformy Azure](sql-database-single-database-get-started.md)
- [Usługi uczenia maszynowego](sql-database-machine-learning-services-overview.md) z włączoną funkcją języka R.
- [Sql Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Skrypty w tym artykule są zapisywane w celu użycia bazy danych **Adventure Works.**

Usługi uczenia maszynowego z języka R to funkcja usługi Azure SQL Database używana do wykonywania skryptów R w bazie danych. Aby uzyskać więcej informacji, zobacz [projekt R](https://www.r-project.org/).

## <a name="get-sql-server-connection-information"></a>Uzyskiwanie informacji o połączeniu z serwerem SQL

Uzyskaj parametry połączenia potrzebne do nawiązania połączenia z bazą danych Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Otwórz stronę **Bazy danych SQL** lub **Wystąpienia zarządzane SQL**.

3. Na stronie **Przegląd** znajdź w pełni kwalifikowaną nazwę serwera obok pola **Nazwa serwera** (w przypadku pojedynczej bazy danych) lub w pełni kwalifikowaną nazwę serwera obok pola **Host** (w przypadku wystąpienia zarządzanego). Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

## <a name="create-code-to-query-your-sql-database"></a>Tworzenie kodu zapytania do bazy danych SQL

1. Otwórz program **SQL Server Management Studio** i nawiąż połączenie z usługą SQL Database.

   Jeśli potrzebujesz pomocy w nawiązywania połączenia, zobacz [Szybki start: Łączenie i wykonywanie zapytań o bazę danych SQL i wykonywanie zapytań o kwerendę .](sql-database-connect-query-ssms.md)

1. Przekaż pełny skrypt języka R do [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedury składowanej.

   Skrypt jest przekazywany przez `@script` argument. Wszystko wewnątrz `@script` argumentu musi być prawidłowy kod R.
   
   >[!IMPORTANT]
   >Kod w tym przykładzie używa przykładowych danych AdventureWorksLT, które można wybrać jako źródło podczas tworzenia bazy danych. Jeśli baza danych zawiera różne dane, w zapytaniu SELECT należy użyć tabel z własnej bazy danych. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Jeśli wystąpią błędy, może to być spowodowane tym, że publiczna wersja zapoznawcza usług Machine Learning Services (z językiem R) nie jest włączona dla usługi SQL Database. Zobacz [Wymagania wstępne](#prerequisites) powyżej.

## <a name="run-the-code"></a>Uruchamianie kodu

1. Wykonaj [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedura składowana.

1. Sprawdź, czy w oknie **Wiadomości** są zwracane 20 wierszy kategorii/produktu.

## <a name="next-steps"></a>Następne kroki

- [Projektowanie pierwszej bazy danych SQL platformy Azure](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (z językiem R)](sql-database-machine-learning-services-overview.md)
- [Tworzenie i uruchamianie prostych skryptów języka R w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md)
- [Pisanie zaawansowanych funkcji języka R w bazie danych SQL usługi Azure przy użyciu usług uczenia maszynowego (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
