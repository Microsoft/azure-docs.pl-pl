---
title: Sesje logowania jednokrotnego w Azure Active Directory B2C | Microsoft Docs
description: Omówienie konfigurowania zachowania sesji w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e02323df3a12c4a74de1fb62b36762fc739e9e5
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750445"
---
# <a name="azure-ad-b2c-session"></a>Sesja Azure AD B2C

Logowanie jednokrotne (SSO) zwiększa bezpieczeństwo i wygodę, gdy użytkownicy logują się w aplikacjach w Azure Active Directory B2C (Azure AD B2C). W tym artykule opisano metody logowania jednokrotnego używane w Azure AD B2C i ułatwiają wybranie najbardziej odpowiedniej metody rejestracji jednokrotnej podczas konfigurowania zasad.

Logowanie jednokrotne umożliwia użytkownikom logowanie jednokrotne przy użyciu jednego konta i uzyskiwanie dostępu do wielu aplikacji. Aplikacja może być aplikacją sieci Web, mobilną lub jednostronicową, niezależnie od nazwy platformy lub domeny.

Gdy użytkownik po raz pierwszy zaloguje się do aplikacji, Azure AD B2C utrzymuje sesję opartą na plikach cookie. Po kolejnych żądaniach uwierzytelniania Azure AD B2C odczytuje i sprawdza poprawność sesji opartej na plikach cookie i wystawia token dostępu bez monitowania użytkownika o ponowne zalogowanie. Jeśli sesja oparta na plikach cookie wygasa lub jest nieprawidłowa, użytkownik zostanie poproszony o ponowne zalogowanie.  

## <a name="sso-session-types"></a>Typy sesji logowania jednokrotnego

Integracja z Azure AD B2C obejmuje trzy typy sesji rejestracji jednokrotnej:

- **Azure AD B2C** — sesja zarządzana przez Azure AD B2C
- **Federacyjny dostawca tożsamości** — sesja zarządzana przez dostawcę tożsamości, na przykład Facebook, Salesforce lub konto Microsoft
- **Aplikacja** — sesja zarządzana przez aplikację sieci Web, aplikacji mobilnej lub jednostronicowej

