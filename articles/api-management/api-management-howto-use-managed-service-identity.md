---
title: Korzystanie z tożsamości zarządzanych w usłudze Azure API Management | Microsoft Docs
description: Dowiedz się, jak tworzyć tożsamości przypisane przez system i przypisane przez użytkownika w programie API Management przy użyciu Azure Portal, programu PowerShell i Resource Manager szablonu.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40ee196f53af040e4099fb344de5488109ce001b
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812250"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Korzystanie z tożsamości zarządzanych w usłudze Azure API Management

W tym artykule pokazano, jak utworzyć tożsamość zarządzaną dla wystąpienia usługi Azure API Management oraz jak uzyskać dostęp do innych zasobów. Tożsamość zarządzana wygenerowana przez usługę Azure Active Directory (Azure AD) umożliwia wystąpieniu usługi API Management łatwy i bezpieczny dostęp do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault. Platforma Azure zarządza tą tożsamością, dzięki czemu nie trzeba aprowizować ani obracać żadnych wpisów tajnych. Aby uzyskać więcej informacji na temat tożsamości zarządzanych, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md).

Możesz przyznać dwa typy tożsamości do API Management wystąpienia:

- Tożsamość *przypisana przez system jest* powiązana z usługą i usuwana w przypadku usunięcia usługi. Usługa może mieć tylko jedną tożsamość przypisaną przez system.
- Tożsamość *przypisana przez użytkownika to* autonomiczny zasób platformy Azure, który można przypisać do usługi. Usługa może mieć wiele tożsamości przypisanych przez użytkownika.

## <a name="create-a-system-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez system

### <a name="azure-portal"></a>Azure Portal

Aby skonfigurować tożsamość zarządzaną w Azure Portal, najpierw utworzysz wystąpienie API Management, a następnie włączysz tę funkcję.

1. Utwórz API Management w portalu w zwykły sposób. Przejdź do niego w portalu.
2. Wybierz **pozycję Tożsamości zarządzane.**
3. Na karcie **Przypisano system** przełącz pozycję **Stan** na **Wł.**. Wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="Opcje włączania tożsamości zarządzanej przypisanej przez system" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W poniższych krokach przedstawiono sposób tworzenia API Management i przypisywania do niego tożsamości przy użyciu Azure PowerShell. 

1. W razie potrzeby zainstaluj Azure PowerShell, korzystając z instrukcji w Azure PowerShell [przewodniku.](/powershell/azure/install-az-ps) Następnie uruchom , `Connect-AzAccount` aby utworzyć połączenie z platformą Azure.

2. Użyj następującego kodu, aby utworzyć wystąpienie. Aby uzyskać więcej przykładów użycia programu Azure PowerShell z wystąpieniem API Management, zobacz [API Management przykłady programu PowerShell.](powershell-samples.md)

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

Możesz utworzyć wystąpienie API Management tożsamości, uwzględniając następującą właściwość w definicji zasobu:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ta właściwość nakazuje platformie Azure utworzenie tożsamości i zarządzanie tą tożsamością API Management wystąpienia.

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

Po utworzeniu wystąpienie ma następujące dodatkowe właściwości:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Właściwość `tenantId` określa dzierżawę usługi Azure AD, do której należy tożsamość. Właściwość `principalId` jest unikatowym identyfikatorem nowej tożsamości wystąpienia. W usłudze Azure AD nazwa główna usługi ma tę samą nazwę, która została nadana API Management wystąpienia.

