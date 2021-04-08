---
title: Azure Relay wyjątki i sposoby ich rozwiązywania | Microsoft Docs
description: Lista wyjątków Azure Relay i sugerowanych działań, które można podjąć, aby rozwiązać te problemy.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 44eeba6eb7b8cfd4e81a923c2d9a3155f1709f2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625152"
---
# <a name="azure-relay-exceptions"></a>Wyjątki Azure Relay

W tym artykule wymieniono wyjątki, które mogą zostać wygenerowane przez interfejsy API Azure Relay. To odwołanie może ulec zmianie, więc sprawdź aktualizacje.

## <a name="exception-categories"></a>Kategorie wyjątków

Interfejsy API przekazywania generują wyjątki, które mogą należeć do następujących kategorii. Wymienione również są sugerowane akcje, które można wykonać, aby pomóc w rozwiązaniu wyjątków.

*   **Błąd kodowania użytkownika**: [System. ArgumentException](/dotnet/api/system.argumentexception), [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception), [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception), [System. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception). 

    **Akcja ogólna**: spróbuj naprawić kod przed kontynuowaniem.
*   **Błąd instalacji/konfiguracji**: [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). 

    **Akcja ogólna**: Przejrzyj konfigurację. W razie potrzeby zmień konfigurację.
