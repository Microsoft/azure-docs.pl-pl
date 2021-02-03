---
title: Włącz Azure Monitor na urządzeniu z systemem Azure Stack Edge
description: Zawiera opis sposobu włączania Azure Monitor na urządzeniu z systemem Azure Stack Edge dla urządzeń z procesorem GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: 171b4dbfb2a5852e270c483a28cad31f97dcb42b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493916"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Włączanie Azure Monitor na urządzeniu z systemem Azure Stack Edge

Kontenery monitorowania na urządzeniu z procesorem GPU w Azure Stack Edge są krytyczne, szczególnie w przypadku uruchamiania wielu aplikacji obliczeniowych. Azure Monitor pozwala zbierać dzienniki kontenerów oraz metryki pamięci i procesora z klastra Kubernetes uruchomionego na urządzeniu.

W tym artykule opisano kroki wymagane do włączenia Azure Monitor na urządzeniu i zbierania dzienników kontenerów w obszarze roboczym Log Analytics. Magazyn metryk Azure Monitor nie jest obecnie obsługiwany w przypadku urządzenia z procesorem GPU Azure Stack Edge. 


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

- Urządzenie Azure Stack EDGE Pro. Upewnij się, że urządzenie zostało aktywowane zgodnie z krokami opisanymi w [samouczku: Aktywuj urządzenie](azure-stack-edge-gpu-deploy-activate.md).
- Ukończono Konfigurowanie kroków **obliczeniowych** zgodnie z [samouczkiem: Konfigurowanie obliczeń na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-configure-compute.md) na urządzeniu. Urządzenie powinno mieć skojarzony zasób IoT Hub, urządzenie IoT i urządzenie IoT Edge.


## <a name="create-log-analytics-workspace"></a>Tworzenie obszaru roboczego usługi Log Analytics

Wykonaj poniższe czynności, aby utworzyć obszar roboczy usługi log Analytics. Obszar roboczy usługi log Analytics to logiczna jednostka magazynowa, w której zbierane są dane dziennika i są przechowywane.

1. W Azure Portal wybierz pozycję **+ Utwórz zasób** i Wyszukaj **log Analytics obszar roboczy** , a następnie wybierz pozycję **Utwórz**. 
1. W **obszarze roboczym tworzenie log Analytics** skonfiguruj następujące ustawienia. Zaakceptuj resztę jako domyślną.

    1. Na karcie **podstawowe** Podaj subskrypcję, grupę zasobów, nazwę i region obszaru roboczego. 

        ![Karta podstawy Log Analytics obszaru roboczego](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. Na karcie **warstwa cenowa** Zaakceptuj domyślny **Plan płatność zgodnie z rzeczywistym** użyciem.

        ![Karta cennika dla Log Analytics obszaru roboczego](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. Na karcie **Recenzja i tworzenie** Przejrzyj informacje dotyczące obszaru roboczego i wybierz pozycję **Utwórz**.

        ![Przegląd + tworzenie dla Log Analytics obszaru roboczego](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

Aby uzyskać więcej informacji, zapoznaj się ze szczegółowymi krokami w temacie [tworzenie log Analytics obszaru roboczego za pośrednictwem Azure Portal](../azure-monitor/learn/quick-create-workspace.md).



## <a name="enable-container-insights"></a>Włączanie usługi Container Insights

Wykonaj następujące kroki, aby włączyć usługi Container Insights w obszarze roboczym. 

1. Postępuj zgodnie ze szczegółowymi krokami, [Aby dodać rozwiązanie Azure monitor Containers](../azure-monitor/insights/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution). Użyj następującego pliku szablonu `containerSolution.json` :

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. Pobierz identyfikator zasobu i lokalizację. Przejdź do witryny `Your Log Analytics workspace > General > Properties`. Skopiuj następujące informacje:

    - **Identyfikator zasobu**, który jest w pełni KWALIFIKOWANYm identyfikatorem zasobów platformy Azure w obszarze roboczym usługi Azure log Analytics. 
    - **Lokalizacja**, która jest regionem świadczenia usługi Azure.

    ![Właściwości obszaru roboczego Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. Użyj poniższego pliku parametrów `containerSolutionParams.json` . Zastąp ciąg `workspaceResourceId` identyfikatorem zasobu i `workspaceRegion` lokalizacją skopiowaną w poprzednim kroku.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    Oto przykładowe dane wyjściowe obszaru roboczego Log Analytics z włączonym usługą Container Insights:
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>Konfigurowanie Azure Monitor na urządzeniu

1. Przejdź do nowo utworzonego zasobu Log Analytics i skopiuj **Identyfikator obszaru roboczego** i **klucz podstawowy** (klucz obszaru roboczego).

    ![Zarządzanie agentami w obszarze roboczym Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    Zapisz te informacje, ponieważ będą używane w późniejszym kroku.

1. [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 
    
1. Użyj identyfikatora i klucza obszaru roboczego usługi log Analytics za pomocą następującego polecenia cmdlet:

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. Po włączeniu Azure Monitor należy wyświetlić dzienniki w obszarze roboczym Log Analytics. Aby wyświetlić stan klastra Kubernetes wdrożonego na urządzeniu, przejdź do pozycji **Azure Monitor > insights > Containers**. Dla opcji środowisko zaznacz opcję **wszystkie**. 

    ![Metryki w obszarze roboczym Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [monitorować obciążenia Kubernetes za pośrednictwem pulpitu nawigacyjnego Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- Dowiedz się, jak [zarządzać powiadomieniami o alertach zdarzeń urządzeń](azure-stack-edge-gpu-manage-device-event-alert-notifications.md). 