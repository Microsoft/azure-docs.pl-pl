---
title: Zarządzanie jednostkami administracyjnymi (wersja zapoznawcza) — azure ad | Dokumenty firmy Microsoft
description: Używanie jednostek administracyjnych do bardziej szczegółowego delegowania uprawnień w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406456"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Zarządzanie jednostkami administracyjnymi w usłudze Azure Active Directory (wersja zapoznawcza)

W tym artykule opisano jednostki administracyjne w usłudze Azure Active Directory (Azure AD). Jednostka administracyjna to zasób usługi Azure AD, który może być kontenerem dla innych zasobów usługi Azure AD. W tej wersji zapoznawczej jednostka administracyjna może zawierać tylko użytkowników i grupy.

Jednostki administracyjne umożliwiają udzielanie uprawnień administratora, które są ograniczone do departamentu, regionu lub innego segmentu organizacji, który definiujesz. Jednostek administracyjnych można użyć do delegowania uprawnień do administratorów regionalnych lub do ustawiania zasad na poziomie szczegółowości. Na przykład administrator konta użytkownika może aktualizować informacje o profilu, resetować hasła i przypisywać licencje użytkownikom tylko w jednostce administracyjnej.

 Na przykład delegowanie do specjalistów pomocy technicznej rola [Administratora helpdesk](directory-assign-admin-roles.md#helpdesk-administrator) ograniczone do zarządzania tylko użytkowników w regionie, który obsługują.

## <a name="deployment-scenario"></a>Scenariusz wdrożenia

Ograniczenie zakresu administracyjnego przy użyciu jednostek administracyjnych może być przydatne w organizacjach, które tworzą niezależne działy dowolnego rodzaju. Rozważmy przykład dużej uczelni, która składa się z wielu autonomicznych szkół (School of Business, School of Engineering i tak dalej), że każdy ma zespół administratorów IT, którzy kontrolują dostęp, zarządzają użytkownikami i ustalają zasady dla swojej szkoły. Centralny administrator może:

- Tworzenie roli z uprawnieniami administracyjnymi tylko dla użytkowników usługi Azure AD w jednostce administracyjnej szkoły biznesowej
- Tworzenie jednostki administracyjnej dla Szkoły Biznesu
- Zapełnij jednostkę administracyjną tylko uczniami szkół biznesowych i pracownikami
- Dodawanie zespołu IT szkoły biznesowej do roli z ich zakresem

## <a name="license-requirements"></a>Wymagania licencyjne

Korzystanie z jednostek administracyjnych wymaga licencji usługi Azure Active Directory Premium dla każdego administratora jednostki administracyjnej i licencji bezpłatnej usługi Azure Active Directory dla członków jednostki administracyjnej. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do usługi Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).

## <a name="manage-administrative-units"></a>Zarządzanie jednostkami administracyjnymi

W tej wersji w wersji zapoznawczej można zarządzać jednostkami administracyjnymi przy użyciu portalu Azure Portal, poleceń cmdlet i skryptów programu PowerShell lub programu Microsoft Graph. Szczegółowe informacje można znaleźć w naszej dokumentacji:

- [Tworzenie, usuwanie, wypełnianie i dodawanie ról do jednostek administracyjnych:](roles-admin-units-manage.md)Kończenie procedur inicjowania
- [Praca z jednostkami administracyjnymi:](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)jak pracować z jednostkami administracyjnymi przy użyciu programu PowerShell
- [Obsługa wykresu jednostki administracyjnej:](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta)Szczegółowa dokumentacja dotycząca programu Microsoft Graph dla jednostek administracyjnych.

### <a name="planning-your-administrative-units"></a>Planowanie jednostek administracyjnych

Jednostki administracyjne mogą być używane do logicznego grupowanie zasobów usługi Azure AD. Na przykład dla organizacji, której dział IT jest rozproszony globalnie, może mieć sens tworzenie jednostek administracyjnych, które definiują te granice geograficzne. W innym scenariuszu, w którym organizacja wielonarodowa ma różne "podorganizacje", które są półautonomicznie w operacjach, każda podorganizatorka może być reprezentowana przez jednostkę administracyjną.

Kryteria tworzenia jednostek administracyjnych będą oparte na unikatowych wymaganiach organizacji. Jednostki administracyjne są powszechnym sposobem definiowania struktury w usługach M365. Zaleca się przygotowanie jednostek administracyjnych z ich użyciem w usługach M365. Maksymalną wartość jednostek administracyjnych można uzyskać, gdy można skojarzyć typowe zasoby w m365 w ramach jednostki administracyjnej.

Można oczekiwać, że utworzenie jednostek administracyjnych w organizacji przejdzie przez następujące etapy:

1. Przyjęcie początkowe: Organizacja rozpocznie tworzenie jednostek administracyjnych na podstawie początkowych kryteriów, a liczba jednostek administracyjnych wzrośnie w miarę udoskonalania kryteriów.
1. Przycinanie: Gdy kryteria są dobrze zdefiniowane, jednostki administracyjne, które nie są już wymagane, zostaną usunięte.
1. Stabilizacja: Struktura organizacyjna jest dobrze zdefiniowana, a liczba jednostek administracyjnych nie zmieni się znacząco w krótkim czasie.

## <a name="currently-supported-scenarios"></a>Obecnie obsługiwane scenariusze

Administratorzy globalni lub administratorzy ról uprzywilejowanych mogą używać portalu usługi Azure AD do tworzenia jednostek administracyjnych, dodawania użytkowników jako członków jednostek administracyjnych, a następnie przypisywania pracowników DZIAŁU IT do ról administratora o zakresie jednostki administracyjnej. Administratorzy jednostek administracyjnych mogą następnie używać portalu usługi Office 365 do podstawowego zarządzania użytkownikami w ich jednostkach administracyjnych.

Ponadto grupy można dodać jako członków jednostki administracyjnej, a administrator grupy o zakresie jednostki administracyjnej może zarządzać nimi za pomocą programu PowerShell, programu Microsoft Graph i portalu usługi Azure AD.

W poniższej tabeli opisano bieżącą obsługę scenariuszy jednostek administracyjnych.

### <a name="administrative-unit-management"></a>Zarządzanie jednostkami administracyjnymi

Uprawnienia |   Wykres MS/Program PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365
----------- | ----------------------- | --------------- | -----------------
Tworzenie i usuwanie jednostek administracyjnych   |    Obsługiwane    |   Obsługiwane   |    Nieobsługiwane
Dodawanie i usuwanie członków jednostki administracyjnej indywidualnie    |   Obsługiwane    |   Obsługiwane   |    Nieobsługiwane
Zbiorcze dodawanie i usuwanie członków jednostki administracyjnej przy użyciu pliku csv   |    Nieobsługiwane     |  Obsługiwane   |    Brak planu wsparcia
Przypisywanie administratorów jednostek administracyjnych  |     Obsługiwane    |   Obsługiwane    |   Nieobsługiwane
Dynamiczne dodawanie i usuwanie elementów członkowskich UA na podstawie atrybutów | Nieobsługiwane | Nieobsługiwane | Nieobsługiwane

### <a name="user-management"></a>Zarządzanie użytkownikami

Uprawnienia |   Wykres MS/Program PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365
----------- | ----------------------- | --------------- | -----------------
zarządzanie właściwościami użytkowników, hasłami, licencjami w zakresie jednostki administracyjnej   |    Obsługiwane     |  Obsługiwane   |   Obsługiwane
blokowanie i odblokowywanie logów użytkowników w zakresie jednostki administracyjnej    |   Obsługiwane   |    Obsługiwane   |    Obsługiwane
zarządzanie poświadczeniami usługi MFA użytkownika o zakresie jednostki administracyjnej   |    Obsługiwane   |   Obsługiwane   |   Nieobsługiwane

### <a name="group-management"></a>Zarządzanie grupami

Uprawnienia |   Wykres MS/Program PowerShell   | Portal usługi Azure AD | Centrum administracyjne platformy Microsoft 365
----------- | ----------------------- | --------------- | -----------------
zarządzanie właściwościami i członkami grupy w zakresie jednostki administracyjnej     |  Obsługiwane   |    Obsługiwane    |  Nieobsługiwane
zarządzanie licencjonowaniem grupowym w zakresie jednostki administracyjnej   |    Obsługiwane  |    Obsługiwane   |   Nieobsługiwane

> [!NOTE]
>
> Administratorzy z zakresem jednostki administracyjnej nie mogą zarządzać regułami członkostwa w grupach dynamicznych.

Jednostki administracyjne stosują zakres tylko do uprawnień zarządzania. Nie uniemożliwiają one członkom ani administratorom używania [domyślnych uprawnień użytkownika](../fundamentals/users-default-permissions.md) do przeglądania innych użytkowników, grup lub zasobów poza jednostką administracyjną. W portalu usługi Office 365 użytkownicy spoza jednostek administracyjnych administratora o określonym zakresie są odfiltrowywały, ale można przeglądać innych użytkowników w portalu usługi Azure AD, programie PowerShell i innych usługach firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie aus](roles-admin-units-manage.md)
- [Zarządzanie użytkownikami w aus](roles-admin-units-add-manage-users.md)
- [Zarządzanie grupami w grupach AU](roles-admin-units-add-manage-groups.md)
- [Przypisywanie ról o określonym zakresie do ua](roles-admin-units-assign-roles.md)