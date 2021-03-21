---
title: Najlepsze rozwiązania dotyczące usługi Azure RBAC
description: Najlepsze rozwiązania dotyczące korzystania z kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: d58398c42cdc6faed758e5dba3431e0841fc0b03
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555556"
---
# <a name="best-practices-for-azure-rbac"></a>Najlepsze rozwiązania dotyczące usługi Azure RBAC

W tym artykule opisano niektóre najlepsze rozwiązania dotyczące korzystania z kontroli dostępu opartej na rolach (Azure RBAC). Te najlepsze rozwiązania wynikają z naszych rozwiązań dzięki funkcji RBAC platformy Azure i klientom.

## <a name="only-grant-the-access-users-need"></a>Udzielaj tylko użytkownikom dostępu

Przy użyciu kontroli RBAC na platformie Azure można przeprowadzać segregowanie zadań w ramach zespołu i nadawać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań. Zamiast przydzielać wszystkim użytkownikom nieograniczone uprawnienia do subskrypcji lub zasobów platformy Azure możesz zezwolić na wykonywanie w określonym zakresie tylko wybranych czynności.

Najlepszym rozwiązaniem podczas planowania strategii kontroli dostępu jest przyznanie użytkownikom najniższego uprawnienia, które muszą mieć, aby wykonać swoją pracę. Należy unikać przypisywania szerszych ról w szerszych zakresach, nawet jeśli początkowo wydaje się to lepiej wygodniejsze. Podczas tworzenia ról niestandardowych należy uwzględnić tylko uprawnienia wymagane przez użytkowników. Ograniczając role i zakresy, ograniczasz, jakie zasoby są zagrożone, jeśli zabezpieczenia podmiotu zabezpieczeń zostały kiedykolwiek naruszone.

Na poniższym diagramie przedstawiono sugerowany wzorzec używania funkcji RBAC platformy Azure.

![Kontrola RBAC i najniższych uprawnień platformy Azure](./media/best-practices/rbac-least-privilege.png)

Aby uzyskać informacje o sposobach przypisywania ról, zobacz [Przypisywanie ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Ogranicz liczbę właścicieli subskrypcji

Aby zmniejszyć ryzyko naruszenia przez zagrożonego właściciela, należy mieć maksymalnie 3 właścicieli subskrypcji. To zalecenie można monitorować w Azure Security Center. Aby poznać inne zalecenia dotyczące tożsamości i dostępu w Security Center, zobacz [zalecenia dotyczące zabezpieczeń — Podręcznik referencyjny](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Użyj Azure AD Privileged Identity Management

Aby chronić konta uprzywilejowane przed złośliwymi atakami cybernetycznymiymi, możesz użyć Azure Active Directory Privileged Identity Management (PIM), aby skrócić czas ekspozycji uprawnień i zwiększyć widoczność do użycia w raportach i alertach. Usługa PIM pomaga chronić konta uprzywilejowane, zapewniając uprzywilejowany dostęp do usługi Azure AD i zasobów platformy Azure w odpowiednim czasie. Dostęp może być związany z upływem czasu, po którym uprawnienia są automatycznie odwoływane. 

Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="assign-roles-to-groups-not-users"></a>Przypisywanie ról do grup, a nie użytkowników

Aby zarządzać przypisaniami ról, należy unikać przypisywania ról bezpośrednio do użytkowników. Zamiast tego Przypisz role do grup. Przypisywanie ról do grup zamiast użytkowników pozwala również zminimalizować liczbę przypisań ról, które mają [limit 2 000 przypisań ról na subskrypcję](troubleshooting.md#azure-role-assignments-limit). 

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)
