---
title: Przenoszenie regionów dla zasobów w firmie Microsoft. resources
description: Pokaż, jak przenieść zasoby znajdujące się w przestrzeni nazw Microsoft. resources do nowych regionów.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88951056"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Przenoszenie zasobów Microsoft. resources do nowego regionu

Może być konieczne przeniesienie istniejącego zasobu do nowego regionu. W tym artykule pokazano, jak przenieść dwa typy zasobów — templateSpecs i deploymentScripts — które znajdują się w przestrzeni nazw Microsoft. resources.

## <a name="move-template-specs-to-new-region"></a>Przenieś specyfikacje szablonu do nowego regionu

Jeśli masz [specyfikację szablonu](../templates/template-specs.md) w jednym regionie i chcesz przenieść ją do nowego regionu, możesz wyeksportować specyfikację szablonu i wdrożyć ją ponownie.

1. Użyj polecenia, aby wyeksportować istniejącą specyfikację szablonu. Dla wartości parametrów podaj wartości pasujące do specyfikacji szablonu, która ma zostać wyeksportowana.

   Aby uzyskać Azure PowerShell, użyj:

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

1. Użyj wyeksportowanej specyfikacji szablonu, aby utworzyć nową specyfikację szablonu. W poniższych przykładach pokazano `westus` dla nowego regionu, ale można podać żądany region.

   Aby uzyskać Azure PowerShell, użyj:

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

## <a name="move-deployment-scripts-to-new-region"></a>Przenoszenie skryptów wdrożenia do nowego regionu

1. Wybierz grupę zasobów zawierającą skrypt wdrożenia, który chcesz przenieść do nowego regionu.

1. [Wyeksportuj szablon](../templates/export-template-portal.md). Podczas eksportowania wybierz skrypt wdrażania i inne wymagane zasoby.

1. W wyeksportowanym szablonie Usuń następujące właściwości:

   * tenantId
   * principalId
   * clientId

1. Wyeksportowany szablon ma wartość stałe dla regionu skryptu wdrażania.

   ```json
   "location": "westus2",
   ```

   Zmień szablon w taki sposób, aby zezwalał na parametr w celu ustawienia lokalizacji. Aby uzyskać więcej informacji, zobacz [Ustawianie lokalizacji zasobów w szablonie ARM](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [Wdróż wyeksportowany szablon](../templates/deploy-powershell.md) i określ nowy region dla skryptu wdrażania.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o przenoszeniu zasobów do nowej grupy zasobów lub subskrypcji, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](move-resource-group-and-subscription.md).
* Aby dowiedzieć się więcej o przenoszeniu zasobów do nowego regionu, zobacz [przemieszczanie zasobów platformy Azure między regionami](move-region.md).
