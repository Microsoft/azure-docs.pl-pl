---
title: Uruchom przechodzenie do szczegółów odzyskiwania po awarii NHyper-V do lokacji dodatkowej za pomocą Azure Site Recovery
description: Dowiedz się, jak uruchomić przechodzenie po awarii dla maszyn wirtualnych funkcji Hyper-V w chmurach programu VMM do dodatkowego lokalnego centrum danych przy użyciu Azure Site Recovery.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sideeksh
ms.openlocfilehash: 569af28f5773d843f49dd9c8143b45e308ae142e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87420417"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>Wykonaj drążenie odzyskiwania po awarii dla maszyn wirtualnych funkcji Hyper-V do lokacji dodatkowej


W tym artykule opisano, jak przeprowadzić przechodzenie do szczegółów odzyskiwania po awarii (DR) dla maszyn wirtualnych funkcji Hyper-V zarządzanych w chmurach System Center Virtual Machine Manager V (MM) do pomocniczej lokacji lokalnej przy użyciu [Azure Site Recovery](site-recovery-overview.md).

Uruchamiasz test pracy w trybie failover, aby zweryfikować strategię replikacji, i przeprowadź drążenie odzyskiwania po awarii bez utraty danych ani przestojów. Test pracy w trybie failover nie ma żadnego wpływu na bieżącą replikację ani w środowisku produkcyjnym. 

## <a name="how-do-test-failovers-work"></a>Jak działa test pracy w trybie failover?

Uruchamiasz test pracy w trybie failover z poziomu podstawowego do lokacji dodatkowej. Aby sprawdzić, czy maszyna wirtualna została przełączona w tryb failover, można uruchomić test pracy w trybie awaryjnym bez ustawień w lokacji dodatkowej. Jeśli chcesz sprawdzić, czy tryb failover aplikacji działa zgodnie z oczekiwaniami, musisz skonfigurować sieć i infrastrukturę w dodatkowej lokalizacji.
- Test pracy w trybie failover można uruchomić na jednej maszynie wirtualnej lub w [planie odzyskiwania](site-recovery-create-recovery-plans.md).
- Możesz uruchomić test pracy w trybie failover bez sieci, z istniejącą siecią lub z automatycznie utworzoną siecią. Więcej informacji na temat tych opcji znajduje się w poniższej tabeli.
    - Test pracy w trybie failover można uruchomić bez sieci. Ta opcja jest przydatna, jeśli chcesz po prostu sprawdzić, czy maszyna wirtualna mogła przełączyć się w tryb failover, ale nie będzie można zweryfikować żadnej konfiguracji sieci.
    - Uruchom tryb failover z istniejącą siecią. Zalecamy, aby nie korzystać z sieci produkcyjnej.
    - Uruchom tryb failover i pozwól Site Recovery automatycznie utworzyć sieć testową. W tym przypadku Site Recovery automatycznie utworzy sieć i wyczyści ją po zakończeniu testowego przejścia w tryb failover.
- Należy wybrać punkt odzyskiwania dla testowej pracy w trybie failover: 
    - **Najnowsza przetworzony**: Ta opcja powoduje, że maszyna wirtualna jest w trybie failover do najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
    - **Najnowsza spójna dla aplikacji**: Ta opcja powoduje przełączenie maszyny wirtualnej w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji przetworzonego przez Site Recovery. 
    - **Najnowsze**: Ta opcja najpierw przetwarza wszystkie dane, które zostały wysłane do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny wirtualnej przed przejściem w tryb failover. Ta opcja zapewnia najniższy cel punktu odzyskiwania, ponieważ maszyna wirtualna utworzona po zakończeniu pracy w trybie failover będzie miała wszystkie dane zreplikowane do Site Recovery podczas wyzwolenia trybu failover.
    - **Ostatnio przetworzone wiele maszyn wirtualnych**: dostępne dla planów odzyskiwania, które obejmują co najmniej jedną maszynę wirtualną z włączoną spójnością wielu maszyn wirtualnych. Maszyny wirtualne z włączonym ustawieniem przechodzą w tryb failover do najnowszego wspólnego punktu odzyskiwania z wielowątkową maszyną wirtualną. Inne maszyny wirtualne są przełączane w tryb failover do ostatniego przetworzonego punktu odzyskiwania.
    - **Najnowsza aplikacja obejmująca wiele maszyn wirtualnych — spójna**: Ta opcja jest dostępna w przypadku planów odzyskiwania z co najmniej jedną maszyną wirtualną, na której włączono spójność wielu maszyn wirtualnych. Maszyny wirtualne, które są częścią grupy replikacji, są przełączane w tryb failover do najnowszego wspólnego punktu odzyskiwania spójnego z aplikacjami wielomaszynowo. Inne maszyny wirtualne są przełączane w tryb failover do najnowszego punktu odzyskiwania spójnego z aplikacją.
    - **Niestandardowe**: Użyj tej opcji, aby przełączyć KONKRETNą maszynę wirtualną do określonego punktu odzyskiwania.



