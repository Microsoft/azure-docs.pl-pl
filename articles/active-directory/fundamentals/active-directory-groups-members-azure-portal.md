---
title: Dodawanie lub usuwanie elementów członkowskich grupy — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące dodawania lub usuwania członków z grupy przy użyciu Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23b4076fe91d807fd67218ef9576a3bf1ff65f21
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604339"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Dodawanie lub usuwanie członków grupy przy użyciu Azure Active Directory
Za pomocą Azure Active Directory można nadal dodawać i usuwać członków grupy.

## <a name="to-add-group-members"></a>Aby dodać członków grupy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **grupy**.

3. Na stronie **grupy — wszystkie grupy** Wyszukaj i wybierz grupę, do której chcesz dodać element członkowski. W takim przypadku należy użyć wcześniej utworzonej grupy, **zasad zarządzania urządzeniami przenośnymi — zachodnie**.

    ![Grupy — Strona wszystkie grupy, wyróżniona nazwa grupy](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Na stronie przeglądu grupy **MDM policy - West** wybierz pozycję **Członkowie** w obszarze **Zarządzanie**.

    ![Zasady zarządzania urządzeniami przenośnymi — Strona przeglądu zachodniego, z wyróżnioną opcją członkowie](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Wybierz pozycję **Dodaj członków**, a następnie wyszukaj i wybierz każdy element członkowski, który chcesz dodać do grupy, a następnie wybierz **pozycję Wybierz**.

    Zostanie wyświetlony komunikat informujący o pomyślnym dodaniu elementów członkowskich.

    ![Strona dodawania członków z wyszukanymi elementami członkowskimi](media/active-directory-groups-members-azure-portal/update-members.png)

6. Odśwież ekran, aby wyświetlić wszystkie nazwy elementów członkowskich dodanych do grupy.

## <a name="to-remove-group-members"></a>Aby usunąć członków grupy

1. Na stronie **grupy — wszystkie grupy** Wyszukaj i wybierz grupę, z której chcesz usunąć członka. Ponownie użyjemy **zasad zarządzania urządzeniami przenośnymi — zachód**.

2. Wybierz **elementy członkowskie** z obszaru **Zarządzanie** , Wyszukaj i wybierz nazwę elementu członkowskiego do usunięcia, a następnie wybierz pozycję **Usuń**.

    ![Strona informacji o elemencie członkowskim z opcją usunięcia](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Edytowanie ustawień grupy](active-directory-groups-settings-azure-portal.md)

- [Zarządzanie dostępem do zasobów przy użyciu grup](active-directory-manage-groups.md)

- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-create-rule.md)

- [Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
