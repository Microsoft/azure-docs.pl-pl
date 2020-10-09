---
title: Schematy śledzenia X12 dla komunikatów B2B
description: Tworzenie schematów śledzenia do monitorowania komunikatów X12 dla Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: 5b2df194761ebc167e67498a985960a4fce35f19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76905308"
---
# <a name="create-schemas-for-tracking-x12-messages-in-azure-logic-apps"></a>Tworzenie schematów śledzenia komunikatów X12 w Azure Logic Apps

Aby ułatwić monitorowanie sukcesu, błędów i właściwości komunikatów dla transakcji typu B2B (Business-to-Business), możesz użyć tych schematów śledzenia X12 na koncie integracji:

* Schemat śledzenia zestawu transakcji X12
* Schemat śledzenia potwierdzenia zestawu transakcji X12
* Schemat śledzenia X12 Interchange
* Schemat śledzenia potwierdzenia wymiany X12
* Schemat śledzenia grup funkcjonalnych X12
* Schemat śledzenia potwierdzania grup funkcjonalnych X12

## <a name="x12-transaction-set-tracking-schema"></a>Schemat śledzenia zestawu transakcji X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "transactionSetControlNumber": "",
      "CorrelationMessageId": "",
      "messageType": "",
      "isMessageFailed": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "needAk2LoopForValidMessages": "",
      "segmentsCount": ""
   }
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| senderPartnerName | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | Ciąg | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | Ciąg | Wyślij identyfikator partnera |
| receiverQualifier | Tak | Ciąg | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | Ciąg | Identyfikator partnera odbierania |
| umowaname | Nie | Ciąg | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który `receive` jest albo `send` |
| interchangeControlNumber | Nie | Ciąg | Numer kontrolny wymiany |
| functionalGroupControlNumber | Nie | Ciąg | Numer kontroli funkcjonalnej |
| transactionSetControlNumber | Nie | Ciąg | Numer kontrolny zestawu transakcji |
| CorrelationMessageId | Nie | Ciąg | Identyfikator komunikatu korelacji, który jest połączeniem {Agreementname} {*GroupControlNumber*} {TransactionSetControlNumber} |
| messageType | Nie | Ciąg | Zestaw transakcji lub typ dokumentu |
| isMessageFailed | Tak | Boolean | Czy komunikat X12 nie powiódł się |
| isTechnicalAcknowledgmentExpected | Tak | Boolean | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12 |
| isFunctionalAcknowledgmentExpected | Tak | Boolean | Czy potwierdzenie funkcjonalności jest skonfigurowane w umowie X12 |
| needAk2LoopForValidMessages | Tak | Boolean | Czy pętla AK2 jest wymagana dla prawidłowego komunikatu |
| segmentsCount | Nie | Liczba całkowita | Liczba segmentów w zestawie transakcji X12 |
|||||

## <a name="x12-transaction-set-acknowledgment-tracking-schema"></a>Schemat śledzenia potwierdzenia zestawu transakcji X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "respondingtransactionSetControlNumber": "",
      "respondingTransactionSetId": "",
      "statusCode": "",
      "processingStatus": "",
      "CorrelationMessageId": "",
      "isMessageFailed": "",
      "ak2Segment": "",
      "ak3Segment": "",
      "ak5Segment": ""
   }
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| senderPartnerName | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | Ciąg | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | Ciąg | Wyślij identyfikator partnera |
| receiverQualifier | Tak | Ciąg | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | Ciąg | Identyfikator partnera odbierania |
| umowaname | Nie | Ciąg | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który `receive` jest albo `send` |
| interchangeControlNumber | Nie | Ciąg | Numer kontrolny wymiany potwierdzenia funkcjonalności. Wartość jest wypełniana tylko dla strony wysyłającej, w której odbierane są potwierdzenia funkcjonalne dla wiadomości wysyłanych do partnera. |
| functionalGroupControlNumber | Nie | Ciąg | Numer kontroli grupy funkcjonalnej potwierdzenia funkcjonalności. Wartość jest wypełniana tylko dla strony wysyłającej, w której odbierane są potwierdzenia funkcjonalne dla wiadomości wysyłanych do partnera |
| isaSegment | Nie | Ciąg | Segment ISA wiadomości. Wartość jest wypełniana tylko dla strony wysyłającej, w której odbierane są potwierdzenia funkcjonalne dla wiadomości wysyłanych do partnera |
| gsSegment | Nie | Ciąg | Segment GS komunikatu. Wartość jest wypełniana tylko dla strony wysyłającej, w której odbierane są potwierdzenia funkcjonalne dla wiadomości wysyłanych do partnera |
| respondingfunctionalGroupControlNumber | Nie | Ciąg | Numer kontroli wymiany odpowiedzi |
| respondingFunctionalGroupId | Nie | Ciąg | Identyfikator grupy funkcjonalnej odpowiedzi, który jest mapowany do AK101 w potwierdzeniu |
| respondingtransactionSetControlNumber | Nie | Ciąg | Numer kontrolny zestawu transakcji odpowiadający |
| respondingTransactionSetId | Nie | Ciąg | Identyfikator zestawu transakcji odpowiadający, który mapuje do AK201 w potwierdzeniu |
| statusCode | Tak | Boolean | Kod stanu potwierdzenia zestawu transakcji |
| segmentsCount | Tak | Wyliczenie | Kod stanu potwierdzenia z tymi dozwolonymi wartościami: `Accepted` , `Rejected` i `AcceptedWithErrors` |
| processingStatus | Tak | Wyliczenie | Przetwarzanie stanu potwierdzenia przy użyciu tych dozwolonych wartości: `Received` , `Generated` , i `Sent` |
| CorrelationMessageId | Nie | Ciąg | Identyfikator komunikatu korelacji, który jest połączeniem {Agreementname} {*GroupControlNumber*} {TransactionSetControlNumber} |
| isMessageFailed | Tak | Boolean | Czy komunikat X12 nie powiódł się |
| ak2Segment | Nie | Ciąg | Potwierdzenie dla zestawu transakcji w odebranej grupie funkcjonalnej |
| ak3Segment | Nie | Ciąg | Zgłasza błędy w segmencie danych |
| ak5Segment | Nie | Ciąg | Informuje, czy zestaw transakcji zidentyfikowany w segmencie AK2 jest akceptowany, czy odrzucany, i dlaczego |
|||||

