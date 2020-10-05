---
title: 'Szybki Start: korzystanie z Azure Service Bus tematów i subskrypcji w języku Python'
description: W tym artykule opisano sposób tworzenia tematu Azure Service Bus, subskrypcji, wysyłania komunikatów do tematu i odbierania komunikatów z subskrypcji.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: f6d1b25cb502b8cb208ba5b59c91667e03c77778
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88064387"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Szybki Start: korzystanie z Service Bus tematów i subskrypcji w języku Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule opisano sposób korzystania z języka Python z tematami i subskrypcjami Azure Service Bus. Przykłady używają pakietu [Azure Python SDK][Azure Python package] , aby: 

- Tworzenie tematów i subskrypcji tematów
- Tworzenie filtrów i reguł subskrypcji
- Wysyłanie komunikatów do tematów 
- Odbieranie komunikatów z subskrypcji
- Usuwanie tematów i subskrypcji

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Przestrzeń nazw Service Bus utworzona przez wykonanie kroków opisanych w [sekcji szybki start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje](service-bus-quickstart-topics-subscriptions-portal.md). Skopiuj nazwę przestrzeni nazw, nazwę klucza dostępu współdzielonego i wartość klucza podstawowego z ekranu **zasad dostępu współdzielonego** do użycia w dalszej części tego przewodnika Szybki Start. 
- Program Python 3.4 x lub nowszy z zainstalowanym pakietem [SDK języka Python platformy Azure][Azure Python package] . Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji języka Python](/azure/developer/python/azure-sdk-install).

## <a name="create-a-servicebusservice-object"></a>Tworzenie obiektu ServiceBusService

