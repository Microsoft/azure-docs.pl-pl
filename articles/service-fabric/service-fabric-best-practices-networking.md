---
title: Najlepsze rozwiązania dotyczące sieci Service Fabric platformy Azure
description: Zasady i zagadnienia dotyczące projektowania dotyczące zarządzania łącznością sieciową za pomocą usługi Azure Service Fabric.
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: b8db69792b31fd82646757423e669e39e8539d06
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630706"
---
# <a name="networking"></a>Sieć

Podczas tworzenia klastrów Service Fabric platformy Azure i zarządzania nimi można zapewnić łączność sieciową dla węzłów i aplikacji. Zasoby sieci obejmują zakresy adresów IP, sieci wirtualne, moduły równoważenia obciążenia i sieciowe grupy zabezpieczeń. W tym artykule przedstawiono najlepsze rozwiązania dotyczące tych zasobów.

Przejrzyj [wzorce sieci Service Fabric](./service-fabric-patterns-networking.md) platformy Azure, aby dowiedzieć się, jak tworzyć klastry korzystające z następujących funkcji: istniejąca sieć wirtualna lub podsieć, statyczny publiczny adres IP, moduł równoważenia obciążenia wyłącznie wewnętrznie lub wewnętrzny i zewnętrzny moduł równoważenia obciążenia.

## <a name="infrastructure-networking"></a>Sieć infrastruktury
Zmaksymalizuj wydajność maszyny wirtualnej za pomocą przyspieszonej sieci, deklarując Właściwość *enableAcceleratedNetworking* w szablonie Menedżer zasobów, Poniższy fragment kodu jest NetworkInterfaceConfigurations zestawu skalowania maszyn wirtualnych, który umożliwia przyspieszenie sieci:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
Klaster Service Fabric może być inicjowany w systemie [Linux przy użyciu przyspieszonej sieci](../virtual-network/create-vm-accelerated-networking-cli.md)i [systemu Windows z przyspieszoną siecią](../virtual-network/create-vm-accelerated-networking-powershell.md).

Przyspieszona sieć jest obsługiwana w przypadku jednostek SKU serii maszyn wirtualnych platformy Azure: D/DSv2, D/DSv3, E/ESv3, F/FS, FSv2 i MS/MMS. Przyspieszona sieć została pomyślnie przetestowana przy użyciu jednostki SKU Standard_DS8_v3 na 01/23/2019 dla Service Fabric klastra systemu Windows i przy użyciu Standard_DS12_v2 na 01/29/2019 dla klastra Service Fabric Linux.

Aby włączyć przyspieszone sieci w istniejącym klastrze Service Fabric, należy najpierw [skalować klaster Service Fabric przez dodanie zestawu skalowania maszyn wirtualnych](./virtual-machine-scale-set-scale-node-type-scale-out.md), aby wykonać następujące czynności:
1. Inicjowanie obsługi administracyjnej NodeType z włączoną obsługą przyspieszonej sieci
2. Migrowanie usług i ich stanu do aprowizacji NodeType z włączoną obsługą przyspieszonej sieci

Skalowanie w górę infrastruktury jest wymagane do włączenia przyspieszonej sieci w istniejącym klastrze, ponieważ włączenie przyspieszonej sieci spowoduje przestoje, ponieważ wymaga ono zatrzymania i cofnięcia przydziału wszystkich maszyn wirtualnych w zestawie dostępności [przed włączeniem przyspieszonej sieci na dowolnej istniejącej karcie sieciowej](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Cluster Networking (Sieć klastrów)

* Klastry Service Fabric można wdrożyć w istniejącej sieci wirtualnej, wykonując czynności opisane w temacie [Service Fabric wzorców sieci](./service-fabric-patterns-networking.md).

* Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) są zalecane w przypadku typów węzłów, aby ograniczyć ruch przychodzący i wychodzący do klastra. Upewnij się, że wymagane porty są otwarte w sieciowej grupy zabezpieczeń. 

