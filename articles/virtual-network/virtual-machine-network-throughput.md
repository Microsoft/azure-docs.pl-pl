---
title: Przepływność sieci maszyn wirtualnych platformy Azure | Microsoft Docs
description: Dowiedz się więcej o przepływności sieci maszyn wirtualnych platformy Azure, w tym o tym, jak przepustowość jest przypisana do maszyny wirtualnej.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: b11bdf9b82352c15b7f7236168494f32fe4a4f9f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221514"
---
# <a name="virtual-machine-network-bandwidth"></a>Przepustowość sieci maszyny wirtualnej

Platforma Azure oferuje różne rozmiary i typy maszyn wirtualnych, z których każdy ma inne możliwości wydajności. Jedną z możliwości jest przepustowość sieci (lub przepustowość) mierzona w megabitach na sekundę (MB/s). Ze względu na to, że maszyny wirtualne są hostowane na udostępnionym sprzęcie, pojemność sieci musi być współdzielona przez maszyny wirtualne korzystające z tego samego sprzętu. Większe maszyny wirtualne są przydzieloną stosunkowo większą przepustowością niż mniejsze maszyny wirtualne.
 
Przepustowość sieci przypisana do każdej maszyny wirtualnej jest naliczana na ruch wychodzący z maszyny wirtualnej. Cały ruch sieciowy opuszczający maszynę wirtualną jest liczony do przydzielonych limitów, niezależnie od miejsca docelowego. Na przykład jeśli maszyna wirtualna ma limit 1 000 MB/s, ten limit dotyczy tego, czy ruch wychodzący jest przeznaczony dla innej maszyny wirtualnej w tej samej sieci wirtualnej, czy poza platformą Azure.
 
Ruch przychodzący nie jest naliczany ani ograniczony bezpośrednio. Istnieją jednak inne czynniki, takie jak limity procesora CPU i magazynu, które mogą mieć wpływ na możliwość przetwarzania danych przychodzących przez maszynę wirtualną.

Przyspieszona sieć to funkcja przeznaczona do poprawy wydajności sieci, w tym opóźnienia, przepływności i użycia procesora CPU. Przyspieszona sieć może zwiększyć przepływność maszyny wirtualnej, więc może to zrobić tylko do przydzieloną przepustowość maszyny wirtualnej. Aby dowiedzieć się więcej o przyspieszonej sieci, zobacz przyspieszone sieci dla maszyn wirtualnych z [systemem Windows](create-vm-accelerated-networking-powershell.md) lub [Linux](create-vm-accelerated-networking-cli.md) .
 
