---
title: Azure Service Bus — wyjątki komunikatów | Microsoft Docs
description: Ten artykuł zawiera listę wyjątków Azure Service Bus komunikatów i sugerowanych akcji do wykonania w przypadku wystąpienia wyjątku.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3b56aff2635593d6cb49adbcf3784ddd5cb4fa39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99219149"
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus wyjątki komunikatów
W tym artykule wymieniono wyjątki platformy .NET wygenerowane przez interfejsy API .NET Framework. 

## <a name="exception-categories"></a>Kategorie wyjątków
Interfejsy API obsługi komunikatów generują wyjątki, które mogą należeć do następujących kategorii oraz skojarzoną akcję, którą można wykonać, aby spróbować rozwiązać te problemy. Znaczenie i przyczyny wyjątku mogą się różnić w zależności od typu jednostki obsługi komunikatów:

1. Błąd kodowania użytkownika ([System. ArgumentException](/dotnet/api/system.argumentexception), [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception), [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception), [System. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception)). Akcja ogólna: spróbuj naprawić kod przed kontynuowaniem.
2. Błąd instalacji/konfiguracji ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). Akcja ogólna: przegląd konfiguracji i zmiana w razie potrzeby.
3. Wyjątki przejściowe ([Microsoft. ServiceBus. Messaging. messagingexception](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. wyjątek serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Akcja ogólna: ponów próbę wykonania operacji lub Powiadom użytkowników. `RetryPolicy`Klasy w zestawie SDK klienta można skonfigurować do automatycznego obsłużenia ponownych prób. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące ponawiania prób](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Inne wyjątki ([System. Actions. TransactionException](/dotnet/api/system.transactions.transactionexception), [System. TimeoutException](/dotnet/api/system.timeoutexception), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Akcja ogólna: określona dla typu wyjątku; Zapoznaj się z tabelą w następującej sekcji: 

## <a name="exception-types"></a>Typy wyjątków
Poniższa tabela zawiera listę typów wyjątków komunikatów i ich przyczyny oraz uwagi sugerowane akcje, które można wykonać.

| **Typ wyjątku** | **Opis/przyczyna/przykłady** | **Sugerowana akcja** | **Uwaga dotycząca automatycznego/natychmiastowego ponawiania próby** |
| --- | --- | --- | --- |
| [TimeoutException](/dotnet/api/system.timeoutexception) |Serwer nie odpowiedział na żądaną operację w określonym czasie, który jest kontrolowany przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Serwer mógł zakończyć żądaną operację. Może się tak zdarzyć z powodu opóźnień sieci lub innych. |Sprawdź stan systemu pod kątem spójności i ponów próbę w razie potrzeby. Zobacz [wyjątki limitów czasu](#timeoutexception). |Ponowienie próby może pomóc w niektórych przypadkach; Dodaj logikę ponowień do kodu. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception) |Żądana operacja użytkownika nie jest dozwolona w ramach serwera lub usługi. Aby uzyskać szczegółowe informacje, zobacz komunikat o wyjątku. Na przykład [pełna ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) generuje ten wyjątek, jeśli wiadomość została odebrana w trybie [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) . |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest prawidłowa. |Ponowienie nie pomaga. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception) |Podjęto próbę wywołania operacji na obiekcie, który został już zamknięty, przerwany lub usunięty. W rzadkich przypadkach transakcja otoczenia została już usunięta. |Sprawdź kod i upewnij się, że nie wywoła operacji na usuniętym obiekcie. |Ponowienie nie pomaga. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) |Obiekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nie może uzyskać tokenu, token jest nieprawidłowy lub token nie zawiera oświadczeń wymaganych do wykonania tej operacji. |Upewnij się, że Dostawca tokenu został utworzony z prawidłowymi wartościami. Sprawdź konfigurację Access Control Service. |Ponowienie próby może pomóc w niektórych przypadkach; Dodaj logikę ponowień do kodu. |
| [ArgumentException](/dotnet/api/system.argumentexception)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception)<br />[Wyjątku ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception) |Jeden lub więcej argumentów dostarczonych do metody są nieprawidłowe.<br /> Identyfikator URI podany w [przestrzeni nazwmanager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) zawiera segmenty ścieżki.<br /> Schemat identyfikatora URI dostarczony do [przestrzeni nazwmanager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) jest nieprawidłowy. <br />Wartość właściwości jest większa niż 32 KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Ponowienie nie pomaga. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Jednostka skojarzona z operacją nie istnieje lub została usunięta. |Upewnij się, że jednostka istnieje. |Ponowienie nie pomaga. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Spróbuj odebrać komunikat z określonym numerem sekwencyjnym. Nie znaleziono tego komunikatu. |Upewnij się, że wiadomość nie została już odebrana. Sprawdź kolejkę utraconych wiadomości, aby sprawdzić, czy wiadomość została deadlettered. |Ponowienie nie pomaga. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient nie może nawiązać połączenia z Service Bus. |Upewnij się, że podana nazwa hosta jest poprawna i że host jest osiągalny. <p>Jeśli kod jest uruchamiany w środowisku z zaporą/serwerem proxy, upewnij się, że ruch do Service Bus domeny/adresu IP i portów nie jest zablokowany.</p>|Ponowienie próby może pomóc w przypadku sporadycznych problemów z łącznością. |
| [Wyjątek serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Usługa nie może w tej chwili przetworzyć żądania. |Klient może czekać przez pewien czas, a następnie ponowić próbę wykonania operacji. |Klient może ponowić próbę po pewnym interwale. Jeśli ponowienie próby spowoduje inny wyjątek, sprawdź ponowienie tego wyjątku. |
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
| [TransactionException](/dotnet/api/system.transactions.transactionexception) |Transakcja otoczenia (*Transaction. Current*) jest nieprawidłowa. Mógł zostać ukończony lub przerwany. Wewnętrzny wyjątek może dostarczyć dodatkowych informacji. | |Ponowienie nie pomaga. |
| [TransactionInDoubtException —](/dotnet/api/system.transactions.transactionindoubtexception) |Podjęto próbę wykonania operacji w transakcji, która jest wątpliwa lub podjęto próbę zatwierdzenia transakcji, a transakcja stanie się wątpliwa. |Aplikacja musi obsłużyć ten wyjątek (w specjalnym przypadku), ponieważ transakcja mogła już zostać zatwierdzona. |- |

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

### <a name="namespaces"></a>Przestrzenie nazw

W przypadku przestrzeni nazw [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) może wskazywać, że aplikacja przekroczyła maksymalną liczbę połączeń z przestrzenią nazw. Na przykład:

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
[Limit czasu](/dotnet/api/system.timeoutexception) wskazuje, że operacja inicjowana przez użytkownika trwa dłużej niż limit czasu operacji. 

Należy sprawdzić wartość właściwości [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) , ponieważ może to spowodować również [przekroczenie limitu czasu](/dotnet/api/system.timeoutexception).

Oczekiwane przekroczenia limitu czasu podczas operacji konserwacji, takich jak aktualizacje usługi Service Bus (lub) aktualizacje systemu operacyjnego na zasobach z uruchomioną usługą. Podczas aktualizacji systemu operacyjnego jednostki są przenoszone wokół, a węzły są aktualizowane lub uruchamiane ponownie, co może spowodować przekroczenie limitu czasu. Aby uzyskać szczegółowe informacje o umowie dotyczącej poziomu usług (SLA) dla usługi Azure Service Bus, zobacz [Umowa SLA dla Service Bus](https://azure.microsoft.com/support/legal/sla/service-bus/).


### <a name="queues-and-topics"></a>Kolejki i tematy
W przypadku kolejek i tematów limit czasu jest określony we właściwości [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) w ramach parametrów połączenia lub przez [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Sam komunikat o błędzie może się różnić, ale zawsze zawiera wartość limitu czasu określoną dla bieżącej operacji. 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>Przyczyna

**MessageLockLostException** jest generowany, gdy wiadomość zostanie odebrana przy użyciu trybu odbierania [PeekLock](message-transfers-locks-settlement.md#peeklock) i blokada przechowywana przez klienta wygaśnie po stronie usługi.

Blokada komunikatu może wygasnąć z różnych powodów — 

  * Czasomierz blokady wygasł przed odnowieniem przez aplikację kliencką.
  * Aplikacja kliencka uzyskała blokadę, Zapisano ją w magazynie trwałym, a następnie ponownie uruchomiona. Po ponownym uruchomieniu aplikacja kliencka przegląda komunikaty numerów porządkowych określających i próbowała wykonać te czynności.

### <a name="resolution"></a>Rozwiązanie

W przypadku **MessageLockLostException** aplikacja kliencka nie może już przetwarzać komunikatu. Aplikacja kliencka może opcjonalnie rozważyć zarejestrowanie wyjątku do analizy, ale klient *musi* usunąć komunikat.

Ponieważ blokada wiadomości wygasła, może ona wrócić do kolejki (lub subskrypcji) i będzie mogła zostać przetworzona przez następną aplikację kliencką, która wywołuje odbieranie.

W przypadku przekroczenia **MaxDeliveryCount** komunikat może zostać przeniesiony do **DeadLetterQueue**.

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>Przyczyna

**SessionLockLostException** jest generowany, gdy sesja zostanie zaakceptowana, a blokada zatrzymywana przez klienta wygaśnie po stronie usługi.

Blokada sesji może wygasnąć z różnych powodów — 

  * Czasomierz blokady wygasł przed odnowieniem przez aplikację kliencką.
  * Aplikacja kliencka uzyskała blokadę, Zapisano ją w magazynie trwałym, a następnie ponownie uruchomiona. Po ponownym uruchomieniu aplikacja kliencka przeszukała sesji numerów porządkowych określających i próbowała przetwarzać komunikaty w tych sesjach.

### <a name="resolution"></a>Rozwiązanie

W przypadku **SessionLockLostException** aplikacja kliencka nie może już przetwarzać komunikatów w sesji. Aplikacja kliencka może rozważyć zarejestrowanie wyjątku do analizy, ale klient *musi* usunąć komunikat.

Ponieważ blokada sesji wygasła, może ona wrócić do kolejki (lub subskrypcji) i można ją zablokować przez następną aplikację kliencką, która akceptuje daną sesję. Ponieważ blokada sesji jest utrzymywana przez pojedynczą aplikację kliencką w dowolnym momencie, jest gwarantowane przetwarzanie w kolejności.

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>Przyczyna

W poniższych przypadkach jest generowany obiekt **SocketException** -
   * Próba nawiązania połączenia nie powiedzie się, ponieważ Host nie odpowiedział prawidłowo po określonym czasie (kod błędu TCP 10060).
   * Nawiązywanie połączenia nie powiodło się, ponieważ podłączony host nie odpowiedział.
   * Wystąpił błąd podczas przetwarzania komunikatu lub Przekroczono limit czasu hosta zdalnego.
   * Problem z zasobem sieciowym.

### <a name="resolution"></a>Rozwiązanie

Błędy **gniazdaexception** wskazują, że maszyna wirtualna obsługująca aplikacje nie może skonwertować nazwy `<mynamespace>.servicebus.windows.net` na odpowiedni adres IP. 

Zaznacz, aby zobaczyć, czy poniższe polecenie powiodło się w mapowaniu na adres IP.

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

który powinien podawać dane wyjściowe poniżej

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

Jeśli powyższa nazwa nie **jest rozpoznawana** jako adres IP i alias przestrzeni nazw, sprawdź, który administrator sieci powinien dokładniej zbadać. Rozpoznawanie nazw odbywa się za pomocą serwera DNS zazwyczaj zasobu w sieci klienta. Jeśli rozwiązanie DNS jest wykonywane przez Azure DNS, skontaktuj się z pomocą techniczną platformy Azure.

Jeśli rozpoznawanie nazw **działa zgodnie z oczekiwaniami**, sprawdź, czy połączenia z Azure Service Bus są dozwolone w [tym miejscu](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues)


## <a name="messagingexception"></a>Komunikatexception

### <a name="cause"></a>Przyczyna

**Messagingexception** to ogólny wyjątek, który może być zgłoszony z różnych powodów. Poniżej wymieniono niektóre z przyczyn.

   * Podjęto próbę utworzenia **QueueClient** w **temacie** lub **subskrypcji**.
   * Rozmiar wysyłanej wiadomości jest większy niż limit dla danej warstwy. Przeczytaj więcej na temat [przydziałów i limitów](service-bus-quotas.md)Service Bus.
   * Żądanie określonej płaszczyzny danych (wysyłanie, odbieranie, zakończenie, Porzuć) zostało przerwane z powodu ograniczenia.
   * Przejściowe problemy spowodowane uaktualnieniem i ponownym uruchomieniem usług.

> [!NOTE]
> Powyższa lista wyjątków nie jest wyczerpująca.

### <a name="resolution"></a>Rozwiązanie

Kroki rozwiązania zależą od tego, co spowodowało zgłoszenie wyjątku **Messaging** .

   * W przypadku **problemów przejściowych** (gdzie **_isprzejściow_*_ jest ustawiona na wartość _*_true_*_) lub w przypadku* problemów z ograniczeniami**, ponawianie próby wykonania operacji może rozwiązać ten problem. Dla tej usługi można użyć domyślnych zasad ponawiania prób w zestawie SDK.
   * W przypadku innych problemów szczegóły w wyjątku wskazują, że kroki problemu i rozwiązania można wywnioskować z tego samego.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać pełne informacje dotyczące interfejsów API platformy .NET Service Bus, zobacz [Dokumentacja interfejsu API platformy Azure](/dotnet/api/overview/azure/service-bus).
Porady dotyczące rozwiązywania problemów można znaleźć w [przewodniku rozwiązywania problemów](service-bus-troubleshooting-guide.md)
