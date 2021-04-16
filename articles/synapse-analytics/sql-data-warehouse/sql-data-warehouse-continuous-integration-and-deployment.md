---
title: Ciągła integracja i ciągłe wdrażanie dedykowanej puli SQL
description: Środowisko DevOps bazy danych klasy korporacyjnej dla dedykowanej puli SQL Azure Synapse Analytics z wbudowaną obsługą ciągłej integracji i ciągłego wdrażania przy użyciu Azure Pipelines.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 95bf3a8c614b8b7c0269257cb62b3c3a0f60be13
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568288"
---
# <a name="continuous-integration-and-deployment-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Ciągła integracja i ciągłe wdrażanie dedykowanej puli SQL w Azure Synapse Analytics

W tym prostym samouczku przedstawiono sposób integracji projektu bazy danych narzędzi SQL Server Data Tools (SSDT) z programem Azure DevOps i wykorzystania Azure Pipelines do skonfigurowania ciągłej integracji i ciągłego wdrażania. Ten samouczek jest drugim krokiem tworzenia potoku ciągłej integracji i wdrażania na rzecz magazynowania danych.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

- Przejdź przez [samouczek integracji kontroli źródła](sql-data-warehouse-source-control-integration.md)

- Konfigurowanie aplikacji i nawiązywanie z Azure DevOps

## <a name="continuous-integration-with-visual-studio-build"></a>Ciągła integracja z Visual Studio kompilacji

1. Przejdź do Azure Pipelines i utwórz nowy potok kompilacji.

      ![Nowy potok](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nowy potok")

2. Wybierz repozytorium kodu źródłowego (Azure Repos Git) i wybierz szablon aplikacji klasycznej .NET.

      ![Konfiguracja potoku](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Konfiguracja potoku")

3. Edytuj plik YAML, aby użyć odpowiedniej puli agenta. Plik YAML powinien wyglądać podobnie do tego:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

W tym momencie masz proste środowisko, w którym każde zaewidencjowanie w głównej gałęzi repozytorium kontroli kodu źródłowego powinno automatycznie wyzwolić pomyślną Visual Studio kompilacji projektu bazy danych. Sprawdź, czy automatyzacja działa w sposób end-to-end, dokonując zmiany w lokalnym projekcie bazy danych i sprawdzając, czy zmiana ta zostanie w gałęzi main.

## <a name="continuous-deployment-with-the-azure-synapse-analytics-or-database-deployment-task"></a>Ciągłe wdrażanie za pomocą Azure Synapse Analytics (lub bazy danych)

1. Dodaj nowe zadanie przy użyciu [Azure SQL Database wdrożenia](/azure/devops/pipelines/targets/azure-sqldb) i wypełnij pola wymagane do nawiązania połączenia z docelowym magazynem danych. Gdy to zadanie jest uruchamiane, DACPAC wygenerowany na podstawie poprzedniego procesu kompilacji jest wdrażany do docelowego magazynu danych. Można również użyć zadania [Azure Synapse Analytics wdrożenia.](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment)

      ![Zadanie wdrażania](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Zadanie wdrażania")

2. Jeśli używasz własnego agenta, upewnij się, że ustawiono zmienną środowiskową tak, aby używać poprawnej SqlPackage.exe dla Azure Synapse Analytics. Ścieżka powinna wyglądać podobnie do tej:

      ![Zmienna środowiskowa](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Zmienna środowiskowa")

   C:\Program Files (x86)\Microsoft Visual Studio\2019\Preview\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\150  

   Uruchom i zweryfikuj potok. Możesz wprowadzać zmiany lokalnie i zaewidencjować zmiany w kontroli źródła, które powinny generować automatyczne kompilowanie i wdrażanie.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z architekturą dedykowanej puli SQL (dawniej SQL DW)](massively-parallel-processing-mpp-architecture.md)
- Szybkie [tworzenie dedykowanej puli SQL (dawniej SQL DW)](create-data-warehouse-portal.md)
- [Ładowanie danych przykładowych](./load-data-from-azure-blob-storage-using-copy.md)
- Eksplorowanie [filmów wideo](sql-data-warehouse-videos.md)