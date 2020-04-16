---
title: Ciągła integracja i ciągłe wdrażanie
description: Środowisko DevOps bazy danych klasy korporacyjnej do magazynowania danych z wbudowaną obsługą ciągłej integracji i wdrażania przy użyciu usługi Azure Pipelines.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: ddd24eb510405d49465ca4e0e0f326f7260e2ed1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416226"
---
# <a name="continuous-integration-and-deployment-for-data-warehousing"></a>Ciągła integracja i wdrażanie w zakresie magazynowania danych

W tym prostym samouczku opisano, jak zintegrować projekt bazy danych narzędzi danych programu SQL Server (SSDT) z platformą Azure DevOps i wykorzystać usługi Azure Pipelines do skonfigurowania ciągłej integracji i wdrażania. Ten samouczek jest drugim krokiem w tworzeniu potoku ciągłej integracji i wdrażania dla hurtowni danych.

## <a name="before-you-begin"></a>Przed rozpoczęciem

- Przejdź przez [samouczek integracji kontroli źródła](sql-data-warehouse-source-control-integration.md)

- Konfigurowanie programów DevOps i łączenie się z nią

## <a name="continuous-integration-with-visual-studio-build"></a>Ciągła integracja z kompilacją programu Visual Studio

1. Przejdź do usługi Azure Potoki i utworzyć nowy potok kompilacji.

      ![Nowy rurociąg](./media/sql-data-warehouse-continuous-integration-and-deployment/1-new-build-pipeline.png "Nowy potok")

2. Wybierz repozytorium kodu źródłowego (Azure Repos Git) i wybierz szablon aplikacji .NET Desktop.

      ![Ustawienia potoku](./media/sql-data-warehouse-continuous-integration-and-deployment/2-pipeline-setup.png "Ustawienia potoku")

3. Edytuj plik YAML, aby użyć odpowiedniej puli agenta. Twój plik YAML powinien wyglądać mniej więcej tak:

      ![YAML](./media/sql-data-warehouse-continuous-integration-and-deployment/3-yaml-file.png "YAML")

W tym momencie masz proste środowisko, w którym wszelkie zaewidencjonowania do gałęzi głównej repozytorium formantu źródła powinny automatycznie wyzwalać pomyślną kompilację programu Visual Studio projektu bazy danych. Sprawdź poprawność automatyzacji działa od końca do końca, wszedło w projekcie lokalnej bazy danych i zaewidencjonowanie tej zmiany w gałęzi głównej.

## <a name="continuous-deployment-with-the-azure-sql-data-warehouse-or-database-deployment-task"></a>Ciągłe wdrażanie za pomocą zadania wdrażania usługi Azure SQL Data Warehouse (lub Database)

1. Dodaj nowe zadanie przy użyciu [zadania wdrażania usługi Azure SQL Database](/devops/pipelines/targets/azure-sqldb?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) i wypełnij wymagane pola, aby połączyć się z docelowym magazynem danych. Po uruchomieniu tego zadania DACPAC generowane z poprzedniego procesu kompilacji jest wdrażany w magazynie danych docelowych. Można również użyć [zadania wdrażania usługi Azure SQL Data Warehouse](https://marketplace.visualstudio.com/items?itemName=ms-sql-dw.SQLDWDeployment).

      ![Zadanie wdrażania](./media/sql-data-warehouse-continuous-integration-and-deployment/4-deployment-task.png "Zadanie wdrażania")

2. Jeśli używasz agenta hostowanego samodzielnie, upewnij się, że ustawiono zmienną środowiskową, aby używała poprawnego programu SqlPackage.exe dla magazynu danych SQL. Ścieżka powinna wyglądać mniej więcej tak:

      ![Zmienna środowiskowa](./media/sql-data-warehouse-continuous-integration-and-deployment/5-environment-variable-preview.png "Zmienna środowiskowa")

   C:\Pliki programów (x86)\Microsoft Visual Studio\2019\Podgląd\Common7\IDE\Rozszerzenia\Microsoft\SQLDB\DAC\150  

   Uruchamianie i sprawdzanie poprawności potoku. Można wprowadzać zmiany lokalnie i zaewidencjonować zmiany w formancie źródłowym, które powinny generować automatyczną kompilację i wdrażanie.

## <a name="next-steps"></a>Następne kroki

- Poznaj [architekturę MPP puli SQL Synapse](massively-parallel-processing-mpp-architecture.md)
- Szybkie [tworzenie puli SQL](create-data-warehouse-portal.md)
- [Ładowanie przykładowych danych](load-data-from-azure-blob-storage-using-polybase.md)
- Przeglądaj [filmy](sql-data-warehouse-videos.md)
