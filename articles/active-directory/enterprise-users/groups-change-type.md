---
title: Zmiana członkostwa grupy statycznej na dynamiczną — Azure AD | Microsoft Docs
description: Dowiedz się, jak konwertować istniejące grupy z członkostwa statycznego na dynamiczne przy użyciu Centrum administracyjnego usługi Azure AD lub poleceń cmdlet programu PowerShell.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a46cc3f4a0f2fb25fc693103a64a319dcec0324
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96860885"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Zmień statyczną składową grupy na dynamiczną Azure Active Directory

Członkostwo w grupie można zmienić z statycznej na dynamiczną (lub odwrotnie) w Azure Active Directory (Azure AD). Usługa Azure AD zachowuje tę samą nazwę grupy i identyfikator w systemie, więc wszystkie istniejące odwołania do grupy są nadal ważne. W przypadku utworzenia nowej grupy należy zaktualizować te odwołania. Dynamiczne członkostwo w grupie eliminuje obciążenie związane z zarządzaniem dodawaniem i usuwaniem użytkowników. W tym artykule opisano sposób konwertowania istniejących grup z statycznej na dynamiczne członkostwo przy użyciu Centrum administracyjnego usługi Azure AD lub poleceń cmdlet programu PowerShell.

> [!WARNING]
> W przypadku zmiany istniejącej grupy statycznej na grupę dynamiczną wszystkie istniejące elementy członkowskie są usuwane z grupy, a następnie jest przetwarzana reguła członkostwa w celu dodania nowych członków. Jeśli grupa służy do kontrolowania dostępu do aplikacji lub zasobów, należy pamiętać, że pierwotne elementy członkowskie mogą utracić dostęp do momentu całkowitego przetworzenia reguły członkostwa.
>
> Zalecamy uprzednie przetestowanie nowej reguły członkostwa, aby upewnić się, że nowe członkostwo w grupie jest zgodne z oczekiwaniami.

## <a name="change-the-membership-type-for-a-group"></a>Zmiana typu członkostwa dla grupy

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym, administratorem użytkowników lub grupą administratorów w organizacji usługi Azure AD.
2. Wybierz pozycję **grupy**.
3. Na liście **wszystkie grupy** Otwórz grupę, którą chcesz zmienić.
4. Wybierz pozycję **Właściwości**.
5. Na stronie **Właściwości** grupy wybierz **Typ członkostwa** przypisany (statyczny), użytkownika dynamicznego lub urządzenie dynamiczne, w zależności od żądanego typu członkostwa. W przypadku członkostwa dynamicznego można użyć konstruktora reguł, aby wybrać opcje prostej reguły lub samodzielnie napisać regułę członkostwa. 

Poniżej przedstawiono przykład zmiany grupy z statycznej na członkostwo dynamiczne dla grupy użytkowników.

1. Na stronie **Właściwości** wybranej grupy wybierz **Typ członkostwa** **użytkownika dynamicznego**, a następnie wybierz pozycję tak w oknie dialogowym wyjaśniającym zmiany członkostwa w grupie, aby kontynuować. 
  
   ![Wybierz typ członkostwa użytkownika dynamicznego](./media/groups-change-type/select-group-to-convert.png)
  
2. Wybierz pozycję **Dodaj zapytanie dynamiczne**, a następnie podaj regułę.
  
   ![Wprowadź regułę dla grupy dynamicznej](./media/groups-change-type/enter-rule.png)
  
3. Po utworzeniu reguły wybierz pozycję **Dodaj zapytanie** w dolnej części strony.
4. Wybierz pozycję **Zapisz** na stronie **Właściwości** dla grupy, aby zapisać zmiany. **Typ członkostwa** grupy zostanie natychmiast zaktualizowany na liście grup.

> [!TIP]
> Konwersja grup może zakończyć się niepowodzeniem, Jeśli wprowadzona reguła członkostwa była nieprawidłowa. W prawym górnym rogu portalu zostanie wyświetlone powiadomienie zawierające wyjaśnienie przyczyny niemożności zaakceptowania reguły przez system. Przeczytaj uważnie, aby zrozumieć, jak można dostosować regułę, aby była prawidłowa. Aby poznać przykłady składni reguł i pełną listę obsługiwanych właściwości, operatorów i wartości dla reguły członkostwa, zobacz [dynamiczne reguły członkostwa dla grup w Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Zmień typ członkostwa dla grupy (program PowerShell)

> [!NOTE]
> Aby zmienić właściwości grupy dynamicznej, należy użyć poleceń cmdlet z **wersji zapoznawczej** programu [Azure AD PowerShell w wersji 2](/powershell/azure/active-directory/install-adv2). Możesz zainstalować wersję zapoznawczą z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).

Oto przykład funkcji służących do przełączania zarządzania członkostwem w istniejącej grupie. W tym przykładzie należy zwrócić uwagę na prawidłowe manipulowanie właściwością GroupTypes i zachowanie wszelkich wartości, które nie są związane z członkostwem dynamicznym.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
Aby uczynić grupę statyczną:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

Aby uczynić grupę dynamiczną:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Następne kroki

Te artykuły zawierają dodatkowe informacje dotyczące grup w Azure Active Directory.

* [Wyświetlanie istniejących grup](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tworzenie nowej grupy i dodawanie członków](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Zarządzanie ustawieniami grupy](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Zarządzanie członkostwem w grupie](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Zarządzanie regułami dynamicznymi dla użytkowników w grupie](groups-dynamic-membership.md)
