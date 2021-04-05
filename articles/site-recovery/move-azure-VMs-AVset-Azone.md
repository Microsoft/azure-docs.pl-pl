---
title: Przenoszenie maszyn wirtualnych do regionu platformy Azure ze strefami dostępności przy użyciu Azure Site Recovery
description: Dowiedz się, jak przenieść maszyny wirtualne do strefy dostępności w innym regionie przy użyciu Site Recovery
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 8224ae4a48bb4915492240c414b90edb86a4c258
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93393136"
---
# <a name="move-azure-vms-into-availability-zones"></a>Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności

W tym artykule opisano sposób przenoszenia maszyn wirtualnych platformy Azure do strefy dostępności w innym regionie. Jeśli chcesz przejść do innej strefy w tym samym regionie, [zapoznaj się z tym artykułem](./azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).


Strefy dostępności na platformie Azure pomaga chronić aplikacje i dane przed awariami centrum danych. Każda strefa dostępności składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności istnieją co najmniej trzy osobne strefy we wszystkich włączonych regionach. Fizyczne rozdzielenie Strefy dostępności w regionie pomaga chronić aplikacje i dane przed awariami centrów danych. Dzięki Strefy dostępności platforma Azure oferuje umowę dotyczącą poziomu usług (SLA) wynoszącą 99,99% czasu na czas pracy maszyn wirtualnych. Strefy dostępności są obsługiwane w wybranych regionach, jak wspomniano w [regionach, które obsługują strefy dostępności](../availability-zones/az-region.md).

W scenariuszu, w którym maszyny wirtualne są wdrażane jako *pojedyncze wystąpienie* w określonym regionie i chcesz poprawić dostępność przez przeniesienie tych maszyn wirtualnych do strefy dostępności, możesz to zrobić za pomocą Azure Site Recovery. Tę akcję można następnie podzielić na:

- Przenoszenie maszyn wirtualnych z pojedynczym wystąpieniem do Strefy dostępności w regionie docelowym
- Przenoszenie maszyn wirtualnych w zestawie dostępności do Strefy dostępności w regionie docelowym

> [!IMPORTANT]
> Aby przenieść maszyny wirtualne platformy Azure do strefy dostępności w innym regionie, zalecamy teraz korzystanie z funkcji [przenoszenia zasobów platformy Azure](../resource-mover/move-region-availability-zone.md). Program przenoszenia zasobów jest w publicznej wersji zapoznawczej i oferuje następujące informacje:
> - Jedno centrum do przemieszczania zasobów między regionami.
> - Krótszy czas przenoszenia i złożoność. Wszystko, czego potrzebujesz, znajduje się w jednej lokalizacji.
> - Proste i spójne środowisko do przemieszczania różnych typów zasobów platformy Azure.
> - Prosty sposób identyfikowania zależności między zasobami, które chcesz przenieść. Ułatwia to przenoszenie powiązanych zasobów razem, dzięki czemu wszystko działa zgodnie z oczekiwaniami w regionie docelowym po przeniesieniu.
> - Automatyczne czyszczenie zasobów w regionie źródłowym, jeśli chcesz je usunąć po przeniesieniu.
> - Testowy. Możesz wypróbować przechodzenie, a następnie odrzucić go, jeśli nie chcesz wykonać pełnego przeniesienia.



## <a name="check-prerequisites"></a>Sprawdzanie wymagań wstępnych

