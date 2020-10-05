---
title: Integracja kontroli źródła
description: Środowisko DevOps Database klasy korporacyjnej dla puli SQL z integracją natywnej kontroli źródła przy użyciu Azure Repos (git i GitHub).
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 84abfea39cb7311e7cd60346d936c08c28c334d4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89441325"
---
# <a name="source-control-integration-for-sql-pool"></a>Integracja kontroli źródła dla puli SQL

W tym samouczku opisano sposób integrowania projektu bazy danych SQL Server Data Tools (SSDT) z kontrolą źródła.  Integracja kontroli źródła to pierwszy krok w tworzeniu potoku ciągłej integracji i wdrażania przy użyciu zasobu puli SQL w usłudze Azure Synapse Analytics.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

- Utwórz konto w [organizacji usługi Azure DevOps](https://azure.microsoft.com/services/devops/)
- Przejdź do samouczka [Tworzenie i łączenie](create-data-warehouse-portal.md)
- [Zainstaluj program Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Konfigurowanie usługi Azure DevOps i nawiązywanie z nią połączenia

1. W organizacji usługi Azure DevOps Utwórz projekt, który będzie hostować projekt bazy danych SSDT za pośrednictwem repozytorium repozytorium Azure.

   ![Utwórz projekt](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Utwórz projekt")

2. Otwórz program Visual Studio i Połącz się z organizacją usługi Azure DevOps i projektem z tego kroku, wybierając pozycję **Zarządzaj połączeniem**.

   ![Zarządzanie połączeniami](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Zarządzanie połączeniami")

3. Połącz się z projektem, wybierając pozycję **Zarządzaj połączeniami**, a następnie połącz się z **projektem**.
 ![Connect1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Connect")


4. Znajdź projekt utworzony w kroku jeden, wybierz pozycję **Połącz**.
![Connect2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Connect")


3. Sklonuj repozytorium Azure DevOps z projektu na komputerze lokalnym.

   ![Klonowanie repozytorium](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klonowanie repozytorium")

Aby uzyskać więcej informacji na temat łączenia projektów przy użyciu programu Visual Studio, zobacz [łączenie się z projektami w Team Explorer](https://docs.microsoft.com/visualstudio/ide/connect-team-project?view=vs-2019). Aby uzyskać wskazówki dotyczące klonowania repozytorium za pomocą programu Visual Studio, zapoznaj się z artykułem [klonowanie wyjścia z repozytorium git](https://docs.microsoft.com/azure/devops/repos/git/clone?view=azure-devops&tabs=visual-studio) . 

## <a name="create-and-connect-your-project"></a>Tworzenie i łączenie projektu

1. W programie Visual Studio Utwórz nowy projekt bazy danych SQL Server z katalogiem i lokalnym repozytorium Git w **lokalnym sklonowanym repozytorium**.

   ![Tworzenie nowego projektu](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Tworzenie nowego projektu")  

2. Kliknij prawym przyciskiem myszy pusty program sqlproject i zaimportuj magazyn danych do projektu bazy danych.

   ![Importuj projekt](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importuj projekt")  

3. W Team Explorer w programie Visual Studio Zatwierdź zmiany w lokalnym repozytorium git.

   ![Zatwierdzenie](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Zatwierdzenie")  

4. Teraz, gdy zmiany zostały zatwierdzone lokalnie w sklonowanym repozytorium, zsynchronizuj i wypchnij zmiany do repozytorium repozytorium platformy Azure w projekcie usługi Azure DevOps.

   ![Synchronizacja i wypychanie](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronizacja i wypychanie")

   ![Synchronizuj i wypchnij](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchronizuj i wypchnij")  

## <a name="validation"></a>Walidacja

1. Sprawdź, czy zmiany zostały wypchnięte do repozytorium platformy Azure przez zaktualizowanie kolumny tabeli w projekcie bazy danych za pomocą programu Visual Studio SQL Server Data Tools (SSDT).

   ![Sprawdź poprawność kolumny aktualizacji](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Sprawdź poprawność kolumny aktualizacji")

2. Zatwierdź i wypchnij zmianę z repozytorium lokalnego do repozytorium platformy Azure.

   ![Wypychanie zmian](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Wypychanie zmian")

3. Sprawdź, czy zmiana została wypchnięcia w repozytorium repozytorium platformy Azure.

   ![Sprawdź](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Weryfikowanie zmian")

4. (**Opcjonalnie**) Użyj opcji Porównaj schemat i zaktualizuj zmiany w docelowym hurtowni danych przy użyciu SSDT, aby upewnić się, że definicje obiektów w repozytorium Azure Repository i repozytorium lokalnym odzwierciedlają magazyn danych.

## <a name="next-steps"></a>Następne kroki

- [Programowanie dla puli SQL](sql-data-warehouse-overview-develop.md)
