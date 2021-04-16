---
title: Zarządzanie dostępem zewnętrznym za pomocą Azure Active Directory zarządzania uprawnieniami
description: Jak używać Azure Active Directory uprawnień w ramach ogólnego planu zabezpieczeń dostępu zewnętrznego.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89744b63a555cc02d35815b4066ce572b7f77e38
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531888"
---
# <a name="manage-external-access-with-entitlement-management"></a>Zarządzanie dostępem zewnętrznym za pomocą zarządzania uprawnieniami 


[Zarządzanie uprawnieniami](../governance/entitlement-management-overview.md) to funkcja zarządzania tożsamościami, która umożliwia organizacjom zarządzanie cyklem życia tożsamości i dostępu na dużą skalę dzięki automatyzacji przepływów pracy żądań dostępu, przypisań dostępu, przeglądów i wygaśnięcia. Zarządzanie uprawnieniami umożliwia delegowanym użytkownikom niebędącym administratorami tworzenie pakietów dostępu, do których użytkownicy zewnętrzni z innych organizacji mogą żądać dostępu. [](../governance/entitlement-management-overview.md) Jeden i wieloetapowy przepływ pracy zatwierdzania można [](../governance/what-is-provisioning.md) skonfigurować tak, aby oceniał żądania i zapewniał użytkownikom ograniczony czasowo dostęp za pomocą cyklicznych przeglądów. Zarządzanie uprawnieniami umożliwia aprowizowanie i coprowizowanie kont zewnętrznych na podstawie zasad.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Kluczowe pojęcia dotyczące włączania zarządzania uprawnieniami

Poniższe kluczowe pojęcia są ważne, aby zrozumieć zarządzanie uprawnieniami.

### <a name="access-packages"></a>Pakiety dostępu

Pakiet [dostępu jest](../governance/entitlement-management-overview.md) podstawą zarządzania uprawnieniami. Pakiety dostępu to grupy zasobów zarządzanych przez zasady, których użytkownik potrzebuje do współpracy nad projektem lub wykonywania innych zadań. Na przykład pakiet dostępu może obejmować:

* dostęp do określonych witryn programu SharePoint.

* aplikacje dla przedsiębiorstw, w tym niestandardowe aplikacje firmowe i SaaS, takie jak Salesforce.

* Kanały aplikacji Microsoft Teams.

* Grupy usługi Microsoft 365. 

### <a name="catalogs"></a>Katalogi

Pakiety dostępu znajdują się [w katalogach](../governance/entitlement-management-catalog-create.md). Katalog należy utworzyć, aby pogrupować powiązane zasoby i uzyskać dostęp do pakietów oraz delegować możliwość zarządzania nimi. Najpierw należy dodać zasoby do katalogu, a następnie dodać te zasoby, aby uzyskać dostęp do pakietów. Na przykład możesz utworzyć katalog "Finanse" [](../governance/entitlement-management-delegate.md) i delegować jego zarządzanie do członka zespołu finansowego. Ta osoba może następnie [dodawać zasoby,](../governance/entitlement-management-catalog-create.md)tworzyć pakiety dostępu i zarządzać zatwierdzaniem dostępu do tych pakietów.

Na poniższym diagramie przedstawiono typowy cykl życia ładu dla użytkownika zewnętrznego uzyskującego dostęp do pakietu dostępu, który ma wygaśnięcie.