*   **Wyjątki przejściowe**: [Microsoft. ServiceBus. Messaging. messagingexception](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. wyjątek serverbusyexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Akcja ogólna**: ponów próbę wykonania operacji lub Powiadom użytkowników.
*   **Inne wyjątki**: [System. Actions. TransactionException](/dotnet/api/system.transactions.transactionexception), [System. TimeoutException](/dotnet/api/system.timeoutexception). 

    **Akcja ogólna**: określona dla typu wyjątku. Zapoznaj się z tabelą w poniższej sekcji. 

## <a name="exception-types"></a>Typy wyjątków

W poniższej tabeli wymieniono typy wyjątków komunikatów i ich przyczyny. Zawarto również informacje na temat sugerowanych akcji, które można podjąć w celu rozwiązania tych wyjątków.

| **Typ wyjątku** | **Opis** | **Zalecane działanie** | **Uwaga dotycząca automatycznego lub natychmiastowego ponawiania próby** |
| --- | --- | --- | --- |
| [Limit czasu](/dotnet/api/system.timeoutexception) |Serwer nie odpowiedział na żądaną operację w określonym czasie, który jest kontrolowany przez [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Serwer mógł zakończyć żądaną operację. Może się to zdarzyć z powodu opóźnień sieci lub innych. |Sprawdź stan systemu pod kątem spójności, a następnie ponów próbę, jeśli to konieczne. Zobacz [TimeoutException](#timeoutexception). |Ponowienie próby może pomóc w niektórych przypadkach; Dodaj logikę ponowień do kodu. |
| [Nieprawidłowa operacja](/dotnet/api/system.invalidoperationexception) |Żądana operacja użytkownika nie jest dozwolona w ramach serwera lub usługi. Aby uzyskać szczegółowe informacje, zobacz komunikat o wyjątku. |Sprawdź kod i dokumentację. Upewnij się, że żądana operacja jest prawidłowa. |Ponowienie próby nie powiedzie się. |
| [Operacja anulowana](/dotnet/api/system.operationcanceledexception) |Podjęto próbę wywołania operacji na obiekcie, który został już zamknięty, przerwany lub usunięty. W rzadkich przypadkach transakcja otoczenia została już usunięta. |Sprawdź kod i upewnij się, że nie wywoła operacji na usuniętym obiekcie. |Ponowienie próby nie powiedzie się. |
| [Nieautoryzowany dostęp](/dotnet/api/system.unauthorizedaccessexception) |Obiekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nie może uzyskać tokenu, token jest nieprawidłowy lub token nie zawiera oświadczeń wymaganych do wykonania tej operacji. |Upewnij się, że Dostawca tokenu został utworzony z prawidłowymi wartościami. Sprawdź konfigurację usługi Access Control. |Ponowienie próby może pomóc w niektórych przypadkach; Dodaj logikę ponowień do kodu. |
| [Wyjątek argumentu](/dotnet/api/system.argumentexception),<br /> [Argument ma wartość null](/dotnet/api/system.argumentnullexception),<br />[Argument poza zakresem](/dotnet/api/system.argumentoutofrangeexception) |Wystąpił co najmniej jeden z następujących elementów:<br />Jeden lub więcej argumentów dostarczonych do metody są nieprawidłowe.<br /> Identyfikator URI podany w [przestrzeni nazwmanager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) zawiera jeden lub więcej segmentów ścieżki.<br />Schemat identyfikatora URI dostarczony do [przestrzeni nazwmanager](/dotnet/api/microsoft.servicebus.namespacemanager) lub [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) jest nieprawidłowy. <br />Wartość właściwości jest większa niż 32 KB. |Sprawdź kod wywołujący i upewnij się, że argumenty są poprawne. |Ponowienie próby nie powiedzie się. |
| [Serwer zajęty](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Usługa nie może w tej chwili przetworzyć żądania. |Klient może czekać przez pewien czas, a następnie ponowić próbę wykonania operacji. |Klient może ponowić próbę po upływie określonego interwału. Jeśli ponowienie próby spowoduje inny wyjątek, sprawdź zachowanie ponowienia tego wyjątku. |
| [Przekroczono limit przydziału](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Jednostka obsługi komunikatów osiągnęła maksymalny dozwolony rozmiar. |Utwórz miejsce w jednostce przez odebranie komunikatów z jednostki lub jej podkolejek. Zobacz [QuotaExceededException](#quotaexceededexception). |Ponowienie próby może pomóc w przypadku usunięcia komunikatów w międzyczasie. |
| [Przekroczono rozmiar komunikatu](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Ładunek komunikatu przekracza limit 256 KB. Należy pamiętać, że limit 256 KB jest całkowitym rozmiarem komunikatu. Łączny rozmiar komunikatu może obejmować właściwości systemu i wszelkie Microsoft .NET obciążenie. |Zmniejsz rozmiar ładunku komunikatu, a następnie spróbuj ponownie wykonać operację. |Ponowienie próby nie powiedzie się. |

## <a name="quotaexceededexception"></a>QuotaExceededException

Wyjątek [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) wskazuje, że przekroczono limit przydziału dla określonej jednostki.

W przypadku przekaźnika ten wyjątek otacza obiekt [System. ServiceModel. QuotaExceededException](/dotnet/api/system.servicemodel.quotaexceededexception), który wskazuje, że Przekroczono maksymalną liczbę odbiorników dla tego punktu końcowego. Jest to wskazane w wartości **MaximumListenersPerEndpoint** komunikatu o wyjątku.

## <a name="timeoutexception"></a>TimeoutException
[Limit czasu](/dotnet/api/system.timeoutexception) wskazuje, że operacja inicjowana przez użytkownika trwa dłużej niż limit czasu operacji. 

Sprawdź wartość właściwości [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . Osiągnięcie tego limitu również może spowodować [przekroczenie limitu czasu](/dotnet/api/system.timeoutexception).

W przypadku usługi Relay może wystąpić wyjątek limitu czasu podczas pierwszego otwarcia połączenia nadawcy przekaźnika. Istnieją dwie typowe przyczyny tego wyjątku:

*   Wartość [OpenTimeout](/previous-versions/) może być za mała (nawet przez część sekundy).
* Lokalny odbiornik przekaźnika może nie odpowiadać (lub mogą wystąpić problemy z regułami zapory, które uniemożliwiają odbiornikom akceptowanie nowych połączeń klienta), a wartość [OpenTimeout](/previous-versions/) jest mniejsza niż około 20 sekund.

Przykład:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Typowe przyczyny
Istnieją dwie typowe przyczyny tego błędu:

*   **Nieprawidłowa konfiguracja**
    
    Limit czasu operacji może być za mały dla warunku operacyjnego. Wartość domyślna limitu czasu operacji w zestawie SDK klienta to 60 sekund. Sprawdź, czy w kodzie jest ustawiona wartość zbyt mała. Należy zauważyć, że użycie procesora CPU i stan sieci może wpływać na czas trwania operacji. Dobrym pomysłem jest, aby nie ustawiać niewielkiej wartości limitu czasu operacji.
*   **Przejściowy błąd usługi**

    Czasami Usługa przekazywania może napotkać opóźnienia w przetwarzaniu żądań. Może się tak zdarzyć na przykład w okresach dużego ruchu. W takim przypadku ponów próbę wykonania operacji po opóźnieniu, dopóki operacja nie powiedzie się. Jeśli ta sama operacja nie powiedzie się po wielu próbach, należy sprawdzić [witrynę stanu usługi platformy Azure](https://azure.microsoft.com/status/) , aby sprawdzić, czy jest znana awaria usługi.

## <a name="next-steps"></a>Następne kroki
* [Azure Relay często zadawane pytania](relay-faq.md)
* [Tworzenie przestrzeni nazw przekaźnika](relay-create-namespace-portal.md)
* [Wprowadzenie do Azure Relay i platformy .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do Azure Relay i węzła](relay-hybrid-connections-node-get-started.md)
