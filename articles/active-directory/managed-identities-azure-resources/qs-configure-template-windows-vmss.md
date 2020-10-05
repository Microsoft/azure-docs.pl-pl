---
title: Konfigurowanie szablonu do korzystania z tożsamości zarządzanych w zestawach skalowania maszyn wirtualnych — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu szablonu Azure Resource Manager.
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
ms.date: 02/20/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30185733a89ecf078bd2077cdcb51d6817f889d2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89266752"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na skalę maszyn wirtualnych platformy Azure przy użyciu szablonu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie.

W tym artykule dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na zestawie skalowania maszyn wirtualnych platformy Azure przy użyciu szablonu wdrażania Azure Resource Manager:
- Włączanie i wyłączanie zarządzanej tożsamości przypisanej przez system w zestawie skalowania maszyn wirtualnych platformy Azure
- Dodawanie i usuwanie tożsamości zarządzanej przypisanej przez użytkownika w zestawie skalowania maszyn wirtualnych platformy Azure

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#managed-identity-types)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby móc wykonywać operacje zarządzania w tym artykule, Twoje konto musi mieć następujące przypisania kontroli dostępu oparte na rolach na platformie Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) w celu utworzenia zestawu skalowania maszyn wirtualnych oraz włączenia i usunięcia tożsamości zarządzanej system i/lub przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych.
    - Rola [współautor tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) do tworzenia tożsamości zarządzanej przypisanej przez użytkownika.
    - Rola [operatora tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) do przypisywania i usuwania tożsamości zarządzanej przypisanej przez użytkownika z i do zestawu skalowania maszyn wirtualnych.

## <a name="azure-resource-manager-templates"></a>Szablony usługi Azure Resource Manager

