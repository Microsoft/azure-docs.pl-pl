---
title: AKS węzłów systemu Windows Server
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak utworzyć połączenie RDP z węzłami systemu Windows Server klastra usługi Azure Kubernetes Service (AKS) na potrzeby zadań związanych z rozwiązywaniem problemów i konserwacją.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 4cfac73c66969148927897de7ed6da0c56aa276a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102173821"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Nawiązywanie połączenia z usługą Azure Kubernetes Service (AKS) klastrami węzłów systemu Windows Server w celu przeprowadzenia konserwacji lub rozwiązywania problemów

W całym cyklu życia klastra usługi Azure Kubernetes Service (AKS) może być konieczne uzyskanie dostępu do węzła AKS systemu Windows Server. Ten dostęp może być przeznaczony do konserwacji, zbierania dzienników lub wykonywania innych operacji związanych z rozwiązywaniem problemów. Dostęp do węzłów AKS systemu Windows Server można uzyskać przy użyciu protokołu RDP. Alternatywnie, jeśli chcesz użyć protokołu SSH w celu uzyskania dostępu do węzłów systemu Windows Server AKS i masz dostęp do tej samej pary kluczy, która została użyta podczas tworzenia klastra, możesz wykonać kroki podane w obszarze [protokołu SSH w węzłach klastra usługi Azure Kubernetes Service (AKS)][ssh-steps]. Ze względów bezpieczeństwa węzły AKS nie są widoczne dla Internetu.

W tym artykule pokazano, jak utworzyć połączenie RDP z węzłem AKS przy użyciu prywatnych adresów IP.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS z węzłem systemu Windows Server. Jeśli potrzebujesz klastra AKS, zapoznaj się z artykułem dotyczącym [tworzenia klastra AKS z kontenerem systemu Windows przy użyciu interfejsu wiersza polecenia platformy Azure][aks-windows-cli]. Wymagana jest nazwa użytkownika i hasło administratora systemu Windows dla węzła systemu Windows Server, którego chcesz użyć do rozwiązywania problemów. Jeśli ich nie znasz, możesz je zresetować, wykonując następujące czynności [resetowania usługi pulpitu zdalnego lub jego hasła administratora na maszynie wirtualnej z systemem Windows ](../virtual-machines/troubleshooting/reset-rdp.md). Wymagany jest również klient RDP, taki jak [pulpit zdalny Microsoft][rdp-mac].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Wdróż maszynę wirtualną w tej samej podsieci co klaster

Węzły systemu Windows Server w klastrze AKS nie mają adresów IP dostępnych na zewnątrz. Aby nawiązać połączenie RDP, można wdrożyć maszynę wirtualną z publicznie dostępnym adresem IP w tej samej podsieci, w której znajduje się węzły systemu Windows Server.

Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* w grupie zasobów *WebResource* .

Najpierw należy uzyskać podsieć używaną przez pulę węzłów systemu Windows Server. Aby uzyskać identyfikator podsieci, potrzebna jest nazwa podsieci. Aby uzyskać nazwę podsieci, potrzebna jest nazwa sieci wirtualnej. Pobierz nazwę sieci wirtualnej, wykonując zapytania dotyczące klastra w celu wyświetlenia jego listy. Aby można było wysyłać zapytania do klastra, potrzebna jest jego nazwa. Wszystkie te możliwości można uzyskać, uruchamiając następujące czynności w Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Teraz, gdy masz SUBNET_ID, uruchom następujące polecenie w tym samym oknie Azure Cloud Shell, aby utworzyć maszynę wirtualną:

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

Następujące przykładowe dane wyjściowe pokazują, że maszyna wirtualna została pomyślnie utworzona i wyświetla publiczny adres IP maszyny wirtualnej.

```console
13.62.204.18
```

Zapisz publiczny adres IP maszyny wirtualnej. Ten adres będzie używany w późniejszym kroku.

## <a name="allow-access-to-the-virtual-machine"></a>Zezwalaj na dostęp do maszyny wirtualnej

