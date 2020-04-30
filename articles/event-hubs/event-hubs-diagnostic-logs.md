---
title: Konfigurowanie dzienników diagnostycznych — centrum zdarzeń Azure | Microsoft Docs
description: Informacje na temat konfigurowania dzienników aktywności i dzienników diagnostycznych dla centrów zdarzeń na platformie Azure.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 04/28/2020
ms.author: shvija
ms.openlocfilehash: 3010ee7b996c9d3e96082edeb9447c960da321bd
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509791"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Konfigurowanie dzienników diagnostycznych na potrzeby centrum zdarzeń platformy Azure

Możesz wyświetlić dwa typy dzienników dla Event Hubs platformy Azure:

* **[Dzienniki aktywności](../azure-monitor/platform/platform-logs-overview.md)**: te dzienniki zawierają informacje o operacjach wykonywanych w ramach zadania. Dzienniki są zawsze włączone. Wpisy dziennika aktywności można wyświetlić, wybierając pozycję **Dziennik aktywności** w lewym okienku dla przestrzeni nazw centrum zdarzeń w Azure Portal. Na przykład: "Utwórz lub zaktualizuj przestrzeń nazw", "Utwórz lub zaktualizuj centrum zdarzeń".

    ![Dziennik aktywności dla Event Hubs przestrzeni nazw](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Dzienniki diagnostyczne](../azure-monitor/platform/platform-logs-overview.md)**: można skonfigurować dzienniki diagnostyczne, aby uzyskać bogatszy widok wszystkiego, co się dzieje z zadaniami. Dzienniki diagnostyczne obejmują działania od momentu utworzenia zadania do momentu usunięcia zadania, w tym aktualizacje i działania, które wystąpiły, gdy zadanie jest uruchomione.

    W poniższej sekcji przedstawiono sposób włączania dzienników diagnostycznych dla przestrzeni nazw Event Hubs.

## <a name="enable-diagnostic-logs"></a>Włączanie dzienników diagnostycznych
Dzienniki diagnostyczne są domyślnie wyłączone. Aby włączyć dzienniki diagnostyczne, wykonaj następujące kroki:

