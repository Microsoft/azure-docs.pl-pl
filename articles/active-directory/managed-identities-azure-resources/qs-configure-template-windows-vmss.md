---
title: Konfigurowanie szablonu do używania tożsamości zarządzanych w zestawach skalowania maszyn wirtualnych — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu Azure Resource Manager szablonu.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e948b96022972dcf702ac5a4d8be85c9afe16e7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365981"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na skalowanie maszyn wirtualnych platformy Azure przy użyciu szablonu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamości zarządzane dla zasobów platformy Azure zapewniają usługom platformy Azure automatycznie zarządzaną tożsamość w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez konieczności posiadania poświadczeń w kodzie.

Z tego artykułu dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure w zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu Azure Resource Manager wdrożenia:

- Włączanie i wyłączanie przypisanej przez system tożsamości zarządzanej w zestawie skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przeglądu](overview.md). Zapoznaj się z różnicą między tożsamością zarządzaną przypisaną przez system i **[przypisaną przez użytkownika.](overview.md#managed-identity-types)**
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje zarządzania w tym artykule, Twoje konto wymaga następujących przypisań kontroli dostępu opartej na rolach platformy Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania roli katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej—](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) tworzenie zestawu skalowania maszyn wirtualnych oraz włączanie i usuwanie tożsamości zarządzanej przypisanej przez system i/lub użytkownika z zestawu skalowania maszyn wirtualnych.
    - [Rola Współautor tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) do tworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - [Rola operatora tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) do przypisywania i usuwania tożsamości zarządzanej przypisanej przez użytkownika z i do zestawu skalowania maszyn wirtualnych.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak Azure Portal i skryptów, [](../../azure-resource-manager/management/overview.md) Azure Resource Manager szablony zapewniają możliwość wdrażania nowych lub zmodyfikowanych zasobów zdefiniowanych przez grupę zasobów platformy Azure. Dostępnych jest kilka opcji edytowania i wdrażania szablonów, zarówno lokalnych, jak i opartych na portalu, w tym:

   - Za pomocą [szablonu niestandardowego](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)z Azure Marketplace , który umożliwia tworzenie szablonu od podstaw lub opieranie go na istniejącym wspólnym szablonie [lub szablonie szybkiego startu.](https://azure.microsoft.com/documentation/templates/)
   - Wyprowadzanie z istniejącej grupy zasobów przez [](../../azure-resource-manager/templates/export-template-portal.md)wyeksportowanie szablonu z oryginalnego wdrożenia lub z bieżącego [stanu wdrożenia](../../azure-resource-manager/templates/export-template-portal.md).
   - Za pomocą lokalnego [edytora JSON (na](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)przykład VS Code) , a następnie przekazywania i wdrażania przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Utworzenie i Visual Studio szablonu za pomocą projektu grupy zasobów platformy [Azure.](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)  

Niezależnie od wybieranych opcji składnia szablonu jest taka sama podczas początkowego wdrażania i ponownego wdrażania. Włączanie tożsamości zarządzanych dla zasobów platformy Azure na nowej lub istniejącej maszynie wirtualnej odbywa się w taki sam sposób. Ponadto domyślnie program Azure Resource Manager przyrostową [aktualizację](../../azure-resource-manager/templates/deployment-modes.md) wdrożeń.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji włączysz i wyłączysz tożsamość zarządzaną przypisaną przez system przy użyciu Azure Resource Manager szablonu.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Włączanie tożsamości zarządzanej przypisanej przez system podczas tworzenia zestawu skalowania maszyn wirtualnych lub istniejącego zestawu skalowania maszyn wirtualnych

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych.
2. Aby włączyć tożsamość zarządzaną przypisaną przez system, załaduj szablon do edytora, znajdź zasób w sekcji resources i dodaj właściwość na tym samym poziomie co `Microsoft.Compute/virtualMachinesScaleSets` `identity` właściwość `"type": "Microsoft.Compute/virtualMachinesScaleSets"` . Użyj następującej składni:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

4. Gdy wszystko będzie gotowe, następujące sekcje powinny zostać dodane do sekcji zasobów szablonu i powinny wyglądać następująco:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
        
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga już tożsamości zarządzanej przypisanej przez system:

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych.

2. Załaduj szablon do [edytora](#azure-resource-manager-templates) i znajdź `Microsoft.Compute/virtualMachineScaleSets` zasób w `resources` sekcji . Jeśli masz maszynę wirtualną, która ma tylko tożsamość zarządzaną przypisaną przez system, możesz ją wyłączyć, zmieniając typ tożsamości na `None` .

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2018-06-01**

   Jeśli wartość właściwości apiVersion to, a maszyna wirtualna ma tożsamości zarządzane przypisane zarówno przez system, jak i użytkownika, usuń z typu tożsamości i zachowaj wartości `2018-06-01` `SystemAssigned` `UserAssigned` słownika userAssignedIdentities.

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2018-06-01**

   Jeśli wartość apiVersion to, a zestaw skalowania maszyn wirtualnych ma tożsamości zarządzane przypisane zarówno przez system, jak i przez użytkownika, usuń z typu tożsamości i zachowaj tablicę tożsamości zarządzanych przypisanych przez `2017-12-01` `SystemAssigned` `UserAssigned` `identityIds` użytkownika.



   W poniższym przykładzie pokazano, jak usunąć tożsamość zarządzaną przypisaną przez system z zestawu skalowania maszyn wirtualnych bez tożsamości zarządzanych przypisanych przez użytkownika:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji przypiszesz tożsamość zarządzaną przypisaną przez użytkownika do zestawu skalowania maszyn wirtualnych przy użyciu Azure Resource Manager szablonu.

> [!Note]
> Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu szablonu Azure Resource Manager, zobacz Tworzenie tożsamości zarządzanej [przypisanej przez użytkownika.](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do zestawu skalowania maszyn wirtualnych

1. W obszarze elementu dodaj następujący wpis, aby przypisać tożsamość zarządzaną przypisaną przez `resources` użytkownika do zestawu skalowania maszyn wirtualnych.  Pamiętaj, aby zastąpić nazwą utworzonej tożsamości zarządzanej `<USERASSIGNEDIDENTITY>` przypisanej przez użytkownika.

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2018-06-01**

   Jeśli wartość właściwości apiVersion to , tożsamości zarządzane przypisane przez użytkownika są przechowywane w formacie słownika, a wartość musi być przechowywana w zmiennej zdefiniowanej w `2018-06-01` `userAssignedIdentities` sekcji `<USERASSIGNEDIDENTITYNAME>` `variables` szablonu.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2017-12-01**

   Jeśli wartość to lub wcześniej, tożsamości zarządzane przypisane przez użytkownika są przechowywane w tablicy, a wartość musi być przechowywana w zmiennej zdefiniowanej w sekcji `apiVersion` `2017-12-01` zmiennych `identityIds` `<USERASSIGNEDIDENTITYNAME>` szablonu.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }

3. When you are done, your template should look similar to the following:

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                }
            }
        }
    ]
   ```

   **Interfejs API Microsoft.Compute/virtualMachines w wersji 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                }
            }
        }
    ]
   ```
### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie wymaga już tożsamości zarządzanej przypisanej przez użytkownika:

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych.

2. Załaduj szablon [do edytora](#azure-resource-manager-templates) i znajdź `Microsoft.Compute/virtualMachineScaleSets` zasób w `resources` sekcji . Jeśli masz zestaw skalowania maszyn wirtualnych, który zawiera tylko tożsamość zarządzaną przypisaną przez użytkownika, możesz ją wyłączyć, zmieniając typ tożsamości na `None` .

   W poniższym przykładzie pokazano, jak usunąć wszystkie tożsamości zarządzane przypisane przez użytkownika z maszyny wirtualnej bez tożsamości zarządzanych przypisanych przez system:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2018-06-01**

   Aby usunąć pojedynczą tożsamość zarządzaną przypisaną przez użytkownika z zestawu skalowania maszyn wirtualnych, usuń ją ze `userAssignedIdentities` słownika .

   Jeśli masz tożsamość przypisaną przez system, zachowaj ją w `type` wartości w obszarze wartości `identity` .

   **Interfejs API Microsoft.Compute/virtualMachineScaleSets w wersji 2017-12-01**

   Aby usunąć pojedynczą tożsamość zarządzaną przypisaną przez użytkownika z zestawu skalowania maszyn wirtualnych, usuń ją z `identityIds` tablicy.

   Jeśli masz tożsamość zarządzaną przypisaną przez system, zachowaj ją w `type` wartości w obszarze wartości `identity` .

## <a name="next-steps"></a>Następne kroki

- [Omówienie tożsamości zarządzanych dla zasobów platformy Azure.](overview.md)