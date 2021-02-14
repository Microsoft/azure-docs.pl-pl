---
title: Azure App Service jako źródło Event Grid
description: W tym artykule opisano sposób użycia Azure App Service jako źródła zdarzeń Event Grid. Zawiera schemat i linki do samouczka oraz artykuły z artykułami.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: jafreebe
ms.openlocfilehash: 224cb44ef7293f47855b5b418830a7fc4bf5ecd1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366657"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń Azure App Service. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md). Zawiera również listę przewodników Szybki Start i samouczków do użycia Azure App Service jako źródło zdarzenia.

## <a name="available-event-types"></a>Dostępne typy zdarzeń

Azure App Service emituje następujące typy zdarzeń

|    Typ zdarzenia                                             |    Opis                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft. Web/Lokacje. BackupOperationStarted             |    Wyzwalane po rozpoczęciu tworzenia kopii zapasowej                             |
|    Microsoft. Web/Lokacje. BackupOperationCompleted           |    Wyzwalane po zakończeniu tworzenia kopii zapasowej                           |
|    Microsoft. Web/Lokacje. BackupOperationFailed              |    Wyzwalane w przypadku niepowodzenia tworzenia kopii zapasowej                              |
|    Microsoft. Web/Lokacje. RestoreOperationStarted            |    Wyzwalane po rozpoczęciu przywracania z kopii zapasowej        |
|    Microsoft. Web/Lokacje. RestoreOperationCompleted          |    Wyzwalane, gdy ukończono przywracanie z kopii zapasowej      |
|    Microsoft. Web/Lokacje. RestoreOperationFailed             |    Wyzwalane, gdy przywracanie z kopii zapasowej zakończyło się niepowodzeniem         |
|    Microsoft. Web/Lokacje. SlotSwapStarted                    |    Wyzwalane po rozpoczęciu wymiany gniazd                          |
|    Microsoft. Web/Lokacje. SlotSwapCompleted                  |    Wyzwalane po zakończeniu wymiany gniazd                      |
|    Microsoft. Web/Lokacje. SlotSwapFailed                     |    Wyzwalane, gdy nastąpiło niepowodzenie wymiany gniazd                           |
|    Microsoft. Web/Lokacje. SlotSwapWithPreviewStarted         |    Wyzwalane po rozpoczęciu wymiany gniazd z podglądem           |
|    Microsoft. Web/Lokacje. SlotSwapWithPreviewCancelled       |    Wyzwalane po anulowaniu zamiany gniazda z podglądem    |
|    Microsoft. Web/Lokacje. AppUpdated. uruchomiono ponownie               |    Wyzwalane, gdy lokacja została ponownie uruchomiona                      |
|    Microsoft. Web/Lokacje. AppUpdated. zatrzymana                 |    Wyzwalane, gdy lokacja została zatrzymana                          |
|    Microsoft. Web/Lokacje. AppUpdated.ChangedAppSettings      |    Wyzwalane po zmianie ustawień aplikacji witryny             |
|    Microsoft. Web/dopuszczalna. AppServicePlanUpdated        |    Wyzwalane po zaktualizowaniu planu App Service                 |

## <a name="properties-common-to-all-events"></a>Właściwości wspólne dla wszystkich zdarzeń

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)
Po wyzwoleniu zdarzenia usługa Event Grid wysyła dane dotyczące tego zdarzenia w celu subskrybowania punktu końcowego.
Ta sekcja zawiera przykład sposobu, w jaki będą wyglądały dane dla każdego zdarzenia. Każde zdarzenie ma następujące dane najwyższego poziomu:

|     Właściwość          |     Typ     |     Opis                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `topic`              |    ciąg    |    Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid.                                      |
|    `subject`            |    ciąg    |    Ścieżka zdefiniowana przez program Publisher do tematu zdarzenia.                                                                                              |
|    `eventType`          |    ciąg    |    Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń.                                                                                  |
|    `eventTime`          |    ciąg    |    Czas generowania zdarzenia na podstawie czasu UTC dostawcy.                                                                         |
|    `id`                 |    ciąg    |    Unikatowy identyfikator zdarzenia.                                                                                                            |
|    `data`               |    object    |    Dane zdarzenia magazynu obiektów BLOB.                                                                                                                    |
|    `dataVersion`        |    ciąg    |    Wersja schematu obiektu danych. Wydawca definiuje wersję schematu.                                                          |
|    `metadataVersion`    |    ciąg    |    Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Event Grid udostępnia tę wartość.    |

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