![Sesja logowania jednokrotnego](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C Logowanie jednokrotne 

W przypadku pomyślnego uwierzytelnienia użytkownika przy użyciu konta lokalnego lub społecznościowego Azure AD B2C w przeglądarce użytkownika jest przechowywana sesja oparta na plikach cookie. Plik cookie jest przechowywany w Azure AD B2C nazwy domeny dzierżawcy, takiej jak `https://contoso.b2clogin.com` .

Jeśli użytkownik początkowo zaloguje się przy użyciu konta federacyjnego, a następnie w oknie czasu sesji (czas wygaśnięcia lub TTL) zaloguje się do tej samej aplikacji lub innej aplikacji, Azure AD B2C próbuje uzyskać nowy token dostępu od dostawcy tożsamości federacyjnych. Jeśli sesja federacyjnego dostawcy tożsamości wygasła lub jest nieprawidłowa, dostawca tożsamości federacyjnych poprosi użytkownika o ich poświadczenia. Jeśli sesja jest nadal aktywna (lub jeśli użytkownik zalogował się przy użyciu konta lokalnego zamiast konta federacyjnego), Azure AD B2C autoryzuje użytkownika i eliminuje dalsze monity.

Można skonfigurować zachowanie sesji, w tym czas wygaśnięcia sesji i sposób, w jaki Azure AD B2C współużytkują sesję dla zasad i aplikacji.

### <a name="federated-identity-provider-sso"></a>Federacyjny dostawca tożsamości — Logowanie jednokrotne

Dostawca tożsamości społecznościowej lub korporacyjnej zarządza własną sesją. Plik cookie jest przechowywany w nazwie domeny dostawcy tożsamości, na przykład `https://login.salesforce.com` . Azure AD B2C nie steruje sesją federacyjnego dostawcy tożsamości. Zamiast tego zachowanie sesji jest określane przez dostawcę tożsamości federacyjnych. 

Poniżej przedstawiono przykładowy scenariusz:

1. Użytkownik loguje się do usługi Facebook, aby sprawdzić ich źródło.
2. Później użytkownik otwiera aplikację i uruchamia proces logowania. Aplikacja przekierowuje użytkownika do Azure AD B2C w celu ukończenia procesu logowania.
3. Na stronie Rejestracja w Azure AD B2C lub logowanie użytkownik zdecyduje się na zalogowanie się przy użyciu konta w serwisie Facebook. Użytkownik zostanie przekierowany do serwisu Facebook. W przypadku aktywnej sesji w serwisie Facebook użytkownik nie jest monitowany o podanie poświadczeń i zostaje natychmiast przekierowany do Azure AD B2C przy użyciu tokenu w usłudze Facebook.

### <a name="application-sso"></a>Logowanie jednokrotne aplikacji

Aplikacja sieci Web, mobilna lub jednostronicowa może być chroniona przez dostęp OAuth, tokeny identyfikatorów lub tokeny SAML. Gdy użytkownik próbuje uzyskać dostęp do chronionego zasobu w aplikacji, aplikacja sprawdzi, czy po stronie aplikacji znajduje się aktywna sesja. Jeśli nie ma żadnej sesji aplikacji lub sesja wygasła, aplikacja przeniesie użytkownika do Azure AD B2C stronie logowania.

Sesja aplikacji może być sesją plików cookie przechowywaną w obszarze Nazwa domeny aplikacji, na przykład `https://contoso.com` . Aplikacje mobilne mogą przechowywać sesję w inny sposób, ale przy użyciu podobnego podejścia.

## <a name="azure-ad-b2c-session-configuration"></a>Konfiguracja sesji Azure AD B2C

### <a name="session-scope"></a>Zakres sesji

Sesję Azure AD B2C można skonfigurować przy użyciu następujących zakresów:

- **Dzierżawca** — to ustawienie jest domyślne. Użycie tego ustawienia umożliwia wielu aplikacjom i przepływom użytkowników w dzierżawie B2C współużytkowanie tej samej sesji użytkownika. Na przykład, gdy użytkownik loguje się do aplikacji, użytkownik może również bezproblemowo zalogować się do innego, aby uzyskać do niego dostęp.
- **Aplikacja** — to ustawienie umożliwia obsługę sesji użytkownika wyłącznie dla aplikacji, niezależnie od innych aplikacji. Można na przykład użyć tego ustawienia, jeśli chcesz, aby użytkownik zalogował się do usługi contoso apteki, niezależnie od tego, czy użytkownik jest już zalogowany do artykułów spożywczych firmy Contoso.
- **Zasady** — to ustawienie umożliwia obsługę sesji użytkownika wyłącznie dla przepływu użytkownika niezależnie od aplikacji, z których korzysta. Na przykład jeśli użytkownik został już zalogowany i ukończył krok uwierzytelniania wieloskładnikowego (MFA), użytkownik może uzyskać dostęp do części o wyższym poziomie zabezpieczeń wielu aplikacji, o ile sesja związana z przepływem użytkownika nie wygasa.
- **Wyłączone** — to ustawienie wymusza uruchomienie użytkownika przez cały przepływ użytkownika przy każdym wykonaniu zasad.

### <a name="session-life-time"></a>Czas trwania sesji

**Czas trwania sesji** to czas, przez jaki plik cookie sesji Azure AD B2C jest przechowywany w przeglądarce użytkownika po pomyślnym uwierzytelnieniu. Czas trwania sesji można ustawić na wartość z okresu od 15 do 720 minut.

### <a name="keep-me-signed-in"></a>Nie wylogowuj mnie

Funkcja [Keeping Me](custom-policy-keep-me-signed-in.md) wydłuża czas trwania sesji przez użycie trwałego pliku cookie. Sesja pozostaje aktywna po zamknięciu i ponownym otwarciu przeglądarki przez użytkownika. Sesja zostanie odwołana tylko wtedy, gdy użytkownik wyloguje się. Funkcja nie wylogowuj mnie dotyczy tylko logowania z kontami lokalnymi.

Funkcja Keeping Me ma pierwszeństwo przed okresem istnienia sesji. Jeśli funkcja nie wylogowuj mnie jest włączona, a użytkownik wybierze ją, ta funkcja będzie określać, kiedy sesja wygaśnie. 

### <a name="session-expiry-type"></a>Typ wygaśnięcia sesji

**Typ wygaśnięcia sesji** wskazuje, w jaki sposób sesja jest rozszerzona o ustawienia czasu istnienia sesji lub ustawienia nie wylogowuj mnie.

- **Krocząca** — wskazuje, że sesja jest rozszerzana za każdym razem, gdy użytkownik wykonuje uwierzytelnianie na podstawie plików cookie (domyślnie).
- **Bezwzględna** — wskazuje, że użytkownik jest zmuszony do ponownego uwierzytelnienia po upływie określonego czasu.

## <a name="sign-out"></a>Wylogowywanie

Jeśli chcesz podpisać użytkownika poza aplikacją, nie wystarczy wyczyścić plików cookie aplikacji lub zakończyć sesję z użytkownikiem. Musisz przekierować użytkownika do Azure AD B2C, aby się wylogować. W przeciwnym razie użytkownik może być w stanie ponownie uwierzytelnić się w aplikacjach bez konieczności ponownego wprowadzania poświadczeń.

W przypadku żądania wylogowania Azure AD B2C:

1. Unieważnia Azure AD B2C sesji opartej na plikach cookie.
1. Próbuje wylogować się z dostawców tożsamości federacyjnych:
   - OpenID Connect Connect — Jeśli dostawca tożsamości dobrze znanego punktu końcowego konfiguracji określa `end_session_endpoint` lokalizację.
   - OAuth2 — Jeśli [metadane dostawcy tożsamości](oauth2-technical-profile.md#metadata) zawierają `end_session_endpoint` lokalizację.
   - SAML — Jeśli [metadane dostawcy tożsamości](saml-identity-provider-technical-profile.md#metadata) zawierają `SingleLogoutService` lokalizację.
1. Opcjonalnie możesz wylogować się z innych aplikacji. Aby uzyskać więcej informacji, zobacz sekcję [Logowanie](#single-sign-out) jednokrotne.

> [!NOTE]
> Przy użyciu [zasad niestandardowych](custom-policy-overview.md)można wyłączyć Wylogowywanie z dostawców tożsamości federacyjnych, ustawiając metadane profilu technicznego dostawcy tożsamości `SingleLogoutEnabled` `false` .

Wylogowanie powoduje wyczyszczenie stanu logowania jednokrotnego użytkownika przy użyciu Azure AD B2C, ale może nie podpisać użytkownika z sesji dostawcy tożsamości społecznościowej. Jeśli użytkownik wybierze tego samego dostawcę tożsamości podczas kolejnego logowania, może ponownie uwierzytelnić się bez wprowadzania poświadczeń. Jeśli użytkownik chce wylogować się z aplikacji, nie musi to oznaczać, że chce się wylogować z konta w serwisie Facebook. Jeśli jednak są używane konta lokalne, sesja użytkownika zostanie zakończona prawidłowo.

### <a name="single-sign-out"></a>Wylogowanie jednokrotne 


> [!NOTE]
> Ta funkcja jest ograniczona do [zasad niestandardowych](custom-policy-overview.md).

Po przekierowaniu użytkownika do punktu końcowego wylogowania Azure AD B2C (dla protokołów OAuth2 i SAML) Azure AD B2C czyści sesję użytkownika z przeglądarki. Jednak użytkownik może nadal być zalogowany do innych aplikacji, które używają Azure AD B2C do uwierzytelniania. Aby umożliwić tym aplikacjom jednokrotne podpisywanie użytkownika, Azure AD B2C wysyła żądanie HTTP GET do zarejestrowanych `LogoutUrl` wszystkich aplikacji, do których użytkownik jest aktualnie zalogowany.


Aplikacje muszą odpowiedzieć na to żądanie przez wyczyszczenie każdej sesji identyfikującej użytkownika i zwracającej `200` odpowiedź. Jeśli chcesz obsługiwać Logowanie jednokrotne w aplikacji, musisz zaimplementować `LogoutUrl` w kodzie aplikacji. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować zachowanie sesji w przepływie użytkownika](session-behavior.md).
- Dowiedz się, jak [skonfigurować zachowanie sesji w zasadach niestandardowych](session-behavior-custom-policy.md).
