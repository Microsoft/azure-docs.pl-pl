---
title: Konfigurowanie kluczy zarządzanych przez klienta dla interfejsu API platformy Azure dla usługi FHIR
description: Korzystaj z własnej funkcji klucza obsługiwanej w interfejsie API platformy Azure dla FHIR za pomocą Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: 08b5f63f1fffc2369eff620ca1937f298c2d9ca5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020387"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Konfigurowanie kluczy zarządzanych przez klienta w spoczynku

Po utworzeniu nowego konta usługi Azure API for FHIR dane są szyfrowane domyślnie przy użyciu kluczy zarządzanych przez firmę Microsoft. Teraz możesz dodać drugą warstwę szyfrowania danych przy użyciu własnego klucza, który można wybrać i zarządzać nimi.

Na platformie Azure jest to zwykle realizowane przy użyciu klucza szyfrowania w Azure Key Vault klienta. W przypadku usługi Azure SQL, usługi Azure Storage i Cosmos DB są to przykłady, które umożliwiają dzisiejszą obsługę tej funkcji. Interfejs API platformy Azure dla usługi FHIR korzysta z tej obsługi Cosmos DB. Podczas tworzenia konta będzie dostępna opcja określania identyfikatora URI klucza Azure Key Vault. Ten klucz zostanie przesłany do Cosmos DB, gdy zostanie zainicjowany konto bazy danych. Po utworzeniu żądania FHIR Cosmos DB Pobiera klucz i używa go do szyfrowania/odszyfrowywania danych. Aby rozpocząć, możesz skorzystać z następujących linków:

- [Zarejestruj dostawcę zasobów Azure Cosmos DB dla subskrypcji platformy Azure](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Skonfiguruj wystąpienie Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Dodawanie zasad dostępu do wystąpienia Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Wygeneruj klucz w Azure Key Vault](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>Korzystanie z witryny Azure Portal

Podczas tworzenia konta usługi Azure API for FHIR na Azure Portal można zobaczyć opcję konfiguracji "szyfrowanie danych" w obszarze "ustawienia bazy danych" na karcie "dodatkowe ustawienia". Domyślnie zostanie wybrana opcja klucz zarządzany przez usługę. 

Możesz wybrać swój klucz z klucza wyboru:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Selektora":::

Możesz też określić klucz Azure Key Vault w tym miejscu, wybierając opcję "klucz zarządzany przez klienta". Identyfikator URI klucza można wprowadzić tutaj:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Tworzenie interfejsu API platformy Azure dla usługi FHIR":::

W przypadku istniejących kont usługi FHIR można wyświetlić opcję szyfrowania klucza (klucz zarządzany przez usługę lub klienta) w bloku "baza danych" w następujący sposób. Nie można już zmodyfikować opcji konfiguracji. Można jednak zmodyfikować i zaktualizować klucz.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database"::: (Baza danych)

Ponadto można utworzyć nową wersję określonego klucza, po którym dane będą szyfrowane za pomocą nowej wersji bez żadnych przerw w świadczeniu usług. Możesz również usunąć dostęp do klucza, aby usunąć dostęp do danych. Gdy klucz jest wyłączony, zapytania spowodują wystąpienie błędu. Jeśli klucz zostanie ponownie włączony, zapytania zakończą się pomyślnie.




## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Za pomocą identyfikatora URI klucza Azure Key Vault można skonfigurować CMK przy użyciu programu PowerShell, uruchamiając poniższe polecenie programu PowerShell:

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Podobnie jak w przypadku metody programu PowerShell, można skonfigurować CMK, przekazując identyfikator URI klucza Azure Key Vault w ramach `key-vault-key-uri` parametru i uruchamiając poniższe polecenie interfejsu wiersza polecenia: 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>Korzystanie z szablonu Azure Resource Manager

Za pomocą identyfikatora URI Azure Key Vault klucza można skonfigurować CMK przez przekazanie go do właściwości **keyVaultKeyUri** w obiekcie **Properties** .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

Szablon można wdrożyć za pomocą następującego skryptu programu PowerShell:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania kluczy zarządzanych przez klienta w czasie spoczynku przy użyciu szablonu Azure Portal, programu PowerShell, interfejsu wiersza polecenia i Menedżer zasobów. Możesz zapoznać się z sekcją często zadawanych pytań Azure Cosmos DB, aby uzyskać dodatkowe pytania: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: jak skonfigurować CMK](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)