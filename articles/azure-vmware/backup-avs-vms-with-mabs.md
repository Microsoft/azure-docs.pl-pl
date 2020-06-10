---
title: Tworzenie kopii zapasowych maszyn wirtualnych rozwiązań VMware (Automatyczna synchronizacja) na platformie Azure przy użyciu serwera Microsoft Azure Backup
description: Skonfiguruj środowisko Azure VMware Solution (Automatyczna synchronizacja), aby tworzyć kopie zapasowe maszyn wirtualnych przy użyciu serwera Microsoft Azure Backup.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 16941753d614bbfacbc63e4ae518986901153662
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612761"
---
# <a name="back-up-avs-vms-with-microsoft-azure-backup-server"></a>Tworzenie kopii zapasowych maszyn wirtualnych automatycznej synchronizacji przy użyciu serwera Microsoft Azure Backup

W tym artykule opisano procedury tworzenia kopii zapasowych maszyn wirtualnych programu VMware działających w ramach rozwiązania Azure VMware (Automatyczna synchronizacja) przy użyciu Azure Backup Server. Przed rozpoczęciem upewnij się, że dokładnie przejdziesz na [serwer Microsoft Azure Backup konfiguracji w celu uzyskania automatycznej synchronizacji](set-up-mabs-for-avs.md). 

Następnie przeprowadzimy przez wszystkie niezbędne procedury, aby:

> [!div class="checklist"] 
> * Skonfiguruj bezpieczny kanał, aby Azure Backup Server mógł komunikować się z serwerami VMware za pośrednictwem protokołu HTTPS. 
> * Dodaj poświadczenia konta do Azure Backup Server. 
> * Dodaj program vCenter do Azure Backup Server. 
> * Skonfiguruj grupę ochrony zawierającą maszyny wirtualne VMware, dla których chcesz utworzyć kopię zapasową, określ ustawienia kopii zapasowej i Zaplanuj kopię zapasową.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Utwórz bezpieczne połączenie z vCenter Server

Domyślnie Azure Backup Server komunikuje się z serwerami VMware za pośrednictwem protokołu HTTPS. Aby skonfigurować połączenie HTTPS, Pobierz certyfikat urzędu certyfikacji VMware i zaimportuj go na Azure Backup Server.

### <a name="set-up-the-certificate"></a>Konfigurowanie certyfikatu

1. W przeglądarce na Azure Backup Server wprowadź adres URL klienta sieci Web vSphere.

   > [!NOTE] 
   > Jeśli strona programu VMware Wprowadzenie nie zostanie wyświetlona, sprawdź ustawienia połączenia i serwera proxy przeglądarki i spróbuj ponownie.

1. Na stronie Wprowadzenie VMware kliknij pozycję **Pobierz certyfikaty zaufanych głównych urzędów certyfikacji**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="Klient sieci Web vSphere":::

1. Zapisz plik **Download. zip** na maszynie Azure Backup Server, a następnie wyodrębnij jego zawartość do folderu **Certyfikaty** , który zawiera:

   - Plik certyfikatu głównego z rozszerzeniem rozpoczynającym się od numerowanej sekwencji takiej jak. 0 i. 1.

   - Plik listy CRL ma rozszerzenie zaczynające się od sekwencji like. r0 lub. R1.

1. W folderze **Certyfikaty** kliknij prawym przyciskiem myszy plik certyfikatu głównego, a następnie wybierz polecenie **Zmień nazwę** , aby zmienić rozszerzenie na **. CRT**.

   Ikona pliku zmieni się na taki, który reprezentuje certyfikat główny.

1. Kliknij prawym przyciskiem myszy certyfikat główny, a następnie wybierz pozycję **Zainstaluj certyfikat**.

