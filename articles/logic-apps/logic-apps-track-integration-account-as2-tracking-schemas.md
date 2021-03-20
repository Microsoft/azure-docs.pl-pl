---
title: Schematy śledzenia AS2 dla komunikatów B2B
description: Tworzenie schematów śledzenia do monitorowania komunikatów AS2 w Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: bccf69362279afd9e8148b20b61ff3ea9b472a03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76906961"
---
# <a name="create-schemas-for-tracking-as2-messages-in-azure-logic-apps"></a>Tworzenie schematów śledzenia komunikatów AS2 w Azure Logic Apps

Aby ułatwić monitorowanie sukcesu, błędów i właściwości komunikatów dla transakcji typu B2B (Business-to-Business), możesz użyć tych schematów śledzenia AS2 na koncie integracji:

* Schemat śledzenia komunikatów AS2
* Schemat śledzenia powiadomienia o AS2 komunikatów (powiadomienia MDN)

## <a name="as2-message-tracking-schema"></a>Schemat śledzenia komunikatów AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "dispositionType": "",
      "fileName": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isMessageEncrypted": "",
      "isMessageCompressed": "",
      "correlationMessageId": "",
      "incomingHeaders": {},
      "outgoingHeaders": {},
      "isNrrEnabled": "",
      "isMdnExpected": "",
      "mdnType": ""
    }
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| senderPartnerName | Nie | Ciąg | Nazwa partnera nadawcy wiadomości AS2 |
| receiverPartnerName | Nie | Ciąg | Nazwa partnera odbiorcy komunikatu AS2 |
| as2To | Tak | Ciąg | Nazwa odbiorcy komunikatu AS2 z nagłówków wiadomości AS2 |
| as2From | Tak | Ciąg | Nazwa nadawcy wiadomości AS2 z nagłówków wiadomości AS2 |
| umowaname | Nie | Ciąg | Nazwa umowy AS2, do której komunikaty są rozpoznawane |
| kierunek | Tak | Ciąg | Kierunek przepływu wiadomości, który `receive` jest albo `send` |
| Identyfikatora | Nie | Ciąg | Identyfikator komunikatu AS2 z nagłówków wiadomości AS2 |
| rozmieśćtype | Nie | Ciąg | Wartość typu dyspozycji powiadomienia o dyspozycji komunikatu (powiadomienia MDN) |
| fileName | Nie | Ciąg | Nazwa pliku z nagłówka komunikatu AS2 |
| isMessageFailed | Tak | Wartość logiczna | Czy komunikat AS2 nie powiódł się |
| isMessageSigned | Tak | Wartość logiczna | Czy wiadomość AS2 została podpisana |
| isMessageEncrypted | Tak | Wartość logiczna | Czy komunikat AS2 został zaszyfrowany |
| isMessageCompressed | Tak | Wartość logiczna | Czy komunikat AS2 został skompresowany |
| correlationMessageId | Nie | Ciąg | Identyfikator komunikatu AS2 w celu skorelowania komunikatów z usługą MDNs |
| incomingHeaders | Nie | Słownik JToken | Szczegóły przychodzącego nagłówka komunikatu AS2 |
| outgoingHeaders | Nie | Słownik JToken | Szczegóły wychodzącego nagłówka komunikatu AS2 |
| isNrrEnabled | Tak | Wartość logiczna | Czy użyć wartości domyślnej, jeśli wartość nie jest znana |
| isMdnExpected | Tak | Wartość logiczna | Czy użyć wartości domyślnej, jeśli wartość nie jest znana |
| mdnType | Tak | Wyliczenie | Dozwolone wartości: `NotConfigured` , `Sync` , i `Async` |
|||||

## <a name="as2-mdn-tracking-schema"></a>Schemat śledzenia powiadomienia MDN AS2

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "as2To": "",
      "as2From": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "messageId": "",
      "originalMessageId": "",
      "dispositionType": "",
      "isMessageFailed": "",
      "isMessageSigned": "",
      "isNrrEnabled": "",
      "statusCode": "",
      "micVerificationStatus": "",
      "correlationMessageId": "",
      "incomingHeaders": {
      },
      "outgoingHeaders": {
      }
   }
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| senderPartnerName | Nie | Ciąg | Nazwa partnera nadawcy wiadomości AS2 |
| receiverPartnerName | Nie | Ciąg | Nazwa partnera odbiorcy komunikatu AS2 |
| as2To | Tak | Ciąg | Nazwa partnera, który odbiera komunikat AS2 |
| as2From | Tak | Ciąg | Nazwa partnera, który wysyła wiadomość AS2 |
| umowaname | Nie | Ciąg | Nazwa umowy AS2, do której komunikaty są rozpoznawane |
| kierunek | Tak | Ciąg | Kierunek przepływu wiadomości, który `receive` jest albo `send` |
| Identyfikatora | Nie | Ciąg | Identyfikator komunikatu AS2 |
| originalMessageId | Nie | Ciąg | Identyfikator oryginalnego komunikatu AS2 |
| rozmieśćtype | Nie | Ciąg | Wartość typu dyspozycji powiadomienia MDN |
| isMessageFailed | Tak | Wartość logiczna | Czy komunikat AS2 nie powiódł się |
| isMessageSigned | Tak | Wartość logiczna | Czy wiadomość AS2 została podpisana |
| isNrrEnabled | Tak | Wartość logiczna | Czy użyć wartości domyślnej, jeśli wartość nie jest znana |
| statusCode | Tak | Wyliczenie | Dozwolone wartości: `Accepted` , `Rejected` , i `AcceptedWithErrors` |
| micVerificationStatus | Tak | Wyliczenie | Dozwolone wartości: `NotApplicable` , `Succeeded` , i `Failed` |
| correlationMessageId | Nie | Ciąg | Identyfikator korelacji, który jest IDENTYFIKATORem oryginalnej wiadomości, dla której skonfigurowano powiadomienia MDN |
| incomingHeaders | Nie | Słownik JToken | Szczegóły nagłówka komunikatu przychodzącego |
| outgoingHeaders | Nie | Słownik JToken | Szczegóły nagłówka wiadomości wychodzącej |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B

Aby uzyskać informacje o schematach śledzenia protokołu B2B, zobacz:

* [Schematy śledzenia X12](logic-apps-track-integration-account-x12-tracking-schema.md)
* [Niestandardowe schematy śledzenia B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)