- Sprawdź, czy region docelowy [obsługuje strefy dostępności](../availability-zones/az-region.md). Sprawdź, czy wybór [kombinacji regionu źródłowego/regionu docelowego jest obsługiwany](./azure-to-azure-support-matrix.md#region-support). Podejmowanie świadomej decyzji w regionie docelowym.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Sprawdź uprawnienia konta. Jeśli właśnie utworzono bezpłatne konto platformy Azure, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, współpracuj z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla maszyny wirtualnej i ostatecznie skopiować dane do obiektu docelowego przy użyciu Azure Site Recovery, musisz dysponować:

    1. Uprawnienie do tworzenia maszyny wirtualnej w zasobach platformy Azure. Wbudowana rola *współautor maszyny wirtualnej* ma następujące uprawnienia:
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
        - Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
        - Uprawnienie do zapisu na wybranym koncie magazynu

    2. Uprawnienie do zarządzania zadaniami Azure Site Recovery. Rola *współautor Site Recovery* ma wszystkie uprawnienia wymagane do zarządzania akcjami Site Recovery w magazynie Recovery Services.

## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

1. Maszyny wirtualne powinny używać usługi Managed disks, jeśli chcesz przenieść je do strefy dostępności przy użyciu Site Recovery. Istnieje możliwość przekonwertowania istniejących maszyn wirtualnych z systemem Windows, które używają dysków niezarządzanych do korzystania z dysków zarządzanych. Wykonaj kroki opisane w sekcji [konwertowanie maszyny wirtualnej z systemem Windows z dysków niezarządzanych na dyski zarządzane](../virtual-machines/windows/convert-unmanaged-to-managed-disks.md). Upewnij się, że zestaw dostępności jest skonfigurowany jako *zarządzany*.
2. Sprawdź, czy na maszynach wirtualnych platformy Azure, które mają zostać przeniesione, są obecne wszystkie najnowsze certyfikaty główne. Jeśli brakuje najnowszych certyfikatów głównych, nie można włączyć kopiowania danych do regionu docelowego z powodu ograniczeń zabezpieczeń.

3. Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku odłączonym postępuj zgodnie ze standardowymi procesami aktualizacji usługi Windows Update i certyfikatów dla swojej organizacji.

4. W przypadku maszyn wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dostarczonymi przez dystrybutora systemu Linux w celu uzyskania najnowszych zaufanych certyfikatów głównych i listy odwołania certyfikatów na maszynie wirtualnej.
5. Upewnij się, że nie używasz serwera proxy uwierzytelniania do kontrolowania łączności sieciowej dla maszyn wirtualnych, które chcesz przenieść.

6. Sprawdź [wymagania dotyczące łączności wychodzącej dla maszyn wirtualnych](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

7. Określ układ sieci źródłowej i aktualnie używane zasoby do weryfikacji, w tym moduły równoważenia obciążenia, sieciowych grup zabezpieczeń i publiczny adres IP.

## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. Sprawdź, czy Twoja subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym używanym do odzyskiwania po awarii. W razie potrzeby skontaktuj się z pomocą techniczną, aby włączyć wymagany przydział.

2. Upewnij się, że zasoby subskrypcji są wystarczające do obsługi maszyn wirtualnych o rozmiarach odpowiadających źródłowym maszynom wirtualnym. Jeśli używasz Site Recovery do kopiowania danych do obiektu docelowego, wybiera on ten sam rozmiar lub najbliższy możliwy rozmiar dla docelowej maszyny wirtualnej.

3. Utwórz zasób docelowy dla każdego składnika identyfikowanego w układzie sieci źródłowej. Ta akcja zapewnia, że po przecięciu do regionu docelowego maszyny wirtualne mają wszystkie funkcje i funkcje, które były dostępne w źródle.

    > [!NOTE]
    > Azure Site Recovery automatycznie wykrywa i tworzy sieć wirtualną i konto magazynu po włączeniu replikacji dla źródłowej maszyny wirtualnej. Możesz również wstępnie utworzyć te zasoby i przypisać je do maszyny wirtualnej w ramach kroku włączania replikacji. Ale w przypadku innych zasobów, jak wspomniano później, należy je ręcznie utworzyć w regionie docelowym.

     Następujące dokumenty przedstawiają sposób tworzenia najczęściej używanych zasobów sieciowych, które są odpowiednie dla Ciebie, na podstawie konfiguracji źródłowej maszyny wirtualnej.

    - [Sieciowe grupy zabezpieczeń](../virtual-network/manage-network-security-group.md)
    - [Moduły równoważenia obciążenia](../load-balancer/index.yml)
    - [Publiczny adres IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Wszystkie inne składniki sieci można znaleźć w [dokumentacji](../index.yml?pivot=products&panel=network)dotyczącej sieci.

    > [!IMPORTANT]
    > Upewnij się, że w miejscu docelowym jest używany nadmiarowy moduł równoważenia obciążenia strefy. Więcej informacji można znaleźć pod adresem [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](../load-balancer/load-balancer-standard-availability-zones.md).

4. Ręcznie [Utwórz sieć nieprodukcyjną](../virtual-network/quick-create-portal.md) w regionie docelowym, jeśli chcesz przetestować konfigurację przed przecięciem na region docelowy. Zalecamy takie podejście, ponieważ powoduje to minimalne zakłócenie w środowisku produkcyjnym.

## <a name="enable-replication"></a>Włączanie replikacji
Poniższe kroki poprowadzą Cię przez Azure Site Recovery, aby włączyć replikację danych do regionu docelowego przed przeniesieniem ich do Strefy dostępności.

> [!NOTE]
> Te kroki dotyczą pojedynczej maszyny wirtualnej. Można to zrobić na wielu maszynach wirtualnych. Przejdź do magazynu Recovery Services, wybierz pozycję **+ Replikuj**, a następnie wybierz odpowiednie maszyny wirtualne.

1. W Azure Portal wybierz pozycję **maszyny wirtualne**, a następnie wybierz maszynę wirtualną, do której chcesz przenieść strefy dostępności.
2. W obszarze **Operacja** wybierz pozycję **Odzyskiwanie po awarii**.
3. W obszarze **Konfigurowanie odzyskiwania po awarii** > **Region docelowy** wybierz region docelowy, w którym maszyna będzie replikowana. Upewnij się, że ten region [obsługuje](../availability-zones/az-region.md) strefy dostępności.
4. Wybierz pozycję **Dalej: Ustawienia zaawansowane**.
5. Wybierz odpowiednie wartości dla subskrypcji docelowej, docelowej grupy zasobów maszyny wirtualnej i sieci wirtualnej.
6. W sekcji **dostępność** wybierz strefę dostępności, w której chcesz przenieść maszynę wirtualną. 
   > [!NOTE]
   > Jeśli nie widzisz opcji zestawu dostępności lub strefy dostępności, upewnij się, że [wymagania wstępne](#prepare-the-source-vms) zostały spełnione i ukończono [Przygotowywanie](#prepare-the-source-vms) źródłowych maszyn wirtualnych.
  

7. Wybierz pozycję **Włącz replikację**. Ta akcja uruchamia zadanie, aby włączyć replikację dla maszyny wirtualnej.

## <a name="check-settings"></a>Sprawdź ustawienia

Po zakończeniu zadania replikacji można sprawdzić stan replikacji, zmodyfikować ustawienia replikacji i przetestować wdrożenie.

1. W menu maszyny wirtualnej wybierz pozycję **Odzyskiwanie po awarii**.
2. Można sprawdzić kondycję replikacji, utworzone punkty odzyskiwania oraz źródłowe i docelowe regiony na mapie.


## <a name="test-the-configuration"></a>Testowanie konfiguracji

1. W menu maszyny wirtualnej wybierz pozycję  **odzyskiwanie po awarii**.
2. Wybierz ikonę **test pracy w trybie failover** .
3. W obszarze **test pracy w trybie failover** wybierz punkt odzyskiwania do użycia w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Ta opcja zapewnia niską wartość celu czasu odzyskiwania (RTO, Recovery Time Objective), ponieważ nie trzeba poświęcać czasu na przetwarzanie danych.
   - **Najnowszy spójny na poziomie aplikacji**: ta opcja wprowadza wszystkie maszyny wirtualne w tryb failover do najnowszego spójnego na poziomie aplikacji punktu odzyskiwania. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz testową docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację. 

    > [!IMPORTANT]
    > Zalecamy użycie oddzielnej sieci maszyn wirtualnych platformy Azure dla niepowodzenia testu, a nie sieci produkcyjnej w regionie docelowym, w której chcesz przenieść maszyny wirtualne.

4. Aby rozpocząć testowanie przenoszenia, wybierz **przycisk OK**. Aby śledzić postęp, wybierz maszynę wirtualną, aby otworzyć jej właściwości. Można też wybrać zadanie **test pracy w trybie failover** w polu Nazwa magazynu > **Ustawienia**  >  **zadania**  >  **Site Recovery zadania**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Jeśli chcesz usunąć maszynę wirtualną utworzoną w ramach testowania przenoszenia, wybierz pozycję **Oczyść test pracy w trybie failover** dla zreplikowanego elementu. W obszarze **Uwagi** zarejestruj i zapisz wszelkie obserwacje związane z testem.

## <a name="move-to-the-target-region-and-confirm"></a>Przejdź do regionu docelowego i Potwierdź

1.  W menu maszyny wirtualnej wybierz pozycję  **odzyskiwanie po awarii**.
2. Wybierz ikonę **trybu failover** .
3. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**. 
4. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy w trybie failover można wykonać na stronie **zadań** . 
5. Po zakończeniu zadania Sprawdź, czy maszyna wirtualna jest wyświetlana w docelowym regionie platformy Azure zgodnie z oczekiwaniami.
6. W obszarze **Replikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie kliknij pozycję **Zatwierdź**. Spowoduje to zakończenie procesu przenoszenia do regionu docelowego. Poczekaj na zakończenie zadania zatwierdzania.

## <a name="discard-the-resource-in-the-source-region"></a>Odrzucanie zasobu w regionie źródłowym

Przejdź do maszyny wirtualnej. Wybierz pozycję **Wyłącz replikację**. Ta akcja powoduje zatrzymanie procesu kopiowania danych dla maszyny wirtualnej.  

> [!IMPORTANT]
> Wykonaj poprzedni krok, aby uniknąć naliczania opłat za Site Recovery replikację po przeniesieniu. Ustawienia replikacji źródła są automatycznie czyszczone. Należy zauważyć, że rozszerzenie Site Recovery instalowane w ramach replikacji nie jest usuwane i należy je usunąć ręcznie.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zwiększono dostępność maszyny wirtualnej platformy Azure przez przejście do zestawu dostępności lub strefy dostępności. Teraz można skonfigurować odzyskiwanie po awarii dla przenoszonej maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)
