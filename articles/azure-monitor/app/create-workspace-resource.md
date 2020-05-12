---
title: Tworzenie nowego Azure Monitor Application Insights zasobów opartych na obszarze roboczym | Microsoft Docs
description: Dowiedz się więcej na temat kroków wymaganych do włączenia nowych Azure Monitor Application Insights zasobów obszaru roboczego.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 65c2220bd3e9ea93e562b256e84796aed9046d00
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211410"
---
# <a name="workspace-based-application-insights-resources-preview"></a>Zasoby Application Insights oparte na obszarze roboczym (wersja zapoznawcza)

Zasoby oparte na obszarze roboczym obsługują pełną integrację między Application Insights i Log Analytics. Teraz możesz wysyłać dane telemetryczne Application Insights do wspólnego Log Analytics obszaru roboczego, dzięki któremu masz pełny dostęp do wszystkich funkcji Log Analytics podczas zachowywania dzienników aplikacji, infrastruktury i platformy w pojedynczej skonsolidowanej lokalizacji.

Pozwala to również na wspólne Access Control oparte na rolach (RBAC) w ramach Twoich zasobów i eliminuje konieczność wykonywania zapytań między aplikacjami i obszarami roboczymi.

> [!NOTE]
> Pozyskiwanie i przechowywanie danych dla zasobów Application Insights opartych na obszarze roboczym jest rozliczane za pośrednictwem Log Analytics obszaru roboczego, w którym znajdują się dane. [Dowiedz się więcej]( https://docs.microsoft.com/azure/azure-monitor/app/pricing#workspace-based-application-insights) o rozliczeniach dla zasobów Application Insights opartych na obszarze roboczym.

Aby przetestować nowe środowisko, zaloguj się do [Azure Portal](https://portal.azure.com)i utwórz zasób Application Insights:

![Zasób Application Insights oparty na obszarze roboczym](./media/create-workspace-resource/create-workspace-based.png)

Jeśli nie masz jeszcze istniejącego obszaru roboczego Log Analytics, [zapoznaj się z dokumentacją dotyczącą tworzenia obszaru roboczego log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

W publicznej wersji zapoznawczej **zasoby na podstawie obszaru roboczego są obecnie ograniczone do zachodnich stanów USA 2, Wschodnie stany USA i Południowo-środkowe stany USA.**

Po utworzeniu zasobu zobaczysz odpowiednie informacje o obszarze roboczym w okienku **Przegląd** :

![Nazwa obszaru roboczego](./media/create-workspace-resource/workspace-name.png)

Kliknięcie niebieskiego linku spowoduje przejście do skojarzonego obszaru roboczego Log Analytics, w którym można wykorzystać nowe środowisko zapytań ujednoliconego obszaru roboczego.

> [!NOTE]
> Firma Microsoft zapewnia pełną zgodność z poprzednimi wersjami Application Insights klasycznych zapytań zasobów, skoroszytów i alertów opartych na dziennikach w ramach Application Insights środowiska. Aby zbadać/wyświetlić [nową strukturę lub schemat tabeli opartych na obszarze roboczym](apm-tables.md) , musisz najpierw przejść do obszaru roboczego log Analytics. W trakcie korzystania z wersji zapoznawczej Wybieranie **dzienników** z poziomu okienek Application Insights umożliwi dostęp do klasycznego zapytania Application Insights.

## <a name="copy-the-connection-string"></a>Kopiowanie parametrów połączenia

[Parametry połączenia]() identyfikują zasób, z którym chcesz skojarzyć dane telemetryczne. Umożliwia również modyfikowanie punktów końcowych, które będą używane przez zasób jako miejsce docelowe dla danych telemetrycznych. Należy skopiować parametry połączenia i dodać je do kodu aplikacji lub do zmiennej środowiskowej.

![Kliknij i skopiuj klucz Instrumentacji](./media/create-new-resource/instrumentation-key.png)

## <a name="monitoring-configuration"></a>Konfiguracja monitorowania

Po utworzeniu zasobu Application Insights opartego na obszarze roboczym konfigurowanie monitorowania jest stosunkowo proste.

### <a name="code-based-application-monitoring"></a>Monitorowanie aplikacji opartej na kodzie

W przypadku monitorowania aplikacji opartej na kodzie wystarczy zainstalować odpowiedni zestaw Application Insights SDK i wskazać go kluczem Instrumentacji lub parametry połączenia do nowo utworzonego zasobu.  

Aby uzyskać szczegółową dokumentację dotyczącą konfigurowania zestawu SDK Application Insights dla monitorowania opartego na kodzie, zapoznaj się z dokumentacją dotyczącą języka/platformy:

- [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
- [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
- [Zadania w tle & nowoczesnych aplikacji konsolowych (.NET/.NET Core)](https://docs.microsoft.com/azure/azure-monitor/app/worker-service)
- [Klasyczne aplikacje konsolowe (.NET)](https://docs.microsoft.com/azure/azure-monitor/app/console) 
- [Oprogramowania](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started?tabs=maven)
- [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
- [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs)
- [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Monitorowanie bezkodowe i tworzenie zasobów programu Visual Studio

W przypadku monitorowania usług, takich jak Azure Functions i App Services platformy Azure, należy najpierw utworzyć zasób Application Insights oparty na obszarze roboczym, a następnie wskazać ten zasób w fazie konfiguracji monitorowania.

Chociaż te usługi oferują opcję tworzenia nowego zasobu Application Insights w ramach procesu tworzenia zasobów, zasoby utworzone za pomocą tych opcji interfejsu użytkownika są obecnie ograniczone do klasycznego Application Insights środowiska.

To samo dotyczy środowiska tworzenia zasobów Application Insights w programie Visual Studio dla ASP.NET i ASP.NET Core. Musisz wybrać istniejący zasób oparty na obszarze roboczym z programu przy użyciu interfejsu użytkownika obsługi funkcji monitorowania programu Visual Studio. Wybranie pozycji Utwórz nowy zasób z poziomu programu Visual Studio spowoduje ograniczenie tworzenia zasobu klasycznego Application Insights.

## <a name="creating-a-resource-automatically"></a>Automatyczne tworzenie zasobu

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby uzyskać dostęp do Application Insights poleceń interfejsu wiersza polecenia platformy Azure w wersji zapoznawczej, należy najpierw wykonać następujące działania:

```azurecli
 az extension add -n application-insights
```

Jeśli polecenie nie zostanie uruchomione `az extension add` , zostanie wyświetlony komunikat o błędzie informujący o tym, że:`az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Teraz można wykonać następujące czynności, aby utworzyć zasób Application Insights:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Przykład

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Aby zapoznać się z pełną dokumentacją interfejsu wiersza polecenia platformy Azure dotyczącą tego przykładu, zapoznaj się z [dokumentacją interfejsu CLI platformy Azure](https://docs.microsoft.com/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-create)

### <a name="azure-powershell"></a>Azure PowerShell

`New-AzApplicationInsights`Polecenie programu PowerShell nie obsługuje obecnie tworzenia zasobów Application Insights opartych na obszarze roboczym. Aby utworzyć zasób oparty na obszarze roboczym przy użyciu programu PowerShell, możesz użyć poniższych szablonów Azure Resource Manager i wdrożyć program PowerShell.

### <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

#### <a name="template-file"></a>Plik szablonu

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Plik parametrów

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Modyfikowanie skojarzonego obszaru roboczego

Po utworzeniu zasobu Application Insights opartego na obszarze roboczym można zmodyfikować skojarzony obszar roboczy Log Analytics.

W okienku zasobów Application Insights wybierz pozycję **Właściwości**  >  **Zmień obszar roboczy**  >  **log Analytics obszary robocze**

## <a name="next-steps"></a>Następne kroki

* [Eksplorowanie metryk](../../azure-monitor/platform/metrics-charts.md)
* [Pisanie zapytań analitycznych](../../azure-monitor/app/analytics.md)

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/platform/metrics-charts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
