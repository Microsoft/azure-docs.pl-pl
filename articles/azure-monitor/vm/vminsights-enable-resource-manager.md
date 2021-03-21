---
title: Włączanie usługi VM Insights za pomocą szablonów Menedżer zasobów
description: W tym artykule opisano, jak włączyć usługę VM Insights dla co najmniej jednej maszyny wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych za pomocą szablonów Azure PowerShell lub Azure Resource Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: fc0c304a3fea81f44e01d3e815f34e44728ea42e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031882"
---
# <a name="enable-vm-insights-using-resource-manager-templates"></a>Włączanie usługi VM Insights za pomocą szablonów Menedżer zasobów
W tym artykule opisano sposób włączania usługi VM Insights dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych przy użyciu szablonów Menedżer zasobów. Ta procedura może być używana w następujących kwestiach:

- Maszyna wirtualna platformy Azure
- Zestaw skalowania maszyn wirtualnych platformy Azure
- Hybrydowa maszyna wirtualna połączona z usługą Azure Arc

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz i skonfiguruj obszar roboczy log Analytics](./vminsights-configure-workspace.md). 
- Zobacz [obsługiwane systemy operacyjne](./vminsights-enable-overview.md#supported-operating-systems) , aby upewnić się, że jest obsługiwany system operacyjny maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych. 

## <a name="resource-manager-templates"></a>Szablony usługi Resource Manager

Utworzyliśmy przykładowe szablony Azure Resource Manager do dołączania maszyn wirtualnych i zestawów skalowania maszyn wirtualnych. Szablony te obejmują scenariusze, których można użyć do włączenia monitorowania istniejącego zasobu i utworzenia nowego zasobu z włączonym monitorowaniem.

>[!NOTE]
>Szablon musi zostać wdrożony w tej samej grupie zasobów co maszyna wirtualna lub zestaw skalowania maszyn wirtualnych.


Szablony Azure Resource Manager są dostępne w pliku archiwum (zip), który można [pobrać](https://aka.ms/VmInsightsARMTemplates) z naszego repozytorium GitHub. Zawartość pliku zawiera foldery, które reprezentują każdy scenariusz wdrożenia z szablonem i plikiem parametrów. Przed uruchomieniem programu należy zmodyfikować plik parametrów i określić wymagane wartości. 

Plik pobierania zawiera następujące szablony dla różnych scenariuszy:

- Szablon **ExistingVmOnboarding** umożliwia szczegółowe informacje o maszynie wirtualnej, jeśli maszyna wirtualna już istnieje.
- Szablon **NewVmOnboarding** tworzy maszynę wirtualną i umożliwia jej monitorowanie w usłudze VM.
- Szablon **ExistingVmssOnboarding** umożliwia szczegółowe informacje o maszynie wirtualnej, jeśli zestaw skalowania maszyn wirtualnych już istnieje.
- Szablon **NewVmssOnboarding** tworzy zestawy skalowania maszyn wirtualnych i umożliwia monitorowanie ich przez maszynę wirtualną.
- Szablon **ConfigureWorkspace** umożliwia skonfigurowanie obszaru roboczego log Analytics do obsługi usługi VM Insights przez włączenie rozwiązań i kolekcji liczników wydajności systemu operacyjnego Linux i Windows.

>[!NOTE]
>Jeśli zestaw skalowania maszyn wirtualnych już istnieje, a zasady uaktualniania są ustawione na **Ręczne**, usługi VM Insights nie będą domyślnie włączone dla wystąpień po uruchomieniu szablonu Azure Resource Manager **ExistingVmssOnboarding** . Musisz ręcznie uaktualnić wystąpienia.

## <a name="deploy-templates"></a>Wdrażanie szablonów
Szablony można wdrażać przy użyciu [dowolnej metody wdrażania dla Menedżer zasobów szablonów](../../azure-resource-manager/templates/deploy-powershell.md) , w tym następujących przykładów przy użyciu programu PowerShell i interfejsu wiersza polecenia.

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az deployment group create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania dla maszyn wirtualnych te informacje są dostępne do analizy za pomocą usługi VM Insights.

- Aby wyświetlić odnalezione zależności aplikacji, zobacz [Wyświetlanie mapy usługi VM Insights](vminsights-maps.md).

- Aby identyfikować wąskie gardła i ogólne wykorzystanie z wydajnością maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).