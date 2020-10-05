---
title: Konfigurowanie zarządzanych tożsamości na maszynie wirtualnej platformy Azure przy użyciu szablonu — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu szablonu Azure Resource Manager.
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
ms.date: 09/26/2019
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18d38d3ab8cbbfb1f409b368cddc1df854baa8e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89266888"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu szablonów

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie.

W tym artykule, korzystając z szablonu wdrażania Azure Resource Manager, dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na maszynie wirtualnej platformy Azure:

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz doświadczenia w korzystaniu z szablonu wdrażania Azure Resource Manager, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#managed-identity-types)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak w przypadku Azure Portal i skryptów, szablony [Azure Resource Manager](../../azure-resource-manager/management/overview.md) umożliwiają wdrażanie nowych lub zmodyfikowanych zasobów zdefiniowanych przez grupę zasobów platformy Azure. Do edycji i wdrażania szablonu są dostępne różne opcje, w tym:

   - Przy użyciu [szablonu niestandardowego z portalu Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), który umożliwia tworzenie szablonu od podstaw lub na podstawie istniejącego szablonu wspólnego lub [szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z [oryginalnego wdrożenia](../../azure-resource-manager/templates/export-template-portal.md)lub z [bieżącego stanu wdrożenia](../../azure-resource-manager/templates/export-template-portal.md).
   - Używanie lokalnego [edytora JSON (takiego jak vs Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Użycie [projektu grupy zasobów platformy Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) dla programu Visual Studio w celu utworzenia i wdrożenia szablonu.  

Bez względu na wybraną opcję, składnia szablonu jest taka sama podczas początkowego wdrożenia i ponownego wdrożenia. Włączenie systemowej lub przypisanej przez użytkownika tożsamości zarządzanej na nowej lub istniejącej maszynie wirtualnej odbywa się w taki sam sposób. Ponadto domyślnie Azure Resource Manager wykonuje [aktualizację przyrostową](../../azure-resource-manager/templates/deployment-modes.md) wdrożeń.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji zostanie włączona i wyłączona tożsamość zarządzana przypisana przez system przy użyciu szablonu Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia maszyny wirtualnej platformy Azure lub istniejącej maszyny wirtualnej

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

2. Aby włączyć tożsamość zarządzaną przypisaną przez system, załaduj szablon do edytora, Znajdź `Microsoft.Compute/virtualMachines` zasób zainteresowania w `resources` sekcji i Dodaj `"identity"` Właściwość na tym samym poziomie, co `"type": "Microsoft.Compute/virtualMachines"` Właściwość. Użyj następującej składni:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Gdy skończysz, następujące sekcje powinny zostać dodane do `resource` sekcji szablonu i powinny wyglądać następująco:

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },

            //The following appears only if you provisioned the optional VM extension (to be deprecated)
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Przypisywanie roli do tożsamości zarządzanej przypisanej do systemu przez maszynę wirtualną

Po włączeniu zarządzanej tożsamości przypisanej przez system na maszynie wirtualnej można przyznać jej rolę, taką jak dostęp **czytelnika** do grupy zasobów, w której została utworzona.

Aby przypisać rolę do tożsamości przypisanej do systemu przez maszynę wirtualną, Twoje konto wymaga przypisania roli [administratora dostępu użytkownika](../../role-based-access-control/built-in-roles.md#user-access-administrator) .

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

2. Załaduj szablon do [edytora](#azure-resource-manager-templates) i Dodaj poniższe informacje, aby zapewnić **czytelnikowi** dostęp do grupy zasobów, w której został utworzony.  Struktura szablonów może się różnić w zależności od edytora i wybranego modelu wdrażania.

   W `parameters` sekcji Dodaj następujące elementy:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    W `variables` sekcji Dodaj następujące elementy:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    W `resources` sekcji Dodaj następujące elementy:

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z maszyny wirtualnej platformy Azure

Aby usunąć tożsamość zarządzaną przypisaną przez system z maszyny wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

2. Załaduj szablon do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachines` zasób zainteresowania w `resources` sekcji. Jeśli masz maszynę wirtualną, która ma tylko tożsamość zarządzaną przypisaną przez system, możesz ją wyłączyć, zmieniając typ tożsamości na `None` .  

   **Interfejs API Microsoft. COMPUTE/virtualMachines w wersji 2018-06-01**

   Jeśli maszyna wirtualna ma zarządzane tożsamości systemowe i przypisane przez użytkownika, Usuń `SystemAssigned` je z typu tożsamości i Zachowaj `UserAssigned` wraz z `userAssignedIdentities` wartościami słownika.

   **Interfejs API Microsoft. COMPUTE/virtualMachines w wersji 2018-06-01**

   Jeśli `apiVersion` istnieje, `2017-12-01` a maszyna wirtualna ma zarządzane tożsamości systemowe i przypisane przez użytkownika, Usuń `SystemAssigned` z typu tożsamości i Zachowaj `UserAssigned` wraz z `identityIds` tablicą zarządzanych tożsamości przypisanych przez użytkownika.  

W poniższym przykładzie przedstawiono sposób usuwania tożsamości zarządzanej przypisanej przez system z maszyny wirtualnej bez tożsamości zarządzanych przez użytkownika:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji przypiszesz tożsamość zarządzaną przez użytkownika do maszyny wirtualnej platformy Azure przy użyciu szablonu Azure Resource Manager.

> [!Note]
> Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu szablonu Azure Resource Manager, zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej platformy Azure

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) [maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. W obszarze `resources` elementu Dodaj następujący wpis, aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do maszyny wirtualnej.  Pamiętaj, aby zamienić na `<USERASSIGNEDIDENTITY>` nazwę utworzonej tożsamości zarządzanej przez użytkownika.

   **Interfejs API Microsoft. COMPUTE/virtualMachines w wersji 2018-06-01**

   Jeśli tak `apiVersion` `2018-06-01` , tożsamości zarządzane przypisane przez użytkownika są przechowywane w `userAssignedIdentities` formacie słownika, a `<USERASSIGNEDIDENTITYNAME>` wartość musi być przechowywana w zmiennej zdefiniowanej w `variables` sekcji szablonu.

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Interfejs API Microsoft. COMPUTE/virtualMachines w wersji 2017-12-01**

   Jeśli jesteś `apiVersion` `2017-12-01` użytkownikiem, zarządzane tożsamości przypisane przez użytkownika są przechowywane w `identityIds` tablicy, a `<USERASSIGNEDIDENTITYNAME>` wartość musi być przechowywana w zmiennej zdefiniowanej w `variables` sekcji szablonu.

   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. Gdy skończysz, następujące sekcje powinny zostać dodane do `resource` sekcji szablonu i powinny wyglądać następująco:

   **Interfejs API Microsoft. COMPUTE/virtualMachines w wersji 2018-06-01**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                  
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]   
   ```
   **Interfejs API Microsoft. COMPUTE/virtualMachines w wersji 2017-12-01**

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },

        //The following appears only if you provisioned the optional VM extension (to be deprecated)                   
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
       }
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej platformy Azure

Aby usunąć tożsamość przypisaną przez użytkownika z maszyny wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

2. Załaduj szablon do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachines` zasób zainteresowania w `resources` sekcji. Jeśli masz maszynę wirtualną, która ma tylko tożsamość zarządzaną przypisaną przez użytkownika, możesz ją wyłączyć, zmieniając typ tożsamości na `None` .

   W poniższym przykładzie pokazano, jak usunąć wszystkie zarządzane tożsamości przypisane przez użytkownika z maszyny wirtualnej bez tożsamości zarządzanych przypisanych do systemu:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Interfejs API Microsoft. COMPUTE/virtualMachines w wersji 2018-06-01**

   Aby usunąć skojarzoną z maszyną wirtualną tożsamość zarządzaną przez użytkownika, usuń ją ze `useraAssignedIdentities` słownika.

   Jeśli masz tożsamość zarządzaną przypisaną przez system, Zachowaj ją w `type` wartości poniżej `identity` wartości.

   **Interfejs API Microsoft. COMPUTE/virtualMachines w wersji 2017-12-01**

   Aby usunąć skojarzoną z maszyną wirtualną tożsamość zarządzaną przez użytkownika, usuń ją z `identityIds` tablicy.

   Jeśli masz tożsamość zarządzaną przypisaną przez system, Zachowaj ją w `type` wartości poniżej `identity` wartości.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie tożsamościami dla zasobów platformy Azure — omówienie](overview.md).