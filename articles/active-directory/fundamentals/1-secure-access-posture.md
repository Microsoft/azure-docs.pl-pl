---
title: Określ stan zabezpieczeń w celu współpracy zewnętrznej z Azure Active Directory
description: Przed wykonaniem planu zabezpieczeń dostępu zewnętrznego należy określić, co chcesz osiągnąć.
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
ms.openlocfilehash: e6814cafcf6dafa6f007bdd9d3623d30ef079084
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222449"
---
# <a name="determine-your-security-posture-for-external-access"></a>Określanie stan zabezpieczeń dla dostępu zewnętrznego 

W miarę rozważania zarządzania dostępem zewnętrznym należy ocenić potrzeby w zakresie zabezpieczeń i współpracy w całej organizacji oraz w poszczególnych scenariuszach. Na poziomie organizacji należy wziąć pod uwagę ilość kontroli, którą potrzebuje Twój zespół IT, aby korzystać z codziennej współpracy. Organizacje w branżach objętych regulacją mogą wymagać większej kontroli IT. Na przykład wykonawca obrony może być zobowiązany do pozytywnego identyfikowania i dokumentowania każdego użytkownika zewnętrznego, dostępu do nich oraz usuwania dostępu. To wymaganie może dotyczyć całego dostępu lub w określonych scenariuszach lub obciążeniach. Na drugim końcu spektrum firma konsultingowa może ogólnie zezwolić użytkownikom końcowym na określenie użytkowników zewnętrznych, którzy potrzebują współpracy, w ramach pewnych szyn IT. 

![I kontrola nad użytkownikami końcowymi](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> Zbyt ścisła kontrola nad współpracą może prowadzić do wyższych budżetów IT, zmniejszonej produktywności i opóźnionych wyników roboczych. Gdy oficjalne kanały współpracy są postrzegane jako zbyt uciążliwe, użytkownicy końcowi zamierzają przeanalizować systemy pod kątem ich wykonywania, aby na przykład wysłać pocztą e-mail niezabezpieczone dokumenty.

## <a name="think-in-terms-of-scenarios"></a>Pomyśl o scenariuszach

W wielu przypadkach może delegować dostęp do partnerów, co najmniej w niektórych scenariuszach, a jednocześnie zapewnia szyny ochronne dla bezpieczeństwa. Szyny IT Guard mogą pomóc zapewnić bezpieczeństwo własności intelektualnej, a jednocześnie poddanie pracownikom współpracy z partnerami w celu wykonania pracy.

Rozważając scenariusze w organizacji, należy ocenić potrzebę pracownikom i partnerom biznesowym dostęp do zasobów. Bank może mieć wymagania dotyczące zgodności, które ograniczają dostęp do określonych zasobów, takich jak informacje o koncie użytkownika, do niewielkiej grupy pracowników wewnętrznych. Natomiast ten sam Bank może umożliwić delegowany dostęp partnerom pracującym nad kampanią marketingową.

![Continuum ładu na scenariusz](media\secure-external-access\1-scenarios.png)

W każdym scenariuszu należy rozważyć 

* czułość informacji narażonych na ryzyko

* bez względu na to, czy chcesz ograniczyć liczbę partnerów, którzy mogą zobaczyć inni użytkownicy

* koszt naruszenia, a waga scentralizowanej kontroli i użytkowników końcowych

 Możesz również zacząć od centralnie zarządzanych formantów w celu spełnienia celów zgodności i delegowania kontroli do użytkowników końcowych w czasie. Wszystkie modele zarządzania dostępem mogą jednocześnie współistnieć w organizacji. 

Korzystanie z [poświadczeń zarządzanych przez partnera](../external-identities/what-is-b2b.md) zapewnia swojej organizacji zasadniczy sygnał, który kończy dostęp do zasobów, gdy użytkownik zewnętrzny utraci dostęp do zasobów swojej firmy.

## <a name="goals-of-securing-external-access"></a>Cele zabezpieczania dostępu zewnętrznego

Cele związane z zarządzanym i delegowanym dostępem różnią się.

**Główne cele związane z dostępem podlegają:**

* Poznaj cele ładu, regulacji i zgodności (GRC). 

* Ścisła kontrola dostępu do partnerów i partnerów, którzy mogą widzieć użytkowników, grup i innych partnerów.

**Główne cele delegowania dostępu to:**

* Umożliwienie właścicielom firmy zarządzania, którzy współpracują z usługą, w ramach ograniczeń IT.

* Umożliwienie partnerom biznesowym żądania dostępu na podstawie reguł zdefiniowanych przez właścicieli firmy.

W zależności od przyjętej organizacji i scenariuszy należy: 

* **Kontroluj dostęp do aplikacji, danych i zawartości**. Można to zrobić za pomocą różnych metod, w zależności od wersji [usługi Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) i [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans). 

* **Zmniejszenie podatności na ataki**. [Zarządzanie tożsamościami uprzywilejowanymi](../privileged-identity-management/pim-configure.md), [Ochrona przed utratą danych (DLP)](https://docs.microsoft.com/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) i [funkcje szyfrowania](https://docs.microsoft.com/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) zmniejszają powierzchnię ataku.

* **Regularnie sprawdzaj aktywność i dziennik inspekcji, aby potwierdzić zgodność**. Może delegować decyzje dotyczące dostępu do właścicieli firmy poprzez zarządzanie prawami, a przeglądy dostępu umożliwiają okresowe Potwierdzanie ciągłego dostępu. Automatyczna Klasyfikacja danych z etykietami czułości ułatwia automatyzację szyfrowania poufnej zawartości, co ułatwia użytkownikom końcowym pracę.

## <a name="next-steps"></a>Następne kroki 

Zapoznaj się z następującymi artykułami dotyczącymi zabezpieczania zewnętrznego dostępu do zasobów. Zalecamy podejmowanie akcji w kolejności na liście.

1. [Określ stan zabezpieczeń dla dostępu zewnętrznego](1-secure-access-posture.md) (Jesteś tutaj).

2. [Odkryj bieżący stan](2-secure-access-current-state.md)

3. [Tworzenie planu ładu](3-secure-access-plan.md)

4. [Używanie grup do zabezpieczeń](4-secure-access-groups.md)

5. [Przejście do usługi Azure AD B2B](5-secure-access-b2b.md)

6. [Bezpieczny dostęp z zarządzaniem prawami](6-secure-access-entitlement-managment.md)

7. [Bezpieczny dostęp przy użyciu zasad dostępu warunkowego](7-secure-access-conditional-access.md)

8. [Bezpieczny dostęp z etykietami czułości](8-secure-access-sensitivity-labels.md)

9. [Bezpieczny dostęp do usług Microsoft Teams, OneDrive i SharePoint](9-secure-access-teams-sharepoint.md)
 

 
