---
title: Wdrażanie na dedykowanym hoście
description: Użyj dedykowanego hosta, aby uzyskać prawdziwą izolację na poziomie hosta dla Azure Container Instances obciążeń
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 72ad05eea9232e7a0d6ac24d1d0d22a971a7f6a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790837"
---
# <a name="deploy-on-dedicated-hosts"></a>Wdrażanie na dedykowanych hostach

"Dedykowane" to jednostki SKU Azure Container Instances (ACI), które zapewniają izolowane i dedykowane środowisko obliczeniowe na bezpieczne uruchamianie kontenerów. Użycie dedykowanej wersji SKU powoduje, że każda grupa kontenerów ma dedykowany serwer fizyczny w centrum danych platformy Azure, zapewniając pełną izolację obciążenia w celu spełnienia wymagań organizacji dotyczących zabezpieczeń i zgodności. 

Dedykowana sku jest odpowiednia dla obciążeń kontenerów, które wymagają izolacji obciążeń z perspektywy serwera fizycznego.

## <a name="prerequisites"></a>Wymagania wstępne

> [!NOTE]
> Ze względu na niektóre bieżące ograniczenia nie wszystkie żądania zwiększenia limitu muszą zostać zatwierdzone.

* Domyślny limit dla dowolnej subskrypcji do korzystania z dedykowanej sku wynosi 0. Jeśli chcesz użyć tej sku dla wdrożeń kontenerów [][azure-support] produkcyjnych, utwórz żądanie pomoc techniczna platformy Azure, aby zwiększyć limit.

## <a name="use-the-dedicated-sku"></a>Korzystanie z dedykowanej sku

> [!IMPORTANT]
> Używanie dedykowanej wersji SKU jest dostępne tylko w najnowszej wersji interfejsu API (2019-12-01), która jest obecnie wprowadzana. Określ tę wersję interfejsu API w szablonie wdrożenia.
>

Począwszy od interfejsu API w wersji 2019-12-01, w sekcji właściwości grupy kontenerów szablonu wdrożenia znajduje się właściwość, która jest wymagana dla wdrożenia `sku` usługi ACI. Obecnie można użyć tej właściwości jako części szablonu wdrożenia Azure Resource Manager usługi ACI. Dowiedz się więcej na temat wdrażania zasobów usługi ACI przy użyciu szablonu z samouczka: wdrażanie grupy z wieloma kontenerami przy użyciu [Resource Manager szablonu.](./container-instances-multi-container-group.md) 

Właściwość `sku` może mieć jedną z następujących wartości:
* `Standard` — standardowy wybór wdrożenia usługi ACI, który nadal gwarantuje zabezpieczenia na poziomie funkcji hypervisor 
* `Dedicated` — służy do izolacji na poziomie obciążenia z dedykowanymi hostami fizycznymi dla grupy kontenerów

## <a name="modify-your-json-deployment-template"></a>Modyfikowanie szablonu wdrożenia JSON

W szablonie wdrożenia zmodyfikuj lub dodaj następujące właściwości:
* W `resources` obszarze ustaw wartość `apiVersion` `2019-12-01` .
* W obszarze właściwości grupy kontenerów dodaj właściwość `sku` z wartością `Dedicated` .

Oto przykładowy fragment kodu dla sekcji resources szablonu wdrożenia grupy kontenerów, który używa dedykowanej sku:

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

Poniżej przedstawiono kompletny szablon, który wdraża przykładową grupę kontenerów z uruchomionym pojedynczym wystąpieniem kontenera:

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

## <a name="deploy-your-container-group"></a>Wdrażanie grupy kontenerów

Jeśli plik szablonu wdrożenia został utworzony i edytowany na pulpicie, możesz przekazać go do katalogu Cloud Shell, przeciągając do niego plik. 

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wd wdrażaj szablon za pomocą [polecenia az deployment group create.][az-deployment-group-create]

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure. Pomyślne wdrożenie odbywa się na dedykowanym hoście.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
