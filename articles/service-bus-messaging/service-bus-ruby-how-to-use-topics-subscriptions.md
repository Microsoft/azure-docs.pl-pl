---
title: 'Szybki Start: jak używać tematów Service Bus (Ruby)'
description: 'Szybki Start: informacje dotyczące korzystania z Service Bus tematów i subskrypcji na platformie Azure. Przykłady kodu są zapisywane dla aplikacji Ruby.'
services: service-bus-messaging
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: aba326a63558632bee3bf0c48d34e471bbe30886
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88067566"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Szybki Start: jak używać tematów Service Bus i subskrypcji za pomocą języka Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule opisano sposób korzystania z Service Bus tematów i subskrypcji z aplikacji Ruby. Omówione scenariusze obejmują:

- Tworzenie tematów i subskrypcji 
- Tworzenie filtrów subskrypcji 
- Wysyłanie komunikatów do tematu 
- Otrzymywanie komunikatów z subskrypcji
- Usuwanie tematów i subskrypcji


## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Wykonaj kroki opisane w [przewodniku szybki start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie](service-bus-quickstart-topics-subscriptions-portal.md) w celu utworzenia **przestrzeni nazw** Service Bus i pobrania **parametrów połączenia**. 

    > [!NOTE]
    > W tym przewodniku szybki start utworzysz **temat** i **subskrypcję** tematu przy użyciu języka **Ruby** . 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Tworzenie tematu
Obiekt **Azure:: ServiceBusService** umożliwia współpracę z tematami. Poniższy kod tworzy obiekt **Azure:: ServiceBusService** . Aby utworzyć temat, użyj `create_topic()` metody. Poniższy przykład tworzy temat lub drukuje błędy.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Można również przekazać obiekt **Azure:: ServiceBus:: tematu** z dodatkowymi opcjami, które umożliwiają przesłonięcie domyślnych ustawień tematu, takich jak czas wiadomości na żywo lub maksymalny rozmiar kolejki. W poniższym przykładzie przedstawiono ustawienie maksymalnego rozmiaru kolejki na 5 GB i czas wygaśnięcia na 1 minutę:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji
Subskrypcje tematów są również tworzone za pomocą obiektu **Azure:: ServiceBusService** . Subskrypcje mają nazwę i mogą mieć opcjonalny filtr, który ogranicza zestaw komunikatów dostarczonych do kolejki wirtualnej subskrypcji.

Domyślnie subskrypcje są trwałe. Są one nadal dostępne do momentu, aż lub temat, z którym są skojarzone, zostaną usunięte. Jeśli aplikacja zawiera logikę do utworzenia subskrypcji, należy najpierw sprawdzić, czy subskrypcja już istnieje przy użyciu metody getsubscription.

