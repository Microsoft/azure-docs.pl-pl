---
title: Wyświetl listę odrzuconych przypisań zasobów platformy Azure za pomocą Azure PowerShell
description: Dowiedz się, jak wyświetlić listę użytkowników, grup, nazw podmiotów usługi i zarządzanych tożsamości, dla których odmówiono dostępu do określonych akcji zasobów platformy Azure w określonych zakresach przy użyciu Azure PowerShell.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77137391"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>Wyświetl listę odrzuconych przypisań zasobów platformy Azure przy użyciu Azure PowerShell

[Odmowa przypisania](deny-assignments.md) uniemożliwia użytkownikom wykonywanie określonych akcji zasobów platformy Azure nawet wtedy, gdy przypisanie roli przyznaje im dostęp. W tym artykule opisano sposób wyświetlania listy zablokowanych przypisań przy użyciu Azure PowerShell.

> [!NOTE]
> Nie można bezpośrednio tworzyć własnych przypisań Odmów. Aby uzyskać informacje na temat sposobu tworzenia przypisań Odmów, zobacz [odmowa przypisań](deny-assignments.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby uzyskać informacje o przypisaniu odmowy, należy dysponować:

- `Microsoft.Authorization/denyAssignments/read`uprawnienie, które jest zawarte w większości [wbudowanych ról dla zasobów platformy Azure](built-in-roles.md)
- Program [PowerShell w Azure Cloud Shell](/azure/cloud-shell/overview) lub [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Lista przypisań odmowy

### <a name="list-all-deny-assignments"></a>Wyświetl listę wszystkich przypisań Odmów

Aby wyświetlić listę wszystkich przypisań Odmów dla bieżącej subskrypcji, użyj polecenie [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Wyświetl listę odrzuconych przydziałów w grupie zasobów

Aby wyświetlić listę wszystkich przypisań Odmów w zakresie grupy zasobów, użyj polecenie [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Wyświetl listę odrzuconych przypisań w zakresie subskrypcji

Aby wyświetlić listę wszystkich przypisań Odmów w zakresie subskrypcji, użyj polecenie [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Aby uzyskać identyfikator subskrypcji, możesz go znaleźć w bloku **subskrypcje** w Azure Portal lub użyć polecenie [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Następne kroki

- [Informacje o odmowie przypisań zasobów platformy Azure](deny-assignments.md)
- [Wyświetl listę odrzuconych przypisań zasobów platformy Azure przy użyciu Azure Portal](deny-assignments-portal.md)
- [Wyświetlanie listy Odmów przypisań zasobów platformy Azure przy użyciu interfejsu API REST](deny-assignments-rest.md)