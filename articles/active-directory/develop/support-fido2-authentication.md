---
title: Obsługa uwierzytelniania bezhaseł przy użyciu kluczy FIDO2 w aplikacjach tworzonych przez Ciebie | Azure
titleSuffix: Microsoft identity platform
description: W tym przewodniku wdrażania wyjaśniono, jak obsługiwać uwierzytelnianie bezhasłem przy użyciu kluczy zabezpieczeń FIDO2 w aplikacjach, które tworzysz
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 1/29/2021
ms.author: nichola
ms.custom: aaddev
ms.openlocfilehash: 5abece0e272d4b72ba6f787ad44b091df5d45226
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417543"
---
# <a name="support-passwordless-authentication-with-fido2-keys-in-apps-you-develop"></a>Obsługa uwierzytelniania bezhaseł przy użyciu kluczy FIDO2 w tworzonych aplikacjach

Te konfiguracje i najlepsze rozwiązania ułatwią uniknięcie typowych scenariuszy, które blokują [FIDO2 uwierzytelnianie bez hasła](../../active-directory/authentication/concept-authentication-passwordless.md) , które są dostępne dla użytkowników aplikacji.

## <a name="general-best-practices"></a>Ogólne najlepsze praktyki

### <a name="domain-hints"></a>Wskazówki dotyczące domeny

Nie należy używać wskazówki domeny w celu obejścia [odnajdywania obszaru macierzystego](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md). Ta funkcja jest przeznaczona do usprawnienia logowania, ale dostawca tożsamości federacyjnych może nie obsługiwać uwierzytelniania bezhaseł.

### <a name="requiring-specific-credentials"></a>Wymaganie określonych poświadczeń

Jeśli używasz protokołu SAML, nie określaj, że hasło jest wymagane [przy użyciu elementu RequestedAuthnContext](single-sign-on-saml-protocol.md#requestauthncontext).

Element RequestedAuthnContext jest opcjonalny, więc aby rozwiązać ten problem, można usunąć go z żądań uwierzytelniania języka SAML. Jest to najlepsze rozwiązanie, ponieważ użycie tego elementu może również uniemożliwić poprawne działanie innych opcji uwierzytelniania, takich jak uwierzytelnianie wieloskładnikowe.

### <a name="using-the-most-recently-used-authentication-method"></a>Korzystanie z ostatnio używanej metody uwierzytelniania

Metoda logowania, która była ostatnio używana przez użytkownika, zostanie wyświetlona jako pierwsza. Może to spowodować pomyłkę, gdy użytkownicy uważają, że muszą użyć pierwszej opcji. Można jednak wybrać inną opcję, wybierając pozycję "inne sposoby logowania", jak pokazano poniżej.

:::image type="content" source="./media/support-fido2-authentication/most-recently-used-method.png" alt-text="Obraz środowiska uwierzytelniania użytkownika z wyróżnionym przyciskiem umożliwiającym użytkownikowi zmianę metody uwierzytelniania.":::

## <a name="platform-specific-best-practices"></a>Najlepsze rozwiązania dotyczące platformy

### <a name="desktop"></a>Klasyczna

Zalecane opcje implementowania uwierzytelniania są następujące:

- Aplikacje klasyczne platformy .NET używające biblioteki Microsoft Authentication Library (MSAL) powinny używać Menedżera uwierzytelniania systemu Windows (WAM). Ta integracja i jej korzyści są [udokumentowane w witrynie GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/wam).
- Użyj [WebView2](https://docs.microsoft.com/microsoft-edge/webview2/) do obsługi FIDO2 w osadzonej przeglądarce.
- Użyj przeglądarki systemowej. Biblioteki MSAL dla platform klasycznych używają tej metody domyślnie. Aby upewnić się, że używana przeglądarka obsługuje uwierzytelnianie FIDO2, możesz zapoznać się z naszą stroną w witrynie FIDO2.

### <a name="mobile"></a>Aplikacje mobilne

Od lutego 2020 FIDO2 nie jest obecnie obsługiwana dla natywnych aplikacji dla systemu iOS lub Android, ale jest w trakcie opracowywania.

Aby przygotować aplikacje pod kątem ich dostępności, a najlepszym rozwiązaniem w przypadku aplikacji dla systemów iOS i Android należy używać MSAL z domyślną konfiguracją programu przy użyciu przeglądarki sieci Web systemu.

Jeśli nie korzystasz z programu MSAL, w celu uwierzytelnienia nadal powinno być używane systemowa przeglądarka sieci Web. Funkcje takie jak logowanie jednokrotne i dostęp warunkowy polegają na udostępnionej powierzchni sieci Web udostępnianej przez systemową przeglądarkę sieci Web. Oznacza to użycie [niestandardowych kart programu Chrome](https://developer.chrome.com/docs/multidevice/android/customtabs/) (Android) lub [uwierzytelnienia użytkownika za pośrednictwem usługi sieci Web | Dokumentacja dla deweloperów firmy Apple](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service) (iOS).

### <a name="web-and-single-page-apps"></a>Aplikacje sieci Web i jednostronicowe

Dostępność FIDO2 uwierzytelniania bezhasła dla aplikacji uruchamianych w przeglądarce sieci Web będzie zależeć od kombinacji przeglądarki i platformy. Możesz zapoznać się z naszą [matrycą zgodności FIDO2](../authentication/fido2-compatibility.md) , aby sprawdzić, czy jest obsługiwana kombinacja Twoich użytkowników.

## <a name="next-steps"></a>Następne kroki

[Opcje uwierzytelniania bezhasło dla Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)
