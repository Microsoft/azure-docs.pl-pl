---
title: Wdrażanie Azure Cache for Redis przy użyciu Azure Resource Manager szablonu
description: Dowiedz się, jak wdrożyć zasób Azure Resource Manager arm za pomocą szablonu Azure Cache for Redis ARM. Szablony są dostępne dla typowych scenariuszy.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 08/18/2020
ms.openlocfilehash: 81940d23ebfcc017455974981649023351b6eeb3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835060"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>Szybki start: tworzenie aplikacji Azure Cache for Redis szablonu usługi ARM

Dowiedz się, jak utworzyć Azure Resource Manager szablonu usługi Arm, który wdraża Azure Cache for Redis. Pamięć podręczna może być używana z istniejącym kontem magazynu do przechowywania danych diagnostycznych. Dowiesz się również, jak definiować wdrażane zasoby i definiować parametry określone podczas wykonywania wdrożenia. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb. Obecnie ustawienia diagnostyczne są współdzielone dla wszystkich pamięci podręcznych w tym samym regionie dla subskrypcji. Aktualizacja jednej pamięci podręcznej w regionie ma wpływ na wszystkie pozostałe pamięci podręczne w regionie.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* **Konto magazynu:** aby je utworzyć, zobacz [Tworzenie konta usługi Azure Storage.](../storage/common/storage-account-create.md?tabs=azure-portal) Konto magazynu jest używane do danych diagnostycznych.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-redis-cache/).

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

Następujące zasoby są zdefiniowane w szablonie:

* [Microsoft.Cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft.Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

Resource Manager szablonów dla nowej warstwy [Premium](cache-overview.md#service-tiers) są również dostępne.

* [Tworzenie klastra w Azure Cache for Redis Premium z klastrami](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [Tworzenie usługi Premium Azure Cache for Redis z trwałością danych](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [Tworzenie Redis Cache Premium wdrożonych w Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

Aby sprawdzić najnowsze szablony, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/) i wyszukaj _Azure Cache for Redis_.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się do platformy Azure i otworzyć szablon.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. Wybierz lub wprowadź następujące wartości:

    * **Subskrypcja:** wybierz subskrypcję platformy Azure użytą do utworzenia udziału danych i innych zasobów.
    * **Grupa zasobów:** wybierz **pozycję Utwórz nową,** aby utworzyć nową grupę zasobów, lub wybierz istniejącą grupę zasobów.
    * **Lokalizacja**: wybierz lokalizację grupy zasobów. Konto magazynu i pamięć podręczna Redis Cache muszą znajdować się w tym samym regionie. Domyślnie pamięć podręczna Redis używa tej samej lokalizacji co grupa zasobów. Dlatego określ tę samą lokalizację co konto magazynu.
    * **Redis Cache Nazwa:** wprowadź nazwę pamięci podręcznej Redis Cache.
    * **Istniejące konto magazynu diagnostyki:** wprowadź identyfikator zasobu konta magazynu. Składnia jest następująca: `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`

    Użyj wartości domyślnej dla pozostałych ustawień.
1. Wybierz **pozycję Wyrażam zgodę na powyższe warunki i** postanowienia, a następnie wybierz pozycję **Kup.**

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Otwórz utworzoną pamięć podręczną Redis Cache.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją, co spowoduje usunięcie zasobów w grupie zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>Następne kroki

Z tego samouczka dowiesz się, jak utworzyć szablon Azure Resource Manager, który wdraża Azure Cache for Redis. Aby dowiedzieć się, jak utworzyć szablon Azure Resource Manager, który wdraża aplikację internetową platformy Azure za pomocą usługi Azure Cache for Redis, zobacz Tworzenie aplikacji internetowej oraz Azure Cache for Redis [użyciu szablonu.](./cache-web-app-arm-with-redis-cache-provision.md)
