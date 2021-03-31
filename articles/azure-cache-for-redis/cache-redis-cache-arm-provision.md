---
title: Wdrażanie usługi Azure cache for Redis przy użyciu szablonu Azure Resource Manager
description: Dowiedz się, jak użyć szablonu Azure Resource Manager (szablon ARM) do wdrożenia pamięci podręcznej platformy Azure dla zasobu Redis. Szablony są udostępniane dla typowych scenariuszy.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 8bd9a45ec7c43d9338dec184afd784d2b163c410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92735970"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Szybki Start: tworzenie pamięci podręcznej platformy Azure dla usługi Redis przy użyciu szablonu ARM

Dowiedz się, jak utworzyć szablon Azure Resource Manager (szablon ARM), który wdraża pamięć podręczną platformy Azure dla Redis. Pamięć podręczna może być używana z istniejącym kontem magazynu w celu przechowywania danych diagnostycznych. Dowiesz się również, jak definiować wdrożone zasoby i jak definiować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Obecnie ustawienia diagnostyczne są udostępniane dla wszystkich pamięci podręcznych w tym samym regionie dla subskrypcji. Aktualizacja jednej pamięci podręcznej w regionie ma wpływ na wszystkie inne pamięci podręczne w regionie.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto magazynu**: aby je utworzyć, zobacz [Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal). Konto magazynu jest używane na potrzeby danych diagnostycznych.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

Następujące zasoby są zdefiniowane w szablonie:

* [Microsoft. cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft. Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

Dostępne są również szablony Menedżer zasobów dla nowej [warstwy Premium](cache-overview.md#service-tiers) .

* [Tworzenie pamięci podręcznej systemu Azure w warstwie Premium dla Redis przy użyciu klastrowania](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Tworzenie pamięci podręcznej systemu Azure w warstwie Premium dla Redis z trwałością danych](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Utwórz Redis Cache w warstwie Premium wdrożone w Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Aby sprawdzić dostępność najnowszych szablonów, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/) i wyszukiwanie w _usłudze Azure cache for Redis_.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz Poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Wybierz lub wprowadź następujące wartości:

    * **Subskrypcja**: wybierz subskrypcję platformy Azure używaną do tworzenia udziału danych i innych zasobów.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową** , aby utworzyć nową grupę zasobów, lub wybierz istniejącą grupę zasobów.
    * **Lokalizacja**: wybierz lokalizację grupy zasobów. Konto magazynu i pamięć podręczna Redis muszą znajdować się w tym samym regionie. Domyślnie pamięć podręczna Redis używa tej samej lokalizacji co grupa zasobów. Należy więc określić tę samą lokalizację, w której znajduje się konto magazynu.
    * **Nazwa Redis Cache**: Wprowadź nazwę pamięci podręcznej Redis.
    * **Istniejące konto magazynu diagnostyki**: Wprowadź identyfikator zasobu konta magazynu. Składnia jest następująca: `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`

    Użyj wartości domyślnej dla pozostałej części ustawień.
1. Wybierz opcję **Akceptuję warunki i postanowienia podane powyżej**, a następnie wybierz pozycję **Kup**.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz utworzoną pamięć podręczną Redis.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już konieczna, usuń ją, co spowoduje usunięcie zasobów z grupy zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia szablonu Azure Resource Manager, który wdraża pamięć podręczną platformy Azure dla Redis. Aby dowiedzieć się, jak utworzyć szablon Azure Resource Manager, który wdraża aplikację internetową platformy Azure w usłudze Azure cache for Redis, zobacz [Tworzenie aplikacji sieci Web i usługi Azure cache for Redis przy użyciu szablonu](./cache-web-app-arm-with-redis-cache-provision.md).
