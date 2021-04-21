---
title: Szybki start — wysyłanie zdarzeń do Event Grid
description: W tym przewodniku Szybki start włączysz zdarzenia Event Grid dla rejestru kontenerów, a następnie wyślesz zdarzenia wypychania obrazu kontenera i usuwania ich do przykładowej aplikacji.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 43dea2640c9c9445ea464205f6c586bc1e486206
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784029"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Szybki start: wysyłanie zdarzeń z prywatnego rejestru kontenerów do Event Grid

Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która zapewnia jednolite zużycie zdarzeń przy użyciu modelu publikowania i subskrybowania. W tym przewodniku Szybki start użyjemy interfejsu wiersza polecenia platformy Azure do utworzenia rejestru kontenerów, zasubskrybowania zdarzeń rejestru, a następnie wdrożenia przykładowej aplikacji internetowej w celu odbierania zdarzeń. Na koniec należy wyzwolić obraz kontenera i zdarzenia oraz wyświetlić ładunek zdarzenia `push` `delete` w przykładowej aplikacji.

Po ukończeniu kroków w tym artykule zdarzenia wysyłane z rejestru kontenerów do usługi Event Grid zostaną wyświetlone w przykładowej aplikacji internetowej:

![Przeglądarka internetowa renderuje przykładową aplikację internetową z trzema odebranych zdarzeniami][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Polecenia interfejsu wiersza polecenia platformy Azure w tym artykule są sformatowane dla powłoki **Bash.** Jeśli używasz innej powłoki, np. programu PowerShell lub wiersza polecenia, może być konieczne odpowiednie dostosowanie znaków kontynuacji wiersza lub wierszy przypisania zmiennej. W tym artykule są używane zmienne w celu zminimalizowania wymaganego edytowania poleceń.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener, w którym wdrażasz zasoby platformy Azure i zarządzasz nimi. Następujące polecenie [az group create][az-group-create] tworzy grupę zasobów o nazwie *myResourceGroup* w *regionie eastus.* Jeśli chcesz użyć innej nazwy dla grupy zasobów, ustaw `RESOURCE_GROUP_NAME` inną wartość.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Następnie wdrożysz rejestr kontenerów w grupie zasobów za pomocą następujących poleceń. Przed uruchomieniem [polecenia az acr create][az-acr-create] ustaw wartość na nazwę `ACR_NAME` rejestru. Nazwa musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Po utworzeniu rejestru interfejs wiersza polecenia platformy Azure zwraca dane wyjściowe podobne do następujących:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
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

Powinna zostać wyświetlona przykładowa aplikacja renderowana bez komunikatów o zdarzeniach:

![Przeglądarka internetowa z wyświetloną przykładową aplikacją internetową bez wyświetlanych zdarzeń][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subskrybowanie zdarzeń rejestru

W Event Grid subskrybujesz temat, aby poinformować go, które zdarzenia chcesz śledzić i gdzie je wysłać.  Następujące polecenie [az eventgrid event-subscription create][az-eventgrid-event-subscription-create] subskrybuje utworzony rejestr kontenerów i określa adres URL aplikacji internetowej jako punkt końcowy, do którego ma wysyłać zdarzenia. Zmienne środowiskowe wypełnione we wcześniejszych sekcjach są ponownie używane w tym miejscu, więc nie są wymagane żadne zmiany.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Po zakończeniu subskrypcji powinny zostać wyświetlony dane wyjściowe podobne do następujących:

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Wyzwalanie zdarzeń rejestru

Teraz, gdy przykładowa aplikacja jest uruchomiona i subskrybujesz rejestr za pomocą Event Grid, możesz wygenerować pewne zdarzenia. W tej sekcji użyjemy zadania usługi ACR, aby skompilować i wypchnąć obraz kontenera do rejestru. zadania usługi ACR to funkcja usługi Azure Container Registry, która umożliwia kompilowanie obrazów kontenerów w chmurze bez konieczności kompilowania aparatu platformy Docker na komputerze lokalnym.

### <a name="build-and-push-image"></a>Kompilowanie i wypychanie obrazu

Wykonaj następujące polecenie interfejsu wiersza polecenia platformy Azure, aby skompilować obraz kontenera z zawartości repozytorium GitHub. Domyślnie program zadania usługi ACR automatycznie wypycha pomyślnie sbudowaną obraz do rejestru, co spowoduje wygenerowanie `ImagePushed` zdarzenia.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

Dane wyjściowe powinny być podobne do następujących podczas zadania usługi ACR kompilacji, a następnie wypychania obrazu. Następujące przykładowe dane wyjściowe zostały obcięte w celu zwięzłości.

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Aby sprawdzić, czy wbudowany obraz znajduje się w rejestrze, wykonaj następujące polecenie, aby wyświetlić tagi w repozytorium "myimage":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

W danych wyjściowych powinien pojawić się tag "v1" obrazu, podobny do następującego:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Usuwanie obrazu

Teraz `ImageDeleted` wygeneruj zdarzenie, usuwając obraz za pomocą [polecenia az acr repository delete:][az-acr-repository-delete]

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Powinny zostać wyświetlony dane wyjściowe podobne do następujących, z prośbą o potwierdzenie usunięcia manifestu i skojarzonych obrazów:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Wyświetlanie zdarzeń rejestru

Obraz został wypchnięty do rejestru, a następnie usunięty. Przejdź do aplikacji internetowej Event Grid Viewer. Powinny zostać wyświetlony zarówno zdarzenia `ImageDeleted` , jak i `ImagePushed` . Możesz również zobaczyć zdarzenie weryfikacji subskrypcji wygenerowane przez wykonanie polecenia w sekcji [Subskrybuj zdarzenia](#subscribe-to-registry-events) rejestru.

Poniższy zrzut ekranu przedstawia przykładową aplikację z trzema zdarzeniami, a zdarzenie jest rozwinięte w celu `ImageDeleted` pokazania jego szczegółów.

![Przeglądarka internetowa z przykładową aplikacją ze zdarzeniami ImagePushed i ImageDeleted][sample-app-03]

Gratulacje! Jeśli zobaczysz zdarzenia i , rejestr wysyła zdarzenia do usługi Event Grid, a Event Grid przekazuje te zdarzenia do `ImagePushed` `ImageDeleted` punktu końcowego aplikacji internetowej.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z zasobami utworzonymi w tym przewodniku Szybki start możesz je usunąć za pomocą następującego polecenia interfejsu wiersza polecenia platformy Azure. Po usunięciu grupy zasobów wszystkie zasoby, które zawiera, zostaną trwale usunięte.

**OSTRZEŻENIE:** Ta operacja jest nieodwracalna. Przed uruchomieniem polecenia upewnij się, że nie są już potrzebne żadne zasoby w grupie.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

Informacje o schemacie komunikatów Azure Container Registry można znaleźć w dokumentacji Event Grid zdarzenia:

[Azure Event Grid zdarzeń dla Container Registry](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono rejestr kontenerów, skonstruowaliśmy obraz z usługą zadania usługi ACR, usunąliśmy go i użyliśmy zdarzeń rejestru z usługi Event Grid za pomocą przykładowej aplikacji. Następnie przejdź do samouczka zadania usługi ACR, aby dowiedzieć się więcej o kompilowaniu obrazów kontenerów w chmurze, w tym o zautomatyzowanych kompilacjach przy aktualizacji obrazu podstawowego:

> [!div class="nextstepaction"]
> [Tworzenie obrazów kontenerów w chmurze za pomocą zadania usługi ACR](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create
[az-group-create]: /cli/azure/group#az_group_create
