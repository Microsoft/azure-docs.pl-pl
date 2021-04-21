---
title: Dynamiczne tworzenie woluminu dysków platformy Azure
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak dynamicznie tworzyć trwały wolumin z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 066a52024e91610882889bb7fbe6b20efa262b71
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776145"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Dynamiczne tworzenie i używanie trwałego woluminu z dyskami platformy Azure w usłudze Azure Kubernetes Service (AKS)

Trwały wolumin reprezentuje fragment magazynu, który został aprowowany do użytku z zasobnikami kubernetes. Wolumin trwały może być używany przez jeden lub wiele zasobników i może być aprowowany dynamicznie lub statycznie. W tym artykule pokazano, jak dynamicznie tworzyć trwałe woluminy z dyskami platformy Azure do użycia przez pojedynczy zasobnik w klastrze usługi Azure Kubernetes Service (AKS).

> [!NOTE]
> Dysk platformy Azure można zamontowany tylko z *typem* trybu dostępu *ReadWriteOnce,* który udostępnia go w jednym węźle w usłudze AKS. Jeśli chcesz udostępnić wolumin trwały w wielu węzłach, użyj programu [Azure Files][azure-files-pvc].

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz Opcje magazynu [dla aplikacji w u usługi AKS][concepts-storage].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="built-in-storage-classes"></a>Wbudowane klasy magazynu

Klasa magazynu służy do definiowania dynamicznego tworzenia jednostki magazynu za pomocą trwałego woluminu. Aby uzyskać więcej informacji na temat klas magazynu Kubernetes, zobacz [Kubernetes Storage Classes (Klasy magazynu Kubernetes).][kubernetes-storage-classes]

Każdy klaster usługi AKS zawiera cztery wstępnie utworzone klasy magazynu, z których dwie są skonfigurowane do pracy z dyskami platformy Azure:

* Domyślna *klasa* magazynu apowiuje standardowy dysk SSD platformy Azure.
    * Magazyn w chmurze w standardowych dyskach SSD zapewnia ekonomiczne magazynowanie, jednocześnie zapewniając niezawodną wydajność. 
* Klasa *magazynu managed-premium* apowiuje dysk platformy Azure w chmurze w chmurze w chmurze.
    * Dyski w warstwie Premium są wspierane przez oparty na technologii SSD dysk o wysokiej wydajności i niskim opóźnieniu. Idealnie nadają się one dla maszyn wirtualnych z uruchomionym obciążeniem produkcyjnym. Jeśli węzły usługi AKS w klastrze używają magazynu w chmurze Premium Storage, wybierz *klasę managed-premium.*
    
Jeśli używasz jednej z domyślnych klas magazynu, nie możesz zaktualizować rozmiaru woluminu po utworzeniu klasy magazynu. Aby można było zaktualizować rozmiar woluminu po utworzeniu klasy magazynu, dodaj wiersz do jednej z domyślnych klas magazynu lub możesz utworzyć własną niestandardową `allowVolumeExpansion: true` klasę magazynu. Należy pamiętać, że nie jest obsługiwane zmniejszanie rozmiaru urządzenia IT (aby zapobiec utracie danych). Istniejącą klasę magazynu można edytować za pomocą `kubectl edit sc` polecenia . 

