---
title: 'Szybki Start: Tworzenie aplikacji z obsługą kontenera'
description: Zacznij korzystać z aplikacji wielokontenerowych na Azure App Service, wdrażając pierwszą aplikację z obsługą kontenera.
keywords: Azure App Service, Web App, Linux, Docker, redagowanie, wielokontenerowe, aplikacje sieci Web dla kontenerów, wiele kontenerów, kontener, WordPress, Azure DB for MySQL, produkcyjna baza danych z kontenerami
author: msangapu-msft
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 2ba42e5e800ae607631e00aee50954bf2638ae43
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97007147"
---
# <a name="create-a-multi-container-preview-app-using-a-docker-compose-configuration"></a>Tworzenie aplikacji z wieloma kontenerami (w wersji zapoznawczej) przy użyciu konfiguracji narzędzia Docker Compose

> [!NOTE]
> Wiele kontenerów jest w wersji zapoznawczej.

Funkcja [Web App for Containers](overview.md#app-service-on-linux) oferuje elastyczny sposób korzystania z obrazów platformy Docker. Ten przewodnik Szybki Start przedstawia sposób wdrażania aplikacji z obsługą kontenera (wersja zapoznawcza) w celu Web App for Containers w [Cloud Shell](../cloud-shell/overview.md) przy użyciu konfiguracji Docker Compose.

![Przykładowa aplikacja wielokontenerowa funkcji Web App for Containers][1]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

Ten artykuł wymaga wersji 2.0.32 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

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

W Cloud Shell Utwórz grupę zasobów za pomocą [`az group create`](/cli/azure/group#az-group-create) polecenia. Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *Południowo-środkowe stany USA*. Aby wyświetlić wszystkie obsługiwane lokalizacje dla usługi App Service w systemie Linux w warstwie **Standardowa**, uruchom polecenie [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Zasadniczo grupy zasobów i zasoby są tworzone w pobliskim regionie.

Po zakończeniu działania polecenia zostaną wyświetlone dane wyjściowe JSON z właściwościami grupy zasobów.

## <a name="create-an-azure-app-service-plan"></a>Tworzenie planu usługi Azure App Service

W Cloud Shell Utwórz plan App Service w grupie zasobów za pomocą [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) polecenia.

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
> W tej chwili Docker Compose na platformie Azure App Services obecnie obowiązuje limit 4 000 znaków.

W terminalu usługi Cloud Shell utwórz wielokontenerową [aplikację internetową](overview.md#app-service-on-linux) w ramach planu usługi App Service `myAppServicePlan`, używając polecenia [az webapp create](/cli/azure/webapp#az-webapp-create). Nie zapomnij zastąpić _\<app_name>_ unikatową nazwą aplikacji (prawidłowe znaki to `a-z` , `0-9` i `-` ).

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

**Gratulacje**, utworzono aplikację z obsługą kontenera w Web App for Containers.

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: wielokontenerowa aplikacja WordPress](tutorial-multi-container-app.md)

> [!div class="nextstepaction"]
> [Konfigurowanie kontenera niestandardowego](configure-custom-container.md)

<!--Image references-->
[1]: media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
