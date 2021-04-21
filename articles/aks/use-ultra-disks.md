---
title: Włączanie Ultra Disk dla Azure Kubernetes Service (AKS)
description: Dowiedz się, jak włączyć i skonfigurować dyski w warstwie Ultra w klastrze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 7dbe0a75ce2079bdec752f7fee0c3e97e3ae2ffa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767353"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>Korzystanie z dysków w warstwie Ultra na Azure Kubernetes Service (wersja zapoznawcza)

[Dyski w warstwie Ultra](../virtual-machines/disks-enable-ultra-ssd.md) platformy Azure oferują wysoką przepływność, dużą ilość operacji we/wy na magazynowanie oraz stały magazyn dyskowy o małych opóźnieniach dla aplikacji stanowych. Jedną z głównych zalet dysków w warstwie Ultra jest możliwość dynamicznej zmiany wydajności dysków SSD wraz z obciążeniami bez konieczności ponownego uruchamiania węzłów agenta. Dyski w warstwie Ultra są odpowiednie dla obciążeń intensywnie obciążanych danymi.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Tę funkcję można ustawić tylko podczas tworzenia klastra lub tworzenia puli węzłów.

> [!IMPORTANT]
> Dyski w warstwie Ultra platformy Azure wymagają puli węzłów wdrożonych w strefach dostępności i regionach, które obsługują te dyski, a także tylko określonych serii maszyn wirtualnych. Zobacz zakres i ograniczenia dotyczące dysków w [**warstwie Ultra.**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)

### <a name="register-the-enableultrassd-preview-feature"></a>Rejestrowanie funkcji w `EnableUltraSSD` wersji zapoznawczej

Aby utworzyć klaster usługi AKS lub pulę węzłów, która może korzystać z dysków w warstwie Ultra, należy włączyć `EnableUltraSSD` flagę funkcji w subskrypcji.

Zarejestruj `EnableUltraSSD` flagę funkcji za pomocą [polecenia az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

Wyświetlanie zarejestrowanego stanu może potrwać *kilka minut.* Stan rejestracji można sprawdzić za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy *zasobów Microsoft.ContainerService* za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster usługi AKS lub pulę węzłów, która może używać dyski w warstwie Ultra, potrzebne jest najnowsze rozszerzenie interfejsu wiersza polecenia *aks-preview.* Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* za pomocą [polecenia az extension add][az-extension-add] lub zainstaluj wszystkie dostępne aktualizacje za pomocą polecenia az extension [update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>Ograniczenia
- Zobacz zakres [ **i ograniczenia dotyczące dysków w warstwie Ultra**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- Obsługiwany zakres rozmiarów dysków w warstwie Ultra to od 100 do 1500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>Tworzenie nowego klastra, który może używać dysków w warstwie Ultra

Utwórz klaster usługi AKS, który może korzystać z dyski w warstwie Ultra przy użyciu następujących poleceń interfejsu wiersza polecenia. Użyj `--aks-custom-headers` flagi , aby ustawić `EnableUltraSSD` funkcję.

Utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Utwórz klaster usługi AKS z obsługą dyski w warstwie Ultra.

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Jeśli chcesz tworzyć klastry bez obsługi dysków w ultra, możesz to zrobić, pomijając parametr `--aks-custom-headers` niestandardowy.

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>Włączanie dysków w warstwie Ultra w istniejącym klastrze

Dyski w warstwie Ultra można włączyć w istniejących klastrach, dodając nową pulę węzłów do klastra, który obsługuje dyski w warstwie Ultra. Skonfiguruj nową pulę węzłów do używania dysków w warstwie Ultra przy użyciu `--aks-custom-headers` flagi .

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

Jeśli chcesz utworzyć nowe pule węzłów bez obsługi dysków w warstwie Ultra, możesz to zrobić, pomijając parametr `--aks-custom-headers` niestandardowy.

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>Dynamiczne używanie dysków w warstwie Ultra z klasą magazynu

Aby używać dysków w warstwie Ultra we wdrożeniach lub zestawach stanowych, możesz użyć [klasy magazynu do dynamicznego aprowizowania.](azure-disks-dynamic-pv.md)

### <a name="create-the-storage-class"></a>Tworzenie klasy magazynu

Klasa magazynu służy do definiowania dynamicznego tworzenia jednostki magazynu za pomocą trwałego woluminu. Aby uzyskać więcej informacji na temat klas magazynu Kubernetes, zobacz [Kubernetes Storage Classes (Klasy magazynu Kubernetes).][kubernetes-storage-classes]

W tym przypadku utworzymy klasę magazynu, która odwołuje się do dysków w warstwie Ultra. Utwórz plik o nazwie `azure-ultra-disk-sc.yaml` i skopiuj go w poniższym manifeście.

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

Utwórz klasę magazynu za pomocą [polecenia kubectl apply][kubectl-apply] i określ plik *azure-ultra-disk-sc.yaml:*

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>Tworzenie trwałego oświadczenia woluminu

Trwałe oświadczenie woluminu jest używane do automatycznego aprowizować magazyn na podstawie klasy magazynu. W takim przypadku za pomocą utworzonej wcześniej klasy magazynu można utworzyć dysk ULTRA.

Utwórz plik o nazwie `azure-ultra-disk-pvc.yaml` i skopiuj go w poniższym manifeście. Oświadczenie żąda dysku o nazwie o `ultra-disk` *rozmiarze 1000 GB* z dostępem *ReadWriteOnce.* Klasa *magazynu ultra-disk-sc* jest określona jako klasa magazynu.

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

Utwórz trwałe oświadczenie woluminu za pomocą [polecenia kubectl apply][kubectl-apply] i określ plik *azure-ultra-disk-format.yaml:*

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>Używanie woluminu trwałego

Po utworzeniu oświadczenia trwałego woluminu i pomyślnym aprowizować dysk można utworzyć zasobnik z dostępem do dysku. Poniższy manifest tworzy podstawowy zasobnik NGINX, który używa trwałego oświadczenia woluminu o nazwie *ultra-disk* do instalacji dysku platformy Azure w ścieżce `/mnt/azure` .

Utwórz plik o nazwie `nginx-ultra.yaml` i skopiuj go w poniższym manifeście.

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

Utwórz zasobnik za pomocą [polecenia kubectl apply,][kubectl-apply] jak pokazano w poniższym przykładzie:

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

Masz teraz uruchomiony zasobnik z dyskiem platformy Azure zainstalowanym w `/mnt/azure` katalogu . Taką konfigurację można zobaczyć podczas inspekcji zasobnika za pośrednictwem usługi , jak `kubectl describe pod nginx-ultra` pokazano w poniższym skróconym przykładzie:

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

- Aby uzyskać więcej informacji na temat dysków w warstwie Ultra, zobacz [Używanie dysków w warstwie Ultra platformy Azure.](../virtual-machines/disks-enable-ultra-ssd.md)
- Aby uzyskać więcej informacji o najlepszych rozwiązaniach dotyczących magazynu, zobacz Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w [Azure Kubernetes Service (AKS)][operator-best-practices-storage]

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
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
