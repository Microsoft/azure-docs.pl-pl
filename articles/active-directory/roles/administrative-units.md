---
title: Jednostki administracyjne w Azure Active Directory | Microsoft Docs
description: Użyj jednostek administracyjnych, aby bardziej szczegółowo delegowania uprawnień w Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: e82be52cff824c69dd2fe4ea5e7e1c0d7489be1d
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740333"
---
# <a name="administrative-units-in-azure-active-directory"></a>Jednostki administracyjne w Azure Active Directory

W tym artykule opisano jednostki administracyjne w Azure Active Directory (Azure AD). Jednostka administracyjna to zasób usługi Azure AD, który może być kontenerem dla innych zasobów usługi Azure AD. Jednostka administracyjna może zawierać tylko użytkowników i grupy.

Jednostki administracyjne ograniczają uprawnienia w roli do dowolnej części zdefiniowanej organizacji. Można na przykład użyć jednostek administracyjnych, aby delegować rolę [administratora działu](permissions-reference.md#helpdesk-administrator) pomocy technicznej do specjalistów, aby mogli zarządzać użytkownikami tylko w regionie, w którym są one obsługiwane.

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

Jednostki administracyjne umożliwiają logiczne grupowanie zasobów usługi Azure AD. Organizacja, której dział IT jest rozproszeni globalnie, może utworzyć jednostki administracyjne, które definiują odpowiednie granice geograficzne. W innym scenariuszu, w którym organizacja globalna ma częściowo autonomiczne w swojej operacji, jednostki administracyjne mogą reprezentować podorganizacji.

Kryteria, w których są tworzone jednostki administracyjne, podlegają unikatowym wymaganiom organizacji. Jednostki administracyjne są typowym sposobem definiowania struktury w ramach usług Microsoft 365. Zalecamy przygotowanie jednostek administracyjnych do ich używania w ramach usług Microsoft 365. Możesz uzyskać maksymalną wartość z jednostek administracyjnych, gdy można kojarzyć typowe zasoby w Microsoft 365 w ramach jednostki administracyjnej.

Aby utworzyć jednostki administracyjne w organizacji, możesz przejść przez następujące etapy:

1. **Początkowe** wdrożenie: organizacja rozpocznie tworzenie jednostek administracyjnych na podstawie kryteriów początkowych, a liczba jednostek administracyjnych zwiększy się, gdy kryteria zostaną ulepszone.
1. **Oczyszczanie**: po zdefiniowaniu kryteriów jednostki administracyjne, które nie są już wymagane, zostaną usunięte.
1. **Stabilizacja**: Struktura organizacyjna jest zdefiniowana, a liczba jednostek administracyjnych nie będzie znacząco zmieniana w krótkim czasie.

## <a name="currently-supported-scenarios"></a>Obecnie obsługiwane scenariusze

Jako Administrator globalny lub administrator ról uprzywilejowanych możesz użyć portalu usługi Azure AD, aby:

- Utwórz jednostki administracyjne
- Dodawanie użytkowników i grup członków jednostek administracyjnych
- Przypisz personelowi IT do ról administratora z zakresem jednostek administracyjnych.

Administratorzy z zakresem administracyjnym mogą korzystać z centrum administracyjnego Microsoft 365, aby uzyskać podstawowe zarządzanie użytkownikami w ich jednostkach administracyjnych. Administrator grupy z zakresem jednostek administracyjnych może zarządzać grupami przy użyciu programu PowerShell, Microsoft Graph i Microsoft 365 centrów administracyjnych.

>[!Note]
>Tylko funkcje opisane w tej sekcji są dostępne w centrum administracyjnym Microsoft 365. Dla roli usługi Azure AD z zakresem jednostki administracyjnej nie są dostępne żadne funkcje na poziomie organizacji.

W poniższych sekcjach opisano bieżące wsparcie dla scenariuszy jednostek administracyjnych.

### <a name="administrative-unit-management"></a>Zarządzanie jednostkami administracyjnymi

| Uprawnienia |   Graph/PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365 |
| --- | --- | --- | --- |
| Tworzenie i usuwanie jednostek administracyjnych   |    Obsługiwane    |   Obsługiwane   |    Nieobsługiwane |
| Dodawanie i usuwanie członków jednostki administracyjnej osobno    |   Obsługiwane    |   Obsługiwane   |    Nieobsługiwane |
| Zbiorcze dodawanie i usuwanie członków jednostek administracyjnych przy użyciu plików CSV   |    Nieobsługiwane     |  Obsługiwane   |    Brak planu do obsługi |
| Przypisywanie administratorów z zakresem administracyjnym  |     Obsługiwane    |   Obsługiwane    |   Nieobsługiwane |
| Dynamiczne dodawanie i usuwanie członków jednostki administracyjnej na podstawie atrybutów | Nieobsługiwane | Nieobsługiwane | Nieobsługiwane

### <a name="user-management"></a>Zarządzanie użytkownikami

| Uprawnienia |   Graph/PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365 |
| --- | --- | --- | --- |
| Zarządzanie właściwościami użytkownika, hasłami i licencjami w zakresie jednostki administracyjnej   |    Obsługiwane     |  Obsługiwane   |   Obsługiwane |
| Blokowanie w zakresie jednostki administracyjnej i odblokowywanie logowania użytkowników    |   Obsługiwane   |    Obsługiwane   |    Obsługiwane |
| Administracyjne uwierzytelnianie wieloskładnikowe użytkownika w zakresie jednostki administracyjnej   |    Obsługiwane   |   Obsługiwane   |   Nieobsługiwane |

### <a name="group-management"></a>Zarządzanie grupami

| Uprawnienia |   Graph/PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365 |
| --- | --- | --- | --- |
| Zarządzanie właściwościami grupy i członkami w zakresie jednostki administracyjnej     |  Obsługiwane   |    Obsługiwane    |  Nieobsługiwane |
| Zarządzanie licencjonowaniem grupowym w zakresie jednostki administracyjnej   |    Obsługiwane  |    Obsługiwane   |   Nieobsługiwane |

Jednostki administracyjne stosują zakres tylko do uprawnień zarządzania. Nie uniemożliwiają członkom ani administratorom korzystania z ich [domyślnych uprawnień użytkownika](../fundamentals/users-default-permissions.md) do przeglądania innych użytkowników, grup lub zasobów poza jednostką administracyjną. W centrum administracyjnym Microsoft 365 użytkownicy spoza jednostek administracyjnych administratora z zakresem są odfiltrowany. Można jednak przeglądać innych użytkowników w portalu usługi Azure AD, programie PowerShell i innych usługach firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie jednostkami administracyjnymi](admin-units-manage.md)
- [Zarządzanie użytkownikami w jednostkach administracyjnych](admin-units-add-manage-users.md)
- [Zarządzaj grupami w jednostkach administracyjnych](admin-units-add-manage-groups.md)
- [Przypisywanie ról objętych zakresem do jednostki administracyjnej](admin-units-assign-roles.md)
