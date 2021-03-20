---
title: Natywne aplikacje w Azure Active Directory
description: Opisuje aplikacje natywne i podstawowe informacje dotyczące przepływu protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
services: active-directory
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
ms.openlocfilehash: 9ecf711f5442b6f21de53d2735ad1c94d7cb6223
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154801"
---
# <a name="native-apps"></a>Aplikacje natywne

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Natywne aplikacje to aplikacje, które wywołują internetowy interfejs API w imieniu użytkownika. Ten scenariusz jest oparty na typie autoryzacji uwierzytelniania OAuth 2,0 z klientem publicznym, zgodnie z opisem w sekcji 4,1 [specyfikacji OAuth 2,0](https://tools.ietf.org/html/rfc6749). Aplikacja natywna uzyskuje token dostępu dla użytkownika przy użyciu protokołu OAuth 2,0. Ten token dostępu jest następnie wysyłany w żądaniu do internetowego interfejsu API, który autoryzuje użytkownika i zwraca żądany zasób.

## <a name="diagram"></a>Diagram

![Diagram aplikacji natywnych dla interfejsu API sieci Web](./media/authentication-scenarios/native-app-to-web-api.png)

## <a name="protocol-flow"></a>Przepływ protokołu

W przypadku korzystania z bibliotek uwierzytelniania usługi AD większość szczegółów protokołu opisanych poniżej jest obsługiwana przez Ciebie, takich jak wyskakujące okienko przeglądarki, buforowanie tokenów i obsługa tokenów odświeżania.

1. Za pomocą okna podręcznego przeglądarki aplikacja natywna wysyła żądanie do punktu końcowego autoryzacji w usłudze Azure AD. To żądanie zawiera identyfikator aplikacji i identyfikator URI przekierowania aplikacji natywnej, jak pokazano w Azure Portal oraz identyfikator URI identyfikatora aplikacji dla internetowego interfejsu API. Jeśli użytkownik nie jest jeszcze zalogowany, zostanie wyświetlony monit o ponowne zalogowanie
1. Usługa Azure AD uwierzytelnia użytkownika. Jeśli jest to aplikacja wielodostępna i zgoda jest wymagana do korzystania z aplikacji, użytkownik będzie musiał wyrazić zgodę, jeśli jeszcze nie zostało to zrobione. Po udzieleniu zgody i po pomyślnym uwierzytelnieniu usługa Azure AD wystawia odpowiedź na kod autoryzacji z powrotem na identyfikator URI przekierowania aplikacji klienta.
1. Gdy usługa Azure AD wystawia odpowiedź kodu autoryzacji z powrotem na identyfikator URI przekierowania, aplikacja kliencka przestanie komunikować się z przeglądarką i wyodrębni kod autoryzacji z odpowiedzi. Korzystając z tego kodu autoryzacji, aplikacja kliencka wysyła żądanie do punktu końcowego tokenu usługi Azure AD, który zawiera kod autoryzacji, szczegóły dotyczące aplikacji klienckiej (identyfikatora aplikacji i identyfikatora URI przekierowania) oraz żądany zasób (identyfikator URI identyfikatora aplikacji dla internetowego interfejsu API).
1. Kod autoryzacji i informacje o aplikacji klienckiej i interfejsie API sieci Web są weryfikowane przez usługę Azure AD. Po pomyślnej weryfikacji usługa Azure AD zwraca dwa tokeny: token dostępu JWT i token odświeżenia tokenu JWT. Ponadto usługa Azure AD zwraca podstawowe informacje o użytkowniku, takie jak nazwa wyświetlana i identyfikator dzierżawy.
1. Za pośrednictwem protokołu HTTPS aplikacja kliencka używa zwróconego tokenu dostępu JWT, aby dodać ciąg JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci Web sprawdza poprawność tokenu JWT i jeśli Walidacja zakończyła się pomyślnie, zwraca żądany zasób.
1. Gdy token dostępu zostanie wygaśnie, aplikacja kliencka wyświetli błąd, który wskazuje, że użytkownik musi ponownie przeprowadzić uwierzytelnienie. Jeśli aplikacja ma prawidłowy token odświeżania, można go użyć, aby uzyskać nowy token dostępu bez monitowania użytkownika o ponowne zalogowanie. Jeśli token odświeżania wygaśnie, aplikacja będzie musiała interaktywnie uwierzytelnić użytkownika.

> [!NOTE]
> Token odświeżania wystawiony przez usługę Azure AD może być używany do uzyskiwania dostępu do wielu zasobów. Na przykład jeśli masz aplikację kliencką, która ma uprawnienia do wywoływania dwóch internetowych interfejsów API, można użyć tokenu odświeżania, aby uzyskać token dostępu do innego internetowego interfejsu API.

## <a name="code-samples"></a>Przykłady kodu

Zapoznaj się z przykładami kodu dotyczącymi scenariuszy interfejsów API sieci Web aplikacji natywnych. I często sprawdzaj, czy nowe przykłady są często dodawane. [Natywna aplikacja do interfejsu API sieci Web](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api).

## <a name="app-registration"></a>Rejestrowanie aplikacji

Aby zarejestrować aplikację w punkcie końcowym usługi Azure AD v 1.0, zobacz [Rejestrowanie aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Pojedyncza dzierżawa — zarówno aplikacja natywna, jak i interfejs API sieci Web, muszą być zarejestrowane w tym samym katalogu w usłudze Azure AD. Internetowy interfejs API można skonfigurować tak, aby uwidaczniał zestaw uprawnień, który służy do ograniczania dostępu do zasobów aplikacji natywnych. Następnie aplikacja kliencka wybiera odpowiednie uprawnienia z menu rozwijanego "uprawnienia do innych aplikacji" w Azure Portal.
* Wiele dzierżawców — aplikacja natywna kiedykolwiek zarejestrowana tylko w katalogu dewelopera lub wydawcy. Następnie aplikacja natywna jest konfigurowana w celu wskazania uprawnień, które wymagają do działania. Ta lista wymaganych uprawnień jest wyświetlana w oknie dialogowym, gdy użytkownik lub administrator w katalogu docelowym wyrazi zgodę na aplikację, która staje się dostępna dla swojej organizacji. Niektóre aplikacje wymagają tylko uprawnień na poziomie użytkownika, do których można wyrazić zgodę. Inne aplikacje wymagają uprawnień na poziomie administratora, których użytkownik w organizacji nie może wyrazić zgody. Tylko administrator katalogu może wyrazić zgodę na aplikacje, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyraził zgodę, w swoim katalogu jest zarejestrowany tylko internetowy interfejs API. 

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Gdy aplikacja natywna używa swojego kodu autoryzacji w celu uzyskania tokenu dostępu JWT, odbiera również token odświeżenia tokenu JWT. Po wygaśnięciu tokenu dostępu można go użyć do ponownego uwierzytelnienia użytkownika bez konieczności ponownego logowania. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, co powoduje uzyskanie nowego tokenu dostępu i tokenu odświeżania.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o innych [typach aplikacji i scenariuszach](app-types.md)
- Poznaj podstawowe informacje na temat [uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
