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
ms.openlocfilehash: 6d9f5538d377be1414089e591559344bde4f381a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98795640"
---
# <a name="security-tokens"></a>Tokeny zabezpieczające

Scentralizowany dostawca tożsamości jest szczególnie przydatny w przypadku aplikacji, które znajdują się na całym świecie, którzy nie muszą logować się z sieci przedsiębiorstwa. Platforma tożsamości firmy Microsoft uwierzytelnia użytkowników i dostarcza tokeny zabezpieczające, takie jak [tokeny dostępu](developer-glossary.md#access-token), tokeny [odświeżania](developer-glossary.md#refresh-token)i [tokeny identyfikatorów](developer-glossary.md#id-token). Tokeny zabezpieczające umożliwiają [aplikacji klienckiej](developer-glossary.md#client-application) dostęp do chronionych zasobów na [serwerze zasobów](developer-glossary.md#resource-server).

**Token dostępu**: token dostępu jest tokenem zabezpieczającym wystawianym przez [serwer autoryzacji](developer-glossary.md#authorization-server) w ramach przepływu [OAuth 2,0](active-directory-v2-protocols.md) . Zawiera informacje o użytkownikach i zasobach, dla których jest przeznaczony token. Informacje te mogą służyć do uzyskiwania dostępu do internetowych interfejsów API i innych chronionych zasobów. Tokeny dostępu są weryfikowane przez zasoby w celu udzielenia dostępu do aplikacji klienckiej. Aby dowiedzieć się więcej o tym, jak platforma tożsamości firmy Microsoft wystawia tokeny dostępu, zobacz [tokeny dostępu](access-tokens.md).

**Token odświeżania**: ponieważ tokeny dostępu są ważne przez krótki czas, serwery autoryzacji czasami wystawią token odświeżenia w tym samym czasie, gdy token dostępu zostanie wystawiony. Aplikacja kliencka może następnie w razie potrzeby wymienić ten token odświeżania dla nowego tokenu dostępu. Aby dowiedzieć się więcej na temat sposobu odwoływania uprawnień przez platformę Microsoft Identity, zobacz [odwoływanie tokenu](access-tokens.md#token-revocation).

**Token identyfikatora**: tokeny identyfikatora są wysyłane do aplikacji klienckiej w ramach przepływu [połączenia OpenID Connect](v2-protocols-oidc.md) . Mogą być wysyłane razem z tokenem dostępu lub zamiast niego. Tokeny identyfikatora są używane przez klienta do uwierzytelniania użytkownika. Aby dowiedzieć się więcej o tym, jak platforma tożsamości firmy Microsoft wystawia tokeny identyfikatorów, zobacz [identyfikatory tokenów](id-tokens.md).

> [!NOTE]
> W tym artykule omówiono tokeny zabezpieczające używane przez protokoły połączeń OAuth2 i OpenID Connect. Wiele aplikacji przedsiębiorstwa używa protokołu SAML do uwierzytelniania użytkowników. Aby uzyskać informacje na temat potwierdzeń SAML, zobacz [Azure Active Directory odwołanie do tokenu SAML](reference-saml-tokens.md).

## <a name="validate-security-tokens"></a>Weryfikowanie tokenów zabezpieczających

Jest to aplikacja, dla której Wygenerowano token, aplikacja sieci Web, która została podpisana przez użytkownika lub interfejs API sieci Web w celu zweryfikowania tokenu. Token jest podpisany przez serwer autoryzacji z kluczem prywatnym. Serwer autoryzacji publikuje odpowiadający mu klucz publiczny. Aby sprawdzić poprawność tokenu, aplikacja weryfikuje podpis przy użyciu klucza publicznego serwera autoryzacji, aby sprawdzić, czy sygnatura została utworzona przy użyciu klucza prywatnego.

Tokeny są ważne tylko przez ograniczony czas. Zazwyczaj serwer autoryzacji zapewnia parę tokenów, takich jak:

* Token dostępu, który uzyskuje dostęp do aplikacji lub chronionego zasobu.
* Token odświeżania używany do odświeżania tokenu dostępu, gdy token dostępu zbliża się do wygaśnięcia.

Tokeny dostępu są przesyłane do internetowego interfejsu API jako token okaziciela w `Authorization` nagłówku. Aplikacja może udostępnić token odświeżania serwerowi autoryzacji. Jeśli dostęp użytkownika do aplikacji nie został odwołany, spowoduje to przywrócenie nowego tokenu dostępu i nowego tokenu odświeżania. Jest to sposób obsługi scenariusza, w którym ktoś opuszcza przedsiębiorstwo. Gdy serwer autoryzacji odbiera token odświeżenia, nie będzie wystawiał innego prawidłowego tokenu dostępu, jeśli użytkownik nie jest już autoryzowany.

## <a name="json-web-tokens-and-claims"></a>Tokeny i oświadczenia sieci Web JSON

Platforma tożsamości firmy Microsoft implementuje tokeny zabezpieczające jako tokeny sieci Web JSON (JWTs), które zawierają *oświadczenia*. Ponieważ JWTs są używane jako tokeny zabezpieczające, ta forma uwierzytelniania jest czasami nazywana *uwierzytelnianiem JWT*.

[Twierdzenie](developer-glossary.md#claim) zawiera potwierdzenia dotyczące jednej jednostki, takiej jak aplikacja kliencka lub [właściciel zasobu](developer-glossary.md#resource-owner), do innej jednostki, takiej jak serwer zasobów. Takie odwołanie może być również określane jako odwołanie JWT lub w przypadku roszczeń tokenów sieci Web JSON.

Oświadczenia są parami nazw lub wartości, które przekazują fakty dotyczące podmiotu tokenu. Na przykład zgłoszenie może zawierać fakty dotyczące podmiotu zabezpieczeń, który został uwierzytelniony przez serwer autoryzacji. Oświadczenia obecne w określonym tokenie zależą od wielu elementów, takich jak typ tokenu, typ poświadczenia używany do uwierzytelniania podmiotu i konfiguracja aplikacji.

Aplikacje mogą używać oświadczeń dla różnych zadań, takich jak:

* Sprawdź poprawność tokenu.
* Określ [dzierżawcę](developer-glossary.md#tenant)podmiotu.
* Wyświetla informacje o użytkowniku.
* Określ autoryzację podmiotu.

Element Claim składa się z par klucz-wartość, które zawierają informacje takie jak:

* Serwer tokenów zabezpieczających, który wygenerował token.
* Data wygenerowania tokenu.
* Podmiot (na przykład użytkownik — z wyjątkiem demonów).
* Odbiorcy, czyli aplikacja, dla której został wygenerowany token.
* Aplikacja (klient), która poprosiła o token. W przypadku aplikacji sieci Web Ta aplikacja może być taka sama jak odbiorcy.

Aby dowiedzieć się więcej o tym, jak platforma tożsamości firmy Microsoft implementuje tokeny i informacje dotyczące roszczeń, zobacz [tokeny dostępu](access-tokens.md) i [tokeny identyfikatorów](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Jak każdy przepływ emituje tokeny i kody

W zależności od sposobu skompilowania klienta można użyć jednego (lub kilku) przepływów uwierzytelniania obsługiwanych przez platformę tożsamości firmy Microsoft. Te przepływy mogą generować różne tokeny (tokeny identyfikatorów, tokeny odświeżania, tokeny dostępu) i kody autoryzacji. Wymagają one innych tokenów, aby działały. Ta tabela zawiera omówienie.

|Przepływ | KONIECZN | Identyfikator tokenu | Token dostępu | Odśwież token | Kod autoryzacji |
|-----|----------|----------|--------------|---------------|--------------------|
|[Przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Niejawny przepływ](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrydowy przepływ OIDC](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Odświeżanie umorzenia tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Odśwież token | x | x | x| |
|[Przepływ „w imieniu”](v2-oauth2-on-behalf-of-flow.md) | Token dostępu| x| x| x| |
|[Poświadczenia klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (tylko aplikacja)| | |

Tokeny wystawione za pośrednictwem trybu niejawnego mają ograniczenie długości, ponieważ są one przesyłane z powrotem do przeglądarki za pośrednictwem adresu URL, gdzie `response_mode` is `query` lub `fragment` . Niektóre przeglądarki mają limit rozmiaru adresu URL, który może być umieszczony na pasku przeglądarki i niepowodzenie, gdy jest zbyt długi. W związku z tym tokeny nie mają `groups` `wids` oświadczeń ani oświadczenia.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat uwierzytelniania i autoryzacji na platformie tożsamości firmy Microsoft, zobacz następujące artykuły:

* Aby dowiedzieć się więcej o podstawowych pojęciach dotyczących uwierzytelniania i autoryzacji, zobacz [uwierzytelnianie a autoryzacja](authentication-vs-authorization.md).
* Aby dowiedzieć się więcej o rejestrowaniu aplikacji do integracji, zobacz [model aplikacji](application-model.md).
* Aby dowiedzieć się więcej o przepływie logowania aplikacji sieci Web, komputerów stacjonarnych i mobilnych, zobacz temat [Rejestrowanie w usłudze Flow](app-sign-in-flow.md).
