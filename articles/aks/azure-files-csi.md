---
title: Używanie Container Storage Interface (CSI) dla Azure Files on Azure Kubernetes Service (AKS)
description: Dowiedz się, jak używać sterowników Container Storage Interface (CSI) dla Azure Files w klastrze Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: a83d2222862db6bc3e3ff86ba4074114c1a872e5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776163"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Używanie Azure Files Container Storage Interface (CSI) w Azure Kubernetes Service (AKS) (wersja zapoznawcza)

Sterownik Azure Files Container Storage Interface (CSI) to sterownik zgodny ze specyfikacją [CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)używany przez usługę Azure Kubernetes Service (AKS) do zarządzania cyklem życia Azure Files danych.

CsI to standard uwidniania dowolnych systemów magazynu blokowego i plików dla konteneryzowanych obciążeń na platformie Kubernetes. Dzięki wdrożeniu i użyciu csi, aks może teraz pisać, wdrażać i iterować wtyczki w celu uwidoczniania lub ulepszania istniejących systemów magazynowania na platformie Kubernetes bez konieczności dotykania podstawowego kodu Kubernetes i oczekiwania na cykle wydań.

Aby utworzyć klaster AKS z obsługą sterowników CSI, zobacz Enable CSI drivers for Azure disks and Azure Files on AKS (Włączanie sterowników CSI dla dysków platformy Azure i Azure Files [usłudze AKS).](csi-storage-drivers.md)

>[!NOTE]
> *Sterowniki w drzewie* odnoszą się do bieżących sterowników magazynu, które są częścią podstawowego kodu Kubernetes, a nie nowych sterowników CSI, które są wtyczkami.

## <a name="use-a-persistent-volume-with-azure-files"></a>Używanie trwałego woluminu z Azure Files

