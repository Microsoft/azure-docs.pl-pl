---
title: 'Samouczek: Używanie funkcji języka Java z Azure Cosmos DB i Event Hubs'
description: W tym samouczku pokazano, jak używać zdarzeń z Event Hubs, aby wprowadzać aktualizacje w Azure Cosmos DB przy użyciu funkcji zapisaną w języku Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: aa9e7612a5b3b9655b0c1981fbba87645526b3a2
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327206"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Samouczek: Tworzenie funkcji w języku Java przy użyciu wyzwalacza centrum zdarzeń i powiązania danych wyjściowych Azure Cosmos DB

W tym samouczku pokazano, jak za pomocą Azure Functions utworzyć funkcję języka Java, która analizuje ciągły strumień danych temperatury i ciśnienia. Zdarzenia centrum zdarzeń reprezentujące odczyty czujników wyzwalają funkcję. Funkcja przetwarza dane zdarzenia, a następnie dodaje wpisy stanu do Azure Cosmos DB.

W tym samouczku przedstawiono następujące instrukcje:

> [!div class="checklist"]
> * Tworzenie i Konfigurowanie zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
> * Twórz i Testuj funkcje języka Java, które współdziałają z tymi zasobami.
> * Wdróż swoje funkcje na platformie Azure i monitoruj je za pomocą Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, musisz mieć zainstalowane następujące elementy:

* Zestaw [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8
* Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) , jeśli wolisz nie używać Cloud Shell
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) w wersji 2.6.666 lub nowszej

> [!IMPORTANT]
> `JAVA_HOME`Aby ukończyć ten samouczek, zmienna środowiskowa musi być ustawiona na lokalizację instalacji JDK.

