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
ms.openlocfilehash: 0fb5da965a9b13667b8a128e83a5a4cd2c2b28d7
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691844"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Konfigurowanie dzienników diagnostycznych na potrzeby centrum zdarzeń platformy Azure

Możesz wyświetlić dwa typy dzienników dla Event Hubs platformy Azure:

* **[Dzienniki aktywności](../azure-monitor/platform/platform-logs-overview.md)**: te dzienniki zawierają informacje o operacjach wykonywanych w ramach zadania. Dzienniki są zawsze włączone. Wpisy dziennika aktywności można wyświetlić, wybierając pozycję **Dziennik aktywności** w lewym okienku dla przestrzeni nazw centrum zdarzeń w Azure Portal. Na przykład: "Utwórz lub zaktualizuj przestrzeń nazw", "Utwórz lub zaktualizuj centrum zdarzeń".

    ![Dziennik aktywności dla Event Hubs przestrzeni nazw](./media/event-hubs-diagnostic-logs/activity-log.png)
* **[Dzienniki diagnostyczne](../azure-monitor/platform/platform-logs-overview.md)**: dzienniki diagnostyczne zawierają bogatsze informacje o operacjach i akcjach, które są wykonywane w odniesieniu do przestrzeni nazw za pomocą interfejsu API, lub za pośrednictwem klientów zarządzania w zestawie SDK języka. 
    
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

| Kategoria | Opis | 
| -------- | ----------- | 
| Archiwizuj dzienniki | Program przechwytuje informacje dotyczące [Event Hubs operacji przechwytywania](event-hubs-capture-overview.md) , w tym dzienników związanych z błędami przechwytywania. |
| Dzienniki operacyjne | Przechwyć wszystkie operacje zarządzania wykonywane w przestrzeni nazw platformy Azure Event Hubs. Operacje na danych nie są przechwytywane z powodu dużej ilości operacji na danych, które są wykonywane na platformie Azure Event Hubs. |
| Automatyczne skalowanie dzienników | Przechwytuje operacje autodostrajania wykonane na Event Hubs przestrzeni nazw. |
| Dzienniki koordynatora Kafka | Przechwytuje operacje koordynatora Kafka dotyczące Event Hubs. |
| Kafka dzienniki błędów użytkownika | Przechwytuje informacje o interfejsach API Kafka, które są wywoływane na Event Hubs. |
| Zdarzenie połączenia sieci wirtualnej Event Hubs (VNet) | Przechwytuje informacje o adresach IP i sieciach wirtualnych wysyłających ruch do Event Hubs. |
| Dzienniki użytkownika klucza zarządzanego przez klienta | Przechwytuje operacje związane z kluczem zarządzanym przez klienta. |


Wszystkie dzienniki są przechowywane w formacie JavaScript Object Notation (JSON). Każdy wpis zawiera pola ciągów, które używają formatu opisanego w poniższych sekcjach.

## <a name="archive-logs-schema"></a>Schemat dzienników archiwalnych

Dzienniki archiwum JSON zawierają elementy wymienione w poniższej tabeli:

Nazwa | Opis
------- | -------
TaskName | Opis zadania, które zakończyło się niepowodzeniem
ActivityId | Identyfikator wewnętrzny używany do śledzenia
trackingId | Identyfikator wewnętrzny używany do śledzenia
resourceId | Identyfikator zasobu Azure Resource Manager
eventHub | Pełna nazwa centrum zdarzeń (łącznie z nazwą przestrzeni nazw)
partitionId | Zapisywana partycja centrum zdarzeń
archiveStep | możliwe wartości: ArchiveFlushWriter, DestinationInit
startTime | Czas rozpoczęcia niepowodzenia
powodzenia | Liczba wystąpień awarii
durationInSeconds | Czas trwania awarii
message | Komunikat o błędzie
category | ArchiveLogs

Poniższy kod jest przykładem ciągu JSON dziennika archiwum:

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "000000000-0000-0000-0000-0000000000000",
   "trackingId": "0000000-0000-0000-0000-00000000000000000",
   "resourceId": "/SUBSCRIPTIONS/000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs Namespace Name>",
   "eventHub": "<Event Hub full name>",
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
ActivityId | Wewnętrzny identyfikator używany do śledzenia |
EventName | Nazwa operacji |
resourceId | Identyfikator zasobu Azure Resource Manager |
SubscriptionId | Identyfikator subskrypcji |
EventTimeString | Czas operacji |
EventProperties | Właściwości operacji |
Stan | Stan operacji |
Obiekt wywołujący | Obiekt wywołujący operacji (Azure Portal lub klient zarządzania) |
Kategoria | OperationalLogs |

Poniższy kod jest przykładem ciągu JSON dziennika operacyjnego:

