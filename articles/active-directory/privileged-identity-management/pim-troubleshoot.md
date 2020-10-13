---
title: Rozwiązywanie problemów z Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z błędami systemu przy użyciu ról w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: b02b8f3b94d191c7439aadf5a56ec0e2a1c38e28
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88782502"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Rozwiązywanie problemów z usługą Privileged Identity Management

Czy masz problem z usługą Privileged Identity Management (PIM) w usłudze Azure Active Directory (Azure AD)? Poniższe informacje mogą pomóc w ponownym uruchomieniu.

## <a name="access-to-azure-resources-denied"></a>Odmowa dostępu do zasobów platformy Azure

### <a name="problem"></a>Problem

Jako aktywny właściciel lub administrator dostępu użytkowników dla zasobu platformy Azure można zobaczyć zasób w Privileged Identity Management, ale nie może wykonać żadnych akcji, takich jak przypisanie kwalifikującego się przypisania lub wyświetlenie listy przypisań ról na stronie Przegląd zasobów. Każda z tych akcji powoduje błąd autoryzacji.

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, gdy rola administratora dostępu użytkownika dla jednostki usługi PIM została przypadkowo usunięta z subskrypcji. Aby usługa Privileged Identity Management mogła uzyskać dostęp do zasobów platformy Azure, jednostka usługi MS-PIM powinna mieć zawsze przypisaną [rolę administratora dostępu użytkownika](../../role-based-access-control/built-in-roles.md#user-access-administrator) w ramach subskrypcji platformy Azure.

### <a name="resolution"></a>Rozwiązanie

Przypisz rolę Administrator dostępu użytkowników do nazwy głównej usługi Privileged Identity Management (MS — PIM) na poziomie subskrypcji. To przypisanie powinno umożliwić usłudze Privileged Identity Management dostęp do zasobów platformy Azure. Rolę można przypisywać na poziomie grupy zarządzania lub na poziomie subskrypcji, w zależności od wymagań. Aby uzyskać więcej informacji o nazwach głównych usługi, zobacz [przypisywanie aplikacji do roli](../develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application).

## <a name="next-steps"></a>Następne kroki

- [Wymagania licencyjne dotyczące używania Privileged Identity Management](subscription-requirements.md)
- [Zabezpieczanie uprzywilejowanego dostępu dla wdrożeń hybrydowych i wdrożeń w chmurze w usłudze Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Wdrażanie usługi Privileged Identity Management](pim-deployment-plan.md)