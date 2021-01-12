---
title: Dodawanie i usuwanie przypisań ról platformy Azure przy użyciu Azure Portal — RBAC
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu Azure Portal i kontroli dostępu opartej na rolach (RBAC) platformy Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: f1753e7bc50fa9ff2c5512696a37dae7578f23b4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117448"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] W tym artykule opisano sposób przypisywania ról przy użyciu Azure Portal.

Jeśli musisz przypisać role administratorów w Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W celu udzielenia dostępu do zasobu platformy Azure w usłudze Azure RBAC należy dodać przypisanie roli. Wykonaj następujące kroki, aby przypisać rolę. Ogólne omówienie kroków można znaleźć w temacie [kroki umożliwiające dodanie przypisania roli](role-assignments-steps.md).

### <a name="step-1-identify-the-needed-scope"></a>Krok 1. identyfikowanie wymaganego zakresu

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Aby uzyskać więcej informacji na temat zakresu, zobacz [Opis zakresu](scope-overview.md).

![Poziomy zakresu dla usługi Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania u góry Wyszukaj zakres, do którego chcesz udzielić dostępu. Na przykład wyszukaj **grupy zarządzania**, **subskrypcje**, **grupy zasobów** lub określony zasób.

    ![Azure Portal wyszukiwanie grupy zasobów](./media/shared/rg-portal-search.png)

1. Kliknij konkretny zasób dla tego zakresu.

    Poniżej przedstawiono przykładową grupę zasobów.

    ![Przegląd grupy zasobów](./media/shared/rg-overview.png)

### <a name="step-2-open-the-add-role-assignment-pane"></a>Krok 2. Otwieranie okienka Dodawanie przypisania roli

**Kontrola dostępu (IAM)** to strona, której zwykle używasz do przypisywania ról w celu udzielenia dostępu do zasobów platformy Azure. Jest on również znany jako Zarządzanie tożsamościami i dostępem (IAM) i pojawia się w kilku lokalizacjach w Azure Portal.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

    Poniżej przedstawiono przykład strony kontroli dostępu (IAM) dla grupy zasobów.

    ![Strona kontroli dostępu (IAM) dla grupy zasobów](./media/shared/rg-access-control.png)

1. Kliknij kartę **przypisania roli** , aby wyświetlić przypisania ról w tym zakresie.

1. Kliknij przycisk **Dodaj**  >  **Dodaj przypisanie roli**.
   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj przypisanie roli](./media/shared/add-role-assignment-menu.png)

    Zostanie otwarte okienko Dodawanie przypisania roli.

   ![Okienko Dodawanie przypisania roli](./media/shared/add-role-assignment.png)

### <a name="step-3-select-the-appropriate-role"></a>Krok 3. Wybierz odpowiednią rolę

1. Na liście **rola** Wyszukaj lub przewiń, aby znaleźć rolę, którą chcesz przypisać.

    Aby ułatwić określenie odpowiedniej roli, możesz umieścić kursor na ikonie informacji, aby wyświetlić opis roli. Aby uzyskać dodatkowe informacje, możesz wyświetlić artykuł [role wbudowane platformy Azure](built-in-roles.md) .

   ![Wybierz rolę w obszarze Dodaj przypisanie roli](./media/role-assignments-portal/add-role-assignment-role.png)

1. Kliknij, aby wybrać rolę.

### <a name="step-4-select-who-needs-access"></a>Krok 4. Wybieranie osób, które wymagają dostępu

1. Z listy **Przypisz dostęp do** wybierz typ podmiotu zabezpieczeń, do którego ma zostać przypisany dostęp.

    | Typ | Opis |
    | --- | --- |
    | **Użytkownik, Grupa lub nazwa główna usługi** | Jeśli chcesz przypisać rolę do użytkownika, grupy lub nazwy głównej usługi (aplikacji), wybierz ten typ. |
    | **Tożsamość zarządzana przypisana przez użytkownika** | Jeśli chcesz przypisać rolę do [tożsamości zarządzanej przypisanej przez użytkownika](../active-directory/managed-identities-azure-resources/overview.md), wybierz ten typ. |
    | *Tożsamość zarządzana przypisana przez system* | Jeśli chcesz przypisać rolę do [tożsamości zarządzanej przypisanej do systemu](../active-directory/managed-identities-azure-resources/overview.md), wybierz wystąpienie usługi platformy Azure, w którym znajduje się tożsamość zarządzana. |

   ![Wybierz typ podmiotu zabezpieczeń w polu Dodaj przypisanie roli](./media/role-assignments-portal/add-role-assignment-type.png)

1. W przypadku wybrania tożsamości zarządzanej przypisanej przez użytkownika lub tożsamości zarządzanej przypisanej do systemu wybierz **subskrypcję** , w której znajduje się tożsamość zarządzana.

1. W sekcji **Wybierz** Wyszukaj podmiot zabezpieczeń, wprowadzając ciąg lub przewijając listę.

   ![Wybierz użytkownika w obszarze Dodaj przypisanie roli](./media/role-assignments-portal/add-role-assignment-user.png)

1. Po znalezieniu podmiotu zabezpieczeń kliknij go, aby go zaznaczyć.

### <a name="step-5-assign-role"></a>Krok 5. Przypisywanie roli

1. Aby przypisać rolę, kliknij przycisk **Zapisz**.

   Po kilku chwilach podmiot zabezpieczeń ma przypisaną rolę w wybranym zakresie.

1. Na karcie **przypisania ról** Sprawdź, czy na liście znajduje się przypisanie roli.

    ![Zapisano dodanie przypisania roli](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

W celu usunięcia dostępu z zasobów platformy Azure w usłudze Azure RBAC należy usunąć przypisanie roli. Wykonaj następujące kroki, aby usunąć przypisanie roli.

1. Otwórz **kontrolę dostępu (IAM)** w zakresie, na przykład grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, w której chcesz usunąć dostęp.

1. Kliknij kartę **przypisania roli** , aby wyświetlić wszystkie przypisania ról w tym zakresie.

1. Na liście przypisań ról dodaj znacznik wyboru obok podmiotu zabezpieczeń z przypisaniem roli, które chcesz usunąć.

   ![Przypisanie roli wybrane do usunięcia](./media/role-assignments-portal/rg-role-assignments-select.png)

1. Kliknij przycisk **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment.png)

1. W wyświetlonym komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

    Jeśli zobaczysz komunikat, że nie można usunąć przypisań ról dziedziczonych, próbujesz usunąć przypisanie roli w zakresie podrzędnym. Należy otworzyć kontrolę dostępu (IAM) w zakresie, w którym rola została przypisana, i ponowić próbę. Szybkim sposobem otwarcia kontroli dostępu (IAM) w prawidłowym zakresie jest Przyjrzyj się kolumnie **zakres** i kliknij łącze obok **(Odziedziczone)**.

   ![Usuń komunikat przypisania roli dla dziedziczonych przypisań ról](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie użytkownika jako administratora subskrypcji platformy Azure](role-assignments-portal-subscription-admin.md)
- [Dodawanie przypisania roli dla tożsamości zarządzanej](role-assignments-portal-managed-identity.md)
- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)
