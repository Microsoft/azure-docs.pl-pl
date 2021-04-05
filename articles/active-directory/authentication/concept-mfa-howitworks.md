---
title: Omówienie usługi Azure AD Multi-Factor Authentication
description: Dowiedz się, jak usługa Azure AD Multi-Factor Authentication pomaga chronić dostęp do danych i aplikacji, a jednocześnie spełnia wymagania użytkowników dotyczące prostego procesu logowania.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cc5227cb4b5de02cba65a60dd469da93d3767e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744061"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>Jak to działa: usługa Azure AD Multi-Factor Authentication

Uwierzytelnianie wieloskładnikowe to proces, w którym podczas procesu logowania użytkownik otrzymuje monit mający na celu uzyskanie dodatkowej formy identyfikacji, na przykład wprowadzenie kodu w telefonie komórkowym lub zeskanowanie odcisku palca.

Jeśli używasz tylko hasła do uwierzytelniania użytkownika, opuszcza on niezabezpieczony wektor na potrzeby ataku. Jeśli hasło jest słabe lub zostało ujawnione w innym miejscu, czy użytkownik loguje się przy użyciu nazwy użytkownika i hasła, czy też jest osoba atakująca? Gdy wymagana jest druga forma uwierzytelniania, zabezpieczenia są zwiększane, ponieważ ten dodatkowy czynnik nie jest czymś, co jest łatwe do uzyskania lub zduplikowania przez osobę atakującą.

![Obraz przedstawiający koncepcję różnych form uwierzytelniania wieloskładnikowego](./media/concept-mfa-howitworks/methods.png)

Usługa Azure AD Multi-Factor Authentication działa, wymagając co najmniej dwóch następujących metod uwierzytelniania:

* Coś, co wiesz, zazwyczaj hasło.
* Coś, na przykład zaufanego urządzenia, które nie jest łatwo duplikowane, takie jak telefon lub klucz sprzętowy.
* Coś, co jest bardzo metryczne, takie jak skanowanie odcisku palca lub z przodu.

Użytkownicy mogą zarejestrować się w ramach samoobsługowego resetowania haseł i usługi Azure AD Multi-Factor Authentication w jednym kroku, aby uprościć środowisko pracy. Administratorzy mogą definiować, które formy uwierzytelniania pomocniczego mogą być używane. Usługi Azure AD Multi-Factor Authentication mogą być również wymagane, gdy użytkownicy wykonują funkcję samoobsługowego resetowania hasła w celu dodatkowego zabezpieczenia tego procesu.

![Metody uwierzytelniania używane na ekranie logowania](media/concept-authentication-methods/overview-login.png)

Usługa Azure AD Multi-Factor Authentication pomaga w zabezpieczeniu dostępu do danych i aplikacji przy jednoczesnym zachowaniu prostoty dla użytkowników. Zapewnia dodatkowe zabezpieczenia, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie za pośrednictwem różnych [metod uwierzytelniania](concept-authentication-methods.md). Użytkownicy mogą lub nie mogą zakwestionować usługi MFA w oparciu o decyzje konfiguracyjne wykonywane przez administratora.

Aplikacje lub usługi nie muszą wprowadzać żadnych zmian w celu korzystania z usługi Azure AD Multi-Factor Authentication. Monity weryfikacyjne są częścią zdarzenia logowania usługi Azure AD, które automatycznie żąda i przetwarza żądanie MFA, jeśli jest to wymagane.

## <a name="available-verification-methods"></a>Dostępne metody weryfikacji

Gdy użytkownik loguje się do aplikacji lub usługi i odbiera monit o uwierzytelnianie wieloskładnikowe, może wybrać jedną z ich zarejestrowanych form dodatkowej weryfikacji. Administrator może wymagać rejestracji tych metod weryfikacji usługi Azure AD Multi-Factor Authentication lub użytkownik może uzyskać dostęp do własnego [mojego profilu](https://myprofile.microsoft.com) , aby edytować lub dodać metody weryfikacyjne.

Następujące dodatkowe formy weryfikacji mogą być używane z usługą Azure AD Multi-Factor Authentication:

* Aplikacja Microsoft Authenticator
* Token sprzętowy OATH
* SMS
* Połączenie głosowe

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Jak włączyć usługę Azure AD Multi-Factor Authentication i korzystać z niej

Użytkownicy i grupy mogą być włączane w usłudze Azure AD Multi-Factor Authentication w celu wyświetlenia monitu o dodatkową weryfikację w trakcie zdarzenia logowania. [Wartości domyślne zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md) są dostępne dla wszystkich dzierżawców usługi Azure AD, aby szybko włączyć korzystanie z aplikacji Microsoft Authenticator dla wszystkich użytkowników.

W celu uzyskania bardziej szczegółowych kontroli zasady [dostępu warunkowego](../conditional-access/overview.md) mogą służyć do definiowania zdarzeń lub aplikacji, które wymagają uwierzytelniania wieloskładnikowego. Te zasady mogą zezwalać na regularne zdarzenia logowania, gdy użytkownik znajduje się w sieci firmowej lub zarejestrowane urządzenie, ale wyświetla monit o dodatkowe czynniki weryfikacyjne w przypadku zdalnego lub na urządzeniu osobistym.

![Diagram omówienia sposobu działania dostępu warunkowego w celu zabezpieczenia procesu logowania](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o licencjonowaniu, zobacz [funkcje i licencje usługi Azure AD Multi-Factor Authentication](concept-mfa-licensing.md).

Aby wyświetlić usługę MFA w działaniu, należy włączyć Multi-Factor Authentication usługi Azure AD dla zestawu użytkowników testowych w następującym samouczku:

> [!div class="nextstepaction"]
> [Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure AD](./tutorial-enable-azure-mfa.md)