> [!NOTE]
> Wystąpienie API Management może mieć jednocześnie tożsamości przypisane przez system i przez użytkownika. W tym przypadku `type` właściwością będzie `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Obsługiwane scenariusze z użyciem tożsamości przypisanej przez system

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Uzyskaj niestandardowy certyfikat TLS/SSL dla wystąpienia API Management z Azure Key Vault
Tożsamość przypisana przez system wystąpienia usługi API Management umożliwia pobieranie niestandardowych certyfikatów protokołu TLS/SSL przechowywanych w Azure Key Vault. Następnie możesz przypisać te certyfikaty do domen niestandardowych w API Management wystąpienia. Należy pamiętać o następujących kwestiach:

- Klucz tajny musi mieć typ zawartości *application/x-pkcs12.*
- Użyj punktu końcowego Key Vault tajnego certyfikatu, który zawiera klucz tajny.

> [!Important]
> Jeśli nie poświadczeń wersji obiektu certyfikatu, program API Management automatycznie uzyska nowszą wersję certyfikatu w ciągu czterech godzin po jego zaktualizowaniu w Key Vault.

W poniższym przykładzie pokazano Azure Resource Manager szablonu, który zawiera następujące kroki:

1. Utwórz wystąpienie API Management przy użyciu tożsamości zarządzanej.
2. Zaktualizuj zasady dostępu do wystąpienia Azure Key Vault i zezwalaj wystąpieniu API Management na uzyskanie z niego wpisów tajnych.
3. Zaktualizuj API Management, ustawiając niestandardową nazwę domeny za pomocą certyfikatu z Key Vault wystąpienia.

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

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Uwierzytelnianie w zadumieniu przy użyciu tożsamości API Management danych

Tożsamość przypisana przez system umożliwia uwierzytelnianie w zakładzie za pomocą zasad [tożsamości zarządzanej uwierzytelniania.](api-management-authentication-policies.md#ManagedIdentity)

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Nawiązywanie połączenia z zasobami platformy Azure za zaporą IP przy użyciu tożsamości zarządzanej przypisanej przez system


API Management to zaufana usługa firmy Microsoft dla następujących zasobów. Dzięki temu usługa może łączyć się z następującymi zasobami za zaporą. Po jawnego przypisaniu odpowiedniej roli platformy Azure do przypisanej przez [system](../active-directory/managed-identities-azure-resources/overview.md) tożsamości zarządzanej dla tego wystąpienia zasobu zakres dostępu dla wystąpienia odpowiada roli platformy Azure przypisanej do tożsamości zarządzanej.


|Usługa platformy Azure | Link|
|---|---|
|Azure Storage | [Zaufany dostęp do usługi Azure Storage](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Usługa Azure Service Bus | [Zaufany dostęp do usługi azure-service-bus](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Centrum zdarzeń Azure | [Trused-access-to-azure-event-hub](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

> [!NOTE]
> Wystąpienie zarządzane API Management można skojarzyć z maksymalnie 10 tożsamościami zarządzanymi przypisanymi przez użytkownika.

### <a name="azure-portal"></a>Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, najpierw utworzysz wystąpienie API Management a następnie włączysz tę funkcję.

1. Utwórz API Management w portalu w zwykły sposób. Przejdź do niego w portalu.
2. Wybierz **pozycję Tożsamości zarządzane.**
3. Na karcie **Przypisano użytkownika** wybierz pozycję **Dodaj**.
4. Wyszukaj utworzoną wcześniej tożsamość i wybierz ją. Wybierz pozycję **Dodaj**.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Opcje włączania tożsamości zarządzanej przypisanej przez użytkownika" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

W poniższych krokach przedstawiono sposób tworzenia API Management i przypisywania do niego tożsamości przy użyciu Azure PowerShell. 

1. W razie potrzeby zainstaluj Azure PowerShell, korzystając z instrukcji w Azure PowerShell [przewodniku.](/powershell/azure/install-az-ps) Następnie uruchom , `Connect-AzAccount` aby utworzyć połączenie z platformą Azure.

2. Użyj następującego kodu, aby utworzyć wystąpienie. Aby uzyskać więcej przykładów użycia programu Azure PowerShell z wystąpieniem API Management, zobacz [API Management przykłady programu PowerShell.](powershell-samples.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Zaktualizuj istniejącą usługę, aby przypisać do usługi tożsamość:

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

Możesz utworzyć wystąpienie API Management tożsamości, uwzględniając następującą właściwość w definicji zasobu:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

Dodanie typu przypisanego przez użytkownika nakazuje platformie Azure użycie tożsamości przypisanej przez użytkownika określonej dla wystąpienia.

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

Po utworzeniu usługi ma ona następujące dodatkowe właściwości:

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

Właściwość `principalId` jest unikatowym identyfikatorem tożsamości używanej do administrowania usługą Azure AD. Właściwość jest unikatowym identyfikatorem nowej tożsamości aplikacji, która służy do określania tożsamości do użycia podczas `clientId` wywołań środowiska uruchomieniowego.

> [!NOTE]
> Wystąpienie API Management może mieć jednocześnie tożsamości przypisane przez system i przez użytkownika. W tym przypadku `type` właściwością będzie `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Obsługiwane scenariusze z użyciem tożsamości zarządzanej przypisanej przez użytkownika

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Uzyskaj niestandardowy certyfikat TLS/SSL dla wystąpienia API Management z Azure Key Vault
Możesz użyć dowolnej tożsamości przypisanej przez użytkownika, aby ustanowić relację zaufania między wystąpieniem API Management i wystąpieniem usługi KeyVault. Tego zaufania można następnie użyć do pobrania niestandardowych certyfikatów protokołu TLS/SSL przechowywanych w Azure Key Vault. Następnie możesz przypisać te certyfikaty do domen niestandardowych w API Management wystąpienia. 

