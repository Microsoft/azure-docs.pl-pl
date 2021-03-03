---
title: Przykłady szablonów Menedżer zasobów dla Azure Monitor
description: Wdrażanie i Konfigurowanie funkcji Azure Monitor przy użyciu szablonów Menedżer zasobów
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: 9218886ded7827d4b7a1e2413f1470ee5cd1563d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733962"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Przykłady szablonów Menedżer zasobów dla Azure Monitor

Azure Monitor można wdrożyć i skonfigurować na dużą skalę przy użyciu [szablonu Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Poniższe artykuły zawierają przykładowe szablony dla różnych funkcji Azure Monitor. Te przykłady można zmodyfikować dla konkretnych wymagań i wdrożyć je przy użyciu dowolnej standardowej metody wdrażania szablonów Menedżer zasobów. 

## <a name="deploying-the-sample-templates"></a>Wdrażanie przykładowych szablonów
Podstawowe kroki umożliwiające korzystanie z przykładów są następujące:

1. Skopiuj szablon i Zapisz go jako plik JSON.
2. Zmodyfikuj parametry środowiska i Zapisz jako plik JSON.
4. Wdróż szablon przy użyciu [dowolnej metody wdrażania dla szablonów Menedżer zasobów](../azure-resource-manager/templates/deploy-powershell.md). 

Na przykład użyj następujących poleceń, aby wdrożyć plik szablonu i parametrów do grupy zasobów przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.


```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name AzureMonitorDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

```azurecli
az login
az deployment group create \
    --name AzureMonitorDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file azure-monitor-deploy.json \
    --parameters azure-monitor-deploy.parameters.json
```

## <a name="list-of-sample-templates"></a>Lista przykładowych szablonów

- [Agenci](agents/resource-manager-agent.md) — Wdróż i skonfiguruj log Analytics agenta i rozszerzenie diagnostyczne.
- Alerty
  - [Reguły alertów dziennika](alerts/resource-manager-alerts-log.md) — alerty z zapytań dzienników i dziennika aktywności platformy Azure.
  - [Reguły alertów dotyczących metryk](alerts/resource-manager-alerts-metric.md) — alerty z metryk przy użyciu różnych rodzajów logiki.
- [Application Insights](app/resource-manager-app-resource.md)
- [Ustawienia diagnostyczne](essentials/resource-manager-diagnostic-settings.md) — umożliwia tworzenie ustawień diagnostycznych w celu przekazywania dzienników i metryk z różnych typów zasobów.
- [Zapytania dziennika](logs/resource-manager-log-queries.md) — Twórz zapisane zapytania dziennika w obszarze roboczym log Analytics.
- [Log Analytics obszarze roboczym](logs/resource-manager-workspace.md) — tworzenie log Analytics obszaru roboczego i Konfigurowanie kolekcji różnych źródeł danych z poziomu agenta programu log Analytics.
- [Skoroszyty](visualize/resource-manager-workbooks.md) — tworzenie skoroszytów.
- [Kontenery usługi Container Insights](containers/resource-manager-container-insights.md) — dołączanie klastrów do usługi Container Insights.
- [Azure monitor dla maszyn wirtualnych](vm/resource-manager-vminsights.md) — dołączanie maszyn wirtualnych do Azure monitor dla maszyn wirtualnych.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [szablonach Menedżer zasobów](../azure-resource-manager/templates/overview.md)