## <a name="x12-interchange-tracking-schema"></a>Schemat śledzenia X12 Interchange

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "isa09": "",
      "isa10": "",
      "isa11": "",
      "isa12": "",
      "isa14": "",
      "isa15": "",
      "isa16": ""
   }
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| senderPartnerName | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | Ciąg | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | Ciąg | Wyślij identyfikator partnera |
| receiverQualifier | Tak | Ciąg | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | Ciąg | Identyfikator partnera odbierania |
| umowaname | Nie | Ciąg | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który `receive` jest albo `send` |
| interchangeControlNumber | Nie | Ciąg | Numer kontrolny wymiany |
| isaSegment | Nie | Ciąg | Segment ISA komunikatu |
| isTechnicalAcknowledgmentExpected | Boolean | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12  |
| isMessageFailed | Tak | Boolean | Czy komunikat X12 nie powiódł się |
| isa09 | Nie | Ciąg | Data wymiany dokumentu X12 |
| isa10 | Nie | Ciąg | Czas wymiany dokumentu X12 |
| isa11 | Nie | Ciąg | X12 — identyfikator standardów kontroli wymiany |
| isa12 | Nie | Ciąg | Numer wersji kontroli X12 Interchange |
| isa14 | Nie | Ciąg | Zażądano potwierdzenia X12 |
| isa15 | Nie | Ciąg | Wskaźnik dla testu lub produkcji |
| isa16 | Nie | Ciąg | Separator elementu |
|||||

## <a name="x12-interchange-acknowledgment-tracking-schema"></a>Schemat śledzenia potwierdzenia wymiany X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "isaSegment": "",
      "respondingInterchangeControlNumber": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ta102": "",
      "ta103": "",
      "ta105": ""
   }
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| senderPartnerName | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | Ciąg | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | Ciąg | Wyślij identyfikator partnera |
| receiverQualifier | Tak | Ciąg | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | Ciąg | Identyfikator partnera odbierania |
| umowaname | Nie | Ciąg | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który `receive` jest albo `send` |
| interchangeControlNumber | Nie | Ciąg | Numer kontroli wymiany dla potwierdzenia technicznego otrzymanego od partnerów |
| isaSegment | Nie | Ciąg | Segment ISA dla potwierdzenia technicznego otrzymanego od partnerów |
| respondingInterchangeControlNumber | Nie | Ciąg | Numer kontroli wymiany dla potwierdzenia technicznego otrzymanego od partnerów |
| isMessageFailed | Tak | Boolean | Czy komunikat X12 nie powiódł się |
| statusCode | Tak | Wyliczenie | Kod stanu potwierdzenia wymiany z tymi dozwolonymi wartościami: `Accepted` , `Rejected` i `AcceptedWithErrors` |
| processingStatus | Tak | Wyliczenie | Stan potwierdzenia z tymi dozwolonymi wartościami: `Received` , `Generated` i `Sent` |
| ta102 | Nie | Ciąg | Data wymiany |
| ta103 | Nie | Ciąg | Czas wymiany |
| ta105 | Nie | Ciąg | Kod notatki wymiany |
|||||

