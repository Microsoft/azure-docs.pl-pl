---
title: Przygotowywanie maszyn wirtualnych VMware do ochrony i powrotu po awarii za pomocą Azure Site Recovery
description: Przygotowanie do powrotu po awarii maszyn wirtualnych VMware po przejściu w tryb failover przy użyciu Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: f3e352577d65bda021f886d6612c8c0101922003
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727510"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Przygotowanie do włączania ponownej ochrony i powrotu po awarii maszyn wirtualnych VMware

Po przejściu w [tryb failover](site-recovery-failover.md) lokalnych maszyn wirtualnych VMware lub serwerów fizycznych na platformę Azure należy ponownie włączyć ochronę maszyn wirtualnych platformy Azure utworzonych po przejściu do trybu failover, aby umożliwić ich replikację do lokacji lokalnej. W przypadku replikacji z platformy Azure do miejsca lokalnego można wrócić po awarii, uruchamiając tryb failover z platformy Azure do lokalnego, gdy wszystko będzie gotowe.

Przed kontynuowaniem zapoznaj się z tym wideo, aby zapoznać się z informacjami dotyczącymi powrotu po awarii z platformy Azure do lokacji lokalnej.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Składniki reprotection/powrót po awarii

Przed ponownym włączeniem ochrony i powrotem po awarii z platformy Azure należy wprowadzić wiele składników i ustawień.

| **Składnik**| **Szczegóły** |
| --- | --- |
| **Lokalny serwer konfiguracji** | Lokalny serwer konfiguracji musi być uruchomiony i połączony z platformą Azure.<br/><br/> Maszyna wirtualna, do której nastąpi powrót, musi znajdować się w bazie danych serwera konfiguracji. Jeśli działanie ma wpływ na serwer konfiguracji, Przywróć go przy użyciu tego samego adresu IP, aby upewnić się, że powrót po awarii.<br/><br/>  Jeśli adresy IP replikowanych maszyn zostały zachowane w trybie failover, należy nawiązać połączenie między maszynami wirtualnymi platformy Azure i kartą sieciową powrotu po awarii serwera konfiguracji. W przypadku zachowanych adresów IP serwer konfiguracji potrzebuje dwóch kart sieciowych — jednego dla połączenia z maszyną źródłową, a drugi na potrzeby łączności z powrotem po awarii platformy Azure. Pozwala to uniknąć nakładania się zakresów adresów podsieci dla źródła i maszyn wirtualnych w trybie failover. |
| **Serwer przetwarzania na platformie Azure** | Aby móc wrócić do lokacji lokalnej, musisz mieć serwer przetwarzania na platformie Azure.<br/><br/> Serwer przetwarzania otrzymuje dane z chronionej maszyny wirtualnej platformy Azure i wysyła je do lokacji lokalnej.<br/><br/> Potrzebna jest sieć o małym opóźnieniu między serwerem przetwarzania a chronioną maszyną wirtualną, dlatego zalecamy wdrożenie serwera przetwarzania na platformie Azure w celu zwiększenia wydajności replikacji.<br/><br/> W celu sprawdzenia koncepcji można użyć lokalnego serwera przetwarzania i ExpressRoute z prywatną komunikację równorzędną.<br/><br/> Serwer przetwarzania powinien znajdować się w sieci platformy Azure, w której znajduje się maszyna wirtualna w trybie failover. Serwer przetwarzania musi być również w stanie komunikować się z lokalnym serwerem konfiguracji i główny serwer docelowy. |
| **Oddzielny główny serwer docelowy** | Główny serwer docelowy otrzymuje dane powrotu po awarii, a domyślnie główny serwer docelowy systemu Windows jest uruchamiany na lokalnym serwerze konfiguracji.<br/><br/> Główny serwer docelowy może mieć do 60 dysków. Maszyny wirtualne, których nie powiodło się, mają więcej niż zbiorową łączną liczbę dysków 60 lub w przypadku niepowodzenia odtwarzania dużych ilości ruchu, należy utworzyć oddzielny główny serwer docelowy na potrzeby powrotu po awarii.<br/><br/> Jeśli maszyny są gromadzone w grupie replikacji na potrzeby spójności między MASZYNami wirtualnymi, wszystkie maszyny wirtualne muszą być w systemie Windows lub muszą mieć wszystko Linux. Dlaczego? Ponieważ wszystkie maszyny wirtualne w grupie replikacji muszą korzystać z tego samego głównego serwera docelowego, a główny serwer docelowy musi mieć ten sam system operacyjny (z tą samą lub nowszą wersją) niż te dla replikowanych maszyn.<br/><br/> Główny serwer docelowy nie powinien mieć żadnych migawek na swoich dyskach, w przeciwnym razie nie będzie można wykonać operacji powrotu po awarii.<br/><br/> Główny element docelowy nie może mieć kontrolera SCSI Paravirtual. Kontroler może być tylko kontrolerem logiki LSI. W przypadku braku kontrolera logiki LSI nie jest przeprowadzana ochrona. |
| **Zasady replikacji w przypadku powrotu po awarii** | Aby przeprowadzić replikację z powrotem do lokacji lokalnej, potrzebne są zasady powrotu po awarii. Te zasady są tworzone automatycznie podczas tworzenia zasad replikacji na platformie Azure.<br/><br/> Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. Jest ustawiony na próg punktu odzyskiwania wynoszący 15 minut, czas trwania okresu trwania przez 24 godziny, a częstotliwość tworzenia migawek na poziomie aplikacji wynosi 60 minut. Nie można edytować zasad. |
| **Prywatna Komunikacja równorzędna sieci VPN typu lokacja-lokacja/ExpressRoute** | Ponownej ochrony i powrotu po awarii wymaga połączenia sieci VPN typu lokacja-lokacja lub ExpressRoute prywatnej komunikacji równorzędnej w celu replikowania danych. |