Obiekt **ServiceBusService** umożliwia korzystanie z tematów i subskrypcji z tematami. Aby programowo uzyskać dostęp do Service Bus, Dodaj następujący wiersz w górnej części pliku języka Python:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Dodaj następujący kod, aby utworzyć obiekt **ServiceBusService** . Zastąp `<namespace>` `<sharedaccesskeyname>` wartości,, i nazwą `<sharedaccesskeyvalue>` obszaru nazw Service Bus, nazwę klucza sygnatury dostępu współdzielonego (SAS) i wartość klucza podstawowego. Te wartości można znaleźć w obszarze **zasady dostępu współdzielonego** w przestrzeni nazw Service Bus w [Azure Portal][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Tworzenie tematu

Poniższy kod używa `create_topic` metody do utworzenia tematu Service Bus o nazwie `mytopic` , z ustawieniami domyślnymi:

```python
bus_service.create_topic('mytopic')
```

Możesz użyć opcji tematu, aby przesłonić domyślne ustawienia tematu, takie jak czas wygaśnięcia komunikatu (TTL) lub maksymalny rozmiar tematu. Poniższy przykład tworzy temat o nazwie `mytopic` z maksymalnym rozmiarem tematu wynoszącym 5 GB i domyślnym czasem wygaśnięcia komunikatu o pojedynczej minucie:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji

Możesz również użyć obiektu **ServiceBusService** , aby utworzyć subskrypcje do tematów. Subskrypcja może mieć filtr, aby ograniczyć zestaw komunikatów dostarczonych do jego kolejki wirtualnej. Jeśli nie określisz filtru, nowe subskrypcje będą używały domyślnego filtru **MatchAll** , który umieści wszystkie komunikaty opublikowane w temacie do kolejki wirtualnej subskrypcji. Poniższy przykład tworzy subskrypcję `mytopic` o nazwie `AllMessages` , która używa filtru **MatchAll** :

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Używanie filtrów z subskrypcjami

Użyj `create_rule` metody obiektu **ServiceBusService** , aby odfiltrować komunikaty, które są wyświetlane w ramach subskrypcji. Możesz określić reguły podczas tworzenia subskrypcji lub dodać reguły do istniejących subskrypcji.

Najbardziej elastycznym typem filtru jest **xmlfilter**, który używa podzestawu SQL-92. Filtry SQL działają na podstawie właściwości komunikatów opublikowanych w temacie. Aby uzyskać więcej informacji na temat wyrażeń, których można używać z filtrem SQL, zobacz składnię [sqlfilter. sqlexpression][SqlFilter.SqlExpression] .

Ponieważ domyślny filtr **MatchAll** jest automatycznie stosowany do wszystkich nowych subskrypcji, należy usunąć go z subskrypcji, które mają być filtrowane, lub **MatchAll** przesłonić wszystkie inne określone filtry. Można usunąć regułę domyślną przy użyciu `delete_rule` metody obiektu **ServiceBusService** .

Poniższy przykład tworzy subskrypcję `mytopic` o nazwie `HighMessages` z regułą **sqlfilter** o nazwie `HighMessageFilter` . `HighMessageFilter`Reguła wybiera tylko komunikaty o `messageposition` właściwości niestandardowej większej niż 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Poniższy przykład tworzy subskrypcję `mytopic` o nazwie `LowMessages` z regułą **sqlfilter** o nazwie `LowMessageFilter` . `LowMessageFilter`Reguła wybiera tylko komunikaty o `messageposition` Właściwości mniejszej lub równej 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

W przypadku `AllMessages` , `HighMessages` , i `LowMessages` wszystkie w efekcie komunikaty wysyłane do `mytopic` są zawsze dostarczane do odbiorców `AllMessages` subskrypcji. Komunikaty są również selektywnie dostarczane do `HighMessages` `LowMessages` subskrypcji lub, w zależności od `messageposition` wartości właściwości komunikatu. 

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu

Aplikacje używają `send_topic_message` metody obiektu **ServiceBusService** do wysyłania komunikatów do tematu Service Bus.

Poniższy przykład wysyła pięć wiadomości testowych do `mytopic` tematu. `messageposition`Wartość właściwości niestandardowej zależy od iteracji pętli i określa, które subskrypcje odbierają wiadomości. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Limity rozmiaru komunikatów i przydziały

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów, które mogą być przechowywane w temacie, ale istnieje limit całkowitego rozmiaru komunikatów, które zawiera temat. Możesz zdefiniować rozmiar tematu podczas tworzenia z górnym limitem wynoszącym 5 GB. 

Aby uzyskać więcej informacji na temat przydziałów, zobacz [Service Bus przydziały][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji

Aplikacje używają `receive_subscription_message` metody w obiekcie **ServiceBusService** do odbierania komunikatów z subskrypcji. Poniższy przykład odbiera komunikaty z `LowMessages` subskrypcji i usuwa je podczas ich odczytywania:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Opcjonalny `peek_lock` parametr określa, `receive_subscription_message` czy Service Bus usuwa wiadomości z subskrypcji podczas ich odczytywania. Domyślnym trybem otrzymywania wiadomości jest *PeekLock*lub `peek_lock` ustawiona na **true**, który odczytuje (dokonuje wglądu) i blokuje komunikaty bez usuwania ich z subskrypcji. Każdy komunikat musi zostać jawnie ukończony, aby usunąć go z subskrypcji.

Aby usunąć wiadomości z subskrypcji podczas ich odczytywania, można ustawić `peek_lock` dla parametru **wartość false**, tak jak w poprzednim przykładzie. Usuwanie komunikatów w ramach operacji odbierania jest najprostszym modelem i działa prawidłowo, jeśli aplikacja może tolerować brakujące komunikaty w przypadku wystąpienia błędu. Aby zrozumieć to zachowanie, Rozważmy scenariusz, w którym aplikacja wystawia żądanie odebrania, a następnie uległa awarii przed jego przetworzeniem. Jeśli wiadomość została usunięta podczas odbierania, gdy aplikacja zostanie ponownie uruchomiona i rozpocznie korzystanie z komunikatów, nie dotarła do niej komunikat otrzymany przed awarią.

Jeśli aplikacja nie może tolerować pominiętych komunikatów, odbieranie zostanie operacją dwuetapową. PeekLock wyszukuje następny komunikat do użycia, blokuje go, aby uniemożliwić innym konsumentom otrzymywanie go i zwraca do aplikacji. Po przetworzeniu lub przechowywaniu komunikatu aplikacja wykonuje drugi etap procesu odbierania przez wywołanie `complete` metody dla obiektu **Message** .  `complete`Metoda oznacza komunikat jako używany i usuwa go z subskrypcji.

Poniższy przykład ilustruje scenariusz blokady wglądu:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Obsługa awarii aplikacji i nieczytelnych wiadomości

Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać `unlock` metodę dla obiektu **Message** . Service Bus odblokowywanie komunikatu w ramach subskrypcji i udostępnienie go do ponownego odebrania przez tę samą lub inną aplikację, która korzysta z aplikacji.

Istnieje również limit czasu dla komunikatów zablokowanych w ramach subskrypcji. Jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady, na przykład w przypadku awarii aplikacji Service Bus odblokowywanie komunikatu automatycznie i udostępnienie go do ponownego odebrania.

Jeśli aplikacja ulegnie awarii po przetworzeniu komunikatu, ale przed wywołaniem `complete` metody, komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Takie zachowanie jest często nazywane *przetwarzaniem co najmniej raz*. Każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach może zostać ponownie dostarczony ten sam komunikat. Jeśli w scenariuszu nie można tolerować zduplikowanego przetwarzania, można użyć właściwości **MessageID** komunikatu, która pozostaje stała między próbami dostarczenia, aby obsłużyć zduplikowane dostarczanie komunikatów. 

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji

Aby usunąć tematy i subskrypcje, użyj [Azure Portal][Azure portal] lub `delete_topic` metody. Poniższy kod usuwa temat o nazwie `mytopic` :

```python
bus_service.delete_topic('mytopic')
```

Usunięcie tematu powoduje usunięcie wszystkich subskrypcji do tematu. Subskrypcje można także usuwać niezależnie. Poniższy kod usuwa subskrypcję o nazwie `HighMessages` z `mytopic` tematu:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Domyślnie tematy i subskrypcje są trwałe i istnieją dopiero po ich usunięciu. Aby automatycznie usuwać subskrypcje po upływie określonego czasu, można ustawić parametr [auto_delete_on_idle](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) dla subskrypcji. 

> [!TIP]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Service Bus Explorer umożliwia łączenie się z przestrzenią nazw Service Bus i łatwe administrowanie jednostkami obsługi komunikatów. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu i możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Service Bus tematów, Skorzystaj z poniższych linków, aby dowiedzieć się więcej:

* [Kolejki, tematy i subskrypcje][Queues, topics, and subscriptions]
* [Sqlfilter. sqlexpression][SqlFilter.SqlExpression] Reference

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md