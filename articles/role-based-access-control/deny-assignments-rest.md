---
title: Wyświetlanie listy przypisań Odmów platformy Azure przy użyciu interfejsu API REST — RBAC
description: Dowiedz się, jak wyświetlić listę przypisań Odmów na platformie Azure dla użytkowników, grup i aplikacji przy użyciu interfejsu API REST i kontroli dostępu opartej na rolach platformy Azure (RBAC).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2f835c270930734bf9963a7c7c3168b873eddaf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84791915"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>Wyświetlanie listy przypisań Odmów platformy Azure przy użyciu interfejsu API REST

[Przydziały Odmów na platformie Azure](deny-assignments.md) blokują użytkownikom możliwość wykonywania określonych akcji zasobów platformy Azure nawet wtedy, gdy przypisanie roli udzielą im dostępu. W tym artykule opisano sposób wyświetlania listy zablokowanych przypisań przy użyciu interfejsu API REST.

> [!NOTE]
> Nie można bezpośrednio tworzyć własnych przypisań Odmów. Aby uzyskać informacje na temat sposobu tworzenia przypisań Odmów, zobacz [Azure Deny przypisań](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu odmowy, należy dysponować:

- `Microsoft.Authorization/denyAssignments/read` uprawnienie, które jest zawarte w większości [wbudowanych ról platformy Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Utwórz listę jednego przypisania odmowy

1. Rozpocznij od następującego żądania:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę przypisań Odmów.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subskrypcja |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp *ciąg {Deny-przypisania-ID}* identyfikatorem przypisania Odmów, który chcesz pobrać.

## <a name="list-multiple-deny-assignments"></a>Wyświetl wiele przypisań Odmów

1. Zacznij od jednego z następujących żądań:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Z opcjonalnymi parametrami:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. W identyfikatorze URI Zastąp wartość *{SCOPE}* zakresem, dla którego chcesz wyświetlić listę przypisań Odmów.

    > [!div class="mx-tableFixed"]
    > | Zakres | Typ |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Subskrypcja |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Grupa zasobów |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Zasób |

1. Zastąp wartość *{Filter}* warunkiem, którą chcesz zastosować, aby odfiltrować listę przypisań Odmów.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | (brak filtru) | Wyświetla wszystkie przydziały Odmów w, powyżej i poniżej określonego zakresu. |
    > | `$filter=atScope()` | Wyświetla listę odrzuconych przypisań tylko dla określonego zakresu i powyżej. Nie obejmuje przypisań Odmów w podzakresach. |
    > | `$filter=assignedTo('{objectId}')` | Wyświetla listę odrzuconych przypisań dla określonego użytkownika lub nazwy głównej usługi.<br/>Jeśli użytkownik jest członkiem grupy, która ma przypisanie odmowy, to na liście zostanie również przypisane przypisanie Odmów. Ten filtr jest przechodni dla grup, co oznacza, że jeśli użytkownik jest członkiem grupy, a grupa jest członkiem innej grupy, która ma przypisanie odmowy, to na liście jest również przypisane przypisanie odmowy.<br/>Ten filtr akceptuje tylko identyfikator obiektu dla użytkownika lub nazwy głównej usługi. Nie można przekazać identyfikatora obiektu dla grupy. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Wyświetla listę odrzuconych przypisań dla określonego użytkownika lub nazwy głównej usługi i w określonym zakresie. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Wyświetla listę odrzuconych przypisań o określonej nazwie. |
    > | `$filter=principalId+eq+'{objectId}'` | Wyświetla listę odrzuconych przypisań dla określonego użytkownika, grupy lub nazwy głównej usługi. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Wyświetl listę odrzuconych przypisań w zakresie głównym (/)

1. Podnieś poziom dostępu zgodnie z opisem w temacie [Podnieś poziom dostępu do zarządzania wszystkimi subskrypcjami i grupami zarządzania platformy Azure](elevate-access-global-admin.md).

1. Użyj następującego żądania:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Zastąp wartość *{Filter}* warunkiem, którą chcesz zastosować, aby odfiltrować listę przypisań Odmów. Wymagany jest filtr.

    > [!div class="mx-tableFixed"]
    > | Filtr | Opis |
    > | --- | --- |
    > | `$filter=atScope()` | Wyświetl listę odrzuconych przypisań tylko dla zakresu głównego. Nie obejmuje przypisań Odmów w podzakresach. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Wyświetl listę odrzuconych przypisań o określonej nazwie. |

1. Usuń dostęp z podwyższonym poziomem uprawnień.

## <a name="next-steps"></a>Następne kroki

- [Informacje o przypisaniach Odmów na platformie Azure](deny-assignments.md)
- [Podnoszenie poziomu dostępu w celu zarządzania wszystkimi subskrypcjami platformy Azure i grupami zarządzania](elevate-access-global-admin.md)
- [Dokumentacja interfejsu API REST platformy Azure](/rest/api/azure/)
