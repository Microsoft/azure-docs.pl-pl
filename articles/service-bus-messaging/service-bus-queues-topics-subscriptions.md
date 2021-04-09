---
title: Azure Service Bus Messaging — kolejki, tematy i subskrypcje
description: Ten artykuł zawiera omówienie jednostek obsługi komunikatów Azure Service Bus (kolejek, tematów i subskrypcji).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: b8fb68509ad920fc6911290377f49b89ec610b58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101096324"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Kolejki, tematy i subskrypcje usługi Service Bus
Azure Service Bus obsługuje zestaw opartych na chmurze technologii oprogramowania pośredniczącego, w tym niezawodnej usługi kolejkowania komunikatów i trwałego przesyłania komunikatów publikowania/subskrybowania. Te możliwości obsługi komunikatów obsługiwanych przez brokera mogą być traktowane jako funkcje obsługi komunikatów, które obsługują publikowanie-subskrybowanie, oddzielanie danych czasowych i scenariusze równoważenia obciążenia przy użyciu obciążenia Service Bus Messaging. Komunikacja oddzielona ma wiele zalet. Na przykład klienci i serwery mogą łączyć się w razie potrzeby i wykonywać operacje w sposób asynchroniczny.

Jednostki obsługi komunikatów, które tworzą rdzeń możliwości obsługi komunikatów w Service Bus są **kolejkami**, **tematami i subskrypcjami** oraz regułami/akcjami.

## <a name="queues"></a>Kolejki
Kolejki zapewniają dostarczanie wiadomości **najpierw w pierwszej kolejności** (FIFO) do jednego lub większej liczby konkurujących klientów. Oznacza to, że odbiorcy zazwyczaj odbierają i przetwarzają komunikaty w kolejności, w której zostały dodane do kolejki. Tylko jeden odbiorca wiadomości odbiera i przetwarza każdy komunikat. Kluczową zaletą korzystania z kolejek jest osiągnięcie czasowego oddzielenia **składników aplikacji**. Innymi słowy, producenci (nadawcy) i konsumenci (odbiorcy) nie muszą jednocześnie wysyłać i odbierać wiadomości. Wynika to z faktu, że komunikaty są przechowywane trwale w kolejce. Ponadto producent nie musi czekać na odpowiedź od konsumenta, aby dalej przetwarzać i wysyłać komunikaty.

Powiązana korzyść to **poziom obciążenia**, który umożliwia producentom i konsumentom wysyłanie i odbieranie komunikatów przy różnych stawkach. W wielu aplikacjach obciążenie systemu zmienia się w miarę upływu czasu. Jednak czas przetwarzania wymagany dla każdej jednostki pracy jest zwykle stały. Producenci komunikatów Intermediating i konsumenci z kolejką oznacza, że zużywana aplikacja musi mieć możliwość obsługi średniego obciążenia zamiast szczytowego obciążenia. Głębokość kolejki rośnie i zmniejsza się w zależności od zmian obciążenia przychodzącego. Ta funkcja bezpośrednio zapisuje pieniądze w odniesieniu do wielkości infrastruktury wymaganej do obsługi obciążenia aplikacji. W miarę wzrostu obciążenia można dodać więcej procesów roboczych do odczytu z kolejki. Każdy komunikat jest przetwarzany tylko przez jeden z procesów roboczych. Ponadto ta funkcja równoważenia obciążenia opartego na ściąganiu umożliwia optymalne korzystanie z komputerów procesów roboczych, nawet w przypadku, gdy Komputery pracujące w systemie przetwarzają komunikaty ściągania zużywają ich własną maksymalną szybkość. Ten wzorzec jest często nazywany wzorcem **konkurujących konsumentów**.

Używanie kolejek do pośrednich między producentami i konsumentami komunikatów zapewnia nieodłączne, luźne sprzężenie między składnikami. Ze względu na to, że producenci i konsumenci nie wiedzą nawzajem, konsument może zostać uaktualniony bez wpływu na producenta.

