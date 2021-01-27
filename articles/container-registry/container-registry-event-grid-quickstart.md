---
title: Szybki Start — wysyłanie zdarzeń do Event Grid
description: W tym przewodniku szybki start włączasz zdarzenia Event Grid dla rejestru kontenerów, a następnie wysyłamy zdarzenia wypychania i usuwania obrazu kontenera do aplikacji przykładowej.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: b4038bb232c14278e838f2c34706dcc103def7e4
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919667"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Szybki Start: wysyłanie zdarzeń z rejestru kontenerów prywatnych do Event Grid

Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która zapewnia jednorodne użycie zdarzeń przy użyciu modelu publikowania/subskrybowania. W tym przewodniku szybki start utworzysz rejestr kontenerów za pomocą interfejsu wiersza polecenia platformy Azure, zasubskrybujesz zdarzenia rejestru, a następnie wdrożono przykładową aplikację sieci Web do odbierania zdarzeń. Na koniec Wyzwalasz obraz kontenera `push` i `delete` zdarzenia i wyświetlasz ładunek zdarzenia w przykładowej aplikacji.

Po wykonaniu kroków opisanych w tym artykule zdarzenia wysyłane z rejestru kontenerów do Event Grid są wyświetlane w przykładowej aplikacji sieci Web:

![Przeglądarka sieci Web renderuje przykładową aplikację internetową przy użyciu trzech odebranych zdarzeń][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Polecenie interfejsu wiersza polecenia platformy Azure w tym artykule jest sformatowane dla powłoki **bash** . Jeśli używasz innej powłoki, na przykład programu PowerShell lub wiersza polecenia, konieczne może być odpowiednio dostosowanie wierszy kontynuacji wiersza lub zmiennych przypisanie. W tym artykule zastosowano zmienne, aby zminimalizować ilość wymagane do edycji poleceń.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener służący do wdrażania zasobów platformy Azure i zarządzania nimi. Następujące polecenie [AZ Group Create][az-group-create] tworzy grupę zasobów o nazwie Moja *resourceName* w regionie *wschodnim* . Jeśli chcesz użyć innej nazwy dla grupy zasobów, ustaw `RESOURCE_GROUP_NAME` inną wartość.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Następnie wdróż rejestr kontenerów w grupie zasobów przy użyciu następujących poleceń. Przed uruchomieniem polecenia [AZ ACR Create][az-acr-create] Ustaw jako `ACR_NAME` nazwę rejestru. Nazwa musi być unikatowa w ramach platformy Azure i jest ograniczona do 5-50 znaków alfanumerycznych.

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

W tej sekcji użyjesz szablonu Menedżer zasobów znajdującego się w repozytorium GitHub do wdrożenia wstępnie skompilowanej przykładowej aplikacji sieci Web do Azure App Service. Później można subskrybować zdarzenia Event Grid w rejestrze i określić tę aplikację jako punkt końcowy, do którego są wysyłane zdarzenia.

Aby wdrożyć przykładową aplikację, Ustaw jako `SITE_NAME` unikatową nazwę aplikacji sieci Web i wykonaj następujące polecenia. Nazwa witryny musi być unikatowa w ramach platformy Azure, ponieważ stanowi część w pełni kwalifikowanej nazwy domeny (FQDN) aplikacji sieci Web. W dalszej części możesz przejść do nazwy FQDN aplikacji w przeglądarce sieci Web, aby wyświetlić zdarzenia rejestru.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Gdy wdrożenie zakończyło się pomyślnie (może to potrwać kilka minut), Otwórz przeglądarkę i przejdź do aplikacji sieci Web, aby upewnić się, że jest uruchomiona:

`http://<your-site-name>.azurewebsites.net`

Powinna zostać wyświetlona Przykładowa aplikacja renderowana bez wyświetlonych komunikatów o zdarzeniach:

![Przeglądarka sieci Web przedstawiająca przykładową aplikację internetową bez wyświetlania zdarzeń][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Subskrybowanie zdarzeń rejestru

W Event Grid zasubskrybujesz *temat* , który ma poinformowanie o zdarzeniach, które chcesz śledzić, i o tym, gdzie je wysłać. Następujące polecenie [AZ eventgrid Event-Subscription Create][az-eventgrid-event-subscription-create] subskrybuje utworzony rejestr kontenerów i określa adres URL aplikacji sieci Web jako punkt końcowy, do którego powinny wysyłać zdarzenia. Zmienne środowiskowe, które zostały wypełnione we wcześniejszych sekcjach, są ponownie używane w tym miejscu, dlatego nie są wymagane żadne zmiany.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Po zakończeniu subskrypcji powinny zostać wyświetlone dane wyjściowe podobne do następujących:

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

Teraz, gdy Przykładowa aplikacja jest uruchomiona i subskrybujesz rejestr za pomocą Event Grid, możesz wygenerować niektóre zdarzenia. W tej sekcji użyjesz zadań ACR do kompilowania i wypychania obrazu kontenera do rejestru. ACR Tasks to funkcja Azure Container Registry, która umożliwia tworzenie obrazów kontenerów w chmurze bez konieczności instalowania aparatu platformy Docker na komputerze lokalnym.

### <a name="build-and-push-image"></a>Kompilowanie i wypychanie obrazu

Wykonaj następujące polecenie interfejsu wiersza polecenia platformy Azure, aby skompilować obraz kontenera z zawartości repozytorium GitHub. Domyślnie zadania ACR automatycznie wypchnięcia pomyślnie skompilowanego obrazu do rejestru, co spowoduje wygenerowanie `ImagePushed` zdarzenia.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

Dane wyjściowe powinny być podobne do następujących, podczas gdy ACR zadania kompilują i wypychają obraz. Następujące przykładowe dane wyjściowe zostały obcięte dla zwięzłości.

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

Aby sprawdzić, czy skompilowany obraz znajduje się w rejestrze, wykonaj następujące polecenie, aby wyświetlić Tagi w repozytorium "Moje obrazy":

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

W danych wyjściowych powinien zostać wyświetlony tag "v1" tworzonego obrazu, podobny do następującego:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Usuń obraz

Teraz Wygeneruj `ImageDeleted` zdarzenie, usuwając obraz za pomocą polecenia [AZ ACR Repository Delete][az-acr-repository-delete] :

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Powinny pojawić się dane wyjściowe podobne do następujących, z prośbą o potwierdzenie usunięcia manifestu i skojarzonych obrazów:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Wyświetlanie zdarzeń rejestru

Teraz można wypchnąć obraz do rejestru, a następnie go usunąć. Przejdź do aplikacji sieci Web w przeglądarce Event Grid i zobacz oba `ImageDeleted` `ImagePushed` zdarzenia i. Możesz również zobaczyć zdarzenie weryfikacji subskrypcji wygenerowane przez wykonanie polecenia w sekcji [subskrybowanie zdarzeń rejestru](#subscribe-to-registry-events) .

Poniższy zrzut ekranu przedstawia przykładową aplikację z trzema zdarzeniami, a `ImageDeleted` zdarzenie jest rozwinięte, aby wyświetlić jego szczegóły.

![Przeglądarka sieci Web przedstawiająca przykładową aplikację ze zdarzeniami ImagePushed i ImageDeleted][sample-app-03]

Gratulacje! Jeśli widzisz `ImagePushed` zdarzenia i `ImageDeleted` , rejestr wysyła zdarzenia do Event Grid, a Event Grid przekazuje te zdarzenia do punktu końcowego aplikacji sieci Web.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po wykonaniu zasobów utworzonych w ramach tego przewodnika Szybki Start można je usunąć za pomocą poniższego polecenia platformy Azure. Po usunięciu grupy zasobów wszystkie zawarte w niej zasoby są trwale usuwane.

**Ostrzeżenie**: Ta operacja jest nieodwracalna. Przed uruchomieniem polecenia upewnij się, że żaden z zasobów w grupie nie jest już potrzebny.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

Informacje o schemacie komunikatu o zdarzeniu Azure Container Registry można znaleźć w dokumentacji Event Grid:

[Schemat zdarzeń Azure Event Grid dla Container Registry](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono rejestr kontenerów, utworzono obraz z zadaniami ACR, usunięto go i wykorzystano zdarzenia rejestru z Event Grid z przykładową aplikacją. Następnie przejdź do samouczka ACR Tasks, aby dowiedzieć się więcej na temat tworzenia obrazów kontenerów w chmurze, w tym zautomatyzowanych kompilacji w ramach aktualizacji obrazu podstawowego:

> [!div class="nextstepaction"]
> [Tworzenie obrazów kontenerów w chmurze za pomocą zadań ACR](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
