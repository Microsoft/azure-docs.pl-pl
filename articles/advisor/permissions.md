---
title: Uprawnienia w Azure Advisor
description: Uprawnienia usługi Advisor oraz sposób, w jaki mogą blokować możliwość konfigurowania subskrypcji lub odroczenia lub odrzucania zaleceń.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 402a21c47c4cba8f747d5d4601f9c95034c99262
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91712924"
---
# <a name="permissions-in-azure-advisor"></a>Uprawnienia w Azure Advisor

Azure Advisor zapewnia zalecenia na podstawie użycia i konfiguracji zasobów i subskrypcji platformy Azure. Usługa Advisor korzysta z [wbudowanych ról](../role-based-access-control/built-in-roles.md) udostępnianych przez funkcję [kontroli dostępu opartej na ROLACH (Azure RBAC)](../role-based-access-control/overview.md) w celu zarządzania dostępem do zaleceń i funkcji usługi Advisor. 

## <a name="roles-and-their-access"></a>Role i ich dostęp

W poniższej tabeli zdefiniowano role i dostęp do nich w usłudze Advisor:

| **Role** | **Wyświetl rekomendacje** | **Edytuj reguły** | **Edytuj konfigurację subskrypcji** | **Edytuj konfigurację grupy zasobów**| **Odrzuć i odłóż zalecenia**|
|---|:---:|:---:|:---:|:---:|:---:|
|Właściciel subskrypcji|**X**|**X**|**X**|**X**|**X**|
|Współautor subskrypcji|**X**|**X**|**X**|**X**|**X**|
|Czytelnik subskrypcji|**X**|--|--|--|--|
|Właściciel grupy zasobów|**X**|--|--|**X**|**X**|
|Współautor grupy zasobów|**X**|--|--|**X**|**X**|
|Czytelnik grupy zasobów|**X**|--|--|--|--|
|Właściciel zasobu|**X**|--|--|--|**X**|
|Współautor zasobów|**X**|--|--|--|**X**|
|Czytnik zasobów|**X**|--|--|--|--|

> [!NOTE]
> Dostęp do zaleceń dotyczących widoku zależy od dostępu do zasobu, którego dotyczy problem.

## <a name="permissions-and-unavailable-actions"></a>Uprawnienia i niedostępne akcje

Brak prawidłowych uprawnień może blokować możliwość wykonywania działań w usłudze Advisor. Poniżej przedstawiono niektóre typowe problemy.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Nie można skonfigurować subskrypcji lub grup zasobów

Podczas próby skonfigurowania subskrypcji lub grup zasobów w usłudze Advisor można zobaczyć, że opcja dołączania lub wykluczania jest wyłączona. Ten stan oznacza, że nie masz wystarczającego poziomu uprawnień dla tej grupy zasobów lub subskrypcji. Aby rozwiązać ten problem, Dowiedz się, jak [udzielić użytkownikowi dostępu](../role-based-access-control/quickstart-assign-role-user-portal.md).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Nie można odłożyć lub odrzucić zalecenia

Jeśli wystąpi błąd podczas próby odroczenia lub odrzucenia zalecenia, możesz nie mieć wystarczających uprawnień. Upewnij się, że masz co najmniej dostęp współautora do zasobu, którego to dotyczy, lub którego nie brakuje. Aby rozwiązać ten problem, Dowiedz się, jak [udzielić użytkownikowi dostępu](../role-based-access-control/quickstart-assign-role-user-portal.md).

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera omówienie sposobu korzystania z usługi Azure RBAC do kontrolowania uprawnień użytkowników i rozwiązywania typowych problemów. Aby dowiedzieć się więcej na temat usługi Advisor, zobacz:

- [Co to jest usługa Azure Advisor?](./advisor-overview.md)
- [Wprowadzenie do usługi Azure Advisor](./advisor-get-started.md)
