---
title: Tworzenie reguły autoryzacji Service Bus przy użyciu szablonu platformy Azure
description: Tworzenie reguły autoryzacji Service Bus dla przestrzeni nazw i kolejki przy użyciu szablonu Azure Resource Manager
author: spelluru
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-azurecli
ms.openlocfilehash: b0ffe022f6c61cf7d06c510c4f8fa5c764d72b42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88067192"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Tworzenie reguły autoryzacji Service Bus dla przestrzeni nazw i kolejki przy użyciu szablonu Azure Resource Manager

W tym artykule pokazano, jak używać szablonu Azure Resource Manager, który tworzy [regułę autoryzacji](service-bus-authentication-and-authorization.md#shared-access-signature) dla Service Bus przestrzeni nazw i kolejki. W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane, oraz jak definiować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

Aby uzyskać więcej informacji na temat tworzenia szablonów, zobacz [Tworzenie szablonów Azure Resource Manager][Authoring Azure Resource Manager templates].

Aby zapoznać się z pełnym szablonem, zapoznaj się z [szablonem reguł autoryzacji Service Bus][Service Bus auth rule template] w witrynie GitHub.

> [!NOTE]
> Następujące szablony Azure Resource Manager są dostępne do pobrania i wdrożenia.
> 
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu kolejki](service-bus-resource-manager-namespace-queue.md)
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu tematu i subskrypcji](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie przestrzeni nazw Service Bus z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Aby sprawdzić najnowsze szablony, odwiedź Galerię [szablonów szybkiego startu platformy Azure][Azure Quickstart Templates] i Wyszukaj **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Co chcesz wdrożyć?

Ten szablon umożliwia wdrożenie reguły autoryzacji Service Bus dla przestrzeni nazw i jednostki obsługi komunikatów (w tym przypadku kolejki).

Ten szablon używa [sygnatury dostępu współdzielonego (SAS)](service-bus-sas.md) do uwierzytelniania. Sygnatura dostępu współdzielonego umożliwia aplikacjom uwierzytelnianie się w Service Bus przy użyciu klucza, który jest skonfigurowany w przestrzeni nazw, lub jednostki obsługi komunikatów (kolejki lub tematu), z którą są skojarzone określone prawa. Następnie można użyć tego klucza do wygenerowania tokenu SAS, którego klienci mogą używać w celu uwierzytelniania do Service Bus.

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametry

Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie `Parameters` , która zawiera wszystkie wartości parametrów. Należy zdefiniować parametr dla tych wartości, które będą się różnić w zależności od wdrażanego projektu lub w oparciu o środowisko, w którym wdrażasz. Nie należy definiować parametrów dla wartości, które zawsze pozostają takie same. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby.

Szablon zawiera definicje następujących parametrów.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Nazwa przestrzeni nazw Service Bus, która ma zostać utworzona.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName

Nazwa reguły autoryzacji dla przestrzeni nazw.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

Nazwa kolejki w przestrzeni nazw Service Bus.

```json
"serviceBusQueueName": {
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

Tworzy standardową przestrzeń nazw Service Bus typu **Messaging** oraz regułę autoryzacji Service Bus dla przestrzeni nazw i jednostki.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

Aby poznać składnię i właściwości JSON, zobacz [przestrzenie nazw](/azure/templates/microsoft.servicebus/namespaces), [kolejki](/azure/templates/microsoft.servicebus/namespaces/queues)i [reguł autoryzacji](/azure/templates/microsoft.servicebus/namespaces/authorizationrules).

## <a name="commands-to-run-deployment"></a>Polecenia umożliwiające uruchomienie wdrożenia

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Następne kroki

Teraz, po utworzeniu i wdrożeniu zasobów przy użyciu Azure Resource Manager, Dowiedz się, jak zarządzać tymi zasobami, wyświetlając następujące artykuły:

* [Zarządzanie magistralą usług za pomocą programu PowerShell](./service-bus-manage-with-ps.md)
* [Zarządzanie zasobami Service Bus za pomocą Eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Uwierzytelnianie i autoryzacja w usłudze Service Bus](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/