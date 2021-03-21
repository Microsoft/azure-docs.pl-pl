---
title: Typowe scenariusze zarządzania aplikacjami dla Azure Active Directory | Microsoft Docs
description: Scentralizowanie zarządzania aplikacjami za pomocą usługi Azure AD
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6372407a5deebefab60e09a9d0c01d977157357b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259574"
---
# <a name="centralize-application-management-with-azure-ad"></a>Scentralizowanie zarządzania aplikacjami za pomocą usługi Azure AD

Hasła, zarówno okropnej IT, jak i bólu dla pracowników na całym świecie. To dlatego, że coraz więcej firm może Azure Active Directory, rozwiązanie do zarządzania tożsamościami i dostępem firmy Microsoft dla chmury oraz innych zasobów. Przeskocz z aplikacji do aplikacji bez konieczności wprowadzania hasła dla każdego z nich. Przejdź do programu Outlook, do produktu Workday, tak szybko, jak można je otworzyć, szybko i bezpiecznie. Następnie Współpracuj z partnerami, a nawet z innymi poza swoją organizacją, bez konieczności wywoływania tej funkcji. Co więcej, usługa Azure AD pomaga w zarządzaniu ryzykiem dzięki zabezpieczeniu używanych aplikacji w taki sposób, jak uwierzytelnianie wieloskładnikowe, aby sprawdzić, kto jest używany, przy użyciu ciągłej adaptacyjnej usługi uczenia maszynowego i analizy zabezpieczeń w celu wykrywania podejrzanych logowań zapewniających bezpieczny dostęp do potrzebnych aplikacji, wszędzie tam, gdzie się znajdują. Jest to nie tylko doskonałe dla użytkowników, ale również do tego celu. Dzięki przeglądom dostępu just-in-Time i pełnemu pakietowi ładu, usługa Azure AD pomaga w zachowaniu zgodności i wymuszania zasad. Aby to zrobić, możesz nawet zautomatyzować Inicjowanie obsługi administracyjnej kont użytkowników, dzięki czemu zarządzanie dostępem jest Breeze. Zapoznaj się z typowymi scenariuszami używanymi przez klienta Azure Active Directory funkcji zarządzania aplikacjami w programie.

**Typowe scenariusze**


> [!div class="checklist"]
> * Logowanie jednokrotne dla wszystkich aplikacji
> * Automatyzowanie aprowizacji i anulowania obsługi 
> * Zabezpieczanie aplikacji
> * Zarządzanie dostępem do aplikacji
> * Bezpieczny dostęp do hybrydowej

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenariusz 1. Konfigurowanie logowania jednokrotnego dla wszystkich aplikacji

Nie ma więcej zarządzania hasłem. Bezpiecznie Uzyskuj dostęp do wszystkich potrzebnych zasobów przy użyciu poświadczeń firmowych. 

|Cecha  | Opis | Zalecenie |
|---------|---------|---------|
|Logowanie jednokrotne|Oparte na standardach federacyjnego Logowanie jednokrotne przy użyciu zaufanych standardów branżowych.|Zawsze używaj protokołu [SAML/OIDC](../develop/v2-howto-app-gallery-listing.md) , aby włączyć logowanie jednokrotne, gdy aplikacja je obsługuje.|
|Moje aplikacje|Zapewnianie użytkownikom prostego centrum w celu odnajdywania i uzyskiwania dostępu do wszystkich aplikacji. Umożliwiają im wydajniejsze korzystanie z funkcji samoobsługowych, takich jak żądanie dostępu do aplikacji i grup oraz zarządzanie dostępem do zasobów w imieniu innych użytkowników.| Wdróż [Moje aplikacje](my-apps-deployment-plan.md) w organizacji po zintegrowaniu aplikacji z usługą Azure AD na potrzeby logowania jednokrotnego.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenariusz 2. Automatyzacja aprowizacji i anulowania obsługi 


Większość aplikacji wymaga, aby użytkownik mógł zostać zainicjowany do aplikacji przed uzyskaniem dostępu do potrzebnych zasobów. Korzystanie z plików CSV lub skryptów złożonych może być kosztowne i trudne do zarządzania. Ponadto klienci muszą upewnić się, że konta są usuwane, gdy ktoś nie powinien już mieć dostępu. Skorzystaj z poniższych narzędzi, aby zautomatyzować obsługę administracyjną i anulować obsługę administracyjną. 


