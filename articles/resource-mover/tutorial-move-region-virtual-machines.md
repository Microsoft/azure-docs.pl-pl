---
title: Przenoszenie maszyn wirtualnych platformy Azure między regionami przy użyciu usługi Azure Resource przenoszącej
description: Dowiedz się, jak przenieść maszyny wirtualne platformy Azure do innego regionu za pomocą usługi Azure Resource przenoszącej
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d208a4a86896c81982aa2b10ca7ce5e7a6773c05
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820216"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Samouczek: przenoszenie maszyn wirtualnych platformy Azure między regionami

W tym artykule dowiesz się, jak przenieść maszyny wirtualne platformy Azure oraz powiązane zasoby sieci/magazynu do innego regionu platformy Azure przy użyciu funkcji [przenoszenia zasobów platformy Azure](overview.md).

> [!NOTE]
> Usługa Azure Resource przeprowadzki jest obecnie dostępna w publicznej wersji zapoznawczej.


Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Sprawdź wymagania wstępne i wymagania.
> * Wybierz zasoby, które chcesz przenieść.
> * Rozwiąż zależności zasobów.
> * Przygotuj i Przenieś źródłową grupę zasobów. 
> * Przygotuj i Przenieś inne zasoby.
> * Zdecyduj, czy chcesz odrzucić lub zatwierdzić przeniesienie. 
> * Opcjonalnie można usunąć zasoby w regionie źródłowym po przeniesieniu.

> [!NOTE]
> Samouczki pokazują najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/). Następnie zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Wymagania wstępne

