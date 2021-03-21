---
title: Używanie sterowników interfejsu magazynu kontenerów (CSI) dla dysków platformy Azure w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak używać sterowników interfejsu magazynu kontenerów (CSI) dla dysków platformy Azure w klastrze usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 2b4079b6d4eb39b65a7a60cd4d149c7748ab39ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178885"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>Korzystanie ze sterowników interfejsu magazynu kontenera platformy Azure (CSI) w usłudze Azure Kubernetes Service (AKS) (wersja zapoznawcza)
Sterownik Azure Disk Container Storage Interface (CSI) jest sterownikiem zgodnym ze [specyfikacją CSI](https://github.com/container-storage-interface/spec/blob/master/spec.md)używanym przez usługę Azure Kubernetes Service (AKS) do zarządzania cyklem życia dysków platformy Azure.

CSI jest standardem do udostępniania dowolnych systemów blokowych i magazynów plików do obciążeń zwirtualizowanych w Kubernetes. Przyjmując i korzystając z CSI, AKS może napisać, wdrożyć i iterować wtyczki, aby uwidocznić nowe lub ulepszyć istniejące systemy magazynowania w Kubernetes, bez konieczności dotykania podstawowego kodu Kubernetes i oczekiwania na jego cykle wydania.

Aby utworzyć klaster AKS z obsługą sterownika CSI, zobacz [Włączanie sterowników CSI dla dysków platformy Azure i Azure Files na AKS](csi-storage-drivers.md).

>[!NOTE]
> *Sterowniki w drzewie* odnoszą się do bieżących sterowników magazynu, które są częścią podstawowego kodu Kubernetes, a nowe sterowniki CSI, które są wtyczkami.

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>Używanie woluminów trwałych CSI z dyskami platformy Azure

[Wolumin trwały](concepts-storage.md#persistent-volumes) (PV) reprezentuje część magazynu, która jest obsługiwana do użycia z Kubernetesem. Funkcja PV może być używana przez jeden lub wiele zasobników i może być dynamicznie lub statycznie obsługiwana. W tym artykule opisano sposób dynamicznego tworzenia PVs za pomocą dysków platformy Azure do użycia przez jeden element w klastrze AKS. Aby uzyskać obsługę statyczną, zobacz [Ręczne tworzenie woluminu i używanie go z dyskami platformy Azure](azure-disk-volume.md).

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz [Opcje magazynu dla aplikacji w AKS][concepts-storage].

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>Dynamiczne tworzenie PVs dysku platformy Azure przy użyciu wbudowanych klas magazynu

Klasa magazynu służy do definiowania sposobu, w jaki jednostka magazynowa jest tworzona dynamicznie z woluminem trwałym. Aby uzyskać więcej informacji na temat klas magazynu Kubernetes, zobacz [Kubernetes Storage Classes][kubernetes-storage-classes]. W przypadku korzystania z sterowników CSI magazynu w systemie AKS dostępne są dwa dodatkowe wbudowane, `StorageClasses` które używają sterowników magazynu CSI dysku platformy Azure. Dodatkowe klasy magazynów CSI są tworzone razem z klastrem obok domyślnych klas magazynów w drzewie.

- `managed-csi`: Program używa platformy Azure SSD w warstwie Standardowa Magazyn lokalnie nadmiarowy (LRS) w celu utworzenia dysku zarządzanego.
- `managed-csi-premium`: Używa usługi Azure Premium LRS do utworzenia dysku zarządzanego.

Zasady odzyskiwania w obu klasach magazynu zapewniają, że podstawowy dysk platformy Azure zostanie usunięty po usunięciu odpowiednich danych PV. Klasy magazynu umożliwiają również skonfigurowanie PVs do rozwijania. Wystarczy edytować trwałego żądania woluminu (PVC) z nowym rozmiarem.

Aby korzystać z tych klas magazynu, należy utworzyć [obwód PVC](concepts-storage.md#persistent-volume-claims) i odpowiednie, na przykład, które odwołują się do nich i używa. Obwód PVC służy do automatycznej aprowizacji magazynu na podstawie klasy magazynu. Obwód PVC może użyć jednej z wstępnie utworzonych klas magazynu lub klasy magazynu zdefiniowanej przez użytkownika w celu utworzenia dysku zarządzanego na platformie Azure dla żądanej jednostki SKU i rozmiaru. Podczas tworzenia definicji pod, obwód PVC jest określany w celu zażądania odpowiedniej pamięci masowej.

Utwórz przykład pod i odpowiedniego obwodu PVC przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

Gdy element jest w stanie uruchomionym, Utwórz nowy plik o nazwie `test.txt` .

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

Teraz można sprawdzić, czy dysk jest prawidłowo zainstalowany, uruchamiając następujące polecenie i sprawdzając, czy plik jest wyświetlany `test.txt` w danych wyjściowych:

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>Tworzenie niestandardowej klasy magazynu

Domyślne klasy magazynu odpowiadają najpopularniejszym scenariuszom, ale nie wszystkim. W niektórych przypadkach można chcieć mieć własną klasę magazynu dostosowaną z własnymi parametrami. Na przykład mamy scenariusz, w którym warto zmienić `volumeBindingMode` klasę.

Domyślne klasy magazynu używają `volumeBindingMode: Immediate` klasy, która gwarantuje, że występuje bezpośrednio po utworzeniu obwodu PVC. W przypadkach, gdy pule węzłów są ograniczone topologii, na przykład za pomocą stref dostępności, PVs byłoby ograniczone lub obsługiwane bez znajomości wymagań związanych z planowaniem na tym komputerze (w tym przypadku w przypadku określonej strefy).

Aby rozwiązać ten scenariusz, można użyć `volumeBindingMode: WaitForFirstConsumer` , który opóźnia powiązanie i Inicjowanie obsługi PV do momentu utworzenia elementu, który używa tego obwodu PVC. W ten sposób PV będzie zgodna i będzie obsługiwana w strefie dostępności (lub innej topologii), która jest określona przez ograniczenia planowania na podstawie.

Utwórz plik o nazwie `sc-azuredisk-csi-waitforfirstconsumer.yaml` i wklej następujący manifest.
Klasa magazynu jest taka sama jak `managed-csi` Klasa magazynu, ale z inną `volumeBindingMode` klasą.

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

Utwórz klasę magazynu za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ `sc-azuredisk-csi-waitforfirstconsumer.yaml` plik:

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>Migawki woluminów

Sterownik CSI dysku platformy Azure obsługuje tworzenie [migawek woluminów trwałych](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html). W ramach tej możliwości sterownik może wykonać *kompletne* lub [ *przyrostowe* migawki](../virtual-machines/disks-incremental-snapshots.md) w zależności od wartości ustawionej w `incremental` parametrze (domyślnie jest to prawdziwe).

Aby uzyskać szczegółowe informacje na temat wszystkich parametrów, zobacz [Volume Snapshot Class Parameters](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass).

### <a name="create-a-volume-snapshot"></a>Utwórz migawkę woluminu

Aby zapoznać się z przykładem tej możliwości, Utwórz [klasę migawek woluminów](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml) przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

Teraz Utwórzmy [migawkę woluminu](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) ze obwodu PVC, który został [dynamicznie utworzony na początku tego samouczka](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `pvc-azuredisk` .


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

Sprawdź, czy migawka została utworzona prawidłowo:

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

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>Tworzenie nowego obwodu PVC na podstawie migawki woluminu

Nowy obwód PVC można utworzyć na podstawie migawki woluminu. Użyj migawki utworzonej w poprzednim kroku i Utwórz [Nowy obwód PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) i [Nowy pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) , aby go używać.

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

Na koniec upewnij się, że jest to ten sam obwód PVC utworzony przed sprawdzeniem zawartości.

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

Zgodnie z oczekiwaniami nadal możemy zobaczyć poprzednio utworzony `test.txt` plik.

## <a name="clone-volumes"></a>Klonowanie woluminów

Sklonowany wolumin jest zdefiniowany jako duplikat istniejącego woluminu Kubernetes. Aby uzyskać więcej informacji na temat klonowania woluminów w Kubernetes, zapoznaj się z dokumentacją dotyczącą [klonowania woluminów](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning).

Sterownik CSI dla dysków platformy Azure obsługuje klonowanie woluminów. Aby zademonstrować, należy utworzyć [sklonowany wolumin](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) [poprzednio utworzonego](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes) `azuredisk-pvc` i [nowego pod kątem korzystania z niego](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml).


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

Teraz można sprawdzić zawartość sklonowanego woluminu, uruchamiając następujące polecenie i potwierdzając, że nadal widzimy `test.txt` utworzony plik.

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>Zmień rozmiar woluminu trwałego

Zamiast tego możesz zażądać większego woluminu dla obwodu PVC. Edytuj obiekt PVC i określ większy rozmiar. Ta zmiana wyzwala rozwinięcie woluminu bazowego, który wykonuje kopię zapasową.

> [!NOTE]
> Nowe WB nigdy nie jest tworzone w celu spełnienia tego żądania. Zamiast tego zmieniany jest rozmiar istniejącego woluminu.

W AKS, wbudowana `managed-csi` Klasa magazynu już zezwala na rozszerzanie, dlatego użyj [obwodu PVC utworzonego wcześniej z tą klasą magazynu](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes). Obwód PVC zażądał woluminu trwałego 10 gi. Możemy potwierdzić, że działa:

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> Obecnie sterownik CSI dysku platformy Azure obsługuje tylko zmienianie rozmiarów obwodów PVC bez skojarzonych z nimi (i woluminy nie są zainstalowane do określonego węzła).

W związku z tym usuniemy utworzony wcześniej:

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

Rozwińmy obwód PVC przez zwiększenie `spec.resources.requests.storage` pola:

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

Sprawdźmy, czy wolumin jest teraz większy:

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE]
> Obwód PVC nie będzie odzwierciedlać nowego rozmiaru, dopóki nie zostanie on ponownie przypisany do.

Utwórzmy nowy pod:

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

A wreszcie Potwierdź rozmiar obwodu PVC i wewnątrz:
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>Dysk udostępniony

[Azure Shared disks](../virtual-machines/disks-shared.md) to funkcja Azure Managed disks, która umożliwia równoczesne dołączanie dysku platformy Azure do węzłów agentów. Dołączanie dysku zarządzanego do wielu węzłów agenta pozwala na przykład wdrożyć nowe lub migrować istniejące aplikacje klastrowane na platformę Azure.

> [!IMPORTANT] 
> Obecnie tylko pierwotne urządzenie blokowe ( `volumeMode: Block` ) jest obsługiwane przez sterownik CSI dysku platformy Azure. Aplikacje powinny zarządzać koordynacji i kontroli zapisu, odczytów, blokad, pamięci podręcznych, instalacji i ogrodzenia na dysku udostępnionym, który jest udostępniany jako pierwotne urządzenie blokowe.

Utwórzmy plik o nazwie, `shared-disk.yaml` kopiując następujące polecenie, które zawiera klasę magazynu udostępnionego dysku i obwód PVC:

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

Utwórz klasę magazynu za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ `shared-disk.yaml` plik:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Teraz Utwórzmy plik o nazwie `deployment-shared.yml` , kopiując następujące polecenie:

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

Utwórz wdrożenie przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ `deployment-shared.yml` plik:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Na koniec sprawdźmy, czy urządzenie blokowe znajduje się w obrębie:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp sh
/ # dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out/s
```

## <a name="windows-containers"></a>Kontenery systemu Windows

Sterownik CSI dysku platformy Azure obsługuje również węzły i kontenery systemu Windows. Jeśli chcesz użyć kontenerów systemu Windows, postępuj zgodnie z [samouczkiem kontenery systemu Windows](windows-container-cli.md) , aby dodać pulę węzłów systemu Windows.

Po utworzeniu puli węzłów systemu Windows można teraz używać wbudowanych klas magazynu, takich jak `managed-csi` . Można wdrożyć przykładowy [zestaw stanowy oparty na systemie Windows](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) , który zapisuje sygnatury czasowe w pliku, `data.txt` wdrażając następujące polecenie za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

Teraz można sprawdzić poprawność zawartości woluminu przez uruchomienie:

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak używać sterowników CSI dla Azure Files, zobacz [używanie Azure Files z sterownikami CSI](azure-files-csi.md).
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