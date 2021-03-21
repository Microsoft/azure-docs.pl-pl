---
title: Obsługa zdarzeń
titleSuffix: An Azure Communication Services concept document
description: Użyj Azure Event Grid, aby wyzwolić procesy w oparciu o akcje wykonywane w usłudze komunikacyjnej.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 34e3563849e4d4d802d4e27d1db4cc18ec01978a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490589"
---
# <a name="event-handling-in-azure-communication-services"></a>Obsługa zdarzeń w usłudze Azure Communications Services

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Usługi komunikacyjne platformy Azure integrują się z [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) , aby zapewnić niezawodne i skalowalne powiadomienia o zdarzeniach w czasie rzeczywistym. Celem tego artykułu jest ułatwienie konfigurowania aplikacji do nasłuchiwania zdarzeń usług komunikacyjnych. Na przykład możesz chcieć zaktualizować bazę danych, utworzyć element roboczy i dostarczyć Powiadomienie wypychane za każdym razem, gdy wiadomość SMS zostanie odebrana przy użyciu numeru telefonu skojarzonego z zasobem usług komunikacyjnych.

Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która używa modelu publikowania/subskrybowania. Event Grid ma wbudowaną obsługę usług platformy Azure, takich jak [Azure Functions](../../azure-functions/functions-overview.md) i [Azure Logic Apps](../../azure-functions/functions-overview.md). Może ona dostarczać alerty zdarzeń do usług innych niż Azure przy użyciu elementów webhook. Aby zapoznać się z pełną listą programów obsługi zdarzeń obsługiwanych przez Event Grid, zobacz [wprowadzenie do Azure Event Grid](../../event-grid/overview.md).

:::image type="content" source="https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png" alt-text="Diagram przedstawiający model zdarzeń Azure Event Grid.":::

> [!NOTE]
> Aby dowiedzieć się więcej o tym, jak zapamiętasz dane względem obsługi zdarzeń, zapoznaj się z [dokumentacją dotyczącą pojęć zamieszkania](./privacy.md)

## <a name="events-types"></a>Typy zdarzeń

