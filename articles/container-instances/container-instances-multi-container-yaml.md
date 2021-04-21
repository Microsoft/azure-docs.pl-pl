---
title: Samouczek — wdrażanie grupy z wieloma kontenerami — YAML
description: Z tego samouczka dowiesz się, jak wdrożyć grupę kontenerów z wieloma kontenerami w usłudze Azure Container Instances użyciu pliku YAML z interfejsem wiersza polecenia platformy Azure.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 74269440357ee2d7ae36661618a31293346fa712
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771267"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Samouczek: wdrażanie grupy z wieloma kontenerami przy użyciu pliku YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances obsługuje wdrażanie wielu kontenerów na jednym hoście przy użyciu [grupy kontenerów.](container-instances-container-groups.md) Grupa kontenerów jest przydatna podczas tworzenia przyczepki aplikacji na potrzeby rejestrowania, monitorowania lub dowolnej innej konfiguracji, w której usługa wymaga drugiego dołączonego procesu.

W tym samouczku wykonaj kroki, aby uruchomić prostą konfigurację dwu kontenerów sidecar, wdrażając plik [YAML przy](container-instances-reference-yaml.md) użyciu interfejsu wiersza polecenia platformy Azure. Plik YAML zapewnia zwięzły format określania ustawień wystąpienia. Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie pliku YAML
> * Wdrażanie grupy kontenerów
> * Wyświetlanie dzienników kontenerów

> [!NOTE]
> Grupy z wieloma kontenerami są obecnie ograniczone do kontenerów systemu Linux.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>Konfigurowanie pliku YAML

Aby wdrożyć grupę z wieloma kontenerami za pomocą polecenia [az container create][az-container-create] w interfejsie wiersza polecenia platformy Azure, musisz określić konfigurację grupy kontenerów w pliku YAML. Następnie przekaż plik YAML jako parametr do polecenia .

Zacznij od skopiowania następującego pliku YAML do nowego pliku o nazwie **deploy-aci.yaml.** W Azure Cloud Shell możesz użyć Visual Studio Code, aby utworzyć plik w katalogu pracy:

```
code deploy-aci.yaml
```

Ten plik YAML definiuje grupę kontenerów o nazwie "myContainerGroup" z dwoma kontenerami, publicznym adresem IP i dwoma ujawnionmi portami. Kontenery są wdrażane z publicznych obrazów firmy Microsoft. Pierwszy kontener w grupie uruchamia aplikację internetową dostępną z Internetu. Drugi kontener, sidecar, okresowo wysyła żądania HTTP do aplikacji internetowej uruchomionej w pierwszym kontenerze za pośrednictwem sieci lokalnej grupy kontenerów.

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

Aby użyć prywatnego rejestru obrazów kontenerów, dodaj właściwość do grupy `imageRegistryCredentials` kontenerów z wartościami zmodyfikowanymi dla twojego środowiska:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>Wdrażanie grupy kontenerów

Utwórz grupę zasobów za pomocą [polecenia az group create:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdróż grupę kontenerów za pomocą [polecenia az container create,][az-container-create] przekazując plik YAML jako argument:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure.

## <a name="view-deployment-state"></a>Wyświetlanie stanu wdrożenia

Aby wyświetlić stan wdrożenia, użyj następującego [polecenia az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Jeśli chcesz wyświetlić uruchamianą aplikację, przejdź do jej adresu IP w przeglądarce. Na przykład adres IP znajduje się `52.168.26.124` w tych przykładowych danych wyjściowych:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

Wyświetl dane wyjściowe dziennika kontenera za pomocą [polecenia az container logs.][az-container-logs] Argument `--container-name` określa kontener, z którego mają być ściągane dzienniki. W tym przykładzie `aci-tutorial-app` określono kontener.

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

Aby wyświetlić dzienniki kontenera sidecar, uruchom podobne polecenie, określając `aci-tutorial-sidecar` kontener.

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

Jak widać, sidecar okresowo wysyła żądanie HTTP do głównej aplikacji internetowej za pośrednictwem sieci lokalnej grupy, aby upewnić się, że jest uruchomiona. Ten przykładowy element sidecar można rozszerzyć, aby wyzwolić alert, jeśli otrzymał kod odpowiedzi HTTP inny niż `200 OK` .

## <a name="next-steps"></a>Następne kroki

W tym samouczku plik YAML został użyty do wdrożenia grupy z wieloma kontenerami w Azure Container Instances. W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie pliku YAML dla grupy z wieloma kontenerami
> * Wdrażanie grupy kontenerów
> * Wyświetlanie dzienników kontenerów

Grupę wielu kontenerów można również określić przy użyciu [Resource Manager szablonu](container-instances-multi-container-group.md). Szablon Resource Manager można łatwo dostosować do scenariuszy, w których trzeba wdrożyć dodatkowe zasoby usługi platformy Azure za pomocą grupy kontenerów.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
