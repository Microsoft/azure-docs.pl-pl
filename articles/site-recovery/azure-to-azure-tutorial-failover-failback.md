---
title: Przejdź w tryb failover i ponownie Włącz ochronę maszyn wirtualnych platformy Azure replikowanych do pomocniczego regionu platformy Azure na potrzeby odzyskiwania po awarii za pomocą usługi Azure Site Recovery.
description: Dowiedz się, jak przełączyć się w tryb failover i ponownie włączyć ochronę maszyn wirtualnych platformy Azure replikowanych do pomocniczego regionu platformy Azure na potrzeby odzyskiwania po awarii przy użyciu usługi Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 8d38aa513b0829c2626fcd4a92c40faabff1f83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502396"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Praca awaryjna i ponowna ochrona maszyn wirtualnych platformy Azure między regionami

W tym samouczku opisano sposób przełączenia maszyny wirtualnej platformy Azure w tryb failover na pomocniczy region platformy Azure z usługą [Azure Site Recovery](site-recovery-overview.md) . Po przełączeniu w tryb failover ponownie Włącz ochronę maszyny wirtualnej. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przełączanie w tryb failover maszyny wirtualnej platformy Azure
> * Ponownie Włącz ochronę pomocniczej maszyny wirtualnej platformy Azure, tak aby była replikowana do regionu podstawowego.

> [!NOTE]
> Ten samouczek zawiera najprostszą ścieżkę z ustawieniami domyślnymi i minimalnym dostosowaniem. W przypadku bardziej złożonych scenariuszy Użyj artykułów z sekcji "jak to" dla maszyn wirtualnych platformy Azure.


## <a name="prerequisites"></a>Wymagania wstępne

- Przed rozpoczęciem Przejrzyj [często zadawane pytania](site-recovery-faq.md#failover) dotyczące trybu failover.
- Ukończenie [próbnego odzyskiwania po awarii](azure-to-azure-tutorial-dr-drill.md) w celu sprawdzenia, czy wszystko działa zgodnie z oczekiwaniami.
- Sprawdzenie właściwości maszyny wirtualnej przed uruchomieniem testu trybu failover. Maszyna wirtualna musi być zgodna z [wymaganiami platformy Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems).

## <a name="run-a-failover-to-the-secondary-region"></a>Uruchamianie trybu failover w regionie pomocniczym

1. W obszarze **Replikowane elementy** wybierz maszynę wirtualną, która ma być przełączona w tryb failover, a następnie wybierz pozycję **Tryb failover**

   ![Zrzut ekranu przedstawiający opcje trybu failover dla maszyny wirtualnej.](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:

   * **Najnowsza** (domyślnie): przetwarza wszystkie dane w usłudze Site Recovery i zapewnia najniższy cel punktu odzyskiwania (RPO).
   * **Najnowsza przetworzony**: przywraca maszynę wirtualną do najnowszego punktu odzyskiwania, który został przetworzony przez usługę Site Recovery.
   * **Niestandardowa**: przełączenie w tryb failover do określonego punktu odzyskiwania. Ta opcja jest przydatna na potrzeby wykonywania testu trybu failover.

3. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , jeśli chcesz, aby Site Recovery próbuje wykonać zamknięcie źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Zamknięcie pomaga zapewnić brak utracie danych. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Site Recovery nie czyści źródła po przejściu w tryb failover.

4. Postęp przełączania w tryb failover można śledzić na karcie **Zadania**.

5. Po przejściu w tryb failover zweryfikuj maszynę wirtualną, logując się do niej. Jeśli chcesz przejść do innego punktu odzyskiwania dla maszyny wirtualnej, możesz użyć opcji **Zmień punkt odzyskiwania**.

6. Po poprawnym skonfigurowaniu przełączania maszyny wirtualnej w tryb failover możesz **zatwierdzić** tryb failover.
   Zatwierdzenie powoduje usunięcie wszystkich punktów odzyskiwania dostępnych w usłudze. Punkt odzyskiwania nie będzie teraz można zmienić.

> [!NOTE]
> Po przełączeniu maszyny wirtualnej w tryb failover, do której dodano dysk po włączeniu replikacji dla maszyny wirtualnej, punkty replikacji będą zawierać dyski dostępne do odzyskania. Na przykład jeśli maszyna wirtualna ma jeden dysk i dodasz nowy, punkty replikacji, które zostały utworzone przed dodaniem dysku, będą wskazywać, że punkt replikacji składa się z "1 z 2 dysków".

![Zrzut ekranu przedstawiający tryb failover z dodanym dyskiem.](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>Ponowne włączanie ochrony pomocniczej maszyny wirtualnej

Po przełączeniu maszyny wirtualnej w tryb failover należy ponownie włączyć jej ochronę, aby ponownie zreplikować ją do regionu podstawowego.

1. Upewnij się, że maszyna wirtualna jest w stanie **Tryb failover zatwierdzony**, a następnie sprawdź, czy region podstawowy jest dostępny oraz czy możesz utworzyć w nim nowe zasoby i uzyskać do nich dostęp.
2. W **Vault**obszarze  >  **zreplikowane elementy**magazynu kliknij prawym przyciskiem myszy maszynę wirtualną, która została przełączona w tryb failover, a następnie wybierz pozycję **Włącz ponownie ochronę**.

   ![Zrzut ekranu opcji ponownej ochrony maszyny wirtualnej.](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Sprawdź, czy kierunek ochrony, dodatkowy do regionu podstawowego, jest już zaznaczony.
3. Przejrzyj informacje o **grupie zasobów, sieci, magazynie i zestawach dostępności**. Wszystkie zasoby oznaczone jako nowe są tworzone w ramach operacji ponownego włączania ochrony.
4. Kliknij przycisk **OK**, aby wyzwolić zadanie włączania ponownej ochrony. To zadanie inicjuje lokację docelową przy użyciu najnowszych danych. Następnie replikuje zmiany do regionu podstawowego. Maszyna wirtualna jest teraz w stanie chronionym.

## <a name="next-steps"></a>Następne kroki
- Po włączeniu ochrony należy [dowiedzieć](azure-to-azure-tutorial-failback.md) się, jak powrócić po awarii do regionu podstawowego, gdy jest on dostępny.
- [Dowiedz się więcej](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) o przepływie ponownej ochrony.
