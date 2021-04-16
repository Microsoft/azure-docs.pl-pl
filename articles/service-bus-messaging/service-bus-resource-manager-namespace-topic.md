---
title: Tworzenie Azure Service Bus przestrzeni nazw przy użyciu szablonu
description: 'Szybki start: tworzenie przestrzeni nazw Service Bus tematem i subskrypcją przy użyciu Azure Resource Manager szablonu'
documentationcenter: .net
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.custom:
- devx-track-azurecli
- devx-track-azurepowershell
- mode-arm
ms.openlocfilehash: 9c235f8e056bf185267aa0bb493b71b897f3c510
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534924"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Szybki start: tworzenie Service Bus nazw z tematem i subskrypcją przy użyciu Azure Resource Manager szablonu

W tym artykule pokazano, jak używać szablonu Azure Resource Manager, który tworzy przestrzeń nazw Service Bus oraz temat i subskrypcję w ramach tej przestrzeni nazw. W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane, oraz jak zdefiniować parametry określone podczas wykonywania wdrożenia. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Kompletny szablon można znaleźć w temacie [przestrzeń Service Bus z szablonem tematu i][Service Bus namespace with topic and subscription] subskrypcji.

> [!NOTE]
> Następujące szablony Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie przestrzeni nazw Service Bus z kolejką](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie Service Bus nazw za pomocą kolejki i reguły autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie Service Bus nazw z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź galerię [Szablony szybkiego startu platformy Azure][Azure Quickstart Templates] i wyszukaj **Service Bus**.

## <a name="what-do-you-deploy"></a>Co wdrażasz?

Za pomocą tego szablonu wdrożysz przestrzeń nazw Service Bus tematu i subskrypcji.

[Service Bus tematy i subskrypcje](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) zapewniają formę komunikacji jeden do wielu we wzorcu *publikowania/subskrybowania.*

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o `Parameters` nazwie zawierającą wszystkie wartości parametrów. Zdefiniuj parametr dla tych wartości, które różnią się w zależności od wdrażanych projektów lub w zależności od środowiska, w którym wdrażasz. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Szablon definiuje następujące parametry:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Nazwa przestrzeni nazw Service Bus do utworzenia.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Nazwa tematu utworzonego w Service Bus nazw.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Nazwa subskrypcji utworzonej w przestrzeni nazw Service Bus nazw.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

Wersja Service Bus API szablonu.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Zasoby wymagające wdrożenia

Tworzy standardową przestrzeń Service Bus typu **Obsługa** komunikatów z tematem i subskrypcją.

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

Aby uzyskać informacje o składni i właściwościach JSON, zobacz [przestrzenie nazw,](/azure/templates/microsoft.servicebus/namespaces) [tematy](/azure/templates/microsoft.servicebus/namespaces/topics)i [subskrypcje](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions).

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

Teraz, po utworzeniu i wdrożeniu zasobów przy użyciu usługi Azure Resource Manager, dowiedz się, jak zarządzać tymi zasobami, wyświetlając następujące artykuły:

* [Zarządzanie magistralą usług za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie Service Bus zasobami za pomocą Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
