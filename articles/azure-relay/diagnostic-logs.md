---
title: Dzienniki diagnostyczne dla Połączenia hybrydowe
description: Ten artykuł zawiera omówienie wszystkich dzienników działań i diagnostyki, które są dostępne dla Azure Relay.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 9b459750ad1445da89a8e89a10a35b878bfb64e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100590875"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Włączanie dzienników diagnostycznych dla Azure Relay Połączenia hybrydowe
Po rozpoczęciu korzystania z Azure Relay Połączenia hybrydowe można monitorować, jak i kiedy odbiorniki i nadawcy są otwierane i zamykane oraz Połączenia hybrydowe jak są tworzone i wysyłane wiadomości. Ten artykuł zawiera omówienie dzienników działań i diagnostyki dostarczonych przez usługę Azure Relay. 

Można wyświetlić dwa typy dzienników dla Azure Relay:

- [Dzienniki aktywności](../azure-monitor/essentials/platform-logs-overview.md): te dzienniki zawierają informacje o operacjach wykonywanych w odniesieniu do przestrzeni nazw w Azure Portal lub za pomocą szablonu Azure Resource Manager. Te dzienniki są zawsze włączone. Na przykład: "Utwórz lub zaktualizuj przestrzeń nazw", "Utwórz lub zaktualizuj połączenie hybrydowe". 
- [Dzienniki diagnostyczne](../azure-monitor/essentials/platform-logs-overview.md): można skonfigurować dzienniki diagnostyczne, aby uzyskać bogatszy widok wszystkich elementów, które są wykonywane z operacjami i akcjami, które są wykonywane w odniesieniu do przestrzeni nazw za pomocą interfejsu API lub zestawu SDK języka.

## <a name="view-activity-logs"></a>Wyświetlanie dzienników aktywności
Aby wyświetlić dzienniki aktywności dla obszaru nazw Azure Relay, przejdź do strony **dziennika aktywności** w Azure Portal.

![Azure Relay — dziennik aktywności](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Włączanie dzienników diagnostycznych

> [!NOTE]
> Dzienniki diagnostyczne są dostępne tylko dla Połączenia hybrydowe, a nie do przekaźnika Windows Communication Foundation (WCF).

Aby włączyć dzienniki diagnostyczne, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com)przejdź do przestrzeni nazw Azure Relay, a następnie w obszarze **monitorowanie** wybierz pozycję  **Ustawienia diagnostyczne**.
1. Na stronie **Ustawienia diagnostyki** wybierz pozycję **Dodaj ustawienie diagnostyczne**.  

   ![Link "Dodaj ustawienie diagnostyczne"](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Skonfiguruj ustawienia diagnostyki, wykonując następujące czynności:
    1. W polu **Nazwa** wprowadź nazwę ustawień diagnostycznych.  
    2. W polu Typ dziennika wybierz pozycję **HybridConnectionsEvent** . 
    3. Wybierz jeden z następujących trzech **miejsc docelowych** dla dzienników diagnostycznych:  
        1. W przypadku wybrania opcji **Archiwizuj na koncie magazynu** Skonfiguruj konto magazynu, na którym będą przechowywane dzienniki diagnostyczne.  
        2. W przypadku wybrania **strumienia do centrum zdarzeń** Skonfiguruj centrum zdarzeń, do którego chcesz przesyłać strumieniowo dzienniki diagnostyczne.
        3. W przypadku wybrania opcji **Wyślij do log Analytics** Określ, które wystąpienie log Analytics zostanie wysłane do diagnostyki.  

        ![Przykładowe ustawienia diagnostyki](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia.

Nowe ustawienia zaczną obowiązywać od około 10 minut. Dzienniki są wyświetlane w skonfigurowanym miejscu docelowym archiwizowania w okienku **dzienniki diagnostyczne** . Więcej informacji o konfigurowaniu ustawień diagnostycznych znajduje się w temacie [Omówienie dzienników diagnostyki platformy Azure](../azure-monitor/essentials/platform-logs-overview.md).


## <a name="schema-for-hybrid-connections-events"></a>Schemat zdarzeń połączeń hybrydowych
Ciągi JSON połączeń hybrydowych dziennika zdarzeń zawierają elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ------- | ------- |
| ResourceId | Identyfikator zasobu Azure Resource Manager |
| ActivityId | Wewnętrzny identyfikator używany do identyfikowania określonej operacji. Może być również znana jako "TrackingId" |
| Punkt końcowy | Adres zasobu przekaźnika |
| OperationName | Typ rejestrowanej operacji Połączenia hybrydowe |
| EventTimeString | Sygnatura czasowa UTC rekordu dziennika |
| Komunikat | Szczegółowy komunikat zdarzenia |
| Kategoria | Kategoria zdarzenia. Obecnie występuje tylko `HybridConnectionsEvents` . 


## <a name="sample-hybrid-connections-event"></a>Zdarzenie przykładowego połączenia hybrydowego
Oto przykładowe zdarzenie połączeń hybrydowych w formacie JSON. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>Zdarzenia i operacje przechwycone w dziennikach diagnostycznych

| Operacja | Opis | 
| --------- | ----------- | 
| AuthorizationFailed | Autoryzacja nie powiodła się.|
| InvalidSasToken | Nieprawidłowy token sygnatury dostępu współdzielonego. | 
| ListenerAcceptingConnection | Odbiornik akceptuje połączenie. |
| ListenerAcceptingConnectionTimeout | Upłynął limit czasu połączenia akceptującego odbiornik. |
| ListenerAcceptingHttpRequestFailed | Żądanie HTTP akceptujące odbiornik nie powiodło się z powodu wyjątku. |
| ListenerAcceptingRequestTimeout | Upłynął limit czasu żądania akceptacji odbiornika. |  
| ListenerClosingFromExpiredToken | Odbiornik jest zamykany, ponieważ token zabezpieczający wygasł. | 
| ListenerRejectedConnection | Odbiornik odrzucił połączenie. |
| ListenerReturningHttpResponse | Odbiornik zwraca odpowiedź HTTP. |  
| ListenerReturningHttpResponseFailed | Odbiornik zwraca odpowiedź HTTP z kodem błędu. | 
 ListenerSentHttpResponse | Usługa przekaźnika odebrała odpowiedź HTTP od odbiornika. | 
| ListenerUnregistered | Odbiornik nie jest zarejestrowany. | 
| ListenerUnresponsive | Odbiornik nie odpowiada podczas zwracania odpowiedzi. | 
| MessageSendingToListener | Wiadomość jest wysyłana do odbiornika. |
| MessageSentToListener | Wiadomość jest wysyłana do odbiornika. | 
| NewListenerRegistered | Zarejestrowano nowy odbiornik. |
| NewSenderRegistering | Trwa rejestrowanie nowego nadawcy. | 
| ProcessingRequestFailed | Przetwarzanie operacji połączenia hybrydowego nie powiodło się. | 
| SenderConnectionClosed | Połączenie nadawcy zostało zamknięte. |
| SenderListenerConnectionEstablished | Pomyślnie nawiązano połączenie nadawcy i odbiornika. |
| SenderSentHttpRequest | Nadawca wysłał żądanie HTTP. | 


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Relay, zobacz:

* [Wprowadzenie do Azure Relay](relay-what-is-it.md)
* [Wprowadzenie do połączeń hybrydowych usługi Relay](relay-hybrid-connections-dotnet-get-started.md)