## <a name="ports-for-reprotectionfailback"></a>Porty do ochrony/powrotu po awarii

Aby można było ponownie chronić/powrót po awarii, należy otworzyć kilka portów. Poniższa ilustracja przedstawia porty i ponownie Chroń/powrót po awarii.

![Porty trybu failover i powrotu po awarii](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Wdrażanie serwera przetwarzania na platformie Azure

1. [Skonfiguruj serwer przetwarzania](vmware-azure-set-up-process-server-azure.md) na platformie Azure na potrzeby powrotu po awarii.
2. Upewnij się, że maszyny wirtualne platformy Azure mogą nawiązać połączenie z serwerem przetwarzania. 
3. Upewnij się, że połączenie sieci VPN typu lokacja-lokacja lub sieć prywatnej komunikacji równorzędnej ExpressRoute ma wystarczającą przepustowość, aby wysyłać dane z serwera przetwarzania do lokacji lokalnej.

## <a name="deploy-a-separate-master-target-server"></a>Wdróż oddzielny główny serwer docelowy

1. Zanotuj [wymagania i ograniczenia dotyczące](#reprotectionfailback-components)głównego serwera docelowego.
2. Utwórz główny serwer docelowy z [systemem Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) lub [Linux](vmware-azure-install-linux-master-target.md) , aby dopasować system operacyjny maszyn wirtualnych do ponownego włączenia ochrony i powrotu po awarii.
3. Upewnij się, że nie używasz usługi Storage vMotion na głównym serwerze docelowym lub powrót po awarii może zakończyć się niepowodzeniem. Nie można uruchomić maszyny wirtualnej, ponieważ dyski nie są dostępne.
    - Aby tego uniknąć, Wyklucz główny serwer docelowy z listy vMotion.
    - Jeśli główny obiekt docelowy przeprowadzi zadanie vMotion magazynu po ponownej ochronie, chronione dyski maszyn wirtualnych dołączone do głównego serwera docelowego są migrowane do obiektu docelowego zadania vMotion. Próba odłączenia dysku nie powiedzie się, ponieważ nie znaleziono dysków. Trudno znaleźć dyski na kontach magazynu. W takim przypadku Znajdź je ręcznie i Dołącz je do maszyny wirtualnej. Następnie można uruchomić lokalną maszynę wirtualną.

4. Dodaj dysk przechowywania do istniejącego głównego serwera docelowego systemu Windows. Dodaj nowy dysk i sformatuj dysk. Dysk przechowywania służy do zatrzymania punktu w czasie, gdy maszyna wirtualna jest replikowana z powrotem do lokacji lokalnej. Zwróć uwagę na te kryteria. Jeśli nie są spełnione, dysk nie znajduje się na liście dla głównego serwera docelowego:
    - Wolumin nie jest używany do żadnego innego celu, takiego jak obiekt docelowy replikacji i nie jest w trybie blokady.
    - Wolumin nie jest woluminem pamięci podręcznej. Niestandardowy wolumin instalacyjny serwera przetwarzania i głównego elementu docelowego nie kwalifikuje się do woluminu przechowywania. Gdy serwer przetwarzania i główny element docelowy są zainstalowane w woluminie, wolumin jest woluminem pamięci podręcznej głównego serwera docelowego.
    - Typ systemu plików woluminu nie jest w systemie FAT lub FAT32.
    - Pojemność woluminu jest różna od zera.
    - Domyślnym woluminem przechowywania dla systemu Windows jest wolumin R.
    - Domyślnym woluminem przechowywania dla systemu Linux jest/mnt/Retention.

5. Dodaj dysk, jeśli używasz istniejącego serwera przetwarzania. Nowy dysk musi spełniać wymagania z ostatniego kroku. Jeśli dysk przechowywania nie istnieje, nie jest wyświetlany na liście rozwijanej wyboru w portalu. Po dodaniu dysku do lokalnego głównego serwera docelowego potrwa to 15 minut, zanim dysk zostanie wyświetlony w wybranym portalu. Można odświeżyć serwer konfiguracji, jeśli dysk nie jest wyświetlany po 15 minutach.
6. Zainstaluj narzędzia VMware lub narzędzia Otwórz maszynę wirtualną na głównym serwerze docelowym. Bez narzędzi nie można wykryć magazynów danych na hoście ESXi głównego elementu docelowego.
7. Ustaw dysk. EnableUUID = true ustawienia w parametrach konfiguracji głównego docelowego maszyny wirtualnej w programie VMware. Jeśli ten wiersz nie istnieje, Dodaj go. To ustawienie jest wymagane, aby zapewnić spójny identyfikator UUID w VMDK, tak aby był poprawnie instalowany.
8. Sprawdź wymagania dotyczące dostępu vCenter Server:
    - Jeśli maszyna wirtualna, do której nastąpi powrót po awarii, znajduje się na hoście ESXi zarządzanym przez VMware vCenter Server, główny serwer docelowy musi mieć dostęp do lokalnego pliku maszyny wirtualnej maszyn wirtualnych (VMDK) w celu zapisania replikowanych danych na dyskach maszyny wirtualnej. Upewnij się, że lokalny magazyn danych maszyny wirtualnej jest zainstalowany na głównym hoście docelowym z dostępem do odczytu i zapisu.
    - Jeśli maszyna wirtualna nie znajduje się na hoście ESXi zarządzanym przez VMware vCenter Server, Site Recovery tworzy nową maszynę wirtualną podczas jej ochrony. Ta maszyna wirtualna jest tworzona na hoście ESXi, na którym tworzysz maszynę wirtualną głównego serwera docelowego. Wybierz Host ESXi uważnie, aby utworzyć maszynę wirtualną na hoście, którego chcesz użyć. Dysk twardy maszyny wirtualnej musi znajdować się w magazynie danych dostępnym dla hosta, na którym uruchomiony jest główny serwer docelowy.
    - Inna opcja, jeśli lokalna maszyna wirtualna już istnieje dla powrotu po awarii, jest usuwana przed powrotem po awarii. Powrót po awarii powoduje utworzenie nowej maszyny wirtualnej na tym samym hoście co główny docelowy Host ESXi. Po powrocie po awarii do innej lokalizacji dane są odzyskiwane do tego samego magazynu danych i tego samego hosta ESXi, który jest używany przez lokalny główny serwer docelowy.
9. W przypadku odwracania maszyn fizycznych do maszyn wirtualnych VMware należy przeprowadzić odnajdywanie hosta, na którym jest uruchomiony główny serwer docelowy, zanim będzie można ponownie włączyć ochronę maszyny.
10. Sprawdź, czy host ESXi, na którym znajduje się główna docelowa maszyna wirtualna, ma dołączony co najmniej jeden magazyn danych maszyny wirtualnej (VMFS). Jeśli nie są dołączone żadne magazyny danych VMFS, dane wejściowe w ustawieniach ochrony są puste i nie będzie można ich wykonać.


## <a name="next-steps"></a>Następne kroki

Ponowne [Włączanie ochrony](vmware-azure-reprotect.md) maszyny wirtualnej.
