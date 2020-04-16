---
title: Integracja kontroli źródła
description: Środowisko DevOps bazy danych klasy korporacyjnej dla puli SQL z natywną integracją kontroli źródła przy użyciu usługi Azure Repos (Git i GitHub).
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 3ec52c5274891619cf7976e99b5241bfc67a4076
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415098"
---
# <a name="source-control-integration-for-sql-pool"></a>Integracja kontroli źródła dla puli SQL

W tym samouczku opisano, jak zintegrować projekt bazy danych narzędzi danych programu SQL Server (SSDT) z kontrolą źródła.  Integracja kontroli źródła jest pierwszym krokiem w tworzeniu potoku ciągłej integracji i wdrażania z zasobem puli SQL w usłudze Azure Synapse Analytics.

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Zarejestruj się w [organizacji Azure DevOps](https://azure.microsoft.com/services/devops/)
- Przejdź przez samouczek [Tworzenie i łączenie](create-data-warehouse-portal.md)
- [Instalowanie programu Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Konfigurowanie programów DevOps i łączenie się z nią

1. W organizacji programu Azure DevOps utwórz projekt, który będzie hostował projekt bazy danych SSDT za pośrednictwem repozytorium usługi Azure Repo

   ![Tworzenie projektu](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Tworzenie projektu")

2. Otwórz program Visual Studio i połącz się z organizacją i projektem usługi Azure DevOps z kroku 1, wybierając opcję "Zarządzaj połączeniami"

   ![Zarządzanie połączeniami](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Zarządzanie połączeniami")

   ![Połącz](./media/sql-data-warehouse-source-control-integration/3-connect.png "Połącz")

3. Klonowanie repozytorium usługi Azure repozytorium z projektu na komputerze lokalnym

   ![Repo klonowania](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Repo klonowania")

## <a name="create-and-connect-your-project"></a>Tworzenie i łączenie projektu

1. W programie Visual Studio utwórz nowy projekt bazy danych programu SQL Server z katalogiem i lokalnym repozytorium Git w **lokalnym sklonowanym repozytorium**

   ![Tworzenie nowego projektu](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Tworzenie nowego projektu")  

2. Kliknij prawym przyciskiem myszy pusty projekt sqlproject i zaimportuj magazyn danych do projektu bazy danych

   ![Importowanie projektu](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importowanie projektu")  

3. W eksploratorze zespołu w programie Visual Studio zaaproponuj wszystkie zmiany w lokalnym repozytorium Git

   ![Zatwierdzenie](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Zatwierdzenie")  

4. Teraz, gdy masz zmiany zatwierdzone lokalnie w sklonowanym repozytorium, zsynchronizuj i wyprowaj zmiany do repozytorium usługi Azure Repo w projekcie Azure DevOps.

   ![Synchronizacja i wypychanie — przemieszczania](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronizacja i wypychanie — przemieszczania")

   ![Synchronizacja i wypychanie](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchronizacja i wypychanie")  

## <a name="validation"></a>Walidacja

1. Sprawdź, czy zmiany zostały wypychane do repozytorium usługi Azure, aktualizując kolumnę tabeli w projekcie bazy danych za pomocą narzędzia danych programu Visual Studio SQL Server Data Tools (SSDT)

   ![Sprawdzanie poprawności kolumny aktualizacji](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Sprawdzanie poprawności kolumny aktualizacji")

2. Zatwierdzanie i wypychanie zmian z lokalnego repozytorium do usługi Azure Repo

   ![Wypychanie zmian](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Wypychanie zmian")

3. Sprawdź, czy zmiana została wypchnięta w repozytorium repozytorium usługi Azure

   ![Sprawdź](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Weryfikowanie zmian")

4. (**Opcjonalnie**) Użyj porównania schematu i zaktualizuj zmiany w docelowym magazynie danych przy użyciu narzędzia SSDT, aby upewnić się, że definicje obiektów w repozytorium repozytorium usługi Azure i repozytorium lokalnym odzwierciedlają magazyn danych

## <a name="next-steps"></a>Następne kroki

- [Tworzenie dla tworzenia dla puli SQL](sql-data-warehouse-overview-develop.md)
