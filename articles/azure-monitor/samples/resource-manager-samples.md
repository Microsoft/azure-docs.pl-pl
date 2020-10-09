---
title: Przykłady szablonów Menedżer zasobów dla Azure Monitor
description: Wdrażanie i Konfigurowanie funkcji Azure Monitor przy użyciu szablonów Menedżer zasobów
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.subservice: ''
ms.openlocfilehash: ab869fc8577d4d1934be96404ded5a2051237bbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87922732"
---
# <a name="resource-manager-template-samples-for-azure-monitor"></a>Przykłady szablonów Menedżer zasobów dla Azure Monitor

Azure Monitor można wdrożyć i skonfigurować na dużą skalę przy użyciu [szablonu Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Poniższe artykuły zawierają przykładowe szablony dla różnych funkcji Azure Monitor. Te przykłady można zmodyfikować dla konkretnych wymagań i wdrożyć je przy użyciu dowolnej standardowej metody wdrażania szablonów Menedżer zasobów. 

## <a name="deploying-the-sample-templates"></a>Wdrażanie przykładowych szablonów
Podstawowe kroki umożliwiające korzystanie z przykładów są następujące:

1. Skopiuj szablon i Zapisz go jako plik JSON.
2. Zmodyfikuj parametry środowiska i Zapisz jako plik JSON.
4. Wdróż szablon przy użyciu [dowolnej metody wdrażania dla szablonów Menedżer zasobów](../../azure-resource-manager/templates/deploy-powershell.md). 

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

- [Agenci](resource-manager-agent.md) — Wdróż i skonfiguruj log Analytics agenta i rozszerzenie diagnostyczne.
- Alerty
  - [Reguły alertów dziennika](resource-manager-alerts-log.md) — alerty z zapytań dzienników i dziennika aktywności platformy Azure.
  - [Reguły alertów dotyczących metryk](resource-manager-alerts-metric.md) — alerty z metryk przy użyciu różnych rodzajów logiki.
- [Application Insights](resource-manager-app-resource.md)
- [Ustawienia diagnostyczne](resource-manager-diagnostic-settings.md) — umożliwia tworzenie ustawień diagnostycznych w celu przekazywania dzienników i metryk z różnych typów zasobów.
- [Zapytania dziennika](resource-manager-log-queries.md) — Twórz zapisane zapytania dziennika w obszarze roboczym log Analytics.
- [Log Analytics obszarze roboczym](resource-manager-workspace.md) — tworzenie log Analytics obszaru roboczego i Konfigurowanie kolekcji różnych źródeł danych z poziomu agenta programu log Analytics.
- [Skoroszyty](resource-manager-workbooks.md) — tworzenie skoroszytów.
- [Azure monitor kontenerów](resource-manager-container-insights.md) — dołączanie klastrów do Azure monitor kontenerów.
- [Azure monitor dla maszyn wirtualnych](resource-manager-vminsights.md) — dołączanie maszyn wirtualnych do Azure monitor dla maszyn wirtualnych.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [szablonach Menedżer zasobów](../../azure-resource-manager/templates/overview.md)