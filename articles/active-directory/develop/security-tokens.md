---
title: Tokeny zabezpieczające | Azure
titleSuffix: Microsoft identity platform
description: Poznaj podstawowe informacje na temat tokenów zabezpieczających na platformie tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 9668d3b0b57e36fb95421f8b502b9b743be8eb31
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756225"
---
# <a name="security-tokens"></a>Tokeny zabezpieczające

Scentralizowany dostawca tożsamości jest szczególnie przydatny dla aplikacji, które znajdują się na całym świecie, niekoniecznie logują się z sieci przedsiębiorstwa. Platforma tożsamości firmy Microsoft uwierzytelnia użytkowników i udostępnia tokeny zabezpieczające, takie jak [token dostępu](developer-glossary.md#access-token), [token odświeżania](developer-glossary.md#refresh-token)i [token identyfikatora](developer-glossary.md#id-token), które umożliwiają [aplikacji klienckiej](developer-glossary.md#client-application) dostęp do chronionych zasobów na [serwerze zasobów](developer-glossary.md#resource-server).

**Token dostępu** jest tokenem zabezpieczającym wystawianym przez [serwer autoryzacji](developer-glossary.md#authorization-server) w ramach przepływu [OAuth 2,0](active-directory-v2-protocols.md) . Zawiera informacje o użytkowniku i aplikacji, dla których jest przeznaczony token; służy do uzyskiwania dostępu do interfejsów API sieci Web i innych chronionych zasobów. Aby dowiedzieć się więcej o tym, jak platforma tożsamości firmy Microsoft wystawia tokeny dostępu, zobacz [tokeny dostępu](access-tokens.md).

Tokeny dostępu są prawidłowe tylko przez krótki czas, więc serwery autoryzacji czasami wydają **token odświeżenia** w tym samym czasie, gdy token dostępu zostanie wystawiony. Aplikacja kliencka może następnie w razie potrzeby wymienić ten token odświeżania dla nowego tokenu dostępu. Aby dowiedzieć się więcej na temat sposobu odwoływania uprawnień przez platformę Microsoft Identity, zobacz [odwoływanie tokenu](access-tokens.md#token-revocation).

**Tokeny identyfikatorów** są wysyłane do aplikacji klienckiej w ramach przepływu [połączenia OpenID Connect](v2-protocols-oidc.md) . Mogą być wysyłane po stronie lub zamiast tokenu dostępu i są używane przez klienta do uwierzytelniania użytkownika. Aby dowiedzieć się więcej o tym, jak platforma tożsamości firmy Microsoft wystawia tokeny identyfikatorów, zobacz [identyfikatory tokenów](id-tokens.md).

> [!NOTE]
> W tym artykule omówiono tokeny zabezpieczające używane przez protokoły połączeń OAuth2 i OpenID Connect. Wiele aplikacji przedsiębiorstwa używa protokołu SAML do uwierzytelniania użytkowników. Aby uzyskać informacje na temat potwierdzeń SAML, zobacz [odwołanie do tokenu SAML usługi Azure AD](reference-saml-tokens.md) .

## <a name="validating-security-tokens"></a>Weryfikowanie tokenów zabezpieczających

Jest to aplikacja, dla której Wygenerowano token, aplikację sieci Web, która została zarejestrowana przez użytkownika lub wywoływany interfejs API sieci Web, w celu zweryfikowania tokenu. Token jest podpisany przez **serwer tokenu zabezpieczającego (STS)** z kluczem prywatnym. Usługa STS publikuje odpowiadający mu klucz publiczny. Aby sprawdzić poprawność tokenu, aplikacja weryfikuje podpis przy użyciu klucza publicznego STS, aby sprawdzić, czy sygnatura została utworzona przy użyciu klucza prywatnego.

Tokeny są prawidłowe tylko przez ograniczony czas. Zazwyczaj usługa STS oferuje parę tokenów:

* Token dostępu do uzyskiwania dostępu do aplikacji lub chronionego zasobu, a także
* Token odświeżania używany do odświeżania tokenu dostępu, gdy token dostępu zbliża się do wygaśnięcia.

Tokeny dostępu są przesyłane do internetowego interfejsu API jako token okaziciela w `Authorization` nagłówku. Aplikacja może dostarczyć token odświeżania do usługi STS, a jeśli użytkownik nie ma dostępu do aplikacji, zostanie przywrócony nowy token dostępu i nowy token odświeżania. Jest to sposób obsługi scenariusza, w którym ktoś opuszcza przedsiębiorstwo. Gdy usługa STS odbierze token odświeżenia, nie będzie wydawać innego prawidłowego tokenu dostępu, jeśli użytkownik nie jest już autoryzowany.

## <a name="json-web-tokens-jwts-and-claims"></a>Tokeny sieci Web JSON (JWTs) i oświadczenia

Platforma tożsamości firmy Microsoft implementuje tokeny zabezpieczające jako **tokeny sieci Web JSON (JWTs)** , które zawierają **oświadczenia**. Ponieważ JWTs są używane jako tokeny zabezpieczające, ta forma uwierzytelniania jest czasami nazywana **uwierzytelnianiem JWT**.

[Twierdzenie](developer-glossary.md#claim) zawiera potwierdzenia dotyczące jednej jednostki, takiej jak aplikacja kliencka lub [właściciel zasobu](developer-glossary.md#resource-owner), do innej jednostki, takiej jak serwer zasobów. Można również odwołać się do żądania jako żądania tokenu JWT lub tokenu sieci Web JSON.

Oświadczenia są parami nazw/wartości, które przekazują fakty dotyczące podmiotu tokenu. Na przykład zgłoszenie może zawierać fakty dotyczące podmiotu zabezpieczeń, który został uwierzytelniony przez serwer autoryzacji. Oświadczenia obecne w danym tokenie zależą od wielu elementów, w tym typu tokenu, typu poświadczenia używanego do uwierzytelniania podmiotu, konfiguracji aplikacji i tak dalej.

Aplikacje mogą używać oświadczeń dla różnych zadań, takich jak:

* Sprawdzanie poprawności tokenu
* Identyfikowanie [dzierżawy](developer-glossary.md#tenant) podmiotu
* Wyświetlanie informacji o użytkowniku
* Określanie autoryzacji podmiotu

Element Claim składa się z par klucz-wartość, które zawierają informacje takie jak:

* Serwer tokenów zabezpieczających, który wygenerował token
* Data wygenerowania tokenu
* Podmiot (na przykład użytkownik — z wyjątkiem demonów)
* Odbiorcy, czyli aplikacja, dla której Wygenerowano token
* Aplikacja (klient), która poprosiła o token. W przypadku aplikacji sieci Web może to być takie samo jak odbiorcy

Aby dowiedzieć się więcej o tym, jak platforma tożsamości firmy Microsoft implementuje tokeny i informacje dotyczące roszczeń, zobacz [tokeny dostępu](access-tokens.md) i [tokeny identyfikatorów](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Jak każdy przepływ emituje tokeny i kody

W zależności od sposobu skompilowania klienta można użyć jednego (lub kilku) przepływów uwierzytelniania obsługiwanych przez platformę tożsamości firmy Microsoft. Te przepływy mogą generować różne tokeny (tokeny identyfikatorów, tokeny odświeżenia, tokeny dostępu), a także kody autoryzacji i wymagać innych tokenów, aby działały. Ten wykres zawiera przegląd:

|Przepływ | KONIECZN | Identyfikator tokenu | token dostępu | Odśwież token | kod autoryzacji |
|-----|----------|----------|--------------|---------------|--------------------|
|[Przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Niejawny przepływ](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrydowy przepływ OIDC](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Odświeżanie umorzenia tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Odśwież token | x | x | x| |
|[Przepływ „w imieniu”](v2-oauth2-on-behalf-of-flow.md) | token dostępu| x| x| x| |
|[Poświadczenia klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (tylko aplikacja)| | |

Tokeny wystawione za pośrednictwem trybu niejawnego mają ograniczenie długości z powodu przekazanie z powrotem do przeglądarki za pośrednictwem adresu URL (gdzie `response_mode` is `query` lub `fragment` ).  Niektóre przeglądarki mają limit rozmiaru adresu URL, który może być umieszczony na pasku przeglądarki i niepowodzenie, gdy jest zbyt długi.  W rezultacie te tokeny nie mają `groups` ani `wids` oświadczenia.

## <a name="next-steps"></a>Następne kroki

W przypadku innych tematów obejmujących podstawowe informacje dotyczące uwierzytelniania i autoryzacji:

* Zobacz [uwierzytelnianie i autoryzacja](authentication-vs-authorization.md) , aby dowiedzieć się więcej na temat podstawowych pojęć związanych z uwierzytelnianiem i autoryzacją w usłudze Microsoft Identity platform.
* Zobacz [model aplikacji](application-model.md) , aby dowiedzieć się więcej o procesie rejestracji aplikacji, aby umożliwić integrację z platformą tożsamości firmy Microsoft.
* Aby dowiedzieć się więcej o przepływie logowania aplikacji sieci Web, komputerów stacjonarnych i aplikacji mobilnych na platformie tożsamości firmy Microsoft, zobacz temat rejestrowanie w usłudze [Flow](app-sign-in-flow.md) .
