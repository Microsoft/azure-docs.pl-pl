---
title: Korzystanie z danych usługi Azure Blockchain Workbench w usłudze Microsoft Power BI
description: Dowiedz się, jak załadować i wyświetlać dane bazy danych SQL DB usługi Azure Blockchain Workbench w usłudze Microsoft Power BI.
ms.date: 04/22/2020
ms.topic: how-to
ms.reviewer: sunri
ms.openlocfilehash: 7e0e585ce45616c2402972c725b502f4b704d1cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000151"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Korzystanie z danych usługi Azure Blockchain Workbench w usłudze Microsoft Power BI

Program Microsoft Power BI umożliwia łatwe generowanie zaawansowanych raportów z baz danych SQL DB przy użyciu Power BI Desktop i publikowanie ich w usłudze [https://www.powerbi.com](https://www.powerbi.com) .

Ten artykuł zawiera przewodnik krok po kroku, w którym przedstawiono instrukcje dotyczące połączenia z bazą danych SQL Database usługi Azure Blockchain Workbench z poziomu programu Power BI Desktop, tworzenia raportu i wdrażania go w witrynie powerbi.com.

## <a name="prerequisites"></a>Wymagania wstępne

* Pobierz [Power BI Desktop](https://powerbi.microsoft.com/desktop/).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Łączenie Power BI z danymi w usłudze Azure łańcucha bloków Workbench

1.  Otwórz program Power BI Desktop.
2.  Wybierz pozycję **Pobierz dane**.

    ![Pobieranie danych](./media/data-powerbi/get-data.png)
3.  Wybierz pozycję **SQL Server** z listy typów źródeł danych.

4.  Podaj nazwę serwera i bazy danych w oknie dialogowym. Określ, czy chcesz importować dane lub wykonać zapytanie w trybie **DirectQuery**. Wybierz przycisk **OK**.

    ![Wybieranie pozycji SQL Server](./media/data-powerbi/select-sql.png)

5.  Podaj poświadczenia bazy danych, aby uzyskać dostęp do usługi Azure Blockchain Workbench. Wybierz pozycję **Baza danych** i wprowadź swoje poświadczenia.

    Jeśli używasz poświadczeń utworzonych przez proces wdrażania usługi Azure Blockchain Workbench, nazwa użytkownika to **dbadmin**, a hasło to hasło podane podczas wdrażania.

    ![Ustawienia usługi SQL DB](./media/data-powerbi/db-settings.png)

6.  Po połączeniu z bazą danych w oknie dialogowym **Nawigator** są wyświetlane tabele i widoki dostępne w bazie danych. Widoki są przeznaczone do celów raportowania i wszystkie mają prefiks **vw**.

    ![Przechwytywanie ekranu Power BI pulpicie przy użyciu okna dialogowego Nawigator z wybranym vwContractAction.](./media/data-powerbi/navigator.png)

7.  Wybierz widoki, które mają zostać uwzględnione. W celach demonstracyjnych zawieramy **vwContractAction**, który zawiera szczegółowe informacje o akcjach, które zostały wykonane w ramach kontraktu.

    ![Wybieranie widoków](./media/data-powerbi/select-views.png)

Teraz możesz tworzyć i publikować raporty, tak jak zwykle przy użyciu usługi Power BI.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](database-views.md)