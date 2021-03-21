---
title: Przypisywanie ról usługi Azure AD w programie PIM-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak przypisywać role usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/16/2021
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd4374067fe0070c379a76ef5f59bb6aef5b29fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123109"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Przypisywanie ról usługi Azure AD w Privileged Identity Management

Za pomocą Azure Active Directory (Azure AD) administrator globalny może tworzyć **trwałe** przypisania roli administratora usługi Azure AD. Te przypisania ról można utworzyć za pomocą [Azure Portal](../roles/permissions-reference.md) lub za pomocą [poleceń programu PowerShell](/powershell/module/azuread#directory_roles).

Usługa Azure AD Privileged Identity Management (PIM) umożliwia także administratorom ról uprzywilejowanych Tworzenie trwałych przypisań roli administratora. Ponadto administratorzy ról uprzywilejowanych mogą **wprowadzać użytkowników do** ról administratorów usługi Azure AD. Uprawniony administrator może aktywować rolę, gdy ich potrzebują, a następnie ich uprawnienia wygasną po zakończeniu.

## <a name="determine-your-version-of-pim"></a>Określanie używanej wersji programu PIM

Od listopada 2019 część Privileged Identity Management ról usługi Azure AD jest aktualizowana do nowej wersji, która jest zgodna z środowiskami dla ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmian w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, procedury, które należy wykonać w tym artykule, zależą od aktualnie posiadanej wersji Privileged Identity Management. Wykonaj kroki opisane w tej sekcji, aby określić, która wersja Privileged Identity Management masz. Po uzyskaniu informacji o wersji Privileged Identity Management możesz wybrać procedury w tym artykule, które pasują do tej wersji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który znajduje się w roli [administrator ról uprzywilejowanych](../roles/permissions-reference.md#privileged-role-administrator) .
1. Otwórz **Azure AD Privileged Identity Management**. Jeśli masz baner w górnej części strony przegląd, postępuj zgodnie z instrukcjami wyświetlanymi na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami podanymi w **poprzedniej wersji** karty.

  [![Wybierz Privileged Identity Management usługi Azure AD >.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Nowa wersja](#tab/new)

## <a name="assign-a-role"></a>Przypisywanie roli

Wykonaj następujące kroki, aby użytkownik mógł kwalifikować się do roli administratora usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który jest członkiem roli [administrator ról uprzywilejowanych](../roles/permissions-reference.md#privileged-role-administrator) .

    Aby uzyskać informacje na temat udzielania innym administratorom dostępu do zarządzania Privileged Identity Management, zobacz [udzielanie dostępu innym administratorom w celu zarządzania Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **role** , aby wyświetlić listę ról dla uprawnień usługi Azure AD.

    ![Zrzut ekranu strony "role" z wybraną akcją "Dodaj przypisania".](./media/pim-how-to-add-role-to-user/roles-list.png)

1. Wybierz pozycję **Dodaj przypisania** , aby otworzyć stronę **Dodawanie przypisań** .

1. Wybierz pozycję **Wybierz rolę** , aby otworzyć stronę **Wybierz rolę** .

    ![Nowe okienko przypisania](./media/pim-how-to-add-role-to-user/select-role.png)

1. Wybierz rolę, którą chcesz przypisać, wybierz członka, do którego chcesz przypisać rolę, a następnie wybierz przycisk **dalej**.

1. Na liście **Typ przypisania** w okienku **Ustawienia członkostwa** wybierz pozycję **kwalifikujące się** lub **aktywne**.

    - **Kwalifikujące się** przypisania wymagają, aby członek roli wykonywał akcję do korzystania z roli. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających.

    - **Aktywne** przypisania nie wymagają, aby członkowie mogli wykonać żadną akcję w celu korzystania z roli. Członkowie przypisani jako aktywni mają uprawnienia przypisane do roli przez cały czas.

1. Aby określić określony czas trwania przypisywania, należy dodać pola daty i godziny rozpoczęcia i zakończenia. Po zakończeniu wybierz pozycję **Przypisz** , aby utworzyć nowe przypisanie roli.

    ![Ustawienia członkostw — Data i godzina](./media/pim-how-to-add-role-to-user/start-and-end-dates.png)

1. Po przypisaniu roli zostanie wyświetlone powiadomienie o stanie przypisania.

    ![Nowe przypisanie — powiadomienie](./media/pim-how-to-add-role-to-user/assignment-notification.png)

## <a name="assign-a-role-with-restricted-scope"></a>Przypisywanie roli z ograniczonym zakresem

W przypadku niektórych ról zakres przyznanych uprawnień może być ograniczony do pojedynczej jednostki administracyjnej, nazwy głównej usługi lub aplikacji. Ta procedura jest przykładem, Jeśli przypiszesz rolę, która ma zakres jednostki administracyjnej. Aby uzyskać listę ról, które obsługują zakres za pośrednictwem jednostki administracyjnej, zobacz [Przypisywanie ról objętych zakresem do jednostki administracyjnej](../roles/admin-units-assign-roles.md). Ta funkcja jest obecnie wdrażana w organizacjach usługi Azure AD.

1. Zaloguj się do [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) z uprawnieniami administratora ról uprzywilejowanych.

1. Wybierz pozycję **Azure Active Directory**  >  **role i Administratorzy**.

1. Wybierz **administratora użytkownika**.

    ![Polecenie Dodaj przypisanie jest dostępne po otwarciu roli w portalu](./media/pim-how-to-add-role-to-user/add-assignment.png)

1. Wybierz pozycję **Dodaj przypisania**.

    ![Gdy rola obsługuje zakres, można wybrać zakres](./media/pim-how-to-add-role-to-user/add-scope.png)

1. Na stronie **Dodawanie przypisań** można:

   - Wybierz użytkownika lub grupę, która ma zostać przypisana do roli
   - Wybierz zakres roli (w tym przypadku jednostki administracyjne)
   - Wybierz jednostkę administracyjną dla zakresu

Aby uzyskać więcej informacji na temat tworzenia jednostek administracyjnych, zobacz [Dodawanie i usuwanie jednostek administracyjnych](../roles/admin-units-manage.md).

## <a name="update-or-remove-an-existing-role-assignment"></a>Aktualizowanie lub usuwanie istniejącego przypisania roli

Wykonaj następujące kroki, aby zaktualizować lub usunąć istniejące przypisanie roli. **Tylko licencjonowani klienci usługi Azure AD P2**: Nie przypisuj grupy jako aktywnej do roli za pomocą usługi Azure AD i PRIVILEGED Identity Management (PIM). Aby uzyskać szczegółowe wyjaśnienie, zobacz [znane problemy](../roles/groups-concept.md#known-issues).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **role** , aby wyświetlić listę ról usługi Azure AD.

1. Wybierz rolę, którą chcesz zaktualizować lub usunąć.

1. Znajdź przypisanie roli na kartach **kwalifikujące się role** lub **role aktywne** .

    ![Aktualizowanie lub usuwanie przypisania roli](./media/pim-how-to-add-role-to-user/remove-update-assignments.png)

1. Wybierz pozycję **Aktualizuj** lub **Usuń** , aby zaktualizować lub usunąć przypisanie roli.

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="make-a-user-eligible-for-a-role"></a>Przyznaj użytkownikowi uprawnienia do roli

Wykonaj następujące kroki, aby użytkownik mógł kwalifikować się do roli administratora usługi Azure AD.

1. Wybierz **role** lub **członków**.

    ![Otwórz role usługi Azure AD](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Wybierz pozycję **Dodaj członka** , aby otworzyć aplet **Dodaj elementy zarządzane**.

1. Wybierz pozycję **Wybierz rolę**, wybierz rolę, którą chcesz zarządzać, a następnie wybierz pozycję **Wybierz**.

    ![Wybierz rolę](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Wybierz pozycję **Wybierz członków**, wybierz użytkowników, którym chcesz przypisać rolę, a następnie wybierz pozycję **Wybierz**.

    ![Wybierz użytkownika lub grupę do przypisania](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. W obszarze **Dodaj zarządzane elementy członkowskie** wybierz **przycisk OK** , aby dodać użytkownika do roli.

1. Na liście ról wybierz właśnie przypisaną rolę, aby wyświetlić listę członków.

     Po przypisaniu roli wybrany użytkownik zostanie wyświetlony na liście członków jako **uprawniony** do roli.

    ![Użytkownik uprawniony do roli](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Teraz, gdy użytkownik kwalifikuje się do roli, daj im znać, że mogą go aktywować zgodnie z instrukcjami w temacie [Aktywowanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-activate-role.md).

    Uprawnieni Administratorzy są proszeni o zarejestrowanie się w usłudze Azure AD Multi-Factor Authentication podczas aktywacji. Jeśli użytkownik nie może zarejestrować się na potrzeby uwierzytelniania wieloskładnikowego lub używa konto Microsoft (na przykład @outlook.com ), musisz trwale wprowadzić wszystkie swoje role.

## <a name="make-a-role-assignment-permanent"></a>Tworzenie trwałego przypisania roli

Domyślnie nowi użytkownicy *kwalifikują* się tylko do roli administratora usługi Azure AD. Wykonaj te kroki, jeśli chcesz, aby przypisanie roli trwało.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz **członków**.

    ![Lista członków](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Wybierz **kwalifikującą się** rolę, która ma być trwała.

1. Wybierz pozycję **więcej** , a następnie wybierz pozycję **Utwórz uprawnienie**.

    ![Utwórz trwale przypisanie roli](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    Rola jest teraz wyświetlana jako **stała**.

    ![Lista członków z trwałą zmianą](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Usuwanie użytkownika z roli

Możesz usunąć użytkowników z przypisań ról, ale upewnij się, że zawsze istnieje co najmniej jeden użytkownik, który jest stałym administratorem globalnym. Jeśli nie masz pewności, którzy użytkownicy nadal potrzebują przypisań ról, możesz [rozpocząć przegląd dostępu dla tej roli](pim-how-to-start-security-review.md).

Wykonaj następujące kroki, aby usunąć określonego użytkownika z roli administratora usługi Azure AD.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz **członków**.

    ![Lista członków](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Wybierz przypisanie roli, które chcesz usunąć.

1. Wybierz pozycję **więcej** , a następnie wybierz pozycję **Usuń**.

    ![Usuwanie roli](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. W komunikacie z prośbą o potwierdzenie wybierz pozycję **tak**.

    ![Potwierdzenie usunięcia](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    Przypisanie roli zostanie usunięte.

## <a name="authorization-error-when-assigning-roles"></a>Błąd autoryzacji podczas przypisywania ról

Jeśli ostatnio włączono Privileged Identity Management dla subskrypcji i wystąpi błąd autoryzacji podczas próby skorzystania z roli administratora usługi Azure AD, może to być spowodowane faktem, że jednostka usługi MS-PIM nie ma jeszcze odpowiednich uprawnień. Nazwa główna usługi MS-PIM musi mieć rolę [administratora dostępu użytkowników](../../role-based-access-control/built-in-roles.md#user-access-administrator) do przypisywania ról innym osobom. Zamiast czekać, aż do MS-PIM zostanie przypisana rola administratora dostępu użytkowników, można przypisać ją ręcznie.

Wykonaj następujące kroki, aby przypisać rolę administratora dostępu użytkownika do nazwy głównej usługi MS-PIM dla subskrypcji.

1. Zaloguj się do Azure Portal jako Administrator globalny.

1. Wybierz pozycję **wszystkie usługi** , a następnie pozycję **subskrypcje**.

1. Wybierz subskrypcję.

1. Wybierz pozycję **Kontrola dostępu (IAM)**.

1. Wybierz **przypisania ról** , aby wyświetlić bieżącą listę przypisań ról w zakresie subskrypcji.

   ![Blok kontroli dostępu (IAM) dla subskrypcji](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Sprawdź, czy nazwa główna usługi **MS-PIM** jest przypisana do roli **administratora dostępu użytkownika** .

1. W przeciwnym razie wybierz pozycję **Dodaj przypisanie roli** , aby otworzyć okienko **Dodaj przypisanie roli** .

1. Z listy rozwijanej **rola** wybierz rolę **administrator dostępu użytkowników** .

1. Z listy **Wybierz** Znajdź i wybierz nazwę główną usługi **MS-PIM** .

   ![Dodawanie okienka przypisania roli — Dodawanie uprawnień dla nazwy głównej usługi MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Wybierz pozycję **Zapisz** , aby przypisać rolę.

   Po kilku chwilach nazwa główna usługi MS-PIM jest przypisana do roli administratora dostępu użytkownika w zakresie subskrypcji.

   ![Strona kontroli dostępu z przypisaniem roli administratora dostępu użytkownika dla nazwy głównej usługi MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

 ---

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj ustawienia roli administratora usługi Azure AD w Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Przypisywanie ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-assign-roles.md)