---
title: Tworzenie tematu przestrzeni nazw Azure Service Bus przy użyciu szablonu
description: 'Szybki Start: tworzenie przestrzeni nazw Service Bus z tematem i subskrypcją przy użyciu szablonu Azure Resource Manager'
documentationcenter: .net
author: spelluru
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 706432c039bc38e9799828ae752d6ad0c92ffde3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704609"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Szybki Start: tworzenie przestrzeni nazw Service Bus z tematem i subskrypcją przy użyciu szablonu Azure Resource Manager

W tym artykule pokazano, jak używać szablonu Azure Resource Manager, który tworzy przestrzeń nazw Service Bus i temat i subskrypcję w tej przestrzeni nazw. W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane, oraz jak definiować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby uzyskać pełny szablon, zapoznaj się z [przestrzenią nazw Service Bus z tematem i][Service Bus namespace with topic and subscription] szablonem subskrypcji.

> [!NOTE]
> Następujące szablony Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu kolejki](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu reguły kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie przestrzeni nazw Service Bus z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź Galerię [szablonów szybkiego startu platformy Azure][Azure Quickstart Templates] i Wyszukaj **Service Bus**.

## <a name="what-do-you-deploy"></a>Co wdrażasz?

Za pomocą tego szablonu można wdrożyć przestrzeń nazw Service Bus z tematem i subskrypcją.

[Tematy Service Bus i subskrypcje](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) zapewniają formę komunikacji typu "jeden do wielu" w wzorcu *publikowania/subskrybowania* .

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` , która zawiera wszystkie wartości parametrów. Zdefiniuj parametr dla tych wartości, które różnią się w zależności od wdrażanego projektu lub w oparciu o środowisko, w którym wdrażasz. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Szablon definiuje następujące parametry:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Nazwa przestrzeni nazw Service Bus, która ma zostać utworzona.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Nazwa tematu utworzonego w przestrzeni nazw Service Bus.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Nazwa subskrypcji utworzonej w przestrzeni nazw Service Bus.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

Wersja szablonu interfejsu API Service Bus.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia

Tworzy standardową przestrzeń nazw Service Bus typu **Messaging** z tematem i subskrypcją.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

Aby poznać składnię i właściwości JSON, zobacz [obszary nazw](/azure/templates/microsoft.servicebus/namespaces), [Tematy](/azure/templates/microsoft.servicebus/namespaces/topics)i [subskrypcje](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions).

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az deployment group create \<my-resource-group\> --name \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Następne kroki

Teraz, po utworzeniu i wdrożeniu zasobów przy użyciu Azure Resource Manager, Dowiedz się, jak zarządzać tymi zasobami, wyświetlając następujące artykuły:

* [Zarządzanie magistralą usług za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami Service Bus za pomocą Eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