Usługi Azure Virtual Machines muszą mieć jeden, ale może mieć kilka podłączonych do nich interfejsów sieciowych. Przepustowość przypisana do maszyny wirtualnej to suma całego ruchu wychodzącego między wszystkimi interfejsami sieciowymi podłączonymi do maszyny wirtualnej. Innymi słowy, przydzieloną przepustowość dotyczy każdej maszyny wirtualnej, niezależnie od liczby interfejsów sieciowych dołączonych do maszyny wirtualnej. Aby dowiedzieć się, ile interfejsów sieciowych obsługuje różne rozmiary maszyn wirtualnych platformy Azure, zobacz rozmiary maszyn wirtualnych z [systemami Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . 

## <a name="expected-network-throughput"></a>Oczekiwana przepływność sieci

Oczekiwana przepływność wychodząca i liczba interfejsów sieciowych obsługiwanych przez poszczególne rozmiary maszyn wirtualnych są szczegółowo opisane w obszarze rozmiary maszyn wirtualnych w [systemie Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Wybierz typ, taki jak ogólnego przeznaczenia, a następnie wybierz serię rozmiarów na stronie wyników, na przykład Dv2. Każda seria zawiera tabelę z specyfikacjami sieci w ostatniej kolumnie zatytułowaną, **Maksymalna liczba kart sieciowych/oczekiwana wydajność sieci (MB/s)**. 

Limit przepływności ma zastosowanie do maszyny wirtualnej. Następujące czynniki nie mają wpływ na przepływność:
- **Liczba interfejsów sieciowych**: limit przepustowości jest kumulowany dla całego ruchu wychodzącego z maszyny wirtualnej.
- **Przyspieszona sieć**: Mimo że funkcja może być przydatna w osiągnięciu opublikowanego limitu, nie zmienia limitu.
- **Miejsce docelowe ruchu**: liczba wszystkich miejsc docelowych w kierunku limitu wychodzącego.
- **Protokół**: cały ruch wychodzący przez wszystkie protokoły jest liczony do limitu.

## <a name="network-flow-limits"></a>Limity przepływu sieci

Oprócz przepustowości liczba połączeń sieciowych znajdujących się na maszynie wirtualnej może mieć wpływ na wydajność sieci. Stos sieci platformy Azure zachowuje stan dla każdego kierunku połączenia TCP/UDP w strukturach danych o nazwie "Flows". Typowe połączenie TCP/UDP będzie miało 2 utworzone przepływy, jeden dla ruchu przychodzącego i innego dla kierunku wychodzącego. 

Transfer danych między punktami końcowymi wymaga utworzenia kilku przepływów oprócz tych, które wykonują transfer danych. Niektóre przykłady to przepływy utworzone dla rozpoznawania DNS i przepływów utworzonych dla sond kondycji modułu równoważenia obciążenia. Należy również zauważyć, że wirtualne urządzenia sieciowe (urządzeń WUS), takie jak bramy, serwery proxy, zapory, będą widzieć przepływy tworzone dla połączeń zakończonych na urządzeniu i pochodzące z urządzenia. 

![Liczba przepływów dla konwersacji TCP za pośrednictwem urządzenia przekazującego](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Limity przepływu i zalecenia

Obecnie stos sieci platformy Azure obsługuje 250 000 łączne przepływy sieciowe z dobrą wydajnością dla maszyn wirtualnych z ponad 8 rdzeniami procesora i łącznym przepływem, dzięki czemu maszyny wirtualne mają mniej niż 8 rdzeni procesora. Dzięki temu ograniczenie wydajności sieci jest bezpieczne dla dodatkowych przepływów, aż do ostatecznego limitu całkowitej liczby przepływów 500 000, 250 000 ruchu przychodzącego i 250 000 wychodzącego, po którym są porzucane dodatkowe przepływy.

| Poziom wydajności | Maszyny wirtualne z <8 rdzeni procesora CPU | Maszyny wirtualne z 8 rdzeniami procesora CPU |
| ----------------- | --------------------- | --------------------- |
|<b>Dobra wydajność</b>|Przepływy 100 000 |Przepływy 250 000|
|<b>Wydajność obniżona</b>|Powyżej przepływy 100 000|Powyżej przepływy 250 000|
|<b>Limit przepływu</b>|Przepływy 500 000|Przepływy 500 000|

Metryki są dostępne w [Azure monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) , aby śledzić liczbę przepływów sieciowych i szybkość tworzenia przepływu na maszynach wirtualnych lub wystąpieniach VMSS.

![Zrzut ekranu przedstawia stronę metryki Azure Monitor z wykresem liniowym i całkowitymi przepływami ruchu przychodzącego i wychodzącego.](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Stawki za połączenia i zwolnienia mogą również wpływać na wydajność sieci, ponieważ ustanowienie połączenia i zakończenie współużytkują procesor z procedurami przetwarzania pakietów. Firma Microsoft zaleca, aby przeprowadzić testy porównawcze względem oczekiwanych wzorców ruchu i odpowiednio skalować obciążenia w celu dopasowania do potrzeb związanych z wydajnością. 

## <a name="next-steps"></a>Następne kroki

- [Optymalizowanie przepływności sieci dla systemu operacyjnego maszyny wirtualnej](virtual-network-optimize-network-bandwidth.md)
- [Przetestuj przepływność sieci](virtual-network-bandwidth-testing.md) dla maszyny wirtualnej.