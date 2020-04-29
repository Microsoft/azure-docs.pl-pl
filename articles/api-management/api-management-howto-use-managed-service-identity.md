---
title: Korzystanie z tożsamości zarządzanych na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak używać tożsamości zarządzanych w programie API Management
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 49576b805e6c6d01340e663bfb5d8e9013917625
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249635"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Korzystanie z tożsamości zarządzanych w usłudze Azure API Management

W tym artykule pokazano, jak utworzyć zarządzaną tożsamość dla wystąpienia usługi API Management i jak uzyskać dostęp do innych zasobów. Zarządzana tożsamość wygenerowana przez usługę Azure Active Directory (Azure AD) umożliwia API Managementemu wystąpieniu i bezpieczny dostęp do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault. Ta tożsamość jest zarządzana przez platformę Azure i nie wymaga aprowizacji ani rotacji żadnych wpisów tajnych. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="create-a-managed-identity-for-an-api-management-instance"></a>Tworzenie tożsamości zarządzanej dla wystąpienia API Management

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, należy najpierw utworzyć wystąpienie API Management jako normalne, a następnie włączyć tę funkcję.

1. Utwórz wystąpienie API Management w portalu, jak zwykle. Przejdź do niej w portalu.
2. Wybierz pozycję **zarządzane tożsamości usługi**.
3. Przełącz rejestr z Azure Active Directory na włączone. Kliknij pozycję Zapisz.

![Włączanie tożsamości usługi zarządzanej](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Korzystanie z szablonu Azure Resource Manager

Można utworzyć wystąpienie API Management z tożsamością, dołączając następującą właściwość w definicji zasobu:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Oznacza to, że platforma Azure będzie mogła utworzyć tożsamość dla wystąpienia API Management i zarządzać nią.

Na przykład kompletny szablon Azure Resource Manager może wyglądać następująco:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2017-03-01",
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
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>Korzystanie z tożsamości usługi zarządzanej w celu uzyskiwania dostępu do innych zasobów

> [!NOTE]
> Obecnie tożsamości zarządzane mogą służyć do uzyskiwania certyfikatów z Azure Key Vault API Management niestandardowych nazw domen. Dalsze scenariusze będą obsługiwane wkrótce.
>
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Uzyskiwanie certyfikatu z Azure Key Vault

#### <a name="prerequisites"></a>Wymagania wstępne
1. Key Vault zawierający certyfikat PFX musi znajdować się w tej samej subskrypcji platformy Azure i tej samej grupie zasobów co usługa API Management. Jest to wymaganie szablonu Azure Resource Manager.
2. Typem zawartości wpisu tajnego musi być *Application/x-PKCS12*. Do przekazania certyfikatu można użyć następującego skryptu:

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> Jeśli nie zostanie podana wersja obiektu certyfikatu, program API Management automatycznie uzyska nowszą wersję certyfikatu po jego przekazaniu do Key Vault.

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
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
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
                "description": "Reference to the KeyVault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
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

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zarządzanych tożsamości dla zasobów platformy Azure:

* [Co to są tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md)
* [Szablony usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)
* [Uwierzytelnianie przy użyciu tożsamości zarządzanej w zasadach](./api-management-authentication-policies.md#ManagedIdentity)