Funkcja Event Grid używa [subskrypcji zdarzeń](../../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów.

Usługi komunikacyjne platformy Azure emitują następujące typy zdarzeń:

| Typ zdarzenia                                                  | Opis                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft. Communications. SMSReceived                         | Opublikowano po odebraniu wiadomości SMS przez numer telefonu skojarzony z usługą komunikacji. |
| Microsoft. Communications. SMSDeliveryReportReceived           | Opublikowany po odebraniu raportu o dostarczeniu dla wiadomości SMS wysłanej przez usługę komunikacyjną.     |
| Microsoft. Communications. ChatMessageReceived                 | Opublikowano po odebraniu komunikatu dla użytkownika w wątku rozmowy, do którego należy.        |
| Microsoft. Communications. ChatMessageEdited                   | Opublikowano, gdy komunikat jest edytowany w wątku rozmowy, do którego należy użytkownik.                |
| Microsoft. Communications. ChatMessageDeleted                  | Opublikowano, gdy wiadomość zostanie usunięta w wątku czatu, do którego należy użytkownik.               |
| Microsoft. Communications. ChatThreadCreatedWithUser           | Opublikowano, gdy użytkownik jest dodawany jako członek w momencie utworzenia wątku rozmowy.           |
| Microsoft. Communications. ChatThreadWithUserDeleted           | Opublikowano, gdy wątek rozmowy został usunięty, którego członkiem jest użytkownik.                           |
| Microsoft. Communications. ChatThreadPropertiesUpdatedPerUser  | Opublikowano, gdy właściwości wątku rozmowy są aktualizowane, których użytkownik jest członkiem.              |
| Microsoft. Communications. ChatMemberAddedToThreadWithUser     | Opublikowano, gdy użytkownik jest dodawany jako członek do wątku rozmowy.                                   |
| Microsoft. Communications. ChatMemberRemovedFromThreadWithUser | Opublikowano, gdy użytkownik zostanie usunięty z wątku rozmowy.                                         |
| Microsoft. Communications. ChatParticipantAddedToThreadWithUser|  Opublikowano dla użytkownika po dodaniu nowego uczestnika do wątku rozmowy, którego użytkownik jest częścią.|
| Microsoft. Communications. ChatParticipantRemovedFromThreadWithUser |  Opublikowano dla użytkownika, gdy uczestnik jest usuwany z wątku rozmowy, którego użytkownik jest częścią. |
| Microsoft. Communications. ChatThreadCreated  | Opublikowano podczas tworzenia wątku rozmowy  |
| Microsoft. Communications. ChatThreadDeleted| Opublikowano po usunięciu wątku rozmowy  |
| Microsoft. Communications. ChatThreadParticipantAdded | Opublikowano po dodaniu nowego uczestnika do wątku rozmowy  |
| Microsoft. Communications. ChatThreadParticipantRemoved | Opublikowano po dodaniu nowego uczestnika do wątku rozmowy.  |  
| Microsoft. Communications. ChatMessageReceivedInThread | Opublikowano po odebraniu komunikatu w wątku rozmowy  |    
| Microsoft. Communications. ChatThreadPropertiesUpdated| Opublikowano, gdy są aktualizowane właściwości wątku rozmowy, jak temat.|    
| Microsoft. Communications. ChatMessageEditedInThread | Opublikowano, gdy komunikat jest edytowany w wątku rozmowy |  
| Microsoft. Communications. ChatMessageDeletedInThread | Opublikowano, gdy wiadomość zostanie usunięta w wątku czatu  |  

Możesz użyć Azure Portal lub interfejsu wiersza polecenia platformy Azure, aby subskrybować zdarzenia emitowane przez zasób usług komunikacyjnych. Rozpocznij pracę z obsługą zdarzeń, analizując [sposób obsługi zdarzeń programu SMS w usługach komunikacyjnych](../quickstarts/telephony-sms/handle-sms-events.md)


## <a name="event-subjects"></a>Tematy zdarzeń

`subject`Pole wszystkich zdarzeń usług komunikacyjnych identyfikuje użytkownika, numer telefonu lub jednostkę, która jest przeznaczona dla zdarzenia. Typowe prefiksy służą do zezwalania na proste [filtrowanie Event Grid](../../event-grid/event-filtering.md).

| Prefiks podmiotu                              | Jednostka usługi komunikacyjnej |
| ------------------------------------------- | ---------------------------- |
| `phonenumber/`                              | Numer telefonu PSTN            |
| `user/`                                     | Użytkownik usług komunikacyjnych  |
| `thread/`                                   | Wątek rozmowy.                 |

Poniższy przykład pokazuje filtr dla wszystkich komunikatów SMS i raportów o dostarczaniu wysyłanych do wszystkich 555 numerów telefonów numer telefonu należących do zasobu usług komunikacyjnych:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Communication.SMSReceived",
    "Microsoft.Communication.SMSDeliveryReportReceived"
  ],
  "subjectBeginsWith": "phonenumber/1555",
}
```

## <a name="sample-event-responses"></a>Przykładowe odpowiedzi na zdarzenia

Po wyzwoleniu zdarzenia usługa Event Grid wysyła dane dotyczące tego zdarzenia w celu subskrybowania punktów końcowych.

Ta sekcja zawiera przykład sposobu, w jaki będą wyglądały dane dla każdego zdarzenia.

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Zdarzenie Microsoft. Communications. SMSDeliveryReportReceived

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "eventType": "Microsoft.Communication.SMSDeliveryReportReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:22:20Z"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Zdarzenie Microsoft. Communications. SMSReceived

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "topic": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events ",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "eventType": "Microsoft.Communication.SMSReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:27:47Z"
}]
```

### <a name="microsoftcommunicationchatmessagereceived-event"></a>Zdarzenie Microsoft. Communications. ChatMessageReceived

