---
title: Użyj sterowników interfejsu magazynu kontenerów (CSI) dla Azure Files w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak używać sterowników interfejsu magazynu kontenerów (CSI) do Azure Files w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 018275b6db4c2d2d1059f35077f74a6f45ec3ba9
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422082"
---
# <a name="use-the-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Korzystanie ze sterowników interfejsu magazynu kontenerów Azure Files (CSI) w usłudze Azure Kubernetes Service (AKS) (wersja zapoznawcza)
Sterownik Azure Files CSI jest zgodny ze [specyfikacją CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md) sterownik używany przez AKS do zarządzania cyklem życia Azure Files udziałów. 

Interfejs magazynu kontenerów (CSI) jest standardem do udostępniania dowolnych systemów blokowych i magazynów plików do obciążeń zwirtualizowanych w systemie Kubernetes. Przyjmując i korzystając z CSI, usługa Azure Kubernetes Service (AKS) może teraz zapisywać, wdrażać i iterować wtyczki udostępniające nowe lub ulepszające istniejące systemy magazynowania w Kubernetes bez konieczności dotykania podstawowego kodu Kubernetes i oczekiwania na jego cykle wydania.

Aby utworzyć klaster AKS z obsługą sterownika CSI, zobacz [Włączanie sterowników CSI dla dysków platformy Azure i Azure Files na AKS](csi-storage-drivers.md).

>[!NOTE]
> *"Sterowniki w drzewie"* oznaczają bieżące sterowniki magazynu, które są częścią podstawowego kodu Kubernetes a nowe sterowniki CSI, które są wtyczkami.

## <a name="use-a-persistent-volume-pv-with-azure-files"></a>Użyj woluminu trwałego (PV) z Azure Files

