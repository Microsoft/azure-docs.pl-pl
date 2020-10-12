---
title: 'Samouczek: Migrowanie danych zdarzenia do usługi Azure Synapse Analytics — Azure Event Hubs'
description: 'Samouczek: w tym samouczku pokazano, jak przechwytywać dane z centrum zdarzeń do usługi Azure Synapse Analytics przy użyciu funkcji platformy Azure wyzwalanej przez usługę Event Grid.'
services: event-hubs
ms.date: 06/23/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: b2a35647422c91d6859e1889f906ae512ce41a56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89436616"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Samouczek: Migrowanie przechwyconych danych Event Hubs do usługi Azure Synapse Analytics przy użyciu Event Grid i Azure Functions

Funkcja [Capture](./event-hubs-capture-overview.md) usługi Event Hubs to najprostszy sposób, aby automatycznie dostarczać przesyłane strumieniowo dane z usługi Event Hubs do usługi Azure Blob Storage lub Azure Data Lake Store. Następnie możesz przetwarzać i dostarczać dane do innych wybranych miejsc docelowych magazynu, takich jak Azure Synapse Analytics lub Cosmos DB. W tym samouczku dowiesz się, jak przechwytywać dane z centrum zdarzeń do usługi Azure Synapse Analytics przy użyciu funkcji platformy Azure wyzwalanej przez usługę [Event Grid](../event-grid/overview.md).

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

