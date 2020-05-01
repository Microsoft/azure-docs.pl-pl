---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka Python (stary)
description: W tym instruktażu przedstawiono sposób tworzenia i uruchamiania skryptów języka Python, które wysyłają zdarzenia do i odbierają zdarzenia z usługi Azure Event Hubs przy użyciu starego pakietu Azure-eventhub w wersji 1.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77162603"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Szybki Start: wysyłanie i odbieranie zdarzeń za pomocą Event Hubs przy użyciu języka Python (Azure-eventhub w wersji 1)
W tym przewodniku szybki start pokazano, jak wysyłać zdarzenia do i odbierać zdarzenia z centrum zdarzeń przy użyciu pakietu języka Python **platformy Azure-eventhub w wersji 1** . 

> [!WARNING]
> Ten przewodnik Szybki Start używa starego pakietu Azure-eventhub w wersji 1. Przewodnik Szybki Start, który używa najnowszej **wersji 5** pakietu, znajduje się w temacie [wysyłanie i odbieranie zdarzeń przy użyciu platformy Azure — eventhub w wersji 5](get-started-python-send-v2.md). Aby przenieść aplikację z używania starego pakietu do nowego, zapoznaj się z [przewodnikiem Migrowanie z platformy Azure — eventhub w wersji 1 do wersji 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli dopiero zaczynasz w usłudze Azure Event Hubs, zapoznaj się z tematem [Event Hubs Overview](event-hubs-about.md) przed wykonaniem tego przewodnika Szybki Start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja Microsoft Azure**. Do korzystania z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebna jest subskrypcja.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu korzystania z [bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybentów MSDN podczas [tworzenia konta](https://azure.microsoft.com).
- Język Python 3,4 lub nowszy z `pip` zainstalowanym i zaktualizowanym programem.
- Pakiet języka Python dla Event Hubs. Aby zainstalować pakiet, Uruchom to polecenie w wierszu polecenia zawierającym Język Python w ścieżce: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Pierwszym krokiem jest użycie [Azure Portal](https://portal.azure.com) do utworzenia przestrzeni nazw typu Event Hubs i uzyskanie poświadczeń zarządzania wymaganych przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie Pobierz wartość klucza dostępu dla centrum zdarzeń, wykonując instrukcje podane w artykule: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Używasz klucza dostępu w kodzie zapisanym w dalszej części tego przewodnika Szybki Start. Domyślna nazwa klucza to: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Wysyłanie zdarzeń

Aby utworzyć aplikację w języku Python, która wysyła zdarzenia do centrum zdarzeń:

> [!NOTE]
> Zamiast korzystać z przewodnika Szybki Start można pobrać i uruchomić [przykładowe aplikacje](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) z usługi GitHub. Zamień ciągi `EventHubConnectionString` i `EventHubName` na wartości z centrum zdarzeń.

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz nowy plik o nazwie *send.py*. Ten skrypt wysyła zdarzenia 100 do centrum zdarzeń.
3. Wklej następujący kod do *send.py*, zastępując Event Hubs \<przestrzeni nazw> \<, eventhub>, \<> AccessKeyName i \<wartość klucza podstawowego> z wartościami: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. Zapisz plik. 

Aby uruchomić skrypt, w katalogu, w którym zapisano *send.py*, uruchom następujące polecenie:

```cmd
start python send.py
```

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="receive-events"></a>Odbieranie zdarzeń

Aby utworzyć aplikację w języku Python, która odbiera zdarzenia z centrum zdarzeń:

1. W edytorze języka Python Utwórz plik o nazwie *recv.py*.
2. Wklej następujący kod do *recv.py*, zastępując Event Hubs \<przestrzeni nazw> \<, eventhub>, \<> AccessKeyName i \<wartość klucza podstawowego> z wartościami: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. Zapisz plik.

Aby uruchomić skrypt, w katalogu, w którym zapisano *recv.py*, uruchom następujące polecenie:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat Event Hubs, zobacz następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)

