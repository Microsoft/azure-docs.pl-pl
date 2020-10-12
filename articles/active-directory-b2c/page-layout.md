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
ms.date: 08/24/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 868d99a82009dc8545fc24ad1cfa1da3959da131
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88852069"
---
# <a name="page-layout-versions"></a>Wersje układu strony

Pakiety układów stron są okresowo aktualizowane w celu uwzględnienia poprawek i ulepszeń w ich elementach strony. Następujący dziennik zmian określa zmiany wprowadzone w każdej wersji.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="self-asserted-page-selfasserted"></a>Strona z własnym potwierdzeniem (selfasserted)

**2.1.0**

- Poprawki lokalizacji i ułatwień dostępu.

**2.0.0**

- Dodano obsługę [formantów wyświetlania](display-controls.md) w zasadach niestandardowych.

**1.2.0**

- Pola username/email i Password używają teraz `form` elementu HTML, aby umożliwić programowi Edge i Internet Explorer (IE) prawidłowe Zapisywanie tych informacji.
- Dodano konfigurowalne opóźnienie sprawdzania poprawności danych użytkownika w celu ulepszenia środowiska użytkownika.
- Poprawki ułatwień dostępu
- Teraz możesz dodać atrybut do `data-preload="true"` [tagów HTML w](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) celu kontrolowania kolejności ładowania dla CSS i JavaScript.
  - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
  - Kontroluj kolejność, w jakiej `script` Tagi są pobierane i wykonywane przed załadowaniem strony.
- Pole e-mail to teraz `type=email` , a klawiatury przenośne zapewniają poprawne sugestie
- Obsługa funkcji tłumaczenia dla programu Chrome

**1.1.0**

- Usunięto alert dotyczący anulowania
- Klasa CSS dla elementów Error
- Ulepszone wyświetlanie/ukrywanie logiki błędów
- Usunięto domyślny kod CSS

**1.0.0**

- Wersja początkowa

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Ujednolicona Strona rejestracji przy użyciu linku resetowania hasła (unifiedssp)

**2.1.0**

- Dodano obsługę wielu linków do rejestracji.
- Dodano obsługę walidacji danych wejściowych użytkownika zgodnie z regułami predykatów zdefiniowanymi w zasadach.

**1.2.0**

- Pola username/email i Password używają teraz `form` elementu HTML, aby umożliwić programowi Edge i Internet Explorer (IE) prawidłowe Zapisywanie tych informacji.
- Poprawki ułatwień dostępu
- Teraz możesz dodać atrybut do `data-preload="true"` [tagów HTML w](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) celu kontrolowania kolejności ładowania dla CSS i JavaScript.
  - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
  - Kontroluj kolejność, w jakiej `script` Tagi są pobierane i wykonywane przed załadowaniem strony.
- Pole e-mail to teraz `type=email` , a klawiatury przenośne zapewniają poprawne sugestie
- Obsługa funkcji tłumaczenia dla programu Chrome

**1.1.0**

- Dodano kontrolkę nie wylogowuj mnie (KMSI)

**1.0.0**

- Wersja początkowa

## <a name="mfa-page-multifactor"></a>Strona usługi MFA (wieloskładnikowa)

**1.2.1**

- Poprawki ułatwień dostępu w domyślnych szablonach

**1.2.0**

- Poprawki ułatwień dostępu
- Teraz możesz dodać atrybut do `data-preload="true"` [tagów HTML w](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) celu kontrolowania kolejności ładowania dla CSS i JavaScript.
  - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
  - Kontroluj kolejność, w jakiej `script` Tagi są pobierane i wykonywane przed załadowaniem strony.
- Pole e-mail to teraz `type=email` , a klawiatury przenośne zapewniają poprawne sugestie
- Obsługa funkcji tłumaczenia dla programu Chrome

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
- Teraz możesz dodać atrybut do `data-preload="true"` [tagów HTML w](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) celu kontrolowania kolejności ładowania dla CSS i JavaScript.
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
- Teraz możesz dodać atrybut do `data-preload="true"` [tagów HTML w](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) celu kontrolowania kolejności ładowania dla CSS i JavaScript.
  - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
  - Kontroluj kolejność, w jakiej `script` Tagi są pobierane i wykonywane przed załadowaniem strony.
- Pole e-mail to teraz `type=email` , a klawiatury przenośne zapewniają poprawne sugestie
- Obsługa funkcji tłumaczenia dla programu Chrome

**1.0.0**

- Wersja początkowa

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat dostosowywania interfejsu użytkownika aplikacji w zasadach niestandardowych, zobacz [Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych](custom-policy-ui-customization.md).