## <a name="prepare-networking"></a>Przygotowywanie sieci

Po uruchomieniu testowej pracy w trybie failover zostanie wyświetlony monit o wybranie ustawień sieci dla maszyn z repliką testową, jak zostało to podsumowane w tabeli.

| **Opcja** | **Szczegóły** | |
| --- | --- | --- |
| **Brak** | Testowa maszyna wirtualna jest tworzona na hoście, na którym znajduje się replika maszyny wirtualnej. Nie jest on dodawany do chmury i nie jest połączony z żadną siecią.<br/><br/> Komputer można połączyć z siecią maszyny wirtualnej po jej utworzeniu.| |
| **Użyj istniejącej** | Testowa maszyna wirtualna jest tworzona na hoście, na którym znajduje się replika maszyny wirtualnej. Nie została dodana do chmury.<br/><br/>Utwórz sieć maszyny wirtualnej, która jest odizolowana od sieci produkcyjnej.<br/><br/>Jeśli używasz sieci opartej na sieci VLAN, zalecamy utworzenie oddzielnej sieci logicznej (nieużywanej w środowisku produkcyjnym) w tym celu. Ta sieć logiczna służy do tworzenia sieci maszyn wirtualnych na potrzeby testowania pracy w trybie failover.<br/><br/>Sieć logiczna powinna być skojarzona z co najmniej jedną kartą sieciową wszystkich serwerów funkcji Hyper-V obsługujących maszyny wirtualne.<br/><br/>W przypadku sieci logicznych VLAN lokacje sieciowe dodawane do sieci logicznej powinny być izolowane.<br/><br/>Jeśli używasz sieci logicznej opartej na wirtualizacji sieci systemu Windows, Azure Site Recovery automatycznie tworzy izolowane sieci maszyn wirtualnych. | |
| **Tworzenie sieci** | Tymczasowa sieć testowa jest tworzona automatycznie na podstawie ustawienia określonego w **sieci logicznej** i powiązanych lokacjach sieciowych.<br/><br/> Przełączenie w tryb failover umożliwia tworzenie maszyn wirtualnych.<br/><br/> Tej opcji należy użyć, jeśli plan odzyskiwania używa więcej niż jednej sieci VMNETWORK.<br/><br/> W przypadku korzystania z sieci wirtualizacji sieci systemu Windows ta opcja umożliwia automatyczne tworzenie sieci maszyn wirtualnych z tymi samymi ustawieniami (podsieciami i pulami adresów IP) w sieci maszyny wirtualnej repliki. Te sieci maszyn wirtualnych są czyszczone automatycznie po zakończeniu testowego przejścia w tryb failover.<br/><br/> Testowa maszyna wirtualna jest tworzona na hoście, na którym istnieje maszyna wirtualna repliki. Nie została dodana do chmury.|

### <a name="best-practices"></a>Najlepsze rozwiązania

- Testowanie sieci produkcyjnej powoduje przestoje w przypadku obciążeń produkcyjnych. Podawaj użytkownikom, aby nie korzystali z aplikacji pokrewnych, gdy trwa przechodzenie do odzyskiwania po awarii.

- Sieć testowa nie musi być zgodna z typem sieci logicznej programu VMM używanym do testowania pracy w trybie failover. Jednak niektóre kombinacje nie działają:

     - Jeśli replika korzysta z izolacji opartej na protokole DHCP i sieci VLAN, sieć maszyny wirtualnej repliki nie potrzebuje puli statycznych adresów IP. Korzystanie z wirtualizacji sieci systemu Windows dla testowej pracy w trybie failover nie będzie możliwe, ponieważ żadne pule adresów nie są dostępne. 
        
     - Test pracy w trybie failover nie będzie działał, jeśli sieć repliki nie korzysta z izolacji, a sieć testowa używa wirtualizacji sieci systemu Windows. Wynika to z faktu, że sieć bez izolacji nie ma podsieci wymaganych do utworzenia sieci wirtualizacji sieci systemu Windows.
        
- Zalecamy, aby nie używać sieci wybranej do mapowania sieci na potrzeby testowego przełączania do trybu failover.

