---
title: Automatyzacja Application Insights platformy Azure przy użyciu programu PowerShell | Microsoft Docs
description: Automatyzacja tworzenia zasobów, alertów i testów dostępności w programie PowerShell oraz zarządzania nimi przy użyciu szablonu Azure Resource Manager.
ms.topic: conceptual
ms.date: 05/02/2020
ms.openlocfilehash: 58ed537a8fa6622756e7bcddceb6e9321cfbd82e
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94741082"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Zarządzanie zasobami Application Insights przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule przedstawiono sposób automatycznego automatyzowania tworzenia i aktualizowania zasobów [Application Insights](./app-insights-overview.md) przy użyciu usługi Azure Resource Management. Można to zrobić na przykład w ramach procesu kompilacji. Wraz z zasobem podstawowa Application Insights można tworzyć [testy sieci Web dostępności](./monitor-web-app-availability.md), konfigurować [alerty](../platform/alerts-log.md), ustawiać [schemat cenowy](pricing.md)oraz tworzyć inne zasoby platformy Azure.

Kluczem do tworzenia tych zasobów są szablony JSON dla [Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md). Podstawowa procedura: pobieranie definicji JSON istniejących zasobów; Sparametryzuj pewne wartości, takie jak Names; a następnie uruchom szablon, gdy chcesz utworzyć nowy zasób. Można spakować wiele zasobów razem, aby utworzyć je wszystkie w jednym miejscu, na przykład monitor aplikacji z testami dostępności, alertami i magazynem na potrzeby eksportu ciągłego. Istnieje kilka subtleties niektórych parameterizations, które wyjaśnimy tutaj.

## <a name="one-time-setup"></a>Konfiguracja jednorazowa
Jeśli nie korzystasz z programu PowerShell z subskrypcją platformy Azure przed:

Zainstaluj moduł Azure PowerShell na komputerze, na którym chcesz uruchomić skrypty:

