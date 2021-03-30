---
title: Włącz replikację szyfrowanych maszyn wirtualnych platformy Azure w Azure Site Recovery
description: W tym artykule opisano sposób konfigurowania replikacji dla maszyn wirtualnych z włączoną funkcją Azure Disk Encryption z jednego regionu świadczenia usługi Azure do innego przy użyciu Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: fa4d61599e102f9a2580e704ee7a02486067daa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135795"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replikowanie maszyn wirtualnych z obsługą usługi Azure Disk Encryption w innym regionie platformy Azure

W tym artykule opisano sposób replikowania maszyn wirtualnych platformy Azure z włączonym Azure Disk Encryption (ADE) z jednego regionu świadczenia usługi Azure do innego.

>[!NOTE]
> Site Recovery obecnie obsługuje ADE, z i bez Azure Active Directory (AAD) dla maszyn wirtualnych z systemami operacyjnymi Windows. W przypadku systemów operacyjnych Linux obsługujemy tylko ADE bez usługi AAD. Ponadto w przypadku maszyn z uruchomionym programem ADE 1,1 (bez usługi AAD) maszyny wirtualne muszą używać dysków zarządzanych. Maszyny wirtualne z dyskami niezarządzanymi nie są obsługiwane. Jeśli przełączysz się z programu ADE 0,1 (z usługą AAD) do 1,1, musisz wyłączyć replikację i włączyć replikację dla maszyny wirtualnej po włączeniu opcji 1,1.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a> Wymagane uprawnienia użytkownika
Site Recovery wymaga, aby użytkownik miał uprawnienia do tworzenia magazynu kluczy w regionie docelowym i kopiowania kluczy z magazynu kluczy regionów źródłowych do magazynu kluczy regionu docelowego.

Aby włączyć replikację maszyn wirtualnych z włączoną funkcją szyfrowania dysku z Azure Portal, użytkownik musi mieć następujące uprawnienia zarówno w **regionie źródłowym, jak i** w magazynach kluczy docelowych.

- Uprawnienia magazynu kluczy
    - List, Create i Get
    
- Uprawnienia klucza tajnego magazynu kluczy
    - Operacje zarządzania kluczami tajnymi
        - Pobierz, Wyświetl i ustaw
    
- Uprawnienia klucza magazynu kluczy (wymagane tylko wtedy, gdy maszyny wirtualne używają klucza szyfrowania klucza do szyfrowania kluczy szyfrowania dysku)
    - Operacje zarządzania kluczami
        - Pobierz, Wyświetl i Utwórz
    - Operacje kryptograficzne
        - Odszyfruj i Szyfruj

Aby zarządzać uprawnieniami, przejdź do zasobu magazynu kluczy w portalu. Dodaj wymagane uprawnienia dla użytkownika. Poniższy przykład pokazuje, jak włączyć uprawnienia do magazynu kluczy *ContosoWeb2Keyvault*, który znajduje się w regionie źródłowym.

