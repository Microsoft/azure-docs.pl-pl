---
title: Przenoszenie zaszyfrowanych maszyn wirtualnych platformy Azure między regionami przy użyciu Azure Resource Mover
description: Dowiedz się, jak przenieść zaszyfrowane maszyny wirtualne platformy Azure do innego regionu przy użyciu Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600696"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Samouczek: przenoszenie zaszyfrowanych maszyn wirtualnych platformy Azure między regionami

W tym artykule omówiono sposób przenoszenia zaszyfrowanych maszyn wirtualnych platformy Azure do innego regionu świadczenia usługi Azure przy użyciu [Azure Resource Mover](overview.md). 

Zaszyfrowane maszyny wirtualne można opisać jako:

- Maszyny wirtualne, które mają dyski z Azure Disk Encryption włączone. Aby uzyskać więcej informacji, zobacz [Tworzenie i szyfrowanie maszyny wirtualnej z](../virtual-machines/windows/disk-encryption-portal-quickstart.md)systemem Windows przy użyciu Azure Portal .
- Maszyny wirtualne, które używają kluczy zarządzanych przez klienta na potrzeby szyfrowania danych w spoczynku lub szyfrowania po stronie serwera. Aby uzyskać więcej informacji, zobacz [Use the Azure Portal to enable server-side encryption with customer-managed keys for managed disks (Włączanie](../virtual-machines/disks-enable-customer-managed-keys-portal.md)szyfrowania po stronie serwera przy użyciu kluczy zarządzanych przez klienta dla dysków zarządzanych).


Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Sprawdź wymagania wstępne. 
> * W przypadku maszyn wirtualnych z włączoną Azure Disk Encryption skopiuj klucze i wpisy tajne z magazynu kluczy regionu źródłowego do magazynu kluczy regionu docelowego.
> * Przygotuj się do przeniesienia maszyn wirtualnych i wybierz zasoby w regionie źródłowym, z którego chcesz je przenieść.
> * Rozwiąż zależności zasobów.
> * W przypadku maszyn wirtualnych z włączoną Azure Disk Encryption ręcznie przypisz docelowy magazyn kluczy. W przypadku maszyn wirtualnych, które używają szyfrowania po stronie serwera z kluczami zarządzanymi przez klienta, ręcznie przypisz szyfrowanie dysków ustawione w regionie docelowym.
> * Przenieś magazyn kluczy lub zestaw szyfrowania dysków.
> * Przygotuj i przenieś źródłową grupę zasobów. 
> * Przygotuj i przenieś inne zasoby.
> * Zdecyduj, czy odrzucić, czy zatwierdzić przeniesienie. 
> * Opcjonalnie usuń zasoby w regionie źródłowym po zakończeniu przenoszenia.

