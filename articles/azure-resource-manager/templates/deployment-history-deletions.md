---
title: Usunięcia historii wdrażania
description: Opisuje, w jaki sposób Azure Resource Manager automatycznie usuwać wdrożenia z historii wdrażania. Wdrożenia są usuwane, gdy historia zbliża się do przekroczenia limitu 800.
ms.topic: conceptual
ms.date: 10/01/2020
ms.openlocfilehash: 13c65f3311e308708034bb5befb7e3c3ee158d38
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91652486"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatyczne usuwanie z historii wdrożenia

Przy każdym wdrożeniu szablonu informacje o wdrożeniu są zapisywane w historii wdrażania. Każda grupa zasobów jest ograniczona do 800 wdrożeń w swojej historii wdrażania.

Azure Resource Manager automatycznie usuwa wdrożenia z historii, jak zbliżasz się do limitu. Automatyczne usuwanie to zmiana w stosunku do wcześniejszego zachowania. Wcześniej trzeba było ręcznie usunąć wdrożenia z historii wdrożenia, aby uniknąć błędów. Ta zmiana została wprowadzona w dniu 6 sierpnia 2020.

**Automatyczne operacje usuwania są obsługiwane w przypadku wdrożeń grup zasobów. Obecnie wdrożenia w historii [subskrypcji](deploy-to-subscription.md), [grupy zarządzania](deploy-to-management-group.md)i wdrożenia [dzierżawców](deploy-to-tenant.md) nie są automatycznie usuwane.**

> [!NOTE]
> Usunięcie wdrożenia z historii nie ma wpływu na żadne wdrożone zasoby.

## <a name="when-deployments-are-deleted"></a>Po usunięciu wdrożeń

Wdrożenia są usuwane z historii w przypadku przekroczenia 775 wdrożeń. Azure Resource Manager usuwa wdrożenia do momentu, aż historia zostanie wyłączona do 750. Najstarsze wdrożenia są zawsze usuwane jako pierwsze.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Usunięcia z historii wdrożenia":::

> [!NOTE]
> Numer początkowy (775) i numer końcowy (750) mogą ulec zmianie.
>
> Jeśli grupa zasobów ma już limit 800, następne wdrożenie zakończy się niepowodzeniem z powodu błędu. Proces automatycznego usuwania rozpocznie się natychmiast. Możesz ponowić próbę wdrożenia po krótkim czasie oczekiwania.

Oprócz wdrożeń wyzwalacze są również wyzwalane po uruchomieniu [operacji działania warunkowego](template-deploy-what-if.md) lub weryfikacji wdrożenia.

Jeśli nastąpi wdrożenie o takiej samej nazwie jak jedna w historii, zresetuj jej miejsce w historii. Wdrożenie przechodzi do ostatniego miejsca w historii. Należy również zresetować miejsce wdrożenia po [wycofaniu tego wdrożenia](rollback-on-error.md) po wystąpieniu błędu.

## <a name="remove-locks-that-block-deletions"></a>Usuń blokady blokujące operacje usuwania

Jeśli masz [blokadę CanNotDelete](../management/lock-resources.md) w grupie zasobów, nie można usunąć wdrożeń dla tej grupy zasobów. Należy usunąć blokadę, aby skorzystać z automatycznych usunięć w historii wdrażania.

Aby usunąć blokadę przy użyciu programu PowerShell, uruchom następujące polecenia:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName lockedRG).LockId
Remove-AzResourceLock -LockId $lockId
```

Aby usunąć blokadę przy użyciu interfejsu wiersza polecenia platformy Azure, uruchom następujące polecenia:

```azurecli-interactive
lockid=$(az lock show --resource-group lockedRG --name deleteLock --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="opt-out-of-automatic-deletions"></a>Rezygnacja z automatycznego usuwania

Można zrezygnować z automatycznego usuwania z historii. **Tej opcji należy używać tylko wtedy, gdy chcesz samodzielnie zarządzać historią wdrożenia.** Obowiązuje limit 800 wdrożeń w historii. W przypadku przekroczenia 800 wdrożeń zostanie wyświetlony komunikat o błędzie, a wdrożenie zakończy się niepowodzeniem.

Aby wyłączyć automatyczne usuwanie, zarejestruj `Microsoft.Resources/DisableDeploymentGrooming` flagę funkcji. Po zarejestrowaniu flagi funkcji można zrezygnować z automatycznego usuwania dla całej subskrypcji platformy Azure. Nie można zrezygnować tylko dla określonej grupy zasobów. Aby ponownie włączyć automatyczne usuwanie, Wyrejestruj flagę funkcji.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W przypadku programu PowerShell należy użyć polecenia [register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Aby wyświetlić bieżący stan subskrypcji, użyj:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Aby ponownie włączyć automatyczne usuwanie, użyj interfejsu API REST platformy Azure lub wiersza polecenia platformy Azure.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenia [AZ Feature Register](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Aby wyświetlić bieżący stan subskrypcji, użyj:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Aby ponownie włączyć automatyczne usuwanie, użyj polecenie [AZ Feature Unregister](/cli/azure/feature#az-feature-unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

W przypadku interfejsu API REST Użyj [funkcji Register](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Aby wyświetlić bieżący stan subskrypcji, użyj:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Aby ponownie włączyć automatyczne usuwanie, użyj [funkcji-Unregister](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wyświetlaniu historii wdrożenia, zobacz [Wyświetlanie historii wdrożenia za pomocą Azure Resource Manager](deployment-history.md).