|Cecha  |Opis|Zalecenie |
|---------|---------|---------|
|Inicjowanie obsługi Standard scim|[Standard scim](https://aka.ms/SCIMOverview) to najlepsze rozwiązanie w branży do automatyzowania aprowizacji użytkowników. Wszystkie aplikacje zgodne z standard scim można zintegrować z usługą Azure AD. Automatycznie twórz, Aktualizuj i usuwaj konta użytkowników bez konieczności zachowywania plików CSV, skryptów niestandardowych ani rozwiązań Premium.|Zapoznaj się z rosnącą listą [wstępnie zintegrowanych](../saas-apps/tutorial-list.md) aplikacji w galerii aplikacji usługi Azure AD|
|Microsoft Graph|Skorzystaj z wdychania i głębokości danych, które usługa Azure AD ma wzbogacać swoją aplikację o dane, których potrzebuje.|Skorzystaj z [programu Microsoft Graph](https://developer.microsoft.com/graph/) , aby uzyskać dane z całego ekosystemu firmy Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Scenariusz 3: Zabezpieczanie aplikacji
Tożsamość jest Linchpin dla bezpieczeństwa. Jeśli tożsamość zostanie naruszona, trudno jest niezwykle efekt programu Domino przed zbyt późnym użyciem. Średnio ponad 100 dni przed wykryciem naruszenia bezpieczeństwa przez organizacje. Skorzystaj z narzędzi oferowanych przez usługę Azure AD, aby zwiększyć stan zabezpieczeń aplikacji. 

|Cecha  |Opis| Zalecenie |
|---------|---------| ---------|
|Usługa Azure AD MFA|Usługa Azure AD Multi-Factor Authentication (MFA) to rozwiązanie firmy Microsoft do weryfikacji dwuetapowej. Przy użyciu zaakceptowanych przez administratora metod uwierzytelniania usługa Azure AD MFA pomaga chronić dostęp do danych i aplikacji, a jednocześnie spełnia wymagania dotyczące prostego procesu logowania.| [Włącz usługę MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) dla użytkowników.  |
|Dostęp warunkowy|Za pomocą dostępu warunkowego można zaimplementować zautomatyzowane decyzje dotyczące kontroli dostępu, które mogą uzyskiwać dostęp do aplikacji w chmurze na podstawie warunków.| Zapoznaj się z [ustawieniami domyślnymi zabezpieczeń](../fundamentals/concept-fundamentals-security-defaults.md) i [typowymi zasadami](../conditional-access/concept-conditional-access-policy-common.md) używanymi przez klientów. | 
|Identity Protection|Usługa Identity Protection korzysta z informacji uzyskanych od firmy Microsoft w organizacjach z usługą Azure AD, przestrzenią użytkownika z kontami Microsoft oraz w grach z konsolą Xbox w celu ochrony użytkowników. Microsoft analizuje 6 500 000 000 000 sygnałów dziennie w celu identyfikowania i ochrony klientów przed zagrożeniami.|Włącz [domyślne zasady ochrony tożsamości](../identity-protection/concept-identity-protection-policies.md) udostępniane przez naszą usługę. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenariusz 4: zarządzanie dostępem do aplikacji
Zarządzanie tożsamościami pomaga organizacjom w osiągnięciu równowagi między produktywnością — jak szybko można uzyskać dostęp do potrzebnych aplikacji, takich jak dołączanie do mojej organizacji? I zabezpieczenia — jak należy zmienić ich dostęp z upływem czasu, na przykład ze względu na zmiany stanu zatrudnienia osoby? 

|Cecha  |Opis|Zalecenie |
|---------|---------| ---------|
|ELM|Zarządzanie prawami w usłudze Azure AD może pomóc użytkownikom zarówno w organizacji, jak i poza nią, efektywniejsze zarządzanie dostępem do aplikacji.| Zezwalaj użytkownikom innym niż Administratorzy na zarządzanie dostępem do aplikacji za pomocą [pakietów dostępu](../governance/entitlement-management-access-package-first.md).|
|Przeglądy dostępu|Dostęp użytkownika do aplikacji może być regularnie przeglądany, aby upewnić się, że tylko odpowiednie osoby mają stały dostęp.| [Przejrzyj dostęp](../governance/access-reviews-overview.md) do najbardziej poufnych aplikacji. |
|Log Analytics|Generowanie raportów o tym, kto uzyskuje dostęp do aplikacji i zapisuje je w wybranym narzędziu SIEM, aby skorelować dane między źródłami danych a z upływem czasu.| Włącz usługę [log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) i skonfiguruj alerty dla zdarzeń krytycznych związanych z aplikacjami. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenariusz 5: bezpieczny dostęp do hybrydowego dostępu
Tożsamość może być tylko płaszczyzną kontroli, jeśli może połączyć wszystko w aplikacjach w chmurze i lokalnych. Skorzystaj z narzędzi udostępnianych przez usługę Azure AD i jej partnerów, aby zabezpieczyć dostęp do starszych aplikacji opartych na uwierzytelnianiu.

|Cecha  |Opis|Zalecenie |
|---------|---------|---------|
|Serwer proxy aplikacji|Obecnie pracownicy chcą pracować wydajnie w dowolnym miejscu i czasie, na dowolnym urządzeniu. Muszą oni uzyskiwać dostęp do aplikacji SaaS w chmurze i aplikacji firmowych w środowisku lokalnym. Serwer proxy aplikacji usługi Azure AD umożliwia ten niezawodny dostęp bez kosztownych i złożonych wirtualnych sieci prywatnych (VPN) lub stref zdemilitaryzowana (stref DMZ).|Skonfiguruj [dostęp zdalny](./application-proxy.md) do aplikacji Premium. |
|F5, Akamai, rozwiązania Zscaler|Korzystając z istniejącego kontrolera sieci i dostarczania, można łatwo chronić starsze aplikacje, które nadal mają kluczowe znaczenie dla procesów firmy, ale nie można było chronić przed usługą Azure AD. Prawdopodobnie masz już wszystko, czego potrzebujesz, aby rozpocząć ochronę tych aplikacji.| Korzystasz z Akamai, Citrix, F5 lub rozwiązania Zscaler? Zapoznaj się z [wbudowanymi rozwiązaniami](./secure-hybrid-access.md). | 

## <a name="related-articles"></a>Pokrewne artykuły:

- [Zarządzanie aplikacjami](./index.yml)
- [Aprowizacja aplikacji](../app-provisioning/user-provisioning.md)
- [Bezpieczny dostęp do hybrydowej](./secure-hybrid-access.md)
- [Nadzór nad tożsamościami](../governance/identity-governance-overview.md)
- [Platforma tożsamości firmy Microsoft](../develop/v2-overview.md)
- [Zabezpieczenia tożsamości](../conditional-access/index.yml)