[Wolumin trwały](concepts-storage.md#persistent-volumes) reprezentuje część magazynu, która jest obsługiwana do użycia z Kubernetesą. Wolumin trwały może być używany przez jeden lub wiele zasobników i może być dynamicznie lub statycznie inicjowany. Jeśli wiele z nich potrzebuje współbieżnego dostępu do tego samego woluminu magazynu, można użyć Azure Files, aby nawiązać połączenie przy użyciu [protokołu SMB (Server Message Block)][smb-overview]. W tym artykule pokazano, jak dynamicznie utworzyć udział Azure Files do użytku przez wiele zasobników w klastrze usługi Azure Kubernetes Service (AKS). Aby uzyskać obsługę statyczną, zobacz [Ręczne tworzenie i używanie woluminu z udziałem Azure Files](azure-files-volume.md).

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz [Opcje magazynu dla aplikacji w AKS][concepts-storage].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-using-the-built-in-storage-classes"></a>Dynamicznie Twórz Azure Files PVs przy użyciu wbudowanych klas magazynu
Klasa magazynu służy do definiowania sposobu tworzenia udziału plików platformy Azure. Konto magazynu jest tworzone automatycznie w [grupie zasobów węzła][node-resource-group] do użytku z klasą magazynu w celu przechowywania udziałów plików platformy Azure. Wybierz następującą [nadmiarowość usługi Azure Storage][storage-skus] dla *skuName*:

* Magazyn lokalnie nadmiarowy *Standard_LRS*
* Magazyn Geograficznie nadmiarowy *Standard_GRS*
* Magazyn strefowo nadmiarowy *Standard_ZRS* strefy standardowej
* *Standard_RAGRS* — standardowy magazyn Geograficznie nadmiarowy do odczytu
* Magazyn lokalnie nadmiarowy *Premium_LRS* -Premium

> [!NOTE]
> Azure Files obsługuje magazyn Premium Storage, minimalny udział plików w warstwie Premium to 100 GB.

W przypadku korzystania z sterowników CSI magazynu na platformie AKS dostępne są 2 dodatkowe wbudowane `StorageClasses` , które wykorzystują **sterowniki magazynu Azure Files CSI**. Dodatkowe klasy magazynów CSI są tworzone razem z klastrem obok domyślnych klas magazynów w drzewie.

- `azurefile-csi` — Używa usługi Azure Standard Storage, aby utworzyć udział plików platformy Azure. 
- `azurefile-csi-premium` — Używa usługi Azure Premium Storage do utworzenia udziału plików platformy Azure. 

Zasady odzyskiwania dla obu klas magazynu zapewniają, że podstawowy udział plików platformy Azure zostanie usunięty po usunięciu odpowiedniego woluminu trwałego. Klasy magazynu umożliwiają również konfigurację udziałów plików, które mają być rozwijane. wystarczy zmienić wartość w polu trwały udział woluminu o nowym rozmiarze.

Aby korzystać z tych klas magazynu, należy utworzyć [trwałego żądania zbiorczego (PVC)](concepts-storage.md#persistent-volume-claims) i odpowiednich, w odniesieniu do nich. W celu automatycznego aprowizacji magazynu na podstawie klasy magazynu jest używana wartość trwałego żądania woluminu. Obwód PVC może użyć jednej ze wstępnie utworzonych klas magazynu lub klasy magazynu zdefiniowanej przez użytkownika, aby utworzyć Azure Files udział dla żądanej jednostki SKU i rozmiaru. W przypadku tworzenia definicji na podstawie trwałego żądania woluminu jest określona w celu zażądanie żądanego magazynu.

Utwórz [przykładowe wystąpienie trwałego woluminu i na stronie, które drukuje bieżącą datę `outfile` do](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) , przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

Gdy pod stanem działania jest uruchomiona, można sprawdzić, czy udział plików jest prawidłowo zainstalowany, uruchamiając poniższe polecenie i sprawdzając, czy dane wyjściowe zawierają `outfile` : 

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>Tworzenie niestandardowej klasy magazynu

Domyślne klasy magazynów odpowiadają najpopularniejszym scenariuszom, ale nie wszystkim. W niektórych przypadkach można chcieć mieć własną klasę magazynu dostosowaną z własnymi parametrami. Na przykład użyj poniższego manifestu, aby skonfigurować `mountOptions` udział plików.

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

Sterownik CSI usługi Azure Files obsługuje tworzenie [migawek woluminów trwałych](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) i źródłowych udziałów plików. 

Utwórz [klasę migawek woluminów](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml) przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

Utwórz [migawkę woluminu](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) ze obwodu PVC, który został [dynamicznie utworzony na początku tego samouczka](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes) `pvc-azurefile` .


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

## <a name="resize-a-persistent-volume-pv"></a>Zmień rozmiar woluminu trwałego (PV)

Możesz zażądać większego woluminu dla obwodu PVC. Edytuj obiekt PVC i określ większy rozmiar. Ta zmiana wyzwala Rozszerzanie woluminu bazowego, który wykonuje kopie zapasowe PersistentVolume. 

> [!NOTE] 
> Nowy PersistentVolume nigdy nie jest tworzony w celu spełnienia tego żądania. Zamiast tego zmieniany jest rozmiar istniejącego woluminu.

W programie AKS wbudowana `azurefile-csi` Klasa magazynu obsługuje już rozszerzanie, dlatego korzystając z tego samego połączenia [PVC utworzonego wcześniej z tą klasą magazynu](#dynamically-create-azure-files-pvs-using-the-built-in-storage-classes). Obwód PVC zażądał udziału plików 100Gi, możemy potwierdzić, że uruchamiając polecenie:

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

Sprawdź, czy zarówno obwód PVC, jak i system plików w tym obszarze pokazują nowy rozmiar:

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```

## <a name="windows-containers"></a>Kontenery systemu Windows

Sterownik usługi Azure Files CSI obsługuje również węzły i kontenery systemu Windows. Jeśli chcesz użyć kontenerów systemu Windows, Skorzystaj z [samouczka kontenery](windows-container-cli.md) systemu Windows, aby dodać pulę węzłów systemu Windows.

Gdy masz pulę węzłów systemu Windows, Skorzystaj z wbudowanych klas magazynu, takich jak `azurefile-csi` lub Utwórz niestandardowe. Można wdrożyć przykładowy [zestaw Stanów opartych na systemie Windows](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , który zapisuje sygnatury czasowe do pliku `data.txt` , wdrażając poniższe polecenie przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
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

- Aby dowiedzieć się, jak używać sterownika CSI dla dysków platformy Azure, zobacz [Korzystanie z dysków platformy Azure z sterownikami CSI](azure-disk-csi.md).
- Aby uzyskać więcej informacji o najlepszych rozwiązaniach dotyczących magazynu, zobacz [najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w usłudze Azure Kubernetes Service (AKS).][operator-best-practices-storage]


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
[premium-storage]: ../virtual-machines/windows/disks-types.md
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
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md