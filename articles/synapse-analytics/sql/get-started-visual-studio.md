---
title: Łączenie się z programem Synapse SQL i wykonywanie zapytań za pomocą programu Visual Studio i SSDT
description: Za pomocą programu Visual Studio kwerendy puli SQL przy użyciu usługi Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428565"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Łączenie się z programem Synapse SQL za pomocą programu Visual Studio i dysku SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Program Visual Studio](get-started-visual-studio.md)
> * [Sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Za pomocą programu Visual Studio kwerendy puli SQL przy użyciu usługi Azure Synapse Analytics. Ta metoda używa rozszerzenia SQL Server Data Tools (SSDT) w programie Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Obsługiwane narzędzia dla sql na żądanie (wersja zapoznawcza)

Visual Studio nie jest obecnie obsługiwany dla sql na żądanie (wersja zapoznawcza). Jednak usługa Azure Data Studi (preview)o jest w pełni obsługiwanym narzędziem. SQL Server Management Studio jest częściowo obsługiwany w wersji 18.4 i ma ograniczone funkcje, takie jak łączenie i wykonywanie zapytań.

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć tego samouczka, musisz mieć następujące składniki:

* Istniejąca pula SQL. Jeśli go nie masz, zobacz [Tworzenie puli SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) w celu ukończenia tego wymaganego.
* Rozszerzenie SSDT dla programu Visual Studio. Jeśli masz visual studio, prawdopodobnie masz już ten składnik. Aby uzyskać instrukcje instalacji i informacje na temat dostępnych opcji, zobacz artykuł [Instalowanie programu Visual Studio i narzędzi SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* W pełni kwalifikowana nazwa serwera SQL. Aby to znaleźć, zobacz [Łączenie z pulą SQL](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. Połącz się z pulą SQL
1. Otwórz program Visual Studio 2019.
2. Otwórz Eksploratora obiektów programu SQL Server. Aby to zrobić, wybierz pozycję **Wyświetl** > **Eksploratora obiektów programu SQL Server**.
   
    ![Eksplorator obiektów SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Kliknij **ikonę dodawania serwera SQL**.
   
    ![Dodawanie serwera SQL](./media/get-started-visual-studio/add-server.png)
4. Wypełnij pola w oknie łączenia z serwerem.
   
    ![Łączenie z serwerem](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nazwa serwera**: Wprowadź wcześniej zidentyfikowaną **nazwę serwera.**
   * **Uwierzytelnianie**: Wybierz **uwierzytelnianie serwera SQL** lub **zintegrowane uwierzytelnianie usługi Active Directory:**
   * **Nazwa użytkownika** i **hasło:** Wprowadź nazwę użytkownika i hasło, jeśli wybrano uwierzytelnianie programu SQL Server powyżej.
   * Kliknij pozycję **Połącz**.
5. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń węzeł AdventureWorksDW, aby zobaczyć tabele w przykładowej bazie danych.
   
    ![Poznawanie bazy danych AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Uruchamianie przykładowej kwerendy
Teraz, gdy połączenie zostało nawiązane do bazy danych, napiszesz zapytanie.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie](./media/get-started-visual-studio/new-query2.png)
3. Skopiuj do okna kwerendy następującą kwerendę T-SQL:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom zapytanie. W tym celu kliknij zieloną strzałkę lub użyj następującego skrótu: `CTRL`+`SHIFT`+`E`.
   
    ![Uruchamianie zapytania](./media/get-started-visual-studio/run-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy możesz się połączyć i zapytać, spróbuj [zwizualizować dane za pomocą usługi Power BI](get-started-power-bi-professional.md).
Aby skonfigurować środowisko uwierzytelniania usługi Azure Active Directory, zobacz [Uwierzytelnianie w puli SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 