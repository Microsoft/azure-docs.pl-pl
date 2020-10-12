---
title: Rozwiązania typowych błędów i problemów w scenariuszach B2B
description: Poznaj rozwiązania typowych błędów i problemów podczas rozwiązywania problemów z scenariuszami B2B w Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 6400cfe7e524dcc16e08c2bba7dfba4a62d00b2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86232563"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Błędy i rozwiązania B2B dla Azure Logic Apps

Ten artykuł pomaga w rozwiązywaniu problemów, które mogą wystąpić w scenariuszach Logic Apps B2B i sugeruje odpowiednie działania w celu poprawienia tych błędów.

## <a name="agreement-resolution"></a>Rozwiązanie umowy

### <a name="no-agreement-found"></a>Nie znaleziono umowy 

**Opis błędu**: nie znaleziono umowy z parametrami rozpoznawania umowy.

**Akcja użytkownika**: Umowa powinna zostać dodana do konta integracji z uzgodnionymi tożsamościami biznesowymi. Tożsamość firmowa powinna być zgodna z identyfikatorami wiadomości wejściowych.

### <a name="no-agreement-found-with-identities"></a>Nie znaleziono umowy z tożsamościami

**Opis błędu**: nie znaleziono umowy z tożsamościami: ' AS2Identity ':: ' Partner1 ' and'AS2Identity ':: ' Partner3 '

**Akcja użytkownika**: nieprawidłowa AS2-From lub AS2-To skonfigurowana dla umowy. Skoryguj AS2 komunikatu "AS2-from" lub "AS2-to" lub "Umowa", aby dopasować identyfikatory AS2 w nagłówkach wiadomości AS2 z konfiguracjami umowy.

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Brak nagłówków komunikatów AS2  

**Opis błędu**: nieprawidłowe nagłówki AS2. Jeden z nagłówków "AS2 do" lub "AS2-from" jest pusty.

**Akcja użytkownika**: Odebrano komunikat AS2, który nie zawiera AS2-From lub AS2-To ani obu nagłówków. Sprawdź AS2-From komunikatu AS2 i AS2-To nagłówki i popraw je w oparciu o konfigurację umowy.

### <a name="missing-as2-message-body-and-headers"></a>Brak treści i nagłówków wiadomości AS2    

**Opis błędu**: zawartość żądania ma wartość null lub jest pusta.

**Akcja użytkownika**: Odebrano komunikat AS2, który nie zawiera treści komunikatu.

### <a name="as2-message-decryption-failure"></a>Niepowodzenie odszyfrowywania komunikatu AS2

**Opis błędu**: [przetworzono/błąd: deszyfrowanie — Niepowodzenie]

**Akcja użytkownika**: Dodaj @base64ToBinary do AS2Message przed wysłaniem do partnera.

Na przykład:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>Niepowodzenie odszyfrowywania powiadomienia MDN

**Opis błędu**: [przetworzono/błąd: deszyfrowanie — Niepowodzenie]

**Akcja użytkownika**: Dodaj @base64ToBinary do powiadomienia MDN przed wysłaniem do partnera.

Na przykład:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Brak certyfikatu podpisywania

**Opis błędu**: certyfikat podpisywania nie został skonfigurowany dla strony AS2. AS2-from: partner1 AS2-to: partner2

**Akcja użytkownika**: Skonfiguruj ustawienia umowy AS2 z prawidłowym certyfikatem dla podpisu.

## <a name="x12-and-edifact"></a>X12 i EDIFACT

### <a name="leading-or-trailing-space-found"></a>Znaleziono początkowe lub końcowe miejsce    

**Opis błędu**: Wystąpił błąd podczas analizowania. Zestaw transakcji EDIFACT o IDENTYFIKATORze "123456" zawarty w wymianie (bez grupy) o IDENTYFIKATORze "987654" z IDENTYFIKATORem "Partner1" i IDENTYFIKATORem odbiornika "Partner2" jest zawieszony z następującymi błędami: "znaleziono początkowy separator końcowego"

**Akcja użytkownika**: ustawienia umowy, które mają być skonfigurowane tak, aby zezwalały na początkowe i końcowe miejsce. Edytuj ustawienia umowy, aby zezwalać na początkowe i końcowe miejsce.

![Zezwalaj na miejsce](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Sprawdzanie duplikatów zostało włączone w umowie

**Opis błędu**: zduplikowany numer kontrolny

**Akcja użytkownika**: ten błąd wskazuje, że odebrana wiadomość ma zduplikowane numery kontrolne. Popraw numer kontrolny i ponownie Wyślij wiadomość.

### <a name="missing-schema-in-the-agreement"></a>Brak schematu w umowie

**Opis błędu**: Wystąpił błąd podczas analizowania. Zestaw transakcji X12 o IDENTYFIKATORze "564220001" zawarty w grupie funkcjonalnej o IDENTYFIKATORze "56422" w ramach wymiany z IDENTYFIKATORem "000056422" z IDENTYFIKATORem nadawcy "12345678" i IDENTYFIKATORem odbiornika "87654321" jest zawieszony z następującymi błędami: "komunikat ma nieznany typ dokumentu i nie został rozpoznany jako żaden z istniejących schematów skonfigurowanych w umowie"

**Akcja użytkownika**: Skonfiguruj schemat w ustawieniach umowy.

### <a name="incorrect-schema-in-the-agreement"></a>Nieprawidłowy schemat w umowie

**Opis błędu**: komunikat ma nieznany typ dokumentu i nie został rozpoznany jako żaden z istniejących schematów skonfigurowanych w umowie.

**Akcja użytkownika**: Skonfiguruj poprawny schemat w ustawieniach umowy.

## <a name="flat-file"></a>Plik prosty

### <a name="input-message-with-no-body"></a>Komunikat wejściowy bez treści

**Opis błędu**: InvalidTemplate. Nie można przetworzyć wyrażeń języka szablonu w danych wejściowych akcji "Flat_File_Decoding" w wierszu "1" i kolumnie "1902": "wymagana właściwość" Content "oczekuje wartości, ale otrzymano wartość null. Ścieżka "". ".

**Akcja użytkownika**: ten błąd wskazuje, że komunikat wejściowy nie zawiera treści.