Trwały [wolumin (PV)](concepts-storage.md#persistent-volumes) reprezentuje część magazynu, która jest aprowizowana do użytku z zasobnikami kubernetes. Pv może być używany przez jeden lub wiele zasobników i może być dynamicznie lub statycznie aprowizowany. Jeśli wiele zasobników wymaga współbieżnego dostępu do tego samego woluminu magazynu, można użyć usługi Azure Files do nawiązania połączenia przy użyciu protokołu bloku komunikatów serwera [(SMB).][smb-overview] W tym artykule pokazano, jak dynamicznie tworzyć udział Azure Files do użytku przez wiele zasobników w klastrze usługi AKS. Aby uzyskać statyczną aprowizowanie, [zobacz Ręczne tworzenie woluminu](azure-files-volume.md)i używanie go z Azure Files udziału.

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz [Storage options for applications in AKS (Opcje magazynu dla aplikacji w UKS).][concepts-storage]

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Dynamiczne tworzenie Azure Files przy użyciu wbudowanych klas magazynu

Klasa magazynu służy do definiowania sposobu tworzenia Azure Files magazynu. Konto magazynu jest automatycznie [][node-resource-group] tworzone w grupie zasobów węzła do użycia z klasą magazynu do przechowywania Azure Files magazynów. Wybierz jedną z następujących [jednostki SKU nadmiarowości usługi Azure Storage][storage-skus] *dla jednostki skuName:*

* **Standard_LRS:** Standardowy magazyn lokalnie nadmiarowy
* **Standard_GRS:** standardowy magazyn geograficznie nadmiarowy
* **Standard_ZRS:** Standardowy magazyn strefowo nadmiarowy
* **Standard_RAGRS:** standardowy magazyn geograficznie nadmiarowy z dostępem do odczytu
* **Premium_LRS:** Magazyn lokalnie nadmiarowy Premium

> [!NOTE]
> Azure Files obsługuje usługę Azure Premium Storage. Minimalny udział plików Premium to 100 GB.

W przypadku używania sterowników CSI magazynu w u usługach AKS istnieją dwie dodatkowe wbudowane, które używają Azure Files `StorageClasses` magazynu CSI. Dodatkowe klasy magazynu CSI są tworzone wraz z klastrem wraz z domyślnymi klasami magazynu w drzewie.

- `azurefile-csi`: używa Azure Standard Storage do tworzenia Azure Files udziału.
- `azurefile-csi-premium`: używa usługi Azure Premium Storage do tworzenia Azure Files udziału.

Zasady odzyskiwania dla obu klas magazynu zapewniają, że bazowy udział Azure Files magazynu jest usuwany po usunięciu odpowiedniego udziału PV. Klasy magazynu konfigurują również udziały plików tak, aby można było je rozszerzać. Wystarczy edytować oświadczenie woluminu trwałego (CSV) z nowym rozmiarem.

Aby użyć tych klas magazynu, utwórz [PRZYCISK i](concepts-storage.md#persistent-volume-claims) odpowiedni zasobnik, który odwołuje się do nich i z nich korzysta. System PLIKÓW JEST używany do automatycznego aprowizować magazyn na podstawie klasy magazynu. Aby utworzyć udział Azure Files dla żądanej sku i rozmiaru, można użyć jednej ze wstępnie utworzonych klas magazynu lub zdefiniowanej przez użytkownika klasy magazynu. Podczas tworzenia definicji zasobnika jest określana wartość w celu zażądania odpowiedniego magazynu.

Za pomocą polecenia kubectl apply utwórz [przykładOWY `outfile` ](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) zasobnik i element , który drukuje bieżącą datę w [poleceniu kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Gdy zasobnik jest w stanie uruchomienia, możesz sprawdzić, czy udział plików jest poprawnie zainstalowany, uruchamiając następujące polecenie i weryfikując, czy dane wyjściowe zawierają polecenie `outfile` :

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Tworzenie niestandardowej klasy magazynu

Domyślne klasy magazynu są dopasowane do najbardziej typowych scenariuszy, ale nie wszystkich. W niektórych przypadkach możesz chcieć mieć własną klasę magazynu dostosowaną przy użyciu własnych parametrów. Na przykład użyj następującego manifestu, aby skonfigurować `mountOptions` udział plików.

Wartość domyślna dla *fileMode* i *dirMode* to *0777* dla zainstalowanych udziałów plików kubernetes. Możesz określić różne opcje instalacji w obiekcie klasy magazynu.

Utwórz plik o nazwie `azure-file-sc.yaml` i wklej następujący przykładowy manifest:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

Utwórz klasę magazynu za pomocą [polecenia kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Sterownik Azure Files CSI obsługuje tworzenie [migawek trwałych woluminów](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) i bazowych udziałów plików.

Utwórz [klasę migawki woluminu](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) za pomocą [polecenia kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Utwórz [migawkę woluminu](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) na podstawie instrukcji THE, która została utworzona dynamicznie [na początku tego samouczka:](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Sprawdź, czy migawka została utworzona poprawnie:

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>Zmienianie rozmiaru trwałego woluminu

Można zażądać większych woluminów dla WOLUMINU. Edytuj obiektTOW i określ większy rozmiar. Ta zmiana wyzwala rozszerzanie woluminu bazowego, który bazuje na woluminie PV.

> [!NOTE]
> Nowy pv nigdy nie jest tworzony w celu spełnienia roszczenia. Zamiast tego rozmiar istniejącego woluminu jest zmieniany.

W udatku AKS wbudowana klasa magazynu obsługuje już rozszerzanie, dlatego użyj funkcji JEJ utworzonej wcześniej `azurefile-csi` [z tą klasą magazynu](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). ŻĄDANIE ZAŻĄDAŁO udziału plików 100Gi. Możemy to potwierdzić, uruchamiając:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Rozwiń pole BRANŻY, zwiększając `spec.resources.requests.storage` pole :

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Sprawdź, czy zarówno w systemie PLIKÓW, jak i w zasobniku jest pokazywany nowy rozmiar:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>Udziały plików NFS
[Azure Files obsługuje teraz protokół NFS w wersji 4.1.](../storage/files/storage-files-how-to-create-nfs-shares.md) Obsługa systemu plików NFS 4.1 dla systemu Azure Files zapewnia w pełni zarządzany system plików NFS jako usługę zbudowaną na platformie magazynu rozproszonego o wysokiej dostępnej i wysoce trwałej odporności.

 Ta opcja jest zoptymalizowana pod kątem obciążeń dostępu losowego z aktualizacjami danych w miejscu i zapewnia pełną obsługę systemu plików POSIX. W tej sekcji pokazano, jak używać udziałów NFS ze sterownikiem CSI usługi Azure File w klastrze usługi AKS.

Upewnij się, że ograniczenia [i dostępność](../storage/files/storage-files-compare-protocols.md#limitations) [regionów są sprawdzane](../storage/files/storage-files-compare-protocols.md#regional-availability) w fazie wersji zapoznawczej.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Rejestrowanie funkcji w `AllowNfsFileShares` wersji zapoznawczej

Aby utworzyć udział plików, który korzysta z systemu plików NFS 4.1, należy włączyć `AllowNfsFileShares` flagę funkcji w subskrypcji.

Zarejestruj `AllowNfsFileShares` flagę funkcji za pomocą [polecenia az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Wyświetlanie stanu Zarejestrowane trwa kilka *minut.* Sprawdź stan rejestracji za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy *zasobów Microsoft.Storage* za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Tworzenie konta magazynu dla udziału plików NFS

[Utwórz `Premium_LRS` Konto magazynu platformy Azure z](../storage/files/storage-how-to-create-file-share.md) następującymi konfiguracjami do obsługi udziałów NFS:
- rodzaj konta: FileStorage
- Wymagany bezpieczny transfer (włącz tylko ruch HTTPS): false
- Wybierz sieć wirtualną węzłów agenta w ustawieniach Zapory i sieci wirtualne, więc możesz wolisz utworzyć konto magazynu w MC_ zasobów.

### <a name="create-nfs-file-share-storage-class"></a>Tworzenie klasy magazynu udziału plików NFS

Zapisz plik `nfs-sc.yaml` z manifestem poniżej, edytując odpowiednie symbole zastępcze.

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi-nfs
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

Po edytowaniu i zapisaniu pliku utwórz klasę magazynu za pomocą [polecenia kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Tworzenie wdrożenia przy użyciu udziału plików z kopią zapasową systemu plików NFS
Możesz wdrożyć przykładowy [zestaw stanowy,](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) który zapisuje znaczniki czasu w pliku, wdrażając następujące polecenie za `data.txt` pomocą polecenia [kubectl apply:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Zweryfikuj zawartość woluminu, uruchamiając:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Należy pamiętać, że ponieważ udział plików NFS znajduje się na koncie Premium, minimalny rozmiar udziału plików wynosi 100 GB. Jeśli utworzysz element CSV o małym rozmiarze magazynu, może wystąpić błąd "Nie można utworzyć udziału plików... size (5)...".


## <a name="windows-containers"></a>Kontenery systemu Windows

Sterownik Azure Files CSI obsługuje również węzły i kontenery systemu Windows. Jeśli chcesz używać kontenerów systemu Windows, postępuj zgodnie z samouczkiem [kontenerów systemu Windows,](windows-container-cli.md) aby dodać pulę węzłów systemu Windows.

Po utworzeniu puli węzłów systemu Windows użyj wbudowanych klas magazynu, takich jak `azurefile-csi` lub utwórz niestandardowe. Możesz wdrożyć przykładowy zestaw stanowy oparty na systemie [Windows,](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) który zapisuje znaczniki czasu w pliku, wdrażając następujące polecenie za pomocą `data.txt` polecenia [kubectl apply:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

Zweryfikuj zawartość woluminu, uruchamiając:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak używać sterowników CSI dla dysków platformy Azure, zobacz [Używanie dysków platformy Azure ze sterownikami CSI.](azure-disk-csi.md)
- Aby uzyskać więcej informacji o najlepszych rozwiązaniach dotyczących magazynu, zobacz [Najlepsze rozwiązania dotyczące][operator-best-practices-storage]magazynu i kopii zapasowych w Azure Kubernetes Service .


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md