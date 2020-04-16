---
title: Wdrażanie aplikacji z tożsamością zarządzaną przypisaną przez użytkownika
description: W tym artykule pokazano, jak wdrożyć aplikację sieci szkieletowej usług z tożsamością zarządzaną przypisaną przez użytkownika
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 9aef81db7a455b72c83cf96898a0c228f1c382fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415641"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Wdrażanie aplikacji sieci szkieletowej usług z przypisaną przez użytkownika tożsamością zarządzaną

Aby wdrożyć aplikację sieci szkieletowej usług z tożsamością zarządzaną, należy ją wdrożyć za pośrednictwem usługi Azure Resource Manager, zazwyczaj z szablonem usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat wdrażania aplikacji sieci szkieletowej usług za pośrednictwem usługi Azure Resource Manager, zobacz [Zarządzanie aplikacjami i usługami jako zasobami usługi Azure Resource Manager](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Aplikacje, które nie są wdrażane jako zasób platformy Azure **nie mogą** mieć tożsamości zarządzanych. 
>
> Wdrażanie aplikacji sieci szkieletowej usług z `"2019-06-01-preview"`tożsamością zarządzaną jest obsługiwane w wersji interfejsu API . Można również użyć tej samej wersji interfejsu API dla typu aplikacji, wersji typu aplikacji i zasobów usługi.
>

## <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Aby włączyć aplikację z tożsamością przypisaną przez użytkownika, należy najpierw dodać właściwość **tożsamości** do zasobu aplikacji z **przypisanym przez użytkownika typem** i tożsamościami przypisanymi przez użytkownika, do których istnieje odwołanie. Następnie dodaj **managedIdentities** sekcji wewnątrz sekcji **właściwości** dla zasobu **aplikacji,** który zawiera listę przyjaznej nazwy do principalId mapowania dla każdej tożsamości przypisane przez użytkownika. Aby uzyskać więcej informacji o tożsamościach przypisanych przez użytkownika, zobacz [Tworzenie, lista lub usuwanie tożsamości zarządzanej przypisanej przez użytkownika](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell).

### <a name="application-template"></a>Szablon aplikacji

Aby włączyć aplikację z tożsamością przypisaną przez użytkownika, najpierw dodaj właściwość **tożsamości** do zasobu aplikacji z **typem userAssigned** i przypisanymi przez niego tożsamościami, a następnie dodaj obiekt **managedIdentities** wewnątrz sekcji **właściwości** zawierającej listę przyjaznej nazwy do mapowania principalId dla każdej tożsamości przypisanej przez użytkownika.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

W powyższym przykładzie nazwa zasobu przypisanej tożsamości użytkownika jest używana jako przyjazna nazwa tożsamości zarządzanej dla aplikacji. W poniższych przykładach przyjęto założenie, że rzeczywista przyjazna nazwa to "AdminUser".

### <a name="application-package"></a>Pakiet aplikacji

1. Dla każdej tożsamości `managedIdentities` zdefiniowanej w sekcji w szablonie usługi Azure Resource Manager dodaj `<ManagedIdentity>` znacznik w manifeście aplikacji w sekcji **Podmioty.** Atrybut `Name` musi być zgodny `name` z właściwością zdefiniowaną w `managedIdentities` sekcji.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. W **ServiceManifestImport** sekcji dodaj **IdentityBindingPolicy** dla usługi, która używa tożsamości zarządzanej. Ta zasada `AdminUser` mapuje tożsamość do nazwy tożsamości specyficznej dla usługi, która musi zostać dodana do manifestu usługi później.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Zaktualizuj manifest usługi, aby dodać **ManagedIdentity** `ServiceIdentityRef` wewnątrz `IdentityBindingPolicy` sekcji **Zasoby** o nazwie pasującej w manifeście aplikacji:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Następne kroki

* [Jak używać tożsamości zarządzanej w kodzie aplikacji sieci szkieletowej usług](how-to-managed-identity-service-fabric-app-code.md)
* [Jak udzielić dostępu aplikacji sieci szkieletowej usług do innych zasobów platformy Azure](how-to-grant-access-other-resources.md)
