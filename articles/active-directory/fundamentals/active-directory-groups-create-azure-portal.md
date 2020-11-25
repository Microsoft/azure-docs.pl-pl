---
title: Tworzenie grupy podstawowej i dodawanie członków — Azure Active Directory | Microsoft Docs
description: Instrukcje tworzenia grupy podstawowej za pomocą usługi Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/05/2020
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e4533334204a3a1cfd46ff27b04ff0c05350dfc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973919"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Tworzenie grupy podstawowej i dodawanie członków przy użyciu usługi Azure Active Directory
Grupę podstawową można utworzyć przy użyciu portalu usługi Azure Active Directory (Azure AD). Na potrzeby tego artykułu grupa podstawowa jest dodawana do pojedynczego zasobu przez właściciela zasobu (administratora) i zawiera określonych członków (pracowników), którzy potrzebują dostępu do tego zasobu. Aby uzyskać informacje o bardziej złożonych scenariuszach, w tym dynamicznym członkostwie i tworzeniu reguł, zobacz [dokumentację zarządzania użytkownikami w usłudze Azure Active Directory](../enterprise-users/index.yml).

## <a name="group-and-membership-types"></a>Typy grup i członkostw
Istnieje kilka typów grup i członkostw. Poniższe informacje wyjaśniają każdą grupę i typ członkostwa oraz ich znaczenie, aby ułatwić podjęcie decyzji o opcjach, które mają być używane podczas tworzenia grupy.

### <a name="group-types"></a>Typy grup:
- **Bezpieczeństwo**. Używany do zarządzania dostępem członków i komputerów do udostępnionych zasobów dla grupy użytkowników. Na przykład można utworzyć grupę zabezpieczeń dla konkretnych zasad zabezpieczeń. W ten sposób można jednocześnie udzielić zestawu uprawnień wszystkim członkom zamiast udzielania uprawnień każdemu członkowi osobno. Grupa zabezpieczeń może zawierać użytkowników, urządzenia, grupy i jednostki usługi jako swoich członków i użytkowników oraz ich jednostki usługi. Aby uzyskać więcej informacji na temat zarządzania dostępem do zasobów, zobacz [Zarządzanie dostępem do zasobów przy użyciu grup usługi Azure Active Directory](active-directory-manage-groups.md).
- **Microsoft 365**. Zapewnia możliwości współpracy przez udostępnienie członkom współużytkowanej skrzynki pocztowej, kalendarza, plików, witryny programu SharePoint i innych. Ta opcja umożliwia również przyznanie osobom spoza organizacji dostępu do grupy. Grupa Microsoft 365 może mieć tylko użytkowników jako członków. Zarówno użytkownicy, jak i podmioty usługi mogą być właścicielami grupy Microsoft 365. Aby uzyskać więcej informacji na temat grup Microsoft 365, Zobacz więcej informacji na [temat Microsoft 365 grup](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2).

### <a name="membership-types"></a>Typy członkostwa:
- **Pisywany.** Umożliwia dodanie do grupy określonych użytkowników z unikatowymi uprawnieniami. Na potrzeby tego artykułu użyjemy tej opcji.
- **Użytkownik dynamiczny.** Umożliwia używanie reguł członkostwa dynamicznego do automatycznego dodawania i usuwania członków. Jeśli atrybuty członka zmienią się, system sprawdzi reguły grupy dynamicznej w katalogu, aby określić, czy członek spełnia wymagania reguły (zostanie dodany), czy też nie spełnia już wymagań reguł (zostanie usunięty).
- **Urządzenie dynamiczne.** Umożliwia korzystanie z reguł grupy dynamicznej do automatycznego dodawania i usuwania urządzeń. Jeśli atrybuty urządzenia zmienią się, system sprawdzi reguły grupy dynamicznej w katalogu, aby określić, czy urządzenie spełnia wymagania reguły (zostanie dodane), czy też nie spełnia już wymagań reguł (zostanie usunięte).

    > [!IMPORTANT]
    > Grupę dynamiczną można utworzyć dla urządzeń lub użytkowników, ale nie dla obu tych elementów. Ponadto nie można utworzyć grupy urządzeń na podstawie atrybutów właścicieli urządzeń. Reguły członkostwa urządzenia mogą przywoływać tylko atrybuty urządzeń. Aby uzyskać więcej informacji na temat tworzenia grupy dynamicznej dla użytkowników i urządzeń, zobacz [Tworzenie grupy dynamicznej i sprawdzanie stanu](../enterprise-users/groups-create-rule.md)

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

1. Wybierz pozycję **Utwórz**. Grupa zostanie utworzona i będzie gotowa na dodanie członków.

1. Wybierz obszar **Elementy członkowskie** na stronie **Grupy**, a następnie rozpocznij wyszukiwanie członków do dodania do tej grupy na stronie **Wybieranie elementów członkowskich**.

    ![Wybieranie członków grupy w ramach procesu tworzenia grupy](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

1. Po zakończeniu dodawania członków wybierz pozycję **Wybierz**.

    Strona **Przegląd grupy** zostanie zaktualizowana i będzie wyświetlać liczbę członków dodanych teraz do grupy.

    ![Strona Przegląd grupy z wyróżnioną liczbą członków](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="turn-on-or-off-group-welcome-email"></a>Włącz lub Wyłącz powitalną wiadomość e-mail

Po utworzeniu nowej grupy Microsoft 365, niezależnie od tego, czy jest to dynamiczny czy statyczny, powiadomienie powitalne jest wysyłane do wszystkich użytkowników, którzy są dodawani do grupy. Po zmianie atrybutów użytkownika lub urządzenia wszystkie reguły grupy dynamicznej w organizacji są przetwarzane pod kątem potencjalnych zmian członkostwa. Dodani użytkownicy otrzymują również powiadomienie powitalne. To zachowanie można wyłączyć w programie [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem do aplikacji SaaS przy użyciu grup](../enterprise-users/groups-saasapps.md)
- [Zarządzanie grupami przy użyciu poleceń programu PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)