---
title: Zarządzanie dostępnością maszyn wirtualnych
description: Dowiedz się, jak korzystać z wielu maszyn wirtualnych, aby zapewnić wysoką dostępność aplikacji na platformie Azure
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cynthn
ms.openlocfilehash: 11444fc599b46ceff90eda562d2fd557bcaf53b2
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961344"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Zarządzanie dostępnością maszyn wirtualnych z systemem Linux

Dowiedz się, jak skonfigurować wiele maszyn wirtualnych i zarządzać nimi, aby zapewnić wysoką dostępność aplikacji systemu Linux na platformie Azure. 


## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Omówienie ponownych rozruchów maszyn wirtualnych — konserwacja a przestój
Istnieją trzy scenariusze, które mogą spowodować wpływ na maszynę wirtualną na platformie Azure: nieplanowana konserwacja sprzętu, nieoczekiwany przestój i planowana konserwacja.

* **Zdarzenie nieplanowanej konserwacji sprzętu** ma miejsce, gdy platforma Azure przewidzi zbliżającą się awarię sprzętu lub dowolnego składnika platformy powiązanego z maszyną fizyczną. Gdy platforma przewidzi awarię, wygeneruje zdarzenie nieplanowanej konserwacji sprzętu, aby zmniejszyć wpływ tej awarii na maszyny wirtualne hostowane na tym sprzęcie. Platforma Azure używa technologii [migracja na żywo](./maintenance-and-updates.md?bc=%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json%252c%252fazure%252fvirtual-machines%252flinux%252fbreadcrumb%252ftoc.json&toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json%253ftoc%253d%252fazure%252fvirtual-machines%252flinux%252ftoc.json) do migrowania Virtual Machines z nieprawidłowego sprzętu na komputer fizyczny w dobrej kondycji. Migracja na żywo to operacja zachowywania maszyny wirtualnej, która jedynie wstrzymuje maszynę wirtualną na krótki czas. Pamięć, otwarte pliki i połączenia sieciowe są utrzymywane, ale wydajność przed i/lub po zdarzeniu może zostać ograniczona. W przypadkach, gdy nie można użyć migracji na żywo, maszyna wirtualna doświadczy nieoczekiwanego przestoju zgodnie z opisem poniżej.


