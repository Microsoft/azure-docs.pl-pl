---
title: Nawiązywanie połączenia z usługą Synapse SQL z programem SQL Server Management Studio (SSMS)
description: Użyj SQL Server Management Studio (SSMS), aby nawiązać połączenie z usługą SQL Synapse i wysyłać do niej zapytania Synapse.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: da698a1a8d91273321d4633abd683a06cb4cf403
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96451632"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Nawiązywanie połączenia z usługą Synapse SQL z programem SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

Za pomocą [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) można nawiązać połączenie z usługą SQL Synapse i wysyłać do niej zapytania za pomocą puli SQL bezserwerowej lub dedykowanych zasobów puli SQL. 

### <a name="supported-tools-for-serverless-sql-pool"></a>Obsługiwane narzędzia dla puli SQL bezserwerowej

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) jest w pełni obsługiwana począwszy od wersji 1.18.0. Program SSMS jest częściowo obsługiwany począwszy od wersji 18,5, można go używać do nawiązywania połączeń i tylko zapytań.

> [!NOTE]
> Jeśli logowanie w usłudze AAD ma otwarte połączenie przez ponad 1 godzinę w czasie wykonywania zapytania, wszelkie zapytania, które opierają się na usłudze AAD, zakończą się niepowodzeniem. Obejmuje to wysyłanie zapytań do magazynu przy użyciu przekazywania usługi AAD i instrukcji, które współpracują z usługą AAD (na przykład CREATE EXTERNAL PROVIDER). Ma to wpływ na wszystkie narzędzia, które utrzymują otwarte połączenie, takie jak w edytorze zapytań w programie SSMS i ADS. Nie dotyczy narzędzi otwierających nowe połączenie z wykonywaniem zapytań, takich jak Synapse Studio.
> Aby rozwiązać ten problem, możesz uruchomić ponownie narzędzie SSMS lub połączyć się i rozłączyć w usłudze ADS. .
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące wymagania wstępne:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* W przypadku dedykowanej puli SQL potrzebny jest istniejący magazyn danych. Aby go utworzyć, zobacz [Tworzenie dedykowanej puli SQL](../quickstart-create-sql-pool-portal.md). W przypadku bezserwerowej puli SQL, która jest już zainicjowana, nazywa się wbudowaną w obszarze roboczym podczas tworzenia. 
* W pełni kwalifikowana nazwa SQL Server. Aby znaleźć tę nazwę, zobacz [nawiązywanie połączenia z usługą SQL Synapse](connect-overview.md).

## <a name="connect"></a>Nawiązywanie połączenia

### <a name="dedicated-sql-pool"></a>Dedykowana pula SQL

Aby nawiązać połączenie z usługą Synapse SQL przy użyciu dedykowanej puli SQL, wykonaj następujące kroki: 

1. Otwórz SQL Server Management Studio (SSMS). 
1. W oknie dialogowym **łączenie z serwerem** Wypełnij pola, a następnie wybierz pozycję **Połącz**: 
  
    ![Łączenie z serwerem 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Nazwa serwera**: wprowadź wcześniej zidentyfikowaną **nazwę serwera** .
   * **Uwierzytelnianie**: Wybierz typ uwierzytelniania, na przykład **uwierzytelnianie SQL Server** lub **Active Directory uwierzytelnianie zintegrowane**.
   * **Nazwa użytkownika** i **hasło**: Wprowadź nazwę użytkownika i hasło w przypadku wybrania powyżej SQL Server uwierzytelniania.

1. Rozwiń SQL Server platformy Azure w **Eksplorator obiektów**. Można wyświetlić bazy danych skojarzone z serwerem, takie jak Przykładowa baza danych AdventureWorksDW. Bazę danych można rozwinąć, aby wyświetlić tabele:
   
    ![Eksploruj AdventureWorksDW 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="serverless-sql-pool"></a>Bezserwerowa pula SQL

Aby nawiązać połączenie z usługą Synapse SQL przy użyciu puli SQL bezserwerowej, wykonaj następujące kroki: 

1. Otwórz SQL Server Management Studio (SSMS).
1. W oknie dialogowym **łączenie z serwerem** Wypełnij pola, a następnie wybierz pozycję **Połącz**: 
   
    ![Łączenie z serwerem 2](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Nazwa serwera**: wprowadź wcześniej zidentyfikowaną **nazwę serwera** .
   * **Uwierzytelnianie**: Wybierz typ uwierzytelniania, na przykład **uwierzytelnianie SQL Server** lub **uwierzytelnianie zintegrowane Active Directory**:
   * **Nazwa użytkownika** i **hasło**: Wprowadź nazwę użytkownika i hasło w przypadku wybrania powyżej SQL Server uwierzytelniania.
   * Wybierz pozycję **Połącz**.

4. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń *demonstrację* , aby zobaczyć zawartość w przykładowej bazie danych.
   
    ![Eksploruj AdventureWorksDW 2](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Uruchamianie przykładowego zapytania

### <a name="dedicated-sql-pool"></a>Dedykowana pula SQL

Po nawiązaniu połączenia z bazą danych można wykonywać zapytania dotyczące danych.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Skopiuj następujące zapytanie T-SQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom zapytanie, wybierając `Execute` lub użyj następującego skrótu: `F5` .
   
    ![Uruchom zapytanie 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Przejrzyj wyniki zapytania. W poniższym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="serverless-sql-pool"></a>Bezserwerowa pula SQL

Po nawiązaniu połączenia z bazą danych można wykonywać zapytania dotyczące danych.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie 2](./media/get-started-ssms/new-query.png)
3. Skopiuj następujące zapytanie T-SQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Uruchom zapytanie, wybierając `Execute` lub użyj następującego skrótu: `F5` .
   
    ![Uruchom zapytanie 2](./media/get-started-ssms/execute-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie widok usPopulationView ma 3664512 wierszy.
   
    ![Wyniki zapytania 2](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy można nawiązać połączenie i wykonywać zapytania, spróbuj [wizualizować dane za pomocą Power BI](get-started-power-bi-professional.md).

Aby skonfigurować środowisko do uwierzytelniania Azure Active Directory, zobacz [uwierzytelnianie w usłudze SQL Synapse](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

