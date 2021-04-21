---
title: Używanie Container Storage Interface (CSI) dla dysków platformy Azure na Azure Kubernetes Service (AKS)
description: Dowiedz się, jak używać sterowników Container Storage Interface (CSI) dla dysków platformy Azure w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: c3421b767f465a4a705bdeb4882fd261c5cf914f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776235"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Używanie sterowników usługi Azure Disk Container Storage Interface (CSI) w usłudze Azure Kubernetes Service (AKS) (wersja zapoznawcza)
Sterownik azure disk Container Storage Interface (CSI) to sterownik zgodny ze specyfikacją [CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)używany przez usługę Azure Kubernetes Service (AKS) do zarządzania cyklem życia dysków platformy Azure.

CSI to standard uwidniania dowolnych systemów magazynu blokowego i plików na konteneryzowanych obciążeniach na platformie Kubernetes. Dzięki wdrożeniu i użyciu csi usługi AKS mogą pisać, wdrażać i iterować wtyczki w celu uwidoczniania lub ulepszania istniejących systemów magazynowania na platformie Kubernetes bez konieczności dotykania podstawowego kodu Kubernetes i oczekiwania na cykle wydań.

Aby utworzyć klaster AKS z obsługą sterowników CSI, zobacz Enable CSI drivers for Azure disks and Azure Files on AKS (Włączanie sterowników CSI dla dysków platformy Azure i Azure Files [usłudze AKS).](csi-storage-drivers.md)

>[!NOTE]
> *Sterowniki w drzewie* odnoszą się do bieżących sterowników magazynu, które są częścią podstawowego kodu Kubernetes, a nie nowych sterowników CSI, które są wtyczkami.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Używanie trwałych woluminów CSI z dyskami platformy Azure