Należy pamiętać o następujących kwestiach:

- Klucz tajny musi mieć typ zawartości *application/x-pkcs12.*
- Użyj punktu końcowego Key Vault tajnego certyfikatu, który zawiera klucz tajny.

> [!Important]
> Jeśli nie poświadczeń wersji obiektu certyfikatu, program API Management automatycznie uzyska nowszą wersję certyfikatu w ciągu czterech godzin po jego zaktualizowaniu w Key Vault.

Aby uzyskać kompletny szablon, zobacz API Management z protokołem SSL opartym na [programie KeyVault przy użyciu tożsamości przypisanej przez użytkownika.](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json)

W tym szablonie wdrożysz:

* Usługa Azure API Management
* Tożsamość przypisana przez użytkownika zarządzanego na platformie Azure
* Usługa Azure KeyVault do przechowywania certyfikatu SSL/TLS

Aby automatycznie uruchomić wdrożenie, kliknij poniższy przycisk:

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Uwierzytelnianie w zadumieniu przy użyciu tożsamości przypisanej przez użytkownika

Tożsamości przypisanej przez użytkownika można użyć do uwierzytelniania w założyeniu za pomocą zasad [tożsamości zarządzanej uwierzytelniania.](api-management-authentication-policies.md#ManagedIdentity)

## <a name="remove-an-identity"></a><a name="remove"></a>Usuwanie tożsamości

Tożsamość przypisaną przez system można usunąć, wyłączając funkcję za pośrednictwem portalu lub szablonu Azure Resource Manager w taki sam sposób, w jaki został utworzony. Tożsamości przypisane przez użytkownika można usuwać indywidualnie. Aby usunąć wszystkie tożsamości, ustaw typ tożsamości na `"None"` .

Usunięcie w ten sposób tożsamości przypisanej przez system spowoduje również usunięcie jej z usługi Azure AD. Tożsamości przypisane przez system są również automatycznie usuwane z usługi Azure AD po API Management wystąpienia.

Aby usunąć wszystkie tożsamości przy użyciu szablonu Azure Resource Manager, zaktualizuj tę sekcję:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Jeśli wystąpienie API Management skonfigurowano przy użyciu niestandardowego certyfikatu SSL z usługi Key Vault i spróbujesz wyłączyć tożsamość zarządzaną, żądanie nie powiedzie się.
>
> Możesz się odblokować, przełączając się z certyfikatu Azure Key Vault na certyfikat zakodowany w tekście, a następnie wyłączając tożsamość zarządzaną. Aby uzyskać więcej informacji, zobacz [Konfigurowanie nazwy domeny niestandardowej](configure-custom-domain.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tożsamościach zarządzanych dla zasobów platformy Azure:

* [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)
* [Szablony usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)
* [Uwierzytelnianie za pomocą tożsamości zarządzanej w zasadach](./api-management-authentication-policies.md#ManagedIdentity)
