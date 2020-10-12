---
title: Azure Service Bus Messaging — kolejki, tematy i subskrypcje
description: Ten artykuł zawiera omówienie jednostek obsługi komunikatów Azure Service Bus (kolejek, tematów i subskrypcji).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3ee03fe5219736a1b1ca66c652fe6ac410cb40cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87799620"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Kolejki, tematy i subskrypcje usługi Service Bus

Microsoft Azure Service Bus obsługuje zestaw opartych na chmurze technologii oprogramowania pośredniczącego, w tym niezawodnej usługi kolejkowania komunikatów i trwałego przesyłania komunikatów publikowania/subskrybowania. Te możliwości obsługi komunikatów obsługiwanych przez brokera mogą być traktowane jako funkcje obsługi komunikatów, które obsługują publikowanie-subskrybowanie, oddzielanie danych czasowych i scenariusze równoważenia obciążenia przy użyciu obciążenia Service Bus Messaging. Komunikacja oddzielona ma wiele zalet, na przykład klienci i serwery mogą nawiązywać połączenie zgodnie z potrzebami i wykonywać ich operacje w sposób asynchroniczny.

Jednostki obsługi komunikatów, które tworzą rdzeń możliwości obsługi komunikatów w Service Bus są kolejkami, tematami i subskrypcjami oraz regułami/akcjami.

## <a name="queues"></a>Kolejki

Kolejki zapewniają dostarczanie wiadomości *najpierw w pierwszej kolejności* (FIFO) do jednego lub większej liczby konkurujących klientów. Oznacza to, że odbiorcy zazwyczaj odbierają i przetwarzają komunikaty w kolejności, w której zostały dodane do kolejki, a tylko jeden odbiorca wiadomości odbiera i przetwarza każdy komunikat. Kluczową zaletą korzystania z kolejek jest osiągnięcie "oddzielenia czasowego" składników aplikacji. Innymi słowy, producenci (nadawcy) i odbiorcy (odbiorcy) nie muszą jednocześnie wysyłać i odbierać wiadomości, ponieważ komunikaty są przechowywane trwale w kolejce. Ponadto producent nie musi czekać na odpowiedź od konsumenta w celu dalszego przetwarzania i wysyłania komunikatów.

Powiązana korzyść to "wyrównywanie obciążeń", co umożliwia producentom i konsumentom wysyłanie i odbieranie komunikatów przy różnych stawkach. W wielu aplikacjach obciążenie systemu jest zależne od czasu; Jednak czas przetwarzania wymagany dla każdej jednostki pracy jest zwykle stały. Producenci komunikatów Intermediating i konsumenci z kolejką oznacza, że zużywana aplikacja musi mieć możliwość obsługi średniego obciążenia zamiast szczytowego obciążenia. Głębokość kolejki rośnie i zmniejsza się w zależności od zmian obciążenia przychodzącego. Ta funkcja bezpośrednio zapisuje pieniądze w odniesieniu do wielkości infrastruktury wymaganej do obsługi obciążenia aplikacji. W miarę wzrostu obciążenia można dodać więcej procesów roboczych do odczytu z kolejki. Każdy komunikat jest przetwarzany tylko przez jeden z procesów roboczych. Ponadto ta funkcja równoważenia obciążenia opartego na ściąganiu umożliwia optymalne korzystanie z komputerów procesów roboczych, nawet jeśli komputery robocze różnią się w odniesieniu do mocy obliczeniowej, ponieważ ściągają komunikaty przy użyciu ich stawki maksymalnej. Ten wzorzec często określa wzór "konsument konkurujący".

Używanie kolejek do pośrednich między producentami i konsumentami komunikatów zapewnia nieodłączne, luźne sprzężenie między składnikami. Ze względu na to, że producenci i konsumenci nie różnią się od siebie, konsument może zostać uaktualniony bez wpływu na producenta.

### <a name="create-queues"></a>Tworzenie kolejek

