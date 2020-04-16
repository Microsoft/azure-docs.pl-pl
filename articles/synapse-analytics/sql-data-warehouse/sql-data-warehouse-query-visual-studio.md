---
title: Połącz się z programem VSTS
description: Zapytanie usługi Azure Synapse Analytics w programie Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 174ee07e389e598fed6ed8487e60303fbce81f77
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416047"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Łączenie się z usługą Azure Synapse Analytics za pomocą programu Visual Studio i SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Program Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Użyj programu Visual Studio, aby zbadać pulę SQL w usłudze Azure Synapse w ciągu zaledwie kilku minut. Ta metoda używa rozszerzenia SQL Server Data Tools (SSDT) w programie Visual Studio 2019. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć tego samouczka, potrzebne są następujące elementy:

* Istniejąca pula SQL. Aby go utworzyć, zobacz [Tworzenie puli SQL](create-data-warehouse-portal.md).
* Rozszerzenie SSDT dla programu Visual Studio. Jeśli masz visual studio, prawdopodobnie masz już SSDT dla programu Visual Studio. Aby uzyskać instrukcje instalacji i informacje na temat dostępnych opcji, zobacz artykuł [Instalowanie programu Visual Studio i narzędzi SSDT](sql-data-warehouse-install-visual-studio.md).
* W pełni kwalifikowana nazwa serwera SQL. Aby znaleźć te informacje, zobacz [Łączenie z pulą SQL](../sql/connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. Połącz się z pulą SQL
1. Otwórz program Visual Studio 2019.
2. Otwórz Eksploratora obiektów programu SQL Server, wybierając **pozycję Wyświetl** > **Eksplorator obiektów programu SQL Server**.
   
    ![Eksplorator obiektów SQL Server](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Kliknij **ikonę dodawania serwera SQL**.
   
    ![Dodawanie serwera SQL](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Wypełnij pola w oknie łączenia z serwerem.
   
    ![Łączenie z serwerem](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Nazwa serwera**. Wprowadź wcześniej ustaloną **nazwę serwera**.
   * **Uwierzytelnianie**. Wybierz opcję **Uwierzytelnianie na serwerze SQL Server** lub **Zintegrowane uwierzytelnianie usługi Active Directory**.
   * **Nazwa użytkownika** i **Hasło**. Wprowadź nazwę użytkownika i hasło, jeżeli powyżej wybrano uwierzytelnianie na serwerze SQL Server.
   * Kliknij pozycję **Połącz**.
5. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń węzeł AdventureWorksDW, aby zobaczyć tabele w przykładowej bazie danych.
   
    ![Poznawanie bazy danych AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Uruchamianie przykładowej kwerendy
Teraz, po nawiązaniu połączenia z bazą danych, napiszemy zapytanie.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Skopiuj do okna kwerendy następującą kwerendę T-SQL:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom kwerendę, klikając zieloną strzałkę lub `CTRL` + `SHIFT` + `E`użyj następującego skrótu: .
   
    ![Uruchamianie zapytania](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy możesz się połączyć i zapytać, spróbuj [zwizualizować dane za pomocą usługi Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

Aby skonfigurować środowisko uwierzytelniania usługi Azure Active Directory, zobacz [Uwierzytelnianie w puli SQL](sql-data-warehouse-authentication.md).