## <a name="x12-functional-group-tracking-schema"></a>Schemat śledzenia grup funkcjonalnych X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "gsSegment": "",
      "isTechnicalAcknowledgmentExpected": "",
      "isFunctionalAcknowledgmentExpected": "",
      "isMessageFailed": "",
      "gs01": "",
      "gs02": "",
      "gs03": "",
      "gs04": "",
      "gs05": "",
      "gs07": "",
      "gs08": ""
   }
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| senderPartnerName | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | Ciąg | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | Ciąg | Wyślij identyfikator partnera |
| receiverQualifier | Tak | Ciąg | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | Ciąg | Identyfikator partnera odbierania |
| umowaname | Nie | Ciąg | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, odebranie lub wysłanie |
| interchangeControlNumber | Nie | Ciąg | Numer kontrolny wymiany |
| functionalGroupControlNumber | Nie | Ciąg | Numer kontroli funkcjonalnej |
| gsSegment | Nie | Ciąg | Segment GS komunikatu |
| isTechnicalAcknowledgmentExpected | Tak | Boolean | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12 |
| isFunctionalAcknowledgmentExpected | Tak | Boolean | Czy potwierdzenie funkcjonalności jest skonfigurowane w umowie X12 |
| isMessageFailed | Tak | Boolean | Czy komunikat X12 nie powiódł się |
| gs01 | Nie | Ciąg | Kod identyfikatora funkcjonalności |
| gs02 | Nie | Ciąg | Kod nadawcy aplikacji |
| gs03 | Nie | Ciąg | Kod odbiorcy aplikacji |
| gs04 | Nie | Ciąg | Data grupy funkcjonalnej |
| gs05 | Nie | Ciąg | Czas grupy funkcjonalnej |
| gs07 | Nie | Ciąg | Kod odpowiedzialnej agencji |
| gs08 | Nie | Ciąg | Kod identyfikatora dla wersji, wersji lub branży |
|||||

## <a name="x12-functional-group-acknowledgment-tracking-schema"></a>Schemat śledzenia potwierdzania grup funkcjonalnych X12

```json
{
   "agreementProperties": {
      "senderPartnerName": "",
      "receiverPartnerName": "",
      "senderQualifier": "",
      "senderIdentifier": "",
      "receiverQualifier": "",
      "receiverIdentifier": "",
      "agreementName": ""
   },
   "messageProperties": {
      "direction": "",
      "interchangeControlNumber": "",
      "functionalGroupControlNumber": "",
      "isaSegment": "",
      "gsSegment": "",
      "respondingfunctionalGroupControlNumber": "",
      "respondingFunctionalGroupId": "",
      "isMessageFailed": "",
      "statusCode": "",
      "processingStatus": "",
      "ak903": "",
      "ak904": "",
      "ak9Segment": ""
   }
}
```

| Właściwość | Wymagany | Typ | Opis |
|----------|----------|------|-------------|
| senderPartnerName | Nie | Ciąg | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | Ciąg | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | Ciąg | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | Ciąg | Wyślij identyfikator partnera |
| receiverQualifier | Tak | Ciąg | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | Ciąg | Identyfikator partnera odbierania |
| umowaname | Nie | Ciąg | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który `receive` jest albo `send` |
| interchangeControlNumber | Nie | Ciąg | Numer kontrolny wymiany, który jest wypełniany po stronie wysyłania po otrzymaniu potwierdzenia technicznego od partnerów |
| functionalGroupControlNumber | Nie | Ciąg | Numer kontroli grupy funkcjonalnej potwierdzenia technicznego, który jest wypełniany po stronie nadawcy w przypadku otrzymania potwierdzenia technicznego od partnerów |
| isaSegment | Nie | Ciąg | Analogicznie jak numer kontrolny wymiany, ale wypełniane tylko w określonych przypadkach |
| gsSegment | Nie | Ciąg | Taki sam jak numer kontroli grupy funkcjonalnej, ale wypełniany tylko w określonych przypadkach |
| respondingfunctionalGroupControlNumber | Nie | Ciąg | Numer kontrolny oryginalnej grupy funkcjonalnej |
| respondingFunctionalGroupId | Nie | Ciąg | Mapuje do AK101 w identyfikator grupy funkcjonalnej potwierdzenia |
| isMessageFailed | Boolean | Czy komunikat X12 nie powiódł się |
| statusCode | Tak | Wyliczenie | Kod stanu potwierdzenia z tymi dozwolonymi wartościami: `Accepted` , `Rejected` i `AcceptedWithErrors` |
| processingStatus | Tak | Wyliczenie | Przetwarzanie stanu potwierdzenia przy użyciu tych dozwolonych wartości: `Received` , `Generated` , i `Sent` |
| ak903 | Nie | Ciąg | Liczba odebranych zestawów transakcji |
| ak904 | Nie | Ciąg | Liczba zestawów transakcji zaakceptowanych w określonej grupie funkcjonalnej |
| ak9Segment | Nie | Ciąg | Czy grupa funkcjonalna identyfikowana w segmencie AK1 jest akceptowana, czy odrzucana i dlaczego |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B

Aby uzyskać informacje o schematach śledzenia protokołu B2B, zobacz:

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Niestandardowe schematy śledzenia B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)