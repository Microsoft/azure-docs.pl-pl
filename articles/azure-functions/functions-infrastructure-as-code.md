---
title: Automatyzowanie wdrażania zasobów aplikacji funkcji na platformie Azure
description: Dowiedz się, jak utworzyć Azure Resource Manager szablonu, który wdraża aplikację funkcji.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 4bbd3491c45b15d43ae0e94b37b916cd8091e655
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834214"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatyzowanie wdrażania zasobów dla aplikacji funkcji w Azure Functions

Do wdrożenia aplikacji funkcji Azure Resource Manager szablonu aplikacji funkcji. Ten artykuł zawiera opis wymaganych zasobów i parametrów. W zależności od wyzwalaczy i [](functions-triggers-bindings.md) powiązań w aplikacji funkcji może być konieczne wdrożenie dodatkowych zasobów.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Przykładowe szablony można znaleźć w te tematach:
- [Aplikacja funkcji w planie Zużycie]
- [Aplikacja funkcji w Azure App Service planie]

## <a name="required-resources"></a>Wymagane zasoby

Wdrożenie Azure Functions zwykle składa się z tych zasobów:

| Zasób                                                                           | Wymaganie | Informacje o składni i właściwościach                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Aplikacja funkcji                                                                     | Wymagane    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |
| Konto [usługi Azure Storage](../storage/index.yml)                                   | Wymagane    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Składnik [Application Insights](../azure-monitor/app/app-insights-overview.md) danych | Opcjonalne    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |
| Plan [hostingu](./functions-scale.md)                                             | Opcjonalne<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Plan hostingu jest wymagany tylko wtedy, gdy wybierzesz uruchamianie aplikacji funkcji w planie [Premium](./functions-premium-plan.md) lub w [planie App Service usługi](../app-service/overview-hosting-plans.md).

> [!TIP]
> Chociaż nie jest to wymagane, zdecydowanie zaleca się skonfigurowanie Application Insights dla aplikacji.

<a name="storage"></a>
### <a name="storage-account"></a>Konto magazynu

