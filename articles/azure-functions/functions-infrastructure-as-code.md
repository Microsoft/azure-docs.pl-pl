---
title: Automatyzowanie wdrożenia zasobów aplikacji funkcji na platformie Azure
description: Dowiedz się, jak utworzyć szablon Azure Resource Manager, który wdraża aplikację funkcji.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2403e3a4bfe7824659a9c34c407e55e6c56e6046
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107729"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatyzowanie wdrażania zasobów dla aplikacji funkcji w Azure Functions

Za pomocą szablonu Azure Resource Manager można wdrożyć aplikację funkcji. W tym artykule opisano wymagane zasoby i parametry. Może być konieczne wdrożenie dodatkowych zasobów w zależności od [wyzwalaczy i powiązań](functions-triggers-bindings.md) w aplikacji funkcji.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

Aby zapoznać się z przykładowymi szablonami, zobacz:
- [Aplikacja funkcji w planie zużycia]
- [Aplikacja funkcji na Azure App Service planie]

## <a name="required-resources"></a>Wymagane zasoby

Wdrożenie Azure Functions zwykle składa się z następujących zasobów:

| Zasób                                                                           | Wymaganie | Informacje o składni i właściwościach                                                         |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|
| Aplikacja funkcji                                                                     | Wymagane    | [Microsoft. Web/witryny](/azure/templates/microsoft.web/sites)                             |
| Konto [usługi Azure Storage](../storage/index.yml)                                   | Wymagane    | [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| Składnik [Application Insights](../azure-monitor/app/app-insights-overview.md) | Opcjonalne    | [Microsoft. Insights/składniki](/azure/templates/microsoft.insights/components)         |
| [Plan hostingu](./functions-scale.md)                                             | Opcjonalne<sup>1</sup>    | [Microsoft. Web/dopuszczalna](/azure/templates/microsoft.web/serverfarms)                 |

<sup>1</sup> Plan hostingu jest wymagany tylko wtedy, gdy użytkownik zdecyduje się na uruchomienie aplikacji funkcji w [planie Premium](./functions-premium-plan.md) lub w [planie App Service](../app-service/overview-hosting-plans.md).

> [!TIP]
> Chociaż nie jest to wymagane, zdecydowanie zaleca się skonfigurowanie Application Insights aplikacji.

<a name="storage"></a>
### <a name="storage-account"></a>Konto magazynu

Konto usługi Azure Storage jest wymagane dla aplikacji funkcji. Potrzebujesz konta ogólnego przeznaczenia, które obsługuje obiekty blob, tabele, kolejki i pliki. Aby uzyskać więcej informacji, zobacz [Azure Functions wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements).

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

Ponadto Właściwość `AzureWebJobsStorage` musi być określona jako ustawienie aplikacji w konfiguracji lokacji. Jeśli aplikacja funkcji nie używa Application Insights do monitorowania, należy również określić `AzureWebJobsDashboard` jako ustawienie aplikacji.

Środowisko uruchomieniowe Azure Functions używa `AzureWebJobsStorage` parametrów połączenia do tworzenia kolejek wewnętrznych.  Gdy Application Insights nie jest włączona, środowisko uruchomieniowe używa `AzureWebJobsDashboard` parametrów połączenia do rejestrowania w usłudze Azure Table Storage i włączania karty **monitor** w portalu.

Te właściwości są określone w `appSettings` kolekcji w `siteConfig` obiekcie:

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

Application Insights jest zalecana do monitorowania aplikacji funkcji. Zasób Application Insights jest zdefiniowany za pomocą typu **Microsoft. Insights/Components** i **sieci Web**:

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

Ponadto klucz Instrumentacji musi być dostarczany do aplikacji funkcji przy użyciu `APPINSIGHTS_INSTRUMENTATIONKEY` Ustawienia aplikacji. Ta właściwość jest określona w `appSettings` kolekcji w `siteConfig` obiekcie:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Plan hostingu

Definicja planu hostingu jest różna i może być jedną z następujących czynności:
* [Plan zużycia](#consumption) (wartość domyślna)
* [Plan Premium](#premium)
* [Plan App Service](#app-service-plan)

### <a name="function-app"></a>Aplikacja funkcji

Zasób aplikacji funkcji jest definiowany przy użyciu zasobu typu **Microsoft. Web/Sites** i typu **functionapp**:

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
> Jeśli jawnie definiujesz plan hostingu, w tablicy dependsOn musi być wymagany dodatkowy element: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Aplikacja funkcji musi zawierać następujące ustawienia aplikacji:

| Nazwa ustawienia                 | Opis                                                                               | Przykładowe wartości                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Parametry połączenia z kontem magazynu, które są używane przez środowisko uruchomieniowe funkcji dla kolejki wewnętrznej | Zobacz [konto magazynu](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Wersja środowiska uruchomieniowego Azure Functions                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Stos języka, który ma być używany dla funkcji w tej aplikacji                                   | `dotnet`, `node` , `java` , `python` lub `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Wymagany tylko w przypadku używania `node` stosu języka, określa wersję do użycia              | `10.14.1`                             |

Te właściwości są określone w `appSettings` kolekcji we `siteConfig` Właściwości:

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
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Wdróż w planie zużycia

Plan zużycia automatycznie przydziela moc obliczeniową, gdy kod jest uruchomiony, skaluje się w miarę potrzeb do obsługi obciążenia, a następnie skaluje się, gdy kod nie jest uruchomiony. Nie musisz uiszczać bezczynnych maszyn wirtualnych i nie musisz zarezerwować z góry pojemności. Aby dowiedzieć się więcej, zobacz [Azure Functions skalowanie i hosting](functions-scale.md#consumption-plan).

Aby zapoznać się z przykładowym szablonem Azure Resource Manager, zobacz [aplikacji funkcji w planie zużycia].

### <a name="create-a-consumption-plan"></a>Tworzenie planu zużycia

Nie trzeba definiować planu zużycia. Jeden zostanie automatycznie utworzony lub wybrany w poszczególnych regionach podczas tworzenia zasobu aplikacji funkcji.

Plan zużycia jest specjalnym typem zasobu "farma serwerów". Dla systemu Windows można określić za pomocą `Dynamic` wartości `computeMode` `sku` właściwości i:

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
> Nie można jawnie zdefiniować planu zużycia dla systemu Linux. Zostanie ona utworzona automatycznie.

W przypadku jawnego definiowania planu zużycia należy ustawić `serverFarmId` Właściwość aplikacji tak, aby wskazywała na identyfikator zasobu planu. Upewnij się, że aplikacja funkcji ma `dependsOn` również ustawienia dla planu.

### <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

#### <a name="windows"></a>Windows

W systemie Windows plan zużycia wymaga dwóch dodatkowych ustawień w konfiguracji lokacji: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` i `WEBSITE_CONTENTSHARE` . Te właściwości konfigurują konto magazynu i ścieżkę pliku, w którym są przechowywane kod i konfiguracja aplikacji funkcji.

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
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
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
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

W systemie Linux aplikacja funkcji musi mieć `kind` ustawioną wartość `functionapp,linux` i musi mieć `reserved` ustawioną właściwość `true` :

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
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Wdróż w planie Premium

Plan Premium oferuje takie same skalowanie jak w przypadku planu zużycia, ale obejmuje zasoby dedykowane i dodatkowe możliwości. Aby dowiedzieć się więcej, zobacz [Azure Functions plan Premium](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Tworzenie planu Premium

Plan Premium jest specjalnym typem zasobu "farma serwerów". Można go określić za pomocą albo `EP1` `EP2` lub `EP3` dla `Name` wartości właściwości w `sku` [obiekcie Description](/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object).

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

Aplikacja funkcji w planie Premium musi mieć `serverFarmId` ustawioną właściwość na identyfikator zasobu utworzonego wcześniej planu. Ponadto plan Premium wymaga dwóch dodatkowych ustawień w konfiguracji lokacji: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` i `WEBSITE_CONTENTSHARE` . Te właściwości konfigurują konto magazynu i ścieżkę pliku, w którym są przechowywane kod i konfiguracja aplikacji funkcji.

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
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
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
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a>

## <a name="deploy-on-app-service-plan"></a>Wdróż w planie App Service

W planie App Service aplikacja funkcji jest uruchamiana na dedykowanych maszynach wirtualnych w jednostkach SKU w warstwach Podstawowa, standardowa i Premium, podobnie jak w przypadku aplikacji sieci Web. Aby uzyskać szczegółowe informacje na temat działania planu App Service, zobacz [szczegółowe Omówienie planów Azure App Service](../app-service/overview-hosting-plans.md).

Aby zapoznać się z przykładowym szablonem Azure Resource Manager, zobacz [Function App on Azure App Service plan].

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

Plan App Service jest definiowany przez zasób "farma serwerów".

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

Aby uruchomić aplikację w systemie Linux, należy również ustawić `kind` `Linux` :

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

Aplikacja funkcji w planie App Service musi mieć `serverFarmId` ustawioną właściwość na identyfikator zasobu utworzonego wcześniej planu.

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
                    "value": "~2"
                }
            ]
        }
    }
}
```

Aplikacje systemu Linux powinny również zawierać `linuxFxVersion` Właściwość w obszarze `siteConfig` . Jeśli po prostu wdrażasz kod, wartość tego elementu jest określana na podstawie żądanego stosu środowiska uruchomieniowego:

| Stos            | Przykładowa wartość                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

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
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

W przypadku [wdrażania niestandardowego obrazu kontenera](./functions-create-function-linux-custom-image.md)należy określić go wraz z `linuxFxVersion` konfiguracją umożliwiającą ściąganie obrazu, jak w [Web App for Containers](../app-service/index.yml). Ponadto ustaw wartość `WEBSITES_ENABLE_APP_SERVICE_STORAGE` `false` , ponieważ zawartość aplikacji znajduje się w kontenerze:

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
                    "value": "~2"
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

Aplikacja funkcji ma wiele zasobów podrzędnych, których można użyć we wdrożeniu, w tym ustawienia aplikacji i opcje kontroli źródła. Możesz również wybrać opcję usunięcia zasobu podrzędnego **sourcecontrols** , a zamiast tego użyć innej [opcji wdrażania](functions-continuous-deployment.md) .

> [!IMPORTANT]
> Aby pomyślnie wdrożyć aplikację przy użyciu Azure Resource Manager, ważne jest, aby zrozumieć, jak zasoby są wdrażane na platformie Azure. W poniższym przykładzie konfiguracje najwyższego poziomu są stosowane przy użyciu **siteConfig**. Ważne jest, aby ustawić te konfiguracje na najwyższym poziomie, ponieważ przekazuje informacje do środowiska uruchomieniowego funkcji i aparatu wdrażania. Przed zastosowaniem podrzędnego zasobu **sourcecontrols/sieci Web** wymagane są informacje najwyższego poziomu. Chociaż istnieje możliwość skonfigurowania tych ustawień w ramach zasobu **konfiguracji/AppSettings** na poziomie podrzędnym, w niektórych przypadkach należy wdrożyć aplikację funkcji *przed* zastosowaniem **konfiguracji/AppSettings** . Na przykład podczas korzystania z funkcji z [Logic Apps](../logic-apps/index.yml), funkcje są zależne od innego zasobu.

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
                "value": "~2"
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
          "FUNCTIONS_EXTENSION_VERSION": "~2",
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
> Ten szablon używa wartości ustawień aplikacji [projektu](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) , która ustawia katalog podstawowy, w którym aparat wdrażania funkcji (kudu) wyszukuje kod do wdrożenia. Nasze funkcje w naszym repozytorium znajdują się w podfolderze folderu **src** . Dlatego w poprzednim przykładzie ustawimy wartość ustawienia aplikacji na `src` . Jeśli Twoje funkcje znajdują się w katalogu głównym repozytorium lub nie są wdrażane z kontroli źródła, możesz usunąć tę wartość ustawienia aplikacji.

## <a name="deploy-your-template"></a>Wdrażanie szablonu

Aby wdrożyć szablon, można użyć dowolnego z poniższych sposobów:

* [Program PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md)
* [Witryna Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
* [Interfejs API REST](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Przycisk Wdróż na platformie Azure

Zamień na ```<url-encoded-path-to-azuredeploy-json>``` [zakodowaną w adresie URL](https://www.bing.com/search?q=url+encode) wersję ścieżki nieprzetworzonego `azuredeploy.json` pliku w serwisie GitHub.

Oto przykład, który używa promocji:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Oto przykład, który używa języka HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Następujące polecenia programu PowerShell tworzą grupę zasobów i wdrażają szablon, który tworzy aplikację funkcji z wymaganymi zasobami. Aby uruchomić lokalnie, musisz mieć zainstalowaną [Azure PowerShell](/powershell/azure/install-az-ps) . Uruchom [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) , aby się zalogować.

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

Aby przetestować to wdrożenie, możesz użyć [szablonu takiego jak ten](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) , który tworzy aplikację funkcji w systemie Windows w planie zużycia. Zamień na `<function-app-name>` unikatową nazwę aplikacji funkcji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat sposobu tworzenia i konfigurowania Azure Functions.

* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Jak skonfigurować ustawienia aplikacji funkcji platformy Azure](functions-how-to-use-azure-function-app-settings.md)
* [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Aplikacja funkcji w planie zużycia]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Aplikacja funkcji na Azure App Service planie]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json