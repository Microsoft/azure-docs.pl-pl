---
title: Tworzenie maszyny wirtualnej platformy Azure z przyspieszoną siecią przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć maszynę wirtualną z systemem Linux z włączoną obsługą przyspieszonej sieci.
services: virtual-network
documentationcenter: na
author: gsilva5
manager: gedegrac
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: gsilva
ms.custom: ''
ms.openlocfilehash: 643a52c9be04fb325b8e1d088faeb68e473aa673
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919956"
---
# <a name="create-a-linux-virtual-machine-with-accelerated-networking-using-azure-cli"></a>Tworzenie maszyny wirtualnej z systemem Linux z przyspieszoną siecią przy użyciu interfejsu wiersza polecenia platformy Azure

W tym samouczku dowiesz się, jak utworzyć maszynę wirtualną z systemem Linux przy użyciu przyspieszonej sieci. Aby utworzyć maszynę wirtualną z systemem Windows przy użyciu przyspieszonej sieci, zobacz [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu przyspieszonej sieci](create-vm-accelerated-networking-powershell.md). Przyspieszona sieć obsługuje wirtualizację we/wy z pojedynczym elementem głównym (SR-IOV) dla maszyny wirtualnej, co znacznie zwiększa wydajność sieci. Ta ścieżka o wysokiej wydajności pomija hosta ze ścieżki datapath, skracając czas opóźnienia, wahania i użycie procesora CPU w celu użycia z najbardziej wymagającymi obciążeniami sieci na obsługiwanych typach maszyn wirtualnych. Na poniższej ilustracji przedstawiono komunikację między dwiema maszynami wirtualnymi za pomocą i bez przyspieszonej sieci:

![Porównanie](./media/create-vm-accelerated-networking/accelerated-networking.png)

Bez przyspieszonej sieci cały ruch sieciowy z i do maszyny wirtualnej musi przechodzić przez hosta i przełącznik wirtualny. Przełącznik wirtualny zapewnia wszystkie wymuszanie zasad, takie jak sieciowe grupy zabezpieczeń, listy kontroli dostępu, izolacja i inne zwirtualizowane w sieci usługi do ruchu sieciowego. Aby dowiedzieć się więcej na temat przełączników wirtualnych, przeczytaj artykuł dotyczący [wirtualizacji sieci funkcji Hyper-V i wirtualnego przełącznika](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134230(v=ws.11)) .

Dzięki przyspieszonej sieci ruch sieciowy dociera do interfejsu sieciowego maszyny wirtualnej, a następnie jest kierowany do maszyny wirtualnej. Wszystkie zasady sieciowe stosowane przez przełącznik wirtualny są teraz odciążanie i stosowane do sprzętu. Zastosowanie zasad sprzętowych pozwala karcie sieciowej przekazywać ruch sieciowy bezpośrednio do maszyny wirtualnej, pomijając hosta i przełącznik wirtualny przy zachowaniu wszystkich zasad, które zostały zastosowane na hoście.

Zalety przyspieszonej sieci mają zastosowanie tylko do maszyny wirtualnej, na której jest włączona. Aby uzyskać najlepsze wyniki, najlepszym rozwiązaniem jest włączenie tej funkcji na co najmniej dwóch maszynach wirtualnych podłączonych do tej samej sieci wirtualnej platformy Azure. Podczas komunikacji między usługą sieci wirtualnych lub połączeniem lokalnym ta funkcja ma minimalny wpływ na ogólne opóźnienia.

