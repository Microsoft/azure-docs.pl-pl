---
title: Zarządzanie użytkownikami w usłudze Azure łańcucha bloków Workbench
description: Jak zarządzać użytkownikami w usłudze Azure łańcucha bloków Workbench.
ms.date: 07/15/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: be078e7149ba008ab8b7ad44a016a0d1cf82df0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91263015"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Zarządzanie użytkownikami w usłudze Azure łańcucha bloków Workbench

Usługa Azure łańcucha bloków Workbench obejmuje zarządzanie użytkownikami dla osób i organizacji, które są częścią konsorcjum.

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane jest wdrożenie łańcucha bloków Workbench. Aby uzyskać szczegółowe informacje dotyczące wdrażania, zobacz [wdrożenie usługi Azure łańcucha bloków Workbench](deploy.md) .

## <a name="add-azure-ad-users"></a>Dodawanie użytkowników usługi Azure AD

Usługa Azure łańcucha bloków Workbench używa usługi Azure Active Directory (Azure AD) do uwierzytelniania, kontroli dostępu i ról. Użytkownicy w dzierżawie usługi Azure AD łańcucha bloków Workbench mogą uwierzytelniać i używać łańcucha bloków Workbench. Dodawanie użytkowników do roli aplikacji administrator w celu współdziałania i wykonywania akcji.

Aby można było przypisywać aplikacje i role, użytkownicy programu łańcucha bloków Workbench muszą istnieć w dzierżawie usługi Azure AD. Aby dodać użytkowników do usługi Azure AD, wykonaj następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz swoje konto w prawym górnym rogu i przejdź do dzierżawy usługi Azure AD skojarzonej z łańcucha bloków Workbench.
1. Wybierz **Azure Active Directory > użytkowników**. Zostanie wyświetlona lista użytkowników w katalogu.
1. Aby dodać użytkowników do katalogu, wybierz pozycję **nowy użytkownik**. W przypadku użytkowników zewnętrznych wybierz pozycję **nowy użytkownik-Gość**.
1. Wypełnij pola wymagane dla nowego użytkownika. Wybierz przycisk **Utwórz**.

Więcej informacji na temat zarządzania użytkownikami w usłudze Azure AD można znaleźć w dokumentacji usługi [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md) .

## <a name="manage-blockchain-workbench-administrators"></a>Zarządzanie administratorami łańcucha bloków Workbench

Po dodaniu użytkowników do katalogu następnym krokiem jest wybranie użytkowników, którzy są administratorami łańcucha bloków Workbench. Użytkownicy z grupy **administratorów** są powiązani z **rolą aplikacji administrator** w programie łańcucha bloków Workbench. Administratorzy mogą dodawać i usuwać użytkowników, przypisywać użytkowników do określonych scenariuszy oraz tworzyć nowe aplikacje.

Aby dodać użytkowników do grupy **administratorów** w katalogu usługi Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Sprawdź, czy jesteś w dzierżawie usługi Azure AD skojarzonym z łańcucha bloków Workbench, wybierając swoje konto w prawym górnym rogu.
1. Wybierz pozycję **Azure Active Directory > aplikacje dla przedsiębiorstw**.
1. Zmień filtr listy rozwijanej **Typ aplikacji** na **wszystkie aplikacje** i wybierz pozycję **Zastosuj**.
1. Wybierz aplikację kliencką usługi Azure AD dla usługi Azure łańcucha bloków Workbench

    ![Wszystkie rejestracje aplikacji dla przedsiębiorstw](./media/manage-users/select-blockchain-client-app.png)

1. Wybierz pozycję **Użytkownicy i grupy > Dodaj użytkownika**.
1. W okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy**. Wybierz lub Wyszukaj użytkownika, który ma zostać dodany jako administrator. Po zakończeniu **zaznaczania** kliknij przycisk Wybierz.

    ![Dodaj przypisanie](./media/manage-users/add-user-assignment.png)

1. Sprawdź, czy **rola** jest ustawiona na **administratora**
1. Wybierz opcję **Przypisz**. Dodani użytkownicy są wyświetlani na liście z przypisaną rolą administratora.

    ![Użytkownicy aplikacji klienta łańcucha bloków](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Zarządzanie członkami łańcucha bloków Workbench

Użyj aplikacji łańcucha bloków Workbench, aby zarządzać użytkownikami i organizacjami, które są częścią konsorcjum. Możesz dodawać i usuwać użytkowników do aplikacji i ról.

1. [Otwórz Workbench łańcucha bloków](deploy.md#blockchain-workbench-web-url) w przeglądarce i zaloguj się jako administrator.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Elementy członkowskie są dodawane do poszczególnych aplikacji. Członkowie mogą mieć co najmniej jedną rolę aplikacji umożliwiającą inicjowanie kontraktów lub podejmowanie działań.

1. Aby zarządzać członkami aplikacji, wybierz kafelek aplikacji w okienku **aplikacje** .

    Liczba członków skojarzonych z wybraną aplikacją zostanie odzwierciedlona na kafelku członkowie.

    ![Wybierz aplikację](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Dodawanie elementu członkowskiego do aplikacji

1. Wybierz kafelek elementu członkowskiego, aby wyświetlić listę bieżących członków.
1. Wybierz pozycję **Dodaj członków**.

    ![Zrzut ekranu przedstawia okno członkostwa aplikacji z wyróżnionym przyciskiem Dodaj element członkowski.](./media/manage-users/application-add-members.png)

1. Wyszukaj nazwę użytkownika.  Na liście znajdują się tylko użytkownicy usługi Azure AD, którzy istnieją w dzierżawie usługi Blockchain Workbench. Jeśli użytkownik nie zostanie odnaleziony, należy [dodać użytkowników usługi Azure AD](#add-azure-ad-users).

    ![Dodawanie członków](./media/manage-users/find-user.png)

1. Wybierz **rolę** z listy rozwijanej.

    ![Wybierz członków roli](./media/manage-users/application-select-role.png)

1. Wybierz pozycję **Dodaj**, aby dodać członka ze skojarzoną rolą do aplikacji.

#### <a name="remove-member-from-application"></a>Usuń członka z aplikacji

1. Wybierz kafelek elementu członkowskiego, aby wyświetlić listę bieżących członków.
1. Dla użytkownika, który chcesz usunąć, wybierz pozycję **Usuń** z listy rozwijanej rola.

    ![Usuwanie członka](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Zmień lub Dodaj rolę

1. Wybierz kafelek elementu członkowskiego, aby wyświetlić listę bieżących członków.
1. Dla użytkownika, który chcesz zmienić, kliknij listę rozwijaną i wybierz nową rolę.

    ![Zmiana roli](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule poznasz sposób zarządzania użytkownikami usługi Azure łańcucha bloków Workbench. Aby dowiedzieć się, jak utworzyć aplikację łańcucha bloków, przejdź do następnej artykułu z artykułem.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji łańcucha bloków w usłudze Azure Blockchain Workbench](create-app.md)
