---
title: Integracja Azure NetApp Files z Azure Kubernetes Service
description: Dowiedz się, jak zintegrować Azure NetApp Files z Azure Kubernetes Service
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 28c5b77f06bc48bf06575e45194adfaed068b30f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776055"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integracja Azure NetApp Files z Azure Kubernetes Service

[Azure NetApp Files][anf] to usługa mierzonego magazynu plików klasy korporacyjnej o wysokiej wydajności, która działa na platformie Azure. W tym artykule pokazano, jak zintegrować Azure NetApp Files z Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Zanim rozpoczniesz
W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Klaster usługi AKS musi również znajdować [się w regionie, który obsługuje Azure NetApp Files][anf-regions].

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="limitations"></a>Ograniczenia

W przypadku korzystania z Azure NetApp Files obowiązują następujące ograniczenia:

* Azure NetApp Files jest dostępna tylko w [wybranych regionach świadczenia usługi Azure.][anf-regions]
* Aby można było używać Azure NetApp Files, musisz uzyskać dostęp do tej Azure NetApp Files usługi. Aby zgłosić wniosek o dostęp, możesz użyć [Azure NetApp Files listy][anf-waitlist] oczekiwania lub przejść https://azure.microsoft.com/services/netapp/#getting-started do . Nie możesz uzyskać dostępu do usługi Azure NetApp Files, dopóki nie otrzymasz oficjalnej wiadomości e-mail z potwierdzeniem od Azure NetApp Files zespołu.
* Po początkowym wdrożeniu klastra usługi AKS obsługiwana jest tylko statyczna Azure NetApp Files klastra.
* Aby używać aprowizowania dynamicznego Azure NetApp Files, zainstaluj i skonfiguruj aplikację [NetApp Trident](https://netapp-trident.readthedocs.io/) w wersji 19.07 lub nowszej.

## <a name="configure-azure-netapp-files"></a>Konfigurowanie Azure NetApp Files

> [!IMPORTANT]
> Przed zarejestrowaniem dostawcy  *zasobów Microsoft.NetApp* należy wypełnić formularz Azure NetApp Files [listy][anf-waitlist] oczekiwania lub przejść do witryny https://azure.microsoft.com/services/netapp/#getting-started dla subskrypcji. Nie można zarejestrować zasobu, dopóki nie otrzymasz oficjalnej wiadomości e-mail z potwierdzeniem od Azure NetApp Files zespołu.

Zarejestruj dostawcę *zasobów Microsoft.NetApp:*

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Może to trochę potrwać.

Podczas tworzenia konta usługi Azure NetApp do użycia z usługą AKS należy utworzyć konto w grupie **zasobów** node. Najpierw pobierz nazwę grupy zasobów za pomocą polecenia [az aks show][az-aks-show] i dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera grupę zasobów węzła dla klastra usługi AKS o nazwie *myAKSCluster* w grupie zasobów *o nazwie myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Utwórz konto Azure NetApp Files w grupie  zasobów węzła i w tym samym regionie co klaster usługi AKS przy użyciu [narzędzia az netappfiles account create][az-netappfiles-account-create]. Poniższy przykład tworzy konto o nazwie *myaccount1* w MC_myResourceGroup_myAKSCluster_eastus *zasobów* i *regionie eastus:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Utwórz nową pulę pojemności za pomocą [az netappfiles pool create.][az-netappfiles-pool-create] Poniższy przykład tworzy nową pulę pojemności o nazwie *mypool1* o rozmiarze 4 TB i poziomie usługi *Premium:*

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Utwórz podsieć do [delegowania do Azure NetApp Files][anf-delegate-subnet] za pomocą [az network vnet subnet create][az-network-vnet-subnet-create]. *Ta podsieć musi znajdować się w tej samej sieci wirtualnej co klaster usługi AKS.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Utwórz wolumin za pomocą narzędzia [az netappfiles volume create][az-netappfiles-volume-create].

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>Tworzenie trwałego woluminu

Wyświetl szczegóły woluminu za pomocą [narzędzia az netappfiles volume show][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Utwórz `pv-nfs.yaml` definiowanie persistentVolume. Zastąp `path` wartości *wartościami creationToken* i `server` *ipAddress* z poprzedniego polecenia. Na przykład:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Zaktualizuj serwer *i* *ścieżkę* do wartości woluminu NFS (Network File System) utworzonego w poprzednim kroku. Utwórz plik PersistentVolume za pomocą [polecenia kubectl apply:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Sprawdź, *czy stan* trwałego woluminu jest *dostępny,* używając polecenia [kubectl describe:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Tworzenie aplikacji PersistentVolumeClaim

Utwórz `pvc-nfs.yaml` definiowanie persistentVolume. Na przykład:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Utwórz za pomocą polecenia [kubectl apply][kubectl-apply] polecenie PersistentVolumeClaim:

```console
kubectl apply -f pvc-nfs.yaml
```

Sprawdź, *czy stan* funkcji PersistentVolumeClaim jest powiązany *za* pomocą [polecenia kubectl describe:][kubectl-describe]

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Zainstaluj przy użyciu zasobnika

Utwórz zasobnik `nginx-nfs.yaml` definiujący, który używa persistentvolumeclaim. Na przykład:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Utwórz zasobnik za pomocą [polecenia kubectl apply:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Sprawdź, czy zasobnik jest *uruchomiony,* używając [polecenia kubectl describe:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Sprawdź, czy wolumin został zainstalowany w zasobniku, używając [narzędzia kubectl exec][kubectl-exec] w celu nawiązania połączenia z zasobnikem, a następnie sprawdź, czy `df -h` wolumin jest zainstalowany.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na Azure NetApp Files, [zobacz Co to jest Azure NetApp Files][anf]. Aby uzyskać więcej informacji na temat używania systemu plików NFS z usługą AKS, zobacz Ręczne tworzenie i używanie woluminu systemu plików [NFS (Network File System) linux Server][aks-nfs]z systemem Azure Kubernetes Service (AKS).


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