## <a name="benefits"></a>Korzyści
* **Mniejsze opóźnienia/wyższe pakiety na sekundę (PPS):** Usunięcie przełącznika wirtualnego ze ścieżki datapath usuwa pakiety czasu spędzane przez hosta w celu przetworzenia zasad i zwiększa liczbę pakietów, które można przetworzyć w ramach maszyny wirtualnej.
* **Ograniczone wahania:** Przetwarzanie przełącznika wirtualnego zależy od ilości zasad, które należy zastosować, oraz obciążenia procesora, które przetwarza. Odciążanie wymuszania zasad sprzętowo eliminuje te zmienności, dostarczając pakiety bezpośrednio do maszyny wirtualnej, usuwając hosta do komunikacji z maszyną wirtualną oraz wszystkie przerwy w oprogramowaniu i przełączenia kontekstu.
* **Zmniejszone użycie procesora CPU:** Obejście przełącznika wirtualnego na hoście prowadzi do mniejszego użycia procesora CPU do przetwarzania ruchu sieciowego.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne
Następujące dystrybucje są obsługiwane z poziomu galerii platformy Azure: 
* **Ubuntu 14,04 z systemem Linux — jądro platformy Azure**
* **Ubuntu 16,04 lub nowszy** 
* **SLES12 SP3 lub nowszy** 
* **RHEL 7,4 lub nowszy**
* **CentOS 7,4 lub nowszy**
* **CoreOS Linux**
* **Debian "Rozciągnij" przy użyciu jądra z wieloma portami, Debian "Buster" lub nowszy**
* **Oracle Linux 7,4 i nowsze dzięki jądrze zgodnemu z systemem Red Hat (RHCK)**
* **Oracle Linux 7,5 i nowsze z UEK wersja 5**
* **FreeBSD 10,4, 11,1 & 12,0 lub nowszego**

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

### <a name="supported-vm-instances"></a>Obsługiwane wystąpienia maszyn wirtualnych
Przyspieszona sieć jest obsługiwana w większości ogólnego przeznaczenia i o rozmiarach wystąpień zoptymalizowanych pod kątem obliczeń przy użyciu co najmniej dwóch procesorów wirtualnych vCPU. W wystąpieniach, które obsługują wielowątkowość, przyspieszona sieć jest obsługiwana w wystąpieniach maszyn wirtualnych z 4 lub więcej procesorów wirtualnych vCPU. 

Obsługę przyspieszonej sieci można znaleźć w dokumentacji poszczególnych [rozmiarów maszyn wirtualnych](../virtual-machines/sizes.md) . 

### <a name="custom-images"></a>Obrazy niestandardowe
Jeśli używasz obrazu niestandardowego, a obraz obsługuje przyspieszone sieci, upewnij się, że wymagane sterowniki współpracują z kartami interfejsu sieciowego Mellanox ConnectX-3 i ConnectX-4 LX na platformie Azure.

### <a name="regions"></a>Regiony
Dostępne we wszystkich publicznych regionach świadczenia usługi Azure oraz w Azure Government chmurach.

<!-- ### Network interface creation 
Accelerated networking can only be enabled for a new NIC. It cannot be enabled for an existing NIC.
removed per issue https://github.com/MicrosoftDocs/azure-docs/issues/9772 -->
### <a name="enabling-accelerated-networking-on-a-running-vm"></a>Włączanie przyspieszonej sieci na uruchomionej maszynie wirtualnej
W przypadku obsługiwanego rozmiaru maszyny wirtualnej bez przyspieszonej sieci można włączyć funkcję włączoną tylko wtedy, gdy została zatrzymana i cofnięta alokacja.  
### <a name="deployment-through-azure-resource-manager"></a>Wdrożenie za Azure Resource Manager
Maszyn wirtualnych (klasycznych) nie można wdrożyć przy użyciu przyspieszonej sieci.

