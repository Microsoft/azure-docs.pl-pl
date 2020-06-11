---
title: Usunięcia historii wdrażania
description: Opisuje, w jaki sposób Azure Resource Manager automatycznie usuwać wdrożenia z historii wdrażania. Wdrożenia są usuwane, gdy historia zbliża się do przekroczenia limitu 800.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c16b71646e20b71c0d0ca8c9f8e028773983022f
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84673993"
---
# <a name="automatic-deletions-from-deployment-history"></a>Automatyczne usuwanie z historii wdrożenia

Przy każdym wdrożeniu szablonu informacje o wdrożeniu są zapisywane w historii wdrażania. Każda grupa zasobów jest ograniczona do 800 wdrożeń w swojej historii wdrażania.

Począwszy od czerwca 2020, Azure Resource Manager automatycznie usuwa wdrożenia z historii, jak zbliżasz się do limitu. Automatyczne usuwanie to zmiana w stosunku do wcześniejszego zachowania. Wcześniej trzeba było ręcznie usunąć wdrożenia z historii wdrożenia, aby uniknąć błędów.

> [!NOTE]
> Usunięcie wdrożenia z historii nie ma wpływu na żadne wdrożone zasoby.
>
> Jeśli masz [blokadę CanNotDelete](../management/lock-resources.md) w grupie zasobów, nie można usunąć wdrożeń dla tej grupy zasobów. Należy usunąć blokadę, aby skorzystać z automatycznych usunięć w historii wdrażania.

## <a name="when-deployments-are-deleted"></a>Po usunięciu wdrożeń

Wdrożenia są usuwane z historii wdrożenia po osiągnięciu wdrożenia 790. Azure Resource Manager usuwa mały zestaw najstarszych wdrożeń w celu wyczyszczenia miejsca na potrzeby przyszłych wdrożeń. Większość historii pozostaje niezmieniona. Najstarsze wdrożenia są zawsze usuwane jako pierwsze.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Usunięcia z historii wdrożenia":::

Oprócz wdrożeń wyzwalacze są również wyzwalane po uruchomieniu [operacji działania warunkowego](template-deploy-what-if.md) lub weryfikacji wdrożenia.

Jeśli nastąpi wdrożenie o takiej samej nazwie jak jedna w historii, zresetuj jej miejsce w historii. Wdrożenie przechodzi do ostatniego miejsca w historii. Należy również zresetować miejsce wdrożenia po [wycofaniu tego wdrożenia](rollback-on-error.md) po wystąpieniu błędu.

> [!NOTE]
> Jeśli grupa zasobów ma już limit 800, następne wdrożenie zakończy się niepowodzeniem z powodu błędu. Proces automatycznego usuwania rozpocznie się natychmiast. Możesz ponowić próbę wdrożenia po krótkim czasie oczekiwania.

## <a name="opt-out-of-automatic-deletions"></a>Rezygnacja z automatycznego usuwania

Można zrezygnować z automatycznego usuwania z historii. **Tej opcji należy używać tylko wtedy, gdy chcesz samodzielnie zarządzać historią wdrożenia.** Obowiązuje limit 800 wdrożeń w historii. W przypadku przekroczenia 800 wdrożeń zostanie wyświetlony komunikat o błędzie, a wdrożenie zakończy się niepowodzeniem.

Aby wyłączyć automatyczne usuwanie, zarejestruj `Microsoft.Resources/DisableDeploymentGrooming` flagę funkcji. Po zarejestrowaniu flagi funkcji można zrezygnować z automatycznego usuwania dla całej subskrypcji platformy Azure. Nie można zrezygnować tylko dla określonej grupy zasobów.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

W przypadku programu PowerShell należy użyć polecenia [register-AzProviderFeature](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Aby wyświetlić bieżący stan subskrypcji, użyj:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

W przypadku interfejsu wiersza polecenia platformy Azure Użyj polecenia [AZ Feature Register](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Aby wyświetlić bieżący stan subskrypcji, użyj:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
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

---

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wyświetlaniu historii wdrożenia, zobacz [Wyświetlanie historii wdrożenia za pomocą Azure Resource Manager](deployment-history.md).
