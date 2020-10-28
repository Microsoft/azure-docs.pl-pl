---
title: Integracja Azure NetApp Files z usługą Azure Kubernetes Service
description: Dowiedz się, jak zintegrować Azure NetApp Files z usługą Azure Kubernetes Service
services: container-service
ms.topic: article
ms.date: 09/26/2019
ms.openlocfilehash: 76bbf0ccaffecd05570848ab487f6d35f5ae5f01
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791566"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integracja Azure NetApp Files z usługą Azure Kubernetes Service

[Azure NetApp Files][anf] to oparta na platformie Azure usługa magazynu plików o wysokiej wydajności, działająca na mocy klasy korporacyjnej. W tym artykule pokazano, jak zintegrować Azure NetApp Files z usługą Azure Kubernetes Service (AKS).

## <a name="before-you-begin"></a>Przed rozpoczęciem
W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

> [!IMPORTANT]
> Klaster AKS musi również znajdować się [w regionie, który obsługuje Azure NetApp Files][anf-regions].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="limitations"></a>Ograniczenia

W przypadku korzystania z Azure NetApp Files są stosowane następujące ograniczenia:

* Azure NetApp Files jest dostępna tylko [w wybranych regionach świadczenia usługi Azure][anf-regions].
* Przed użyciem Azure NetApp Files należy uzyskać dostęp do usługi Azure NetApp Files. Aby uzyskać dostęp do programu, można użyć [formularza Azure NetApp Files waitlist][anf-waitlist]. Nie możesz uzyskać dostępu do usługi Azure NetApp Files, dopóki nie otrzymasz oficjalnej wiadomości e-mail z zespołu Azure NetApp Files.
* Usługa Azure NetApp Files musi zostać utworzona w tej samej sieci wirtualnej co klaster AKS.
* Po początkowym wdrożeniu klastra AKS obsługiwane jest tylko statyczne Inicjowanie obsługi Azure NetApp Files.
* Aby użyć dynamicznej obsługi administracyjnej z Azure NetApp Files, zainstaluj i skonfiguruj program [NetApp Trident](https://netapp-trident.readthedocs.io/) w wersji 19,07 lub nowszej.

## <a name="configure-azure-netapp-files"></a>Konfigurowanie Azure NetApp Files

> [!IMPORTANT]
> Przed zarejestrowaniem dostawcy zasobów  *Microsoft. NetApp* należy wykonać [formularz Azure NetApp Files waitlist][anf-waitlist] w ramach subskrypcji. Nie można zarejestrować zasobu do momentu otrzymania oficjalnej wiadomości e-mail z potwierdzeniem z zespołu Azure NetApp Files.

Zarejestruj dostawcę zasobów *Microsoft. NetApp* :

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Ukończenie tej operacji może zająć trochę czasu.

Podczas tworzenia konta usługi Azure NetApp do użytku z usługą AKS należy utworzyć konto w grupie zasobów **węzła** . Najpierw Pobierz nazwę grupy zasobów za pomocą polecenia [AZ AKS show][az-aks-show] i Dodaj `--query nodeResourceGroup` parametr zapytania. Poniższy przykład pobiera grupę zasobów węzła dla klastra AKS o nazwie *myAKSCluster* w grupie *zasobów nazwa zasobu* :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Utwórz konto Azure NetApp Files w grupie zasobów **węzła** i w tym samym regionie co klaster AKS przy użyciu polecenia [AZ netappfiles Account Create][az-netappfiles-account-create]. Poniższy przykład tworzy konto o nazwie *myaccount1* w grupie zasobów *MC_myResourceGroup_myAKSCluster_eastus* i regionie *wschodniego* :

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Utwórz nową pulę pojemności za pomocą polecenia [AZ netappfiles Pool Create][az-netappfiles-pool-create]. Poniższy przykład tworzy nową pulę pojemności o nazwie *mypool1* z 4 TB w rozmiarze i poziomie usługi *Premium* :

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Utwórz podsieć, do której [chcesz delegować Azure NetApp Files][anf-delegate-subnet] przy użyciu polecenia [AZ Network VNET Subnet Create][az-network-vnet-subnet-create]. *Ta podsieć musi znajdować się w tej samej sieci wirtualnej co klaster AKS.*

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

Utwórz wolumin za pomocą polecenia [AZ netappfiles Volume Create][az-netappfiles-volume-create].

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

## <a name="create-the-persistentvolume"></a>Tworzenie PersistentVolume

Wyświetl szczegóły woluminu za pomocą polecenia [AZ netappfiles Volume show][az-netappfiles-volume-show]

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

Utwórz element `pv-nfs.yaml` definiujący PersistentVolume. Zamień na `path` *creationToken* i `server` z *adresem IP* z poprzedniego polecenia. Na przykład:

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
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Zaktualizuj *serwer* i *ścieżkę* do wartości woluminu NFS (sieciowego systemu plików) utworzonego w poprzednim kroku. Utwórz PersistentVolume za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pv-nfs.yaml
```

Sprawdź, czy *stan* PersistentVolume jest *dostępny* za pomocą polecenia [polecenia kubectl opisywania][kubectl-describe] :

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>Tworzenie PersistentVolumeClaim

Utwórz element `pvc-nfs.yaml` definiujący PersistentVolume. Na przykład:

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

Utwórz PersistentVolumeClaim za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
kubectl apply -f pvc-nfs.yaml
```

Sprawdź, czy *stan* PersistentVolumeClaim jest *powiązany* przy użyciu polecenia [polecenia kubectl opisywania][kubectl-describe] :

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Zainstaluj przy użyciu pod

Utwórz element `nginx-nfs.yaml` a, który używa PersistentVolumeClaim. Na przykład:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: nginx
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

Utwórz pod za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
kubectl apply -f nginx-nfs.yaml
```

Sprawdź, czy pod poleceniem jest *uruchomiony* program, używając polecenia [polecenia kubectl opisywania][kubectl-describe] :

```console
kubectl describe pod nginx-nfs
```

Sprawdź, czy wolumin został zainstalowany w obszarze pod przy użyciu programu [polecenia kubectl exec][kubectl-exec] , aby nawiązać połączenie z usługą pod `df -h` warunkiem, aby sprawdzić, czy wolumin jest zainstalowany.

```console
$ kubectl exec -it nginx-nfs -- bash
```

```output
root@nginx-nfs:/# df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure NetApp Files, zobacz [co to jest Azure NetApp Files][anf]. Aby uzyskać więcej informacji na temat korzystania z systemu plików NFS z AKS, zobacz [Ręczne tworzenie i używanie woluminu systemu plików NFS (sieciowego systemu plików) z systemem Linux za pomocą usługi Azure Kubernetes Service (AKS)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account?view=azure-cli-latest#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool?view=azure-cli-latest#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume?view=azure-cli-latest#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
