---
title: Przypisywanie użytkownika jako administratora subskrypcji platformy Azure — RBAC
description: Dowiedz się, jak sprawić, aby użytkownik był administratorem subskrypcji platformy Azure przy użyciu Azure Portal i kontroli dostępu opartej na rolach (RBAC) na platformie Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556831"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Przypisywanie użytkownika jako administratora subskrypcji platformy Azure

Aby użytkownik był administratorem subskrypcji platformy Azure, przypisz im rolę [właściciela](built-in-roles.md#owner) w zakresie subskrypcji. Rola właściciela daje użytkownikowi pełny dostęp do wszystkich zasobów w ramach subskrypcji, w tym uprawnienia do udzielania dostępu innym osobom. Te kroki są takie same jak w przypadku innych przypisań ról.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Krok 1. Otwieranie subskrypcji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania u góry Wyszukaj pozycję subskrypcje.

    ![Azure Portal wyszukiwanie grupy zasobów](./media/shared/sub-portal-search.png)

1. Kliknij subskrypcję, której chcesz użyć.

    Poniżej przedstawiono przykładową subskrypcję.

    ![Przegląd grupy zasobów](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>Krok 2. Otwieranie okienka Dodawanie przypisania roli

**Kontrola dostępu (IAM)** to strona, której zwykle używasz do przypisywania ról w celu udzielenia dostępu do zasobów platformy Azure. Jest on również znany jako Zarządzanie tożsamościami i dostępem (IAM) i pojawia się w kilku lokalizacjach w Azure Portal.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

    Poniżej przedstawiono przykład strony kontroli dostępu (IAM) dla subskrypcji.

    ![Strona kontroli dostępu (IAM) dla grupy zasobów](./media/shared/sub-access-control.png)

1. Kliknij kartę **przypisania roli** , aby wyświetlić przypisania ról w tym zakresie.

1. Kliknij przycisk **Dodaj**  >  **Dodaj przypisanie roli**.
   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj przypisanie roli](./media/shared/add-role-assignment-menu.png)

    Zostanie otwarte okienko Dodawanie przypisania roli.

   ![Okienko Dodawanie przypisania roli](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>Krok 3. Wybieranie roli właściciela

Rola [właściciela](built-in-roles.md#owner) przyznaje pełen dostęp do zarządzania wszystkimi zasobami, w tym możliwość przypisywania ról w usłudze Azure RBAC. Aby zmniejszyć ryzyko naruszenia przez zagrożonego właściciela, należy mieć maksymalnie 3 właścicieli subskrypcji.

- Na liście **rola** wybierz rolę **właściciela** .

   ![Wybierz rolę właściciela w okienku Dodaj przypisanie roli](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>Krok 4. Wybieranie osób, które wymagają dostępu

1. Na liście **Przypisz dostęp do** wybierz pozycję **użytkownik, Grupa lub nazwa główna usługi**.

1. W sekcji **Wybierz** Wyszukaj użytkownika, wprowadzając ciąg lub przewijając listę.

   ![Wybierz użytkownika w obszarze Dodaj przypisanie roli](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Po znalezieniu użytkownika kliknij go, aby go zaznaczyć.

## <a name="step-5-assign-role"></a>Krok 5. Przypisywanie roli

1. Aby przypisać rolę, kliknij przycisk **Zapisz**.

   Po kilku chwilach użytkownik zostanie przypisany do roli w wybranym zakresie.

1. Na karcie **przypisania ról** Sprawdź, czy na liście znajduje się przypisanie roli.

    ![Zapisano dodanie przypisania roli](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md)
- [Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal](role-assignments-list-portal.md)
- [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../governance/management-groups/overview.md)
