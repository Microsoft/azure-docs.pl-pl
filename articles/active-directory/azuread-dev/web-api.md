---
title: Aplikacje internetowego interfejsu API w Azure Active Directory
description: Zawiera opis aplikacji internetowego interfejsu API i podstawowe informacje dotyczące przepływu protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 9cf5a9c81ca1d7a42a5a8e342dee55f335656c3e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154427"
---
# <a name="web-api"></a>Interfejs API sieci Web

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web API Apps to aplikacje sieci Web, które muszą pobierać zasoby z internetowego interfejsu API. W tym scenariuszu istnieją dwa typy tożsamości, których aplikacja sieci Web może używać do uwierzytelniania i wywoływania internetowego interfejsu API:

- **Tożsamość aplikacji** — w tym scenariuszu są stosowane poświadczenia klienta OAuth 2,0. Przyznaj uwierzytelnianie jako aplikację i dostęp do internetowego interfejsu API. W przypadku korzystania z tożsamości aplikacji interfejs API sieci Web może wykrywać, że aplikacja sieci Web wywołuje ją, ponieważ internetowy interfejs API nie otrzymuje żadnych informacji o użytkowniku. Jeśli aplikacja otrzymuje informacje o użytkowniku, zostanie ona wysłana za pośrednictwem protokołu aplikacji i nie jest podpisana przez usługę Azure AD. Internetowy interfejs API ufa, że aplikacja sieci Web uwierzytelnił użytkownika. Z tego powodu ten wzorzec jest nazywany zaufanym podsystemem.
- **Tożsamość delegowanego użytkownika** — ten scenariusz można wykonać na dwa sposoby: OpenID Connect Connect oraz kod autoryzacji OAuth 2,0 przyznaje klientowi poufnemu. Aplikacja sieci Web uzyskuje token dostępu dla użytkownika, który udowadnia internetowy interfejs API, który został pomyślnie uwierzytelniony przez użytkownika do aplikacji sieci Web i że aplikacja sieci Web mogła uzyskać delegowaną tożsamość użytkownika w celu wywołania interfejsu API sieci Web. Ten token dostępu jest wysyłany w żądaniu do internetowego interfejsu API, który autoryzuje użytkownika i zwraca żądany zasób.

Zarówno tożsamość aplikacji, jak i delegowane typy tożsamości użytkownika zostały omówione w poniższym przepływie. Kluczową różnicą jest to, że delegowana tożsamość użytkownika musi najpierw nabyć kod autoryzacji, zanim użytkownik będzie mógł zalogować się i uzyskać dostęp do internetowego interfejsu API.

## <a name="diagram"></a>Diagram

![Diagram aplikacji sieci Web z interfejsem API sieci Web](./media/authentication-scenarios/web-app-to-web-api.png)

## <a name="protocol-flow"></a>Przepływ protokołu

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Tożsamość aplikacji z uwierzytelnianiem przy użyciu poświadczeń klienta OAuth 2,0

1. Użytkownik jest zalogowany do usługi Azure AD w aplikacji sieci Web (zobacz sekcję **Web Apps** , aby uzyskać więcej informacji).
1. Aplikacja sieci Web musi uzyskać token dostępu, aby można było uwierzytelnić się w interfejsie API sieci Web i pobrać żądany zasób. Wysyła żądanie do punktu końcowego tokenu usługi Azure AD, dostarczając poświadczenia, identyfikator aplikacji i identyfikator URI aplikacji interfejsu API sieci Web.
1. Usługa Azure AD uwierzytelnia aplikację i zwraca token dostępu JWT używany do wywoływania interfejsu API sieci Web.
1. Za pośrednictwem protokołu HTTPS aplikacja sieci Web używa zwróconego tokenu dostępu JWT, aby dodać ciąg JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci Web sprawdza poprawność tokenu JWT i jeśli Walidacja zakończyła się pomyślnie, zwraca żądany zasób.

### <a name="delegated-user-identity-with-openid-connect"></a>Tożsamość delegowanego użytkownika z OpenID Connect Connect

1. Użytkownik jest zalogowany do aplikacji sieci Web przy użyciu usługi Azure AD (zobacz sekcję przeglądarki sieci Web w aplikacji sieci Web powyżej). Jeśli użytkownik aplikacji sieci Web nie został jeszcze przesłany, aby umożliwić aplikacji sieci Web wywoływanie internetowego interfejsu API w jego imieniu, użytkownik będzie musiał wyrazić zgodę. Aplikacja wyświetli wymagane uprawnienia i jeśli którykolwiek z nich ma uprawnienia na poziomie administratora, zwykły użytkownik w katalogu nie będzie mógł wyrazić zgody. Ten proces wyrażania zgody dotyczy tylko aplikacji wielodostępnych, a nie pojedynczych aplikacji dzierżawców, ponieważ aplikacja ma już wymagane uprawnienia. Po zalogowaniu się użytkownika aplikacja sieci Web otrzymała token identyfikatora z informacjami o użytkowniku, a także kodem autoryzacji.
1. Przy użyciu kodu autoryzacji wydanego przez usługę Azure AD aplikacja sieci Web wysyła żądanie do punktu końcowego tokenu usługi Azure AD, który zawiera kod autoryzacji, szczegóły dotyczące aplikacji klienckiej (identyfikatora aplikacji i identyfikatora URI przekierowania) oraz żądany zasób (identyfikator URI identyfikatora aplikacji dla internetowego interfejsu API).
1. Kod autoryzacji i informacje o aplikacji sieci Web i interfejsie API sieci Web są sprawdzane przez usługę Azure AD. Po pomyślnej weryfikacji usługa Azure AD zwraca dwa tokeny: token dostępu JWT i token odświeżenia tokenu JWT.
1. Za pośrednictwem protokołu HTTPS aplikacja sieci Web używa zwróconego tokenu dostępu JWT, aby dodać ciąg JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci Web sprawdza poprawność tokenu JWT i jeśli Walidacja zakończyła się pomyślnie, zwraca żądany zasób.

### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Tożsamość delegowanego użytkownika z przyznaniem kodu autoryzacji OAuth 2,0

1. Użytkownik jest już zalogowany do aplikacji sieci Web, której mechanizm uwierzytelniania jest niezależny od usługi Azure AD.
1. Aplikacja sieci Web wymaga kodu autoryzacji w celu uzyskania tokenu dostępu, więc wysyła żądanie przez przeglądarkę do punktu końcowego autoryzacji usługi Azure AD, podając identyfikator aplikacji i identyfikator URI przekierowania dla aplikacji sieci Web po pomyślnym uwierzytelnieniu. Użytkownik loguje się do usługi Azure AD.
1. Jeśli użytkownik aplikacji sieci Web nie został jeszcze przesłany, aby umożliwić aplikacji sieci Web wywoływanie internetowego interfejsu API w jego imieniu, użytkownik będzie musiał wyrazić zgodę. Aplikacja wyświetli wymagane uprawnienia i jeśli którykolwiek z nich ma uprawnienia na poziomie administratora, zwykły użytkownik w katalogu nie będzie mógł wyrazić zgody. Ta zgoda dotyczy zarówno aplikacji o pojedynczej, jak i wielu dzierżawców. W przypadku pojedynczej dzierżawy administrator może wykonać zgodę administratora na zgodę w imieniu użytkowników. Można to zrobić za pomocą `Grant Permissions` przycisku w [Azure Portal](https://portal.azure.com). 
1. Gdy użytkownik wyraził zgodę, aplikacja sieci Web otrzymuje kod autoryzacji potrzebny do uzyskania tokenu dostępu.
1. Przy użyciu kodu autoryzacji wydanego przez usługę Azure AD aplikacja sieci Web wysyła żądanie do punktu końcowego tokenu usługi Azure AD, który zawiera kod autoryzacji, szczegóły dotyczące aplikacji klienckiej (identyfikatora aplikacji i identyfikatora URI przekierowania) oraz żądany zasób (identyfikator URI identyfikatora aplikacji dla internetowego interfejsu API).
1. Kod autoryzacji i informacje o aplikacji sieci Web i interfejsie API sieci Web są sprawdzane przez usługę Azure AD. Po pomyślnej weryfikacji usługa Azure AD zwraca dwa tokeny: token dostępu JWT i token odświeżenia tokenu JWT.
1. Za pośrednictwem protokołu HTTPS aplikacja sieci Web używa zwróconego tokenu dostępu JWT, aby dodać ciąg JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci Web sprawdza poprawność tokenu JWT i jeśli Walidacja zakończyła się pomyślnie, zwraca żądany zasób.

## <a name="code-samples"></a>Przykłady kodu

Zapoznaj się z przykładami kodu dotyczącymi scenariuszy usługi Web API dla aplikacji sieci Web. I często sprawdzaj, czy nowe przykłady są często dodawane. Aplikacja sieci Web [do interfejsu API sieci Web](sample-v1-code.md#web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity).

## <a name="app-registration"></a>Rejestrowanie aplikacji

Aby zarejestrować aplikację w punkcie końcowym usługi Azure AD v 1.0, zobacz [Rejestrowanie aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Pojedynczy dzierżawca — zarówno w przypadku tożsamości aplikacji, jak i delegowanych przypadków tożsamości użytkowników aplikacja sieci Web i internetowy interfejs API muszą być zarejestrowane w tym samym katalogu w usłudze Azure AD. Internetowy interfejs API można skonfigurować tak, aby uwidaczniał zestaw uprawnień, który służy do ograniczania dostępu aplikacji sieci Web do zasobów. Jeśli jest używany typ tożsamości delegowanego użytkownika, aplikacja sieci Web musi wybrać odpowiednie uprawnienia z menu rozwijanego **uprawnienia do innych aplikacji** w Azure Portal. Ten krok nie jest wymagany, jeśli jest używany typ tożsamości aplikacji.
* Wiele dzierżawców — aplikacja sieci Web jest skonfigurowana tak, aby wskazywała uprawnienia wymagane do działania. Ta lista wymaganych uprawnień jest wyświetlana w oknie dialogowym, gdy użytkownik lub administrator w katalogu docelowym wyrazi zgodę na aplikację, która staje się dostępna dla swojej organizacji. Niektóre aplikacje wymagają tylko uprawnień na poziomie użytkownika, do których można wyrazić zgodę. Inne aplikacje wymagają uprawnień na poziomie administratora, których użytkownik w organizacji nie może wyrazić zgody. Tylko administrator katalogu może wyrazić zgodę na aplikacje, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyraził zgodę, aplikacja sieci Web i internetowy interfejs API są rejestrowane w ich katalogu.

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Gdy aplikacja sieci Web używa swojego kodu autoryzacji w celu uzyskania tokenu dostępu JWT, odbiera również token odświeżenia tokenu JWT. Po wygaśnięciu tokenu dostępu można użyć tokenu odświeżania, aby ponownie uwierzytelnić użytkownika bez konieczności ponownego logowania. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, co powoduje uzyskanie nowego tokenu dostępu i tokenu odświeżania.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o innych [typach aplikacji i scenariuszach](app-types.md)
- Poznaj podstawowe informacje na temat [uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
