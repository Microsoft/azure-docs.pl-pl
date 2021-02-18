---
title: Opis ponownego uruchomienia systemu dla maszyny wirtualnej platformy Azure | Microsoft Docs
description: Wyświetla listę zdarzeń, które mogą spowodować ponowne uruchomienie maszyny wirtualnej
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c3dcb4a7b92f5aab1336e49a7d299614855b48fd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591156"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Opis ponownego uruchomienia systemu dla maszyny wirtualnej platformy Azure

Usługi Azure Virtual Machines (VM) mogą czasami przeprowadzić ponowne uruchomienie w niewidocznym przypadku, bez oznak zainicjowania operacji ponownego rozruchu. W tym artykule wymieniono akcje i zdarzenia, które mogą spowodować ponowne uruchomienie maszyn wirtualnych oraz szczegółowe informacje na temat sposobu uniknięcia nieoczekiwanych problemów z ponownym uruchomieniem lub zmniejszenia wpływu takich problemów.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurowanie maszyn wirtualnych pod kątem wysokiej dostępności

Najlepszym sposobem na ochronę aplikacji działającej na platformie Azure przed ponownym uruchomieniem maszyny wirtualnej i przestojem jest skonfigurowanie maszyn wirtualnych pod kątem wysokiej dostępności.

Aby zapewnić ten poziom nadmiarowości aplikacji, zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Ta konfiguracja gwarantuje, że podczas planowanego lub nieplanowanego zdarzenia konserwacji jest dostępna co najmniej jedna maszyna wirtualna i spełnia warunki [umowy SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/)na 99,95%.

Aby uzyskać więcej informacji na temat zestawów dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych](../manage-availability.md)

## <a name="resource-health-information"></a>Informacje Resource Health