Po wyzwoleniu zdarzenia usługa Event Grid wysyła dane dotyczące tego zdarzenia w celu subskrybowania punktu końcowego.
Ta sekcja zawiera przykład sposobu, w jaki będą wyglądały dane dla każdego zdarzenia. Każde zdarzenie ma następujące dane najwyższego poziomu:

|     Właściwość          |     Typ     |     Opis                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    `source`              |    ciąg    |    Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid.                                      |
|    `subject`            |    ciąg    |    Ścieżka zdefiniowana przez program Publisher do tematu zdarzenia.                                                                                              |
|    `type`          |    ciąg    |    Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń.                                                                                  |
|    `time`          |    ciąg    |    Czas generowania zdarzenia na podstawie czasu UTC dostawcy.                                                                         |
|    `id`                 |    ciąg    |    Unikatowy identyfikator zdarzenia.                                                                                                            |
|    `data`               |    object    |    Dane zdarzenia magazynu obiektów BLOB.                                                                                                                    |
| `specversion` | ciąg | Wersja specyfikacji schematu CloudEvents. |

---

## <a name="example-events"></a>Przykładowe zdarzenia

### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.BackupOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```
# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.BackupOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
    "specversion": "1.0"
}
```

---

Obiekt danych zawiera następujące właściwości:

|    Właściwość                |    Typ      |    Opis                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Szczegóły akcji w aplikacji                                                                                       |
|    `action`                  |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    `name`                    |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    `clientRequestId`         |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    `correlationRequestId`    |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    `requestId`               |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    `address`                 |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    `verb`                    |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |

### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.RestoreOperationStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.RestoreOperationStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

Obiekt danych zawiera następujące właściwości:

|    Właściwość                |    Typ      |    Opis                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Szczegóły akcji w aplikacji                                                                                       |
|    `action`                  |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    `name`                    |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    `clientRequestId`         |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    `correlationRequestId`    |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    `requestId`               |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    `address`                 |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    `verb`                    |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |

### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

Obiekt danych zawiera następujące właściwości:

|    Właściwość                |    Typ      |    Opis                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Szczegóły akcji w aplikacji                                                                                       |
|    `action`                 |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    `name`                    |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    `clientRequestId`         |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    `correlationRequestId`    |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|   `requestId`               |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    `address`                 |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    `verb`                    |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |
|    `sourceSlot`              |    ciąg    |    Miejsce źródłowe wymiany                                                                                       |

### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "eventTime": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

```json
{
    "id": "7c5d6de5-eb70-4de2-b788-c52a544e68b8",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.SlotSwapWithPreviewStarted",
    "time": "2020-01-28T18:26:51.7194887Z",
    "data": {
        "appEventTypeDetail": null,
        "siteName": "<site-name>",
        "clientRequestId": "922f4841-20d9-4dd6-8c5b-23f0d85e5592",
        "correlationRequestId": "9ac46505-2b8a-4e06-834c-05ffbe2e8c3a",
        "requestId": "765117aa-eaf8-4bd2-a644-1dbf69c7b0fd",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production",
        "verb": "POST",
        "sourceSlot": "staging",
        "targetSlot": "production"
    },
    "specversion": "1.0"
}
```

---

Obiekt danych zawiera następujące właściwości:

|    Właściwość                |    Typ      |    Opis                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Szczegóły akcji w aplikacji                                                                                       |
|    `action`                 |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    `name`                    |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    `clientRequestId`         |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    `correlationRequestId`    |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    `requestId`               |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    `address`                 |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    `verb`                    |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |

### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated. restarted, AppUpdated. zatrzymany, AppUpdated. ChangedAppSettings

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "topic": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "eventType": "Microsoft.Web.AppUpdated",
    "eventTime": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "siteName": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "dataVersion": "1'",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

```json
{
    "id": "b74ea56b-2a3f-4de5-a5d7-38e60c81cf23",
    "source": "/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>",
    "subject": "/Microsoft.Web/sites/<site-name>",
    "type": "Microsoft.Web.AppUpdated",
    "time": "2020-01-28T18:22:30.2760952Z",
    "data": {
        "appEventTypeDetail": {
            "action": "Stopped"
        },
        "siteName": "<site-name>",
        "clientRequestId": "64a5e0aa-7cee-4ff1-9093-b9197b820014",
        "correlationRequestId": "25bb36a5-8f6c-4f04-b615-e9a0ee045756",
        "requestId": "f2e8eb3f-b190-42de-b99e-6acefe587374",
        "address": "/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop",
        "verb": "POST"
    },
    "specversion": "1.0"
}
```

---

Obiekt danych ma następujące właściwości:

|    Właściwość                |    Typ      |    Opis                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appEventTypeDetail`      |    object    |    Szczegóły akcji w aplikacji                                                                                       |
|    `action`                  |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    `name`                    |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    `clientRequestId`         |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    `correlationRequestId`    |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    `requestId`               |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    `address`                 |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    `verb`                    |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |

