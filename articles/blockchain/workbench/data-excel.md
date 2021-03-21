---
title: Korzystanie z danych usługi Azure Blockchain Workbench w programie Microsoft Excel
description: Dowiedz się, jak ładować i wyświetlać dane SQL usługi Azure łańcucha bloków Workbench w wersji zapoznawczej w programie Microsoft Excel.
ms.date: 09/05/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: 4dd941abdded6a93510f4a71119769cd73855bcc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000168"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Wyświetlanie danych usługi Azure Blockchain Workbench za pomocą programu Microsoft Excel

Za pomocą programu Microsoft Excel można wyświetlać dane z bazy danych SQL DB usługi Azure Blockchain Workbench. W tym artykule przedstawiono kroki, które należy wykonać w celu:

* Nawiązania połączenia z bazą danych usługi Blockchain Workbench z poziomu programu Microsoft Excel
* Wyświetlenia tabel i widoków bazy danych usługi Blockchain Workbench
* Załadowania danych widoku usługi Blockchain Workbench do programu Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Nawiązywanie połączenia z bazą danych usługi Workbench Blockchain

Aby nawiązać połączenie z bazą danych usługi Blockchain Workbench:

1. Otwórz program Microsoft Excel.
2. Na karcie **Dane** wybierz pozycję **Pobierz dane**.
3. Wybierz pozycję **Z platformy Azure**, a następnie wybierz pozycję **Z usługi Azure SQL Database**.

   ![Łączenie z bazą danych Azure SQL Database](./media/data-excel/connect-sql-db.png)

4. W oknie dialogowym **Baza danych programu SQL Server**:

    * W polu **Serwer** wprowadź nazwę serwera usługi Blockchain Workbench.
    * W polu **Baza danych (opcjonalnie)** wprowadź nazwę bazy danych.

   ![Podawanie nazwy serwera bazy danych i bazy danych](./media/data-excel/provide-server-db.png)

5. Na pasku nawigacyjnym okna dialogowego **Baza danych programu SQL Server** wybierz pozycję **Baza danych**. Wprowadź **nazwę użytkownika** i **hasło**, a następnie wybierz pozycję **Połącz**.

    > [!NOTE]
    > Jeśli używasz poświadczeń utworzonych podczas procesu wdrażania usługi Azure Blockchain Workbench, **Nazwa użytkownika** to `dbadmin`. **Hasło** to hasło utworzone podczas wdrażania usługi Blockchain Workbench.
    
   ![Podawanie poświadczeń w celu uzyskania dostępu do bazy danych](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Przeglądanie tabel i widoków baz danych

Okno dialogowe Nawigator programu Excel zostanie otwarte po nawiązaniu połączenia z bazą danych. Nawigator umożliwia przyjrzenie się tabelom i widokom w bazie danych. Widoki są przeznaczone do celów raportowania i ich nazwy są poprzedzone prefiksem **vw**.

   ![Podgląd widoku w nawigatorze programu Excel](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Ładowanie danych widoku do skoroszytu programu Excel

W następnym przykładzie pokazano, jak załadować dane z widoku do skoroszytu programu Excel.

1. Na pasku przewijania **Nawigatora** wybierz widok **vwContractAction**. Podgląd widoku **vwContractAction** pokazuje wszystkie akcje związane z kontraktem w bazie danych usługi Blockchain Workbench.
2. Wybierz pozycję **Załaduj**, aby pobrać wszystkie dane w widoku i umieścić je w skoroszycie programu Excel.

   ![Dane załadowane z widoku](./media/data-excel/view-data.png)

Teraz, po załadowaniu danych, możesz użyć funkcji programu Excel w celu utworzenia własnych raportów przy użyciu metadanych i danych transakcji z bazy danych usługi Azure Blockchain Workbench.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Widoki bazy danych w usłudze Azure Blockchain Workbench](database-views.md)