1.  W [Azure Portal](https://portal.azure.com)przejdź do przestrzeni nazw Event Hubs. 
2. Wybierz pozycję **Ustawienia diagnostyki** w obszarze **monitorowanie** w lewym okienku, a następnie wybierz pozycję **+ Dodaj ustawienie diagnostyczne**. 

    ![Strona ustawień diagnostycznych — Dodawanie ustawień diagnostycznych](./media/event-hubs-diagnostic-logs/diagnostic-settings-page.png)
4. W sekcji **szczegóły kategorii** wybierz **typy dzienników diagnostycznych** , które chcesz włączyć. Szczegółowe informacje o tych kategoriach znajdziesz w dalszej części tego artykułu. 
5. W sekcji **szczegóły miejsca docelowego** ustaw wartość docelowy archiwum (miejsce docelowe). na przykład konto magazynu, centrum zdarzeń lub obszar roboczy Log Analytics.

    ![Dodawanie strony ustawień diagnostycznych](./media/event-hubs-diagnostic-logs/aDD-diagnostic-settings-page.png)
6.  Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia diagnostyki.

    Nowe ustawienia zaczną obowiązywać od około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym archiwizowania w okienku **dzienniki diagnostyczne** .

    Więcej informacji o konfigurowaniu diagnostyki znajduje się w temacie [Omówienie dzienników diagnostycznych platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="diagnostic-logs-categories"></a>Kategorie dzienników diagnostycznych

Event Hubs przechwytuje dzienniki diagnostyczne dla następujących kategorii:

- **Dzienniki archiwum**: dzienniki związane z archiwami Event Hubs, w tym dzienniki związane z błędami archiwum.
- **Dzienniki operacyjne**: informacje o tym, co się dzieje w trakcie operacji Event Hubs, w szczególności o typie operacji, w tym o tworzeniu centrum zdarzeń, używanych zasobach i stanie operacji.
- **Automatyczne skalowanie dzienników**: informacje o operacjach skalowania automatycznego wykonywane na Event Hubs przestrzeni nazw. 
- **Dzienniki koordynatora Kafka** -informacje na temat operacji koordynatora Kafka związanych z Event Hubs. 
- **Kafka dzienniki użytkownika**: informacje o operacjach użytkownika Kafka związanych z Event Hubs. 
- **Event Hubs zdarzenie połączenia sieci wirtualnej (VNET)**: informacje dotyczące Event Hubs zdarzeń połączenia z siecią wirtualną. 
- **Dzienniki użytkownika z kluczami zarządzanymi przez klienta**: informacje o operacjach związanych z kluczem zarządzanym przez klienta. 


    Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis zawiera pola ciągów, które używają formatu opisanego w poniższych sekcjach.

## <a name="archive-logs-schema"></a>Schemat dzienników archiwalnych

Dzienniki archiwum JSON zawierają elementy wymienione w poniższej tabeli:

Nazwa | Opis
------- | -------
TaskName | Opis zadania, które zakończyło się niepowodzeniem.
ActivityId | Wewnętrzny identyfikator używany do śledzenia.
trackingId | Wewnętrzny identyfikator używany do śledzenia.
resourceId | Azure Resource Manager identyfikator zasobu.
eventHub | Pełna nazwa centrum zdarzeń (łącznie z nazwą przestrzeni nazw).
partitionId | Partycja centrum zdarzeń, w której jest zapisywana.
archiveStep | ArchiveFlushWriter
startTime | Godzina rozpoczęcia niepowodzenia.
powodzenia | Liczba wystąpień niepowodzeń.
durationInSeconds | Czas trwania błędu.
message | Komunikat o błędzie.
category | ArchiveLogs

Poniższy kod jest przykładem ciągu JSON dziennika archiwum:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

## <a name="operational-logs-schema"></a>Schemat dzienników operacyjnych

Ciągi JSON dziennika operacyjnego zawierają elementy wymienione w poniższej tabeli:

Nazwa | Opis
------- | -------
ActivityId | Wewnętrzny identyfikator używany do śledzenia celu.
EventName | nazwa operacji.  
resourceId | Azure Resource Manager identyfikator zasobu.
SubscriptionId | Identyfikator subskrypcji.
EventTimeString | Czas operacji.
EventProperties | Właściwości operacji.
Stan | Stan operacji.
Obiekt wywołujący | Obiekt wywołujący operacji (Azure Portal lub klient zarządzania).
category | OperationalLogs

Poniższy kod jest przykładem ciągu JSON dziennika operacyjnego:

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Schemat dzienników automatycznego skalowania
Plik JSON dziennika skalowania automatycznego zawiera elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ---- | ----------- | 
| trackingId | Identyfikator wewnętrzny, który jest używany na potrzeby śledzenia |
| resourceId | Identyfikator wewnętrzny, który zawiera identyfikator subskrypcji platformy Azure i nazwę przestrzeni nazw |
| message | Komunikat informacyjny, który zawiera szczegółowe informacje na temat akcji autodostrajania. Komunikat zawiera poprzednią i bieżącą wartość jednostki przepływności dla danego obszaru nazw oraz sposób wyzwalający poznanie jednostek PRZEPŁYWNOŚCI. |

## <a name="kafka-coordinator-logs-schema"></a>Schemat dzienników koordynatora Kafka
Plik JSON dziennika koordynatora Kafka zawiera elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ---- | ----------- | 
| IdentyfikatorŻądania | Identyfikator żądania, który jest używany na potrzeby śledzenia |
| resourceId | Identyfikator wewnętrzny, który zawiera identyfikator subskrypcji platformy Azure i nazwę przestrzeni nazw |
| operationName | Nazwa operacji wykonywanej w ramach koordynacji grupy |
| clientId | Identyfikator klienta |
| namespaceName | Nazwa przestrzeni nazw | 
| subscriptionId | Identyfikator subskrypcji platformy Azure |
| message | Komunikat informacyjny, który zawiera szczegółowe informacje o akcjach wykonywanych podczas koordynacji grupy konsumentów. |

## <a name="kafka-user-error-logs-schema"></a>Schemat dzienników błędów użytkownika Kafka
Plik JSON dziennika błędów użytkownika Kafka zawiera elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ---- | ----------- |
| trackingId | Identyfikator śledzenia, który jest używany na potrzeby śledzenia. |
| namespaceName | Nazwa przestrzeni nazw |
| eventhub | Nazwa centrum zdarzeń |
| partitionId | Identyfikator partycji |
| groupId | Identyfikator grupy |
| ClientId | Identyfikator klienta |
| resourceId | Identyfikator wewnętrzny, który zawiera identyfikator subskrypcji platformy Azure i nazwę przestrzeni nazw |
| message | Komunikat informacyjny, który zawiera szczegółowe informacje o błędzie |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Schemat zdarzenia połączenia z siecią wirtualną Event Hubs

Plik JSON zdarzenia połączenia sieci wirtualnej (VNet) Event Hubs zawiera elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ---  | ----------- | 
| subscriptionId | Identyfikator subskrypcji platformy Azure |
| namespaceName | Nazwa przestrzeni nazw |
| Adresu | Adres IP klienta łączącego się z usługą Event Hubs |
| action | Akcja wykonywana przez usługę Event Hubs podczas oceniania żądań połączeń. Obsługiwane akcje to **AcceptConnection** i **RejectConnection**. |
| reason | Zawiera powód, dla którego akcja została wykonana |
| count | Liczba wystąpień danej akcji |
| resourceId | Wewnętrzny identyfikator zasobu, który zawiera identyfikator subskrypcji i nazwę przestrzeni nazw. |

## <a name="customer-managed-key-user-logs"></a>Dzienniki użytkownika klucza zarządzanego przez klienta
Plik JSON dziennika użytkownika klucza zarządzanego przez klienta zawiera elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ---- | ----------- | 
| category | Typ kategorii wiadomości. Jest to jedna z następujących wartości: **błąd** i **informacje** |
| resourceId | Wewnętrzny identyfikator zasobu, który zawiera identyfikator subskrypcji platformy Azure i nazwę przestrzeni nazw |
| keyVault | Nazwa zasobu Key Vault |
| key | Nazwa klucza Key Vault. |
| Wersja | Wersja klucza Key Vault |
| operacje | Nazwa operacji wykonanej do obsługi żądań |
| kod | Kod stanu |
| message | Komunikat, który zawiera szczegółowe informacje o błędzie lub komunikat informacyjny |



## <a name="next-steps"></a>Następne kroki
- [Wprowadzenie do Event Hubs](event-hubs-what-is-event-hubs.md)
- [Omówienie interfejsu API usługi Event Hubs](event-hubs-api-overview.md)
- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
