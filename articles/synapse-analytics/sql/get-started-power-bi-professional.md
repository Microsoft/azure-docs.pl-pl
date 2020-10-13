---
title: Nawiązywanie połączenia z programem Power BI Professional
description: W tym samouczku wykonamy instrukcje dotyczące sposobu łączenia Power BI pulpitu z programem SQL na żądanie (wersja zapoznawcza).
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 48b6639ad8228347737669fffca06eb660a25874
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91288702"
---
# <a name="connect-to-synapse-sql-with-power-bi-professional"></a>Nawiązywanie połączenia z usługą Synapse SQL przy użyciu programu Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Program Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

W tym samouczku przedstawiono procedurę łączenia Power BI Desktop z programem SQL na żądanie (wersja zapoznawcza).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wystawiać zapytania, potrzebne są następujące narzędzia:

- Wybrany klient SQL:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI zainstalowany pulpit

Parametry:

| Parametr                                 | Opis                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Adres punktu końcowego usługi SQL na żądanie    | Zostanie użyta jako nazwa serwera                                   |
| Region punktu końcowego usługi SQL na żądanie     | Zostanie użyta do określenia magazynu, który będzie używany w przykładach |
| Nazwa użytkownika i hasło dostępu do punktu końcowego | Zostanie użyta w celu uzyskania dostępu do punktu końcowego                               |
| Baza danych, która będzie używana do tworzenia widoków     | Ta baza danych zostanie użyta jako punkt wyjścia w przykładach       |

## <a name="first-time-setup"></a>Konfiguracja pierwszego czasu

Przed rozpoczęciem korzystania z przykładów należy wykonać dwie czynności:

1. Tworzenie bazy danych dla widoków
2. Utwórz poświadczenia, które będą używane przez SQL na żądanie do uzyskiwania dostępu do plików w magazynie

### <a name="create-database"></a>Tworzenie bazy danych

W tym artykule wprowadzającym należy utworzyć własną bazę danych, która będzie używana jako Demonstracja. Do utworzenia widoków jest wymagana baza danych. Ta baza danych będzie używana w niektórych przykładowych zapytaniach w tej dokumentacji.

> [!NOTE]
> Bazy danych są używane tylko do wyświetlania metadanych, a nie dla rzeczywistych danych.
>
> Zapisz nazwę używanej bazy danych, która będzie potrzebna później.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Utwórz poświadczenia

Aby można było uruchamiać zapytania, musimy utworzyć poświadczenia. To poświadczenie będzie używane przez usługę SQL na żądanie do uzyskiwania dostępu do plików w magazynie.

> [!NOTE]
> Musisz utworzyć poświadczenia dla dostępu do konta magazynu. Chociaż usługa SQL na żądanie może uzyskać dostęp do magazynu z różnych regionów, posiadanie magazynu i obszaru roboczego usługi Azure Synapse w tym samym regionie zapewni lepszą wydajność.

**Fragment kodu dotyczący tworzenia poświadczeń dla kontenerów danych spisu**i uruchamiania:

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

## <a name="create-a-power-bi-desktop-report"></a>Tworzenie raportu na pulpicie Power BI

Otwórz aplikację klasyczną Power BI i wybierz opcję **Pobierz dane** .

![Otwórz aplikację klasyczną Power BI i wybierz pozycję Pobierz dane.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Krok 1. Wybieranie źródła danych

Wybierz pozycję **Azure** w menu, a następnie **Azure SQL Database**.
![Wybierz pozycję źródło danych.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Krok 2. Wybieranie bazy danych

Zapisz adres URL bazy danych i nazwę bazy danych, w której znajduje się widok.
![Wybierz bazę danych w punkcie końcowym.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Następne kroki

Przejdź do [plików magazynu](get-started-azure-data-studio.md) , aby dowiedzieć się, jak nawiązać połączenie z usługą SQL na żądanie przy użyciu Azure Data Studio.
 