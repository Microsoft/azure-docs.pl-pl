---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78187007"
---
## <a name="ropc-flow-notes"></a>Uwagi dotyczące przepływu ROPC
W Azure Active Directory B2C (Azure AD B2C) obsługiwane są następujące opcje:

- **Klient natywny**: interakcja użytkownika podczas uwierzytelniania występuje, gdy kod jest uruchamiany na urządzeniu po stronie użytkownika. Urządzenie może być aplikacją mobilną, która działa w natywnym systemie operacyjnym, takim jak Android i iOS.
- **Publiczny przepływ klienta**: tylko poświadczenia użytkownika zebrane przez aplikację są wysyłane w wywołaniu interfejsu API. Poświadczenia aplikacji nie są wysyłane.
- **Dodawanie nowych oświadczeń**: zawartość tokenu identyfikatora można zmienić, aby dodać nowe oświadczenia.

Następujące przepływy nie są obsługiwane:

- **Serwer-serwer**: system ochrony tożsamości musi mieć niezawodny adres IP zebrany od wywołującego (natywnego klienta) w ramach interakcji. W wywołaniu interfejsu API po stronie serwera jest używany tylko adres IP serwera. Jeśli zostanie przekroczony dynamiczny próg nieudanych uwierzytelnień, system ochrony tożsamości może zidentyfikować powtórzony adres IP jako osobę atakującą.
- **Poufny przepływ klienta**: identyfikator klienta aplikacji został zweryfikowany, ale wpis tajny aplikacji nie jest zweryfikowany.

W przypadku korzystania z przepływu ROPC należy wziąć pod uwagę następujące kwestie:

- ROPC nie działa, gdy istnieje jakiekolwiek przerwanie przepływu uwierzytelniania, które wymaga interakcji z użytkownikiem. Jeśli na przykład hasło wygasło lub wymaga zmiany, wymagane jest uwierzytelnianie wieloskładnikowe lub jeśli trzeba będzie zbierać więcej informacji podczas logowania (na przykład użytkownik wyraża zgodę).
- ROPC obsługuje tylko konta lokalne. Użytkownicy nie mogą zalogować się za pomocą federacyjnych dostawców tożsamości, takich jak Microsoft, Google +, Twitter, AD FS lub Facebook.
- Zarządzanie sesjami, w tym nie wylogowuj mnie (KMSI), nie ma zastosowania.