```json
[{
    "id": "02272459-badb-4e2e-b538-4cb8a2f71da6",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/{rawId}/recipient/{rawId}",
    "data": {
      "messageBody": "Welcome to Azure Communication Services",
      "messageId": "1613694358927",
      "senderId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724"
        }
      },
      "senderDisplayName": "Jhon",
      "composeTime": "2021-02-19T00:25:58.927Z",
      "type": "Text",
      "version": 1613694358927,
      "recipientId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d05-83fe-084822000f6d",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d05-83fe-084822000f6d",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d05-83fe-084822000f6d"
        }
      },
      "transactionId": "oh+LGB2dUUadMcTAdRWQxQ.1.1.1.1.1827536918.1.7",
      "threadId": "19:6e5d6ca1d75044a49a36a7965ec4a906@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatMessageReceived",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-19T00:25:59.9436666Z"
  }
]
```

### <a name="microsoftcommunicationchatmessageedited-event"></a>Zdarzenie Microsoft. Communications. ChatMessageEdited

```json
[{
    "id": "93fc1037-b645-4eb0-a0f2-d7bb3ba6e060",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/{rawId}/recipient/{rawId}",
    "data": {
      "editTime": "2021-02-19T00:28:20.784Z",
      "messageBody": "Let's Chat about new communication services.",
      "messageId": "1613694357917",
      "senderId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724"
        }
      },
      "senderDisplayName": "Bob(Admin)",
      "composeTime": "2021-02-19T00:25:57.917Z",
      "type": "Text",
      "version": 1613694500784,
      "recipientId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f"
        }
      },
      "transactionId": "1mL4XZH2gEecu/alk9tOtw.2.1.2.1.1833042153.1.7",
      "threadId": "19:6e5d6ca1d75044a49a36a7965ec4a906@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatMessageEdited",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-19T00:28:21.7456718Z"
  }]
```

### <a name="microsoftcommunicationchatmessagedeleted-event"></a>Zdarzenie Microsoft. Communications. ChatMessageDeleted
```json
[{
    "id": "23cfcc13-33f2-4ae1-8d23-b5015b05302b",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/{rawId}/recipient/{rawId}",
    "data": {
      "deleteTime": "2021-02-19T00:43:10.14Z",
      "messageId": "1613695388152",
      "senderId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e"
        }
      },
      "senderDisplayName": "Bob(Admin)",
      "composeTime": "2021-02-19T00:43:08.152Z",
      "type": "Text",
      "version": 1613695390361,
      "recipientId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d60-83fe-084822000f6f"
        }
      },
      "transactionId": "fFs4InlBn0O/0WyhfQZVSQ.1.1.2.1.1867776045.1.4",
      "threadId": "19:48899258eec941e7a281e03edc8f4964@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatMessageDeleted",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-19T00:43:10.9982947Z"
  }]
```

### <a name="microsoftcommunicationchatthreadcreatedwithuser-event"></a>Zdarzenie Microsoft. Communications. ChatThreadCreatedWithUser

```json
[{
    "id": "eba02b2d-37bf-420e-8656-3a42ef74c435",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/createdBy/rawId/recipient/rawId",
    "data": {
      "createdBy": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9",
      "createdByCommunicationIdentifier": {
        "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9",
        "communicationUser": {
          "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9"
        }
      },
      "properties": {
        "topic": "Chat about new commuication services"
      },
      "members": [
        {
          "displayName": "Bob",
          "memberId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9"
        },
        {
          "displayName": "John",
          "memberId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-289b-07fd-0848220015ea"
        }
      ],
      "participants": [
        {
          "displayName": "Bob",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9",
            "communicationUser": {
              "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286d-e1fe-0848220013b9"
            }
          }
        },
        {
          "displayName": "John",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-289b-07fd-0848220015ea",
            "communicationUser": {
              "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-289b-07fd-0848220015ea"
            }
          }
        }
      ],
      "createTime": "2021-02-18T23:47:26.91Z",
      "version": 1613692046910,
      "recipientId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286e-84f5-08482200181c",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286e-84f5-08482200181c",
        "communicationUser": {
          "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-576c-286e-84f5-08482200181c"
        }
      },
      "transactionId": "zbZt+9h/N0em+XCW2QvyIA.1.1.1.1.1737228330.0.1737490483.1.6",
      "threadId": "19:1d594fb1eeb14566903cbc5decb5bf5b@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatThreadCreatedWithUser",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-18T23:47:34.7437103Z"
  }]
```

