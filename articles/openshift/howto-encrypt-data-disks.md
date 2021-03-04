---
title: Szyfrowanie trwałych oświadczeń zbiorczych za pomocą klucza zarządzanego przez klienta (CMK) na platformie Azure Red Hat OpenShift (ARO)
description: Użyj własnego klucza (BYOK)/klucza zarządzanego przez klienta (CMK) instrukcji dotyczących wdrażania usługi Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: szyfrowanie, BYOK, ARO, OpenShift, Red Hat
ms.openlocfilehash: 09e1f92a967c7b77d3bb8e27769f4cafd4fd4f53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054779"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Szyfrowanie trwałych oświadczeń zbiorczych za pomocą klucza zarządzanego przez klienta (CMK) na platformie Azure Red Hat OpenShift (ARO) (wersja zapoznawcza)

Usługa Azure Storage szyfruje wszystkie dane na koncie magazynu w stanie spoczynku. Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych przez platformę Microsoft, które obejmują dyski systemu operacyjnego i danych. Aby uzyskać większą kontrolę nad kluczami szyfrowania, można dostarczyć klucze zarządzane przez klienta w celu szyfrowania danych w klastrach usługi Azure Red Hat OpenShift.

> [!NOTE]
> Na tym etapie pomoc techniczna jest dostępna tylko w przypadku szyfrowania woluminów trwałych ARO z kluczami zarządzanymi przez klienta. Ta funkcja nie jest obecnie dostępna dla dysków systemu operacyjnego.

> [!IMPORTANT]
> Funkcje wersji zapoznawczej wystawcy są dostępne w ramach samoobsługowego i samodzielnego wyboru. Wersje zapoznawcze są udostępniane w postaci "AS IS" i "jako dostępne" i są wykluczone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze wersji zapoznawczej są częściowo objęte wsparciem klienta w oparciu o optymalną pracę. W związku z tym funkcje te nie są przeznaczone do użytku produkcyjnego.

## <a name="before-you-begin"></a>Zanim rozpoczniesz
W tym artykule przyjęto założenie, że:

* Masz już istniejący klaster ARO o godzinie OpenShift w wersji 4,4 (lub nowszej).

* Masz narzędzie wiersza polecenia OpenShift "OC", Base64 (część podstawowych narzędzi) i zainstalowane polecenie "AZ" platformy Azure.

* Użytkownik jest zalogowany w klastrze ARO przy użyciu *OC* jako globalnego administratora klastra (kubeadmin).

* Użytkownik jest zalogowany do interfejsu wiersza polecenia platformy Azure przy użyciu polecenia *AZ* z kontem autoryzowanym do udzielenia uprawnienia "Współautor" w tej samej subskrypcji co klaster ARO.

## <a name="limitations"></a>Ograniczenia

* Dostępność dla szyfrowania klucza zarządzanego przez klienta jest specyficzna dla regionu. Aby sprawdzić stan określonego regionu platformy Azure, sprawdź [regiony platformy Azure][supported-regions].
* Jeśli używasz usługi Ultra disks, przed rozpoczęciem Włącz opcję Ultra disks w Twojej subskrypcji.

## <a name="create-an-azure-key-vault-instance"></a>Tworzenie wystąpienia Azure Key Vault
Aby można było przechowywać klucze, należy użyć wystąpienia Azure Key Vault. Utwórz nowy Key Vault z ochroną przed przeczyszczaniem i włączonym usuwaniem nietrwałym. Następnie utwórz nowy klucz w magazynie do przechowywania własnego klucza niestandardowego:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table
# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Tworzenie zestawu Azure Disk Encryption

Zestaw Azure Disk Encryption jest używany jako punkt odniesienia dla dysków w wydaniu. Jest on połączony z Azure Key Vault, który został utworzony w poprzednim kroku, i będzie ściągał klucze zarządzane przez klienta z tej lokalizacji.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $myRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Przyznaj szyfrowanie dysków dostęp do Key Vault
Użyj zestawu szyfrowania dysków utworzonego we wcześniejszych krokach i Udziel dostępu do szyfrowania dysków Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's AppId value.
desIdentity="$(az disk-encryption-set show -n $desName -g $myRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $myRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the disk encryption set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Uzyskanie innych identyfikatorów wymaganych do przypisywania ról
Musimy zezwolić klastrowi z użyciem szyfrowania dysków na szyfrowanie trwałych oświadczeń woluminów (PVC) w klastrze. W tym celu utworzymy nowy tożsamość usługi zarządzanej (MSI). Ustawimy również inne uprawnienia dla Instalatora MSI ARO i dla zestawu szyfrowania dysków.
```
# First, get the application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $myRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity application ID.
aroMSIAppId="$(az identity show -n $msiName -g $myRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
myRGResourceId="$(az group show -n $myRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-byokcmk-encryption"></a>Zaimplementuj inne przypisania ról wymagane przez szyfrowanie BYOK/CMK
Zastosuj wymagane przypisania ról przy użyciu zmiennych uzyskanych w poprzednim kroku:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $myRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $myRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks-optional"></a>Utwórz klasę magazynu k8s dla zaszyfrowanego dysku Premium & Ultra disks (opcjonalnie)
Generuj klasy magazynu, które mają być używane dla BYOK/CMK dla dysków Premium_LRS i UltraSSD_LRS:
```
# Premium Disks
cat > managed-premium-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```
### <a name="set-up-your-storage-class-configuration"></a>Konfigurowanie konfiguracji klasy magazynu
Zastąp zmienne, które są unikatowe dla klastra ARO, do dwóch plików konfiguracji klasy magazynu:
```
# Insert your current active subscription ID into the configuration
sed -i "s/subId/$subId/g" managed-premium-encrypted-byok.yaml
sed -i "s/subId/$subId/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the Resource Group which contains the disk encryption set and Key Vault
sed -i "s/myRG/$myRG/g" managed-premium-encrypted-byok.yaml
sed -i "s/myRG/$myRG/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the disk encryption set
sed -i "s/desName/$desName/g" managed-premium-encrypted-byok.yaml
sed -i "s/desName/$desName/g" managed-ultra-encrypted-byok.yaml
```
Następnie Uruchom to wdrożenie w klastrze ARO, aby zastosować konfigurację klasy magazynu:
```
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-byok.yaml
oc apply -f managed-ultra-encrypted-byok.yaml
```
## <a name="test-encryption-with-customer-managed-keys"></a>Testowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta
Aby sprawdzić, czy klaster używa klucza zarządzanego przez klienta do szyfrowania obwodu PVC, należy utworzyć trwałego żądania woluminu przy użyciu odpowiedniej klasy magazynu. Poniższy fragment kodu tworzy pod i instaluje trwałego żądania woluminu przy użyciu dysków standardowych
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-byok-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-byok
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-byok-encryption
spec:
  containers:
  - name: mypod-with-byok-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-byok-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file"></a>Zastosuj test pod plikiem konfiguracyjnym
Wykonaj poniższe polecenia, aby zastosować konfigurację test pod i zwrócić identyfikator UID nowego zastrzeżenia trwałego woluminu. Identyfikator UID zostanie użyty do sprawdzenia, czy dysk jest szyfrowany przy użyciu BYOK/CMK.
```
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o json | jq -r '.items[0].metadata.uid')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o json |jq -r '.spec.azureDisk.diskName')"
```
### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey"></a>Weryfikowanie, czy dysk PVC jest skonfigurowany przy użyciu "EncryptionAtRestWithCustomerKey" 
W obszarze należy utworzyć trwałego żądania, które odwołuje się do klasy magazynu BYOK/CMK. Uruchomienie następującego polecenia spowoduje zweryfikowanie, czy obwód PVC został wdrożony zgodnie z oczekiwaniami:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $myRG -o json --query [encryption]
```

## <a name="next-steps"></a>Następne kroki

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