- Najpierw należy utworzyć centrum zdarzeń z włączoną funkcją **Capture** i ustawić usługę Azure Blob Storage jako miejsce docelowe. Dane wygenerowane przez element WindTurbineGenerator będą przesyłane strumieniowo do centrum zdarzeń i automatycznie zapisywane w usłudze Azure Storage jako pliki Avro.
- Następnie należy utworzyć subskrypcję usługi Azure Event Grid, w której przestrzeń nazw usługi Event Hubs jest źródłem, a punkt końcowy funkcji platformy Azure — miejscem docelowym.
- Zawsze gdy nowy plik Avro zostanie dostarczony do obiektu blob usługi Azure Storage przez funkcję Capture usługi Event Hubs, usługa Event Grid powiadomi funkcję platformy Azure za pomocą identyfikatora URI obiektu blob. Następnie funkcja migruje dane z obiektu BLOB do usługi Azure Synapse Analytics.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>
> - Wdrażanie infrastruktury
> - Publikowanie kodu do aplikacji usługi Functions
> - Tworzenie subskrypcji usługi Event Grid na podstawie aplikacji usługi Functions
> - Przesyłanie strumieniowe danych przykładowych do usługi Event Hub
> - Weryfikowanie przechwyconych danych w usłudze Azure Synapse Analytics

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Program Visual studio 2019](https://www.visualstudio.com/vs/). Podczas instalacji zainstaluj następujące pakiety robocze: programowanie aplikacji klasycznych dla platformy .NET, programowanie na platformie Azure, tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych, programowanie na platformie Node.js i programowanie w języku Python.
- Pobierz [przykład git](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/EventHubsCaptureEventGridDemo) przykładowe rozwiązanie zawiera następujące składniki:

  - *WindTurbineDataGenerator* — prosty wydawca, który wysyła przykładowe dane turbiny wiatrowej do centrum zdarzeń, w którym włączono funkcję Capture.
  - *FunctionDWDumper* — funkcja platformy Azure, która odbiera powiadomienia usługi Event Grid, gdy plik Avro zostanie zapisany w obiekcie blob w usłudze Azure Storage. Otrzymuje ścieżkę identyfikatora URI obiektu BLOB, odczytuje jego zawartość i wypycha te dane do usługi Azure Synapse Analytics.

  Ten przykład używa najnowszego pakietu Azure. Messaging. EventHubs. Stary przykład wykorzystujący pakiet Microsoft. Azure. EventHubs można znaleźć [tutaj](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).

### <a name="deploy-the-infrastructure"></a>Wdrażanie infrastruktury

Użyj programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, aby wdrożyć infrastrukturę potrzebną w tym samouczku, korzystając z tego [szablonu usługi Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json). Ten szablon umożliwia utworzenie następujących zasobów:

- Centrum Event Hub z włączoną funkcją Capture
- Konto usługi Azure Storage na potrzeby zapisanych danych zdarzenia
- Plan usługi Azure App Service do hostowania aplikacji usługi Functions
- Aplikacja funkcji do przetwarzania plików zapisanych zdarzeń
- Logiczny serwer SQL do hostowania hurtowni danych
- Usługa Azure Synapse Analytics do przechowywania zmigrowanych danych

Następujące sekcje zawierają polecenia interfejsu wiersza polecenia platformy Azure i programu Azure PowerShell umożliwiające wdrożenie infrastruktury wymaganej na potrzeby tego samouczka. Przed uruchomieniem poleceń należy zaktualizować nazwy następujących obiektów: 

- Grupa zasobów platformy Azure 
- Region grupy zasobów
- Przestrzeń nazw usługi Event Hubs
- Centrum zdarzeń
- Logiczny serwer SQL
- Nazwa użytkownika bazy danych SQL (i hasło)
- Baza danych Azure SQL Database
- Azure Storage 
- Aplikacja usługi Azure Functions

Utworzenie wszystkich artefaktów platformy Azure przy użyciu skryptów może zająć trochę czasu. Zanim przejdziesz dalej, zaczekaj na ukończenie wykonywania skryptu. Jeśli z jakiegoś powodu wdrażanie nie powiedzie się, usuń grupę zasobów, napraw zgłoszony problem i ponownie uruchom polecenie. 

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby wdrożyć szablon przy użyciu interfejsu wiersza polecenia platformy Azure, użyj następujących poleceń:

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create `
  --resource-group rgDataMigrationSample `
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json `
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Aby wdrożyć szablon przy użyciu programu PowerShell, użyj następujących poleceń:

```powershell
New-AzResourceGroup -Name rgDataMigration -Location westcentralus

New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

### <a name="create-a-table-in-azure-synapse-analytics"></a>Tworzenie tabeli w usłudze Azure Synapse Analytics

Utwórz tabelę w usłudze Azure Synapse Analytics, uruchamiając skrypt [CreateDataWarehouseTable. SQL](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) przy użyciu [programu Visual Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), [SQL Server Management Studio](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-query-ssms.md)lub edytora zapytań w portalu. 

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-code-to-the-functions-app"></a>Publikowanie kodu do aplikacji usługi Functions

1. Otwórz rozwiązanie *EventHubsCaptureEventGridDemo. sln* w programie Visual Studio 2019.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję *FunctionEGDWDumper* i wybierz polecenie **Opublikuj**.

   ![Publikowanie aplikacji funkcji](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Wybierz pozycję **Aplikacja funkcji platformy Azure**, a następnie pozycję **Wybierz istniejące**. Kliknij pozycję **Opublikuj**.

   ![Docelowa aplikacja funkcji](./media/store-captured-data-data-warehouse/pick-target.png)

1. Wybierz aplikację funkcji, która została wdrożona za pomocą szablonu. Wybierz przycisk **OK**.

   ![Wybieranie aplikacji funkcji](./media/store-captured-data-data-warehouse/select-function-app.png)

1. Gdy program Visual Studio skonfiguruje profil, wybierz pozycję **Opublikuj**.

   ![Wybieranie pozycji Opublikuj](./media/store-captured-data-data-warehouse/select-publish.png)

Po opublikowaniu funkcji możesz przystąpić do subskrybowania zdarzenia Capture w usłudze Event Hubs.


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Tworzenie subskrypcji usługi Event Grid na podstawie aplikacji usługi Functions
 
1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). Wybierz swoją grupę zasobów i aplikację funkcji.

   ![Wyświetlanie aplikacji funkcji](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Wybierz funkcję.

   ![Wybieranie funkcji](./media/store-captured-data-data-warehouse/select-function.png)

1. Wybierz pozycję **Dodaj subskrypcję siatki zdarzeń**.

   ![Dodawanie subskrypcji](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Nadaj nazwę subskrypcji siatki zdarzeń. Jako typu zdarzenia użyj wartości **Przestrzenie nazw usługi Event Hubs**. Podaj wartości, aby wybrać swoje wystąpienie przestrzeni nazw usługi Event Hubs. W polu punktu końcowego subskrybenta pozostaw podaną wartość. Wybierz przycisk **Utwórz**.

   ![Tworzenie subskrypcji](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Generowanie danych przykładowych  
Już skonfigurowano centrum zdarzeń, usługę Azure Synapse Analytics, platformę Azure aplikacja funkcji i subskrypcję Event Grid. Możesz uruchomić plik WindTurbineDataGenerator.exe, aby wygenerować strumienie danych w centrum Event Hub po zaktualizowaniu parametrów połączenia i nazwy centrum zdarzeń w kodzie źródłowym. 

1. W portalu wybierz przestrzeń nazw swojego centrum zdarzeń. Wybierz **Parametry połączenia**.

   ![Wybieranie parametrów połączenia](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Wybierz **RootManageSharedAccessKey**

   ![Wybieranie klucza](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Skopiuj zawartość pola **Parametry połączenia — klucz podstawowy**

   ![Kopiowanie klucza](./media/store-captured-data-data-warehouse/copy-key.png)

4. Wróć do projektu programu Visual Studio. W projekcie *WindTurbineDataGenerator* otwórz plik *program.cs*.

5. Zaktualizuj wartości **EventHubConnectionString** oraz **EventHubName**, wstawiając parametry połączenia i nazwę centrum zdarzeń. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Skompiluj rozwiązanie, a następnie uruchom aplikację WindTurbineGenerator.exe. 

## <a name="verify-captured-data-in-data-warehouse"></a>Weryfikowanie zapisanych danych w magazynie danych
Po kilku minutach Zbadaj tabelę w usłudze Azure Synapse Analytics. Należy zauważyć, że dane wygenerowane przez usługę WindTurbineDataGenerator zostały przesłane strumieniowo do centrum zdarzeń, przechwycone w kontenerze usługi Azure Storage, a następnie zmigrowane do tabeli usługi Azure Synapse Analytics przez funkcję platformy Azure.  

## <a name="next-steps"></a>Następne kroki 
Aby uzyskać praktyczne informacje, możesz użyć zaawansowanych narzędzi do wizualizacji danych w magazynie danych.

W tym artykule pokazano, jak używać [Power BI z usługą Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)