Azure Resource Health to usługa, która uwidacznia prawidłowość poszczególnych zasobów platformy Azure i zapewnia wskazówki umożliwiające podejmowanie problemów. W środowisku chmury, w którym nie można bezpośrednio uzyskać dostępu do serwerów lub elementów infrastruktury, celem Resource Health jest skrócenie czasu poświęcanego na rozwiązywanie problemów. W szczególności celem jest skrócenie czasu poświęcanego na ustalenie, czy katalog główny problemu występuje w aplikacji, czy też w ramach platformy Azure. Aby uzyskać więcej informacji, zobacz [Omówienie i używanie Resource Health](../../service-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Akcje i zdarzenia, które mogą spowodować ponowne uruchomienie maszyny wirtualnej

### <a name="planned-maintenance"></a>Planowana konserwacja

Microsoft Azure okresowo przeprowadza aktualizacje na całym świecie, aby zwiększyć niezawodność, wydajność i bezpieczeństwo infrastruktury hosta, która jest zależna od maszyn wirtualnych. Wiele z tych aktualizacji, w tym zachowywanie pamięci, jest wykonywane bez wpływu na maszyny wirtualne lub usługi w chmurze.

Jednak niektóre aktualizacje wymagają ponownego uruchomienia. W takich przypadkach maszyny wirtualne są zamykane podczas stosowania poprawek infrastruktury, a następnie maszyny wirtualne zostaną uruchomione ponownie.

Aby zrozumieć, jaka jest planowana konserwacja platformy Azure i jak może ona mieć wpływ na dostępność maszyn wirtualnych z systemem Linux, zobacz artykuły wymienione tutaj. Artykuły zawierają podstawowe informacje na temat procesu planowanej konserwacji platformy Azure oraz sposobu planowania tej konserwacji, aby jeszcze bardziej ograniczyć jej wpływ.

- [Planowana konserwacja maszyn wirtualnych platformy Azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)
- [How to schedule planned maintenance on Azure VMs (Jak zaplanować planowaną konserwację na maszynach wirtualnych platformy Azure)](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)

### <a name="memory-preserving-updates"></a>Aktualizacje pozwalające zachować stan pamięci

W przypadku tej klasy aktualizacji w Microsoft Azure użytkownicy nie mają wpływu na uruchomione maszyny wirtualne. Wiele z tych aktualizacji dotyczy składników lub usług, które można zaktualizować bez zakłócania pracy uruchomionego wystąpienia. Niektóre z nich to aktualizacje infrastruktury platformy w systemie operacyjnym hosta, które można zastosować bez ponownego uruchamiania maszyn wirtualnych.

Te aktualizacje pozwalające zachować stan pamięci są przeprowadzane za pomocą technologii umożliwiającej migrację na żywo w miejscu. Gdy jest aktualizowana, maszyna wirtualna jest umieszczana w stanie *wstrzymania* . Pozwala to zachować stan pamięci RAM, gdy w podstawowym systemie operacyjnym hosta wprowadzane są niezbędne aktualizacje i poprawki. Maszyna wirtualna jest wznawiana w ciągu 30 sekund od momentu wstrzymania. Po wznowieniu maszyny wirtualnej jej zegar jest automatycznie synchronizowany.

Ze względu na krótki okres wstrzymania wdrażanie aktualizacji za pośrednictwem tego mechanizmu znacznie zmniejsza wpływ na maszyny wirtualne. Jednak nie wszystkie aktualizacje można wdrożyć w ten sposób. 

Aktualizacje wielu wystąpień (dla maszyn wirtualnych w zestawie dostępności) są stosowane dla jednej domeny aktualizacji jednocześnie.

> [!NOTE]
> Na maszynach z systemem Linux, na których znajdują się stare wersje jądra, ma wpływ awaryjnego jądra w trakcie tej metody aktualizacji. Aby uniknąć tego problemu, zaktualizuj jądro do wersji 3.10.0-327.10.1 lub nowszej. Aby uzyskać więcej informacji, zapoznaj się z [maszyną wirtualną platformy Azure z systemem Linux na rozruchem jądra opartym na 3,10 po uaktualnieniu węzła hosta](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Zainicjowane przez użytkownika akcje ponownego uruchomienia lub zamknięcia

W przypadku ponownego uruchomienia z Azure Portal, Azure PowerShell, interfejsu wiersza polecenia lub interfejsu API REST można znaleźć zdarzenie w [dzienniku aktywności platformy Azure](../../azure-monitor/essentials/platform-logs-overview.md).

Jeśli wykonujesz akcję z systemu operacyjnego maszyny wirtualnej, możesz znaleźć zdarzenie w dziennikach systemu.

Inne scenariusze, które zwykle powodują ponowne uruchomienie maszyny wirtualnej, obejmują wiele akcji zmiany konfiguracji. Zobaczysz zwykle komunikat ostrzegawczy informujący o tym, że wykonanie określonej akcji spowoduje ponowne uruchomienie maszyny wirtualnej. Przykładami mogą być wszystkie operacje zmiany rozmiaru maszyny wirtualnej, zmiana hasła konta administracyjnego i ustawienie statycznego adresu IP.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center i Windows Update

Azure Security Center monitoruje codzienne maszyny wirtualne z systemami Windows i Linux pod kątem braku aktualizacji systemu operacyjnego. Security Center pobiera listę dostępnych aktualizacji zabezpieczeń i krytycznych z Windows Update lub Windows Server Update Services (WSUS), w zależności od tego, która usługa została skonfigurowana na maszynie wirtualnej z systemem Windows. Security Center również sprawdza najnowsze aktualizacje dla systemów Linux. Jeśli maszyna wirtualna nie ma aktualizacji systemu, Security Center zaleca zastosowanie aktualizacji systemu. Te aktualizacje systemu są kontrolowane przez Security Center w Azure Portal. Po zastosowaniu niektórych aktualizacji może być wymagane ponowne uruchomienie maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [stosowanie aktualizacji systemu w Azure Security Center](../../security-center/asset-inventory.md).

Podobnie jak w przypadku serwerów lokalnych, platforma Azure nie wypychanie aktualizacji z Windows Update do maszyn wirtualnych z systemem Windows, ponieważ te maszyny mają być zarządzane przez ich użytkowników. Zaleca się jednak pozostawienie ustawienia Windows Update automatycznie włączone. Automatyczna instalacja aktualizacji z Windows Update może także spowodować, że ponowne uruchomienie nastąpi po zastosowaniu aktualizacji. Aby uzyskać więcej informacji, zobacz [Windows Update często zadawane pytania](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Inne sytuacje wpływające na dostępność maszyny wirtualnej

Istnieją inne przypadki, w których platforma Azure może aktywnie zawiesić korzystanie z maszyny wirtualnej. Powiadomienia e-mail będą wysyłane przed wykonaniem tej akcji, dzięki czemu będziesz mieć szansę rozwiązać podstawowe problemy. Przykłady problemów, które mają wpływ na dostępność maszyny wirtualnej, obejmują naruszenia zabezpieczeń i wygaśnięcia metod płatności.

### <a name="host-server-faults"></a>Błędy serwera hosta

Maszyna wirtualna jest hostowana na serwerze fizycznym, który działa w centrum danych platformy Azure. Na serwerze fizycznym jest uruchamiany Agent o nazwie Agent hosta oprócz kilku innych składników platformy Azure. Gdy te składniki oprogramowania platformy Azure na serwerze fizycznym przestają odpowiadać, system monitorowania wyzwala ponowne uruchomienie serwera hosta w celu podjęcia próby odzyskania. Maszyna wirtualna jest zazwyczaj dostępna ponownie w ciągu pięciu minut i będzie nadal aktywna na tym samym hoście, co wcześniej.

Błędy serwera są zwykle spowodowane awariami sprzętowymi, takimi jak awaria dysku twardego lub dysku SSD. Platforma Azure stale monitoruje te wystąpienia, identyfikuje podstawowe błędy i przedstawia aktualizacje po zaimplementowaniu i przetestowaniu środków zaradczych.

Niektóre błędy serwera hosta mogą być specyficzne dla tego serwera, jednak powtórzona sytuacja ponownego uruchomienia maszyny wirtualnej może zostać ulepszona przez ręczne ponowne wdrożenie maszyny wirtualnej na innym serwerze hosta. Tę operację można wyzwolić przy użyciu opcji **ponownego wdrażania** na stronie szczegółów maszyny wirtualnej lub przez zatrzymanie i ponowne uruchomienie maszyny wirtualnej w Azure Portal.

### <a name="auto-recovery"></a>Autoodzyskiwanie

Jeśli z jakiegoś powodu serwer hosta nie może się ponownie uruchomić, platforma Azure zainicjuje akcję Autoodzyskiwania, aby przeanalizować wadliwy serwer hosta w celu przeprowadzenia dalszej analizy. 

Wszystkie maszyny wirtualne na tym hoście zostaną automatycznie przeniesione do innego, zdrowego serwera hosta. Ten proces jest zwykle zakończony w ciągu 15 minut. Aby dowiedzieć się więcej na temat procesu Autoodzyskiwania, zobacz [Autoodzyskiwanie maszyn wirtualnych](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Nieplanowana konserwacja

W rzadkich przypadkach zespół operacyjny platformy Azure może wymagać wykonania czynności konserwacyjnych w celu zapewnienia ogólnej kondycji platformy Azure. Takie zachowanie może mieć wpływ na dostępność maszyny wirtualnej, a zwykle jest to ta sama akcja Autoodzyskiwania, zgodnie z wcześniejszym opisem.  

Nieplanowana konserwacja obejmuje następujące elementy:

- Pilna defragmentacja węzła
- Pilne aktualizacje przełącznika sieci

### <a name="vm-crashes"></a>Awarie maszyny wirtualnej

Maszyny wirtualne mogą zostać ponownie uruchomione z powodu problemów w samej maszynie wirtualnej. Obciążenie lub rola działająca na maszynie wirtualnej może spowodować sprawdzenie błędów w systemie operacyjnym gościa. Aby uzyskać pomoc w ustaleniu przyczyny awarii, Wyświetl Dzienniki systemu i aplikacji dla maszyn wirtualnych z systemem Windows oraz dzienniki seryjne dla maszyn wirtualnych z systemem Linux.

### <a name="storage-related-forced-shutdowns"></a>Wymuszone zamknięcia związane z magazynem

Maszyny wirtualne na platformie Azure korzystają z dysków wirtualnych dla systemu operacyjnego i magazynu danych hostowanego w infrastrukturze usługi Azure Storage. Za każdym razem, gdy dostępność lub łączność między MASZYNą wirtualną i skojarzonymi dyskami wirtualnymi ma więcej niż 120 sekund, platforma Azure wykonuje wymuszone zamykanie maszyn wirtualnych w celu uniknięcia uszkodzenia danych. Maszyny wirtualne są automatycznie włączane ponownie po przywróceniu połączenia z magazynem. 

Czas trwania zamknięcia może być krótszy niż pięć minut, ale może być znacznie dłuższy. Poniżej przedstawiono jedno z określonych przypadków związanych z wymuszonymi zamknięciami związanymi z magazynem: 

**Przekraczanie limitów operacji we/wy**

Maszyny wirtualne mogą być tymczasowo zamykane, gdy żądania we/wy są stale ograniczane, ponieważ liczba operacji we/wy na sekundę (IOPS) przekracza limity we/wy dysku. (Magazyn dyskowy w warstwie Standardowa jest ograniczony do 500 operacji we/wy na sekundę). Aby rozwiązać ten problem, należy użyć rozłożenia dysku lub skonfigurować miejsce do magazynowania w ramach maszyny wirtualnej gościa, w zależności od obciążenia. 

### <a name="other-incidents"></a>Inne zdarzenia

W rzadkich przypadkach powszechny problem może mieć wpływ na wiele serwerów w centrum danych platformy Azure. W przypadku wystąpienia tego problemu zespół platformy Azure wysyła powiadomienia e-mail do odpowiednich subskrypcji. Możesz sprawdzić [Azure Service Health pulpit nawigacyjny](https://azure.microsoft.com/status/) i Azure Portal w celu uzyskania stanu trwających i przeszłych zdarzeń.