---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854268"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Omówienie aplikacji":::

Ten diagram przedstawia przepływ pracy rozwiązania, które tworzysz w ramach tego samouczka: 

1. Dane wysłane do centrum zdarzeń platformy Azure są przechwytywane w magazynie obiektów blob Azure.
2. Po zakończeniu przechwytywania następuje wygenerowanie zdarzenia i wysłanie go do siatki zdarzeń Azure. 
3. Siatka zdarzeń przekazuje te dane zdarzeń do aplikacji funkcji platformy Azure.
4. Aplikacja funkcji pobiera obiekt blob z magazynu, używając adresu URL obiektu blob. 
5. Aplikacja funkcji migruje dane obiektów BLOB do usługi Azure Synapse Analytics. 

W tym artykule wykonasz następujące kroki:

> [!div class="checklist"]
> - Wdróż wymaganą infrastrukturę na potrzeby samouczka
> - Publikowanie kodu do aplikacji usługi Functions
> - Tworzenie subskrypcji usługi Event Grid 
> - Przesyłanie strumieniowe danych przykładowych do Event Hubs
> - Weryfikowanie przechwyconych danych w usłudze Azure Synapse Analytics

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka niezbędne są następujące elementy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
- [Program Visual studio 2019](https://www.visualstudio.com/vs/) z obciążeniami dla: Programowanie aplikacji klasycznych platformy .NET, programowanie na platformie Azure, programowanie ASP.NET i sieci Web, programowanie Node.js i programowanie w języku Python.
- Pobierz na komputer [przykładowy projekt EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
    - WindTurbineDataGenerator — prosty Wydawca, który wysyła dane przykładowego wiatru turbiny do centrum zdarzeń z obsługą przechwytywania
    - FunctionDWDumper — funkcja platformy Azure, która odbiera powiadomienia usługi Event Grid, gdy plik Avro zostanie zapisany w obiekcie blob w usłudze Azure Storage. Otrzymuje ścieżkę identyfikatora URI obiektu BLOB, odczytuje jego zawartość i wypycha te dane do usługi Azure Synapse Analytics (dedykowanej puli SQL).

## <a name="deploy-the-infrastructure"></a>Wdrażanie infrastruktury
W tym kroku wdrożysz wymaganą infrastrukturę za pomocą [szablonu usługi Resource Manager](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). Podczas wdrażania szablonu tworzone są następujące zasoby:

* Centrum zdarzeń z włączoną funkcją Capture
* Konto magazynu na potrzeby przechwyconych plików 
* Plan usługi aplikacji do hostowania aplikacji funkcji
* Aplikacja funkcji do przetwarzania zdarzeń
* Rozwiązanie SQL Server do hostowania magazynu danych
* Azure Synapse Analytics (dedykowana Pula SQL) do przechowywania zmigrowanych danych

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Wdrażanie infrastruktury przy użyciu interfejsu wiersza polecenia platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz przycisk **Cloud Shell** u góry.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Witryna Azure Portal":::
3. W dolnej części przeglądarki zostanie otwarta usługa Cloud Shell.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. Jeśli w usłudze Cloud Shell zostaną wyświetlone opcje **Bash** i **PowerShell** do wyboru, wybierz pozycję **Bash**. 
5. Jeśli używasz usługi Cloud Shell po raz pierwszy, utwórz konto magazynu, wybierając pozycję **Utwórz magazyn**. Usługa Azure Cloud Shell wymaga konta magazynu platformy Azure do przechowywania niektórych plików. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Utwórz magazyn dla Cloud Shell":::
6. Zaczekaj na zainicjowanie usługi Cloud Shell. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell zainicjowany":::
1. Utwórz grupę zasobów platformy Azure, uruchamiając następujące polecenie interfejsu wiersza polecenia: 
    1. Skopiuj poniższe polecenie i wklej je w oknie usługi Cloud Shell. W razie potrzeby zmień nazwę i lokalizację grupy zasobów.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Naciśnij klawisz **Enter**. 

        Oto przykład:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Wdróż wszystkie zasoby wymienione w poprzedniej sekcji (centrum zdarzeń, konto magazynu, aplikację funkcji, Azure Synapse Analytics), uruchamiając następujące polecenie interfejsu wiersza polecenia: 
    1. Skopiuj polecenie i wklej je w oknie usługi Cloud Shell. Możesz również skopiować polecenie i wkleić je do wybranego edytora, określić wartości, a następnie skopiować polecenie do usługi Cloud Shell. 

        > [!IMPORTANT]
        > Określ wartości następujących jednostek przed uruchomieniem polecenia: 
        > - Nazwa utworzonej wcześniej grupy zasobów.
        > - Nazwa przestrzeni nazw centrum zdarzeń. 
        > - Nazwa centrum zdarzeń. Możesz pozostawić wartość bez zmian (hubdatamigration).
        > - Nazwa serwera SQL.
        > - Nazwa użytkownika i hasło SQL. 
        > - Nazwa bazy danych.
        > - Nazwa konta magazynu. 
        > - Nazwa aplikacji funkcji. 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  W oknie usługi Cloud Shell naciśnij klawisz **ENTER**, aby uruchomić polecenie. Ten proces może zająć trochę czasu, ponieważ tworzysz wiele zasobów. W wyniku wykonania polecenia upewnij się, że nie wystąpiły żadne błędy. 
1. Zamknij Cloud Shell, zaznaczając przycisk **Cloud Shell** w prawym górnym rogu okna Cloud Shell w portalu ( **lub).** 

### <a name="verify-that-the-resources-are-created"></a>Sprawdzanie, czy są tworzone zasoby

1. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie. 
2. Przefiltruj listę grup zasobów, wprowadzając w polu wyszukiwania nazwę grupy zasobów. 
3. Wybierz grupę zasobów na liście.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Wybierz grupę zasobów":::
4. Sprawdź, czy w grupie zasobów są wyświetlane następujące zasoby:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Zasoby w grupie zasobów" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Tworzenie tabeli w usłudze Azure Synapse Analytics
Utwórz tabelę w swoim magazynie danych, uruchamiając skrypt [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Do uruchomienia skryptu możesz użyć programu Visual Studio lub Edytora zapytań w portalu. W poniższych krokach pokazano, jak użyć Edytora zapytań: 

1. Na liście zasobów w grupie zasobów wybierz **dedykowaną pulę SQL**. 
2. Na **dedykowanej puli SQL** w sekcji **typowe zadania** w menu po lewej stronie wybierz pozycję **Edytor zapytań (wersja zapoznawcza)**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Strona analizy usługi Azure Synapse":::
2. Wprowadź nazwę **użytkownika** i **hasło** dla serwera SQL, a następnie wybierz pozycję **OK**. Jeśli zostanie wyświetlony komunikat informujący o umożliwieniu klientowi dostępu do programu SQL Server, wykonaj następujące kroki:
    1. Wybierz łącze: **Ustaw zaporę serwera**. 
    2. Na stronie **Ustawienia zapory** wybierz pozycję **Dodaj adres IP klienta** na pasku narzędzi, a następnie wybierz pozycję **Zapisz** na pasku narzędzi. 
    3. W komunikacie o powodzeniu wybierz pozycję **OK** .
    4. Przejdź z powrotem do **dedykowanej puli SQL** , a następnie wybierz pozycję **Edytor zapytań (wersja zapoznawcza)** w menu po lewej stronie. 
    5. Wprowadź nazwę **użytkownika** i **hasło**, a następnie wybierz przycisk **OK**. 
1. W oknie zapytania skopiuj i uruchom następujący skrypt SQL: 

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

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="Uruchamianie zapytania SQL":::
5. Nie zamykaj tej karty lub okna, aby na końcu tego samouczka można było sprawdzić, czy dane zostały utworzone. 

### <a name="update-the-function-runtime-version"></a>Aktualizowanie wersji środowiska uruchomieniowego funkcji

1. Otwórz kolejną kartę w przeglądarce sieci Web i przejdź do [Azure Portal](https://portal.azure.com).
1. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie.
1. Wybierz grupę zasobów, w której istnieje aplikacja funkcji. 
1. Wybierz **aplikację funkcji** z listy zasobów w grupie zasobów.
1. Wybierz pozycję **Konfiguracja** w obszarze **Ustawienia** w menu po lewej stronie. 
1. Przejdź do karty **Ustawienia środowiska uruchomieniowego funkcji** w okienku po prawej stronie. 
1. Zaktualizuj **wersję środowiska uruchomieniowego** do **~ 3**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="Aktualizacja wersji środowiska uruchomieniowego funkcji":::
6. Wybierz pozycję **Zapisz** na pasku narzędzi. 
1. Na wyskakującym okienku **Zapisz potwierdzenie zmian** wybierz pozycję **Kontynuuj**. 

## <a name="publish-the-azure-functions-app"></a>Publikowanie aplikacji usługi Azure Functions

1. Uruchom program Visual Studio.
2. Otwórz rozwiązanie **EventHubsCaptureEventGridDemo.sln**, które zostało pobrane z repozytorium [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) w ramach wymagań wstępnych. Można go znaleźć w `/samples/e2e/EventHubsCaptureEventGridDemo` folderze. 
3. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **FunctionEGDWDumper** i wybierz polecenie **Publikuj**.
4. Jeśli zostanie wyświetlony poniższy ekran, wybierz pozycję **Uruchom**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="Przycisk Uruchom w sekcji Publikuj.":::
5. W oknie dialogowym **Publikowanie** wybierz pozycję **Azure** for **Target**, a następnie wybierz pozycję **dalej**. 
6. Wybierz pozycję **Azure aplikacja funkcji (Windows)**, a **następnie** wybierz pozycję Dalej.
7. Na karcie **wystąpienie funkcji** wybierz subskrypcję platformy Azure, rozwiń grupę zasobów i wybierz aplikację, a następnie wybierz pozycję **Zakończ**. Musisz zalogować się do konta platformy Azure, jeśli jeszcze tego nie zrobiono. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="Wybierz aplikację funkcji":::
8. Na stronie **Publikowanie** w sekcji **zależności usługi** wybierz pozycję **Konfiguruj** dla **magazynu**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="Wybierz pozycję Konfiguruj łącze dla zależności usługi magazynu":::
1. Na stronie **Konfigurowanie zależności** wykonaj następujące kroki: 
    1. Wybierz utworzone wcześniej **konto magazynu** , a następnie wybierz pozycję **dalej**. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Wybieranie konta magazynu":::
    10. Określ **nazwę parametrów połączenia**, a następnie wybierz opcję **Brak** dla opcji **Zapisz parametry połączenia** , a następnie wybierz przycisk **dalej**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Określ nazwę parametrów połączenia":::      
    1. Wyczyść opcję **plik kodu C#** i **Magazyn wpisów tajnych** , a następnie wybierz pozycję **Zakończ**.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Przejrzyj podsumowanie zmian":::
1. Gdy program Visual Studio skonfiguruje profil, wybierz pozycję **Opublikuj**.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Wybierz pozycję Publikuj":::
2. Na karcie, na której jest otwarta strona **funkcji platformy Azure** , wybierz pozycję  **funkcje** w menu po lewej stronie. Upewnij się, że funkcja **EventGridTriggerMigrateData** pojawia się na liście. Jeśli go nie widzisz, spróbuj ponownie wykonać publikowanie z programu Visual Studio, a następnie Odśwież stronę w portalu. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="Potwierdź utworzenie funkcji":::    

Po opublikowaniu funkcji możesz przystąpić do subskrybowania zdarzenia.

## <a name="subscribe-to-the-event"></a>Subskrybowanie zdarzenia

1. Na nowej karcie lub w nowym oknie przeglądarki internetowej przejdź do witryny [Azure Portal](https://portal.azure.com).
2. W witrynie Azure Portal wybierz pozycję **Grupy zasobów** w menu po lewej stronie. 
3. Przefiltruj listę grup zasobów, wprowadzając w polu wyszukiwania nazwę grupy zasobów. 
4. Wybierz grupę zasobów na liście.
1. Wybierz **przestrzeń nazw Event Hubs** z listy zasobów.
1. Na stronie **obszar nazw Event Hubs** wybierz pozycję **zdarzenia** w menu po lewej stronie, a następnie wybierz pozycję **+ subskrypcja zdarzeń** na pasku narzędzi. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Dodaj link subskrypcji zdarzeń na stronie zdarzenia dla Event Hubs przestrzeni nazw":::
1. Na stronie **Tworzenie subskrypcji zdarzeń** wykonaj następujące kroki:
    1. Wprowadź nazwę **subskrypcji zdarzeń**. 
    1. Wprowadź nazwę **tematu systemowego**. Temat systemowy zawiera punkt końcowy do wysyłania zdarzeń przez nadawcę. Aby uzyskać więcej informacji, zobacz [Tematy systemowe](../articles/event-grid/system-topics.md)
    1. W obszarze **Typ punktu końcowego** wybierz pozycję **Funkcja platformy Azure**.
    1. W polu **punkt końcowy** wybierz link.
    1. Na stronie **Wybierz funkcję platformy Azure** wykonaj następujące kroki, jeśli nie zostaną wypełnione automatycznie.
        1. Wybierz subskrypcję platformy Azure, która ma funkcję platformy Azure. 
        1. Wybierz grupę zasobów dla funkcji. 
        1. Wybierz aplikację funkcji.
        1. Wybierz miejsce wdrożenia. 
        1. Wybierz funkcję **EventGridTriggerMigrateData**. 
    1. Na stronie **Wybierz funkcję platformy Azure** wybierz pozycję **Potwierdź wybór**.
    1. Następnie na stronie **Tworzenie subskrypcji zdarzeń** wybierz pozycję **Utwórz**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="Tworzenie subskrypcji zdarzeń przy użyciu funkcji" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Sprawdź, czy utworzono subskrypcję zdarzenia. Przejdź do karty **subskrypcje zdarzeń** na stronie **zdarzenia** dla przestrzeni nazw Event Hubs. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Potwierdź subskrypcję zdarzeń" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. Wybierz plan App Service (nie App Service) na liście zasobów w grupie zasobów. 

## <a name="run-the-app-to-generate-data"></a>Uruchamianie aplikacji w celu wygenerowania danych
Zakończono konfigurowanie centrum zdarzeń, dedykowanej puli SQL (dawniej SQL Data Warehouse), aplikacji funkcji platformy Azure i subskrypcji zdarzeń. Przed uruchomieniem aplikacji, która generuje dane dla centrum zdarzeń, należy skonfigurować kilka wartości.

1. W witrynie Azure Portal jak poprzednio przejdź do grupy zasobów. 
2. Wybierz przestrzeń nazw usługi Event Hubs.
3. Na stronie **Przestrzeń nazw usługi Event Hubs** wybierz pozycję **Zasady dostępu współużytkowanego** w menu po lewej stronie.
4. Z listy zasad wybierz pozycję **RootManageSharedAccessKey**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Strona zasady dostępu współdzielonego dla przestrzeni nazw Event Hubs":::    
1. Wybierz przycisk kopiowania obok pola tekstowego **Parametry połączenia — klucz podstawowy**. 
1. Wróć do rozwiązania programu Visual Studio. 
1. Kliknij prawym przyciskiem myszy projekt **WindTurbineDataGenerator** , a następnie wybierz pozycję **Ustaw jako projekt startowy**. 
1. W projekcie WindTurbineDataGenerator otwórz plik **program.cs**.
1. Zamień na `<EVENT HUBS NAMESPACE CONNECTION STRING>` Parametry połączenia skopiowane z portalu. 
1. Zamień `<EVENT HUB NAME>` na nazwę centrum zdarzeń. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Skompiluj rozwiązanie. Uruchom aplikację **WindTurbineGenerator.exe** . 
7. Po kilku minutach na karcie inne przeglądarki, w której jest otwarte okno zapytania, należy wykonać zapytanie do tabeli w magazynie danych dla zmigrowanych danych.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Wyniki zapytania](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>Monitorowanie rozwiązania
Ta sekcja ułatwia monitorowanie rozwiązania lub rozwiązywanie związanych z nim problemów. 

### <a name="view-captured-data-in-the-storage-account"></a>Wyświetl przechwycone dane na koncie magazynu
1. Przejdź do grupy zasobów i wybierz konto magazynu używane do przechwytywania danych zdarzenia. 
1. Na stronie **konto magazynu** wybierz pozycję **Eksplorator usługi Storage (wersja zapoznawcza**) w menu po lewej stronie.
1. Rozwiń węzeł **kontenery obiektów BLOB** i wybierz pozycję **windturbinecapture**. 
1. Otwórz folder o nazwie tak samo jak **przestrzeń nazw Event Hubs** w okienku po prawej stronie. 
1. Otwórz folder o nazwie takiej samej jak centrum zdarzeń (**hubdatamigration**). 
1. Przechodzenie do szczegółów folderów i wyświetlanie plików AVRO. Oto przykład:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="Przechwycony plik w magazynie" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Sprawdź, czy wyzwalacz Event Grid wywołał funkcję
1. Przejdź do grupy zasobów, a następnie wybierz aplikację funkcji. 
1. Wybierz pozycję **funkcje** w menu po lewej stronie.
1. Wybierz z listy funkcję **EventGridTriggerMigrateData** . 
1. Na stronie **Funkcja** wybierz pozycję **monitor** w menu po lewej stronie. 
1. Wybierz pozycję **Konfiguruj** , aby skonfigurować usługi Application Insights do przechwytywania dzienników wywołań. 
1. Utwórz nowy zasób **Application Insights** lub Użyj istniejącego zasobu. 
1. Przejdź z powrotem do strony **monitorowanie** funkcji. 
1. Upewnij się, że aplikacja kliencka (**WindTurbineDataGenerator**), która wysyła zdarzenia, jest nadal uruchomiona. Jeśli nie, uruchom aplikację. 
1. Poczekaj kilka minut (5 minut lub więcej) i wybierz przycisk **Odśwież** , aby zobaczyć wywołania funkcji.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="Wywołania funkcji":::
1. Wybierz wywołanie, aby wyświetlić szczegóły.

    Usługa Event Grid dystrybuuje dane zdarzenia do subskrybentów. Poniższy przykład przedstawia dane zdarzeń wygenerowane po przechwyceniu w obiekcie blob danych przesyłanych strumieniowo za pośrednictwem centrum zdarzeń. W szczególności zwróć uwagę, że właściwość `fileUrl` w obiekcie `data` wskazuje obiekt blob w magazynie. Aplikacja funkcji używa tego adresu URL do pobierania pliku obiektu blob zawierającego przechwycone dane.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Sprawdzanie, czy dane są przechowywane w dedykowanej puli SQL
Na karcie Przeglądarka, w której jest otwarte okno zapytania, zbadaj tabelę w dedykowanej puli SQL dla zmigrowanych danych.

![Wyniki zapytania](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