### <a name="microsoftcommunicationchatthreadwithuserdeleted-event"></a>Zdarzenie Microsoft. Communications. ChatThreadWithUserDeleted

```json
[{
    "id": "f5d6750c-c6d7-4da8-bb05-6f3fca6c7295",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/deletedBy/{rawId}/recipient/{rawId}",
    "data": {
      "deletedBy": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-6473-83fe-084822000e21",
      "deletedByCommunicationIdentifier": {
        "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-6473-83fe-084822000e21",
        "communicationUser": {
          "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-6473-83fe-084822000e21"
        }
      },
      "deleteTime": "2021-02-18T23:57:51.5987591Z",
      "createTime": "2021-02-18T23:54:15.683Z",
      "version": 1613692578672,
      "recipientId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-647b-e1fe-084822001416",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-647b-e1fe-084822001416",
        "communicationUser": {
          "id": "8:acs:3d703c91-9657-4b3f-b19c-ef9d53f99710_00000008-5772-647b-e1fe-084822001416"
        }
      },
      "transactionId": "mrliWVUndEmLwkZbeS5KoA.1.1.2.1.1761607918.1.6",
      "threadId": "19:5870b8f021d74fd786bf5aeb095da291@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatThreadWithUserDeleted",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-18T23:57:52.1597234Z"
  }]
```

### <a name="microsoftcommunicationchatparticipantaddedtothreadwithuser--event"></a>Zdarzenie Microsoft. Communications. ChatParticipantAddedToThreadWithUser 
```json
[{
    "id": "049a5a7f-6cd7-43c1-b352-df9e9e6146d1",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/participantAdded/{rawId}/recipient/{rawId}",
    "data": {
      "time": "2021-02-25T06:37:29.9232485Z",
      "addedByCommunicationIdentifier": {
        "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8767-1655-373a0d00885d",
        "communicationUser": {
          "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8767-1655-373a0d00885d"
        }
      },
      "participantAdded": {
        "displayName": "John Smith",
        "participantCommunicationIdentifier": {
          "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8785-1655-373a0d00885f",
          "communicationUser": {
            "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8785-1655-373a0d00885f"
          }
        }
      },
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8781-1655-373a0d00885e",
        "communicationUser": {
          "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8781-1655-373a0d00885e"
        }
      },
      "createTime": "2021-02-25T06:37:17.371Z",
      "version": 1614235049907,
      "transactionId": "q7rr9by6m0CiGiQxKdSO1w.1.1.1.1.1473446055.1.6",
      "threadId": "19:f1400e1c542f4086a606b52ad20cd0bd@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatParticipantAddedToThreadWithUser",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-25T06:37:31.4880091Z"
  }]
```

### <a name="microsoftcommunicationchatparticipantremovedfromthreadwithuser-event"></a>Zdarzenie Microsoft. Communications. ChatParticipantRemovedFromThreadWithUser 
```json
[{
    "id": "e8a4df24-799d-4c53-94fd-1e05703a4549",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/participantRemoved/{rawId}/recipient/{rawId}",
    "data": {
      "time": "2021-02-25T06:40:20.3564556Z",
      "removedByCommunicationIdentifier": {
        "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8767-1655-373a0d00885d",
        "communicationUser": {
          "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8767-1655-373a0d00885d"
        }
      },
      "participantRemoved": {
        "displayName": "Bob",
        "participantCommunicationIdentifier": {
          "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8785-1655-373a0d00885f",
          "communicationUser": {
            "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8785-1655-373a0d00885f"
          }
        }
      },
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8781-1655-373a0d00885e",
        "communicationUser": {
          "id": "8:acs:0a420b29-555c-4f6b-841e-de8059893bb9_00000008-77c9-8781-1655-373a0d00885e"
        }
      },
      "createTime": "2021-02-25T06:37:17.371Z",
      "version": 1614235220325,
      "transactionId": "usv74GQ5zU+JmWv/bQ+qfg.1.1.1.1.1480065078.1.5",
      "threadId": "19:f1400e1c542f4086a606b52ad20cd0bd@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatParticipantRemovedFromThreadWithUser",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-25T06:40:24.2244945Z"
  }]
```

