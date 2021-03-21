---
title: Użyj sterowników interfejsu magazynu kontenerów (CSI) dla Azure Files w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak używać sterowników interfejsu magazynu kontenerów (CSI) do Azure Files w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 93f7f7a3c59beca362145ac16f7cf727df773f81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174065"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Korzystanie ze sterowników interfejsu magazynu kontenera Azure Files (CSI) w usłudze Azure Kubernetes Service (AKS) (wersja zapoznawcza)

Sterownik Azure Files (CSI) jest sterownikiem zgodnym ze [specyfikacją CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)używanym przez usługę Azure Kubernetes Service (AKS) do zarządzania cyklem życia udziałów Azure Files.

CSI jest standardem do udostępniania dowolnych systemów blokowych i magazynów plików do obciążeń zwirtualizowanych w Kubernetes. Przyjmując i korzystając z CSI, AKS teraz może pisać, wdrażać i iterować wtyczki, aby ujawniać nowe lub ulepszać istniejące systemy magazynowania w Kubernetes bez konieczności dotykania podstawowego kodu Kubernetes i oczekiwania na jego cykle wydania.

Aby utworzyć klaster AKS z obsługą sterownika CSI, zobacz [Włączanie sterowników CSI dla dysków platformy Azure i Azure Files na AKS](csi-storage-drivers.md).

>[!NOTE]
> *Sterowniki w drzewie* odnoszą się do bieżących sterowników magazynu, które są częścią podstawowego kodu Kubernetes, a nowe sterowniki CSI, które są wtyczkami.

## <a name="use-a-persistent-volume-with-azure-files"></a>Użyj woluminu trwałego z Azure Files