Subskrypcje mogą być automatycznie usuwane przez ustawienie [Właściwości AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
Jeśli podczas tworzenia nowej subskrypcji nie zostanie określony żaden filtr, zostanie użyty filtr **MatchAll** (domyślnie). Gdy filtr **MatchAll** jest używany, wszystkie komunikaty opublikowane w temacie są umieszczane w kolejce wirtualnej subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie "All-Messages" i używa domyślnego filtru **MatchAll** .

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami
Można również zdefiniować filtry, które umożliwiają określenie, które komunikaty wysyłane do tematu powinny być widoczne w ramach określonej subskrypcji.

Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest **platforma Azure:: ServiceBus:: sqlfilter**, która implementuje PODZESTAW elementu standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat wyrażeń, które mogą być używane z filtrem SQL, zobacz składnię [sqlfilter](service-bus-messaging-sql-filter.md) .

Filtry do subskrypcji można dodać przy użyciu `create_rule()` metody obiektu **Azure:: ServiceBusService** . Ta metoda umożliwia dodawanie nowych filtrów do istniejącej subskrypcji.

Ponieważ domyślny filtr jest automatycznie stosowany do wszystkich nowych subskrypcji, należy najpierw usunąć filtr domyślny lub **MatchAll** zastępuje wszystkie inne filtry, które można określić. Można usunąć regułę domyślną przy użyciu `delete_rule()` metody w obiekcie **Azure:: ServiceBusService** .

Poniższy przykład tworzy subskrypcję o nazwie "High-Messages" przy użyciu elementu **Azure:: ServiceBus:: sqlfilter** , który wybiera tylko komunikaty o `message_number` właściwości niestandardowej większej niż 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Podobnie Poniższy przykład tworzy subskrypcję o nazwie `low-messages` przy użyciu elementu **Azure:: ServiceBus:: sqlfilter** , który wybiera tylko komunikaty o `message_number` Właściwości mniejszej lub równej 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Gdy wiadomość jest teraz wysyłana do usługi `test-topic` , jest zawsze dostarczana do odbiorców, którzy zasubskrybują `all-messages` subskrypcję tematu, i wybiórczo dostarczane do odbiorników subskrybowanych w ramach subskrypcji `high-messages` `low-messages` tematu i (w zależności od zawartości wiadomości).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Aby wysłać komunikat do Service Bus tematu, aplikacja musi używać `send_topic_message()` metody w obiekcie **Azure:: ServiceBusService** . Komunikaty wysyłane do tematów Service Bus są wystąpieniami obiektów **Azure:: ServiceBus:: BrokeredMessage** . **Azure:: ServiceBus:: BrokeredMessage** obiekty mają zestaw właściwości standardowych (takich jak `label` i `time_to_live` ), słownik używany do przechowywania niestandardowych właściwości specyficznych dla aplikacji i treść ciągu. Aplikacja może ustawić treść komunikatu przez przekazanie wartości ciągu do `send_topic_message()` metody, a wszystkie wymagane właściwości standardowe są wypełniane wartościami domyślnymi.

W poniższym przykładzie pokazano, jak wysłać pięć wiadomości testowych do `test-topic` . `message_number`Wartość właściwości niestandardowej każdego komunikatu różni się w zależności od iteracji pętli (określa, którą subskrypcję otrzymuje):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Komunikaty są odbierane z subskrypcji przy użyciu `receive_subscription_message()` metody w obiekcie **Azure:: ServiceBusService** . Domyślnie komunikaty są odczytywane (szczytowe) i zablokowane bez usuwania ich z subskrypcji. Możesz odczytać i usunąć komunikat z subskrypcji, ustawiając `peek_lock` opcję na **false**.

Zachowanie domyślne polega na odczycie i usunięciu operacji dwuetapowej, co również umożliwia obsługę aplikacji, które nie mogą tolerować brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Gdy aplikacja zakończy przetwarzanie komunikatu (lub zapisuje ją w sposób niezawodny w przyszłości), kończy drugi etap procesu odbierania przez wywołanie `delete_subscription_message()` metody i podanie komunikatu, który ma zostać usunięty jako parametr. `delete_subscription_message()`Metoda oznacza komunikat jako używany i usuwa go z subskrypcji.

Jeśli `:peek_lock` parametr ma wartość **false**, odczytywanie i usuwanie wiadomości stanie się najprostszym modelem i sprawdza się najlepiej w scenariuszach, w których aplikacja może tolerować nie przetwarzać komunikatu w przypadku wystąpienia błędu. Rozważmy scenariusz, w którym odbiorca wysyła żądanie odebrania, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Service Bus oznaczył komunikat jako używany, wtedy, gdy aplikacja zostanie ponownie uruchomiona i rozpocznie korzystanie z komunikatów, zostanie pominięty komunikat, który był używany przed awarią.

W poniższym przykładzie pokazano, jak komunikaty mogą być odbierane i przetwarzane przy użyciu `receive_subscription_message()` . Przykład najpierw odbiera i usuwa komunikat z `low-messages` subskrypcji przy użyciu opcji `:peek_lock` Ustaw na **wartość false**, a następnie otrzymuje kolejną wiadomość od, `high-messages` a następnie usuwa komunikat przy użyciu `delete_subscription_message()` :

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać `unlock_subscription_message()` metodę w obiekcie **Azure:: ServiceBusService** . Powoduje Service Bus odblokowania wiadomości w ramach subskrypcji i udostępnienie jej do ponownego odebrania przez tę samą aplikację lub przez inną aplikację, która korzysta z aplikacji.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w ramach subskrypcji i jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład jeśli awaria aplikacji), Service Bus odblokować komunikat automatycznie i udostępnić go ponownie.

W przypadku awarii aplikacji po przetworzeniu komunikatu, ale przed `delete_subscription_message()` wywołaniem metody, komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Często nazywa się to *co najmniej raz na przetwarzanie*; oznacza to, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach może zostać ponownie dostarczony ten sam komunikat. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Ta logika jest często osiągana przy użyciu `message_id` Właściwości komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Tematy i subskrypcje są trwałe, chyba że [Właściwość AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) jest ustawiona. Można je usunąć za pomocą [Azure Portal][Azure portal] lub programowo. Poniższy przykład ilustruje sposób usuwania tematu o nazwie `test-topic` .

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem. Subskrypcje mogą być również usuwane niezależnie. Poniższy kod ilustruje sposób usuwania subskrypcji o nazwie `high-messages` z `test-topic` tematu:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy Service Bus tematów, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Zobacz [kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md).
* Dokumentacja interfejsów API dla elementu [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Odwiedź witrynę [Azure SDK dla repozytorium Ruby](https://github.com/Azure/azure-sdk-for-ruby) w serwisie GitHub.

[Azure portal]: https://portal.azure.com