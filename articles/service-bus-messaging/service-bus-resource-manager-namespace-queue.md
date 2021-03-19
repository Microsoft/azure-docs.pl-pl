---
title: Tworzenie Azure Service Bus przestrzeni nazw i kolejki przy użyciu szablonu platformy Azure
description: 'Szybki Start: tworzenie przestrzeni nazw Service Bus i kolejki przy użyciu szablonu Azure Resource Manager'
documentationcenter: .net
author: spelluru
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 69cffb6000df9d8b058d92231c130ea8a601e6d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88660635"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>Szybki Start: tworzenie przestrzeni nazw Service Bus i kolejki przy użyciu szablonu ARM

W tym artykule pokazano, jak używać szablonu Azure Resource Manager (szablon ARM), który tworzy Service Bus przestrzeni nazw i kolejki w tej przestrzeni nazw. W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane, oraz jak definiować parametry, które są określone podczas wdrażania. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json":::

Zasoby zdefiniowane w szablonie obejmują:

- [**Microsoft. ServiceBus/przestrzenie nazw**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft. ServiceBus/przestrzenie nazw/kolejki**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Następujące szablony usługi ARM są dostępne do pobrania i wdrożenia.
>
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu reguły kolejki i autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie przestrzeni nazw Service Bus przy użyciu tematu i subskrypcji](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie przestrzeni nazw Service Bus z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)

Możesz znaleźć więcej szablonów na podstawie [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Za pomocą tego szablonu należy wdrożyć przestrzeń nazw Service Bus z kolejką.

[Kolejki Service Bus](service-bus-queues-topics-subscriptions.md#queues) oferują dostarczanie komunikatów First In, First Out (FIFO) do jednego lub większej liczby konkurujących klientów.

Aby automatycznie uruchomić wdrożenie, kliknij następujący przycisk: Utwórz nową grupę zasobów dla wdrożenia, aby można było ją łatwo oczyścić później.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Wybierz pozycję **powiadomienia** u góry, aby wyświetlić stan wdrożenia. Poczekaj, aż wdrożenie zakończy się pomyślnie. Następnie wybierz pozycję **Przejdź do grupy zasobów** w komunikacie powiadomienia, aby przejść do strony dla grupy zasobów zawierającej Service Bus przestrzeni nazw. 

    ![Powiadomienie z wdrożenia](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Upewnij się, że przestrzeń nazw Service Bus została wyświetlona na liście zasobów. 

    ![Grupa zasobów — przestrzeń nazw](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Wybierz przestrzeń nazw z listy, aby wyświetlić stronę **Service Bus przestrzeni nazw** . 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

1. W Azure Portal przejdź do strony **grupy zasobów** dla grupy zasobów.
2. Wybierz pozycję **Usuń grupę zasobów** z paska narzędzi. 
3. Wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**. 

    ![Grupa zasobów — usuwanie](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Następne kroki

Zobacz następujący temat, który pokazuje, jak utworzyć regułę autoryzacji dla przestrzeni nazw/kolejki:

[Tworzenie reguły autoryzacji Service Bus dla przestrzeni nazw i kolejki przy użyciu szablonu ARM](service-bus-resource-manager-namespace-auth-rule.md)

Dowiedz się, jak zarządzać tymi zasobami, wyświetlając następujące artykuły:

* [Zarządzanie magistralą usług za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie zasobami Service Bus za pomocą Eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md