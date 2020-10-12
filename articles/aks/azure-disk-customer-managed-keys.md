---
title: Szyfrowanie dysków platformy Azure w usłudze Azure Kubernetes Service (AKS) przy użyciu klucza zarządzanego przez klienta
description: Korzystaj z własnych kluczy (BYOK), aby szyfrować system operacyjny AKS i dyski z danymi.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 8687d95878cde7d0ed3308d67f26ffc266abad1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297760"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Przenoszenie własnych kluczy (BYOK) z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, można podać klucze zarządzane przez klienta, które będą używane do szyfrowania dla dysków systemu operacyjnego i danych dla klastrów AKS. Dowiedz się więcej o kluczach zarządzanych przez klienta w systemach [Linux][customer-managed-keys-linux] i [Windows][customer-managed-keys-windows].

## <a name="limitations"></a>Ograniczenia
* Obsługa szyfrowania dysków danych jest ograniczona do klastrów AKS z systemem Kubernetes w wersji 1,17 lub nowszej.
* Szyfrowanie systemu operacyjnego i dysku danych z kluczami zarządzanymi przez klienta można włączyć tylko podczas tworzenia klastra AKS.

## <a name="prerequisites"></a>Wymagania wstępne
* Należy włączyć nietrwałe usuwanie i przeczyszczanie ochrony dla *Azure Key Vault* podczas korzystania z Key Vault do szyfrowania dysków zarządzanych.
* Potrzebujesz interfejsu wiersza polecenia platformy Azure w wersji 2.11.1 lub nowszej.

## <a name="create-an-azure-key-vault-instance"></a>Tworzenie wystąpienia Azure Key Vault

Użyj wystąpienia Azure Key Vault, aby przechowywać klucze.  Opcjonalnie możesz użyć Azure Portal, aby [skonfigurować klucze zarządzane przez klienta za pomocą Azure Key Vault][byok-azure-portal]

Utwórz nową *grupę zasobów*, a następnie utwórz nowe wystąpienie *Key Vault* i Włącz trwałe usuwanie i przeczyszczanie.  Upewnij się, że dla każdego polecenia używasz tego samego regionu i nazwy grupy zasobów.

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

## <a name="create-an-instance-of-a-diskencryptionset"></a>Tworzenie wystąpienia elementu DiskEncryptionSet

Zastąp *myKeyVaultName* nazwą magazynu kluczy.  Konieczne będzie również posiadanie *klucza* przechowywanego w Azure Key Vault, aby wykonać następujące czynności.  Zapisz istniejący klucz w Key Vault utworzonym w poprzednich krokach lub [Wygeneruj nowy klucz][key-vault-generate] i Zastąp element *NazwaKlucza* poniżej nazwą klucza.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query [id] -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query [key.kid] -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>Przyznaj DiskEncryptionSet dostęp do magazynu kluczy

Użyj DiskEncryptionSet i grup zasobów utworzonych w poprzednich krokach i Udziel dostępowi DiskEncryptionSet do Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query [identity.principalId] -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Tworzenie nowego klastra AKS i szyfrowanie dysku systemu operacyjnego

Utwórz **nową grupę zasobów** i klaster AKS, a następnie użyj klucza do szyfrowania dysku systemu operacyjnego. Klucze zarządzane przez klienta są obsługiwane tylko w wersjach Kubernetes większych niż 1,17. 

> [!IMPORTANT]
> Upewnij się, że tworzysz nową grupę zasobów dla klastra AKS

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query [id] -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Po dodaniu nowych pul węzłów do klastra utworzonego powyżej klucz zarządzany przez klienta podany podczas tworzenia jest używany do szyfrowania dysku systemu operacyjnego.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Szyfruj dysk danych klastra AKS (opcjonalnie)
Klucz szyfrowania dysku systemu operacyjnego będzie używany do szyfrowania dysku danych, jeśli nie podano klucza dla dysku danych z programu v 1.17.2. można także szyfrować dyski danych AKS z innymi kluczami.

> [!IMPORTANT]
> Upewnij się, że masz odpowiednie poświadczenia AKS. Jednostka usługi musi mieć dostęp współautora do grupy zasobów, w której wdrożono diskencryptionset. W przeciwnym razie zostanie wyświetlony komunikat o błędzie z sugestią, że jednostka usługi nie ma uprawnień.

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

Utwórz plik o nazwie **BYOK-Azure-Disk. YAML** zawierający poniższe informacje.  Zastąp wartości myAzureSubscriptionId, XmlResources i myDiskEncrptionSetName wartościami i Zastosuj YAML.  Upewnij się, że korzystasz z grupy zasobów, w której wdrożono DiskEncryptionSet.  Jeśli używasz Azure Cloud Shell, ten plik można utworzyć przy użyciu VI lub nano jako w przypadku pracy w systemie wirtualnym lub fizycznym:

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
Następnie Uruchom to wdrożenie w klastrze AKS:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi zabezpieczeń klastra AKS][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/storage-encryption-keys-portal.md
[customer-managed-keys-windows]: ../virtual-machines/windows/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/linux/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/windows/disk-encryption.md#supported-regions
