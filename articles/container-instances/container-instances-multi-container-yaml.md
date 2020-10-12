---
title: Samouczek — wdrażanie wielokontenerowych grup — YAML
description: W tym samouczku dowiesz się, jak wdrożyć grupę kontenerów z wieloma kontenerami w Azure Container Instances przy użyciu pliku YAML z interfejsem wiersza polecenia platformy Azure.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: f6ba9152003c68192ddc0fdffa5652bc0b6daa86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88750903"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Samouczek: wdrażanie wielokontenerowej grupy przy użyciu pliku YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances obsługuje wdrażanie wielu kontenerów na jednym hoście przy użyciu [grupy kontenerów](container-instances-container-groups.md). Grupa kontenerów jest przydatna podczas tworzenia przyczepki aplikacji do rejestrowania, monitorowania lub innej konfiguracji, w której usługa wymaga drugiego dołączonego procesu.

W tym samouczku przedstawiono procedurę uruchamiania prostej konfiguracji dwukontenerowej przyczepki przez wdrożenie [pliku YAML](container-instances-reference-yaml.md) przy użyciu interfejsu wiersza polecenia platformy Azure. Plik YAML zawiera zwięzły format służący do określania ustawień wystąpienia. Omawiane kwestie:

> [!div class="checklist"]
> * Konfiguruj plik YAML
> * Wdróż grupę kontenerów
> * Wyświetlanie dzienników kontenerów

> [!NOTE]
> Grupy wielokontenerowe są obecnie ograniczone do kontenerów systemu Linux.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Konfiguruj plik YAML

Aby wdrożyć grupę z wiele kontenerów za pomocą polecenia [AZ Container Create][az-container-create] w interfejsie użytkownika platformy Azure, należy określić konfigurację grupy kontenerów w pliku YAML. Następnie Przekaż plik YAML jako parametr do polecenia.

Zacznij od skopiowania następującego YAML do nowego pliku o nazwie **Deploy-ACI. YAML**. W Azure Cloud Shell można użyć Visual Studio Code, aby utworzyć plik w katalogu roboczym:

```
code deploy-aci.yaml
```

Ten plik YAML definiuje grupę kontenerów o nazwie "model kontenera" z dwoma kontenerami, publicznym adresem IP i dwoma uwidocznionymi portami. Kontenery są wdrażane z publicznych obrazów firmy Microsoft. Pierwszy kontener w grupie uruchamia internetową aplikację sieci Web. Drugi kontener, Przyczepka, okresowo wysyła żądania HTTP do aplikacji sieci Web uruchomionej w pierwszym kontenerze za pośrednictwem sieci lokalnej grupy kontenerów.

```YAML
apiVersion: 2019-12-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
type: Microsoft.ContainerInstance/containerGroups
```

Aby użyć rejestru prywatnego obrazu kontenera, Dodaj `imageRegistryCredentials` Właściwość do grupy kontenerów z wartościami zmodyfikowanymi dla danego środowiska:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Wdróż grupę kontenerów

Utwórz grupę zasobów za pomocą polecenia [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdróż grupę kontenerów za pomocą polecenia [AZ Container Create][az-container-create] , przekazując plik YAML jako argument:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure.

## <a name="view-deployment-state"></a>Wyświetl stan wdrożenia

Aby wyświetlić stan wdrożenia, użyj następującego polecenia [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Jeśli chcesz wyświetlić uruchomioną aplikację, przejdź do jej adresu IP w przeglądarce. Na przykład adres IP jest `52.168.26.124` w tym przykładzie:

```bash
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

W tym samouczku użyto pliku YAML do wdrożenia wielokontenerowej grupy w Azure Container Instances. W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie pliku YAML dla grupy wielokontenerowej
> * Wdróż grupę kontenerów
> * Wyświetlanie dzienników kontenerów

Można również określić grupę wielokontenerową przy użyciu [szablonu Menedżer zasobów](container-instances-multi-container-group.md). Szablon Menedżer zasobów można łatwo dostosować do scenariuszy, gdy konieczne jest wdrożenie dodatkowych zasobów usługi platformy Azure z grupą kontenerów.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
