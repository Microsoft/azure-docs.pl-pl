---
title: Skorzystaj z Azure Portal, aby tworzyć Service Bus i subskrypcje
description: 'Szybki start: w tym przewodniku Szybki start dowiesz się, jak utworzyć Service Bus tematu i subskrypcji tego tematu przy użyciu Azure Portal.'
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: 25b0579f05c1f7669a8dfc1df02e9a1f575ea066
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537145"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Używanie witryny Azure Portal do tworzenia tematu usługi Service Bus i subskrybowania tematu
W tym przewodniku Szybki start użyjemy Azure Portal tematu, a Service Bus tematu, a następnie utworzysz subskrypcje tego tematu. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co to są tematy i subskrypcje usługi Service Bus?
Tematy i subskrypcje usługi Service Bus obsługują model komunikacji z użyciem *publikowania/subskrypcji* komunikatów. Podczas korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, lecz wymieniają komunikaty za pośrednictwem tematu, która działa jako pośrednik.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

W przeciwieństwie do Service Bus, w których każdy komunikat jest przetwarzany przez jednego konsumenta, tematy i subskrypcje zapewniają formę komunikacji jeden do wielu przy użyciu wzorca publikowania/subskrybowania. Istnieje możliwość zarejestrowania wielu subskrypcji danego tematu. Po wysłaniu komunikatu do tematu jest on następnie udostępniany poszczególnym subskrypcjom w celu niezależnej obsługi lub niezależnego przetworzenia. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Opcjonalnie można zarejestrować reguły filtrowania dla tematu dla 1 subskrypcji, co umożliwia filtrowanie lub ograniczanie komunikatów do tematu otrzymywanych przez które subskrypcje tematów.

Service Bus tematy i subskrypcje umożliwiają skalowanie w celu przetwarzania dużej liczby komunikatów dla dużej liczby użytkowników i aplikacji.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Za pomocą Service Bus można zarządzać [zasobami Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Usługa Service Bus Explorer umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie udostępnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono przestrzeń Service Bus, temat w przestrzeni nazw i trzy subskrypcje tematu. Aby dowiedzieć się, jak publikować komunikaty w temacie i subskrybować komunikaty z subskrypcji, zobacz jeden z następujących przewodników Szybki start w sekcji Publikowanie i **subskrybowanie komunikatów.** 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
- [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)
