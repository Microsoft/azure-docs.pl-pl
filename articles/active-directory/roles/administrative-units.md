---
title: Jednostki administracyjne w Azure Active Directory | Microsoft Docs
description: Użyj jednostek administracyjnych, aby bardziej szczegółowo delegowania uprawnień w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fb24d0cd2714969b5a888b1036f524c4c062d76
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027622"
---
# <a name="administrative-units-in-azure-active-directory"></a>Jednostki administracyjne w Azure Active Directory

W tym artykule opisano jednostki administracyjne w Azure Active Directory (Azure AD). Jednostka administracyjna to zasób usługi Azure AD, który może być kontenerem dla innych zasobów usługi Azure AD. Jednostka administracyjna może zawierać tylko użytkowników i grupy.

Korzystając z jednostek administracyjnych, można przyznać uprawnienia administratora, które są ograniczone do działu, regionu lub innego segmentu zdefiniowanej organizacji. Jednostki administracyjne mogą służyć do delegowania uprawnień do administratorów regionalnych lub do ustawiania zasad na poziomie szczegółowym. Administrator konta użytkownika może na przykład zaktualizować informacje o profilu, zresetować hasła i przypisać licencje dla użytkowników tylko w ich jednostce administracyjnej.

Na przykład można delegować do specjalistów pomocy [technicznej w regionie, który](permissions-reference.md#helpdesk-administrator) jest ograniczony do zarządzania użytkownikami tylko w obszarze, który obsługuje.

## <a name="deployment-scenario"></a>Scenariusz wdrożenia

Może być przydatne, aby ograniczyć zakres administracyjny przy użyciu jednostek administracyjnych w organizacjach, które składają się z niezależnych oddziałów dowolnego rodzaju. Rozważmy przykład dużego University, który składa się z wielu szkół autonomicznych (Szkoła biznesowa, szkoły inżynieryjnej itp.). Każda szkoła ma zespół administratorów IT, którzy kontrolują dostęp, zarządzać użytkownikami i ustawiać zasady dla swojej szkoły. 

Administrator centralny może:

- Utwórz rolę z uprawnieniami administracyjnymi tylko dla użytkowników usługi Azure AD w jednostce administracyjnej szkoły biznesowej.
- Utwórz jednostkę administracyjną dla szkoły biznesowej.
- Wypełnij jednostkę administracyjną wyłącznie uczniami i personelem szkoły szkolnej.
- Dodaj zespół ds. biznesu IT do roli wraz z jego zakresem.

## <a name="license-requirements"></a>Wymagania licencyjne

Aby korzystać z jednostek administracyjnych, należy Azure Active Directory — wersja Premium licencję dla każdego administratora jednostki administracyjnej i Azure Active Directory — wersja Bezpłatna licencje dla członków jednostki administracyjnej. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure AD — wersja Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Zarządzanie jednostkami administracyjnymi

Jednostkami administracyjnymi można zarządzać za pomocą Azure Portal, poleceń cmdlet programu PowerShell i skryptów lub Microsoft Graph. Aby uzyskać więcej informacji, zobacz:

- [Tworzenie, usuwanie, zapełnianie i Dodawanie ról do jednostek administracyjnych](admin-units-manage.md): zawiera kompletne procedury postępowania.
- [Współpraca z jednostkami administracyjnymi](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true): obejmuje sposób pracy z jednostkami administracyjnymi przy użyciu programu PowerShell.
- [Obsługa grafu jednostki administracyjnej](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true): zawiera szczegółową dokumentację dotyczącą Microsoft Graph dla jednostek administracyjnych.

### <a name="plan-your-administrative-units"></a>Planowanie jednostek administracyjnych

Jednostki administracyjne umożliwiają logiczne grupowanie zasobów usługi Azure AD. Na przykład w przypadku organizacji, której dział IT jest rozproszeni globalnie, warto utworzyć jednostki administracyjne, które definiują te granice geograficzne. W innym scenariuszu, w którym organizacja wielonarodowy ma różne *organizacje podrzędne* , które są częściowo autonomiczne w swoich operacjach, jednostka administracyjna może reprezentować każdą podorganizację.

Kryteria, w których są tworzone jednostki administracyjne, podlegają unikatowym wymaganiom organizacji. Jednostki administracyjne są typowym sposobem definiowania struktury w ramach usług Microsoft 365. Zalecamy przygotowanie jednostek administracyjnych do ich używania w ramach usług Microsoft 365. Możesz uzyskać maksymalną wartość z jednostek administracyjnych, gdy można kojarzyć typowe zasoby w Microsoft 365 w ramach jednostki administracyjnej.

Aby utworzyć jednostki administracyjne w organizacji, możesz przejść przez następujące etapy:

1. **Początkowe** wdrożenie: organizacja rozpocznie tworzenie jednostek administracyjnych na podstawie kryteriów początkowych, a liczba jednostek administracyjnych zwiększy się, gdy kryteria zostaną ulepszone.
1. **Oczyszczanie** : po pomyślnym zdefiniowaniu kryteriów jednostki administracyjne, które nie są już wymagane, zostaną usunięte.
1. **Stabilizacja** : Struktura organizacyjna jest dobrze zdefiniowana i liczba jednostek administracyjnych nie zmienia się znacząco w krótkim czasie.

## <a name="currently-supported-scenarios"></a>Obecnie obsługiwane scenariusze

Jako Administrator globalny lub administrator ról uprzywilejowanych możesz użyć portalu usługi Azure AD, aby utworzyć jednostki administracyjne, dodać użytkowników jako członków jednostek administracyjnych, a następnie przypisać personelowi IT do ról administratora z zakresem jednostki administracyjnej. Administratorzy z zakresem administracyjnym mogą następnie użyć Centrum administracyjnego Microsoft 365, aby uzyskać podstawowe zarządzanie użytkownikami w swoich jednostkach administracyjnych.

Ponadto można dodać grupy jako członków jednostki administracyjnej. Administrator grupy z zakresem jednostki administracyjnej może zarządzać nimi przy użyciu programu PowerShell, Microsoft Graph i portalu usługi Azure AD.

W poniższych tabelach opisano bieżącą obsługę scenariuszy jednostek administracyjnych:

### <a name="administrative-unit-management"></a>Zarządzanie jednostkami administracyjnymi

| Uprawnienia |   Graph/PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365 | 
| -- | -- | -- | -- |
| Tworzenie i usuwanie jednostek administracyjnych   |    Obsługiwane    |   Obsługiwane   |    Nieobsługiwane | 
| Dodawanie i usuwanie członków jednostki administracyjnej osobno    |   Obsługiwane    |   Obsługiwane   |    Nieobsługiwane | 
| Zbiorcze dodawanie i usuwanie członków jednostek administracyjnych przy użyciu plików CSV   |    Nieobsługiwane     |  Obsługiwane   |    Brak planu do obsługi | 
| Przypisywanie administratorów z zakresem administracyjnym  |     Obsługiwane    |   Obsługiwane    |   Nieobsługiwane | 
| Dynamiczne dodawanie i usuwanie członków jednostki administracyjnej na podstawie atrybutów | Nieobsługiwane | Nieobsługiwane | Nieobsługiwane 

### <a name="user-management"></a>Zarządzanie użytkownikami

| Uprawnienia |   Graph/PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365 |
| -- | -- | -- | -- |
| Zarządzanie właściwościami użytkownika, hasłami i licencjami w zakresie jednostki administracyjnej   |    Obsługiwane     |  Obsługiwane   |   Obsługiwane |
| Blokowanie w zakresie jednostki administracyjnej i odblokowywanie logowania użytkowników    |   Obsługiwane   |    Obsługiwane   |    Obsługiwane |
| Administracyjne uwierzytelnianie wieloskładnikowe użytkownika w zakresie jednostki administracyjnej   |    Obsługiwane   |   Obsługiwane   |   Nieobsługiwane |

### <a name="group-management"></a>Zarządzanie grupami

| Uprawnienia |   Graph/PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365 |
| -- | -- | -- | -- |
| Zarządzanie właściwościami grupy i członkami w zakresie jednostki administracyjnej     |  Obsługiwane   |    Obsługiwane    |  Nieobsługiwane |
| Zarządzanie licencjonowaniem grupowym w zakresie jednostki administracyjnej   |    Obsługiwane  |    Obsługiwane   |   Nieobsługiwane |


Jednostki administracyjne stosują zakres tylko do uprawnień zarządzania. Nie uniemożliwiają członkom ani administratorom korzystania z ich [domyślnych uprawnień użytkownika](../fundamentals/users-default-permissions.md) do przeglądania innych użytkowników, grup lub zasobów poza jednostką administracyjną. W centrum administracyjnym Microsoft 365 użytkownicy spoza jednostek administracyjnych administratora z zakresem są odfiltrowany. Można jednak przeglądać innych użytkowników w portalu usługi Azure AD, programie PowerShell i innych usługach firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie jednostkami administracyjnymi](admin-units-manage.md)
- [Zarządzanie użytkownikami w jednostkach administracyjnych](admin-units-add-manage-users.md)
- [Zarządzaj grupami w jednostkach administracyjnych](admin-units-add-manage-groups.md)
- [Przypisywanie ról objętych zakresem do jednostki administracyjnej](admin-units-assign-roles.md)
