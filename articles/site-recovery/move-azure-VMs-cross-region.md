---
title: Przenoszenie maszyn wirtualnych platformy Azure do innego regionu za pomocą Azure Site Recovery
description: Używanie usługi Azure Site Recovery do przenoszenia maszyn wirtualnych IaaS platformy Azure z jednego regionu świadczenia usługi Azure do innego.
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 5ae930240872c00c8dbb45857e4e77d82766eadf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93398066"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do innego regionu platformy Azure

Można przenieść maszyny wirtualne infrastruktury jako usługi (IaaS) platformy Azure z jednego regionu do drugiego w celu poprawy niezawodności, dostępności, zarządzania lub ładu. W tym samouczku pokazano, jak przenieść maszyny wirtualne do innego regionu przy użyciu Azure Site Recovery. Omawiane tematy:

> [!div class="checklist"]
> * Weryfikacja wymagań wstępnych
> * Przygotowywanie źródłowych maszyn wirtualnych
> * Przygotowywanie regionu docelowego
> * Kopiowanie danych do regionu docelowego
> * Testowanie konfiguracji
> * Przenoszenie
> * Odrzuć zasoby z regionu źródłowego


> [!IMPORTANT]
> W tym artykule opisano *,* jak przenieść maszyny wirtualne platformy Azure z jednego regionu do innego. Jeśli chcesz zwiększyć dostępność infrastruktury przez przeniesienie maszyn wirtualnych do stref dostępności, zobacz [przenoszenie maszyn wirtualnych platformy Azure do strefy dostępności](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że masz maszyny wirtualne platformy Azure w źródłowym regionie platformy Azure, *z* którego chcesz przejść.
- Sprawdź, czy wybór kombinacji regionów źródłowych i regionu [docelowego jest obsługiwany](./azure-to-azure-support-matrix.md#region-support), i uważnie wybierz region docelowy.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Sprawdź uprawnienia konta. Jeśli właśnie utworzono bezpłatne konto platformy *Azure, jesteś* administratorem subskrypcji. Jeśli nie jesteś administratorem, skontaktuj się z administratorem, aby uzyskać wymagane uprawnienia:
  -  Aby włączyć replikację maszyny wirtualnej i skopiować dane do obiektu docelowego przy użyciu Site Recovery, musisz mieć uprawnienia do tworzenia maszyny wirtualnej w zasobach platformy Azure. Wbudowana rola „Współautor maszyny wirtualnej” ma te uprawnienia. Za pomocą uprawnień można:
        - Tworzenie maszyny wirtualnej w wybranej grupie zasobów.
        - Tworzenie maszyny wirtualnej w wybranej sieci wirtualnej.
        - Zapis na wybranym koncie magazynu.

  - Wymagane są również uprawnienia do zarządzania operacjami Site Recovery. Rola współautor Site Recovery ma wszystkie uprawnienia wymagane do zarządzania operacjami Site Recovery w magazynie Recovery Services platformy Azure.

## <a name="prepare-the-source-vms"></a>Przygotowywanie źródłowych maszyn wirtualnych

1. Sprawdź, czy maszyny wirtualne platformy Azure, które planujesz przenieść, mają najnowsze certyfikaty główne. Jeśli nie, nie można włączyć kopiowania danych do regionu docelowego z powodu ograniczeń zabezpieczeń.

    - W przypadku maszyn wirtualnych z systemem Windows zainstaluj najnowsze aktualizacje systemu Windows, aby wszystkie zaufane certyfikaty główne były na tym komputerze. W środowisku odłączonym postępuj zgodnie ze standardowymi procesami Windows Update i aktualizacji certyfikatu w organizacji.
    - W przypadku maszyn wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami od dystrybutora systemu Linux w celu uzyskania najnowszych zaufanych certyfikatów głównych i listy odwołania certyfikatów.
2. Upewnij się, że nie używasz serwera proxy uwierzytelniania do kontrolowania łączności sieciowej dla maszyn wirtualnych, które zamierzasz przenieść.
3. Jeśli maszyna wirtualna, którą chcesz przenieść, nie ma dostępu do Internetu i używa serwera proxy zapory do kontrolowania dostępu wychodzącego, sprawdź [wymagania](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).
4. Udokumentowanie układu sieci źródłowej i wszystkich aktualnie używanych zasobów, w tym między innymi modułów równoważenia obciążenia, sieciowych grup zabezpieczeń i publicznych adresów IP w celu weryfikacji.

## <a name="prepare-the-target-region"></a>Przygotowywanie regionu docelowego

1. W ramach subskrypcji platformy Azure Sprawdź, czy można tworzyć maszyny wirtualne w regionie docelowym używanym do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału w razie potrzeby.

2. Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów do obsługi Twoich źródłowych maszyn wirtualnych. Jeśli używasz Site Recovery do kopiowania danych do obiektu docelowego, wybiera on ten sam rozmiar lub najbliższy dostępny rozmiar dla docelowych maszyn wirtualnych.

3. Upewnij się, że tworzysz zasób docelowy dla każdego składnika, który został zidentyfikowany w układzie sieci źródłowej. Dzięki temu maszyny wirtualne będą miały wszystkie funkcje i funkcje w regionie docelowym, które znajdowały się w regionie źródłowym.

   Azure Site Recovery automatycznie wykrywa i tworzy sieć wirtualną i konto magazynu po włączeniu replikacji dla źródłowej maszyny wirtualnej. Możesz również wstępnie utworzyć te zasoby i przypisać je do maszyny wirtualnej w ramach kroku włączania replikacji. Ale należy ręcznie utworzyć wszystkie inne zasoby w regionie docelowym. Zapoznaj się z poniższymi dokumentami, aby utworzyć najczęściej używane zasoby sieciowe na podstawie konfiguracji źródłowej maszyny wirtualnej:

   - [Sieciowe grupy zabezpieczeń](../virtual-network/manage-network-security-group.md)
   - [Moduły równoważenia obciążenia](../load-balancer/index.yml)
   - [Publiczny adres IP](../virtual-network/virtual-network-public-ip-address.md)
    
   Wszystkie inne składniki sieci można znaleźć w dokumentacji dotyczącej [sieci platformy Azure](../index.yml?pivot=products&panel=network). 

4. Aby przetestować konfigurację przed wykonaniem przeniesienia, ręcznie [Utwórz sieć nieprodukcyjną](../virtual-network/quick-create-portal.md) w regionie docelowym. Testowanie instalacji powoduje utworzenie minimalnych zakłóceń w środowisku produkcyjnym i zalecamy ich użycie.
    
## <a name="copy-data-to-the-target-region"></a>Kopiowanie danych do regionu docelowego
Poniższe kroki używają Azure Site Recovery do kopiowania danych do regionu docelowego.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Tworzenie magazynu w dowolnym regionie, z wyjątkiem źródła

1. Zaloguj się do Recovery Services [Azure Portal](https://portal.azure.com)  >  .
2. Wybierz pozycję **Utwórz zasób**  >  **Narzędzia do zarządzania** zasobami  >  **i Site Recovery**.
3. W polu **Nazwa** Określ przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
4. Utwórz grupę zasobów **ContosoRG**.
5. Określ region platformy Azure. Aby sprawdzić Obsługiwane regiony, zobacz [szczegóły cennika Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. W przypadku magazynów Recovery Services wybierz pozycję **Przegląd**  >  **ConsotoVMVault**  >  **+ replikacja**.
7. W obszarze **Źródło** wybierz pozycję **Azure**.
8. W polu **lokalizacja źródłowa** wybierz źródłowy region platformy Azure, w którym są obecnie uruchomione maszyny wirtualne.
9. Wybierz model wdrażania Azure Resource Manager. Następnie wybierz **subskrypcję źródłową** i **źródłową grupę zasobów**.
10. Wybierz **przycisk OK** , aby zapisać ustawienia.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Włącz replikację maszyn wirtualnych platformy Azure i Rozpocznij kopiowanie danych

Site Recovery pobiera listę maszyn wirtualnych skojarzonych z subskrypcją i grupą zasobów.

1. Wybierz maszynę wirtualną, którą chcesz przenieść, a następnie wybierz pozycję **OK**.
2. W obszarze **Ustawienia** wybierz pozycję **odzyskiwanie po awarii**.
3. W obszarze Skonfiguruj region docelowy **odzyskiwania po awarii**  >  Wybierz docelowy region, do którego jest wykonywana replikacja.
4. Wybierz, aby użyć domyślnych zasobów docelowych lub tych, które zostały wcześniej utworzone.
5. Wybierz pozycję **Włącz replikację** , aby uruchomić zadanie.


 

## <a name="test-the-configuration"></a>Testowanie konfiguracji


1. Przejdź do magazynu. W obszarze **Ustawienia**  >  **zreplikowane elementy** wybierz maszynę wirtualną, która ma zostać przeniesiona do regionu docelowego. Następnie wybierz pozycję **test pracy w trybie failover**.
2. W obszarze **test pracy w trybie failover** wybierz punkt odzyskiwania do użycia w trybie failover:

   - **Najnowszy przetworzony**: wprowadza maszynę wirtualną w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez usługę Site Recovery. Wyświetlana jest sygnatura czasowa. Brak czasu poświęcanego na przetwarzanie danych, dlatego ta opcja zapewnia niski cel czasu odzyskiwania (RTO).
   - **Najnowsza spójna dla aplikacji**: przełączenie wszystkich maszyn wirtualnych do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji. Wyświetlana jest sygnatura czasowa.
   - **Niestandardowy**: można wybrać dowolny punkt odzyskiwania.

3. Wybierz docelową sieć wirtualną platformy Azure, do której chcesz przenieść maszyny wirtualne platformy Azure, aby przetestować konfigurację.

   > [!IMPORTANT]
   > Zalecamy używanie oddzielnej sieci maszyn wirtualnych platformy Azure do testowania pracy w trybie failover, a nie sieci produkcyjnej w regionie docelowym.

4. Aby rozpocząć testowanie przenoszenia, wybierz **przycisk OK**. Aby śledzić postęp, wybierz maszynę wirtualną, aby otworzyć jej **właściwości.** Lub wybierz zadanie **test pracy w trybie failover** w magazynie. Następnie wybierz kolejno pozycje **Ustawienia**  >  **zadania**  >  **Site Recovery zadania**.
5. Po zakończeniu trybu failover w obszarze Azure Portal > **Maszyny wirtualne** będzie widoczna replika maszyny wirtualnej platformy Azure. Upewnij się, że maszyna wirtualna jest uruchomiona, ma właściwy rozmiar i została połączona z odpowiednią siecią.
6. Aby usunąć maszynę wirtualną utworzoną do testowania, wybierz pozycję **Oczyść test pracy w trybie failover** dla zreplikowanego elementu. W obszarze **notatki** Zarejestruj i Zapisz wszelkie obserwacje związane z testem.

## <a name="perform-the-move-and-confirm"></a>Wykonaj przeniesienie i Potwierdź

1. Przejdź do magazynu w obszarze **Ustawienia**  >  **zreplikowane elementy**, wybierz maszynę wirtualną, a następnie wybierz **pozycję Tryb failover**.
1. W obszarze **tryb failover** wybierz pozycję **najnowsze**. 
2. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Jednak tryb failover kontynuuje działanie nawet w przypadku niepowodzenia wyłączenia. Postęp pracy w trybie failover można wykonać na stronie **zadań** .
3. Po zakończeniu zadania Sprawdź, czy maszyna wirtualna jest wyświetlana w docelowym regionie platformy Azure zgodnie z oczekiwaniami.
4. W obszarze **zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie wybierz pozycję **Zatwierdź**. Spowoduje to zakończenie przenoszenia. Poczekaj na zakończenie zadania zatwierdzania.

## <a name="discard-the-resources-from-the-source-region"></a>Odrzuć zasoby z regionu źródłowego

- Przejdź do maszyny wirtualnej i wybierz pozycję **Wyłącz replikację**. Spowoduje to zatrzymanie procesu kopiowania danych maszyny wirtualnej.

  > [!IMPORTANT]
  > Wykonaj ten krok, aby uniknąć naliczania opłat za replikację Site Recovery po przeniesieniu.

Jeśli nie planujesz ponownego użycia zasobów źródłowych, wykonaj następujące czynności:

1. Usuń wszystkie odpowiednie zasoby sieciowe w regionie źródłowym wymienionym w kroku 4 [Przygotowywanie źródłowych maszyn wirtualnych](#prepare-the-source-vms).
2. Usuń odpowiednie konto magazynu w regionie źródłowym.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób przenoszenia maszyn wirtualnych platformy Azure do innego regionu platformy Azure. Teraz można skonfigurować odzyskiwanie po awarii dla tych maszyn wirtualnych.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)
