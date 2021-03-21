---
title: Konfigurowanie Data Manager łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure — usługa Azure łańcucha bloków
description: Tworzenie Data Manager łańcucha bloków dla usługi Azure łańcucha bloków i zarządzanie nim przy użyciu interfejsu wiersza polecenia platformy Azure
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 867a51b60afa56005bbb297b345f8a9260160ab8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722640"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Konfigurowanie menedżera danych łańcucha bloków przy użyciu interfejsu wiersza polecenia platformy Azure

Skonfiguruj Data Manager łańcucha bloków dla usługi Azure łańcucha bloków, aby przechwytywać dane łańcucha bloków wysyłane do Azure Event Grid tematu.

Aby skonfigurować wystąpienie Data Manager łańcucha bloków:

* Tworzenie wystąpienia Menedżera łańcucha bloków
* Utwórz dane wejściowe w węźle transakcji usługi Azure łańcucha bloków Service
* Tworzenie danych wyjściowych w temacie Azure Event Grid
* Dodawanie aplikacji łańcucha bloków
* Uruchom wystąpienie

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zaloguj się przy użyciu programu `az login` .
* Kończenie [szybkiego startu: użyj Visual Studio Code, aby nawiązać połączenie z siecią konsorcjum usługi Azure łańcucha bloków](connect-vscode.md). W przypadku korzystania z usługi łańcucha bloków Data Manager zaleca się użycie warstwy *Standard* usług Azure łańcucha bloków.
* Tworzenie [tematu Event Grid](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Informacje o [obsłudze zdarzeń w Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie.

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Cloud Shell można również uruchomić na osobnej karcie przeglądarki, przechodząc do [https://shell.azure.com/bash](https://shell.azure.com/bash) . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.

Jeśli wolisz zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.51 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Utwórz wystąpienie

Wystąpienie Data Manager łańcucha bloków monitoruje węzeł transakcji usługi Azure łańcucha bloków. Wystąpienie przechwytuje wszystkie nieprzetworzone i nieprzetworzone dane transakcji z węzła transakcji. Łańcucha bloków Data Manager publikuje komunikat **RawBlockAndTransactionMsg** , który jest nadzbiorem informacji zwracanych z web3. ETH [getblock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) i [gettransaction](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction) zapytania.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parametr | Opis |
|-----------|-------------|
| resource-group | Nazwa grupy zasobów, w której ma zostać utworzone wystąpienie Data Manager łańcucha bloków. |
| name | Nazwa wystąpienia Data Manager łańcucha bloków. |
| Typ zasobu | Typ zasobu dla wystąpienia usługi łańcucha bloków Data Manager to **Microsoft. łańcucha bloków/WATCHS**. |
| is-full-Object | Wskazuje, że właściwości zawierają opcje dla zasobu obserwatora. |
| properties | Ciąg w formacie JSON zawierający właściwości dla zasobu obserwatora. Można przesłać jako ciąg lub plik.  |

### <a name="create-instance-examples"></a>Tworzenie przykładów wystąpienia

Przykład konfiguracji JSON tworzenia wystąpienia Menedżera łańcucha bloków w regionie **Wschodnie stany USA** .

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Element | Opis |
|---------|-------------|
| location | Region, w którym ma zostać utworzony zasób obserwatora |
| properties | Właściwości do ustawienia podczas tworzenia zasobu obserwatora |

Utwórz wystąpienie Data Manager łańcucha bloków o nazwie " *Watch* " przy użyciu ciągu JSON na potrzeby konfiguracji.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Utwórz wystąpienie Data Manager łańcucha bloków o nazwie " *Watch* " przy użyciu pliku konfiguracyjnego JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Utwórz dane wejściowe

Dane wejściowe nawiązują połączenie Data Manager łańcucha bloków z węzłem transakcji usługi Azure łańcucha bloków. Połączenie może utworzyć tylko użytkownicy z dostępem do węzła transakcji.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parametr | Opis |
|-----------|-------------|
| resource-group | Nazwa grupy zasobów, w której ma zostać utworzony zasób wejściowy. |
| name | Nazwa danych wejściowych. |
| namespace | Użyj przestrzeni nazw dostawcy **Microsoft. łańcucha bloków** . |
| Typ zasobu | Typ zasobu dla danych wejściowych łańcucha bloków Data Manager to **dane** wejściowe. |
| nadrzędny | Ścieżka do obserwatora, z którym jest skojarzone dane wejściowe. Na przykład **obserwatorzy/czujka**. |
| is-full-Object | Wskazuje, że właściwości zawierają opcje dla zasobu wejściowego. |
| properties | Ciąg w formacie JSON zawierający właściwości dla zasobu wejściowego. Można przesłać jako ciąg lub plik. |

### <a name="input-examples"></a>Przykłady danych wejściowych

Przykład konfiguracji JSON tworzenia zasobu wejściowego w regionie *Wschodnie stany USA* , który jest połączony z \<Blockchain member\> .

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Element | Opis |
|---------|-------------|
| location | Region, w którym ma zostać utworzony zasób wejściowy. |
| Atrybutów InputType | Typ księgi elementu członkowskiego usługi Azure łańcucha bloków. Obecnie **Ethereum** jest obsługiwana. |
| resourceId | Węzeł transakcji, do którego jest połączone dane wejściowe. Zastąp \<Subscription ID\> \<Resource group\> wartości, i \<Blockchain member\> wartościami dla zasobu węzła transakcji. Dane wejściowe nawiązują połączenie z domyślnym węzłem transakcji dla elementu członkowskiego usługi Azure łańcucha bloków. |

Utwórz dane wejściowe o nazwie Moje *dane wejściowe* dla elementu *czujka* przy użyciu ciągu JSON na potrzeby konfiguracji.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Utwórz dane wejściowe o nazwie Moje *dane wejściowe* dla elementu *czujka* przy użyciu pliku konfiguracyjnego JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Utwórz dane wyjściowe

Połączenie wychodzące wysyła dane łańcucha bloków do Azure Event Grid. Dane łańcucha bloków można wysyłać do jednego miejsca docelowego lub wysyłać dane łańcucha bloków do wielu miejsc docelowych. Łańcucha bloków Data Manager obsługuje wiele połączeń wychodzących tematu Event Grid dla danego wystąpienia Data Manager łańcucha bloków.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parametr | Opis |
|-----------|-------------|
| resource-group | Nazwa grupy zasobów, w której ma zostać utworzony zasób wyjściowy. |
| name | Nazwa danych wyjściowych. |
| namespace | Użyj przestrzeni nazw dostawcy **Microsoft. łańcucha bloków** . |
| Typ zasobu | Typ zasobu dla danych wyjściowych łańcucha bloków **Data Manager to wyjście**. |
| nadrzędny | Ścieżka do obserwatora, z którym jest skojarzone dane wyjściowe. Na przykład **obserwatorzy/czujka**. |
| is-full-Object | Wskazuje, że właściwości zawierają opcje dla zasobu wyjściowego. |
| properties | Ciąg w formacie JSON zawierający właściwości dla zasobu wyjściowego. Można przesłać jako ciąg lub plik. |

### <a name="output-examples"></a>Przykłady danych wyjściowych

Przykład konfiguracji JSON tworzenia zasobu wyjściowego w regionie *Wschodnie stany USA* , który jest połączony z tematem w usłudze Event Grid o nazwie \<event grid topic\> .

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Element | Opis |
|---------|-------------|
| location | Region, w którym ma zostać utworzony zasób wyjściowy. |
| outputType | Typ danych wyjściowych. Obecnie **EventGrid** jest obsługiwana. |
| resourceId | Zasób, do którego jest połączone dane wyjściowe. Zastąp \<Subscription ID\> \<Resource group\> wartości, i \<Blockchain member\> wartościami dla zasobu usługi Event Grid. |

Utwórz dane wyjściowe o nazwie Moje *dane wyjściowe* dla elementu *czujka* , który nawiązuje połączenie z tematem usługi Event Grid przy użyciu ciągu konfiguracji JSON.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Utwórz dane wyjściowe o nazwie Moje *dane wyjściowe* dla elementu *czujka* , który nawiązuje połączenie z tematem usługi Event Grid przy użyciu pliku konfiguracyjnego JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Dodawanie aplikacji łańcucha bloków

Jeśli dodasz aplikację łańcucha bloków, łańcucha bloków Data Manager dekoduje zdarzenie i stan właściwości aplikacji. W przeciwnym razie wysyłane są tylko nieprzetworzone dane transakcji blokowych i nieprzetworzonych. Łańcucha bloków Data Manager również wykrywa adresy kontraktu po wdrożeniu kontraktu. Można dodać wiele aplikacji łańcucha bloków do wystąpienia Data Manager łańcucha bloków.


> [!IMPORTANT]
> Obecnie aplikacje łańcucha bloków, które deklarują [Typy tablic](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) stałych lub [typy mapowania](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) , nie są w pełni obsługiwane. Właściwości zadeklarowane jako tablica lub typy mapowania nie będą zdekodowane w wiadomościach *ContractPropertiesMsg* lub *DecodedContractEventsMsg* .

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parametr | Opis |
|-----------|-------------|
| resource-group | Nazwa grupy zasobów, w której ma zostać utworzony zasób aplikacji. |
| name | Nazwa aplikacji. |
| namespace | Użyj przestrzeni nazw dostawcy **Microsoft. łańcucha bloków** . |
| Typ zasobu | Typem zasobu dla aplikacji łańcucha bloków Data Manager jest **artefakty**. |
| nadrzędny | Ścieżka do obserwatora, z którym jest skojarzona aplikacja. Na przykład **obserwatorzy/czujka**. |
| is-full-Object | Wskazuje, że właściwości zawierają opcje dla zasobu aplikacji. |
| properties | Ciąg w formacie JSON zawierający właściwości dla zasobu aplikacji. Można przesłać jako ciąg lub plik. |

### <a name="blockchain-application-examples"></a>Przykłady aplikacji łańcucha bloków

Przykład konfiguracji JSON tworzenia zasobu aplikacji w regionie *Wschodnie stany USA* , który monitoruje kontrakt inteligentny zdefiniowany przez ABI kontraktu i kod bajtowy.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Element | Opis |
|---------|-------------|
| location | Region, w którym ma zostać utworzony zasób aplikacji. |
| artefakttype | Typ aplikacji. Obecnie **EthereumSmartContract** jest obsługiwana. |
| abiFileUrl | Adres URL pliku JSON ABI kontraktu inteligentnego. Aby uzyskać więcej informacji na temat uzyskiwania ABIów kontraktu i tworzenia adresu URL, zobacz [pobieranie kontraktu ABI i kod bajtowy](data-manager-portal.md#get-contract-abi-and-bytecode) oraz [Tworzenie ABI i adresów URL kontraktu](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | Adres URL pliku JSON wdrożenia kontraktu inteligentnego. Aby uzyskać więcej informacji na temat uzyskiwania kodu bajtowego wdrożonego w kontrakcie inteligentnym i tworzenia adresu URL, zobacz [pobieranie kontraktu ABI i kod bajtowy](data-manager-portal.md#get-contract-abi-and-bytecode) oraz [Tworzenie kontraktu ABI i URL kodu bajtowego](data-manager-portal.md#create-contract-abi-and-bytecode-url). Uwaga: łańcucha bloków Data Manager wymaga **wdrożonego kodu bajtowego**. |
| queryTargetTypes | Typy opublikowanych komunikatów. Określanie **ContractProperties** publikuje *ContractPropertiesMsg* typ komunikatu. Określanie **ContractEvents** publikuje *DecodedContractEventsMsg* typ komunikatu. Uwaga: typy wiadomości *RawBlockAndTransactionMsg* i *RawTransactionContractCreationMsg* są zawsze publikowane. |

Utwórz aplikację o nazwie Moja *aplikacja* dla elementu *czujka* , która monitoruje kontrakt inteligentny zdefiniowany przez ciąg JSON.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Utwórz aplikację o nazwie Moja *aplikacja* dla elementu *czujka* , która obserwuje kontrakt inteligentny zdefiniowany przy użyciu pliku konfiguracyjnego JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Uruchom wystąpienie

Po uruchomieniu wystąpienie programu łańcucha bloków Manager monitoruje zdarzenia łańcucha bloków ze zdefiniowanych danych wejściowych i wysyła dane do zdefiniowanych danych wyjściowych.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametr | Opis |
|-----------|-------------|
| akcja | Uruchom obserwatora przy użyciu **menu Start** . |
| identyfikatory | Identyfikator zasobu obserwatora. Zastąp \<Subscription ID\> \<Resource group\> wartości, i \<Watcher name\> wartościami dla zasobu obserwatora.|

### <a name="start-instance-example"></a>Przykład uruchomienia wystąpienia

Uruchom wystąpienie Data Manager łańcucha bloków o nazwie *czujka*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Zatrzymaj wystąpienie

Zatrzymaj wystąpienie Data Manager łańcucha bloków.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parametr | Opis |
|-----------|-------------|
| akcja | Użyj **Zatrzymaj** , aby zatrzymać obserwatora. |
| identyfikatory | Nazwa obserwatora. Zastąp \<Subscription ID\> \<Resource group\> wartości, i \<Watcher name\> wartościami dla zasobu obserwatora. |

### <a name="stop-watcher-example"></a>Zatrzymywanie przykładu obserwatora

Zatrzymaj wystąpienie o nazwie *czujka*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Usuń wystąpienie

Usuń wystąpienie Data Manager łańcucha bloków.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parametr | Opis |
|-----------|-------------|
| resource-group | Nazwa grupy zasobów obserwatora do usunięcia. |
| name | Nazwa obserwatora do usunięcia. |
| Typ zasobu | Typ zasobu dla obserwatora Data Manager łańcucha bloków to **Microsoft. łańcucha bloków/WATCHS**. |

### <a name="delete-instance-example"></a>Przykład usuwania wystąpienia

Usuń wystąpienie o nazwie " *czujka* " w grupie zasobów *mojagz* .

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Następne kroki

Wypróbuj następny samouczek tworzenia Eksploratora komunikatów transakcji łańcucha bloków za pomocą łańcucha bloków Data Manager i Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Wysyłanie danych do usługi Azure Cosmos DB za pomocą menedżera danych łańcucha bloków](data-manager-cosmosdb.md)
