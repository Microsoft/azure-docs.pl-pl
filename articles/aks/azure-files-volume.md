---
title: Ręczne tworzenie Azure Files udziału
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak ręcznie utworzyć wolumin za pomocą Azure Files do użycia z wieloma współbieżnych zasobnikami w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 7f3c8ae63e908f440740277084293a011b80b9d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776092"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>Ręczne tworzenie i używanie woluminu z Azure Files w Azure Kubernetes Service (AKS)

Aplikacje oparte na kontenerach często muszą mieć dostęp do danych i utrwalać je w zewnętrznym woluminie danych. Jeśli wiele zasobników wymaga współbieżnego dostępu do tego samego woluminu magazynu, możesz użyć usługi Azure Files do nawiązania połączenia przy użyciu protokołu bloku komunikatów serwera [(SMB).][smb-overview] W tym artykule pokazano, jak ręcznie utworzyć udział Azure Files i dołączyć go do zasobnika w użytce AKS.

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz Opcje magazynu [dla aplikacji w u usługi AKS][concepts-storage].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure

Aby można było używać Azure Files jako woluminu Kubernetes, należy utworzyć konto usługi Azure Storage i udział plików. Następujące polecenia tworzą grupę zasobów o nazwie *myAKSShare,* konto magazynu i udział plików *o nazwie aksshare:*

```azurecli-interactive
# Change these four parameters as needed for your own environment
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create a resource group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create a storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv)

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME --connection-string $AZURE_STORAGE_CONNECTION_STRING

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storage account key: $STORAGE_KEY
```

Zanotuj nazwę konta magazynu i klucz wyświetlane na końcu danych wyjściowych skryptu. Te wartości są potrzebne podczas tworzenia woluminu Kubernetes w jednym z następujących kroków.

## <a name="create-a-kubernetes-secret"></a>Tworzenie tajnego kluczem kubernetes

Aby uzyskać dostęp do udziału plików utworzonego w poprzednim kroku, kubernetes wymaga poświadczeń. Te poświadczenia są przechowywane w [kluczu tajnym kubernetes,][kubernetes-secret]do którego odwołuje się podczas tworzenia zasobnika kubernetes.

Użyj polecenia `kubectl create secret` , aby utworzyć klucz tajny. Poniższy przykład tworzy udostępniony klucz o nazwie *azure-secret* i wypełnia wartości *azurestorageaccountname* i *azurestorageaccountkey* z poprzedniego kroku. Aby użyć istniejącego konta usługi Azure Storage, podaj nazwę i klucz konta.

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-file-share-as-an-inline-volume"></a>Zainstaluj udział plików jako wolumin w tekście
> Uwaga: począwszy od 1.18.15, 1.19.7, 1.20.2, 1.21.0, przestrzeń nazw tajnego w woluminie w tekście można ustawić tylko jako przestrzeń nazw, aby określić inną przestrzeń nazw tajnego, zamiast tego użyj poniższego przykładu `azureFile` `default` trwałego woluminu.

Aby zainstalować udział Azure Files w zasobniku, skonfiguruj wolumin w specyfikacji kontenera. Utwórz nowy plik o `azure-files-pod.yaml` nazwie z następującą zawartością. Jeśli zmieniono nazwę udziału plików lub nazwy tajnej, zaktualizuj wartości *shareName* i *secretName*. W razie potrzeby zaktualizuj plik , czyli ścieżkę, w której `mountPath` udział Plików jest zainstalowany w zasobniku. W przypadku kontenerów systemu Windows Server określ *ścieżkę mountPath przy* użyciu konwencji ścieżek systemu Windows, na przykład *"D:".*

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
  volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

Użyj `kubectl` polecenia , aby utworzyć zasobnik.

```console
kubectl apply -f azure-files-pod.yaml
```

Masz teraz uruchomiony zasobnik z zainstalowanym Azure Files */mnt/azure.* Możesz użyć funkcji `kubectl describe pod mypod` , aby sprawdzić, czy udział został pomyślnie zainstalowany. Następujące skrócone przykładowe dane wyjściowe pokazują wolumin zainstalowany w kontenerze:

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Sat, 02 Mar 2019 00:05:47 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="mount-file-share-as-an-persistent-volume"></a>Zainstaluj udział plików jako wolumin trwały
 - Mount options (Opcje instalacji)

Wartość domyślna dla *trybów fileMode* i *dirMode* to *0777* dla kubernetes w wersji 1.15 lub nowszej. Poniższy przykład ustawia *0755* obiektu *PersistentVolume:*

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    secretNamespace: default
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0755
  - file_mode=0755
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Aby zaktualizować opcje instalacji, utwórz plik *azurefile-mount-options-pv.yaml* za pomocą pliku *PersistentVolume.* Na przykład:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: aksshare
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
```

Utwórz plik *azurefile-mount-options-do.yaml* za pomocą rozszerzenia *PersistentVolumeClaim,* które używa rozszerzenia *PersistentVolume.* Na przykład:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 5Gi
```

Użyj poleceń `kubectl` , aby utworzyć pliki *PersistentVolume* i *PersistentVolumeClaim.*

```console
kubectl apply -f azurefile-mount-options-pv.yaml
kubectl apply -f azurefile-mount-options-pvc.yaml
```

Sprawdź, czy utworzono i powiązano *trwałevolumeclaim* z *trwałejvolume*.

```console
$ kubectl get pvc azurefile

NAME        STATUS   VOLUME      CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound    azurefile   5Gi        RWX            azurefile      5s
```

Zaktualizuj specyfikację kontenera, aby odwoływać się *do aplikacji PersistentVolumeClaim* i zaktualizować zasobnik. Na przykład:

```yaml
...
  volumes:
  - name: azure
    persistentVolumeClaim:
      claimName: azurefile
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać skojarzone najlepsze rozwiązania, zobacz [Best practices for storage and backups in AKS][operator-best-practices-storage](Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w u usługi AKS).

Aby uzyskać więcej informacji na temat interakcji klastrów usługi AKS z Azure Files, zobacz [wtyczkę Kubernetes dla usługi Azure Files][kubernetes-files].

Aby uzyskać parametry klasy magazynu, zobacz [Static Provision(bring your own file share).](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#static-provisionbring-your-own-file-share)

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
