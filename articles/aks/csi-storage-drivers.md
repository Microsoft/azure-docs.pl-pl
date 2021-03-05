---
title: Włączanie sterowników interfejsu magazynu kontenerów (CSI) w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak włączyć sterowniki interfejsu magazynu kontenerów (CSI) dla dysków platformy Azure i Azure Files w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: fa40ab22f0c1ebf47bb490a50f782a848d1441e1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182115"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>Włącz sterowniki interfejsu magazynu kontenerów (CSI) dla dysków platformy Azure i Azure Files w usłudze Azure Kubernetes Service (wersja zapoznawcza)

Interfejs magazynu kontenerów (CSI) jest standardem do udostępniania dowolnych systemów blokowych i magazynów plików do obciążeń zwirtualizowanych w systemie Kubernetes. Przyjmując i korzystając z CSI, usługa Azure Kubernetes Service (AKS) może pisać, wdrażać i iterować wtyczki, aby ujawniać nowe lub ulepszać istniejące systemy magazynowania w Kubernetes bez konieczności dotykania podstawowego kodu Kubernetes i oczekiwania na jego cykle wydania.

Obsługa sterowników magazynu CSI w systemie AKS umożliwia natywne używanie:
- [*Dyski platformy Azure*](azure-disk-csi.md), których można użyć do utworzenia zasobu Kubernetes *Datadisk* . Dyski mogą korzystać z usługi Azure Premium Storage, obsługiwanej przez dysków SSD o wysokiej wydajności lub na platformie Azure w warstwie Standardowa, za pomocą regularnego HDD lub standardowego dysków SSD. W przypadku większości obciążeń produkcyjnych i programistycznych należy użyć Premium Storage. Dyski platformy Azure są instalowane jako *ReadWriteOnce*, więc są dostępne tylko dla jednego pod. W przypadku woluminów magazynu, do których można uzyskać dostęp jednocześnie przez wiele zasobników, użyj Azure Files.
- [*Azure Files*](azure-files-csi.md), którego można użyć do zainstalowania udziału SMB 3,0 obsługiwanego przez konto usługi Azure Storage w ramach platformy. Za pomocą Azure Files można udostępniać dane między wieloma węzłami i zasobnikami. Azure Files mogą korzystać z usługi Azure Standard Storage, która jest obsługiwana przez zwykłe HDD lub Premium Storage platformy Azure za pomocą dysków SSD o wysokiej wydajności.

> [!IMPORTANT]
> Począwszy od Kubernetes w wersji 1,21, Kubernetes będzie używać tylko sterowników CSI i domyślnie. Te sterowniki to przyszłość obsługi magazynu w programie Kubernetes.
>
> *Sterowniki w drzewie* odnoszą się do bieżących sterowników magazynu, które są częścią podstawowego kodu Kubernetes, a nowe sterowniki CSI, które są wtyczkami.

## <a name="limitations"></a>Ograniczenia

- Tę funkcję można ustawić tylko w czasie tworzenia klastra.
- Minimalna wersja pomocnicza Kubernetes, która obsługuje sterowniki CSI, to v 1.17.
- W trakcie okresu zapoznawczego domyślna Klasa magazynu nadal będzie taka [sama jak Klasa magazynu w drzewie](concepts-storage.md#storage-classes). Gdy ta funkcja jest ogólnie dostępna, domyślną klasą magazynu będzie Klasa magazynu `managed-csi` i klasy magazynu w drzewie zostaną usunięte.
- W pierwszej fazie wersji zapoznawczej jest obsługiwany tylko interfejs wiersza polecenia platformy Azure.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>Rejestrowanie `EnableAzureDiskFileCSIDriver` funkcji w wersji zapoznawczej

Aby utworzyć klaster AKS, który może używać sterowników CSI dla dysków platformy Azure i Azure Files, należy włączyć `EnableAzureDiskFileCSIDriver` flagę funkcji w ramach subskrypcji.

Zarejestruj `EnableAzureDiskFileCSIDriver` flagę funkcji za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

Wyświetlenie stanu *rejestracji* może potrwać kilka minut. Sprawdź stan rejestracji za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby utworzyć klaster AKS lub pulę węzłów, która może korzystać ze sterowników magazynu CSI, potrzebne jest najnowsze rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej AKS* . Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej AKS* , używając polecenie [AZ Extension Add][az-extension-add] . Lub zainstalować wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Utwórz nowy klaster, który może korzystać ze sterowników magazynu CSI

Utwórz nowy klaster, który może używać sterowników magazynu CSI dla dysków platformy Azure i Azure Files przy użyciu następujących poleceń interfejsu wiersza polecenia. Użyj `--aks-custom-headers` flagi, aby ustawić `EnableAzureDiskFileCSIDriver` funkcję.

Utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Utwórz klaster AKS z obsługą sterowników magazynu CSI:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Jeśli chcesz utworzyć klastry w ramach sterowników magazynu drzewa zamiast sterowników magazynu CSI, możesz to zrobić, pomijając `--aks-custom-headers` parametr niestandardowy.


Sprawdź, ile woluminów opartych na dyskach Azure można dołączyć do tego węzła, uruchamiając:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Następne kroki

- Aby korzystać z dysku CSI dla dysków platformy Azure, zobacz [Korzystanie z dysków platformy Azure z sterownikami CSI](azure-disk-csi.md).
- Aby użyć dysku CSI dla Azure Files, zobacz [używanie Azure Files z sterownikami CSI](azure-files-csi.md).
- Aby uzyskać więcej informacji o najlepszych rozwiązaniach dotyczących magazynu, zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze Azure Kubernetes Service][operator-best-practices-storage].

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register