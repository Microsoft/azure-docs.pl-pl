---
title: Nawiązywanie połączenia Synapse SQL za pomocą SQL Server Management Studio (SSMS)
description: Użyj SQL Server Management Studio (SSMS), aby nawiązać połączenie i Synapse SQL w Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 222505b03aac5bfc4e8d00b8c8977bece34dee85
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567522"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Nawiązywanie połączenia Synapse SQL za pomocą SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

Za pomocą programu [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) można nawiązywać połączenia z bazami danych Synapse SQL w programie Azure Synapse Analytics za pośrednictwem bez serwera puli SQL lub dedykowanych zasobów puli SQL. 

### <a name="supported-tools-for-serverless-sql-pool"></a>Obsługiwane narzędzia dla bez serwera puli SQL

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) jest w pełni obsługiwana, począwszy od wersji 1.18.0. Program SSMS jest obsługiwany częściowo, począwszy od wersji 18.5. Można go używać tylko do nawiązywania połączeń i wykonywania zapytań.

> [!NOTE]
> Jeśli logowanie do aplikacji AAD ma otwarte połączenie przez więcej niż 1 godzinę w czasie wykonywania zapytania, każde zapytanie, które opiera się na UAD, nie powiedzie się. Obejmuje to wykonywanie zapytań o magazyn przy użyciu przekazania usługi AAD i instrukcji, które wchodzą w interakcję z usługą AAD (na przykład CREATE EXTERNAL PROVIDER). Ma to wpływ na każde narzędzie, które utrzymuje otwarte połączenie, tak jak w edytorze zapytań w programach SSMS i ADS. Nie ma to wpływu na narzędzia, które otwierają nowe połączenie w celu wykonania zapytania, takie Synapse Studio.
> Aby rozwiązać ten problem, możesz ponownie uruchomić program SSMS lub nawiązać połączenie i rozłączyć się w programie ADS. .
## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące wymagania wstępne:  

* [SQL Server Management Studio (SSMS).](/sql/ssms/download-sql-server-management-studio-ssms) 
* W przypadku dedykowanej puli SQL potrzebny jest istniejący magazyn danych. Aby go utworzyć, zobacz [Tworzenie dedykowanej puli SQL.](../quickstart-create-sql-pool-portal.md) W przypadku bez serwera jedna pula SQL jest już aprowizowana o nazwie Wbudowana w obszarze roboczym w czasie tworzenia. 
* W pełni kwalifikowana SQL Server nazwy użytkownika. Aby znaleźć tę nazwę, zobacz [Nawiązywanie połączenia z Synapse SQL](connect-overview.md).

## <a name="connect"></a>Nawiązywanie połączenia

### <a name="dedicated-sql-pool"></a>Dedykowana pula SQL

Aby nawiązać połączenie z Synapse SQL przy użyciu dedykowanej puli SQL, wykonaj następujące kroki: 

1. Otwórz SQL Server Management Studio (SSMS). 
1. W **oknie dialogowym** Łączenie z serwerem wypełnij pola, a następnie wybierz pozycję **Połącz:** 
  
    ![Nawiązywanie połączenia z serwerem 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Nazwa serwera:** wprowadź wcześniej **zidentyfikowaną** nazwę serwera.
   * **Uwierzytelnianie:** wybierz typ uwierzytelniania, na przykład **uwierzytelnianie SQL Server lub** zintegrowane **uwierzytelnianie usługi Active Directory.**
   * **Nazwa użytkownika** i **hasło:** wprowadź nazwę użytkownika i hasło, jeśli powyżej SQL Server uwierzytelnianie.

1. Rozwiń swój Azure SQL Server **w** Eksplorator obiektów . Możesz wyświetlić bazy danych skojarzone z serwerem, takie jak przykładowa baza danych AdventureWorksDW. Możesz rozwinąć bazę danych, aby wyświetlić tabele:
   
    ![Eksplorowanie adventureWorksDW 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="serverless-sql-pool"></a>Bezserwerowa pula SQL

Aby nawiązać połączenie z Synapse SQL przy użyciu bez serwera puli SQL, wykonaj następujące kroki: 

1. Otwórz SQL Server Management Studio (SSMS).
1. W **oknie dialogowym** Łączenie z serwerem wypełnij pola, a następnie wybierz pozycję **Połącz:** 
   
    ![Nawiązywanie połączenia z serwerem 2](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Nazwa serwera:** wprowadź wcześniej **zidentyfikowaną** nazwę serwera.
   * **Uwierzytelnianie:** wybierz typ uwierzytelniania, na przykład **SQL Server uwierzytelnianie** lub zintegrowane **uwierzytelnianie usługi Active Directory:**
   * **Nazwa użytkownika** i **hasło:** wprowadź nazwę użytkownika i hasło, jeśli powyżej SQL Server uwierzytelnianie.
   * Wybierz pozycję **Połącz**.

4. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń *wersję demonstracyjną,* aby wyświetlić zawartość w przykładowej bazie danych.
   
    ![Eksplorowanie adventureWorksDW 2](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Uruchamianie przykładowego zapytania

### <a name="dedicated-sql-pool"></a>Dedykowana pula SQL

Teraz, po nawiązaniu połączenia z bazą danych, możesz utworzyć zapytanie o dane.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Skopiuj następujące zapytanie T-SQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom zapytanie, wybierając `Execute` lub użyj następującego skrótu: `F5` .
   
    ![Uruchamianie zapytania 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Przejrzyj wyniki zapytania. W poniższym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="serverless-sql-pool"></a>Bezserwerowa pula SQL

Teraz, po nawiązaniu połączenia z bazą danych, możesz zapytania o dane.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie 2](./media/get-started-ssms/new-query.png)
3. Skopiuj następujące zapytanie T-SQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Uruchom zapytanie, wybierając `Execute` lub użyj następującego skrótu: `F5` .
   
    ![Uruchamianie zapytania 2](./media/get-started-ssms/execute-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie widok usPopulationView ma 3664512 wierszy.
   
    ![Wyniki zapytania 2](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy możesz nawiązać połączenie i wykonać zapytanie, spróbuj [wykonać wizualizację danych za pomocą Power BI](get-started-power-bi-professional.md).

Aby skonfigurować środowisko na potrzeby uwierzytelniania Azure Active Directory, [zobacz Authenticate to Synapse SQL (Uwierzytelnianie w Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)).

