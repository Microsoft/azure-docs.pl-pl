---
title: Edytuj informacje o grupie — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące edytowania informacji o grupach przy użyciu Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e9b38208526ba83e0a0e31e2e139e340a3ee511
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87797529"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Edytuj informacje o grupie za pomocą Azure Active Directory

Za pomocą Azure Active Directory (Azure AD) można edytować ustawienia grupy, w tym aktualizując jej nazwę, opis lub typ członkostwa.

## <a name="to-edit-your-group-settings"></a>Aby edytować ustawienia grupy
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **grupy**.

    Zostanie wyświetlona strona **grupy — wszystkie grupy** , w której wyświetlane są wszystkie aktywne grupy.

3. Na stronie **grupy — wszystkie grupy** wpisz dowolną część nazwy grupy, którą można znaleźć w polu **wyszukiwania** . Na potrzeby tego artykułu wyszukiwana jest Grupa **zasad zarządzania urządzeniami przenośnymi** .

    Wyniki wyszukiwania są wyświetlane w polu **wyszukiwania** , co pozwala aktualizować w miarę wpisywania więcej znaków.

    ![Strona wszystkie grupy z tekstem wyszukiwania w polu wyszukiwania](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Wybierz pozycję **zasady grupy MDM — zachód**, a następnie wybierz pozycję **Właściwości** w obszarze **Zarządzaj** .

    ![Strona przeglądu grupy z wyróżnioną opcją elementu członkowskiego i informacjami](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Zaktualizuj ogólne informacje o **ustawieniach** , w tym:

    ![Ustawienia właściwości dla grupy](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Nazwa grupy.** Edytuj nazwę istniejącej grupy.
    
    - **Opis grupy.** Edytuj opis istniejącej grupy.

    - **Typ grupy.** Nie można zmienić typu grupy po jej utworzeniu. Aby zmienić **Typ grupy**, należy usunąć grupę i utworzyć nową.
    
    - **Typ członkostwa.** Zmień typ członkostwa. Aby uzyskać więcej informacji o różnych dostępnych typach członkostwa, zobacz [How to: Create a podstawową grupę i Dodawanie członków przy użyciu portalu Azure Active Directory](active-directory-groups-create-azure-portal.md).
    
    - **Identyfikator obiektu.** Nie można zmienić identyfikatora obiektu, ale można skopiować go do użycia w poleceniach programu PowerShell dla grupy. Aby uzyskać więcej informacji na temat używania poleceń cmdlet programu PowerShell, zobacz [Azure Active Directory polecenia cmdlet do konfigurowania ustawień grupy](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Następne kroki
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Tworzenie grupy podstawowej i dodawanie członków](active-directory-groups-create-azure-portal.md)

- [Jak dodać lub usunąć członków z grupy](active-directory-groups-members-azure-portal.md)

- [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](../users-groups-roles/groups-create-rule.md)

- [Zarządzanie członkostwem w grupie](active-directory-groups-membership-azure-portal.md)

- [Zarządzanie dostępem do zasobów przy użyciu grup](active-directory-manage-groups.md)

- [Kojarzenie lub dodawanie subskrypcji platformy Azure do usługi Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
