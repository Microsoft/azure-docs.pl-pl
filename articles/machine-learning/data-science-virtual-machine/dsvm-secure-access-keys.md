---
title: Bezpieczne przechowywanie poświadczeń dostępu
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak bezpiecznie przechowywać poświadczenia dostępu na Data Science Virtual Machine. Dowiesz się, jak używać tożsamości usługi zarządzanej i Azure Key Vault do przechowywania poświadczeń dostępu.
keywords: głębokie uczenie się, AI, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, analiza geoprzestrzenna, proces nauki o danych zespołowych
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: adc66fc794ac6cce3fc0f8fd1552f701a4968860
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519681"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Bezpieczne przechowywanie poświadczeń dostępu na platformie Azure Data Science Virtual Machine

Często kod w aplikacjach w chmurze zawiera poświadczenia służące do uwierzytelniania w usługach w chmurze. Zarządzanie tymi poświadczeniami i ich Zabezpieczanie to dobrze znane wyzwanie w tworzeniu aplikacji w chmurze. W idealnym przypadku poświadczenia nigdy nie powinny występować na stacjach roboczych deweloperów ani nie są zaewidencjonowane do kontroli źródła.

[Zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) ułatwiają rozwiązanie tego problemu, dając usługi platformy Azure automatycznie zarządzaną tożsamość w Azure Active Directory (Azure AD). Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD bez konieczności przechowywania poświadczeń w kodzie.

Jednym ze sposobów zabezpieczania poświadczeń jest używanie Instalator Windows (MSI) w połączeniu z [Azure Key Vault](../../key-vault/index.yml), zarządzaną usługą platformy Azure, która umożliwia bezpieczne przechowywanie tajemnic i kluczy kryptograficznych. Możesz uzyskać dostęp do magazynu kluczy przy użyciu tożsamości zarządzanej, a następnie pobrać wpisy tajne i klucze kryptograficzne z magazynu kluczy.

Dokumentacja dotycząca zarządzanych tożsamości dla zasobów platformy Azure i Key Vault składa się ze wszechstronnego zasobu, aby uzyskać szczegółowe informacje na temat tych usług. W dalszej części tego artykułu przedstawiono podstawowe użycie MSI i Key Vault w Data Science Virtual Machine (DSVM) w celu uzyskania dostępu do zasobów platformy Azure. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Tworzenie tożsamości zarządzanej na DSVM

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Przypisywanie uprawnień dostępu Key Vault do podmiotu maszyny wirtualnej

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Dostęp do wpisu tajnego w magazynie kluczy z DSVM

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Dostęp do kluczy magazynu z DSVM

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>Dostęp do magazynu kluczy z poziomu języka Python

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>Dostęp do magazynu kluczy z poziomu interfejsu wiersza polecenia platformy Azure

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```