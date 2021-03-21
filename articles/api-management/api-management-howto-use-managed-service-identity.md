---
title: Korzystanie z tożsamości zarządzanych na platformie Azure API Management | Microsoft Docs
description: Informacje o sposobie tworzenia tożsamości przypisanych do systemu i przypisanych do użytkownika w API Management przy użyciu szablonu Azure Portal, programu PowerShell i Menedżer zasobów.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 98237efae89e7d88dd23cb7e8fc9f7e9f05bca70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521547"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Korzystanie z tożsamości zarządzanych w usłudze Azure API Management

W tym artykule pokazano, jak utworzyć zarządzaną tożsamość wystąpienia usługi Azure API Management i jak uzyskać dostęp do innych zasobów. Zarządzana tożsamość wygenerowana przez usługę Azure Active Directory (Azure AD) umożliwia API Managementemu wystąpieniu i bezpieczny dostęp do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault. Platforma Azure zarządza tą tożsamością, więc nie trzeba dostarczać ani obracać żadnych wpisów tajnych. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Do wystąpienia API Management można przyznać dwa typy tożsamości:

- *Tożsamość przypisana do systemu* jest powiązana z usługą i jest usuwana, jeśli usługa zostanie usunięta. Usługa może mieć tylko jedną tożsamość przypisaną do systemu.
- *Tożsamość przypisana przez użytkownika* to autonomiczny zasób platformy Azure, który można przypisać do usługi. Usługa może mieć wiele tożsamości przypisanych do użytkownika.

## <a name="create-a-system-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez system

### <a name="azure-portal"></a>Azure Portal

Aby skonfigurować tożsamość zarządzaną w Azure Portal, należy najpierw utworzyć wystąpienie API Management, a następnie włączyć funkcję.

1. Utwórz wystąpienie API Management w portalu, jak zwykle. Przejdź do niego w portalu.
2. Wybierz pozycję **zarządzane tożsamości**.
3. Na karcie **przypisana przez system** Przełącz pozycję **stan** na wartość **włączone**. Wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Wybory umożliwiające włączenie tożsamości zarządzanej przypisanej do systemu" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższe kroki przeprowadzą Cię przez proces tworzenia wystąpienia API Management i przypisywania go do tożsamości przy użyciu Azure PowerShell. 

1. W razie konieczności Zainstaluj Azure PowerShell, korzystając z instrukcji w [przewodniku Azure PowerShell](/powershell/azure/install-az-ps). Następnie uruchom polecenie `Connect-AzAccount` , aby utworzyć połączenie z platformą Azure.

