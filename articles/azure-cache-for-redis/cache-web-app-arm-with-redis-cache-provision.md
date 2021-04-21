---
title: Aprowizuj aplikację internetową za pomocą Azure Cache for Redis
description: Użyj Azure Resource Manager, aby wdrożyć aplikację internetową za pomocą Azure Cache for Redis.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 089d7d7990f0f94135f629a5cd7a461700aa3433
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830776"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Tworzenie aplikacji internetowej i Azure Cache for Redis przy użyciu szablonu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie dowiesz się, jak utworzyć szablon Azure Resource Manager, który wdraża aplikację internetową platformy Azure za pomocą Azure Cache for Redis. Dowiesz się, jak definiować wdrażane zasoby i definiować parametry określone podczas wykonywania wdrożenia. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Authoring Azure Resource Manager Templates](../azure-resource-manager/templates/template-syntax.md)(Tworzenie Azure Resource Manager szablonów). Aby dowiedzieć się więcej o składni i właściwościach JSON dla typów zasobów pamięci podręcznej, zobacz [Typy zasobów Microsoft.Cache.](/azure/templates/microsoft.cache/allversions)

Aby uzyskać kompletny szablon, zobacz [Web App with Azure Cache for Redis template (Aplikacja internetowa z Azure Cache for Redis szablonu).](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json)

## <a name="what-you-will-deploy"></a>Co wdrożysz
W tym szablonie wdrożysz:

* Aplikacja internetowa platformy Azure
* Azure Cache for Redis

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Parametry do określenia
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Zmienne nazw
Ten szablon używa zmiennych do konstruowania nazw zasobów. Używa on funkcji [uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) do konstruowania wartości na podstawie identyfikatora grupy zasobów.

```json
"variables": {
  "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
  "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
  "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
},
```


## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Tworzy Azure Cache for Redis, który jest używany z aplikacją internetową. Nazwa pamięci podręcznej jest określona w zmiennej **cacheName.**

Szablon tworzy pamięć podręczną w tej samej lokalizacji co grupa zasobów.

```json
{
  "name": "[variables('cacheName')]",
  "type": "Microsoft.Cache/Redis",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-08-01",
  "dependsOn": [ ],
  "tags": {
    "displayName": "cache"
  },
  "properties": {
    "sku": {
      "name": "[parameters('cacheSKUName')]",
      "family": "[parameters('cacheSKUFamily')]",
      "capacity": "[parameters('cacheSKUCapacity')]"
    }
  }
}
```


### <a name="web-app"></a>Aplikacja internetowa
Tworzy aplikację internetową o nazwie określonej w zmiennej **webSiteName.**

Zwróć uwagę, że aplikacja internetowa jest skonfigurowana przy użyciu właściwości ustawień aplikacji, które umożliwiają jej pracę z Azure Cache for Redis. Te ustawienia aplikacji są tworzone dynamicznie na podstawie wartości podanych podczas wdrażania.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
    "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
      }
    }
  ]
}
```

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
