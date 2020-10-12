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
ms.openlocfilehash: 30b547ecd4ff76e650d7be837fa5bce03046da50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89318594"
---
# <a name="add-or-remove-group-owners-in-azure-active-directory"></a>Dodawanie lub usuwanie właścicieli grupy w Azure Active Directory
Grupy Azure Active Directory (Azure AD) należą do właścicieli grup i są przez nich zarządzane. Właściciele grup mogą być użytkownikami lub jednostkami usługi i mogą zarządzać grupą, w tym członkostwem. Tylko istniejący właściciele grup lub Administratorzy grup mogą przypisywać właścicieli grup. Właściciele grupy nie muszą być członkami grupy.

Gdy grupa nie ma właściciela, Administratorzy zarządzający grupami nadal mogą zarządzać tą grupą. Jest to zalecane dla każdej grupy, aby mieć co najmniej jednego właściciela. Gdy właściciele są przypisać do grupy, nie można usunąć ostatniego właściciela grupy. Przed usunięciem ostatniego właściciela z grupy upewnij się, że wybrano innego właściciela.

## <a name="add-an-owner-to-a-group"></a>Dodawanie właściciela do grupy
Poniżej znajdują się instrukcje dodawania użytkownika jako właściciela do grupy przy użyciu portalu usługi Azure AD. Aby dodać nazwę główną usługi jako właściciela grupy, postępuj zgodnie z instrukcjami, aby to zrobić przy użyciu [programu PowerShell](/powershell/module/Azuread/Add-AzureADGroupOwner?view=azureadps-2.0).

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

    ![Zasady zarządzania urządzeniami przenośnymi — Strona przeglądu zachodniego z wyróżnioną opcją właściciele](media/active-directory-accessmanagement-managing-group-owners/remove-owners-option-overview-blade.png)

4. Na stronie **zasady zarządzania urządzeniami przenośnymi-zachodni-właściciele** wybierz użytkownika, którego chcesz usunąć jako właściciela grupy, wybierz pozycję **Usuń** ze strony informacje o użytkowniku, a następnie wybierz pozycję **tak** , aby potwierdzić decyzję.

    ![Strona informacji użytkownika z wyróżnioną opcją usuwania](media/active-directory-accessmanagement-managing-group-owners/remove-owner-info-blade.png)

    Po usunięciu właściciela można wrócić do strony **właściciele** i zobaczyć, że nazwa została usunięta z listy właścicieli.

## <a name="next-steps"></a>Następne kroki
- [Zarządzanie dostępem do zasobów za pomocą grup Azure Active Directory](active-directory-manage-groups.md)

- [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-cmdlets.md)

- [Przypisywanie dostępu do zintegrowanej aplikacji SaaS przy użyciu grup](../users-groups-roles/groups-saasapps.md)

- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)

- [Polecenia cmdlet usługi Azure Active Directory służące do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-v2-cmdlets.md)