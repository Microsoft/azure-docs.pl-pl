---
title: Jak wysyłać zdarzenia usługi Azure sygnalizacyjne do Event Grid
description: Przewodnik przedstawiający sposób włączania zdarzeń Event Grid dla usługi sygnalizującego, a następnie wysyłania zdarzeń podłączonych/odłączonych połączenia klienta do przykładowej aplikacji.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 84b83c1dd541418c446a89a6f51be668cb41e54e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94562648"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Jak wysyłać zdarzenia z usługi Azure SignalR Service do usługi Event Grid

Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która zapewnia jednorodne użycie zdarzeń przy użyciu modelu pub-sub. W tym przewodniku użyjesz interfejsu wiersza polecenia platformy Azure, aby utworzyć usługę Azure Signal, subskrybować zdarzenia połączeń, a następnie wdrożyć przykładową aplikację sieci Web do odbierania zdarzeń. Na koniec można nawiązać połączenie i rozłączyć i wyświetlić ładunek zdarzenia w przykładowej aplikacji.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Polecenie interfejsu wiersza polecenia platformy Azure w tym artykule jest sformatowane dla powłoki **bash** . Jeśli używasz innej powłoki, na przykład programu PowerShell lub wiersza polecenia, konieczne może być odpowiednio dostosowanie wierszy kontynuacji wiersza lub zmiennych przypisanie. W tym artykule zastosowano zmienne, aby zminimalizować ilość wymagane do edycji poleceń.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener służący do wdrażania zasobów platformy Azure i zarządzania nimi. Następujące polecenie [AZ Group Create][az-group-create] tworzy grupę zasobów o nazwie Moja *resourceName* w regionie *wschodnim* . Jeśli chcesz użyć innej nazwy dla grupy zasobów, ustaw `RESOURCE_GROUP_NAME` inną wartość.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Tworzenie usługi SignalR Service

Następnie wdróż usługę Azure Signal Service w grupie zasobów przy użyciu następujących poleceń.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Po utworzeniu usługi sygnalizującej interfejs wiersza polecenia platformy Azure zwraca dane wyjściowe podobne do następujących:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Tworzenie punktu końcowego zdarzenia

W tej sekcji użyjesz szablonu Menedżer zasobów znajdującego się w repozytorium GitHub do wdrożenia wstępnie skompilowanej przykładowej aplikacji sieci Web do Azure App Service. Później można subskrybować zdarzenia Event Grid w rejestrze i określić tę aplikację jako punkt końcowy, do którego są wysyłane zdarzenia.

Aby wdrożyć przykładową aplikację, Ustaw jako `SITE_NAME` unikatową nazwę aplikacji sieci Web i wykonaj następujące polecenia. Nazwa witryny musi być unikatowa w ramach platformy Azure, ponieważ stanowi część w pełni kwalifikowanej nazwy domeny (FQDN) aplikacji sieci Web. W dalszej części możesz przejść do nazwy FQDN aplikacji w przeglądarce sieci Web, aby wyświetlić zdarzenia rejestru.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Po pomyślnym wdrożeniu (może to potrwać kilka minut) Otwórz przeglądarkę i przejdź do aplikacji sieci Web, aby upewnić się, że jest uruchomiona:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subskrybowanie zdarzeń rejestru

W Event Grid zasubskrybujesz *temat* , który ma poinformowanie o zdarzeniach, które chcesz śledzić, i o tym, gdzie je wysłać. Następujące polecenie [AZ eventgrid Event-Subscription Create][az-eventgrid-event-subscription-create] subskrybuje utworzoną usługę Azure Signal Service i określa adres URL aplikacji sieci Web jako punkt końcowy, do którego powinny wysyłać zdarzenia. Zmienne środowiskowe, które zostały wypełnione we wcześniejszych sekcjach, są ponownie używane w tym miejscu, dlatego nie są wymagane żadne zmiany.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Po zakończeniu subskrypcji powinny zostać wyświetlone dane wyjściowe podobne do następujących:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Wyzwalanie zdarzeń rejestru

Przejdź do trybu usługi `Serverless Mode` i skonfiguruj połączenie z klientem do usługi sygnalizującej. Jako odwołanie można pobrać [bezserwerowy przykład](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) .

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Wyświetlanie zdarzeń rejestru

Klient został podłączony do usługi sygnalizującej. Przejdź do aplikacji sieci Web w przeglądarce Event Grid i zobaczysz `ClientConnectionConnected` zdarzenie. Jeśli klient zostanie przerwany, zobaczysz również `ClientConnectionDisconnected` zdarzenie.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
