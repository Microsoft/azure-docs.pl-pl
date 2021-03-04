---
title: Połączenie SSH z węzłami klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć połączenie SSH z węzłami klastra usługi Azure Kubernetes Service (AKS) na potrzeby zadań związanych z rozwiązywaniem problemów i konserwacją.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 7455b98348f2b8c40f2ffc125abe1297af88fbd8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034459"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Nawiązywanie połączenia przy użyciu protokołu SSH z węzłami klastra usługi Azure Kubernetes Service w celu konserwacji lub rozwiązywania problemów

W całym cyklu życia klastra usługi Azure Kubernetes Service (AKS) może być konieczne uzyskanie dostępu do węzła AKS. Ten dostęp może być przeznaczony do konserwacji, zbierania dzienników lub wykonywania innych operacji związanych z rozwiązywaniem problemów. Dostęp do węzłów AKS można uzyskać przy użyciu protokołu SSH, w tym węzłów systemu Windows Server. Można także [połączyć się z węzłami systemu Windows Server przy użyciu połączeń RDP (Remote Desktop Protocol)][aks-windows-rdp]. Ze względów bezpieczeństwa węzły AKS nie są widoczne dla Internetu. Do węzłów AKS używany jest prywatny adres IP.

W tym artykule opisano sposób tworzenia połączenia SSH z węzłem AKS przy użyciu prywatnych adresów IP.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Domyślnie klucze SSH są pobierane lub generowane, a następnie dodawane do węzłów podczas tworzenia klastra AKS. W tym artykule pokazano, jak określić różne klucze SSH niż klucze SSH używane podczas tworzenia klastra AKS. W tym artykule pokazano również, jak ustalić prywatny adres IP węzła i połączyć się z nim przy użyciu protokołu SSH. Jeśli nie musisz określać innego klucza SSH, możesz pominąć krok dodawania klucza publicznego SSH do węzła.

W tym artykule przyjęto również, że masz klucz SSH. Klucz SSH można utworzyć za pomocą [macOS lub Linux][ssh-nix] lub [Windows][ssh-windows]. Jeśli używasz wypełniania generacji do utworzenia pary kluczy, Zapisz parę kluczy w formacie OpenSSH, a nie domyślny format klucza prywatnego (plik PPK).

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Konfigurowanie klastrów AKS opartych na zestawie skalowania maszyn wirtualnych na potrzeby dostępu SSH

Aby skonfigurować zestaw skalowania maszyn wirtualnych na podstawie dostępu SSH, Znajdź nazwę zestawu skalowania maszyn wirtualnych klastra i Dodaj swój klucz publiczny SSH do tego zestawu skalowania.

