---
title: Wykonywanie zapytań dotyczących danych usługi Azure łańcucha bloków Workbench przy użyciu SQL Server Management Studio
description: Dowiedz się, jak nawiązać połączenie z bazą danych SQL usługi Azure Blockchain Workbench z poziomu programu SQL Server Management Studio.
ms.date: 11/20/2019
ms.topic: how-to
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: b316d2579e1dde98b04a2dc7652a9721c7f8de77
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85214217"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Korzystanie z danych usługi Azure Blockchain Workbench w programie SQL Server Management Studio

Microsoft SQL Server Management Studio zapewnia możliwość szybkiego zapisywania i testowania zapytań względem bazy danych SQL usługi Azure łańcucha bloków Workbench. Ta sekcja zawiera przewodnik krok po kroku dotyczący sposobu nawiązywania połączenia z SQL Databaseem usługi Azure łańcucha bloków Workbench z poziomu SQL Server Management Studio.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz program [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Łączenie programu SQL Server Management Studio z danymi w usłudze Azure Blockchain Workbench

1. Otwórz program SQL Server Management Studio i wybierz pozycję **Połącz**.
2. Wybierz pozycję **Aparat bazy danych**.

    ![Aparat bazy danych](./media/data-sql-management-studio/database-engine.png)

3. W oknie dialogowym **Łączenie z serwerem** wprowadź nazwę serwera i poświadczenia bazy danych.

    Jeśli używasz poświadczeń utworzonych przez proces wdrażania usługi Azure Blockchain Workbench, nazwa użytkownika to **dbadmin**, a hasło to hasło podane podczas wdrażania.

    ![Wprowadzanie poświadczeń usługi SQL](./media/data-sql-management-studio/sql-creds.png)

   1. Program SQL Server Management Studio wyświetla listę baz danych, widoków bazy danych i procedur składowanych w bazie danych usługi Azure Blockchain Workbench.

      ![Lista baz danych](./media/data-sql-management-studio/db-list.png)

5. Aby wyświetlić dane skojarzone z dowolnymi widokami bazy danych, możesz automatycznie wygenerować instrukcję select, wykonując następujące kroki.
6. Kliknij prawym przyciskiem myszy dowolny widok bazy danych w Eksplorator obiektów.
7. Wybierz pozycję **Skrypt widoku jako**.
8. Wybierz pozycję **SELECT to**.
9. Wybierz pozycję **Nowe okno edytora zapytań**.
10. Nowe zapytanie można utworzyć, wybierając pozycję **Nowe zapytanie**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](database-views.md)
