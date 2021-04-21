---
title: Połączenie SSH z węzłami klastra usługi Azure Kubernetes Service (AKS)
description: Dowiedz się, jak utworzyć połączenie SSH z węzłami Azure Kubernetes Service (AKS) na potrzeby rozwiązywania problemów i zadań konserwacji.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 8df3e8be14e258aac34881014057dd7ee7ec3239
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769539"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>Nawiązywanie połączenia przy użyciu protokołu SSH z węzłami klastra usługi Azure Kubernetes Service w celu konserwacji lub rozwiązywania problemów

W całym cyklu życia klastra Azure Kubernetes Service (AKS) może być konieczne uzyskanie dostępu do węzła usługi AKS. Ten dostęp może dotyczyć konserwacji, zbierania dzienników lub innych operacji rozwiązywania problemów. Dostęp do węzłów usługi AKS można uzyskać przy użyciu usługi SSH, w tym węzłów systemu Windows Server. Można również połączyć się z węzłami systemu Windows Server przy użyciu połączeń protokołu [RDP (Remote Desktop Protocol).][aks-windows-rdp] Ze względów bezpieczeństwa węzły usługi AKS nie są widoczne w Internecie. Do nasyłania protokołu SSH z węzłami usługi AKS należy użyć prywatnego adresu IP.

W tym artykule pokazano, jak utworzyć połączenie SSH z węzłem usługi AKS przy użyciu ich prywatnych adresów IP.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub Azure Portal [.][aks-quickstart-portal]

Domyślnie klucze SSH są uzyskiwane lub generowane, a następnie dodawane do węzłów podczas tworzenia klastra usługi AKS. W tym artykule pokazano, jak określić inne klucze SSH niż klucze SSH użyte podczas tworzenia klastra usługi AKS. W tym artykule pokazano również, jak określić prywatny adres IP węzła i nawiązać z nim połączenie przy użyciu protokołu SSH. Jeśli nie musisz określać innego klucza SSH, możesz pominąć krok dodawania klucza publicznego SSH do węzła.

W tym artykule przyjęto również założenie, że masz klucz SSH. Klucz SSH można utworzyć przy użyciu systemu [macOS, Linux][ssh-nix] lub [Windows.][ssh-windows] Jeśli używasz programu PuTTY Gen do utworzenia pary kluczy, zapisz parę kluczy w formacie OpenSSH, a nie w domyślnym formacie klucza prywatnego PuTTy (plik ppk).

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.64 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Konfigurowanie klastrów usługi AKS opartych na zestawie skalowania maszyn wirtualnych w celu uzyskiwania dostępu do SSH

Aby skonfigurować oparty na zestawie skalowania maszyn wirtualnych dostęp SSH, znajdź nazwę zestawu skalowania maszyn wirtualnych klastra i dodaj klucz publiczny SSH do tego zestawu skalowania.