### <a name="microsoftcommunicationchatthreadpropertiesupdatedperuser-event"></a>Zdarzenie Microsoft. Communications. ChatThreadPropertiesUpdatedPerUser

```json
[{
    "id": "d57342ff-264e-4a5e-9c54-ef05b7d50082",
    "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/editedBy/{rawId}/recipient/{rawId}",
    "data": {
      "editedBy": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e",
      "editedByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7d07-83fe-084822000f6e"
        }
      },
      "editTime": "2021-02-19T00:28:28.7390282Z",
      "properties": {
        "topic": "Communication in Azure"
      },
      "createTime": "2021-02-19T00:28:25.864Z",
      "version": 1613694508719,
      "recipientId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
      "recipientCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-578d-7caf-07fd-084822001724"
        }
      },
      "transactionId": "WLXPrnJ/I0+LTj2cwMrNMQ.1.1.1.1.1833369763.1.4",
      "threadId": "19:2cc3504c41244d7483208a4f58a1f188@thread.v2"
    },
    "eventType": "Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-19T00:28:29.559726Z"
  }]
```

### <a name="microsoftcommunicationchatmemberaddedtothreadwithuser-event"></a>Zdarzenie Microsoft. Communications. ChatMemberAddedToThreadWithUser

```json
[{
  "id": "4abd2b49-d1a9-4fcc-9cd7-170fa5d96443",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/{thread-id}/memberAdded/{rawId}/recipient/{rawId}",
  "data": {
    "time": "2020-09-18T00:47:13.1867087Z",
    "addedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberAdded": {
      "displayName": "John Smith",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390033176,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "pVIjw/pHEEKUOUJ2DAAl5A.1.1.1.1.1818361951.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMemberAddedToThreadWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:47:13.2342692Z"
}]
```

### <a name="microsoftcommunicationchatmemberremovedfromthreadwithuser-event"></a>Zdarzenie Microsoft. Communications. ChatMemberRemovedFromThreadWithUser

```json
[{
  "id": "b3701976-1ea2-4d66-be68-4ec4fc1b4b96",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/{thread-id}/memberRemoved/{rawId}/recipient/{rawId}",
  "data": {
    "time": "2020-09-18T00:47:51.1461742Z",
    "removedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberRemoved": {
      "displayName": "John",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071131,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "G9Y+UbjVmEuxAG3O4bEyvw.1.1.1.1.1819803816.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMemberRemovedFromThreadWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:47:51.2244511Z"
}]
```

### <a name="microsoftcommunicationchatthreadcreated-event"></a>Zdarzenie Microsoft. Communications. ChatThreadCreated

```json
[ {
    "id": "a607ac52-0974-4d3c-bfd8-6f708a26f509",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/createdBy/{rawId}",
    "data": {
      "createdByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
        }
      },
      "properties": {
        "topic": "Talk about new Thread Events in commuication services"
      },
      "participants": [
        {
          "displayName": "Bob",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
            "communicationUser": {
              "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
            }
          }
        },
        {
          "displayName": "Scott",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e6-07fd-084822002467",
            "communicationUser": {
              "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e6-07fd-084822002467"
            }
          }
        },
        {
          "displayName": "Shawn",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38f6-83fe-084822002337",
            "communicationUser": {
              "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38f6-83fe-084822002337"
            }
          }
        },
        {
          "displayName": "Anthony",
          "participantCommunicationIdentifier": {
            "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e3-e1fe-084822002c35",
            "communicationUser": {
              "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e3-e1fe-084822002c35"
            }
          }
        }
      ],
      "createTime": "2021-02-20T00:31:54.365+00:00",
      "version": 1613781114365,
      "threadId": "19:e07c8ddc5bab4c059ea9f11d29b544b6@thread.v2",
      "transactionId": "gK6+kgANy0O1wchlVKVTJg.1.1.1.1.921436178.1"
    },
    "eventType": "Microsoft.Communication.ChatThreadCreated",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:31:54.5369967Z"
  }]
```
### <a name="microsoftcommunicationchatthreadpropertiesupdated-event"></a>Zdarzenie Microsoft. Communications. ChatThreadPropertiesUpdated

