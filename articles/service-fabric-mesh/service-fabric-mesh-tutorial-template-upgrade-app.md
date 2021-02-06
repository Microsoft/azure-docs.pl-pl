---
title: Samouczek — Uaktualnianie aplikacji działającej na platformie Azure Service Fabric siatką
description: W tym samouczku dowiesz się, jak uaktualnić aplikację usługi Service Fabric uruchomioną w usłudze Service Fabric Mesh.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 8a71e854f03bee75b757e0a0aa02e7aa2c24469b
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626564"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>Samouczek: uaktualnianie aplikacji usługi Service Fabric uruchomionej w usłudze Service Fabric Mesh

> [!IMPORTANT]
> Wersja zapoznawcza siatki Service Fabric platformy Azure została wycofana. Nowe wdrożenia nie będą już dozwolone za pomocą interfejsu API Service Fabric siatki. Obsługa istniejących wdrożeń będzie kontynuowana do 28 kwietnia 2021.
> 
> Aby uzyskać szczegółowe informacje, zobacz wycofywanie w [wersji zapoznawczej usługi Azure Service Fabric siatki](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ten samouczek jest trzecią częścią serii. Dowiesz się, jak uaktualnić aplikację usługi Service Fabric, która została [wcześniej wdrożona do usługi Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md), zwiększając przydzielone zasoby procesora.  Po zakończeniu usługa frontonu internetowego będzie działać i mieć dostęp do większych zasobów procesora.

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zmienianie konfiguracji aplikacji
> * Uaktualnianie aplikacji uruchomionej w usłudze Service Fabric Mesh

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Wdrażanie aplikacji w usłudze Service Fabric Mesh przy użyciu szablonu](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Skalowanie aplikacji uruchomionej w usłudze Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * Uaktualnianie aplikacji uruchomionej w usłudze Service Fabric Mesh
> * [Usuwanie aplikacji](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz [utworzyć bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Otwórz usługę [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) lub [zainstaluj lokalnie interfejs wiersza polecenia platformy Azure oraz interfejs wiersza polecenia usługi Service Fabric Mesh](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="upgrade-application-configurations"></a>Uaktualnianie konfiguracji aplikacji

Jedną z głównych zalet wdrażania aplikacji w usłudze Service Fabric Mesh jest możliwość łatwego aktualizowania konfiguracji aplikacji.  Może to dotyczyć na przykład zasobów procesora lub pamięci dla usługi.

W tym samouczku jako przykładu użyto listy zadań do wykonania, którą [wcześniej wdrożono](service-fabric-mesh-tutorial-template-deploy-app.md) i która powinna być teraz uruchomiona. Aplikacja ma dwie usługi: WebFrontEnd i ToDoService. Każdą z usług pierwotnie wdrożono z liczbą zasobów procesora wynoszącą 0,5.  Aby wyświetlić zasoby procesora usługi WebFrontEnd, uruchom następujące polecenie:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

W szablonie wdrażania dla zasobu aplikacji każda usługa ma właściwość *cpu*, która może służyć do ustawiania żądanych zasobów procesora. Aplikacja może składać się z wielu usług, z których każda ma unikatowe ustawienie *cpu* oraz które są razem wdrażane i zarządzane. Aby zwiększyć ilość zasobów procesora usługi frontonu internetowego, zmodyfikuj wartość *cpue* w szablonie wdrożenia lub pliku parametrów.  Następnie uaktualnij aplikację.

### <a name="modify-the-deployment-template-parameters"></a>Modyfikowanie parametrów szablonu wdrożenia

Gdy masz w szablonie wartości, które planujesz zmienić po wdrożeniu aplikacji, lub chcesz mieć możliwość ich zmiany w poszczególnych wdrożeniach (jeśli planujesz ponownie użyć tego szablonu dla innych wdrożeń), najlepszym rozwiązaniem jest sparametryzowanie wartości.

Wcześniej aplikacja została wdrożona przy użyciu plików [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) i [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Otwórz lokalnie plik parametrów [mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) i ustaw wartość *frontEndCpu* na 1:

```json
      "frontEndCpu":{
        "value": "1"
      }
```

Zapisz zmiany w pliku parametrów.  

Parametr *frontEndCpu* jest deklarowany w sekcji *parameters*[szablonu wdrożenia mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

Właściwość *codePackages->resources->requests->cpu* usługi WebFrontEnd odwołuje się do parametru *frontEndCpu*:

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
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
              ...
```

### <a name="upgrade-your-application"></a>Uaktualnianie aplikacji

Gdy aplikacja jest uruchomiona, możesz uaktualnić ją przez ponowne wdrożenie szablonu i zaktualizowanego pliku parametrów:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Spowoduje to uruchomienie uaktualnienia stopniowego do aplikacji, a po kilku minutach powinno być widoczne zwiększenie liczby zasobów procesora.  Aby wyświetlić zasoby procesora usługi WebFrontEnd, uruchom następujące polecenie:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zmienianie konfiguracji aplikacji
> * Uaktualnianie aplikacji uruchomionej w usłudze Service Fabric Mesh

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Remove a Service Fabric Mesh application](service-fabric-mesh-tutorial-template-remove-app.md) (Usuwanie aplikacji usługi Service Fabric Mesh)