## <a name="create-a-linux-vm-with-azure-accelerated-networking"></a>Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej sieci platformy Azure
## <a name="portal-creation"></a>Tworzenie portalu
Mimo że ten artykuł zawiera instrukcje tworzenia maszyny wirtualnej z przyspieszoną siecią przy użyciu interfejsu wiersza polecenia platformy Azure, można również [utworzyć maszynę wirtualną z przyspieszoną siecią przy użyciu Azure Portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Podczas tworzenia maszyny wirtualnej w portalu w bloku **Utwórz maszynę wirtualną** wybierz kartę **Sieć** .  Na tej karcie jest dostępna opcja dla **przyspieszonej sieci**.  W przypadku wybrania [obsługiwanego systemu operacyjnego](#supported-operating-systems) i [rozmiaru maszyny wirtualnej](#supported-vm-instances)ta opcja spowoduje automatyczne wypełnienie do "włączone".  Jeśli nie, spowoduje to wypełnienie opcji "off" dla przyspieszonej sieci i nadanie użytkownikowi przyczyny, dlaczego nie jest on włączony.   

* *Uwaga:* Tylko obsługiwane systemy operacyjne można włączyć za pomocą portalu.  Jeśli używasz obrazu niestandardowego, a obraz obsługuje przyspieszone sieci, Utwórz maszynę wirtualną przy użyciu interfejsu wiersza polecenia lub programu PowerShell. 

Po utworzeniu maszyny wirtualnej można potwierdzić, że przyspieszona sieć jest włączona, postępując zgodnie z instrukcjami w obszarze [upewnij się, że włączona jest funkcja przyspieszonej sieci](#confirm-that-accelerated-networking-is-enabled).

## <a name="cli-creation"></a>Tworzenie interfejsu wiersza polecenia
### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Zainstaluj najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zaloguj się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index). W poniższych przykładach Zastąp przykładowe nazwy parametrów własnymi wartościami. Przykładowe nazwy parametrów dołączone do *zasobów*, *myNic* i *myVm*.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *centralnej* :

```azurecli
az group create --name myResourceGroup --location centralus
```

Wybierz obsługiwany region systemu Linux na liście w [przyspieszonej sieci systemu Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview).

Utwórz sieć wirtualną za pomocą polecenia [az network vnet create](/cli/azure/network/vnet). Poniższy przykład tworzy sieć wirtualną o nazwie *myVnet* z jedną podsiecią:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń
Utwórz sieciową grupę zabezpieczeń za pomocą [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNetworkSecurityGroup*:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Grupa zabezpieczeń sieci zawiera kilka reguł domyślnych, z których jeden wyłącza dostęp przychodzący z Internetu. Otwórz port, aby umożliwić dostęp SSH do maszyny wirtualnej za pomocą [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule):

```azurecli
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNetworkSecurityGroup \
  --name Allow-SSH-Internet \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 22
```

### <a name="create-a-network-interface-with-accelerated-networking"></a>Tworzenie interfejsu sieciowego przy użyciu przyspieszonej sieci

Utwórz publiczny adres IP za pomocą polecenia [az network public-ip create](/cli/azure/network/public-ip). Publiczny adres IP nie jest wymagany, jeśli nie masz dostępu do maszyny wirtualnej z Internetu, ale do wykonania kroków opisanych w tym artykule jest to wymagane.

```azurecli
az network public-ip create \
    --name myPublicIp \
    --resource-group myResourceGroup
```

Utwórz interfejs sieciowy za pomocą [AZ Network nic Create](/cli/azure/network/nic) z włączoną obsługą przyspieszonej sieci. Poniższy przykład tworzy interfejs sieciowy o nazwie *myNic* w podsieci z *podsiecią* sieci wirtualnej *myVnet* i kojarzy grupę zabezpieczeń sieci *myNetworkSecurityGroup* z interfejsem sieciowym:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --accelerated-networking true \
    --public-ip-address myPublicIp \
    --network-security-group myNetworkSecurityGroup
```

### <a name="create-a-vm-and-attach-the-nic"></a>Tworzenie maszyny wirtualnej i dołączanie karty sieciowej
Podczas tworzenia maszyny wirtualnej należy określić kartę sieciową utworzoną za pomocą programu `--nics` . Wybierz rozmiar i dystrybucję na liście w [przyspieszonej sieci systemu Linux](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview). 

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Poniższy przykład tworzy maszynę wirtualną o nazwie *myVM* z obrazem UbuntuLTS i rozmiar obsługujący przyspieszone sieci (*Standard_DS4_v2*):

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS4_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic
```

Aby uzyskać listę wszystkich rozmiarów maszyn wirtualnych i ich charakterystyk, zobacz [rozmiary maszyn wirtualnych z systemem Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Po utworzeniu maszyny wirtualnej zwraca dane wyjściowe podobne do następujących przykładowych danych wyjściowych. Zanotuj wartość adresu **publicIpAddress**. Ten adres jest używany w celu uzyskania dostępu do maszyny wirtualnej w kolejnych krokach.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "centralus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "192.168.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

### <a name="confirm-that-accelerated-networking-is-enabled"></a>Upewnij się, że włączona jest szybsza sieć

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Zastąp `<your-public-ip-address>` wartość publicznym adresem IP przypisanym do utworzonej maszyny wirtualnej i Zastąp ciąg *azureuser* , jeśli `--admin-username` podczas tworzenia maszyny wirtualnej użyto innej wartości.

```bash
ssh azureuser@<your-public-ip-address>
```

Z poziomu powłoki bash wprowadź `uname -r` i sprawdź, czy wersja jądra ma jedną z następujących wersji lub wyższą:

* **Ubuntu 16,04**: 4.11.0-1013
* **SLES SP3**: 4.4.92-6.18
* **RHEL**: 7.4.2017120423
* **CentOS**: 7.4.20171206


Upewnij się, że urządzenie Mellanox VF jest widoczne dla maszyny wirtualnej za pomocą `lspci` polecenia. Zwrócone dane wyjściowe są podobne do następujących:

```output
0000:00:00.0 Host bridge: Intel Corporation 440BX/ZX/DX - 82443BX/ZX/DX Host bridge (AGP disabled) (rev 03)
0000:00:07.0 ISA bridge: Intel Corporation 82371AB/EB/MB PIIX4 ISA (rev 01)
0000:00:07.1 IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)
0000:00:07.3 Bridge: Intel Corporation 82371AB/EB/MB PIIX4 ACPI (rev 02)
0000:00:08.0 VGA compatible controller: Microsoft Corporation Hyper-V virtual VGA
0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
```

Sprawdź działanie w funkcji VF (funkcja wirtualna) za pomocą `ethtool -S eth0 | grep vf_` polecenia. Jeśli otrzymujesz dane wyjściowe podobne do następujących przykładowych danych wyjściowych, przyspieszona sieć jest włączona i działa.

```output
vf_rx_packets: 992956
vf_rx_bytes: 2749784180
vf_tx_packets: 2656684
vf_tx_bytes: 1099443970
vf_tx_dropped: 0
```
Przyspieszona sieć jest teraz włączona dla maszyny wirtualnej.

## <a name="handle-dynamic-binding-and-revocation-of-virtual-function"></a>Obsługa dynamicznego powiązania i odwoływania funkcji wirtualnej 
Aplikacje muszą działać przez syntetyczną kartę sieciową, która jest udostępniona w maszynie wirtualnej. Jeśli aplikacja działa bezpośrednio za pośrednictwem karty interfejsu sieciowego VF, nie odbiera **wszystkich** pakietów, które są przeznaczone dla maszyny wirtualnej, ponieważ niektóre pakiety są widoczne w interfejsie syntetycznym.
Jeśli uruchamiasz aplikację za pośrednictwem syntetycznej karty sieciowej, gwarantuje to, że aplikacja odbiera **wszystkie** pakiety, do których są przeznaczone. Sprawdza również, czy aplikacja działa, nawet jeśli klient jest odwołany, gdy host jest w trakcie obsługi. Aplikacje powiązane z syntetyczną kartą sieciową **są wymaganym warunkiem** dla wszystkich aplikacji korzystających z **przyspieszonej łączności sieciowej**.

## <a name="enable-accelerated-networking-on-existing-vms"></a>Włączanie przyspieszonej sieci na istniejących maszynach wirtualnych
Jeśli utworzono maszynę wirtualną bez przyspieszonej sieci, można włączyć tę funkcję na istniejącej maszynie wirtualnej.  Maszyna wirtualna musi obsługiwać przyspieszone sieci, spełniając poniższe wymagania wstępne, które zostały opisane powyżej:

* Maszyna wirtualna musi być obsługiwanym rozmiarem dla przyspieszonej sieci
* Maszyna wirtualna musi być obsługiwanym obrazem galerii platformy Azure (i wersją jądra dla systemu Linux)
* Wszystkie maszyny wirtualne w zestawie dostępności lub VMSS muszą zostać zatrzymane/cofnięte alokacje przed włączeniem przyspieszonej sieci na dowolnej karcie sieciowej

### <a name="individual-vms--vms-in-an-availability-set"></a>Poszczególne maszyny wirtualne & maszyny wirtualne w zestawie dostępności
Najpierw Zatrzymaj/Cofnij przydział maszyny wirtualnej lub, jeśli zestaw dostępności, wszystkie maszyny wirtualne w zestawie:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

Należy pamiętać, że jeśli maszyna wirtualna została utworzona osobno, bez zestawu dostępności, wystarczy zatrzymać/cofnąć alokację pojedynczej maszyny wirtualnej w celu włączenia przyspieszonej sieci.  Jeśli maszyna wirtualna została utworzona przy użyciu zestawu dostępności, wszystkie maszyny wirtualne znajdujące się w zestawie dostępności muszą zostać zatrzymane/cofnięte alokacje przed włączeniem przyspieszonej sieci na dowolnej z kart sieciowych. 

Po zatrzymaniu Włącz przyspieszone sieci na karcie sieciowej maszyny wirtualnej:

```azurecli
az network nic update \
    --name myNic \
    --resource-group myResourceGroup \
    --accelerated-networking true
```

Uruchom ponownie maszynę wirtualną lub, jeśli w zestawie dostępności, wszystkie maszyny wirtualne w zestawie i upewnij się, że włączona jest funkcja przyspieszonej sieci: 

```azurecli
az vm start --resource-group myResourceGroup \
    --name myVM
```

### <a name="vmss"></a>Zestaw skalowania maszyn wirtualnych
VMSS jest nieco inny, ale następuje po tym samym przepływie pracy.  Najpierw Zatrzymaj maszyny wirtualne:

```azurecli
az vmss deallocate \
    --name myvmss \
    --resource-group myrg
```

Po zatrzymaniu maszyn wirtualnych zaktualizuj właściwość sieci przyspieszonej w interfejsie sieciowym:

```azurecli
az vmss update --name myvmss \
    --resource-group myrg \
    --set virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].enableAcceleratedNetworking=true
```

Należy pamiętać, że VMSS ma uaktualnienia maszyny wirtualnej, które stosują aktualizacje przy użyciu trzech różnych ustawień, automatycznych, stopniowych i ręcznych.  W tych instrukcjach zasada jest ustawiona na automatyczne, dzięki czemu VMSS będzie pobierać zmiany natychmiast po ponownym uruchomieniu.  Aby ustawić automatyczne pobieranie zmian, aby zmiany zostały wprowadzone: 

```azurecli
az vmss update \
    --name myvmss \
    --resource-group myrg \
    --set upgradePolicy.mode="automatic"
```

Na koniec Uruchom ponownie VMSS:

```azurecli
az vmss start \
    --name myvmss \
    --resource-group myrg
```

Po ponownym uruchomieniu Zaczekaj na zakończenie uaktualnień, ale po jej zakończeniu zostanie wyświetlona w obrębie maszyny wirtualnej.  (Upewnij się, że korzystasz z obsługiwanego systemu operacyjnego i rozmiaru maszyny wirtualnej).

### <a name="resizing-existing-vms-with-accelerated-networking"></a>Zmienianie rozmiarów istniejących maszyn wirtualnych za pomocą przyspieszonej sieci

Zmiany rozmiaru maszyn wirtualnych z włączoną przyspieszoną siecią można zmienić tylko na maszyny wirtualne, które obsługują przyspieszone sieci.  

Nie można zmienić rozmiaru maszyny wirtualnej z włączoną obsługą przyspieszonej sieci do wystąpienia maszyny wirtualnej, która nie obsługuje przyspieszonej sieci przy użyciu operacji zmiany rozmiaru.  Zamiast tego, aby zmienić rozmiar jednej z tych maszyn wirtualnych: 

* Zatrzymaj/Cofnij przydział maszyny wirtualnej lub jeśli znajduje się w zestawie dostępności/VMSS, Zatrzymaj/Cofnij przydział wszystkich maszyn wirtualnych w zestawie/VMSS.
* Przyspieszone sieci muszą być wyłączone na karcie sieciowej maszyny wirtualnej lub w zestawie dostępności/VMSS wszystkie maszyny wirtualne w zestawie/VMSS.
* Po wyłączeniu przyspieszonej sieci można przenieść zestaw VM/Availability/VMSS do nowego rozmiaru, który nie obsługuje przyspieszonej sieci i ponownego uruchomienia.