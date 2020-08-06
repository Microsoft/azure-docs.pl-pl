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
ms.openlocfilehash: dcb7ab566d861e0c4256acda8f8facd1e94d4ad6
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87796781"
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

W przypadku wielu organizacji cykl życia tożsamości dla pracowników jest powiązany z reprezentacją tego użytkownika w systemie HCM (zarządzanie dużymi środkami).  Azure AD — wersja Premium automatycznie obsługuje tożsamości użytkowników dla osób przedstawionych w dniach roboczych w Active Directory i Azure Active Directory, zgodnie z opisem w [samouczku inicjowania obsługi ruchu przychodzącego](../saas-apps/workday-inbound-tutorial.md)dla programu Workday.  Azure AD — wersja Premium obejmuje również [Microsoft Identity Manager](/microsoft-identity-manager/), które mogą importować rekordy z lokalnych systemów HCM, takich jak SAP, Oracle eBusiness i Oracle PeopleSoft.

W coraz większym stopniu scenariusze wymagają współpracy z osobami spoza organizacji. Współpraca B2B w usłudze [Azure AD](/azure/active-directory/b2b/) umożliwia bezpieczne udostępnianie aplikacji i usług organizacji użytkownikom-Gościom i partnerom zewnętrznym z dowolnej organizacji przy zachowaniu kontroli nad danymi firmowymi.  [Zarządzanie prawami w usłudze Azure AD](entitlement-management-overview.md) pozwala wybrać, którzy użytkownicy organizacji mogą żądać dostępu i mogą być dodawani jako Goście B2B do katalogu organizacji, a także zagwarantować, że Ci Goście zostaną usunięci, gdy nie potrzebują już dostępu.

## <a name="access-lifecycle"></a>Cykl życia dostępu

Organizacje muszą mieć proces zarządzania dostępem poza to, co zostało początkowo zainicjowane dla użytkownika, gdy tożsamość tego użytkownika została utworzona.  Ponadto organizacje korporacyjne muszą być w stanie wydajnie skalować, aby mieć możliwość ciągłego tworzenia i wymuszania zasad dostępu i kontroli.

![Cykl życia dostępu](./media/identity-governance-overview/access-lifecycle.png)

Zwykle deleguje decyzje dotyczące zatwierdzenia dostępu do podmiotów podejmujących decyzje biznesowe.  Ponadto może to dotyczyć samych użytkowników.  Na przykład użytkownicy, którzy uzyskują dostęp do poufnych danych klienta w aplikacji marketingowej firmy w Europie, muszą znać zasady firmy. Użytkownicy-Goście mogą nie wiedzieć, jakie są wymagania dotyczące obsługi danych w organizacji, do której zostały zaproszone.

Organizacje mogą zautomatyzować proces cyklu życia dostępu za pomocą technologii, takich jak [grupy dynamiczne](../users-groups-roles/groups-dynamic-membership.md), w połączeniu z obsługą użytkowników, aby [SaaS aplikacje](../saas-apps/tutorial-list.md) lub [Aplikacje zintegrowane z usługą Standard scim](../app-provisioning/use-scim-to-provision-users-and-groups.md).  Organizacje mogą również kontrolować, którzy [Użytkownicy-Goście mają dostęp do aplikacji lokalnych](../b2b/hybrid-cloud-to-on-premises.md).  Te prawa dostępu można następnie regularnie przeglądać przy użyciu cyklicznych [przeglądów dostępu do usługi Azure AD](access-reviews-overview.md).   [Zarządzanie prawami w usłudze Azure AD](entitlement-management-overview.md) umożliwia także definiowanie sposobu, w jaki użytkownicy żądają dostępu między pakietami członkostwa grup i zespołów, rolami aplikacji i rolami usługi SharePoint Online.

Gdy użytkownik próbuje uzyskać dostęp do aplikacji, usługa Azure AD wymusza zasady [dostępu warunkowego](/azure/active-directory/conditional-access/) . Na przykład zasady dostępu warunkowego mogą zawierać [warunki użytkowania](../conditional-access/terms-of-use.md) i [upewnić się, że użytkownik wyraził zgodę na te warunki](../conditional-access/require-tou.md) przed uzyskaniem dostępu do aplikacji.