```json
[{
    "id": "cf867580-9caf-45be-b49f-ab1cbfcaa59f",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/editedBy/{rawId}",
    "data": {
      "editedByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5c9e-9e35-07fd-084822002264",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5c9e-9e35-07fd-084822002264"
        }
      },
      "editTime": "2021-02-20T00:04:07.7152073+00:00",
      "properties": {
        "topic": "Talk about new Thread Events in commuication services"
      },
      "createTime": "2021-02-20T00:00:40.126+00:00",
      "version": 1613779447695,
      "threadId": "19:9e8eefe67b3c470a8187b4c2b00240bc@thread.v2",
      "transactionId": "GBE9MB2a40KEWzexIg0D3A.1.1.1.1.856359041.1"
    },
    "eventType": "Microsoft.Communication.ChatThreadPropertiesUpdated",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:04:07.8410277Z"
  }]
```
### <a name="microsoftcommunicationchatthreaddeleted-event"></a>Zdarzenie Microsoft. Communications. ChatThreadDeleted

```json
[
{
    "id": "1dbd5237-4823-4fed-980c-8d27c17cf5b0",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/deletedBy/{rawId}",
    "data": {
      "deletedByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5c9e-a300-07fd-084822002266",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5c9e-a300-07fd-084822002266"
        }
      },
      "deleteTime": "2021-02-20T00:00:42.109802+00:00",
      "createTime": "2021-02-20T00:00:39.947+00:00",
      "version": 1613779241389,
      "threadId": "19:c9e9f3060b884e448671391882066ac3@thread.v2",
      "transactionId": "KibptDpcLEeEFnlR7cI3QA.1.1.2.1.848298005.1"
    },
    "eventType": "Microsoft.Communication.ChatThreadDeleted",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:00:42.5428002Z"
  }
  ]
```
### <a name="microsoftcommunicationchatthreadparticipantadded-event"></a>Zdarzenie Microsoft. Communications. ChatThreadParticipantAdded

```json
[
{
    "id": "3024eb5d-1d71-49d1-878c-7dc3165433d9",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/participantadded/{rawId}",
    "data": {
      "time": "2021-02-20T00:54:42.8622646+00:00",
      "addedByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
        }
      },
      "participantAdded": {
        "displayName": "Bob",
        "participantCommunicationIdentifier": {
          "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38f3-88f7-084822002454",
          "communicationUser": {
            "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38f3-88f7-084822002454"
          }
        }
      },
      "createTime": "2021-02-20T00:31:54.365+00:00",
      "version": 1613782482822,
      "threadId": "19:e07c8ddc5bab4c059ea9f11d29b544b6@thread.v2",
      "transactionId": "9q6cO7i4FkaZ+5RRVzshVw.1.1.1.1.974913783.1"
    },
    "eventType": "Microsoft.Communication.ChatThreadParticipantAdded",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:54:43.9866454Z"
  }
]
```
### <a name="microsoftcommunicationchatthreadparticipantremoved-event"></a>Zdarzenie Microsoft. Communications. ChatThreadParticipantRemoved

```json
[
{
    "id": "6ed810fd-8776-4b13-81c2-1a0c4f791a07",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/participantremoved/{rawId}",
    "data": {
      "time": "2021-02-20T00:56:18.1118825+00:00",
      "removedByCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
        }
      },
      "participantRemoved": {
        "displayName": "Shawn",
        "participantCommunicationIdentifier": {
          "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e6-07fd-084822002467",
          "communicationUser": {
            "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38e6-07fd-084822002467"
          }
        }
      },
      "createTime": "2021-02-20T00:31:54.365+00:00",
      "version": 1613782578096,
      "threadId": "19:e07c8ddc5bab4c059ea9f11d29b544b6@thread.v2",
      "transactionId": "zGCq8IGRr0aEF6COuy7wSA.1.1.1.1.978649284.1"
    },
    "eventType": "Microsoft.Communication.ChatThreadParticipantRemoved",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:56:18.856721Z"
  }
]
```
### <a name="microsoftcommunicationchatmessagereceivedinthread-event"></a>Zdarzenie Microsoft. Communications. ChatMessageReceivedInThread

