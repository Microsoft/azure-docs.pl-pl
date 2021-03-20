---
title: Protokoły uwierzytelniania OAuth 2,0 i OpenID Connect na platformie tożsamości firmy Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Przewodnik dotyczący protokołów uwierzytelniania OAuth 2,0 i OpenID Connect, które są obsługiwane przez platformę tożsamości firmy Microsoft.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 765c363542b07deac44d47b94731e1109fcba045
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755747"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>Protokoły uwierzytelniania OAuth 2,0 i OpenID Connect na platformie tożsamości firmy Microsoft

Punkt końcowy platformy tożsamości firmy Microsoft dla tożsamości jako usługi implementuje uwierzytelnianie i autoryzację przy użyciu standardowych protokołów OpenID Connect Connect (OIDC) i OAuth 2,0. Mimo że usługa jest zgodna ze standardami, mogą istnieć drobne różnice między dwoma implementacjami tych protokołów. Te informacje będą przydatne w przypadku, gdy użytkownik zdecyduje się pisać kod przez bezpośrednie wysyłanie i Obsługiwanie żądań HTTP lub użycie biblioteki typu open-source innej firmy zamiast korzystania z jednej z naszych [bibliotek typu open source](reference-v2-libraries.md).

## <a name="the-basics"></a>Podstawy

W prawie wszystkie przepływy za pomocą protokołu OAuth 2,0 i OpenID Connect łączą się cztery strony w wymianie:

![Diagram przedstawiający role OAuth 2,0](./media/active-directory-v2-flows/protocols-roles.svg)

* **Serwer autoryzacji** jest platformą tożsamości firmy Microsoft i jest odpowiedzialny za zapewnienie tożsamości użytkownika, przydzielanie i odwoływanie dostępu do zasobów oraz wystawianie tokenów. Serwer autoryzacji jest również znany jako dostawca tożsamości — bezpiecznie obsługuje wszystko, aby zrobić z informacjami o użytkowniku, ich dostępem i relacjami zaufania między stronami w przepływie.
* **Właścicielem zasobu** jest zazwyczaj użytkownik końcowy. Jest to strona, która jest właścicielem danych i ma możliwość zezwalania klientom na dostęp do tych danych lub zasobów.
* **Klient OAuth** jest aplikacją identyfikowaną przez identyfikator aplikacji. Klient uwierzytelniania OAuth jest zwykle stroną, z którą użytkownik końcowy współdziała, i żąda tokenów z serwera autoryzacji. Klient musi mieć przyznane uprawnienie dostępu do zasobu przez właściciela zasobu.
* **Serwer zasobów** jest miejscem, w którym znajdują się zasoby lub dane. Ufa serwerowi autoryzacji do bezpiecznego uwierzytelniania i autoryzacji klienta OAuth i używa tokenów dostępu okaziciela, aby zapewnić, że dostęp do zasobu można udzielić.

## <a name="app-registration"></a>Rejestrowanie aplikacji

