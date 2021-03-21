---
title: Tworzenie Service Bus tematów i subskrypcji za pomocą Azure Portal
description: 'Szybki Start: w tym przewodniku szybki start dowiesz się, jak utworzyć temat Service Bus i subskrypcje w tym temacie przy użyciu Azure Portal.'
author: spelluru
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 749ba3d2947dfd7defdf2549305f2a61c16ba713
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95803282"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Używanie witryny Azure Portal do tworzenia tematu usługi Service Bus i subskrybowania tematu
W tym przewodniku szybki start użyjesz Azure Portal do utworzenia tematu Service Bus, a następnie tworzenia subskrypcji w tym temacie. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co to są tematy i subskrypcje usługi Service Bus?
Tematy i subskrypcje usługi Service Bus obsługują model komunikacji z użyciem *publikowania/subskrypcji* komunikatów. Podczas korzystania z tematów i subskrypcji składniki aplikacji rozproszonej nie komunikują się bezpośrednio ze sobą, lecz wymieniają komunikaty za pośrednictwem tematu, która działa jako pośrednik.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

W przeciwieństwie do kolejek Service Bus, w których każdy komunikat jest przetwarzany przez jednego konsumenta, tematy i subskrypcje zapewniają formę komunikacji typu "jeden do wielu" przy użyciu wzorca publikowania/subskrybowania. Istnieje możliwość zarejestrowania wielu subskrypcji danego tematu. Po wysłaniu komunikatu do tematu jest on następnie udostępniany poszczególnym subskrypcjom w celu niezależnej obsługi lub niezależnego przetworzenia. Subskrypcja tematu przypomina wirtualną kolejkę, która odbiera kopie komunikatów wysłanych do tematu. Opcjonalnie można zarejestrować reguły filtru dla tematu w oparciu o subskrypcję, co umożliwia filtrowanie lub ograniczanie komunikatów do tematu otrzymywanych przez poszczególne subskrypcje tematów.

Tematy Service Bus i subskrypcje umożliwiają skalowanie do przetwarzania dużej liczby komunikatów w dużej liczbie użytkowników i aplikacji.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono Service Bus przestrzeń nazw, temat w przestrzeni nazw oraz trzy subskrypcje w temacie. Aby dowiedzieć się, jak publikować komunikaty w temacie i subskrybować wiadomości z subskrypcji, zobacz jeden z następujących przewodników szybki start w sekcji **Publikowanie i subskrybowanie komunikatów** . 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
- [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)