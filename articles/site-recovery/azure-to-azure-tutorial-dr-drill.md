---
title: Uruchom drążenie odzyskiwania po awarii maszyny wirtualnej platformy Azure za pomocą Azure Site Recovery
description: Dowiedz się, jak uruchomić przechodzenie awaryjne w regionie pomocniczym dla maszyn wirtualnych platformy Azure przy użyciu usługi Azure Site Recovery.
services: site-recovery
ms.topic: tutorial
ms.date: 01/16/2020
ms.custom: mvc
ms.openlocfilehash: b2ce157f0f192135ab0507e4aae4c0a282bda1ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76166193"
---
# <a name="run-a-disaster-recovery-drill-to-a-secondary-region-for-azure-vms"></a>Uruchom przechodzenie do szczegółów odzyskiwania po awarii do regionu pomocniczego dla maszyn wirtualnych platformy Azure

Usługa [Azure Site Recovery](site-recovery-overview.md) przyczynia się do strategii związanej z ciągłością biznesową i odzyskiwaniem po awarii (BCDR, business continuity and disaster recovery) przez zapewnienie niezawodnego działania aplikacji biznesowych i ich dostępności podczas planowanych lub nieplanowanych przestojów. Usługa Site Recovery zarządza odzyskiwaniem po awarii maszyn lokalnych i maszyn wirtualnych platformy Azure, a także organizuje to odzyskiwanie. Obejmuje to replikację, przechodzenie w tryb failover i odzyskiwanie.

W tym samouczku przedstawiono sposób uruchamiania próbnego odzyskiwania po awarii dla maszyn wirtualnych platformy Azure z jednego regionu platformy Azure w innym, wraz z testem trybu failover. Ta próba pozwala zweryfikować strategię replikacji bez utraty danych ani przestoju i nie ma wpływu na środowisko produkcyjne. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdzanie wymagań wstępnych
> * Uruchamianie testu trybu failover dla pojedynczej maszyny wirtualnej

> [!NOTE]
> Ten samouczek ułatwia przeprowadzenie drążenia awaryjnego z minimalnymi krokami. Aby dowiedzieć się więcej na temat różnych funkcji związanych z przechodzeniem do odzyskiwania po awarii, zobacz dokumentację dotyczącą [replikacji](azure-to-azure-how-to-enable-replication.md)maszyn wirtualnych platformy Azure, [sieci](azure-to-azure-about-networking.md), [automatyzacji](azure-to-azure-powershell.md)lub [rozwiązywania problemów](azure-to-azure-troubleshoot-errors.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem tego samouczka zapoznaj się z następującymi elementami:

- Przed uruchomieniem testowego przejścia w tryb failover zalecamy sprawdzenie właściwości maszyny wirtualnej w celu upewnienia się, że jest ona skonfigurowana pod kątem odzyskiwania po awarii. Przejdź do **Operations**  >  **Właściwości odzyskiwania po awarii**operacji maszyny wirtualnej,  >  **Properties** aby wyświetlić właściwości replikacji i trybu failover.
- **Zalecamy, aby do testowania trybu failover użyć oddzielnej sieci maszyn wirtualnych platformy Azure**, a nie domyślnej sieci skonfigurowanej podczas włączania replikacji.
- W zależności od konfiguracji sieci źródłowej dla każdej karty sieciowej można określić **podsieć**, **prywatny adres**IP, **publicznych adresów IP**, **sieciową grupę zabezpieczeń**lub **moduł równoważenia obciążenia** w celu dołączenia do każdej karty sieciowej w obszarze Ustawienia testu pracy w trybie failover w **obliczeniach i sieci** przed wykonaniem operacji drążenia odzyskiwania po awarii.

## <a name="run-a-test-failover"></a>Wykonywanie próby przejścia w tryb failover

Ten przykład pokazuje, jak używać magazynu Recovery Services do przeprowadzenia testowej pracy w trybie failover.

1. Wybierz magazyn i przejdź do **pozycji chronione elementy**  >  **zreplikowane elementy** i wybierz maszynę wirtualną.
1. W obszarze **test pracy w trybie failover**wybierz punkt odzyskiwania do użycia w trybie failover:
   - **Najnowsze**: przetwarza wszystkie dane w Site Recovery i zapewnia najniższy RTO (cel czasu odzyskiwania).
   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Po wybraniu tej opcji nie ma czasu poświęcanego na przetwarzanie danych, więc zapewnia to niską RTO.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowa**: przełączenie w tryb failover do określonego punktu odzyskiwania. Element Custom jest dostępny tylko po przełączeniu w tryb failover pojedynczej maszyny wirtualnej, a nie w przypadku pracy w trybie awaryjnym z planem odzyskiwania.
1. Wybierz docelową sieć wirtualną platformy Azure, z którą maszyny wirtualne platformy Azure w regionie pomocniczym będą się łączyć po przejściu w tryb failover.

   > [!NOTE]
   > Jeśli ustawienia testowej pracy w trybie failover są wstępnie skonfigurowane dla zreplikowanego elementu, menu rozwijane służące do wybierania sieci wirtualnej platformy Azure nie jest widoczne.

1. Aby rozpocząć pracę w trybie failover, wybierz **przycisk OK**. Aby śledzić postęp z magazynu, przejdź do pozycji **monitorowanie**  >  **Site Recovery zadania** i wybierz zadanie **test pracy w trybie failover** .
1. Po zakończeniu pracy w trybie failover replika maszyny wirtualnej platformy Azure zostanie wyświetlona w **Virtual Machines**Azure Portal. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
1. Aby usunąć maszyny wirtualne, które zostały utworzone podczas testu pracy w trybie failover, wybierz pozycję **Oczyść test pracy w trybie failover** dla zreplikowanego elementu lub planu odzyskiwania. W obszarze **uwagi**Zarejestruj i Zapisz wszelkie obserwacje związane z testem pracy w trybie failover.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uruchamianie produkcyjnego trybu failover](azure-to-azure-tutorial-failover-failback.md)
