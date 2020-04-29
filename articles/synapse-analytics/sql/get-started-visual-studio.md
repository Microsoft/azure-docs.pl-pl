---
title: Łączenie się z usługą SQL Synapse i tworzenie zapytań w programie Visual Studio i SSDT
description: Za pomocą programu Visual Studio Zbadaj pulę SQL przy użyciu usługi Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428565"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Nawiązywanie połączenia z usługą Synapse SQL za pomocą programu Visual Studio i SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Za pomocą programu Visual Studio Zbadaj pulę SQL przy użyciu usługi Azure Synapse Analytics. Ta metoda używa rozszerzenia SQL Server Data Tools (SSDT) w programie Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Obsługiwane narzędzia dla SQL na żądanie (wersja zapoznawcza)

Program Visual Studio nie jest obecnie obsługiwany w przypadku wersji SQL na żądanie (wersja zapoznawcza). Jednak usługa Azure Data Studi (wersja zapoznawcza) o to w pełni obsługiwane narzędzie. SQL Server Management Studio jest częściowo obsługiwana w wersji 18,4 i ma ograniczone funkcje, takie jak łączenie i wykonywanie zapytań.

## <a name="prerequisites"></a>Wymagania wstępne
Aby skorzystać z tego samouczka, należy dysponować następującymi składnikami:

* Istniejąca Pula SQL. Jeśli go nie masz, zobacz [Tworzenie puli SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) , aby ukończyć to wymaganie wstępne.
* Rozszerzenie SSDT dla programu Visual Studio. Jeśli masz program Visual Studio, prawdopodobnie masz już ten składnik. Aby uzyskać instrukcje instalacji i informacje na temat dostępnych opcji, zobacz artykuł [Instalowanie programu Visual Studio i narzędzi SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* W pełni kwalifikowana nazwa serwera SQL. Aby to sprawdzić, zobacz [nawiązywanie połączenia z pulą SQL](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. Łączenie z pulą SQL
1. Otwórz program Visual Studio 2019.
2. Otwórz Eksplorator obiektów SQL Server. Aby to zrobić, wybierz pozycję **Wyświetl** > **Eksplorator obiektów SQL Server**.
   
    ![Eksplorator obiektów SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Kliknij **ikonę dodawania serwera SQL**.
   
    ![Dodawanie serwera SQL](./media/get-started-visual-studio/add-server.png)
4. Wypełnij pola w oknie łączenia z serwerem.
   
    ![Łączenie z serwerem](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nazwa serwera**: wprowadź wcześniej zidentyfikowaną **nazwę serwera** .
   * **Uwierzytelnianie**: wybierz opcję **uwierzytelnianie SQL Server** lub **Active Directory uwierzytelnianie zintegrowane**:
   * **Nazwa użytkownika** i **hasło**: Wprowadź nazwę użytkownika i hasło w przypadku wybrania powyżej SQL Server uwierzytelniania.
   * Kliknij pozycję **Połącz**.
5. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń węzeł AdventureWorksDW, aby zobaczyć tabele w przykładowej bazie danych.
   
    ![Poznawanie bazy danych AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Uruchom przykładowe zapytanie
Po nawiązaniu połączenia z bazą danych napiszesz zapytanie.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie](./media/get-started-visual-studio/new-query2.png)
3. Skopiuj następujące zapytanie T-SQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom zapytanie. W tym celu kliknij zieloną strzałkę lub użyj następującego skrótu: `CTRL`+`SHIFT`+`E`.
   
    ![Uruchamianie zapytania](./media/get-started-visual-studio/run-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy można nawiązać połączenie i wykonywać zapytania, spróbuj [wizualizować dane za pomocą Power BI](get-started-power-bi-professional.md).
Aby skonfigurować środowisko do uwierzytelniania Azure Active Directory, zobacz [uwierzytelnianie w puli SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 