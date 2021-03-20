---
title: Zarządzanie dostępem zewnętrznym przy użyciu Azure Active Directory zarządzania uprawnieniami
description: Jak korzystać z Azure Active Directory zarządzania prawami w ramach ogólnego planu zabezpieczeń dostępu zewnętrznego.
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
ms.openlocfilehash: f06a54f59405d9833194b2e7d4488bc93d2437ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725182"
---
# <a name="manage-external-access-with-entitlement-management"></a>Zarządzanie dostępem zewnętrznym przy użyciu zarządzania uprawnieniami 


[Zarządzanie prawami](../governance/entitlement-management-overview.md) jest możliwością zarządzania tożsamościami, która umożliwia organizacjom Zarządzanie cyklem życia tożsamości i dostępem w dużej skali przez Automatyzowanie przepływów pracy żądań dostępu, przypisań, przeglądów i wygaśnięcia. Zarządzanie prawami pozwala delegowanym niezarządzanym administratorom tworzyć [pakiety dostępu](../governance/entitlement-management-overview.md) , do których użytkownicy zewnętrzni z innych organizacji mogą żądać dostępu. Jeden i wieloetapowy przepływ pracy zatwierdzania można skonfigurować w celu oceny żądań i [zapewnienia](../governance/what-is-provisioning.md) użytkownikom dostępu ograniczonego do czasu za pomocą cyklicznych przeglądów. Zarządzanie prawami umożliwia obsługę administracyjną na podstawie zasad i cofanie aprowizacji kont zewnętrznych.

## <a name="key-concepts-for-enabling-entitlement-management"></a>Kluczowe pojęcia dotyczące włączania zarządzania uprawnieniami

Poniższe kluczowe pojęcia są ważne do zrozumienia zarządzania prawami.

### <a name="access-packages"></a>Pakiety dostępu

[Pakietem dostępu](../governance/entitlement-management-overview.md) jest podstawą zarządzania prawami. Pakiety dostępu to grupy zasobów podlegających zasadom, które użytkownik musi współpracować nad projektem lub wykonywać inne zadania. Na przykład pakiet dostępu może obejmować:

* dostęp do określonych witryn programu SharePoint.

* aplikacje dla przedsiębiorstw, w tym niestandardowe aplikacje wewnętrzne i SaaS, takie jak Salesforce.

* Kanały Microsoft Teams.

* Grupy Microsoft 365. 

### <a name="catalogs"></a>Katalogi

Pakiety dostępu znajdują się w [wykazach](../governance/entitlement-management-catalog-create.md). Katalog można utworzyć, jeśli chcesz grupować powiązane zasoby i uzyskiwać dostęp do pakietów i delegować możliwości zarządzania nimi. Najpierw Dodaj zasoby do wykazu, a następnie możesz dodać te zasoby, aby uzyskać dostęp do pakietów. Na przykład może być konieczne utworzenie wykazu "Finanse" i [delegowanie jego zarządzania](../governance/entitlement-management-delegate.md) do członka zespołu finansowego. Ta osoba może następnie [dodawać zasoby](../governance/entitlement-management-catalog-create.md), tworzyć pakiety dostępu i zarządzać zatwierdzaniem dostępu do tych pakietów.

Na poniższym diagramie przedstawiono typowy cykl życia ładu dla użytkownika zewnętrznego, który uzyskuje dostęp do pakietu dostępu, który ma wygaśnięcie.