Podobnie jak w przypadku Azure Portal i skryptów, szablony [Azure Resource Manager](../../azure-resource-manager/management/overview.md) umożliwiają wdrażanie nowych lub zmodyfikowanych zasobów zdefiniowanych przez grupę zasobów platformy Azure. Do edycji i wdrażania szablonu są dostępne różne opcje, w tym:

   - Przy użyciu [szablonu niestandardowego z portalu Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), który umożliwia tworzenie szablonu od podstaw lub na podstawie istniejącego szablonu wspólnego lub [szybkiego startu](https://azure.microsoft.com/documentation/templates/).
   - Wyprowadzanie z istniejącej grupy zasobów przez wyeksportowanie szablonu z [oryginalnego wdrożenia](../../azure-resource-manager/templates/export-template-portal.md)lub z [bieżącego stanu wdrożenia](../../azure-resource-manager/templates/export-template-portal.md).
   - Używanie lokalnego [edytora JSON (takiego jak vs Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), a następnie przekazywanie i wdrażanie przy użyciu programu PowerShell lub interfejsu wiersza polecenia.
   - Użycie [projektu grupy zasobów platformy Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) dla programu Visual Studio w celu utworzenia i wdrożenia szablonu.  

Bez względu na wybraną opcję, składnia szablonu jest taka sama podczas początkowego wdrożenia i ponownego wdrożenia. Włączenie zarządzanych tożsamości dla zasobów platformy Azure na nowej lub istniejącej maszynie wirtualnej odbywa się w taki sam sposób. Ponadto domyślnie Azure Resource Manager wykonuje [aktualizację przyrostową](../../azure-resource-manager/templates/deployment-modes.md) wdrożeń.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji zostanie włączona i wyłączona tożsamość zarządzana przypisana przez system przy użyciu szablonu Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia zestawu skalowania maszyn wirtualnych lub istniejącego zestawu skalowania maszyn wirtualnych

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych.
2. Aby włączyć tożsamość zarządzaną przypisaną przez system, załaduj szablon do edytora, Znajdź `Microsoft.Compute/virtualMachinesScaleSets` interesujący zasób w sekcji Resources i Dodaj `identity` Właściwość na tym samym poziomie, co `"type": "Microsoft.Compute/virtualMachinesScaleSets"` Właściwość. Użyj następującej składni:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Opcjonalnie można zainicjować obsługę tożsamości zarządzanych dla rozszerzenia zestawu skalowania maszyn wirtualnych zasobów platformy Azure, określając je w `extensionProfile` elemencie szablonu. Ten krok jest opcjonalny, ponieważ można użyć punktu końcowego tożsamości usługi Azure Instance Metadata Service (IMDS), aby uzyskać również tokeny.  Aby uzyskać więcej informacji, zobacz [Migrowanie z rozszerzenia maszyny wirtualnej do usługi Azure IMDS na potrzeby uwierzytelniania](howto-migrate-vm-extension.md).


4. Gdy skończysz, następujące sekcje powinny zostać dodane do sekcji zasobów szablonu i powinny wyglądać następująco:

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Wyłączanie tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie potrzebuje już tożsamości zarządzanej przypisanej do systemu:

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych.

2. Załaduj szablon do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachineScaleSets` zasób zainteresowania w `resources` sekcji. Jeśli masz maszynę wirtualną, która ma tylko tożsamość zarządzaną przypisaną przez system, możesz ją wyłączyć, zmieniając typ tożsamości na `None` .

   **Interfejs API Microsoft. COMPUTE/virtualMachineScaleSets w wersji 2018-06-01**

   Jeśli apiVersion jest, `2018-06-01` a maszyna wirtualna ma zarządzane tożsamości systemowe i przypisane przez użytkownika, Usuń `SystemAssigned` z typu tożsamości i Zachowaj `UserAssigned` wartości słowników resourceidentity.

   **Interfejs API Microsoft. COMPUTE/virtualMachineScaleSets w wersji 2018-06-01**

   Jeśli apiVersion to, `2017-12-01` a zestaw skalowania maszyn wirtualnych ma zarządzane tożsamości systemowe i przypisane przez użytkownika, Usuń `SystemAssigned` z typu tożsamości i Zachowaj `UserAssigned` wraz z `identityIds` tablicą zarządzanych tożsamości przypisanych przez użytkownika.



   Poniższy przykład pokazuje, jak usunąć tożsamość zarządzaną przypisaną przez system z zestawu skalowania maszyn wirtualnych bez tożsamości zarządzanej przypisanej przez użytkownika:

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

Ta sekcja zawiera przypisanie zarządzanej tożsamości przypisanej przez użytkownika do zestawu skalowania maszyn wirtualnych przy użyciu szablonu Azure Resource Manager.

> [!Note]
> Aby utworzyć tożsamość zarządzaną przypisaną przez użytkownika przy użyciu szablonu Azure Resource Manager, zobacz [Tworzenie tożsamości zarządzanej przypisanej przez użytkownika](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do zestawu skalowania maszyn wirtualnych

1. W obszarze `resources` elementu Dodaj następujący wpis, aby przypisać tożsamość zarządzaną przypisaną przez użytkownika do zestawu skalowania maszyn wirtualnych.  Pamiętaj, aby zamienić na `<USERASSIGNEDIDENTITY>` nazwę utworzonej tożsamości zarządzanej przez użytkownika.

   **Interfejs API Microsoft. COMPUTE/virtualMachineScaleSets w wersji 2018-06-01**

   Jeśli apiVersion jest `2018-06-01` , zarządzane tożsamości przypisane przez użytkownika są przechowywane w `userAssignedIdentities` formacie słownika, a `<USERASSIGNEDIDENTITYNAME>` wartość musi być przechowywana w zmiennej zdefiniowanej w `variables` sekcji szablonu.

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

   **Interfejs API Microsoft. COMPUTE/virtualMachineScaleSets w wersji 2017-12-01**

   Jeśli masz `apiVersion` `2017-12-01` lub wcześniej, zarządzane tożsamości przypisane przez użytkownika są przechowywane w `identityIds` tablicy, a `<USERASSIGNEDIDENTITYNAME>` wartość musi być przechowywana w zmiennej zdefiniowanej w sekcji zmiennych szablonu.

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
   ```
> [!NOTE]
> Opcjonalnie można zainicjować obsługę tożsamości zarządzanych dla rozszerzenia zestawu skalowania maszyn wirtualnych zasobów platformy Azure, określając je w `extensionProfile` elemencie szablonu. Ten krok jest opcjonalny, ponieważ można użyć punktu końcowego tożsamości usługi Azure Instance Metadata Service (IMDS), aby uzyskać również tokeny.  Aby uzyskać więcej informacji, zobacz [Migrowanie z rozszerzenia maszyny wirtualnej do usługi Azure IMDS na potrzeby uwierzytelniania](howto-migrate-vm-extension.md).

3. Gdy skończysz, szablon powinien wyglądać podobnie do poniższego:

   **Interfejs API Microsoft. COMPUTE/virtualMachineScaleSets w wersji 2018-06-01**   

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

   **Interfejs API Microsoft. COMPUTE/virtualMachines w wersji 2017-12-01**

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych platformy Azure

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie potrzebuje już tożsamości zarządzanej przypisanej przez użytkownika:

1. Niezależnie od tego, czy logujesz się do platformy Azure lokalnie, czy za pośrednictwem Azure Portal, użyj konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych.

2. Załaduj szablon do [edytora](#azure-resource-manager-templates) i Znajdź `Microsoft.Compute/virtualMachineScaleSets` zasób zainteresowania w `resources` sekcji. Jeśli masz zestaw skalowania maszyn wirtualnych, który ma tylko tożsamość zarządzaną przez użytkownika, możesz ją wyłączyć, zmieniając typ tożsamości na `None` .

   W poniższym przykładzie pokazano, jak usunąć wszystkie zarządzane tożsamości przypisane przez użytkownika z maszyny wirtualnej bez tożsamości zarządzanych przez system:

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

   **Interfejs API Microsoft. COMPUTE/virtualMachineScaleSets w wersji 2018-06-01**

   Aby usunąć tożsamość zarządzaną pojedynczą przez użytkownika z zestawu skalowania maszyn wirtualnych, usuń ją ze `userAssignedIdentities` słownika.

   Jeśli masz tożsamość przypisaną do systemu, Zachowaj ją w `type` wartości poniżej `identity` wartości.

   **Interfejs API Microsoft. COMPUTE/virtualMachineScaleSets w wersji 2017-12-01**

   Aby usunąć tożsamość zarządzaną pojedynczą przez użytkownika z zestawu skalowania maszyn wirtualnych, usuń ją z `identityIds` tablicy.

   Jeśli masz tożsamość zarządzaną przypisaną przez system, Zachowaj ją w `type` wartości poniżej `identity` wartości.

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie tożsamościami dla zasobów platformy Azure — omówienie](overview.md).