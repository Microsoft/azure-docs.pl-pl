---
title: Przenoszenie maszyn wirtualnych platformy Azure do innego regionu platformy Azure za pomocą Azure Site Recovery
description: Użyj Azure Site Recovery, aby przenieść maszyny wirtualne platformy Azure z jednego regionu świadczenia usługi Azure do innego.
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: 076adbfd4cecf7dae9ffc490e911fcb7ffce48e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394836"
---
# <a name="move-vms-to-another-azure-region"></a>Przenoszenie maszyn wirtualnych do innego regionu platformy Azure

Istnieją scenariusze, w których należy przenieść istniejące maszyny wirtualne IaaS platformy Azure z jednego regionu do innego. Na przykład chcesz zwiększyć niezawodność i dostępność istniejących maszyn wirtualnych, zwiększyć możliwości zarządzania lub przenieść się z powodów związanych z zarządzaniem. Aby uzyskać więcej informacji, zobacz [Omówienie przenoszenia maszyn wirtualnych platformy Azure](azure-to-azure-move-overview.md). 

Za pomocą usługi [Azure Site Recovery](site-recovery-overview.md) można przenieść maszyny wirtualne platformy Azure do regionu pomocniczego.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> 
> * Sprawdź wymagania wstępne dotyczące przenoszenia
> * Przygotowywanie źródłowych maszyn wirtualnych i regionu docelowego
> * Skopiuj dane i Włącz replikację
> * Przetestuj konfigurację i przeprowadź przeniesienie
> * Usuwanie zasobów w regionie źródłowym


> [!IMPORTANT]
> Aby przenieść maszyny wirtualne platformy Azure do innego regionu, zalecamy teraz korzystanie z funkcji [przenoszenia zasobów platformy Azure](../resource-mover/tutorial-move-region-virtual-machines.md). Program przenoszenia zasobów jest w publicznej wersji zapoznawczej i oferuje następujące informacje:
> - Jedno centrum do przemieszczania zasobów między regionami.
> - Krótszy czas przenoszenia i złożoność. Wszystko, czego potrzebujesz, znajduje się w jednej lokalizacji.
> - Proste i spójne środowisko do przemieszczania różnych typów zasobów platformy Azure.
> - Prosty sposób identyfikowania zależności między zasobami, które chcesz przenieść. Ułatwia to przenoszenie powiązanych zasobów razem, dzięki czemu wszystko działa zgodnie z oczekiwaniami w regionie docelowym po przeniesieniu.
> - Automatyczne czyszczenie zasobów w regionie źródłowym, jeśli chcesz je usunąć po przeniesieniu.
> - Testowy. Możesz wypróbować przechodzenie, a następnie odrzucić go, jeśli nie chcesz wykonać pełnego przeniesienia.



