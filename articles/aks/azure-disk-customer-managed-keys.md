---
title: Szyfrowanie dysków platformy Azure w usłudze Azure Kubernetes Service (AKS) przy użyciu klucza zarządzanego przez klienta
description: Bring your own keys (BYOK) do szyfrowania dysków systemu operacyjnego i danych AKS.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: c5c555d7eb5142f5f41f65b24f754c65450a2713
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776195"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Korzystanie z własnych kluczy (BYOK) z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, możesz podać klucze zarządzane przez klienta do użycia na potrzeby szyfrowania danych w spoczynku zarówno dla dysków systemu operacyjnego, jak i dysków danych dla klastrów usługi AKS. Dowiedz się więcej o kluczach zarządzanych przez klienta w [systemach Linux][customer-managed-keys-linux] [i Windows.][customer-managed-keys-windows]

## <a name="limitations"></a>Ograniczenia
* Obsługa szyfrowania dysków danych jest ograniczona do klastrów usługi AKS z uruchomionym platformą Kubernetes w wersji 1.17 lub nowszą.
* Szyfrowanie dysku systemu operacyjnego i danych przy użyciu kluczy zarządzanych przez klienta można włączyć tylko podczas tworzenia klastra usługi AKS.

## <a name="prerequisites"></a>Wymagania wstępne
* Należy włączyć ochronę usuwania nie soft i przeczyszczania Azure Key Vault *podczas* używania Key Vault do szyfrowania dysków zarządzanych.
* Potrzebny jest interfejs wiersza polecenia platformy Azure w wersji 2.11.1 lub nowszej.

## <a name="create-an-azure-key-vault-instance"></a>Tworzenie wystąpienia Azure Key Vault danych

Użyj wystąpienia Azure Key Vault do przechowywania kluczy.  Opcjonalnie możesz użyć tej Azure Portal, aby skonfigurować klucze zarządzane przez [klienta przy użyciu Azure Key Vault][byok-azure-portal]

Utwórz nową *grupę zasobów,* a następnie utwórz nowe wystąpienie *Key Vault* i włącz usuwanie nie softowe i ochronę przed przeczyszczaniem.  Upewnij się, że używasz tych samych nazw regionów i grup zasobów dla każdego polecenia.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Tworzenie wystąpienia zestawu DiskEncryptionSet

Zastąp *element myKeyVaultName* nazwą magazynu kluczy.  Do wykonania poniższych  kroków będzie również potrzebny klucz Azure Key Vault w uciekinie.  Przechowuj istniejący klucz w Key Vault utworzonym w poprzednich krokach lub wygeneruj nowy klucz i zastąp nazwę *myKeyName* poniżej nazwą klucza. [][key-vault-generate]
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Udzielanie funkcji DiskEncryptionSet dostępu do magazynu kluczy

Użyj zestawu DiskEncryptionSet i grup zasobów utworzonych w poprzednich krokach, a następnie przyznaj zasobowi DiskEncryptionSet dostęp do Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Tworzenie nowego klastra usługi AKS i szyfrowanie dysku systemu operacyjnego

Utwórz **nową grupę zasobów i** klaster usługi AKS, a następnie zaszyfruj dysk systemu operacyjnego przy użyciu klucza. Klucze zarządzane przez klienta są obsługiwane tylko w wersjach usługi Kubernetes większych niż 1.17. 

> [!IMPORTANT]
> Upewnij się, że tworzysz nową grupę zasobów dla klastra usługi AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Po dodaniu nowych pul węzłów do klastra utworzonego powyżej klucz zarządzany przez klienta podany podczas tworzenia jest używany do szyfrowania dysku systemu operacyjnego.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Szyfrowanie dysku danych klastra AKS (opcjonalnie)
Klucz szyfrowania dysku systemu operacyjnego będzie używany do szyfrowania dysku danych, jeśli nie podano klucza dla dysku danych z wersji 1.17.2, a także można zaszyfrować dyski danych usługi AKS przy użyciu innych kluczy.

> [!IMPORTANT]
> Upewnij się, że masz odpowiednie poświadczenia usługi AKS. Tożsamość zarządzana musi mieć dostęp współautora do grupy zasobów, w której wdrożono zestaw diskencryptionset. W przeciwnym razie wystąpi błąd z sugestią, że tożsamość zarządzana nie ma uprawnień.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Utwórz plik o nazwie **byok-azure-disk.yaml** zawierający następujące informacje.  Zastąp wartości myAzureSubscriptionId, myResourceGroup i myDiskEncrptionSetName swoimi wartościami, a następnie zastosuj kod yaml.  Upewnij się, że używasz grupy zasobów, w której wdrożono zestaw DiskEncryptionSet.  Jeśli używasz tej Azure Cloud Shell, ten plik można utworzyć przy użyciu narzędzia vi lub nano, tak jak podczas pracy w systemie wirtualnym lub fizycznym:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Następnie uruchom to wdrożenie w klastrze usługi AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj [się z najlepszymi rozwiązaniami w zakresie zabezpieczeń klastra usługi AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions