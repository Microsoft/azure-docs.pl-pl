---
title: Integracja kontroli źródła
description: Środowisko DevOps bazy danych klasy korporacyjnej dla dedykowanej puli SQL z natywną integracją kontroli źródła przy użyciu Azure Repos (Git i GitHub).
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 53adbc0288c28c3b18632539c3f812bbca82da92
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566158"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integracja kontroli źródła dla dedykowanej puli SQL w Azure Synapse Analytics

W tym samouczku przedstawiono sposób integrowania projektu bazy danych SQL Server Data Tools (SSDT) z kontrolą źródła.  Integracja kontroli źródła to pierwszy krok w procesie tworzenia potoku ciągłej integracji i wdrażania z dedykowanym zasobem puli SQL w Azure Synapse Analytics.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

- Rejestracja w organizacji [Azure DevOps organizacji](https://azure.microsoft.com/services/devops/)
- Przejdź przez samouczek [Tworzenie i łączenie](create-data-warehouse-portal.md)
- [Instalowanie Visual Studio 2019 r.](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Konfigurowanie aplikacji i nawiązywanie z Azure DevOps

1. W organizacji Azure DevOps utwórz projekt, który będzie hostować projekt bazy danych SSDT za pośrednictwem repozytorium repozytorium platformy Azure.

   ![Tworzenie projektu](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Tworzenie projektu")

2. Otwórz Visual Studio i połącz się z Azure DevOps projektami z kroku 1, wybierając pozycję **Zarządzaj połączeniem.**

   ![Zarządzanie połączeniami](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Zarządzanie połączeniami")

3. Połącz się z projektem, wybierając **pozycję Zarządzaj połączeniami,** a następnie **pozycję Połącz z projektem.**
 
    ![Połącz 1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Połącz")


4. Znajdź projekt utworzony w kroku 1 i wybierz pozycję **Połącz**.
 
    ![Połącz 2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Połącz")


3. Sklonuj Azure DevOps z projektu na komputer lokalny.

   ![Klonowanie repo](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klonowanie repo")

Aby uzyskać więcej informacji na temat łączenia projektów przy użyciu Visual Studio, zobacz Connect to projects in Team Explorer (Nawiązywanie połączenia [z projektami w programie Team Explorer](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true)). Aby uzyskać wskazówki dotyczące klonowania repozytorium przy użyciu Visual Studio, zapoznaj się z artykułem Clone an exiting Git repo (Klonowanie [wyjściowego repozytorium Git).](/azure/devops/repos/git/clone?tabs=visual-studio) 

## <a name="create-and-connect-your-project"></a>Tworzenie i łączenie projektu

1. W Visual Studio utwórz nowy projekt bazy SQL Server Database z katalogiem i lokalnym repozytorium Git w lokalnym **sklonowanym repozytorium**.

   ![Tworzenie nowego projektu](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Tworzenie nowego projektu")  

2. Kliknij prawym przyciskiem myszy pusty projekt sqlproject i zaimportuj magazyn danych do projektu bazy danych.

   ![Importowanie projektu](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importowanie projektu")  

3. W Team Explorer w Visual Studio zat zatwierdzanie zmian w lokalnym repozytorium Git.

   ![Zatwierdzenie](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Zatwierdzenie")  

4. Teraz, gdy zmiany zostały zatwierdzone lokalnie w sklonowanym repozytorium, zsynchronizuj je i wypchń do repozytorium repozytorium platformy Azure w Azure DevOps projektu.

   ![Synchronizacja i wypychanie — przejściowe](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronizacja i wypychanie — przejściowe")

   ![Synchronizacja i wypychanie](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchronizacja i wypychanie")  

## <a name="validation"></a>Walidacja

1. Sprawdź, czy zmiany zostały wypchnięto do usługi Azure Repo, aktualizując kolumnę tabeli w projekcie bazy danych z Visual Studio SQL Server Data Tools (SSDT).

   ![Weryfikowanie kolumny aktualizacji](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Weryfikowanie kolumny aktualizacji")

2. Zat zatwierdzanie i wypychanie zmiany z repozytorium lokalnego do repozytorium platformy Azure.

   ![Wypychanie zmian](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Wypychanie zmian")

3. Sprawdź, czy zmiana została wypchnięta do repozytorium repozytorium platformy Azure.

   ![Sprawdź](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Weryfikowanie zmian")

4. **(Opcjonalnie**) Użyj funkcji Porównania schematów i zaktualizuj zmiany w docelowej dedykowanej puli SQL przy użyciu programu SSDT, aby upewnić się, że definicje obiektów w repozytorium repozytorium platformy Azure i repozytorium lokalnym odzwierciedlają dedykowaną pulę SQL.

## <a name="next-steps"></a>Następne kroki

- [Tworzenie dla dedykowanej puli SQL](sql-data-warehouse-overview-develop.md)