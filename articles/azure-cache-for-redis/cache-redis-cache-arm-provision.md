---
title: Wdróż usługę Azure cache for Redis za pomocą Azure Resource Manager
description: Dowiedz się, jak użyć szablonu Azure Resource Manager do wdrożenia pamięci podręcznej platformy Azure dla zasobu Redis. Szablony są udostępniane dla typowych scenariuszy.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 0dbcbd173ce0a7c4c6a123f0644b870aa3cec2f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829894"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Tworzenie pamięci podręcznej platformy Azure dla usługi Redis przy użyciu szablonu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W tym temacie dowiesz się, jak utworzyć szablon Azure Resource Manager, który wdraża pamięć podręczną platformy Azure dla Redis. Pamięć podręczna może być używana z istniejącym kontem magazynu w celu przechowywania danych diagnostycznych. Dowiesz się również, jak definiować wdrożone zasoby i jak definiować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Obecnie ustawienia diagnostyczne są udostępniane dla wszystkich pamięci podręcznych w tym samym regionie dla subskrypcji. Aktualizacja jednej pamięci podręcznej w regionie ma wpływ na wszystkie inne pamięci podręczne w regionie.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Aby dowiedzieć się więcej na temat składni i właściwości JSON dla typów zasobów pamięci podręcznej, zobacz [Microsoft. cache — typy zasobów](/azure/templates/microsoft.cache/allversions).

Aby zapoznać się z pełnym szablonem, zobacz [pamięć podręczna Azure dla szablonu Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Dostępne są szablony Menedżer zasobów dla nowej [warstwy Premium](cache-premium-tier-intro.md) . 
> 
> * [Tworzenie pamięci podręcznej systemu Azure w warstwie Premium dla Redis przy użyciu klastrowania](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Tworzenie pamięci podręcznej systemu Azure w warstwie Premium dla Redis z trwałością danych](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Utwórz Redis Cache w warstwie Premium wdrożone w Virtual Network](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> Aby sprawdzić dostępność najnowszych szablonów, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates/) i Wyszukaj `Azure Cache for Redis` .
> 
> 

## <a name="what-you-will-deploy"></a>Co zostanie wdrożone
W tym szablonie zostanie wdrożona pamięć podręczna systemu Azure dla usługi Redis, która korzysta z istniejącego konta magazynu dla danych diagnostycznych.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry
Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie Parameters, która zawiera wszystkie wartości parametrów.
Parametr powinien obejmować wartości, które różnią się w zależności od wdrażanego projektu lub środowiska, w którym odbywa się wdrożenie. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Lokalizacja pamięci podręcznej platformy Azure dla Redis. Aby uzyskać najlepszą wydajność, Użyj tej samej lokalizacji co aplikacja, która ma być używana z pamięcią podręczną.

```json
  "redisCacheLocation": {
    "type": "string"
  }
```

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Nazwa istniejącego konta magazynu, które ma być używane na potrzeby diagnostyki. 

```json
  "existingDiagnosticsStorageAccountName": {
    "type": "string"
  }
```

### <a name="enablenonsslport"></a>enableNonSslPort
Wartość logiczna wskazująca, czy zezwalać na dostęp za pośrednictwem portów innych niż SSL.

```json
  "enableNonSslPort": {
    "type": "bool"
  }
```

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Wartość wskazująca, czy Diagnostyka jest włączona. Użyj opcji włączone lub wyłączone.

```json
  "diagnosticsStatus": {
    "type": "string",
    "defaultValue": "ON",
    "allowedValues": [
          "ON",
          "OFF"
      ]
  }
```

## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia
### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Tworzy pamięć podręczną platformy Azure dla Redis.

```json
  {
    "apiVersion": "2015-08-01",
    "name": "[parameters('redisCacheName')]",
    "type": "Microsoft.Cache/Redis",
    "location": "[parameters('redisCacheLocation')]",
    "properties": {
      "enableNonSslPort": "[parameters('enableNonSslPort')]",
      "sku": {
        "capacity": "[parameters('redisCacheCapacity')]",
        "family": "[parameters('redisCacheFamily')]",
        "name": "[parameters('redisCacheSKU')]"
      }
    },
    "resources": [
      {
        "apiVersion": "2017-05-01-preview",
        "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
        "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
        "location": "[parameters('redisCacheLocation')]",
        "dependsOn": [
          "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
        ],
        "properties": {
          "status": "[parameters('diagnosticsStatus')]",
          "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
        }
      }
    ]
  }
```

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
