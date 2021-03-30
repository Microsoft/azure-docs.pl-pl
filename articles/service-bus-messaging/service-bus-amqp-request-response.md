---
title: Operacje żądania/odpowiedzi AMQP 1,0 w Azure Service Bus
description: W tym artykule zdefiniowano listę operacji AMQP i opartych na odpowiedziach w Microsoft Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b845f4086ee1ac4fe868571c1754caf6d29b9021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88064419"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1,0 w Microsoft Azure Service Bus: operacje oparte na odpowiedziach na żądanie

Ten artykuł definiuje listę operacji Microsoft Azure Service Bus żądania/odpowiedzi. Te informacje są oparte na roboczym programie AMQP Management w wersji 1,0.  
  
Szczegółowy przewodnik dotyczący protokołu AMQP 1,0, który wyjaśnia, w jaki sposób Service Bus implementuje i kompiluje w specyfikacji technicznej języka Oasis AMQP, patrz [AMQP 1,0 in Azure Service Bus i Event Hubs Przewodnik po]protokole[AMQP 1,0 Guide].  
  
## <a name="concepts"></a>Pojęcia  
  
### <a name="entity-description"></a>Opis jednostki  

Opis jednostki odwołuje się do Service Bus [klasy QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), [klasy TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)lub obiektu [klasy SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) .  
  
### <a name="brokered-message"></a>Komunikat obsługiwany przez brokera  

