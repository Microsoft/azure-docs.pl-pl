---
title: Dynamicznie Twórz wolumin dysków platformy Azure
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak dynamicznie tworzyć wolumin trwały za pomocą dysków platformy Azure w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: ad51bfdf8c494e763921de880926b839cdb7be62
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021643"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dynamiczne tworzenie i używanie woluminu trwałego z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)

Wolumin trwały reprezentuje część magazynu, która została zainicjowana do użycia z Kubernetes. Wolumin trwały może być używany przez jeden lub wiele zasobników i może być dynamicznie lub statycznie inicjowany. W tym artykule opisano sposób dynamicznego tworzenia woluminów trwałych za pomocą dysków platformy Azure do użycia przez jeden element w klastrze usługi Azure Kubernetes Service (AKS).

> [!NOTE]
> Dysk platformy Azure można zainstalować tylko przy użyciu typu *trybu dostępu* *ReadWriteOnce*, który sprawia, że jest dostępny dla jednego węzła w AKS. Jeśli musisz udostępnić wolumin trwały w wielu węzłach, użyj [Azure Files][azure-files-pvc].

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz [Opcje magazynu dla aplikacji w AKS][concepts-storage].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="built-in-storage-classes"></a>Wbudowane klasy magazynu

Klasa magazynu służy do definiowania sposobu, w jaki jednostka magazynowa jest tworzona dynamicznie z woluminem trwałym. Aby uzyskać więcej informacji na temat klas magazynu Kubernetes, zobacz [Kubernetes Storage Classes][kubernetes-storage-classes].

Każdy klaster AKS zawiera cztery wstępnie utworzone klasy magazynu, dwie z nich zostały skonfigurowane do pracy z dyskami platformy Azure:

* *Domyślna* Klasa magazynu stanowi standardowy dysk SSD dysku platformy Azure.
    * Usługa Storage w warstwie Standardowa jest obsługiwana przez standardową dysków SSD i zapewnia oszczędny magazyn przy jednoczesnym dostarczaniu niezawodnej wydajności. 
* Klasa magazynu *Managed-Premium* udostępnia dysk platformy Azure w warstwie Premium.
    * Dyski w warstwie Premium są wspierane przez oparty na technologii SSD dysk o wysokiej wydajności i niskim opóźnieniu. Idealnie nadają się one dla maszyn wirtualnych z uruchomionym obciążeniem produkcyjnym. Jeśli węzły AKS w klastrze korzystają z magazynu Premium Storage, wybierz klasę *Managed-Premium* .
    
Jeśli używasz jednej z domyślnych klas magazynu, nie możesz zaktualizować rozmiaru woluminu po utworzeniu klasy magazynu. Aby można było zaktualizować rozmiar woluminu po utworzeniu klasy magazynu, należy dodać wiersz `allowVolumeExpansion: true` do jednej z domyślnych klas magazynu lub utworzyć własną niestandardową klasę magazynu. Należy pamiętać, że nie jest obsługiwane zmniejszenie rozmiaru obwodu PVC (aby zapobiec utracie danych). Istniejącą klasę magazynu można edytować za pomocą `kubectl edit sc` polecenia. 

