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
ms.openlocfilehash: 00fd00112dad9efc31cea83f69bb458a8e1ca935
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617378"
---
# <a name="best-practices-for-azure-rbac"></a>Najlepsze rozwiązania dotyczące usługi Azure RBAC

W tym artykule opisano niektóre najlepsze rozwiązania dotyczące korzystania z kontroli dostępu opartej na rolach (Azure RBAC). Te najlepsze rozwiązania wynikają z naszych rozwiązań dzięki funkcji RBAC platformy Azure i klientom.

## <a name="only-grant-the-access-users-need"></a>Udzielaj tylko użytkownikom dostępu

Przy użyciu kontroli RBAC na platformie Azure można przeprowadzać segregowanie zadań w ramach zespołu i nadawać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań. Zamiast przydzielać wszystkim użytkownikom nieograniczone uprawnienia do subskrypcji lub zasobów platformy Azure możesz zezwolić na wykonywanie w określonym zakresie tylko wybranych czynności.

Najlepszym rozwiązaniem podczas planowania strategii kontroli dostępu jest przyznanie użytkownikom najniższego uprawnienia, które muszą mieć, aby wykonać swoją pracę. Należy unikać przypisywania szerszych ról w szerszych zakresach, nawet jeśli początkowo wydaje się to lepiej wygodniejsze. Podczas tworzenia ról niestandardowych należy uwzględnić tylko uprawnienia wymagane przez użytkowników. Ograniczając role i zakresy, ograniczasz, jakie zasoby są zagrożone, jeśli zabezpieczenia podmiotu zabezpieczeń zostały kiedykolwiek naruszone.

Na poniższym diagramie przedstawiono sugerowany wzorzec używania funkcji RBAC platformy Azure.

![Kontrola RBAC i najniższych uprawnień platformy Azure](./media/best-practices/rbac-least-privilege.png)

Informacje o sposobach dodawania przypisań ról znajdują się w temacie [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Ogranicz liczbę właścicieli subskrypcji

Aby zmniejszyć ryzyko naruszenia przez zagrożonego właściciela, należy mieć maksymalnie 3 właścicieli subskrypcji. To zalecenie można monitorować w Azure Security Center. Aby poznać inne zalecenia dotyczące tożsamości i dostępu w Security Center, zobacz [zalecenia dotyczące zabezpieczeń — Podręcznik referencyjny](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Użyj Azure AD Privileged Identity Management

Aby chronić konta uprzywilejowane przed złośliwymi atakami cybernetycznymiymi, możesz użyć Azure Active Directory Privileged Identity Management (PIM), aby skrócić czas ekspozycji uprawnień i zwiększyć widoczność do użycia w raportach i alertach. Usługa PIM pomaga chronić konta uprzywilejowane, zapewniając uprzywilejowany dostęp do usługi Azure AD i zasobów platformy Azure w odpowiednim czasie. Dostęp może być związany z upływem czasu, po którym uprawnienia są automatycznie odwoływane. 

Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)