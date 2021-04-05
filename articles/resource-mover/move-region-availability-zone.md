---
title: Przenoszenie maszyn wirtualnych platformy Azure do stref dostępności w innym regionie przy użyciu obszaru przenoszenia zasobów platformy Azure
description: Dowiedz się, jak przenieść maszyny wirtualne platformy Azure do stref dostępności za pomocą usługi Azure Resource przenoszącej.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543004"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Przenoszenie maszyn wirtualnych platformy Azure do strefy dostępności w innym regionie

W tym artykule dowiesz się, jak przenieść maszyny wirtualne platformy Azure (i powiązane zasoby sieciowe/magazynowe) do strefy dostępności w innym regionie platformy Azure przy użyciu funkcji [przenoszenia zasobów platformy Azure](overview.md).

[Strefy dostępności platformy Azure](../availability-zones/az-overview.md#availability-zones) pomagają w ochronie wdrożenia platformy Azure przed awariami centrum danych. Każda strefa dostępności składa się z co najmniej jednego centrum danych wyposażonego w niezależne zasilanie, chłodzenie i sieć. W celu zapewnienia odporności dostępne są co najmniej trzy oddzielne strefy we wszystkich [włączonych regionach](../availability-zones/az-region.md). Za pomocą przenoszenia zasobów można przenieść:

- Maszyna wirtualna z jednym wystąpieniem do strefy dostępności/zestawu dostępności w regionie docelowym.
- Maszyna wirtualna w zestawie dostępności jest ustawiona na strefę dostępności/dostępność ustawioną w regionie docelowym.
- Maszyna wirtualna w strefie dostępności regionu źródłowego do strefy dostępności w regionie docelowym.


> [!IMPORTANT]
> Usługa Azure Resource przeprowadzki jest obecnie dostępna w publicznej wersji zapoznawczej.

Jeśli chcesz przenieść maszyny wirtualne do innej strefy dostępności w tym samym regionie, [zapoznaj się z tym artykułem](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Wymagania wstępne

- Dostęp *właściciela* do subskrypcji, w której znajdują się zasoby, które chcesz przenieść.
    - Przy pierwszym dodawaniu zasobu do określonego mapowania źródła i miejsca docelowego w ramach subskrypcji platformy Azure usługa zarządzania zasobami tworzy [tożsamość zarządzaną przypisaną przez system](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (znaną wcześniej jako identyfikator usługi zarządzanej (msi), która jest zaufana przez subskrypcję.
    - Aby utworzyć tożsamość i przypisać do niej wymaganą rolę (współautor lub administratora dostępu użytkownika w subskrypcji źródłowej), konto używane do dodawania zasobów wymaga uprawnień *właściciela* do subskrypcji. [Dowiedz się więcej](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) na temat ról platformy Azure.
- Subskrypcja wymaga wystarczającego limitu przydziału, aby można było utworzyć zasoby źródłowe w regionie docelowym. Jeśli nie, zażądaj dodatkowych limitów. [Dowiedz się więcej](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Sprawdź ceny i opłaty związane z regionem docelowym, do którego przenosisz maszyny wirtualne. Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) , aby uzyskać pomoc.
    


## <a name="check-vm-requirements"></a>Sprawdź wymagania dotyczące maszyny wirtualnej

1. Sprawdź, czy maszyny wirtualne, które chcesz przenieść, są obsługiwane.

    - [Sprawdź](support-matrix-move-region-azure-vm.md#windows-vm-support) obsługiwane maszyny wirtualne z systemem Windows.
    - [Sprawdź](support-matrix-move-region-azure-vm.md#linux-vm-support) obsługiwane maszyny wirtualne i wersje jądra systemu Linux.
    - Sprawdź obsługiwane ustawienia [obliczeń](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [magazynu](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)i [sieci](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
2. Sprawdź, czy maszyny wirtualne, które chcesz przenieść, są włączone.
3. Upewnij się, że maszyny wirtualne mają najnowsze zaufane certyfikaty główne i zaktualizowaną listę odwołania certyfikatów (CRL). 
    - Na maszynach wirtualnych platformy Azure z systemem Windows zainstaluj najnowsze aktualizacje systemu Windows.
    - Na maszynach wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dystrybutora systemu Linux, aby upewnić się, że komputer ma najnowsze certyfikaty i listę CRL. 
4. Zezwalaj na połączenia wychodzące z maszyn wirtualnych:
    - Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych [adresów URL](support-matrix-move-region-azure-vm.md#url-access)
    - Jeśli używasz reguł sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu kontrolowania łączności wychodzącej, Utwórz te [reguły tagów usług](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Wybierz zasoby do przeniesienia

Wybierz zasoby, które chcesz przenieść.

- Możesz wybrać dowolny obsługiwany typ zasobu dla grup zasobów w wybranym regionie źródłowym.
- Przenoszenie zasobów do regionu docelowego w subskrypcji regionu źródłowego. Jeśli chcesz zmienić subskrypcję, możesz to zrobić po przeniesieniu zasobów.

1. W Azure Portal Wyszukaj pozycję przeniesienie zasobów. Następnie w obszarze **usługi** wybierz pozycję **Azure Resource** przenosząca.

    ![Wyszukiwanie zasobów przenoszenia](./media/move-region-availability-zone/search.png)

2. W obszarze **Przegląd** wybierz pozycję **Rozpocznij**.

    ![Przycisk, aby rozpocząć](./media/move-region-availability-zone/get-started.png)

3. W obszarze **Przenieś zasoby**  >  **Źródło + miejsce docelowe** wybierz źródłową subskrypcję i region.
4. W obszarze **Lokalizacja docelowa** wybierz region, do którego chcesz przenieść maszyny wirtualne. Następnie kliknij przycisk **Dalej**.

     ![Strona do wypełnienia dla źródłowej i docelowej subskrypcji/regionu](./media/move-region-availability-zone/source-target.png)

6. W obszarze **zasoby do przeniesienia** kliknij pozycję **Wybierz zasoby**.
7. W obszarze **Wybierz zasoby** wybierz maszynę wirtualną. Można dodawać tylko zasoby obsługiwane do przenoszenia. Następnie kliknij przycisk **gotowe**. W obszarze **zasoby do przeniesienia** kliknij przycisk **dalej**.

    ![Strona umożliwiająca wybranie maszyn wirtualnych do przeniesienia](./media/move-region-availability-zone/select-vm.png)
8. W obszarze **Recenzja + Dodawanie** Sprawdź ustawienia źródłowe i docelowe.

    ![Strona umożliwiająca przejrzenie ustawień i przejście do przenoszenia](./media/move-region-availability-zone/review.png)

9. Kliknij przycisk " **Zastosuj**", aby rozpocząć dodawanie zasobów.
10. Po pomyślnym zakończeniu dodawania kliknij pozycję **Dodaj zasoby do przeniesienia** w ikonie powiadomienia.

    ![Komunikat w powiadomieniach](./media/move-region-availability-zone/notification.png)

Po kliknięciu powiadomienia wyświetlane są zasoby na stronie **między regionami** .

> [!NOTE]
> Po kliknięciu powiadomienia na stronie **między regionami** zostaną wyświetlone zasoby w stanie oczekiwania na *przygotowanie* .
> - Jeśli chcesz usunąć zasób z kolekcji Move, metoda dla operacji, która zależy od tego, gdzie jesteś w procesie przenoszenia. [Dowiedz się więcej](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Rozwiązywanie zależności

1. Jeśli w kolumnie **problemy** zostanie wyświetlony komunikat *Weryfikuj zależności* , kliknij przycisk **Weryfikuj zależności** . Proces sprawdzania poprawności.
2. Jeśli znajdują się zależności, kliknij przycisk **Dodaj zależności**. 
3. W obszarze **Dodaj zależności** Wybierz zasoby zależne > **Dodaj zależności**. Monitoruj postęp w powiadomieniach.

    ![Przycisk dodawania zależności](./media/move-region-availability-zone/add-dependencies.png)

3. W razie konieczności Dodaj dodatkowe zależności i ponownie sprawdź zależności. 

    ![Strona umożliwiająca dodanie dodatkowych zależności](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Na stronie **między regionami** Sprawdź, czy zasoby są teraz w stanie *gotowości do przygotowania* , bez problemów.

    ![Strona wyświetlająca zasoby w stanie oczekiwanie na przygotowanie](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Przenoszenie źródłowej grupy zasobów 

Aby można było przygotować i przenieść maszyny wirtualne, źródłowa Grupa zasobów musi znajdować się w regionie docelowym. 

### <a name="prepare-to-move-the-source-resource-group"></a>Przygotowanie do przeniesienia źródłowej grupy zasobów

Przygotuj się w następujący sposób:

1. W **różnych regionach** wybierz źródłową grupę zasobów > **Przygotuj**.
2. W obszarze **Przygotowywanie zasobów** kliknij pozycję **Przygotuj**.

    ![Przycisk przygotowujący źródłową grupę zasobów](./media/move-region-availability-zone/prepare-resource-group.png)

    Podczas przygotowania proces przenoszenia zasobów generuje szablony Azure Resource Manager (ARM) przy użyciu ustawień grupy zasobów. Nie ma to wpływu na zasoby znajdujące się w grupie zasobów.

> [!NOTE]
>  Po przygotowaniu grupy zasobów znajduje się ona w stanie " *Inicjowanie przenoszenia oczekujące* ". 

![Stan wyświetlania stanu oczekiwania na zainicjowanie](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Przenoszenie źródłowej grupy zasobów

Zainicjuj przechodzenie w następujący sposób:

1. W **różnych regionach** wybierz grupę zasobów > **zainicjować przenoszenie**
2. LN **przenoszenie zasobów**, kliknij przycisk **Inicjuj przenoszenie**. Grupa zasobów przechodzi do stanu *inicjowania przenoszenia w toku* .
3. Po zainicjowaniu przenoszenia docelowa Grupa zasobów zostanie utworzona na podstawie wygenerowanego szablonu ARM. Źródłowa Grupa zasobów przechodzi w stan *oczekiwania na przeniesienie zatwierdzenia* .

![Stan pokazujący przenoszenie zatwierdzenia](./media/move-region-availability-zone/commit-move-pending.png)

Aby zatwierdzić i zakończyć proces przenoszenia:

1. W **różnych regionach** wybierz grupę zasobów, w której ma zostać **przeniesiona > zatwierdzenie**
2. w obszarze **Przenieś zasoby** kliknij pozycję **Zatwierdź**.

> [!NOTE]
> Po zatwierdzeniu przeniesienia źródłowa Grupa zasobów jest w stanie oczekiwania na *usunięcie źródła* .


## <a name="add-a-target-availability-zone"></a>Dodawanie docelowej strefy dostępności

Przed przeniesieniem reszty zasobów ustawimy docelową strefę dostępności dla maszyny wirtualnej.

1. Na stronie **między regionami** kliknij link w kolumnie **Konfiguracja DOCELOWa** maszyny wirtualnej, która jest przenoszona.

    ![Właściwości maszyny wirtualnej](./media/move-region-availability-zone/select-vm-settings.png)

3. W obszarze **Ustawienia konfiguracji** Określ ustawienie dla docelowej maszyny wirtualnej. Maszynę wirtualną można skonfigurować w regionie docelowym w następujący sposób:
    -  Utwórz nowy odpowiednik zasobu w regionie docelowym. Z wyjątkiem określonych ustawień, zasób docelowy jest tworzony przy użyciu tych samych ustawień co źródło.
    - Wybierz istniejącą maszynę wirtualną w regionie docelowym. 

4. W obszarze **strefy** wybierz strefę, w której chcesz umieścić maszynę wirtualną podczas jej przenoszenia.

    Zmiany są wykonywane tylko dla edytowanego zasobu. Należy osobno zaktualizować wszystkie zależne zasoby.

5. W obszarze **jednostka SKU** Określ [warstwę platformy Azure](..//virtual-machines/sizes.md) , która ma zostać przypisana do docelowej maszyny wirtualnej.
6. W obszarze **zestaw dostępności** wybierz zestaw dostępności, jeśli chcesz, aby docelowa maszyna wirtualna była uruchamiana w ramach zestawu dostępności w strefie dostępności.
7. Wybierz pozycję **Save changes** (Zapisz zmiany).

    ![Ustawienia maszyny wirtualnej](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>Przygotowanie zasobów do przeniesienia

Po przeniesieniu źródłowej grupy zasobów można przystąpić do przenoszenia innych zasobów.

1. W obszarze **między regionami** Wybierz zasoby, które chcesz przygotować. 

    ![Strona do wybrania przygotowania do innych zasobów](./media/move-region-availability-zone/prepare-other.png)

2. Wybierz pozycję **Przygotuj**. 

> [!NOTE]
> - W trakcie procesu przygotowywania Agent Azure Site Recovery Mobility jest instalowany na maszynach wirtualnych na potrzeby replikacji.
> - Dane maszyn wirtualnych są okresowo replikowane do regionu docelowego. Nie ma to wpływu na źródłową maszynę wirtualną.
> - Przeniesienie zasobu powoduje wygenerowanie szablonów ARM dla innych zasobów źródłowych.
> - Po przygotowaniu zasobów znajdują się one w stanie " *Inicjowanie przenoszenia oczekujące* ".

![Strona wyświetlająca zasoby w stanie inicjowania przenoszenia oczekujących](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Inicjowanie przenoszenia

Po przygotowaniu zasobów można teraz zainicjować przenoszenie. 

1. W **różnych regionach** wybierz pozycję zasoby z stanem *Inicjuj przenoszenie oczekujące*. Następnie kliknij pozycję **Inicjuj przenoszenie**
2. W obszarze **Przenieś zasoby** kliknij pozycję **Inicjuj przenoszenie**.

    ![Strona umożliwiająca zainicjowanie przenoszenia zasobów](./media/move-region-availability-zone/initiate-move.png)

3. Śledź postęp przenoszenia na pasku powiadomień.


> [!NOTE]
> - W przypadku maszyn wirtualnych repliki maszyn wirtualnych są tworzone w regionie docelowym. Źródłowa maszyna wirtualna jest wyłączona i występuje trochę przestoju (zwykle w minutach).
> - Przeniesienie zasobów umożliwia odtworzenie innych zasobów przy użyciu szablonów ARM, które zostały przygotowane. Zwykle nie ma przestojów.
> - Po przygotowaniu zasobów są one w stanie *oczekiwania na przeniesienie* .


![Strona do wyświetlania zasobów w stanie oczekiwania na przeniesienie zatwierdzenia](./media/move-region-availability-zone/resources-commit-move-pending.png)

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
> W przypadku maszyn wirtualnych po odrzuceniu zasobów znajdują się one w stanie " *Inicjowanie przenoszenia oczekujące* ".

## <a name="commit-the-move"></a>Zatwierdź przeniesienie

Jeśli chcesz zakończyć proces przenoszenia, Zatwierdź przeniesienie. 

1. W obszarze **między regionami** wybierz pozycję zasoby z *oczekującym przeniesieniem* stanu, a następnie kliknij pozycję **Zatwierdź przeniesienie**.
2. W obszarze **Zatwierdź zasoby** kliknij pozycję **Zatwierdź**.

    ![Strona do zatwierdzania zasobów w celu sfinalizowania przenoszenia](./media/move-region-availability-zone/commit-resources.png)

3. Śledź postęp zatwierdzania na pasku powiadomień.

> [!NOTE]
> - Po zatwierdzeniu przenoszenia maszyny wirtualne zatrzymają replikację. Zatwierdzenie nie ma wpływu na źródłową maszynę wirtualną.
> - Zatwierdzenie nie ma wpływu na zasoby sieci źródłowej.
> - Po zatwierdzeniu przenoszenia zasoby są w stanie oczekiwania na *usunięcie źródła* .

![Strona wyświetlająca zasoby w * stan oczekiwania na usunięcie źródła *](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Skonfiguruj ustawienia po przeniesieniu

Usługa mobilności nie została automatycznie odinstalowana z maszyn wirtualnych. Odinstaluj je ręcznie lub pozostaw to pole, jeśli planujesz ponownie przenieść serwer.


## <a name="delete-source-resources-after-commit"></a>Usuń zasoby źródłowe po zatwierdzeniu

Po przeniesieniu można opcjonalnie usunąć zasoby w regionie źródłowym.

1. W **różnych regionach** kliknij nazwę każdego zasobu źródłowego, który chcesz usunąć.
2. Na stronie właściwości dla każdego zasobu wybierz pozycję **Usuń**.

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

[Dowiedz się więcej o](about-move-process.md) procesie przenoszenia.