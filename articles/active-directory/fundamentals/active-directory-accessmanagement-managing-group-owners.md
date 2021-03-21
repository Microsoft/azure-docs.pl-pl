---
title: Dodawanie lub usuwanie właścicieli grup — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące dodawania lub usuwania właścicieli grup przy użyciu Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/11/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b255f64547c3bae56d31415dc94a751989ca1f45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504903"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Dodawanie lub usuwanie właścicieli grupy w Azure Active Directory
Grupy Azure Active Directory (Azure AD) należą do właścicieli grup i są przez nich zarządzane. Właściciele grup mogą być użytkownikami lub jednostkami usługi i mogą zarządzać grupą, w tym członkostwem. Tylko istniejący właściciele grup lub Administratorzy grup mogą przypisywać właścicieli grup. Właściciele grupy nie muszą być członkami grupy.

Gdy grupa nie ma właściciela, Administratorzy zarządzający grupami nadal mogą zarządzać tą grupą. Jest to zalecane dla każdej grupy, aby mieć co najmniej jednego właściciela. Gdy właściciele są przypisani do grupy, nie można usunąć ostatniego właściciela grupy. Przed usunięciem ostatniego właściciela z grupy upewnij się, że wybrano innego właściciela.

## <a name="add-an-owner-to-a-group"></a>Dodawanie właściciela do grupy
Poniżej znajdują się instrukcje dodawania użytkownika jako właściciela do grupy przy użyciu portalu usługi Azure AD. Aby dodać nazwę główną usługi jako właściciela grupy, postępuj zgodnie z instrukcjami, aby to zrobić przy użyciu [programu PowerShell](/powershell/module/Azuread/Add-AzureADGroupOwner).

### <a name="to-add-a-group-owner"></a>Aby dodać właściciela grupy
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **grupy**, a następnie wybierz grupę, dla której chcesz dodać właściciela (na przykład *zasady zarządzania urządzeniami przenośnymi — zachodni*).

3. Na stronie **Przegląd zasad zarządzania urządzeniami przenośnymi** wybierz pozycję **właściciele**.

    ![Zasady zarządzania urządzeniami przenośnymi — Strona przeglądu zachodniego z wyróżnioną opcją właściciele](media/active-directory-accessmanagement-managing-group-owners/add-owners-option-overview-blade.png)

4. Na stronie **zasady zarządzania urządzeniami przenośnymi-zachodni-właściciele** wybierz pozycję **Dodaj właścicieli**, a następnie wyszukaj i wybierz użytkownika, który będzie nowym właścicielem grupy, a następnie wybierz **pozycję Wybierz**.

    ![Zasady zarządzania urządzeniami przenośnymi — zachodnie strony właściciele z wyróżnioną opcją Dodaj właścicieli](media/active-directory-accessmanagement-managing-group-owners/add-owners-owners-blade.png)

    Po wybraniu nowego właściciela można odświeżyć stronę **właściciele** i zobaczyć nazwę dodaną do listy właścicieli.

## <a name="remove-an-owner-from-a-group"></a>Usuwanie właściciela z grupy
Usuń właściciela z grupy przy użyciu usługi Azure AD.

### <a name="to-remove-an-owner"></a>Aby usunąć właściciela
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **grupy**, a następnie wybierz grupę, dla której chcesz usunąć właściciela (na przykład *zasady zarządzania urządzeniami przenośnymi — zachodni*).

3. Na stronie **Przegląd zasad zarządzania urządzeniami przenośnymi** wybierz pozycję **właściciele**.

    ![Zasady zarządzania urządzeniami przenośnymi — Strona przeglądu zachodniego z wyróżnioną opcją Usuń właścicieli](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na stronie **zasady zarządzania urządzeniami przenośnymi-zachodni-właściciele** wybierz użytkownika, którego chcesz usunąć jako właściciela grupy, wybierz pozycję **Usuń** ze strony informacje o użytkowniku, a następnie wybierz pozycję **tak** , aby potwierdzić decyzję.

    ![Strona informacji użytkownika z wyróżnioną opcją usuwania](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Po usunięciu właściciela można wrócić do strony **właściciele** i zobaczyć, że nazwa została usunięta z listy właścicieli.

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie dostępem do zasobów za pomocą grup Azure Active Directory](active-directory-manage-groups.md)

- [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../enterprise-users/groups-settings-cmdlets.md)

- [Przypisywanie dostępu do zintegrowanej aplikacji SaaS przy użyciu grup](../enterprise-users/groups-saasapps.md)

- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../enterprise-users/groups-settings-v2-cmdlets.md)
