---
title: Skonfiguruj adresowanie IP po przejściu w tryb failover do lokacji dodatkowej za pomocą Azure Site Recovery
description: Opisuje sposób konfigurowania adresowania IP na potrzeby nawiązywania połączenia z maszynami wirtualnymi w dodatkowej lokacji lokalnej po odzyskiwaniu po awarii i przejściu do trybu failover za pomocą Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 43942c20a353ff69383f3e721679e4c95ab9d230
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87495947"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>Konfigurowanie adresowania IP w celu nawiązania połączenia z dodatkową lokacją lokalną po przejściu do trybu failover

Po przełączeniu w tryb failover maszyn wirtualnych funkcji Hyper-V w chmurach System Center Virtual Machine Manager (VMM) do lokacji dodatkowej należy mieć możliwość łączenia się z maszynami wirtualnymi replik. W tym artykule opisano, jak to zrobić. 

## <a name="connection-options"></a>Opcje połączenia

Po przejściu w tryb failover istnieje kilka sposobów obsługi adresowania IP dla maszyn wirtualnych repliki: 

- **Zachowaj ten sam adres IP po** przejściu w tryb failover: w tym scenariuszu REPLIKOWANA maszyna wirtualna ma ten sam adres IP co podstawowa maszyna wirtualna. Upraszcza to problemy związane z siecią po przejściu w tryb failover, ale wymaga pewnego działania infrastruktury.
- **Użyj innego adresu IP po** przejściu w tryb failover: w tym SCENARIUSZU maszyna wirtualna Pobiera nowy adres IP po przejściu do trybu failover. 
 

## <a name="retain-the-ip-address"></a>Zachowywanie adresu IP

Jeśli chcesz zachować adresy IP z lokacji głównej, po przejściu do trybu failover do lokacji dodatkowej, możesz:

- Wdróż rozciągnięta podsieć między lokacjami podstawowymi i dodatkowymi.
- Wykonaj pełną podsieć w trybie failover z lokacji podstawowej do dodatkowej. Należy zaktualizować trasy, aby wskazać nową lokalizację adresów IP.


### <a name="deploy-a-stretched-subnet"></a>Wdróż rozciągnięta podsieć

W konfiguracji rozciąganej podsieć jest dostępna jednocześnie zarówno w lokacji głównej, jak i dodatkowej. W przypadku przełączenia maszyny i konfiguracji adresu IP (warstwy 3) do lokacji dodatkowej sieć automatycznie kieruje ruch do nowej lokalizacji. 

- Z perspektywy warstwy 2 (warstwy łącza danych) potrzebny jest sprzęt sieciowy, który może zarządzać rozciągniętymi sieciami VLAN.
- Dzięki rozciągnięciu sieci VLAN potencjalna domena błędów rozciąga się na obie lokacje. To single point of failure. Chociaż jest to mało prawdopodobne, w takim scenariuszu może nie być możliwe odizolowanie zdarzenia, takiego jak burza emisji. 


### <a name="fail-over-a-subnet"></a>Przełączenie w tryb failover podsieci

Całą podsieć można przełączyć w tryb failover, aby uzyskać zalety rozciągniętej podsieci, bez jej rzeczywistego rozciągania. W tym rozwiązaniu podsieć jest dostępna w lokacji źródłowej lub docelowej, ale nie jednocześnie.

- Aby zachować przestrzeń adresów IP w przypadku przejścia w tryb failover, można programowo rozmieścić infrastrukturę routera, aby przenieść podsieci z jednej lokacji do drugiej.
- W przypadku przejścia w tryb failover podsieci są przenoszone ze skojarzonych maszyn wirtualnych.
- Główną wadą tego podejścia jest to, że w przypadku awarii należy przenieść całą podsieć.

#### <a name="example"></a>Przykład

Oto przykład pełnej podsieci trybu failover. 

- Przed przejściem w tryb failover lokacja główna ma aplikacje działające w podsieci 192.168.1.0/24.
- Podczas pracy w trybie failover wszystkie maszyny wirtualne w tej podsieci są przenoszone do lokacji dodatkowej i zachowują ich adresy IP. 
- Trasy między wszystkimi lokacjami należy zmodyfikować w taki sposób, aby odzwierciedlały fakt, że wszystkie maszyny wirtualne w podsieci 192.168.1.0/24 zostały przeniesione do lokacji dodatkowej.

Poniższe grafiki ilustrują podsieci przed i po przejściu do trybu failover.


**Przed przejściem w tryb failover**

![Diagram przedstawiający podsieci przed przejściem w tryb failover.](./media/hyper-v-vmm-networking/network-design2.png)

**Po przejściu w tryb failover**

![Diagram przedstawiający podsieci po przejściu do trybu failover.](./media/hyper-v-vmm-networking/network-design3.png)

Po przejściu w tryb failover Site Recovery przydziela adres IP dla każdego interfejsu sieciowego na maszynie wirtualnej. Adres jest przypisywany z puli statycznych adresów IP w odpowiedniej sieci dla każdego wystąpienia maszyny wirtualnej.

- Jeśli pula adresów IP w lokacji dodatkowej jest taka sama jak w lokacji źródłowej, Site Recovery przydziela ten sam adres IP (źródłowej maszyny wirtualnej) do maszyny wirtualnej repliki. Adres IP jest zastrzeżony w programie VMM, ale nie jest ustawiony jako adres IP trybu failover na hoście funkcji Hyper-V. Adres IP trybu failover na hoście funkcji Hyper-v jest ustawiany tuż przed przełączeniem w tryb failover.
- Jeśli ten sam adres IP jest niedostępny, Site Recovery przydziela inny dostępny adres IP z puli.
- Jeśli maszyny wirtualne korzystają z protokołu DHCP, Site Recovery nie zarządzają adresami IP. Należy sprawdzić, czy serwer DHCP w lokacji dodatkowej może przydzielić adresy z tego samego zakresu, w którym znajduje się lokacja źródłowa.

### <a name="validate-the-ip-address"></a>Weryfikowanie adresu IP

Po włączeniu ochrony maszyny wirtualnej można użyć następującego przykładowego skryptu do zweryfikowania adresu przypisanego do maszyny wirtualnej. Ten adres IP jest ustawiany jako adres IP trybu failover i przypisany do maszyny wirtualnej w momencie przejścia w tryb failover:

```powershell
$vm = Get-SCVirtualMachine -Name <VM_NAME>
$na = $vm[0].VirtualNetworkAdapters>
$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
$ip.address
```

## <a name="use-a-different-ip-address"></a>Użyj innego adresu IP

W tym scenariuszu adresy IP maszyn wirtualnych, które są przełączane w tryb failover, są zmieniane. Wadą tego rozwiązania jest wymagana konserwacja.  Może być konieczne zaktualizowanie wpisów DNS i pamięci podręcznej. Może to spowodować przestoje, co może być skorygowane w następujący sposób:

- Używaj wartości niskiego czasu wygaśnięcia dla aplikacji intranetowych.
- Aby zaktualizować serwer DNS, należy użyć następującego skryptu w planie odzyskiwania Site Recovery. Nie jest potrzebny skrypt, jeśli używasz dynamicznej rejestracji w systemie DNS.

    ```powershell
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Przykład 

W tym przykładzie mamy różne adresy IP w lokacjach głównych i dodatkowych, a także trzecią lokację, z której można uzyskać dostęp do aplikacji hostowanych w lokacji głównej lub odzyskania.

- Przed przejściem w tryb failover aplikacje są hostowaną podsiecią 192.168.1.0/24 w lokacji głównej.
- Po przejściu w tryb failover aplikacje są konfigurowane w podsieci 172.16.1.0/24 w lokacji dodatkowej.
- Wszystkie trzy Lokacje mogą uzyskiwać dostęp do siebie nawzajem.
- Po przejściu w tryb failover aplikacje zostaną przywrócone w podsieci odzyskiwania.
- W tym scenariuszu nie ma potrzeby przełączania całej podsieci do trybu failover i nie są wymagane żadne zmiany w celu ponownego skonfigurowania sieci VPN lub tras sieciowych. W trybie failover i niektórych aktualizacjach DNS upewnij się, że aplikacje są nadal dostępne.
- Jeśli serwer DNS jest skonfigurowany tak, aby zezwalał na aktualizacje dynamiczne, maszyny wirtualne będą się rejestrować przy użyciu nowego adresu IP, gdy zostaną uruchomione po przejściu do trybu failover.

**Przed przejściem w tryb failover**

![Diagram przedstawiający różne adresy IP przed przełączeniem w tryb failover.](./media/hyper-v-vmm-networking/network-design10.png)

**Po przejściu w tryb failover**

![Diagram przedstawiający różne adresy IP po przejściu w tryb failover.](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Następne kroki

[Uruchamianie trybu failover](hyper-v-vmm-failover-failback.md)