1. Zainstaluj [Instalator platformy Microsoft Web (w wersji 5 lub nowszej)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Służy do instalowania Microsoft Azure PowerShell.

Oprócz używania szablonów Menedżer zasobów istnieje bogaty zestaw [Application Insights poleceń cmdlet programu PowerShell](/powershell/module/az.applicationinsights), które ułatwiają konfigurowanie Application Insights zasobów programowo. Funkcje dostępne w poleceniach cmdlet obejmują:

* Tworzenie i usuwanie zasobów Application Insights
* Pobierz listy zasobów Application Insights i ich właściwości
* Tworzenie eksportu ciągłego i zarządzanie nim
* Tworzenie kluczy aplikacji i zarządzanie nimi
* Ustaw limit dzienny
* Ustawianie planu cenowego

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Tworzenie zasobów Application Insights przy użyciu polecenia cmdlet programu PowerShell

Poniżej przedstawiono sposób tworzenia nowego zasobu usługi Application Insights w centrum danych we/wschodnich stanach USA przy użyciu polecenia cmdlet [New-AzApplicationInsights](/powershell/module/az.applicationinsights/new-azapplicationinsights) :

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Tworzenie Application Insights zasobów przy użyciu szablonu Menedżer zasobów

Poniżej przedstawiono sposób tworzenia nowego zasobu Application Insights przy użyciu szablonu Menedżer zasobów.

### <a name="create-the-azure-resource-manager-template"></a>Tworzenie szablonu Azure Resource Manager

Utwórz nowy plik JSON — Przywołajmy go `template1.json` w tym przykładzie. Skopiuj do niej tę zawartość:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 0,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Tworzenie nowego zasobu Application Insights przy użyciu szablonu Menedżer zasobów

1. W programie PowerShell Zaloguj się do platformy Azure przy użyciu `$Connect-AzAccount`
2. Ustawianie kontekstu na subskrypcję za pomocą `Set-AzContext "<subscription ID>"`
2. Uruchom nowe wdrożenie, aby utworzyć nowy zasób Application Insights:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` jest grupą, w której chcesz utworzyć nowe zasoby.
   * `-TemplateFile` musi następować przed parametrami niestandardowymi.
   * `-appName` Nazwa zasobu do utworzenia.

Możesz dodać inne parametry — opisy można znaleźć w sekcji Parametry szablonu.

## <a name="get-the-instrumentation-key"></a>Pobieranie klucza Instrumentacji

Po utworzeniu zasobu aplikacji należy użyć klucza Instrumentacji: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Aby wyświetlić listę wielu innych właściwości zasobu Application Insights, użyj:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Dodatkowe właściwości są dostępne za pośrednictwem poleceń cmdlet:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Zapoznaj się z [dokumentacją](/powershell/module/az.applicationinsights) dotyczącą parametrów tych poleceń cmdlet.  

## <a name="set-the-data-retention"></a>Ustaw przechowywanie danych

Poniżej znajdują się trzy metody programistycznego ustawiania przechowywania danych w zasobie Application Insights.

### <a name="setting-data-retention-using-a-powershell-commands"></a>Ustawianie przechowywania danych za pomocą poleceń programu PowerShell

Oto prosty zestaw poleceń programu PowerShell służących do ustawiania przechowywania danych dla Application Insights zasobu:

```PS
$Resource = Get-AzResource -ResourceType Microsoft.Insights/components -ResourceGroupName MyResourceGroupName -ResourceName MyResourceName
$Resource.Properties.RetentionInDays = 365
$Resource | Set-AzResource -Force
```

### <a name="setting-data-retention-using-rest"></a>Ustawianie przechowywania danych przy użyciu REST

Aby uzyskać bieżące przechowywanie danych dla zasobu Application Insights, można użyć narzędzia OSS [ARMClient](https://github.com/projectkudu/ARMClient).  (Dowiedz się więcej na temat ARMClient z artykułów przez [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) i [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)).  Oto przykład użycia `ARMClient` , aby pobrać bieżące przechowywanie:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Aby ustawić przechowywanie, polecenie jest podobne do UMIESZCZAnia:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Aby ustawić przechowywanie danych na 365 dni przy użyciu szablonu powyżej, uruchom polecenie:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

### <a name="setting-data-retention-using-a-powershell-script"></a>Ustawianie przechowywania danych przy użyciu skryptu programu PowerShell

Aby zmienić przechowywanie, można również użyć następującego skryptu. Skopiuj ten skrypt, aby zapisać go jako `Set-ApplicationInsightsRetention.ps1` .

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Ten skrypt może być następnie używany jako:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Ustaw limit dzienny

Aby uzyskać właściwości dziennego zakończenia, użyj polecenia cmdlet [Set-AzApplicationInsightsPricingPlan](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) : 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Aby ustawić właściwości dziennego limitu, użyj tego samego polecenia cmdlet. Na przykład, aby ustawić limit na 300 GB/dzień,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

Można również użyć [ARMClient](https://github.com/projectkudu/ARMClient) do pobrania i ustawienia dziennych parametrów Cap.  Aby uzyskać bieżące wartości, użyj:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>Ustaw godzinę resetowania dziennego limitu

Aby ustawić dzienny czas resetowania, możesz użyć [ARMClient](https://github.com/projectkudu/ARMClient). Oto przykład użycia `ARMClient` , aby ustawić godzinę resetowania na nową godzinę (w tym przykładzie 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Ustawianie planu cenowego 

Aby uzyskać bieżący plan cenowy, należy użyć polecenia cmdlet [Set-AzApplicationInsightsPricingPlan](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) :

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Aby ustawić plan cenowy, użyj tego samego polecenia cmdlet z `-PricingPlan` określonym:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

Możesz również ustawić plan cenowy dla istniejącego zasobu Application Insights przy użyciu szablonu Menedżer zasobów powyżej, pomijając zasób "Microsoft. Insights/Components" i `dependsOn` węzeł z zasobu rozliczeń. Na przykład aby ustawić dla planu na GB (dawniej nazywany planem Basic), uruchom polecenie:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

`priceCode`Jest definiowana jako:

|priceCode|plan|
|---|---|
|1|Za GB (dawniej nazwany plan podstawowy)|
|2|Na węzeł (dawniej Nazwa planu przedsiębiorstwa)|

Na koniec możesz użyć [ARMClient](https://github.com/projectkudu/ARMClient) , aby pobrać i ustawić plany cenowe oraz parametry dziennego limitu.  Aby uzyskać bieżące wartości, użyj:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

I można ustawić wszystkie te parametry przy użyciu:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Ustawi dzienny limit na 200 GB/dzień, skonfiguruj dzienny czas resetowania do 12:00 czasu UTC, Wyślij wiadomości e-mail zarówno po osiągnięciu limitu, jak i poziom ostrzeżenia, a następnie ustaw próg ostrzeżenia na 90% limitu.  

## <a name="add-a-metric-alert"></a>Dodawanie alertu metryki

Aby zautomatyzować tworzenie alertów metryk, zapoznaj się z [artykułem szablon alertów dotyczących metryk](../platform/alerts-metric-create-templates.md#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Dodaj Test dostępności

Aby zautomatyzować testy dostępności, zapoznaj się z [artykułem szablon alertów dotyczących metryk](../platform/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="add-more-resources"></a>Dodaj więcej zasobów

Aby zautomatyzować tworzenie dowolnego innego zasobu dowolnego rodzaju, Utwórz przykład ręcznie, a następnie skopiuj i Sparametryzuj jego kod z [Azure Resource Manager](https://resources.azure.com/). 

1. Otwórz [Azure Resource Manager](https://resources.azure.com/). Przejdź w dół `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` do zasobu aplikacji. 
   
    ![Nawigacja w Azure Resource Explorer](./media/powershell/01.png)
   
    *Składniki* to podstawowe zasoby Application Insights do wyświetlania aplikacji. Istnieją oddzielne zasoby dla skojarzonych reguł alertów i testów dostępności sieci Web.
2. Skopiuj kod JSON składnika do odpowiedniego miejsca w `template1.json` .
3. Usuń następujące właściwości:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Otwórz `webtests` sekcje i `alertrules` i skopiuj kod JSON dla poszczególnych elementów do szablonu. (Nie Kopiuj z `webtests` węzłów lub `alertrules` : Przejdź do elementów znajdujących się poniżej).
   
    Każdy test sieci Web ma skojarzoną regułę alertu, dlatego należy skopiować oba te testy.
   
5. Wstaw ten wiersz do każdego zasobu:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Sparametryzuj szablonu
Teraz należy zamienić określone nazwy na parametry. Aby [Sparametryzuj szablon](../../azure-resource-manager/templates/template-syntax.md), należy napisać wyrażenia przy użyciu [zestawu funkcji pomocnika](../../azure-resource-manager/templates/template-functions.md). 

Nie można Sparametryzuj tylko części ciągu, więc Użyj `concat()` do kompilowania ciągów.

Poniżej przedstawiono przykłady podstawień, które należy wykonać. Istnieje kilka wystąpień każdego podstawiania. Mogą być potrzebne inne osoby w szablonie. W tych przykładach użyto parametrów i zmiennych zdefiniowanych w górnej części szablonu.

| find | Zamień na |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (małe litery) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Ustawianie zależności między zasobami
Platforma Azure powinna skonfigurować zasoby w ścisłej kolejności. Aby upewnić się, że jedna konfiguracja zostanie zakończona przed rozpoczęciem następnego, Dodaj linie zależności:

* W zasobie Test dostępności:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* W zasobie alertu dla testu dostępności:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Następne kroki
Inne artykuły dotyczące usługi Automation:

* Utwórz szybką metodę [Application Insights zasobów](./create-new-resource.md#creating-a-resource-automatically) bez użycia szablonu.
* [Utwórz testy sieci Web](https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-alerts-metric#availability-test-with-metric-alert)
* [Wysyłanie Diagnostyki Azure do usługi Application Insights](powershell-azure-diagnostics.md)
* [Utwórz adnotacje wydania](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

