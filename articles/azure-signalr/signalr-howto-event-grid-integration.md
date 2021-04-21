---
title: Jak wysyłać zdarzenia Azure SignalR Service do Event Grid
description: Przewodnik po tym, jak włączyć zdarzenia Event Grid dla aplikacji SignalR Service, a następnie wysłać zdarzenia połączenia klienta połączone/odłączone do przykładowej aplikacji.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 65fb54dbfc5158ef8cc2b488f267c92f601502f6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784609"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Jak wysyłać zdarzenia z usługi Azure SignalR Service do usługi Event Grid

Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która zapewnia jednolite zużycie zdarzeń przy użyciu modelu pub-sub. W tym przewodniku użyjemy interfejsu wiersza polecenia platformy Azure, aby utworzyć Azure SignalR Service, zasubskrybować zdarzenia połączenia, a następnie wdrożyć przykładową aplikację internetową w celu odbierania zdarzeń. Na koniec możesz nawiązać połączenie i rozłączyć się, aby zobaczyć ładunek zdarzenia w przykładowej aplikacji.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Polecenia interfejsu wiersza polecenia platformy Azure w tym artykule są sformatowane dla powłoki **Bash.** Jeśli używasz innej powłoki, np. programu PowerShell lub wiersza polecenia, może być konieczne odpowiednie dostosowanie znaków kontynuacji wiersza lub wierszy przypisania zmiennej. W tym artykule są używane zmienne w celu zminimalizowania wymaganego edytowania poleceń.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener, w którym wdrażasz zasoby platformy Azure i zarządzasz nimi. Następujące polecenie [az group create][az-group-create] tworzy grupę zasobów o nazwie *myResourceGroup* w *regionie eastus.* Jeśli chcesz użyć innej nazwy dla grupy zasobów, ustaw `RESOURCE_GROUP_NAME` inną wartość.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Tworzenie usługi SignalR Service

Następnie wd wdrażają usługę Azure Signalr Service w grupie zasobów za pomocą następujących poleceń.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Po utworzeniu SignalR Service interfejs wiersza polecenia platformy Azure zwraca dane wyjściowe podobne do następujących:

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

W tej sekcji użyjemy szablonu Resource Manager znajdującego się w repozytorium GitHub, aby wdrożyć wstępnie sbudowaną przykładową aplikację internetową w usłudze Azure App Service. Później zasubskrybujesz zdarzenia Event Grid rejestru i określisz tę aplikację jako punkt końcowy, do którego są wysyłane zdarzenia.

Aby wdrożyć przykładową aplikację, ustaw unikatową nazwę aplikacji internetowej `SITE_NAME` i wykonaj następujące polecenia. Nazwa witryny musi być unikatowa w obrębie platformy Azure, ponieważ stanowi część w pełni kwalifikowanej nazwy domeny (FQDN) aplikacji internetowej. W dalszej sekcji przejdź do sieci FQDN aplikacji w przeglądarce internetowej, aby wyświetlić zdarzenia rejestru.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Po zakończeniu wdrażania (może to potrwać kilka minut) otwórz przeglądarkę i przejdź do aplikacji internetowej, aby upewnić się, że jest uruchomiona:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subskrybowanie zdarzeń rejestru

W Event Grid subskrybujesz temat, aby poinformować go o tym, które zdarzenia chcesz śledzić i dokąd je wysłać.  Następujące [polecenie az eventgrid event-subscription create][az-eventgrid-event-subscription-create] subskrybuje Azure SignalR Service i określa adres URL aplikacji internetowej jako punkt końcowy, do którego ma wysyłać zdarzenia. Zmienne środowiskowe wypełnione we wcześniejszych sekcjach są ponownie używane w tym miejscu, więc nie są wymagane żadne zmiany.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Po zakończeniu subskrypcji powinny zostać wyświetlony dane wyjściowe podobne do następujących:

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

Przełącz się do trybu usługi `Serverless Mode` i skonfiguruj połączenie klienta z SignalR Service. Jako odwołanie możesz [użyć](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) przykładu bez serwera.

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

Klient został połączony z SignalR Service. Przejdź do aplikacji Event Grid Viewer. Powinno zostać wyświetlony `ClientConnectionConnected` zdarzenie. Jeśli zakończysz działanie klienta, zobaczysz również `ClientConnectionDisconnected` zdarzenie.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create
[az-group-create]: /cli/azure/group#az_group_create
