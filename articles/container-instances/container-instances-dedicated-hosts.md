---
title: Wdróż na dedykowanym hoście
description: Użyj dedykowanego hosta, aby osiągnąć prawdziwą izolację na poziomie hosta dla obciążeń Azure Container Instances
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 68b9b31cdfb55e8150b05e3efd35389320905cdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98034275"
---
# <a name="deploy-on-dedicated-hosts"></a>Wdrażanie na dedykowanych hostach

"Dedykowana" to jednostka SKU Azure Container Instances (ACI), która zapewnia odizolowane i dedykowane środowisko obliczeniowe do bezpiecznego uruchamiania kontenerów. Użycie dedykowanej jednostki SKU powoduje, że każda grupa kontenerów ma dedykowany serwer fizyczny w centrum danych platformy Azure, zapewniając pełną izolację obciążenia, która pomaga spełnić wymagania dotyczące zabezpieczeń i zgodności w organizacji. 

Dedykowana jednostka SKU jest odpowiednia dla obciążeń kontenera, które wymagają izolacji obciążenia z perspektywy serwera fizycznego.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Ze względu na niektóre bieżące ograniczenia nie wszystkie żądania zwiększenia limitu są gwarantowane.

* Domyślny limit dla każdej subskrypcji w celu użycia dedykowanej jednostki SKU wynosi 0. Jeśli chcesz używać tej jednostki SKU do wdrożeń kontenerów produkcyjnych, Utwórz [support Request platformy Azure][azure-support] w celu zwiększenia limitu.

## <a name="use-the-dedicated-sku"></a>Użyj dedykowanej jednostki SKU

> [!IMPORTANT]
> Użycie dedykowanej jednostki SKU jest dostępne tylko w najnowszej wersji interfejsu API (2019-12-01), która jest obecnie wycofywana. Określ tę wersję interfejsu API w szablonie wdrożenia.
>

Począwszy od interfejsu API w wersji 2019-12-01, istnieje `sku` Właściwość w sekcji Właściwości grupy kontenerów szablonu wdrożenia, który jest wymagany do wdrożenia ACI. Obecnie można użyć tej właściwości jako części szablonu wdrażania Azure Resource Manager ACI. Dowiedz się więcej o wdrażaniu zasobów ACI za pomocą szablonu w [samouczku: Wdróż grupę z wieloma kontenerami przy użyciu szablonu Menedżer zasobów](./container-instances-multi-container-group.md). 

`sku`Właściwość może mieć jedną z następujących wartości:
* `Standard` — wybór standardowego wdrożenia ACI, który nadal gwarantuje zabezpieczenia na poziomie funkcji hypervisor 
* `Dedicated` — służy do izolacji poziomu obciążenia z dedykowanymi hostami fizycznymi dla grupy kontenerów.

## <a name="modify-your-json-deployment-template"></a>Modyfikowanie szablonu wdrożenia JSON

W szablonie wdrożenia zmodyfikuj lub Dodaj następujące właściwości:
* W obszarze `resources` Ustaw `apiVersion` wartość `2019-12-01` .
* W obszarze właściwości grupy kontenerów Dodaj `sku` Właściwość o wartości `Dedicated` .

Oto przykładowy fragment dla sekcji Resources szablonu wdrożenia grupy kontenerów, który używa dedykowanej jednostki SKU:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

Poniżej znajduje się kompletny szablon, który wdraża przykładową grupę kontenerów z pojedynczym wystąpieniem kontenera:

```json
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
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>Wdróż grupę kontenerów

Jeśli utworzono i edytowano plik szablonu wdrożenia na pulpicie, można przekazać go do katalogu Cloud Shell, przeciągając plik do niego. 

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdróż szablon za pomocą polecenia [AZ Deployment Group Create][az-deployment-group-create] .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure. Pomyślne wdrożenie odbywa się na dedykowanym hoście.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
