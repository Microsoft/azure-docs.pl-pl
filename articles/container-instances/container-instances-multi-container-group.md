---
title: Samouczek — Wdrażanie szablonu grupy wielokontenerowej
description: W tym samouczku dowiesz się, jak wdrożyć grupę kontenerów z wieloma kontenerami w Azure Container Instances przy użyciu szablonu Azure Resource Manager z interfejsem wiersza polecenia platformy Azure.
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bc956bed8324398c2d60f4641cd0bcb821fb51c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93091365"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Samouczek: wdrażanie wielokontenerowej grupy przy użyciu szablonu Menedżer zasobów

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances obsługuje wdrażanie wielu kontenerów na jednym hoście przy użyciu [grupy kontenerów](container-instances-container-groups.md). Grupa kontenerów jest przydatna podczas tworzenia przyczepki aplikacji do rejestrowania, monitorowania lub innej konfiguracji, w której usługa wymaga drugiego dołączonego procesu.

W tym samouczku przedstawiono procedurę uruchamiania prostej konfiguracji dwukontenerowej przyczepki przez wdrożenie szablonu Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie wielokontenerowego szablonu grupy
> * Wdróż grupę kontenerów
> * Wyświetlanie dzienników kontenerów

Szablon Menedżer zasobów można łatwo dostosować do scenariuszy, gdy konieczne jest wdrożenie dodatkowych zasobów usługi platformy Azure (na przykład udziału Azure Files lub sieci wirtualnej) z grupą kontenerów. 

> [!NOTE]
> Grupy wielokontenerowe są obecnie ograniczone do kontenerów systemu Linux. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-template"></a>Konfigurowanie szablonu

Zacznij od skopiowania następującego kodu JSON do nowego pliku o nazwie `azuredeploy.json` . W Azure Cloud Shell można użyć Visual Studio Code, aby utworzyć plik w katalogu roboczym:

```
code azuredeploy.json
```

Ten szablon Menedżer zasobów definiuje grupę kontenerów z dwoma kontenerami, publicznym adresem IP i dwoma udostępnionymi portami. Pierwszy kontener w grupie uruchamia internetową aplikację sieci Web. Drugi kontener, Przyczepka, wysyła żądanie HTTP do głównej aplikacji sieci Web za pośrednictwem sieci lokalnej grupy.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": 80
            },
            {
                "protocol": "tcp",
                "port": 8080
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Aby użyć rejestru prywatnego kontenera obrazu, Dodaj obiekt do dokumentu JSON w następującym formacie. Aby zapoznać się z przykładową implementacją tej konfiguracji, zobacz dokumentację [referencyjną szablonu Menedżer zasobów ACI][template-reference] .

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdróż szablon za pomocą polecenia [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure.

## <a name="view-deployment-state"></a>Wyświetl stan wdrożenia

Aby wyświetlić stan wdrożenia, użyj następującego polecenia [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Jeśli chcesz wyświetlić uruchomioną aplikację, przejdź do jej adresu IP w przeglądarce. Na przykład adres IP jest `52.168.26.124` w tym przykładzie:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

Wyświetlanie danych wyjściowych kontenera za pomocą polecenia [AZ Container Logs][az-container-logs] . `--container-name`Argument określa kontener, z którego mają być ściągane dzienniki. W tym przykładzie `aci-tutorial-app` kontener jest określony.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Dane wyjściowe:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Aby wyświetlić dzienniki dla kontenera przyczepki, uruchom podobne polecenie określające `aci-tutorial-sidecar` kontener.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Dane wyjściowe:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

Jak widać, Przyczepka okresowo przesyła żądanie HTTP do głównej aplikacji sieci Web za pośrednictwem sieci lokalnej grupy, aby upewnić się, że jest uruchomiona. Tego przykładu przyczepki można rozszerzyć, aby wyzwolić alert, jeśli otrzymał kod odpowiedzi HTTP inny niż `200 OK` .

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto szablonu Azure Resource Manager do wdrożenia grupy wielokontenerowej w programie Azure Container Instances. W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie wielokontenerowego szablonu grupy
> * Wdróż grupę kontenerów
> * Wyświetlanie dzienników kontenerów

Dodatkowe przykłady szablonów można znaleźć w temacie [Azure Resource Manager templates for Azure Container Instances](container-instances-samples-rm.md).

Można również określić grupę wielokontenerową przy użyciu [pliku YAML](container-instances-multi-container-yaml.md). Ze względu na bardziej zwięzły charakter formatu YAML, wdrożenie z plikiem YAML jest dobrym rozwiązaniem, gdy wdrożenie obejmuje tylko wystąpienia kontenerów.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
