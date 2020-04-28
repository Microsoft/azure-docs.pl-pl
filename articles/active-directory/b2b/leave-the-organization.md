---
title: Opuść organizację jako użytkownika-gościa — Azure Active Directory
description: Pokazuje, w jaki sposób użytkownik systemu Azure AD B2B może opuścić organizację przy użyciu panelu dostępu.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74272494"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Opuść organizację jako użytkownika-gościa

Azure Active Directory (Azure AD) użytkownik-Gość B2B może zdecydować się na pozostawienie organizacji w dowolnym momencie, jeśli nie muszą już korzystać z aplikacji z tej organizacji lub zachować żadnego skojarzenia. Użytkownik może samodzielnie opuścić organizację, bez konieczności kontaktowania się z administratorem.

> [!NOTE]
> Użytkownik-Gość nie może opuścić organizacji, jeśli konto jest wyłączone w dzierżawie głównej lub dzierżawie zasobów. Jeśli konto jest wyłączone, użytkownik będący gościem musi skontaktować się z administratorem dzierżawy, który może usunąć konto gościa lub włączyć konto gościa, aby użytkownik mógł opuścić organizację.

## <a name="leave-an-organization"></a>Opuszczanie organizacji

Aby opuścić organizację, wykonaj następujące kroki.

1. Przejdź do strony profilu panelu dostępu, wykonując jedną z następujących czynności:
   
   - W [Azure Portal](https://portal.azure.com)kliknij swoją nazwę w prawym górnym rogu i wybierz pozycję **Wyświetl konto**.
   - Otwórz [panel dostępu](https://myapps.microsoft.com), kliknij swoją nazwę w prawym górnym rogu, a obok pozycji **organizacje**wybierz ikonę ustawień (koła zębatego).
 
   ![Zrzut ekranu przedstawiający ustawienia użytkownika w panelu dostępu](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Jeśli jeszcze nie zalogowano się do organizacji, którą chcesz opuścić, w obszarze **organizacje**kliknij link Zaloguj się, **Aby opuścić organizację** obok nazwy organizacji. Po zalogowaniu kliknij swoją nazwę ponownie w prawym górnym rogu, a obok pozycji **organizacje**wybierz ikonę ustawień (koła zębatego).

3. W obszarze **organizacje**Znajdź organizację, którą chcesz opuścić, a następnie wybierz pozycję **Opuść organizację**.

   ![Zrzut ekranu przedstawiający opcję Opuść organizację w interfejsie użytkownika](media/leave-the-organization/LeaveOrg.png)

4. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **Pozostaw**. 

## <a name="account-removal"></a>Usuwanie konta

Gdy użytkownik opuści organizację, konto użytkownika jest "nietrwałe usunięte" w katalogu. Domyślnie obiekt użytkownika jest przenoszony do obszaru **usuniętych użytkowników** w usłudze Azure AD, ale nie jest on trwale usuwany przez 30 dni. To usuwanie nietrwałe umożliwia administratorowi przywrócenie konta użytkownika (w tym grup i uprawnień), jeśli użytkownik wysyła żądanie przywrócenia konta w ramach 30-dniowego okresu.

W razie potrzeby Administrator dzierżawy może trwale usunąć konto w dowolnym momencie w okresie 30 dni. W tym celu:

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Azure Active Directory**.
2. W obszarze **Zarządzaj** wybierz pozycję **Użytkownicy**.
3. Wybierz pozycję **usunięci użytkownicy**.
4. Zaznacz pole wyboru obok usuniętego użytkownika, a następnie wybierz pozycję **Usuń trwale**.

W przypadku trwałego usunięcia użytkownika Ta akcja jest nieodwołalna.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem usługi Azure AD B2B, zobacz [co to jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)



