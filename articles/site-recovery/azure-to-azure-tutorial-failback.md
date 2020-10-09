---
title: Powrót po awarii maszyn wirtualnych platformy Azure do regionu podstawowego za pomocą usługi Azure Site Recovery.
description: Opisuje sposób powrotu maszyn wirtualnych platformy Azure do regionu podstawowego przy użyciu usługi Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 432c92bcfa8a2e0df26adf1516f5bdc9ee73d267
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502379"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Powrót po awarii maszyny wirtualnej platformy Azure między regionami platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii odzyskiwania po awarii przez zarządzanie replikacją, trybem failover i powrotem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure.

W tym samouczku opisano sposób powrotu po awarii pojedynczej maszyny wirtualnej platformy Azure. Po przełączeniu w tryb failover należy powrócić po awarii do regionu podstawowego, gdy jest on dostępny. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Powrót po awarii maszyny wirtualnej w regionie pomocniczym.
> * Ponownie Włącz ochronę podstawowej maszyny wirtualnej z powrotem do regionu pomocniczego.
> 
> [!NOTE]
> 
> Ten samouczek ułatwia przechodzenie w tryb failover kilku maszyn wirtualnych do regionu docelowego i powrót do regionu źródłowego z minimalnymi dostosowaniami. Aby uzyskać bardziej szczegółowe instrukcje, zapoznaj się z [przewodnikami z instrukcjami na maszynach wirtualnych platformy Azure](../virtual-machines/windows/index.yml).

## <a name="before-you-start"></a>Przed rozpoczęciem

* Upewnij się, że stan maszyny wirtualnej to **przekazana do trybu failover**.
* Sprawdź, czy region podstawowy jest dostępny i czy masz możliwość tworzenia nowych zasobów i uzyskiwania do nich dostępu.
* Upewnij się, że włączono ochronę.

## <a name="fail-back-to-the-primary-region"></a>Powrót po awarii do regionu podstawowego

Po ponownym włączeniu ochrony maszyn wirtualnych można wrócić do regionu podstawowego w razie potrzeby.

1. W magazynie wybierz pozycję **zreplikowane elementy**, a następnie wybierz maszynę wirtualną, która została ponownie włączona.

    ![Zrzut ekranu przedstawiający powrót po awarii do podstawowego w Azure Portal.](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. W obszarze **zreplikowane elementy**wybierz maszynę wirtualną, a następnie wybierz pozycję **tryb failover**.
3. W obszarze **tryb failover**wybierz punkt odzyskiwania, w którym ma zostać przełączona praca awaryjna:
    - **Najnowsza (domyślnie)**: przetwarza wszystkie dane w usłudze Site Recovery i zapewnia najniższy cel punktu odzyskiwania (RPO).
    - **Najnowsza przetworzony**: przywraca maszynę wirtualną do najnowszego punktu odzyskiwania, który został przetworzony przez Site Recovery.
    - **Niestandardowa**: przełączenie w tryb failover do określonego punktu odzyskiwania. Ta opcja jest przydatna na potrzeby wykonywania testu trybu failover.
4. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , jeśli chcesz, aby Site Recovery próby zamknięcia maszyn wirtualnych w regionie Dr przed wyzwoleniem trybu failover. Tryb failover kontynuuje działanie nawet wtedy, gdy wyłączenie nie powiedzie się. 
5. Postęp przełączania w tryb failover można śledzić na karcie **Zadania**.
6. Po zakończeniu pracy w trybie failover Zweryfikuj maszynę wirtualną, logując się do niej. Punkt odzyskiwania można zmienić odpowiednio do potrzeb.
7. Po zweryfikowaniu trybu failover wybierz pozycję **Zatwierdź tryb failover**. Zatwierdzenie usuwa wszystkie dostępne punkty odzyskiwania. Opcja Zmień punkt odzyskiwania nie jest już dostępna.
8. Maszyna wirtualna powinna być wyświetlana jako przełączona w tryb failover i ponownie zakończona.

    ![Zrzut ekranu przedstawiający maszynę wirtualną w regionie podstawowym i pomocniczym.](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> W przypadku maszyn korzystających z dysków zarządzanych i uruchamiania rozszerzenia Site Recovery 9.28. x. x i [pakietu zbiorczego aktualizacji 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Site Recovery czyści maszyny w dodatkowym regionie odzyskiwania po awarii, po zakończeniu powrotu po awarii i ponownym włączeniu ochrony maszyn wirtualnych. Nie ma potrzeby ręcznego usuwania maszyn wirtualnych i kart sieciowych w regionie pomocniczym. Należy pamiętać, że maszyny wirtualne z dyskami niezarządzanymi nie są czyszczone. W przypadku całkowitego wyłączenia replikacji po powrocie po awarii program Site Recovery czyści dyski w regionie odzyskiwania po awarii, a także maszyny wirtualne i karty sieciowe.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o przepływie ponownej ochrony.