- Sposób podłączenia maszyn wirtualnych repliki do mapowanych sieci maszyn wirtualnych po przejściu w tryb failover zależy od konfiguracji sieci maszyn wirtualnych w konsoli programu VMM.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Sieć maszyn wirtualnych skonfigurowana bez izolacji lub izolowania sieci VLAN

Jeśli sieć VMNETWORK jest skonfigurowana w programie VMM bez izolacji lub izolacji sieci VLAN, należy pamiętać o następujących kwestiach:

- Jeśli usługa DHCP jest zdefiniowana dla sieci VMNETWORK, maszyna wirtualna repliki jest połączona z IDENTYFIKATORem sieci VLAN za pomocą ustawień określonych dla lokacji sieciowej w skojarzonej sieci logicznej. Maszyna wirtualna otrzymuje swój adres IP od dostępnego serwera DHCP.
- Nie musisz definiować puli statycznych adresów IP dla docelowej sieci maszyn wirtualnych. Jeśli pula statycznych adresów IP jest używana w sieci maszyn wirtualnych, maszyna wirtualna repliki jest połączona z IDENTYFIKATORem sieci VLAN za pomocą ustawień określonych dla lokacji sieciowej w skojarzonej sieci logicznej.
- Maszyna wirtualna otrzymuje swój adres IP z puli zdefiniowanej dla sieci VMNETWORK. Jeśli pula statycznych adresów IP nie jest zdefiniowana w docelowej sieci maszyn wirtualnych, alokacja adresów IP zakończy się niepowodzeniem. Utwórz pulę adresów IP zarówno na źródłowym, jak i docelowym serwerze programu VMM, który będzie używany do ochrony i odzyskiwania.

### <a name="vm-network-with-windows-network-virtualization"></a>Sieć VMNETWORK z wirtualizacją sieci systemu Windows

Jeśli sieć VMNETWORK jest skonfigurowana w programie VMM przy użyciu wirtualizacji sieci systemu Windows, należy zwrócić uwagę na następujące kwestie:

- Należy zdefiniować pulę statyczną dla docelowej sieci maszyn wirtualnych, niezależnie od tego, czy źródłowa sieć maszyn wirtualnych jest skonfigurowana do korzystania z protokołu DHCP czy puli statycznych adresów IP. 
- W przypadku zdefiniowania protokołu DHCP docelowy serwer programu VMM działa jako serwer DHCP i udostępnia adres IP z puli zdefiniowanej dla docelowej sieci maszyn wirtualnych.
- Jeśli dla serwera źródłowego zdefiniowano użycie puli statycznych adresów IP, docelowy serwer programu VMM przydzieli adres IP z puli. W obu przypadkach alokacja adresów IP zakończy się niepowodzeniem, jeśli nie zdefiniowano puli statycznych adresów IP.



## <a name="prepare-the-infrastructure"></a>Przygotowywanie infrastruktury

Jeśli po prostu chcesz sprawdzić, czy maszyna wirtualna może działać w trybie failover, możesz uruchomić test pracy awaryjnej bez infrastruktury. Aby przeprowadzić pełne przechodzenie do trybu failover aplikacji testowej, należy przygotować infrastrukturę w lokacji dodatkowej:

- Jeśli uruchamiasz test pracy w trybie failover przy użyciu istniejącej sieci, przygotuj Active Directory, DHCP i DNS w tej sieci.
- W przypadku uruchomienia testowej pracy w trybie failover z opcją utworzenia sieci VMNETWORK automatycznie należy dodać zasoby infrastruktury do automatycznie utworzonej sieci, przed uruchomieniem testowej pracy w trybie failover. W planie odzyskiwania można to ułatwić, dodając ręczny krok przed grupą 1 w planie odzyskiwania, który ma być używany na potrzeby testu pracy w trybie failover. Następnie przed uruchomieniem testowej pracy w trybie failover należy dodać zasoby infrastruktury do automatycznie utworzonej sieci.


### <a name="prepare-dhcp"></a>Przygotowywanie protokołu DHCP
Jeśli maszyny wirtualne występujące w testowaniu pracy w trybie failover korzystają z protokołu DHCP, należy utworzyć testowy serwer DHCP w sieci izolowanej na potrzeby testu pracy w trybie failover.


