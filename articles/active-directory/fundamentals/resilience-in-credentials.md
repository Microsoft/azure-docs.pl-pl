---
title: Tworzenie odporności przy użyciu zarządzania poświadczeniami w Azure Active Directory
description: Przewodnik dla architektów i administratorów IT na potrzeby tworzenia odpornej strategii poświadczeń.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5fd5cf419adb137df5c578e3b17b88749215be5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919981"
---
# <a name="build-resilience-with-credential-management"></a>Tworzenie odporności przy użyciu zarządzania poświadczeniami

Gdy w żądaniu tokenu zostanie podane poświadczenie usługi Azure AD, istnieje wiele zależności, które muszą być dostępne do walidacji. Pierwszy czynnik uwierzytelniania opiera się na uwierzytelnianiu usługi Azure AD, a w niektórych przypadkach w infrastrukturze lokalnej. Aby uzyskać więcej informacji na temat architektury uwierzytelniania hybrydowego, zobacz [odporność na kompilacje w infrastrukturze hybrydowej](resilience-in-hybrid.md). 

W przypadku zaimplementowania drugiego czynnika zależności dla drugiego czynnika są dodawane do zależności dla pierwszej. Na przykład, jeśli pierwszy czynnik jest za pośrednictwem PTA, a drugi jest SMS, zależności są następujące:

* Usługi uwierzytelniania usługi Azure AD

* Usługa Azure MFA

* Infrastruktura lokalna

* Przewoźnik telefonu

* Urządzenie użytkownika (bez obrazu)

 
![Obraz metod uwierzytelniania i zależności](./media/resilience-in-credentials/admin-resilience-credentials.png)

Strategia poświadczeń powinna uwzględniać zależności poszczególnych typów uwierzytelniania i udostępniać metody, które unikają single point of failure. 

Ponieważ metody uwierzytelniania mają różne zależności, dobrym pomysłem jest umożliwienie użytkownikom zarejestrowania się w możliwie największej liczbie innych opcji. Jeśli to możliwe, należy uwzględnić dwa czynniki z różnymi zależnościami. Na przykład połączenia głosowe i wiadomości SMS jako dwa czynniki mają te same zależności, dzięki czemu są one takie same jak te, które nie ograniczają ryzyka.

Najbardziej odporną strategią poświadczeń jest użycie uwierzytelniania bezhaseł. Klucze zabezpieczeń funkcji Windows Hello dla firm i FIDO 2,0 mają mniejszą liczbę zależności niż silne uwierzytelnianie z dwoma odrębnymi czynnikami. Klucze zabezpieczeń Microsoft Authenticator aplikacji, Windows Hello dla firm i Fido 2,0 są najbezpieczniejsze. 

W przypadku drugiego czynnika aplikacja Microsoft Authenticator lub inne aplikacje uwierzytelniania przy użyciu jednorazowego kodu dostępu (TOTP) lub tokenów sprzętowych OATH mają najmniejszą liczbę zależności i dlatego są bardziej odporne na błędy.

## <a name="how-do-multiple-credentials-help-resilience"></a>Jak wiele poświadczeń pomaga w odporności?

Obsługa wielu typów poświadczeń zapewnia użytkownikom opcje, które ponoszą Preferencje i ograniczenia środowiska. W związku z tym uwierzytelnianie interaktywne, w przypadku których użytkownicy są monitowani o uwierzytelnianie wieloskładnikowe, będą bardziej odporne na niedostępność określonych zależności w czasie żądania. Można [zoptymalizować ponowne uwierzytelnienie w ramach uwierzytelniania wieloskładnikowego](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

Oprócz powyższej odporności poszczególnych użytkowników, przedsiębiorstwa powinny planować sytuacje awaryjne w przypadku zakłóceń na dużą skalę, takich jak błędy operacyjne, które wprowadzają błędną konfigurację, klęskę żywiołową lub awarię zasobów całego przedsiębiorstwa do lokalnej usługi federacyjnej (szczególnie w przypadku korzystania z uwierzytelniania wieloskładnikowego). 

## <a name="how-do-i-implement-resilient-credentials"></a>Jak mogę zaimplementować odporne poświadczenia?

* Wdróż [poświadczenia Bezhasła](../authentication/howto-authentication-passwordless-deployment.md) , takie jak Windows Hello dla firm, uwierzytelnianie telefoniczne i klucze zabezpieczeń FIDO2, aby zmniejszyć zależności.

* Wdróż [aplikację Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) jako drugi składnik.

* Włącz [synchronizację skrótów haseł](../hybrid/whatis-phs.md) dla kont hybrydowych, które są synchronizowane z systemem Windows Server Active Directory. Tę opcję można włączyć wraz z usługami federacyjnymi, takimi jak AD FS, i w przypadku awarii usługi federacyjnej.

* [Analizuj użycie metod uwierzytelniania wieloskładnikowego](https://docs.microsoft.com/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) , aby zwiększyć komfort pracy użytkowników.

* [Implementowanie elastycznej strategii kontroli dostępu](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Następne kroki
Odporność zasobów dla administratorów i architektów
 
* [Tworzenie odporności przy użyciu Stanów urządzeń](resilience-with-device-states.md)

* [Tworzenie odporności przy użyciu ciągłej oceny dostępu (CAE)](resilience-with-continuous-access-evaluation.md)

* [Tworzenie odporności w uwierzytelnianiu użytkowników zewnętrznych](resilience-b2b-authentication.md)

* [Tworzenie odporności w ramach uwierzytelniania hybrydowego](resilience-in-hybrid.md)

* [Tworzenie odporności w dostępie do aplikacji za pomocą serwera proxy aplikacji](resilience-on-premises-access.md)

Zasoby dotyczące odporności dla deweloperów

* [Tworzenie odporności IAM w aplikacjach](resilience-app-development-overview.md)

* [Odporność na kompilacje w systemach CIAM](resilience-b2c.md)
