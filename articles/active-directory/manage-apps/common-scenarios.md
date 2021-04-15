---
title: Typowe scenariusze zarządzania aplikacją dla Azure Active Directory | Microsoft Docs
description: Centralizowanie zarządzania aplikacją za pomocą usługi Azure AD
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: overview
ms.date: 03/02/2019
ms.author: iangithinji
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1acfff8a9152c767b61463bed0165d5ae390f649
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375002"
---
# <a name="centralize-application-management-with-azure-ad"></a>Centralizowanie zarządzania aplikacją za pomocą usługi Azure AD

Hasła, zarówno dla pracowników IT, jak i dla pracowników na całym świecie. Z tego powodu coraz więcej firm przejmuje się Azure Active Directory, rozwiązania firmy Microsoft do zarządzania tożsamościami i dostępem dla chmury i wszystkich innych zasobów. Przeskakij z aplikacji do aplikacji bez konieczności wprowadzania hasła dla każdej z nich. Przeskakuj z programu Outlook do aplikacji Workday do protokołu ADP tak szybko, jak możesz je szybko i bezpiecznie otworzyć. Następnie współpracuj z partnerami, a nawet innymi osobami spoza organizacji, bez konieczności wołania do it. Ponadto usługa Azure AD pomaga zarządzać ryzykiem, zabezpieczając aplikacje, których używasz z uwierzytelnianiem wieloskładnikowym, aby sprawdzić, kim jesteś, używając ciągle adaptacyjnego uczenia maszynowego i analizy zabezpieczeń do wykrywania podejrzanych logów, zapewniając bezpieczny dostęp do potrzebnych aplikacji, gdziekolwiek jesteś. Jest to doskonałe nie tylko dla użytkowników, ale również dla it. Dzięki przeglądom dostępu just in time i pakietowi ładu w pełnej skali usługa Azure AD pomaga zachować zgodność i wymuszać zasady. Możesz też zautomatyzować aprowizowanie kont użytkowników, co ułatwia zarządzanie dostępem. Zapoznaj się z niektórymi z typowych scenariuszy, w których klient Azure Active Directory możliwości zarządzania aplikacją.

**Typowe scenariusze**


> [!div class="checklist"]
> * Logowanie jednokrotne dla wszystkich aplikacji
> * Automatyzowanie aprowzowania i coania aprowzowania 
> * Zabezpieczanie aplikacji
> * Reguluj dostęp do aplikacji
> * Hybrydowy bezpieczny dostęp

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>Scenariusz 1. Konfigurowanie logowania jednokrotnego dla wszystkich aplikacji

Koniec z zarządzaniem hasłem. Bezpieczny dostęp do wszystkich potrzebnych zasobów przy użyciu poświadczeń firmowych. 

|Cecha  | Opis | Zalecenie |
|---------|---------|---------|
|Logowanie jednokrotne|Federacyjna logowanie jednokrotne oparte na standardach z użyciem zaufanych standardów branżowych.|Zawsze używaj [saml/OIDC,](../develop/v2-howto-app-gallery-listing.md) aby włączyć logowanie jednokrotne, gdy aplikacja go obsługuje.|
|Moje aplikacje|Zaoferuj użytkownikom proste centrum do odnajdywania wszystkich swoich aplikacji i uzyskiwania do nich dostępu. Umożliwienie im większej produktywności dzięki możliwościom samoobsługi, na przykład żądaniu dostępu do aplikacji i grup lub zarządzaniu dostępem do zasobów w imieniu innych osób.| Wdrażanie [Moje aplikacje](my-apps-deployment-plan.md) w organizacji po zintegrowaniu aplikacji z usługą Azure AD w celu logowania jednokrotnego.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>Scenariusz 2. Automatyzacja aprowzowania i coprowizowania 


Większość aplikacji wymaga aprowizowania użytkownika w aplikacji przed uzyskaniem dostępu do potrzebnych zasobów. Korzystanie z plików CSV lub złożonych skryptów może być kosztowne i trudne do zarządzania. Ponadto klienci muszą upewnić się, że konta są usuwane, gdy ktoś nie powinien już mieć dostępu. Skorzystaj z poniższych narzędzi, aby zautomatyzować aprowizowanie i coprowizowanie. 


|Cecha  |Opis|Zalecenie |
|---------|---------|---------|
|Inicjowanie obsługi standardu SCIM|[SCIM](https://aka.ms/SCIMOverview) to najlepsze w branży rozwiązanie do automatyzacji aprowzowania użytkowników. Każdą aplikację zgodną ze standardem SCIM można zintegrować z usługą Azure AD. Automatycznie twórz, aktualizuj i usuwaj konta użytkowników bez konieczności obsługi plików CSV, skryptów niestandardowych ani rozwiązań w tym zakresie.|Zapoznaj się z rosnącą listą [wstępnie zintegrowanych aplikacji](../saas-apps/tutorial-list.md) w galerii aplikacji usługi Azure AD|
|Microsoft Graph|Wykorzystaj wiele i głębi danych, których usługa Azure AD potrzebuje, aby wzbogacić aplikację o potrzebne dane.|Skorzystaj z [programu Microsoft Graph,](https://developer.microsoft.com/graph/) aby pobrać dane z całego ekosystemu firmy Microsoft. |


## <a name="scenario-3-secure-your-applications"></a>Scenariusz 3. Zabezpieczanie aplikacji
Tożsamość to linchpin dla zabezpieczeń. Jeśli tożsamość zostanie naruszona, niezwykle trudno jest zatrzymać efekt domina, zanim będzie za opóźniona. Średnio ponad 100 dni minąć, zanim organizacje odkryją, że doszło do naruszenia bezpieczeństwa. Użyj narzędzi dostarczonych przez usługę Azure AD, aby poprawić poziom zabezpieczeń aplikacji. 

|Cecha  |Opis| Zalecenie |
|---------|---------| ---------|
|Azure AD MFA|Usługa Azure AD Multi-Factor Authentication (MFA) to rozwiązanie firmy Microsoft do weryfikacji dwuetapowej. Korzystając z metod uwierzytelniania zatwierdzonych przez administratora, usługa Azure AD MFA pomaga chronić dostęp do danych i aplikacji, jednocześnie spełniając wymagania dotyczące prostego procesu logowania.| [Włącz usługę MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) dla użytkowników.  |
|Dostęp warunkowy|Za pomocą dostępu warunkowego można wdrożyć zautomatyzowane decyzje dotyczące kontroli dostępu dla osób, które mogą uzyskać dostęp do aplikacji w chmurze, na podstawie warunków.| Przejrzyj wartości [domyślne zabezpieczeń i](../fundamentals/concept-fundamentals-security-defaults.md) typowe [zasady, z których](../conditional-access/concept-conditional-access-policy-common.md) korzysta klient. | 
|Identity Protection|Usługa Identity Protection korzysta z informacji uzyskanych przez firmę Microsoft z pracy w organizacjach korzystających z usługi Azure AD, przestrzeni konsumentów z kontami Microsoft oraz gier z konsolą Xbox w celu ochrony użytkowników. Firma Microsoft analizuje 6,5 biliona sygnałów dziennie, aby identyfikować i chronić klientów przed zagrożeniami.|Włącz domyślne [zasady ochrony tożsamości](../identity-protection/concept-identity-protection-policies.md) udostępniane przez naszą usługę. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>Scenariusz 4. Reguluj dostęp do aplikacji
Nadzór nad tożsamościami pomaga organizacjom osiągnąć równowagę między produktywnością — jak szybko osoba może mieć dostęp do potrzebnych aplikacji, takich jak dołączenie do mojej organizacji? Zabezpieczenia — jak jej dostęp powinien zmieniać się w czasie, na przykład ze względu na zmiany stanu zatrudnienia tej osoby? 

|Cecha  |Opis|Zalecenie |
|---------|---------| ---------|
|Elm|Zarządzanie uprawnieniami w usłudze Azure AD może pomóc użytkownikom w organizacji i poza nią wydajniej zarządzać dostępem do aplikacji.| Zezwalanie użytkownikom niebędącym administratorami na zarządzanie dostępem do swoich aplikacji przy użyciu [pakietów dostępu.](../governance/entitlement-management-access-package-first.md)|
|Przeglądy dostępu|Dostęp użytkowników do aplikacji można regularnie przeglądać, aby upewnić się, że tylko odpowiednie osoby nadal mają dostęp.| [Przejrzyj dostęp](../governance/access-reviews-overview.md) do najbardziej poufnych aplikacji. |
|Log Analytics|Generowanie raportów o tym, kto uzyskuje dostęp do aplikacji i które przechowują je w narzędziu SIEM do korelowania danych między źródłami danych i w czasie.| Włącz [analizę dzienników](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) i skonfiguruj alerty dla zdarzeń krytycznych związanych z aplikacjami. |


## <a name="scenario-5-hybrid-secure-access"></a>Scenariusz 5. Bezpieczny dostęp hybrydowy
Tożsamość może być płaszczyzną kontroli tylko wtedy, gdy może połączyć wszystko między aplikacjami w chmurze i lokalnymi. Korzystaj z narzędzi dostarczanych przez usługę Azure AD i jej partnerów, aby zabezpieczyć dostęp do aplikacji opartych na starszych uwierzytelnieniach.

|Cecha  |Opis|Zalecenie |
|---------|---------|---------|
|Serwer proxy aplikacji|Obecnie pracownicy chcą pracować wydajnie w dowolnym miejscu i czasie, na dowolnym urządzeniu. Muszą oni uzyskać dostęp do aplikacji SaaS w chmurze i lokalnych aplikacji firmowych. Serwer proxy aplikacji usługi Azure AD zapewnia ten niezawodny dostęp bez kosztownych i złożonych wirtualnych sieci prywatnych (VPN) ani stref zdemilitaryzowanych (DMZ).|Skonfiguruj [dostęp zdalny](./application-proxy.md) dla aplikacji internetowych. |
|F5, Akamai, Zscaler|Korzystając z istniejącego kontrolera sieci i kontrolera dostarczania, możesz łatwo chronić starsze aplikacje, które nadal mają kluczowe znaczenie dla procesów biznesowych, ale których wcześniej nie można było chronić za pomocą usługi Azure AD. Prawdopodobnie masz już wszystko, czego potrzebujesz, aby rozpocząć ochronę tych aplikacji.| Korzystasz z akamai, Citrix, F5 czy Zscaler? Zapoznaj się z [naszymi wstępnie sbudowaną rozwiązaniami](./secure-hybrid-access.md). | 

## <a name="related-articles"></a>Pokrewne artykuły:

- [Zarządzanie aplikacjami](./index.yml)
- [Aprowizacja aplikacji](../app-provisioning/user-provisioning.md)
- [Hybrydowy bezpieczny dostęp](./secure-hybrid-access.md)
- [Nadzór nad tożsamościami](../governance/identity-governance-overview.md)
- [Platforma tożsamości firmy Microsoft](../develop/v2-overview.md)
- [Zabezpieczenia tożsamości](../conditional-access/index.yml)