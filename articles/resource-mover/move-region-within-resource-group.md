---
title: Przenoszenie zasobów do innego regionu za pomocą usługi Azure Resource przenoszącej
description: Informacje o sposobie przenoszenia zasobów w grupie zasobów do innego regionu za pomocą usługi Azure Resource przenoszącej.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 716928761d23c2cf04ebcc72e253ad7884408065
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061850"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Przenoszenie zasobów między regionami (z grupy zasobów)

W tym artykule dowiesz się, jak przenieść zasoby w określonej grupie zasobów do innego regionu platformy Azure. W grupie zasobów należy wybrać zasoby, które mają zostać przeniesione. Następnie można je przenieść przy użyciu funkcji [przenoszenia zasobów platformy Azure](overview.md).

> [!IMPORTANT]
> Usługa Azure Resource przeprowadzki jest obecnie dostępna w publicznej wersji zapoznawczej.


## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć dostęp *właściciela* do subskrypcji, w której znajdują się zasoby, które chcesz przenieść.
    - Przy pierwszym dodawaniu zasobu do określonego mapowania źródła i miejsca docelowego w ramach subskrypcji platformy Azure usługa zarządzania zasobami tworzy [tożsamość zarządzaną przypisaną przez system](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (znaną wcześniej jako identyfikator usługi zarządzanej (msi), która jest zaufana przez subskrypcję.
    - Aby utworzyć tożsamość i przypisać do niej wymaganą rolę (współautor lub administratora dostępu użytkownika w subskrypcji źródłowej), konto używane do dodawania zasobów wymaga uprawnień *właściciela* do subskrypcji. [Dowiedz się więcej](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) na temat ról platformy Azure.
- Subskrypcja wymaga wystarczającego limitu przydziału, aby można było utworzyć zasoby źródłowe w regionie docelowym. Jeśli nie, zażądaj dodatkowych limitów. [Dowiedz się więcej](/azure/azure-resource-manager/management/azure-subscription-service-limits).
- Sprawdź ceny i opłaty związane z regionem docelowym, do którego przenosisz maszyny wirtualne. Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) , aby uzyskać pomoc.
- Sprawdź, czy zasoby, które chcesz przenieść, są obsługiwane przez program do przenoszenia zasobów:
    - Maszyny wirtualne platformy Azure i skojarzone dyski
    - Karty interfejsów sieciowych
    - Zestawy dostępności
    - Sieci wirtualne platformy Azure
    - Publiczne adresy IP
    - Sieciowe grupy zabezpieczeń
    - Wewnętrzne i publiczne usługi równoważenia obciążenia
    - Bazy danych SQL Azure i pule elastyczne


## <a name="check-vm-requirements"></a>Sprawdź wymagania dotyczące maszyny wirtualnej

1. Sprawdź, czy maszyny wirtualne, które chcesz przenieść, są obsługiwane.

    - [Sprawdź](support-matrix-move-region-azure-vm.md#windows-vm-support) obsługiwane maszyny wirtualne z systemem Windows.
    - [Sprawdź](support-matrix-move-region-azure-vm.md#linux-vm-support) obsługiwane maszyny wirtualne i wersje jądra systemu Linux.
    - Sprawdź obsługiwane ustawienia [obliczeń](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [magazynu](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)i [sieci](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
2. Upewnij się, że maszyny wirtualne mają najnowsze zaufane certyfikaty główne i zaktualizowaną listę odwołania certyfikatów (CRL). 
    - Na maszynach wirtualnych platformy Azure z systemem Windows zainstaluj najnowsze aktualizacje systemu Windows.
    - Na maszynach wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dystrybutora systemu Linux, aby upewnić się, że komputer ma najnowsze certyfikaty i listę CRL. 
3. Zezwalaj na połączenia wychodzące z maszyn wirtualnych:
    - Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych [adresów URL](support-matrix-move-region-azure-vm.md#url-access)
    - Jeśli używasz reguł sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu kontrolowania łączności wychodzącej, Utwórz te [reguły tagów usług](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Wybierz zasoby do przeniesienia

Wybierz zasoby, które chcesz przenieść. Przenoszenie zasobów do regionu docelowego w subskrypcji regionu źródłowego. Jeśli chcesz zmienić subskrypcję, możesz to zrobić po przeniesieniu zasobów.

1. W Azure Portal Otwórz odpowiednią grupę zasobów.
2. Na stronie Grupa zasobów wybierz zasoby, które chcesz przenieść.
3. Wybierz pozycję **Przenieś**  >  **Przenieś do innego regionu**.

    ![Wybór do przeniesienia zasobów do innego regionu](./media/move-region-within-resource-group/select-move-region.png)
    
4. W obszarze **Źródło + miejsce docelowe**wybierz region docelowy, do którego chcesz przenieść zasoby. Następnie wybierz pozycję **Dalej**.


    ![Strona źródłowa i docelowa do wyboru regionu docelowego](./media/move-region-within-resource-group/source-target.png)


7. W obszarze **zasoby do przeniesienia**wybierz pozycję **dalej**.  
8. W obszarze **Wybieranie zasobów**wybierz zasób, który chcesz przenieść. Można dodawać tylko zasoby obsługiwane do przenoszenia. Następnie wybierz pozycję **Done** (Gotowe).
9. W obszarze **Przenieś zasoby**wybierz pozycję **dalej**. 
10. W obszarze **Recenzja + Dodawanie**Sprawdź szczegóły źródłowe i docelowe.
11. Należy upewnić się, że rozumiesz, że metadane dotyczące przenoszonych zasobów będą przechowywane w grupie zasobów utworzonej w tym celu, a w celu umożliwienia przenoszenia zasobów można utworzyć tożsamość zarządzaną przez system w celu uzyskania dostępu do zasobów subskrypcji.
1. Wybierz **pozycję Rozpocznij,** aby rozpocząć dodawanie zasobów.

    ![Strona podsumowania, aby sprawdzić szczegóły i kontynuować przenoszenie](./media/move-region-within-resource-group/summary.png)    

11. Operacja Dodaj zasób zostanie rozpoczęta. Po zakończeniu operacji powiadomienia pokazują, że zasoby zostały dodane i wdrożenie zakończyło się pomyślnie.
13. W obszarze powiadomienia wybierz pozycję **Dodaj zasoby do przeniesienia**.

    ![Komunikat wyświetlany w powiadomieniach](./media/move-region-within-resource-group/notification.png)    


14. Po wybraniu powiadomienia zostaną dodane wybrane zasoby do kolekcji Przenieś w centrum przenoszenia zasobów platformy Azure.  Narzędzie do przenoszenia zasobów ułatwia sprawdzenie zależności, a następnie rozpoczęcie przenoszenia zasobów do regionu docelowego.

## <a name="resolve-dependencies"></a>Rozwiązywanie zależności

Zasoby, które przenosisz, są wyświetlane na stronie **między regionami** w stanie *oczekiwania na przygotowanie* . Uruchom weryfikację w następujący sposób:

1. Jeśli w kolumnie **problemy** zostanie wyświetlony komunikat *Weryfikuj zależności* , wybierz przycisk **Weryfikuj zależności** . Rozpocznie się proces walidacji.

    ![Przycisk służący do walidacji zależności](./media/move-region-within-resource-group/validate-dependencies.png)

2. Jeśli zostaną znalezione zależności, wybierz pozycję **Dodaj zależności**. 
3. W obszarze **Dodaj zależności**Wybierz zasoby zależne > **Dodaj zależności**. Monitoruj postęp w powiadomieniach.

    ![Przycisk dodawania zależności](./media/move-region-within-resource-group/add-dependencies.png)

3. W razie konieczności Dodaj dodatkowe zależności i sprawdź poprawność zależności zgodnie z wymaganiami. Wybierz pozycję **Odśwież** , aby upewnić się, że zasoby zawierają aktualny stan.

4. Na stronie **między regionami** Sprawdź, czy zasoby są teraz w stanie *gotowości do przygotowania* , bez problemów.

    ![Strona do wyświetlania stanu oczekiwania na przygotowanie dla wszystkich zasobów](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Przenoszenie źródłowej grupy zasobów 

Przed przystąpieniem do przygotowywania i przenoszenia zasobów źródłowa Grupa zasobów musi znajdować się w regionie docelowym. 

### <a name="prepare-to-move-the-source-resource-group"></a>Przygotowanie do przeniesienia źródłowej grupy zasobów

Przygotuj się w następujący sposób:

1. W **różnych regionach**wybierz źródłową grupę zasobów > **Przygotuj**.
2. W obszarze **przygotowanie zasobów**wybierz pozycję **Przygotuj**.
1. 
    ![Przycisk przygotowujący źródłową grupę zasobów](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Podczas przygotowania proces przenoszenia zasobów generuje szablony Azure Resource Manager (ARM) przy użyciu ustawień grupy zasobów. Nie ma to wpływu na zasoby znajdujące się w grupie zasobów.
    
> [!NOTE]
>  Po przygotowaniu grupy zasobów znajduje się ona w stanie " *Inicjowanie przenoszenia oczekujące* ". Odśwież, aby wyświetlić najnowszy stan.

![Stan wyświetlania stanu oczekiwania na zainicjowanie](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Przenoszenie źródłowej grupy zasobów

Zainicjuj przechodzenie w następujący sposób:

1. W **różnych regionach**wybierz grupę zasobów > **zainicjować przenoszenie**
2. LN **przenoszenie zasobów**, wybierz pozycję **Inicjuj przenoszenie**. Grupa zasobów przechodzi do stanu *inicjowania przenoszenia w toku* .
3. Po zainicjowaniu przenoszenia docelowa Grupa zasobów zostanie utworzona na podstawie wygenerowanego szablonu ARM. Źródłowa Grupa zasobów przechodzi w stan *oczekiwania na przeniesienie zatwierdzenia* .

![Stan pokazujący przenoszenie zatwierdzenia](./media/move-region-availability-zone/commit-move-pending.png)

Aby zatwierdzić i zakończyć proces przenoszenia:

1. W **różnych regionach**wybierz grupę zasobów, w której ma zostać **przeniesiona > zatwierdzenie**
2. **Przenieś zasoby przenoszenia**, wybierz pozycję **Zatwierdź**.

> [!NOTE]
> Po zatwierdzeniu przeniesienia źródłowa Grupa zasobów jest w stanie oczekiwania na *usunięcie źródła* .

## <a name="modify-target-settings"></a>Modyfikowanie ustawień elementu docelowego

Jeśli nie chcesz przenosić zasobu źródłowego, możesz wykonać jedną z następujących czynności:

- Utwórz zasób w regionie docelowym o tej samej nazwie i ustawieniach co zasób w regionie źródłowym.
- Utwórz nowy odpowiednik zasobu w regionie docelowym. Z wyjątkiem określonych ustawień, zasób docelowy jest tworzony przy użyciu tych samych ustawień co źródło.
- Użyj istniejącego zasobu w regionie docelowym.

Zmodyfikuj ustawienie w następujący sposób:

1. Aby zmodyfikować ustawienie, wybierz wpis w kolumnie **Konfiguracja docelowa** dla zasobu.
2. Na stronie **Konfiguracja docelowa** Określ ustawienia docelowe, których chcesz użyć.
    Zmiany są wykonywane tylko dla edytowanego zasobu. Należy osobno zaktualizować wszystkie zależne zasoby.   
    
Dokładne ustawienia, które modyfikujesz, zależą od typu zasobu. [Dowiedz się więcej](modify-target-settings.md) o edytowaniu ustawień docelowych.

## <a name="prepare-resources-to-move"></a>Przygotowanie zasobów do przeniesienia

Po przeniesieniu źródłowej grupy zasobów można przystąpić do przenoszenia innych zasobów.

1. W obszarze **między regionami**Wybierz zasoby, które chcesz przygotować. 

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

1. W **różnych regionach**wybierz pozycję zasoby z stanem *Inicjuj przenoszenie oczekujące*. Następnie wybierz pozycję **Inicjuj przenoszenie**.
2. W obszarze **Przenieś zasoby**wybierz pozycję **Inicjuj przenoszenie**.

    ![Wybierz dla przycisku inicjowania przenoszenia](./media/move-region-within-resource-group/initiate-move.png)

3. Śledź postęp przenoszenia na pasku powiadomień.


> [!NOTE]
> - W przypadku maszyn wirtualnych repliki maszyn wirtualnych są tworzone w regionie docelowym. Źródłowa maszyna wirtualna jest wyłączona i występuje trochę przestoju (zwykle w minutach).<br/>
> - Przeniesienie zasobów umożliwia odtworzenie innych zasobów przy użyciu szablonów ARM, które zostały przygotowane. Zwykle nie ma przestojów.<br/> 
> - W przypadku modułów równoważenia obciążenia reguły NAT nie są kopiowane. Utwórz je w regionie docelowym po zatwierdzeniu przeniesienia.
> - W przypadku publicznych adresów IP etykieta nazwy DNS nie jest kopiowana. Ponownie Utwórz etykietę po zatwierdzeniu przeniesienia.
> - Po przygotowaniu zasobów są one w stanie *oczekiwania na przeniesienie* .


## <a name="discard-or-commit"></a>Odrzucić lub zatwierdzić?

Po początkowym przeniesieniu możesz zdecydować, czy chcesz zatwierdzić przeniesienie, czy go odrzucić. 

- **Odrzuć**: możesz odrzucić przeniesienie, jeśli testujesz, i nie chcesz faktycznie przenosić zasobu źródłowego. Odrzucanie przesunięcia spowoduje zwrócenie zasobu do stanu *inicjacja oczekującego przeniesienia*.
- **Zatwierdzenie**: zatwierdzenie powoduje zakończenie przejścia do regionu docelowego. Po zatwierdzeniu zasób źródłowy będzie w stanie *oczekiwania na usunięcie źródła*i można zdecydować, czy ma zostać usunięty.


## <a name="discard-the-move"></a>Odrzuć przeniesienie 

Możesz odrzucić przeniesienie w następujący sposób:

1. W **różnych regionach**wybierz pozycję zasoby z *oczekującą przeniesieniem*stanu, a następnie wybierz pozycję **odrzuć przeniesienie**.
2. W polu **odrzuć przeniesienie**wybierz pozycję **Odrzuć**.
3. Śledź postęp przenoszenia na pasku powiadomień.
4. Gdy powiadomienia pokazują, że przenoszenie zakończyło się pomyślnie, wybierz pozycję **Odśwież**. 

> [!NOTE]
> W przypadku maszyn wirtualnych po odrzuceniu zasobów znajdują się one w stanie " *Inicjowanie przenoszenia oczekujące* ".

## <a name="commit-the-move"></a>Zatwierdź przeniesienie

Jeśli chcesz zakończyć proces przenoszenia, Zatwierdź przeniesienie. 


1. W **różnych regionach**wybierz pozycję zasoby z *oczekującym przeniesieniem*stanu, a następnie wybierz pozycję **Zatwierdź przeniesienie**.
2. W obszarze **zatwierdzanie zasobów**wybierz pozycję **Zatwierdź**.

    ![Strona do zatwierdzania zasobów w celu sfinalizowania przenoszenia](./media/move-region-within-resource-group/commit-resources.png)

3. Śledź postęp zatwierdzania na pasku powiadomień.

> [!NOTE]
> - Po zatwierdzeniu przenoszenia maszyny wirtualne zatrzymają replikację. Zatwierdzenie nie ma wpływu na źródłową maszynę wirtualną.
> - Zatwierdzenie nie ma wpływu na zasoby sieci źródłowej.
> - Po zatwierdzeniu przenoszenia zasoby są w stanie oczekiwania na *usunięcie źródła* .

## <a name="configure-settings-after-the-move"></a>Skonfiguruj ustawienia po przeniesieniu

1. Ponieważ etykiety nazw DNS nie są kopiowane przez publiczne adresy IP, po zakończeniu przenoszenia przejdź do zasobów docelowych i zaktualizuj etykietę. 
2. W przypadku wewnętrznych modułów równoważenia obciążenia, ponieważ reguły NAT nie są kopiowane przez program, przejdź do zasobów utworzonych w regionie docelowym i zaktualizuj reguły NAT.
3. Usługa mobilności nie została automatycznie odinstalowana z maszyn wirtualnych. Odinstaluj je ręcznie lub pozostaw to pole, jeśli planujesz ponownie przenieść serwer.
## <a name="delete-source-resources-after-commit"></a>Usuń zasoby źródłowe po zatwierdzeniu

Po przeniesieniu można opcjonalnie usunąć zasoby w regionie źródłowym. 

1. W **różnych regionach**wybierz nazwę każdego zasobu źródłowego, który chcesz usunąć.
2. Na stronie właściwości dla każdego zasobu wybierz pozycję **Usuń**.

## <a name="delete-additional-resources-created-for-move"></a>Usuń dodatkowe zasoby utworzone do przeniesienia

Po przeniesieniu można ręcznie usunąć kolekcję Move i Site Recovery utworzone zasoby.

- Kolekcja Move jest domyślnie ukryta. Aby zobaczyć, należy włączyć ukryte zasoby.
- Magazyn pamięci podręcznej ma blokadę, która musi zostać usunięta, zanim będzie można jej usunąć.

Usuń w następujący sposób: 

1. Znajdź zasoby w grupie zasobów w ```RegionMoveRG-<sourceregion>-<target-region>``` regionie źródłowym.
2. Sprawdź, czy wszystkie maszyny wirtualne i inne zasoby źródłowe w kolekcji przenoszenia zostały przeniesione/usunięte. Dzięki temu nie ma żadnych oczekujących zasobów, których używają.
2. Usuń zasoby:

    - Nazwa kolekcji przenoszenia to ```movecollection-<sourceregion>-<target-region>``` .
    - Nazwa konta magazynu pamięci podręcznej to ```resmovecache<guid>```
    - Nazwa magazynu to ```ResourceMove-<sourceregion>-<target-region>-GUID``` .

## <a name="next-steps"></a>Następne kroki


[Dowiedz się więcej o](about-move-process.md) procesie przenoszenia.
