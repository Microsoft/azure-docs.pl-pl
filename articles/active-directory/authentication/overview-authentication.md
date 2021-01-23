---
title: Omówienie uwierzytelniania Azure Active Directory
description: Zapoznaj się z różnymi metodami uwierzytelniania i funkcjami zabezpieczeń dla logowania użytkowników przy użyciu Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6f4659b9ee809cc1f1caeb1cb9c0d626b1b3a3b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725658"
---
# <a name="what-is-azure-active-directory-authentication"></a>Co to jest Azure Active Directory Authentication?

Jedną z głównych funkcji platformy tożsamości jest weryfikowanie i *uwierzytelnianie* poświadczeń, gdy użytkownik loguje się do urządzenia, aplikacji lub usługi. W Azure Active Directory (Azure AD) uwierzytelnianie obejmuje więcej niż tylko weryfikację nazwy użytkownika i hasła. Aby zwiększyć bezpieczeństwo i ograniczyć potrzebę pomocy technicznej, uwierzytelnianie w usłudze Azure AD obejmuje następujące składniki:

* Samoobsługowe resetowanie haseł
* Uwierzytelnianie wieloskładnikowe w usłudze Azure AD
* Integracja hybrydowa do zapisywania zmian haseł z powrotem w środowisku lokalnym
* Integracja hybrydowa do wymuszania zasad ochrony hasłem dla środowiska lokalnego
* Uwierzytelnianie bez hasła

Zapoznaj się z naszym krótkim wideo, aby dowiedzieć się więcej o tych składnikach uwierzytelniania.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4KVJA]

## <a name="improve-the-end-user-experience"></a>Ulepszanie środowiska użytkownika końcowego

Usługa Azure AD pomaga chronić tożsamość użytkownika i uprościć ich środowisko logowania. Funkcje, takie jak Samoobsługowe resetowanie hasła, pozwalają użytkownikom aktualizować lub zmieniać hasła przy użyciu przeglądarki sieci Web z dowolnego urządzenia. Ta funkcja jest szczególnie przydatna, gdy użytkownik zapomniał hasła lub konto jest zablokowane. Bez czekania na pomoc techniczną lub administratora, użytkownik może odblokować siebie i kontynuować pracę.

Usługa Azure AD Multi-Factor Authentication umożliwia użytkownikom wybranie dodatkowej formy uwierzytelniania podczas logowania, na przykład połączenia telefonicznego lub powiadomienia aplikacji mobilnej. Pozwala to ograniczyć wymagania dotyczące pojedynczej, ustalonej postaci uwierzytelniania pomocniczego, takiego jak token sprzętowy. Jeśli użytkownik nie ma obecnie jednej postaci dodatkowego uwierzytelniania, może wybrać inną metodę i kontynuować pracę.

![Metody uwierzytelniania używane na ekranie logowania](media/concept-authentication-methods/overview-login.png)

Uwierzytelnianie bez hasła eliminuje konieczność utworzenia i zapamiętania bezpiecznego hasła przez użytkownika. Funkcje, takie jak Windows Hello dla firm lub FIDO2, umożliwiają użytkownikom logowanie się do urządzenia lub aplikacji bez hasła. Ta możliwość zmniejsza złożoność zarządzania hasłami w różnych środowiskach.

## <a name="self-service-password-reset"></a>Samoobsługowe resetowanie haseł

Funkcja samoobsługowego resetowania hasła umożliwia użytkownikom zmianę lub Resetowanie hasła bez konieczności korzystania z administratora lub pomocy technicznej. Jeśli konto użytkownika jest zablokowane lub zapomni swoje hasło, może wykonać monit o odblokowanie siebie i zawracanie do pracy. Ta możliwość zmniejsza liczbę wywołań pomocy technicznej i utratę produktywności, gdy użytkownik nie może zalogować się na urządzeniu ani w aplikacji.

Funkcja samoobsługowego resetowania hasła działa w następujących scenariuszach:

* **Zmiana hasła —** gdy użytkownik zna swoje hasło, ale chce zmienić je na nowe.
* **Resetowanie hasła —** gdy użytkownik nie może się zalogować, na przykład gdy nie pamięta hasła i chcesz zresetować swoje hasło.
* **Odblokowywanie konta —** gdy użytkownik nie może się zalogować, ponieważ konto jest zablokowane i chcesz odblokować swoje konto.

Gdy użytkownik zaktualizuje lub zresetuje hasło przy użyciu funkcji samoobsługowego resetowania hasła, to hasło można także zapisać z powrotem do lokalnego środowiska Active Directoryowego. Funkcja zapisywania zwrotnego haseł gwarantuje, że użytkownik może natychmiast używać zaktualizowanych poświadczeń z lokalnymi urządzeniami i aplikacjami.

## <a name="azure-ad-multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe w usłudze Azure AD

