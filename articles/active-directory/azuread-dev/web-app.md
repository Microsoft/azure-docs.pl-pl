---
title: Aplikacje sieci Web w Azure Active Directory
description: Zawiera opis aplikacji sieci Web oraz podstawowe informacje dotyczące przepływu protokołu, rejestracji i wygaśnięcia tokenu dla tego typu aplikacji.
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
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80154410"
---
# <a name="web-apps"></a>Aplikacje internetowe

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Aplikacje sieci Web to aplikacje, które uwierzytelniają użytkownika w przeglądarce internetowej w aplikacji sieci Web. W tym scenariuszu aplikacja sieci Web kieruje przeglądarkę użytkownika do podpisania ich w usłudze Azure AD. Usługa Azure AD zwraca odpowiedź logowania za pomocą przeglądarki użytkownika, która zawiera oświadczenia dotyczące użytkownika w tokenie zabezpieczającym. Ten scenariusz obsługuje logowanie przy użyciu protokołów OpenID Connect Connect, SAML 2,0 i WS-Federation.

## <a name="diagram"></a>Diagram

![Przepływ uwierzytelniania dla aplikacji przeglądarki sieci Web](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Przepływ protokołu

1. Gdy użytkownik odwiedza aplikację i musi się zalogować, zostaje przekierowany przez żądanie logowania do punktu końcowego uwierzytelniania w usłudze Azure AD.
1. Użytkownik loguje się na stronie logowania.
1. W przypadku pomyślnego uwierzytelnienia usługa Azure AD utworzy token uwierzytelniania i zwróci odpowiedź logowania do adresu URL odpowiedzi aplikacji, który został skonfigurowany w Azure Portal. W przypadku aplikacji produkcyjnej ten adres URL odpowiedzi powinien mieć wartość HTTPS. Zwrócony token zawiera oświadczenia dotyczące użytkownika i usługi Azure AD, które są wymagane przez aplikację do weryfikacji tokenu.
1. Aplikacja sprawdza poprawność tokenu przy użyciu publicznego klucza podpisywania i informacji o wystawce dostępnych w dokumencie metadanych Federacji dla usługi Azure AD. Gdy aplikacja zweryfikuje token, rozpocznie nową sesję z użytkownikiem. Ta sesja umożliwia użytkownikowi uzyskanie dostępu do aplikacji do momentu jej wygaśnięcia.

## <a name="code-samples"></a>Przykłady kodu

Zapoznaj się z przykładami kodu dotyczącymi scenariuszy aplikacji sieci Web w przeglądarce internetowej. I sprawdź często, jak często dodawane są nowe przykłady.

## <a name="app-registration"></a>Rejestracja aplikacji

Aby zarejestrować aplikację sieci Web, zobacz [Rejestrowanie aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

* Pojedyncze dzierżawy — Jeśli tworzysz aplikację tylko dla organizacji, musi ona być zarejestrowana w katalogu firmy przy użyciu Azure Portal.
* Wiele dzierżawców — w przypadku kompilowania aplikacji, która może być używana przez użytkowników spoza organizacji, musi być ona zarejestrowana w katalogu firmy, ale również musi być zarejestrowana w katalogu każdej organizacji, który będzie używać aplikacji. Aby udostępnić aplikację w swoim katalogu, możesz dołączyć proces rejestracji dla klientów, który umożliwia im zgodę na korzystanie z aplikacji. Po zarejestrowaniu się w aplikacji zostanie wyświetlone okno dialogowe, w którym są wyświetlane uprawnienia wymagane przez aplikację, a następnie opcja wyrażania zgody. W zależności od wymaganych uprawnień administrator w innej organizacji może być zobowiązany do udzielenia zgody. Gdy użytkownik lub administrator wyraził zgodę, aplikacja zostanie zarejestrowana w swoim katalogu.

## <a name="token-expiration"></a>Wygaśnięcie tokenu

Sesja użytkownika wygasa, gdy okres istnienia tokenu wystawionego przez usługę Azure AD wygaśnie. W razie potrzeby aplikacja może skrócić ten okres, na przykład wylogować użytkowników na podstawie okresu nieaktywności. Po wygaśnięciu sesji użytkownik zostanie poproszony o ponowne zalogowanie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [typach aplikacji i scenariuszach](app-types.md)
* Poznaj podstawowe informacje na temat [uwierzytelniania](v1-authentication-scenarios.md) usługi Azure AD
