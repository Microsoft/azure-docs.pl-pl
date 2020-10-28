---
title: Dodawanie lub zmienianie administratorów subskrypcji platformy Azure
description: Opisuje sposób dodawania lub zmieniania administratora subskrypcji platformy Azure przy użyciu kontroli dostępu na podstawie ról (RBAC) platformy Azure.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 10956953f9ab3a9e32b9da4ab8a3501d38b0e2c3
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369662"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Dodawanie lub zmienianie administratorów subskrypcji platformy Azure


Zarządzanie dostępem do zasobów platformy Azure wymaga odpowiedniej roli administratora. Platforma Azure ma system autoryzacji zwany [kontrolą dostępu na podstawie ról platformy Azure (Azure RBAC)](../../role-based-access-control/overview.md) z kilkoma wbudowanymi rolami do wyboru. Te role można przypisywać do różnych zakresów, takich jak grupa zarządzania, subskrypcja lub grupa zasobów. Domyślnie osoba, która tworzy nową subskrypcję platformy Azure, może przypisywać innym użytkownikom dostęp administracyjny do tej subskrypcji.

W tym artykule opisano sposób dodawania lub zmieniania roli administratora dla użytkownika przy użyciu systemu RBAC platformy Azure w zakresie subskrypcji.

Firma Microsoft zaleca, aby zarządzać dostępem do zasobów przy użyciu systemu RBAC platformy Azure. Jeśli jednak nadal korzystasz z klasycznego modelu wdrażania i zarządzasz zasobami klasycznymi przy użyciu [modułu programu PowerShell do zarządzania usługami platformy Azure](/powershell/module/servicemanagement/azure.service), musisz użyć administratora klasycznego.

> [!TIP]
> Jeśli do zarządzania zasobami klasycznymi używasz tylko witryny Azure Portal, nie musisz używać administratora klasycznego.

Aby uzyskać więcej informacji, zobacz [Porównanie usługi Azure Resource Manager i wdrożenia klasycznego](../../azure-resource-manager/management/deployment-models.md) oraz [Klasyczni administratorzy subskrypcji platformy Azure](../../role-based-access-control/classic-administrators.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Przypisywanie administratora subskrypcji

Aby nadać użytkownikowi uprawnienia administratora subskrypcji platformy Azure, istniejący administrator przypisuje mu rolę [Właściciel](../../role-based-access-control/built-in-roles.md#owner) (rolę na platformie Azure) w zakresie subskrypcji. Rola właściciela daje użytkownikowi pełen dostęp do wszystkich zasobów w subskrypcji, w tym prawo do przydzielania dostępu innym osobom. Te kroki są takie same jak w przypadku innych przypisań ról.

Jeśli nie wiesz, kto jest administratorem konta dla subskrypcji, wykonaj poniższe kroki, aby to sprawdzić.

1. Otwórz [stronę Subskrypcje w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Wybierz subskrypcję, którą chcesz sprawdzić, a następnie przejrzyj **Ustawienia** .
1. Wybierz pozycję **Właściwości** . Administrator konta subskrypcji jest wyświetlany w polu **Administrator konta** .

### <a name="to-assign-a-user-as-an-administrator"></a>Aby przypisać użytkownika jako administratora

1. Zaloguj się w witrynie Azure Portal jako właściciel subskrypcji i otwórz sekcję [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Kliknij subskrypcję, do której chcesz udzielić dostępu.

1. Kliknij pozycję **Kontrola dostępu (IAM)** .

1. Kliknij kartę **Przypisania ról** , aby wyświetlić wszystkie przypisania ról dla tej subskrypcji.

    ![Zrzut ekranu pokazujący przypisania ról](./media/add-change-subscription-administrator/role-assignments.png)

1. Kliknij pozycję **Dodaj** > **Dodaj przypisanie roli** , aby otworzyć okienko **Dodawanie przypisania roli** .

    Jeśli nie masz uprawnień do przypisywania ról, ta opcja będzie wyłączona.

1. Z listy rozwijanej **Rola** wybierz rolę **Właściciel** .

1. Z listy **Wybierz** wybierz użytkownika. Jeśli nie widzisz użytkownika na liście, możesz wpisać tekst w polu **Wybierz** , aby wyszukać w katalogu nazwy wyświetlane i adresy e-mail.

    ![Zrzut ekranu przedstawiający wybraną rolę właściciela](./media/add-change-subscription-administrator/add-role.png)

1. Kliknij przycisk **Zapisz** , aby przypisać rolę.

    Po kilku chwilach użytkownikowi zostanie przypisana rola właściciela w zakresie subskrypcji.

## <a name="next-steps"></a>Następne kroki

* [Co to jest kontrola dostępu oparta na rolach na platformie Azure (Azure RBAC)?](../../role-based-access-control/overview.md)
* [Understand the different roles in Azure (Omówienie ról na platformie Azure)](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Kojarzenie subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory lub dodawanie subskrypcji](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Uprawnienia ról administratorów w usłudze Azure Active Directory](../../active-directory/roles/permissions-reference.md)

## <a name="need-help-contact-support"></a>Potrzebujesz pomocy? Kontakt z pomocą techniczną

Jeśli nadal potrzebujesz pomocy, [skontaktuj się z pomocą techniczną](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), aby szybko rozwiązać problem.
