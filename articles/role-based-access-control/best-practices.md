---
title: Najważniejsze wskazówki dotyczące usługi Azure RBAC
description: Najważniejsze wskazówki dotyczące korzystania z kontroli dostępu opartej na rolach platformy Azure (Azure RBAC).
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726776"
---
# <a name="best-practices-for-azure-rbac"></a>Najważniejsze wskazówki dotyczące usługi Azure RBAC

W tym artykule opisano niektóre najlepsze rozwiązania dotyczące korzystania z kontroli dostępu opartej na rolach platformy Azure (Azure RBAC). Te najlepsze rozwiązania pochodzą z naszego środowiska z usługą Azure RBAC i środowiska klientów takich jak Ty.

## <a name="only-grant-the-access-users-need"></a>Udziel dostępu tylko użytkownikom dostępu

Za pomocą usługi Azure RBAC, można segregować obowiązki w ramach zespołu i udzielić tylko ilość dostępu do użytkowników, których potrzebują do wykonywania swoich zadań. Zamiast przydzielać wszystkim użytkownikom nieograniczone uprawnienia do subskrypcji lub zasobów platformy Azure możesz zezwolić na wykonywanie w określonym zakresie tylko wybranych czynności.

Najlepszym rozwiązaniem podczas planowania strategii kontroli dostępu jest przyznanie użytkownikom najniższego uprawnienia, które muszą mieć, aby wykonać swoją pracę. Na poniższym diagramie przedstawiono sugerowany wzorzec korzystania z funkcji RBAC.

![RBAC i najniższe uprawnienie](./media/best-practices/rbac-least-privilege.png)

Aby uzyskać informacje dotyczące dodawania przypisań ról, zobacz [Dodawanie lub usuwanie przypisań ról](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Ograniczanie liczby właścicieli subskrypcji

Powinieneś mieć maksymalnie 3 właścicieli subskrypcji, aby zmniejszyć ryzyko naruszenia przez właściciela, którego bezpieczeństwo zostało naruszone. To zalecenie można monitorować w usłudze Azure Security Center. Aby zapoznać się z innymi zaleceniami dotyczącymi tożsamości i dostępu w u centrum zabezpieczeń, zobacz [Zalecenia dotyczące zabezpieczeń — przewodnik po numerach referencyjnych](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Korzystanie z zarządzania tożsamościami uprzywilejowanymi usługi Azure AD

Aby chronić konta uprzywilejowane przed złośliwymi atakami cybernetycznymi, można użyć usługi Azure Active Directory Privileged Identity Management (PIM), aby obniżyć czas narażenia uprawnień i zwiększyć wgląd w ich użycie za pomocą raportów i alertów. Usługa PIM pomaga chronić konta uprzywilejowane, zapewniając uprzywilejowany dostęp do zasobów usługi Azure AD i platformy Azure tylko w czasie. Dostęp może być związany z czasem, po którym uprawnienia są automatycznie odwoływane. 

Aby uzyskać więcej informacji, zobacz [Co to jest zarządzanie tożsamościami uprzywilejowanymi usługi Azure AD?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="next-steps"></a>Następne kroki

- [Rozwiązywanie problemów z usługą Azure RBAC](troubleshooting.md)