-  Sprawdź, czy masz dostęp *właściciela* do subskrypcji zawierającej zasoby, które chcesz przenieść.
    - Przy pierwszym dodawaniu zasobu dla określonej pary źródłowej i docelowej w ramach subskrypcji platformy Azure usługa zarządzania zasobami tworzy [tożsamość zarządzaną przypisaną przez system](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (znaną wcześniej jako identyfikator usługi zarządzanej (msi), która jest zaufana przez subskrypcję.
    - Aby utworzyć tożsamość i przypisać do niej wymaganą rolę (współautor lub administratora dostępu użytkownika w subskrypcji źródłowej), konto używane do dodawania zasobów wymaga uprawnień *właściciela* do subskrypcji. [Dowiedz się więcej](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) na temat ról platformy Azure.
- Subskrypcja wymaga wystarczającego limitu przydziału, aby utworzyć zasoby, które są przenoszone w regionie docelowym. Jeśli nie ma limitu przydziału, [Zażądaj dodatkowych limitów](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Sprawdź ceny i opłaty związane z regionem docelowym, do którego przenosisz maszyny wirtualne. Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) , aby uzyskać pomoc.
    

## <a name="check-vm-requirements"></a>Sprawdź wymagania dotyczące maszyny wirtualnej

1. Sprawdź, czy maszyny wirtualne, które chcesz przenieść, są obsługiwane.

    - [Sprawdź](support-matrix-move-region-azure-vm.md#windows-vm-support) obsługiwane maszyny wirtualne z systemem Windows.
    - [Sprawdź](support-matrix-move-region-azure-vm.md#linux-vm-support) obsługiwane maszyny wirtualne i wersje jądra systemu Linux.
    - Sprawdź obsługiwane ustawienia [obliczeń](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [magazynu](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)i [sieci](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
2. Sprawdź, czy maszyny wirtualne, które chcesz przenieść, są włączone.
3. Upewnij się, że maszyny wirtualne mają najnowsze zaufane certyfikaty główne oraz zaktualizowaną listę odwołania certyfikatów (CRL). W tym celu:
    - Na maszynach wirtualnych z systemem Windows zainstaluj najnowsze aktualizacje systemu Windows.
    - Na maszynach wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dystrybutora, aby komputery miały najnowsze certyfikaty i listę CRL. 
4. Zezwalaj na połączenia wychodzące z maszyn wirtualnych:
    - Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych [adresów URL](support-matrix-move-region-azure-vm.md#url-access)
    - Jeśli używasz reguł sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu kontrolowania łączności wychodzącej, Utwórz te [reguły tagów usług](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources"></a>Wybieranie zasobów 

Wybierz zasoby, które chcesz przenieść.

- Zostaną wyświetlone wszystkie obsługiwane typy zasobów w grupach zasobów w wybranym regionie źródłowym.
- Zasoby, które zostały już dodane do przeniesienia między regionami, nie są wyświetlane.
- Przenoszenie zasobów do regionu docelowego w tej samej subskrypcji co region źródłowy. Jeśli chcesz zmienić subskrypcję, możesz to zrobić po przeniesieniu zasobów.

1. W Azure Portal Wyszukaj pozycję *przeniesienie zasobów*. Następnie w obszarze **usługi** wybierz pozycję **Azure Resource** przenosząca.

    ![Wyniki wyszukiwania dla przenoszenia zasobów w Azure Portal](./media/tutorial-move-region-virtual-machines/search.png)

2. W obszarze **Przegląd** kliknij pozycję **Rozpocznij**.

    ![Przycisk dodawania zasobów do przenoszenia do innego regionu](./media/tutorial-move-region-virtual-machines/get-started.png)

3. W obszarze **Przenieś zasoby**  >  **Źródło + miejsce docelowe** wybierz źródłową subskrypcję i region.
4. W obszarze **Lokalizacja docelowa** wybierz region, do którego chcesz przenieść maszyny wirtualne. Następnie kliknij przycisk **Dalej**.

    ![Strona umożliwiająca wybranie regionu źródłowego i docelowego](./media/tutorial-move-region-virtual-machines/source-target.png)

6. W obszarze **zasoby do przeniesienia** kliknij pozycję **Wybierz zasoby**.
7. W obszarze **Wybierz zasoby** wybierz maszynę wirtualną. Można dodawać tylko [zasoby obsługiwane do przenoszenia](#check-vm-requirements). Następnie kliknij przycisk **gotowe**.

    ![Strona umożliwiająca wybranie maszyn wirtualnych do przeniesienia](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  W obszarze **zasoby do przeniesienia** kliknij przycisk **dalej**.
9. W obszarze **Recenzja + Dodawanie** Sprawdź ustawienia źródłowe i docelowe. 

    ![Strona umożliwiająca przejrzenie ustawień i przejście do przenoszenia](./media/tutorial-move-region-virtual-machines/review.png)
10. Kliknij przycisk " **Zastosuj**", aby rozpocząć dodawanie zasobów.
11. Po pomyślnym zakończeniu dodawania kliknij pozycję **Dodaj zasoby do przeniesienia** w ikonie powiadomienia.
12. Po kliknięciu powiadomienia Przejrzyj zasoby na stronie **między regionami** .

> [!NOTE]
> - Dodane zasoby są w stanie *oczekiwania na przygotowanie* .
> - Jeśli chcesz usunąć zasób z kolekcji Move, metoda dla operacji, która zależy od tego, gdzie jesteś w procesie przenoszenia. [Dowiedz się więcej](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Rozwiązywanie zależności

1. Jeśli w kolumnie **problemy** zostanie wyświetlony komunikat *Weryfikuj zależności* , kliknij przycisk **Weryfikuj zależności** . Rozpocznie się proces walidacji.
2. Jeśli znajdują się zależności, kliknij przycisk **Dodaj zależności**. 
3. W obszarze **Dodaj zależności** Wybierz zasoby zależne > **Dodaj zależności**. Monitoruj postęp w powiadomieniach.

    ![Dodaj zależności](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. W razie konieczności Dodaj dodatkowe zależności i ponownie sprawdź zależności. 
    ![Strona umożliwiająca dodanie dodatkowych zależności](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)

4. Na stronie **między regionami** Sprawdź, czy zasoby są teraz w stanie *gotowości do przygotowania* , bez problemów.

    ![Strona wyświetlająca zasoby w stanie oczekiwanie na przygotowanie](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

> [!NOTE]
> Jeśli chcesz edytować ustawienia docelowe przed rozpoczęciem przenoszenia, wybierz link w kolumnie **Konfiguracja docelowa** dla zasobu i Edytuj ustawienia. Jeśli edytujesz ustawienia docelowej maszyny wirtualnej, docelowy rozmiar maszyny wirtualnej nie powinien być mniejszy niż rozmiar źródłowej maszyny wirtualnej.  

## <a name="move-the-source-resource-group"></a>Przenoszenie źródłowej grupy zasobów 

Aby można było przygotować i przenieść maszyny wirtualne, Grupa zasobów maszyny wirtualnej musi znajdować się w regionie docelowym. 

### <a name="prepare-to-move-the-source-resource-group"></a>Przygotowanie do przeniesienia źródłowej grupy zasobów

Podczas przygotowania proces przenoszenia zasobów generuje szablony Azure Resource Manager (ARM) przy użyciu ustawień grupy zasobów. Nie ma to wpływu na zasoby znajdujące się w grupie zasobów.

Przygotuj się w następujący sposób:

1. W **różnych regionach** wybierz źródłową grupę zasobów > **Przygotuj**.
2. W obszarze **Przygotowywanie zasobów** kliknij pozycję **Przygotuj**.

    ![Przygotuj grupę zasobów](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> Po przygotowaniu grupy zasobów znajduje się ona w stanie " *Inicjowanie przenoszenia oczekujące* ". 

 
### <a name="move-the-source-resource-group"></a>Przenoszenie źródłowej grupy zasobów

Zainicjuj przechodzenie w następujący sposób:

1. W **różnych regionach** wybierz grupę zasobów > **zainicjować przenoszenie**
2. LN **przenoszenie zasobów**, kliknij przycisk **Inicjuj przenoszenie**. Grupa zasobów przechodzi do stanu *inicjowania przenoszenia w toku* .
3. Po zainicjowaniu przenoszenia docelowa Grupa zasobów zostanie utworzona na podstawie wygenerowanego szablonu ARM. Źródłowa Grupa zasobów przechodzi w stan *oczekiwania na przeniesienie zatwierdzenia* .

    ![Kliknij przycisk Inicjuj przenoszenie](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

Aby zatwierdzić i zakończyć proces przenoszenia:

1. W **różnych regionach** wybierz grupę zasobów > **Zatwierdź przeniesienie**.
2. w obszarze **Przenieś zasoby** kliknij pozycję **Zatwierdź**.

> [!NOTE]
> Po zatwierdzeniu przeniesienia źródłowa Grupa zasobów jest w stanie oczekiwania na *usunięcie źródła* .

## <a name="prepare-resources-to-move"></a>Przygotowanie zasobów do przeniesienia

Po przeniesieniu źródłowej grupy zasobów można przystąpić do przenoszenia innych zasobów.

1. W obszarze **między regionami** Wybierz zasoby, które chcesz przygotować. 

    ![Strona do wybrania przygotowania do innych zasobów](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. Wybierz pozycję **Przygotuj**. 

> [!NOTE]
> - W trakcie procesu przygotowywania Agent Azure Site Recovery Mobility jest instalowany na maszynach wirtualnych w celu replikowania ich.
> - Dane maszyn wirtualnych są okresowo replikowane do regionu docelowego. Nie ma to wpływu na źródłową maszynę wirtualną.
> - Przeniesienie zasobu powoduje wygenerowanie szablonów ARM dla innych zasobów źródłowych.
> - Po przygotowaniu zasobów znajdują się one w stanie " *Inicjowanie przenoszenia oczekujące* ".

![Strona wyświetlająca zasoby w stanie inicjowania przenoszenia oczekujących](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>Inicjowanie przenoszenia

Po przygotowaniu zasobów można teraz zainicjować przenoszenie. 

1. W **różnych regionach** wybierz pozycję zasoby z stanem *Inicjuj przenoszenie oczekujące*. Następnie kliknij pozycję **zainicjuj przenoszenie**.
2. W obszarze **Przenieś zasoby** kliknij pozycję **Inicjuj przenoszenie**.

    ![Kliknij, aby kliknąć przycisk inicjowania przenoszenia](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. Śledź postęp przenoszenia na pasku powiadomień.

> [!NOTE]
> - W przypadku maszyn wirtualnych repliki maszyn wirtualnych są tworzone w regionie docelowym. Źródłowa maszyna wirtualna jest wyłączona i występuje trochę przestoju (zwykle w minutach).
> - Przeniesienie zasobów umożliwia odtworzenie innych zasobów przy użyciu szablonów ARM, które zostały przygotowane. Zwykle nie ma przestojów.
> - Po przeniesieniu zasobów są one w stanie *oczekiwania na przeniesienie zatwierdzenia* .

![Strona wyświetlająca zasoby w * stan oczekiwania na usunięcie źródła *](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="discard-or-commit"></a>Odrzucić lub zatwierdzić?

Po początkowym przeniesieniu możesz zdecydować, czy chcesz zatwierdzić przeniesienie, czy go odrzucić. 

- **Odrzuć**: możesz odrzucić przeniesienie, jeśli testujesz, i nie chcesz faktycznie przenosić zasobu źródłowego. Odrzucanie przesunięcia spowoduje zwrócenie zasobu do stanu *inicjacja oczekującego przeniesienia*.
- **Zatwierdzenie**: zatwierdzenie powoduje zakończenie przejścia do regionu docelowego. Po zatwierdzeniu zasób źródłowy będzie w stanie *oczekiwania na usunięcie źródła* i można zdecydować, czy ma zostać usunięty.


## <a name="discard-the-move"></a>Odrzuć przeniesienie 

Możesz odrzucić przeniesienie w następujący sposób:

1. W obszarze **między regionami** wybierz pozycję zasoby z *oczekującym przeniesieniem* stanu, a następnie kliknij pozycję **odrzuć przeniesienie**.
2. W polu **Odrzuć przenoszenie** kliknij pozycję **Odrzuć**.
3. Śledź postęp przenoszenia na pasku powiadomień.


> [!NOTE]
> Po odrzuceniu zasobów maszyny wirtualne znajdują się w stanie " *Inicjowanie przenoszenia oczekujące* ".

## <a name="commit-the-move"></a>Zatwierdź przeniesienie

Jeśli chcesz zakończyć proces przenoszenia, Zatwierdź przeniesienie. 

1. W obszarze **między regionami** wybierz pozycję zasoby z *oczekującym przeniesieniem* stanu, a następnie kliknij pozycję **Zatwierdź przeniesienie**.
2. W obszarze **Zatwierdź zasoby** kliknij pozycję **Zatwierdź**.

    ![Strona do zatwierdzania zasobów w celu sfinalizowania przenoszenia](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Śledź postęp zatwierdzania na pasku powiadomień.

> [!NOTE]
> - Po zatwierdzeniu przenoszenia maszyny wirtualne zatrzymają replikację. Zatwierdzenie nie ma wpływu na źródłową maszynę wirtualną.
> - Zatwierdzenie nie ma wpływu na zasoby sieci źródłowej.
> - Po zatwierdzeniu przenoszenia zasoby są w stanie oczekiwania na *usunięcie źródła* .

![Strona wyświetlająca zasoby w * stan oczekiwania na usunięcie źródła *](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Skonfiguruj ustawienia po przeniesieniu

- Usługa mobilności nie została automatycznie odinstalowana z maszyn wirtualnych. Odinstaluj je ręcznie lub pozostaw to pole, jeśli planujesz ponownie przenieść serwer.
- Modyfikuj reguły kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure po przeniesieniu.


## <a name="delete-source-resources-after-commit"></a>Usuń zasoby źródłowe po zatwierdzeniu

Po przeniesieniu można opcjonalnie usunąć zasoby w regionie źródłowym. 

> [!NOTE]
> Nie można usunąć z portalu kilku zasobów, takich jak magazyny kluczy i serwery SQL Server, i należy je usunąć ze strony właściwości zasobu.

1. W **różnych regionach** kliknij nazwę zasobu źródłowego, który chcesz usunąć.
2. Wybierz pozycję **Usuń źródło**.

## <a name="delete-additional-resources-created-for-move"></a>Usuń dodatkowe zasoby utworzone do przeniesienia

Po przeniesieniu można ręcznie usunąć kolekcję Move i Site Recovery utworzone zasoby.

- Kolekcja Move jest domyślnie ukryta. Aby zobaczyć, należy włączyć ukryte zasoby.
- Magazyn pamięci podręcznej ma blokadę, która musi zostać usunięta, zanim będzie można jej usunąć.

Usuń w następujący sposób: 
1. Znajdź zasoby w grupie zasobów ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Sprawdź, czy wszystkie maszyny wirtualne i inne zasoby źródłowe w regionie źródłowym zostały przeniesione lub usunięte. Dzięki temu nie ma żadnych oczekujących zasobów, których używają.
2. Usuń zasoby:

    - Nazwa kolekcji przenoszenia to ```movecollection-<sourceregion>-<target-region>``` .
    - Nazwa konta magazynu pamięci podręcznej to ```resmovecache<guid>```
    - Nazwa magazynu to ```ResourceMove-<sourceregion>-<target-region>-GUID``` .
## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Przenoszenie maszyn wirtualnych platformy Azure do innego regionu platformy Azure.
> * Przenoszenie zasobów skojarzonych z maszynami wirtualnymi do innego regionu.

Teraz trwa próba przeniesienia baz danych i pul elastycznych usługi Azure SQL Server do innego regionu.

> [!div class="nextstepaction"]
> [Przenoszenie zasobów usługi Azure SQL](./tutorial-move-region-sql.md)