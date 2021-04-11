---
title: Nawiązywanie połączenia z dedykowaną pulą SQL (dawniej SQL DW) za pomocą VSTS
description: Wykonaj zapytanie dotyczące dedykowanej puli SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics przy użyciu programu Visual Studio.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0baf2396b7c5af103f0b3aa223d0bccf725babbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584146"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Nawiązywanie połączenia z dedykowaną pulą SQL (dawniej SQL DW) w usłudze Azure Synapse Analytics przy użyciu programu Visual Studio i SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Użyj programu Visual Studio, aby wysłać zapytanie do dedykowanej puli SQL (dawniej SQL DW) w ramach usługi Azure Synapse w ciągu zaledwie kilku minut. Ta metoda używa rozszerzenia SQL Server Data Tools (SSDT) w programie Visual Studio 2019. 

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć tego samouczka, potrzebne są następujące elementy:

* Istniejąca dedykowana Pula SQL (dawniej SQL DW). Aby go utworzyć, zobacz [Tworzenie dedykowanej puli SQL (dawniej SQL DW)](create-data-warehouse-portal.md).
* Rozszerzenie SSDT dla programu Visual Studio. Jeśli masz program Visual Studio, prawdopodobnie masz już SSDT dla programu Visual Studio. Aby uzyskać instrukcje instalacji i informacje na temat dostępnych opcji, zobacz artykuł [Instalowanie programu Visual Studio i narzędzi SSDT](sql-data-warehouse-install-visual-studio.md).
* W pełni kwalifikowana nazwa serwera SQL. Aby znaleźć te informacje, zobacz [nawiązywanie połączenia z dedykowaną pulą SQL (dawniej SQL DW)](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. Nawiąż połączenie z dedykowaną pulą SQL (dawniej SQL DW)
1. Otwórz program Visual Studio 2019.
2. Otwórz Eksplorator obiektów SQL Server, wybierając pozycję **Wyświetl**  >  **Eksplorator obiektów SQL Server**.
   
    ![Eksplorator obiektów SQL Server](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Kliknij **ikonę dodawania serwera SQL**.
   
    ![Dodawanie serwera SQL](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Wypełnij pola w oknie łączenia z serwerem.
   
    ![Łączenie z serwerem](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Nazwa serwera**. Wprowadź wcześniej ustaloną **nazwę serwera**.
   * **Uwierzytelnianie**. Wybierz opcję **Uwierzytelnianie na serwerze SQL Server** lub **Zintegrowane uwierzytelnianie usługi Active Directory**.
   * **Nazwa użytkownika** i **hasło**. Wprowadź nazwę użytkownika i hasło, jeżeli powyżej wybrano uwierzytelnianie na serwerze SQL Server.
   * Kliknij przycisk **Połącz**.
5. W celach poznawczych rozwiń węzeł serwera Azure SQL. Możesz przejrzeć skojarzone z serwerem bazy danych. Rozwiń węzeł AdventureWorksDW, aby zobaczyć tabele w przykładowej bazie danych.
   
    ![Poznawanie bazy danych AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Uruchom przykładowe zapytanie
Teraz, po nawiązaniu połączenia z bazą danych, napiszemy zapytanie.

1. Kliknij prawym przyciskiem myszy bazę danych w Eksploratorze obiektów SQL Server.
2. Wybierz pozycję **Nowe zapytanie**. Otworzy się okno nowego zapytania.
   
    ![Nowe zapytanie](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Skopiuj następujące zapytanie T-SQL do okna zapytania:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Uruchom zapytanie, klikając zieloną strzałkę lub użyj następującego skrótu: `CTRL` + `SHIFT` + `E` .
   
    ![Uruchamianie zapytania](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Przejrzyj wyniki zapytania. W tym przykładzie tabela FactInternetSales ma 60398 wierszy.
   
    ![Wyniki zapytania](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy można nawiązać połączenie i wykonywać zapytania, spróbuj [wizualizować dane za pomocą Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect).

Aby skonfigurować środowisko do uwierzytelniania Azure Active Directory, zobacz [uwierzytelnianie w dedykowanej puli SQL (dawniej SQL DW)](sql-data-warehouse-authentication.md).