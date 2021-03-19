---
title: Informacje o przypisaniach Odmów na platformie Azure — kontrola RBAC platformy Azure
description: Dowiedz się więcej o przypisaniach Odmów na platformie Azure w kontroli dostępu opartej na rolach (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: a5f17f009caa9306631debf511f2c890f8f2a450
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82733777"
---
# <a name="understand-azure-deny-assignments"></a>Informacje o przypisaniach Odmów na platformie Azure

Podobnie jak przypisanie roli, *przypisanie odmowy* dołącza zestaw akcji Odmów do użytkownika, grupy lub jednostki usługi w określonym zakresie w celu odmowy dostępu. Odmowa przypisania uniemożliwia użytkownikom wykonywanie określonych akcji zasobów platformy Azure nawet wtedy, gdy przypisanie roli przyznaje im dostęp.

W tym artykule opisano sposób definiowania przypisań Odmów.

## <a name="how-deny-assignments-are-created"></a>Sposób tworzenia przypisań Odmów

Przypisywanie Odmów jest tworzone i zarządzane przez platformę Azure w celu ochrony zasobów. Plany platformy Azure i aplikacje zarządzane przez platformę Azure używają przypisań Odmów do ochrony zasobów zarządzanych przez system. Plany platformy Azure i aplikacje zarządzane przez platformę Azure są jedynym sposobem tworzenia przypisań Odmów. Nie można bezpośrednio tworzyć własnych przypisań Odmów. Aby uzyskać więcej informacji na temat sposobu, w jaki plany używają przypisań Odmów do blokowania zasobów, zobacz [Omówienie blokowania zasobów w planach platformy Azure](../governance/blueprints/concepts/resource-locking.md).

> [!NOTE]
> Nie można bezpośrednio tworzyć własnych przypisań Odmów.

## <a name="compare-role-assignments-and-deny-assignments"></a>Porównanie przypisań ról i odrzucania przypisań

Odmowa przypisania jest zgodna z podobnym wzorcem jako przypisaniem roli, ale również zawiera pewne różnice.

| Możliwość | Przypisanie roli | Odmów przypisania |
| --- | --- | --- |
| Udzielanie dostępu | :heavy_check_mark: |  |
| Odmowa dostępu |  | :heavy_check_mark: |
| Można utworzyć bezpośrednio | :heavy_check_mark: |  |
| Zastosuj w zakresie | :heavy_check_mark: | :heavy_check_mark: |
| Wyklucz podmioty zabezpieczeń |  | :heavy_check_mark: |
| Zapobiegaj dziedziczeniu do zakresów podrzędnych |  | :heavy_check_mark: |
| Zastosuj do [klasycznych](rbac-and-directory-admin-roles.md) przypisań administratora subskrypcji |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Właściwości przypisania Odmów

 Przypisanie Odmów ma następujące właściwości:

> [!div class="mx-tableFixed"]
> | Właściwość | Wymagany | Typ | Opis |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Tak | Ciąg | Nazwa wyświetlana przypisania Odmów. Nazwy muszą być unikatowe dla danego zakresu. |
> | `Description` | Nie | Ciąg | Opis przypisania Odmów. |
> | `Permissions.Actions` | Co najmniej jedno działanie lub jedna akcja dataactions | Ciąg [] | Tablica ciągów, które określają operacje zarządzania, do których blok przypisania Odmów dostępu. |
> | `Permissions.NotActions` | Nie | Ciąg [] | Tablica ciągów, które określają operacje zarządzania, które mają zostać wykluczone z przypisania Odmów. |
> | `Permissions.DataActions` | Co najmniej jedno działanie lub jedna akcja dataactions | Ciąg [] | Tablica ciągów, które określają operacje na danych, do których blok przypisania Odmów dostępu. |
> | `Permissions.NotDataActions` | Nie | Ciąg [] | Tablica ciągów, które określają operacje na danych, które mają zostać wykluczone z przypisania Odmów. |
> | `Scope` | Nie | Ciąg | Ciąg określający zakres, do którego odnosi się przypisanie odmowy. |
> | `DoNotApplyToChildScopes` | Nie | Wartość logiczna | Określa, czy przypisanie odmowy dotyczy zakresów podrzędnych. Wartość domyślna to false. |
> | `Principals[i].Id` | Tak | Ciąg [] | Tablica identyfikatorów obiektów podmiotu zabezpieczeń usługi Azure AD (użytkownik, Grupa, nazwa główna usługi lub tożsamość zarządzana), do której ma zastosowanie przypisanie odmowy. Ustaw na pusty identyfikator GUID `00000000-0000-0000-0000-000000000000` reprezentujący wszystkie podmioty zabezpieczeń. |
> | `Principals[i].Type` | Nie | Ciąg [] | Tablica typów obiektów reprezentowanych przez podmioty zabezpieczeń [i]. ID. ustawiona do `SystemDefined` reprezentowania wszystkich podmiotów zabezpieczeń. |
> | `ExcludePrincipals[i].Id` | Nie | Ciąg [] | Tablica identyfikatorów obiektów podmiotu zabezpieczeń usługi Azure AD (użytkownik, Grupa, nazwa główna usługi lub tożsamość zarządzana), do której nie ma zastosowania przypisanie odmowy. |
> | `ExcludePrincipals[i].Type` | Nie | Ciąg [] | Tablica typów obiektów reprezentowana przez ExcludePrincipals [i]. ID. |
> | `IsSystemProtected` | Nie | Wartość logiczna | Określa, czy to przypisanie odmowy zostało utworzone przez platformę Azure i nie można go edytować ani usunąć. Obecnie wszystkie przydziały Odmów są chronione przez system. |

## <a name="the-all-principals-principal"></a>Główne wszystkie podmioty zabezpieczeń

Aby można było obsługiwać przypisania odmowy, wprowadzono podmiot zabezpieczeń zdefiniowany przez system o nazwie *wszystkie podmioty zabezpieczeń* . Ten podmiot zabezpieczeń reprezentuje wszystkich użytkowników, grupy, nazwy główne usługi i zarządzane tożsamości w katalogu usługi Azure AD. Jeśli identyfikator podmiotu zabezpieczeń jest zerowym identyfikatorem GUID, `00000000-0000-0000-0000-000000000000` a typ podmiotu zabezpieczeń to `SystemDefined` , podmiot zabezpieczeń reprezentuje wszystkie podmioty zabezpieczeń. W Azure PowerShell danych wyjściowych wszystkie podmioty zabezpieczeń wyglądają następująco:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Wszystkie podmioty zabezpieczeń mogą być łączone z `ExcludePrincipals` , aby odmówić wszystkim podmiotom, z wyjątkiem niektórych użytkowników. Wszystkie podmioty zabezpieczeń mają następujące ograniczenia:

- Może być używany tylko w `Principals` i nie może być używany w `ExcludePrincipals` .
- `Principals[i].Type` musi być ustawiony na `SystemDefined` .

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Ochrona nowych zasobów przy użyciu blokad zasobów usługi Azure Plans](../governance/blueprints/tutorials/protect-new-resources.md)
* [Wyświetl listę przypisań Odmów platformy Azure przy użyciu Azure Portal](deny-assignments-portal.md)
