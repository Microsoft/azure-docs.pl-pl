---
title: Aplikacje usługi Service to Service w Azure Active Directory
description: Opisuje aplikacje usług i usługi, a także podstawowe informacje dotyczące przepływu protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 179034533d90dbbb6ca362fc6f72996f32873729
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154767"
---
# <a name="service-to-service-apps"></a>Aplikacje usługi Service to Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplikacje typu "Usługa-Usługa" to demon lub aplikacja serwera, która musi pobrać zasoby z internetowego interfejsu API. Istnieją dwa scenariusze podrzędne, które mają zastosowanie do tej sekcji:

- Demon wymagający wywołania interfejsu API sieci Web, który jest oparty na uwierzytelnianiu poświadczeń klienta OAuth 2,0

    W tym scenariuszu ważne jest, aby zrozumieć kilka rzeczy. W pierwszej kolejności interakcja użytkownika nie jest możliwa w przypadku aplikacji demona, która wymaga, aby aplikacja miała własną tożsamość. Przykładem aplikacji demona jest zadanie wsadowe lub usługa systemu operacyjnego działająca w tle. Ten typ aplikacji żąda tokenu dostępu przy użyciu tożsamości aplikacji i prezentuje identyfikator aplikacji, poświadczenia (hasło lub certyfikat) i identyfikator URI aplikacji do usługi Azure AD. Po pomyślnym uwierzytelnieniu demon otrzymuje token dostępu z usługi Azure AD, który jest następnie używany do wywoływania internetowego interfejsu API.

- Aplikacja serwera (na przykład internetowy interfejs API), która musi wywołać interfejs API sieci Web, oparty na specyfikacji OAuth 2,0 w imieniu użytkownika

    W tym scenariuszu Załóżmy, że użytkownik został uwierzytelniony w aplikacji natywnej, a ta aplikacja natywna musi wywołać internetowy interfejs API. Usługa Azure AD wystawia token dostępu JWT, aby wywołać internetowy interfejs API. Jeśli internetowy interfejs API musi wywołać inny podrzędny interfejs API sieci Web, może użyć przepływu w imieniu w celu delegowania tożsamości użytkownika i uwierzytelnienia w sieci Web API drugiej warstwy.

## <a name="diagram"></a>Diagram

![Diagram interfejsu API sieci Web aplikacji demona lub serwer](./media/authentication-scenarios/daemon-server-app-to-web-api.png)

## <a name="protocol-flow"></a>Przepływ protokołu

### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Tożsamość aplikacji z uwierzytelnianiem przy użyciu poświadczeń klienta OAuth 2,0

1. Najpierw aplikacja serwera musi uwierzytelniać się za pomocą usługi Azure AD, bez żadnej interakcji człowieka, takiej jak interakcyjne okno dialogowe logowania. Wysyła żądanie do punktu końcowego tokenu usługi Azure AD, podając poświadczenia, identyfikator aplikacji i identyfikator URI aplikacji.
1. Usługa Azure AD uwierzytelnia aplikację i zwraca token dostępu JWT używany do wywoływania interfejsu API sieci Web.
1. Za pośrednictwem protokołu HTTPS aplikacja sieci Web używa zwróconego tokenu dostępu JWT, aby dodać ciąg JWT z oznaczeniem "Bearer" w nagłówku autoryzacji żądania do internetowego interfejsu API. Interfejs API sieci Web sprawdza poprawność tokenu JWT i jeśli Walidacja zakończyła się pomyślnie, zwraca żądany zasób.

### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Tożsamość delegowanego użytkownika ze specyfikacją OAuth 2,0 w imieniu

W ramach przepływu omówionego poniżej przyjęto założenie, że użytkownik został uwierzytelniony w innej aplikacji (na przykład aplikacja natywna), a ich tożsamość użytkownika została użyta w celu uzyskania tokenu dostępu do pierwszego poziomu interfejsu API sieci Web.

