---
title: Zarządzanie tożsamościami — Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance pozwala zrównoważyć potrzebę organizacji dla bezpieczeństwa i produktywności pracowników przy użyciu odpowiednich procesów i widoczności.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e02df83d4b7874a1d158aae45f1619eb543e0aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92362454"
---
# <a name="what-is-azure-ad-identity-governance"></a>Co to jest usługa Azure AD Identity Governance?

Usługa Azure Active Directory (Azure AD) Zarządzanie tożsamościami pozwala zrównoważyć potrzebę organizacji w zakresie zabezpieczeń i produktywności pracowników z właściwymi procesami i widocznością. Zapewnia to możliwość zapewnienia, że odpowiednie osoby mają właściwy dostęp do odpowiednich zasobów. Te i powiązane funkcje usługi Azure AD i Enterprise Mobility + Security umożliwiają zmniejszenie ryzyka związanego z dostępem przez ochronę, monitorowanie i inspekcję dostępu do krytycznych zasobów — jednocześnie zapewniając produktywność pracowników i partnerów.  

Zarządzanie tożsamościami umożliwia organizacjom wykonywanie następujących zadań między pracownikami, partnerami biznesowymi i dostawcami oraz między usługami i aplikacjami zarówno lokalnie, jak i w chmurach:

- Zarządzanie cyklem życia tożsamości
- Zarządzanie cyklem życia dostępu
- Bezpieczny dostęp uprzywilejowany dla administracji

W odróżnieniu od tych czterech najważniejszych pytań warto ułatwić organizacjom Rozwiązywanie problemów:

- Którzy użytkownicy powinni mieć dostęp do zasobów?
- Jakie są użytkownicy korzystający z tego dostępu?
- Czy istnieją skuteczne kontrole organizacyjne na potrzeby zarządzania dostępem?
- Czy inspekcje mogą sprawdzić, czy kontrolki działają?

## <a name="identity-lifecycle"></a>Cykl życia tożsamości

Zarządzanie tożsamościami pomaga organizacjom w osiągnięciu równowagi między *produktywnością* — jak szybko można uzyskać dostęp do potrzebnych zasobów, takich jak dołączanie do mojej organizacji? I *zabezpieczenia* — jak należy zmienić ich dostęp z upływem czasu, na przykład ze względu na zmiany stanu zatrudnienia osoby?  Zarządzanie cyklem życia tożsamości jest podstawą do zarządzania tożsamościami, a skuteczne zarządzanie na dużą skalę wymaga modernizacji infrastruktury zarządzania cyklem życia tożsamości dla aplikacji.

![Cykl życia tożsamości](./media/identity-governance-overview/identity-lifecycle.png)

W przypadku wielu organizacji cykl życia tożsamości dla pracowników jest powiązany z reprezentacją tego użytkownika w systemie HCM (zarządzanie dużymi środkami).  Azure AD — wersja Premium automatycznie obsługuje tożsamości użytkowników dla osób, które są reprezentowane w dniach roboczych i SuccessFactors w Active Directory i Azure Active Directory, zgodnie z opisem w  [aplikacji Cloud kadr w Azure Active Directory podręczniku planowania użytkowników](../app-provisioning/plan-cloud-hr-provision.md).  Azure AD — wersja Premium obejmuje również [Microsoft Identity Manager](/microsoft-identity-manager/), które mogą importować rekordy z lokalnych systemów HCM, takich jak SAP HCM, Oracle eBusiness i Oracle PeopleSoft.

W coraz większym stopniu scenariusze wymagają współpracy z osobami spoza organizacji. Współpraca B2B w usłudze [Azure AD](/azure/active-directory/b2b/) umożliwia bezpieczne udostępnianie aplikacji i usług organizacji użytkownikom-Gościom i partnerom zewnętrznym z dowolnej organizacji przy zachowaniu kontroli nad danymi firmowymi.  [Zarządzanie prawami w usłudze Azure AD](entitlement-management-overview.md) pozwala wybrać, którzy użytkownicy organizacji mogą żądać dostępu i mogą być dodawani jako Goście B2B do katalogu organizacji, a także zagwarantować, że Ci Goście zostaną usunięci, gdy nie potrzebują już dostępu.

## <a name="access-lifecycle"></a>Cykl życia dostępu

Organizacje muszą mieć proces zarządzania dostępem poza to, co zostało początkowo zainicjowane dla użytkownika, gdy tożsamość tego użytkownika została utworzona.  Ponadto organizacje korporacyjne muszą być w stanie wydajnie skalować, aby mieć możliwość ciągłego tworzenia i wymuszania zasad dostępu i kontroli.

![Cykl życia dostępu](./media/identity-governance-overview/access-lifecycle.png)

Zwykle deleguje decyzje dotyczące zatwierdzenia dostępu do podmiotów podejmujących decyzje biznesowe.  Ponadto może to dotyczyć samych użytkowników.  Na przykład użytkownicy, którzy uzyskują dostęp do poufnych danych klienta w aplikacji marketingowej firmy w Europie, muszą znać zasady firmy. Użytkownicy-Goście mogą nie wiedzieć, jakie są wymagania dotyczące obsługi danych w organizacji, do której zostały zaproszone.

Organizacje mogą zautomatyzować proces cyklu życia dostępu za pomocą technologii, takich jak [grupy dynamiczne](../enterprise-users/groups-dynamic-membership.md), w połączeniu z obsługą użytkowników, aby [SaaS aplikacje](../saas-apps/tutorial-list.md) lub [Aplikacje zintegrowane z usługą Standard scim](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Organizacje mogą również kontrolować, którzy [Użytkownicy-Goście mają dostęp do aplikacji lokalnych](../external-identities/hybrid-cloud-to-on-premises.md).  Te prawa dostępu można następnie regularnie przeglądać przy użyciu cyklicznych [przeglądów dostępu do usługi Azure AD](access-reviews-overview.md).   [Zarządzanie prawami w usłudze Azure AD](entitlement-management-overview.md) umożliwia także definiowanie sposobu, w jaki użytkownicy żądają dostępu między pakietami członkostwa grup i zespołów, rolami aplikacji i rolami usługi SharePoint Online.

Gdy użytkownik próbuje uzyskać dostęp do aplikacji, usługa Azure AD wymusza zasady [dostępu warunkowego](../conditional-access/index.yml) . Na przykład zasady dostępu warunkowego mogą zawierać [warunki użytkowania](../conditional-access/terms-of-use.md) i [upewnić się, że użytkownik wyraził zgodę na te warunki](../conditional-access/require-tou.md) przed uzyskaniem dostępu do aplikacji.

## <a name="privileged-access-lifecycle"></a>Cykl życia uprzywilejowanego dostępu

W przeszłości dostęp uprzywilejowany został opisany przez innych dostawców jako osobną możliwość od ładu zarządzania tożsamościami. Jednak firma Microsoft uważa, że zarządzanie uprzywilejowanym dostępem jest kluczową częścią zarządzania tożsamościami — szczególnie w przypadku, gdy potencjalną przyczyną nieprawidłowego użycia są skojarzone z tymi prawami administratora. Pracownicy, dostawcy i wykonawcy, którzy podejmują prawa administracyjne, muszą być zarządzani.

![Cykl życia uprzywilejowanego dostępu](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) oferuje dodatkowe funkcje kontroli dostosowane do zabezpieczania praw dostępu do zasobów w usłudze Azure AD, na platformie Azure i innych usługach online firmy Microsoft.  Możliwości dostępu just-in-Time i alertów dotyczące zmiany roli udostępniane przez usługę Azure AD PIM, oprócz uwierzytelniania wieloskładnikowego i dostępu warunkowego, zapewniają kompleksowy zestaw kontrolek ładu, aby pomóc w zabezpieczeniu zasobów firmy (katalogów, Microsoft 365 i ról zasobów platformy Azure). Podobnie jak w przypadku innych form dostępu, organizacje mogą korzystać z przeglądów dostępu w celu skonfigurowania cyklicznej certyfikacji dostępu dla wszystkich użytkowników w rolach administratora.

## <a name="governance-capabilities-in-other-azure-ad-features"></a>Możliwości ładu w innych funkcjach usługi Azure AD

Oprócz funkcji wymienionych powyżej, dodatkowe funkcje usługi Azure AD często używane do zapewniania scenariuszy zarządzania tożsamościami obejmują:

| Możliwość | Scenariusz |Cecha
| ------- | --------------------- |-----|
|Cykl życia tożsamości (pracownicy)|Administratorzy mogą włączyć Inicjowanie obsługi konta użytkownika z poziomu produktu Workday lub SuccessFactors Cloud HR lub lokalnej KADRy.|[Inicjowanie obsługi administracyjnej użytkowników w chmurze w usłudze Azure AD](../app-provisioning/plan-cloud-hr-provision.md)|
|Cykl życia tożsamości (Goście)|Administratorzy mogą włączyć dołączenie do samoobsługowego użytkownika-gościa z innej dzierżawy usługi Azure AD, Federacji bezpośredniej, jednorazowego kodu dostępu (OTP) lub kont Google.  Użytkownicy-Goście są automatycznie obsługiwani i cofają aprowizacji z uwzględnieniem zasad cyklu życia.|[Zarządzanie prawami](entitlement-management-overview.md) przy użyciu [B2B](../external-identities/what-is-b2b.md)|
|Zarządzanie upoważnieniami|Właściciele zasobów mogą tworzyć pakiety dostępu zawierające aplikacje, zespoły, usługi Azure AD i grupy Microsoft 365 oraz witryny programu SharePoint Online.|[Zarządzanie upoważnieniami](entitlement-management-overview.md)|
|Żądania dostępu|Użytkownicy końcowi mogą żądać członkostwa w grupie lub dostępu do aplikacji. Użytkownicy końcowi, w tym Goście z innych organizacji, mogą zażądać dostępu do pakietów.|[Zarządzanie upoważnieniami](entitlement-management-overview.md)|
|Przepływ pracy|Właściciele zasobów mogą definiować osoby zatwierdzające i osoby zatwierdzające eskalacji dla żądań dostępu i osób zatwierdzających dla żądań aktywacji roli.  |[Zarządzanie prawami](entitlement-management-overview.md) i [PIM](../privileged-identity-management/pim-configure.md)|
|Zarządzanie zasadami i rolami|Administrator może zdefiniować zasady dostępu warunkowego dla dostępu do aplikacji w czasie wykonywania.  Właściciele zasobów mogą definiować zasady dostępu użytkownika za pośrednictwem pakietów dostępu.|Zasady [dostępu warunkowego](../conditional-access/overview.md) i [zarządzania uprawnieniami](entitlement-management-overview.md)|
|Dostęp do certyfikacji|Administratorzy mogą włączyć cykliczny certyfikat dostępu do aplikacji SaaS lub członkostwa w grupach w chmurze, usługi Azure AD lub przypisań ról zasobów platformy Azure. Automatycznie usuwaj dostęp do zasobów, Blokuj dostęp gościa i usuwaj konta gościa.|[Przeglądy dostępu](access-reviews-overview.md), również w programie [PIM](../privileged-identity-management/pim-how-to-start-security-review.md)|
|Realizacja i Inicjowanie obsługi|Automatyczne Inicjowanie obsługi i cofanie aprowizacji do aplikacji połączonych z usługą Azure AD, w tym za pośrednictwem Standard scim oraz witryn usługi SharePoint Online. |[Inicjowanie obsługi użytkowników](../app-provisioning/user-provisioning.md)|
|Raportowanie i analiza|Administratorzy mogą pobrać dzienniki inspekcji dotyczące ostatniej aprowizacji i aktywności użytkownika. Integracja z Azure Monitor i "kto ma dostęp" za pośrednictwem pakietów dostępu.|Raporty i [monitorowanie](../reports-monitoring/overview-monitoring.md) [usługi Azure AD](../reports-monitoring/overview-reports.md)|
|Dostęp uprzywilejowany|Dostęp just-in-Time i zaplanowany, alerty, przepływy pracy zatwierdzania dla ról usługi Azure AD (w tym ról niestandardowych) i ról zasobów platformy Azure.|[Usługa Azure AD PIM](../privileged-identity-management/pim-configure.md)|
|Inspekcja|Administratorzy mogą otrzymywać alerty o tworzeniu kont administratorów.|[Alerty usługi Azure AD PIM](../privileged-identity-management/pim-how-to-configure-security-alerts.md)|

## <a name="getting-started"></a>Wprowadzenie

Zapoznaj się z kartą wprowadzenie w temacie Zarządzanie **tożsamościami** w Azure Portal, aby rozpocząć korzystanie z funkcji zarządzania prawami, przeglądów dostępu, Privileged Identity Management i warunki użytkowania.

![Wprowadzenie do zarządzania tożsamościami](./media/identity-governance-overview/getting-started.png)


Jeśli masz opinię na temat funkcji zarządzania tożsamościami, kliknij przycisk **otrzymasz opinię?** w Azure Portal przesłać swoją opinię. Zespół regularnie przegląda swoją opinię.

Chociaż nie ma doskonałego rozwiązania lub rekomendacji dla każdego klienta, następujące przewodniki konfiguracyjne zawierają również zasady linii bazowej firmy Microsoft zalecane do zapewnienia większego bezpieczeństwa i produktywności pracowników.

- [Konfiguracje tożsamości i dostępu do urządzeń](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Zabezpieczanie dostępu uprzywilejowanego](../roles/security-planning.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Dodatek-najmniejsze role uprzywilejowane do zarządzania w funkcjach ładu tożsamości

Najlepszym rozwiązaniem jest użycie roli najniższych uprawnień do wykonywania zadań administracyjnych w ramach nadzoru tożsamości. Zalecamy użycie usługi Azure AD PIM do uaktywnienia roli w razie potrzeby w celu wykonania tych zadań. Poniżej wymieniono najmniej uprzywilejowane role katalogu w celu skonfigurowania funkcji zarządzania tożsamościami:

| Cecha | Najmniejsza rola uprzywilejowana |
| ------- | --------------------- |
| Zarządzanie upoważnieniami | Administrator użytkowników (z wyjątkiem dodawania witryn usługi SharePoint Online do wykazów, które wymagają administratora globalnego) |
| Przeglądy dostępu | Administrator użytkowników (z wyjątkiem przeglądów dostępu dotyczących ról platformy Azure lub usługi Azure AD, które wymagają uprzywilejowanego administratora roli) |
|Privileged Identity Management | Administrator ról uprzywilejowanych |
| Warunki użytkowania | Administrator zabezpieczeń lub administrator dostępu warunkowego |

## <a name="next-steps"></a>Następne kroki

- [Co to jest zarządzanie upoważnieniami w usłudze Azure AD?](entitlement-management-overview.md)
- [Co to są przeglądy dostępu do usługi Azure AD?](access-reviews-overview.md)
- [Co to jest usługa Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Do czego można wykorzystać warunki użytkowania?](../conditional-access/terms-of-use.md)