### <a name="serverfarmsappserviceplanupdated"></a>Dopuszczalna. AppServicePlanUpdated

# <a name="event-grid-event-schema"></a>[Schemat zdarzeń usługi Event Grid](#tab/event-grid-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "topic": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "eventType": "Microsoft.Web.AppServicePlanUpdated",
    "eventTime": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "dataVersion": "1",
    "metaDataVersion": "1"
}
```

# <a name="cloud-event-schema"></a>[Schemat zdarzeń w chmurze](#tab/cloud-event-schema)

```json
{
    "id": "56501672-9150-40e1-893a-18420c7fdbf7",
    "source": "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
    "subject": "/Microsoft.Web/serverfarms/<plan-name>",
    "type": "Microsoft.Web.AppServicePlanUpdated",
    "time": "2020-01-28T18:22:23.5516004Z",
    "data": {
        "serverFarmEventTypeDetail": {
            "stampKind": "Public",
            "action": "Updated",
            "status": "Started"
        },
        "serverFarmId": "0",
        "sku": {
            "name": "P1v2",
            "tier": "PremiumV2",
            "size": "P1v2",
            "family": "Pv2",
            "capacity": 1
        },
        "clientRequestId": "8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId": "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId": "b973a8e6-6949-4783-b44c-ac778be831bb",
        "address": "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb": "PUT"
    },
    "specversion": "1.0"
}
```

---

Obiekt danych ma następujące właściwości:

|    Właściwość                         |    Typ      |    Opis                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    `appServicePlanEventTypeDetail`    |    object    |    Szczegóły akcji w planie usługi App Service                                                                          |
|    `stampKind`                        |    ciąg    |    Rodzaj środowiska, w którym jest plan usługi App Service                                                                     |
|    `action`                           |    ciąg    |    Typ akcji w planie usługi App Service                                                                            |
|    `status`                           |    ciąg    |    Stan operacji w planie usługi App Service                                                                   |
|    `sku`                              |    object    |    jednostka SKU planu usługi App Service                                                                                       |
|    `name`                             |    ciąg    |    Nazwa planu usługi App Service                                                                                      |
|    `Tier`                             |    ciąg    |    warstwa planu usługi App Service                                                                                      |
|    `Size`                             |    ciąg    |    rozmiar planu usługi App Service                                                                                      |
|    `Family`                           |    ciąg    |    Rodzina planu usługi App Service                                                                                        |
|    `Capacity`                         |    ciąg    |    Pojemność planu usługi App Service                                                                                      |
|    `action`                           |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    `name`                             |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    `clientRequestId`                  |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    `correlationRequestId`             |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    `requestId`                        |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    `address`                         |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    `verb`                             |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md)