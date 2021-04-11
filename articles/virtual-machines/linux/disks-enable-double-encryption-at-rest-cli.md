---
title: Włącz podwójne szyfrowanie w obszarze REST — dyski zarządzane w interfejsie wiersza polecenia platformy Azure
description: Włącz podwójne szyfrowanie dla danych dysku zarządzanego przy użyciu interfejsu wiersza polecenia platformy Azure.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 266ba4a6fbe0607fd09f86f5cd01addfa60252aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558325"
---
# <a name="use-the-azure-cli-to-enable-double-encryption-at-rest-for-managed-disks"></a>Użyj interfejsu wiersza polecenia platformy Azure, aby włączyć podwójne szyfrowanie dla dysków zarządzanych

Azure Disk Storage obsługuje podwójne szyfrowanie dla dysków zarządzanych. Aby uzyskać informacje koncepcyjne na temat podwójnego szyfrowania w stanie spoczynku, a także innych typów szyfrowania dysków zarządzanych, zobacz sekcję [podwójne szyfrowanie w spoczynku](../disk-encryption.md#double-encryption-at-rest) artykułu szyfrowanie dysków.

## <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zaloguj się do konta platformy Azure przy użyciu [AZ login](/cli/azure/reference-index).

## <a name="getting-started"></a>Wprowadzenie

1. Utwórz wystąpienie Azure Key Vault i klucza szyfrowania.

    Podczas tworzenia wystąpienia Key Vault należy włączyć opcję trwałe usuwanie i przeczyszczanie ochrony. Usuwanie nietrwałe gwarantuje, że Key Vault przechowuje usunięty klucz dla danego okresu przechowywania (wartość domyślna 90). Ochrona przed przeczyszczeniem gwarantuje, że usunięty klucz nie może zostać trwale usunięty, dopóki nie upłynie okres przechowywania. Te ustawienia chronią przed utratą danych z powodu przypadkowego usunięcia. Te ustawienia są obowiązkowe w przypadku używania Key Vault do szyfrowania dysków zarządzanych.

    
    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Utwórz element DiskEncryptionSet z atrybutem EncryptionType ustawiony jako EncryptionAtRestWithPlatformAndCustomerKeys. Użyj interfejsu API w wersji **2020-05-01** w szablonie Azure Resource Manager (ARM). 
    
        ```azurecli
        az deployment group create -g $rgName \
       --template-uri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" \
        --parameters "diskEncryptionSetName=$diskEncryptionSetName" "encryptionType=EncryptionAtRestWithPlatformAndCustomerKeys" "keyVaultId=$keyVaultId" "keyVaultKeyUrl=$keyVaultKeyUrl" "region=$location"
        ```

1.    Przyznaj zasobowi DiskEncryptionSet dostęp do magazynu kluczy. 

        > [!NOTE]
        > Utworzenie tożsamości DiskEncryptionSet w Azure Active Directory przez platformę Azure może potrwać kilka minut. Jeśli zostanie wyświetlony błąd "nie można odnaleźć Active Directory obiektu" podczas uruchamiania poniższego polecenia, poczekaj kilka minut i spróbuj ponownie.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)

        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
        ```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu i skonfigurowaniu tych zasobów można użyć ich do zabezpieczenia dysków zarządzanych. Poniższe linki zawierają przykładowe skrypty, z których każdy może być używany do zabezpieczania dysków zarządzanych.

- [Przykłady szablonów usługi Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)
- [Włącz klucze zarządzane przez klienta przy użyciu szyfrowania po stronie serwera — przykłady](disks-enable-customer-managed-keys-cli.md#examples)
