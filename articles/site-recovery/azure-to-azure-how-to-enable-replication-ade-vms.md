---
title: Włączanie replikacji zaszyfrowanych maszyn wirtualnych platformy Azure w usłudze Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania replikacji dla maszyn wirtualnych obsługujących szyfrowanie dysków platformy Azure z jednego regionu platformy Azure do drugiego przy użyciu usługi Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 2bbb02df782439d934e96e7c16f28b9c11cc01fe
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408633"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replikowanie maszyn wirtualnych z obsługą szyfrowania dysków platformy Azure do innego regionu platformy Azure

W tym artykule opisano sposób replikowania maszyn wirtualnych platformy Azure z włączoną funkcją szyfrowania dysków Azure (ADE), z jednego regionu platformy Azure do drugiego.

>[!NOTE]
> Usługa Site Recovery obsługuje obecnie usługę ADE, z usługą Azure Active Directory (AAD) i bez niej dla maszyn wirtualnych z systemami operacyjnymi Windows. W przypadku systemów operacyjnych Linux obsługujemy tylko ADE bez AAD. Ponadto w przypadku komputerów z systemem ADE 1.1 (bez usługi AAD) maszyny wirtualne muszą używać dysków zarządzanych. Maszyny wirtualne z dyskami niezarządzanymi nie są obsługiwane. W przypadku przełączenia z usługi ADE 0.1 (z aad) na 1.1 należy wyłączyć replikację i włączyć replikację maszyny Wirtualnej po włączeniu wersji 1.1.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a>Wymagane uprawnienia użytkownika
Usługa Site Recovery wymaga, aby użytkownik miał uprawnienia do tworzenia magazynu kluczy w regionie docelowym i kopiowania kluczy z magazynu kluczy regionu źródłowego do magazynu kluczy regionu docelowego.

Aby włączyć replikację maszyn wirtualnych z włączoną obsługą szyfrowania dysku z witryny Azure portal, użytkownik potrzebuje następujących uprawnień zarówno do magazynów kluczy **regionu źródłowego,** jak i docelowego regionu.

- Uprawnienia magazynu kluczy
    - Lista, Tworzenie i Pobierz
    
- Uprawnienia tajnego magazynu kluczy
    - Tajne operacje zarządzania
        - Pobierz, lista i ustaw
    
- Uprawnienia kluczy magazynu kluczy (wymagane tylko wtedy, gdy maszyny wirtualne używają klucza szyfrowania klucza do szyfrowania kluczy szyfrowania kluczy dysków)
    - Kluczowe operacje zarządzania
        - Pobierz, wystaw i utwórz
    - Operacje kryptograficzne
        - Odszyfrowywanie i szyfrowanie

Aby zarządzać uprawnieniami, przejdź do zasobu magazynu kluczy w portalu. Dodaj wymagane uprawnienia dla użytkownika. W poniższym przykładzie pokazano, jak włączyć uprawnienia do magazynu kluczy *ContosoWeb2Keyvault*, który znajduje się w regionie źródłowym.