1. Przejdź do **głównych**  >  **magazynów** kluczy  >  **ContosoWeb2KeyVault > zasad dostępu**.

   ![Okno uprawnień magazynu kluczy](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Możesz zobaczyć, że nie ma żadnych uprawnień użytkownika. Wybierz pozycję **Dodaj nowy**. Wprowadź informacje o użytkowniku i uprawnieniach.

   ![Uprawnienia magazynu kluczy](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Jeśli użytkownik, który włączył odzyskiwanie po awarii (DR) nie ma uprawnień do kopiowania kluczy, administrator zabezpieczeń, który ma odpowiednie uprawnienia, może użyć następującego skryptu w celu skopiowania kluczy tajnych szyfrowania i klucza do regionu docelowego.

Rozwiązywanie problemów z uprawnieniami można znaleźć [w dalszej](#trusted-root-certificates-error-code-151066) części tego artykułu.

>[!NOTE]
>Aby włączyć replikację maszyn wirtualnych z włączoną funkcją szyfrowania dysków z portalu, musisz mieć co najmniej uprawnienia "list" dotyczące magazynów kluczy, wpisów tajnych i kluczy.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Kopiowanie kluczy szyfrowania dysku do regionu DR przy użyciu skryptu programu PowerShell

1. [Otwórz kod skryptu RAW "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Skopiuj skrypt do pliku i nadaj mu nazwę **Copy-keys.ps1**.
3. Otwórz aplikację Windows PowerShell i przejdź do folderu, w którym zapisano plik.
4. Wykonaj Copy-keys.ps1.
5. Podaj poświadczenia platformy Azure, aby się zalogować.
6. Wybierz **subskrypcję platformy Azure** dla maszyn wirtualnych.
7. Zaczekaj na załadowanie grup zasobów, a następnie wybierz **grupę zasobów** maszyn wirtualnych.
8. Wybierz Maszyny wirtualne z wyświetlonej listy. Na liście znajdują się tylko maszyny wirtualne, na których włączono szyfrowanie dysków.
9. Wybierz **lokalizację docelową**.

    - **Magazyny kluczy szyfrowania dysków**
    - **Magazyny kluczy szyfrowania kluczy**

   Domyślnie Site Recovery tworzy nowy magazyn kluczy w regionie docelowym. Nazwa magazynu ma sufiks "ASR" oparty na kluczach szyfrowania źródłowych dysków maszyny wirtualnej. Jeśli magazyn kluczy już istnieje, który został utworzony przez Site Recovery, jest ponownie używany. W razie potrzeby wybierz inny magazyn kluczy z listy.

## <a name="enable-replication"></a>Włączanie replikacji

W tym przykładzie głównym regionem świadczenia usługi Azure jest Azja Wschodnia, a region pomocniczy to południowo Azja Wschodnia.

1. W magazynie wybierz pozycję **+ Replikuj**.
2. Zwróć uwagę na następujące pola.
    - **Źródło**: punkt pochodzenia maszyn wirtualnych, który jest w tym przypadku **platformą Azure**.
    - **Lokalizacja źródłowa**: region świadczenia usługi Azure, w którym chcesz chronić maszyny wirtualne. W tym przykładzie lokalizacja źródłowa to "Azja Wschodnia".
    - **Model wdrażania**: model wdrażania platformy Azure dla maszyn źródłowych.
    - **Subskrypcja źródłowa**: subskrypcja, do której należą źródłowe maszyny wirtualne. Może to być dowolna subskrypcja, która znajduje się w tej samej dzierżawie Azure Active Directory, co magazyn usługi Recovery Services.
    - **Grupa zasobów**: Grupa zasobów, do której należą źródłowe maszyny wirtualne. W następnym kroku zostaną wyświetlone wszystkie maszyny wirtualne w wybranej grupie zasobów do ochrony.

3. W obszarze **Virtual Machines**  >  **Wybierz pozycję Maszyny wirtualne** zaznacz każdą maszynę wirtualną, którą chcesz zreplikować. Możesz wybrać tylko te maszyny, dla których można włączyć replikację. Następnie wybierz przycisk **OK**.

4. W obszarze **Ustawienia** można skonfigurować następujące ustawienia lokacji docelowej.

    - **Lokalizacja docelowa**: lokalizacja, w której będą replikowane dane źródłowej maszyny wirtualnej. Site Recovery zawiera listę odpowiednich regionów docelowych na podstawie lokalizacji wybranej maszyny. Zalecamy używanie tej samej lokalizacji co lokalizacja magazynu Recovery Services.
    - **Subskrypcja docelowa**: subskrypcja docelowa używana na potrzeby odzyskiwania po awarii. Domyślnie subskrypcja docelowa jest taka sama jak w przypadku subskrypcji źródłowej.
    - **Docelowa Grupa zasobów**: Grupa zasobów, do której należą wszystkie zreplikowane maszyny wirtualne. Domyślnie Site Recovery tworzy nową grupę zasobów w regionie docelowym. Nazwa pobiera sufiks "ASR". Jeśli grupa zasobów już istnieje, która została utworzona przez Azure Site Recovery, zostanie ponownie użyta. Możesz również wybrać opcję dostosowywania, jak pokazano w poniższej sekcji. Lokalizacją docelowej grupy zasobów może być dowolny region świadczenia usługi Azure, z wyjątkiem regionu, w którym są hostowane źródłowe maszyny wirtualne.
    - **Docelowa sieć wirtualna**: domyślnie Site Recovery tworzy nową sieć wirtualną w regionie docelowym. Nazwa pobiera sufiks "ASR". Jest on mapowany do sieci źródłowej i używany do ochrony w przyszłości. [Dowiedz się więcej](./azure-to-azure-network-mapping.md) na temat mapowania sieci.
    - **Docelowe konta magazynu (jeśli źródłowa maszyna wirtualna nie używa dysków zarządzanych)**: domyślnie Site Recovery tworzy nowe docelowe konto magazynu, naśladując konfigurację magazynu ŹRÓDŁOWEJ maszyny wirtualnej. Jeśli konto magazynu już istnieje, jest ponownie używane.
    - **Dyski zarządzane repliki (jeśli źródłowa maszyna wirtualna korzysta z dysków zarządzanych)**: Site Recovery tworzy nowe dyski zarządzane repliki w regionie docelowym, aby dublować dyski zarządzane ŹRÓDŁOWEJ maszyny wirtualnej tego samego typu magazynu (w warstwie Standardowa lub Premium) jako dyski zarządzane ŹRÓDŁOWEJ maszyny wirtualnej.
    - **Konta magazynu pamięci podręcznej**: Site Recovery potrzebuje dodatkowego konta magazynu o nazwie *Magazyn pamięci podręcznej* w regionie źródłowym. Wszystkie zmiany na źródłowych maszynach wirtualnych są śledzone i wysyłane do konta magazynu pamięci podręcznej. Są one następnie replikowane do lokalizacji docelowej.
    - **Zestaw dostępności**: domyślnie Site Recovery tworzy nowy zestaw dostępności w regionie docelowym. Nazwa ma sufiks "ASR". Jeśli zestaw dostępności, który został utworzony przez Site Recovery już istnieje, jest ponownie używany.
    - **Magazyny kluczy szyfrowania dysku**: domyślnie Site Recovery tworzy nowy magazyn kluczy w regionie docelowym. Ma sufiks "ASR" oparty na kluczach szyfrowania źródłowych dysków maszyny wirtualnej. Jeśli magazyn kluczy utworzony przez Azure Site Recovery już istnieje, jest ponownie używany.
    - **Magazyny kluczy szyfrowania kluczy**: domyślnie Site Recovery tworzy nowy magazyn kluczy w regionie docelowym. Nazwa ma sufiks "ASR" oparty na kluczach szyfrowania źródłowych maszyn wirtualnych. Jeśli magazyn kluczy utworzony przez Azure Site Recovery już istnieje, jest ponownie używany.
    - **Zasady replikacji**: określa ustawienia dla historii przechowywania punktów odzyskiwania i częstotliwości migawek spójnych na poziomie aplikacji. Domyślnie program Site Recovery tworzy nowe zasady replikacji z domyślnymi ustawieniami *24 godzin* w przypadku przechowywania punktów odzyskiwania i *60 minut* w przypadku częstotliwości migawek spójnych na poziomie aplikacji.

## <a name="customize-target-resources"></a>Dostosowywanie zasobów docelowych

Wykonaj następujące kroki, aby zmodyfikować domyślne ustawienia obiektu docelowego Site Recovery.

1. Wybierz pozycję **Dostosuj** obok pozycji "subskrypcja docelowa", aby zmodyfikować domyślną subskrypcję docelową. Wybierz subskrypcję z listy subskrypcji dostępnych w dzierżawie usługi Azure AD.

2. Wybierz pozycję **Dostosuj** obok pozycji "Grupa zasobów, Sieć, magazyn i zestawy dostępności", aby zmodyfikować następujące ustawienia domyślne:
    - Dla **docelowej grupy zasobów** wybierz grupę zasobów z listy grup zasobów w lokalizacji docelowej subskrypcji.
    - W polu **docelowa sieć wirtualna** wybierz sieć z listy sieci wirtualnych w lokalizacji docelowej.
    - W przypadku **zestawu dostępności** można dodać ustawienia zestawu dostępności do maszyny wirtualnej, jeśli są one częścią zestawu dostępności w regionie źródłowym.
    - W przypadku **docelowych kont magazynu** wybierz konto, które ma być używane.

2. Wybierz pozycję **Dostosuj** obok pozycji "ustawienia szyfrowania", aby zmodyfikować następujące ustawienia domyślne:
   - W przypadku **magazynu kluczy szyfrowania dysku docelowego** wybierz magazyn klucza szyfrowania dysku docelowego z listy magazynów kluczy w lokalizacji docelowej subskrypcji.
   - W przypadku **magazynu kluczy szyfrowania klucza docelowego** Wybierz docelowy Magazyn kluczy szyfrowania z listy magazynów kluczy w lokalizacji docelowej subskrypcji.

3. Wybierz pozycję **Utwórz zasób docelowy**  >  **Włącz replikację**.
4. Po włączeniu maszyn wirtualnych na potrzeby replikacji można sprawdzić stan kondycji maszyn wirtualnych w obszarze **zreplikowane elementy**.

>[!NOTE]
>Podczas replikacji początkowej stan może zająć trochę czasu, bez pozornego postępu. Kliknij przycisk **Odśwież**  , aby pobrać najnowszy stan.

## <a name="update-target-vm-encryption-settings"></a>Zaktualizuj ustawienia szyfrowania docelowej maszyny wirtualnej
W następujących scenariuszach wymagane jest zaktualizowanie ustawień szyfrowania docelowej maszyny wirtualnej:
  - Włączono Site Recovery replikację na maszynie wirtualnej. Później można było włączyć szyfrowanie dysków na źródłowej maszynie wirtualnej.
  - Włączono Site Recovery replikację na maszynie wirtualnej. Później zmieniono klucz szyfrowania dysku lub klucz szyfrowania klucza na źródłowej maszynie wirtualnej.

Możesz użyć [skryptu](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) , aby skopiować klucze szyfrowania do regionu docelowego, a następnie zaktualizować docelowe ustawienia szyfrowania w obszarze właściwości zreplikowanego elementu **magazynu usługi Recovery Services**  >    >    >  .

![Okno dialogowe Aktualizowanie ustawień ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Rozwiązywanie problemów z uprawnieniami magazynu kluczy podczas replikacji maszyny wirtualnej platformy Azure na platformę Azure

Azure Site Recovery wymaga co najmniej uprawnienia do odczytu w magazynie kluczy w regionie źródłowym i uprawnienia do zapisu w magazynie kluczy w regionie docelowym w celu odczytu wpisu tajnego i skopiowania go do magazynu kluczy w regionie docelowym. 

**Przyczyna 1:** Nie masz uprawnienia "GET" w **magazynie kluczy obszaru źródłowego** , aby odczytać klucze. </br>
**Jak naprawić:** Bez względu na to, czy jesteś administratorem subskrypcji, czy nie, ważne jest, aby uzyskać uprawnienia do magazynu kluczy.

1. Przejdź do magazynu kluczy źródłowych regionów, w tym przykładzie jest to "ContososourceKeyvault" > **zasad dostępu** 
2. W obszarze **Wybierz podmiot zabezpieczeń** Dodaj nazwę użytkownika na przykład: " dradmin@contoso.com "
3. W obszarze **uprawnienia klucza** wybierz pozycję Pobierz. 
4. W obszarze **uprawnienia klucza tajnego** wybierz pozycję Pobierz 
5. Zapisz zasady dostępu

**Przyczyna 2:** Nie masz wymaganego uprawnienia do **magazynu kluczy w regionie docelowym** , aby zapisać klucze. </br>

*Na przykład*: próbujesz replikować maszynę wirtualną, która ma *ContososourceKeyvault* magazynu kluczy w regionie źródłowym.
Masz wszystkie uprawnienia do magazynu kluczy w regionie źródłowym. Ale w trakcie ochrony wybierasz już utworzony ContosotargetKeyvault magazynu kluczy, który nie ma uprawnień. Wystąpił błąd.

Wymagane uprawnienie w [magazynie kluczy docelowych](#required-user-permissions)

**Jak naprawić:** Przejdź do pozycji **Główne**  >  **magazyny** kluczy  >  **ContosotargetKeyvault**  >  **zasady dostępu** i Dodaj odpowiednie uprawnienia.

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej](site-recovery-test-failover-to-azure.md) o uruchamianiu testowej pracy w trybie failover.
