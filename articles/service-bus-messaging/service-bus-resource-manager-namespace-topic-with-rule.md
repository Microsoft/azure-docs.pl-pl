---
title: Tworzenie subskrypcji tematu Service Bus i reguły przy użyciu szablonu platformy Azure
description: Tworzenie Service Bus przestrzeni nazw z tematem, subskrypcją i regułą przy użyciu szablonu Azure Resource Manager
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3f3287dd67f89f678a9875ddce93e2d0d26b2209
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89077628"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Tworzenie Service Bus przestrzeni nazw z tematem, subskrypcją i regułą przy użyciu szablonu Azure Resource Manager

W tym artykule pokazano, jak używać szablonu Azure Resource Manager, który tworzy przestrzeń nazw Service Bus z tematem, subskrypcją i regułą (Filter). W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane, oraz jak definiować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów usługi Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby uzyskać więcej informacji na temat zasad i wzorców konwencji nazewnictwa zasobów platformy Azure, zobacz [zalecane konwencje nazewnictwa dla zasobów platformy Azure][Recommended naming conventions for Azure resources].

Aby uzyskać pełny szablon, zobacz [obszar nazw Service Bus z tematem, subskrypcją i][Service Bus namespace with topic, subscription, and rule] szablonem reguły.

> [!NOTE]
> Następujące szablony Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu reguły kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu kolejki](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu tematu i subskrypcji](service-bus-resource-manager-namespace-topic.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź Galerię [szablonów szybkiego startu platformy Azure][Azure Quickstart Templates] i Wyszukaj Service Bus.

## <a name="what-do-you-deploy"></a>Co wdrażasz?

Za pomocą tego szablonu można wdrożyć przestrzeń nazw Service Bus z tematem, subskrypcją i regułą (filtr).

[Tematy Service Bus i subskrypcje](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) zapewniają formę komunikacji typu "jeden do wielu" w wzorcu *publikowania/subskrybowania* . W przypadku korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, zamiast wymieniają komunikaty za pośrednictwem tematu, który działa jako pośrednik. Subskrypcja tematu jest podobna do kolejki wirtualnej, która otrzymuje kopie komunikatów, które zostały wysłane do tematu. Filtr subskrypcji pozwala określić, które komunikaty wysyłane do tematu powinny być wyświetlane w ramach subskrypcji określonego tematu.

## <a name="what-are-rules-filters"></a>Co to są reguły (filtry)?

W wielu scenariuszach komunikaty o określonych cechach muszą być przetwarzane na różne sposoby. Aby włączyć to przetwarzanie niestandardowe, można skonfigurować subskrypcje w celu znalezienia komunikatów, które mają określone właściwości, a następnie przeprowadzenia modyfikacji tych właściwości. Chociaż subskrypcje Service Bus Zobacz wszystkie komunikaty wysłane do tematu, można skopiować tylko podzestaw tych komunikatów do kolejki subskrypcji wirtualnej. Jest on realizowany przy użyciu filtrów subskrypcji. Aby dowiedzieć się więcej na temat reguł (filtrów), zobacz [reguły i akcje](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Za pomocą Azure Resource Manager Zdefiniuj parametry dla wartości, które chcesz określić podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` obejmującą wszystkie wartości parametrów. Zdefiniuj parametr dla tych wartości, które różnią się w zależności od wdrażanego projektu lub w oparciu o środowisko, w którym wdrażasz. Nie należy definiować parametrów dla wartości, które pozostają niezmienione. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

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

### <a name="servicebusrulename"></a>serviceBusRuleName

Nazwa reguły (filtru) utworzonej w przestrzeni nazw Service Bus.

```json
   "serviceBusRuleName": {
   "type": "string",
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

Tworzy standardową przestrzeń nazw Service Bus typu **Messaging** z tematem i subskrypcją i regułami.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
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
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

Aby poznać składnię i właściwości JSON, zobacz [obszary nazw](/azure/templates/microsoft.servicebus/namespaces), [Tematy](/azure/templates/microsoft.servicebus/namespaces/topics), [subskrypcje](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)i [reguły](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać tymi zasobami, wyświetlając następujące artykuły:

* [Zarządzaj Azure Service Bus](service-bus-management-libraries.md)
* [Zarządzanie magistralą usług za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami Service Bus za pomocą Eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
