---
title: Z protokołu RDP do węzłów systemu Windows Server usługi AKS
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak utworzyć połączenie RDP z węzłami Azure Kubernetes Service klastra (AKS) systemu Windows Server na potrzeby rozwiązywania problemów i zadań konserwacji.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 62f29c0550b858e34d888da61f1bd7fbd358f82d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782931"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Nawiązywanie połączenia za pomocą protokołu RDP z węzłami Azure Kubernetes Service klastra (AKS) systemu Windows Server w celu konserwacji lub rozwiązywania problemów

W całym cyklu życia klastra Azure Kubernetes Service (AKS) może być konieczne uzyskanie dostępu do węzła systemu Windows Server usługi AKS. Ten dostęp może dotyczyć konserwacji, zbierania dzienników lub innych operacji rozwiązywania problemów. Dostęp do węzłów systemu Windows Server usługi AKS można uzyskać przy użyciu protokołu RDP. Alternatywnie, jeśli chcesz użyć SSH w celu uzyskania dostępu do węzłów systemu Windows Server usługi AKS i masz dostęp do tej samej klawiatury kluczy, która była używana podczas tworzenia klastra, możesz wykonać kroki opisane w teksie SSH w węzłach klastra usługi [Azure Kubernetes Service (AKS).][ssh-steps] Ze względów bezpieczeństwa węzły usługi AKS nie są widoczne w Internecie.

W tym artykule pokazano, jak utworzyć połączenie RDP z węzłem usługi AKS przy użyciu ich prywatnych adresów IP.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS z węzłem systemu Windows Server. Jeśli potrzebujesz klastra AKS, zapoznaj się z artykułem na temat tworzenia klastra usługi AKS z kontenerem systemu Windows przy [użyciu interfejsu wiersza polecenia platformy Azure.][aks-windows-cli] Potrzebujesz nazwy użytkownika i hasła administratora systemu Windows dla węzła systemu Windows Server, którego chcesz rozwiązać. Jeśli ich nie znasz, możesz je zresetować, korzystając z funkcji Resetowanie Usługi pulpitu zdalnego lub jej hasła administratora na maszynie [wirtualnej z systemem Windows. ](/troubleshoot/azure/virtual-machines/reset-rdp) Potrzebny jest również klient RDP, taki [jak Pulpit zdalny Microsoft][rdp-mac].

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Wdrażanie maszyny wirtualnej w tej samej podsieci co klaster

Węzły systemu Windows Server klastra usługi AKS nie mają adresów IP dostępnych zewnętrznie. Aby nawiązyć połączenie RDP, możesz wdrożyć maszynę wirtualną z publicznie dostępnym adresem IP w tej samej podsieci co węzły systemu Windows Server.

Poniższy przykład tworzy maszynę wirtualną o *nazwie myVM* w grupie zasobów *myResourceGroup.*

Najpierw pobierz podsieć używaną przez pulę węzłów systemu Windows Server. Aby uzyskać identyfikator podsieci, potrzebna jest nazwa podsieci. Aby uzyskać nazwę podsieci, potrzebna jest nazwa sieci wirtualnej. Pobierz nazwę sieci wirtualnej, odpytując klaster o listę sieci. Aby można było odpytować klaster, potrzebna jest jego nazwa. Wszystkie te informacje można uzyskać, uruchamiając następujące Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Teraz, gdy masz już SUBNET_ID uruchom następujące polecenie w tym samym oknie Azure Cloud Shell, aby utworzyć maszynę wirtualną:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

Następujące przykładowe dane wyjściowe pokazują, że maszyna wirtualna została pomyślnie utworzona, i wyświetla publiczny adres IP maszyny wirtualnej.

```console
13.62.204.18
```

Zanotuj publiczny adres IP maszyny wirtualnej. Ten adres zostanie podany w późniejszym kroku.

## <a name="allow-access-to-the-virtual-machine"></a>Zezwalaj na dostęp do maszyny wirtualnej

Podsieci puli węzłów usługi AKS są domyślnie chronione za pomocą sieciowych grup zabezpieczeń. Aby uzyskać dostęp do maszyny wirtualnej, musisz włączyć dostęp w sieciowej sieciowej organizacji.

> [!NOTE]
> NSG są kontrolowane przez usługę AKS. Wszelkie zmiany w sieciowej sieciowej organizacji zostaną w dowolnym momencie zastąpione przez płaszczyznę sterowania.
>

Najpierw pobierz grupę zasobów i nazwę sieciowej grupy zasobów, aby dodać regułę do:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Następnie utwórz regułę sieciowej grupy zasad:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Uzyskiwanie adresu węzła

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl][kubectl]. Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować narzędzie `kubectl` lokalnie, użyj polecenia [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Wymień wewnętrzny adres IP węzłów systemu Windows Server za pomocą [polecenia kubectl get:][kubectl-get]

```console
kubectl get nodes -o wide
```

Następujące przykładowe dane wyjściowe pokazują wewnętrzne adresy IP wszystkich węzłów w klastrze, w tym węzłów systemu Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zanotuj wewnętrzny adres IP węzła systemu Windows Server, który chcesz rozwiązać. Ten adres zostanie podany w późniejszym kroku.

## <a name="connect-to-the-virtual-machine-and-node"></a>Nawiązywanie połączenia z maszyną wirtualną i węzłem

Połącz się z publicznym adresem IP utworzonej wcześniej maszyny wirtualnej przy użyciu klienta RDP, takiego [jak Pulpit zdalny Microsoft][rdp-mac].

![Obraz nawiązywania połączenia z maszyną wirtualną przy użyciu klienta RDP](media/rdp/vm-rdp.png)

Po nawiązania połączenia z maszyną wirtualną połącz się z wewnętrznym adresem *IP* węzła systemu Windows Server, którego rozwiązywanie chcesz rozwiązać przy użyciu klienta RDP z poziomu maszyny wirtualnej.

![Obraz nawiązywania połączenia z węzłem systemu Windows Server przy użyciu klienta RDP](media/rdp/node-rdp.png)

Masz teraz połączenie z węzłem systemu Windows Server.

![Obraz okna cmd w węźle systemu Windows Server](media/rdp/node-session.png)

Teraz możesz uruchamiać dowolne polecenia rozwiązywania problemów w *oknie polecenia cmd.* Ponieważ węzły systemu Windows Server używają systemu Windows Server Core, nie ma pełnego graficznego interfejsu użytkownika ani innych narzędzi graficznego interfejsu użytkownika podczas nawiązywania połączenia z węzłem systemu Windows Server za pośrednictwem protokołu RDP.

## <a name="remove-rdp-access"></a>Usuwanie dostępu RDP

Po jej nawiązaniu zamknij połączenie RDP z węzłem systemu Windows Server, a następnie zamknij sesję protokołu RDP z maszyną wirtualną. Po zakończeniu obu sesji protokołu RDP usuń maszynę wirtualną za pomocą [polecenia az vm delete:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

A reguła sieciowej sieciowej sieciowej sieci:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz dodatkowych danych dotyczących rozwiązywania problemów, możesz wyświetlić dzienniki węzła głównego [kubernetes][view-master-logs] lub Azure Monitor [.][azure-monitor-containers]

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-vm-delete]: /cli/azure/vm#az_vm_delete
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md