---
title: Dodawanie i usuwanie przypisań ról platformy Azure przy użyciu Azure Portal — RBAC
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu Azure Portal i kontroli dostępu opartej na rolach (RBAC) platformy Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 7c58641f0039982f05be14d0f24ba89c62273d4b
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964307"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] W tym artykule opisano sposób przypisywania ról przy użyciu Azure Portal.

Jeśli musisz przypisać role administratorów w Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write` i `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Kontrola dostępu (IAM)

**Kontrola dostępu (IAM)** to strona, której zwykle używasz do przypisywania ról w celu udzielenia dostępu do zasobów platformy Azure. Jest on również znany jako Zarządzanie tożsamościami i dostępem i pojawia się w kilku lokalizacjach w Azure Portal. Poniżej przedstawiono przykład strony kontroli dostępu (IAM) dla subskrypcji.

![Strona kontroli dostępu (IAM) dla subskrypcji](./media/role-assignments-portal/access-control-subscription.png)

Aby była najbardziej efektywna ze stroną kontroli dostępu (IAM), należy wykonać następujące kroki, aby przypisać rolę.

1. Określ, kto ma mieć dostęp. Rolę można przypisać do użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej.

1. Znajdź odpowiednią rolę. Uprawnienia są pogrupowane w role. Możesz wybrać jedną z listy [wbudowanych ról platformy Azure](built-in-roles.md) lub użyć własnych ról niestandardowych.

1. Zidentyfikuj wymagany zakres. Platforma Azure udostępnia cztery poziomy zakresu: [grupę zarządzania](../governance/management-groups/overview.md), subskrypcję, [grupę zasobów](../azure-resource-manager/management/overview.md#resource-groups)i zasób. Aby uzyskać więcej informacji na temat zakresu, zobacz [Opis zakresu](scope-overview.md).

1. Wykonaj kroki opisane w jednej z poniższych sekcji, aby przypisać rolę.

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W celu udzielenia dostępu do zasobu platformy Azure w usłudze Azure RBAC należy dodać przypisanie roli. Wykonaj następujące kroki, aby przypisać rolę.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres, do którego chcesz udzielić dostępu. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów** lub zasób.

1. Kliknij konkretny zasób dla tego zakresu.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **przypisania roli** , aby wyświetlić przypisania ról w tym zakresie.

    ![Kontrola dostępu (IAM) i przypisywanie ról — karta](./media/role-assignments-portal/role-assignments.png)

1. Kliknij przycisk **Dodaj**  >  **Dodaj przypisanie roli**.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj przypisanie roli](./media/shared/add-role-assignment-menu.png)

    Zostanie otwarte okienko Dodawanie przypisania roli.

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

