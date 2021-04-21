---
title: Tworzenie woluminu statycznego dla zasobników w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak ręcznie utworzyć wolumin z dyskami platformy Azure do użycia z zasobnikiem w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/01/2019
ms.openlocfilehash: 617ad75eda766963a91fe3d41b1dbfefae62b41b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776217"
---
# <a name="manually-create-and-use-a-volume-with-azure-disks-in-azure-kubernetes-service-aks"></a>Ręczne tworzenie i korzystanie z woluminu za pomocą dysków platformy Azure w usłudze Azure Kubernetes Service (AKS)

Aplikacje oparte na kontenerach często muszą mieć dostęp do danych w zewnętrznym woluminie danych i je utrwalać. Jeśli jeden zasobnik wymaga dostępu do magazynu, możesz użyć dysków platformy Azure, aby przedstawić wolumin natywny do użycia w aplikacji. W tym artykule pokazano, jak ręcznie utworzyć dysk platformy Azure i dołączyć go do zasobnika w usłudze AKS.

> [!NOTE]
> Dysk platformy Azure może być zainstalowany tylko w jednym zasobniku na raz. Jeśli chcesz udostępnić trwały wolumin w wielu zasobnikach, użyj programu [Azure Files][azure-files-volume].

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz [Storage options for applications in AKS (Opcje magazynu dla aplikacji w UKS).][concepts-storage]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub Azure Portal [.][aks-quickstart-portal]

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-an-azure-disk"></a>Tworzenie dysku platformy Azure

Podczas tworzenia dysku platformy Azure do użycia z usługą AKS można utworzyć zasób dysku w grupie **zasobów** węzła. Takie podejście umożliwia klastrowi AKS dostęp do zasobu dysku i zarządzanie nim. Jeśli zamiast tego utworzysz dysk w oddzielnej grupie zasobów, musisz przyznać tożsamości zarządzanej Azure Kubernetes Service (AKS) dla klastra rolę do grupy `Contributor` zasobów dysku.

Na podstawie tego artykułu utwórz dysk w grupie zasobów węzła. Najpierw pobierz nazwę grupy zasobów za pomocą polecenia [az aks show][az-aks-show] i dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera grupę zasobów węzła dla nazwy klastra usługi AKS *myAKSCluster* w grupie zasobów *o nazwie myResourceGroup:*

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Teraz utwórz dysk za pomocą [polecenia az disk create.][az-disk-create] Określ nazwę grupy zasobów węzła uzyskaną w poprzednim poleceniu, a następnie nazwę zasobu dysku, taką jak *myAKSDisk.* Poniższy przykład tworzy dysk *20* GiB i generuje identyfikator dysku po jego utworzeniu. Jeśli musisz utworzyć dysk do użycia z kontenerami systemu Windows Server, dodaj `--os-type windows` parametr , aby poprawnie sformatować dysk.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk \
  --size-gb 20 \
  --query id --output tsv
```

> [!NOTE]
> Opłaty za dyski platformy Azure są naliczane według sku za określony rozmiar. Te jednostki SKU mają zakres od 32GiB dla dysków S4 lub P4 do 32TiB dla dysków S80 lub P80 (w wersji zapoznawczej). Przepływność i wydajność IOPS dysku zarządzanego Premium zależy zarówno od jednostki SKU, jak i rozmiaru wystąpienia węzłów w klastrze usługi AKS. Zobacz [Cennik i wydajność Dyski zarządzane][managed-disk-pricing-performance].

Identyfikator zasobu dysku jest wyświetlany po pomyślnym zakończeniu polecenia, jak pokazano w poniższych przykładowych danych wyjściowych. Ten identyfikator dysku jest używany do instalacji dysku w następnym kroku.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Zainstaluj dysk jako wolumin

Aby zainstalować dysk platformy Azure w zasobniku, skonfiguruj wolumin w specyfikacji kontenera. Utwórz nowy plik o `azure-disk-pod.yaml` nazwie o następującej zawartości. Zaktualizuj przy użyciu nazwy dysku utworzonego w poprzednim kroku oraz identyfikatora dysku pokazanego w danych wyjściowych `diskName` `diskURI` polecenia tworzenia dysku. W razie potrzeby zaktualizuj , czyli ścieżkę, w której `mountPath` dysk platformy Azure jest zainstalowany w zasobniku. W przypadku kontenerów systemu Windows Server określ *ścieżkę mountPath* przy użyciu konwencji ścieżki systemu Windows, na przykład *"D:".*

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
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Użyj `kubectl` polecenia , aby utworzyć zasobnik.

```console
kubectl apply -f azure-disk-pod.yaml
```

Masz teraz uruchomiony zasobnik z dyskiem platformy Azure zainstalowanym `/mnt/azure` pod . Możesz użyć narzędzia `kubectl describe pod mypod` , aby sprawdzić, czy dysk został pomyślnie zainstalowany. Następujące skrócone przykładowe dane wyjściowe pokazują wolumin zainstalowany w kontenerze:

```
[...]
Volumes:
  azure:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     myAKSDisk
    DiskURI:      /subscriptions/<subscriptionID/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
    Optional:    false
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              1m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-z5sd7"
  Normal  SuccessfulMountVolume  41s   kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "azure"
[...]
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać skojarzone najlepsze rozwiązania, zobacz [Best practices for storage and backups in AKS][operator-best-practices-storage](Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w u usługi AKS).

Aby uzyskać więcej informacji na temat interakcji klastrów usługi AKS z dyskami platformy Azure, zobacz [wtyczkę Kubernetes dla dysków platformy Azure.][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az_resource_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-files-volume]: azure-files-volume.md
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