* Typ węzła podstawowego, który zawiera Service Fabric usług systemu nie musi być narażony za pośrednictwem zewnętrznego modułu równoważenia obciążenia i może być narażony przez [wewnętrzny moduł równoważenia obciążenia](./service-fabric-patterns-networking.md#internal-only-load-balancer)

* Użyj [statycznego publicznego adresu IP](./service-fabric-patterns-networking.md#static-public-ip-address-1) dla klastra.

## <a name="network-security-rules"></a>Reguły zabezpieczeń sieci

Podstawowe reguły w tym miejscu są minimalne dla blokady zabezpieczeń w klastrze Service Fabric zarządzanym przez platformę Azure. Nie można otworzyć następujących portów lub zatwierdzeniu adresu IP/adresu URL uniemożliwi prawidłowe działanie klastra i może nie być obsługiwane. W przypadku tego ustawienia reguły jest wymagana wyłącznie do korzystania z [automatycznych uaktualnień obrazu systemu operacyjnego](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md). w przeciwnym razie konieczne będzie otwarcie dodatkowych portów.

### <a name="inbound"></a>Inbound 
|Priorytet   |Nazwa               |Port        |Protokół  |Element źródłowy             |Element docelowy       |Akcja   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |Zezwalaj
|3910       |Klient             |19000       |TCP       |Internet           |VirtualNetwork    |Zezwalaj
|3920       |Klaster            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |Zezwalaj
|3930       |Rzeczywisty          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |Zezwalaj
|3940       |Aplikacja        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |Zezwalaj
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |Zezwalaj
|3960       |RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |Zablokuj
|3970       |Protokół SSH                |22          |TCP       |Internet           |VirtualNetwork    |Zablokuj
|3980       |Niestandardowy punkt końcowy    |80          |TCP       |Internet           |VirtualNetwork    |Zezwalaj
|4100       |Blokuj ruch przychodzący      |443         |Dowolne       |Dowolne                |Dowolne               |Zezwalaj

Więcej informacji na temat reguł zabezpieczeń dla ruchu przychodzącego:

* Na **platformie Azure**. Ten port jest używany przez Service Fabric Explorer do przeglądania i zarządzania klastrem, a także jest używany przez dostawcę zasobów Service Fabric do wysyłania zapytań dotyczących informacji o klastrze w celu wyświetlenia ich w portal zarządzania platformy Azure. Jeśli ten port nie jest dostępny z poziomu dostawcy zasobów Service Fabric, zobaczysz komunikat, taki jak "węzły nie znaleziono" lub "UpgradeServiceNotReachable" w Azure Portal, a lista węzeł i aplikacja będzie wyświetlana jako pusta. Oznacza to, że jeśli chcesz mieć wgląd w klaster na platformie Azure portal zarządzania, moduł równoważenia obciążenia musi ujawniać publiczny adres IP, a sieciowej grupy zabezpieczeń musi zezwalać na ruch przychodzący 19080.  

* **Klient**. Punkt końcowy połączenia klienta dla interfejsów API, takich jak REST/PowerShell/CLI. 

* **Klaster**. Używany do komunikacji między węzłami; nigdy nie powinna być blokowana.

* **Tymczasowych**. Service Fabric używa tych portów jako portów aplikacji, a pozostałe są dostępne dla systemu operacyjnego. Ponadto mapuje ten zakres na istniejący zakres obecny w systemie operacyjnym, dlatego można użyć zakresów podano w przykładzie w tym miejscu. Upewnij się, że różnica między portem początkowym i końcowym wynosi co najmniej 255. Mogą wystąpić konflikty, jeśli różnica jest zbyt niska, ponieważ ten zakres jest współużytkowany z systemem operacyjnym. Aby zobaczyć skonfigurowany zakres portów dynamicznych, uruchom polecenie *netsh int IPv4 show Dynamic port TCP*. Te porty nie są potrzebne w przypadku klastrów systemu Linux.

* **Aplikacja**. Zakres portów aplikacji powinien być wystarczająco duży, aby pokryć wymagania dotyczące punktu końcowego aplikacji. Ten zakres powinien być poza zakresem portów dynamicznych na komputerze, czyli zakresem ephemeralPorts ustawionym w konfiguracji. Service Fabric używa tych portów, gdy wymagane są nowe porty i należy zachować ostrożność otwierania zapory dla tych portów w węzłach.

* Protokół **SMB**. Protokół SMB jest używany przez usługę magazynu ImageStore w przypadku dwóch scenariuszy. Ten port jest wymagany do pobrania pakietów z magazynu ImageStore przez węzły, a także do replikowania ich między replikami. 

* **Protokół RDP**. Opcjonalne, jeśli protokół RDP jest wymagany z Internetu lub VirtualNetwork na potrzeby scenariuszy serwera przesiadkowego. 

* Protokół **SSH**. Opcjonalne, jeśli protokół SSH jest wymagany z Internetu lub VirtualNetwork na potrzeby scenariuszy serwera przesiadkowego.

* **Niestandardowy punkt końcowy**. Przykład dla aplikacji, aby umożliwić dostęp do internetowego punktu końcowego.

### <a name="outbound"></a>Outbound

|Priorytet   |Nazwa               |Port        |Protokół  |Element źródłowy             |Element docelowy       |Akcja   
|---        |---                |---         |---       |---                |---               |---
|3900       |Sieć            |Dowolne         |TCP       |VirtualNetwork     |VirtualNetwork    |Zezwalaj
|3910       |Dostawca zasobów  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Zezwalaj
|3920       |Uaktualnienie            |443         |TCP       |VirtualNetwork     |Internet          |Zezwalaj
|3950       |Blokuj ruch wychodzący     |Dowolne         |Dowolne       |Dowolne                |Dowolne               |Zablokuj

Więcej informacji na temat reguł zabezpieczeń dla ruchu wychodzącego:

* **Sieć**. Kanał komunikacyjny dla podsieci i innych sieci wirtualnych.

* **Dostawca zasobów**. Połączenie przez UpgradeService, aby wykonać wszystkie wdrożenia ARM przez dostawcę zasobów Service Fabric.

* **Uaktualnienie**. Usługa uaktualnienia korzystająca z adresu download.microsoft.com do uzyskiwania bitów, jest wymagana w przypadku uaktualnień na potrzeby instalacji, ponownego obrazu i środowiska uruchomieniowego. Usługa działa z dynamicznymi adresami IP. W scenariuszu usługi równoważenia obciążenia "tylko wewnętrzne" do szablonu należy dodać dodatkowy zewnętrzny moduł równoważenia obciążenia z regułą zezwalającą na ruch wychodzący dla portu 443. Opcjonalnie ten port może być blokowany po pomyślnym zakończeniu instalacji, ale w tym przypadku pakiet uaktualnienia musi być dystrybuowany do węzłów lub trzeba otworzyć port przez krótki okres, a następnie przeprowadzić uaktualnienie ręczne.

Za pomocą zapory platformy Azure z [dziennikiem przepływu sieciowej grupy zabezpieczeń](../network-watcher/network-watcher-nsg-flow-logging-overview.md) i [analizą ruchu](../network-watcher/traffic-analytics.md) można śledzić problemy z blokadą zabezpieczeń. Szablon ARM [Service Fabric z sieciowej grupy zabezpieczeń](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) jest dobrym przykładem do uruchomienia. 


## <a name="application-networking"></a>Sieć aplikacji

* Aby można było uruchomić obciążenia kontenerów systemu Windows, należy użyć [trybu otwartej sieci](./service-fabric-networking-modes.md#set-up-open-networking-mode) do ułatwienia komunikacji między usługami.

* Użyj zwrotnego serwera proxy, takiego jak [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) lub [Service Fabric zwrotnego serwera proxy](./service-fabric-reverseproxy.md) w celu udostępnienia typowych portów aplikacji, takich jak 80 lub 443.

* W przypadku kontenerów systemu Windows hostowanych na maszynach gapped powietrznych, które nie mogą pobierać warstw podstawowych z magazynu w chmurze platformy Azure, Zastąp zachowanie warstwy obcej przy użyciu flagi [--Allow-undystrybucyjne-artefakty](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) w demona platformy Docker.

## <a name="next-steps"></a>Następne kroki

* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Windows Server: [Service Fabric tworzenia klastra dla systemu Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Tworzenie klastra na maszynach wirtualnych lub komputerach z systemem Linux: [Tworzenie klastra systemu Linux](service-fabric-cluster-creation-via-portal.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
