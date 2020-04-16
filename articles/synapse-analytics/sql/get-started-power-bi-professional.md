---
title: Łączenie się z programem Power BI Professional
description: W tym samouczku przejdziemy do kroków, jak podłączyć pulpit usługi Power BI do sql na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0ce8f3a447f1896ae6d96d343782f8cdb44d4c6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422564"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Łączenie się z programem Synapse SQL za pomocą usługi Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Program Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [Sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

W tym samouczku przejdziemy do kroków, jak podłączyć pulpit usługi Power BI do sql na żądanie (wersja zapoznawcza).

## <a name="prerequisites"></a>Wymagania wstępne

Narzędzie do wystawiania zapytań:

- Wybrany klient SQL:

  - Azure Data Studio
  - SQL Server Management Studio

- Zainstalowany pulpit usługi Power BI

Parametry:

| Parametr                                 | Opis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres punktu końcowego usługi SQL na żądanie    | Będzie używany jako nazwa serwera                                   |
| Region punktu końcowego usługi SQL na żądanie     | Będzie używany do określenia, jakiego przechowywania będziemy używać w próbkach |
| Nazwa użytkownika i hasło dostępu do punktu końcowego | Będzie używany do uzyskiwania dostępu do punktu końcowego                               |
| Baza danych, której użyjesz do tworzenia widoków     | Ta baza danych będzie wykorzystywana jako punkt wyjścia w przykładach       |

## <a name="first-time-setup"></a>Konfiguracja po raz pierwszy

Istnieją dwa kroki przed użyciem próbek:

1. Tworzenie bazy danych dla widoków
2. Tworzenie poświadczeń używanych przez sql na żądanie w celu uzyskania dostępu do plików w magazynie

### <a name="create-database"></a>Tworzenie bazy danych

Ponieważ będziesz używać środowiska demo, należy utworzyć własną bazę danych do celów demonstracyjnych. Baza danych jest potrzebna do tworzenia widoków w nim. Użyjesz tej bazy danych w niektórych przykładowych kwerend w tej dokumentacji.

> [!NOTE]
> Należy zauważyć, że bazy danych są używane tylko dla metadanych widoku, a nie dla rzeczywistych danych.
>
> Zapisz nazwę bazy danych, której używasz, będziesz jej potrzebować później.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Tworzenie poświadczeń

Musimy utworzyć poświadczenia, zanim będzie można uruchamiać kwerendy. To poświadczenie będzie używane przez usługę SQL na żądanie, aby uzyskać dostęp do plików w magazynie.

> [!NOTE]
> Należy zauważyć, że należy utworzyć poświadczenia dostępu do konta magazynu. Chociaż SQL na żądanie może uzyskać dostęp do magazynów z różnych regionów, posiadanie magazynu i obszaru roboczego Usługi Azure Synapse w tym samym regionie zapewni lepszą wydajność.

**Fragment kodu dotyczący sposobu tworzenia poświadczeń dla kontenerów danych spisu,** uruchom:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="creating-power-bi-desktop-report"></a>Tworzenie raportu klasycznego usługi Power BI

Otwórz aplikację klasyczną Power BI i wybierz opcję "Pobierz dane".
![Otwórz aplikację klasyczną Power BI i wybierz pozycję Pobierz dane.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Krok 1 - Wybór źródła danych

Wybierz "Azure" w menu, a następnie "Azure SQL Database".
![Wybierz źródło danych.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Krok 2 - Wybierz bazę danych

Napisz adres URL bazy danych i nazwę bazy danych, w której znajduje się widok.
![Wybierz bazę danych w punkcie końcowym.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Następne kroki

Przejdź do [plików magazynu zapytań,](get-started-azure-data-studio.md) aby dowiedzieć się, jak połączyć się z sql na żądanie przy użyciu usługi Azure Data Studio.
 