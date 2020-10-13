---
title: Najlepsze rozwiązania dotyczące usługi Azure RBAC
description: Najlepsze rozwiązania dotyczące korzystania z kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2020
ms.author: rolyon
ms.openlocfilehash: 4cb3e1fe0275c676e2ce54ff9201502fc3595937
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91595480"
---
# <a name="best-practices-for-azure-rbac"></a>Najlepsze rozwiązania dotyczące usługi Azure RBAC

W tym artykule opisano niektóre najlepsze rozwiązania dotyczące korzystania z kontroli dostępu opartej na rolach (Azure RBAC). Te najlepsze rozwiązania wynikają z naszych rozwiązań dzięki funkcji RBAC platformy Azure i klientom.

## <a name="only-grant-the-access-users-need"></a>Udzielaj tylko użytkownikom dostępu

Przy użyciu kontroli RBAC na platformie Azure można przeprowadzać segregowanie zadań w ramach zespołu i nadawać użytkownikom tylko takie uprawnienia dostępu, które są im niezbędne do wykonywania zadań. Zamiast przydzielać wszystkim użytkownikom nieograniczone uprawnienia do subskrypcji lub zasobów platformy Azure możesz zezwolić na wykonywanie w określonym zakresie tylko wybranych czynności.

Najlepszym rozwiązaniem podczas planowania strategii kontroli dostępu jest przyznanie użytkownikom najniższego uprawnienia, które muszą mieć, aby wykonać swoją pracę. Należy unikać przypisywania szerszych ról w szerszych zakresach, nawet jeśli początkowo wydaje się to lepiej wygodniejsze. Ograniczając role i zakresy ograniczają, jakie zasoby są zagrożone w przypadku naruszenia zabezpieczeń podmiotu zabezpieczeń.

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