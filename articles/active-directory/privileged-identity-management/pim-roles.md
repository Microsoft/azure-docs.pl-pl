---
title: Role, którymi nie można zarządzać w Privileged Identity Management Azure Active Directory | Microsoft Docs
description: Opisuje role, którymi nie można zarządzać w programie Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72587c5486ed61215fd20c215a1dd194f4b7bc4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92372416"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Role, którymi nie można zarządzać w Privileged Identity Management

Usługa Azure Active Directory (Azure AD) Privileged Identity Management (PIM) umożliwia zarządzanie wszystkimi [rolami usługi Azure AD](../roles/permissions-reference.md) i wszystkimi [rolami platformy Azure](../../role-based-access-control/built-in-roles.md). Role platformy Azure mogą również obejmować niestandardowe role dołączone do grup zarządzania, subskrypcji, grup zasobów i zasobów. Istnieje jednak kilka ról, których nie można zarządzać. W tym artykule opisano role, którymi nie można zarządzać w programie Privileged Identity Management.

## <a name="classic-subscription-administrator-roles"></a>Role klasycznego administratora subskrypcji

W Privileged Identity Management nie można zarządzać następującymi rolami klasycznych administratorów subskrypcji:

- Administrator konta
- Administrator usługi
- Współadministrator

Aby uzyskać więcej informacji na temat ról administratora klasycznej subskrypcji, zobacz Role [administratora subskrypcji klasycznej, role platformy Azure i role administratorów usługi Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-microsoft-365-admin-roles"></a>Co Microsoft 365 role administratorów?

Obsługiwane są wszystkie role Microsoft 365 w interfejsie role usługi Azure AD i portalu administratorów, takie jak administrator programu Exchange i administrator programu SharePoint, ale nie są obsługiwane określone role w ramach usług Exchange RBAC lub RBAC programu SharePoint. Aby uzyskać więcej informacji na temat tych Microsoft 365 usług, zobacz [Microsoft 365 role administratorów](/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> Uprawnieni użytkownicy dla roli administratora programu SharePoint, roli administratora urządzenia i wszystkich ról próbujących uzyskać dostęp do centrum zabezpieczeń i zgodności firmy Microsoft mogą napotkać opóźnienia nawet kilka godzin po aktywowaniu ich roli. Pracujemy nad tymi zespołami, aby rozwiązać te problemy.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Przypisywanie ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-assign-roles.md)