Jeśli wolisz użyć kodu dla tego samouczka bezpośrednio, zobacz repozytorium przykładu [Java-Functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

W tym samouczku potrzebne są następujące zasoby:

* Grupa zasobów zawierająca inne zasoby
* Event Hubs przestrzeni nazw, centrum zdarzeń i reguły autoryzacji
* Konto Cosmos DB, baza danych i kolekcja
* Aplikacja funkcji i konto magazynu do hostowania

W poniższych sekcjach przedstawiono sposób tworzenia tych zasobów przy użyciu interfejsu wiersza polecenia platformy Azure.

### <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Jeśli nie używasz Cloud Shell, musisz lokalnie użyć interfejsu wiersza polecenia platformy Azure, aby uzyskać dostęp do swojego konta. Użyj `az login` polecenia z wiersza bash, aby uruchomić środowisko logowania oparte na przeglądarce. Jeśli masz dostęp do więcej niż jednej subskrypcji platformy Azure, ustaw wartość domyślną przy użyciu `az account set --subscription` identyfikatora subskrypcji.

### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Następnie utwórz pewne zmienne środowiskowe dla nazw i lokalizacji tworzonych zasobów. Użyj następujących poleceń, zastępując `<value>` symbole zastępcze wybraną wartością. Wartości powinny być zgodne z [regułami nazewnictwa i ograniczeniami dotyczącymi zasobów platformy Azure](/azure/architecture/best-practices/resource-naming). Dla `LOCATION` zmiennej Użyj jednej z wartości utworzonych przez `az functionapp list-consumption-locations` polecenie.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCE_GROUP=<value>
set EVENT_HUB_NAMESPACE=<value>
set EVENT_HUB_NAME=<value>
set EVENT_HUB_AUTHORIZATION_RULE=<value>
set COSMOS_DB_ACCOUNT=<value>
set STORAGE_ACCOUNT=<value>
set FUNCTION_APP=<value>
set LOCATION=<value>
```

---

W pozostałej części tego samouczka są używane te zmienne. Należy pamiętać, że te zmienne utrzymują się tylko w czasie trwania bieżącego interfejsu wiersza polecenia platformy Azure lub sesji Cloud Shell. Musisz ponownie uruchomić te polecenia, jeśli używasz innego lokalnego okna terminalu lub sesji Cloud Shell przekroczyć limit czasu.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Platforma Azure używa grup zasobów do zbierania wszystkich powiązanych zasobów na koncie. Dzięki temu można je wyświetlić jako jednostkę i usunąć je za pomocą jednego polecenia, gdy skończysz z nimi pracować.

Aby utworzyć grupę zasobów, użyj następującego polecenia:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az group create ^
    --name %RESOURCE_GROUP% ^
    --location %LOCATION%
```

---

### <a name="create-an-event-hub"></a>Tworzenie centrum zdarzeń

Następnie utwórz przestrzeń nazw usługi Azure Event Hubs, centrum zdarzeń i regułę autoryzacji przy użyciu następujących poleceń:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventhubs namespace create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_NAMESPACE%
az eventhubs eventhub create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_NAME% ^
    --namespace-name %EVENT_HUB_NAMESPACE% ^
    --message-retention 1
az eventhubs eventhub authorization-rule create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_AUTHORIZATION_RULE% ^
    --eventhub-name %EVENT_HUB_NAME% ^
    --namespace-name %EVENT_HUB_NAMESPACE% ^
    --rights Listen Send
```

---

Przestrzeń nazw Event Hubs zawiera rzeczywiste centrum zdarzeń i jego regułę autoryzacji. Reguła autoryzacji umożliwia funkcjom wysyłanie komunikatów do centrum i nasłuchiwanie odpowiednich zdarzeń. Jedna funkcja wysyła komunikaty reprezentujące dane telemetryczne. Inna funkcja nasłuchuje zdarzeń, analizuje dane zdarzeń i zapisuje wyniki w Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Tworzenie bazy danych Azure Cosmos DB

Następnie utwórz konto Azure Cosmos DB, bazę danych i kolekcję przy użyciu następujących poleceń:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb sql database create \
    --resource-group $RESOURCE_GROUP \
    --account-name $COSMOS_DB_ACCOUNT \
    --name TelemetryDb
az cosmosdb sql container create \
    --resource-group $RESOURCE_GROUP \
    --account-name $COSMOS_DB_ACCOUNT \
    --database-name TelemetryDb \
    --name TelemetryInfo \
    --partition-key-path '/temperatureStatus'
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az cosmosdb create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %COSMOS_DB_ACCOUNT%
az cosmosdb sql database create ^
    --resource-group %RESOURCE_GROUP% ^
    --account-name %COSMOS_DB_ACCOUNT% ^
    --name TelemetryDb
az cosmosdb sql container create ^
    --resource-group %RESOURCE_GROUP% ^
    --account-name %COSMOS_DB_ACCOUNT% ^
    --database-name TelemetryDb ^
    --name TelemetryInfo ^
    --partition-key-path "/temperatureStatus"
```

---

Wartość dzieli `partition-key-path` dane na podstawie `temperatureStatus` wartości poszczególnych elementów. Klucz partycji umożliwia Cosmos DB zwiększenie wydajności przez podział danych do odrębnych zestawów, które mogą uzyskać do nich dostęp niezależnie.

### <a name="create-a-storage-account-and-function-app"></a>Tworzenie konta magazynu i aplikacji funkcji

Następnie utwórz konto usługi Azure Storage, które jest wymagane przez Azure Functions, a następnie Utwórz aplikację funkcji. Użyj następujących poleceń:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java \
    --functions-version 2
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az storage account create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %STORAGE_ACCOUNT% ^
    --sku Standard_LRS
az functionapp create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %FUNCTION_APP% ^
    --storage-account %STORAGE_ACCOUNT% ^
    --consumption-plan-location %LOCATION% ^
    --runtime java ^
    --functions-version 2
```

---

Gdy `az functionapp create` polecenie tworzy aplikację funkcji, tworzy również zasób Application Insights o tej samej nazwie. Aplikacja funkcji jest automatycznie konfigurowana przy użyciu ustawienia o nazwie `APPINSIGHTS_INSTRUMENTATIONKEY` , które łączy go z Application Insights. Możesz wyświetlić dane telemetryczne aplikacji po wdrożeniu funkcji na platformie Azure zgodnie z opisem w dalszej części tego samouczka.

## <a name="configure-your-function-app"></a>Konfigurowanie aplikacji funkcji

Aplikacja funkcji będzie musiała uzyskać dostęp do innych zasobów, aby działała prawidłowo. W poniższych sekcjach pokazano, jak skonfigurować aplikację funkcji, aby można ją było uruchomić na komputerze lokalnym.

### <a name="retrieve-resource-connection-strings"></a>Pobieranie parametrów połączenia zasobu

Użyj następujących poleceń, aby pobrać parametry połączenia magazynu, centrum zdarzeń i Cosmos DB i zapisać je w zmiennych środowiskowych:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query 'connectionStrings[0].connectionString' \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
FOR /F "delims=" %X IN (' ^
    az storage account show-connection-string ^
        --name %STORAGE_ACCOUNT% ^
        --query connectionString ^
        --output tsv') DO SET AZURE_WEB_JOBS_STORAGE=%X
FOR /F "delims=" %X IN (' ^
    az eventhubs eventhub authorization-rule keys list ^
        --resource-group %RESOURCE_GROUP% ^
        --name %EVENT_HUB_AUTHORIZATION_RULE% ^
        --eventhub-name %EVENT_HUB_NAME% ^
        --namespace-name %EVENT_HUB_NAMESPACE% ^
        --query primaryConnectionString ^
        --output tsv') DO SET EVENT_HUB_CONNECTION_STRING=%X
FOR /F "delims=" %X IN (' ^
    az cosmosdb keys list ^
        --resource-group %RESOURCE_GROUP% ^
        --name %COSMOS_DB_ACCOUNT% ^
        --type connection-strings ^
        --query connectionStrings[0].connectionString ^
        --output tsv') DO SET COSMOS_DB_CONNECTION_STRING=%X
```

---

Te zmienne są ustawione na wartości pobierane z poleceń interfejsu wiersza polecenia platformy Azure. Każde polecenie używa zapytania JMESPath do wyodrębnienia parametrów połączenia z zwróconego ładunku JSON. Parametry połączenia są również wyświetlane przy użyciu `echo` , dlatego można potwierdzić, że zostały pomyślnie pobrane.

### <a name="update-your-function-app-settings"></a>Aktualizowanie ustawień aplikacji funkcji

Następnie użyj poniższego polecenia, aby przesłać wartości parametrów połączenia do ustawień aplikacji na koncie usługi Azure Functions:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az functionapp config appsettings set ^
    --resource-group %RESOURCE_GROUP% ^
    --name %FUNCTION_APP% ^
    --settings ^
        AzureWebJobsStorage=%AZURE_WEB_JOBS_STORAGE% ^
        EventHubConnectionString=%EVENT_HUB_CONNECTION_STRING% ^
        CosmosDBConnectionString=%COSMOS_DB_CONNECTION_STRING%
```

---

Twoje zasoby platformy Azure zostały teraz utworzone i skonfigurowane do prawidłowego działania.

## <a name="create-and-test-your-functions"></a>Tworzenie i testowanie funkcji

Następnie utworzysz projekt na komputerze lokalnym, dodajesz kod Java i przetestujesz go. Będziesz używać poleceń, które współpracują z wtyczką Azure Functions dla Maven i Azure Functions Core Tools. Funkcje zostaną uruchomione lokalnie, ale będą korzystać z utworzonych przez siebie zasobów opartych na chmurze. Po uzyskaniu funkcji działających lokalnie możesz użyć programu Maven, aby wdrożyć je w chmurze i obserwować gromadzenie danych i analiz.

Jeśli do tworzenia zasobów użyto Cloud Shell, nie będziesz mieć lokalnego połączenia z platformą Azure. W takim przypadku należy użyć `az login` polecenia, aby uruchomić proces logowania opartego na przeglądarce. Następnie w razie potrzeby Ustaw domyślną subskrypcję przy użyciu `az account set --subscription` identyfikatora subskrypcji. Na koniec Uruchom następujące polecenia, aby ponownie utworzyć niektóre zmienne środowiskowe na komputerze lokalnym. Zastąp `<value>` symbole zastępcze tymi samymi wartościami, które zostały wcześniej użyte.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCE_GROUP=<value>
set FUNCTION_APP=<value>
```

---

### <a name="create-a-local-functions-project"></a>Tworzenie projektu funkcji lokalnych

Użyj następującego polecenia Maven, aby utworzyć projekt Functions i dodać wymagane zależności.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate --batch-mode ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype ^
    -DappName=%FUNCTION_APP% ^
    -DresourceGroup=%RESOURCE_GROUP% ^
    -DgroupId=com.example ^
    -DartifactId=telemetry-functions
```

---

To polecenie generuje kilka plików w `telemetry-functions` folderze:

* `pom.xml`Plik do użycia z Maven
* `local.settings.json`Plik do przechowywania ustawień aplikacji na potrzeby lokalnego testowania
* `host.json`Plik, który umożliwia pakiet rozszerzenia Azure Functions, wymagany do Cosmos DB powiązania danych wyjściowych w funkcji analizy danych
* `Function.java`Plik zawierający domyślną implementację funkcji
* Kilka plików testowych, które nie są potrzebne w tym samouczku

Aby uniknąć błędów kompilacji, należy usunąć pliki testowe. Uruchom następujące polecenia, aby przejść do folderu nowego projektu i usunąć folder testowy:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd telemetry-functions
rm -r src/test
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd telemetry-functions
rmdir /s /q src\test
```

---

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Pobieranie ustawień aplikacji funkcji do użytku lokalnego

W przypadku testowania lokalnego projekt funkcji będzie potrzebować parametrów połączenia, które zostały dodane do aplikacji funkcji na platformie Azure wcześniej w tym samouczku. Użyj poniższego polecenia Azure Functions Core Tools, które pobiera wszystkie ustawienia aplikacji funkcji przechowywane w chmurze i dodaje je do `local.settings.json` pliku:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
func azure functionapp fetch-app-settings %FUNCTION_APP%
```

---

### <a name="add-java-code"></a>Dodawanie kodu Java

Następnie otwórz `Function.java` plik i Zastąp jego zawartość następującym kodem.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Jak widać, ten plik zawiera dwie funkcje `generateSensorData` i `processSensorData` . `generateSensorData`Funkcja symuluje czujnik, który wysyła odczyty temperatury i ciśnienia do centrum zdarzeń. Wyzwalacz czasomierza uruchamia funkcję co 10 sekund, a powiązanie wyjściowe centrum zdarzeń wysyła wartość zwracaną do centrum zdarzeń.

Gdy centrum zdarzeń odbiera komunikat, generuje zdarzenie. `processSensorData`Funkcja jest uruchamiana po odebraniu zdarzenia. Następnie przetwarza dane zdarzenia i używa Azure Cosmos DB powiązania danych wyjściowych do wysyłania wyników do Azure Cosmos DB.

Dane używane przez te funkcje są przechowywane przy użyciu klasy o nazwie `TelemetryItem` , która musi zostać wdrożona. Utwórz nowy plik o nazwie `TelemetryItem.java` w tej samej lokalizacji co `Function.java` i Dodaj następujący kod:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Uruchamianie lokalnie

Teraz można kompilować i uruchamiać funkcje lokalnie i wyświetlać dane w Azure Cosmos DB.

Użyj następujących poleceń Maven do kompilowania i uruchamiania funkcji:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn clean package
mvn azure-functions:run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn clean package
mvn azure-functions:run
```

---

Po kilku komunikatach kompilacji i uruchamiania zobaczysz dane wyjściowe podobne do poniższego przykładu za każdym razem, gdy funkcje zostaną uruchomione:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

Następnie możesz przejść do [Azure Portal](https://portal.azure.com) i przejść do konta Azure Cosmos DB. Wybierz pozycję **Eksplorator danych**, rozwiń węzeł **TelemetryInfo**, a następnie wybierz pozycję **elementy** , aby wyświetlić dane po nadejściu.

![Cosmos DB Eksplorator danych](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Wdrażanie na platformie Azure i wyświetlanie telemetrii aplikacji

Na koniec możesz wdrożyć aplikację na platformie Azure i sprawdzić, czy będzie ona nadal działała tak samo jak lokalnie.

Wdróż projekt na platformie Azure przy użyciu następującego polecenia:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn azure-functions:deploy
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn azure-functions:deploy
```

---

Twoje funkcje działają teraz na platformie Azure i kontynuują gromadzenie danych w Azure Cosmos DB. Wdrożoną aplikację funkcji można wyświetlić w Azure Portal i wyświetlić dane telemetryczne aplikacji za pomocą połączonego zasobu Application Insights, jak pokazano na poniższych zrzutach ekranu:

**Live Metrics Stream:**

![Application Insights Live Metrics Stream](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Skuteczności**

![Application Insights blok wydajności](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z zasobami platformy Azure, które zostały utworzone w tym samouczku, można je usunąć za pomocą następującego polecenia:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az group delete --name %RESOURCE_GROUP%
```

---

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia funkcji platformy Azure, która obsługuje zdarzenia centrum zdarzeń i aktualizuje Cosmos DB. Aby uzyskać więcej informacji, zobacz [przewodnik dewelopera języka Java w Azure Functions](./functions-reference-java.md). Aby uzyskać informacje na temat używanych adnotacji, zobacz Kompendium [com. Microsoft. Azure. Functions. Annotation](/java/api/com.microsoft.azure.functions.annotation) .

W tym samouczku użyto zmiennych środowiskowych i ustawień aplikacji do przechowywania wpisów tajnych, takich jak parametry połączenia. Aby uzyskać informacje na temat przechowywania tych wpisów tajnych w Azure Key Vault, zobacz [Use Key Vault References for App Service i Azure Functions](../app-service/app-service-key-vault-references.md).

Następnie Dowiedz się, jak używać Azure Pipelines ciągłej integracji/ciągłego wdrażania:

> [!div class="nextstepaction"]
> [Kompiluj i wdrażaj środowisko Java do Azure Functions](/azure/devops/pipelines/ecosystems/java-function)
