---
title: Dodawanie lub usuwanie grupy z innej grupy — Azure AD
description: Instrukcje dotyczące dodawania lub usuwania grupy z innej grupy przy użyciu Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f3918016e35029ea6cc1b60d407ad475b35895b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89565468"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Dodawanie lub usuwanie grupy z innej grupy przy użyciu Azure Active Directory
Ten artykuł pomaga w dodawaniu i usuwaniu grupy z innej grupy przy użyciu Azure Active Directory.

>[!Note]
>Jeśli próbujesz usunąć grupę nadrzędną, zobacz [jak zaktualizować lub usunąć grupę i jej członków](active-directory-groups-delete-group.md).

## <a name="add-a-group-to-another-group"></a>Dodawanie grupy do innej grupy
Istniejącą grupę zabezpieczeń można dodać do innej istniejącej grupy zabezpieczeń (znanej również jako grupy zagnieżdżone), tworząc grupę członkowską (podgrupę) i grupę nadrzędną. Grupa członkowska dziedziczy atrybuty i właściwości grupy nadrzędnej, oszczędzając czas konfiguracji.

>[!Important]
>Obecnie nie obsługujemy:<ul><li>Dodawanie grup do grupy synchronizowanej z Active Directoryami lokalnymi.</li><li>Dodawanie grup zabezpieczeń do grup Microsoft 365.</li><li>Dodawanie grup Microsoft 365 do grup zabezpieczeń lub innych grup Microsoft 365.</li><li>Przypisywanie aplikacji do grup zagnieżdżonych.</li><li>Stosowanie licencji do grup zagnieżdżonych.</li><li>Dodawanie grup dystrybucyjnych w scenariuszach zagnieżdżania.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Aby dodać grupę jako element członkowski innej grupy

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **grupy**.

3. Na stronie **grupy — wszystkie grupy** Wyszukaj i wybierz grupę, która ma zostać członkiem innej grupy. W tym ćwiczeniu używana jest Grupa **zasad zarządzania urządzeniami przenośnymi** .

    >[!Note]
    >Grupę możesz dodać jako członka tylko do jednej grupy jednocześnie. Ponadto pole **Wybierz grupę** filtruje ekran w oparciu o pasujący wpis do dowolnej części nazwy użytkownika lub urządzenia. Jednak symbole wieloznaczne nie są obsługiwane.

    ![Grupy — wszystkie grupy Strona z zasadami zarządzania urządzeniami przenośnymi — wybrana grupa Zachodnia](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. Na stronie **zasady zarządzania urządzeniami przenośnymi-zachodnie grupy członkostwa** wybierz pozycję **członkostwa w grupie**, wybierz pozycję **Dodaj**, Znajdź grupę, do której należy Grupa, a następnie wybierz **pozycję Wybierz**. W tym ćwiczeniu korzystamy z **zasad zarządzania urządzeniami przenośnymi — wszystkie grupy organizacji** .

    **Zasady zarządzania urządzeniami przenośnymi — West** jest teraz członkiem **zasad zarządzania urządzeniami przenośnymi — wszystkie grupy organizacyjne** , które dziedziczą wszystkie właściwości i konfigurację zasad zarządzania urządzeniami przenośnymi — wszystkie grupy organizacji.

    ![Tworzenie członkostwa w grupie przez dodanie grupy do innej grupy](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. Przejrzyj stronę **zasady zarządzania urządzeniami przenośnymi-zachodnie grupy członkostwa** , aby wyświetlić relacje między grupami i członkami.

6. Aby uzyskać bardziej szczegółowy widok relacji grupy i elementu członkowskiego, wybierz nazwę grupy (**zasady zarządzania urządzeniami przenośnymi — cała organizacja**) i zapoznaj się z **zasadami zarządzania urządzeniami przenośnymi —** informacje o stronie zachodniej.

## <a name="remove-a-group-from-another-group"></a>Usuwanie grupy z innej grupy
Istnieje możliwość usunięcia istniejącej grupy zabezpieczeń z innej grupy zabezpieczeń. Jednak usunięcie grupy spowoduje również usunięcie wszystkich dziedziczonych atrybutów i właściwości elementów członkowskich.

### <a name="to-remove-a-member-group-from-another-group"></a>Aby usunąć grupę członkowską z innej grupy
1. Na stronie **grupy — wszystkie grupy** Wyszukaj i wybierz grupę, która ma zostać usunięta jako członek innej grupy. W tym ćwiczeniu będziemy ponownie używać grupy **zasad zarządzania urządzeniami przenośnymi** .

2. Na stronie **Przegląd zasad zarządzania urządzeniami przenośnymi** wybierz pozycję **członkostwa w grupie**.

3. Wybierz pozycję **zasady zarządzania urządzeniami przenośnymi — wszystkie grupy organizacji** na stronie **zasady zarządzania urządzeniami przenośnymi-zachodnie grupy członkostwa** , a następnie wybierz pozycję **Usuń** z szczegółów na stronie **zasady zarządzania urządzeniami przenośnymi** .

    ![Strona przynależności do grupy pokazująca zarówno element członkowski, jak i grupę szczegóły](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>Dodatkowe informacje
Te artykuły zawierają dodatkowe informacje o usłudze Azure Active Directory.

- [Wyświetlanie grup i elementów członkowskich](active-directory-groups-view-azure-portal.md)

- [Tworzenie grupy podstawowej i dodawanie członków](active-directory-groups-create-azure-portal.md)

- [Dodawanie lub usuwanie elementów członkowskich z grupy](active-directory-groups-members-azure-portal.md)

- [Edytowanie ustawień grupy](active-directory-groups-settings-azure-portal.md)

- [Using a group to manage access to SaaS applications (Używanie grupy do zarządzania dostępem do aplikacji SaaS)](../users-groups-roles/groups-saasapps.md)

- [Scenariusze, ograniczenia i znane problemy przy użyciu grup do zarządzania licencjonowaniem w programie Azure Active Directory](../users-groups-roles/licensing-group-advanced.md#limitations-and-known-issues)
