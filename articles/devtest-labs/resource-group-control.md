---
title: Określ grupę zasobów dla maszyn wirtualnych w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak określić grupę zasobów dla maszyn wirtualnych w laboratorium w Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7b72048405d3025ca21b324b6ad3168dd0c9ac95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483367"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Określ grupę zasobów dla maszyn wirtualnych laboratorium w Azure DevTest Labs

Jako właściciel laboratorium można skonfigurować maszyny wirtualne laboratorium do utworzenia w określonej grupie zasobów. Ta funkcja pomaga w następujących scenariuszach:

- W Twojej subskrypcji masz mniejszą liczbę grup zasobów utworzonych przez laboratoria.
- Czy Twoje laboratoria działają w ramach ustalonego zestawu grup zasobów, które konfigurujesz.
- Obejście ograniczeń i zatwierdzeń wymaganych do tworzenia grup zasobów w ramach subskrypcji platformy Azure.
- Konsoliduj wszystkie zasoby laboratorium w ramach jednej grupy zasobów, aby uprościć śledzenie tych zasobów i stosowanie [zasad](../governance/policy/overview.md) do zarządzania zasobami na poziomie grupy zasobów.

Za pomocą tej funkcji można określić nową lub istniejącą grupę zasobów w ramach subskrypcji platformy Azure dla wszystkich maszyn wirtualnych laboratorium. Obecnie Azure DevTest Labs obsługuje tę funkcję za poorednictwem interfejsu API.

> [!NOTE]
> Wszystkie limity subskrypcji są stosowane podczas tworzenia laboratoriów w DevTest Labs. Pomyśl o laboratorium jako innego zasobu w ramach subskrypcji. W przypadku grup zasobów limit dotyczy [980 grup zasobów na subskrypcję](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Wykonaj następujące kroki, aby określić grupę zasobów dla wszystkich maszyn wirtualnych utworzonych w laboratorium. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie usługi** w menu nawigacji po lewej stronie. 
3. Wybierz z listy pozycję **DevTest Labs**.
4. Z listy laboratoriów wybierz **laboratorium**.  
5. Wybierz pozycję **Konfiguracja i zasady** w sekcji **Ustawienia** w menu po lewej stronie. 
6. Wybierz pozycję **Ustawienia laboratorium** w menu po lewej stronie. 
7. Wybierz **wszystkie maszyny wirtualne w jednej grupie zasobów**. 
8. Wybierz istniejącą grupę zasobów z listy rozwijanej (lub) wybierz pozycję **Utwórz nową**, wprowadź **nazwę** grupy zasobów, a następnie wybierz **przycisk OK**. 

    ![Wybierz grupę zasobów dla wszystkich maszyn wirtualnych laboratorium](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Korzystanie z programu PowerShell 
Poniższy przykład pokazuje, jak używać skryptu programu PowerShell do tworzenia wszystkich maszyn wirtualnych laboratorium w nowej grupie zasobów.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Wywołaj skrypt przy użyciu następującego polecenia. ResourceGroup.ps1 to plik zawierający poprzedni skrypt:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Używanie szablonu Azure Resource Manager
Jeśli tworzysz laboratorium przy użyciu szablonu Azure Resource Manager, użyj właściwości **vmCreationResourceGroupId** w sekcji Właściwości laboratorium szablonu, jak pokazano w następującym przykładzie:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>Interfejs API służący do konfigurowania grupy zasobów dla maszyn wirtualnych laboratorium
W przypadku korzystania z tego interfejsu API dostępne są następujące opcje jako właściciel laboratorium:

- Wybierz **grupę zasobów laboratorium** dla wszystkich maszyn wirtualnych.
- Wybierz **istniejącą grupę zasobów** inną niż grupa zasobów laboratorium dla wszystkich maszyn wirtualnych.
- Wprowadź **nową nazwę grupy zasobów** dla wszystkich maszyn wirtualnych.
- Kontynuuj korzystanie z istniejących zachowań, w których Grupa zasobów jest tworzona dla każdej maszyny wirtualnej w laboratorium.
 
To ustawienie dotyczy nowych maszyn wirtualnych utworzonych w laboratorium. Starsze maszyny wirtualne w laboratorium, które zostały utworzone w ich własnych grupach zasobów, pozostaną bez zmian. Środowiska utworzone w laboratorium nadal pozostają w swoich własnych grupach zasobów.

Jak używać tego interfejsu API:
- Użyj interfejsu API w wersji **2018_10_15_preview**.
- Jeśli określisz nową grupę zasobów, upewnij się, że masz **uprawnienia do zapisu w grupach zasobów** w ramach subskrypcji. Jeśli nie masz uprawnień do zapisu, tworzenie nowych maszyn wirtualnych w określonej grupie zasobów zakończy się niepowodzeniem.
- Podczas korzystania z interfejsu API należy przekazać **pełny identyfikator grupy zasobów**. Przykład: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Upewnij się, że grupa zasobów znajduje się w tej samej subskrypcji co laboratorium. 


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Ustawianie zasad dla laboratorium](devtest-lab-set-lab-policy.md)
- [Często zadawane pytania](devtest-lab-faq.md)
