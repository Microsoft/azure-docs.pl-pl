---
title: Przenoszenie zaszyfrowanych maszyn wirtualnych platformy Azure między regionami przy użyciu usługi Azure Resource przenoszącej
description: Dowiedz się, jak przenosić zaszyfrowane maszyny wirtualne platformy Azure do innego regionu za pomocą usługi Azure Resource przenoszącej
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361013"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Samouczek: przenoszenie zaszyfrowanych maszyn wirtualnych platformy Azure między regionami

W tym artykule dowiesz się, jak przenosić zaszyfrowane maszyny wirtualne platformy Azure do innego regionu platformy Azure przy użyciu [usługi Azure Resource](overview.md)przenoszącej. Oto co oznacza szyfrowanie:

- Maszyny wirtualne z włączonym szyfrowaniem dysków Azure. [Dowiedz się więcej](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- Lub maszyny wirtualne korzystające z kluczy zarządzanych przez klienta (CMKs) do szyfrowania w czasie spoczynku (szyfrowanie po stronie serwera). [Dowiedz się więcej](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Sprawdź wymagania wstępne. 
> * W przypadku maszyn wirtualnych z włączonym szyfrowaniem dysków Azure Skopiuj klucze i wpisy tajne z magazynu kluczy regionu źródłowego do magazynu kluczy regionu docelowego.
> * Przygotuj maszyny wirtualne, aby je przenieść, a następnie wybierz zasoby w regionie źródłowym, które chcesz przenieść.
> * Rozwiąż zależności zasobów.
> * W przypadku maszyn wirtualnych z włączonym szyfrowaniem dysków Azure ręcznie Przypisz docelowy Magazyn kluczy. W przypadku maszyn wirtualnych korzystających z szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta ręcznie Przypisz zestaw szyfrowanie dysków w regionie docelowym.
> * Przenieś magazyn kluczy i/lub zestaw szyfrowania dysków.
> * Przygotuj i Przenieś źródłową grupę zasobów. 
> * Przygotuj i Przenieś inne zasoby.
> * Zdecyduj, czy chcesz odrzucić lub zatwierdzić przeniesienie. 
> * Opcjonalnie można usunąć zasoby w regionie źródłowym po przeniesieniu.

> [!NOTE]
> Samouczki pokazują najszybszą ścieżkę w celu wypróbowania scenariusza i używają opcji domyślnych. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/). Następnie zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Wymagania wstępne

**Wymaganie** |**Szczegóły**
--- | ---
**Uprawnienia subskrypcji** | Sprawdź, czy masz dostęp *właściciela* do subskrypcji zawierającej zasoby, które chcesz przenieść.<br/><br/> **Dlaczego mam dostęp do właściciela?** Przy pierwszym dodawaniu zasobu dla określonej pary źródłowej i docelowej w ramach subskrypcji platformy Azure usługa zarządzania zasobami tworzy [tożsamość zarządzaną przypisaną przez system](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (znaną wcześniej jako identyfikator usługi zarządzanej (msi), która jest zaufana przez subskrypcję. Aby utworzyć tożsamość i przypisać do niej wymaganą rolę (współautor i administrator dostępu użytkowników w subskrypcji źródłowej), konto używane do dodawania zasobów wymaga uprawnień *właściciela* do subskrypcji. [Dowiedz się więcej](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) na temat ról platformy Azure.
**Obsługa maszyn wirtualnych** | Sprawdź, czy maszyny wirtualne, które chcesz przenieść, są obsługiwane.<br/><br/> - [Sprawdź](support-matrix-move-region-azure-vm.md#windows-vm-support) obsługiwane maszyny wirtualne z systemem Windows.<br/><br/> - [Sprawdź](support-matrix-move-region-azure-vm.md#linux-vm-support) obsługiwane maszyny wirtualne i wersje jądra systemu Linux.<br/><br/> -Sprawdź obsługiwane ustawienia [obliczeń](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [magazynu](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)i [sieci](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) .
**Wymagania dotyczące magazynu kluczy (usługa Azure Disk Encryption)** | Jeśli masz włączone szyfrowanie dysków Azure dla maszyn wirtualnych, oprócz magazynu kluczy w regionie źródłowym potrzebujesz magazynu kluczy w regionie docelowym. [Utwórz magazyn kluczy](../key-vault/general/quick-create-portal.md).<br/><br/> W przypadku magazynów kluczy w regionie źródłowym i docelowym wymagane są następujące uprawnienia:<br/><br/> -Kluczowe uprawnienia: operacje zarządzania kluczami (Get, list); Operacje kryptograficzne (odszyfrowywanie i szyfrowanie).<br/><br/> -Tajne uprawnienia: operacje zarządzania kluczami tajnymi (Get, list i Set)<br/><br/> -Certificate (lista i Get).
**Zestaw szyfrowania dysków (szyfrowanie po stronie serwera z CMK)** | W przypadku korzystania z maszyn wirtualnych z szyfrowaniem po stronie serwera przy użyciu CMK, oprócz szyfrowania dysków w regionie źródłowym, należy ustawić szyfrowanie dysków w regionie docelowym. [Utwórz zestaw szyfrowania dysków](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Przechodzenie między regionami nie jest obsługiwane, jeśli są używane klucze HSM dla kluczy zarządzanych przez klienta.
**Przydział regionu docelowego** | Subskrypcja wymaga wystarczającego limitu przydziału, aby utworzyć zasoby, które są przenoszone w regionie docelowym. Jeśli nie ma limitu przydziału, [Zażądaj dodatkowych limitów](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Opłaty w regionie docelowym** | Sprawdź ceny i opłaty związane z regionem docelowym, do którego przenosisz maszyny wirtualne. Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/) , aby uzyskać pomoc.


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>Weryfikowanie uprawnień użytkowników w magazynie kluczy dla maszyn wirtualnych przy użyciu Azure Disk Encryption (ADE)

Jeśli przenosisz maszyny wirtualne z włączonym szyfrowaniem dysków Azure, musisz uruchomić skrypt opisany [poniżej](#copy-the-keys-to-the-destination-key-vault) , dla którego użytkownik wykonujący skrypt powinien mieć odpowiednie uprawnienia. Zapoznaj się z poniższą tabelą, aby dowiedzieć się o wymaganych uprawnieniach. Opcje zmiany uprawnień można znaleźć, przechodząc do magazynu kluczy w Azure Portal, w obszarze **Ustawienia**, wybierz pozycję **zasady dostępu**.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Przycisk umożliwiający otwarcie zasad dostępu magazynu kluczy." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Jeśli nie ma żadnych uprawnień użytkownika, wybierz pozycję **Dodaj zasady dostępu** i określ uprawnienia. Jeśli konto użytkownika ma już zasady, w obszarze **użytkownik** Ustaw uprawnienia zgodnie z poniższą tabelą.

Maszyny wirtualne platformy Azure korzystające z programu ADE mogą mieć następujące odmiany i należy odpowiednio ustawić uprawnienia dla odpowiednich składników.
- Opcja domyślna, w której dysk jest szyfrowany tylko przy użyciu kluczy tajnych
- Dodano zabezpieczenia przy użyciu [klucza szyfrowania klucza](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-keyvault"></a>Magazyn kluczy w regionie źródłowym

Należy ustawić poniższe uprawnienia dla użytkownika wykonującego skrypt 

**Składnik** | **Wymagana uprawnienia**
--- | ---
Wpisy tajne|  Pobierz uprawnienie <br> </br> W obszarze tajne uprawnienia do zarządzania wpisami **tajnymi** >   wybierz pozycję **Pobierz** . 
Klucze <br> </br> Jeśli używasz klucza szyfrowania klucza (KEK), musisz mieć to uprawnienie oprócz wpisów tajnych| Uprawnienie pobieranie i odszyfrowywanie <br> </br> W obszarze **uprawnienia klucza**  >  **operacje zarządzania kluczami** wybierz pozycję **Pobierz**. W obszarze **operacje kryptograficzne** wybierz opcję **Odszyfruj**.

### <a name="destination-region-keyvault"></a>Magazyn kluczy regionu docelowego

W obszarze **zasady dostępu** upewnij się, że **Azure Disk Encryption szyfrowania woluminów** jest włączona. 

Należy ustawić poniższe uprawnienia dla użytkownika wykonującego skrypt 

**Składnik** | **Wymagana uprawnienia**
--- | ---
Wpisy tajne|  Ustaw uprawnienie <br> </br> W obszarze tajne uprawnienia do zarządzania wpisami **tajnymi** >   wybierz pozycję **Ustaw** . 
Klucze <br> </br> Jeśli używasz klucza szyfrowania klucza (KEK), musisz mieć to uprawnienie oprócz wpisów tajnych| Uprawnienie pobieranie, tworzenie i szyfrowanie <br> </br> W obszarze **uprawnienia klucza**  >  **operacje zarządzania kluczami** wybierz pozycję **Pobierz** i **Utwórz** . W obszarze **operacje kryptograficzne** wybierz pozycję **Szyfruj**.

Oprócz powyższych uprawnień w magazynie kluczy docelowych należy dodać uprawnienia do [tożsamości systemu zarządzanego](./common-questions.md#how-is-managed-identity-used-in-resource-mover) używanego przez urządzenia do zarządzania zasobami platformy Azure w Twoim imieniu. 

1. W obszarze **Ustawienia** wybierz pozycję **Dodaj zasady dostępu**. 
2. W obszarze **Wybierz podmiot zabezpieczeń** Wyszukaj plik msi. Nazwa MSI to ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
3. Dodaj poniższe uprawnienia dla pliku MSI

**Składnik** | **Wymagana uprawnienia**
--- | ---
Wpisy tajne|  Uprawnienie pobieranie i wyświetlanie <br> </br> W  >   **operacjach tajnych zarządzania** uprawnieniami tajnymi, wybierz pozycję **Pobierz** i **Wyświetl** 
Klucze <br> </br> Jeśli używasz klucza szyfrowania klucza (KEK), musisz mieć to uprawnienie oprócz wpisów tajnych| Pobieranie, uprawnienie do listy <br> </br> W obszarze **uprawnienia klucza**  >  **operacje zarządzania kluczami** wybierz pozycję **Pobierz** i **Wyświetl**



### <a name="copy-the-keys-to-the-destination-key-vault"></a>Kopiuj klucze do docelowego magazynu kluczy

Należy skopiować wpisy tajne i klucze szyfrowania z magazynu kluczy źródłowych do docelowego magazynu kluczy przy użyciu udostępnianego przez nas skryptu.

- Skrypt jest uruchamiany w programie PowerShell. Zalecamy uruchomienie najnowszej wersji programu PowerShell.
- Skrypt wymaga następujących modułów:
    - Az.Compute
    - AZ. 3.0.0
    - AZ. accounts (wersja 2.2.3)

Postępuj w następujący sposób:

1. Przejdź do [skryptu](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) w serwisie GitHub.
2. Skopiuj zawartość skryptu do pliku lokalnego i nadaj jej nazwę *Copy-keys.ps1*.
3. Uruchom skrypt.
4. Zaloguj się do platformy Azure.
5. W oknie podręcznym **dane wejściowe użytkownika** wybierz subskrypcję źródłową, grupę zasobów i ŹRÓDŁową maszynę wirtualną. Następnie wybierz lokalizację docelową i docelowe magazyny na potrzeby szyfrowania dysku i klucza.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Wyskakujące okienko wprowadzania wartości skryptu." :::


6. Po zakończeniu działania skryptu ekran danych wyjściowych wskazuje, że CopyKeys powiodło się.

## <a name="prepare-vms"></a>Przygotowywanie maszyn wirtualnych

1. Po [sprawdzeniu, czy maszyny wirtualne spełniają wymagania](#prerequisites), upewnij się, że maszyny wirtualne, które chcesz przenieść, są włączone. Wszystkie dyski maszyn wirtualnych, które mają być dostępne w regionie docelowym, muszą być dołączane i inicjowane na maszynie wirtualnej.
3. Sprawdź, czy maszyny wirtualne mają najnowsze zaufane certyfikaty główne oraz zaktualizowaną listę odwołania certyfikatów (CRL). W tym celu:
    - Na maszynach wirtualnych z systemem Windows zainstaluj najnowsze aktualizacje systemu Windows.
    - Na maszynach wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dystrybutora, aby komputery miały najnowsze certyfikaty i listę CRL. 
4. Zezwalaj na połączenia wychodzące z maszyn wirtualnych w następujący sposób:
    - Jeśli używasz serwera proxy zapory opartego na adresie URL w celu kontrolowania łączności wychodzącej, Zezwól na dostęp do tych [adresów URL](support-matrix-move-region-azure-vm.md#url-access)
    - Jeśli używasz reguł sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu kontrolowania łączności wychodzącej, Utwórz te [reguły tagów usług](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Wybierz zasoby do przeniesienia


- Możesz wybrać dowolny obsługiwany typ zasobu w dowolnej grupie zasobów w wybranym regionie źródłowym.  
- Przenoszenie zasobów do regionu docelowego, który znajduje się w tej samej subskrypcji co region źródłowy. Jeśli chcesz zmienić subskrypcję, możesz to zrobić po przeniesieniu zasobów.

Wybierz zasoby w następujący sposób:

1. W Azure Portal Wyszukaj pozycję *przeniesienie zasobów*. Następnie w obszarze **usługi** wybierz pozycję **Azure Resource** przenosząca.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Wyniki wyszukiwania dla przenoszenia zasobów w Azure Portal." :::

2. W obszarze **Przegląd** kliknij pozycję **Przenieś między regionami**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Przycisk, aby dodać zasoby do przeniesienia do innego regionu." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. W obszarze **Przenieś zasoby**  >  **Źródło + miejsce docelowe** wybierz źródłową subskrypcję i region.
4. W obszarze **Lokalizacja docelowa** wybierz region, do którego chcesz przenieść maszyny wirtualne. Następnie kliknij przycisk **Dalej**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Strona umożliwiająca wybranie regionu źródłowego i docelowego." :::

5. W obszarze **zasoby do przeniesienia** kliknij pozycję **Wybierz zasoby**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Kliknij przycisk, aby wybrać zasób do przeniesienia.]." :::

6. W obszarze **Wybierz zasoby** Wybierz Maszyny wirtualne. Można dodawać tylko zasoby, które są [obsługiwane do przenoszenia](#prepare-vms). Następnie kliknij przycisk **gotowe**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Strona umożliwiająca wybranie maszyn wirtualnych do przeniesienia." :::

    > [!NOTE]
    >  W tym samouczku wybieramy maszynę wirtualną korzystającą z szyfrowania po stronie serwera (Rayne-VM) z kluczem zarządzanym przez klienta, a maszyna wirtualna z włączonym szyfrowaniem dysków (Rayne-VM-ADE).

7.  W obszarze **zasoby do przeniesienia** kliknij przycisk **dalej**.
8. W obszarze **Przegląd** Sprawdź ustawienia źródłowe i docelowe. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Strona umożliwiająca przejrzenie ustawień i Kontynuuj przenoszenie." :::

9. Kliknij przycisk " **Zastosuj**", aby rozpocząć dodawanie zasobów.
10. Wybierz ikonę powiadomienia, aby śledzić postęp. Po pomyślnym zakończeniu dodawania wybierz pozycję **dodane zasoby do przeniesienia** w powiadomieniach.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Powiadomienie o potwierdzeniu zasobów zostało pomyślnie dodane." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. Po kliknięciu powiadomienia Przejrzyj zasoby na stronie **między regionami** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Strony pokazujące dodane zasoby z oczekującą przygotowaniem." :::

> [!NOTE]
> - Dodawane zasoby są umieszczane w stanie *oczekiwania na przygotowanie* .
> - Grupa zasobów dla maszyn wirtualnych jest dodawana automatycznie.
> - W przypadku zmodyfikowania wpisów **konfiguracji docelowej** w celu użycia zasobu, który już istnieje w regionie docelowym, stan zasobu jest ustawiony na *oczekujące*, ponieważ nie trzeba inicjować przenoszenia dla niego.
> - Jeśli chcesz usunąć zasób, który został dodany, metoda, która zależy od tego, gdzie jesteś w procesie przenoszenia. [Dowiedz się więcej](remove-move-resources.md).


## <a name="resolve-dependencies"></a>Rozwiązywanie zależności

1. Jeśli w kolumnie **problemy** zostanie wyświetlony komunikat *Weryfikuj zależności* , wybierz przycisk **Weryfikuj zależności** .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Nprzycisk do sprawdzenia zależności." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Rozpocznie się proces walidacji.
2. Jeśli znajdują się zależności, kliknij przycisk **Dodaj zależności** .  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Przycisk, aby dodać zależności." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. W obszarze **Dodawanie zależności** Pozostaw domyślną opcję **Pokaż wszystkie zależności** .

    - **Pokaż wszystkie zależności** iteracji przez wszystkie zależności bezpośrednie i pośrednie dla zasobu. Na przykład dla maszyny wirtualnej jest wyświetlana karta sieciowa, Sieć wirtualna, sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) itp.
    - **Pokaż zależności pierwszego poziomu wyświetla tylko** zależności bezpośrednie. Na przykład dla maszyny wirtualnej jest wyświetlana karta sieciowa, ale nie Sieć wirtualna.
 
4. Wybierz zasoby zależne, które chcesz dodać > **Dodaj zależności**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Wybierz pozycję zależności z listy zależności." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. Ponownie Zweryfikuj zależności. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Ponownie sprawdź poprawność strony." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Przypisywanie zasobów docelowych

Zasoby docelowe skojarzone z szyfrowaniem wymagają przypisania ręcznego.

- Jeśli przenosisz maszynę wirtualną, która ma usługę Azure Disk Encryption (ADE), Magazyn kluczy w regionie docelowym będzie widoczny jako zależność.
- Jeśli przenosisz maszynę wirtualną, która ma szyfrowanie po stronie serwera, która używa niestandardowych kluczy (CMKs), wówczas szyfrowanie dysków ustawione w regionie docelowym jest wyświetlane jako zależność. 
- Ponieważ ten samouczek przeniesie maszynę wirtualną z włączonym programem ADE oraz maszynę wirtualną z systemem CMK, zarówno docelowy Magazyn kluczy, jak i zestaw szyfrowania dysków są wyświetlane jako zależności.

Przypisz ręcznie w następujący sposób:

1. W pozycji wpis ustawienia szyfrowania dysku wybierz pozycję **zasób nieprzypisany** w kolumnie **Konfiguracja docelowa** .
2. W obszarze **Ustawienia konfiguracji** wybierz zestaw szyfrowanie dysku docelowego. Następnie wybierz pozycję **Zapisz zmiany**.
3. Można wybrać opcję zapisywania i weryfikowania zależności zasobu, który jest modyfikowany, lub po prostu zapisać zmiany i sprawdzić poprawność wszystkich modyfikacji w jednym.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Strona wybierania zestawu szyfrowanie dysków w regionie docelowym." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Po dodaniu zasobu docelowego stan zestawu szyfrowania dysku zmieni się na *Zatwierdź oczekujące przeniesienie*.
3. W wpis magazynu kluczy wybierz pozycję **zasób nieprzypisany** w kolumnie **Konfiguracja docelowa** . **Ustawienia konfiguracji** Wybierz docelowy Magazyn kluczy. Zapisz zmiany. 

Na tym etapie zestaw szyfrowanie dysków i stan magazynu kluczy są zmieniane na *Zatwierdź oczekujące przeniesienia*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Strony, aby wybrać przygotowanie do innych zasobów." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Aby zatwierdzić i zakończyć proces przenoszenia dla zasobów szyfrowania.

1. W **różnych regionach** wybierz zasób (zestaw szyfrowania dysku lub Magazyn kluczy) > **przeniesieniu**.
2. w obszarze **Przenieś zasoby** kliknij pozycję **Zatwierdź**.

> [!NOTE]
> Po zatwierdzeniu przeniesienia zasób jest w stanie *oczekiwania na usunięcie źródła* .


## <a name="move-the-source-resource-group"></a>Przenoszenie źródłowej grupy zasobów 

Aby można było przygotować i przenieść maszyny wirtualne, Grupa zasobów maszyny wirtualnej musi znajdować się w regionie docelowym. 

### <a name="prepare-to-move-the-source-resource-group"></a>Przygotowanie do przeniesienia źródłowej grupy zasobów

Podczas przygotowania proces przenoszenia zasobów generuje szablony Azure Resource Manager (ARM) przy użyciu ustawień grupy zasobów. Nie ma to wpływu na zasoby znajdujące się w grupie zasobów.

Przygotuj się w następujący sposób:

1. W **różnych regionach** wybierz źródłową grupę zasobów > **Przygotuj**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Przygotuj grupę zasobów." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. W obszarze **Przygotowywanie zasobów** kliknij pozycję **Przygotuj**.

> [!NOTE]
> Po przygotowaniu grupy zasobów znajduje się ona w stanie " *Inicjowanie przenoszenia oczekujące* ". 

 
### <a name="move-the-source-resource-group"></a>Przenoszenie źródłowej grupy zasobów

Zainicjuj przechodzenie w następujący sposób:

1. W **różnych regionach** wybierz grupę zasobów > **zainicjować przenoszenie**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Przycisk umożliwiający zainicjowanie przenoszenia." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. LN **przenoszenie zasobów**, kliknij przycisk **Inicjuj przenoszenie**. Grupa zasobów przechodzi do stanu *inicjowania przenoszenia w toku* .   
3. Po zainicjowaniu przenoszenia docelowa Grupa zasobów zostanie utworzona na podstawie wygenerowanego szablonu ARM. Źródłowa Grupa zasobów przechodzi w stan *oczekiwania na przeniesienie zatwierdzenia* .

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Przejrzyj stan oczekiwania przeniesienia zatwierdzenia." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Aby zatwierdzić i zakończyć proces przenoszenia:

1. W **różnych regionach** wybierz grupę zasobów > **Zatwierdź przeniesienie**.
2. w obszarze **Przenieś zasoby** kliknij pozycję **Zatwierdź**.

> [!NOTE]
> Po zatwierdzeniu przeniesienia źródłowa Grupa zasobów jest w stanie oczekiwania na *usunięcie źródła* .

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Przejrzyj stan oczekiwania na usunięcie przeniesienia." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Przygotowanie zasobów do przeniesienia

Po przeniesieniu zasobów szyfrowania i źródłowej grupy zasobów można przystąpić do przenoszenia innych zasobów w stanie *oczekiwania na przygotowanie* .


1. W **różnych regionach** Sprawdź ponownie i rozwiąż wszelkie problemy.
2. Jeśli chcesz edytować ustawienia docelowe przed rozpoczęciem przenoszenia, wybierz link w kolumnie **Konfiguracja docelowa** dla zasobu i Edytuj ustawienia. Jeśli edytujesz ustawienia docelowej maszyny wirtualnej, docelowy rozmiar maszyny wirtualnej nie powinien być mniejszy niż rozmiar źródłowej maszyny wirtualnej.
3. Wybierz pozycję **Przygotuj** dla zasobów w stanie *oczekiwania na przygotowanie* , który chcesz przenieść.
3. W obszarze **Przygotowywanie zasobów** wybierz pozycję **Przygotuj** .

    - W trakcie procesu przygotowywania Agent Azure Site Recovery Mobility jest instalowany na maszynach wirtualnych w celu replikowania ich.
    - Dane maszyn wirtualnych są okresowo replikowane do regionu docelowego. Nie ma to wpływu na źródłową maszynę wirtualną.
    - Przeniesienie zasobu powoduje wygenerowanie szablonów ARM dla innych zasobów źródłowych.

Po przygotowaniu zasobów znajdują się one w stanie " *Inicjowanie przenoszenia oczekujące* ".

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Strona wyświetlająca zasoby w stanie inicjowania przenoszenia oczekujących." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Inicjowanie przenoszenia

Po przygotowaniu zasobów można teraz zainicjować przenoszenie. 

1. W **różnych regionach** wybierz pozycję zasoby z stanem *Inicjuj przenoszenie oczekujące*. Następnie kliknij pozycję **zainicjuj przenoszenie**.
2. W obszarze **Przenieś zasoby** kliknij pozycję **Inicjuj przenoszenie**.
3. Śledź postęp przenoszenia na pasku powiadomień.

    - W przypadku maszyn wirtualnych repliki maszyn wirtualnych są tworzone w regionie docelowym. Źródłowa maszyna wirtualna jest wyłączona i występuje trochę przestoju (zwykle w minutach).
    - Przeniesienie zasobów umożliwia odtworzenie innych zasobów przy użyciu szablonów ARM, które zostały przygotowane. Zwykle nie ma przestojów.
    - Po przeniesieniu zasobów są one w stanie *oczekiwania na przeniesienie zatwierdzenia* .

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Strona wyświetlająca zasoby w stanie oczekiwania na przeniesienie zatwierdzenia." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


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

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Strona do zatwierdzania zasobów w celu sfinalizowania przenoszenia." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. Śledź postęp zatwierdzania na pasku powiadomień.

> [!NOTE]
> - Po zatwierdzeniu przenoszenia maszyny wirtualne zatrzymają replikację. Zatwierdzenie nie ma wpływu na źródłową maszynę wirtualną.
> - Zatwierdzenie nie ma wpływu na zasoby sieci źródłowej.
> - Po zatwierdzeniu przenoszenia zasoby są w stanie oczekiwania na *usunięcie źródła* .



## <a name="configure-settings-after-the-move"></a>Skonfiguruj ustawienia po przeniesieniu

- Usługa mobilności nie została automatycznie odinstalowana z maszyn wirtualnych. Odinstaluj je ręcznie lub pozostaw to pole, jeśli planujesz ponownie przenieść serwer.
- Modyfikuj reguły kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure po przeniesieniu.

## <a name="delete-source-resources-after-commit"></a>Usuń zasoby źródłowe po zatwierdzeniu

Po przeniesieniu można opcjonalnie usunąć zasoby w regionie źródłowym. 

1. W **różnych regionach** zaznacz każdy zasób źródłowy, który chcesz usunąć. następnie wybierz pozycję **Usuń źródło**.
2. W obszarze **Usuwanie źródła** Przejrzyj elementy, które zamierzasz usunąć, a następnie w obszarze **Potwierdź usunięcie** wpisz **wartość tak**. Akcja jest nieodwracalna, dlatego sprawdź uważnie!
3. Po wpisaniu **opcji tak** wybierz pozycję **Usuń źródło**.

> [!NOTE]
>  W portalu przenoszenia zasobów nie można usunąć grup zasobów, magazynów kluczy ani serwerów SQL Server. Należy usunąć je pojedynczo ze strony właściwości dla każdego zasobu.


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
> * Przeniesiono zaszyfrowane maszyny wirtualne platformy Azure i ich zasoby zależne do innego regionu platformy Azure.


Teraz trwa próba przeniesienia baz danych i pul elastycznych usługi Azure SQL Server do innego regionu.

> [!div class="nextstepaction"]
> [Przenoszenie zasobów usługi Azure SQL](./tutorial-move-region-sql.md)
