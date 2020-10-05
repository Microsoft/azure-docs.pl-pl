---
title: 'Szybki Start: korzystanie z kolejek Azure Service Bus w języku Python'
description: W tym artykule pokazano, jak za pomocą języka Python tworzyć, wysyłać wiadomości do i odbierać komunikaty z kolejek Azure Service Bus.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: a09f20b2c392dbf219750a76e9570239227dc865
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89458565"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Szybki Start: korzystanie z kolejek Azure Service Bus w języku Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym artykule pokazano, jak za pomocą języka Python tworzyć, wysyłać wiadomości do i odbierać komunikaty z kolejek Azure Service Bus. 

Aby uzyskać więcej informacji na temat bibliotek Azure Service Bus Python, zobacz [biblioteki Service Bus dla języka Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Przestrzeń nazw Service Bus utworzona przez wykonanie kroków opisanych w [sekcji szybki start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje](service-bus-quickstart-topics-subscriptions-portal.md). Skopiuj podstawowe parametry połączenia z ekranu **zasad dostępu współdzielonego** do użycia w dalszej części tego artykułu. 
- Język Python 3.4 x lub nowszy z zainstalowanym pakietem [Azure Service Bus języka Python][Python Azure Service Bus package] . Aby uzyskać więcej informacji, zobacz [Przewodnik instalacji języka Python](/azure/developer/python/azure-sdk-install). 

## <a name="create-a-queue"></a>Tworzenie kolejki

Obiekt **ServiceBusClient** umożliwia korzystanie z kolejek. Aby programowo uzyskać dostęp do Service Bus, Dodaj następujący wiersz w górnej części pliku języka Python:

```python
from azure.servicebus import ServiceBusClient
```

Dodaj następujący kod, aby utworzyć obiekt **ServiceBusClient** . Zamień `<connectionstring>` na wartość parametrów połączenia podstawowego Service Bus. Tę wartość można znaleźć w obszarze **zasady dostępu współdzielonego** w przestrzeni nazw Service Bus w [Azure Portal][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

Poniższy kod używa `create_queue` metody **ServiceBusClient** , aby utworzyć kolejkę o nazwie `taskqueue` z ustawieniami domyślnymi:

```python
sb_client.create_queue("taskqueue")
```

Możesz użyć opcji, aby przesłonić domyślne ustawienia kolejki, takie jak czas wygaśnięcia komunikatu (TTL) lub maksymalny rozmiar tematu. Poniższy kod tworzy kolejkę o nazwie `taskqueue` z maksymalnym rozmiarem kolejki wynoszącym 5 GB i wartością TTL równą 1 minucie:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki

Aby wysłać komunikat do kolejki Service Bus, aplikacja wywołuje `send` metodę dla obiektu **ServiceBusClient** . Poniższy przykład kodu tworzy klienta kolejki i wysyła wiadomość testową do `taskqueue` kolejki. Zamień `<connectionstring>` na wartość parametrów połączenia podstawowego Service Bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Limity rozmiaru komunikatów i przydziały

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów, które mogą być przechowywane w kolejce, ale istnieje limit całkowitego rozmiaru komunikatów, które zawiera kolejka. Można zdefiniować rozmiar kolejki w czasie tworzenia, z górnym limitem wynoszącym 5 GB. 

Aby uzyskać więcej informacji na temat przydziałów, zobacz [Service Bus przydziały][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki

Klient kolejki otrzymuje komunikaty z kolejki przy użyciu `get_receiver` metody w obiekcie **ServiceBusClient** . Poniższy przykład kodu tworzy klienta kolejki i odbiera komunikat z `taskqueue` kolejki. Zamień `<connectionstring>` na wartość parametrów połączenia podstawowego Service Bus. 

```python
from azure.servicebus import QueueClient

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>Użyj parametru peek_lock

Opcjonalny `peek_lock` parametr określa, `get_receiver` czy Service Bus usuwa komunikaty z kolejki podczas ich odczytywania. Domyślnym trybem otrzymywania wiadomości jest *PeekLock*lub `peek_lock` ustawiona na **true**, który odczytuje (dokonuje wglądu) i blokuje komunikaty bez usuwania ich z kolejki. Każdy komunikat musi zostać jawnie ukończony, aby usunąć go z kolejki.

Aby usunąć wiadomości z kolejki podczas ich odczytywania, można ustawić `peek_lock` parametr `get_receiver` na **wartość false**. Usuwanie komunikatów w ramach operacji odbierania jest najprostszym modelem, ale działa tylko wtedy, gdy aplikacja może tolerować brakujące komunikaty w przypadku wystąpienia błędu. Aby zrozumieć to zachowanie, rozważ scenariusz, w którym odbiorca wysyła żądanie odebrania, a następnie ulega awarii przed jego przetworzeniem. Jeśli wiadomość została usunięta podczas odbierania, gdy aplikacja zostanie ponownie uruchomiona i rozpocznie korzystanie z komunikatów, nie dotarła do niej komunikat otrzymany przed awarią.

Jeśli aplikacja nie może tolerować pominiętych komunikatów, odbieranie jest operacją dwuetapową. PeekLock wyszukuje następny komunikat do użycia, blokuje go, aby uniemożliwić innym konsumentom otrzymywanie go i zwraca do aplikacji. Po przetworzeniu lub przechowywaniu komunikatu aplikacja wykonuje drugi etap procesu odbierania przez wywołanie `complete` metody dla obiektu **Message** .  `complete`Metoda oznacza komunikat jako używany i usuwa go z kolejki.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Obsługa awarii aplikacji i nieczytelnych wiadomości

Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać `unlock` metodę dla obiektu **Message** . Service Bus odblokowywanie komunikatu w kolejce i udostępnienie go do ponownego odebrania przez tę samą lub inną aplikację.

Istnieje również limit czasu dla komunikatów zablokowanych w kolejce. Jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady, na przykład w przypadku awarii aplikacji Service Bus odblokowywanie komunikatu automatycznie i udostępnienie go do ponownego odebrania.

Jeśli aplikacja ulegnie awarii po przetworzeniu komunikatu, ale przed wywołaniem `complete` metody, komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Takie zachowanie jest często nazywane *przetwarzaniem co najmniej raz*. Każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach może zostać ponownie dostarczony ten sam komunikat. Jeśli w scenariuszu nie można tolerować zduplikowanego przetwarzania, można użyć właściwości **MessageID** komunikatu, która pozostaje stała między próbami dostarczenia, aby obsłużyć zduplikowane dostarczanie komunikatów. 

> [!TIP]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Service Bus Explorer umożliwia łączenie się z przestrzenią nazw Service Bus i łatwe administrowanie jednostkami obsługi komunikatów. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu i możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Service Busych kolejek, zobacz sekcję [kolejki, tematy i subskrypcje][Queues, topics, and subscriptions] , aby dowiedzieć się więcej.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