1. Z listy **Wybierz** wybierz użytkownika, grupę, nazwę główną usługi lub tożsamość zarządzaną. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach podmiot zabezpieczeń ma przypisaną rolę w wybranym zakresie.

    ![Zapisano dodanie przypisania roli](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Przypisywanie użytkownika jako administratora subskrypcji

Aby użytkownik był administratorem subskrypcji platformy Azure, przypisz im rolę [właściciela](built-in-roles.md#owner) w zakresie subskrypcji. Rola właściciela daje użytkownikowi pełny dostęp do wszystkich zasobów w ramach subskrypcji, w tym uprawnienia do udzielania dostępu innym osobom. Te kroki są takie same jak w przypadku innych przypisań ról.

1. W witrynie Azure Portal kliknij pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

1. Kliknij subskrypcję, do której chcesz udzielić dostępu.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **przypisania ról** , aby wyświetlić przypisania ról dla tej subskrypcji.

    ![Kontrola dostępu (IAM) i przypisywanie ról — karta](./media/role-assignments-portal/role-assignments.png)

1. Kliknij przycisk **Dodaj**  >  **Dodaj przypisanie roli**.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodawanie przypisania roli dla subskrypcji](./media/shared/add-role-assignment-menu.png)

    Zostanie otwarte okienko Dodawanie przypisania roli.

   ![Dodawanie okienka przypisania roli dla subskrypcji](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę **Właściciel**.

1. Z listy **Wybierz** wybierz użytkownika. Jeśli nie widzisz użytkownika na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane i adresy e-mail.

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach użytkownikowi zostanie przypisana rola właściciela w zakresie subskrypcji.

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Dodawanie przypisania roli dla tożsamości zarządzanej (wersja zapoznawcza)

Można dodać przypisania ról dla tożsamości zarządzanej przy użyciu strony **kontroli dostępu (IAM)** zgodnie z opisem we wcześniejszej części tego artykułu. Korzystając ze strony kontrola dostępu (IAM), rozpoczynasz od zakresu, a następnie wybierasz zarządzaną tożsamość i rolę. W tej sekcji opisano alternatywny sposób dodawania przypisań ról do tożsamości zarządzanej. Korzystając z tych kroków, należy rozpocząć od tożsamości zarządzanej, a następnie wybrać zakres i rolę.

> [!IMPORTANT]
> Dodawanie przypisania roli dla tożsamości zarządzanej przy użyciu tych alternatywnych kroków jest obecnie w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

Wykonaj następujące kroki, aby przypisać rolę do zarządzanej tożsamości przypisanej do systemu, rozpoczynając od tożsamości zarządzanej.

1. W Azure Portal Otwórz przypisaną przez system tożsamość zarządzaną.

1. W menu po lewej stronie kliknij pozycję **tożsamość**.

    ![Tożsamość zarządzana przypisana przez system](./media/shared/identity-system-assigned.png)

1. W obszarze **uprawnienia** kliknij pozycję **przypisania ról platformy Azure**.

    Jeśli role są już przypisane do wybranej tożsamości zarządzanej przypisanej do systemu, zostanie wyświetlona lista przypisań ról. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla tożsamości zarządzanej przypisanej do systemu](./media/shared/role-assignments-system-assigned.png)

1. Aby zmienić subskrypcję, kliknij listę **subskrypcji** .

1. Kliknij pozycję **Dodaj przypisanie roli (wersja zapoznawcza)**.

1. Użyj list rozwijanych, aby wybrać zestaw zasobów, do których ma zastosowanie przypisanie roli, takie jak **subskrypcja**, **Grupa zasobów** lub zasób.

    Jeśli nie masz uprawnień do zapisu przypisania roli dla wybranego zakresu, zostanie wyświetlony komunikat w wierszu. 

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

   ![Dodawanie okienka przypisania roli dla tożsamości zarządzanej przypisanej do systemu](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach zarządzana tożsamość zostanie przypisana do roli w wybranym zakresie.

### <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

Wykonaj następujące kroki, aby przypisać rolę do tożsamości zarządzanej przypisanej przez użytkownika, rozpoczynając od tożsamości zarządzanej.

1. W Azure Portal Otwórz tożsamość zarządzaną przypisaną przez użytkownika.

1. W menu po lewej stronie kliknij pozycję **przypisania ról platformy Azure**.

    Jeśli role są już przypisane do wybranej tożsamości zarządzanej przypisanej przez użytkownika, zostanie wyświetlona lista przypisań ról. Ta lista zawiera wszystkie przypisania ról, do których masz uprawnienia do odczytu.

    ![Przypisania ról dla tożsamości zarządzanej przypisanej przez użytkownika](./media/shared/role-assignments-user-assigned.png)

1. Aby zmienić subskrypcję, kliknij listę **subskrypcji** .

1. Kliknij pozycję **Dodaj przypisanie roli (wersja zapoznawcza)**.

1. Użyj list rozwijanych, aby wybrać zestaw zasobów, do których ma zastosowanie przypisanie roli, takie jak **subskrypcja**, **Grupa zasobów** lub zasób.

    Jeśli nie masz uprawnień do zapisu przypisania roli dla wybranego zakresu, zostanie wyświetlony komunikat w wierszu. 

1. Z listy rozwijanej **Rola** wybierz rolę, taką jak **Współautor·maszyny·wirtualnej**.

   ![Dodawanie okienka przypisanie roli dla tożsamości zarządzanej przypisanej przez użytkownika](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach zarządzana tożsamość zostanie przypisana do roli w wybranym zakresie.

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

W celu usunięcia dostępu z zasobów platformy Azure w usłudze Azure RBAC należy usunąć przypisanie roli. Wykonaj następujące kroki, aby usunąć przypisanie roli.

1. Otwórz **kontrolę dostępu (IAM)** w zakresie, na przykład grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, w której chcesz usunąć dostęp.

1. Kliknij kartę **Przypisania ról**, aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.

1. Na liście przypisań ról dodaj znacznik wyboru obok podmiotu zabezpieczeń z przypisaniem roli, które chcesz usunąć.

   ![Przypisanie roli wybrane do usunięcia](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kliknij przycisk **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment.png)

1. W wyświetlonym komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

    Jeśli zobaczysz komunikat, że nie można usunąć przypisań ról dziedziczonych, próbujesz usunąć przypisanie roli w zakresie podrzędnym. Należy otworzyć kontrolę dostępu (IAM) w zakresie, w którym rola została przypisana, i ponowić próbę. Szybkim sposobem otwarcia kontroli dostępu (IAM) w prawidłowym zakresie jest Przyjrzyj się kolumnie **zakres** i kliknij łącze obok **(Odziedziczone)**.

   ![Usuń komunikat przypisania roli dla dziedziczonych przypisań ról](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal](role-assignments-list-portal.md)
- [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure Portal](quickstart-assign-role-user-portal.md)
- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)
- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)