2. Użyj poniższego kodu, aby utworzyć wystąpienie. Aby uzyskać więcej przykładów użycia Azure PowerShell z wystąpieniem API Management, zobacz [API Management przykładów programu PowerShell](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Zaktualizuj istniejące wystąpienie, aby utworzyć tożsamość:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Można utworzyć wystąpienie API Management z tożsamością, dołączając następującą właściwość w definicji zasobu:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ta właściwość informuje platformę Azure, aby utworzyć tożsamość dla wystąpienia API Management i zarządzać nią.

Na przykład kompletny szablon Azure Resource Manager może wyglądać następująco:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

Po utworzeniu wystąpienia ma ono następujące dodatkowe właściwości:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`tenantId`Właściwość identyfikuje dzierżawę usługi Azure AD, do której należy tożsamość. `principalId`Właściwość jest unikatowym identyfikatorem nowej tożsamości wystąpienia. W ramach usługi Azure AD nazwa główna usługi ma taką samą nazwę, która została nadana API Management wystąpieniu.

> [!NOTE]
> Wystąpienie API Management może mieć zarówno tożsamości przypisane do systemu, jak i przypisane przez użytkownika. W tym przypadku właściwość będzie `type` `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Obsługiwane scenariusze przy użyciu tożsamości przypisanej do systemu

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Uzyskaj niestandardowy certyfikat TLS/SSL dla wystąpienia API Management z Azure Key Vault
Można użyć tożsamości przypisanej do systemu API Management wystąpienia, aby pobrać niestandardowe certyfikaty TLS/SSL przechowywane w Azure Key Vault. Następnie można przypisać te certyfikaty do domen niestandardowych w wystąpieniu API Management. Należy pamiętać o następujących kwestiach:

- Typem zawartości wpisu tajnego musi być *Application/x-PKCS12*.
- Użyj punktu końcowego tajnego certyfikatu Key Vault, który zawiera wpis tajny.

> [!Important]
> Jeśli nie podano wersji tego obiektu certyfikatu, program API Management automatycznie uzyska nowszą wersję certyfikatu w ciągu czterech godzin od jego zaktualizowania w Key Vault.

Poniższy przykład przedstawia szablon Azure Resource Manager, który zawiera następujące kroki:

1. Utwórz wystąpienie API Management z tożsamością zarządzaną.
2. Zaktualizuj zasady dostępu wystąpienia Azure Key Vault i zezwól na wystąpienie API Management, aby uzyskać z niego wpisy tajne.
3. Zaktualizuj wystąpienie API Management, ustawiając niestandardową nazwę domeny za pośrednictwem certyfikatu z wystąpienia Key Vault.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Uwierzytelnianie na zapleczu przy użyciu tożsamości API Management

Można użyć tożsamości przypisanej do systemu do uwierzytelniania na zapleczu za pomocą zasad [uwierzytelniania zarządzanych tożsamości](api-management-authentication-policies.md#ManagedIdentity) .

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Nawiązywanie połączenia z zasobami platformy Azure za zaporą IP przy użyciu tożsamości zarządzanej przypisanej przez system


API Management to zaufana usługa firmy Microsoft do następujących zasobów. Umożliwia to usłudze łączenie się z następującymi zasobami za zaporą. Po jawnie przypisaniu odpowiedniej roli platformy Azure do [zarządzanej tożsamości przypisanej do systemu](../active-directory/managed-identities-azure-resources/overview.md) dla tego wystąpienia zasobu zakres dostępu dla tego wystąpienia odpowiada roli platformy Azure przypisanej do zarządzanej tożsamości.


|Usługa platformy Azure | Link|
|---|---|
|Azure Storage | [Zaufane — dostęp do platformy Azure — magazyn](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Usługa Azure Service Bus | [Zaufane — dostęp do platformy Azure — Service-Bus](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Centrum zdarzeń Azure | [Trused — dostęp do platformy Azure — centrum zdarzeń](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

> [!NOTE]
> Można skojarzyć wystąpienie API Management z maksymalnie 10 tożsamościami zarządzanymi przez użytkownika.

### <a name="azure-portal"></a>Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, należy najpierw utworzyć wystąpienie API Management, a następnie włączyć funkcję.

1. Utwórz wystąpienie API Management w portalu, jak zwykle. Przejdź do niego w portalu.
2. Wybierz pozycję **zarządzane tożsamości**.
3. Na karcie **przypisane przez użytkownika** wybierz pozycję **Dodaj**.
4. Wyszukaj utworzoną wcześniej tożsamość i wybierz ją. Wybierz pozycję **Dodaj**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Wybory dotyczące włączania tożsamości zarządzanej przypisanej przez użytkownika" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższe kroki przeprowadzą Cię przez proces tworzenia wystąpienia API Management i przypisywania go do tożsamości przy użyciu Azure PowerShell. 

1. W razie konieczności Zainstaluj Azure PowerShell, korzystając z instrukcji w [przewodniku Azure PowerShell](/powershell/azure/install-az-ps). Następnie uruchom polecenie `Connect-AzAccount` , aby utworzyć połączenie z platformą Azure.

2. Użyj poniższego kodu, aby utworzyć wystąpienie. Aby uzyskać więcej przykładów użycia Azure PowerShell z wystąpieniem API Management, zobacz [API Management przykładów programu PowerShell](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Zaktualizuj istniejącą usługę, aby przypisać tożsamość do usługi:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Szablon usługi Azure Resource Manager

Można utworzyć wystąpienie API Management z tożsamością, dołączając następującą właściwość w definicji zasobu:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Dodanie typu przypisanego przez użytkownika informuje platformę Azure, aby używała tożsamości przypisanej do użytkownika określonego dla danego wystąpienia.

Na przykład kompletny szablon Azure Resource Manager może wyglądać następująco:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
         "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

Gdy usługa zostanie utworzona, ma następujące dodatkowe właściwości:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

`principalId`Właściwość jest unikatowym identyfikatorem tożsamości, która jest używana do administrowania usługą Azure AD. `clientId`Właściwość jest unikatowym identyfikatorem nowej tożsamości aplikacji używanej do określania tożsamości, która ma być używana podczas wywołań środowiska uruchomieniowego.

> [!NOTE]
> Wystąpienie API Management może mieć zarówno tożsamości przypisane do systemu, jak i przypisane przez użytkownika. W tym przypadku właściwość będzie `type` `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Obsługiwane scenariusze przy użyciu tożsamości zarządzanej przypisanej przez użytkownika

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Uzyskaj niestandardowy certyfikat TLS/SSL dla wystąpienia API Management z Azure Key Vault
Możesz użyć dowolnej tożsamości przypisanej do użytkownika, aby ustanowić relację zaufania między wystąpieniem API Management i magazynem kluczy. Tego zaufania można następnie użyć do pobrania niestandardowych certyfikatów protokołu TLS/SSL przechowywanych w Azure Key Vault. Następnie można przypisać te certyfikaty do domen niestandardowych w wystąpieniu API Management. 

Należy pamiętać o następujących kwestiach:

- Typem zawartości wpisu tajnego musi być *Application/x-PKCS12*.
- Użyj punktu końcowego tajnego certyfikatu Key Vault, który zawiera wpis tajny.

> [!Important]
> Jeśli nie podano wersji tego obiektu certyfikatu, program API Management automatycznie uzyska nowszą wersję certyfikatu w ciągu czterech godzin od jego zaktualizowania w Key Vault.

Aby uzyskać pełny szablon, zobacz [API Management z użyciem protokołu SSL opartego na magazynie kluczy przy użyciu tożsamości przypisanej do użytkownika](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json).

W tym szablonie zostaną wdrożone następujące narzędzia:

* Usługa Azure API Management
* Tożsamość przypisana przez użytkownika zarządzanego przez platformę Azure
* Magazyn kluczy platformy Azure do przechowywania certyfikatu SSL/TLS

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Uwierzytelnianie na zapleczu przy użyciu tożsamości przypisanej do użytkownika

Możesz użyć tożsamości przypisanej do użytkownika do uwierzytelniania na zapleczu, korzystając z zasad [uwierzytelniania zarządzanych tożsamości](api-management-authentication-policies.md#ManagedIdentity) .

## <a name="remove-an-identity"></a><a name="remove"></a>Usuwanie tożsamości

Tożsamość przypisaną przez system można usunąć, wyłączając tę funkcję za pomocą portalu lub szablonu Azure Resource Manager w taki sam sposób, w jaki został utworzony. Tożsamości przypisane do użytkownika można usuwać pojedynczo. Aby usunąć wszystkie tożsamości, ustaw typ tożsamości na `"None"` .

Usunięcie tożsamości przypisanej do systemu w ten sposób spowoduje również usunięcie jej z usługi Azure AD. Tożsamości przypisane do systemu są również automatycznie usuwane z usługi Azure AD po usunięciu wystąpienia API Management.

Aby usunąć wszystkie tożsamości przy użyciu szablonu Azure Resource Manager, zaktualizuj tę sekcję:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Jeśli wystąpienie API Management jest skonfigurowane przy użyciu niestandardowego certyfikatu protokołu SSL z Key Vault i podjęto próbę wyłączenia tożsamości zarządzanej, żądanie zakończy się niepowodzeniem.
>
> Możesz odblokować siebie, przełączając certyfikat Azure Key Vault na wbudowany certyfikat zakodowany, a następnie wyłączając zarządzaną tożsamość. Aby uzyskać więcej informacji, zobacz [Konfigurowanie nazwy domeny niestandardowej](configure-custom-domain.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zarządzanych tożsamości dla zasobów platformy Azure:

* [Jakie są zarządzane tożsamości dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)
* [Szablony usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)
* [Uwierzytelnianie przy użyciu tożsamości zarządzanej w zasadach](./api-management-authentication-policies.md#ManagedIdentity)
