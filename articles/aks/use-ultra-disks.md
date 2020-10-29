---
title: Włącz obsługę Ultra Disk w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak włączyć i skonfigurować Ultra disks w klastrze usługi Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 049c2682a8f61bb658083b0418a4fcf99dc477a5
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900045"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Korzystanie z Azure Ultra disks w usłudze Azure Kubernetes Service (wersja zapoznawcza)

[Usługa Azure Ultra disks](../virtual-machines/disks-enable-ultra-ssd.md) oferuje wysoką przepływność, dużą liczbę IOPS i spójną małą ilość miejsca na dysku dla aplikacji stanowych. Jedną z głównych zalet funkcji Ultra disks jest możliwość dynamicznego zmieniania wydajności dysków SSD wraz z obciążeniami bez konieczności ponownego uruchamiania węzłów agentów. Ultra dyski są odpowiednie dla obciążeń intensywnie korzystających z danych.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Tę funkcję można ustawić tylko podczas tworzenia klastra lub tworzenia puli węzłów.

> [!IMPORTANT]
> Usługa Azure Ultra disks wymaga nodepools wdrożonych w strefach dostępności i regionach, które obsługują te dyski, a także tylko określonej serii maszyn wirtualnych. Zobacz [**zakres i ograniczenia dotyczące Ultra disks**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations).

### <a name="register-the-enableultrassd-preview-feature"></a>Rejestrowanie `EnableUltraSSD` funkcji w wersji zapoznawczej

Aby utworzyć klaster AKS lub pulę węzłów, która może korzystać z Ultra disks, należy włączyć `EnableUltraSSD` flagę funkcji w subskrypcji.

Zarejestruj `EnableUltraSSD` flagę funkcji za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Wyświetlenie stanu *rejestracji* może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster AKS lub pulę węzłów, która może korzystać z Ultra disks, potrzebne jest najnowsze rozszerzenie interfejsu wiersza polecenia *AKS-Preview* . Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej* przy użyciu poleceń [AZ Extension Add][az-extension-add] lub zainstaluj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Ograniczenia
- Zobacz [ **zakres i ograniczenia dotyczące Ultra DISKs ga**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Zakres obsługiwanego rozmiaru dla Ultra disks jest z zakresu od 100 do 1500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Tworzenie nowego klastra, który może korzystać z Ultra disks

Utwórz klaster AKS, który może korzystać z Ultra disks przy użyciu następujących poleceń interfejsu wiersza polecenia. Użyj `--aks-custom-headers` flagi, aby ustawić `EnableUltraSSD` funkcję.

Utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Utwórz klaster AKS z obsługą dla Ultra Disks.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Jeśli chcesz utworzyć klastry bez obsługi Ultra Disk, możesz to zrobić, pomijając `--aks-custom-headers` parametr niestandardowy.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Włącz Ultra disks w istniejącym klastrze

Można włączyć opcję Ultra disks w istniejących klastrach, dodając do klastra nową pulę węzłów, która obsługuje Ultra Disks. Skonfiguruj nową pulę węzłów do korzystania z szyfrowania opartego na hoście przy użyciu `--aks-custom-headers` flagi.

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Jeśli chcesz utworzyć nowe pule węzłów bez obsługi dla Ultra disks, możesz to zrobić, pomijając `--aks-custom-headers` parametr niestandardowy.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Dynamiczne używanie Ultra disks z klasą magazynu

Aby korzystać z usługi Ultra disks w naszych wdrożeniach lub zestawach stanowych, można użyć [klasy magazynu do dynamicznego inicjowania obsługi](azure-disks-dynamic-pv.md).

### <a name="create-the-storage-class"></a>Tworzenie klasy magazynu

Klasa magazynu służy do definiowania sposobu, w jaki jednostka magazynowa jest tworzona dynamicznie z woluminem trwałym. Aby uzyskać więcej informacji na temat klas magazynu Kubernetes, zobacz [Kubernetes Storage Classes][kubernetes-storage-classes].

W takim przypadku utworzymy klasę magazynu, która odwołuje się do bardzo małych dysków. Utwórz plik o nazwie `azure-ultra-disk-sc.yaml` i skopiuj go do poniższego manifestu.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

Utwórz klasę magazynu za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ plik *Azure-Ultra-Disk-SC. YAML* :

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Tworzenie trwałego żądania woluminu

W celu automatycznego aprowizacji magazynu na podstawie klasy magazynu jest używana wartość trwałego żądania woluminu. W takim przypadku obwód PVC może użyć wcześniej utworzonej klasy magazynu w celu utworzenia Ultra Disk.

Utwórz plik o nazwie `azure-ultra-disk-pvc.yaml` i skopiuj go do poniższego manifestu. Żądanie wymaga dysku o nazwie o `ultra-disk` rozmiarze *1000 GB* z dostępem *ReadWriteOnce* . Klasa magazynu *Ultra-Disk-SC* jest określana jako Klasa magazynu.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

Utwórz wartość trwałego odszkodowania z [zastosowaniem polecenia polecenia kubectl][kubectl-apply] i określ plik *Azure-Ultra-PVC. YAML* :

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Użyj woluminu trwałego

Po utworzeniu trwałego wystąpienia woluminu i zainicjowaniu obsługi dysku można utworzyć element pod za pomocą dostępu do dysku. Poniższy manifest tworzy podstawowy NGINX pod, który używa trwałego żądania o nazwie *Ultra-Disk* do zainstalowania dysku platformy Azure na ścieżce `/mnt/azure` .

Utwórz plik o nazwie `nginx-ultra.yaml` i skopiuj go do poniższego manifestu.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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
        claimName: ultra-disk
```

Utwórz pod za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] , jak pokazano w następującym przykładzie:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

Masz teraz uruchomione miejsce na dysku platformy Azure zainstalowanym w `/mnt/azure` katalogu. Ta konfiguracja może być widoczna podczas sprawdzania pod kątem za pośrednictwem programu `kubectl describe pod nginx-ultra` , jak pokazano w następującym zagęszczonym przykładzie:

```console
$ kubectl describe pod nginx-ultra

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```


## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat Ultra disks, zobacz [Korzystanie z usługi Azure Ultra disks](../virtual-machines/disks-enable-ultra-ssd.md).
- Aby uzyskać więcej informacji o najlepszych rozwiązaniach dotyczących magazynu, zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze Azure Kubernetes Service (AKS).][operator-best-practices-storage]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
