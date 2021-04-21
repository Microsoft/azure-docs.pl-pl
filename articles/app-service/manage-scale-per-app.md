---
title: Skalowanie per-app w celu hostowania o wysokiej gęstości
description: Skalowanie aplikacji niezależnie od App Service i optymalizowanie wystąpień skalowanych w celu skalowania w skali w planie.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 756117a2a231fcb406fd3e3102a16c318c621aa0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832612"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hosting o wysokiej gęstości na Azure App Service przy użyciu skalowania 1 aplikacji

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Korzystając z App Service, możesz skalować aplikacje, skalując App Service [ich](overview-hosting-plans.md) uruchamianie. Jeśli wiele aplikacji jest uruchamianych w tym samym planie App Service, każde skalowane wystąpienie uruchamia wszystkie aplikacje w planie.

*Skalowanie dla aplikacji* można włączyć na poziomie planu App Service, aby umożliwić skalowanie aplikacji niezależnie od planu App Service, który ją hostuje. W ten sposób App Service można skalować do 10 wystąpień, ale aplikację można ustawić tak, aby używać tylko pięciu wystąpień.

> [!NOTE]
> Skalowanie dla aplikacji jest dostępne tylko dla warstw **cenowych Standardowa,** **Premium,** **Premium V2** i **Izolowana.**
>

Aplikacje są przydzielane do dostępnych App Service planu przy użyciu najlepszego podejścia do równomiernego rozkładu między wystąpieniami. Chociaż dystrybucja równomierna nie jest gwarantowana, platforma zapewnia, że dwa wystąpienia tej samej aplikacji nie będą hostowane w tym samym App Service planie.

Platforma nie korzysta z metryk, aby decydować o alokacji procesów roboczych. Aplikacje są ponownie równoważenia tylko wtedy, gdy wystąpienia są dodawane lub usuwane z App Service planu.

## <a name="per-app-scaling-using-powershell"></a>Skalowanie według aplikacji przy użyciu programu PowerShell

Utwórz plan ze skalowaniem dla aplikacji, przekazując ```-PerSiteScaling $true``` parametr do ```New-AzAppServicePlan``` polecenia cmdlet .

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Włącz skalowanie dla aplikacji przy użyciu istniejącego planu App Service, przekazując `-PerSiteScaling $true` parametr do ```Set-AzAppServicePlan``` polecenia cmdlet .

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Na poziomie aplikacji skonfiguruj liczbę wystąpień, których aplikacja może używać w planie App Service aplikacji.

W poniższym przykładzie aplikacja jest ograniczona do dwóch wystąpień, niezależnie od tego, do ilu wystąpień jest skalowany bazowy plan usługi App Service.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` różni się od `$newapp.MaxNumberOfWorkers` . Skalowanie dla aplikacji używa `$newapp.SiteConfig.NumberOfWorkers` funkcji do określania właściwości skalowania aplikacji.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Skalowanie per-app przy użyciu Azure Resource Manager

Poniższy szablon Azure Resource Manager tworzy:

- Plan App Service skalowany w celu skalowania do 10 wystąpień
- aplikacja skonfigurowana do skalowania do maksymalnie pięciu wystąpień.

Plan App Service ustawia właściwość **PerSiteScaling na** wartość `"perSiteScaling": true` true. Aplikacja ustawia liczbę pracowników **do** użycia na 5 `"properties": { "numberOfWorkers": "5" }` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Zalecana konfiguracja hostingu o wysokiej gęstości

Skalowanie według aplikacji to funkcja, która jest włączona zarówno w globalnych regionach świadczenia usługi Azure, [jak i App Service Środowiskach.](environment/app-service-app-service-environment-intro.md) Jednak zalecaną strategią jest użycie środowisk App Service, aby korzystać z zaawansowanych funkcji i większej App Service planu.  

Wykonaj następujące kroki, aby skonfigurować hosting o wysokiej gęstości dla aplikacji:

1. Wyznaczanie planu App Service jako planu o wysokiej gęstości i skalowanie go w poziomie do żądanej pojemności.
1. Ustaw `PerSiteScaling` flagę na wartość true w App Service planie.
1. Nowe aplikacje są tworzone i przypisywane do tego App Service z **właściwością numberOfWorkers ustawioną** na **1**.
   - Użycie tej konfiguracji daje najwyższą możliwą gęstość.
1. Liczbę pracowników można skonfigurować niezależnie dla aplikacji, aby w razie potrzeby przyznać dodatkowe zasoby. Na przykład:
   - Aplikacja o wysokim użyciu może ustawić **wartość numberOfWorkers na** **3,** aby mieć większą pojemność przetwarzania dla tej aplikacji.
   - Aplikacje o niskim użyciu ustawiłyby **wartość numberOfWorkers na** **1**.

## <a name="next-steps"></a>Następne kroki

- [Azure App Service szczegółowe omówienie planów](overview-hosting-plans.md)
- [Wprowadzenie do usługi App Service Environment](environment/app-service-app-service-environment-intro.md)
