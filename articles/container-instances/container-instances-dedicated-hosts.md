---
title: Wdrażanie na dedykowanym hoście
description: Użyj dedykowanego hosta, aby osiągnąć prawdziwą izolację na poziomie hosta dla obciążeń wystąpień kontenerów platformy Azure
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: a614d6b5d0cf5c6c1df5ffcb90e56960d6b8a2a9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025037"
---
# <a name="deploy-on-dedicated-hosts"></a>Wdrażanie na dedykowanych hostach

"Dedykowane" to sku wystąpienia kontenerów platformy Azure (ACI), które zapewnia izolowane i dedykowane środowisko obliczeniowe dla bezpiecznie uruchomionych kontenerów. Przy użyciu dedykowanych wyników sku w każdej grupie kontenerów posiadających dedykowany serwer fizyczny w centrum danych platformy Azure, zapewniając pełną izolację obciążenia, aby pomóc spełnić wymagania dotyczące zabezpieczeń i zgodności organizacji. 

Dedykowane sku jest odpowiedni dla obciążeń kontenera, które wymagają izolacji obciążenia z perspektywy serwera fizycznego.

## <a name="prerequisites"></a>Wymagania wstępne

* Domyślny limit dla każdej subskrypcji do korzystania z dedykowanego sku jest 0. Jeśli chcesz użyć tego sku dla wdrożeń kontenera produkcyjnego, utwórz [żądanie pomocy technicznej platformy Azure,][azure-support] aby zwiększyć limit.

## <a name="use-the-dedicated-sku"></a>Użyj dedykowanego sku

> [!IMPORTANT]
> Korzystanie z dedykowanego sku jest dostępne tylko w najnowszej wersji interfejsu API (2019-12-01), która jest obecnie wprowadzana. Określ tę wersję interfejsu API w szablonie wdrożenia.
>

Począwszy od wersji interfejsu API 2019-12-01, istnieje `sku` właściwość w sekcji właściwości grupy kontenerów szablonu wdrożenia, która jest wymagana dla wdrożenia usługi ACI. Obecnie można użyć tej właściwości jako część szablonu wdrażania usługi Azure Resource Manager dla usługi ACI. Dowiedz się więcej o wdrażaniu zasobów usługi ACI za pomocą szablonu w [samouczku: Wdrażanie grupy wielu kontenerów przy użyciu szablonu Menedżera zasobów](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Właściwość `sku` może mieć jedną z następujących wartości:
* `Standard`- standardowy wybór wdrożenia ACI, który nadal gwarantuje bezpieczeństwo na poziomie hipernadzorcy 
* `Dedicated`- służy do izolacji na poziomie obciążenia z dedykowanymi hostami fizycznymi dla grupy kontenerów

## <a name="modify-your-json-deployment-template"></a>Modyfikowanie szablonu wdrożenia JSON

W szablonie wdrożenia zmodyfikuj lub dodaj następujące właściwości:
* W `resources`obszarze `apiVersion` `2019-12-01`, ustawiono na .
* W obszarze właściwości grupy kontenerów `sku` dodaj `Dedicated`właściwość o wartości .

Oto przykładowy fragment kodu dla sekcji zasobów szablonu wdrażania grupy kontenerów, który używa dedykowanego sku:

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

## <a name="deploy-your-container-group"></a>Wdrażanie grupy kontenerów

Jeśli plik szablonu wdrożenia został utworzony i edytowany na pulpicie, można go przekazać do katalogu Cloud Shell, przeciągając do niego plik. 

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdrażanie szablonu za pomocą polecenia [tworzenie wdrażania grupy AZ.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure. Pomyślne wdrożenie odbywa się na dedykowanym hoście.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
