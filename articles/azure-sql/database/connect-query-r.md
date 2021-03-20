---
title: Używanie języka R z Azure SQL Database Machine Learning Services (wersja zapoznawcza) do wykonywania zapytań w bazie danych
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: W tym artykule pokazano, jak używać skryptu języka R z Machine Learning Services Azure SQL Database, aby nawiązać połączenie z bazą danych w Azure SQL Database i wykonać zapytania przy użyciu instrukcji języka Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, sstein
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2e32a4abeae78aa7105f21ecffbb18c2eae841a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185627"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>Szybki Start: używanie języka R z Azure SQL Database Machine Learning Services (wersja zapoznawcza) w celu wykonywania zapytań względem bazy danych 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym przewodniku szybki start użyjesz języka R z Azure SQL Database Machine Learning Services, aby nawiązać połączenie z bazą danych w Azure SQL Database i użyć instrukcji T-SQL do wykonywania zapytań dotyczących danych.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL Database](single-database-create-quickstart.md)
- [Machine Learning Services](../managed-instance/machine-learning-services-overview.md) z włączonym językiem R.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> Skrypty w tym artykule są przeznaczone do korzystania z bazy danych firmy **Adventure Works** .

Machine Learning Services z R to funkcja Azure SQL Database używana do wykonywania skryptów języka R w bazie danych. Aby uzyskać więcej informacji, zobacz [Projekt R](https://www.r-project.org/).

## <a name="get-the-sql-server-connection-information"></a>Pobierz informacje o połączeniu SQL Server

Pobierz informacje o połączeniu potrzebne do nawiązania połączenia z bazą danych w Azure SQL Database. W następnych procedurach będą potrzebne w pełni kwalifikowana nazwa serwera lub nazwa hosta, nazwa bazy danych i informacje logowania.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Przejdź do strony **bazy danych SQL**  lub **wystąpienia zarządzane SQL** .

3. Na stronie **Przegląd** Sprawdź w pełni kwalifikowaną nazwę serwera obok pozycji **Nazwa serwera** dla bazy danych w Azure SQL Database lub w pełni kwalifikowana nazwa serwera obok **hosta** dla wystąpienia zarządzanego w wystąpieniu zarządzanym Azure SQL. Aby skopiować nazwę serwera lub hosta, umieść na niej wskaźnik myszy i wybierz ikonę **Kopiuj**.

## <a name="create-code-to-query-your-database"></a>Utwórz kod, aby wykonać zapytanie dotyczące bazy danych

1. Otwórz **SQL Server Management Studio** i nawiąż połączenie z bazą danych.

   Jeśli potrzebujesz pomocy przy nawiązywaniu połączenia, zobacz [Szybki Start: użyj SQL Server Management Studio, aby nawiązać połączenie z bazą danych i zbadać ją w programie Azure SQL Database](connect-query-ssms.md).

1. Przekaż kompletny skrypt języka R do [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedury składowanej.

   Skrypt jest przesyłany przez `@script` argument. Wszystkie elementy wewnątrz `@script` argumentu muszą być prawidłowym kodem R.
   
   >[!IMPORTANT]
   >Kod w tym przykładzie używa przykładowych danych AdventureWorksLT, które można wybrać jako źródło podczas tworzenia bazy danych. Jeśli baza danych zawiera różne dane, w zapytaniu SELECT należy użyć tabel z własnej bazy danych. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > W przypadku wystąpienia błędów może to być spowodowane tym, że publiczna wersja zapoznawcza Machine Learning Services (z R) nie jest włączona dla Twojej bazy danych. Zobacz powyższe [wymagania wstępne](#prerequisites) .

## <a name="run-the-code"></a>Uruchamianie kodu

1. Wykonaj [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) procedury składowanej.

1. Sprawdź, czy pierwsze 20 wierszy kategorii/produktu jest zwracanych w oknie **komunikaty** .

## <a name="next-steps"></a>Następne kroki

- [Projektuj swoją pierwszą bazę danych w Azure SQL Database](design-first-database-tutorial.md)
- [Azure SQL Database Machine Learning Services (z językiem R)](../managed-instance/machine-learning-services-overview.md)
- [Twórz i uruchamiaj proste skrypty języka R w Azure SQL Database Machine Learning Services (wersja zapoznawcza)](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context)