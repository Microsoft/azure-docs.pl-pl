---
title: Przypisywanie ról platformy Azure do tożsamości zarządzanej (wersja zapoznawcza) — RBAC
description: Dowiedz się, jak przypisywać role platformy Azure, rozpoczynając od tożsamości zarządzanej, a następnie wybierając zakres i rolę przy użyciu Azure Portal i kontroli dostępu opartej na rolach platformy Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 57c8c00a64996bc6223fbe7e514db9db38ccdcc2
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556858"
---
# <a name="assign-azure-roles-to-a-managed-identity-preview"></a>Przypisywanie ról platformy Azure do tożsamości zarządzanej (wersja zapoznawcza)

Rolę można przypisać do tożsamości zarządzanej przy użyciu strony **kontroli dostępu (IAM)** , zgodnie z opisem w [przypisywaniu ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md). Korzystając ze strony kontrola dostępu (IAM), rozpoczynasz od zakresu, a następnie wybierasz zarządzaną tożsamość i rolę. W tym artykule opisano alternatywny sposób przypisywania ról dla tożsamości zarządzanej. Korzystając z tych kroków, należy rozpocząć od tożsamości zarządzanej, a następnie wybrać zakres i rolę.

> [!IMPORTANT]
> Przypisywanie roli do tożsamości zarządzanej przy użyciu tych alternatywnych kroków jest obecnie dostępna w wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

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

   ![Dodawanie okienka przypisania roli dla tożsamości zarządzanej przypisanej do systemu](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach zarządzana tożsamość zostanie przypisana do roli w wybranym zakresie.

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

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

   ![Dodawanie okienka przypisanie roli dla tożsamości zarządzanej przypisanej przez użytkownika](./media/role-assignments-portal-managed-identity/add-role-assignment-with-scope.png)

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach zarządzana tożsamość zostanie przypisana do roli w wybranym zakresie.

## <a name="next-steps"></a>Następne kroki

- [Jakie są zarządzane tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- [Przypisywanie ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md)
- [Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal](role-assignments-list-portal.md)