[Wolumin trwały (PV)](concepts-storage.md#persistent-volumes) reprezentuje część magazynu, która jest obsługiwana do użycia z Kubernetesem. Funkcja PV może być używana przez jeden lub wiele zasobników i może być dynamicznie lub statycznie obsługiwana. W przypadku konieczności jednoczesnego dostępu do tego samego woluminu magazynu przez wiele zasobów, można użyć Azure Files, aby nawiązać połączenie przy użyciu [protokołu SMB (Server Message Block)][smb-overview]. W tym artykule pokazano, jak dynamicznie utworzyć udział Azure Files do użytku przez wiele zasobników w klastrze AKS. Aby uzyskać obsługę statyczną, zobacz [Ręczne tworzenie i używanie woluminu z udziałem Azure Files](azure-files-volume.md).

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz [Opcje magazynu dla aplikacji w AKS][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>Dynamicznie Twórz Azure Files PVs przy użyciu wbudowanych klas magazynu

Klasa magazynu służy do definiowania sposobu tworzenia udziału Azure Files. Konto magazynu jest tworzone automatycznie w [grupie zasobów węzła][node-resource-group] do użytku z klasą magazynu w celu przechowywania udziałów Azure Files. Wybierz jedną z następujących [jednostek SKU nadmiarowości usługi Azure Storage][storage-skus] dla *skuName*:

* **Standard_LRS**: standardowy magazyn lokalnie nadmiarowy
* **Standard_GRS**: standardowy magazyn Geograficznie nadmiarowy
* **Standard_ZRS**: Strefa standardowa — magazyn nadmiarowy
* **Standard_RAGRS**: standardowy magazyn Geograficznie nadmiarowy do odczytu
* **Premium_LRS**: Magazyn lokalnie nadmiarowy w warstwie Premium

> [!NOTE]
> Azure Files obsługuje platformę Azure Premium Storage. Minimalny udział plików w warstwie Premium to 100 GB.

W przypadku korzystania z sterowników CSI magazynu w systemie AKS dostępne są dwa dodatkowe wbudowane, w `StorageClasses` których są używane sterowniki magazynu Azure Files CSI. Dodatkowe klasy magazynów CSI są tworzone razem z klastrem obok domyślnych klas magazynów w drzewie.

- `azurefile-csi`: Program korzysta z usługi Azure Standard Storage w celu utworzenia udziału Azure Files.
- `azurefile-csi-premium`: Program korzysta z usługi Azure Premium Storage do tworzenia udziału Azure Files.

Zasady odzyskiwania dla obu klas magazynu zapewniają, że podstawowy udział Azure Files zostanie usunięty po usunięciu odpowiednich danych PV. Klasy magazynu umożliwiają również konfigurację udziałów plików, które mają być rozwijane. wystarczy zmienić wartość trwałego żądania woluminu (PVC) o nowy rozmiar.

Aby użyć tych klas magazynu, należy utworzyć [obwód PVC](concepts-storage.md#persistent-volume-claims) i odpowiednie, pod tym względem, i korzystać z nich. Obwód PVC służy do automatycznej aprowizacji magazynu na podstawie klasy magazynu. Obwód PVC może użyć jednej ze wstępnie utworzonych klas magazynu lub klasy magazynu zdefiniowanej przez użytkownika, aby utworzyć Azure Files udział dla żądanej jednostki SKU i rozmiaru. Podczas tworzenia definicji pod, obwód PVC jest określany w celu zażądania odpowiedniej pamięci masowej.

Utwórz [przykładowy obwód PVC i pod, który drukuje bieżącą datę do `outfile` ](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Gdy pod stanem działania jest uruchomiona, można sprawdzić, czy udział plików jest prawidłowo zainstalowany, uruchamiając następujące polecenie i sprawdzając, czy dane wyjściowe zawierają `outfile` :

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Tworzenie niestandardowej klasy magazynu

Domyślne klasy magazynu odpowiadają najpopularniejszym scenariuszom, ale nie wszystkim. W niektórych przypadkach można chcieć mieć własną klasę magazynu dostosowaną z własnymi parametrami. Na przykład użyj poniższego manifestu, aby skonfigurować `mountOptions` udział plików.

Domyślna wartość *parametru FileMode* i *dirMode* to *0777* dla Kubernetes zainstalowanych udziałów plików. Można określić różne opcje instalacji w obiekcie klasy magazynu.

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

Utwórz klasę magazynu za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Sterownik Azure Files CSI obsługuje tworzenie [migawek woluminów trwałych](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) i źródłowych udziałów plików.

Utwórz [klasę migawek woluminów](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Utwórz [migawkę woluminu](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) ze obwodu PVC, który został [dynamicznie utworzony na początku tego samouczka](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes) `pvc-azurefile` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

Sprawdź, czy migawka została utworzona prawidłowo:

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

## <a name="resize-a-persistent-volume"></a>Zmień rozmiar woluminu trwałego

Możesz zażądać większego woluminu dla obwodu PVC. Edytuj obiekt PVC i określ większy rozmiar. Ta zmiana wyzwala rozwinięcie woluminu bazowego, który wykonuje kopię zapasową.

> [!NOTE]
> Nowe WB nigdy nie jest tworzone w celu spełnienia tego żądania. Zamiast tego zmieniany jest rozmiar istniejącego woluminu.

W AKS, wbudowana `azurefile-csi` Klasa magazynu obsługuje już rozszerzanie, dlatego użyj [obwodu PVC utworzonego wcześniej z tą klasą magazynu](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes). Obwód PVC zażądał udziału plików 100Gi. Możemy potwierdzić, że działa:

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

Rozwiń obwód PVC przez zwiększenie `spec.resources.requests.storage` pola:

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

Upewnij się, że zarówno obwód PVC, jak i system plików w tym obszarze pokazują nowy rozmiar:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>Udziały plików NFS
[Azure Files teraz obsługuje protokół NFS v 4.1](../storage/files/storage-files-how-to-create-nfs-shares.md). Obsługa systemu plików NFS 4,1 dla Azure Files zapewnia w pełni zarządzany system plików NFS jako usługę utworzoną na wysokiej dostępności i wysoce trwałej platformie rozproszonej pamięci masowej.

 Ta opcja jest zoptymalizowana pod kątem obciążeń dostępu losowego za pomocą aktualizacji danych w miejscu i zapewnia pełną obsługę systemu plików POSIX. W tej sekcji pokazano, jak używać udziałów NFS z sterownikiem usługi Azure File CSI w klastrze AKS.

Pamiętaj o sprawdzeniu [ograniczeń](../storage/files/storage-files-compare-protocols.md#limitations) i [dostępności regionów](../storage/files/storage-files-compare-protocols.md#regional-availability) w fazie zapoznawczej.

### <a name="register-the-allownfsfileshares-preview-feature"></a>Rejestrowanie `AllowNfsFileShares` funkcji w wersji zapoznawczej

Aby utworzyć udział plików, który wykorzystuje system plików NFS 4,1, należy włączyć `AllowNfsFileShares` flagę funkcji w subskrypcji.

Zarejestruj `AllowNfsFileShares` flagę funkcji za pomocą polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

Wyświetlenie stanu *rejestracji* może potrwać kilka minut. Sprawdź stan rejestracji za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. Storage* przy użyciu polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>Tworzenie konta magazynu dla udziału plików NFS

[Utwórz `Premium_LRS` Konto usługi Azure Storage](../storage/files/storage-how-to-create-file-share.md) z następującymi konfiguracjami do obsługi udziałów NFS:
- rodzaj konta: FileStorage
- wymagany bezpieczny transfer (Włącz tylko ruch HTTPS): FAŁSZ
- w obszarze zapory i sieci wirtualne wybierz sieć wirtualną węzłów agenta, aby utworzyć konto magazynu w MC_ grupie zasobów.

### <a name="create-nfs-file-share-storage-class"></a>Utwórz klasę magazynu udziału plików NFS

Zapisz `nfs-sc.yaml` plik z manifestem poniżej, edytując odpowiednie symbole zastępcze.

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

Po edytowaniu i zapisaniu pliku Utwórz klasę magazynu za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>Tworzenie wdrożenia z udziałem plików z kopią zapasową NFS
Można wdrożyć przykładowy [zestaw stanowy](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) , który zapisuje sygnatury czasowe do pliku `data.txt` , wdrażając następujące polecenie za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

Sprawdź poprawność zawartości woluminu, uruchamiając następujące elementy:

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

>[!NOTE]
> Należy pamiętać, że ponieważ udział plików NFS jest na koncie Premium, minimalny rozmiar udziału plików wynosi 100 GB. W przypadku utworzenia obwodu PVC o małym rozmiarze magazynu może wystąpić błąd "nie można utworzyć udziału plików... rozmiar (5)... ".


## <a name="windows-containers"></a>Kontenery systemu Windows

Sterownik Azure Files CSI obsługuje również węzły i kontenery systemu Windows. Jeśli chcesz użyć kontenerów systemu Windows, postępuj zgodnie z [samouczkiem kontenery systemu Windows](windows-container-cli.md) , aby dodać pulę węzłów systemu Windows.

Po utworzeniu puli węzłów systemu Windows należy użyć wbudowanych klas magazynu, takich jak `azurefile-csi` lub utworzyć niestandardowe. Można wdrożyć przykładowy [zestaw stanowy oparty na systemie Windows](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , który zapisuje sygnatury czasowe do pliku `data.txt` , wdrażając następujące polecenie za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

Sprawdź poprawność zawartości woluminu, uruchamiając następujące elementy:

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak używać sterowników CSI dla dysków platformy Azure, zobacz [Korzystanie z dysków platformy Azure z sterownikami CSI](azure-disk-csi.md).
- Aby uzyskać więcej informacji o najlepszych rozwiązaniach dotyczących magazynu, zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze Azure Kubernetes Service][operator-best-practices-storage].


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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md