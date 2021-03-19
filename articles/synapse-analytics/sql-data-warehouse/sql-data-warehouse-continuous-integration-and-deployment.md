---
title: Ciągła integracja i wdrażanie dedykowanej puli SQL
description: DevOps Database klasy korporacyjnej dla dedykowanej puli SQL w usłudze Azure Synapse Analytics dzięki wbudowanej obsłudze ciągłej integracji i wdrażania przy użyciu Azure Pipelines.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: d43039c98e991cd23a8e5c4fb866a8e3dcab6fc2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585642"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Ciągła integracja i wdrażanie dedykowanej puli SQL w usłudze Azure Synapse Analytics

W tym prostym samouczku przedstawiono sposób integrowania projektu bazy danych SQL Server Data Tools (SSDT) z usługą Azure DevOps i korzystania z Azure Pipelines w celu skonfigurowania ciągłej integracji i wdrażania. Ten samouczek to drugi krok tworzenia potoku ciągłej integracji i wdrażania na potrzeby magazynowania danych.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

- Zapoznaj się z [samouczkiem dotyczącym integracji kontroli źródła](sql-data-warehouse-source-control-integration.md)

- Konfigurowanie usługi Azure DevOps i nawiązywanie z nią połączenia

## <a name="continuous-integration-with-visual-studio-build"></a>Ciągła integracja z kompilacją programu Visual Studio

1. Przejdź do Azure Pipelines i Utwórz nowy potok kompilacji.

      ![Nowy potok](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nowy potok")

2. Wybierz repozytorium kodu źródłowego (Azure Repos Git) i wybierz szablon aplikacji klasycznej platformy .NET.

      ![Konfiguracja potoku](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Konfiguracja potoku")

3. Edytuj plik YAML, aby używać odpowiedniej puli agenta. Plik YAML powinien wyglądać następująco:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

W tym momencie istnieje proste środowisko, w którym każde ewidencjonowanie głównej gałęzi w repozytorium kontroli źródła powinno automatycznie wyzwolić pomyślną kompilację projektu bazy danych w programie Visual Studio. Sprawdź, czy Automatyzacja działa na zakończenie, wprowadzając zmianę w lokalnym projekcie bazy danych i sprawdzając tę zmianę w gałęzi głównej.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Ciągłe wdrażanie za pomocą zadania wdrażania usługi Azure Synapse Analytics (lub bazy danych)

1. Dodaj nowe zadanie przy użyciu [zadania wdrażania Azure SQL Database](/azure/devops/pipelines/targets/azure-sqldb) i Wypełnij wymagane pola, aby nawiązać połączenie z docelowym magazynem danych. Po uruchomieniu tego zadania DACPAC wygenerowanego przez poprzedni proces kompilacji jest wdrażany w docelowym magazynie danych. Można również użyć [zadania wdrożenia usługi Azure Synapse Analytics](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment).

      ![Zadanie wdrażania](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Zadanie wdrażania")

2. Jeśli używasz agenta samoobsługowego, upewnij się, że ustawisz zmienną środowiskową tak, aby korzystała z poprawnej SqlPackage.exe usługi Azure Synapse Analytics. Ścieżka powinna wyglądać następująco:

      ![Zmienna środowiskowa](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Zmienna środowiskowa")

   C:\Program Files (x86) \Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Uruchom i sprawdź poprawność potoku. Zmiany można wprowadzać lokalnie i zaewidencjonować zmiany w kontroli źródła, które powinny generować automatyczne Kompilowanie i wdrażanie.

## <a name="next-steps"></a>Następne kroki

- Eksplorowanie [dedykowanej puli SQL (dawniej SQL DW)](massively-parallel-processing-mpp-architecture.md)
- Szybkie [Tworzenie dedykowanej puli SQL (dawniej SQL DW)](create-data-warehouse-portal.md)
- [Ładowanie danych przykładowych](./load-data-from-azure-blob-storage-using-copy.md)
- Eksploruj [wideo](sql-data-warehouse-videos.md)