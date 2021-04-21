---
title: Włączanie Container Storage Interface (CSI) na Azure Kubernetes Service (AKS)
description: Dowiedz się, jak włączyć sterowniki Container Storage Interface (CSI) dla dysków platformy Azure i Azure Files w klastrze Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c9edfdf1c9740ec1fdaaeeedbc6ba92793eb0b3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779961"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Włączanie Container Storage Interface (CSI) dla dysków platformy Azure i Azure Files na Azure Kubernetes Service (AKS) (wersja zapoznawcza)

Standard Container Storage Interface (CSI) to standard uwidniania dowolnych systemów magazynowania bloków i plików w kontenerach obciążeń na platformie Kubernetes. Wdrażając i używając csi, program Azure Kubernetes Service (AKS) może pisać, wdrażać i iterować wtyczki w celu uwidoczniania lub ulepszania istniejących systemów magazynowania na platformie Kubernetes bez konieczności dotykania podstawowego kodu Kubernetes i oczekiwania na cykle wydań.

Obsługa sterowników magazynu CSI w u usługi AKS umożliwia natywne korzystanie z:
- [*Dyski platformy Azure,*](azure-disk-csi.md)których można użyć do utworzenia zasobu Kubernetes *DataDisk.* Dyski mogą używać usługi Azure Premium Storage z dyskami SSD o wysokiej wydajności lub Azure Standard Storage, które są regularnie dyskami HDD lub dyskami SSD w warstwie Standardowa. W przypadku większości obciążeń produkcyjnych i programistów należy Premium Storage. Dyski platformy Azure są instalowane jako *ReadWriteOnce,* więc są dostępne tylko dla jednego zasobnika. W przypadku woluminów magazynu, do których można uzyskiwać dostęp jednocześnie przez wiele zasobników, użyj Azure Files.
- [*Azure Files*](azure-files-csi.md), którego można użyć do instalacji w zasobnikach udziału SMB 3.0, którego kopię zapasową stanowi konto usługi Azure Storage. Dzięki Azure Files można udostępniać dane między wieloma węzłami i zasobnikami. Azure Files mogą używać dysków Azure Standard Storage z regularnymi dyskami HDD lub azure Premium Storage dyskami SSD o wysokiej wydajności.

> [!IMPORTANT]
> Począwszy od wersji 1.21 usługi Kubernetes, będzie ona domyślnie używać tylko sterowników CSI. Te sterowniki są przyszłością obsługi magazynu na kubernetes.
>
> *Sterowniki w drzewie* odnoszą się do bieżących sterowników magazynu, które są częścią podstawowego kodu Kubernetes, a nie nowych sterowników CSI, które są wtyczkami.

## <a name="limitations"></a>Ograniczenia

- Tę funkcję można ustawić tylko podczas tworzenia klastra.
- Minimalna wersja pomocnicza kubernetes obsługująca sterowniki CSI to 1.17.
- W wersji zapoznawczej domyślna klasa magazynu będzie nadal mieć tę samą klasę magazynu w [drzewie](concepts-storage.md#storage-classes). Gdy ta funkcja będzie ogólnie dostępna, domyślną klasą magazynu będzie klasa magazynu, a klasy magazynu w `managed-csi` drzewie zostaną usunięte.
- W pierwszej fazie wersji zapoznawczej obsługiwany jest tylko interfejs wiersza polecenia platformy Azure.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Rejestrowanie funkcji w `EnableAzureDiskFileCSIDriver` wersji zapoznawczej

Aby utworzyć klaster usługi AKS, który może używać sterowników CSI dla dysków platformy Azure i Azure Files, należy włączyć `EnableAzureDiskFileCSIDriver` flagę funkcji w subskrypcji.

Zarejestruj `EnableAzureDiskFileCSIDriver` flagę funkcji za pomocą [polecenia az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Wyświetlanie stanu Zarejestrowane trwa kilka *minut.* Sprawdź stan rejestracji za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy *zasobów Microsoft.ContainerService* za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster usługi AKS lub pulę węzłów, która może korzystać ze sterowników magazynu CSI, potrzebne jest najnowsze rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview.* Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* za pomocą [polecenia az extension add.][az-extension-add] Możesz też zainstalować wszystkie dostępne aktualizacje za pomocą [polecenia az extension update.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Tworzenie nowego klastra, który może używać sterowników magazynu CSI

Utwórz nowy klaster, który może używać sterowników magazynu CSI dla dysków platformy Azure i Azure Files za pomocą następujących poleceń interfejsu wiersza polecenia. Użyj `--aks-custom-headers` flagi , aby ustawić `EnableAzureDiskFileCSIDriver` funkcję.

Utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Utwórz klaster AKS z obsługą sterowników magazynu CSI:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Jeśli chcesz utworzyć klastry w sterownikach magazynu w drzewie zamiast sterowników magazynu CSI, możesz to zrobić, pomijając parametr `--aks-custom-headers` niestandardowy.


Sprawdź, ile woluminów opartych na dyskach platformy Azure można dołączyć do tego węzła, uruchamiając:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Następne kroki

- Aby użyć dysku CSI dla dysków platformy Azure, zobacz [Używanie dysków platformy Azure ze sterownikami CSI.](azure-disk-csi.md)
- Aby użyć dysku CSI na Azure Files, zobacz Używanie Azure Files [ze sterownikami CSI](azure-files-csi.md).
- Aby uzyskać więcej informacji o najlepszych rozwiązaniach dotyczących magazynu, zobacz [Najlepsze rozwiązania dotyczące][operator-best-practices-storage]magazynu i kopii zapasowych w Azure Kubernetes Service .

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