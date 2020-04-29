---
title: Azure Service Bus — wyjątki komunikatów | Microsoft Docs
description: Ten artykuł zawiera listę wyjątków Azure Service Bus komunikatów i sugerowanych akcji do wykonania w przypadku wystąpienia wyjątku.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: d04902a8d53397b7e7d9712a1c75ce44cc7aa7ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80880792"
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus wyjątki komunikatów
W tym artykule wymieniono wyjątki platformy .NET wygenerowane przez interfejsy API .NET Framework. 

## <a name="exception-categories"></a>Kategorie wyjątków
Interfejsy API obsługi komunikatów generują wyjątki, które mogą należeć do następujących kategorii oraz skojarzoną akcję, którą można wykonać, aby spróbować rozwiązać te problemy. Znaczenie i przyczyny wyjątku mogą się różnić w zależności od typu jednostki obsługi komunikatów:

1. Błąd kodowania użytkownika ([System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. Serialization. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Akcja ogólna: spróbuj naprawić kod przed kontynuowaniem.
2. Błąd instalacji/konfiguracji ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Akcja ogólna: przegląd konfiguracji i zmiana w razie potrzeby.
3. Wyjątki przejściowe ([Microsoft. ServiceBus. Messaging. messagingexception](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. wyjątek serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Akcja ogólna: ponów próbę wykonania operacji lub Powiadom użytkowników. `RetryPolicy` Klasy w zestawie SDK klienta można skonfigurować do automatycznego obsłużenia ponownych prób. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące ponawiania prób](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Inne wyjątki ([System. Actions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Akcja ogólna: określona dla typu wyjątku; Zapoznaj się z tabelą w następującej sekcji: 

## <a name="exception-types"></a>Typy wyjątków
Poniższa tabela zawiera listę typów wyjątków komunikatów i ich przyczyny oraz uwagi sugerowane akcje, które można wykonać.

| **Typ wyjątku** | **Opis/przyczyna/przykłady** | **Sugerowana akcja** | **Uwaga dotycząca automatycznego/natychmiastowego ponawiania próby** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądaną operację w określonym czasie, który jest kontrolowany przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Serwer mógł zakończyć żądaną operację. Może się tak zdarzyć z powodu opóźnień sieci lub innych. |Sprawdź stan systemu pod kątem spójności i ponów próbę w razie potrzeby. Zobacz [wyjątki limitów czasu](#timeoutexception). |Ponowienie próby może pomóc w niektórych przypadkach; Dodaj logikę ponowień do kodu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Żądana operacja użytkownika nie jest dozwolona w ramach serwera lub usługi. Aby uzyskać szczegółowe informacje, zobacz komunikat o wyjątku. Na przykład [pełna ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) generuje ten wyjątek, jeśli wiadomość została odebrana w trybie [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) . |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest prawidłowa. |Ponowienie nie pomaga. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Podjęto próbę wywołania operacji na obiekcie, który został już zamknięty, przerwany lub usunięty. W rzadkich przypadkach transakcja otoczenia została już usunięta. |Sprawdź kod i upewnij się, że nie wywoła operacji na usuniętym obiekcie. |Ponowienie nie pomaga. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Obiekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nie może uzyskać tokenu, token jest nieprawidłowy lub token nie zawiera oświadczeń wymaganych do wykonania tej operacji. |Upewnij się, że Dostawca tokenu został utworzony z prawidłowymi wartościami. Sprawdź konfigurację Access Control Service. |Ponowienie próby może pomóc w niektórych przypadkach; Dodaj logikę ponowień do kodu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Wyjątku ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden lub więcej argumentów dostarczonych do metody są nieprawidłowe.<br /> Identyfikator URI podany w [przestrzeni nazwmanager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) zawiera segmenty ścieżki.<br /> Schemat identyfikatora URI dostarczony do [przestrzeni nazwmanager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) jest nieprawidłowy. <br />Wartość właściwości jest większa niż 32 KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Ponowienie nie pomaga. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Jednostka skojarzona z operacją nie istnieje lub została usunięta. |Upewnij się, że jednostka istnieje. |Ponowienie nie pomaga. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Spróbuj odebrać komunikat z określonym numerem sekwencyjnym. Nie znaleziono tego komunikatu. |Upewnij się, że wiadomość nie została już odebrana. Sprawdź kolejkę utraconych wiadomości, aby sprawdzić, czy wiadomość została deadlettered. |Ponowienie nie pomaga. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient nie może nawiązać połączenia z Service Bus. |Upewnij się, że podana nazwa hosta jest poprawna i że host jest osiągalny. |Ponowienie próby może pomóc w przypadku sporadycznych problemów z łącznością. |
| [Wyjątek serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Usługa nie może w tej chwili przetworzyć żądania. |Klient może czekać przez pewien czas, a następnie ponowić próbę wykonania operacji. |Klient może ponowić próbę po pewnym interwale. Jeśli ponowienie próby spowoduje inny wyjątek, sprawdź ponowienie tego wyjątku. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Token blokady skojarzony z komunikatem wygasł lub nie znaleziono tokenu blokady. |Usuń wiadomość. |Ponowienie nie pomaga. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Blokada skojarzona z tą sesją zostanie utracona. |Przerwij obiekt [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |Ponowienie nie pomaga. |
| [Komunikatexception](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Ogólny wyjątek komunikatów, który może być zgłaszany w następujących przypadkach:<p>Podjęto próbę utworzenia [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) przy użyciu nazwy lub ścieżki, która należy do innego typu jednostki (na przykład tematu).</p><p>Podjęto próbę wysłania komunikatu o rozmiarze większym niż 256 KB. </p>Serwer lub usługa napotkała błąd podczas przetwarzania żądania. Aby uzyskać szczegółowe informacje, zobacz komunikat o wyjątku. Zwykle jest to wyjątek przejściowy.</p><p>Żądanie zostało przerwane, ponieważ trwa ograniczanie jednostki. Kod błędu: 50001, 50002, 50008. </p> | Sprawdź kod i upewnij się, że tylko obiekty możliwe do serializacji są używane dla treści wiadomości (lub użyj serializatora niestandardowego). <p>Zapoznaj się z dokumentacją dla obsługiwanych typów wartości właściwości i używaj tylko obsługiwanych typów.</p><p> Sprawdź Właściwość [Isprzejściową](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Jeśli jest to **prawdziwe**, można ponowić próbę wykonania operacji. </p>| Jeśli wyjątek jest spowodowany ograniczeniem, poczekaj kilka sekund, a następnie spróbuj ponownie wykonać operację. Zachowanie przy ponowieniu próby jest niezdefiniowane i może nie pomóc w innych scenariuszach.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Podjęto próbę utworzenia jednostki o nazwie, która jest już używana przez inną jednostkę w tej przestrzeni nazw usługi. |Usuń istniejącą jednostkę lub wybierz inną nazwę dla jednostki, która ma zostać utworzona. |Ponowienie nie pomaga. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Jednostka obsługi komunikatów osiągnęła maksymalny dozwolony rozmiar lub Przekroczono maksymalną liczbę połączeń z przestrzenią nazw. |Utwórz miejsce w jednostce przez odebranie komunikatów z jednostki lub jej podkolejek. Zobacz [QuotaExceededException](#quotaexceededexception). |Ponowienie próby może pomóc w przypadku usunięcia komunikatów w międzyczasie. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus zwraca ten wyjątek, Jeśli podjęto próbę utworzenia nieprawidłowej akcji reguły. Service Bus dołącza ten wyjątek do komunikatu deadlettered, jeśli wystąpi błąd podczas przetwarzania akcji reguły dla tego komunikatu. |Sprawdź akcję reguły, aby sprawdzić poprawność. |Ponowienie nie pomaga. |
| [Filtrexception](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus zwraca ten wyjątek, jeśli próbujesz utworzyć nieprawidłowy filtr. Service Bus dołącza ten wyjątek do komunikatu deadlettered, jeśli wystąpił błąd podczas przetwarzania filtru dla tej wiadomości. |Sprawdź, czy filtr jest prawidłowy. |Ponowienie nie pomaga. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Podjęto próbę zaakceptowania sesji z określonym IDENTYFIKATORem sesji, ale sesja jest obecnie zablokowana przez innego klienta. |Upewnij się, że sesja jest odblokowana przez innych klientów. |Ponowienie próby może pomóc, jeśli sesja została wydana w tymczasowym. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Zbyt wiele operacji jest częścią transakcji. |Zmniejsz liczbę operacji, które są częścią tej transakcji. |Ponowienie nie pomaga. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Żądanie operacji środowiska uruchomieniowego dla wyłączonej jednostki. |Aktywuj jednostkę. |Ponowienie próby może pomóc, jeśli jednostka została aktywowana w tymczasowym. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus zwraca ten wyjątek, jeśli wysyła komunikat do tematu, który ma włączoną funkcję wstępnego filtrowania, i żaden z filtrów nie jest zgodny. |Upewnij się, że co najmniej jeden filtr jest zgodny. |Ponowienie nie pomaga. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Ładunek komunikatu przekracza limit 256 KB. Limit 256 KB to całkowity rozmiar komunikatu, który może obejmować właściwości systemu i dowolnych obciążeń związanych z platformą .NET. |Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Ponowienie nie pomaga. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Transakcja otoczenia (*Transaction. Current*) jest nieprawidłowa. Mógł zostać ukończony lub przerwany. Wewnętrzny wyjątek może dostarczyć dodatkowych informacji. | |Ponowienie nie pomaga. |
| [TransactionInDoubtException —](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Podjęto próbę wykonania operacji w transakcji, która jest wątpliwa lub podjęto próbę zatwierdzenia transakcji, a transakcja stanie się wątpliwa. |Aplikacja musi obsłużyć ten wyjątek (w specjalnym przypadku), ponieważ transakcja mogła już zostać zatwierdzona. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
Wyjątek [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

### <a name="queues-and-topics"></a>Kolejki i tematy
W przypadku kolejek i tematów często jest to rozmiar kolejki. Właściwość komunikat o błędzie zawiera dalsze szczegóły, jak w poniższym przykładzie:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Komunikat informuje o tym, że temat przekroczył swój limit rozmiaru, w tym przypadku 1 GB (domyślny limit rozmiaru). 

### <a name="namespaces"></a>Namespaces

W przypadku przestrzeni nazw [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) może wskazywać, że aplikacja przekroczyła maksymalną liczbę połączeń z przestrzenią nazw. Przykład:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu: Kolejka utraconych wiadomości i niedziałające odbiorcy komunikatów.

1. **[Kolejka utraconych wiadomości](service-bus-dead-letter-queues.md)** Czytnik kończy się niepowodzeniem, a komunikaty są zwracane do kolejki/tematu, gdy blokada wygaśnie. Może się tak zdarzyć, jeśli czytnik napotka wyjątek, który uniemożliwia jego wywołanie [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Gdy wiadomość zostanie odczytana 10 razy, domyślnie przechodzi do kolejki utraconych wiadomości. Takie zachowanie jest kontrolowane przez właściwość [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) i ma wartość domyślną 10. Jako że komunikaty są ustawiane w kolejce utraconych wiadomości, zajmują miejsce.
   
    Aby rozwiązać ten problem, należy odczytać i zakończyć komunikaty z kolejki utraconych wiadomości, tak jak w przypadku każdej innej kolejki. Można użyć metody [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) , aby ułatwić formatowanie ścieżki kolejki utraconych wiadomości.
2. **Odbiornik został zatrzymany**. Odbiornik przestał odbierać komunikaty z kolejki lub subskrypcji. Aby zidentyfikować ten sposób, należy sprawdzić Właściwość [QueueDescription. MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , która pokazuje pełny podział komunikatów. Jeśli właściwość [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) jest wysoka lub rosnąca, wiadomości nie są odczytywane tak szybko, jak są zapisywane.

## <a name="timeoutexception"></a>TimeoutException
[Limit czasu](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że operacja inicjowana przez użytkownika trwa dłużej niż limit czasu operacji. 

Należy sprawdzić wartość właściwości [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , ponieważ może to spowodować również [przekroczenie limitu czasu](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Kolejki i tematy
W przypadku kolejek i tematów limit czasu jest określony we właściwości [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) w ramach parametrów połączenia lub przez [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Sam komunikat o błędzie może się różnić, ale zawsze zawiera wartość limitu czasu określoną dla bieżącej operacji. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać pełne informacje dotyczące interfejsów API platformy .NET Service Bus, zobacz [Dokumentacja interfejsu API platformy Azure](/dotnet/api/overview/azure/service-bus).
Porady dotyczące rozwiązywania problemów można znaleźć w [przewodniku rozwiązywania problemów](service-bus-troubleshooting-guide.md)
