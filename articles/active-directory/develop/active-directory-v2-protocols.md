---
title: Protokoły OAuth 2.0 i OpenID Connect - Platforma tożsamości firmy Microsoft | Azure
description: Przewodnik po protokołach OAuth 2.0 i OpenID Connect obsługiwanych przez punkt końcowy platformy tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: e94bdd6e79ff13d8ba736e140538bae74091f727
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680090"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>Protokoły OAuth 2.0 i OpenID Connect na platformie tożsamości firmy Microsoft

Punkt końcowy platformy tożsamości firmy Microsoft dla tożsamości jako usługi z protokołami standardowymi branżowymi, OpenID Connect i OAuth 2.0. Chociaż usługa jest zgodna ze standardami, mogą istnieć subtelne różnice między dowolnymi dwiema implementacjami tych protokołów. Informacje w tym miejscu będą przydatne, jeśli zdecydujesz się napisać kod, wysyłając i obsługując żądania HTTP lub korzystając z biblioteki open source innej firmy, zamiast korzystać z jednej z naszych [bibliotek open source.](reference-v2-libraries.md)

## <a name="the-basics"></a>Podstawy

W prawie wszystkich przepływach OAuth 2.0 i OpenID Connect w wymianie biorą udział cztery strony:

![Diagram przedstawiający role OAuth 2.0](./media/active-directory-v2-flows/protocols-roles.svg)

* **Serwer autoryzacji** jest punktem końcowym platformy tożsamości firmy Microsoft i jest odpowiedzialny za zapewnienie tożsamości użytkownika, udzielanie i odwoływanie dostępu do zasobów oraz wystawianie tokenów. Serwer autoryzacji znany również jako dostawca tożsamości — bezpiecznie obsługuje wszystko, co ma związek z informacjami o użytkowniku, jego dostępem i relacjami zaufania między stronami w przepływie.
* **Właściciel zasobu** jest zazwyczaj użytkownikiem końcowym. Jest to strona, która jest właścicielem danych i ma prawo umożliwić stronom trzecim dostęp do tych danych lub zasobów.
* **Klient OAuth** jest aplikacją, identyfikowaną przez jego identyfikator aplikacji. Klient OAuth jest zwykle stroną, z którą użytkownik końcowy wchodzi w interakcję i żąda tokenów z serwera autoryzacji. Klient musi mieć uprawnienia dostępu do zasobu przez właściciela zasobu.
* **Serwer zasobów** to miejsce, w którym znajduje się zasób lub dane. Ufa serwerowi autoryzacji w celu bezpiecznego uwierzytelniania i autoryzowania klienta OAuth i używa tokenów dostępu na okaziciela, aby zapewnić możliwość udzielenia dostępu do zasobu.

## <a name="app-registration"></a>Rejestracja aplikacji

