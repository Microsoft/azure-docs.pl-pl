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
ms.openlocfilehash: 38e281ce3d8117bff719b1bb572f09acbbb89669
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75666690"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Błędy i rozwiązania B2B dla Azure Logic Apps

Ten artykuł pomaga w rozwiązywaniu problemów, które mogą wystąpić w scenariuszach Logic Apps B2B i sugeruje odpowiednie działania w celu poprawienia tych błędów.

## <a name="agreement-resolution"></a>Rozwiązanie umowy

### <a name="no-agreement-found"></a>Nie znaleziono umowy 

|   |   |  
|---|---|
| Opis błędu | Nie znaleziono umowy z parametrami rozpoznawania umowy. | 
| Akcja użytkownika | Umowę należy dodać do konta integracji przy użyciu uzgodnionych tożsamości firmowych. </br>Tożsamość firmowa powinna być zgodna z identyfikatorami wiadomości wejściowych. |  
|   |   |

### <a name="no-agreement-found-with-identities"></a>Nie znaleziono umowy z tożsamościami

|   |   | 
|---|---|
| Opis błędu | Nie znaleziono umów z tożsamościami: ' AS2Identity ':: ' Partner1 ' and'AS2Identity ':: ' Partner3 ' | 
| Akcja użytkownika | Nieprawidłowa AS2-from lub AS2-to konfiguracja dla umowy. </br>Skoryguj AS2 komunikatu "AS2-from" lub "AS2-to" lub "Umowa", aby dopasować identyfikatory AS2 w nagłówkach wiadomości AS2 z konfiguracjami umowy. |
|   |   |     

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Brak nagłówków komunikatów AS2  

|   |   |  
|---|---|
| Opis błędu | Nieprawidłowe nagłówki AS2. Jeden z nagłówków "AS2 do" lub "AS2-from" jest pusty. | 
| Akcja użytkownika | Odebrano komunikat AS2, który nie zawiera nagłówka AS2-from lub AS2-to ani obu. </br> Sprawdź AS2 wiadomości AS2 — od i AS2-do nagłówków i popraw je na podstawie konfiguracji umowy. |
|  |  | 

### <a name="missing-as2-message-body-and-headers"></a>Brak treści i nagłówków wiadomości AS2    

|   |   |  
|---|---|
| Opis błędu | Zawartość żądania ma wartość null lub jest pusta. | 
| Akcja użytkownika | Odebrano komunikat AS2, który nie zawiera treści komunikatu. |
|  |  | 

### <a name="as2-message-decryption-failure"></a>Niepowodzenie odszyfrowywania komunikatu AS2

|   |   | 
|---|---|
| Opis błędu |  [przetworzono/błąd: deszyfrowanie — Niepowodzenie] | 
| Akcja użytkownika | Dodaj @base64ToBinary do AS2Message przed wysłaniem do partnera. |
|||

Przykład:

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

|   |   | 
|---|---|
| Opis błędu |  [przetworzono/błąd: deszyfrowanie — Niepowodzenie] | 
| Akcja użytkownika | Dodaj @base64ToBinary do powiadomienia MDN przed wysłaniem do partnera. | 
|||

Przykład:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Brak certyfikatu podpisywania

|   |   |  
|---|---|
| Opis błędu| Certyfikat podpisywania nie został skonfigurowany dla AS2 strony. </br>AS2-from: partner1 AS2-to: partner2 | 
| Akcja użytkownika | Skonfiguruj ustawienia umowy AS2 z prawidłowym certyfikatem dla podpisu. |
|  |  | 

## <a name="x12-and-edifact"></a>X12 i EDIFACT

### <a name="leading-or-trailing-space-found"></a>Znaleziono początkowe lub końcowe miejsce    
    
|   |   | 
|---|---|
| Opis błędu | Napotkano błąd podczas analizowania. Zestaw transakcji EDIFACT o IDENTYFIKATORze "123456" zawarty w wymianie (bez grupy) o IDENTYFIKATORze "987654" o identyfikatorze "Partner1" i identyfikatorze odbiornika "Partner2" jest zawieszony z następującymi błędami: <p>"Znaleziono początkowy separator końcowy" |
| Akcja użytkownika | Ustawienia umowy, które mają być skonfigurowane tak, aby zezwalały na początkowe i końcowe miejsce. </br>Edytuj ustawienia umowy, aby zezwalać na początkowe i końcowe miejsce. |
|   |   |

![Zezwalaj na miejsce](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>Sprawdzanie duplikatów zostało włączone w umowie

|   |   | 
|---|---| 
| Opis błędu | Zduplikowany numer kontrolny |
| Akcja użytkownika | Ten błąd wskazuje, że odebrana wiadomość ma zduplikowane numery kontrolne. </br>Popraw numer kontrolny i ponownie Wyślij wiadomość. |
|   |   |

### <a name="missing-schema-in-the-agreement"></a>Brak schematu w umowie

|   |   | 
|---|---| 
| Opis błędu | Napotkano błąd podczas analizowania. Zestaw transakcji X12 o IDENTYFIKATORze "564220001" zawarty w grupie funkcjonalnej o IDENTYFIKATORze "56422" w ramach wymiany z IDENTYFIKATORem "000056422" z IDENTYFIKATORem nadawcy "12345678" i IDENTYFIKATORem odbiornika "87654321" jest zawieszony z następującymi błędami: <p>"Komunikat ma nieznany typ dokumentu i nie został rozpoznany jako żaden z istniejących schematów skonfigurowanych w umowie" |
| Akcja użytkownika | Skonfiguruj schemat w ustawieniach umowy.  |
|   |   |

### <a name="incorrect-schema-in-the-agreement"></a>Nieprawidłowy schemat w umowie

|   |   | 
|---|---| 
| Opis błędu | Komunikat ma nieznany typ dokumentu i nie został rozpoznany jako żaden z istniejących schematów skonfigurowanych w umowie. |
| Akcja użytkownika | Skonfiguruj poprawny schemat w ustawieniach umowy. |
|   |   |

## <a name="flat-file"></a>Plik prosty

### <a name="input-message-with-no-body"></a>Komunikat wejściowy bez treści

|   |   | 
|---|---|
| Opis błędu | InvalidTemplate. Nie można przetworzyć wyrażeń języka szablonu w danych wejściowych akcji "Flat_File_Decoding" w wierszu "1" i kolumnie "1902": "wymagana właściwość" Content "oczekuje wartości, ale otrzymano wartość null. Ścieżka "". ". |
| Akcja użytkownika | Ten błąd wskazuje, że komunikat wejściowy nie zawiera treści. |
|   |   | 