Reprezentuje komunikat w Service Bus, który jest mapowany na komunikat AMQP. Mapowanie jest zdefiniowane w [przewodniku po protokole Service Bus AMQP](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Dołącz do węzła zarządzania jednostkami  

Wszystkie operacje opisane w tym dokumencie są zgodne ze wzorcem żądania/odpowiedzi, są objęte zakresem jednostki i wymagają dołączenia do węzła zarządzania jednostkami.  
  
### <a name="create-link-for-sending-requests"></a>Utwórz link do wysyłania żądań  

Tworzy łącze do węzła zarządzania w celu wysyłania żądań.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Utwórz link do odbierania odpowiedzi  

Tworzy link do odbierania odpowiedzi z węzła zarządzania.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Prześlij komunikat żądania  

Przesyła komunikat żądania.  
Dla operacji obsługujących transakcję można opcjonalnie dodać stan transakcji.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Odbieranie komunikatu odpowiedzi  

Odbiera komunikat odpowiedzi z linku odpowiedzi.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Komunikat odpowiedzi ma następującą postać:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Adres jednostki Service Bus  

Jednostki Service Bus muszą być rozkierowane w następujący sposób:  
  
|Typ jednostki|Adres|Przykład|  
|-----------------|-------------|-------------|  
|kolejka|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|temat|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subskrypcja|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operacje na komunikatach  
  
### <a name="message-renew-lock"></a>Blokada odnowienia komunikatu  

Rozszerza blokadę komunikatu o czas określony w opisie podmiotu.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
 Treść komunikatu żądania musi składać się z sekcji AMQP-Value zawierającej mapę z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|Tablica identyfikatora UUID|Tak|Tokeny blokady komunikatów do odnowienia.|  

> [!NOTE]
> Tokeny blokady są `DeliveryTag` właściwością odebranych komunikatów. Zapoznaj się z poniższym przykładem w [zestawie SDK platformy .NET](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) , który pobiera te elementy. Token może być również wyświetlany w elemencie "DeliveryAnnotations" jako "x-opt-Lock-token", ale nie jest to gwarantowane i `DeliveryTag` preferowane. 
> 
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — powodzenie; w przeciwnym razie nie powiodło się.|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi musi składać się z sekcji AMQP-Value zawierającej mapę z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|wygaśnięcia|Tablica znaczników czasu|Tak|Token blokady wiadomości — nowe wygaśnięcie odpowiadające tokenom blokady żądania.|  
  
### <a name="peek-message"></a>Wgląd do wiadomości  

Wgląd w wiadomości bez blokowania.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|długi|Tak|Numer sekwencyjny, od którego ma zostać rozpoczęty wgląd.|  
|`message-count`|int|Tak|Maksymalna liczba komunikatów do wglądu.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — zawiera więcej komunikatów<br /><br /> 204: Brak zawartości — nie ma więcej komunikatów|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista map|Tak|Lista komunikatów, w których każda mapa Reprezentuje komunikat.|  
  
Mapa reprezentująca komunikat musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|message|Tablica bajtów|Tak|Komunikat kodowany przez sieć AMQP 1,0.|  
  
### <a name="schedule-message"></a>Komunikat harmonogramu  

Planuje komunikaty. Ta operacja obsługuje transakcję.
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista map|Tak|Lista komunikatów, w których każda mapa Reprezentuje komunikat.|  
  
Mapa reprezentująca komunikat musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Identyfikator komunikatu|ciąg|Tak|`amqpMessage.Properties.MessageId` jako ciąg|  
|Identyfikator sesji|ciąg|Nie|`amqpMessage.Properties.GroupId as string`|  
|klucz partycji|ciąg|Nie|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|za pośrednictwem-Partition-Key|ciąg|Nie|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|Tablica bajtów|Tak|Komunikat kodowany przez sieć AMQP 1,0.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — powodzenie; w przeciwnym razie nie powiodło się.|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi musi składać się z sekcji **AMQP-Value** zawierającej mapę z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|numery sekwencyjne|Tablica Long|Tak|Liczba sekwencji zaplanowanych komunikatów. Numer sekwencyjny jest używany do anulowania.|  
  
### <a name="cancel-scheduled-message"></a>Anuluj zaplanowaną wiadomość  

Anuluje zaplanowane wiadomości.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|numery sekwencyjne|Tablica Long|Tak|Numery sekwencji zaplanowanych komunikatów do anulowania.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — powodzenie; w przeciwnym razie nie powiodło się.|  
|statusDescription|ciąg|Nie|Opis stanu.|   
  
## <a name="session-operations"></a>Operacje sesji  
  
### <a name="session-renew-lock"></a>Blokada odnawiania sesji  

Rozszerza blokadę komunikatu o czas określony w opisie podmiotu.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Identyfikator sesji|ciąg|Tak|Identyfikator sesji.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — zawiera więcej komunikatów<br /><br /> 204: Brak zawartości — nie ma więcej komunikatów|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi musi składać się z sekcji **AMQP-Value** zawierającej mapę z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|datę|sygnatura czasowa|Tak|Nowe wygaśnięcie.|  
  
### <a name="peek-session-message"></a>Wgląd w komunikat sesji  

Wgląd w komunikaty sesji bez blokowania.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Numer sekwencyjny|długi|Tak|Numer sekwencyjny, od którego ma zostać rozpoczęty wgląd.|  
|Liczba komunikatów|int|Tak|Maksymalna liczba komunikatów do wglądu.|  
|Identyfikator sesji|ciąg|Tak|Identyfikator sesji.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — zawiera więcej komunikatów<br /><br /> 204: Brak zawartości — nie ma więcej komunikatów|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi musi składać się z sekcji **AMQP-Value** zawierającej mapę z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista map|Tak|Lista komunikatów, w których każda mapa Reprezentuje komunikat.|  
  
 Mapa reprezentująca komunikat musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|message|Tablica bajtów|Tak|Komunikat kodowany przez sieć AMQP 1,0.|  
  
### <a name="set-session-state"></a>Ustaw stan sesji  

Ustawia stan sesji.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Identyfikator sesji|ciąg|Tak|Identyfikator sesji.|  
|stan sesji|Tablica bajtów|Tak|Nieprzezroczyste dane binarne.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — powodzenie; w przeciwnym razie nie powiodło się|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
### <a name="get-session-state"></a>Pobierz stan sesji  

Pobiera stan sesji.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Identyfikator sesji|ciąg|Tak|Identyfikator sesji.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — powodzenie; w przeciwnym razie nie powiodło się|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|stan sesji|Tablica bajtów|Tak|Nieprzezroczyste dane binarne.|  
  
### <a name="enumerate-sessions"></a>Wyliczanie sesji  

Wylicza sesje w jednostce obsługi komunikatów.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|czas ostatniej aktualizacji|sygnatura czasowa|Tak|Filtr w celu uwzględnienia tylko sesji zaktualizowanych po danym czasie.|  
|Pomiń|int|Tak|Pomiń liczbę sesji.|  
|top (pierwsze)|int|Tak|Maksymalna liczba sesji.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — zawiera więcej komunikatów<br /><br /> 204: Brak zawartości — nie ma więcej komunikatów|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Pomiń|int|Tak|Liczba pominiętych sesji, jeśli kod stanu to 200.|  
|sesje — identyfikatory|tablica ciągów|Tak|Tablica identyfikatorów sesji, jeśli kod stanu to 200.|  
  
## <a name="rule-operations"></a>Operacje reguł  
  
### <a name="add-rule"></a>Dodaj regułę  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Nazwa reguły|ciąg|Tak|Nazwa reguły, bez uwzględniania nazw subskrypcji i tematu.|  
|Reguła — opis|map (mapa)|Tak|Opis reguły określony w następnej sekcji.|  
  
Mapa **opisowa reguły** musi zawierać następujące wpisy, w których Filtrowanie **SQL** i filtr **korelacji** wzajemnie się wykluczają:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|SQL — filtr|map (mapa)|Tak|`sql-filter`, zgodnie z opisem w następnej sekcji.|  
|Korelacja — filtr|map (mapa)|Tak|`correlation-filter`, zgodnie z opisem w następnej sekcji.|  
|SQL-reguła-akcja|map (mapa)|Tak|`sql-rule-action`, zgodnie z opisem w następnej sekcji.|  
  
Mapa filtrów SQL musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|expression|ciąg|Tak|Wyrażenie filtru SQL.|  
  
Mapa **korelacji filtru** musi zawierać co najmniej jedną z następujących pozycji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|correlation-id|ciąg|Nie||  
|Identyfikator komunikatu|ciąg|Nie||  
|na wartość|ciąg|Nie||  
|Odpowiedz do|ciąg|Nie||  
|label|ciąg|Nie||  
|Identyfikator sesji|ciąg|Nie||  
|Identyfikator odpowiedzi na sesję|ciąg|Nie||  
|Typ zawartości|ciąg|Nie||  
|properties|map (mapa)|Nie|Mapuje do Service Bus [BrokeredMessage. Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
Mapa **akcji SQL-Rule** musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|expression|ciąg|Tak|Wyrażenie akcji SQL.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — powodzenie; w przeciwnym razie nie powiodło się|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
### <a name="remove-rule"></a>Usuń regułę  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Nazwa reguły|ciąg|Tak|Nazwa reguły, bez uwzględniania nazw subskrypcji i tematu.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — powodzenie; w przeciwnym razie nie powiodło się|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
### <a name="get-rules"></a>Pobierz reguły

#### <a name="request"></a>Żądanie

Komunikat żądania musi zawierać następujące właściwości aplikacji:

|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  

Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|top (pierwsze)|int|Tak|Liczba reguł do pobrania na stronie.|  
|Pomiń|int|Tak|Liczba reguł do pominięcia. Definiuje początkowy indeks (+ 1) na liście reguł. | 

#### <a name="response"></a>Reakcja

Komunikat odpowiedzi zawiera następujące właściwości:

|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — powodzenie; w przeciwnym razie nie powiodło się|  
|rules| Tablica map|Tak|Tablica reguł. Każda reguła jest reprezentowana przez mapę.|

Każdy wpis mapy w tablicy zawiera następujące właściwości:

|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Reguła — opis|Tablica opisanych obiektów|Tak|`com.microsoft:rule-description:list` za pomocą AMQP kodu 0x0000013700000004| 

`com.microsoft.rule-description:list` jest tablicą opisanych obiektów. Tablica obejmuje następujące elementy:

|Indeks|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
| 0 | Tablica opisanych obiektów | Tak | `filter` zgodnie z poniższym opisem. |
| 1 | Tablica opisanego obiektu | Tak | `ruleAction` zgodnie z poniższym opisem. |
| 2 | ciąg | Tak | Nazwa reguły. |

`filter` może być jednego z następujących typów:

| Nazwa deskryptora | Kod deskryptora | Wartość |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtr SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtr korelacji |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Filtr prawdziwy reprezentujący 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Filtr fałszywy reprezentujący 1 = 0 |

`com.microsoft:sql-filter:list` to opisana tablica, która obejmuje:

|Indeks|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
| 0 | ciąg | Tak | Wyrażenie filtru SQL |

`com.microsoft:correlation-filter:list` to opisana tablica, która obejmuje:

|Indeks (jeśli istnieje)|Typ wartości|Zawartość wartości|  
|---------|----------------|--------------|
| 0 | ciąg | Identyfikator korelacji |
| 1 | ciąg | Identyfikator komunikatu |
| 2 | ciąg | Działanie |
| 3 | ciąg | Odpowiedz na |
| 4 | ciąg | Etykieta |
| 5 | ciąg | Identyfikator sesji |
| 6 | ciąg | Odpowiedz na identyfikator sesji|
| 7 | ciąg | Typ zawartości |
| 8 | Mapa | Mapa właściwości zdefiniowanych przez aplikację |

`ruleAction` może być jeden z następujących typów:

| Nazwa deskryptora | Kod deskryptora | Wartość |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Akcja reguły pustej — nie istnieje żadna akcja reguły |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Akcja reguły SQL |

`com.microsoft:sql-rule-action:list` jest tablicą opisanych obiektów, których pierwszy wpis jest ciągiem zawierającym wyrażenie akcji reguły SQL.

## <a name="deferred-message-operations"></a>Operacje odroczonego komunikatu  
  
### <a name="receive-by-sequence-number"></a>Odbierz według numeru sekwencyjnego  

Odbiera odroczone komunikaty według numeru sekwencyjnego.  
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|numery sekwencyjne|Tablica Long|Tak|Numery sekwencji.|  
|Tryb rozliczania odbiorcy|ubyte|Tak|Tryb **rozliczenia odbiornika** określony w AMQP Core v 1.0.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — powodzenie; w przeciwnym razie nie powiodło się|  
|statusDescription|ciąg|Nie|Opis stanu.|  
  
Treść komunikatu odpowiedzi musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|z chmury do urządzenia|Lista map|Tak|Lista komunikatów, gdzie każda mapa Reprezentuje komunikat.|  
  
Mapa reprezentująca komunikat musi zawierać następujące wpisy:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Lock — token|uuid|Tak|Token blokady, jeśli `receiver-settle-mode` jest 1.|  
|message|Tablica bajtów|Tak|Komunikat kodowany przez sieć AMQP 1,0.|  
  
### <a name="update-disposition-status"></a>Aktualizowanie stanu dyspozycji  

Aktualizuje stan dyspozycji odroczonych komunikatów. Ta operacja obsługuje transakcje.
  
#### <a name="request"></a>Żądanie  

Komunikat żądania musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|operation|ciąg|Tak|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nie|Limit czasu serwera operacji (w milisekundach).|  
  
Treść komunikatu żądania musi składać się z sekcji **AMQP-Value** zawierającej **mapę** z następującymi wpisami:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|Dyspozycja — stan|ciąg|Tak|pełni<br /><br /> przerwane<br /><br /> wieszon|  
|tokeny blokady|Tablica identyfikatora UUID|Tak|Tokeny blokady komunikatów do aktualizowania stanu dyspozycji.|  
|utracony — Przyczyna|ciąg|Nie|Może być ustawiona, jeśli stan dyspozycji jest ustawiony na **zawieszone**.|  
|utracony — opis|ciąg|Nie|Może być ustawiona, jeśli stan dyspozycji jest ustawiony na **zawieszone**.|  
|właściwości do modyfikacji|map (mapa)|Nie|Lista Service Bus właściwości komunikatu obsługiwanego przez brokera do zmodyfikowania.|  
  
#### <a name="response"></a>Reakcja  

Komunikat odpowiedzi musi zawierać następujące właściwości aplikacji:  
  
|Klucz|Typ wartości|Wymagane|Zawartość wartości|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Tak|Kod odpowiedzi HTTP [RFC2616]<br /><br /> 200: OK — powodzenie; w przeciwnym razie nie powiodło się|  
|statusDescription|ciąg|Nie|Opis stanu.|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat AMQP i Service Bus, odwiedź następujące linki:

* [Service Bus AMQP — Omówienie]
* [Przewodnik dotyczący protokołu AMQP 1.0]
* [AMQP w Service Bus dla systemu Windows Server]

[Service Bus AMQP — Omówienie]: service-bus-amqp-overview.md
[Przewodnik dotyczący protokołu AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP w Service Bus dla systemu Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)