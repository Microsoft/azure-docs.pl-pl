---
title: Wersje układu strony
titleSuffix: Azure AD B2C
description: Historia wersji układu stron dla dostosowywania interfejsu użytkownika w zasadach niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc52414fa0d2eeffe0381a68bd66eadafe4862ea
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781283"
---
# <a name="page-layout-versions"></a>Wersje układu strony

Pakiety układów stron są okresowo aktualizowane w celu uwzględnienia poprawek i ulepszeń w ich elementach strony. Następujący dziennik zmian określa zmiany wprowadzone w każdej wersji.

## <a name="jquery-version"></a>wersja jQuery

Azure AD B2C układ strony używa następującej wersji [biblioteki jQuery](https://jquery.com/):

|Wersja układu strony  |wersja jQuery  |
|---------|---------|
|2.1.4 | 3.5.1 |
|1.2.0 | 3.4.1 |
|1.1.0 | 1.10.2 |

## <a name="self-asserted-page-selfasserted"></a>Strona z własnym potwierdzeniem (selfasserted)

**2.1.2**
- Rozwiązano problem z kodowaniem lokalizacji dla języków takich jak hiszpański i francuski.

**2.1.1**

- Dodano element UXString, który `heading` oprócz `intro` elementu ma być wyświetlany na stronie jako tytuł. Ta wartość jest domyślnie ukryta.
- Dodano obsługę zapisywania haseł do łańcucha kluczy usługi iCloud.
- Dodano obsługę używania zasad lub parametru QueryString w `pageFlavor` celu wybrania układu (klasyczny, oceanBlue lub slateGray).
- Dodano odrzuty na stronie z własnym potwierdzeniem.
- Fokus jest teraz umieszczany w pierwszym edytowalnym polu podczas ładowania strony.
- Fokus jest teraz umieszczany w pierwszym polu błędu, gdy wiele pól ma błędy.
- Fokus jest teraz umieszczany na przycisku "Zmień" po zweryfikowaniu kodu weryfikacyjnego wiadomości e-mail.

**2.1.0**

- Poprawki lokalizacji i ułatwień dostępu.

**2.0.0**

- Dodano obsługę [formantów wyświetlania](display-controls.md) w zasadach niestandardowych.

**1.2.0**

- Pola username/email i Password używają teraz `form` elementu HTML, aby umożliwić programowi Edge i Internet Explorer (IE) prawidłowe Zapisywanie tych informacji.
- Dodano konfigurowalne opóźnienie sprawdzania poprawności danych użytkownika w celu ulepszenia środowiska użytkownika.
- Poprawki ułatwień dostępu
- Rozwiązano problem z ułatwieniami dostępu, dzięki czemu komunikaty o błędach są teraz odczytywane przez program Narrator. 
- Fokus jest teraz umieszczany w polu hasło po zweryfikowaniu wiadomości e-mail.
- Usunięte `autofocus` z kontrolki CheckBox. 
- Dodano obsługę kontrolki wyświetlania dla weryfikacji numeru telefonu.
- Teraz możesz dodać `data-preload="true"` atrybut [do tagów HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content)
  - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
  - Kontroluj kolejność, w jakiej `script` Tagi są pobierane i wykonywane przed załadowaniem strony.
- Pole e-mail to teraz `type=email` , a klawiatury przenośne zapewniają poprawne sugestie.
- Obsługa funkcji Przekształć dla programu Chrome.
- Dodano obsługę znakowania firmowego na stronach przepływu użytkownika.

**1.1.0**

- Usunięto alert dotyczący anulowania
- Klasa CSS dla elementów Error
- Ulepszone wyświetlanie/ukrywanie logiki błędów
- Usunięto domyślny kod CSS

**1.0.0**

- Wersja początkowa

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Ujednolicona Strona rejestracji przy użyciu linku resetowania hasła (unifiedssp)

> [!TIP]
> W przypadku zlokalizowania strony do obsługi wielu ustawień regionalnych lub języków w przepływie użytkownika. Artykuł [identyfikatory lokalizacji](localization-string-ids.md) zawiera listę identyfikatorów lokalizacji, których można użyć dla wybranej wersji strony.

**2.1.2**
- Rozwiązano problem z kodowaniem lokalizacji dla języków takich jak hiszpański i francuski.
- Zezwalanie na używanie linku "zapomniane hasło" jako wymiany oświadczeń. Aby uzyskać więcej informacji, zobacz samoobsługowe [Resetowanie hasła](add-password-reset-policy.md#self-service-password-reset-recommended).

**2.1.1**
- Dodano element UXString, który `heading` oprócz `intro` elementu ma być wyświetlany na stronie jako tytuł. Ta wartość jest domyślnie ukryta.
- Dodano obsługę używania zasad lub parametru QueryString w `pageFlavor` celu wybrania układu (klasyczny, oceanBlue lub slateGray).
- Dodano obsługę zapisywania haseł do łańcucha kluczy usługi iCloud.
- Fokus jest teraz umieszczany w pierwszym polu błędu, gdy wiele pól ma błędy.
- Fokus jest teraz umieszczany w pierwszym edytowalnym polu podczas ładowania strony.
- Dodano nową lokalizację linku wyboru dostawcy oświadczeń `bottomUnderFormClaimsProviderSelections` .
- Usunięto UXStrings, które nie są już używane.

**2.1.0**

- Dodano obsługę wielu linków do rejestracji.
- Dodano obsługę walidacji danych wejściowych użytkownika zgodnie z regułami predykatów zdefiniowanymi w zasadach.

**1.2.0**

- Pola username/email i Password używają teraz `form` elementu HTML, aby umożliwić programowi Edge i Internet Explorer (IE) prawidłowe Zapisywanie tych informacji.
- Poprawki ułatwień dostępu
- Teraz możesz dodać atrybut do `data-preload="true"` [tagów HTML w](customize-ui-with-html.md#guidelines-for-using-custom-page-content) celu kontrolowania kolejności ładowania dla CSS i JavaScript.
  - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
  - Kontroluj kolejność, w jakiej `script` Tagi są pobierane i wykonywane przed załadowaniem strony.
- Pole e-mail to teraz `type=email` , a klawiatury przenośne zapewniają poprawne sugestie.
- Obsługa funkcji Przekształć dla programu Chrome.
- Dodano obsługę znakowania dzierżawy na stronach przepływu użytkownika.

**1.1.0**

- Dodano kontrolkę nie wylogowuj mnie (KMSI)

**1.0.0**

- Wersja początkowa

## <a name="mfa-page-multifactor"></a>Strona usługi MFA (wieloskładnikowa)

**ppkt**
- Rozwiązano problem dotyczący autouzupełniania kodu weryfikacyjnego w przypadku korzystania z systemu iOS.
- Rozwiązano problem związany z przekierowywaniem tokenu do jednostki uzależnionej z programu Android WebView. 
- Dodano element UXString, który `heading` oprócz `intro` elementu ma być wyświetlany na stronie jako tytuł. Ta wartość jest domyślnie ukryta.  
- Dodano obsługę używania zasad lub parametru QueryString w `pageFlavor` celu wybrania układu (klasyczny, oceanBlue lub slateGray).

**1.2.1**

- Poprawki ułatwień dostępu w domyślnych szablonach

**1.2.0**

- Poprawki ułatwień dostępu
- Teraz możesz dodać atrybut do `data-preload="true"` [tagów HTML w](customize-ui-with-html.md#guidelines-for-using-custom-page-content) celu kontrolowania kolejności ładowania dla CSS i JavaScript.
  - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
  - Kontroluj kolejność, w jakiej `script` Tagi są pobierane i wykonywane przed załadowaniem strony.
- Pole e-mail to teraz `type=email` , a klawiatury przenośne zapewniają poprawne sugestie
- Obsługa funkcji Przekształć dla programu Chrome.
- Dodano obsługę znakowania dzierżawy na stronach przepływu użytkownika.

**1.1.0**

- Przycisk "Potwierdź kod" został usunięty
- Pole wejściowe dla kodu przyjmuje teraz tylko dane wejściowe z maksymalnie sześciu (6) znaków
- Strona automatycznie podejmie próbę zweryfikowania kodu wprowadzonego po wprowadzeniu 6-cyfrowy kod bez kliknięcia przycisku
- Jeśli kod jest nieprawidłowy, pole wejściowe zostanie automatycznie wyczyszczone
- Po trzech (3) próbach z nieprawidłowym kodem B2C wysyła błąd z powrotem do jednostki uzależnionej
- Poprawki ułatwień dostępu
- Usunięto domyślny kod CSS

**1.0.0**

- Wersja początkowa

## <a name="exception-page-globalexception"></a>Strona wyjątku (globalexception)

**1.2.0**

- Poprawki ułatwień dostępu
- Teraz możesz dodać atrybut do `data-preload="true"` [tagów HTML w](customize-ui-with-html.md#guidelines-for-using-custom-page-content) celu kontrolowania kolejności ładowania dla CSS i JavaScript.
  - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
  - Kontroluj kolejność, w jakiej `script` Tagi są pobierane i wykonywane przed załadowaniem strony.
- Pole e-mail to teraz `type=email` , a klawiatury przenośne zapewniają poprawne sugestie
- Obsługa funkcji tłumaczenia dla programu Chrome

**1.1.0**

- Poprawka ułatwień dostępu
- Usunięto komunikat domyślny, gdy nie ma kontaktu z zasad
- Usunięto domyślny kod CSS

**1.0.0**

- Wersja początkowa

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>Inne strony (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.0**

- Poprawki ułatwień dostępu
- Teraz możesz dodać atrybut do `data-preload="true"` [tagów HTML w](customize-ui-with-html.md#guidelines-for-using-custom-page-content) celu kontrolowania kolejności ładowania dla CSS i JavaScript.
  - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
  - Kontroluj kolejność, w jakiej `script` Tagi są pobierane i wykonywane przed załadowaniem strony.
- Pole e-mail to teraz `type=email` , a klawiatury przenośne zapewniają poprawne sugestie
- Obsługa funkcji tłumaczenia dla programu Chrome

**1.0.0**

- Wersja początkowa

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat dostosowywania interfejsu użytkownika aplikacji w zasadach niestandardowych, zobacz [Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych](customize-ui-with-html.md).