![Diagram cyklu zarządzania użytkownikami zewnętrznymi.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Dostęp zewnętrzny samoobsługi

Pakiety dostępu można otwierać za pośrednictwem witryny [Azure AD Mój dostęp Portal,](../governance/entitlement-management-request-access.md) aby umożliwić użytkownikom zewnętrznym żądanie dostępu. Zasady określają, kto może żądać pakietu dostępu. Określasz, kto może żądać pakietu dostępu:

* Określone [połączone organizacje](../governance/entitlement-management-organization.md)

* Wszystkie skonfigurowane połączone organizacje

* Wszyscy użytkownicy z dowolnej organizacji

* Użytkownicy-goście lub członek już w dzierżawie

### <a name="approvals"></a>Zatwierdzenia   
Pakiety dostępu mogą zawierać obowiązkowe zatwierdzenie dostępu. **Zawsze implementuj procesy zatwierdzania dla użytkowników zewnętrznych.** Zatwierdzenia mogą być zatwierdzeniem jednoetapowym lub wieloetapowym. Zatwierdzenia są określane przez zasady. Jeśli zarówno użytkownicy wewnętrzni, jak i zewnętrzni muszą uzyskać dostęp do tego samego pakietu, prawdopodobnie skonfigurujesz różne zasady dostępu dla różnych kategorii połączonych organizacji i dla użytkowników wewnętrznych.

### <a name="expiration"></a>Wygaśnięcie  
Pakiety dostępu mogą zawierać datę wygaśnięcia. Wygaśnięcie można ustawić na określony dzień lub nadać użytkownikowi określoną liczbę dni na dostęp. Gdy pakiet dostępu wygaśnie, a użytkownik nie ma innego dostępu, obiekt użytkownika-gościa B2B reprezentujący użytkownika może zostać usunięty lub zablokowany. Zalecamy wymuszanie wygaśnięcia w pakietach dostępu dla użytkowników zewnętrznych. Nie wszystkie pakiety dostępu wygasają. W przypadku tych, które tego nie zdają, upewnij się, że wykonujesz przeglądy dostępu.

### <a name="access-reviews"></a>Przeglądy dostępu

Pakiety dostępu mogą wymagać okresowych przeglądów [dostępu,](../governance/manage-guest-access-with-access-reviews.md)które wymagają od właściciela pakietu lub obiektu projektowego potwierdzania dalszego potrzeby dostępu użytkowników. 

Przed skonfigurowaniem przeglądu określ następujące elementy.

* Który

   * Jakie są kryteria dalszego dostępu?

   * Kim są wery recenzentzy?

* Jak często powinny być zaplanowane przeglądy?

   * Wbudowane opcje obejmują co miesiąc, co kwartał, co dwa roku lub co rok. 

   * W przypadku pakietów, które obsługują dostęp zewnętrzny, zalecamy co kwartał lub częściej. 

 

> [!IMPORTANT]
> Przeglądy dostępu pakietów dostępu przejrzyj tylko dostęp udzielany za pośrednictwem zarządzania uprawnieniami. W związku z tym należy skonfigurować inne procesy w celu przeglądania dostępu udostępnianego użytkownikom zewnętrznym poza zarządzaniem uprawnieniami.

Aby uzyskać więcej informacji na temat przeglądów dostępu, zobacz [Planowanie wdrożenia przeglądów dostępu usługi Azure AD.](../governance/deploy-access-reviews.md)

## <a name="using-automation-in-entitlement-management"></a>Korzystanie z automatyzacji w zarządzaniu uprawnieniami

Funkcje zarządzania [uprawnieniami można wykonywać przy użyciu Microsoft Graph](/graph/tutorial-access-package-api), w tym

* [Zarządzanie pakietami dostępu](/graph/api/resources/accesspackage?view=graph-rest-beta&preserve-view=true)

* [Zarządzanie przeglądami dostępu](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)

* [Zarządzanie połączonymi organizacjami](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true)

* [Zarządzanie ustawieniami zarządzania uprawnieniami](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta&preserve-view=true)

## <a name="recommendations"></a>Zalecenia 

Zalecamy stosowanie rozwiązań do zarządzania dostępem zewnętrznym za pomocą zarządzania uprawnieniami.

**W przypadku projektów z co [](../governance/entitlement-management-access-package-create.md) najmniej jednym** partnerem biznesowym należy utworzyć pakiety dostępu i używać ich do dołączania i aprowizować dostęp użytkowników tych partnerów do zasobów. 

* Jeśli masz już użytkowników B2B w katalogu, możesz również bezpośrednio przypisać ich do odpowiednich pakietów dostępu.

* Dostęp można przypisać w Azure Portal [lub](../governance/entitlement-management-access-package-assignments.md)za pośrednictwem [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta&preserve-view=true).

**Użyj ustawień zarządzania tożsamościami, aby usunąć użytkowników z katalogu po wygaśnięciu pakietów dostępu.**

![Zrzut ekranu przedstawiający konfigurowanie zarządzania cyklem życia użytkowników zewnętrznych.](media/secure-external-access/6-manage-external-lifecycle.png)

Te ustawienia dotyczą tylko użytkowników, którzy zostali dosiedli za pośrednictwem zarządzania uprawnieniami.

**[Delegowanie zarządzania katalogami i pakietami dostępu](../governance/entitlement-management-delegate.md)** do właścicieli firm, którzy mają więcej informacji na temat tego, kto powinien uzyskać dostęp do usługi .

![Zrzut ekranu przedstawiający konfigurowanie wykazu.](media/secure-external-access/6-catalog-management.png)

**[Wymuszaj wygaśnięcie pakietów dostępu,](../governance/entitlement-management-access-package-lifecycle-policy.md) do których użytkownicy zewnętrzni mają dostęp.**


![Zrzut ekranu przedstawiający konfigurowanie wygasania pakietu dostępu.](media/secure-external-access/6-access-package-expiration.png)

* Jeśli znasz datę zakończenia pakietu dostępu opartego na projekcie, ustaw określoną datę za pomocą daty w dniu. 

* W przeciwnym razie zalecamy, aby czas wygaśnięcia nie był dłuższy niż 365 dni, chyba że wiadomo, że jest to wieloroczne zaangażowanie.

* Zezwalaj użytkownikom na rozszerzanie dostępu.

* Wymagaj zatwierdzenia w celu udzielenia rozszerzenia.

**[Wymuszanie przeglądów dostępu pakietów w](../governance/manage-guest-access-with-access-reviews.md) celu uniknięcia niewłaściwego dostępu gości.**

![Zrzut ekranu przedstawiający tworzenie nowego pakietu dostępu.](media/secure-external-access/6-new-access-package.png)

* Wymuszaj przeglądy co kwartał.

* W przypadku projektów wrażliwych na zgodność należy ustawić recenzentów jako określonych recenzentów, zamiast samodzielnego przeglądania dla użytkowników zewnętrznych. Użytkownicy, którzy mają dostęp do menedżerów pakietów, są dobrym miejscem do rozpoczęcia dla recenzentów. 

* W przypadku mniej poufnych projektów samodzielna weryfikacja użytkowników zmniejszy obciążenie organizacji związane z usuwaniem dostępu użytkownikom, którzy już nie są w swojej organizacji macierzyej.

Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem dla użytkowników zewnętrznych w usłudze Azure AD Entitlement Management](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły dotyczące zabezpieczania zewnętrznego dostępu do zasobów. Zalecamy, aby podjąć akcje w podanej kolejności.

1. [Określanie bezpieczeństwa dostępu zewnętrznego](1-secure-access-posture.md)

2. [Odnajdywanie bieżącego stanu](2-secure-access-current-state.md)

3. [Tworzenie planu ładu](3-secure-access-plan.md)

4. [Używanie grup na użytek zabezpieczeń](4-secure-access-groups.md)

5. [Przejście do usługi Azure AD B2B](5-secure-access-b2b.md)

6. [Bezpieczny dostęp przy użyciu zarządzania uprawnieniami](6-secure-access-entitlement-managment.md) (jesteś tutaj).

7. [Bezpieczny dostęp przy użyciu zasad dostępu warunkowego](7-secure-access-conditional-access.md)

8. [Bezpieczny dostęp przy użyciu etykiet czułości](8-secure-access-sensitivity-labels.md)

9. [Bezpieczny dostęp do aplikacji Microsoft Teams, OneDrive i SharePoint](9-secure-access-teams-sharepoint.md)

 

