---
title: Tworzenie Azure Service Bus i kolejki przy użyciu szablonu platformy Azure
description: 'Szybki start: tworzenie przestrzeni nazw Service Bus kolejki przy użyciu Azure Resource Manager szablonu'
documentationcenter: .net
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 6577abf1900a166087ff63c331d7c0390fab1342
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535020"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>Szybki start: tworzenie Service Bus nazw i kolejki przy użyciu szablonu usługi ARM

W tym artykule pokazano, jak używać szablonu usługi Azure Resource Manager (arm), który tworzy Service Bus nazw i kolejkę w ramach tej przestrzeni nazw. W tym artykule wyjaśniono, jak określić, które zasoby są wdrażane, oraz jak zdefiniować parametry określone podczas wykonywania wdrożenia. Można użyć tego szablonu na potrzeby własnych wdrożeń lub dostosować go do konkretnych potrzeb.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json":::

Zasoby zdefiniowane w szablonie obejmują:

- [**Microsoft.ServiceBus/namespaces**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/namespaces/queues**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Następujące szablony usługi ARM są dostępne do pobrania i wdrożenia.
>
> * [Tworzenie Service Bus nazw za pomocą kolejki i reguły autoryzacji](service-bus-resource-manager-namespace-auth-rule.md)
> * [Tworzenie Service Bus nazw z tematem i subskrypcją](service-bus-resource-manager-namespace-topic.md)
> * [Tworzenie przestrzeni nazw usługi Service Bus](service-bus-resource-manager-namespace.md)
> * [Tworzenie Service Bus nazw z tematem, subskrypcją i regułą](service-bus-resource-manager-namespace-topic-with-rule.md)

Więcej szablonów można znaleźć w [szablonach szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Za pomocą tego szablonu wdrożysz przestrzeń Service Bus z kolejką.

[Service Bus oferują](service-bus-queues-topics-subscriptions.md#queues) dostarczanie komunikatów FIFO (first in, first out) do jednego lub większej liczby konkurencyjnych odbiorców.

Aby automatycznie uruchomić wdrożenie, kliknij następujący przycisk: Utwórz nową grupę zasobów dla wdrożenia, aby można było je łatwo wyczyścić później.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Wybierz **pozycję** Powiadomienia u góry, aby wyświetlić stan wdrożenia. Zaczekaj na powodzenie wdrożenia. Następnie wybierz pozycję **Przejdź do grupy zasobów** w komunikacie powiadomienia, aby przejść do strony grupy zasobów zawierającej Service Bus nazw. 

    ![Powiadomienie z wdrożenia](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Upewnij się, że Service Bus przestrzeni nazw na liście zasobów. 

    ![Grupa zasobów — przestrzeń nazw](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Wybierz przestrzeń nazw z listy, aby wyświetlić Service Bus **Przestrzeń nazw.** 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

1. W Azure Portal przejdź do strony **Grupa zasobów** dla grupy zasobów.
2. Wybierz pozycję **Usuń grupę zasobów** z paska narzędzi. 
3. Wpisz nazwę grupy zasobów, a następnie wybierz pozycję **Usuń.** 

    ![Grupa zasobów — usuwanie](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Następne kroki

Zobacz następujący temat, który pokazuje, jak utworzyć regułę autoryzacji dla przestrzeni nazw/kolejki:

[Tworzenie reguły autoryzacji Service Bus dla przestrzeni nazw i kolejki przy użyciu szablonu usługi ARM](service-bus-resource-manager-namespace-auth-rule.md)

Dowiedz się, jak zarządzać tymi zasobami, wyświetlając następujące artykuły:

* [Zarządzanie magistralą usług za pomocą programu PowerShell](service-bus-manage-with-ps.md)
* [Zarządzanie Service Bus zasobami za pomocą Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md
