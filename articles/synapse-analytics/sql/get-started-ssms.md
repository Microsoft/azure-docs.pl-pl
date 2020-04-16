---
title: 'SSMS: Łączenie i wykonywanie zapytań synapse SQL'
description: Program SQL Server Management Studio (SSMS) umożliwia łączenie się z programem Synapse SQL i wykonywanie zapytań w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 704da86fd1d816dbf5d6cd9cf67dfee53fce2622
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423740"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Łączenie się z programem Synapse SQL za pomocą programu SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Program Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

Za pomocą [programu SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) można łączyć się z programem Synapse SQL i wysyłać zapytania do niego za pośrednictwem zasobów sql na żądanie (wersja zapoznawcza) lub sql. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Obsługiwane narzędzia dla sql na żądanie (wersja zapoznawcza)

SSMS jest częściowo obsługiwany począwszy od wersji 18.5 z ograniczonymi funkcjami, takimi jak łączenie i wykonywanie zapytań. [Usługa Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) jest w pełni obsługiwana.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące wymagania wstępne:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* W przypadku puli SQL potrzebny jest istniejący magazyn danych. Aby go utworzyć, zobacz [Tworzenie puli SQL](../quickstart-create-sql-pool.md). W przypadku języka SQL na żądanie jeden jest już aprowizowana w obszarze roboczym w czasie tworzenia. 
* W pełni kwalifikowana nazwa programu SQL Server. Aby to znaleźć, zobacz [Łączenie się z programem Synapse SQL](connect-overview.md).

## <a name="connect"></a>Połącz

### <a name="sql-pool"></a>Pula SQL

Aby połączyć się z programem Synapse SQL przy użyciu puli SQL, wykonaj następujące kroki: 

1. Otwórz program SQL Server Management Studio (SSMS). 
1. W oknie dialogowym **Łączenie z serwerem** wypełnij pola, a następnie wybierz pozycję **Połącz**: 
  
    ![Łączenie z serwerem](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Nazwa serwera**: Wprowadź wcześniej zidentyfikowaną **nazwę serwera.**
   * **Uwierzytelnianie**: Wybierz typ uwierzytelniania, taki jak **uwierzytelnianie serwera SQL Server** lub **zintegrowane uwierzytelnianie usługi Active Directory**.
   * **Nazwa użytkownika** i **hasło:** Wprowadź nazwę użytkownika i hasło, jeśli wybrano uwierzytelnianie programu SQL Server powyżej.

1. Rozwiń program Azure SQL Server w **Eksploratorze obiektów**. Można wyświetlić bazy danych skojarzone z serwerem, takie jak przykładowa baza danych AdventureWorksDW. Można rozwinąć bazę danych, aby wyświetlić tabele:
   
    ![Poznawanie bazy danych AdventureWorksDW](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="sql-on-demand-preview"></a>SQL na żądanie (wersja zapoznawcza)

Aby połączyć się z programem Synapse SQL przy użyciu języka SQL na żądanie, wykonaj następujące kroki: 

1. Otwórz program SQL Server Management Studio (SSMS).
1. W oknie dialogowym **Łączenie z serwerem** wypełnij pola, a następnie wybierz pozycję **Połącz**: 
   
    ![Łączenie z serwerem](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Nazwa serwera**: Wprowadź wcześniej zidentyfikowaną **nazwę serwera.**
   * **Uwierzytelnianie**: Wybierz typ uwierzytelniania, taki jak **uwierzytelnianie serwera SQL** Lub **zintegrowane uwierzytelnianie usługi Active Directory:**
   * **Nazwa użytkownika** i **hasło:** Wprowadź nazwę użytkownika i hasło, jeśli wybrano uwierzytelnianie programu SQL Server powyżej.
   * Kliknij pozycję **Połącz**.

4. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń *demo,* aby wyświetlić zawartość w przykładowej bazie danych.
   
    ![Poznawanie bazy danych AdventureWorksDW](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Uruchamianie przykładowego zapytania

### <a name="sql-pool"></a>Pula SQL

Teraz, gdy połączenie z bazą danych zostało nawiązane, można zbadać dane.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Skopiuj tę kwerendę T-SQL do okna kwerendy:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom zapytanie. Aby to zrobić, kliknij `Execute` lub `F5`użyj następującego skrótu: .
   
    ![Uruchamianie zapytania](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="sql-on-demand"></a>SQL na żądanie

Po nawiązaniu połączenia z bazą danych można zbadać dane.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie](./media/get-started-ssms/new-query.png)
3. Skopiuj do okna kwerendy następującą kwerendę T-SQL:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Uruchom zapytanie. Aby to zrobić, kliknij `Execute` lub `F5`użyj następującego skrótu: .
   
    ![Uruchamianie zapytania](./media/get-started-ssms/execute-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie usPopulationView view ma 3664512 wierszy.
   
    ![Wyniki zapytania](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy możesz się połączyć i zapytać, spróbuj [zwizualizować dane za pomocą usługi Power BI](get-started-power-bi-professional.md).

Aby skonfigurować środowisko uwierzytelniania usługi Azure Active Directory, zobacz [Uwierzytelnianie w języku Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