![Diagram cyklu zarządzania użytkownikami zewnętrznymi.](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>Samoobsługowy dostęp zewnętrzny

Aby umożliwić użytkownikom zewnętrznym żądanie dostępu, możesz uzyskać dostęp do pakietów za pomocą [usługi Azure AD my Access](../governance/entitlement-management-request-access.md) . Zasady określają, kto może zażądać pakietu dostępu. Należy określić, kto może zażądać pakietu dostępu:

* Określone [organizacje połączone](../governance/entitlement-management-organization.md)

* Wszystkie skonfigurowane połączone organizacje

* Wszyscy użytkownicy z dowolnej organizacji

* Użytkownicy lub członkowie-gość już w dzierżawie

### <a name="approvals"></a>Zatwierdzenia   
Pakiety dostępu mogą obejmować obowiązkowe zatwierdzanie dostępu. **Zawsze implementuj procesy zatwierdzania dla użytkowników zewnętrznych**. Zatwierdzenia mogą być zatwierdzeniem pojedynczego lub wieloetapowego. Zatwierdzenia są określane przez zasady. Jeśli zarówno użytkownicy wewnętrzni, jak i zewnętrzni muszą uzyskać dostęp do tego samego pakietu, prawdopodobnie skonfigurujesz różne zasady dostępu dla różnych kategorii połączonych organizacji i dla użytkowników wewnętrznych.

### <a name="expiration"></a>Wygaśnięcie  
Pakiety dostępu mogą zawierać datę wygaśnięcia. Dla wygaśnięcia można ustawić określony dzień lub dać użytkownikowi określoną liczbę dni w celu uzyskania dostępu. Gdy pakiet dostępu zostanie wygaśnie, a użytkownik nie ma innego dostępu, obiekt użytkownika-gościa B2B reprezentujący użytkownika może zostać usunięty lub zablokowany do logowania. Zalecamy wymuszenie wygaśnięcia pakietów dostępu dla użytkowników zewnętrznych. Nie wszystkie pakiety dostępu mają wygaśnięcia. W przypadku tych, które nie zapewniają kontroli dostępu.

### <a name="access-reviews"></a>Przeglądy dostępu

Pakiety dostępu mogą wymagać okresowych [przeglądów dostępu](../governance/manage-guest-access-with-access-reviews.md), które wymagają od właściciela pakietu lub wyznaczonego do ciągłego dostępu użytkowników. 

Przed rozpoczęciem konfigurowania przeglądu Ustal następujące kwestie.

* Którzy

   * Jakie są kryteria ciągłego dostępu?

   * Kogo są określeni recenzenci?

* Jak często mają być planowane przeglądy?

   * Wbudowane opcje obejmują co miesiąc, co kwartał, co rok lub co rok. 

   * Zalecamy co najmniej jeden raz dla pakietów, które obsługują dostęp zewnętrzny. 

 

> [!IMPORTANT]
> Przeglądy dostępu do pakietów dostępu są sprawdzane tylko w przypadku udzielenia dostępu przez Zarządzanie uprawnieniami. W związku z tym należy skonfigurować inne procesy w celu przejrzenia dostępu określonego użytkownikom zewnętrznym poza zarządzaniem prawami.

Aby uzyskać więcej informacji o przeglądach dostępu, zobacz [Planowanie wdrożenia przeglądów dostępu do usługi Azure AD](../governance/deploy-access-reviews.md).

## <a name="using-automation-in-entitlement-management"></a>Korzystanie z automatyzacji w zarządzaniu prawami

[Funkcje zarządzania prawami można wykonywać za pomocą Microsoft Graph](/graph/tutorial-access-package-api), w tym

* [Zarządzanie pakietami dostępu](/graph/api/resources/accesspackage?view=graph-rest-beta)

* [Zarządzanie recenzjami dostępu](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [Zarządzanie połączonymi organizacjami](/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [Zarządzaj ustawieniami zarządzania uprawnieniami](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>Zalecenia 

Zalecamy stosowanie praktyk związanych z zarządzaniem dostępem zewnętrznym.

**W przypadku projektów z co najmniej jednym partnerem biznesowym można [tworzyć i używać pakietów dostępu](../governance/entitlement-management-access-package-create.md) do dołączania i udostępniania użytkownikom tego partnera dostępu do zasobów**. 

* Jeśli masz już użytkowników B2B w katalogu, możesz również bezpośrednio przypisać je do odpowiednich pakietów dostępu.

* Można przypisać dostęp w [Azure Portal](../governance/entitlement-management-access-package-assignments.md)lub za pośrednictwem [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta).

**Użyj ustawień zarządzania tożsamościami, aby usunąć użytkowników z katalogu po wygaśnięciu pakietów dostępu**.

![Zrzut ekranu przedstawiający Konfigurowanie zarządzania cyklem życia użytkowników zewnętrznych.](media/secure-external-access/6-manage-external-lifecycle.png)

Te ustawienia mają zastosowanie tylko do użytkowników, którzy zostali dołączeni za poorednictwem zarządzania prawami.

**[Delegowanie zarządzania wykazami i uzyskiwania dostępu](../governance/entitlement-management-delegate.md) do właścicieli firmy, którzy mają więcej informacji na temat tego, kto powinien mieć dostęp**.

![Zrzut ekranu przedstawiający Konfigurowanie wykazu.](media/secure-external-access/6-catalog-management.png)

**[Wymuś wygaśnięcie pakietów dostępu](../governance/entitlement-management-access-package-lifecycle-policy.md) , do których użytkownicy zewnętrzni mają dostęp.**


![Zrzut ekranu przedstawiający Konfigurowanie wygaśnięcia pakietu dostępu.](media/secure-external-access/6-access-package-expiration.png)

* Jeśli znasz datę końcową pakietu dostępu opartego na projekcie, użyj wartości w dniu, aby ustawić określoną datę. 

* W przeciwnym razie zalecamy wygaśnięcie okresu ważności nie dłużej niż 365 dni, chyba że wiadomo, że jest to wieloletnie zaangażowanie.

* Zezwól użytkownikom na zwiększenie dostępu.

* Wymagaj zatwierdzenia do udzielenia rozszerzenia.

**[Wymuś przeglądy dostępu pakietów](../governance/manage-guest-access-with-access-reviews.md) , aby uniknąć niewłaściwego dostępu dla Gości.**

![Zrzut ekranu przedstawiający tworzenie nowego pakietu dostępu.](media/secure-external-access/6-new-access-package.png)

* Wymuś przeglądy kwartalnie.

* W przypadku projektów z uwzględnieniem zgodności należy ustawić recenzentów jako konkretnych recenzentów, a nie samoprzeglądów dla użytkowników zewnętrznych. Użytkownicy, którzy są dostępni do menedżerów pakietów, są dobrym miejscem do rozpoczęcia pracy dla recenzentów. 

* W przypadku mniej wrażliwych projektów, których samodzielne przeglądanie użytkowników zmniejszą obciążenie organizacji w celu usunięcia dostępu użytkowników, którzy nie są już w swojej organizacji domowej.

Aby uzyskać więcej informacji, zobacz temat [Zarządzanie dostępem użytkowników zewnętrznych w usłudze Azure AD upoważnienia](../governance/entitlement-management-external-users.md) 

### <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania zewnętrznego dostępu do zasobów. Zalecamy podejmowanie akcji w kolejności na liście.

1. [Określanie stan zabezpieczeń dla dostępu zewnętrznego](1-secure-access-posture.md)

2. [Odkryj bieżący stan](2-secure-access-current-state.md)

3. [Tworzenie planu ładu](3-secure-access-plan.md)

4. [Używanie grup do zabezpieczeń](4-secure-access-groups.md)

5. [Przejście do usługi Azure AD B2B](5-secure-access-b2b.md)

6. [Bezpieczny dostęp z zarządzaniem prawami](6-secure-access-entitlement-managment.md) (Jesteś tutaj).

7. [Bezpieczny dostęp przy użyciu zasad dostępu warunkowego](7-secure-access-conditional-access.md)

8. [Bezpieczny dostęp z etykietami czułości](8-secure-access-sensitivity-labels.md)

9. [Bezpieczny dostęp do usług Microsoft Teams, OneDrive i SharePoint](9-secure-access-teams-sharepoint.md)

 