1. Aplikacja natywna wysyła token dostępu do interfejsu API sieci Web pierwszego rzędu.
1. Interfejs API sieci Web pierwszej warstwy wysyła żądanie do punktu końcowego tokenu usługi Azure AD, podając identyfikator aplikacji i poświadczenia, a także token dostępu użytkownika. Ponadto żądanie jest wysyłane z parametrem on_behalf_of, który wskazuje, że internetowy interfejs API żąda nowych tokenów w celu wywołania podrzędnego internetowego interfejsu API w imieniu oryginalnego użytkownika.
1. Usługa Azure AD weryfikuje, czy pierwszy warstwowy interfejs API sieci Web ma uprawnienia dostępu do wielowarstwowego interfejsu API sieci Web i weryfikuje żądanie, zwracając token dostępu JWT i token odświeżenia tokenu JWT do pierwszego warstwy interfejsu API sieci Web.
1. Za pośrednictwem protokołu HTTPS interfejs API sieci Web pierwszego rzędu wywołuje interfejs API sieci Web drugiej warstwy, dołączając ciąg tokenu w nagłówku autoryzacji w żądaniu. Interfejs API sieci Web pierwszego rzędu może kontynuować wywoływanie wielowarstwowego interfejsu API sieci Web, o ile token dostępu i tokeny odświeżania są prawidłowe.

## <a name="code-samples"></a>Przykłady kodu

Zapoznaj się z przykładami kodu dotyczącymi scenariuszy dla interfejsu API sieci Web lub aplikacji serwerowych: [serwer lub demon do interfejsu API sieci Web](sample-v1-code.md#daemon-applications-accessing-web-apis-with-the-applications-identity)

## <a name="app-registration"></a>Rejestrowanie aplikacji

* Pojedynczy dzierżawca — zarówno w przypadku tożsamości aplikacji, jak i delegowanych przypadków tożsamości użytkowników, Demon lub aplikacja serwera musi być zarejestrowana w tym samym katalogu w usłudze Azure AD. Internetowy interfejs API można skonfigurować tak, aby uwidaczniał zestaw uprawnień, który służy do ograniczania dostępu do zasobów przez demon lub serwer. Jeśli jest używany typ tożsamości delegowanego użytkownika, aplikacja serwera musi wybrać odpowiednie uprawnienia. Na stronie **uprawnienia interfejsu API** do rejestracji aplikacji po wybraniu opcji **Dodaj uprawnienie** i wybraniu rodziny interfejsów API wybierz pozycję **uprawnienia delegowane**, a następnie wybierz swoje uprawnienia. Ten krok nie jest wymagany, jeśli jest używany typ tożsamości aplikacji.
* Dla wielu dzierżawców — aplikacja demona lub serwer jest skonfigurowana tak, aby wskazywała uprawnienia wymagane do działania. Ta lista wymaganych uprawnień jest wyświetlana w oknie dialogowym, gdy użytkownik lub administrator w katalogu docelowym wyrazi zgodę na aplikację, która staje się dostępna dla swojej organizacji. Niektóre aplikacje wymagają tylko uprawnień na poziomie użytkownika, do których można wyrazić zgodę. Inne aplikacje wymagają uprawnień na poziomie administratora, których użytkownik w organizacji nie może wyrazić zgody. Tylko administrator katalogu może wyrazić zgodę na aplikacje, które wymagają tego poziomu uprawnień. Gdy użytkownik lub administrator wyraził zgodę, oba interfejsy API sieci Web są rejestrowane w ich katalogu.

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Gdy pierwsza aplikacja używa swojego kodu autoryzacji w celu uzyskania tokenu dostępu JWT, odbiera również token odświeżenia tokenu JWT. Po wygaśnięciu tokenu dostępu można go użyć do ponownego uwierzytelnienia użytkownika bez monitowania o poświadczenia. Ten token odświeżania jest następnie używany do uwierzytelniania użytkownika, co powoduje uzyskanie nowego tokenu dostępu i tokenu odświeżania.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o innych [typach aplikacji i scenariuszach](app-types.md)
- Poznaj podstawowe informacje na temat [uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