> [!NOTE]
> W tym samouczku przedstawiono najszybszą ścieżkę do wypróbowania scenariusza. Używa tylko opcji domyślnych. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/). Następnie zaloguj się do [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Wymagania wstępne

Wymaganie |Szczegóły
--- | ---
**Uprawnienia subskrypcji** | Upewnij się, że masz *dostęp właściciela* do subskrypcji zawierającej zasoby, które chcesz przenieść.<br/><br/> *Dlaczego potrzebuję dostępu właściciela?* Podczas pierwszego dodawania zasobu dla określonej pary źródłowej i docelowej w subskrypcji platformy Azure usługa Resource Mover tworzy tożsamość zarządzaną przypisaną przez system [,](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)znaną wcześniej jako tożsamość usługi zarządzanej (MSI). Ta tożsamość jest zaufana przez subskrypcję. Aby można było utworzyć tożsamość i przypisać do niego wymagane role *(współautor* i *administrator* dostępu  użytkowników w subskrypcji źródłowej), konto, za pomocą których dodajesz zasoby, musi mieć uprawnienia właściciela w subskrypcji. Aby uzyskać więcej informacji, zobacz [Role klasycznego administratora subskrypcji, role platformy Azure i role usługi Azure AD.](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)
**Obsługa maszyn wirtualnych** | Upewnij się, że maszyny wirtualne, które chcesz przenieść, są obsługiwane, wykonując następujące czynności:<li>[Sprawdź obsługiwane](support-matrix-move-region-azure-vm.md#windows-vm-support) maszyny wirtualne z systemem Windows.<li>[Sprawdź obsługiwane](support-matrix-move-region-azure-vm.md#linux-vm-support) maszyny wirtualne z systemem Linux i wersje jądra.<li>Sprawdź obsługiwane [ustawienia obliczeniowe,](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings) [magazynowe](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) [i sieciowe.](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)
**Wymagania dotyczące magazynu kluczy (Azure Disk Encryption)** | Jeśli masz włączoną Azure Disk Encryption maszyn wirtualnych, potrzebujesz magazynu kluczy zarówno w regionach źródłowym, jak i docelowym. Aby uzyskać więcej informacji, [zobacz Tworzenie magazynu kluczy](../key-vault/general/quick-create-portal.md).<br/><br/> W przypadku magazynów kluczy w regionach źródłowym i docelowym potrzebne są następujące uprawnienia:<li>Uprawnienia klucza: operacje zarządzania kluczami (get, list) i operacje kryptograficzne (odszyfrowywanie i szyfrowanie)<li>Uprawnienia dotyczące tajnych: operacje zarządzania kluczami tajnymi (Get, List i Set)<li>Certyfikat (lista i pobierz)
**Zestaw szyfrowania dysków (szyfrowanie po stronie serwera za pomocą klucza cmk)** | Jeśli używasz maszyn wirtualnych z szyfrowaniem po stronie serwera, które używa klucza cmk, musisz ustawić szyfrowanie dysków zarówno w regionach źródłowym, jak i docelowym. Aby uzyskać więcej informacji, [zobacz Tworzenie zestawu szyfrowania dysków.](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)<br/><br/> Przenoszenie między regionami nie jest obsługiwane, jeśli używasz sprzętowego modułu zabezpieczeń (kluczy HSM) dla kluczy zarządzanych przez klienta.
**Limit przydziału regionu docelowego** | Subskrypcja wymaga wystarczającego limitu przydziału, aby utworzyć zasoby, które przenosisz w regionie docelowym. Jeśli nie ma limitu przydziału, [zażądaj dodatkowych limitów](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Opłaty za region docelowy** | Sprawdź ceny i opłaty skojarzone z regionem docelowym, do którego przenosisz maszyny wirtualne. Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/).


## <a name="verify-permissions-in-the-key-vault"></a>Weryfikowanie uprawnień w magazynie kluczy

Jeśli przenosisz maszyny wirtualne, które mają włączoną Azure Disk Encryption, musisz uruchomić skrypt, jak wspomniano w sekcji Kopiowanie kluczy do [docelowego magazynu](#copy-the-keys-to-the-destination-key-vault) kluczy. Użytkownicy, którzy wykonują skrypt, powinni mieć do tego odpowiednie uprawnienia. Aby zrozumieć, które uprawnienia są potrzebne, zapoznaj się z następującą tabelą. Opcje zmiany uprawnień można znaleźć, przechodząc do magazynu kluczy w Azure Portal. W **obszarze Ustawienia** wybierz pozycję Zasady **dostępu.**

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Zrzut ekranu przedstawiający link &quot;Zasady dostępu&quot; w okienku Ustawienia magazynu kluczy." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Jeśli uprawnienia użytkownika nie są na miejscu, wybierz pozycję Dodaj zasady **dostępu,** a następnie określ uprawnienia. Jeśli konto użytkownika ma już zasady, w obszarze **Użytkownik** ustaw uprawnienia zgodnie z instrukcjami w poniższej tabeli.

Maszyny wirtualne platformy Azure, które używają Azure Disk Encryption, mogą mieć następujące odmiany i musisz ustawić uprawnienia zgodnie z ich odpowiednimi składnikami. Maszyny wirtualne mogą mieć:
- Domyślna opcja, w której dysk jest szyfrowany tylko przy użyciu wpisów tajnych.
- Dodano zabezpieczenia, które używa klucza szyfrowania [klucza (KEK).](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-key-vault"></a>Magazyn kluczy w regionie źródłowym

W przypadku użytkowników, którzy wykonują skrypt, ustaw uprawnienia dla następujących składników: 

Składnik | Wymagane uprawnienia
--- | ---
Wpisy tajne |  *Get* <br></br> Wybierz **pozycję Uprawnienia tajne** Operacje zarządzania  >  **kluczami tajnymi,** a następnie wybierz pozycję **Pobierz**. 
Klucze <br></br> Jeśli używasz kluczy KEK, potrzebujesz tych uprawnień oprócz uprawnień do wpisów tajnych. | *Pobierz i* odszyfruj  <br></br> Wybierz **pozycję Operacje zarządzania**  >  **kluczami uprawnień klucza,** a następnie wybierz pozycję **Pobierz**. Na **stronie Operacje kryptograficzne** wybierz pozycję **Odszyfruj**.

### <a name="destination-region-key-vault"></a>Magazyn kluczy regionu docelowego

W **zasadach dostępu** upewnij się, że Azure Disk Encryption szyfrowania **woluminów.** 

W przypadku użytkowników, którzy wykonują skrypt, ustaw uprawnienia dla następujących składników: 

Składnik | Wymagane uprawnienia
--- | ---
Wpisy tajne |  *Set* <br></br> Wybierz **pozycję Uprawnienia tajne** Operacje zarządzania  >  **kluczami tajnymi,** a następnie wybierz pozycję **Ustaw**. 
Klucze <br></br> Jeśli używasz kluczy KEK, potrzebujesz tych uprawnień oprócz uprawnień do wpisów tajnych. | *Pobierz,* *utwórz* i *zaszyfruj* <br></br> Wybierz **pozycję Operacje zarządzania**  >  **kluczami uprawnień klucza,** a następnie wybierz pozycję Pobierz **i** **utwórz.** W **opcji Operacje kryptograficzne** wybierz pozycję **Szyfruj.**

<br>

Oprócz powyższych uprawnień w docelowym magazynie kluczy należy dodać uprawnienia [](./common-questions.md#how-is-managed-identity-used-in-resource-mover) do tożsamości systemu zarządzanego, która jest używana przez usługę Resource Mover do uzyskiwania dostępu do zasobów platformy Azure w Twoim imieniu. 

1. W **obszarze Ustawienia** wybierz pozycję Dodaj zasady **dostępu.** 
1. W **polu Wybierz podmiot** zabezpieczeń wyszukaj pozycję MSI. Nazwa MSI to ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
1. W przypadku pliku MSI dodaj następujące uprawnienia:

    Składnik | Wymagane uprawnienia
    --- | ---
    Wpisy tajne|  *Uzyskiwanie* i *uzyskiwanie listy* <br></br> Wybierz **pozycję Secret permissions** Secret Management Operations  >  **(Operacje zarządzania kluczami tajnymi),** a następnie wybierz pozycję Get and List **(Pobierz** i **wymień).** 
    Klucze <br></br> Jeśli używasz kluczy KEK, potrzebujesz tych uprawnień oprócz uprawnień do wpisów tajnych. | *Uzyskiwanie* i *uzyskiwanie listy* <br></br> Wybierz **pozycję Key Permissions** Key Management Operations (Operacje zarządzania kluczami uprawnień  >  klucza), a następnie wybierz pozycję Get and List **(Pobierz** i **wymień).**

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>Kopiowanie kluczy do docelowego magazynu kluczy

Skopiuj wpisy tajne i klucze szyfrowania z magazynu kluczy źródłowych do docelowego magazynu kluczy przy użyciu [skryptu,](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) który udostępniliśmy.

- Uruchom skrypt w programie PowerShell. Zalecamy używanie najnowszej wersji programu PowerShell.
- W szczególności skrypt wymaga tych modułów:
    - Az.Compute
    - Az.KeyVault (wersja 3.0.0)
    - Az.Accounts (wersja 2.2.3)

Aby uruchomić skrypt, wykonaj następujące czynności:

1. Otwórz skrypt [w](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) usłudze GitHub.
1. Skopiuj zawartość skryptu do pliku lokalnego i nadaj jej *nazwęCopy-keys.ps1*.
1. Uruchom skrypt.
1. Zaloguj się w witrynie Azure Portal.
1. Na listach rozwijanych w oknie **Dane** wejściowe użytkownika wybierz subskrypcję źródłową, grupę zasobów i źródłową maszynę wirtualną, a następnie wybierz lokalizację docelową oraz docelowe magazyny szyfrowania dysków i kluczy.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Zrzut ekranu przedstawiający okno &quot;User Inputs&quot; (Dane wejściowe użytkownika) służące do wprowadzania wartości skryptu." :::

1. Wybierz przycisk **Wybierz**. 
   
   Po zakończeniu działania skryptu zostanie wyświetlony komunikat z powiadomieniem, że klucz CopyKeys zakończył się pomyślnie.

## <a name="prepare-vms"></a>Przygotowywanie maszyn wirtualnych

1. Po sprawdzeniu, czy maszyny wirtualne spełniają wymagania [wstępne,](#prerequisites)upewnij się, że maszyny wirtualne, które chcesz przenieść, są włączone. Wszystkie dyski maszyny wirtualnej, które mają być dostępne w regionie docelowym, muszą być dołączone i zainicjowane na maszynie wirtualnej.
1. Aby upewnić się, że maszyny wirtualne mają najnowsze zaufane certyfikaty główne i zaktualizowaną listę odwołania certyfikatów (CRL), wykonaj następujące czynności:
    - Na komputerach wirtualnych z systemem Windows zainstaluj najnowsze aktualizacje systemu Windows.
    - Na maszynach wirtualnych z systemem Linux postępuj zgodnie ze wskazówkami dystrybutora, aby maszyny były zgodne z najnowszymi certyfikatami i listą CRL. 
1. Aby zezwolić na łączność wychodzącą z maszyn wirtualnych, wykonaj jedną z następujących czynności:
    - Jeśli używasz serwera proxy zapory opartego na adresach URL do kontrolowania łączności wychodzącej, zezwładuj na dostęp [do adresów URL.](support-matrix-move-region-azure-vm.md#url-access)
    - Jeśli używasz reguł sieciowej grupy zabezpieczeń (NSG) do kontrolowania łączności wychodzącej, utwórz te [reguły tagów usługi](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-the-resources-to-move"></a>Wybieranie zasobów do przeniesienia

- Możesz wybrać dowolny obsługiwany typ zasobu w dowolnej grupie zasobów w regionie źródłowym, który wybierzesz.  
- Zasoby można przenieść do regionu docelowego, który jest w tej samej subskrypcji co region źródłowy. Jeśli chcesz zmienić subskrypcję, możesz to zrobić po zmianie zasobów.

Aby wybrać zasoby, wykonaj następujące czynności:

1. W Azure Portal wyszukaj w **polu resource mover .** Następnie w obszarze **Usługi** wybierz pozycję **Azure Resource Mover**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Zrzut ekranu przedstawiający wyniki wyszukiwania Azure Resource Mover w Azure Portal." :::

1. W okienku Azure Resource Mover **Przegląd** wybierz pozycję **Przenieś między regionami.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Zrzut ekranu przedstawiający przycisk &quot;Przenieś między regionami&quot; dodawania zasobów w celu przeniesienia ich do innego regionu." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. W **okienku Przenoszenie zasobów** wybierz **kartę Źródło i miejsce** docelowe. Następnie z list rozwijanych wybierz subskrypcję źródłową i region.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Strona wybierania regionu źródłowego i docelowego." :::

1. W **obszarze** Miejsce docelowe wybierz region, do którego chcesz przenieść maszyny wirtualne, a następnie wybierz pozycję **Dalej.**

1. Wybierz **kartę Zasoby do przeniesienia,** a następnie wybierz **pozycję Wybierz zasoby.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Zrzut ekranu przedstawiający okienko &quot;Przenoszenie zasobów&quot; i przycisk &quot;Wybierz zasoby&quot;." :::

1. W **okienku Wybierz** zasoby wybierz maszyny wirtualne, które chcesz przenieść. Jak wspomniano w sekcji Wybieranie zasobów do [przeniesienia,](#select-the-resources-to-move) można dodać tylko te zasoby, które są obsługiwane w przypadku przenoszenia.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Zrzut ekranu przedstawiający okienko &quot;Wybieranie zasobów&quot; służące do wybierania maszyn wirtualnych do przeniesienia." :::

    > [!NOTE]
    >  W tym samouczku wybierasz maszynę wirtualną, która korzysta z szyfrowania po stronie serwera (rayne-vm) z kluczem zarządzanym przez klienta, oraz maszynę wirtualną z włączonym szyfrowaniem dysków (rayne-vm-ade).

1. Kliknij **Gotowe**.
1. Wybierz **kartę Zasoby do przeniesienia,** a następnie wybierz pozycję **Dalej.**
1. Wybierz **kartę Przegląd,** a następnie sprawdź ustawienia źródła i miejsca docelowego. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Zrzut ekranu przedstawiający okienko służące do przeglądania ustawień źródła i miejsca docelowego." :::

1. Wybierz **pozycję Kontynuuj,** aby rozpocząć dodawanie zasobów.
1. Wybierz ikonę powiadomień, aby śledzić postęp. Po pomyślnym zakończeniu procesu w **okienku Powiadomienia** wybierz pozycję **Dodano zasoby do przeniesienia.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Zrzut ekranu przedstawiający okienko &quot;Powiadomienia&quot; służące do potwierdzenia, że zasoby zostały pomyślnie dodane." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. Po wybraniu powiadomienia przejrzyj zasoby na stronie **Wiele regionów.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Zrzut ekranu przedstawiający dodane zasoby ze stanem &quot;Przygotowanie oczekuje&quot;." :::

> [!NOTE]
> - Zasoby, które dodajesz, są umieszczane w stanie *Oczekiwanie na przygotowanie.*
> - Grupa zasobów dla maszyn wirtualnych jest dodawana automatycznie.
> - W przypadku  zmodyfikowania wpisów konfiguracji Miejsce docelowe w celu użycia zasobu, który już istnieje w regionie docelowym, stan zasobu zostanie ustawiony na Oczekujące *zatwierdzenie,* ponieważ nie trzeba inicjować dla niego przeniesienia.
> - Jeśli chcesz usunąć dodany zasób, metoda, której użyjesz, zależy od tego, gdzie jesteś w procesie przenoszenia. Aby uzyskać więcej informacji, zobacz [Zarządzanie kolekcjami przenoszenia i grupami zasobów.](remove-move-resources.md)


## <a name="resolve-dependencies"></a>Rozwiązywanie zależności

1. Jeśli jakiekolwiek zasoby pokazują komunikat *Weryfikuj zależności* w kolumnie **Problemy,** wybierz przycisk **Weryfikuj zależności.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Zrzut ekranu przedstawiający przycisk &quot;Weryfikuj zależności&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Rozpocznie się proces walidacji.
1. Jeśli zależności zostaną znalezione, wybierz **pozycję Dodaj zależności.**  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Zrzut ekranu przedstawiający przycisk &quot;Dodaj zależności&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. W **okienku Dodawanie zależności** pozostaw domyślną **opcję Pokaż wszystkie** zależności.

    - **Pokaż wszystkie zależności** iteruje po wszystkich zależnościach bezpośrednich i pośrednich dla zasobu. Na przykład w przypadku maszyny wirtualnej przedstawia ona kartę sieciową, sieć wirtualną, sieciowe grupy zabezpieczeń i tak dalej.
    - **Pokazywanie zależności pierwszego poziomu pokazuje tylko** zależności bezpośrednie. Na przykład w przypadku maszyny wirtualnej pokazuje ona kartę sieciową, ale nie sieć wirtualną.
 
1. Wybierz zasoby zależne, które chcesz dodać, a następnie wybierz **pozycję Dodaj zależności.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Zrzut ekranu przedstawiający listę zależności i przycisk &quot;Dodaj zależności&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. Ponownie zweryfikuj zależności. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Zrzut ekranu przedstawiający okienko służące do ponownego szacowania zależności." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Przypisywanie zasobów docelowych

Należy ręcznie przypisać zasoby docelowe skojarzone z szyfrowaniem.

- Jeśli przenosisz maszynę wirtualną, która ma włączoną Azure Disk Encryption, magazyn kluczy w regionie docelowym jest wyświetlany jako zależność.
- Jeśli przenosisz maszynę wirtualną z szyfrowaniem po stronie serwera, która używa kluczy CMK, szyfrowanie dysków ustawione w regionie docelowym będzie wyświetlane jako zależność. 
- Ponieważ w tym samouczku pokazano przenoszenie maszyny wirtualnej, Azure Disk Encryption została włączona i która używa klucza cmk, zarówno docelowy magazyn kluczy, jak i konfiguracja szyfrowania dysków są wyświetlane jako zależności.

Aby ręcznie przypisać zasoby docelowe, wykonaj następujące czynności:

1. We wpisie zestawu szyfrowania dysków wybierz **pozycję Zasób nie przypisano** w **kolumnie Konfiguracja lokalizacji** docelowej.
1. W **ustawieniach konfiguracji** wybierz docelowy zestaw szyfrowania dysków, a następnie wybierz **pozycję Zapisz zmiany.**
1. Możesz zapisać i zweryfikować zależności dla modyfikowanego zasobu lub zapisać tylko zmiany, a następnie zweryfikować wszystkie zmiany w tym samym czasie.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Zrzut ekranu przedstawiający okienko &quot;Konfiguracja miejsca docelowego&quot; służące do zapisywania zmian w regionie docelowym." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Po dodaniu zasobu docelowego stan zestawu szyfrowania dysków zmieni się na *Oczekiwanie na zatwierdzenie przeniesienia.*

1. We wpisie magazynu kluczy wybierz **pozycję Zasób nie przypisano w** kolumnie **Konfiguracja docelowa.** W **obszarze Ustawienia** konfiguracji wybierz docelowy magazyn kluczy, a następnie zapisz zmiany. 

Na tym etapie stan zestawu szyfrowania dysków i magazynu kluczy jest zmieniany na *Oczekiwanie na zatwierdzenie przeniesienia.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Zrzut ekranu przedstawiający okienko do przygotowywania innych zasobów." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Aby zatwierdzić i zakończyć proces przenoszenia zasobów szyfrowania, wykonaj następujące czynności:

1. W **poszczególnych regionach** wybierz zasób (zestaw szyfrowania dysków lub magazyn kluczy), a następnie wybierz **pozycję Zat zatwierdzanie przeniesienia**.
1. W **elektorze Przenoszenie zasobów** wybierz pozycję **Zat zatwierdzanie**.

> [!NOTE]
> Po zakończeniu przenoszenia stan zasobu zmieni się na *Usuń oczekujące źródło.*


## <a name="move-the-source-resource-group"></a>Przenoszenie źródłowej grupy zasobów 

Aby można było przygotować i przenieść maszyny wirtualne, grupa zasobów maszyny wirtualnej musi znajdować się w regionie docelowym. 

### <a name="prepare-to-move-the-source-resource-group"></a>Przygotowanie do przeniesienia źródłowej grupy zasobów

Podczas procesu przygotowywania zasób mover generuje Azure Resource Manager (ARM) na podstawie ustawień grupy zasobów. Nie ma to wpływu na zasoby w grupie zasobów.

Aby przygotować się do przeniesienia źródłowej grupy zasobów, wykonaj następujące czynności:

1. W **poszczególnych regionach** wybierz źródłową grupę zasobów, a następnie wybierz pozycję **Przygotuj**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Zrzut ekranu przedstawiający przycisk &quot;Przygotuj&quot; w okienku &quot;Przygotowywanie zasobów&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. W **elektorze Przygotowywanie zasobów** wybierz pozycję **Przygotuj**.

> [!NOTE]
> Po przygotowaniu przeniesienia stan grupy zasobów zmieni się na Zainicjuj *oczekiwanie na przeniesienie.* 

 
### <a name="move-the-source-resource-group"></a>Przenoszenie źródłowej grupy zasobów

Rozpocznij przenoszenie źródłowej grupy zasobów, wykonując następujące czynności:

1. W **okienku Między regionami** wybierz grupę zasobów, a następnie wybierz pozycję **Zainicjuj przenoszenie**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Zrzut ekranu przedstawiający przycisk &quot;Zainicjuj przenoszenie&quot; w okienku &quot;Między regionami&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. W **okienku Przenoszenie zasobów** wybierz pozycję **Zainicjuj przenoszenie.** Stan grupy zasobów zmieni się na *Inicjowanie przenoszenia w toku.*   
1. Po zainicjowaniu przenoszenia zostanie utworzona docelowa grupa zasobów na podstawie wygenerowanego szablonu usługi ARM. Stan źródłowej grupy zasobów zmieni się na *Oczekujące na zatwierdzenie przeniesienia.*

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Zrzut ekranu przedstawiający okienko &quot;Przenoszenie zasobów&quot; pokazujące, że stan grupy zasobów został zmieniony na &quot;Oczekiwanie na zatwierdzenie przeniesienia&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Aby zatwierdzić przeniesienie i zakończyć proces, wykonaj następujące czynności:

1. W **okienku Między regionami** wybierz grupę zasobów, a następnie wybierz pozycję **Zat zatwierdzeniu przeniesienia**.
1. W **okienku Przenoszenie zasobów** wybierz pozycję **Zat zatwierdzanie.**

> [!NOTE]
> Po zakończeniu przenoszenia stan źródłowej grupy zasobów zmieni się na *Usuń oczekujące źródło.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Zrzut ekranu przedstawiający źródłową grupę zasobów ze zmienionym stanem na &quot;Oczekiwanie na usunięcie źródła&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Przygotowywanie zasobów do przeniesienia

Teraz, gdy zasoby szyfrowania i źródłową grupę zasobów zostały przeniesione, możesz przygotować się do przeniesienia innych zasobów, których bieżący stan to *Oczekiwanie na przygotowanie.*


1. W **okienku Między regionami** ponownie zweryfikuj przeniesienie i rozwiąż wszelkie problemy.
1. Jeśli chcesz edytować ustawienia docelowe przed rozpoczęciem przenoszenia,  wybierz link w kolumnie Konfiguracja lokalizacji docelowej dla zasobu, a następnie edytuj ustawienia. W przypadku edytowania ustawień docelowej maszyny wirtualnej rozmiar docelowej maszyny wirtualnej nie powinien być mniejszy niż rozmiar źródłowej maszyny wirtualnej.
1. W przypadku zasobów ze *stanem Oczekiwanie na przygotowanie,* które chcesz przenieść, wybierz pozycję **Przygotuj.**
1. W **okienku Przygotowywanie zasobów** wybierz pozycję **Przygotuj**.

    - Podczas przygotowywania agent mobilności Azure Site Recovery jest instalowany na maszynach wirtualnych w celu ich replikacji.
    - Dane maszyny wirtualnej są okresowo replikowane do regionu docelowego. Nie ma to wpływu na źródłową maszynę wirtualną.
    - Przenoszenie zasobów generuje szablony arm dla innych zasobów źródłowych.

> [!NOTE]
> Po przygotowaniu zasobów ich stan zmieni się na *Oczekiwanie na zainicjowanie przeniesienia.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Zrzut ekranu przedstawiający okienko &quot;Przygotowywanie zasobów&quot; z zasobami w stanie &quot;Zainicjuj oczekujące przeniesienie&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Inicjowanie przenoszenia

Teraz, po przygotowaniu zasobów, możesz zainicjować przeniesienie. 

1. W **okienku Między regionami** wybierz zasoby, których stan to *Oczekiwanie* na przeniesienie Inicjowanie, a następnie wybierz pozycję **Zainicjuj przenoszenie.**
1. W **okienku Przenoszenie zasobów** wybierz pozycję **Zainicjuj przenoszenie.**
1. Śledź postęp przenoszenia na pasku powiadomień.

    - W przypadku maszyn wirtualnych repliki maszyn wirtualnych są tworzone w regionie docelowym. Źródłowa maszyna wirtualna jest zamykana i występuje pewien przestój (zazwyczaj minuty).
    - Zasób Mover ponownie tworzy inne zasoby przy użyciu przygotowanych szablonów usługi ARM. Zwykle nie występuje przestój.
    - Po przenoszeniu zasobów ich stan zmieni się na Zatwierdzenie *przeniesienia oczekujące.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Zrzut ekranu przedstawiający listę zasobów ze stanem &quot;Oczekiwanie na przeniesienie zatwierdzenia&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Odrzucasz lub zatwierdzasz?

Po początkowym przenoszeniu możesz zdecydować, czy zatwierdzić przeniesienie, czy odrzucić je. 

- **Odrzuć:** możesz odrzucić przeniesienie, jeśli go testujesz i nie chcesz w rzeczywistości przenosić zasobu źródłowego. Odrzucenie przeniesienia powoduje, że zasób ma stan *Inicjuj oczekiwanie na* przeniesienie.
- **Zatwierdzenie:** zatwierdzenie kończy przenoszenie do regionu docelowego. Po zatwierdzona zasobu źródłowego jego stan zmieni się na Usuń źródło oczekujące *i* możesz zdecydować, czy chcesz go usunąć.


## <a name="discard-the-move"></a>Odrzucanie przeniesienia 

Aby odrzucić przeniesienie, wykonaj następujące czynności:

1. W **okienku Między regionami** wybierz zasoby, których stan to *Oczekujące* na przeniesienie zatwierdzenia, a następnie wybierz pozycję **Odrzuć przeniesienie**.
1. W **okienku Odrzuć przeniesienie** wybierz pozycję **Odrzuć**.
1. Śledź postęp przenoszenia na pasku powiadomień.


> [!NOTE]
> Po odrzuceniu zasobów stan maszyny wirtualnej zmieni się na *Inicjuj oczekujące przeniesienie.*

## <a name="commit-the-move"></a>Zatwierdzanie przeniesienia

Aby ukończyć proces przenoszenia, należy zatwierdzić przeniesienie, wykonując następujące czynności: 

1. W **okienku Między regionami** wybierz zasoby, których stan to *Oczekiwanie* na przeniesienie zatwierdzenia, a następnie wybierz **pozycję Zat zatwierdzanie przeniesienia.**
1. W **okienku Commit resources (Zatwierdzanie zasobów)** wybierz **pozycję Commit (Zat zatwierdzanie).**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Zrzut ekranu przedstawiający listę zasobów do zatwierdzenia zasobów w celu sfinalizowania przeniesienia." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. Śledź postęp zatwierdzania na pasku powiadomień.

> [!NOTE]
> - Po zakończeniu przenoszenia maszyny wirtualne przestają być replikować. Zatwierdzenie nie ma wpływu na źródłową maszynę wirtualną.
> - Proces zatwierdzania nie ma wpływu na źródłowe zasoby sieciowe.
> - Po zakończeniu przenoszenia stan zasobów zmieni się na *Usuń oczekujące źródło.*



## <a name="configure-settings-after-the-move"></a>Konfigurowanie ustawień po przenoszeniu

- Usługa mobilności nie jest automatycznie odinstalowywać z maszyn wirtualnych. Odinstaluj go ręcznie lub pozostaw je, jeśli planujesz ponownie przenieść serwer.
- Po zakończeniu przenoszenia zmodyfikuj reguły kontroli dostępu opartej na rolach (RBAC) platformy Azure.

## <a name="delete-source-resources-after-commit"></a>Usuwanie zasobów źródłowych po zatwierdzeniu

Po zakończeniu przenoszenia możesz opcjonalnie usunąć zasoby w regionie źródłowym. 

1. W **okienku Wiele regionów** wybierz każdy zasób źródłowy, który chcesz usunąć, a następnie wybierz pozycję **Usuń źródło.**
1. W **jęz.** Usuń źródło sprawdź, co zamierzasz usunąć, a następnie w **potwierdź** usunięcie, wpisz **tak.** Akcja jest nieodwracalna, więc sprawdź dokładnie!
1. Po wpisaniu **tak** wybierz pozycję **Usuń źródło.**

> [!NOTE]
>  W portalu przenoszenia zasobów nie można usuwać grup zasobów, magazynów kluczy ani SQL Server zasobów. Musisz usunąć każdą z nich indywidualnie ze strony właściwości dla każdego zasobu.


## <a name="delete-resources-that-you-created-for-the-move"></a>Usuwanie zasobów utworzonych na czas przenoszenia

Po zakończeniu przenoszenia możesz ręcznie usunąć kolekcję przenoszenia i Site Recovery zasobów utworzonych podczas tego procesu.

- Kolekcja przenoszenia jest domyślnie ukryta. Aby to zobaczyć, należy włączyć ukryte zasoby.
- Magazyn pamięci podręcznej ma blokadę, która musi zostać usunięta, zanim będzie można ją usunąć.

Aby usunąć zasoby, wykonaj następujące czynności: 
1. Znajdź zasoby w grupie zasobów ```RegionMoveRG-<sourceregion>-<target-region>``` .
1. Upewnij się, że wszystkie maszyny wirtualne i inne zasoby źródłowe w regionie źródłowym zostały przeniesione lub usunięte. Ten krok zapewnia, że nie ma żadnych oczekujących zasobów korzystających z nich.
1. Usuń zasoby:

    - Przenieś nazwę kolekcji: ```movecollection-<sourceregion>-<target-region>```
    - Nazwa konta magazynu pamięci podręcznej: ```resmovecache<guid>```
    - Nazwa magazynu: ```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Przeniesiono zaszyfrowane maszyny wirtualne platformy Azure i ich zasoby zależne do innego regionu świadczenia usługi Azure.


Następnym krokiem jest przeniesienie baz danych i pul elastycznych Azure SQL do innego regionu.

> [!div class="nextstepaction"]
> [Przenoszenie Azure SQL zasobów](./tutorial-move-region-sql.md)