Konto usługi Azure Storage jest wymagane dla aplikacji funkcji. Potrzebujesz konta ogólnego przeznaczenia, które obsługuje obiekty blob, tabele, kolejki i pliki. Aby uzyskać więcej informacji, [zobacz Azure Functions wymagania dotyczące konta magazynu.](storage-considerations.md#storage-account-requirements)

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-06-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Ponadto właściwość musi `AzureWebJobsStorage` być określona jako ustawienie aplikacji w konfiguracji witryny. Jeśli aplikacja funkcji nie używa usługi Application Insights do monitorowania, powinna również określić `AzureWebJobsDashboard` jako ustawienie aplikacji.

Środowisko Azure Functions używa parametrów `AzureWebJobsStorage` połączenia do tworzenia kolejek wewnętrznych.  Gdy Application Insights nie jest włączona, środowisko uruchomieniowe używa parametrów połączenia do logowania się do usługi Azure Table Storage i zasilania `AzureWebJobsDashboard` **karty Monitor** w portalu.

Te właściwości są określone w `appSettings` kolekcji w `siteConfig` obiekcie :

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights do monitorowania aplikacji funkcji. Zasób Application Insights jest zdefiniowany za pomocą typu **Microsoft.Insights/components** i rodzaju **web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Ponadto klucz instrumentacji musi zostać dostarczony do aplikacji funkcji przy użyciu `APPINSIGHTS_INSTRUMENTATIONKEY` ustawienia aplikacji. Ta właściwość jest określona w `appSettings` kolekcji w `siteConfig` obiekcie :

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plan hostingu

Definicja planu hostingu może być różna i może być jedną z następujących:
* [Plan Zużycia](#consumption) (domyślny)
* [Plan Premium](#premium)
* [Plan usługi App Service](#app-service-plan)

### <a name="function-app"></a>Aplikacja funkcji

Zasób aplikacji funkcji jest definiowany przy użyciu zasobu typu **Microsoft.Web/sites** i kind **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
}
```

> [!IMPORTANT]
> Jeśli jawnie definiujesz plan hostingu, w tablicy dependsOn będzie potrzebny dodatkowy element: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Aplikacja funkcji musi zawierać następujące ustawienia aplikacji:

| Nazwa ustawienia                 | Opis                                                                               | Przykładowe wartości                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Parametrów połączenia z kontem magazynu, których środowisko uruchomieniowe usługi Functions używa do wewnętrznego kolejkowania | Zobacz [Konto magazynu](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Wersja środowiska Azure Functions uruchomieniowego                                                | `~3`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Stos języka, który ma być używany dla funkcji w tej aplikacji                                   | `dotnet`, `node` , `java` , `python` lub `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Wymagane tylko w przypadku `node` używania stosu języka, określa wersję do użycia              | `10.14.1`                             |

Te właściwości są określone w `appSettings` kolekcji we właściwości `siteConfig` :

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Wdrażanie w planie Zużycie

Plan Zużycie automatycznie przydziela moc obliczeniową, gdy kod jest uruchomiony, skaluje w zewnątrz zgodnie z potrzebami w celu obsługi obciążenia, a następnie skaluje w czasie, gdy kod nie jest uruchomiony. Nie musisz płacić za bezczynne maszyny wirtualne i nie musisz rezerwować pojemności z wyprzedzeniem. Aby dowiedzieć się więcej, zobacz [Azure Functions skalowanie i hostowanie](consumption-plan.md).

Aby uzyskać przykładowy szablon Azure Resource Manager, zobacz [Aplikacja funkcji w planie Zużycie].

### <a name="create-a-consumption-plan"></a>Tworzenie planu zużycie

Nie trzeba określać planu Zużycie. Jeden z nich zostanie automatycznie utworzony lub wybrany dla 1 regionu podczas tworzenia samego zasobu aplikacji funkcji.

Plan Zużycie jest specjalnym typem zasobu "serverfarm". W przypadku systemu Windows można go określić przy użyciu `Dynamic` wartości właściwości `computeMode` i `sku` :

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Planu Zużycie nie można jawnie zdefiniować dla systemu Linux. Zostanie ona utworzona automatycznie.

Jeśli jawnie zdefiniujesz plan Zużycie, musisz ustawić właściwość w aplikacji tak, aby wskazujeła identyfikator `serverFarmId` zasobu planu. Upewnij się, że aplikacja funkcji `dependsOn` ma również ustawienie planu.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Ustawienia wymagane przez aplikację funkcji uruchamianą w planie Zużycie są odroczeń między systemami Windows i Linux. 

#### <a name="windows"></a>Windows

W systemie Windows plan Zużycie wymaga dodatkowego ustawienia w konfiguracji lokacji: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Ta właściwość konfiguruje konto magazynu, na którym jest przechowywany kod i konfiguracja aplikacji funkcji.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

> [!IMPORTANT]
> Nie należy ustawiać ustawienia, ponieważ jest ono generowane podczas [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) pierwszego tworzenia witryny.  

#### <a name="linux"></a>Linux

W systemie Linux aplikacja funkcji musi mieć ustawioną wartość , a właściwość `kind` `functionapp,linux` musi mieć wartość `reserved` `true` . 

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        },
        "reserved": true
    }
}
```

Ustawienia [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) i nie są obsługiwane w systemie [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) Linux.

<a name="premium"></a>
## <a name="deploy-on-premium-plan"></a>Wdrażanie w planie Premium

Plan Premium oferuje takie samo skalowanie jak plan Zużycie, ale obejmuje dedykowane zasoby i dodatkowe możliwości. Aby dowiedzieć się więcej, zobacz [Azure Functions Premium.](./functions-premium-plan.md)

### <a name="create-a-premium-plan"></a>Tworzenie planu Premium

Plan Premium jest specjalnym typem zasobu "serverfarm". Można go określić przy użyciu albo `EP1` , , albo dla wartości właściwości w `EP2` `EP3` `Name` `sku` [obiekcie description](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Aplikacja funkcji w planie Premium musi mieć właściwość ustawioną na identyfikator `serverFarmId` zasobu utworzonego wcześniej planu. Ponadto plan Premium wymaga dodatkowego ustawienia w konfiguracji lokacji: [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](functions-app-settings.md#website_contentazurefileconnectionstring) . Ta właściwość konfiguruje konto magazynu, na którym jest przechowywany kod i konfiguracja aplikacji funkcji.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```
> [!IMPORTANT]
> Nie należy ustawiać ustawienia, ponieważ jest ono generowane podczas [`WEBSITE_CONTENTSHARE`](functions-app-settings.md#website_contentshare) pierwszego tworzenia witryny.  

<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Wdrażanie w App Service planu

W planie App Service funkcji działa na dedykowanych maszyn wirtualnych w przypadku jednostki SKU w chmurze Podstawowa, Standardowa i Premium, podobnie jak w przypadku aplikacji internetowych. Aby uzyskać szczegółowe informacje na temat App Service planu, zobacz szczegółowe omówienie Azure App Service [planów.](../app-service/overview-hosting-plans.md)

Aby uzyskać przykładowy szablon Azure Resource Manager, zobacz [Aplikacja funkcji w Azure App Service planie].

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

Plan App Service jest definiowany przez zasób "serverfarm".

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Aby uruchomić aplikację w systemie Linux, musisz również ustawić wartość `kind` `Linux` na :

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Aplikacja funkcji w planie App Service musi mieć właściwość ustawioną na identyfikator zasobu `serverFarmId` utworzonego wcześniej planu.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ]
        }
    }
}
```

Aplikacje systemu Linux powinny również zawierać `linuxFxVersion` właściwość w obszarze `siteConfig` . Jeśli tylko wdrażasz kod, jego wartość jest określana przez żądany stos środowiska uruchomieniowego w formacie ```runtime|runtimeVersion``` :

| Stos            | Przykładowa wartość                                         |
|------------------|-------------------------------------------------------|
| Python           | `python|3.7`      |
| JavaScript       | `node|12`          |
| .NET             | `dotnet|3.1` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                }
            ],
            "linuxFxVersion": "node|12"
        }
    }
}
```