> [!NOTE]
> W tym samouczku pokazano, jak przenieść maszyny wirtualne platformy Azure z jednego regionu do innego. Jeśli potrzebujesz poprawić dostępność przez przeniesienie maszyn wirtualnych w zestawie dostępności do obszaru maszyny wirtualne przypięte do strefy w innym regionie, zobacz [samouczek przenoszenie maszyn wirtualnych platformy Azure do strefy dostępności](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że maszyny wirtualne platformy Azure znajdują się w regionie świadczenia usługi Azure, z którego chcesz przenieść.
- Sprawdź, czy wybór kombinacji regionu źródłowego i regionu [docelowego jest obsługiwany](./azure-to-azure-support-matrix.md#region-support), i podjęcie świadomej decyzji dotyczącej regionu docelowego.
- Przeanalizuj informacje o [składnikach i architekturze scenariusza](azure-to-azure-architecture.md).
- Zapoznaj się z [ograniczeniami i wymaganiami dotyczącymi pomocy technicznej](azure-to-azure-support-matrix.md).
- Sprawdź uprawnienia konta. Jeśli utworzono bezpłatne konto platformy Azure, jesteś administratorem subskrypcji. Jeśli nie jesteś administratorem subskrypcji, skontaktuj się z administratorem w celu przypisania potrzebnych uprawnień. Aby włączyć replikację dla maszyny wirtualnej i zasadniczo skopiować dane przy użyciu Azure Site Recovery, musisz dysponować:

    - Uprawnienia do tworzenia maszyny wirtualnej w ramach zasobów platformy Azure. Wbudowana rola współautor maszyny wirtualnej ma następujące uprawnienia:
    - Uprawnienie do tworzenia maszyny wirtualnej w wybranej grupie zasobów
    - Uprawnienie do tworzenia maszyny wirtualnej w wybranej sieci wirtualnej
    - Uprawnienie do zapisu na wybranym koncie magazynu
    
    - Uprawnienia do zarządzania operacjami Azure Site Recovery. Rola współautor Site Recovery ma wszystkie uprawnienia wymagane do zarządzania operacjami Site Recovery w magazynie Recovery Services.

- Upewnij się, że wszystkie najnowsze certyfikaty główne znajdują się na maszynach wirtualnych platformy Azure, które chcesz przenieść. Jeśli najnowsze certyfikaty główne nie znajdują się na maszynie wirtualnej, ograniczenia zabezpieczeń uniemożliwią skopiowanie danych do regionu docelowego.

- Aby zainstalować wszystkie zaufane certyfikaty główne na maszynach wirtualnych z systemem Windows, zainstaluj wszystkie najnowsze aktualizacje systemu Windows. W środowisku bez połączenia postępuj zgodnie ze standardową procedurą usługi Windows Update i procedurą aktualizacji certyfikatów, które są używane w danej organizacji.
    
- W przypadku maszyn wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dostarczonymi przez dystrybutora systemu Linux w celu uzyskania najnowszych zaufanych certyfikatów głównych i listy odwołania certyfikatów na maszynie wirtualnej.
- Upewnij się, że nie używasz serwera proxy uwierzytelniania do kontrolowania łączności sieciowej dla maszyn wirtualnych, które chcesz przenieść.

- Jeśli maszyna wirtualna, którą próbujesz przenieść, nie ma dostępu do Internetu lub używa serwera proxy zapory do kontrolowania dostępu wychodzącego, [Sprawdź wymagania](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

- Zidentyfikuj układ sieci źródłowej i wszystkie aktualnie używane zasoby. Obejmuje to między innymi usługi równoważenia obciążenia, sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i publiczne adresy IP.

- Sprawdź, czy subskrypcja platformy Azure umożliwia tworzenie maszyn wirtualnych w regionie docelowym używanym do odzyskiwania po awarii. Skontaktuj się z pomocą techniczną, aby włączyć wymagany limit przydziału.

- Upewnij się, że Twoja subskrypcja ma wystarczającą ilość zasobów do obsługi maszyn wirtualnych o rozmiarach, które pasują do Twoich źródłowych maszyn wirtualnych. Jeśli używasz Site Recovery do kopiowania danych do obiektu docelowego, Site Recovery wybierz ten sam rozmiar lub najbliższy możliwy rozmiar dla docelowej maszyny wirtualnej.

- Upewnij się, że tworzysz zasób docelowy dla każdego składnika, który jest identyfikowany w układzie sieci źródłowej. Ten krok jest ważne, aby upewnić się, że maszyny wirtualne mają wszystkie funkcje i funkcje w regionie docelowym, który istniał w regionie źródłowym.

     > [!NOTE] 
     > Azure Site Recovery automatycznie wykrywa i tworzy sieć wirtualną po włączeniu replikacji dla źródłowej maszyny wirtualnej. Możesz również wstępnie utworzyć sieć i przypisać ją do maszyny wirtualnej w przepływie użytkownika w celu włączenia replikacji. Jak wspomniano wcześniej, należy ręcznie utworzyć wszystkie inne zasoby w regionie docelowym.

    Aby utworzyć najczęściej używane zasoby sieciowe, które są odpowiednie dla Ciebie na podstawie konfiguracji źródłowej maszyny wirtualnej, zapoznaj się z następującą dokumentacją:
    - [Sieciowe grupy zabezpieczeń](../virtual-network/manage-network-security-group.md)
    - [Moduły równoważenia obciążenia](../load-balancer/index.yml)
    -  [Publiczny adres IP](../virtual-network/virtual-network-public-ip-address.md)
    - Wszystkie inne składniki sieci można znaleźć w dokumentacji dotyczącej [sieci](../index.yml?pivot=products&panel=network).



## <a name="prepare"></a>Przygotowywanie
Poniższe kroki pokazują, jak przygotować maszynę wirtualną do przenoszenia przy użyciu Azure Site Recovery jako rozwiązanie. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Tworzenie magazynu w dowolnym regionie, z wyjątkiem regionu źródłowego

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. W obszarze Wyszukaj wpisz Recovery Services > kliknij pozycję magazyny Recovery Services
1. W menu magazyny Recovery Services kliknij pozycję + Dodaj.
1. W polu **Nazwa** podaj przyjazną nazwę **ContosoVMVault**. Jeśli masz więcej niż jedną subskrypcję, wybierz jedną z nich.
1. Utwórz grupę zasobów **ContosoRG**.
1. Określ region platformy Azure. Aby sprawdzić Obsługiwane regiony, zobacz temat dostępność geograficzna w [szczegółach cennika Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. W obszarze **magazyny Recovery Services** wybierz pozycję **ContosoVMVault**  >  **zreplikowane elementy**  >  **+ replikacja**.
1. Z listy rozwijanej wybierz pozycję **Azure Virtual Machines**.
1. W obszarze **Lokalizacja źródłowa** wybierz źródłowy region świadczenia usługi Azure, w którym są uruchomione maszyny wirtualne.
1. Wybierz model wdrażania usługi Resource Manager. Następnie wybierz pozycje **Subskrypcja źródłowa** i **Źródłowa grupa zasobów**.
1. Wybierz **przycisk OK** , aby zapisać ustawienia.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Włącz replikację maszyn wirtualnych platformy Azure i Rozpocznij kopiowanie danych

Site Recovery pobiera listę maszyn wirtualnych skojarzonych z subskrypcją i grupą zasobów.

1. W następnym kroku Wybierz maszynę wirtualną, którą chcesz przenieść, a następnie wybierz pozycję **OK**.
1. W obszarze **Ustawienia** wybierz pozycję **odzyskiwanie po awarii**.
1. W obszarze **Konfigurowanie odzyskiwania po awarii** > **Region docelowy** wybierz region docelowy, w którym maszyna będzie replikowana.
1. Dla celów tego samouczka zaakceptuj inne ustawienia domyślne.
1. Wybierz pozycję **Włącz replikację**. Ten krok umożliwia uruchomienie zadania w celu włączenia replikacji dla maszyny wirtualnej.



## <a name="move"></a>Move

Poniższe kroki pokazują, jak przeprowadzić przeniesienie do regionu docelowego.

1. Przejdź do magazynu. W obszarze **Ustawienia**  >  **zreplikowane elementy** wybierz maszynę wirtualną, a następnie wybierz pozycję **tryb failover**.
2. W obszarze **Tryb failover** wybierz pozycję **Najnowsze**.
3. Wybierz pozycję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover**. Usługa Site Recovery próbuje zamknąć źródłową maszynę wirtualną przed wyzwoleniem trybu failover. Przełączanie do trybu failover będzie kontynuowane, nawet jeśli zamknięcie nie powiedzie się. Postęp pracy w trybie failover można wykonać na stronie **zadań** .
4. Po zakończeniu zadania Sprawdź, czy maszyna wirtualna jest wyświetlana w docelowym regionie platformy Azure zgodnie z oczekiwaniami.


## <a name="discard"></a>Odrzuć 

W przypadku zaznaczenia przenoszonej maszyny wirtualnej i wprowadzenia zmiany do punktu pracy w trybie failover lub przejścia do poprzedniego punktu w obszarze **zreplikowane elementy** kliknij prawym przyciskiem myszy maszynę wirtualną > **Zmień punkt odzyskiwania**. Ten krok pozwala określić inny punkt odzyskiwania i tryb failover dla tego kroku. 


## <a name="commit"></a>Zatwierdzenie 

Po sprawdzeniu przeniesionej maszyny wirtualnej i przygotowaniu się do zatwierdzenia zmiany w obszarze **zreplikowane elementy** kliknij prawym przyciskiem myszy pozycję > **zatwierdzenie** maszyny wirtualnej. Ten krok kończy proces przenoszenia w regionie docelowym. Poczekaj na zakończenie zadania zatwierdzania.

## <a name="clean-up"></a>Czyszczenie

Poniższe kroki przeprowadzą Cię przez proces oczyszczania regionu źródłowego, a także powiązane zasoby, które były używane do przenoszenia.

Dla wszystkich zasobów, które były używane do przenoszenia:

- Przejdź do maszyny wirtualnej. Wybierz pozycję **Wyłącz replikację**. Ten krok uniemożliwia procesowi skopiowanie danych dla maszyny wirtualnej.

   > [!IMPORTANT]
   > Ważne jest, aby wykonać ten krok, aby uniknąć naliczania opłat za replikację Azure Site Recovery.

Jeśli nie masz planu, aby ponownie użyć żadnego z zasobów źródłowych, wykonaj następujące dodatkowe czynności:

1. Usuń wszystkie odpowiednie zasoby sieciowe w regionie źródłowym wskazanym w sekcji [wymagania wstępne](#prerequisites).
1. Usuń odpowiednie konto magazynu w regionie źródłowym.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przeniesiono maszynę wirtualną platformy Azure do innego regionu platformy Azure. Teraz można skonfigurować odzyskiwanie po awarii dla przenoszonej maszyny wirtualnej.

> [!div class="nextstepaction"]
> [Konfigurowanie odzyskiwania po awarii po migracji](azure-to-azure-quickstart.md)
