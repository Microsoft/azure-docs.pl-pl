---
title: Tworzenie Azure Service Bus przestrzeni nazw i kolejki przy użyciu szablonu platformy Azure
description: 'Szybki Start: tworzenie przestrzeni nazw Service Bus i kolejki przy użyciu szablonu Azure Resource Manager'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/30/2020
ms.author: spelluru
ms.openlocfilehash: b08253104eeb61f6bb09fde507473d235a996494
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80422678"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Szybki Start: tworzenie Service Bus przestrzeni nazw i kolejki przy użyciu szablonu Azure Resource Manager

W tym artykule pokazano, jak używać szablonu Azure Resource Manager, który tworzy przestrzeń nazw Service Bus i kolejkę w tej przestrzeni nazw. W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane, oraz jak definiować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Brak

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Tworzenie Service Bus przestrzeni nazw i kolejki

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

Zasoby zdefiniowane w szablonie obejmują:

- [**Microsoft. ServiceBus/przestrzenie nazw**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft. ServiceBus/przestrzenie nazw/kolejki**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Następujące szablony Azure Resource Manager są dostępne do pobrania i wdrożenia.
>
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu reguły kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu tematu i subskrypcji](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie przestrzeni nazw Service Bus z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)

Możesz znaleźć więcej szablonów na podstawie [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Za pomocą tego szablonu należy wdrożyć przestrzeń nazw Service Bus z kolejką.

[Kolejki Service Bus](service-bus-queues-topics-subscriptions.md#queues) oferują dostarczanie komunikatów First In, First Out (FIFO) do jednego lub większej liczby konkurujących klientów.

Aby automatycznie uruchomić wdrożenie, kliknij następujący przycisk: Utwórz nową grupę zasobów dla wdrożenia, aby można było ją łatwo oczyścić później.

[![Wdrażanie na platformie Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. Wybierz pozycję **powiadomienia** u góry, aby wyświetlić stan wdrożenia. Poczekaj, aż wdrożenie zakończy się pomyślnie. Następnie wybierz pozycję **Przejdź do grupy zasobów** w komunikacie powiadomienia, aby przejść do strony dla grupy zasobów zawierającej Service Bus przestrzeni nazw. 

    ![Powiadomienie z wdrożenia](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Upewnij się, że przestrzeń nazw Service Bus została wyświetlona na liście zasobów. 

    ![Grupa zasobów — przestrzeń nazw](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Wybierz przestrzeń nazw z listy, aby wyświetlić stronę **Service Bus przestrzeni nazw** . 

## <a name="cleanup-resources"></a>Oczyszczanie zasobów

1. W Azure Portal przejdź do strony **grupy zasobów** dla grupy zasobów.
2. Wybierz pozycję **Usuń grupę zasobów** na pasku narzędzi. 
3. Wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**. 

    ![Grupa zasobów — usuwanie](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Następne kroki

Zobacz następujący temat, który pokazuje, jak utworzyć regułę autoryzacji dla przestrzeni nazw/kolejki:

[Tworzenie reguły autoryzacji Service Bus dla przestrzeni nazw i kolejki przy użyciu szablonu Azure Resource Manager](service-bus-resource-manager-namespace-auth-rule.md)

Dowiedz się, jak zarządzać tymi zasobami, wyświetlając następujące artykuły:

* [Zarządzanie magistralą usług za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami Service Bus za pomocą Eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