W przypadku [wdrażania niestandardowego](./functions-create-function-linux-custom-image.md)obrazu kontenera należy określić go za pomocą i dołączyć konfigurację umożliwiającą ściąganie obrazu, tak jak w `linuxFxVersion` [Web App for Containers](../app-service/index.yml). Ponadto ustaw `WEBSITES_ENABLE_APP_SERVICE_STORAGE` wartość , ponieważ zawartość aplikacji jest `false` dostarczana w kontenerze:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~3"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Dostosowywanie wdrożenia

Aplikacja funkcji zawiera wiele zasobów podrzędnych, których można użyć we wdrożeniu, w tym ustawienia aplikacji i opcje kontroli źródła. Możesz również usunąć zasób **podrzędny sourcecontrols** i zamiast tego użyć [innej](functions-continuous-deployment.md) opcji wdrażania.

> [!IMPORTANT]
> Aby pomyślnie wdrożyć aplikację przy użyciu Azure Resource Manager, ważne jest zrozumienie sposobu wdrażania zasobów na platformie Azure. W poniższym przykładzie konfiguracje najwyższego poziomu są stosowane przy użyciu narzędzia **siteConfig**. Ważne jest, aby ustawić te konfiguracje na najwyższym poziomie, ponieważ przekazują informacje do środowiska uruchomieniowego usługi Functions i aparatu wdrażania. Informacje najwyższego poziomu są wymagane przed zastosowaniem **podrzędnego zasobu źródłowego/zasobu** internetowego. Mimo że istnieje możliwość skonfigurowania tych ustawień w zasobie **config/appSettings** na poziomie podrzędnym, w niektórych przypadkach aplikacja funkcji musi zostać wdrożona przed zastosowaniem  **konfiguracji/ustawień** aplikacji. Jeśli na przykład używasz funkcji z Logic Apps [,](../logic-apps/index.yml)twoje funkcje są zależnością innego zasobu.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~3"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2019-06-01').keys[0].value)]",
          "FUNCTIONS_EXTENSION_VERSION": "~3",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Ten szablon używa wartości [Ustawienia](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) aplikacji projektu, która określa katalog podstawowy, w którym aparat wdrażania usługi Functions (Kudu) szuka kodu do wdrożenia. W naszym repozytorium nasze funkcje znajdują się w podfolderze folderu **src.** Dlatego w poprzednim przykładzie ustawiliśmy wartość ustawień aplikacji na `src` . Jeśli funkcje znajdują się w katalogu głównym repozytorium lub jeśli nie wdrażasz z kontroli źródła, możesz usunąć tę wartość ustawień aplikacji.

## <a name="deploy-your-template"></a>Wdrażanie szablonu

Aby wdrożyć szablon, można użyć dowolnego z następujących sposobów:

* [Program PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)
* [Witryna Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
* [Interfejs API REST](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Przycisk Wdróż na platformie Azure

Zastąp ```<url-encoded-path-to-azuredeploy-json>``` wartością [zakodowaną w adresie URL](https://www.bing.com/search?q=url+encode) wersją ścieżki pierwotnej `azuredeploy.json` pliku w usłudze GitHub.

Oto przykład, który używa znaczników markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Oto przykład, który używa kodu HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Następujące polecenia programu PowerShell tworzą grupę zasobów i wdrażają szablon, który tworzy aplikację funkcji z wymaganymi zasobami. Aby można było uruchamiać lokalnie, [Azure PowerShell](/powershell/azure/install-az-ps) zainstalowane. Uruchom, [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) aby się zalogować.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Aby przetestować to wdrożenie, [](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) możesz użyć szablonu takiego jak ten, który tworzy aplikację funkcji w systemie Windows w planie zużycie. Zastąp `<function-app-name>` nazwą unikatową aplikacji funkcji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat tworzenia i konfigurowania Azure Functions.

* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Jak skonfigurować ustawienia aplikacji funkcji platformy Azure](functions-how-to-use-azure-function-app-settings.md)
* [Tworzenie pierwszej funkcji platformy Azure](./functions-get-started.md)

<!-- LINKS -->

[Aplikacja funkcji w planie Zużycie]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplikacja funkcji w Azure App Service planu]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
