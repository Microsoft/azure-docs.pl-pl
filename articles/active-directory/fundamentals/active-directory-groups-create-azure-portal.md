---
title: Tworzenie grupy podstawowej i dodawanie członków — Azure Active Directory | Microsoft Docs
description: Instrukcje tworzenia grupy podstawowej za pomocą usługi Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85e00c360fb8fa279ec836deaf4e9b3b7e2efdb4
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84484443"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory
Grupę podstawową można utworzyć przy użyciu portalu usługi Azure Active Directory (Azure AD). Na potrzeby tego artykułu grupa podstawowa jest dodawana do pojedynczego zasobu przez właściciela zasobu (administratora) i zawiera określonych członków (pracowników), którzy potrzebują dostępu do tego zasobu. Aby uzyskać informacje o bardziej złożonych scenariuszach, w tym dynamicznym członkostwie i tworzeniu reguł, zobacz [dokumentację zarządzania użytkownikami w usłudze Azure Active Directory](../users-groups-roles/index.yml).

## <a name="group-and-membership-types"></a>Typy grup i członkostw
Istnieje kilka typów grup i członkostw. Poniższe informacje wyjaśniają każdą grupę i typ członkostwa oraz ich znaczenie, aby ułatwić podjęcie decyzji o opcjach, które mają być używane podczas tworzenia grupy.

### <a name="group-types"></a>Typy grup:
- **Zabezpieczenia**. Używany do zarządzania dostępem członków i komputerów do udostępnionych zasobów dla grupy użytkowników. Na przykład można utworzyć grupę zabezpieczeń dla konkretnych zasad zabezpieczeń. W ten sposób można jednocześnie udzielić zestawu uprawnień wszystkim członkom zamiast udzielania uprawnień każdemu członkowi osobno. Aby uzyskać więcej informacji na temat zarządzania dostępem do zasobów, zobacz [Zarządzanie dostępem do zasobów za pomocą Azure Active Directory groupsManage dostęp do zasobów przy użyciu grup Azure Active Directory](active-directory-manage-groups.md).
- **Pakiet Office 365**. Zapewnia możliwości współpracy przez udostępnienie członkom współużytkowanej skrzynki pocztowej, kalendarza, plików, witryny programu SharePoint i innych. Ta opcja umożliwia również przyznanie osobom spoza organizacji dostępu do grupy. Aby uzyskać więcej informacji na temat grup usługi Office 365, zobacz [Więcej informacji na temat grup usługi Office 365](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

### <a name="membership-types"></a>Typy członkostwa:
- **Pisywany.** Umożliwia dodanie do grupy określonych użytkowników z unikatowymi uprawnieniami. Na potrzeby tego artykułu użyjemy tej opcji.
- **Użytkownik dynamiczny.** Umożliwia używanie reguł członkostwa dynamicznego do automatycznego dodawania i usuwania członków. Jeśli atrybuty członka zmienią się, system sprawdzi reguły grupy dynamicznej w katalogu, aby określić, czy członek spełnia wymagania reguły (zostanie dodany), czy też nie spełnia już wymagań reguł (zostanie usunięty).
- **Urządzenie dynamiczne.** Umożliwia korzystanie z reguł grupy dynamicznej do automatycznego dodawania i usuwania urządzeń. Jeśli atrybuty urządzenia zmienią się, system sprawdzi reguły grupy dynamicznej w katalogu, aby określić, czy urządzenie spełnia wymagania reguły (zostanie dodane), czy też nie spełnia już wymagań reguł (zostanie usunięte).

    > [!IMPORTANT]
    > Grupę dynamiczną można utworzyć dla urządzeń lub użytkowników, ale nie dla obu tych elementów. Ponadto nie można utworzyć grupy urządzeń na podstawie atrybutów właścicieli urządzeń. Reguły członkostwa urządzenia mogą przywoływać tylko atrybuty urządzeń. Aby uzyskać więcej informacji na temat tworzenia grupy dynamicznej dla użytkowników i urządzeń, zobacz [Tworzenie grupy dynamicznej i sprawdzanie stanu](../users-groups-roles/groups-create-rule.md)

## <a name="create-a-basic-group-and-add-members"></a>Tworzenie grupy podstawowej i dodawanie członków
Istnieje możliwość jednoczesnego utworzenia grupy podstawowej i dodania członków. Aby utworzyć grupę podstawową i dodać członków, użyj następującej procedury:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

1. Wyszukaj i wybierz pozycję **Azure Active Directory**.

1. Na stronie **Active Directory** wybierz pozycję **grupy** , a następnie wybierz pozycję **Nowa grupa**.

    ![Strona usługi Azure AD z pokazywaniem grup](media/active-directory-groups-create-azure-portal/group-full-screen.png)

1. Zostanie wyświetlone okienko **Nowa grupa** i konieczne będzie wypełnienie wymaganych informacji.

    ![Strona nowej grupy wypełniona za pomocą informacji z przykładu](media/active-directory-groups-create-azure-portal/new-group-blade.png)

1. Wybierz wstępnie zdefiniowany **Typ grupy**. Aby uzyskać więcej informacji na temat typów grup, zobacz [typy grup i członkostw](#group-types).

1. Utwórz i Dodaj **nazwę grupy.** Wybierz nazwę, która będzie zapamiętać, i która ma sens dla grupy. Sprawdzenie zostanie wykonane w celu ustalenia, czy nazwa jest już używana przez inną grupę. Jeśli nazwa jest już używana, aby uniknąć zduplikowanych nazw, zostanie wyświetlony monit o zmianę nazwy grupy.

1. Dodaj **adres E-mail grupy** dla grupy lub Zachowaj adres e-mail, który jest wypełniany automatycznie.

1. **Opis grupy.** Do grupy możesz dodać opcjonalny opis.

1. Wybierz wstępnie zdefiniowany **Typ członkostwa (wymagane).** Aby uzyskać więcej informacji na temat typów członkostwa, zobacz [typy grup i członkostw](#membership-types).

1. Wybierz przycisk **Utwórz**. Grupa zostanie utworzona i będzie gotowa na dodanie członków.

1. Wybierz obszar **Elementy członkowskie** na stronie **Grupy**, a następnie rozpocznij wyszukiwanie członków do dodania do tej grupy na stronie **Wybieranie elementów członkowskich**.

    ![Wybieranie członków grupy w ramach procesu tworzenia grupy](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Po zakończeniu dodawania członków wybierz pozycję **Wybierz**.

    Strona **Przegląd grupy** zostanie zaktualizowana i będzie wyświetlać liczbę członków dodanych teraz do grupy.

    ![Strona Przegląd grupy z wyróżnioną liczbą członków](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-group-welcome-email"></a>Włącz lub Wyłącz powitalną wiadomość e-mail

Po utworzeniu nowej grupy pakietu Office 365, niezależnie od tego, czy jest to dynamiczny czy statyczny, powiadomienie powitalne jest wysyłane do wszystkich użytkowników, którzy są dodawani do grupy. Po zmianie atrybutów użytkownika lub urządzenia wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem potencjalnych zmian członkostwa. Dodani użytkownicy otrzymują również powiadomienie powitalne. To zachowanie można wyłączyć w programie [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem do aplikacji SaaS przy użyciu grup](../users-groups-roles/groups-saasapps.md)
- [Zarządzanie grupami przy użyciu poleceń programu PowerShell](../users-groups-roles/groups-settings-v2-cmdlets.md)

