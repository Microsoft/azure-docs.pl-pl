---
title: Samouczek — wdrażanie aplikacji na platformie Azure Service Fabric siatką
description: Z tego samouczka dowiesz się, jak wdrożyć aplikację w usłudze Service Fabric Mesh przy użyciu szablonu.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 3727e9a83827261bf9e8a526ffedb6d3fc644afa
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745981"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>Samouczek: wdrażanie aplikacji w usłudze Service Fabric Mesh przy użyciu szablonu

Niniejszy samouczek jest pierwszą częścią serii. Dowiesz się, jak wdrożyć aplikację usługi Azure Service Fabric Mesh przy użyciu szablonu.  Aplikacja składa się z usługi internetowej frontonu platformy ASP.NET i usługi zaplecza internetowego interfejsu API platformy ASP.NET Core, które znajdują się w usłudze Docker Hub.  Ściągniesz te dwa obrazy kontenerów z usługi Docker Hub, a następnie wypchniesz je do swojego własnego, prywatnego rejestru. Następnie utworzysz szablon usługi Azure RM dla aplikacji i wdrożysz aplikację ze swojego rejestru kontenerów do usługi Service Fabric Mesh. Gdy skończysz, będziesz mieć prostą aplikację listy zadań do wykonania działającą w usłudze Service Fabric Mesh.

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie prywatnego wystąpienia usługi Azure Container Registry
> * Wypychanie obrazu kontenera do rejestru
> * Tworzenie plików parametrów i szablonu usługi RM
> * Wdrażanie aplikacji w usłudze Service Fabric Mesh

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wdrażanie aplikacji w usłudze Service Fabric Mesh przy użyciu szablonu
> * [Skalowanie usług w aplikacji uruchomionej w usłudze Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Uaktualnianie aplikacji uruchomionej w usłudze Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Usuwanie aplikacji](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* [Zainstaluj platformę Docker](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [Zainstaluj lokalnie interfejs wiersza polecenia platformy Azure oraz interfejs wiersza polecenia usługi Service Fabric Mesh](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Obrazy kontenera skojarzone z usługami w aplikacji usługi Service Fabric Mesh muszą być przechowywane w rejestrze kontenerów.  W tym samouczku używane jest prywatne wystąpienie usługi Azure Container Registry (ACR). 

Utwórz wystąpienie usługi ACR przy użyciu poniższej procedury.  Jeśli masz już skonfigurowane wystąpienia usługi ACR, możesz pominąć ten krok i przejść dalej.

### <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

Zaloguj się do platformy Azure i ustaw aktywną subskrypcję.

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Przy użyciu poniższego polecenia utwórz grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus* .

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>Tworzenie rejestru kontenerów

Utwórz wystąpienie usługi ACR za pomocą polecenia `az acr create`. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. W poniższym przykładzie użyto nazwy *myContainerRegistry* . Jeśli zostanie wyświetlony komunikat o błędzie z informacją, że nazwa rejestru jest już używana, wybierz inną nazwę.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

Po utworzeniu rejestru zostaną wyświetlone dane wyjściowe podobne do następujących:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
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

## <a name="push-the-images-to-azure-container-registry"></a>Wypychanie obrazów do usługi Azure Container Registry

W tym samouczku używamy przykładowej aplikacji To Do List (lista zadań do wykonania).  Obrazy kontenera dla usług [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) i [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) można znaleźć w usłudze Docker Hub. Zobacz [Tworzenie aplikacji internetowej usługi Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md), aby uzyskać informacje na temat tworzenia aplikacji w programie Visual Studio. W usłudze Service Fabric Mesh można uruchamiać kontenery Docker systemu Windows i systemu Linux.  Jeśli pracujesz z kontenerami systemu Linux, wybierz pozycję **Przełącz na kontenery systemu Linux** na platformie Docker.  Jeśli pracujesz z kontenerami systemu Windows, wybierz pozycję **Przełącz na kontenery systemu Windows** na platformie Docker.

Aby wypchnąć obraz do wystąpienia usługi ACR, najpierw musisz mieć ten obraz kontenera. Jeśli nie masz jeszcze żadnych lokalnych obrazów kontenerów, użyj polecenia [docker pull](https://docs.docker.com/engine/reference/commandline/pull/), aby ściągnąć obrazy [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) i [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) z usługi Docker Hub.

Ściągnij obrazy systemu Windows:

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Zanim będzie można wypchnąć obraz do rejestru, musisz go otagować w pełni kwalifikowaną nazwą serwera logowania usługi ACR.

Uruchom następujące polecenie, aby uzyskać pełną nazwę serwera logowania wystąpienia usługi ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

Teraz dodaj tag do obrazu platformy Docker, używając polecenia [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). W poniższym przykładzie taki są dodawane do obrazów seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 i seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709. Jeśli używasz innych obrazów, wstaw ich nazwy w poniższym poleceniu.

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Zaloguj się do Azure Container Registry.

```azurecli
az acr login -n myContainerRegistry
```

Wypchnij obraz do wystąpienia usługi ACR za pomocą polecenia [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>Tworzenie listy obrazów kontenerów

Aby wyświetlić repozytoria w swoim wystąpieniu usługi ACR, uruchom następujące polecenie:

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

Poniższy przykład wyświetla listę tagów w repozytorium **azure-mesh-todo-service** .

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

Poprzednie dane wyjściowe potwierdzają obecność obrazu `azure-mesh-todo-service:1.0-nanoserver-1709` w prywatnym rejestrze kontenerów.  Zweryfikuj również obecność obrazu `azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.

## <a name="retrieve-credentials-for-the-registry"></a>Pobieranie poświadczeń dla rejestru

> [!IMPORTANT]
> Włączanie uprawnień administratora dla wystąpienia usługi ACR nie jest zalecane w scenariuszach produkcyjnych. Tutaj zostało to zrobione dla wygody. W przypadku scenariuszy produkcyjnych używaj [nazwy głównej usługi](../container-registry/container-registry-auth-service-principal.md) na potrzeby uwierzytelniania zarówno użytkownika, jak i systemu.

W celu wdrożenia wystąpienia kontenera z rejestru utworzonego przy użyciu szablonu, podczas wdrażania musisz podać poświadczenia rejestru. Najpierw włącz administratora w rejestrze za pomocą następującego polecenia:

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

Następnie pobierz nazwę serwera logowania rejestru, nazwę użytkownika i hasło przy użyciu następujących poleceń:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

Użyj zwróconych wartości nazwy serwera logowania usługi ACR, nazwy użytkownika i hasła podczas tworzenia plików parametrów i szablonu usługi RM w następnej sekcji.

## <a name="download-and-explore-the-template-and-parameters-files"></a>Pobieranie i eksplorowanie szablonu i plików parametrów

Aplikacja usługi Service Fabric Mesh jest zasobem platformy Azure, który można wdrożyć i którym można zarządzać przy użyciu szablonów usługi Azure Resource Manager (RM). Jeśli nie znasz pojęć dotyczących wdrażania Twoich rozwiązań platformy Azure i zarządzania nimi, zobacz [Omówienie usługi Azure Resource Manager](../azure-resource-manager/management/overview.md) i [Omówienie struktury i składni szablonów usługi RM](../azure-resource-manager/templates/template-syntax.md).

W tym samouczku używamy przykładowej aplikacji To Do List.  Zamiast tworzyć nowy szablon i nowe pliki parametrów, pobierz pliki [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) i [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

### <a name="parameters"></a>Parametry
Gdy masz w szablonie wartości, które planujesz zmienić po wdrożeniu aplikacji, lub chcesz mieć możliwość ich zmiany w poszczególnych wdrożeniach (jeśli planujesz ponownie użyć tego szablonu dla innych wdrożeń), najlepszym rozwiązaniem jest sparametryzowanie wartości. Najlepiej zrobić to, tworząc sekcję „parameters” (parametry) w górnej części szablonu wdrożenia, gdzie można określić nazwy i właściwości parametrów, które następnie są przywoływane w dalszej części szablonu wdrożenia. Każda definicja parametru zawiera sekcję *type* (typ), *defaultValue* (wartość_domyślna) oraz opcjonalną sekcję *metadata* (metadane) wraz z *opisem* .

Sekcja parametrów jest definiowana w górnej części szablonu wdrożenia bezpośrednio przed sekcją *resources* (zasoby):

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

W [szablonie wdrożenia mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) deklarowane są następujące parametry: location, registryPassword, registryUserName, registryServer, frontEndImage, serviceImage, frontEndCpu, serviceCpu, frontEndMemory, serviceMemory, frontEndReplicaCount, serviceReplicaCount.  Opis każdego parametru można znaleźć w pliku szablonu wdrożenia.

Te parametry są używane w [pliku parametrów mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).  Użycie oddzielnego pliku parametrów daje możliwość zmiany wartości parametrów dla poszczególnych wdrożeń bez aktualizowania samego szablonu wdrożenia.

### <a name="overview-of-the-application-and-services"></a>Omówienie usług i aplikacji

Usługi są określone w szablonie jako właściwości zasobu aplikacji.  Aplikacje są wdrażane w sieci prywatnej, która została zadeklarowana jako zasób w szablonie.  Usługi mogą używać woluminów, aby utrwalać dane, które zostały zadeklarowane jako zasoby w szablonie.  Dla każdej usługi określa się typ systemu operacyjnego, pakiety kodu, liczbę replik i sieć jako właściwości usługi.  Dla każdego pakietu kodu należy określić obraz kontenera, punkty końcowe, pamięć i zasoby procesora CPU oraz poświadczenia repozytorium obrazów. Na wysokim poziomie szablon dla aplikacji usługi Service Fabric Mesh z wieloma usługami wygląda następująco:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 80
                    }
                  ],
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

Zapoznaj się plikiem [szablonu wdrożenia mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json), aby uzyskać szczegółowe informacje na temat aplikacji To Do List.

## <a name="deploy-the-application-to-service-fabric-mesh"></a>Wdrażanie aplikacji w usłudze Service Fabric Mesh
Utwórz aplikację i powiązane zasoby przy użyciu następującego polecenia i podaj poświadczenia z poprzedniego kroku [Pobieranie poświadczeń dla rejestru](#retrieve-credentials-for-the-registry).

W pliku parametrów zaktualizuj następujące wartości parametrów:

|Parametr|Wartość|
|---|---|
|location|Region, w którym ma zostać wdrożona aplikacja.  Na przykład „eastus”.|
|registryPassword|Hasło uzyskane wcześniej w kroku [Pobieranie poświadczeń dla rejestru](#retrieve-credentials-for-the-registry). Ten parametr w szablonie jest bezpiecznym ciągiem i nie będzie wyświetlany w stanie wdrożenia ani w poleceniach `az mesh service show`.|
|registryUserName|Nazwa użytkownika uzyskana w kroku [Pobieranie poświadczeń dla rejestru](#retrieve-credentials-for-the-registry).|
|registryServer|Nazwa serwera rejestru uzyskana w kroku [Pobieranie poświadczeń dla rejestru](#retrieve-credentials-for-the-registry).|
|frontEndImage|Obraz kontenera dla usługi frontonu.  Na przykład `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.|
|serviceImage|Obraz kontenera dla usługi zaplecza.  Na przykład `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709`.|

Aby wdrożyć aplikację, uruchom następujące polecenie:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

To polecenie powoduje wygenerowanie fragmentu kodu JSON, który pokazano poniżej. W sekcji ```outputs``` danych wyjściowych JSON skopiuj właściwość ```publicIPAddress```.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Te dane pochodzą z sekcji ```outputs``` szablonu ARM. Jak pokazano poniżej, ta sekcja przywołuje zasób bramy w celu pobrania publicznego adresu IP. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Otwieranie aplikacji

Po pomyślnym wdrożeniu aplikacji uzyskaj publiczny adres IP punktu końcowego usługi. Polecenie wdrożenia zwraca publiczny adres IP punktu końcowego usługi. Opcjonalnie możesz także wysłać zapytanie względem zasobu sieciowego, aby znaleźć publiczny adres IP punktu końcowego usługi. Nazwa zasobu sieciowego dla tej aplikacji to `todolistappNetwork`. Pobierz informacje o niej przy użyciu następującego polecenia. 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

Przejdź do tego adresu IP w przeglądarce internetowej.

## <a name="check-application-status"></a>Sprawdzanie stanu aplikacji

Stan aplikacji możesz sprawdzić przy użyciu polecenia app show. Nazwa aplikacji dla wdrożonej aplikacji brzmi „todolistapp”, więc pobierz jej szczegóły.

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

Sprawdź dzienniki wdrożonej aplikacji przy użyciu polecenia `az mesh code-package-log get`:
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie prywatnego rejestru kontenerów
> * Wypychanie obrazu kontenera do rejestru
> * Tworzenie plików parametrów i szablonu
> * Wdrażanie aplikacji w usłudze Service Fabric Mesh

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Skalowanie aplikacji uruchomionej w usłudze Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
