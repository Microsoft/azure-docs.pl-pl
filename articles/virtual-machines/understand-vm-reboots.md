---
title: Opis ponownych uruchomień maszyn wirtualnych
description: Opis ponownych uruchomień maszyn wirtualnych — konserwacja vs przestoju
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 9342e0c39b558cf8b6ff5773d623ce55062f9ab5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607477"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Opis ponownych uruchomień maszyn wirtualnych — konserwacja i przestoje
Istnieją trzy scenariusze, które mogą prowadzić do maszyn wirtualnych na platformie Azure: nieplanowana konserwacja sprzętu, nieoczekiwany przestój i planowana konserwacja.

## <a name="unplanned-hardware-maintenance-event"></a>Nieplanowane zdarzenie konserwacji sprzętu
Nieplanowana konserwacja sprzętu występuje, gdy platforma Azure przewidywalna, że sprzęt lub dowolny składnik platformy skojarzony z komputerem fizycznym zakończył się niepowodzeniem. Gdy platforma przewidzi awarię, wygeneruje zdarzenie nieplanowanej konserwacji sprzętu, aby zmniejszyć wpływ tej awarii na maszyny wirtualne hostowane na tym sprzęcie. Platforma Azure używa technologii [migracja na żywo](./maintenance-and-updates.md) do migrowania Virtual Machines z nieprawidłowego sprzętu na komputer fizyczny w dobrej kondycji. Migracja na żywo to operacja zachowywania maszyny wirtualnej, która jedynie wstrzymuje maszynę wirtualną na krótki czas. Pamięć, otwarte pliki i połączenia sieciowe są utrzymywane, ale wydajność przed i/lub po zdarzeniu może zostać ograniczona. W przypadkach, gdy nie można użyć migracji na żywo, maszyna wirtualna doświadczy nieoczekiwanego przestoju zgodnie z opisem poniżej.


## <a name="unexpected-downtime"></a>Nieoczekiwany przestój
Nieoczekiwany przestój polega na nieoczekiwanej awarii sprzętu lub infrastruktury fizycznej maszyny wirtualnej. Może to obejmować awarie sieci lokalnej, błędy dysku lokalnego lub inne awarie poziomu stojaka. W przypadku wykrycia platforma Azure automatycznie migruje maszynę wirtualną do zdrowej maszyny fizycznej w tym samym centrum danych. Podczas wykonywania procedury naprawiania maszyny wirtualne doświadczają przestoju (ponownego rozruchu), a w niektórych przypadkach dochodzi do utraty dysku tymczasowego. Dołączone dyski systemu operacyjnego i danych są zawsze zachowywane.

Na maszynach wirtualnych można również wypróbować przestoje w prawdopodobnym zdarzeniu awarii lub awarii, które mają wpływ na całe centrum danych, a nawet cały region. W tych scenariuszach platforma Azure oferuje opcje ochrony, w tym  [strefy dostępności](../availability-zones/az-overview.md) i [obszary sparowane](regions.md#region-pairs).

## <a name="planned-maintenance-events"></a>Zdarzenia planowanej konserwacji
Zdarzenia planowanej konserwacji to okresowe aktualizacje tworzone przez firmę Microsoft dla podstawowej platformy Azure w celu zwiększenia ogólnej niezawodności, wydajności i bezpieczeństwa infrastruktury platformy działania maszyn wirtualnych. Większość tych aktualizacji jest przeprowadzana bez żadnego wpływu na Virtual Machines lub Cloud Services (zobacz [konserwacja, która nie wymaga ponownego uruchomienia](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Platforma Azure próbuje przeprowadzać konserwację zachowującą maszynę wirtualną przy wszystkich możliwych okazjach, w rzadkich przypadkach jednak aktualizacje wymagają ponownego rozruchu w celu zaktualizowania infrastruktury podstawowej. W takiej sytuacji można przeprowadzić planowaną konserwację platformy Azure z operacją ponownego wdrożenia po konserwacji, inicjując konserwację maszyn wirtualnych w odpowiednim przedziale czasu. Aby uzyskać więcej informacji, zobacz [Planned maintenance for virtual machines in Azure (Planowana konserwacja maszyn wirtualnych na platformie Azure)](maintenance-and-updates.md).

## <a name="reduce-downtime"></a>Zmniejsz czas przestoju
Aby zmniejszyć wpływ przestoju spowodowanego co najmniej jednym z tych zdarzeń, zalecamy zastosowanie następujących najlepszych rozwiązań zapewniających wysoką dostępność maszyn wirtualnych:

* Używanie [strefy dostępności](../availability-zones/az-overview.md) do ochrony przed awariami centrum danych
* Konfigurowanie wielu maszyn wirtualnych w [zestawie dostępności](availability-set-overview.md) w celu zapewnienia nadmiarowości
* Używanie [zaplanowanych zdarzeń dla systemu Linux](/azure/virtual-machines/linux/scheduled-events) lub [zaplanowanych zdarzeń w](/azure/virtual-machines/windows/scheduled-events) celu aktywnego reagowania na zdarzenia wpływające na maszynę wirtualną
* Konfigurowanie każdej warstwy aplikacji w osobnych zestawach dostępności
* Łączenie [modułu równoważenia obciążenia](../load-balancer/load-balancer-overview.md) z strefami dostępności lub zestawami

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o opcjach dostępności na platformie Azure, zobacz [Omówienie dostępności](availability.md).
