---
title: Szyfrowanie trwałych oświadczeń zbiorczych za pomocą klucza zarządzanego przez klienta (CMK) na platformie Azure Red Hat OpenShift (ARO)
description: Użyj własnego klucza (BYOK)/klucza zarządzanego przez klienta (CMK) instrukcji dotyczących wdrażania usługi Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: szyfrowanie, BYOK, ARO, CMK, OpenShift, Red Hat
ms.openlocfilehash: cf028456cc8971678373d36214885c3f79df8e82
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047069"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Szyfrowanie trwałych oświadczeń zbiorczych za pomocą klucza zarządzanego przez klienta (CMK) na platformie Azure Red Hat OpenShift (ARO) (wersja zapoznawcza)

Usługa Azure Storage używa szyfrowania po stronie serwera (SSE), aby automatycznie [szyfrować](../storage/common/storage-service-encryption.md) dane po utrwaleniu ich w chmurze. Domyślnie dane są szyfrowane za pomocą kluczy zarządzanych przez platformę Microsoft. Aby uzyskać dodatkową kontrolę nad kluczami szyfrowania, możesz podać własne klucze zarządzane przez klienta do szyfrowania danych w klastrach usługi Azure Red Hat OpenShift.

> [!NOTE]
> Na tym etapie pomoc techniczna jest dostępna tylko w przypadku szyfrowania woluminów trwałych ARO z kluczami zarządzanymi przez klienta. Ta funkcja nie jest obecnie dostępna dla dysków z systemem operacyjnym lub głównym węzłów procesu roboczego.

> [!IMPORTANT]
> Funkcje wersji zapoznawczej wystawcy są dostępne w ramach samoobsługowego i samodzielnego wyboru. Wersje zapoznawcze są udostępniane w postaci "AS IS" i "jako dostępne" i są wyłączone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze wersji zapoznawczej są częściowo objęte wsparciem klienta w oparciu o optymalną pracę. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego.

## <a name="before-you-begin"></a>Zanim rozpoczniesz
W tym artykule przyjęto założenie, że:

* Masz już istniejący klaster ARO o godzinie OpenShift w wersji 4,4 (lub nowszej).

* Masz narzędzie wiersza polecenia OpenShift **OC** (część Coreutils) i zainstalowano polecenie **AZ** Azure CLI.

* Użytkownik jest zalogowany w klastrze ARO przy użyciu **OC** jako globalnego administratora klastra (kubeadmin).

* Użytkownik jest zalogowany do interfejsu wiersza polecenia platformy Azure przy użyciu polecenia **AZ** z kontem uprawnionym do udzielenia uprawnienia "Współautor" w tej samej subskrypcji co klaster ARO.

## <a name="limitations"></a>Ograniczenia

* Dostępność dla szyfrowania klucza zarządzanego przez klienta jest specyficzna dla regionu. Aby sprawdzić stan określonego regionu platformy Azure, sprawdź [regiony platformy Azure][supported-regions].
* Jeśli chcesz korzystać z usługi Ultra disks, musisz najpierw włączyć je w subskrypcji przed rozpoczęciem pracy.

## <a name="declare-cluster--encryption-variables"></a>Zadeklaruj zmienne szyfrowania & klastra
Należy skonfigurować poniższe zmienne do dowolnego, co może być odpowiednie dla Ciebie w klastrze ARO, w którym chcesz włączyć klucze szyfrowania zarządzane przez klienta:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Uzyskaj identyfikator subskrypcji
Identyfikator subskrypcji platformy Azure jest używany wiele razy w konfiguracji CMK. Uzyskaj ją i Zapisz jako zmienną:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Tworzenie wystąpienia Azure Key Vault
Aby można było przechowywać klucze, należy użyć wystąpienia Azure Key Vault. Utwórz nowy Key Vault z włączoną funkcją przeczyszczania ochrony. Następnie utwórz nowy klucz w magazynie do przechowywania własnego klucza niestandardowego:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Tworzenie zestawu Azure Disk Encryption

Zestaw Azure Disk Encryption jest używany jako punkt odniesienia dla dysków w wydaniu. Jest ona połączona z Azure Key Vault utworzoną w poprzednim kroku i spowoduje ściągnięcie kluczy zarządzanych przez klienta z tej lokalizacji.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Przyznaj szyfrowanie dysków dostęp do Key Vault
Użyj zestawu szyfrowania dysków utworzonego we wcześniejszych krokach i Udziel dostępu do szyfrowania dysków Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Uzyskanie innych identyfikatorów wymaganych do przypisywania ról
Musimy zezwolić klastrowi z użyciem szyfrowania dysków na szyfrowanie trwałych oświadczeń woluminów (PVC) w klastrze. W tym celu utworzymy nowy tożsamość usługi zarządzanej (MSI). Ustawimy również inne uprawnienia dla Instalatora MSI ARO i dla zestawu szyfrowania dysków.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Zaimplementuj inne przypisania ról wymagane przez szyfrowanie CMK
Zastosuj wymagane przypisania ról przy użyciu zmiennych uzyskanych w poprzednim kroku:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Tworzenie klasy magazynu k8s dla zaszyfrowanych dysków Premium & Ultra
Generuj klasy magazynu, które mają być używane na potrzeby CMK dla dysków Premium_LRS i UltraSSD_LRS:

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

Następnie Uruchom to wdrożenie w klastrze ARO, aby zastosować konfigurację klasy magazynu:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Testowanie szyfrowania przy użyciu kluczy zarządzanych przez klienta (opcjonalnie)
Aby sprawdzić, czy klaster korzysta z klucza zarządzanego przez klienta do szyfrowania obwodu PVC, należy utworzyć trwałego żądania woluminu przy użyciu nowej klasy magazynu. Poniższy fragment kodu tworzy pod i instaluje trwałego żądania woluminu przy użyciu dysków w warstwie Premium.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
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
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>Zastosuj test pod plik konfiguracyjny (opcjonalnie)
Wykonaj poniższe polecenia, aby zastosować konfigurację test pod i zwrócić identyfikator UID nowego zastrzeżenia trwałego woluminu. Identyfikator UID zostanie użyty do zweryfikowania, że dysk jest szyfrowany przy użyciu CMK.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Podczas stosowania przypisań ról w ramach Azure Active Directory mogą wystąpić niewielkie opóźnienia. W zależności od szybkości wykonywania tych instrukcji polecenie "Określ pełną nazwę dysku platformy Azure" może zakończyć się niepowodzeniem. W takim przypadku Przejrzyj dane wyjściowe w **języku OC opis obwodu PVC mypod-with-CMK-Encryption-PVC** , aby upewnić się, że dysk został pomyślnie zainicjowany. Jeśli Propagacja przypisania roli nie została ukończona, może być konieczne *usunięcie* i ponowne &*zastosowanie* YAML obwodu PVC.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Sprawdź, czy dysk PVC jest skonfigurowany przy użyciu "EncryptionAtRestWithCustomerKey" (opcjonalnie)
Na stronie należy utworzyć trwałego żądania, które odwołuje się do klasy magazynu CMK. Uruchomienie następującego polecenia spowoduje zweryfikowanie, czy obwód PVC został wdrożony zgodnie z oczekiwaniami:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions