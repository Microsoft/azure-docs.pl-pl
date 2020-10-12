---
title: Tworzenie kopii zapasowych maszyn wirtualnych rozwiązań VMware platformy Azure przy użyciu Azure Backup Server
description: Skonfiguruj środowisko rozwiązań VMware platformy Azure, aby tworzyć kopie zapasowe maszyn wirtualnych przy użyciu Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: b8b5236a8da165efbb8e479e25b58872c4a735ee
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893020"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Tworzenie kopii zapasowych maszyn wirtualnych rozwiązań VMware platformy Azure przy użyciu Azure Backup Server

W tym artykule opisano procedury tworzenia kopii zapasowych maszyn wirtualnych VMware działających na platformie Azure VMware przy użyciu Azure Backup Server. Przed rozpoczęciem upewnij się, że dokładnie przejdziesz do [konfiguracji Microsoft Azure Backup Server for Azure VMware Solution](set-up-backup-server-for-azure-vmware-solution.md).

Następnie przeprowadzimy przez wszystkie niezbędne procedury, aby:

> [!div class="checklist"] 
> * Skonfiguruj bezpieczny kanał, aby Azure Backup Server mógł komunikować się z serwerami VMware za pośrednictwem protokołu HTTPS. 
> * Dodaj poświadczenia konta do Azure Backup Server. 
> * Dodaj program vCenter do Azure Backup Server. 
> * Skonfiguruj grupę ochrony zawierającą maszyny wirtualne VMware, dla których chcesz utworzyć kopię zapasową, określ ustawienia kopii zapasowej i Zaplanuj kopię zapasową.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Utwórz bezpieczne połączenie z serwerem vCenter

Domyślnie Azure Backup Server komunikuje się z serwerami VMware za pośrednictwem protokołu HTTPS. Aby skonfigurować połączenie HTTPS, Pobierz certyfikat urzędu certyfikacji VMware (CA) i zaimportuj go na Azure Backup Server.

### <a name="set-up-the-certificate"></a>Konfigurowanie certyfikatu

1. W przeglądarce na komputerze Azure Backup Server wprowadź adres URL klienta sieci Web vSphere.

   > [!NOTE] 
   > Jeśli strona programu VMware **wprowadzenie** nie zostanie wyświetlona, sprawdź ustawienia połączenia i serwera proxy przeglądarki i spróbuj ponownie.

1. Na stronie **wprowadzenie** VMware wybierz pozycję **Pobierz certyfikaty zaufanych głównych urzędów certyfikacji**.

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="Klient sieci Web vSphere":::

1. Zapisz plik **download.zip** na maszynie Azure Backup Server, a następnie wyodrębnij jego zawartość do folderu **Certyfikaty** , który zawiera:

   - Plik certyfikatu głównego z rozszerzeniem rozpoczynającym się od numerowanej sekwencji takiej jak. 0 i. 1.
   - Plik listy CRL z rozszerzeniem rozpoczynającym się od sekwencji takich jak. r0 lub. R1.

1. W folderze **Certyfikaty** kliknij prawym przyciskiem myszy plik certyfikatu głównego, a następnie wybierz polecenie **Zmień nazwę** , aby zmienić rozszerzenie na **. CRT**.

   Ikona pliku zmieni się na taki, który reprezentuje certyfikat główny.

1. Kliknij prawym przyciskiem myszy certyfikat główny, a następnie wybierz pozycję **Zainstaluj certyfikat**.

1. W **Kreatorze importu certyfikatów**wybierz pozycję **komputer lokalny** jako miejsce docelowe certyfikatu, a następnie wybierz przycisk **dalej**.

   ![Strona powitalna kreatora](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > Jeśli zostanie wyświetlony monit, potwierdź, że chcesz zezwolić na wprowadzanie zmian na komputerze.

1. Wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie**, a następnie wybierz pozycję **Przeglądaj** , aby wybrać magazyn certyfikatów.

   ![Magazyn certyfikatów](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Wybierz pozycję **Zaufane główne** urzędy certyfikacji jako folder docelowy, a następnie wybierz **przycisk OK**.

1. Przejrzyj ustawienia, a następnie wybierz pozycję **Zakończ** , aby rozpocząć Importowanie certyfikatu.

   ![Sprawdź, czy certyfikat znajduje się w odpowiednim folderze](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Po potwierdzeniu importu certyfikatu Zaloguj się do serwera vCenter, aby upewnić się, że połączenie jest bezpieczne.

### <a name="enable-tls-12-on-azure-backup-server"></a>Włącz protokół TLS 1,2 na Azure Backup Server

W przypadku oprogramowania VMware 6,7 jako protokołu komunikacyjnego włączono protokół TLS. 

1. Skopiuj poniższe ustawienia rejestru i wklej je do Notatnika. Następnie Zapisz plik jako TLS. REG bez rozszerzenia. txt.

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


## <a name="add-the-account-on-azure-backup-server"></a>Dodaj konto na Azure Backup Server

1. Otwórz Azure Backup Server, a następnie w konsoli Azure Backup Server wybierz pozycję **Zarządzanie**  >  **serwery produkcyjne**zarządzanie  >  **programem VMware**.

   ![Konsola Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. W oknie dialogowym **Zarządzanie poświadczeniami** wybierz pozycję **Dodaj**.

   ![W oknie dialogowym Zarządzanie poświadczeniami wybierz pozycję Dodaj.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. W oknie dialogowym **Dodaj poświadczenia** wprowadź nazwę i opis nowego poświadczenia. Określ nazwę użytkownika i hasło zdefiniowane na serwerze VMware.

   > [!NOTE] 
   > Jeśli serwer VMware i Azure Backup Server nie znajdują się w tej samej domenie, określ domenę w polu **Nazwa użytkownika** .

   ![Okno dialogowe Dodawanie poświadczeń Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Wybierz pozycję **Dodaj** , aby dodać nowe poświadczenie.

   ![Zrzut ekranu Azure Backup Server przedstawia okno dialogowe Zarządzanie poświadczeniami z wyświetlonymi nowymi poświadczeniami.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Dodaj serwer vCenter do Azure Backup Server

1. W konsoli Azure Backup Server wybierz pozycję **Zarządzanie**  >  **serwery produkcyjne**  >  **Dodaj**.

   ![Kreator otwierania dodawania serwera produkcyjnego](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Wybierz pozycję **serwery VMware**, a **następnie**kliknij przycisk Dalej.

   ![Kreator dodawania do serwera produkcyjnego](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Określ adres IP serwera vCenter.

   ![Określ serwer VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. W polu **Port SSL** Wprowadź port używany do komunikacji z programem vCenter.

   > [!TIP] 
   > Port 443 jest portem domyślnym, ale można go zmienić, jeśli serwer vCenter nasłuchuje na innym porcie.

1. W polu **Określ poświadczenia** wybierz poświadczenie, które zostało utworzone w poprzedniej sekcji.

1. Wybierz pozycję **Dodaj** , aby dodać serwer vCenter do listy serwerów, a następnie wybierz przycisk **dalej**.

   ![Dodaj serwer i poświadczenie oprogramowania VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Na stronie **Podsumowanie** wybierz pozycję **Dodaj** , aby dodać program vCenter do Azure Backup Server.

   Nowy serwer zostanie dodany natychmiast. Program vCenter nie potrzebuje agenta.

   ![Dodaj serwer VMware do Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Na stronie **zakończenie** Sprawdź ustawienia, a następnie wybierz pozycję **Zamknij**.

   ![Strona końcowa](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Serwer vCenter powinien zostać wyświetlony na liście na **serwerze produkcyjnym** z typem jako **serwer VMware** i **stan agenta** jako **OK**. Jeśli widzisz **stan agenta** jako **nieznany**, wybierz pozycję **Odśwież**.

## <a name="configure-a-protection-group"></a>Konfigurowanie grupy ochrony

Grupy ochrony zbierają wiele maszyn wirtualnych i stosują te same ustawienia przechowywania i tworzenia kopii zapasowych danych do wszystkich maszyn wirtualnych w grupie.

1. W konsoli Azure Backup Server wybierz pozycję **Ochrona**  >  **Nowa**.

   ![Otwórz Kreatora tworzenia nowej grupy ochrony](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na stronie powitalnej Kreatora **tworzenia nowej grupy ochrony** wybierz pozycję **dalej**.

   ![Kreator tworzenia nowej grupy ochrony — okno dialogowe](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na stronie **Wybierz typ grupy ochrony** wybierz pozycję **serwery**, a następnie wybierz przycisk **dalej**. Zostanie wyświetlona strona **Wybierz członków grupy** .

1. Na stronie **Wybierz członków grupy** Wybierz Maszyny wirtualne (lub foldery maszyn wirtualnych), których kopię zapasową chcesz utworzyć, a następnie wybierz przycisk **dalej**.

   > [!NOTE]
   > W przypadku wybrania folderu lub maszyn wirtualnych foldery znajdujące się w tym folderze również są wybierane do kopii zapasowej. Możesz wyczyścić foldery lub maszyny wirtualne, których kopia zapasowa ma nie być wykonana. Jeśli tworzona jest kopia zapasowa maszyny wirtualnej lub folderu, nie można jej wybrać, co gwarantuje, że nie zostaną utworzone zduplikowane punkty odzyskiwania dla maszyny wirtualnej.

   ![Wybierz członków grupy](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na stronie **Wybierz metodę ochrony danych** wprowadź nazwę grupy ochrony i ustawienia ochrony. 

1. Ustaw krótkoterminową ochronę na **dysku**, Włącz ochronę w trybie online, a następnie wybierz przycisk **dalej**.

   ![Wybieranie metody ochrony danych](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Określ, jak długo mają być przechowywane kopie zapasowe danych na dysku.

   - **Zakres przechowywania**: liczba dni przechowywania punktów odzyskiwania dysków.
   - **Ekspresowa pełna kopia zapasowa**: jak często są pobierane punkty odzyskiwania dysków. Aby zmienić godziny lub daty wystąpienia krótkoterminowych kopii zapasowych, wybierz pozycję **Modyfikuj**.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Klient sieci Web vSphere":::

1. Na stronie **przegląd Disk Storage alokacji** Sprawdź miejsce na dysku dla kopii zapasowych maszyny wirtualnej.

   - Zalecane alokacje dysków są zależne od określonego zakresu przechowywania, typu obciążenia i rozmiaru chronionych danych. Wprowadź wymagane zmiany, a następnie wybierz przycisk **dalej**.
   - **Rozmiar danych:** Rozmiar danych w grupie ochrony.
   - **Miejsce na dysku:** Zalecana ilość miejsca na dysku dla grupy ochrony. Aby zmodyfikować to ustawienie, należy przydzielić całkowite miejsce, które jest nieco większe niż wielkość Szacowana każdego źródła danych.
   - **Szczegóły puli magazynu:** Pokazuje stan puli magazynów, w tym łączny rozmiar i czas wolnego dysku.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Klient sieci Web vSphere":::

   > [!NOTE]
   > W niektórych scenariuszach raportowany rozmiar danych jest wyższy niż rozmiar rzeczywistej maszyny wirtualnej. Jesteśmy świadomi problemu i obecnie badamy go.

1. Na stronie **Wybierz metodę tworzenia repliki** wskaż, jak chcesz pobrać początkową kopię zapasową, a następnie wybierz pozycję **dalej**.

   - Wartość domyślna jest **automatycznie przez sieć** i **teraz**. Jeśli używasz wartości domyślnej, określ czas poza szczytem. W przypadku wybrania opcji **później**Określ dzień i godzinę.
   - W przypadku dużych ilości danych lub mniej niż optymalnych warunków sieciowych należy rozważyć replikację danych w trybie offline za pomocą nośników wymiennych.

   ![Wybierz metodę tworzenia repliki](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. W obszarze **Opcje sprawdzania spójności**wybierz, jak i kiedy zautomatyzować sprawdzanie spójności, a następnie wybierz przycisk **dalej**.

   - Sprawdzanie spójności można uruchomić, gdy dane repliki staną się niespójne lub zgodnie z ustalonym harmonogramem.
   - Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, możesz uruchomić sprawdzanie ręczne, klikając prawym przyciskiem myszy grupę ochrony, aby **przeprowadzić sprawdzanie spójności**.

1. Na stronie **Określ dane ochrony w trybie online** Wybierz Maszyny wirtualne lub foldery maszyn wirtualnych, których kopię zapasową chcesz utworzyć, a następnie wybierz przycisk **dalej**. 

   > [!TIP]
   > Możesz wybrać członków pojedynczo lub wybierz **pozycję Zaznacz wszystko** , aby wybrać wszystkie elementy członkowskie.

   ![Określ dane ochrony w trybie online](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych online Określ** , jak często mają być wykonywane kopie zapasowe danych z magazynu lokalnego na platformę Azure, a następnie wybierz przycisk **dalej**. 

   - Punkty odzyskiwania w chmurze do wygenerowania danych zgodnie z harmonogramem. 
   - Po wygenerowaniu punkt odzyskiwania zostanie on przeniesiony do magazynu Recovery Services na platformie Azure.

   ![Określ harmonogram kopii zapasowych online](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na stronie **Określanie zasad przechowywania online** wskaż, jak długo mają być przechowywane punkty odzyskiwania tworzone z kopii zapasowych codziennie, co tydzień, co miesiąc lub co rok, a następnie wybierz przycisk **dalej**.

   - Nie ma limitu czasu, w którym można przechowywać dane na platformie Azure.
   - Jedynym ograniczeniem jest to, że nie można mieć więcej niż 9 999 punktów odzyskiwania dla każdego chronionego wystąpienia. W tym przykładzie chronione wystąpienie jest serwerem VMware.

   ![Określ zasady przechowywania danych online](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz grupę**.

   ![Członek grupy ochrony i podsumowanie ustawień](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Monitorowanie za pomocą konsoli Azure Backup Server

Po skonfigurowaniu grupy ochrony do tworzenia kopii zapasowych maszyn wirtualnych rozwiązań VMware platformy Azure można monitorować stan zadania i alertu kopii zapasowej za pomocą konsoli Azure Backup Server. Oto, co można monitorować.

- Na karcie **alerty** w okienku **monitorowanie** można monitorować błędy, ostrzeżenia i ogólne informacje dotyczące grupy ochrony, dla określonego komputera chronionego lub ważności wiadomości. Możesz wyświetlić aktywne i nieaktywne alerty oraz skonfigurować powiadomienia e-mail.
- Na karcie **zadania** w okienku **monitorowanie** można wyświetlić zadania zainicjowane przez Azure Backup Server dla określonego chronionego źródła danych lub grupy ochrony. Można obserwować postęp zadania lub sprawdzić zasoby używane przez zadania.
- W obszarze zadania **Ochrona** można sprawdzić stan woluminów i udziałów w grupie ochrony. Można także sprawdzić ustawienia konfiguracji, takie jak ustawienia odzyskiwania, przydział dysku i harmonogram tworzenia kopii zapasowych.
- W obszarze zadania **zarządzania** można wyświetlić karty **dyski, online**i **agenci** , aby sprawdzić stan dysków w puli magazynów, zarejestrować się na platformie Azure i wdrożyć stan agenta programu DPM.

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Klient sieci Web vSphere":::

## <a name="restore-vmware-virtual-machines"></a>Przywróć maszyny wirtualne VMware

W konsola administratora Azure Backup Server istnieją dwa sposoby znajdowania możliwych do odzyskania danych. Możesz wyszukiwać lub przeglądać. Podczas odzyskiwania danych można lub nie chcieć przywracać danych lub maszyn wirtualnych do tej samej lokalizacji. Z tego powodu Azure Backup Server obsługuje trzy opcje odzyskiwania kopii zapasowych maszyn wirtualnych VMware:

- **Odzyskiwanie do oryginalnej lokalizacji (OLR)**: Użyj OLR do przywrócenia chronionej maszyny wirtualnej do jej oryginalnej lokalizacji. Maszynę wirtualną można przywrócić do jej oryginalnej lokalizacji tylko wtedy, gdy nie zostały dodane lub usunięte dyski od momentu utworzenia kopii zapasowej. Jeśli dyski zostały dodane lub usunięte, musisz użyć odzyskiwania do lokalizacji alternatywnej.
- **Odzyskiwanie do lokalizacji alternatywnej (ALR)**: gdy brakuje oryginalnej maszyny wirtualnej lub nie chcesz zakłócać oryginalnej maszyny wirtualnej, ODZYSKAj maszynę wirtualną do lokalizacji alternatywnej. Aby odzyskać maszynę wirtualną do lokalizacji alternatywnej, należy podać lokalizację hosta ESXi, puli zasobów, folderu oraz magazynu danych magazynu i ścieżki. Aby ułatwić odróżnienie przywróconej maszyny wirtualnej od oryginalnej maszyny wirtualnej, Azure Backup Server dołączenie "-odzyskiwane" do nazwy maszyny wirtualnej.
- **Odzyskiwanie poszczególnych lokalizacji plików (ILR)**: Jeśli chroniona maszyna wirtualna jest maszyną wirtualną z systemem Windows Server, można odzyskać pojedyncze pliki lub foldery w ramach maszyny wirtualnej za pomocą funkcji ILR Azure Backup Server. Aby odzyskać poszczególne pliki, zapoznaj się z procedurą w dalszej części tego artykułu. Przywracanie pojedynczego pliku z maszyny wirtualnej jest dostępne tylko dla punktów odzyskiwania maszyn wirtualnych i dysków z systemem Windows.

### <a name="restore-a-recovery-point"></a>Przywracanie punktu odzyskiwania

1. W konsola administratora Azure Backup Server wybierz widok **odzyskiwania** . 

1. Za pomocą okienka **przeglądania** Przeglądaj lub Filtruj, aby znaleźć maszynę wirtualną, którą chcesz odzyskać. Po wybraniu maszyny wirtualnej lub folderu **punkty odzyskiwania dla** okienka wyświetla dostępne punkty odzyskiwania.

   ![Dostępne punkty odzyskiwania](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. W okienku **punkty odzyskiwania dla** Użyj menu kalendarz i listy rozwijanej, aby wybrać datę utworzenia punktu odzyskiwania. Pogrubienie dat kalendarza ma dostępne punkty odzyskiwania. Alternatywnie możesz kliknąć prawym przyciskiem myszy maszynę wirtualną i wybrać opcję **Pokaż wszystkie punkty odzyskiwania** , a następnie wybrać punkt odzyskiwania z listy.

   > [!NOTE] 
   > W przypadku ochrony krótkoterminowej wybierz punkt odzyskiwania oparty na dysku do szybszego odzyskiwania. Po wygaśnięciu krótkoterminowych punktów odzyskiwania są wyświetlane tylko punkty odzyskiwania **online** do odzyskania.

1. Przed odzyskiwaniem z punktu odzyskiwania online upewnij się, że lokalizacja tymczasowa zawiera wystarczającą ilość wolnego miejsca do przechowywania pełnego nieskompresowanego rozmiaru maszyny wirtualnej, którą chcesz odzyskać. Lokalizację przemieszczania można wyświetlić lub zmienić, uruchamiając **Kreatora konfiguracji ustawień subskrypcji**.

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Klient sieci Web vSphere":::

1. Wybierz pozycję **Odzyskaj** , aby otworzyć **Kreatora odzyskiwania**.

   ![Kreator odzyskiwania, Przegląd strony wyboru odzyskiwania](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Wybierz pozycję **dalej** , aby przejść do ekranu **Określanie opcji odzyskiwania** . Wybierz ponownie przycisk **dalej** , aby przejść do ekranu **Wybieranie typu odzyskiwania** . 

   > [!NOTE]
   > Obciążenia oprogramowania VMware nie obsługują włączania ograniczania przepustowości sieci.

1. Na stronie **Wybierz typ odzyskiwania** wybierz, czy chcesz odzyskać do oryginalnego wystąpienia, czy nowej lokalizacji, a następnie wybierz przycisk **dalej**.

   - W przypadku wybrania opcji **Odzyskaj do oryginalnego wystąpienia**nie trzeba wprowadzać więcej opcji w kreatorze. Dane dla oryginalnego wystąpienia są używane.
   - Jeśli wybierzesz opcję **Odzyskaj jako maszynę wirtualną na dowolnym hoście**, na ekranie **określ miejsce docelowe** podaj informacje dotyczące **hosta ESXi**, **puli zasobów**, **folderu**i **ścieżki**.

   ![Wybieranie strony typu odzyskiwania](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz pozycję **Odzyskaj** , aby rozpocząć proces odzyskiwania. 

   Ekran **stan odzyskiwania** pokazuje postęp operacji odzyskiwania.

### <a name="restore-an-individual-file-from-a-vm"></a>Przywracanie pojedynczego pliku z maszyny wirtualnej

Można przywrócić pojedyncze pliki z chronionego punktu odzyskiwania maszyny wirtualnej. Ta funkcja jest dostępna tylko dla maszyn wirtualnych z systemem Windows Server. Przywracanie pojedynczych plików jest podobne do przywracania całej maszyny wirtualnej, z wyjątkiem przechodzenia do dysku VMDK i znajdowania odpowiednich plików przed rozpoczęciem procesu odzyskiwania. 

> [!NOTE]
> Przywracanie pojedynczego pliku z maszyny wirtualnej jest dostępne tylko dla punktów odzyskiwania maszyn wirtualnych i dysków z systemem Windows.

1. W konsola administratora Azure Backup Server wybierz widok **odzyskiwania** .

1. Za pomocą okienka **przeglądania** Przeglądaj lub Filtruj, aby znaleźć maszynę wirtualną, którą chcesz odzyskać. Po wybraniu maszyny wirtualnej lub folderu **punkty odzyskiwania dla** okienka wyświetla dostępne punkty odzyskiwania.

   ![Dostępne punkty odzyskiwania](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. W okienku **punkty odzyskiwania dla** Użyj kalendarza, aby wybrać datę zawierającą żądane punkty odzyskiwania. W zależności od sposobu skonfigurowania zasad tworzenia kopii zapasowych daty mogą mieć więcej niż jeden punkt odzyskiwania. 

1. Po wybraniu dnia, w którym wykonano punkt odzyskiwania, należy upewnić się, że wybrano prawidłowy **czas odzyskiwania**. 

   > [!NOTE]
   > Jeśli wybrana data ma wiele punktów odzyskiwania, wybierz punkt odzyskiwania, wybierając go z menu rozwijanego **godzina odzyskiwania** . 

   Po wybraniu punktu odzyskiwania w okienku **ścieżka** zostanie wyświetlona lista elementów możliwych do odzyskania.

1. Aby znaleźć pliki do odzyskania, w okienku **ścieżki** kliknij dwukrotnie element w kolumnie element możliwy do **odzyskania** , aby go otworzyć. Następnie wybierz plik lub foldery, które chcesz odzyskać. Aby zaznaczyć wiele elementów, wybierz klawisz **Ctrl** podczas zaznaczania każdego elementu. Użyj okienka **ścieżka** , aby przeszukać listę plików lub folderów, które pojawiają się w kolumnie element możliwy do **odzyskania** .
    
   > [!NOTE]
   > **Lista wyszukiwania poniżej** nie przeszukiwania do podfolderów. Aby przeszukać podfoldery, kliknij dwukrotnie folder. Użyj przycisku do **góry** , aby przejść z folderu podrzędnego do folderu nadrzędnego. Można wybrać wiele elementów (plików i folderów), ale muszą one znajdować się w tym samym folderze nadrzędnym. Nie można odzyskać elementów z wielu folderów w ramach tego samego zadania odzyskiwania.

   ![Przegląd wyboru odzyskiwania](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Po wybraniu elementów do odzyskania na Wstążce narzędzia konsola administratora wybierz pozycję **Odzyskaj** , aby otworzyć **Kreatora odzyskiwania**. W **Kreatorze odzyskiwania**ekran **Przegląd opcji odzyskiwania** pokazuje wybrane elementy do odzyskania.

1. Na stronie **Określanie opcji odzyskiwania** wykonaj jedną z następujących czynności:

   - Wybierz pozycję **Modyfikuj** , aby włączyć ograniczanie przepustowości sieci. W oknie dialogowym **ograniczanie** przepustowości wybierz opcję **Włącz ograniczenie przepustowości sieci** , aby je włączyć. Po włączeniu Skonfiguruj **Ustawienia** i **harmonogram pracy**.
   - Wybierz pozycję **dalej** , aby wyłączyć funkcję ograniczania przepustowości sieci.

1. Na ekranie **Wybieranie typu odzyskiwania** wybierz pozycję **dalej**. Pliki lub foldery można odzyskać tylko do folderu sieciowego.

1. Na ekranie **określ miejsce docelowe** wybierz pozycję **Przeglądaj** , aby znaleźć lokalizację sieciową dla plików lub folderów. Azure Backup Server tworzy folder, do którego zostaną skopiowane wszystkie odzyskane elementy. Nazwa folderu ma prefiks MABS_day-miesiąc-Year. W przypadku wybrania lokalizacji odzyskiwanych plików lub folderów są dostępne szczegółowe informacje dotyczące tej lokalizacji, takie jak **Lokalizacja docelowa**, **ścieżka docelowa**i **miejsce**.

   ![Określ lokalizację do odzyskania plików](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Na stronie **Określ opcje odzyskiwania** wybierz ustawienia zabezpieczeń, które mają zostać zastosowane. Można wybrać opcję modyfikacji ograniczenia przepustowości sieci, ale ograniczenie przepustowości jest domyślnie wyłączone. Ponadto odzyskiwanie i **powiadamianie** **sieci San** nie jest włączone.

1. Na ekranie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz polecenie **Odzyskaj** , aby rozpocząć proces odzyskiwania. Ekran **stan odzyskiwania** pokazuje postęp operacji odzyskiwania.

## <a name="next-steps"></a>Następne kroki

Informacje dotyczące rozwiązywania problemów podczas konfigurowania kopii zapasowych znajdują się w przewodniku rozwiązywania problemów Azure Backup Server.

> [!div class="nextstepaction"]
> [Przewodnik rozwiązywania problemów Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
