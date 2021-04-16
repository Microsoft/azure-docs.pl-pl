---
title: Określanie grupy zasobów dla maszyn wirtualnych w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak określić grupę zasobów dla maszyn wirtualnych w laboratorium w Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c6f576a20fc8fada9dd515e8ba2a266761a3e586
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377492"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Określanie grupy zasobów dla maszyn wirtualnych laboratorium w Azure DevTest Labs

Jako właściciel laboratorium możesz skonfigurować maszyny wirtualne laboratorium do tworzenia w określonej grupie zasobów. Ta funkcja pomaga w następujących scenariuszach:

- Mieć mniej grup zasobów utworzonych przez laboratoria w ramach subskrypcji.
- Laboratoria mogą działać w ramach skonfigurowanego stałego zestawu grup zasobów.
- Omiń ograniczenia i zatwierdzenia wymagane do tworzenia grup zasobów w ramach subskrypcji platformy Azure.
- Konsoliduj wszystkie zasoby laboratorium w ramach jednej [](../governance/policy/overview.md) grupy zasobów, aby uprościć śledzenie tych zasobów i stosowanie zasad w celu zarządzania zasobami na poziomie grupy zasobów.

Dzięki tej funkcji możesz użyć skryptu, aby określić nową lub istniejącą grupę zasobów w ramach subskrypcji platformy Azure dla wszystkich maszyn wirtualnych laboratorium. Obecnie usługa Azure DevTest Labs tę funkcję za pośrednictwem interfejsu API.

> [!NOTE]
> Wszystkie limity subskrypcji mają zastosowanie podczas tworzenia laboratoriów w u usługi DevTest Labs. Laboratorium można myśleć jak o dowolnym innym zasobie w ramach subskrypcji. W przypadku grup zasobów limit wynosi [980 grup zasobów na subskrypcję](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Wykonaj następujące kroki, aby określić grupę zasobów dla wszystkich maszyn wirtualnych utworzonych w laboratorium. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **pozycję Wszystkie usługi** w menu nawigacji po lewej stronie. 
3. Wybierz z listy pozycję **DevTest Labs**.
4. Z listy laboratoriów wybierz **laboratorium**.  
5. Wybierz **pozycję Konfiguracja i zasady** w sekcji **Ustawienia** w menu po lewej stronie. 
6. Wybierz **pozycję Ustawienia laboratorium** w menu po lewej stronie. 
7. Wybierz **pozycję Wszystkie maszyny wirtualne w jednej grupie zasobów.** 
8. Wybierz istniejącą grupę zasobów z listy rozwijanej lub wybierz  pozycję Utwórz nową, wprowadź nazwę grupy zasobów i wybierz przycisk **OK.** 

    ![Wybierz grupę zasobów dla wszystkich maszyn wirtualnych laboratorium](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Korzystanie z programu PowerShell 
W poniższym przykładzie pokazano, jak za pomocą skryptu programu PowerShell utworzyć wszystkie maszyny wirtualne laboratorium w nowej grupie zasobów.

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

Wywołaj skrypt przy użyciu następującego polecenia. ResourceGroup.ps1 to plik, który zawiera poprzedni skrypt:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Korzystanie z Azure Resource Manager szablonu
Jeśli tworzysz laboratorium przy użyciu szablonu Azure Resource Manager, użyj właściwości **vmCreationResourceGroupId** w sekcji właściwości laboratorium szablonu, jak pokazano w poniższym przykładzie:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018-10-15-preview",
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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>Interfejs API do konfigurowania grupy zasobów dla maszyn wirtualnych laboratorium
W przypadku korzystania z tego interfejsu API jako właściciel laboratorium masz następujące opcje:

- Wybierz **grupę zasobów laboratorium dla** wszystkich maszyn wirtualnych.
- Wybierz **istniejącą grupę** zasobów inną niż grupa zasobów laboratorium dla wszystkich maszyn wirtualnych.
- Wprowadź **nową nazwę grupy zasobów** dla wszystkich maszyn wirtualnych.
- Kontynuuj korzystanie z istniejącego zachowania, w którym grupa zasobów jest tworzona dla każdej maszyny wirtualnej w laboratorium.
 
To ustawienie dotyczy nowych maszyn wirtualnych utworzonych w laboratorium. Nie ma to wpływu na starsze maszyny wirtualne w laboratorium, które zostały utworzone w ich własnych grupach zasobów. Środowiska utworzone w laboratorium nadal pozostają we własnych grupach zasobów.

Jak używać tego interfejsu API:
- Użyj interfejsu API **w wersji 2018-10-15-preview.**
- Jeśli określisz nową grupę zasobów, upewnij się, że masz uprawnienia do zapisu dla **grup zasobów** w subskrypcji. Jeśli nie masz uprawnień do zapisu, tworzenie nowych maszyn wirtualnych w określonej grupie zasobów nie powiedzie się.
- Podczas korzystania z interfejsu API przekaż pełny **identyfikator grupy zasobów**. Na przykład: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Upewnij się, że grupa zasobów znajduje się w tej samej subskrypcji co laboratorium. 


## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Ustawianie zasad dla laboratorium](devtest-lab-set-lab-policy.md)
- [Często zadawane pytania](devtest-lab-faq.md)