```json
Example:
{
   "ActivityId": "00000000-0000-0000-0000-00000000000000",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/<Event Hubs namespace name>",
   "SubscriptionId": "000000000-0000-0000-0000-000000000000",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"0000000000-0000-0000-0000-000000000000\",\"Namespace\":\"<Namespace Name>\",\"Via\":\"https://<Namespace Name>.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="autoscale-logs-schema"></a>Schemat dzienników automatycznego skalowania
Plik JSON dziennika skalowania automatycznego zawiera elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ---- | ----------- | 
| TrackingId | Identyfikator wewnętrzny, który jest używany na potrzeby śledzenia |
| ResourceId | Azure Resource Manager identyfikator zasobu. |
| Komunikat | Komunikat informacyjny, który zawiera szczegółowe informacje na temat akcji autodostrajania. Komunikat zawiera poprzednią i bieżącą wartość jednostki przepływności dla danego obszaru nazw oraz sposób wyzwalający poznanie jednostek PRZEPŁYWNOŚCI. |

## <a name="kafka-coordinator-logs-schema"></a>Schemat dzienników koordynatora Kafka
Plik JSON dziennika koordynatora Kafka zawiera elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ---- | ----------- | 
| IdentyfikatorŻądania | Identyfikator żądania, który jest używany na potrzeby śledzenia |
| ResourceId | Identyfikator zasobu Azure Resource Manager |
| Operacja | Nazwa operacji wykonywanej w ramach koordynacji grupy |
| ClientId | Identyfikator klienta |
| NamespaceName | Nazwa przestrzeni nazw | 
| SubscriptionId | Identyfikator subskrypcji platformy Azure |
| Komunikat | Komunikat informacyjny lub ostrzegawczy, który zawiera szczegółowe informacje o akcjach wykonywanych podczas koordynacji grupy. |

### <a name="example"></a>Przykład

```json
{
    "RequestId": "FE01001A89E30B020000000304620E2A_KafkaExampleConsumer#0",
    "Operation": "Join.Start",
    "ClientId": "KafkaExampleConsumer#0",
    "Message": "Start join group for new member namespace-name:c:$default:I:KafkaExampleConsumer#0-cc40856f7f3c4607915a571efe994e82, current group size: 0, API version: 2, session timeout: 10000ms, rebalance timeout: 300000ms.",
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "KafkaCoordinatorLogs"
}
```

## <a name="kafka-user-error-logs-schema"></a>Schemat dzienników błędów użytkownika Kafka
Plik JSON dziennika błędów użytkownika Kafka zawiera elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ---- | ----------- |
| TrackingId | Identyfikator śledzenia, który jest używany na potrzeby śledzenia. |
| NamespaceName | Nazwa przestrzeni nazw |
| Eventhub | Nazwa centrum zdarzeń |
| PartitionId | Identyfikator partycji |
| Identyfikator grupy | Identyfikator grupy |
| ClientId | Identyfikator klienta |
| ResourceId | Azure Resource Manager identyfikator zasobu. |
| Komunikat | Komunikat informacyjny, który zawiera szczegółowe informacje o błędzie |

## <a name="event-hubs-virtual-network-connection-event-schema"></a>Schemat zdarzenia połączenia z siecią wirtualną Event Hubs

Plik JSON zdarzenia połączenia sieci wirtualnej (VNet) Event Hubs zawiera elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ---  | ----------- | 
| SubscriptionId | Identyfikator subskrypcji platformy Azure |
| NamespaceName | Nazwa przestrzeni nazw |
| IPAddress | Adres IP klienta łączącego się z usługą Event Hubs |
| Akcja | Akcja wykonywana przez usługę Event Hubs podczas oceniania żądań połączeń. Obsługiwane akcje są **akceptowane** i **odmawiają**połączenia. |
| Przyczyna | Zawiera powód, dla którego akcja została wykonana |
| Liczba | Liczba wystąpień danej akcji |
| ResourceId | Azure Resource Manager identyfikator zasobu. |

### <a name="example"></a>Przykład

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

## <a name="customer-managed-key-user-logs"></a>Dzienniki użytkownika klucza zarządzanego przez klienta
Plik JSON dziennika użytkownika klucza zarządzanego przez klienta zawiera elementy wymienione w poniższej tabeli:

| Nazwa | Opis |
| ---- | ----------- | 
| Kategoria | Typ kategorii wiadomości. Jest to jedna z następujących wartości: **błąd** i **informacje** |
| ResourceId | Wewnętrzny identyfikator zasobu, który zawiera identyfikator subskrypcji platformy Azure i nazwę przestrzeni nazw |
| KeyVault | Nazwa zasobu Key Vault |
| Klucz | Nazwa klucza Key Vault. |
| Wersja | Wersja klucza Key Vault |
| Operacja | Nazwa operacji wykonanej do obsługi żądań |
| Code | Kod stanu |
| Komunikat | Komunikat, który zawiera szczegółowe informacje o błędzie lub komunikat informacyjny |



## <a name="next-steps"></a>Następne kroki
- [Wprowadzenie do Event Hubs](event-hubs-what-is-event-hubs.md)
- [Omówienie interfejsu API usługi Event Hubs](event-hubs-api-overview.md)
- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