```json
[
{
    "id": "4f614f97-c451-4b82-a8c9-1e30c3bfcda1",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cdb-4916-07fd-084822002624",
    "data": {
      "messageBody": "Talk about new Thread Events in commuication services",
      "messageId": "1613783230064",
      "type": "Text",
      "version": "1613783230064",
      "senderDisplayName": "Bob",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cdb-4916-07fd-084822002624",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cdb-4916-07fd-084822002624"
        }
      },
      "composeTime": "2021-02-20T01:07:10.064+00:00",
      "threadId": "19:5b3809e80e4a439d92c3316e273f4a2b@thread.v2",
      "transactionId": "foMkntkKS0O/MhMlIE5Aag.1.1.1.1.1004077250.1"
    },
    "eventType": "Microsoft.Communication.ChatMessageReceivedInThread",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T01:07:10.5704596Z"
  }
]
```
### <a name="microsoftcommunicationchatmessageeditedinthread-event"></a>Zdarzenie Microsoft. Communications. ChatMessageEditedInThread

```json
[
  {
    "id": "7b8dc01e-2659-41fa-bc8c-88a967714510",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/{rawId}",
    "data": {
      "editTime": "2021-02-20T00:59:10.464+00:00",
      "messageBody": "8effb181-1eb2-4a58-9d03-ed48a461b19b",
      "messageId": "1613782685964",
      "type": "Text",
      "version": "1613782750464",
      "senderDisplayName": "Scott",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
        }
      },
      "composeTime": "2021-02-20T00:58:05.964+00:00",
      "threadId": "19:e07c8ddc5bab4c059ea9f11d29b544b6@thread.v2",
      "transactionId": "H8Gpj3NkIU6bXlWw8WPvhQ.2.1.2.1.985333801.1"
    },
    "eventType": "Microsoft.Communication.ChatMessageEditedInThread",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T00:59:10.7600061Z"
  }
]
```

### <a name="microsoftcommunicationchatmessagedeletedinthread-event"></a>Zdarzenie Microsoft. Communications. ChatMessageDeletedInThread

```json
[
 {
    "id": "17d9c39d-0c58-4ed8-947d-c55959f57f75",
    "topic": "/subscriptions/{subscription-id}/resourcegroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
    "subject": "thread/{thread-id}/sender/{rawId}",
    "data": {
      "deleteTime": "2021-02-20T00:59:10.464+00:00",
      "messageId": "1613782685440",
      "type": "Text",
      "version": "1613782814333",
      "senderDisplayName": "Scott",
      "senderCommunicationIdentifier": {
        "rawId": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453",
        "communicationUser": {
          "id": "8:acs:109f0644-b956-4cd9-87b1-71024f6e2f44_00000008-5cbb-38a0-88f7-084822002453"
        }
      },
      "composeTime": "2021-02-20T00:58:05.44+00:00",
      "threadId": "19:e07c8ddc5bab4c059ea9f11d29b544b6@thread.v2",
      "transactionId": "HqU6PeK5AkCRSpW8eAbL0A.1.1.2.1.987824181.1"
    },
    "eventType": "Microsoft.Communication.ChatMessageDeletedInThread",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "eventTime": "2021-02-20T01:00:14.8518034Z"
  }
]
```


## <a name="quickstarts-and-how-tos"></a>Przewodniki Szybki Start i porady

| Tytuł | Opis |
| --- | --- |
| [Jak obsługiwać zdarzenia programu SMS w usługach komunikacyjnych](../quickstarts/telephony-sms/handle-sms-events.md) | Obsługa wszystkich zdarzeń programu SMS odebranych przez usługę komunikacyjną przy użyciu elementu webhook. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](../../event-grid/overview.md)
* Aby zapoznać się z wprowadzeniem do Azure Event Grid pojęć, zobacz [pojęcia w Event Grid?](../../event-grid/concepts.md)
* Aby zapoznać się z wprowadzeniem do Azure Event Grid SystemTopics, zobacz [Tematy systemowe w Azure Event Grid?](../../event-grid/system-topics.md)