Uwierzytelnianie wieloskładnikowe to proces, w którym podczas procesu logowania użytkownik otrzymuje monit mający na celu uzyskanie dodatkowej formy identyfikacji, na przykład wprowadzenie kodu w telefonie komórkowym lub zeskanowanie odcisku palca.

Jeśli używasz tylko hasła do uwierzytelniania użytkownika, opuszcza on niezabezpieczony wektor na potrzeby ataku. Jeśli hasło jest słabe lub zostało ujawnione w innym miejscu, czy użytkownik loguje się przy użyciu nazwy użytkownika i hasła, czy też jest osoba atakująca? Gdy wymagana jest druga forma uwierzytelniania, zabezpieczenia są zwiększane, ponieważ ten dodatkowy czynnik nie jest czymś, co jest łatwe do uzyskania lub zduplikowania przez osobę atakującą.

![Obraz przedstawiający koncepcję różnych form uwierzytelniania wieloskładnikowego](./media/concept-mfa-howitworks/methods.png)

Usługa Azure AD Multi-Factor Authentication działa, wymagając co najmniej dwóch następujących metod uwierzytelniania:

* Coś, co wiesz, zazwyczaj hasło.
* Coś, na przykład zaufanego urządzenia, które nie jest łatwo duplikowane, takie jak telefon lub klucz sprzętowy.
* Coś, co jest bardzo metryczne, takie jak skanowanie odcisku palca lub z przodu.

Użytkownicy mogą zarejestrować się w ramach samoobsługowego resetowania haseł i usługi Azure AD Multi-Factor Authentication w jednym kroku, aby uprościć środowisko pracy. Administratorzy mogą definiować, które formy uwierzytelniania pomocniczego mogą być używane. Usługi Azure AD Multi-Factor Authentication mogą być również wymagane, gdy użytkownicy wykonują funkcję samoobsługowego resetowania hasła w celu dodatkowego zabezpieczenia tego procesu.

## <a name="password-protection"></a>Ochrona hasłem

Domyślnie usługa Azure AD blokuje słabe hasła, takie jak *Password1*. Globalna lista wykluczonych haseł jest automatycznie aktualizowana i wymuszana, która zawiera znane hasła słabe. Jeśli użytkownik usługi Azure AD podejmie próbę ustawienia hasła do jednego z tych słabych haseł, otrzyma powiadomienie, aby wybrać bezpieczniejsze hasło.

Aby zwiększyć bezpieczeństwo, można zdefiniować niestandardowe zasady ochrony haseł. Te zasady mogą używać filtrów do blokowania każdej zmiany hasła zawierającej nazwę, np. *contoso* lub lokalizację, np. *Londyn*.

W przypadku zabezpieczeń hybrydowych można zintegrować ochronę hasłem usługi Azure AD za pomocą środowiska lokalnego Active Directory. Składnik zainstalowany w środowisku Premium otrzymuje globalną listę haseł zabronionych i niestandardowe zasady ochrony haseł z usługi Azure AD, a kontrolery domeny używają ich do przetwarzania zdarzeń zmiany hasła. Takie podejście hybrydowe gwarantuje, że niezależnie od tego, jak i kiedy użytkownik zmienia swoje poświadczenia, wymusza się użycie silnych haseł.

## <a name="passwordless-authentication"></a>Uwierzytelnianie bez hasła

Celem końcowym wielu środowisk jest usunięcie używania haseł w ramach zdarzeń związanych z logowaniem. Funkcje, takie jak ochrona hasłem platformy Azure lub usługa Azure AD Multi-Factor Authentication, ułatwiają lepsze zabezpieczenia, ale nazwa użytkownika i hasło pozostają słabo zgodne z uwierzytelnianiem, które mogą być narażone na ataki lub wymuszanie.

![Bezpieczeństwo i wygoda dzięki procesowi uwierzytelniania, który prowadzi do bezhaseł](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Po zalogowaniu się przy użyciu metody bez hasła poświadczenia są udostępniane przy użyciu metod takich jak biometria z usługą Windows Hello dla firm lub klucza zabezpieczeń FIDO2. Te metody uwierzytelniania nie mogą być w łatwy sposób duplikowane przez osobę atakującą.

Usługa Azure AD zapewnia sposoby natywnego uwierzytelniania przy użyciu metod bezopartych na hasłach, aby uprościć środowisko logowania dla użytkowników i zmniejszyć ryzyko ataków.

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz Samouczek dotyczący samoobsługowego [resetowania hasła (SSPR)][tutorial-sspr] i [usługi Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Aby dowiedzieć się więcej na temat koncepcji samoobsługowego resetowania haseł, zobacz Jak działa funkcja samoobsługowego [resetowania hasła w usłudze Azure AD][concept-sspr].

Aby dowiedzieć się więcej o pojęciach dotyczących uwierzytelniania wieloskładnikowego, zobacz [jak działa usługa Azure AD Multi-Factor Authentication][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
