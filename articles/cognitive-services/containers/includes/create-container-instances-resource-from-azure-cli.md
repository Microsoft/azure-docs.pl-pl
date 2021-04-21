---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób wystąpienia kontenera platformy Azure z interfejsu wiersza polecenia platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 32b00e031e3cf865093c267084117a8704b6e272
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799798"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Tworzenie zasobu wystąpienia kontenera platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure

Poniższy przykład YAML definiuje zasób wystąpienia kontenera platformy Azure. Skopiuj i wklej zawartość do nowego pliku o nazwie i zastąp komentarze `my-aci.yaml` własnymi wartościami. Prawidłowy format YAML [można][template-format] znaleźć w formacie szablonu. Aby uzyskać dostępne nazwy obrazów i odpowiadające im [repozytoria,][repositories-and-images] zapoznaj się z repozytoriami kontenerów i obrazami. Aby uzyskać więcej informacji na temat odwołania YAML dla wystąpień kontenerów, zobacz [yaml reference: Azure Container Instances][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
properties:
  imageRegistryCredentials: # This is only required if you are pulling a non-public image that requires authentication to access. For example Text Analytics for health.
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Nie wszystkie lokalizacje mają taką samą dostępność procesora CPU i pamięci. Zapoznaj się z [tabelą lokalizacji i zasobów,][location-to-resource] aby uzyskać listę dostępnych zasobów dla kontenerów na lokalizację i system operacyjny.

Będziemy polegać na pliku YAML utworzonym dla [`az container create`][azure-container-create] polecenia . W interfejsie wiersza polecenia platformy Azure wykonaj `az container create` polecenie , zastępując polecenie `<resource-group>` własnym. Ponadto w przypadku zabezpieczania wartości w ramach wdrożenia YAML zapoznaj się z [bezpiecznymi wartościami][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Dane wyjściowe polecenia są prawidłowe. Po pewnym czasie dane wyjściowe zmienią się na ciąg `Running...` JSON reprezentujący nowo utworzony zasób ACI. Obraz kontenera jest prawdopodobnie niedostępny przez jakiś czas, ale zasób jest teraz wdrożony.

> [!TIP]
> Zwróć szczególną uwagę na lokalizacje ofert usługi Azure Cognitive Service w publicznej wersji zapoznawczej, ponieważ należy odpowiednio dostosować yaml, aby dopasować go do lokalizacji.

[azure-container-create]: /cli/azure/container#az_container_create
[template-format]: /azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../container-image-tags.md
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
