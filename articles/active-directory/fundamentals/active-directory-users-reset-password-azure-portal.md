---
title: Resetowanie hasła użytkownika — Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące resetowania hasła użytkownika przy użyciu Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8809f8c168e7095f05587c7a572e08287637dc5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034595"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Resetowanie hasła użytkownika przy użyciu usługi Azure Active Directory

Jako administrator możesz zresetować hasło użytkownika, jeśli hasło jest zapomniane, jeśli użytkownik zostanie zablokowany poza urządzeniem lub jeśli użytkownik nigdy nie otrzymał hasła.

>[!Note]
>Jeśli dzierżawa usługi Azure AD nie jest katalogiem macierzystym dla użytkownika, nie będzie można resetować hasła. Oznacza to, że jeśli użytkownik loguje się do organizacji przy użyciu konta z innej organizacji, konto Microsoft lub konta Google, nie będzie można resetować hasła.<br><br>Jeśli użytkownik ma źródło uprawnień jako Windows Server Active Directory, będzie można zresetować hasło tylko w przypadku włączenia funkcji zapisywania zwrotnego haseł.<br><br>Jeśli użytkownik ma źródło uprawnień jako zewnętrzną usługę Azure AD, nie będzie można resetować hasła. Tylko użytkownik lub administrator w zewnętrznej usłudze Azure AD może zresetować hasło.

>[!Note]
>Jeśli nie jesteś administratorem i szukasz instrukcji dotyczących sposobu resetowania własnego hasła służbowego, zobacz [Resetowanie hasła służbowego lub szkolnego](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Aby zresetować hasło

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) jako administrator użytkownika lub hasło. Aby uzyskać więcej informacji na temat dostępnych ról, zobacz [wbudowane role usługi Azure AD](../roles/permissions-reference.md)

2. Wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, Wyszukaj i wybierz użytkownika wymagającego resetowania, a następnie wybierz pozycję **Resetuj hasło**.

    Zostanie wyświetlona strona **Alain Charon-profile** z opcją **resetowania hasła** .

    ![Strona profil użytkownika z wyróżnioną opcją resetowania hasła](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Na stronie **Resetowanie hasła** wybierz pozycję **Resetuj hasło**.

    > [!Note]
    > W przypadku korzystania z Azure Active Directory hasło tymczasowe jest automatycznie generowane dla użytkownika. W przypadku korzystania z Active Directory lokalnego należy utworzyć hasło dla użytkownika.

4. Skopiuj hasło i nadaj użytkownikowi. Użytkownik będzie musiał zmienić hasło podczas następnego procesu logowania.

    >[!Note]
    >Tymczasowe hasło nigdy nie wygasa. Po następnym zalogowaniu się użytkownika hasło będzie nadal działało, niezależnie od tego, jak dużo czasu upłynął od momentu wygenerowania hasła tymczasowego.

> [!IMPORTANT]
> Jeśli administrator nie może zresetować hasła użytkownika, a w dziennikach zdarzeń aplikacji na serwerze Azure AD Connect jest widoczny następujący kod błędu HR = 80231367, sprawdź atrybuty użytkownika w Active Directory.  Jeśli atrybut **AdminCount** jest ustawiony na 1, uniemożliwi to administratorowi zresetowanie hasła użytkownika.  Atrybut **AdminCount** musi mieć wartość 0, aby administratorzy mogli zresetować hasło użytkownika.


## <a name="next-steps"></a>Następne kroki

Po zresetowaniu hasła użytkownika można wykonać następujące podstawowe procesy:

- [Dodawanie lub usuwanie użytkowników](add-users-azure-active-directory.md)

- [Przypisywanie ról do użytkowników](active-directory-users-assign-role-azure-portal.md)

- [Dodawanie lub zmiana informacji o profilu](active-directory-users-profile-azure-portal.md)

- [Tworzenie grupy podstawowej i dodawanie członków](active-directory-groups-create-azure-portal.md)

Można też wykonywać bardziej złożone scenariusze użytkowników, takie jak przypisywanie delegatów, korzystanie z zasad i udostępnianie kont użytkowników. Aby uzyskać więcej informacji na temat innych dostępnych akcji, zobacz [Azure Active Directory dokumentacja dotycząca zarządzania użytkownikami](../enterprise-users/index.yml).