* **Nieoczekiwany przestój** polega na nieoczekiwanej awarii sprzętu lub infrastruktury fizycznej maszyny wirtualnej. Może to obejmować awarie sieci lokalnej, błędy dysku lokalnego lub inne awarie poziomu stojaka. W przypadku wykrycia platforma Azure automatycznie migruje maszynę wirtualną do zdrowej maszyny fizycznej w tym samym centrum danych. Podczas wykonywania procedury naprawiania maszyny wirtualne doświadczają przestoju (ponownego rozruchu), a w niektórych przypadkach dochodzi do utraty dysku tymczasowego. Dołączone dyski systemu operacyjnego i danych są zawsze zachowywane.

  Maszyny wirtualne mogą również powodować przestoje w przypadku nieoczekiwanego wystąpienia awarii lub awarii, która ma wpływ na całe centrum danych, a nawet cały region. W tych scenariuszach platforma Azure oferuje opcje ochrony, w tym  [strefy dostępności](../availability-zones/az-overview.md) i [obszary sparowane](regions.md#region-pairs).

* **Zdarzenia planowanej konserwacji** to okresowe aktualizacje wprowadzane przez firmę Microsoft do podstawowej platformy Azure w celu zwiększenia ogólnej niezawodności, wydajności i bezpieczeństwa infrastruktury platformy, na której działają maszyny wirtualne. Większość tych aktualizacji jest przeprowadzana bez żadnego wpływu na Virtual Machines lub Cloud Services (zobacz [konserwacja, która nie wymaga ponownego uruchomienia](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Platforma Azure próbuje przeprowadzać konserwację zachowującą maszynę wirtualną przy wszystkich możliwych okazjach, w rzadkich przypadkach jednak aktualizacje wymagają ponownego rozruchu w celu zaktualizowania infrastruktury podstawowej. W takiej sytuacji można przeprowadzić planowaną konserwację platformy Azure z operacją ponownego wdrożenia po konserwacji, inicjując konserwację maszyn wirtualnych w odpowiednim przedziale czasu. Aby uzyskać więcej informacji, zobacz [Planned maintenance for virtual machines in Azure (Planowana konserwacja maszyn wirtualnych na platformie Azure)](maintenance-and-updates.md).


Aby zmniejszyć wpływ przestoju spowodowanego co najmniej jednym z tych zdarzeń, zalecamy zastosowanie następujących najlepszych rozwiązań zapewniających wysoką dostępność maszyn wirtualnych:

* Używanie stref Availabiilty do ochrony przed awariami centrum danych
* Konfigurowanie wielu maszyn wirtualnych w zestawie dostępności w celu zapewnienia nadmiarowości
* Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności
* Używanie zaplanowanych zdarzeń do aktywnego reagowania na zdarzenia wpływające na maszynę wirtualną
* Konfigurowanie każdej warstwy aplikacji w osobnych zestawach dostępności
* Łączenie modułu równoważenia obciążenia z strefami dostępności lub zestawami
* Używanie stref dostępności do ochrony przed awariami na poziomie centrum danych

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Używanie stref dostępności do ochrony przed awariami na poziomie centrum danych

[Strefy dostępności](../availability-zones/az-overview.md) rozszerzają poziom kontroli, aby zachować dostępność aplikacji i danych na maszynach wirtualnych. Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. Aby zapewnić odporność, istnieją co najmniej trzy oddzielne strefy we wszystkich włączonych regionach. Fizyczna separacja stref dostępności w ramach regionu chroni aplikacje i dane przed awariami centrum danych. Usługi strefowo nadmiarowe replikujeją aplikacje i dane między Strefy dostępności, aby chronić je przed awariami jednego punktu.

Strefa dostępności w regionie świadczenia usługi Azure to kombinacja **domeny błędów** i **domeny aktualizacji**. Jeśli na przykład utworzysz trzy lub więcej maszyn wirtualnych w trzech strefach w regionie świadczenia usługi Azure, maszyny wirtualne będą rozproszone w trzech domenach błędów i trzech domenach aktualizacji. Platforma Azure rozpoznaje to rozproszenie w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są aktualizowane w tym samym czasie.

Dzięki strefom dostępności platforma Azure oferuje najlepszą w branży umowę dotycząca poziomu usług (SLA) gwarantującą czas działania na poziomie 99,99%. Dzięki zaprojektowaniu rozwiązań do korzystania z replikowanych maszyn wirtualnych w strefach można chronić aplikacje i dane przed utratą centrum danych. W przypadku naruszenia zabezpieczeń jednej strefy zreplikowane aplikacje i dane są natychmiast dostępne w innej strefie.

![Strefy dostępności](./media/virtual-machines-common-manage-availability/three-zones-per-region.png)

Dowiedz się więcej o wdrażaniu maszyny wirtualnej z [systemem Windows](./windows/create-powershell-availability-zone.md) lub [Linux](./linux/create-cli-availability-zone.md) w strefie dostępności.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurowanie wielu maszyn wirtualnych w zestawie dostępności w celu zapewnienia nadmiarowości
Zestawy dostępności są inną konfiguracją centrum danych w celu zapewnienia nadmiarowości i dostępności maszyn wirtualnych. Ta konfiguracja w centrum danych gwarantuje, że podczas planowanego lub nieplanowanego zdarzenia konserwacji jest dostępna co najmniej jedna maszyna wirtualna i spełnia warunki umowy SLA platformy Azure o wartości 99,95%. Aby uzyskać więcej informacji, zobacz [Virtual Machines — umowa SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Pojedyncze wystąpienie maszyny wirtualnej w zestawie dostępności musi używać SSD w warstwie Premium lub Ultra Disk dla wszystkich dysków systemu operacyjnego i dysków z danymi, aby można było zakwalifikować się do umowy SLA dotyczącej łączności z maszyną wirtualną wynoszącą co najmniej 99,9% czasu. 
> 
> W przypadku maszyny wirtualnej o pojedynczej instancji z SSD w warstwie Standardowa będzie dostępna umowa SLA o wartości co najmniej 99,5%, podczas gdy maszyna wirtualna o pojedynczej instancji z HDD w warstwie Standardowa będzie miała umowę SLA równą co najmniej 95%.  Zobacz umowę [SLA dla Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/)

Każda maszyna wirtualna w zestawie dostępności ma przypisaną **domenę aktualizacji** i **domenę błędów** przez podstawową platformę Azure. Dany zestaw dostępności ma pięć domyślnie przypisanych domen aktualizacji, których użytkownik nie może konfigurować (następnie można zwiększyć liczbę wystąpień usługi Resource Manager, aby oferowała do 20 domen aktualizacji). Umożliwia to wskazanie grup maszyn wirtualnych i odpowiedniego sprzętu fizycznego, które mogą być uruchamiane równocześnie. Jeśli w pojedynczym zestawie dostępności skonfigurowano więcej niż pięć maszyn wirtualnych, szósta maszyna wirtualna jest umieszczana w tej samej domenie aktualizacji, co pierwsza maszyna wirtualna, siódma — w tej samej domenie aktualizacji co druga maszyna wirtualna itd. Podczas planowanej konserwacji domeny aktualizacji mogą nie być ponownie uruchamiane kolejno, ale w danym momencie tylko jedna domena aktualizacji jest uruchamiana ponownie. Domena aktualizacji po ponownym rozruchu otrzymuje 30 minut na odzyskanie sprawności zanim konserwacja zostanie zainicjowana w innej domenie aktualizacji.

Domeny błędów definiują grupę maszyn wirtualnych, które korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Domyślnie maszyny wirtualne skonfigurowane w zestawie dostępności są rozdzielane między maksymalnie trzy domeny błędów w przypadku wdrożeń usługi Resource Manager (dwie domeny błędów w przypadku modelu klasycznego). Umieszczenie maszyn wirtualnych w zestawie dostępności nie chroni aplikacji przed błędami związanymi z systemem operacyjnym lub daną aplikacją, ale ogranicza wpływ potencjalnych awarii sprzętu fizycznego, awarii sieci i przerw w dostawie prądu.

<!--Image reference-->
   ![Rysunek koncepcyjny przedstawiający konfigurację domeny aktualizacji i domeny błędów](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Używanie dysków zarządzanych dla maszyn wirtualnych w zestawie dostępności
Jeśli obecnie korzystasz z maszyn wirtualnych z dyskami niezarządzanymi, zdecydowanie zalecamy przekonwertować z niezarządzanych do dysków zarządzanych dla systemów [Linux](./linux/convert-unmanaged-to-managed-disks.md) i [Windows](./windows/convert-unmanaged-to-managed-disks.md).

[Dyski zarządzane](./managed-disks-overview.md) zapewniają większą niezawodność zestawów dostępności przez zagwarantowanie, że dyski maszyn wirtualnych w zestawie dostępności są wystarczająco odizolowane od siebie, aby uniknąć pojedynczych punktów awarii. Jest to możliwe przez automatyczne umieszczenie dysków w różnych domenach błędów magazynu (klastrów magazynu) i dostosowanie ich do domeny błędów maszyny wirtualnej. Jeśli domena błędów magazynu ulegnie awarii ze względu na awarię sprzętu lub oprogramowania, tylko wystąpienie maszyny wirtualnej z dyskami w domenie błędów magazynu zakończy się niepowodzeniem.
![Domenami błędów dysków zarządzanych](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Liczba domen błędów dla zarządzanych zestawów dostępności jest zależna od regionu i może wynosić dwa lub trzy na region. Domenę błędów można zobaczyć dla każdego regionu, uruchamiając następujące skrypty.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> W pewnych okolicznościach dwie maszyny wirtualne w tym samym zestawie dostępności mogą współużytkować domenę błędów. Można potwierdzić udostępnioną domenę błędów, przechodząc do zestawu dostępności i sprawdzając kolumnę **domena błędów** . Wspólna domena błędów może być spowodowana przez wykonanie następującej sekwencji podczas wdrażania maszyn wirtualnych:
> 1. Wdróż pierwszą maszynę wirtualną.
> 1. Zatrzymaj/Cofnij przydział pierwszej maszyny wirtualnej.
> 1. Wdróż drugą maszynę wirtualną.
>
> W tych okolicznościach dysk systemu operacyjnego drugiej maszyny wirtualnej może zostać utworzony w tej samej domenie błędów co pierwsza maszyna wirtualna, więc dwie maszyny wirtualne będą znajdować się w tej samej domenie błędów. Aby uniknąć tego problemu, zalecamy nie zatrzymywanie/cofanie alokacji maszyn wirtualnych między wdrożeniami.

Jeśli planujesz używanie maszyn wirtualnych z dyskami niezarządzanymi, postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi kont magazynu, w których wirtualne dyski twarde (VHD) maszyn wirtualnych są przechowywane jako [stronicowe obiekty blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Obsługuj wszystkie dyski (systemu operacyjnego i danych) skojarzone z maszyną wirtualną na tym samym koncie magazynu.**
2. Przed dodaniem kolejnych dysków VHD do konta magazynu **Przejrzyj [limity](../storage/blobs/scalability-targets-premium-page-blobs.md) liczby dysków niezarządzanych na koncie usługi Azure Storage** .
3. **Użyj oddzielnego konta magazynu dla każdej maszyny wirtualnej w zestawie dostępności.** Nie stosuj współużytkowania kont magazynu przez wiele maszyn wirtualnych w tym samym zestawie dostępności. Jest ona akceptowalna dla maszyn wirtualnych w różnych zestawach dostępności, aby współużytkować konta magazynu, jeśli powyższe najlepsze rozwiązania są po wykonaniu ![ domenami błędów dysków niezarządzanych](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Używanie zaplanowanych zdarzeń do aktywnego reagowania na zdarzenia wpływające na maszynę wirtualną

Gdy subskrybujesz [zaplanowane zdarzenia](./linux/scheduled-events.md), maszyna wirtualna zostanie powiadomiona o nadchodzących zdarzeniach konserwacyjnych, które mogą mieć wpływ na maszynę wirtualną. Po włączeniu zaplanowanych zdarzeń dana maszyna wirtualna ma minimalny czas przed wykonaniem działania konserwacji. Na przykład aktualizacje systemu operacyjnego hosta, które mogą mieć wpływ na maszynę wirtualną, są umieszczane w kolejce jako zdarzenia, które określają wpływ, a także czas, w którym będzie wykonywana konserwacja, jeśli nie zostanie podjęta żadna akcja. Zdarzenia harmonogramu są również umieszczane w kolejce, gdy platforma Azure wykrywa bezpośrednie awarie sprzętu, które mogą mieć wpływ na maszynę wirtualną, co umożliwia podjęcie decyzji o tym, kiedy nastąpi przeprowadzenie naprawy. Klienci mogą używać tego zdarzenia do wykonywania zadań przed konserwacją, takich jak zapisywanie stanu, przechodzenie w tryb failover do pomocniczego i tak dalej. Po dodaniu logiki do bezpiecznego obsłużenia zdarzenia konserwacji możesz zatwierdzić zaległe zaplanowane zdarzenie, aby umożliwić platformie dalszą konserwację.


## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Łączenie modułu równoważenia obciążenia z strefami dostępności lub zestawami
Połącz [Azure Load Balancer](../load-balancer/load-balancer-overview.md) ze strefą dostępności lub ustaw, aby uzyskać największą odporność aplikacji. Usługa Azure Load Balancer dystrybuuje ruch między wieloma maszynami wirtualnymi. W przypadku naszych maszyn wirtualnych w warstwie Standardowa usługa Azure Load Balancer jest uwzględniana. Nie wszystkie warstwy maszyn wirtualnych obejmują usługę Azure Load Balancer. Aby uzyskać więcej informacji na temat równoważenia obciążenia maszyn wirtualnych, zobacz **równoważenie obciążenia maszyn wirtualnych** dla systemu [Linux](linux/tutorial-load-balancer.md) lub [Windows](windows/tutorial-load-balancer.md).

Jeśli moduł równoważenia obciążenia nie został skonfigurowany do równoważenia ruchu między wieloma maszynami wirtualnymi, każde zdarzenie planowanej konserwacji wpływa tylko na maszynę wirtualną obsługującą ruch, co powoduje awarię w warstwie aplikacji. Umieszczenie wielu maszyn wirtualnych z tej samej warstwy w tym samym module równoważenia obciążenia i zestawie dostępności umożliwia stałą obsługę ruchu przez co najmniej jedno wystąpienie.

Aby zapoznać się z samouczkiem dotyczącym równoważenia obciążenia w strefach dostępności, zobacz [Samouczek: Równoważenie obciążenia maszyn wirtualnych w strefach dostępności za pomocą usługa Load Balancer w warstwie Standardowa przy użyciu Azure Portal](../load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal.md).


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat równoważenia obciążenia maszyn wirtualnych, zobacz [równoważenie obciążenia maszyn wirtualnych](../load-balancer/load-balancer-overview.md).