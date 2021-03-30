---
title: Skalowanie dla aplikacji na potrzeby hostingu o wysokiej gęstości
description: Skaluj aplikacje niezależnie od planów App Service i Optymalizuj wystąpienia skalowania w poziomie w planie.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74672348"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hosting o dużej gęstości na Azure App Service przy użyciu skalowania dla aplikacji

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W przypadku korzystania z App Service można skalować aplikacje, skalowanie [App Service planu](overview-hosting-plans.md) , w którym są uruchamiane. Gdy wiele aplikacji jest uruchomionych w tym samym planie App Service, każde wystąpienie skalowane w poziomie uruchamia wszystkie aplikacje w planie.

*Skalowanie dla aplikacji* można włączyć na poziomie planu App Service, aby umożliwić skalowanie aplikacji niezależnie od planu App Service, który go obsługuje. W ten sposób plan App Service można skalować do 10 wystąpień, ale aplikacja może być ustawiona tak, aby korzystała tylko z pięciu.

> [!NOTE]
> Skalowanie dla aplikacji jest dostępne tylko dla warstwy cenowej **standardowa**, **Premium**, **Premium v2** i **izolowanych** .
>

Aplikacje są przydzieleni do dostępnego planu App Service przy użyciu najlepszego podejścia do równomiernego rozkładu między wystąpieniami. Chociaż dystrybucja parzysta nie jest gwarantowana, platforma sprawdzi, czy dwa wystąpienia tej samej aplikacji nie będą hostowane w tym samym wystąpieniu App Serviceego planu.

Platforma nie polega na metrykach, które decydują o alokacji procesów roboczych. Aplikacje są ponownie rozbilansowane tylko wtedy, gdy wystąpienia są dodawane lub usuwane z planu App Service.

## <a name="per-app-scaling-using-powershell"></a>Skalowanie na aplikację przy użyciu programu PowerShell

Utwórz plan z skalowaniem dla aplikacji, przekazując ```-PerSiteScaling $true``` parametr do ```New-AzAppServicePlan``` polecenia cmdlet.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Włącz skalowanie dla aplikacji za pomocą istniejącego planu App Service, przekazując `-PerSiteScaling $true` parametr do ```Set-AzAppServicePlan``` polecenia cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Na poziomie aplikacji Skonfiguruj liczbę wystąpień, z których aplikacja może korzystać w planie App Service.

W poniższym przykładzie aplikacja jest ograniczona do dwóch wystąpień, niezależnie od liczby wystąpień, do których jest skalowany plan usługi App Service.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` różni się od `$newapp.MaxNumberOfWorkers` . Skalowanie dla aplikacji używa `$newapp.SiteConfig.NumberOfWorkers` do określania charakterystyki skali aplikacji.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Skalowanie dla aplikacji za pomocą Azure Resource Manager

Poniższy szablon Azure Resource Manager tworzy:

- Plan App Service, który jest skalowany do 10 wystąpień
- aplikacja, która jest skonfigurowana do skalowania do maksymalnie pięciu wystąpień.

W planie App Service jest ustawiana wartość true dla właściwości **PerSiteScaling** `"perSiteScaling": true` . Aplikacja ustawia **liczbę procesów roboczych** , które mają być używane do 5 `"properties": { "numberOfWorkers": "5" }` .

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

## <a name="recommended-configuration-for-high-density-hosting"></a>Zalecana konfiguracja dla hostingu o wysokiej gęstości

Skalowanie na aplikację to funkcja, która jest włączona zarówno w globalnych regionach platformy Azure, jak i w [środowiskach App Service](environment/app-service-app-service-environment-intro.md). Jednak zalecaną strategią jest korzystanie z App Service środowiska, aby korzystać z zaawansowanych funkcji i większych App Service planu.  

Wykonaj następujące kroki, aby skonfigurować hosting o wysokiej gęstości dla aplikacji:

1. Wyznacz plan App Service jako plan o wysokiej gęstości i Skaluj go do żądanej pojemności.
1. Ustaw `PerSiteScaling` flagę na wartość true w planie App Service.
1. Nowe aplikacje są tworzone i przypisywane do tego planu App Service z właściwością **numberOfWorkers** ustawioną na **1**.
   - Użycie tej konfiguracji daje najwyższą możliwą gęstość.
1. Liczbę procesów roboczych można skonfigurować niezależnie dla każdej aplikacji, aby udzielić dodatkowych zasobów zgodnie z wymaganiami. Na przykład:
   - Aplikacja o wysokiej wydajności może ustawić **numberOfWorkers** na **3** w celu zapewnienia większej pojemności przetwarzania dla tej aplikacji.
   - Aplikacje o niskim użyciu spowodują ustawienie **numberOfWorkers** na **1**.

## <a name="next-steps"></a>Następne kroki

- [Szczegółowe omówienie planów Azure App Service](overview-hosting-plans.md)
- [Wprowadzenie do usługi App Service Environment](environment/app-service-app-service-environment-intro.md)
