---
title: Dołączanie maszyny w celu Azure Automanage szablonu usługi ARM
description: Dowiedz się, jak do dołączać maszynę, aby Azure Automanage za pomocą Azure Resource Manager szablonu.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 78cf28903311c542a83c9ace4f794e1cdda9a61c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368643"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Dołączanie maszyny do funkcji Automatycznego Azure Resource Manager szablonu (ARM)


## <a name="overview"></a>Omówienie
Wykonaj poniższe kroki, aby do dołączać maszynę do rozwiązania Automanage Best Practices. Poniższy szablon usługi ARM utworzy obiekt , który jest zasobem platformy Azure reprezentującym maszynę, która została dowana `configurationProfileAssignment` do automatycznego zarządzania.

## <a name="prerequisites"></a>Wymagania wstępne
* Musisz utworzyć istniejące konto automatycznegomanage. Zobacz [ten dokument,](./automanage-account.md) aby uzyskać więcej informacji na temat konta automatycznego i sposobu jego tworzenia.
* Musisz mieć rolę **Współautor w** grupie zasobów zawierającej maszyny, które chcesz dodać do automatycznego zarządzania

## <a name="arm-template-overview"></a>Omówienie szablonu usługi ARM
Poniższy szablon usługi ARM dołącza określoną maszynę do Azure Automanage najlepszych rozwiązań. Szczegółowe informacje o szablonie usługi ARM i instrukcje dotyczące wdrażania znajdują się w poniższej sekcji dotyczącej wdrażania szablonu [usługi](#arm-template-deployment)ARM.
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>Wdrażanie szablonu usługi ARM
Powyższy szablon usługi ARM utworzy przypisanie profilu konfiguracji dla określonej maszyny przy użyciu określonego konta automatycznego zarządzania. Jeśli nie utworzono konta automatycznego, dowiedz się więcej z tego [doc.](./automanage-account.md)

Wartość `configurationProfileAssignment` może być jedną z następujących wartości:
* "Produkcja"
* "DevTest"

Wykonaj następujące kroki, aby wdrożyć szablon usługi ARM:
1. Zapisz poniższy szablon usługi ARM jako `azuredeploy.json`
1. Uruchamianie wdrożenia szablonu usługi ARM za pomocą `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`
1. Po wyświetleniu monitu podaj wartości parametrów machineName, automanageAccountName i configurationProfileAssignment
1. Gotowe!

Podobnie jak w przypadku każdego szablonu usługi ARM, podczas wdrażania można wydzielić parametry do oddzielnego pliku i użyć ich `azuredeploy.parameters.json` jako argumentu.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat automatycznegomanage dla [systemów Linux i](./automanage-linux.md) [Windows](./automanage-windows-server.md)