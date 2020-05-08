---
title: Dodawanie i usuwanie przypisań ról platformy Azure przy użyciu Azure Portal — RBAC
description: Dowiedz się, jak udzielić dostępu do zasobów platformy Azure dla użytkowników, grup, nazw głównych usług lub tożsamości zarządzanych przy użyciu Azure Portal i kontroli dostępu opartej na rolach (RBAC) platformy Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 88cb1e9893e0ca058e57ce12f00747a59248fb19
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735695"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Dodawanie i usuwanie przypisań ról platformy Azure przy użyciu Azure Portal

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]W tym artykule opisano sposób przypisywania ról przy użyciu Azure Portal.

Jeśli musisz przypisać role administratorów w Azure Active Directory, zobacz [Wyświetlanie i przypisywanie ról administratorów w Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby dodać lub usunąć przypisania ról, musisz mieć:

- `Microsoft.Authorization/roleAssignments/write`i `Microsoft.Authorization/roleAssignments/delete` uprawnienia, takie jak [administrator dostępu użytkowników](built-in-roles.md#user-access-administrator) lub [właściciel](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Kontrola dostępu (IAM)

**Kontrola dostępu (IAM)** to blok służący do przypisywania ról w celu udzielania dostępu do zasobów platformy Azure. Jest on również znany jako Zarządzanie tożsamościami i dostępem i pojawia się w kilku lokalizacjach w Azure Portal. Poniżej przedstawiono przykład bloku kontroli dostępu (IAM) dla subskrypcji.

![Blok kontroli dostępu (IAM) dla subskrypcji](./media/role-assignments-portal/access-control-subscription.png)

Aby najbardziej efektywnie korzystać z bloku kontroli dostępu (IAM), można uzyskać odpowiedzi na następujące trzy pytania podczas próby przypisania roli:

1. **Kto musi mieć dostęp?**

    Kto odwołuje się do użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej. Jest to również nazywane *podmiotem zabezpieczeń*.

1. **Jaką rolę potrzebują?**

    Uprawnienia są pogrupowane w role. Możesz wybrać jedną z listy [wbudowanych ról](built-in-roles.md) lub użyć własnych ról niestandardowych.

1. **Gdzie są potrzebne dostęp?**

    Gdzie odwołuje się do zestawu zasobów, do którego odnosi się ten dostęp. Gdzie może być grupą zarządzania, subskrypcją, grupą zasobów lub pojedynczym zasobem, takim jak konto magazynu. Jest to nazywane *zakresem*.

## <a name="add-a-role-assignment"></a>Dodaj przypisanie roli

W celu udzielenia dostępu do zasobu platformy Azure w usłudze Azure RBAC należy dodać przypisanie roli. Wykonaj następujące kroki, aby przypisać rolę.

1. W Azure Portal kliknij pozycję **wszystkie usługi** , a następnie wybierz zakres, do którego chcesz udzielić dostępu. Można na przykład wybrać **grupy zarządzania**, **subskrypcje**, **grupy zasobów**lub zasób.

1. Kliknij konkretny zasób dla tego zakresu.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **przypisania roli** , aby wyświetlić przypisania ról w tym zakresie.

    ![Kontrola dostępu (IAM) i przypisywanie ról — karta](./media/role-assignments-portal/role-assignments.png)

1. Kliknij przycisk **Dodaj** > **Dodaj przypisanie roli**.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj](./media/role-assignments-portal/add-menu.png)

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

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **przypisania ról** , aby wyświetlić przypisania ról dla tej subskrypcji.

    ![Kontrola dostępu (IAM) i przypisywanie ról — karta](./media/role-assignments-portal/role-assignments.png)

1. Kliknij przycisk **Dodaj** > **Dodaj przypisanie roli**.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj](./media/role-assignments-portal/add-menu.png)

    Zostanie otwarte okienko Dodawanie przypisania roli.

   ![Okienko Dodawanie przypisania roli](./media/role-assignments-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz rolę **Właściciel**.

1. Z listy **Wybierz** wybierz użytkownika. Jeśli nie widzisz użytkownika na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane i adresy e-mail.

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach użytkownikowi zostanie przypisana rola właściciela w zakresie subskrypcji.

## <a name="remove-a-role-assignment"></a>Usuwanie przypisania roli

W celu usunięcia dostępu z zasobów platformy Azure w usłudze Azure RBAC należy usunąć przypisanie roli. Wykonaj następujące kroki, aby usunąć przypisanie roli.

1. Otwórz **kontrolę dostępu (IAM)** w zakresie, na przykład grupy zarządzania, subskrypcji, grupy zasobów lub zasobu, w której chcesz usunąć dostęp.

1. Kliknij kartę **Przypisania ról**, aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.

1. Na liście przypisań ról dodaj znacznik wyboru obok podmiotu zabezpieczeń z przypisaniem roli, które chcesz usunąć.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Kliknij pozycję **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment.png)

1. W wyświetlonym komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

    Jeśli zobaczysz komunikat, że nie można usunąć przypisań ról dziedziczonych, próbujesz usunąć przypisanie roli w zakresie podrzędnym. Należy otworzyć kontrolę dostępu (IAM) w zakresie, w którym rola została przypisana, i ponowić próbę. Szybkim sposobem otwarcia kontroli dostępu (IAM) w prawidłowym zakresie jest Przyjrzyj się kolumnie **zakres** i kliknij łącze obok **(Odziedziczone)**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Następne kroki

- [Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal](role-assignments-list-portal.md)
- [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure Portal](quickstart-assign-role-user-portal.md)
- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)
- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)
