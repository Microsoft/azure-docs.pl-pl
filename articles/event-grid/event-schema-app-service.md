---
title: Azure App Service jako źródło Event Grid
description: W tym artykule opisano sposób użycia Azure App Service jako źródła zdarzeń Event Grid. Zawiera schemat i linki do samouczka oraz artykuły z artykułami.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jafreebe
ms.openlocfilehash: 2465b2f260ed6c174b762fcf64a71100a148254d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86106715"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Azure App Service jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń Azure App Service. Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md). Zawiera również listę przewodników Szybki Start i samouczków do użycia Azure App Service jako źródło zdarzenia.

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

### <a name="available-event-types"></a>Dostępne typy zdarzeń

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

### <a name="the-contents-of-an-event-response"></a>Zawartość odpowiedzi na zdarzenie

Po wyzwoleniu zdarzenia usługa Event Grid wysyła dane dotyczące tego zdarzenia w celu subskrybowania punktu końcowego.
Ta sekcja zawiera przykład sposobu, w jaki będą wyglądały dane dla każdego zdarzenia. Każde zdarzenie ma następujące dane najwyższego poziomu:

|     Właściwość          |     Type     |     Opis                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    temat              |    ciąg    |    Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid.                                      |
|    subject            |    ciąg    |    Ścieżka zdefiniowana przez program Publisher do tematu zdarzenia.                                                                                              |
|    eventType          |    ciąg    |    Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń.                                                                                  |
|    eventTime          |    ciąg    |    Czas generowania zdarzenia na podstawie czasu UTC dostawcy.                                                                         |
|    identyfikator                 |    ciąg    |    Unikatowy identyfikator zdarzenia.                                                                                                            |
|    dane               |    object    |    Dane zdarzenia magazynu obiektów BLOB.                                                                                                                    |
|    dataVersion        |    ciąg    |    Wersja schematu obiektu danych. Wydawca definiuje wersję schematu.                                                          |
|    metadataVersion    |    ciąg    |    Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Event Grid udostępnia tę wartość.    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.BackupOperationStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

Obiekt danych zawiera następujące właściwości:

|    Właściwość                |    Type      |    Opis                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Szczegóły akcji w aplikacji                                                                                       |
|    akcja                  |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    name                    |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    Identyfikatorem żądania klienta         |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    correlationRequestId    |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    IdentyfikatorŻądania               |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    adres                 |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    czasownik                    |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.RestoreOperationStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: { 
            action: "Started" 
        },
        siteName: "<site-name>",
        clientRequestId: "None",
        correlationRequestId: "None",
        requestId: "292f499d-04ee-4066-994d-c2df57b99198",
        address: "None",
        verb: "POST"
    }
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Obiekt danych zawiera następujące właściwości:

|    Właściwość                |    Type      |    Opis                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Szczegóły akcji w aplikacji                                                                                       |
|    akcja                  |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    name                    |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    Identyfikatorem żądania klienta         |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    correlationRequestId    |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    IdentyfikatorŻądania               |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    adres                 |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    czasownik                    |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Obiekt danych zawiera następujące właściwości:

|    Właściwość                |    Type      |    Opis                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Szczegóły akcji w aplikacji                                                                                       |
|    akcja                  |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    name                    |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    Identyfikatorem żądania klienta         |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    correlationRequestId    |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    IdentyfikatorŻądania               |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    adres                 |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    czasownik                    |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |
|    sourceSlot              |    ciąg    |    Miejsce źródłowe wymiany                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Obiekt danych zawiera następujące właściwości:

|    Właściwość                |    Type      |    Opis                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Szczegóły akcji w aplikacji                                                                                       |
|    akcja                  |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    name                    |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    Identyfikatorem żądania klienta         |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    correlationRequestId    |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    IdentyfikatorŻądania               |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    adres                 |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    czasownik                    |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated. restarted, AppUpdated. zatrzymany, AppUpdated. ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Obiekt danych ma następujące właściwości:

|    Właściwość                |    Type      |    Opis                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Szczegóły akcji w aplikacji                                                                                       |
|    akcja                  |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    name                    |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    Identyfikatorem żądania klienta         |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    correlationRequestId    |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    IdentyfikatorŻądania               |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    adres                 |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    czasownik                    |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Dopuszczalna. AppServicePlanUpdated

```js
{
   id: "56501672-9150-40e1-893a-18420c7fdbf7",
   subject: "/Microsoft.Web/serverfarms/<plan-name>",
   eventType: "Microsoft.Web.AppServicePlanUpdated",
   eventTime: "2020-01-28T18:22:23.5516004Z",
   data: {
        serverFarmEventTypeDetail: {
            stampKind: "Public",
            action: "Updated",
            status: "Started"
        },
        serverFarmId: "0",
        sku: {
            name: "P1v2",
            tier: "PremiumV2",
            size: "P1v2",
            family: "Pv2",
            capacity: 1
        },
        clientRequestId: "8f880321-a991-45c7-b743-6ff63fe4c004",
        correlationRequestId: "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        requestId: "b973a8e6-6949-4783-b44c-ac778be831bb",
        address: "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        verb: "PUT"
   },
   topic: "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
   dataVersion: "1",
   metaDataVersion: "1"
}
```

Obiekt danych ma następujące właściwości:

|    Właściwość                         |    Type      |    Opis                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    object    |    Szczegóły akcji w planie usługi App Service                                                                          |
|    stampKind                        |    ciąg    |    Rodzaj środowiska, w którym jest plan usługi App Service                                                                     |
|    akcja                           |    ciąg    |    Typ akcji w planie usługi App Service                                                                            |
|    status                           |    ciąg    |    Stan operacji w planie usługi App Service                                                                   |
|    sku                              |    object    |    jednostka SKU planu usługi App Service                                                                                       |
|    name                             |    ciąg    |    Nazwa planu usługi App Service                                                                                      |
|    Warstwa                             |    ciąg    |    warstwa planu usługi App Service                                                                                      |
|    Rozmiar                             |    ciąg    |    rozmiar planu usługi App Service                                                                                      |
|    Family                           |    ciąg    |    Rodzina planu usługi App Service                                                                                        |
|    Pojemność                         |    ciąg    |    Pojemność planu usługi App Service                                                                                      |
|    akcja                           |    ciąg    |    Typ akcji dla operacji                                                                                   |
|    name                             |    ciąg    |    Nazwa witryny sieci Web, która wymagała tego zdarzenia                                                                          |
|    Identyfikatorem żądania klienta                  |    ciąg    |    Identyfikator żądania klienta wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie         |
|    correlationRequestId             |    ciąg    |    Identyfikator żądania korelacji wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie    |
|    IdentyfikatorŻądania                        |    ciąg    |    Identyfikator żądania wygenerowany przez usługę App Service dla operacji interfejsu API lokacji, która wyzwoliła to zdarzenie                |
|    adres                          |    ciąg    |    Adres URL żądania HTTP dla tej operacji                                                                                |
|    czasownik                             |    ciąg    |    Czasownik HTTP dla tej operacji                                                                                       |