1. W **Kreatorze importu certyfikatów**wybierz pozycję **komputer lokalny** jako miejsce docelowe certyfikatu, a następnie kliknij przycisk **dalej**.

   ![Kreator — Zapraszamy!](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Jeśli zostanie wyświetlony monit, potwierdź, że chcesz zezwolić na wprowadzanie zmian na komputerze.    

1. Wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie kliknij przycisk **Przeglądaj** , aby wybrać magazyn certyfikatów.

   ![Magazyn certyfikatów](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Wybierz pozycję **Zaufane główne** urzędy certyfikacji jako folder docelowy, a następnie kliknij przycisk **OK**.

1. Przejrzyj ustawienia i kliknij przycisk **Zakończ** , aby rozpocząć Importowanie certyfikatu.

   ![Sprawdź, czy certyfikat znajduje się w odpowiednim folderze](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Po potwierdzeniu importu certyfikatu Zaloguj się do vCenter Server, aby upewnić się, że połączenie jest bezpieczne.

### <a name="enable-tls-12-on-azure-backup-server"></a>Włącz protokół TLS 1,2 na Azure Backup Server

W przypadku oprogramowania VMWare 6,7 jako protokołu komunikacyjnego włączono protokół TLS. 

1. Skopiuj poniższe ustawienia rejestru i wklej je do Notatnika, a następnie Zapisz plik jako TLS. REG bez rozszerzenia. txt.

   ```text
   
   Windows Registry Editor Version 5.00
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v2.0.50727]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   
      "SystemDefaultTlsVersions"=dword:00000001
   
      "SchUseStrongCrypto"=dword:00000001
   
   ```

1. Kliknij prawym przyciskiem myszy protokół TLS. Plik REG i wybierz pozycję **Scal** lub **Otwórz** , aby dodać ustawienia do rejestru.

## <a name="add-the-provisioning-ip-address-for-avs-esxi-hosts-on-azure-backup-server"></a>Dodaj adres IP aprowizacji na potrzeby automatycznej synchronizacji hostów ESXi na Azure Backup Server

W trakcie okresu zapoznawczego, automatyczna synchronizacja nie rozpoznaje hosta ESX z maszyny wirtualnej wdrożonej w sieci wirtualnej. Należy wykonać dodatkowe czynności, aby dodać wpis do pliku hosta na Azure Backup Server maszynie wirtualnej.

### <a name="identify-ip-address-for-esxi-hosts"></a>Identyfikuj adresy IP dla hostów ESXi

1. Otwórz przeglądarkę i zaloguj się do adresów URL vCenter. 

   > [!TIP]
   > Adresy URL można znaleźć w artykule [nawiązywanie połączenia z lokalnym programem vCenter w chmurze prywatnej](tutorial-access-private-cloud.md#connect-to-the-local-vcenter-of-your-private-cloud) .

1. W kliencie vSphere wybierz klaster, dla którego ma zostać włączona kopia zapasowa.

   :::image type="content" source="media/avs-backup/vsphere-client-select-host.png" alt-text="Klient VSphere — Wybieranie hosta":::

1. Wybierz pozycję **Konfiguruj**  >  **sieci**  >  **VMkernel karty**, a następnie na liście urządzeń Zidentyfikuj kartę sieciową, która ma włączoną rolę **aprowizacji** , a następnie zanotuj **adres IP** i nazwę hosta ESXi.

   :::image type="content" source="media/avs-backup/vmkernel-adapters-provisioning-enabled.png" alt-text="Karty VMKernel — urządzenia obsługujące obsługę administracyjną":::

1. Powtórz poprzedni krok dla każdego hosta ESXi w każdym klastrze, w którym ma zostać włączona kopia zapasowa.

### <a name="update-the-host-file-on-the-azure-backup-server"></a>Zaktualizuj plik hosta na Azure Backup Server

1. Otwórz program **Notepad** jako administrator.

2. Wybierz **plik > Otwórz** i Wyszukaj c:\Windows\System32\Drivers\etc\hosts.

3. Dodaj wpis dla każdego hosta ESXi wraz z adresem IP wskazanym w powyższej sekcji.

4. Zapisz zmiany i zamknij Notatnik.

## <a name="add-the-account-on-azure-backup-server"></a>Dodaj konto na Azure Backup Server


1. Otwórz Azure Backup Server, a następnie w konsoli Azure Backup Server wybierz pozycję **Zarządzanie**  >  **serwery produkcyjne**zarządzanie  >  **programem VMware**.

   ![Konsola Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. W oknie dialogowym **Zarządzanie poświadczeniami** kliknij przycisk **Dodaj**.

   ![Okno dialogowe Zarządzanie poświadczeniami Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. W polu **Dodaj poświadczenie**wprowadź nazwę i opis nowego poświadczenia, a następnie określ nazwę użytkownika i hasło zdefiniowane na serwerze VMware.

   > [!NOTE] 
   > Jeśli serwer VMware i Azure Backup Server nie znajdują się w tej samej domenie, określ domenę w polu Nazwa użytkownika.

   ![Okno dialogowe Dodawanie poświadczeń Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Kliknij przycisk **Dodaj** , aby dodać nowe poświadczenie.

   ![Okno dialogowe Zarządzanie poświadczeniami Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Dodaj vCenter Server do Azure Backup Server

1. W konsoli Azure Backup Server kliknij pozycję **Zarządzanie**  >  **serwerami produkcyjnymi**  >  **Dodaj**.

   ![Kreator otwierania dodawania serwera produkcyjnego](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Wybierz pozycję **serwery VMware** , a następnie kliknij przycisk **dalej**.

   ![Kreator dodawania do serwera produkcyjnego](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Określ adres IP serwera vCenter.

   ![Określ serwer VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. W polu **Port SSL**Wprowadź port używany do komunikacji z programem vCenter.

   > [!TIP] 
   > Port 443 jest portem domyślnym, ale można go zmienić, jeśli serwer vCenter nasłuchuje na innym porcie.

1. W polu **Określ poświadczenie**wybierz poświadczenie utworzone w poprzedniej sekcji.

1. Kliknij przycisk **Dodaj** , aby dodać serwer vCenter do listy serwerów, a następnie kliknij przycisk **dalej**.

   ![Dodaj serwer i poświadczenie oprogramowania VMWare](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Na stronie **Podsumowanie** kliknij przycisk **Dodaj** , aby dodać program vCenter do Azure Backup Server.

   Nowy serwer zostanie dodany natychmiast; Program vCenter nie potrzebuje agenta.

   ![Dodaj serwer VMware do Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Na stronie **zakończenie** Sprawdź ustawienia, a następnie kliknij przycisk **Zamknij**.

   ![Strona końcowa](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Serwer vCenter powinien zostać wyświetlony na liście na serwerze produkcyjnym z typem jako serwer VMware i stan agenta jako *OK*. Jeśli zobaczysz stan agenta jako *nieznany*, kliknij przycisk **Odśwież**.

## <a name="configure-a-protection-group"></a>Konfigurowanie grupy ochrony

Grupy ochrony zbierają wiele maszyn wirtualnych i stosują te same ustawienia przechowywania i tworzenia kopii zapasowych danych do wszystkich maszyn wirtualnych w grupie.

1. W konsoli Azure Backup Server kliknij pozycję **Ochrona**  >  **Nowa**.

   ![Otwórz Kreatora tworzenia nowej grupy ochrony](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na stronie powitalnej Kreatora **tworzenia nowej grupy ochrony** kliknij przycisk **dalej**.

   ![Kreator tworzenia nowej grupy ochrony — okno dialogowe](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na stronie **Wybierz typ grupy ochrony** wybierz pozycję **serwery** , a następnie kliknij przycisk **dalej**. Zostanie wyświetlona strona **Wybierz członków grupy** .

1. W obszarze **Wybierz członków grupy**Wybierz Maszyny wirtualne (lub foldery maszyn wirtualnych), których kopię zapasową chcesz utworzyć. Następnie kliknij przycisk **Dalej**.

   > [!NOTE]
   > Po wybraniu folderu lub maszyn wirtualnych lub folderów w tym folderze są również wybierane jako kopie zapasowe. Możesz wyczyścić foldery lub maszyny wirtualne, których kopia zapasowa ma nie być wykonana. Jeśli tworzona jest kopia zapasowa maszyny wirtualnej lub folderu, nie można jej wybrać, co gwarantuje, że nie zostaną utworzone zduplikowane punkty odzyskiwania dla maszyny wirtualnej.

   ![Wybierz członków grupy](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na stronie **Wybierz metodę ochrony danych** wprowadź nazwę grupy ochrony i ustawienia ochrony. 

1. Ustaw krótkoterminową ochronę na **dysku**, Włącz ochronę w trybie online, a następnie kliknij przycisk **dalej**.

   ![Wybieranie metody ochrony danych](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Określ, jak długo mają być przechowywane kopie zapasowe danych na dysku.

   - **Zakres przechowywania** — liczba dni, przez które punkty odzyskiwania dysku są przechowywane.
   - **Ekspresowa pełna kopia zapasowa** — jak często są pobierane punkty odzyskiwania dysków. Aby zmienić godziny/daty, gdy występują krótkoterminowe kopie zapasowe, kliknij przycisk **Modyfikuj**.

   :::image type="content" source="media/avs-backup/new-protection-group-specify-short-term-goals.png" alt-text="Określ cele krótkoterminowe dla ochrony opartej na dyskach":::

1. Na stronie **Przejrzyj przydział dysku**Przejrzyj miejsce na dysku udostępnione dla kopii zapasowych maszyn wirtualnych.

   - Zalecane alokacje dysków są zależne od określonego zakresu przechowywania, typu obciążenia i rozmiaru chronionych danych. Wprowadź wymagane zmiany, a następnie kliknij przycisk **dalej**.

   - **Rozmiar danych:** Rozmiar danych w grupie ochrony.

   - **Miejsce na dysku:** Zalecana ilość miejsca na dysku dla grupy ochrony. Jeśli chcesz zmodyfikować to ustawienie, należy przydzielić całkowite miejsce, które jest nieco większe niż wielkość Szacowana każdego źródła danych.

   - **Szczegóły puli magazynu:** Pokazuje stan puli magazynów, w tym całkowity rozmiar dysku i pozostałe.

   :::image type="content" source="media/avs-backup/review-disk-allocation.png" alt-text="Przejrzyj miejsce na dysku przydzieloną w puli magazynów":::

   > [!NOTE]
   > W niektórych scenariuszach raportowany rozmiar danych jest wyższy niż rozmiar rzeczywistej maszyny wirtualnej. Jesteśmy świadomi problemu i obecnie badamy go.

1. Na stronie **Wybierz metodę tworzenia repliki** wskaż, jak chcesz pobrać początkową kopię zapasową, a następnie kliknij przycisk **dalej**.

   - Wartość domyślna jest **automatycznie przez sieć** i **teraz**.   Jeśli używasz domyślnego, zalecamy określenie czasu poza godzinami szczytu. Wybierz pozycję **dalej** i określ datę i godzinę.

   - W przypadku dużych ilości danych lub mniej niż optymalnych warunków sieciowych należy rozważyć replikację danych w trybie offline za pomocą nośników wymiennych.

   ![Wybierz metodę tworzenia repliki](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. W obszarze **Opcje sprawdzania spójności**wybierz, jak i kiedy zautomatyzować sprawdzanie spójności, a następnie kliknij przycisk **dalej**.

   - Sprawdzanie spójności można uruchomić, gdy dane repliki staną się niespójne lub zgodnie z ustalonym harmonogramem.

   - Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, możesz uruchomić sprawdzanie ręczne, klikając prawym przyciskiem myszy grupę ochrony, aby **przeprowadzić sprawdzanie spójności**.

1. Na stronie **Określ dane ochrony w trybie online** Wybierz Maszyny wirtualne lub foldery maszyn wirtualnych, których kopię zapasową chcesz utworzyć, a następnie kliknij przycisk **dalej**. 

   > [!TIP]
   > Możesz wybrać członków indywidualnie lub kliknąć przycisk **Zaznacz wszystko** , aby wybrać wszystkich członków.

   ![Określ dane ochrony w trybie online](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych online Określ** , jak często mają być wykonywane kopie zapasowe danych z magazynu lokalnego na platformę Azure, a następnie kliknij przycisk **dalej**. 

   - Punkty odzyskiwania w chmurze do wygenerowania danych zgodnie z harmonogramem. 

   - Po wygenerowaniu punkt odzyskiwania zostanie on przeniesiony do magazynu Recovery Services na platformie Azure.

   ![Określ harmonogram kopii zapasowych online](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na stronie **Określanie zasad przechowywania w trybie online** wskaż, jak długo mają być przechowywane punkty odzyskiwania, które mają być tworzone na podstawie codziennych/cotygodniowych/comiesięcznych kopii zapasowych na platformie Azure, a następnie kliknij przycisk **dalej**.

   - Nie ma limitu czasu, w którym można przechowywać dane na platformie Azure.

   - Jedynym ograniczeniem jest to, że nie można mieć więcej niż 9999 punktów odzyskiwania dla każdego chronionego wystąpienia. W tym przykładzie chronione wystąpienie jest serwerem VMware.

   ![Określ zasady przechowywania danych online](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie kliknij przycisk **Utwórz grupę**.

   ![Członek grupy ochrony i podsumowanie ustawień](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-azure-backup-server-console"></a>Monitorowanie za pomocą konsoli Azure Backup Server

Po skonfigurowaniu grupy ochrony w celu tworzenia kopii zapasowych maszyn wirtualnych automatycznej synchronizacji można monitorować stan zadania i alertu kopii zapasowej za pomocą konsoli Azure Backup Server. Oto co można monitorować.

- Na karcie alerty w okienku Monitorowanie można monitorować błędy, ostrzeżenia i ogólne informacje dotyczące grupy ochrony, dla określonego komputera chronionego lub ważności wiadomości. Można wyświetlać aktywne i nieaktywne alerty oraz konfigurować powiadomienia e-mail.

- Na karcie zadania w okienku Monitorowanie można wyświetlić zadania zainicjowane przez Azure Backup Server dla określonego chronionego źródła danych lub grupy ochrony. Można obserwować postęp zadania lub sprawdzić zasoby używane przez zadania.

- W obszarze zadania **Ochrona** można sprawdzić stan woluminów i udziałów w grupie ochrony oraz sprawdzić ustawienia konfiguracji, takie jak ustawienia odzyskiwania, przydział dysku i harmonogram tworzenia kopii zapasowych.

- W obszarze zadania **zarządzania** można wyświetlić kartę **dyski, online**i **agenci** , aby sprawdzić stan dysków w puli magazynów, zarejestrować się na platformie Azure i wdrożyć stan agenta programu DPM.

:::image type="content" source="media/avs-backup/monitor-backup-jobs-in-mabs.png" alt-text="Monitorowanie stanu zadań tworzenia kopii zapasowej w Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>Przywróć maszyny wirtualne VMware

W Azure Backup Server konsola administratora istnieją dwa sposoby znajdowania odzyskiwalnych danych — wyszukiwanie i przeglądanie. Podczas odzyskiwania danych można lub nie chcieć przywracać danych ani maszyn wirtualnych do tej samej lokalizacji. Z tego powodu Azure Backup Server obsługuje trzy opcje odzyskiwania kopii zapasowych maszyn wirtualnych VMware:

- **Odzyskiwanie do oryginalnej lokalizacji (OLR)** — umożliwia przywrócenie chronionej maszyny wirtualnej do jej oryginalnej lokalizacji przy użyciu funkcji OLR. Maszynę wirtualną można przywrócić do jej oryginalnej lokalizacji tylko wtedy, gdy nie dodano ani nie usunięto żadnych dysków od momentu utworzenia kopii zapasowej. Jeśli dyski zostały dodane lub usunięte, musisz użyć odzyskiwania do lokalizacji alternatywnej.

- **Odzyskiwanie do lokalizacji alternatywnej (ALR)** — gdy brakuje oryginalnej maszyny wirtualnej lub nie chcesz zakłócać oryginalnej maszyny wirtualnej, ODZYSKAj maszynę wirtualną do lokalizacji alternatywnej. Aby odzyskać maszynę wirtualną do lokalizacji alternatywnej, należy podać lokalizację hosta ESXi, puli zasobów, folderu oraz magazynu danych magazynu i ścieżki. Aby ułatwić odróżnienie przywróconej maszyny wirtualnej od oryginalnej maszyny wirtualnej, Azure Backup Server dołączenie "-odzyskiwane" do nazwy maszyny wirtualnej.

- **Odzyskiwanie poszczególnych lokalizacji plików (ILR)** — Jeśli chroniona maszyna wirtualna jest maszyną wirtualną z systemem Windows Server, można odzyskać pojedyncze pliki/foldery w ramach maszyny wirtualnej przy użyciu funkcji ILR Azure Backup Server. Aby odzyskać poszczególne pliki, zapoznaj się z procedurą w dalszej części tego artykułu. Przywracanie pojedynczego pliku z maszyny wirtualnej jest dostępne tylko dla punktów odzyskiwania maszyn wirtualnych i dysków z systemem Windows.

### <a name="restore-a-recovery-point"></a>Przywracanie punktu odzyskiwania

1. W konsola administratora Azure Backup Server kliknij pozycję Widok odzyskiwania. 

1. Za pomocą okienka przeglądania Przeglądaj lub Filtruj, aby znaleźć maszynę wirtualną, którą chcesz odzyskać. Po wybraniu maszyny wirtualnej lub folderu punkty odzyskiwania w okienku zostaną wyświetlone dostępne punkty odzyskiwania.

   ![Dostępne punkty odzyskiwania](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. W polu **punkty odzyskiwania dla** Użyj menu kalendarz i listy rozwijanej, aby wybrać datę utworzenia punktu odzyskiwania. Pogrubienie dat kalendarza ma dostępne punkty odzyskiwania. Alternatywnie możesz kliknąć prawym przyciskiem myszy maszynę wirtualną i wybrać opcję **Pokaż wszystkie punkty odzyskiwania** , a następnie wybrać punkt odzyskiwania z listy.

   > [!NOTE] 
   > W przypadku ochrony krótkoterminowej wybierz punkt odzyskiwania oparty na dysku do szybszego odzyskiwania. Po wygaśnięciu krótkoterminowych punktów odzyskiwania widoczne są tylko punkty odzyskiwania online do odzyskania.

1. Przed odzyskiwaniem z punktu odzyskiwania online upewnij się, że lokalizacja tymczasowa zawiera wystarczającą ilość wolnego miejsca do przechowywania pełnego nieskompresowanego rozmiaru maszyny wirtualnej, którą chcesz odzyskać. Lokalizację przemieszczania można wyświetlić/zmienić, uruchamiając **Kreatora konfiguracji ustawień subskrypcji**.

   :::image type="content" source="media/avs-backup/mabs-recovery-folder-settings.png" alt-text="Ustawienia folderu odzyskiwania Azure Backup Server":::

1. Kliknij przycisk **Odzyskaj** , aby otworzyć **Kreatora odzyskiwania**.

   ![Kreator odzyskiwania, przeglądanie wyboru odzyskiwania](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Kliknij przycisk **dalej** , aby przejść do ekranu **Określanie opcji odzyskiwania** , a następnie ponownie kliknij przycisk **dalej** , aby przejść do ekranu **Wybierz typ odzyskiwania** . 

   > [!NOTE]
   > Obciążenia oprogramowania VMware nie obsługują włączania ograniczania przepustowości sieci.

1. Na stronie **Wybierz typ odzyskiwania** wybierz, czy chcesz odzyskać do oryginalnego wystąpienia, czy też nowej lokalizacji, a następnie kliknij przycisk **dalej**.

   - W przypadku wybrania opcji **Odzyskaj do oryginalnego wystąpienia**nie trzeba wprowadzać więcej opcji w kreatorze. Dane dla oryginalnego wystąpienia są używane.

   - Jeśli wybierzesz opcję **Odzyskaj jako maszynę wirtualną na dowolnym hoście**, na ekranie **określ miejsce docelowe** podaj informacje dotyczące **hosta ESXi, puli zasobów, folderu**i **ścieżki**.

   ![Wybierz typ odzyskiwania](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie kliknij przycisk **Odzyskaj** , aby rozpocząć proces odzyskiwania. 

   Ekran stan odzyskiwania pokazuje postęp operacji odzyskiwania.

### <a name="restore-an-individual-file-from-a-vm"></a>Przywracanie pojedynczego pliku z maszyny wirtualnej

Można przywrócić pojedyncze pliki z chronionego punktu odzyskiwania maszyny wirtualnej. Ta funkcja jest dostępna tylko dla maszyn wirtualnych z systemem Windows Server. Przywracanie pojedynczych plików jest podobne do przywracania całej maszyny wirtualnej, z tą różnicą, że przed rozpoczęciem procesu odzyskiwania przejdziesz do dysku VMDK i wyszukasz wymagane pliki. 

> [!NOTE]
> Przywracanie pojedynczego pliku z maszyny wirtualnej jest dostępne tylko dla punktów odzyskiwania maszyn wirtualnych i dysków z systemem Windows.

1. W konsola administratora Azure Backup Server kliknij pozycję Widok **odzyskiwania** .

1. Za pomocą okienka **przeglądania** Przeglądaj lub Filtruj, aby znaleźć maszynę wirtualną, którą chcesz odzyskać. Po wybraniu maszyny wirtualnej lub folderu punkty odzyskiwania w okienku zostaną wyświetlone dostępne punkty odzyskiwania.

   ![Dostępne punkty odzyskiwania](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. W okienku **punkty odzyskiwania dla** Użyj kalendarza, aby wybrać datę zawierającą żądane punkty odzyskiwania. W zależności od sposobu skonfigurowania zasad tworzenia kopii zapasowych daty mogą mieć więcej niż jeden punkt odzyskiwania. 

1. Po wybraniu dnia, w którym wykonano punkt odzyskiwania, upewnij się, że wybrano prawidłowy **czas odzyskiwania**. 

   > [!NOTE]
   > Jeśli wybrana data ma wiele punktów odzyskiwania, wybierz punkt odzyskiwania, wybierając go z menu rozwijanego godzina odzyskiwania. 

   Po wybraniu punktu odzyskiwania w okienku **ścieżka** zostanie wyświetlona lista elementów możliwych do odzyskania.  

1. Aby znaleźć pliki do odzyskania, w okienku **ścieżki** kliknij dwukrotnie element w kolumnie **element** możliwy do odzyskania, a następnie wybierz plik lub foldery, które chcesz odzyskać. Aby zaznaczyć wiele elementów, naciśnij klawisz **Ctrl** podczas zaznaczania każdego elementu. Użyj okienka **ścieżka** , aby przeszukać listę plików lub folderów wyświetlanych w kolumnie element możliwy do **odzyskania** .
    
   > [!NOTE]
   > **Poniższa lista wyszukiwania** nie przeszukuje podfolderów. Aby przeszukać podfoldery, kliknij dwukrotnie folder. Użyj przycisku do **góry** , aby przejść z folderu podrzędnego do folderu nadrzędnego. Można wybrać wiele elementów (plików i folderów), ale muszą one znajdować się w tym samym folderze nadrzędnym. Nie można odzyskać elementów z wielu folderów w ramach tego samego zadania odzyskiwania.

   ![Przegląd wyboru odzyskiwania](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Po wybraniu elementów do odzyskania na Wstążce narzędzia konsola administratora kliknij przycisk **Odzyskaj** , aby otworzyć **Kreatora odzyskiwania**. W Kreatorze odzyskiwania ekran **Przegląd opcji odzyskiwania** pokazuje wybrane elementy do odzyskania.

1. Na stronie **Określanie opcji odzyskiwania** wykonaj jedną z następujących czynności:

   - Kliknij przycisk **Modyfikuj** , aby włączyć ograniczanie przepustowości sieci.  W oknie dialogowym ograniczanie przepustowości wybierz opcję **Włącz ograniczenie przepustowości sieci** , aby je włączyć. Po włączeniu Skonfiguruj **Ustawienia** i **harmonogram pracy**.
    
   - Kliknij przycisk **dalej** , aby wyłączyć funkcję ograniczania przepustowości sieci.

1. Na ekranie **Wybieranie typu odzyskiwania** kliknij przycisk **dalej**. Pliki lub foldery można odzyskiwać tylko do folderu sieciowego.

1. Na ekranie **określ miejsce docelowe** kliknij przycisk **Przeglądaj** , aby znaleźć lokalizację sieciową dla plików lub folderów. Azure Backup Server tworzy folder, do którego zostaną skopiowane wszystkie odzyskane elementy. Nazwa folderu ma prefiks, MABS_day-miesiąc — rok. W przypadku wybrania lokalizacji odzyskiwanych plików lub folderów zostaną podane szczegóły dotyczące tej lokalizacji (lokalizacji docelowej, ścieżki docelowej i dostępnego miejsca).

   ![Określ lokalizację do odzyskania plików](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Na stronie **Określ opcje odzyskiwania** wybierz ustawienia zabezpieczeń, które mają zostać zastosowane. Można wybrać opcję modyfikacji ograniczenia przepustowości sieci, ale ograniczenie przepustowości jest domyślnie wyłączone. Ponadto odzyskiwanie i **powiadamianie** **sieci San** nie są włączone.

1. Na ekranie **Podsumowanie** przejrzyj ustawienia, a następnie kliknij przycisk **Odzyskaj** , aby rozpocząć proces odzyskiwania. Ekran **stan odzyskiwania** pokazuje postęp operacji odzyskiwania.

## <a name="next-steps"></a>Następne kroki

Informacje dotyczące rozwiązywania problemów podczas konfigurowania kopii zapasowych znajdują się w przewodniku rozwiązywania problemów Azure Backup Server.



> [!div class="nextstepaction"]
> [Przewodnik rozwiązywania problemów Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
