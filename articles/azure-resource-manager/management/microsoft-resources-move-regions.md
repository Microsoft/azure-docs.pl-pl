---
title: Przenoszenie regionów dla zasobów w witrynie Microsoft.Resources
description: Pokazuje, jak przenieść zasoby, które znajdują się w przestrzeni nazw Microsoft.Resources, do nowych regionów.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 898e5efef22f76dc07395fcfcad413ef4582dafd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725876"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Przenoszenie zasobów Microsoft.Resources do nowego regionu

Może być konieczne przeniesienie istniejącego zasobu do nowego regionu. W tym artykule pokazano, jak przenieść dwa typy zasobów — templateSpecs i deploymentScripts — które znajdują się w przestrzeni nazw Microsoft.Resources.

## <a name="move-template-specs-to-new-region"></a>Przenoszenie specyfikacji szablonu do nowego regionu

Jeśli masz specyfikację [szablonu w](../templates/template-specs.md) jednym regionie i chcesz przenieść ją do nowego regionu, możesz wyeksportować specyfikację szablonu i ponownie ją wduszyć.

1. Użyj polecenia , aby wyeksportować istniejącą specyfikację szablonu. W przypadku wartości parametrów podaj wartości zgodne ze specyfikacją szablonu, którą chcesz wyeksportować.

   Na Azure PowerShell użyj:

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. Użyj specyfikacji wyeksportowanego szablonu, aby utworzyć nową specyfikację szablonu. W poniższych przykładach `westus` przedstawiono nowy region, ale możesz podać region, którego potrzebujesz.

   Na Azure PowerShell użyj:

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>Przenoszenie skryptów wdrażania do nowego regionu

1. Wybierz grupę zasobów zawierającą skrypt wdrażania, który chcesz przenieść do nowego regionu.

1. [Wyeksportuj szablon](../templates/export-template-portal.md). Podczas eksportowania wybierz skrypt wdrażania i inne wymagane zasoby.

1. W wyeksportowanych szablonach usuń następujące właściwości:

   * tenantId
   * principalId
   * clientId

1. Wyeksportowany szablon ma wykodowany na stałą wartość regionu skryptu wdrożenia.

   ```json
   "location": "westus2",
   ```

   Zmień szablon, aby zezwolić na parametr ustawiania lokalizacji. Aby uzyskać więcej informacji, zobacz [Ustawianie lokalizacji zasobów w szablonie usługi ARM](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Wd wdrażaj wyeksportowany szablon](../templates/deploy-powershell.md) i określ nowy region dla skryptu wdrażania.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przenoszeniu zasobów do nowej grupy zasobów lub subskrypcji, zobacz Przenoszenie zasobów do nowej grupy [zasobów lub subskrypcji.](move-resource-group-and-subscription.md)
* Aby dowiedzieć się więcej o przenoszeniu zasobów do nowego regionu, zobacz [Przenoszenie zasobów między regionami.](move-resources-overview.md#move-resources-across-regions)
