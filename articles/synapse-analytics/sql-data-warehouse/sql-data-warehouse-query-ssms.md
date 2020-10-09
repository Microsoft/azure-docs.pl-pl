---
title: Nawiązywanie połączenia z programem SSMS
description: Użyj SQL Server Management Studio (SSMS), aby nawiązać połączenie z usługą Azure Synapse Analytics i uzyskać do niej zapytanie.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 772e4253244f5e13a60f155549d5f5513695e5fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85200753"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Nawiązywanie połączenia z usługą Azure Synapse Analytics przy użyciu programu SQL Server Management Studio (SSMS)

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Program Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Użyj SQL Server Management Studio (SSMS), aby nawiązać połączenie z magazynem danych i wysłać do niego zapytanie w ramach usługi Azure Synapse.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego samouczka, potrzebne są następujące elementy:

* Istniejąca Pula SQL. Aby go utworzyć, zobacz [Tworzenie puli SQL](create-data-warehouse-portal.md).
* SQL Server Management Studio (SSMS). [Pobierz](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) bezpłatnie program SSMS, jeśli go jeszcze nie masz.
* W pełni kwalifikowana nazwa serwera SQL. Aby znaleźć te informacje, zobacz [nawiązywanie połączenia z pulą SQL](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. Nawiązywanie połączenia z pulą SQL

1. Otwórz program SSMS.
2. Otwórz Eksplorator obiektów, wybierając pozycję **plik**  >  **Połącz Eksplorator obiektów**.

    ![Eksplorator obiektów SQL Server](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Wypełnij pola w oknie łączenia z serwerem.

   ![Łączenie z serwerem](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **Nazwa serwera**. Wprowadź wcześniej ustaloną **nazwę serwera**.
   * **Uwierzytelnianie**. Wybierz opcję **Uwierzytelnianie na serwerze SQL Server** lub **Zintegrowane uwierzytelnianie usługi Active Directory**.
   * **Nazwa użytkownika** i **hasło**. Wprowadź nazwę użytkownika i hasło, jeżeli powyżej wybrano uwierzytelnianie na serwerze SQL Server.
   * Kliknij przycisk **Połącz**.
4. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń węzeł AdventureWorksDW, aby zobaczyć tabele w przykładowej bazie danych.

   ![Poznawanie bazy danych AdventureWorksDW](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Uruchom przykładowe zapytanie

Teraz, po nawiązaniu połączenia z bazą danych, napiszemy zapytanie.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.

   ![Nowe zapytanie](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Skopiuj następujące zapytanie T-SQL do okna zapytania:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Uruchom zapytanie, klikając `Execute` lub używając następującego skrótu: `F5` .

   ![Uruchamianie zapytania](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.

   ![Wyniki zapytania](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy można nawiązać połączenie i wykonywać zapytania, spróbuj [wizualizować dane za pomocą Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md). Aby skonfigurować środowisko do uwierzytelniania Azure Active Directory, zobacz [uwierzytelnianie w puli SQL](sql-data-warehouse-authentication.md).