Użyj polecenia [az aks show,][az-aks-show] aby uzyskać nazwę grupy zasobów klastra usługi AKS, a następnie polecenia [az vmss list,][az-vmss-list] aby uzyskać nazwę zestawu skalowania.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query '[0].name' -o tsv)
```

W powyższym przykładzie przypisano nazwę grupy zasobów klastra dla klastra *myAKSCluster* w grupie *myResourceGroup* do *CLUSTER_RESOURCE_GROUP*. Następnie w przykładzie *użyto CLUSTER_RESOURCE_GROUP,* aby wyświetlić nazwę zestawu skalowania i przypisać go do *SCALE_SET_NAME*.

> [!IMPORTANT]
> W tej chwili klucze SSH należy aktualizować tylko dla klastrów usługi AKS opartych na zestawie skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure.
> 
> W przypadku węzłów systemu Linux klucze SSH można obecnie dodawać tylko przy użyciu interfejsu wiersza polecenia platformy Azure. Jeśli chcesz nawiązać połączenie z węzłem systemu Windows Server przy użyciu połączenia SSH, użyj kluczy SSH podanych podczas tworzenia klastra usługi AKS i pomiń następny zestaw poleceń dodawania klucza publicznego SSH. Nadal będziesz potrzebować adresu IP węzła, którego chcesz rozwiązać problem, który jest wyświetlany w ostatnim poleceniu w tej sekcji. Alternatywnie można nawiązać połączenie z węzłami systemu Windows Server przy użyciu połączeń protokołu [RDP (Remote Desktop Protocol)][aks-windows-rdp] zamiast protokołu SSH.

Aby dodać klucze SSH do węzłów w zestawie skalowania maszyn wirtualnych, użyj poleceń [az vmss extension set][az-vmss-extension-set] i az [vmss update-instances.][az-vmss-update-instances]

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

W powyższym przykładzie *użyto CLUSTER_RESOURCE_GROUP* *i SCALE_SET_NAME* zmiennych z poprzednich poleceń. W powyższym przykładzie *użyto również lokalizacji ~/.ssh/id_rsa.pub* jako lokalizacji klucza publicznego SSH.

> [!NOTE]
> Domyślnie nazwa użytkownika węzłów usługi AKS to *azureuser.*

Po dodaniu klucza publicznego SSH do zestawu skalowania możesz na maszynie wirtualnej węzła w tym zestawie skalowania użyć adresu IP. Wyświetl prywatne adresy IP węzłów klastra usługi AKS za pomocą [polecenia kubectl get][kubectl-get].

```console
kubectl get nodes -o wide
```

Następujące przykładowe dane wyjściowe pokazują wewnętrzne adresy IP wszystkich węzłów w klastrze, w tym węzła systemu Windows Server.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zanotuj wewnętrzny adres IP węzła, który chcesz rozwiązać.

Aby uzyskać dostęp do węzła przy użyciu połączenia SSH, wykonaj kroki opisane w te [tematu Tworzenie połączenia SSH.](#create-the-ssh-connection)

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Konfigurowanie klastrów AKS opartych na zestawie dostępności maszyny wirtualnej w celu uzyskania dostępu do SSH

Aby skonfigurować klaster usługi AKS oparty na zestawie dostępności maszyny wirtualnej na podstawie dostępu SSH, znajdź nazwę węzła systemu Linux klastra i dodaj klucz publiczny SSH do tego węzła.

Użyj polecenia [az aks show,][az-aks-show] aby uzyskać nazwę grupy zasobów klastra usługi AKS, a następnie polecenia [az vm list,][az-vm-list] aby wyświetlić listę nazw maszyn wirtualnych węzła systemu Linux klastra.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

Powyższy przykład przypisuje nazwę grupy zasobów klastra dla klastra *myAKSCluster* w *grupie myResourceGroup* do CLUSTER_RESOURCE_GROUP *.* W przykładzie użyto *CLUSTER_RESOURCE_GROUP,* aby wyświetlić nazwę maszyny wirtualnej. Przykładowe dane wyjściowe pokazują nazwę maszyny wirtualnej:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Aby dodać klucze SSH do węzła, użyj [polecenia az vm user update.][az-vm-user-update]

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

W powyższym przykładzie użyto *CLUSTER_RESOURCE_GROUP* i nazwy maszyny wirtualnej węzła z poprzednich poleceń. W powyższym przykładzie *użyto również lokalizacji ~/.ssh/id_rsa.pub* jako lokalizacji klucza publicznego SSH. Można również użyć zawartości klucza publicznego SSH zamiast określania ścieżki.

> [!NOTE]
> Domyślnie nazwa użytkownika węzłów usługi AKS to *azureuser.*

Po dodaniu klucza publicznego SSH do maszyny wirtualnej węzła możesz na tej maszynie wirtualnej podsieć SSH przy użyciu jej adresu IP. Wyświetl prywatny adres IP węzła klastra usługi AKS za pomocą [polecenia az vm list-ip-addresses.][az-vm-list-ip-addresses]

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

W powyższym przykładzie użyto *CLUSTER_RESOURCE_GROUP* zmiennej ustawionej w poprzednich poleceniach. Następujące przykładowe dane wyjściowe pokazują prywatne adresy IP węzłów usługi AKS:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Tworzenie połączenia SSH

Aby utworzyć połączenie SSH z węzłem usługi AKS, należy uruchomić zasobnik pomocnika w klastrze usługi AKS. Ten zasobnik pomocnika zapewnia dostęp SSH do klastra, a następnie dodatkowy dostęp do węzła SSH. Aby utworzyć i użyć tego zasobnika pomocnika, wykonaj następujące kroki:

1. Uruchom obraz `debian` kontenera i dołącz do niego sesję terminalu. Tego kontenera można użyć do utworzenia sesji SSH z dowolnym węzłem w klastrze usługi AKS:

    ```console
    kubectl run -it --rm aks-ssh --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
    ```

    > [!TIP]
    > Jeśli używasz węzłów systemu Windows Server, dodaj selektor węzłów do polecenia , aby zaplanować kontener Debian w węźle systemu Linux:
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. Po na połączeniu sesji terminalowej z kontenerem zainstaluj klienta SSH przy użyciu narzędzia `apt-get` :

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Otwórz nowe okno terminalu, które nie jest połączone z kontenerem, skopiuj prywatny klucz SSH do zasobnika pomocnika. Ten klucz prywatny służy do tworzenia konta SSH w węźle AKS. 

   W razie potrzeby *zmień ~/.ssh/id_rsa* na lokalizację prywatnego klucza SSH:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Wróć do sesji terminalowej kontenera i zaktualizuj uprawnienia skopiowanego prywatnego klucza SSH, aby był on tylko `id_rsa` do odczytu przez użytkownika:

    ```console
    chmod 0400 id_rsa
    ```

1. Utwórz połączenie SSH z węzłem AKS. Ponownie domyślną nazwą użytkownika węzłów usługi AKS jest *azureuser.* Zaakceptuj monit o kontynuowanie połączenia, ponieważ klucz SSH jest najpierw zaufany. Następnie zostanie wyświetlony monit powłoki bash węzła usługi AKS:

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

## <a name="remove-ssh-access"></a>Usuwanie dostępu SSH

Gdy wszystko będzie gotowe, `exit` sesja SSH, a następnie `exit` interaktywna sesja kontenera. Po zamknięciu tej sesji kontenera zasobnik używany do uzyskiwania dostępu do SSH z klastra usługi AKS zostanie usunięty.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz dodatkowych danych dotyczących rozwiązywania problemów, możesz wyświetlić dzienniki [kubelet][view-kubelet-logs] lub dzienniki węzła głównego [kubernetes.][view-master-logs]

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-vm-list]: /cli/azure/vm#az_vm_list
[az-vm-user-update]: /cli/azure/vm/user#az_vm_user_update
[az-vm-list-ip-addresses]: /cli/azure/vm#az_vm_list_ip_addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az_vmss_list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az_vmss_extension_set
[az-vmss-update-instances]: /cli/azure/vmss#az_vmss_update_instances