### <a name="prepare-active-directory"></a>Przygotuj Active Directory
Aby uruchomić test pracy w trybie failover na potrzeby testowania aplikacji, w środowisku testowym potrzebna jest kopia środowiska produkcyjnego Active Directory. Aby uzyskać więcej informacji, zapoznaj się z [uwagami dotyczącymi testowania pracy w trybie failover Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Przygotowywanie systemu DNS
Przygotuj serwer DNS dla testowej pracy w trybie failover w następujący sposób:

* **DHCP**: Jeśli maszyny wirtualne korzystają z protokołu DHCP, na testowanym serwerze DHCP należy zaktualizować adres IP testowego DNS. Jeśli używasz typu sieci wirtualizacji sieci systemu Windows, serwer programu VMM działa jako serwer DHCP. W związku z tym adres IP DNS powinien zostać zaktualizowany w sieci testowej pracy w trybie failover. W takim przypadku maszyny wirtualne rejestrują się na odpowiednim serwerze DNS.
* **Adres statyczny**: Jeśli maszyny wirtualne używają statycznego adresu IP, należy zaktualizować adres IP testowego serwera DNS w sieci testowej pracy w trybie failover. Może być konieczne zaktualizowanie systemu DNS przy użyciu adresu IP testowanych maszyn wirtualnych. W tym celu można użyć następującego przykładowego skryptu:

  ```powershell
  Param(
  [string]$Zone,
  [string]$name,
  [string]$IP
  )
  $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
  $newrecord = $record.clone()
  $newrecord.RecordData[0].IPv4Address  =  $IP
  Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
  ```

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

W tej procedurze opisano, jak uruchomić test pracy w trybie failover dla planu odzyskiwania. Alternatywnie można uruchomić tryb failover dla pojedynczej maszyny wirtualnej na karcie **Virtual Machines** .

1. Wybierz pozycję **plany odzyskiwania**  >  *recoveryplan_name*. Kliknij **pozycję**test pracy w trybie failover  >  **Test Failover**.
2. W bloku **Testowanie pracy w trybie failover** Określ, jak maszyny wirtualne repliki mają być połączone z sieciami po testowym przejściu w tryb failover.
3. Śledź postęp pracy w trybie failover na karcie **zadania** .
4. Po zakończeniu pracy w trybie failover Sprawdź, czy maszyny wirtualne zostały pomyślnie uruchomione.
5. Gdy skończysz, kliknij pozycję **Oczyść test pracy w trybie failover** w planie odzyskiwania. W obszarze **uwagi**Zarejestruj i Zapisz wszelkie obserwacje związane z testem pracy w trybie failover. Ten krok usuwa wszystkie maszyny wirtualne i sieci, które zostały utworzone przez Site Recovery podczas testu pracy w trybie failover. 

![Testowanie pracy w trybie failover](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> Adres IP określony dla maszyny wirtualnej podczas testu pracy w trybie failover to ten sam adres IP, który będzie odbierany przez maszynę wirtualną w przypadku planowanego lub nieplanowanego przejścia w tryb failover (zakładając, że adres IP jest dostępny w sieci testowej pracy w trybie failover). Jeśli ten sam adres IP nie jest dostępny w sieci testowej pracy w trybie failover, maszyna wirtualna otrzyma inny adres IP, który jest dostępny w sieci testowej pracy w trybie failover.



### <a name="run-a-test-failover-to-a-production-network"></a>Uruchamianie testowego przejścia w tryb failover do sieci produkcyjnej

Zalecamy, aby nie uruchamiać testowej pracy w trybie failover do sieci lokacji odzyskiwania produkcyjnego określonej podczas mapowania sieci. Ale jeśli konieczne jest zweryfikowanie kompleksowej łączności sieciowej w maszynie wirtualnej w trybie failover, należy pamiętać o następujących kwestiach:

* Upewnij się, że podstawowa maszyna wirtualna jest ZAMKNIĘTA podczas przeprowadzania testowej pracy w trybie failover. Jeśli nie, dwie maszyny wirtualne o tej samej tożsamości będą działać w tej samej sieci w tym samym czasie. Ta sytuacja może prowadzić do niepożądanych konsekwencji.
* Wszelkie zmiany wprowadzane do maszyn wirtualnych z testem pracy w trybie failover zostaną utracone po oczyszczeniu maszyny wirtualnej testowej pracy w trybie failover. Te zmiany nie są replikowane z powrotem do podstawowych maszyn wirtualnych.
* Testowanie podobne do tego prowadzi do przestojów w aplikacji produkcyjnej. Poproszenie użytkowników aplikacji o nieużywanie aplikacji po przejściu do szczegółów odzyskiwania po awarii.  


## <a name="next-steps"></a>Następne kroki
Po pomyślnym uruchomieniu testowania odzyskiwania po [awarii można uruchomić pełną pracę w trybie failover](site-recovery-failover.md).



