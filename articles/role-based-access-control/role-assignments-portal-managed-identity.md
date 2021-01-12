---
title: Dodawanie przypisania roli dla tożsamości zarządzanej (wersja zapoznawcza) — Kontrola RBAC platformy Azure
description: Dowiedz się, jak dodać przypisanie roli, rozpoczynając od tożsamości zarządzanej, a następnie wybierając zakres i rolę przy użyciu Azure Portal i kontroli dostępu opartej na rolach platformy Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: a01246c0cf35653f4d13262183cf9df28b056c69
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122238"
---
# <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Dodawanie przypisania roli dla tożsamości zarządzanej (wersja zapoznawcza)

Można dodać przypisania ról dla tożsamości zarządzanej przy użyciu strony **kontroli dostępu (IAM)** , zgodnie z opisem w temacie [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md). Korzystając ze strony kontrola dostępu (IAM), rozpoczynasz od zakresu, a następnie wybierasz zarządzaną tożsamość i rolę. W tym artykule opisano alternatywną metodę dodawania przypisań ról do tożsamości zarządzanej. Korzystając z tych kroków, należy rozpocząć od tożsamości zarządzanej, a następnie wybrać zakres i rolę.

> [!IMPORTANT]
> Dodawanie przypisania roli dla tożsamości zarządzanej przy użyciu tych alternatywnych kroków jest obecnie w wersji zapoznawczej.
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
- [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu witryny Azure Portal](role-assignments-portal.md)
- [Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal](role-assignments-list-portal.md)
