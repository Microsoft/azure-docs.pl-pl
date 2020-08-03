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
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4548b50e4168f260cb401c40dd4e61192cea1015
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87489541"
---
# <a name="page-layout-versions"></a>Wersje układu strony

Pakiety układów stron są okresowo aktualizowane w celu uwzględnienia poprawek i ulepszeń w ich elementach strony. Następujący dziennik zmian określa zmiany wprowadzone w każdej wersji.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="210"></a>2.1.0

- Strona z własnym potwierdzeniem ( `selfasserted` )
  - Poprawki lokalizacji i ułatwień dostępu.
- Ujednolicona Strona dostawcy SSP ( `unifiedssp` )
  - Dodano obsługę wielu linków do rejestracji.
  - Dodano obsługę walidacji danych wejściowych użytkownika zgodnie z regułami predykatów zdefiniowanymi w zasadach.

## <a name="200"></a>2.0.0

- Strona z własnym potwierdzeniem ( `selfasserted` )
  - Dodano obsługę [formantów wyświetlania](display-controls.md) w zasadach niestandardowych.

## <a name="120"></a>1.2.0

- Wszystkie strony
  - Poprawki ułatwień dostępu
  - Teraz możesz dodać atrybut do `data-preload="true"` [tagów HTML w](custom-policy-ui-customization.md#guidelines-for-using-custom-page-content) celu kontrolowania kolejności ładowania dla CSS i JavaScript.
    - Załaduj połączone pliki CSS jednocześnie jako szablon HTML, aby nie "migotać" między ładowaniem plików.
    - Kontroluj kolejność, w jakiej `script` Tagi są pobierane i wykonywane przed załadowaniem strony.
  - Pole e-mail to teraz `type=email` , a klawiatury przenośne zapewniają poprawne sugestie
  - Obsługa funkcji tłumaczenia dla programu Chrome
- Ujednolicone i samodzielne strony
  - Pola username/email i Password używają teraz `form` elementu HTML, aby umożliwić programowi Edge i Internet Explorer (IE) prawidłowe Zapisywanie tych informacji.
- Strona z własnym potwierdzeniem
  - Dodano konfigurowalne opóźnienie sprawdzania poprawności danych użytkownika w celu ulepszenia środowiska użytkownika.

## <a name="110"></a>1.1.0

- Strona wyjątku (globalexception)
  - Poprawka ułatwień dostępu
  - Usunięto komunikat domyślny, gdy nie ma kontaktu z zasad
  - Usunięto domyślny kod CSS
- Strona usługi MFA (wieloskładnikowa)
  - Przycisk "Potwierdź kod" został usunięty
  - Pole wejściowe dla kodu przyjmuje teraz tylko dane wejściowe z maksymalnie sześciu (6) znaków
  - Strona automatycznie podejmie próbę zweryfikowania kodu wprowadzonego po wprowadzeniu 6-cyfrowy kod bez kliknięcia przycisku
  - Jeśli kod jest nieprawidłowy, pole wejściowe zostanie automatycznie wyczyszczone
  - Po trzech (3) próbach z nieprawidłowym kodem B2C wysyła błąd z powrotem do jednostki uzależnionej
  - Poprawki ułatwień dostępu
  - Usunięto domyślny kod CSS
- Strona z własnym potwierdzeniem (selfasserted)
  - Usunięto alert dotyczący anulowania
  - Klasa CSS dla elementów Error
  - Ulepszone wyświetlanie/ukrywanie logiki błędów
  - Usunięto domyślny kod CSS
- Ujednolicony Dostawca SSP (unifiedssp)
  - Dodano kontrolkę nie wylogowuj mnie (KMSI)

## <a name="100"></a>1.0.0

- Wersja początkowa

## <a name="next-steps"></a>Następne kroki

Aby uzyskać szczegółowe informacje na temat dostosowywania interfejsu użytkownika aplikacji w zasadach niestandardowych, zobacz [Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych](custom-policy-ui-customization.md).