1. Przejdź do **ekranu główne** > **kluczy** > **ContosoWeb2KeyVault > zasady dostępu**.

   ![Okno uprawnień magazynu kluczy](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Widać, że nie ma żadnych uprawnień użytkownika. Wybierz **pozycję Dodaj nowy**. Wprowadź informacje o użytkowniku i uprawnieniach.

   ![Uprawnienia keyvault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Jeśli użytkownik, który włącza odzyskiwanie po awarii (DR) nie ma uprawnień do kopiowania kluczy, administrator zabezpieczeń, który ma odpowiednie uprawnienia, może użyć następującego skryptu do skopiowania kluczy tajnych i kluczy szyfrowania do regionu docelowego.

Aby rozwiązać problemy z uprawnieniami, zapoznaj się [z problemami z uprawnieniami magazynu kluczy](#trusted-root-certificates-error-code-151066) w dalszej części tego artykułu.

>[!NOTE]
>Aby włączyć replikację maszyn wirtualnych z włączoną obsługą szyfrowania dysku z portalu, potrzebujesz co najmniej uprawnień "Lista" do magazynów kluczy, wpisów tajnych i kluczy.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Kopiowanie kluczy szyfrowania dysku do regionu odzyskiwania po awarii przy użyciu skryptu programu PowerShell

1. [Otwórz nieprzetworzony kod skryptu "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Skopiuj skrypt do pliku i **nazwij go Copy-keys.ps1**.
3. Otwórz aplikację programu Windows PowerShell i przejdź do folderu, w którym został zapisany plik.
4. Wykonaj polecenie Copy-keys.ps1.
5. Podaj poświadczenia platformy Azure, aby się zalogować.
6. Wybierz **subskrypcję platformy Azure** maszyn wirtualnych.
7. Poczekaj na załadowanie grup zasobów, a następnie wybierz **grupę zasobów** maszyn wirtualnych.
8. Wybierz maszyny wirtualne z listy, która jest wyświetlana. Na liście znajdują się tylko maszyny wirtualne, które są włączone do szyfrowania dysku.
9. Wybierz **lokalizację docelową**.

    - **Magazyny kluczy szyfrowania dysków**
    - **Magazyny kluczy szyfrowania kluczy**

   Domyślnie usługa Site Recovery tworzy nową przechowalnię kluczy w regionie docelowym. Nazwa magazynu ma sufiks "asr", który jest oparty na źródłowych kluczy szyfrowania dysku maszyny Wirtualnej. Jeśli magazyn kluczy już istnieje, który został utworzony przez odzysk witryny, jest ponownie. W razie potrzeby wybierz inną przechowalnię kluczy z listy.

## <a name="enable-replication"></a>Włączanie replikacji

W tym przykładzie podstawowym regionem platformy Azure jest Azja Wschodnia, a region pomocniczy to Azja Południowo-Wschodnia.

1. W przechowalni wybierz **+Replikuj**.
2. Zwróć uwagę na następujące pola.
    - **Źródło**: Punkt pochodzenia maszyn wirtualnych, który w tym przypadku jest **Azure**.
    - **Lokalizacja źródła:** region platformy Azure, w którym chcesz chronić maszyny wirtualne. W tym przykładzie lokalizacją źródłą jest "Azja Wschodnia".
    - **Model wdrażania:** model wdrażania platformy Azure maszyn źródłowych.
    - **Subskrypcja źródłona:** subskrypcja, do której należą źródłowe maszyny wirtualne. Może to być dowolna subskrypcja, która znajduje się w tej samej dzierżawie usługi Azure Active Directory co magazyn usług odzyskiwania.
    - **Grupa zasobów:** Grupa zasobów, do której należą źródłowe maszyny wirtualne. Wszystkie maszyny wirtualne w wybranej grupie zasobów są wymienione w celu ochrony w następnym kroku.

3. W **maszynach wirtualnych** > **Wybierz maszyny wirtualne**wybierz każdą maszynę wirtualną, którą chcesz replikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie wybierz przycisk **OK**.

4. W **ustawieniach**można skonfigurować następujące ustawienia witryny docelowej.

    - **Lokalizacja docelowa:** lokalizacja, w której będą replikowane dane źródłowej maszyny wirtualnej. Odzysk lokacji zawiera listę odpowiednich regionów docelowych na podstawie lokalizacji wybranego komputera. Zaleca się korzystanie z tej samej lokalizacji co lokalizacja magazynu usług odzyskiwania.
    - **Subskrypcja docelowa:** subskrypcja docelowa używana do odzyskiwania po awarii. Domyślnie subskrypcja docelowa jest taka sama jak subskrypcji źródłowej.
    - **Docelowa grupa zasobów:** grupa zasobów, do której należą wszystkie zreplikowane maszyny wirtualne. Domyślnie program Site Recovery tworzy nową grupę zasobów w regionie docelowym. Nazwa otrzymuje sufiks "asr". Jeśli grupa zasobów już istnieje, który został utworzony przez usługę Azure Site Recovery, jest ponownie. Można również dostosować go, jak pokazano w poniższej sekcji. Lokalizacja docelowej grupy zasobów może być dowolny region platformy Azure z wyjątkiem regionu, w którym są hostowane źródłowe maszyny wirtualne.
    - **Docelowa sieć wirtualna:** Domyślnie usługa Site Recovery tworzy nową sieć wirtualną w regionie docelowym. Nazwa otrzymuje sufiks "asr". Jest mapowany do sieci źródłowej i używany do wszelkiej przyszłej ochrony. [Dowiedz się więcej](site-recovery-network-mapping-azure-to-azure.md) o mapowaniu sieci.
    - **Docelowe konta magazynu (jeśli źródłowa maszyna wirtualna nie używa dysków zarządzanych)**: Domyślnie usługa Site Recovery tworzy nowe konto magazynu docelowego, naśladując konfigurację źródłowego magazynu maszyn wirtualnych. Jeśli konto magazynu już istnieje, jest ponownie używane.
    - **Replika dysków zarządzanych (jeśli źródłowa maszyna wirtualna używa dysków zarządzanych)**: Usługa Site Recovery tworzy nowe dyski zarządzane repliki w regionie docelowym w celu odzwierciedlenia dysków zarządzanych źródłowej maszyny Wirtualnej tego samego typu magazynu (standardowego lub premium) co dyski zarządzane źródłowej maszyny Wirtualnej.
    - **Konta magazynu pamięci podręcznej:** Usługa Site Recovery wymaga dodatkowego konta magazynu o nazwie *magazyn pamięci podręcznej* w regionie źródłowym. Wszystkie zmiany na źródłowych maszynach wirtualnych są śledzone i wysyłane do konta magazynu pamięci podręcznej. Są one następnie replikowane do lokalizacji docelowej.
    - **Zestaw dostępności:** Domyślnie usługa Site Recovery tworzy nowy zestaw dostępności w regionie docelowym. Nazwa ma sufiks "asr". Jeśli zestaw dostępności utworzony przez odzysk witryny już istnieje, jest ponownie on akces.
    - **Magazyny kluczy szyfrowania dysku:** Domyślnie usługa Site Recovery tworzy nową przechowalnię kluczy w regionie docelowym. Ma sufiks "asr", który jest oparty na źródłowych kluczy szyfrowania dysku maszyny Wirtualnej. Jeśli magazyn kluczy, który został utworzony przez usługę Azure Site Recovery już istnieje, jest ponownie.
    - **Magazyny kluczy szyfrowania kluczy:** Domyślnie usługa Site Recovery tworzy nową przechowalnię kluczy w regionie docelowym. Nazwa ma sufiks "asr", który jest oparty na źródłowych kluczy szyfrowania klucza maszyny Wirtualnej. Jeśli magazyn kluczy utworzony przez usługę Azure Site Recovery już istnieje, jest ponownie używany.
    - **Zasady replikacji:** Definiuje ustawienia historii przechowywania punktów odzyskiwania i częstotliwości migawek spójnych z aplikacją. Domyślnie usługa Site Recovery tworzy nową zasadę replikacji z domyślnymi ustawieniami *24 godzin* przechowywania punktów odzyskiwania i *60 minut* dla częstotliwości migawek spójnych z aplikacją.

## <a name="customize-target-resources"></a>Dostosowywanie zasobów docelowych

Wykonaj następujące kroki, aby zmodyfikować domyślne ustawienia docelowe usługi Site Recovery.

1. Wybierz **pozycję Dostosuj** obok opcji "Subskrypcja docelowa", aby zmodyfikować domyślną subskrypcję docelową. Wybierz subskrypcję z listy subskrypcji, które są dostępne w dzierżawie usługi Azure AD.

2. Wybierz **pozycję Dostosuj** obok pozycji "Grupy zasobów, sieci, magazynu i dostępności", aby zmodyfikować następujące ustawienia domyślne:
    - W przypadku **docelowej grupy zasobów**wybierz grupę zasobów z listy grup zasobów w lokalizacji docelowej subskrypcji.
    - W przypadku **docelowej sieci wirtualnej**wybierz sieć z listy sieci wirtualnych w lokalizacji docelowej.
    - W przypadku **zestawu dostępności**można dodać ustawienia zestawu dostępności do maszyny Wirtualnej, jeśli są one częścią zestawu dostępności w regionie źródłowym.
    - W przypadku **kont magazynu docelowego**wybierz konto, którego chcesz użyć.

2. Wybierz **pozycję Dostosuj** obok pozycji "Ustawienia szyfrowania", aby zmodyfikować następujące ustawienia domyślne:
   - W przypadku **magazynu kluczy szyfrowania dysku docelowego**wybierz magazyn kluczy szyfrowania dysku docelowego z listy magazynów kluczy w lokalizacji docelowej subskrypcji.
   - W przypadku **magazynu kluczy szyfrowania klucza docelowego**wybierz magazyn kluczy szyfrowania klucza docelowego z listy magazynów kluczy w lokalizacji docelowej subskrypcji.

3. Wybierz **pozycję Utwórz zasób docelowy** > **Włącz replikację**.
4. Po włączeniu maszyn wirtualnych do replikacji można sprawdzić stan kondycji maszyn wirtualnych w obszarze **Elementy replikowane**.

>[!NOTE]
>Podczas replikacji początkowej stan może zająć trochę czasu, aby odświeżyć, bez widocznego postępu. Kliknij **przycisk Odśwież,** aby uzyskać najnowszy stan.

## <a name="update-target-vm-encryption-settings"></a>Aktualizowanie docelowych ustawień szyfrowania maszyn wirtualnych
W następujących scenariuszach musisz zaktualizować docelowe ustawienia szyfrowania maszyny Wirtualnej:
  - Włączona replikacja odzyskiwania lokacji na maszynie wirtualnej. Później włączono szyfrowanie dysku na źródłowej maszynie wirtualnej.
  - Włączona replikacja odzyskiwania lokacji na maszynie wirtualnej. Później zmieniono klucz szyfrowania dysku lub klucz szyfrowania klucza na źródłowej maszynie wirtualnej.

Za pomocą [skryptu](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) można skopiować klucze szyfrowania do regionu docelowego, a następnie zaktualizować docelowe ustawienia szyfrowania w **magazynie** > *replikowanych elementów* > usługi odzyskiwania**Właściwości** > **obliczeniowe i sieć**.

![Okno dialogowe Aktualizowanie ustawień ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Rozwiązywanie problemów z uprawnieniami magazynu kluczy podczas replikacji maszyny Wirtualnej platformy Azure do platformy Azure

Usługa Azure Site Recovery wymaga co najmniej uprawnienia do odczytu w magazynie kluczy regionu źródłowego i uprawnienia do zapisu w magazynie kluczy regionu docelowego, aby odczytać klucz tajny i skopiować go do magazynu kluczy regionu docelowego. 

**Przyczyna 1:** Nie masz uprawnień "GET" w **regionie źródłowym Magazyn kluczy** do odczytu kluczy. </br>
**Jak naprawić:** Niezależnie od tego, czy jesteś administratorem subskrypcji, czy nie, ważne jest, aby uzyskać uprawnienia do magazynu kluczy.

1. Przejdź do regionu źródłowego Magazyn kluczy, który w tym przykładzie jest "ContososourceKeyvault" > **zasady programu Access** 
2. W obszarze **Wybierz podmiot zabezpieczeń** dodajdradmin@contoso.comnazwę użytkownika na przykład: " "
3. W obszarze **Uprawnienia klucz** wybierz GET 
4. W obszarze **Tajne uprawnienie** wybierz POZYCJĘ POBIERZ 
5. Zapisywanie zasad dostępu

**Przyczyna 2:** Nie masz wymaganych uprawnień do **magazynu kluczy regionu docelowego** do zapisywania kluczy. </br>

*Na przykład:* Spróbuj replikować maszynę wirtualną, która ma magazyn kluczy *ContososourceKeyvault* w regionie źródłowym.
Masz wszystkie uprawnienia do magazynu kluczy regionu źródłowego. Ale podczas ochrony, należy wybrać już utworzony magazyn kluczy ContosotargetKeyvault, który nie ma uprawnień. Wystąpi błąd.

Uprawnienie wymagane dla [docelowego magazynu kluczy](#required-user-permissions)

**Jak naprawić:** Przejdź do **home** > **keyvaults** > **ContosotargetKeyvault** > **Access zasady** i dodać odpowiednie uprawnienia.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu pracy awaryjnej testu.
