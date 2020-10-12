---
title: Dodawanie lub aktualizowanie informacji o profilu użytkownika — usługa Azure AD
description: Instrukcje dotyczące sposobu dodawania informacji do profilu użytkownika w Azure Active Directory, w tym informacje o obrazie i zadaniu.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63b716aa22954af51a9eb53a0d04a8299d348c71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87797241"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Dodawanie lub aktualizowanie informacji w profilu użytkownika przy użyciu usługi Azure Active Directory
Dodawanie informacji o profilu użytkownika, w tym obrazu profilu, informacji specyficznych dla zadania i niektórych ustawień przy użyciu Azure Active Directory (Azure AD). Aby uzyskać więcej informacji na temat dodawania nowych użytkowników, zobacz [jak dodać lub usunąć użytkowników w Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Dodawanie lub zmiana informacji o profilu
Jak widać, w profilu użytkownika są dostępne więcej informacji niż to, co można dodać podczas tworzenia użytkownika. Wszystkie te dodatkowe informacje są opcjonalne i mogą być dodawane zgodnie z potrzebami organizacji.

## <a name="to-add-or-change-profile-information"></a>Aby dodać lub zmienić informacje o profilu
1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator użytkownika w organizacji.

2. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz użytkownika. Na przykład _Alain Charon_.

    Zostanie wyświetlona strona **Alain Charon-profile** .

    ![Strona profilu użytkownika, w tym informacje edytowalne](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Wybierz pozycję **Edytuj** , aby opcjonalnie dodać lub zaktualizować informacje zawarte w każdej z dostępnych sekcji.

    ![Strona profilu użytkownika pokazująca obszary edytowalne](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Obraz profilu.** Wybierz obraz miniatury dla konta użytkownika. Ten obraz jest wyświetlany w Azure Active Directory i na osobistych stronach użytkownika, na przykład na stronie myapps.microsoft.com.

    - **Identity.** Dodaj lub zaktualizuj dodatkową wartość tożsamości dla użytkownika, na przykład nazwisko ślubu. Tę nazwę można ustawić niezależnie od wartości imię i nazwisko. Na przykład można użyć go do uwzględnienia inicjałów, nazwy firmy lub zmiany sekwencji wyświetlanych nazw. W innym przykładzie dla dwóch użytkowników, których nazwy są "Krzysztof Green", można użyć ciągu tożsamości w celu ustawienia ich nazw na "Krzysztof B. zielony" "Krzysztof R. Green (contoso)".

    - **Informacje o zadaniu.** Dodaj wszelkie informacje związane z zadaniami, takie jak stanowisko użytkownika, dział lub Menedżer.

    - **Ustawienia.** Zdecyduj, czy użytkownik może zalogować się do dzierżawy Azure Active Directory. Możesz również określić lokalizację globalną użytkownika.

    - **Informacje kontaktowe.** Dodaj wszelkie odpowiednie informacje kontaktowe dla użytkownika, z wyjątkiem informacji o telefonie lub kontakcie komórkowym użytkownika (tylko administrator globalny może aktualizować użytkowników w rolach ról administratorów).

    - **Informacje kontaktowe uwierzytelniania.** Sprawdź te informacje, aby upewnić się, że dla użytkownika istnieje aktywny numer telefonu i adres e-mail. Te informacje są używane przez Azure Active Directory, aby upewnić się, że użytkownik jest naprawdę użytkownikiem podczas logowania. Informacje kontaktowe uwierzytelniania mogą być aktualizowane tylko przez administratora globalnego.

4. Wybierz pozycję **Zapisz**.

    Wszystkie zmiany zostaną zapisane dla użytkownika.

    >[!Note]
    >Aby zaktualizować tożsamość, informacje kontaktowe lub informacje o zadaniu dla użytkowników, których źródłem urzędu jest Active Directory Windows Server, należy użyć Active Directory systemu Windows Server. Po zakończeniu aktualizacji należy poczekać na zakończenie następnego cyklu synchronizacji, zanim zobaczysz zmiany.

## <a name="next-steps"></a>Następne kroki
Po zaktualizowaniu profilów użytkowników można wykonać następujące podstawowe procesy:

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Tworzenie grupy podstawowej i dodawanie członków](active-directory-groups-create-azure-portal.md)

Można też wykonywać inne zadania zarządzania użytkownikami, takie jak przypisywanie delegatów, korzystanie z zasad i udostępnianie kont użytkowników. Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [Azure Active Directory dokumentacja dotycząca zarządzania użytkownikami](../users-groups-roles/index.yml).