Użyj polecenia [AZ AKS show][az-aks-show] , aby pobrać nazwę grupy zasobów klastra AKS, a następnie polecenie [AZ VMSS list][az-vmss-list] , aby uzyskać nazwę zestawu skalowania.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query '[0].name' -o tsv)
```

Powyższy przykład przypisuje nazwę grupy zasobów klastra dla *myAKSCluster* w elemencie *webresourceing* do *CLUSTER_RESOURCE_GROUP*. Przykład używa *CLUSTER_RESOURCE_GROUP* , aby wyświetlić listę Nazwa zestawu skalowania i przypisać ją do *SCALE_SET_NAME*.

> [!IMPORTANT]
> W tej chwili należy zaktualizować klucze SSH dla klastrów AKS opartych na zestawie skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure.
> 
> W przypadku węzłów systemu Linux klucze SSH można obecnie dodawać tylko przy użyciu interfejsu wiersza polecenia platformy Azure. Jeśli chcesz nawiązać połączenie z węzłem systemu Windows Server przy użyciu protokołu SSH, użyj kluczy SSH dostarczonych podczas tworzenia klastra AKS i Pomiń następny zestaw poleceń dodawania klucza publicznego SSH. Nadal będzie potrzebny adres IP węzła, który ma zostać rozwiązaniu, który jest wyświetlany w końcowym poleceniu tej sekcji. Alternatywnie można [połączyć się z węzłami systemu Windows Server przy użyciu połączeń RDP (Remote Desktop Protocol)][aks-windows-rdp] , zamiast korzystać z protokołu SSH.

Aby dodać klucze SSH do węzłów w zestawie skalowania maszyn wirtualnych, użyj polecenia [AZ VMSS Extension Set][az-vmss-extension-set] i [AZ VMSS Update-Instances][az-vmss-update-instances] .

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

W powyższym przykładzie są stosowane zmienne *CLUSTER_RESOURCE_GROUP* i *SCALE_SET_NAME* z poprzednich poleceń. Powyższy przykład używa również *~/.ssh/id_rsa. pub* jako lokalizacji klucza publicznego SSH.

> [!NOTE]
> Domyślnie nazwa użytkownika dla węzłów AKS to *azureuser*.

Po dodaniu klucza publicznego SSH do zestawu skalowania można użyć protokołu SSH do maszyny wirtualnej węzła w tym zestawie skalowania przy użyciu jego adresu IP. Wyświetlanie prywatnych adresów IP węzłów klastra AKS za pomocą [polecenia Get polecenia kubectl][kubectl-get].

```console
kubectl get nodes -o wide
```

Poniższe przykładowe dane wyjściowe przedstawiają wewnętrzne adresy IP wszystkich węzłów w klastrze, w tym węzła systemu Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zapisz wewnętrzny adres IP węzła, którego chcesz rozwiązać.

Aby uzyskać dostęp do węzła przy użyciu protokołu SSH, wykonaj kroki opisane w temacie [Tworzenie połączenia SSH](#create-the-ssh-connection).

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Konfigurowanie klastrów AKS opartych na zestawie dostępności maszyn wirtualnych na potrzeby dostępu SSH

Aby skonfigurować klaster AKS oparty na zestawie dostępności maszyn wirtualnych na potrzeby dostępu SSH, Znajdź nazwę węzła systemu Linux klastra i Dodaj swój klucz publiczny SSH do tego węzła.

Użyj polecenia [AZ AKS show][az-aks-show] , aby pobrać nazwę grupy zasobów klastra AKS, a następnie polecenie [AZ VM list][az-vm-list] , aby wyświetlić listę Nazwa maszyny wirtualnej węzła systemu Linux.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Powyższy przykład przypisuje nazwę grupy zasobów klastra dla *myAKSCluster* w elemencie *webresourceing* do *CLUSTER_RESOURCE_GROUP*. W przykładzie zostanie użyta *CLUSTER_RESOURCE_GROUP* do wyświetlenia nazwy maszyny wirtualnej. Przykładowe dane wyjściowe przedstawiają nazwę maszyny wirtualnej:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Aby dodać klucze SSH do węzła, użyj polecenia [AZ VM User Update][az-vm-user-update] .

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Powyższy przykład używa zmiennej *CLUSTER_RESOURCE_GROUP* i nazwy maszyny wirtualnej węzła z poprzednich poleceń. Powyższy przykład używa również *~/.ssh/id_rsa. pub* jako lokalizacji klucza publicznego SSH. Możesz również użyć zawartości klucza publicznego SSH zamiast określić ścieżkę.

> [!NOTE]
> Domyślnie nazwa użytkownika dla węzłów AKS to *azureuser*.

Po dodaniu klucza publicznego SSH do maszyny wirtualnej węzła można użyć protokołu SSH do tej maszyny wirtualnej przy użyciu jego adresu IP. Zapoznaj się z prywatnym adresem IP węzła klastra AKS za pomocą polecenia [AZ VM list-IP-addresss][az-vm-list-ip-addresses] .

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

W powyższym przykładzie zastosowano zmienną *CLUSTER_RESOURCE_GROUP* ustawioną w poprzednich poleceniach. Następujące przykładowe dane wyjściowe przedstawiają prywatne adresy IP węzłów AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Tworzenie połączenia SSH

Aby utworzyć połączenie SSH z węzłem AKS, należy uruchomić pomocnika w klastrze AKS. Ten pomocnik w systemie zapewnia dostęp SSH do klastra, a następnie dodatkowy dostęp do węzła SSH. Aby utworzyć i korzystać z tego pomocnika, wykonaj następujące czynności:

1. Uruchom `debian` obraz kontenera i Dołącz do niego sesję terminalu. Tego kontenera można użyć do utworzenia sesji SSH z dowolnym węzłem w klastrze AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Jeśli używasz węzłów systemu Windows Server, Dodaj selektor węzła do polecenia w celu zaplanowania kontenera Debian w węźle z systemem Linux:
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. Po powiązaniu sesji terminalu z kontenerem Zainstaluj klienta SSH przy użyciu polecenia `apt-get` :

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Otwórz nowe okno terminalu, które nie jest połączone z kontenerem, skopiuj prywatny klucz SSH do pomocnika. Ten klucz prywatny służy do tworzenia protokołu SSH w węźle AKS. 

   W razie potrzeby zmień *~/.ssh/id_rsa* na lokalizację prywatnego klucza SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Wróć do sesji terminala do kontenera, zaktualizuj uprawnienia do skopiowanego `id_rsa` prywatnego klucza SSH, tak aby był on tylko do odczytu:

    ```console
    chmod 0400 id_rsa
    ```

1. Utwórz połączenie SSH z węzłem AKS. Ponownie domyślna nazwa użytkownika dla węzłów AKS to *azureuser*. Zaakceptuj monit, aby kontynuować połączenie, ponieważ klucz SSH jest najpierw zaufany. Następnie zostanie wyświetlony monit bash węzła AKS:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>Usuń dostęp SSH

Po zakończeniu `exit` sesja SSH, a następnie `exit` interaktywna sesja kontenera. Po zamknięciu tej sesji kontenera używany na potrzeby dostępu SSH z klastra AKS zostaje usunięty.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebne są dodatkowe dane dotyczące rozwiązywania problemów, można [wyświetlić dzienniki kubelet][view-kubelet-logs] lub [wyświetlić dzienniki głównego węzła Kubernetes][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances