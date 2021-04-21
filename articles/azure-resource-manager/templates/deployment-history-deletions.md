---
title: Usunięcia historii wdrażania
description: Opisuje, Azure Resource Manager automatycznie usuwa wdrożenia z historii wdrażania. Wdrożenia są usuwane, gdy historia jest bliska przekroczenia limitu 800.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: b55c022c35c43be6818bb3c551d5db85b1927ebb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781851"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatyczne usuwanie z historii wdrażania

Za każdym razem, gdy wdrażasz szablon, informacje o wdrożeniu są zapisywane w historii wdrożenia. Każda grupa zasobów jest ograniczona do 800 wdrożeń w swojej historii wdrażania.

Azure Resource Manager automatycznie usuwa wdrożenia z historii, gdy zbliżasz się do limitu. Automatyczne usuwanie to zmiana zachowania z przeszłości. Wcześniej trzeba było ręcznie usunąć wdrożenia z historii wdrożenia, aby uniknąć wystąpienia błędu. Ta zmiana została zaimplementowana 6 sierpnia 2020 r.

**Automatyczne usuwanie jest obsługiwane w przypadku wdrożeń grup zasobów. Obecnie wdrożenia w historii [subskrypcji,](deploy-to-subscription.md) [grup](deploy-to-management-group.md)zarządzania [](deploy-to-tenant.md) i wdrożeń dzierżawy nie są automatycznie usuwane.**

> [!NOTE]
> Usunięcie wdrożenia z historii nie ma wpływu na wszystkie wdrożone zasoby.

## <a name="when-deployments-are-deleted"></a>Po usunięciu wdrożeń

Wdrożenia są usuwane z historii po przekroczeniu 775 wdrożeń. Azure Resource Manager usuwa wdrożenia do momentu, gdy historia nie zostanie zmieść 750. Najstarsze wdrożenia są zawsze najpierw usuwane.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Usunięcia z historii wdrażania":::

> [!NOTE]
> Numer początkowy (775) i numer końcowy (750) mogą ulec zmianie.
>
> Jeśli grupa zasobów jest już w granicach limitu 800, następne wdrożenie zakończy się niepowodzeniem z błędem. Proces automatycznego usuwania rozpoczyna się natychmiast. Możesz spróbować ponownie wykonać wdrożenie po krótkim czasie oczekiwania.

Oprócz wdrożeń można również wyzwalać usunięcia po uruchomieniu operacji [what-if lub](template-deploy-what-if.md) zweryfikowaniu wdrożenia.

Jeśli nadasz wdrożeniu taką samą nazwę jak w historii, zresetuj jego miejsce w historii. Wdrożenie zostanie przeniesiony do ostatniego miejsca w historii. Zresetowanie miejsca wdrożenia jest również resetowane po przywróceniu do [tego wdrożenia](rollback-on-error.md) po błędzie.

## <a name="remove-locks-that-block-deletions"></a>Usuwanie blokad, które blokują usunięcia

Jeśli masz [blokadę CanNotDelete](../management/lock-resources.md) dla grupy zasobów, nie można usunąć wdrożeń dla tej grupy zasobów. Aby korzystać z funkcji automatycznego usuwania w historii wdrażania, należy usunąć blokadę.

Aby usunąć blokadę przy użyciu programu PowerShell, uruchom następujące polecenia:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Aby usunąć blokadę za pomocą interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="required-permissions"></a>Wymagane uprawnienia

Usunięcia są żądane w ramach tożsamości użytkownika, który wdrożył szablon. Aby usunąć wdrożenia, użytkownik musi mieć dostęp do akcji **Microsoft.Resources/deployments/delete.** Jeśli użytkownik nie ma wymaganych uprawnień, wdrożenia nie są usuwane z historii.

Jeśli bieżący użytkownik nie ma wymaganych uprawnień, podczas następnego wdrożenia zostanie ponownie podejmowana próba automatycznego usunięcia.

## <a name="opt-out-of-automatic-deletions"></a>Rezygnacja z automatycznego usuwania

Możesz zrezygnować z automatycznego usuwania z historii. **Tej opcji należy używać tylko wtedy, gdy chcesz samodzielnie zarządzać historią wdrożenia.** Limit 800 wdrożeń w historii jest nadal wymuszany. Jeśli przekroczysz 800 wdrożeń, wystąpi błąd, a wdrożenie nie powiedzie się.

Aby wyłączyć automatyczne usuwanie, zarejestruj `Microsoft.Resources/DisableDeploymentGrooming` flagę funkcji. Podczas rejestrowania flagi funkcji zrezygnuje się z automatycznego usuwania dla całej subskrypcji platformy Azure. Nie można zrezygnować tylko z określonej grupy zasobów. Aby ponownie wyrejestrować automatyczne usuwanie, wyrejestruj flagę funkcji.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W przypadku programu PowerShell użyj [polecenia Register-AzProviderFeature.](/powershell/module/az.resources/Register-AzProviderFeature)

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Aby wyświetlić bieżący stan subskrypcji, użyj:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Aby ponownie wspiąć automatyczne usuwanie, użyj interfejsu API REST platformy Azure lub interfejsu wiersza polecenia platformy Azure.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W przypadku interfejsu wiersza polecenia platformy Azure użyj [polecenia az feature register](/cli/azure/feature#az_feature_register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Aby wyświetlić bieżący stan subskrypcji, użyj:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Aby ponownie wyrejestrować automatyczne usuwanie, użyj [az feature unregister](/cli/azure/feature#az_feature_unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

W przypadku interfejsu API REST użyj [funkcji — zarejestruj](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Aby wyświetlić bieżący stan subskrypcji, użyj:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Aby ponownie wyrejestrować automatyczne usuwanie, użyj [funkcji — wyrejestrowanie](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wyświetlaniu historii wdrażania, zobacz [Wyświetlanie historii wdrażania za pomocą Azure Resource Manager](deployment-history.md).