Kolejki można tworzyć przy użyciu szablonów [Azure Portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [interfejsu wiersza polecenia](service-bus-quickstart-cli.md)lub [Menedżer zasobów](service-bus-resource-manager-namespace-queue.md). Następnie można wysyłać i odbierać komunikaty przy użyciu obiektu [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) .

Aby szybko dowiedzieć się, jak utworzyć kolejkę, a następnie wysyłać i odbierać komunikaty do i z kolejki, zobacz [Przewodniki Szybki Start](service-bus-quickstart-portal.md) dla każdej z tych metod. Aby zapoznać się z bardziej szczegółowym samouczkiem dotyczącym korzystania z kolejek, zobacz [Rozpoczynanie pracy z kolejkami Service Bus](service-bus-dotnet-get-started-with-queues.md).

Aby zapoznać się z przykładem roboczym, zobacz [przykład BasicSendReceiveUsingQueueClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) w witrynie GitHub.

### <a name="receive-modes"></a>Tryby odbierania

Można określić dwa różne tryby, w których Service Bus odbiera komunikaty: *ReceiveAndDelete* lub *PeekLock*. W trybie [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) operacja Receive jest jednobajtowa; oznacza to, że gdy Service Bus otrzyma żądanie od konsumenta, oznacza komunikat jako używany i zwraca go do aplikacji konsumenta. Tryb **ReceiveAndDelete** jest najprostszym modelem i najlepiej sprawdza się w scenariuszach, w których aplikacja może tolerować nie przetwarzać komunikatu w przypadku wystąpienia błędu. Aby zrozumieć ten scenariusz, Rozważmy scenariusz, w którym odbiorca wysyła żądanie odebrania, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Service Bus oznacza komunikat jako używany, gdy aplikacja jest ponownie uruchamiana i zaczyna zużywać komunikaty, zostanie pominięty komunikat, który był używany przed awarią.

W trybie [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) operacja Receive staje się dwuetapowa, co umożliwia obsługę aplikacji, które nie mogą tolerować brakujących komunikatów. Gdy Service Bus otrzyma żądanie, znajdzie następny komunikat do użycia, zablokuje go, aby uniemożliwić innym konsumentom odbieranie go, a następnie zwraca go do aplikacji. Gdy aplikacja zakończy przetwarzanie komunikatu (lub zapisuje ją w sposób niegodny w przyszłości), kończy drugi etap procesu odbierania przez wywołanie [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) w odebranej wiadomości. Gdy Service Bus widzi wywołanie **CompleteAsync** , oznacza komunikat jako używany.

Jeśli aplikacja nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać metodę [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) dla odebranego komunikatu (zamiast [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)). Ta metoda umożliwia Service Bus odblokowywanie wiadomości i udostępnienie jej do ponownego odbierania przez tego samego klienta lub przez innego konkurującego użytkownika. Po drugie, istnieje limit czasu skojarzony z blokadą i jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład w przypadku awarii aplikacji), Service Bus odblokować komunikat i udostępnić go ponownie (zasadniczo domyślnie wykonuje operację [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) ).

