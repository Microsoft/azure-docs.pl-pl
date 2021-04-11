---
title: Biblioteki uwierzytelniania platformy tożsamości firmy Microsoft | Azure
description: Lista bibliotek klienta i oprogramowania pośredniczącego zgodnych z platformą tożsamości firmy Microsoft. Te biblioteki służą do dodawania obsługi logowania użytkownika (uwierzytelniania) i dostępu do chronionego internetowego interfejsu API (autoryzacji) do aplikacji.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 03/30/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 321a5c473df30dd6f00bbcd1294d48ce8da34009
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060384"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Biblioteki uwierzytelniania platformy tożsamości firmy Microsoft

W poniższych tabelach przedstawiono obsługę biblioteki uwierzytelniania firmy Microsoft dla kilku typów aplikacji. Obejmują one linki do kodu źródłowego biblioteki, gdzie można pobrać pakiet dla projektu aplikacji oraz czy Biblioteka obsługuje logowanie użytkownika (uwierzytelnianie), dostęp do chronionych interfejsów API sieci Web (autoryzacja) lub obie te funkcje.

Platforma tożsamości firmy Microsoft została certyfikowana przez OpenID Connect Foundation jako [Certyfikowany dostawca OpenID Connect](https://openid.net/certification/). Jeśli wolisz używać biblioteki innej niż biblioteka uwierzytelniania firmy Microsoft (MSAL) lub innej biblioteki obsługiwanej przez firmę Microsoft, wybierz jedną z [certyfikowaną implementacją programu OpenID Connect Connect](https://openid.net/developers/certified/).

W przypadku wybrania opcji ręcznego tworzenia kodu na poziomie protokołu [OAuth 2,0 lub OpenID Connect Connect 1,0](active-directory-v2-protocols.md)należy zwrócić szczególną uwagę na kwestie dotyczące zabezpieczeń w każdej specyfikacji standardu i postępować zgodnie z metodologią cyklu projektowania oprogramowania (SDL), taką jak [Microsoft SDL][Microsoft-SDL].

## <a name="single-page-application-spa"></a>Aplikacja jednostronicowa (SPA)

Jednostronicowa aplikacja działa całkowicie w przeglądarce i pobiera dane strony (HTML, CSS i JavaScript) dynamicznie lub w czasie ładowania aplikacji. Może wywoływać interfejsy API sieci Web w celu współpracy z źródłami danych zaplecza.

Ponieważ kod SPA działa całkowicie w przeglądarce, jest traktowany jako *Klient publiczny* , który nie może bezpiecznie przechowywać wpisów tajnych.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="web-application"></a>Aplikacja internetowa

Aplikacja sieci Web uruchamia kod na serwerze, który generuje i wysyła pliki HTML, CSS i JavaScript do przeglądarki sieci Web użytkownika do renderowania. Tożsamość użytkownika jest utrzymywana jako sesja między przeglądarką użytkownika (frontonem) a serwerem sieci Web (zapleczem).

Ponieważ kod aplikacji sieci Web jest uruchamiany na serwerze sieci Web, jest traktowany jako *poufny klient* , który umożliwia bezpieczne przechowywanie wpisów tajnych.

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

## <a name="desktop-application"></a>Aplikacja klasyczna

Aplikacja klasyczna to zazwyczaj kod binarny (skompilowany), który wyświetla interfejs użytkownika i jest przeznaczony do uruchamiania na pulpicie użytkownika.

Ponieważ aplikacja klasyczna jest uruchamiana na pulpicie użytkownika, jest traktowana jako *Klient publiczny* , który nie może bezpiecznie przechowywać wpisów tajnych.

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="mobile-application"></a>Aplikacja mobilna

Aplikacja mobilna to zazwyczaj kod binarny (skompilowany), który wyświetla interfejs użytkownika i jest przeznaczony do uruchamiania na urządzeniu przenośnym użytkownika.

Ponieważ aplikacja mobilna jest uruchamiana na urządzeniu przenośnym użytkownika, jest traktowana jako *Klient publiczny* , który nie może bezpiecznie przechowywać wpisów tajnych.

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="service--daemon"></a>Usługa/demon

Usługi i demony są często używane w przypadku komunikacji między serwerami i nienadzorowanymi (czasami nazywanymi *bezobsługowymi*). Ponieważ na klawiaturze nie ma uprawnień do wprowadzania poświadczeń lub zgody na dostęp do zasobów, te aplikacje są uwierzytelniane jako same, a nie jako użytkownik, podczas żądania autoryzowanego dostępu do zasobów internetowego interfejsu API.

Usługa lub demon działający na serwerze jest traktowany jako *poufny klient* , który umożliwia bezpieczne przechowywanie swoich kluczy tajnych.

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat biblioteki uwierzytelniania firmy Microsoft, zobacz [Omówienie biblioteki uwierzytelniania firmy Microsoft (MSAL)](msal-overview.md).

<!--Image references-->
[y]: ./media/common/yes.png
[n]: ./media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