### <a name="create-queues"></a>Tworzenie kolejek
Kolejki można tworzyć przy użyciu szablonów [Azure Portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [interfejsu wiersza polecenia](service-bus-quickstart-cli.md)lub [Menedżer zasobów](service-bus-resource-manager-namespace-queue.md). Następnie wysyłaj i odbieraj komunikaty przy użyciu klientów pisanych w [językach C#](service-bus-dotnet-get-started-with-queues.md), [Java](service-bus-java-how-to-use-queues.md), [Python](service-bus-python-how-to-use-queues.md), [JavaScript](service-bus-nodejs-how-to-use-queues.md), [php](service-bus-php-how-to-use-queues.md)i [Ruby](service-bus-ruby-how-to-use-queues.md). 

### <a name="receive-modes"></a>Tryby odbierania
Można określić dwa różne tryby, w których Service Bus odbiera komunikaty.

- **Odbierz i Usuń**. W tym trybie, gdy Service Bus otrzyma żądanie od konsumenta, oznacza komunikat jako używany i zwraca go do aplikacji konsumenta. Ten tryb jest najprostszym modelem. Najlepiej sprawdza się w scenariuszach, w których aplikacja może tolerować nieprzetwarzanie komunikatu w przypadku wystąpienia błędu. Aby zrozumieć ten scenariusz, Rozważmy scenariusz, w którym odbiorca wysyła żądanie odebrania, a następnie ulega awarii przed jego przetworzeniem. Jak Service Bus oznacza komunikat jako używany, aplikacja zacznie zużywać komunikaty po ponownym uruchomieniu. Spowoduje to odrzucenie wiadomości, która była używana przed awarią.
- **Zablokuj blokadę**. W tym trybie operacja odbierania staje się dwuetapowa, co umożliwia obsługę aplikacji, które nie mogą tolerować brakujących komunikatów. 
    1. Znajduje następny komunikat do użycia, **blokuje** go, aby uniemożliwić innym konsumentom otrzymywanie go, a następnie zwrócić komunikat do aplikacji. 
    1. Po zakończeniu przetwarzania komunikatu aplikacja zażąda usługi Service Bus, aby ukończyć drugi etap procesu odbierania. Następnie usługa **oznacza komunikat jako używany**. 

        Jeśli aplikacja nie może przetworzyć komunikatu z jakiegoś powodu, może zażądać usługi Service Bus, aby **porzucić** komunikat. Service Bus **odblokowywanie** wiadomości i udostępnienie jej do ponownego odbierania przez tego samego klienta lub przez innego użytkownika konkurującego. Po drugie, istnieje **limit czasu** skojarzony z blokadą. Jeśli aplikacja nie będzie przetwarzać komunikatu przed upływem limitu czasu blokady, Service Bus odblokowuje komunikat i udostępni go do ponownego odebrania.

        Jeśli aplikacja ulegnie awarii po przetworzeniu komunikatu, ale przed zażądaniem usługi Service Bus, aby zakończyć ten komunikat, Service Bus ponownie dostarczy komunikat do aplikacji po jej ponownym uruchomieniu. Ten proces jest często wywoływany **co najmniej raz podczas** przetwarzania. Oznacza to, że każdy komunikat jest przetwarzany co najmniej jeden raz. Jednak w niektórych sytuacjach ten sam komunikat może zostać dostarczony. Jeśli scenariusz nie może tolerować zduplikowanego przetwarzania, Dodaj dodatkową logikę w aplikacji, aby wykryć duplikaty. Aby uzyskać więcej informacji, zobacz [Wykrywanie duplikatów](duplicate-detection.md). Ta funkcja jest znana **dokładnie po** przetworzeniu.

        > [!NOTE]
        > Aby uzyskać więcej informacji na temat tych dwóch trybów, zobacz [rozliczanie operacji odbioru](message-transfers-locks-settlement.md#settling-receive-operations).

## <a name="topics-and-subscriptions"></a>Tematy i subskrypcje
Kolejka umożliwia przetwarzanie komunikatu przez pojedynczego konsumenta. W przeciwieństwie do kolejek, tematy i subskrypcje zapewniają formę komunikacji typu "jeden do wielu" w wzorcu **publikowania i subskrybowania** . Jest to przydatne w przypadku skalowania do dużej liczby odbiorców. Każdy opublikowany komunikat jest udostępniany każdej subskrypcji zarejestrowanej w temacie. Wydawca wysyła komunikat do tematu, a co najmniej jeden subskrybent otrzymuje kopię wiadomości, w zależności od reguł filtru ustawionych w tych subskrypcjach. Subskrypcje mogą używać dodatkowych filtrów, aby ograniczyć liczbę wiadomości, które mają zostać odebrane. Wydawcy wysyłają komunikaty do tematu w taki sam sposób, w jaki wysyłają komunikaty do kolejki. Jednak konsumenci nie odbierają wiadomości bezpośrednio z tematu. Zamiast tego odbiorcy odbierają wiadomości z subskrypcji tematu. Subskrypcja tematu jest podobna do kolejki wirtualnej, która odbiera kopie komunikatów wysyłanych do tematu. Odbiorcy odbierają wiadomości z subskrypcji identycznie do sposobu, w jaki odbierają komunikaty z kolejki.

Funkcja wysyłania komunikatów w kolejce mapuje się bezpośrednio do tematu, a jego funkcja otrzymywania komunikatów jest mapowana na subskrypcję. Ta funkcja oznacza między innymi, że subskrypcje obsługują te same wzorce opisane wcześniej w tej sekcji, w odniesieniu do kolejek: konkurujących klientów, oddzielenia czasowego, równoważenia obciążenia i równoważenie obciążenia.

### <a name="create-topics-and-subscriptions"></a>Tworzenie tematów i subskrypcji
Tworzenie tematu jest podobne do tworzenia kolejki, zgodnie z opisem w poprzedniej sekcji. Tematy i subskrypcje można tworzyć przy użyciu szablonów [Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [interfejsu wiersza polecenia](service-bus-tutorial-topics-subscriptions-cli.md)lub [Menedżer zasobów](service-bus-resource-manager-namespace-topic.md). Następnie Wysyłaj komunikaty do tematu i odbieraj komunikaty z subskrypcji przy użyciu klientów utworzonych w [językach C#](service-bus-dotnet-how-to-use-topics-subscriptions.md), [Java](service-bus-java-how-to-use-topics-subscriptions.md), [Python](service-bus-python-how-to-use-topics-subscriptions.md), [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md), [php](service-bus-php-how-to-use-topics-subscriptions.md)i [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md). 

### <a name="rules-and-actions"></a>Reguły i akcje
W wielu scenariuszach komunikaty o określonych cechach muszą być przetwarzane na różne sposoby. Aby włączyć to przetwarzanie, można skonfigurować subskrypcje w celu znalezienia komunikatów, które mają żądane właściwości, a następnie wykonać pewne modyfikacje tych właściwości. Chociaż subskrypcje Service Bus Zobacz wszystkie komunikaty wysłane do tematu, można skopiować tylko podzestaw tych komunikatów do kolejki subskrypcji wirtualnej. To filtrowanie jest realizowane przy użyciu filtrów subskrypcji. Takie modyfikacje są nazywane **akcjami filtrowania**. Po utworzeniu subskrypcji można podać wyrażenie filtru, które działa na właściwościach komunikatu. Właściwości mogą być zarówno właściwościami systemu (na przykład **etykieta**), jak i właściwościami aplikacji niestandardowych (na przykład **StoreName**). Wyrażenie filtru SQL jest w tym przypadku opcjonalne. Bez wyrażenia filtru SQL wszystkie akcje filtru zdefiniowane w ramach subskrypcji zostaną wykonane na wszystkich komunikatach dla tej subskrypcji.

Aby uzyskać pełny przykład pracy, zobacz [przykład TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) w witrynie GitHub.

Aby uzyskać więcej informacji na temat możliwych wartości filtru, zobacz dokumentację klas [sqlfilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) i [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) .

## <a name="java-message-service-jms-20-entities"></a>Jednostki usługi wiadomości Java (JMS) 2,0
Następujące jednostki są dostępne za pomocą interfejsu API usługi wiadomości Java (JMS) 2,0.

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


