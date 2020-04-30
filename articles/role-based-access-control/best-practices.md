---
title: Najlepsze rozwiązania dotyczące usługi Azure RBAC
description: Najlepsze rozwiązania dotyczące korzystania z kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: dc86dd488ff9e8649ae80f4768941791dd37fce6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81726776"
---
# <a name="best-practices-for-azure-rbac"></a>Najlepsze rozwiązania dotyczące usługi Azure RBAC

W tym artykule opisano niektóre najlepsze rozwiązania dotyczące korzystania z kontroli dostępu opartej na rolach (Azure RBAC). Te najlepsze rozwiązania wynikają z naszych rozwiązań dzięki funkcji RBAC platformy Azure i klientom.

## <a name="only-grant-the-access-users-need"></a>Udzielaj tylko użytkownikom dostępu

Korzystając z funkcji RBAC systemu Azure, można oddzielić cła w obrębie zespołu i przyznać dostęp tylko do użytkowników, których potrzebują do wykonywania swoich zadań. Zamiast przydzielać wszystkim użytkownikom nieograniczone uprawnienia do subskrypcji lub zasobów platformy Azure możesz zezwolić na wykonywanie w określonym zakresie tylko wybranych czynności.

Najlepszym rozwiązaniem podczas planowania strategii kontroli dostępu jest przyznanie użytkownikom najniższego uprawnienia, które muszą mieć, aby wykonać swoją pracę. Na poniższym diagramie przedstawiono sugerowany wzorzec korzystania z funkcji RBAC.

![RBAC i najniższe uprawnienie](./media/best-practices/rbac-least-privilege.png)

Informacje o sposobach dodawania przypisań ról można znaleźć w temacie [Dodawanie lub usuwanie przypisań ról](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Ogranicz liczbę właścicieli subskrypcji

Aby zmniejszyć ryzyko naruszenia przez zagrożonego właściciela, należy mieć maksymalnie 3 właścicieli subskrypcji. To zalecenie można monitorować w Azure Security Center. Aby poznać inne zalecenia dotyczące tożsamości i dostępu w Security Center, zobacz [zalecenia dotyczące zabezpieczeń — Podręcznik referencyjny](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Użyj Azure AD Privileged Identity Management

Aby chronić konta uprzywilejowane przed złośliwymi atakami cybernetycznymiymi, możesz użyć Azure Active Directory Privileged Identity Management (PIM), aby skrócić czas ekspozycji uprawnień i zwiększyć widoczność do użycia w raportach i alertach. Usługa PIM pomaga chronić konta uprzywilejowane, zapewniając uprzywilejowany dostęp do usługi Azure AD i zasobów platformy Azure w odpowiednim czasie. Dostęp może być związany z upływem czasu, po którym uprawnienia są automatycznie odwoływane. 

Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)