Wolumin [trwały](concepts-storage.md#persistent-volumes) (PV) reprezentuje część magazynu, która jest aprowizowana do użytku z zasobnikami kubernetes. Pv może być używana przez jeden lub wiele zasobników i może być dynamicznie lub statycznie aprowizowana. W tym artykule pokazano, jak dynamicznie tworzyć maszyny wirtualne z dyskami platformy Azure do użycia przez pojedynczy zasobnik w klastrze usługi AKS. Aby uzyskać statyczną aprowizowanie, zobacz Ręczne tworzenie woluminu i [używanie go z dyskami platformy Azure.](azure-disk-volume.md)

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz Opcje magazynu [dla aplikacji w u usługi AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Dynamiczne tworzenie dyskowych woluminów twardych platformy Azure przy użyciu wbudowanych klas magazynu

Klasa magazynu służy do definiowania dynamicznego tworzenia jednostki magazynu za pomocą trwałego woluminu. Aby uzyskać więcej informacji na temat klas magazynu Kubernetes, zobacz [Kubernetes storage classes (Klasy magazynu Kubernetes).][kubernetes-storage-classes] W przypadku używania sterowników CSI magazynu w usłudze AKS istnieją dwa dodatkowe wbudowane sterowniki magazynu `StorageClasses` CSI dysków platformy Azure. Dodatkowe klasy magazynu CSI są tworzone wraz z klastrem wraz z domyślnymi klasami magazynu w drzewie.

- `managed-csi`: używa usługi Azure SSD w warstwie Standardowa magazynu lokalnie nadmiarowego (LRS) do utworzenia dysku zarządzanego.
- `managed-csi-premium`: używa usługi Azure Premium LRS do utworzenia dysku zarządzanego.

Zasady odzyskiwania w obu klasach magazynu zapewniają, że podstawowy dysk platformy Azure zostanie usunięty po usunięciu odpowiedniego woluminu PV. Klasy magazynu konfigurują również możliwość rozszerzania tych plików. Wystarczy edytować oświadczenie woluminu trwałego (THE) przy użyciu nowego rozmiaru.

Aby korzystać z tych klas magazynu, należy utworzyć [klasę DOSTĘPNĄ i](concepts-storage.md#persistent-volume-claims) odpowiedni zasobnik, który odwołuje się do nich i z nich korzysta. System PLIKÓW JEST używany do automatycznego aprowizować magazyn na podstawie klasy magazynu. Za pomocą interfejsu ONE można użyć jednej ze wstępnie utworzonych klas magazynu lub zdefiniowanej przez użytkownika klasy magazynu, aby utworzyć dysk zarządzany przez platformę Azure dla żądanej wersji SKU i odpowiedniego rozmiaru. Podczas tworzenia definicji zasobnika jest określana wartość ZASZ, aby zażądać odpowiedniego magazynu.

Utwórz przykładowy zasobnik i odpowiednie elementy ZA POMOCĄ polecenia [kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Po uruchomieniu zasobnika utwórz nowy plik o nazwie `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Teraz możesz sprawdzić, czy dysk jest poprawnie zainstalowany, uruchamiając następujące polecenie i weryfikując, czy `test.txt` plik jest wyświetlany w danych wyjściowych:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Tworzenie niestandardowej klasy magazynu

Domyślne klasy magazynu są dopasowane do najbardziej typowych scenariuszy, ale nie wszystkich. W niektórych przypadkach możesz chcieć mieć własną klasę magazynu dostosowaną przy użyciu własnych parametrów. Na przykład mamy scenariusz, w którym możesz chcieć zmienić `volumeBindingMode` klasę.

Można użyć klasy, która gwarantuje, że nastąpi to `volumeBindingMode: Immediate` natychmiast po utworzeniu klasy NASTĘPNIE. W przypadkach, gdy pule węzłów są ograniczone topologią, na przykład przy użyciu stref dostępności, widoki PVs są powiązane lub aprowowane bez znajomości wymagań dotyczących planowania zasobnika (w tym przypadku w określonej strefie).

Aby rozwiązać ten scenariusz, można użyć funkcji , która opóźnia powiązanie i aprowizowanie pv, dopóki nie zostanie utworzony zasobnik, który korzysta `volumeBindingMode: WaitForFirstConsumer` z usługi BIND. W ten sposób plik PV będzie zgodny i aprowony w strefie dostępności (lub innej topologii) określonej przez ograniczenia planowania zasobnika. Domyślne klasy magazynu używają `volumeBindingMode: WaitForFirstConsumer` klasy .

Utwórz plik o nazwie `sc-azuredisk-csi-waitforfirstconsumer.yaml` i wklej następujący manifest.
Klasa magazynu jest taka sama jak nasza `managed-csi` klasa magazynu, ale z inną `volumeBindingMode` klasą.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

Utwórz klasę magazynu za pomocą [polecenia kubectl apply][kubectl-apply] i określ `sc-azuredisk-csi-waitforfirstconsumer.yaml` plik:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Migawki woluminów

Sterownik CSI dysku platformy Azure obsługuje [tworzenie migawek trwałych woluminów.](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) W ramach tej możliwości sterownik może  wykonywać [  ](../virtual-machines/disks-incremental-snapshots.md) pełne lub przyrostowe migawki w zależności od wartości ustawionej w parametrze `incremental` (domyślnie jest to prawda).

Aby uzyskać szczegółowe informacje na temat wszystkich parametrów, zobacz [parametry klasy migawki woluminu](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Tworzenie migawki woluminu

Aby uzyskać przykład tej możliwości, utwórz klasę migawki [woluminu za](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) pomocą [polecenia kubectl apply:][kubectl-apply]

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Teraz utwórzmy migawkę woluminu [z](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) poziomu systemu , który dynamicznie utworzyliśmy na początku [tego samouczka](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes): `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Sprawdź, czy migawka została utworzona poprawnie:

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Tworzenie nowej migawki WOLUMINU na podstawie migawki woluminu

Możesz utworzyć nową migawkę WOLUMINU opartą na migawce woluminu. Użyj migawki utworzonej w poprzednim kroku, a następnie utwórz nowy [rekord NASTĘPNIE i](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) nowy [zasobnik,](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) aby z niego korzystać.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Na koniec upewnijmy się, że jest to ten sam tworzona wcześniej powłoka IT, sprawdzając jej zawartość.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Zgodnie z oczekiwaniami nadal możemy zobaczyć utworzony wcześniej `test.txt` plik.

## <a name="clone-volumes"></a>Klonowanie woluminów

Sklonowany wolumin jest definiowany jako duplikat istniejącego woluminu Kubernetes. Aby uzyskać więcej informacji na temat klonowania woluminów na platformie Kubernetes, zobacz dokumentację koncepcyjną [klonowania woluminów](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning).

Sterownik CSI dla dysków platformy Azure obsługuje klonowanie woluminów. Aby to zademonstrować, [](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) utwórz [sklonowany wolumin](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) utworzonego wcześniej woluminu `azuredisk-pvc` i nowy [zasobnik, aby z niego korzystać.](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Teraz możemy sprawdzić zawartość sklonowanego woluminu, uruchamiając następujące polecenie i potwierdzając, że nadal widzimy `test.txt` utworzony plik.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Zmienianie rozmiaru woluminu trwałego

Zamiast tego można zażądać większego woluminu DLA WOLUMINU. Edytuj obiektTOW i określ większy rozmiar. Ta zmiana wyzwala rozszerzanie woluminu bazowego, który jest podstawą pv.

> [!NOTE]
> W celu spełnienia roszczenia nigdy nie jest tworzona nowa pv. Zamiast tego rozmiar istniejącego woluminu jest zmieniany.

W udatku AKS wbudowana klasa magazynu umożliwia już rozszerzanie, dlatego użyj utworzonego wcześniej `managed-csi` środowiska ZA POMOCĄ TEJ klasy [magazynu](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). Zażądano woluminu trwałego 10-Gi. Możemy to potwierdzić, uruchamiając:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Obecnie sterownik CSI dysku platformy Azure obsługuje tylko zmienianie rozmiaru woluminów PVC bez skojarzonych zasobników (i woluminu nie zainstalowanego w określonym węźle).

W związku z tym usuń utworzony wcześniej zasobnik:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Rozwińmy pole BRANŻY, zwiększając `spec.resources.requests.storage` pole :

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Upewnijmy się, że wolumin jest teraz większy:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> Model ITN nie będzie odzwierciedlał nowego rozmiaru, dopóki nie zostanie z nim ponownie skojarzony zasobnik.

Utwórzmy nowy zasobnik:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

Na koniec potwierdź rozmiar pliku NASTĘPNIE i wewnątrz zasobnika:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Dysk udostępniony

[Dyski udostępnione platformy Azure](../virtual-machines/disks-shared.md) to funkcja dysków zarządzanych platformy Azure, która umożliwia jednoczesne dołączanie dysku platformy Azure do węzłów agentów. Dołączanie dysku zarządzanego do wielu węzłów agenta umożliwia na przykład wdrażanie nowych lub migrowanie istniejących klastrowanych aplikacji na platformę Azure.

> [!IMPORTANT] 
> Obecnie sterownik CSI dysku platformy Azure obsługuje tylko nieprzetworzone urządzenie blokowe ( `volumeMode: Block` ). Aplikacje powinny zarządzać koordynacją i kontrolą zapisu, odczytów, blokad, pamięci podręcznych, instalacji i ogrodzenia na dysku udostępnionym, który jest udostępniany jako nieprzetworzone urządzenie blokowe.

Utwórzmy plik o nazwie przez skopiowanie następującego polecenia, które zawiera klasę magazynu dysku `shared-disk.yaml` udostępnionego i klasę CSV:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Utwórz klasę magazynu za pomocą [polecenia kubectl apply][kubectl-apply] i określ `shared-disk.yaml` plik:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Teraz utwórzmy plik o nazwie `deployment-shared.yml` , kopiując następujące polecenie:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Utwórz wdrożenie za pomocą [polecenia kubectl apply][kubectl-apply] i określ `deployment-shared.yml` plik:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Na koniec sprawdźmy urządzenie blokowe w zasobniku:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Kontenery systemu Windows

Sterownik CSI dysku platformy Azure obsługuje również węzły i kontenery systemu Windows. Jeśli chcesz używać kontenerów systemu Windows, postępuj zgodnie z samouczkiem [kontenerów systemu Windows,](windows-container-cli.md) aby dodać pulę węzłów systemu Windows.

Po użyciu puli węzłów systemu Windows można teraz używać wbudowanych klas magazynu, takich jak `managed-csi` . Możesz wdrożyć przykładowy zestaw stanowy oparty na systemie [Windows,](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) który zapisuje znaczniki czasu w pliku, wdrażając następujące polecenie za pomocą `data.txt` polecenia [kubectl apply:][kubectl-apply]

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Teraz możesz zweryfikować zawartość woluminu, uruchamiając:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak używać sterowników CSI na Azure Files, zobacz Używanie Azure Files [ze sterownikami CSI.](azure-files-csi.md)
- Aby uzyskać więcej informacji na temat najlepszych rozwiązań dotyczących magazynu, zobacz Najlepsze rozwiązania dotyczące magazynu i kopii [zapasowych][operator-best-practices-storage]w Azure Kubernetes Service .


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
