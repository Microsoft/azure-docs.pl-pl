---
title: 'Samouczek: wysyłanie danych Event Hubs do magazynu danych — Event Grid'
description: 'Samouczek: zawiera opis sposobu używania Azure Event Grid i Event Hubs do migrowania danych do usługi Azure Synapse Analytics. Do pobierania pliku przechwytywania służy funkcja platformy Azure.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: e6dfcac17d79edd417af07179224fdf922906c4e
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841380"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Samouczek: przesyłanie strumieniowe danych Big Data do magazynu danych
Usługa Azure [Event Grid](overview.md) jest inteligentną usługą routingu zdarzeń, która umożliwia reagowanie na powiadomienia (zdarzenia) z aplikacji i usług. Może na przykład spowodować, że funkcja platformy Azure będzie przetwarzać dane centrum zdarzeń, które zostały przechwycone przez usługę Azure Blob Storage lub usługę Azure Data Lake Storage, a także przeprowadzać migrację danych do innych repozytoriów danych. Ten [Event Hubs i Event Grid integracji](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) pokazują, jak używać Event Hubs z Event Grid, aby bezproblemowo migrować przechwycone dane Event Hubs z magazynu obiektów BLOB do usługi Azure Synapse Analytics (dawniej SQL Data Warehouse).

![Omówienie aplikacji](media/event-grid-event-hubs-integration/overview.png)

Ten diagram przedstawia przepływ pracy rozwiązania, które tworzysz w ramach tego samouczka: 

1. Dane wysłane do centrum zdarzeń platformy Azure są przechwytywane w magazynie obiektów blob Azure.
2. Po zakończeniu przechwytywania następuje wygenerowanie zdarzenia i wysłanie go do siatki zdarzeń Azure. 
3. Siatka zdarzeń przekazuje te dane zdarzeń do aplikacji funkcji platformy Azure.
4. Aplikacja funkcji pobiera obiekt blob z magazynu, używając adresu URL obiektu blob. 
5. Aplikacja funkcji migruje dane obiektów BLOB do usługi Azure Synapse Analytics. 

W tym artykule wykonasz następujące kroki:

> [!div class="checklist"]
> * Użyj szablonu Azure Resource Manager, aby wdrożyć infrastrukturę: centrum zdarzeń, konto magazynu, aplikacja funkcji, analiza Synapse.
> * Tworzenie tabeli w magazynie danych.
> * Dodawanie kodu do aplikacji funkcji.
> * Subskrybowanie zdarzenia. 
> * Uruchamianie aplikacji, która wysyła dane do centrum zdarzeń.
> * Wyświetlanie zmigrowanych danych w magazynie danych.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* [Program Visual studio 2019](https://www.visualstudio.com/vs/) z obciążeniami dla: Programowanie aplikacji klasycznych platformy .NET, programowanie na platformie Azure, programowanie ASP.NET i sieci Web, programowanie Node.js i programowanie w języku Python.
* Pobierz na komputer [przykładowy projekt EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).

## <a name="deploy-the-infrastructure"></a>Wdrażanie infrastruktury
W tym kroku wdrożysz wymaganą infrastrukturę za pomocą [szablonu usługi Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Podczas wdrażania szablonu tworzone są następujące zasoby:

* Centrum zdarzeń z włączoną funkcją Capture
* Konto magazynu na potrzeby przechwyconych plików 
* Plan usługi aplikacji do hostowania aplikacji funkcji
* Aplikacja funkcji do przetwarzania zdarzeń
* Rozwiązanie SQL Server do hostowania magazynu danych
* Usługa Azure Synapse Analytics do przechowywania zmigrowanych danych

### <a name="launch-azure-cloud-shell-in-azure-portal"></a>Uruchamianie usługi Azure Cloud Shell w witrynie Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz przycisk **Cloud Shell** u góry.

    ![Azure Portal](media/event-grid-event-hubs-integration/azure-portal.png)
3. W dolnej części przeglądarki zostanie otwarta usługa Cloud Shell.

    ![Cloud Shell](media/event-grid-event-hubs-integration/launch-cloud-shell.png) 
4. Jeśli w usłudze Cloud Shell zostaną wyświetlone opcje **Bash** i **PowerShell** do wyboru, wybierz pozycję **Bash**. 
5. Jeśli używasz usługi Cloud Shell po raz pierwszy, utwórz konto magazynu, wybierając pozycję **Utwórz magazyn**. Usługa Azure Cloud Shell wymaga konta magazynu platformy Azure do przechowywania niektórych plików. 

    ![Zrzut ekranu przedstawiający okno dialogowe "nie masz zainstalowanego magazynu" z wybranym przyciskiem Utwórz magazyn.](media/event-grid-event-hubs-integration/create-storage-cloud-shell.png)
6. Zaczekaj na zainicjowanie usługi Cloud Shell. 

    ![Tworzenie magazynu dla usługi Cloud Shell](media/event-grid-event-hubs-integration/cloud-shell-initialized.png)


### <a name="use-azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Utwórz grupę zasobów platformy Azure, uruchamiając następujące polecenie interfejsu wiersza polecenia: 
    1. Skopiuj poniższe polecenie i wklej je w oknie usługi Cloud Shell. W razie potrzeby zmień nazwę i lokalizację grupy zasobów.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Naciśnij klawisz **Enter**. 

        Oto przykład:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n ehubegridgrp
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/ehubegridgrp",
          "location": "eastus",
          "managedBy": null,
          "name": "ehubegridgrp",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Wdróż wszystkie zasoby wymienione w poprzedniej sekcji (centrum zdarzeń, konto magazynu, aplikację funkcji, Azure Synapse Analytics), uruchamiając następujące polecenie interfejsu wiersza polecenia: 
    1. Skopiuj polecenie i wklej je w oknie usługi Cloud Shell. Możesz również skopiować polecenie i wkleić je do wybranego edytora, określić wartości, a następnie skopiować polecenie do usługi Cloud Shell. 

        ```azurecli
        az group deployment create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    2. Określ wartości dla następujących jednostek:
        1. Nazwa utworzonej wcześniej grupy zasobów.
        2. Nazwa przestrzeni nazw centrum zdarzeń. 
        3. Nazwa centrum zdarzeń. Możesz pozostawić wartość bez zmian (hubdatamigration).
        4. Nazwa serwera SQL.
        5. Nazwa użytkownika i hasło SQL. 
        6. Nazwa usługi Azure Synapse Analytics
        7. Nazwa konta magazynu. 
        8. Nazwa aplikacji funkcji. 
    3.  W oknie usługi Cloud Shell naciśnij klawisz **ENTER**, aby uruchomić polecenie. Ten proces może zająć trochę czasu, ponieważ tworzysz wiele zasobów. W wyniku wykonania polecenia upewnij się, że nie wystąpiły żadne błędy. 
    

### <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

1. W usłudze Azure Cloud Shell przełącz się w tryb programu PowerShell. W lewym górnym rogu usługi Azure Cloud Shell wybierz strzałkę w dół, a następnie wybierz pozycję **PowerShell**.

    ![Przełączanie na program PowerShell](media/event-grid-event-hubs-integration/select-powershell-cloud-shell.png)
2. Utwórz grupę zasobów platformy Azure, uruchamiając następujące polecenie: 
    1. Skopiuj poniższe polecenie i wklej je w oknie usługi Cloud Shell.

        ```powershell
        New-AzResourceGroup -Name rgDataMigration -Location eastus
        ```
    2. Określ nazwę **grupy zasobów**.
    3. Naciśnij klawisz ENTER. 
3. Wdróż wszystkie zasoby wymienione w poprzedniej sekcji (centrum zdarzeń, konto magazynu, aplikację funkcji, Azure Synapse Analytics), uruchamiając następujące polecenie:
    1. Skopiuj polecenie i wklej je w oknie usługi Cloud Shell. Możesz również skopiować polecenie i wkleić je do wybranego edytora, określić wartości, a następnie skopiować polecenie do usługi Cloud Shell. 

        ```powershell
        New-AzResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
        ```
    2. Określ wartości dla następujących jednostek:
        1. Nazwa utworzonej wcześniej grupy zasobów.
        2. Nazwa przestrzeni nazw centrum zdarzeń. 
        3. Nazwa centrum zdarzeń. Możesz pozostawić wartość bez zmian (hubdatamigration).
        4. Nazwa serwera SQL.
        5. Nazwa użytkownika i hasło SQL. 
        6. Nazwa usługi Azure Synapse Analytics
        7. Nazwa konta magazynu. 
        8. Nazwa aplikacji funkcji. 
    3.  W oknie usługi Cloud Shell naciśnij klawisz **ENTER**, aby uruchomić polecenie. Ten proces może zająć trochę czasu, ponieważ tworzysz wiele zasobów. W wyniku wykonania polecenia upewnij się, że nie wystąpiły żadne błędy. 

### <a name="close-the-cloud-shell"></a>Zamykanie usługi Cloud Shell 
Zamknij usługę Cloud Shell, wybierając przycisk **Cloud Shell** w portalu (lub) przycisk **X** w prawym górnym rogu okna usługi Cloud Shell. 

### <a name="verify-that-the-resources-are-created"></a>Sprawdzanie, czy są tworzone zasoby

1. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie. 
2. Przefiltruj listę grup zasobów, wprowadzając w polu wyszukiwania nazwę grupy zasobów. 
3. Wybierz grupę zasobów na liście.

    ![Wybierz grupę zasobów](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Sprawdź, czy w grupie zasobów są wyświetlane następujące zasoby:

    ![Zasoby w grupie zasobów](media/event-grid-event-hubs-integration/resources-in-resource-group.png)

### <a name="create-a-table-in-azure-synapse-analytics"></a>Tworzenie tabeli w usłudze Azure Synapse Analytics
Utwórz tabelę w swoim magazynie danych, uruchamiając skrypt [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Do uruchomienia skryptu możesz użyć programu Visual Studio lub Edytora zapytań w portalu. W poniższych krokach pokazano, jak użyć Edytora zapytań: 

1. Na liście zasobów w grupie zasobów wybierz **dedykowaną pulę SQL**. 
2. Na stronie Analiza usługi Azure Synapse wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** w menu po lewej stronie. 

    ![Strona analizy usługi Azure Synapse](media/event-grid-event-hubs-integration/sql-data-warehouse-page.png)
2. Wprowadź nazwę **użytkownika** i **hasło** dla serwera SQL, a następnie wybierz pozycję **OK**. Może być konieczne dodanie adresu IP klienta do zapory, aby pomyślnie zalogować się do programu SQL Server. 

    ![Uwierzytelnianie serwera SQL](media/event-grid-event-hubs-integration/sql-server-authentication.png)
4. W oknie zapytania skopiuj i uruchom następujący skrypt SQL: 

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

    ![Uruchamianie zapytania SQL](media/event-grid-event-hubs-integration/run-sql-query.png)
5. Nie zamykaj tej karty lub okna, aby na końcu tego samouczka można było sprawdzić, czy dane zostały utworzone. 

### <a name="update-the-function-runtime-version"></a>Aktualizowanie wersji środowiska uruchomieniowego funkcji

1. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie.
2. Wybierz grupę zasobów, w której istnieje aplikacja funkcji. 
3. Wybierz aplikację funkcji typu **App Service** na liście zasobów w grupie zasobów.
4. Wybierz pozycję **Konfiguracja** w obszarze **Ustawienia** w menu po lewej stronie. 
5. Przejdź do karty **Ustawienia środowiska uruchomieniowego funkcji** w okienku po prawej stronie. 
5. Zaktualizuj **wersję środowiska uruchomieniowego** do **~ 3**. 

    ![Aktualizacja wersji środowiska uruchomieniowego funkcji](media/event-grid-event-hubs-integration/function-runtime-version.png)
    

## <a name="publish-the-azure-functions-app"></a>Publikowanie aplikacji usługi Azure Functions

1. Uruchom program Visual Studio.
2. Otwórz rozwiązanie **EventHubsCaptureEventGridDemo.sln**, które zostało pobrane z repozytorium [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) w ramach wymagań wstępnych.
3. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy pozycję **FunctionEGDWDumper** i wybierz polecenie **Opublikuj**.

   ![Publikowanie aplikacji funkcji](media/event-grid-event-hubs-integration/publish-function-app.png)
4. Jeśli zostanie wyświetlony poniższy ekran, wybierz pozycję **Uruchom**. 

   ![Zrzut ekranu przedstawiający program Visual Studios z przyciskiem "Start" w sekcji Publikuj.](media/event-grid-event-hubs-integration/start-publish-button.png) 
5. W oknie dialogowym **Publikowanie** wybierz pozycję **Azure** for **Target**, a następnie wybierz pozycję **dalej**. 

   ![Przycisk uruchamiania publikowania](media/event-grid-event-hubs-integration/publish-select-azure.png)
6. Wybierz pozycję **Azure aplikacja funkcji (Windows)**, a **następnie** wybierz pozycję Dalej. 

   ![Wybierz pozycję Azure aplikacja funkcji — system Windows](media/event-grid-event-hubs-integration/select-azure-function-windows.png)
7. Na karcie **wystąpienie funkcji** wybierz subskrypcję platformy Azure, rozwiń grupę zasobów i wybierz aplikację, a następnie wybierz pozycję **Zakończ**. Musisz zalogować się do konta platformy Azure, jeśli jeszcze tego nie zrobiono. 

   ![Wybierz aplikację funkcji](media/event-grid-event-hubs-integration/publish-select-function-app.png)
8. W sekcji **zależności usługi** wybierz pozycję **Konfiguruj**.
9. Na stronie **Konfigurowanie zależności** wybierz utworzone wcześniej konto magazynu, a następnie wybierz przycisk **dalej**. 
10. Zachowaj ustawienia dla nazwy i wartości parametrów połączenia, a następnie wybierz przycisk **dalej**.
11. Wyczyść opcję **Magazyn wpisów tajnych** , a następnie wybierz pozycję **Zakończ**.  
8. Gdy program Visual Studio skonfiguruje profil, wybierz pozycję **Opublikuj**.

   ![Wybieranie pozycji Opublikuj](media/event-grid-event-hubs-integration/select-publish.png)

Po opublikowaniu funkcji możesz przystąpić do subskrybowania zdarzenia.

## <a name="subscribe-to-the-event"></a>Subskrybowanie zdarzenia

1. Na nowej karcie lub w nowym oknie przeglądarki internetowej przejdź do witryny [Azure Portal](https://portal.azure.com).
2. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie. 
3. Przefiltruj listę grup zasobów, wprowadzając w polu wyszukiwania nazwę grupy zasobów. 
4. Wybierz grupę zasobów na liście.

    ![Wybierz grupę zasobów](media/event-grid-event-hubs-integration/select-resource-group.png)
4. Wybierz plan App Service (nie App Service) na liście zasobów w grupie zasobów. 
5. Na stronie Plan usługi App Service wybierz pozycję **Aplikacje** w menu po lewej stronie, a następnie wybierz aplikację funkcji. 

    ![Wybieranie aplikacji funkcji](media/event-grid-event-hubs-integration/select-function-app-app-service-plan.png)
6. Rozwiń aplikację funkcji, rozwiń funkcje, a następnie wybierz swoją funkcję. 
7. Na pasku narzędzi wybierz pozycję **Dodaj subskrypcję siatki zdarzeń**. 

    ![Wybieranie funkcji platformy Azure](media/event-grid-event-hubs-integration/select-function-add-button.png)
8. Na stronie **Tworzenie subskrypcji usługi Event Grid** wykonaj następujące czynności: 
    1. Na stronie **SZCZEGÓŁY SUBSKRYPCJI ZDARZEŃ** wprowadź nazwę subskrypcji (na przykład: captureEventSub) i wybierz pozycję **Utwórz**. 
    2. W sekcji **SZCZEGÓŁY TEMATU** wykonaj następujące czynności:
        1. Wybierz **Event Hubs przestrzenie nazw** dla **typów tematów**. 
        2. Wybierz swoją subskrypcję platformy Azure.
        2. Wybierz grupę zasobów platformy Azure.
        3. Wybierz przestrzeń nazw Event Hubs.
    3. W sekcji **typy zdarzeń** upewnij się, że **utworzony plik przechwytywania** został wybrany do **filtrowania typów zdarzeń**. 
    4. W sekcji **Szczegóły punktu końcowego** upewnij się, że **Typ punktu końcowego** jest ustawiony na **funkcję Azure Function** , a **punkt końcowy** jest ustawiony na funkcję platformy Azure. 
    
        ![Tworzenie subskrypcji usługi Event Grid](media/event-grid-event-hubs-integration/create-event-subscription.png)

## <a name="run-the-app-to-generate-data"></a>Uruchamianie aplikacji w celu wygenerowania danych
Zakończono konfigurowanie centrum zdarzeń, usługi Azure Synapse Analytics, aplikacji funkcji platformy Azure i subskrypcji zdarzeń. Przed uruchomieniem aplikacji, która generuje dane dla centrum zdarzeń, należy skonfigurować kilka wartości.

1. W witrynie Azure Portal jak poprzednio przejdź do grupy zasobów. 
2. Wybierz przestrzeń nazw usługi Event Hubs.
3. Na stronie **Przestrzeń nazw usługi Event Hubs** wybierz pozycję **Zasady dostępu współużytkowanego** w menu po lewej stronie.
4. Z listy zasad wybierz pozycję **RootManageSharedAccessKey**. 
5. Wybierz przycisk kopiowania obok pola tekstowego **Parametry połączenia — klucz podstawowy**. 

    ![Parametry połączenia dla przestrzeni nazw centrum zdarzeń](media/event-grid-event-hubs-integration/get-connection-string.png)
1. Wróć do rozwiązania programu Visual Studio. 
2. W projekcie WindTurbineDataGenerator otwórz plik **program.cs**.
5. Zastąp dwie wartości stałe. Dla obiektu **EventHubConnectionString** użyj wartości skopiowanej. Jako nazwy centrum zdarzeń użyj wartości **hubdatamigration**. Jeśli dla centrum zdarzeń użyto innej nazwy, określ tę nazwę. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Skompiluj rozwiązanie. Uruchom aplikację **WindTurbineGenerator.exe** . 
7. Po kilku minutach wyślij zapytanie do tabeli w magazynie danych dla migrowanych danych.

    ![Wyniki zapytania](media/event-grid-event-hubs-integration/query-results.png)

### <a name="event-data-generated-by-the-event-hub"></a>Dane zdarzeń wygenerowane przez centrum zdarzeń
Usługa Event Grid dystrybuuje dane zdarzenia do subskrybentów. Poniższy przykład przedstawia dane zdarzeń wygenerowane po przechwyceniu w obiekcie blob danych przesyłanych strumieniowo za pośrednictwem centrum zdarzeń. W szczególności zwróć uwagę, że właściwość `fileUrl` w obiekcie `data` wskazuje obiekt blob w magazynie. Aplikacja funkcji używa tego adresu URL do pobierania pliku obiektu blob zawierającego przechwycone dane.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```


## <a name="next-steps"></a>Następne kroki

* Aby poznać różnice miedzy usługami obsługi komunikatów na platformie Azure, zobacz [Wybieranie między usługami platformy Azure dostarczającymi komunikaty](compare-messaging-services.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby zapoznać się z wprowadzeniem do funkcji Event Hubs Capture, zobacz [Włączanie funkcji przechwytywania usługi Event Hubs przy użyciu witryny Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Aby uzyskać więcej informacji na temat konfigurowania i uruchamiania przykładu, zobacz [Przykład funkcji Event Hubs Capture i usługi Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
