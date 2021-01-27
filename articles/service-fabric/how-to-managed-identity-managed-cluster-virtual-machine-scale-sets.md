---
title: Dodawanie tożsamości zarządzanej do Service Fabric typu węzła klastra zarządzanego (wersja zapoznawcza)
description: W tym artykule pokazano, jak dodać tożsamość zarządzaną do Service Fabric typu węzła klastra zarządzanego
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 9edcf75451f43f2a00cd01d5ca7f385704b1ea7f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878430"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>Dodawanie tożsamości zarządzanej do Service Fabric typu węzła klastra zarządzanego (wersja zapoznawcza)

Każdy typ węzła w klastrze zarządzanym Service Fabric jest obsługiwany przez zestaw skalowania maszyn wirtualnych. Aby umożliwić używanie tożsamości zarządzanych z typem węzła zarządzanego klastra, właściwość `vmManagedIdentity` została dodana do definicji typu węzła zawierającego listę tożsamości, które mogą być używane `userAssignedIdentities` . Funkcja odzwierciedla, jak zarządzane tożsamości mogą być używane w niezarządzanych klastrach, na przykład przy użyciu tożsamości zarządzanej z [rozszerzeniem zestawu skalowania maszyn wirtualnych Azure Key Vault](../virtual-machines/extensions/key-vault-windows.md).


Aby zapoznać się z przykładem wdrożenia klastra zarządzanego Service Fabric, które korzysta z tożsamości zarządzanej w typie węzła, zobacz [ten szablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI). Aby uzyskać listę obsługiwanych regionów, zapoznaj się z [często zadawanymi pytaniami dotyczącymi zarządzanych klastrów](./faq-managed-cluster.md#what-regions-are-supported-in-the-preview).

> [!NOTE]
> Tylko tożsamości przypisane przez użytkownika są obecnie obsługiwane dla tej funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
* Jeśli planujesz korzystanie z programu PowerShell, [Zainstaluj](/cli/azure/install-azure-cli) interfejs wiersza polecenia platformy Azure w celu uruchomienia poleceń referencyjnych interfejsu CLI.

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika 

Tożsamość zarządzana przypisana przez użytkownika można zdefiniować w sekcji Resources szablonu Azure Resource Manager (ARM) na potrzeby tworzenia po wdrożeniu:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

lub utworzone za pomocą programu PowerShell:

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Dodawanie przypisania roli przy użyciu dostawcy zasobów Service Fabric

Dodaj przypisanie roli do zarządzanej tożsamości za pomocą aplikacji dostawcy zasobów Service Fabric. To przypisanie umożliwia dostawcy zasobów Service Fabric przypisanie tożsamości do zestawu skalowania maszyn wirtualnych klastra zarządzanego. 

Należy użyć następujących wartości, jeśli ma to zastosowanie:

|Nazwa|Odpowiadająca wartość dostawcy zasobów Service Fabric|
|----|-------------------------------------|
|Identyfikator aplikacji|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|Identyfikator obiektu|fbc587f2-66f5-4459-a027-bcd908b9d278|


|Nazwa definicji roli|Identyfikator definicji roli|
|----|-------------------------------------|
|Operator tożsamości zarządzanej|f1a07417-d97a-45cb-824c-7a7467783830
|



To przypisanie roli można zdefiniować w sekcji Resources przy użyciu identyfikatora obiektu i identyfikatora definicji roli:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

lub utworzone za pomocą programu PowerShell przy użyciu identyfikatora aplikacji i identyfikatora definicji roli:

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

lub identyfikator obiektu i identyfikator definicji roli:

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>Dodaj zarządzane właściwości tożsamości do definicji typu węzła

Na koniec Dodaj `vmManagedIdentity` właściwości i `userAssignedIdentities` do definicji typu węzła zarządzanego klastra:

```json

"properties": {
    "isPrimary" : true,
    "vmInstanceCount": 5,
    "dataDiskSizeGB": 100,
    "vmSize": "Standard_D2",
    "vmImagePublisher" : "MicrosoftWindowsServer",
    "vmImageOffer" : "WindowsServer",
    "vmImageSku" : "2019-Datacenter",
    "vmImageVersion" : "latest",
    "vmManagedIdentity": {
        "userAssignedIdentities": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
        ]
    }
}
```

Po wdrożeniu utworzona tożsamość zarządzana została dodana do zestawu skalowania maszyn wirtualnych z wyznaczonym typem węzła i może być używana zgodnie z oczekiwaniami, podobnie jak w przypadku dowolnego niezarządzanego klastra.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Nie można poprawnie dodać przypisania roli, zostanie spełniony następujący błąd we wdrożeniu:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Wystąpił błąd wdrażania Azure Portal, w którym klient z IDENTYFIKATORem obiektu/aplikacji SFRP nie ma uprawnień do wykonywania działań związanych z zarządzaniem tożsamościami":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdrażanie aplikacji w klastrze zarządzanym Service Fabric](./tutorial-managed-cluster-deploy-app.md)