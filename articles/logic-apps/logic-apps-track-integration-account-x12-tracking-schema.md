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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
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
| senderPartnerName | Nie | String | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | String | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | String | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | String | Wyślij identyfikator partnera |
| receiverQualifier | Tak | String | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | String | Identyfikator partnera odbierania |
| umowaname | Nie | String | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który `receive` jest albo`send` |
| interchangeControlNumber | Nie | String | Numer kontrolny wymiany |
| functionalGroupControlNumber | Nie | String | Numer kontroli funkcjonalnej |
| transactionSetControlNumber | Nie | String | Numer kontrolny zestawu transakcji |
| CorrelationMessageId | Nie | String | Identyfikator komunikatu korelacji, który jest połączeniem {Agreementname} {*GroupControlNumber*} {TransactionSetControlNumber} |
| messageType | Nie | String | Zestaw transakcji lub typ dokumentu |
| isMessageFailed | Tak | Boolean | Czy komunikat X12 nie powiódł się |
| isTechnicalAcknowledgmentExpected | Tak | Boolean | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12 |
| isFunctionalAcknowledgmentExpected | Tak | Boolean | Czy potwierdzenie funkcjonalności jest skonfigurowane w umowie X12 |
| needAk2LoopForValidMessages | Tak | Boolean | Czy pętla AK2 jest wymagana dla prawidłowego komunikatu |
| segmentsCount | Nie | Integer | Liczba segmentów w zestawie transakcji X12 |
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
| senderPartnerName | Nie | String | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | String | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | String | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | String | Wyślij identyfikator partnera |
| receiverQualifier | Tak | String | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | String | Identyfikator partnera odbierania |
| umowaname | Nie | String | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który `receive` jest albo`send` |
| interchangeControlNumber | Nie | String | Numer kontrolny wymiany potwierdzenia funkcjonalności. Wartość jest wypełniana tylko dla strony wysyłającej, w której odbierane są potwierdzenia funkcjonalne dla wiadomości wysyłanych do partnera. |
| functionalGroupControlNumber | Nie | String | Numer kontroli grupy funkcjonalnej potwierdzenia funkcjonalności. Wartość jest wypełniana tylko dla strony wysyłającej, w której odbierane są potwierdzenia funkcjonalne dla wiadomości wysyłanych do partnera |
| isaSegment | Nie | String | Segment ISA wiadomości. Wartość jest wypełniana tylko dla strony wysyłającej, w której odbierane są potwierdzenia funkcjonalne dla wiadomości wysyłanych do partnera |
| gsSegment | Nie | String | Segment GS komunikatu. Wartość jest wypełniana tylko dla strony wysyłającej, w której odbierane są potwierdzenia funkcjonalne dla wiadomości wysyłanych do partnera |
| respondingfunctionalGroupControlNumber | Nie | String | Numer kontroli wymiany odpowiedzi |
| respondingFunctionalGroupId | Nie | String | Identyfikator grupy funkcjonalnej odpowiedzi, który jest mapowany do AK101 w potwierdzeniu |
| respondingtransactionSetControlNumber | Nie | String | Numer kontrolny zestawu transakcji odpowiadający |
| respondingTransactionSetId | Nie | String | Identyfikator zestawu transakcji odpowiadający, który mapuje do AK201 w potwierdzeniu |
| Stanu | Tak | Boolean | Kod stanu potwierdzenia zestawu transakcji |
| segmentsCount | Tak | Wyliczenie | Kod stanu potwierdzenia z tymi dozwolonymi wartościami: `Accepted` , `Rejected` i`AcceptedWithErrors` |
| processingStatus | Tak | Wyliczenie | Przetwarzanie stanu potwierdzenia przy użyciu tych dozwolonych wartości: `Received` , `Generated` , i`Sent` |
| CorrelationMessageId | Nie | String | Identyfikator komunikatu korelacji, który jest połączeniem {Agreementname} {*GroupControlNumber*} {TransactionSetControlNumber} |
| isMessageFailed | Tak | Boolean | Czy komunikat X12 nie powiódł się |
| ak2Segment | Nie | String | Potwierdzenie dla zestawu transakcji w odebranej grupie funkcjonalnej |
| ak3Segment | Nie | String | Zgłasza błędy w segmencie danych |
| ak5Segment | Nie | String | Informuje, czy zestaw transakcji zidentyfikowany w segmencie AK2 jest akceptowany, czy odrzucany, i dlaczego |
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
| senderPartnerName | Nie | String | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | String | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | String | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | String | Wyślij identyfikator partnera |
| receiverQualifier | Tak | String | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | String | Identyfikator partnera odbierania |
| umowaname | Nie | String | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który `receive` jest albo`send` |
| interchangeControlNumber | Nie | String | Numer kontrolny wymiany |
| isaSegment | Nie | String | Segment ISA komunikatu |
| isTechnicalAcknowledgmentExpected | Boolean | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12  |
| isMessageFailed | Tak | Boolean | Czy komunikat X12 nie powiódł się |
| isa09 | Nie | String | Data wymiany dokumentu X12 |
| isa10 | Nie | String | Czas wymiany dokumentu X12 |
| isa11 | Nie | String | X12 — identyfikator standardów kontroli wymiany |
| isa12 | Nie | String | Numer wersji kontroli X12 Interchange |
| isa14 | Nie | String | Zażądano potwierdzenia X12 |
| isa15 | Nie | String | Wskaźnik dla testu lub produkcji |
| isa16 | Nie | String | Separator elementu |
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
| senderPartnerName | Nie | String | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | String | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | String | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | String | Wyślij identyfikator partnera |
| receiverQualifier | Tak | String | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | String | Identyfikator partnera odbierania |
| umowaname | Nie | String | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który `receive` jest albo`send` |
| interchangeControlNumber | Nie | String | Numer kontroli wymiany dla potwierdzenia technicznego otrzymanego od partnerów |
| isaSegment | Nie | String | Segment ISA dla potwierdzenia technicznego otrzymanego od partnerów |
| respondingInterchangeControlNumber | Nie | String | Numer kontroli wymiany dla potwierdzenia technicznego otrzymanego od partnerów |
| isMessageFailed | Tak | Boolean | Czy komunikat X12 nie powiódł się |
| Stanu | Tak | Wyliczenie | Kod stanu potwierdzenia wymiany z tymi dozwolonymi wartościami: `Accepted` , `Rejected` i`AcceptedWithErrors` |
| processingStatus | Tak | Wyliczenie | Stan potwierdzenia z tymi dozwolonymi wartościami: `Received` , `Generated` i`Sent` |
| ta102 | Nie | String | Data wymiany |
| ta103 | Nie | String | Czas wymiany |
| ta105 | Nie | String | Kod notatki wymiany |
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
| senderPartnerName | Nie | String | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | String | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | String | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | String | Wyślij identyfikator partnera |
| receiverQualifier | Tak | String | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | String | Identyfikator partnera odbierania |
| umowaname | Nie | String | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, odebranie lub wysłanie |
| interchangeControlNumber | Nie | String | Numer kontrolny wymiany |
| functionalGroupControlNumber | Nie | String | Numer kontroli funkcjonalnej |
| gsSegment | Nie | String | Segment GS komunikatu |
| isTechnicalAcknowledgmentExpected | Tak | Boolean | Czy potwierdzenie techniczne jest skonfigurowane w umowie X12 |
| isFunctionalAcknowledgmentExpected | Tak | Boolean | Czy potwierdzenie funkcjonalności jest skonfigurowane w umowie X12 |
| isMessageFailed | Tak | Boolean | Czy komunikat X12 nie powiódł się |
| gs01 | Nie | String | Kod identyfikatora funkcjonalności |
| gs02 | Nie | String | Kod nadawcy aplikacji |
| gs03 | Nie | String | Kod odbiorcy aplikacji |
| gs04 | Nie | String | Data grupy funkcjonalnej |
| gs05 | Nie | String | Czas grupy funkcjonalnej |
| gs07 | Nie | String | Kod odpowiedzialnej agencji |
| gs08 | Nie | String | Kod identyfikatora dla wersji, wersji lub branży |
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
| senderPartnerName | Nie | String | Nazwa partnera nadawcy wiadomości X12 |
| receiverPartnerName | Nie | String | Nazwa partnera odbiorcy komunikatu X12 |
| senderQualifier | Tak | String | Wyślij kwalifikator partnera |
| senderIdentifier | Tak | String | Wyślij identyfikator partnera |
| receiverQualifier | Tak | String | Odbierz kwalifikator partnera |
| receiverIdentifier | Tak | String | Identyfikator partnera odbierania |
| umowaname | Nie | String | Nazwa umowy X12, do której komunikaty są rozpoznawane |
| kierunek | Tak | Wyliczenie | Kierunek przepływu wiadomości, który `receive` jest albo`send` |
| interchangeControlNumber | Nie | String | Numer kontrolny wymiany, który jest wypełniany po stronie wysyłania po otrzymaniu potwierdzenia technicznego od partnerów |
| functionalGroupControlNumber | Nie | String | Numer kontroli grupy funkcjonalnej potwierdzenia technicznego, który jest wypełniany po stronie nadawcy w przypadku otrzymania potwierdzenia technicznego od partnerów |
| isaSegment | Nie | String | Analogicznie jak numer kontrolny wymiany, ale wypełniane tylko w określonych przypadkach |
| gsSegment | Nie | String | Taki sam jak numer kontroli grupy funkcjonalnej, ale wypełniany tylko w określonych przypadkach |
| respondingfunctionalGroupControlNumber | Nie | String | Numer kontrolny oryginalnej grupy funkcjonalnej |
| respondingFunctionalGroupId | Nie | String | Mapuje do AK101 w identyfikator grupy funkcjonalnej potwierdzenia |
| isMessageFailed | Boolean | Czy komunikat X12 nie powiódł się |
| Stanu | Tak | Wyliczenie | Kod stanu potwierdzenia z tymi dozwolonymi wartościami: `Accepted` , `Rejected` i`AcceptedWithErrors` |
| processingStatus | Tak | Wyliczenie | Przetwarzanie stanu potwierdzenia przy użyciu tych dozwolonych wartości: `Received` , `Generated` , i`Sent` |
| ak903 | Nie | String | Liczba odebranych zestawów transakcji |
| ak904 | Nie | String | Liczba zestawów transakcji zaakceptowanych w określonej grupie funkcjonalnej |
| ak9Segment | Nie | String | Czy grupa funkcjonalna identyfikowana w segmencie AK1 jest akceptowana, czy odrzucana i dlaczego |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>Schematy śledzenia protokołu B2B

Aby uzyskać informacje o schematach śledzenia protokołu B2B, zobacz:

* [Schematy śledzenia AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Niestandardowe schematy śledzenia B2B](logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Następne kroki

* [Monitorowanie komunikatów B2B przy użyciu dzienników usługi Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)