Podsieci puli węzłów AKS są domyślnie chronione za pomocą sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń). Aby uzyskać dostęp do maszyny wirtualnej, musisz włączyć dostęp w sieciowej grupy zabezpieczeń.

> [!NOTE]
> Sieciowych grup zabezpieczeń są kontrolowane przez usługę AKS. Wszystkie zmiany wprowadzone w sieciowej grupy zabezpieczeń zostaną zastąpione w dowolnym momencie przez płaszczyznę kontroli.
>

Najpierw pobierz grupę zasobów i sieciowej grupy zabezpieczeń nazwę sieciowej grupy zabezpieczeń, do której chcesz dodać regułę:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Następnie utwórz regułę sieciowej grupy zabezpieczeń:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>Pobierz adres węzła

Aby zarządzać klastrem Kubernetes, należy użyć klienta wiersza polecenia usługi Kubernetes — narzędzia [kubectl][kubectl]. Jeśli korzystasz z usługi Azure Cloud Shell, narzędzie `kubectl` jest już zainstalowane. Aby zainstalować narzędzie `kubectl` lokalnie, użyj polecenia [az aks install-cli][az-aks-install-cli]:
    
```azurecli-interactive
az aks install-cli
```

Aby skonfigurować narzędzie `kubectl` w celu nawiązania połączenia z klastrem Kubernetes, użyj polecenia [az aks get-credentials][az-aks-get-credentials]. To polecenie powoduje pobranie poświadczeń i zastosowanie ich w konfiguracji interfejsu wiersza polecenia Kubernetes.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Utwórz listę wewnętrznego adresu IP węzłów systemu Windows Server za pomocą polecenia [polecenia kubectl Get][kubectl-get] :

```console
kubectl get nodes -o wide
```

Poniższe przykładowe dane wyjściowe przedstawiają wewnętrzne adresy IP wszystkich węzłów w klastrze, w tym węzły systemu Windows Server.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Zapisz wewnętrzny adres IP węzła systemu Windows Server, którego chcesz rozwiązać. Ten adres będzie używany w późniejszym kroku.

## <a name="connect-to-the-virtual-machine-and-node"></a>Nawiązywanie połączenia z maszyną wirtualną i węzłem

Połącz się z publicznym adresem IP maszyny wirtualnej utworzonej wcześniej przy użyciu klienta RDP, takiego jak [pulpit zdalny Microsoft][rdp-mac].

![Obraz przedstawiający łączenie się z maszyną wirtualną przy użyciu klienta RDP](media/rdp/vm-rdp.png)

Po nawiązaniu połączenia z maszyną wirtualną należy nawiązać połączenie z *wewnętrznym adresem IP* węzła systemu Windows Server, który ma być rozwiązywany przy użyciu klienta RDP z poziomu maszyny wirtualnej.

![Obraz przedstawiający łączenie się z węzłem systemu Windows Server przy użyciu klienta RDP](media/rdp/node-rdp.png)

Masz teraz połączenie z węzłem systemu Windows Server.

![Obraz okna cmd w węźle systemu Windows Server](media/rdp/node-session.png)

Teraz można uruchomić dowolne polecenia rozwiązywania problemów w oknie *cmd* . Ponieważ węzły systemu Windows Server używają systemu Windows Server Core, podczas łączenia się z węzłem systemu Windows Server za pośrednictwem protokołu RDP nie ma pełnego graficznego interfejsu użytkownika ani innych narzędzi graficznego interfejsu użytkownika.

## <a name="remove-rdp-access"></a>Usuń dostęp RDP

Po zakończeniu zamknij połączenie RDP z węzłem systemu Windows Server, a następnie Zakończ sesję RDP na maszynie wirtualnej. Po zakończeniu obu sesji RDP Usuń maszynę wirtualną za pomocą polecenia [AZ VM Delete][az-vm-delete] :

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

I reguła sieciowej grupy zabezpieczeń:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebne są dodatkowe dane dotyczące rozwiązywania problemów, można [wyświetlić dzienniki węzła głównego Kubernetes][view-master-logs] lub [Azure monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md