Jeśli na przykład chcesz użyć dysku o rozmiarze 4 TiB, należy utworzyć klasę magazynu, która definiuje, `cachingmode: None` ponieważ [buforowanie dysków nie jest obsługiwane dla dysków 4 TIB i większych](../virtual-machines/premium-storage-performance.md#disk-caching).

Aby uzyskać więcej informacji na temat klas magazynu i tworzenia własnej klasy magazynu, zobacz [Opcje magazynu dla aplikacji w AKS][storage-class-concepts].

Aby wyświetlić wstępnie utworzone klasy magazynu, użyj polecenia [Get SC polecenia kubectl][kubectl-get] . Poniższy przykład przedstawia klasy magazynu przedprodukcyjnego dostępne w klastrze AKS:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Trwałe oświadczenia woluminu są określone w GiB, ale usługa Azure Managed disks jest rozliczana według jednostki SKU z określonym rozmiarem. Te jednostki SKU należą do zakresu od 32GiB dla dysków S4 lub P4 do 32TiB dla dysków S80 lub P80 (w wersji zapoznawczej). Wydajność i przepustowość operacji we/wy dysku zarządzanego w warstwie Premium zależy od jednostki SKU i rozmiaru wystąpienia węzłów w klastrze AKS. Aby uzyskać więcej informacji, zobacz [Cennik i wydajność Managed disks][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Tworzenie trwałego żądania woluminu

W celu automatycznego aprowizacji magazynu na podstawie klasy magazynu jest używana wartość trwałego żądania woluminu. W takim przypadku obwód PVC może użyć jednej z wstępnie utworzonych klas magazynu do utworzenia dysku zarządzanego w warstwie Standardowa lub Premium platformy Azure.

Utwórz plik o nazwie `azure-premium.yaml` i skopiuj go do poniższego manifestu. Zgłoszenie żąda dysku o nazwie `azure-managed-disk` *5 GB* w rozmiarze z dostępem *ReadWriteOnce* . Klasa magazynu *Managed-Premium* jest określana jako Klasa magazynu.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> Aby utworzyć dysk korzystający ze standardowego magazynu, użyj `storageClassName: default` zamiast *Managed-Premium*.

Utwórz wartość trwałego wystąpienia woluminu za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ plik *Azure-Premium. YAML* :

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Użyj woluminu trwałego

Po utworzeniu trwałego wystąpienia woluminu i zainicjowaniu obsługi dysku można utworzyć element pod za pomocą dostępu do dysku. Poniższy manifest tworzy podstawowy NGINX pod, który używa trwałego żądania o nazwie *Azure-Managed-Disk* do zainstalowania dysku platformy Azure na ścieżce `/mnt/azure` . W przypadku kontenerów systemu Windows Server należy określić *mountPath* przy użyciu konwencji ścieżki systemu Windows, takiej jak *'d: '*.

Utwórz plik o nazwie `azure-pvc-disk.yaml` i skopiuj go do poniższego manifestu.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
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
        claimName: azure-managed-disk
```

Utwórz pod za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] , jak pokazano w następującym przykładzie:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Masz teraz uruchomione miejsce na dysku platformy Azure zainstalowanym w `/mnt/azure` katalogu. Ta konfiguracja może być widoczna podczas sprawdzania pod kątem za pośrednictwem programu `kubectl describe pod mypod` , jak pokazano w następującym zagęszczonym przykładzie:

```console
$ kubectl describe pod mypod

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

## <a name="use-ultra-disks"></a>Korzystanie z Ultra disks
Aby korzystać z Ultra Disk, zobacz [Korzystanie z Ultra disks w usłudze Azure Kubernetes Service (AKS)](use-ultra-disks.md).

## <a name="back-up-a-persistent-volume"></a>Tworzenie kopii zapasowej woluminu trwałego

Aby utworzyć kopię zapasową danych w woluminie trwałym, należy wykonać migawkę dysku zarządzanego dla tego woluminu. Następnie można użyć tej migawki do utworzenia przywróconego dysku i dołączenia do zasobników jako metody przywracania danych.

Najpierw Pobierz nazwę woluminu za pomocą `kubectl get pvc` polecenia, na przykład dla obwodu PVC o nazwie *Azure-Managed-Disk*:

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Ta nazwa woluminu stanowi nazwę podstawowego dysku platformy Azure. Wykonaj zapytanie dotyczące identyfikatora dysku za pomocą elementu [AZ Disk list][az-disk-list] i podaj nazwę woluminu obwodu PVC, jak pokazano w następującym przykładzie:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Użyj identyfikatora dysku, aby utworzyć migawkę dysku za pomocą [AZ Snapshot Create][az-snapshot-create]. Poniższy przykład tworzy migawkę o nazwie *pvcSnapshot* w tej samej grupie zasobów co klaster AKS (*MC_myResourceGroup_myAKSCluster_eastus*). W przypadku tworzenia migawek i przywracania dysków w grupach zasobów, do których nie ma dostępu klaster AKS, mogą wystąpić problemy z uprawnieniami.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

W zależności od ilości danych na dysku utworzenie migawki może potrwać kilka minut.

## <a name="restore-and-use-a-snapshot"></a>Przywracanie i używanie migawki

Aby przywrócić dysk i użyć go z Kubernetes pod, użyj migawki jako źródła podczas tworzenia dysku za pomocą polecenie [AZ Disk Create][az-disk-create]. Ta operacja zachowuje pierwotny zasób, jeśli trzeba będzie uzyskać dostęp do oryginalnej migawki danych. Poniższy przykład tworzy dysk o nazwie *pvcRestored* z migawki o nazwie *pvcSnapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Aby użyć przywróconego dysku z systemem, określ identyfikator dysku w manifeście. Pobierz identyfikator dysku za pomocą polecenia [AZ Disk show][az-disk-show] . Poniższy przykład pobiera identyfikator dysku dla *pvcRestored* utworzonych w poprzednim kroku:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Utwórz manifest pod nazwą `azure-restored.yaml` i określ identyfikator URI dysku uzyskany w poprzednim kroku. Poniższy przykład tworzy podstawowy serwer sieci Web NGINX z przywróconym dyskiem zainstalowanym jako wolumin w */mnt/Azure*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
  - name: mypodrestored
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
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Utwórz pod za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] , jak pokazano w następującym przykładzie:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Możesz użyć `kubectl describe pod mypodrestored` , aby wyświetlić szczegóły dotyczące elementu, na przykład następujące wąskie przykładowe informacje o woluminie:

```console
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Następne kroki

W przypadku skojarzonych najlepszych rozwiązań zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w AKS][operator-best-practices-storage].

Dowiedz się więcej o woluminach trwałych Kubernetes przy użyciu usługi Azure Disks.

> [!div class="nextstepaction"]
> [Wtyczka Kubernetes dla dysków platformy Azure][azure-disk-volume]

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
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