## <a name="privileged-access-lifecycle"></a>Cykl życia uprzywilejowanego dostępu

W przeszłości dostęp uprzywilejowany został opisany przez innych dostawców jako osobną możliwość od ładu zarządzania tożsamościami. Jednak firma Microsoft uważa, że zarządzanie uprzywilejowanym dostępem jest kluczową częścią zarządzania tożsamościami — szczególnie w przypadku, gdy potencjalną przyczyną nieprawidłowego użycia są skojarzone z tymi prawami administratora. Pracownicy, dostawcy i wykonawcy, którzy podejmują prawa administracyjne, muszą być zarządzani.

![Cykl życia uprzywilejowanego dostępu](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) oferuje dodatkowe funkcje kontroli dostosowane do zabezpieczania praw dostępu do zasobów w usłudze Azure AD, na platformie Azure i innych usługach online firmy Microsoft.  Możliwości dostępu just-in-Time i alertów dotyczące zmiany roli udostępniane przez usługę Azure AD PIM, oprócz uwierzytelniania wieloskładnikowego i dostępu warunkowego, zapewniają kompleksowy zestaw kontrolek ładu, który pomaga w zabezpieczeniu zasobów firmy (katalog, pakiet Office 365 i role zasobów platformy Azure). Podobnie jak w przypadku innych form dostępu, organizacje mogą korzystać z przeglądów dostępu w celu skonfigurowania cyklicznej certyfikacji dostępu dla wszystkich użytkowników w rolach administratora.

## <a name="getting-started"></a>Wprowadzenie

Zapoznaj się z kartą wprowadzenie w temacie Zarządzanie **tożsamościami** w Azure Portal, aby rozpocząć korzystanie z funkcji zarządzania prawami, przeglądów dostępu, Privileged Identity Management i warunki użytkowania.

![Wprowadzenie do zarządzania tożsamościami](./media/identity-governance-overview/getting-started.png)


Jeśli masz opinię na temat funkcji zarządzania tożsamościami, kliknij przycisk **otrzymasz opinię?** w Azure Portal przesłać swoją opinię. Zespół regularnie przegląda swoją opinię.

Chociaż nie ma doskonałego rozwiązania lub rekomendacji dla każdego klienta, następujące przewodniki konfiguracyjne zawierają również zasady linii bazowej firmy Microsoft zalecane do zapewnienia większego bezpieczeństwa i produktywności pracowników.

- [Konfiguracje tożsamości i dostępu do urządzeń](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Zabezpieczanie dostępu uprzywilejowanego](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>Dodatek-najmniejsze role uprzywilejowane do zarządzania w funkcjach ładu tożsamości

Najlepszym rozwiązaniem jest użycie roli najniższych uprawnień do wykonywania zadań administracyjnych w ramach nadzoru tożsamości. Zalecamy użycie usługi Azure AD PIM do uaktywnienia roli w razie potrzeby w celu wykonania tych zadań. Poniżej wymieniono najmniej uprzywilejowane role katalogu w celu skonfigurowania funkcji zarządzania tożsamościami:

| Cechy | Najmniejsza rola uprzywilejowana |
| ------- | --------------------- |
| Zarządzanie upoważnieniami | Administrator użytkowników (z wyjątkiem dodawania witryn usługi SharePoint Online do wykazów, które wymagają administratora globalnego) |
| Przeglądy dostępu | Administrator użytkowników (z wyjątkiem przeglądów dostępu dotyczących ról platformy Azure lub usługi Azure AD, które wymagają uprzywilejowanego administratora roli) |
|Privileged Identity Management | Administrator ról uprzywilejowanych |
| Warunki użytkowania | Administrator zabezpieczeń lub administrator dostępu warunkowego |

## <a name="next-steps"></a>Następne kroki

- [Co to jest zarządzanie upoważnieniami w usłudze Azure AD?](entitlement-management-overview.md)
- [Co to są przeglądy dostępu do usługi Azure AD?](access-reviews-overview.md)
- [Co to jest usługa Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)
- [Do czego można wykorzystać warunki użytkowania?](active-directory-tou.md)


