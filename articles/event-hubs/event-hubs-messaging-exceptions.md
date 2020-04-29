---
title: Przewodnik rozwiązywania problemów — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera listę wyjątków i sugerowanych akcji usługi Azure Event Hubs Messaging.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76309782"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Przewodnik rozwiązywania problemów dotyczących usługi Azure Event Hubs
W tym artykule przedstawiono niektóre wyjątki platformy .NET wygenerowane przez Event Hubs .NET Framework interfejsy API, a także inne wskazówki dotyczące rozwiązywania problemów. 

## <a name="event-hubs-messaging-exceptions---net"></a>Event Hubs wyjątki komunikatów — .NET
Ta sekcja zawiera listę wyjątków platformy .NET wygenerowanych przez interfejsy API .NET Framework. 

### <a name="exception-categories"></a>Kategorie wyjątków

Event Hubs interfejsy API platformy .NET generują wyjątki, które mogą należeć do następujących kategorii oraz skojarzoną akcję, którą można wykonać, aby spróbować rozwiązać te problemy.

1. Błąd kodowania użytkownika: [System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. Serialization. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Akcja ogólna: spróbuj naprawić kod przed kontynuowaniem.
2. Błąd instalacji/konfiguracji: [Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft. Azure. EventHubs. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Akcja ogólna: przegląd konfiguracji i zmiana w razie potrzeby.
3. Wyjątki przejściowe: [Microsoft. ServiceBus. Messaging. messagingexception](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. wyjątek serverbusyexception](#serverbusyexception), [Microsoft. Azure. EventHubs. wyjątek serverbusyexception](#serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Akcja ogólna: ponów próbę wykonania operacji lub Powiadom użytkowników.
4. Inne wyjątki: [System. Actions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](#timeoutexception), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Akcja ogólna: określona dla typu wyjątku; Zapoznaj się z tabelą w poniższej sekcji. 

### <a name="exception-types"></a>Typy wyjątków
Poniższa tabela zawiera listę typów wyjątków komunikatów i ich przyczyny oraz uwagi sugerowane akcje, które można wykonać.

| Typ wyjątku | Opis/przyczyna/przykłady | Sugerowana akcja | Uwaga dotycząca automatycznego/natychmiastowego ponawiania próby |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serwer nie odpowiedział na żądaną operację w określonym czasie, który jest kontrolowany przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Serwer mógł zakończyć żądaną operację. Ten wyjątek może wystąpić z powodu opóźnień między siecią lub innymi. |Sprawdź stan systemu pod kątem spójności i ponów próbę w razie potrzeby.<br /> Zobacz [TimeoutException](#timeoutexception). | Ponowienie próby może pomóc w niektórych przypadkach; Dodaj logikę ponowień do kodu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Żądana operacja użytkownika nie jest dozwolona w ramach serwera lub usługi. Aby uzyskać szczegółowe informacje, zobacz komunikat o wyjątku. Na przykład [Zakończ](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) generuje ten wyjątek, jeśli wiadomość została odebrana w trybie [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . | Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest prawidłowa. | Ponowienie próby nie powiedzie się. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Podjęto próbę wywołania operacji na obiekcie, który został już zamknięty, przerwany lub usunięty. W rzadkich przypadkach transakcja otoczenia została już usunięta. | Sprawdź kod i upewnij się, że nie wywoła operacji na usuniętym obiekcie. | Ponowienie próby nie powiedzie się. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | Obiekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nie może uzyskać tokenu, token jest nieprawidłowy lub token nie zawiera oświadczeń wymaganych do wykonania tej operacji. | Upewnij się, że Dostawca tokenu został utworzony z prawidłowymi wartościami. Sprawdź konfigurację Access Control Service. | Ponowienie próby może pomóc w niektórych przypadkach; Dodaj logikę ponowień do kodu. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Wyjątku ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Jeden lub więcej argumentów dostarczonych do metody są nieprawidłowe. Identyfikator URI podany w [przestrzeni nazwmanager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) zawiera segmenty ścieżki. Schemat identyfikatora URI dostarczony do [przestrzeni nazwmanager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) jest nieprawidłowy. Wartość właściwości jest większa niż 32 KB. | Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. | Ponowienie próby nie powiedzie się. |
| [Microsoft. ServiceBus. Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft. Azure. EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Jednostka skojarzona z operacją nie istnieje lub została usunięta. | Upewnij się, że jednostka istnieje. | Ponowienie próby nie powiedzie się. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klient nie może nawiązać połączenia z centrum zdarzeń. |Upewnij się, że podana nazwa hosta jest poprawna i że host jest osiągalny. | Ponowienie próby może pomóc w przypadku sporadycznych problemów z łącznością. |
| [Microsoft. ServiceBus. Messaging wyjątek serverbusyexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft. Azure. EventHubs wyjątek serverbusyexception](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Usługa nie może w tej chwili przetworzyć żądania. | Klient może czekać przez pewien czas, a następnie ponowić próbę wykonania operacji. <br /> Zobacz [wyjątek serverbusyexception](#serverbusyexception). | Klient może ponowić próbę po pewnym interwale. Jeśli ponowienie próby spowoduje inny wyjątek, sprawdź ponowienie tego wyjątku. |
| [Komunikatexception](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Ogólny wyjątek komunikatów, który może zostać zgłoszony w następujących przypadkach: podjęto próbę utworzenia [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) przy użyciu nazwy lub ścieżki, która należy do innego typu jednostki (na przykład tematu). Podjęto próbę wysłania komunikatu o rozmiarze większym niż 1 MB. Serwer lub usługa napotkała błąd podczas przetwarzania żądania. Aby uzyskać szczegółowe informacje, zobacz komunikat o wyjątku. Ten wyjątek jest zwykle wyjątek przejściowy. | Sprawdź kod i upewnij się, że tylko obiekty możliwe do serializacji są używane dla treści wiadomości (lub użyj serializatora niestandardowego). Zapoznaj się z dokumentacją dla obsługiwanych typów wartości właściwości i używaj tylko obsługiwanych typów. Sprawdź Właściwość [Isprzejściową](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Jeśli wartość jest **równa true**, można ponowić próbę wykonania operacji. | Zachowanie przy ponowieniu próby jest niezdefiniowane i może nie pomóc. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Podjęto próbę utworzenia jednostki o nazwie, która jest już używana przez inną jednostkę w tej przestrzeni nazw usługi. | Usuń istniejącą jednostkę lub wybierz inną nazwę dla jednostki, która ma zostać utworzona. | Ponowienie próby nie powiedzie się. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Jednostka obsługi komunikatów osiągnęła maksymalny dozwolony rozmiar. Ten wyjątek może wystąpić, jeśli Maksymalna liczba odbiorników (czyli 5) została już otwarta na poziomie grupy dla poszczególnych użytkowników. | Utwórz miejsce w jednostce przez odebranie komunikatów z jednostki lub jej podkolejek. <br /> Zobacz [QuotaExceededException](#quotaexceededexception) | Ponowienie próby może pomóc w przypadku usunięcia komunikatów w międzyczasie. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Żądanie operacji środowiska uruchomieniowego dla wyłączonej jednostki. |Aktywuj jednostkę. | Ponowienie próby może pomóc, jeśli jednostka została aktywowana w tymczasowym. |
| [Microsoft. ServiceBus. Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft. Azure. EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Ładunek komunikatu przekracza limit 1 MB. Ten limit 1 MB jest przeznaczony dla łącznej wiadomości, która może obejmować właściwości systemu i wszystkie obciążenia platformy .NET. | Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Ponowienie próby nie powiedzie się. |

### <a name="quotaexceededexception"></a>QuotaExceededException
Wyjątek [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

Ten wyjątek może wystąpić, jeśli Maksymalna liczba odbiorników (5) została już otwarta na poziomie grupy dla poszczególnych użytkowników.

#### <a name="event-hubs"></a>Event Hubs
Event Hubs ma limit 20 grup konsumenckich na centrum zdarzeń. Gdy próbujesz utworzyć więcej, otrzymujesz [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
[Limit czasu](https://msdn.microsoft.com/library/system.timeoutexception.aspx) wskazuje, że operacja inicjowana przez użytkownika trwa dłużej niż limit czasu operacji. 

W przypadku Event Hubs limit czasu jest określany jako część parametrów połączenia lub przez [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Sam komunikat o błędzie może się różnić, ale zawsze zawiera wartość limitu czasu określoną dla bieżącej operacji. 

#### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu: nieprawidłowa konfiguracja lub przejściowy błąd usługi.

1. **Niepoprawna konfiguracja** Limit czasu operacji może być za mały dla warunku operacyjnego. Wartość domyślna limitu czasu operacji w zestawie SDK klienta to 60 sekund. Sprawdź, czy w kodzie jest ustawiona wartość zbyt mała. Warunek użycia sieci i procesora może mieć wpływ na czas potrzebny na wykonanie określonej operacji, więc limit czasu operacji nie powinien być ustawiony na małą wartość.
2. **Przejściowy błąd usługi** Czasami usługa Event Hubs może napotkać opóźnienia w przetwarzaniu żądań; na przykład podczas okresów dużego ruchu. W takich przypadkach można ponowić próbę wykonania operacji po opóźnieniu, dopóki operacja nie powiedzie się. Jeśli ta sama operacja nadal kończy się niepowodzeniem po wielu próbach, odwiedź [witrynę stanu usługi platformy Azure](https://azure.microsoft.com/status/) , aby sprawdzić, czy występują jakieś znane awarie usługi.

### <a name="serverbusyexception"></a>Wyjątek serverbusyexception

Obiekt [Microsoft. ServiceBus. Messaging. wyjątek serverbusyexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) lub [Microsoft. Azure. EventHubs. wyjątek serverbusyexception](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) wskazuje, że serwer jest przeciążony. Dla tego wyjątku istnieją dwa odpowiednie kody błędów.

#### <a name="error-code-50002"></a>Kod błędu 50002

Ten błąd może wystąpić z jednego z dwóch powodów:

1. Obciążenie nie jest równomiernie dystrybuowane we wszystkich partycjach w centrum zdarzeń, a jedna z nich trafi na ograniczenie lokalnej jednostki przepływności.
    
    Rozwiązanie: korygowanie strategii dystrybucji partycji lub próba [EventHubClient. Send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) może pomóc.

2. Przestrzeń nazw Event Hubs nie ma wystarczającej liczby jednostek przepływności (można sprawdzić ekran **metryk** w oknie Event Hubs przestrzeni nazw w [Azure Portal](https://portal.azure.com) , aby potwierdzić). W portalu są wyświetlane zagregowane informacje (1 minuty), ale mierzy przepływność w czasie rzeczywistym, więc jest to tylko oszacowanie.

    Rozwiązanie: zwiększenie liczby jednostek przepływności w przestrzeni nazw może pomóc. Tę operację można wykonać w portalu, w oknie **skalowanie** na ekranie Event Hubs przestrzeni nazw. Można też użyć [autorozdęcie](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Kod błędu 50001

Ten błąd powinien występować rzadko. Dzieje się tak, gdy w przypadku kontenera, w którym działa kod dla danego obszaru nazw, jest mało czasu procesora CPU — nie więcej niż kilka sekund przed rozpoczęciem Event Hubsego modułu równoważenia obciążenia.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Limit wywołań metody GetRuntimeInformation —
Usługa Azure Event Hubs obsługuje do 50 wywołań na sekundę w ciągu sekundy do GetRuntimeInfo. Po osiągnięciu limitu może zostać wyświetlony wyjątek podobny do następującego:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemy z łącznością, certyfikatem lub limitem czasu
Poniższe kroki mogą pomóc w rozwiązywaniu problemów z łącznością/certyfikatem/przekroczeniem limitu czasu dla wszystkich usług w programie *. servicebus.windows.net. 

- Przejdź do lub [Wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Pomaga sprawdzić, czy masz problemy z filtrowaniem adresów IP lub siecią wirtualną lub łańcuchem certyfikatów (najbardziej typowe w przypadku korzystania z zestawu Java SDK).

    Przykład pomyślnego komunikatu:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Przykładowy komunikat o błędzie błędu:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Uruchom następujące polecenie, aby sprawdzić, czy dowolny port jest zablokowany przez zaporę. Używane porty to 443 (HTTPS), 5671 (AMQP) i 9093 (Kafka). W zależności od używanej biblioteki używane są również inne porty. Oto przykładowe polecenie, które sprawdza, czy port 5671 jest zablokowany.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    W systemie Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Gdy występują sporadyczne problemy z łącznością, uruchom następujące polecenie, aby sprawdzić, czy pakiety pozostaną usunięte. To polecenie próbuje nawiązać 25 różnych połączeń TCP co 1 sekundę z usługą. Następnie można sprawdzić, ile z nich zakończyło się powodzeniem/niepowodzeniem, i zobaczyć także opóźnienia połączeń TCP. Możesz pobrać narzędzie z `psping` tego [miejsca](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Możesz użyć równoważnych poleceń, jeśli używasz innych narzędzi, takich jak `tnc`, `ping`, i tak dalej. 
- Uzyskaj ślad sieci, jeśli poprzednie kroki nie pomagają i nie analizują go przy użyciu narzędzi, takich jak [Wireshark](https://www.wireshark.org/). W razie konieczności skontaktuj się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/) . 

## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