W przypadku awarii aplikacji po przetworzeniu komunikatu, ale przed wystawieniem żądania **CompleteAsync** komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Ten proces jest często wywoływany *co najmniej raz podczas* przetwarzania; oznacza to, że każdy komunikat jest przetwarzany co najmniej jeden raz. Jednak w niektórych sytuacjach ten sam komunikat może zostać dostarczony. Jeśli w scenariuszu nie można tolerować zduplikowanego przetwarzania, w aplikacji musi być wymagana dodatkowa logika umożliwiająca wykrycie duplikatów, które można osiągnąć na podstawie właściwości [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia. Ta funkcja jest znana *dokładnie po* przetworzeniu.

## <a name="topics-and-subscriptions"></a>Tematy i subskrypcje

W przeciwieństwie do kolejek, w których każdy komunikat jest przetwarzany przez jednego konsumenta, *Tematy* i *subskrypcje* zapewniają formę komunikacji typu "jeden do wielu" w wzorcu *publikowania/subskrybowania* . Przydaje się do skalowania do dużej liczby odbiorców, każdy opublikowany komunikat jest udostępniany każdej subskrypcji zarejestrowanej w temacie. Komunikaty są wysyłane do tematu i dostarczane do co najmniej jednej skojarzonej subskrypcji, w zależności od zasad filtrowania, które mogą być ustawiane dla poszczególnych subskrypcji. Subskrypcje mogą używać dodatkowych filtrów, aby ograniczyć liczbę wiadomości, które mają zostać odebrane. Komunikaty są wysyłane do tematu w taki sam sposób, w jaki są wysyłane do kolejki, ale wiadomości nie są odbierane bezpośrednio z tematu. Zamiast tego są one odbierane z subskrypcji. Subskrypcja tematu jest podobna do kolejki wirtualnej, która odbiera kopie komunikatów wysyłanych do tematu. Komunikaty są odbierane z subskrypcji identycznie w sposób, w jaki są odbierane z kolejki.

W wyniku porównania funkcja wysyłania komunikatów w kolejce jest mapowana bezpośrednio do tematu, a jej funkcja otrzymywania komunikatów jest mapowana na subskrypcję. Ta funkcja oznacza między innymi, że subskrypcje obsługują te same wzorce opisane wcześniej w tej sekcji, w odniesieniu do kolejek: konkurujących klientów, oddzielenia czasowego, równoważenia obciążenia i równoważenie obciążenia.

### <a name="create-topics-and-subscriptions"></a>Tworzenie tematów i subskrypcji

Tworzenie tematu jest podobne do tworzenia kolejki, zgodnie z opisem w poprzedniej sekcji. Następnie wysyłasz komunikaty przy użyciu klasy [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) . Aby odbierać komunikaty, należy utworzyć co najmniej jedną subskrypcję w temacie. Podobnie jak w przypadku kolejek, komunikaty są odbierane z subskrypcji przy użyciu obiektu [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) zamiast obiektu [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) . Utwórz klienta subskrypcji, przekazując nazwę tematu, nazwę subskrypcji i (opcjonalnie) tryb odbioru jako parametry.

Aby uzyskać pełny przykład pracy, zobacz [przykład BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient) w witrynie GitHub.

### <a name="rules-and-actions"></a>Reguły i akcje

W wielu scenariuszach komunikaty o określonych cechach muszą być przetwarzane na różne sposoby. Aby włączyć to przetwarzanie, można skonfigurować subskrypcje w celu znalezienia komunikatów, które mają żądane właściwości, a następnie wykonać pewne modyfikacje tych właściwości. Chociaż subskrypcje Service Bus Zobacz wszystkie komunikaty wysłane do tematu, można skopiować tylko podzestaw tych komunikatów do kolejki subskrypcji wirtualnej. To filtrowanie jest realizowane przy użyciu filtrów subskrypcji. Takie modyfikacje są nazywane *akcjami filtrowania*. Po utworzeniu subskrypcji można podać wyrażenie filtru, które działa na właściwościach komunikatu, zarówno właściwości systemu (na przykład **etykieta**), jak i niestandardowe właściwości aplikacji (na przykład **StoreName**). Wyrażenie filtru SQL jest w tym przypadku opcjonalne; bez wyrażenia filtru SQL wszystkie akcje filtru zdefiniowane w ramach subskrypcji będą wykonywane na wszystkich komunikatach dla tej subskrypcji.

Aby uzyskać pełny przykład pracy, zobacz [przykład TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) w witrynie GitHub.

Aby uzyskać więcej informacji na temat możliwych wartości filtru, zobacz dokumentację klas [sqlfilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) i [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) .

## <a name="java-message-service-jms-20-entities-preview"></a>Jednostki usługi wiadomości Java (JMS) 2,0 (wersja zapoznawcza)

Poniższe jednostki są dostępne za pomocą interfejsu API usługi wiadomości Java (JMS) 2,0.

  * Kolejki tymczasowe
  * Tematy tymczasowe
  * Udostępnione trwałe subskrypcje
  * Nieudostępniane trwałe subskrypcje
  * Udostępnione nietrwałe subskrypcje
  * Nieudostępniane subskrypcje nietrwałe

Dowiedz się więcej o [jednostkach JMS 2,0](java-message-service-20-entities.md) i sposobach [ich użycia](how-to-use-java-message-service-20.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji i przykłady używania Service Bus Messaging, zobacz następujące tematy zaawansowane:

* [Przegląd komunikatów Service Bus](service-bus-messaging-overview.md)
* [Szybki start: wysyłanie i odbieranie komunikatów przy użyciu witryny Azure Portal i platformy .NET](service-bus-quickstart-portal.md)
* [Samouczek: aktualizowanie magazynu przy użyciu witryny Azure Portal oraz tematów/subskrypcji](service-bus-tutorial-topics-subscriptions-portal.md)


