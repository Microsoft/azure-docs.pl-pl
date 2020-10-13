---
title: Tworzenie zasobów Azure Service Bus przy użyciu szablonów
description: Automatyzacja tworzenia zasobów Service Bus za pomocą szablonów Azure Resource Manager
documentationcenter: .net
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df8a7fde9114f03521f0e57e072f81a867efcf39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89075260"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Tworzenie zasobów Service Bus przy użyciu szablonów Azure Resource Manager

W tym artykule opisano sposób tworzenia i wdrażania zasobów Service Bus przy użyciu szablonów Azure Resource Manager, programu PowerShell i dostawcy zasobów Service Bus.

Szablony Azure Resource Manager ułatwiają Definiowanie zasobów do wdrożenia w ramach rozwiązania oraz Określanie parametrów i zmiennych, które umożliwiają wprowadzanie wartości dla różnych środowisk. Szablon jest zapisywana w formacie JSON i składa się z wyrażeń, których można użyć do konstruowania wartości dla wdrożenia. Aby uzyskać szczegółowe informacje na temat pisania szablonów Azure Resource Manager i dyskusji w formacie szablonu, zobacz [Struktura i składnia szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md).

> [!NOTE]
> W przykładach w tym artykule pokazano, jak za pomocą Azure Resource Manager utworzyć Service Bus przestrzeni nazw i jednostki obsługi komunikatów (queue). Inne przykłady szablonów można znaleźć w [galerii szablonów szybkiego startu platformy Azure][Azure Quickstart Templates gallery] i wyszukać **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Szablony Menedżer zasobów Service Bus

Te Service Bus szablony Azure Resource Manager są dostępne do pobrania i wdrożenia. Kliknij poniższe linki, aby uzyskać szczegółowe informacje o każdej z nich, z linkami do szablonów w witrynie GitHub:

* [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
* [Tworzenie przestrzeni nazw Service Bus przy użyciu kolejki](service-bus-resource-manager-namespace-queue.md)
* [Tworzenie przestrzeni nazw Service Bus przy użyciu tematu i subskrypcji](service-bus-resource-manager-namespace-topic.md)
* [Tworzenie przestrzeni nazw Service Bus przy użyciu reguły kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
* [Tworzenie przestrzeni nazw Service Bus z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Wdrażanie przy użyciu programu PowerShell

Poniższa procedura opisuje sposób użycia programu PowerShell do wdrożenia szablonu Azure Resource Manager, który tworzy warstwę Standardowa Service Bus przestrzeni nazw oraz kolejki w tej przestrzeni nazw. Ten przykład jest oparty na [tworzeniu Service Bus przestrzeni nazw z](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) szablonem kolejki. Przybliżony przepływ pracy jest następujący:

1. Zainstaluj program PowerShell.
2. Utwórz szablon i (opcjonalnie) plik parametrów.
3. W programie PowerShell Zaloguj się do konta platformy Azure.
4. Utwórz nową grupę zasobów, jeśli taka nie istnieje.
5. Przetestuj wdrożenie.
6. W razie potrzeby ustaw tryb wdrożenia.
7. Wdróż szablon.

Aby uzyskać pełne informacje na temat wdrażania szablonów Azure Resource Manager, zobacz [wdrażanie zasobów za pomocą szablonów Azure Resource Manager][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Instalowanie programu PowerShell

Zainstaluj Azure PowerShell, postępując zgodnie z instrukcjami w temacie Rozpoczynanie [pracy z Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Tworzenie szablonu

Sklonuj repozytorium lub Skopiuj szablon [201-ServiceBus-CREATE-QUEUE](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) z usługi GitHub:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Utwórz plik parametrów (opcjonalnie)

Aby użyć opcjonalnego pliku parametrów, skopiuj plik [201-ServiceBus-CREATE-QUEUE](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) . Zastąp wartość `serviceBusNamespaceName` nazwą Service Bus przestrzeni nazw, która ma zostać utworzona w tym wdrożeniu, i Zastąp wartość `serviceBusQueueName` nazwą kolejki, którą chcesz utworzyć.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [parametrów](../azure-resource-manager/templates/parameter-files.md) .

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Zaloguj się do platformy Azure i ustaw subskrypcję platformy Azure

W wierszu polecenia programu PowerShell uruchom następujące polecenie:

```powershell
Connect-AzAccount
```

Zostanie wyświetlony monit o zalogowanie się do konta platformy Azure. Po zalogowaniu Uruchom następujące polecenie, aby wyświetlić dostępne subskrypcje:

```powershell
Get-AzSubscription
```

To polecenie zwraca listę dostępnych subskrypcji platformy Azure. Wybierz subskrypcję bieżącej sesji, uruchamiając następujące polecenie. Zamień na `<YourSubscriptionId>` Identyfikator GUID subskrypcji platformy Azure, której chcesz użyć:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Ustawianie grupy zasobów

Jeśli nie masz istniejącej grupy zasobów, Utwórz nową grupę zasobów za pomocą polecenia **New-AzResourceGroup** . Podaj nazwę grupy zasobów i lokalizacji, której chcesz użyć. Na przykład:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Jeśli to się powiedzie, zostanie wyświetlona Podsumowanie nowej grupy zasobów.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testowanie wdrożenia

Sprawdź poprawność wdrożenia, uruchamiając `Test-AzResourceGroupDeployment` polecenie cmdlet. Podczas testowania wdrożenia podaj parametry dokładnie tak, jak podczas wykonywania wdrożenia.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Tworzenie wdrożenia

Aby utworzyć nowe wdrożenie, uruchom `New-AzResourceGroupDeployment` polecenie cmdlet i podaj wymagane parametry po wyświetleniu monitu. Parametry obejmują nazwę wdrożenia, nazwę grupy zasobów oraz ścieżkę lub adres URL pliku szablonu. Jeśli parametr **mode** nie zostanie określony, zostanie użyta wartość domyślna **przyrostu** . Aby uzyskać więcej informacji, zobacz [wdrożenia przyrostowe i kompletne](../azure-resource-manager/templates/deployment-modes.md).

Następujące polecenie poprosi o trzy parametry w oknie programu PowerShell:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Aby zamiast tego określić plik parametrów, użyj następującego polecenia:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Można również użyć parametrów wbudowanych podczas uruchamiania polecenia cmdlet wdrażania. Polecenie to wygląda następująco:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Aby uruchomić [kompletne](../azure-resource-manager/templates/deployment-modes.md) wdrożenie **, należy ustawić**parametr **mode** na:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia
Jeśli zasoby zostały wdrożone pomyślnie, w oknie programu PowerShell zostanie wyświetlone podsumowanie wdrożenia:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Następne kroki
Zaobserwowano podstawowy przepływ pracy i polecenia służące do wdrażania szablonu Azure Resource Manager. Aby uzyskać szczegółowe informacje, odwiedź następujące linki:

* [Przegląd Azure Resource Manager][Azure Resource Manager overview]
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)][Deploy resources with Azure Resource Manager templates]
* [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Typy zasobów Microsoft. ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