Jeśli na przykład chcesz użyć dysku o rozmiarze 4 TiB, musisz utworzyć klasę magazynu, która definiuje, ponieważ buforowanie dysku nie jest obsługiwane w przypadku dysków `cachingmode: None` [4 TiB i większych.](../virtual-machines/premium-storage-performance.md#disk-caching)

Aby uzyskać więcej informacji na temat klas magazynu i tworzenia własnej klasy magazynu, zobacz Opcje magazynu [dla aplikacji w u usługi AKS.][storage-class-concepts]

Użyj polecenia [kubectl get sc,][kubectl-get] aby wyświetlić wstępnie utworzone klasy magazynu. W poniższym przykładzie przedstawiono wstępnie utworzyć klasy magazynu dostępne w klastrze usługi AKS:

```console
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Oświadczenia trwałe woluminu są określane w gib, ale dyski zarządzane platformy Azure są rozliczane według SKU dla określonego rozmiaru. Te jednostki SKU obejmują zakres od 32GiB dla dysków S4 lub P4 do 32TiB dla dysków S80 lub P80 (w wersji zapoznawczej). Przepływność i wydajność IOPS dysku zarządzanego w wersji Premium zależy od jednostki SKU i rozmiaru wystąpienia węzłów w klastrze usługi AKS. Aby uzyskać więcej informacji, zobacz [Cennik i wydajność Dyski zarządzane][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Tworzenie trwałego oświadczenia woluminu

Trwałe oświadczenie woluminu jest używane do automatycznego aprowizować magazyn na podstawie klasy magazynu. W takim przypadku system ZARZĄDZANIA dostępem do sieci może użyć jednej ze wstępnie utworzonych klas magazynu do utworzenia dysku zarządzanego platformy Azure w chmurze w standardowych lub premium wersjach.

Utwórz plik o nazwie `azure-premium.yaml` i skopiuj go w poniższym manifeście. Oświadczenie żąda dysku o nazwie o rozmiarze `azure-managed-disk` *5 GB* z *dostępem ReadWriteOnce.* Klasa *magazynu managed-premium* jest określana jako klasa magazynu.

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
> Aby utworzyć dysk, który używa magazynu standardowego, użyj `storageClassName: default` zamiast *zarządzanej wersji Premium*.

Utwórz trwałe oświadczenie woluminu za pomocą [polecenia kubectl apply][kubectl-apply] i określ plik *azure-premium.yaml:*

```console
$ kubectl apply -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Używanie woluminu trwałego

Po utworzeniu oświadczenia woluminu trwałego i pomyślnym aprowizować dysk można utworzyć zasobnik z dostępem do dysku. Poniższy manifest tworzy podstawowy zasobnik NGINX, który używa trwałego oświadczenia woluminu o nazwie *azure-managed-disk* do instalacji dysku platformy Azure w ścieżce `/mnt/azure` . W przypadku kontenerów systemu Windows Server określ *ścieżkę mountPath* przy użyciu konwencji ścieżki systemu Windows, na przykład *"D:".*

Utwórz plik o nazwie `azure-pvc-disk.yaml` i skopiuj go w poniższym manifeście.

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

Utwórz zasobnik za pomocą [polecenia kubectl apply,][kubectl-apply] jak pokazano w poniższym przykładzie:

```console
$ kubectl apply -f azure-pvc-disk.yaml

pod/mypod created
```

Masz teraz uruchomiony zasobnik z dyskiem platformy Azure zainstalowanym w `/mnt/azure` katalogu . Tę konfigurację można zobaczyć podczas inspekcji zasobnika za pośrednictwem usługi `kubectl describe pod mypod` , jak pokazano w poniższym skróconym przykładzie:

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

## <a name="use-ultra-disks"></a>Korzystanie z dyski w warstwie Ultra
Aby wykorzystać dysk w kącie Ultra, zobacz [Use dyski w warstwie Ultra on Azure Kubernetes Service (AKS) (Używanie dysków w Azure Kubernetes Service (AKS).](use-ultra-disks.md)

## <a name="back-up-a-persistent-volume"></a>Kopii zapasowej woluminu trwałego

Aby utworzyć kopię zapasową danych na woluminie trwałym, należy utworzyć migawkę dysku zarządzanego dla woluminu. Następnie możesz użyć tej migawki, aby utworzyć przywrócony dysk i dołączyć go do zasobników w celu przywrócenia danych.

Najpierw pobierz nazwę woluminu za pomocą `kubectl get pvc` polecenia , na przykład dla maszyny wirtualnej o nazwie *azure-managed-disk:*

```console
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Ta nazwa woluminu stanowi podstawową nazwę dysku platformy Azure. Zapytanie o identyfikator dysku za pomocą [narzędzia az disk list][az-disk-list] i podanie nazwy woluminu WIĘC, jak pokazano w poniższym przykładzie:

```azurecli-interactive
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Użyj identyfikatora dysku, aby utworzyć dysk migawki za pomocą [narzędzia az snapshot create][az-snapshot-create]. Poniższy przykład tworzy migawkę o nazwie *yeSnapshot* w tej samej grupie zasobów co klaster usługi AKS (*MC_myResourceGroup_myAKSCluster_eastus*). Problemy z uprawnieniami mogą wystąpić w przypadku tworzenia migawek i przywracania dysków w grupach zasobów, do których klaster usługi AKS nie ma dostępu.

```azurecli-interactive
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

W zależności od ilości danych na dysku utworzenie migawki może potrwać kilka minut.

## <a name="restore-and-use-a-snapshot"></a>Przywracanie i używanie migawki

Aby przywrócić dysk i użyć go z zasobnikiem kubernetes, użyj migawki jako źródła podczas tworzenia dysku za pomocą narzędzia [az disk create.][az-disk-create] Ta operacja zachowuje oryginalny zasób, jeśli następnie trzeba uzyskać dostęp do oryginalnej migawki danych. Poniższy przykład tworzy dysk o *nazwierestored z* migawki o *nazwie dyskiem o nazwie jenapshot*:

```azurecli-interactive
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Aby użyć przywróconego dysku z zasobnikiem, określ identyfikator dysku w manifeście. Pobierz identyfikator dysku za pomocą [polecenia az disk show.][az-disk-show] Poniższy przykład pobiera identyfikator dysku dla *kontarestored* utworzonego w poprzednim kroku:

```azurecli-interactive
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Utwórz manifest zasobnika o nazwie `azure-restored.yaml` i określ jego nazwę URI uzyskaną w poprzednim kroku. Poniższy przykład tworzy podstawowy serwer internetowy NGINX z przywróconym dyskiem zainstalowanym jako wolumin *o adresie /mnt/azure:*

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

Utwórz zasobnik za pomocą [polecenia kubectl apply,][kubectl-apply] jak pokazano w poniższym przykładzie:

```console
$ kubectl apply -f azure-restored.yaml

pod/mypodrestored created
```

Możesz użyć funkcji , aby wyświetlić szczegóły zasobnika, takie jak poniższy `kubectl describe pod mypodrestored` skrócony przykład, który pokazuje informacje o woluminie:

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

Aby uzyskać skojarzone najlepsze rozwiązania, zobacz [Best practices for storage and backups in AKS][operator-best-practices-storage](Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w u usługi AKS).

Dowiedz się więcej o trwałych woluminach kubernetes korzystających z dysków platformy Azure.

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
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