Każda aplikacja, która chce akceptować konta osobiste i służbowe, musi zostać zarejestrowana przez **rejestracje aplikacji** środowisko w [Azure Portal](https://aka.ms/appregistrations) , zanim będzie mogła podpisać tych użytkowników przy użyciu protokołu OAuth 2,0 lub OpenID Connect Connect. Proces rejestracji aplikacji będzie zbierać i przypisywać kilka wartości do aplikacji:

* **Identyfikator aplikacji** , który jednoznacznie identyfikuje aplikację
* **Identyfikator URI przekierowania** (opcjonalnie), który może służyć do kierowania odpowiedzi z powrotem do aplikacji
* Kilka innych wartości specyficznych dla scenariusza.

Aby uzyskać więcej szczegółowych informacji, zapoznaj się z procedurą [rejestracji aplikacji](quickstart-register-app.md).

## <a name="endpoints"></a>Punkty końcowe

Po zarejestrowaniu aplikacja komunikuje się z platformą tożsamości firmy Microsoft, wysyłając żądania do punktu końcowego:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Gdzie `{tenant}` może przyjmować jedną z czterech różnych wartości:

| Wartość | Opis |
| --- | --- |
| `common` | Zezwala użytkownikom na logowanie się do aplikacji zarówno dla osobistych kont Microsoft, jak i kont służbowych z usługi Azure AD. |
| `organizations` | Umożliwia logowanie się do aplikacji wyłącznie użytkownikom z kontami służbowymi z usługi Azure AD. |
| `consumers` | Umożliwia logowanie się do aplikacji wyłącznie użytkownikom z osobistymi kontami Microsoft (MSA). |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` lub `contoso.onmicrosoft.com` | Zezwala tylko użytkownikom z kontami służbowymi z określonej dzierżawy usługi Azure AD na logowanie do aplikacji. Można użyć przyjaznej nazwy domeny dzierżawy usługi Azure AD lub identyfikatora GUID dzierżawcy. |

Aby dowiedzieć się, jak korzystać z tych punktów końcowych, wybierz konkretny typ aplikacji w sekcji [Protokoły](#protocols) i postępuj zgodnie z linkami, aby uzyskać więcej informacji.

> [!TIP]
> Każda aplikacja zarejestrowana w usłudze Azure AD może korzystać z platformy tożsamości firmy Microsoft, nawet jeśli nie logują się na kontach osobistych.  Dzięki temu można migrować istniejące aplikacje na platformę tożsamości firmy Microsoft i [MSAL](reference-v2-libraries.md) bez konieczności ponownego tworzenia aplikacji.

## <a name="tokens"></a>Tokeny

Uwierzytelnianie OAuth 2,0 i OpenID Connect umożliwiają użycie **tokenów okaziciela**, ogólnie przedstawionych jako [JWTs (tokeny sieci Web JSON)](https://tools.ietf.org/html/rfc7519). Token okaziciela jest lekkim tokenem zabezpieczającym, który przyznaje "okaziciela" dostęp do chronionego zasobu. W tym sensie "okaziciela" to każda osoba, która pobiera kopię tokenu. Chociaż strona musi najpierw przeprowadzić uwierzytelnienie na platformie tożsamości firmy Microsoft w celu uzyskania tokenu okaziciela, jeśli nie zostaną podjęte wymagane kroki w celu zabezpieczenia tokenu w ramach przesyłania i przechowywania, można je przechwycić i wykorzystać przez niezamierzoną stronę. Chociaż niektóre tokeny zabezpieczające mają wbudowany mechanizm uniemożliwiający korzystanie z nich przez nieuprawnionych stron, tokeny okaziciela nie mają tego mechanizmu i muszą być transportowane w bezpiecznym kanale, takim jak Transport Layer Security (HTTPS). Jeśli token okaziciela zostanie przesłany w sposób oczywisty, złośliwa strona może korzystać z ataku typu man-in-the-Middle w celu uzyskania tokenu i używania go w celu uzyskania nieautoryzowanego dostępu do chronionego zasobu. Te same zasady zabezpieczeń mają zastosowanie w przypadku przechowywania lub buforowania tokenów okaziciela na potrzeby późniejszego użycia. Zawsze upewnij się, że aplikacja przesyła tokeny okaziciela i przechowuje je w bezpieczny sposób. Aby uzyskać więcej informacji dotyczących zabezpieczeń dotyczących tokenów okaziciela, zobacz [RFC 6750, sekcja 5](https://tools.ietf.org/html/rfc6750).

W przypadku uwierzytelniania OAuth 2,0/OIDC istnieją głównie 3 typy tokenów:

* [Tokeny dostępu](access-tokens.md) — tokeny, które serwer zasobów odbiera od klienta, z uprawnieniami udzielonymi przez klienta.  
* [Tokeny identyfikatorów](id-tokens.md) — tokeny odbierane przez klienta z serwera autoryzacji, używane do logowania użytkownika i uzyskiwania podstawowych informacji o nich.
* Odśwież tokeny — używane przez klienta do uzyskiwania nowych tokenów dostępu i identyfikatorów w czasie.  Są to nieprzezroczyste ciągi i są zrozumiałe tylko dla serwera autoryzacji.

## <a name="protocols"></a>Protokoły

Jeśli chcesz zobaczyć przykładowe żądania, Rozpocznij pracę z jednym z poniższych dokumentów protokołu. Każdy z nich odpowiada konkretnemu scenariuszowi uwierzytelniania. Jeśli potrzebujesz pomocy przy ustaleniu, który z nich jest odpowiedni dla Ciebie, sprawdź [typy aplikacji, które można skompilować za pomocą platformy tożsamości firmy Microsoft](v2-app-types.md).

* [Tworzenie aplikacji mobilnych, natywnych i sieci Web przy użyciu protokołu OAuth 2,0](v2-oauth2-auth-code-flow.md)
* [Logowanie użytkowników przy użyciu programu OpenID Connect Connect](v2-protocols-oidc.md)
* [Tworzenie demonów lub procesów po stronie serwera za pomocą przepływu poświadczeń klienta OAuth 2,0](v2-oauth2-client-creds-grant-flow.md)
* [Pobieranie tokenów w interfejsie API sieci Web za pomocą przepływu OAuth 2,0 w imieniu](v2-oauth2-on-behalf-of-flow.md)
* [Twórz aplikacje jednostronicowe za pomocą niejawnego przepływu OAuth 2,0](v2-oauth2-implicit-grant-flow.md)