Każda aplikacja, która chce zaakceptować konta osobiste i służbowe lub **szkolne,** musi być zarejestrowana za pośrednictwem środowiska rejestracji aplikacji w [witrynie Azure portal,](https://aka.ms/appregistrations) zanim będzie mogła podpisać tych użytkowników przy użyciu OAuth 2.0 lub OpenID Connect. Proces rejestracji aplikacji będzie zbierać i przypisywać kilka wartości do aplikacji:

* **Identyfikator aplikacji,** który jednoznacznie identyfikuje aplikację
* Identyfikator **URI przekierowania** (opcjonalnie), który może służyć do bezpośredniego reagowania z powrotem do aplikacji
* Kilka innych wartości specyficznych dla scenariusza.

Aby uzyskać więcej szczegółowych informacji, zapoznaj się z procedurą [rejestracji aplikacji](quickstart-register-app.md).

## <a name="endpoints"></a>Punkty końcowe

Po zarejestrowaniu aplikacja komunikuje się z platformą tożsamości firmy Microsoft, wysyłając żądania do punktu końcowego:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Gdzie `{tenant}` można przyjąć jedną z czterech różnych wartości:

| Wartość | Opis |
| --- | --- |
| `common` | Umożliwia użytkownikom korzystającym zarówno z osobistymi kontami Microsoft, jak i kontami służbowymi/szkolnymi z usługi Azure AD, aby zalogować się do aplikacji. |
| `organizations` | Zezwala tylko użytkownikom z kontami służbowymi/szkolnymi z usługi Azure AD na zalogowanie się do aplikacji. |
| `consumers` | Zezwala tylko użytkownikom z osobistymi kontami Microsoft (MSA) na logowanie się do aplikacji. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` | Zezwala tylko użytkownikom z kontami służbowymi/szkolnymi z określonej dzierżawy usługi Azure AD na zalogowanie się do aplikacji. Można użyć przyjaznej nazwy domeny dzierżawy usługi Azure AD lub identyfikatora GUID dzierżawy. |

Aby dowiedzieć się, jak wchodzić w interakcje z tymi punktami końcowymi, wybierz określony typ aplikacji w sekcji [Protokoły](#protocols) i skorzystaj z łączy, aby uzyskać więcej informacji.

> [!TIP]
> Każda aplikacja zarejestrowana w usłudze Azure AD może używać punktu końcowego platformy tożsamości firmy Microsoft, nawet jeśli nie loguje się na kontach osobistych.  W ten sposób można migrować istniejące aplikacje do platformy tożsamości firmy Microsoft i [msal](reference-v2-libraries.md) bez ponownego tworzenia aplikacji.

## <a name="tokens"></a>Tokeny

Implementacja platformy tożsamości firmy Microsoft OAuth 2.0 i OpenID Connect w szerokim zakresie wykorzystuje tokeny nośnika, w tym tokeny nośnika reprezentowane jako JWTs. Token nośnika jest lekki token zabezpieczający, który udziela "nośnik" dostęp do chronionego zasobu. W tym sensie "okaziciel" jest dowolną stroną, która może przedstawić token. Chociaż strona musi najpierw uwierzytelnić się za pomocą platformy tożsamości firmy Microsoft, aby otrzymać token nośnika, jeśli nie zostaną podjęte wymagane kroki w celu zabezpieczenia tokenu w transmisji i magazynie, może zostać przechwycona i używana przez niezamierzoną stronę. Niektóre tokeny zabezpieczające mają wbudowany mechanizm uniemożliwiający nieautoryzowanym stronom korzystanie z nich, tokeny nośnika nie mają tego mechanizmu i muszą być transportowane w bezpiecznym kanale, takim jak zabezpieczenia warstwy transportu (HTTPS). Jeśli token nośnika jest przesyłany w clear, złośliwa strona może użyć ataku man-in-the-middle, aby uzyskać token i użyć go do nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie podczas przechowywania lub buforowania tokenów na okaziciela do późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła i przechowuje tokeny na okaziciela w bezpieczny sposób. Aby uzyskać więcej informacji na temat zabezpieczeń tokenów okaziciela, zobacz [RFC 6750 Sekcja 5](https://tools.ietf.org/html/rfc6750).

Dalsze szczegóły dotyczące różnych typów tokenów używanych w punkcie końcowym platformy tożsamości firmy Microsoft są dostępne w [odwołaniu do tokenu punktu końcowego platformy tożsamości firmy Microsoft.](v2-id-and-access-tokens.md)

## <a name="protocols"></a>Protokoły

Jeśli jesteś gotowy, aby zobaczyć kilka przykładowych żądań, rozpocznij pracę z jednym z poniższych samouczków. Każdy z nich odpowiada określonego scenariusza uwierzytelniania. Jeśli potrzebujesz pomocy w określeniu, który jest odpowiedni przepływ dla Ciebie, sprawdź [typy aplikacji, które można zbudować za pomocą platformy tożsamości firmy Microsoft](v2-app-types.md).

* [Tworzenie aplikacji mobilnej i natywnej z OAuth 2.0](v2-oauth2-auth-code-flow.md)
* [Tworzenie aplikacji internetowych za pomocą OpenID Connect](v2-protocols-oidc.md)
* [Tworzenie aplikacji jednostronicowych za pomocą przepływu niejawnego OAuth 2.0](v2-oauth2-implicit-grant-flow.md)
* [Tworzenie demonów lub procesów po stronie serwera za pomocą przepływu poświadczeń klienta OAuth 2.0](v2-oauth2-client-creds-grant-flow.md)
* [Pobierz tokeny w internetowym interfejsie API za pomocą programu OAuth 2.0 w imieniu flow](v2-oauth2-on-behalf-of-flow.md)
