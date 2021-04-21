---
title: Dynamiczne tworzenie Azure Files udziału
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak dynamicznie tworzyć wolumin trwały za pomocą Azure Files do użytku z wieloma współbieżnych zasobnikami w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: f301a01e479d03647bebf7cb042564a258e9250e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776127"
---
# <a name="dynamically-create-and-use-a-persistent-volume-with-azure-files-in-azure-kubernetes-service-aks"></a>Dynamiczne tworzenie i korzystanie z trwałego woluminu za pomocą usługi Azure Files w usłudze Azure Kubernetes Service (AKS)

Trwały wolumin reprezentuje fragment magazynu, który został aprowowany do użytku z zasobnikami kubernetes. Wolumin trwały może być używany przez jeden lub wiele zasobników i może być aprowowany dynamicznie lub statycznie. Jeśli wiele zasobników wymaga współbieżnego dostępu do tego samego woluminu magazynu, możesz użyć usługi Azure Files do nawiązania połączenia przy użyciu protokołu bloku komunikatów serwera [(SMB).][smb-overview] W tym artykule przedstawiono sposób dynamicznego tworzenia udziału Azure Files do użytku przez wiele zasobników w klastrze usługi Azure Kubernetes Service (AKS).

Aby uzyskać więcej informacji na temat woluminów Kubernetes, zobacz Opcje magazynu [dla aplikacji w u usługi AKS][concepts-storage].

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-a-storage-class"></a>Tworzenie klasy magazynu

Klasa magazynu służy do definiowania sposobu tworzenia udziału plików platformy Azure. Konto magazynu jest automatycznie tworzone w grupie zasobów [węzła][node-resource-group] do użycia z klasą magazynu do przechowywania udziałów plików platformy Azure. Wybierz jedną z następujących [nadmiarowości usługi Azure Storage][storage-skus] dla *nazwy skuName:*

* *Standard_LRS —* standardowy magazyn lokalnie nadmiarowy (LRS)
* *Standard_GRS —* standardowy magazyn geograficznie nadmiarowy (GRS)
* *Standard_ZRS —* standardowy magazyn strefowo nadmiarowy (ZRS)
* *Standard_RAGRS —* standardowy magazyn geograficznie nadmiarowy z dostępem do odczytu (RA-GRS)
* *Premium_LRS* — magazyn lokalnie nadmiarowy (LRS) w chmurze Premium
* *Premium_ZRS* — magazyn strefowo nadmiarowy w chmurze (ZRS)

> [!NOTE]
> Azure Files premium storage w klastrach usługi AKS z usługą Kubernetes w wersji 1.13 lub wyższej, minimalny udział plików w chmurze Premium wynosi 100 GB

Aby uzyskać więcej informacji na temat klas magazynu Kubernetes dla Azure Files, zobacz [Klasy magazynu Kubernetes][kubernetes-storage-classes].

Utwórz plik o nazwie `azure-file-sc.yaml` i skopiuj go w poniższym przykładowym manifeście. Aby uzyskać więcej informacji *na temat opcji instalacji,* zobacz [sekcję Opcje instalacji.][mount-options]

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

Utwórz klasę magazynu za pomocą [polecenia kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Tworzenie trwałego oświadczenia woluminu

Trwałe oświadczenie woluminu używa obiektu klasy magazynu do dynamicznego aprowizowanie udziału plików platformy Azure. Następujący kod YAML może służyć do tworzenia trwałego oświadczenia *woluminu o rozmiarze 5 GB* przy użyciu *dostępu ReadWriteMany.* Aby uzyskać więcej informacji na temat trybów dostępu, zobacz dokumentację [dotyczącą trwałych woluminów kubernetes.][access-modes]

Teraz utwórz plik o nazwie `azure-file-pvc.yaml` i skopiuj go w poniższym pliku YAML. Upewnij się, że *storageClassName odpowiada* klasie magazynu utworzonej w ostatnim kroku:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: my-azurefile
  resources:
    requests:
      storage: 5Gi
```

> [!NOTE]
> Jeśli używasz *Premium_LRS* sku dla klasy magazynu, minimalna wartość magazynu *musi* być *100Gi.*

Utwórz trwałe oświadczenie woluminu za pomocą [polecenia kubectl apply:][kubectl-apply]

```console
kubectl apply -f azure-file-pvc.yaml
```

Po zakończeniu zostanie utworzony udział plików. Tworzony jest również klucz tajny kubernetes, który zawiera informacje o połączeniu i poświadczenia. Możesz użyć polecenia [kubectl get,][kubectl-get] aby wyświetlić stan wiersza POLECENIA:

```console
$ kubectl get pvc my-azurefile

NAME           STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
my-azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            my-azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Używanie woluminu trwałego

Poniższy kod YAML tworzy zasobnik, który używa trwałego oświadczenia woluminu *my-azurefile* do instalacji udziału plików platformy Azure w *ścieżce /mnt/azure.* W przypadku kontenerów systemu Windows Server określ *ścieżkę mountPath* przy użyciu konwencji ścieżki systemu Windows, na przykład *"D:".*

Utwórz plik o `azure-pvc-files.yaml` nazwie i skopiuj go w poniższym pliku YAML. Upewnij się, że *claimName pasuje* do rekordu WSZYSTKIEGO utworzonego w ostatnim kroku.

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
        claimName: my-azurefile
```

Utwórz zasobnik za pomocą [polecenia kubectl apply.][kubectl-apply]

```console
kubectl apply -f azure-pvc-files.yaml
```

Masz teraz uruchomiony zasobnik z twoim Azure Files zainstalowanym w *katalogu /mnt/azure.* Tę konfigurację można zobaczyć podczas inspekcji zasobnika za pośrednictwem programu `kubectl describe pod mypod` . Następujące skrócone przykładowe dane wyjściowe pokazują wolumin zainstalowany w kontenerze:

```
Containers:
  mypod:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Fri, 01 Mar 2019 23:56:16 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  my-azurefile
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Mount options (Opcje instalacji)

Wartość domyślna dla *trybów fileMode* i *dirMode* to *0777* dla kubernetes w wersji 1.13.0 lub nowszej. W przypadku dynamicznego tworzenia trwałego woluminu z klasą magazynu można określić opcje instalacji w obiekcie klasy magazynu. Poniższy przykład ustawia *0777:*

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict
  - actimeo=30
parameters:
  skuName: Standard_LRS
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać skojarzone najlepsze rozwiązania, zobacz [Best practices for storage and backups in AKS][operator-best-practices-storage](Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych w u usługi AKS).

Aby uzyskać parametry klasy magazynu, zobacz [Dynamic Provision](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/driver-parameters.md#dynamic-provision).

Dowiedz się więcej na temat trwałych woluminów Kubernetes korzystających z Azure Files.

> [!div class="nextstepaction"]
> [Wtyczka Kubernetes dla Azure Files][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/aks#az_aks_show
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[mount-options]: #mount-options
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[storage-skus]: ../storage/common/storage-redundancy.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
