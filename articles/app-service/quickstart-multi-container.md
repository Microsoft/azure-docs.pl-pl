---
title: 'Szybki start: tworzenie aplikacji z wieloma kontenerami'
description: Rozpoczynanie pracy z aplikacjami wielo kontenerów na Azure App Service przez wdrożenie pierwszej aplikacji z wieloma kontenerami.
keywords: azure app service, web app, linux, docker, compose, multicontainer, multi-container, web app for containers, multiple containers, container, wordpress, azure db for mysql, production database with containers
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 0ea55c36c239b5ecdb51ef3dc7a3ff762718bd1e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769010"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Tworzenie aplikacji z wieloma kontenerami (w wersji zapoznawczej) przy użyciu konfiguracji narzędzia Docker Compose

> [!NOTE]
> Wiele kontenerów jest w wersji zapoznawczej.

Funkcja [Web App for Containers](overview.md#app-service-on-linux) oferuje elastyczny sposób korzystania z obrazów platformy Docker. W tym przewodniku Szybki start pokazano, jak wdrożyć aplikację z wieloma kontenerami (wersja zapoznawcza) Web App for Containers w Cloud Shell [przy](../cloud-shell/overview.md) użyciu Docker Compose konfiguracji.

![Przykładowa aplikacja wielokontenerowa funkcji Web App for Containers][1]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.32 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="download-the-sample"></a>Pobieranie przykładu

W tym przewodniku Szybki Start używany jest plik Compose usługi [Docker](https://docs.docker.com/compose/wordpress/#define-the-project). Plik konfiguracji można znaleźć na stronie z [przykładami dla platformy Azure](https://github.com/Azure-Samples/multicontainerwordpress).

[!code-yml[Main](../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

W usłudze Cloud Shell utwórz katalog Szybki start, a następnie przejdź do niego.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny. Następnie zmień katalog na `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

[!INCLUDE [resource group intro text](../../includes/resource-group.md)]

W Cloud Shell utwórz grupę zasobów za pomocą [`az group create`](/cli/azure/group#az_group_create) polecenia . Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *Południowo-środkowe stany USA*. Aby wyświetlić wszystkie obsługiwane lokalizacje dla usługi App Service w systemie Linux w warstwie **Standardowa**, uruchom polecenie [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice#az_appservice_list_locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Zasadniczo grupy zasobów i zasoby są tworzone w pobliskim regionie.

Po zakończeniu działania polecenia zostaną wyświetlone dane wyjściowe JSON z właściwościami grupy zasobów.

## <a name="create-an-azure-app-service-plan"></a>Tworzenie planu usługi Azure App Service

W Cloud Shell utwórz plan App Service w grupie zasobów za pomocą [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) polecenia .

Poniższy przykład tworzy plan usługi App Service o nazwie `myAppServicePlan` przy użyciu warstwy cenowej **Standardowa** (`--sku S1`) w kontenerze systemu Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Po utworzeniu planu usługi App Service interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do następujących:

<pre>
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
</pre>

## <a name="create-a-docker-compose-app"></a>Tworzenie aplikacji narzędzia Docker Compose

> [!NOTE]
> Docker Compose usłudze Azure App Services obecnie limit 4000 znaków.

W terminalu usługi Cloud Shell utwórz wielokontenerową [aplikację internetową](overview.md#app-service-on-linux) w ramach planu usługi App Service `myAppServicePlan`, używając polecenia [az webapp create](/cli/azure/webapp#az_webapp_create). Nie zapomnij zastąpić wartości unikatową nazwą aplikacji _\<app_name>_ (prawidłowe znaki to `a-z` , i `0-9` `-` ).

```azurecli
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

<pre>
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app_name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

### <a name="browse-to-the-app"></a>Przechodzenie do aplikacji

Przejdź do wdrożonej aplikacji pod adresem `http://<app_name>.azurewebsites.net`. Ładowanie aplikacji może potrwać kilka minut. Jeśli wystąpi błąd, zaczekaj jeszcze kilka minut, a następnie odśwież przeglądarkę.

![Przykładowa aplikacja wielokontenerowa funkcji Web App for Containers][1]

**Gratulacje,** aplikacja z wieloma kontenerami została utworzona w Web App for Containers.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: aplikacja WordPress z wieloma kontenerami](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie kontenera niestandardowego](configure-custom-container.md)

<!--Image references-->
[1]: media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
