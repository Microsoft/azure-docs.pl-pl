---
title: Kopii zapasowej Azure VMware Solution wirtualnych za pomocą Azure Backup Server
description: Skonfiguruj środowisko Azure VMware Solution, aby konfigurować kopię zapasową maszyn wirtualnych przy użyciu Azure Backup Server.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 92affbf883215f0d051115fb64e9e7bf7a1430b3
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871781"
---
# <a name="back-up-azure-vmware-solution-vms-with-azure-backup-server"></a>Kopii zapasowej Azure VMware Solution wirtualnych za pomocą Azure Backup Server

W tym artykule będziemy back up VMware virtual machines (VMs) running on Azure VMware Solution with Azure Backup Server(Maszyny wirtualne VMware) uruchomione na Azure VMware Solution z Azure Backup Server. Najpierw dokładnie przejmij [kroki konfiguracji Microsoft Azure Backup Server dla Azure VMware Solution.](set-up-backup-server-for-azure-vmware-solution.md)

Następnie przejedzemy przez wszystkie niezbędne procedury, aby:

> [!div class="checklist"] 
> * Skonfiguruj bezpieczny kanał, aby Azure Backup Server z serwerami VMware za pośrednictwem protokołu HTTPS. 
> * Dodaj poświadczenia konta, aby Azure Backup Server. 
> * Dodaj program vCenter do Azure Backup Server. 
> * Skonfiguruj grupę ochrony zawierającą maszyny wirtualne VMware, dla których chcesz utworzyć kopię zapasową, określ ustawienia kopii zapasowej i zaplanuj tworzenie kopii zapasowej.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Tworzenie bezpiecznego połączenia z serwerem vCenter

Domyślnie program Azure Backup Server się z serwerami VMware za pośrednictwem protokołu HTTPS. Aby skonfigurować połączenie HTTPS, pobierz certyfikat urzędu certyfikacji VMware i zaimportuj go na Azure Backup Server.

### <a name="set-up-the-certificate"></a>Konfigurowanie certyfikatu

1. W przeglądarce na maszynie Azure Backup Server wprowadź adres URL klienta sieci Web vSphere.

   > [!NOTE] 
   > Jeśli strona programu VMware **Wprowadzenie** nie jest wyświetlana, sprawdź ustawienia połączenia i serwera proxy przeglądarki, a następnie spróbuj ponownie.

1. Na stronie VMware **Wprowadzenie** wybierz pozycję Pobierz certyfikaty **zaufanego głównego urzędu certyfikacji.**

   :::image type="content" source="../backup/media/backup-azure-backup-server-vmware/vsphere-web-client.png" alt-text="Klient internetowy vSphere":::

1. Zapisz plik **download.zip** na maszynie Azure Backup Server, a następnie wyodrębnij jego zawartość do folderu **certs,** który zawiera:

   - Plik certyfikatu głównego z rozszerzeniem, który rozpoczyna się od numerowanych sekwencji, takich jak .0 i .1.
   - Plik listy CRL z rozszerzeniem, który rozpoczyna się od sekwencji, np. r0 lub r1.

1. W **folderze certs** kliknij prawym przyciskiem myszy plik certyfikatu głównego i wybierz **polecenie** Zmień nazwę, aby zmienić rozszerzenie na **crt.**

   Ikona pliku zmieni się na ikonę reprezentującą certyfikat główny.

1. Kliknij prawym przyciskiem myszy certyfikat główny, a następnie wybierz **polecenie Zainstaluj certyfikat.**

1. W **Kreatorze importu certyfikatów** wybierz **pozycję Komputer lokalny** jako miejsce docelowe certyfikatu, a następnie wybierz pozycję **Dalej.**

   ![Strona powitana kreatora](../backup/media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

   > [!NOTE] 
   > W razie monitu potwierdź, że chcesz zezwolić na zmiany na komputerze.

1. Wybierz **pozycję Umieść wszystkie certyfikaty w następującym magazynie, a** następnie wybierz pozycję **Przeglądaj,** aby wybrać magazyn certyfikatów.

   ![Magazyn certyfikatów](../backup/media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

1. Wybierz **Zaufane główne urzędy certyfikacji** jako folder docelowy, a następnie wybierz przycisk **OK.**

1. Przejrzyj ustawienia i wybierz pozycję **Zakończ,** aby rozpocząć importowanie certyfikatu.

   ![Sprawdź, czy certyfikat znajduje się we właściwym folderze](../backup/media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

1. Po potwierdzeniu importu certyfikatu zaloguj się do serwera vCenter, aby potwierdzić, że połączenie jest bezpieczne.

### <a name="enable-tls-12-on-azure-backup-server"></a>Włączanie obsługi TLS 1.2 na Azure Backup Server

Protokół TLS w programie VMware 6.7 lub jego wersji był włączony jako protokół komunikacyjny. 

1. Skopiuj następujące ustawienia rejestru i wklej je do Notatnika. Następnie zapisz plik jako TLS. Reg bez rozszerzenia txt.

   ```
   
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

1. Kliknij prawym przyciskiem myszy pozycję TLS. Reg pliku, a następnie wybierz **scalania** lub **Otwórz,** aby dodać ustawienia do rejestru.


## <a name="add-the-account-on-azure-backup-server"></a>Dodaj konto w Azure Backup Server

1. Otwórz Azure Backup Server, a następnie w konsoli Azure Backup Server wybierz pozycję Zarządzanie serwerami  >  **produkcyjnymi** Zarządzaj  >  **programem VMware.**

   ![Azure Backup Server konsoli programu](../backup/media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

1. W **oknie dialogowym Zarządzanie poświadczeniami** wybierz pozycję **Dodaj**.

   ![W oknie dialogowym Zarządzanie poświadczeniami wybierz pozycję Dodaj.](../backup/media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

1. W **oknie dialogowym Dodawanie** poświadczeń wprowadź nazwę i opis nowego poświadczenia. Określ nazwę użytkownika i hasło zdefiniowane na serwerze VMware.

   > [!NOTE] 
   > Jeśli serwer VMware i Azure Backup Server nie są w tej samej domenie, określ domenę w **polu Nazwa** użytkownika.

   ![Azure Backup Server dialogowe Dodawanie poświadczeń](../backup/media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

1. Wybierz **pozycję Dodaj,** aby dodać nowe poświadczenie.

   ![Zrzut ekranu przedstawia Azure Backup Server Zarządzanie poświadczeniami z wyświetlonymi nowymi poświadczeniami.](../backup/media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Dodaj serwer vCenter do Azure Backup Server

1. W konsoli Azure Backup Server wybierz pozycję **Zarządzanie**  >  **serwerami produkcyjnymi**  >  **Dodaj**.

   ![Otwieranie Kreatora dodatku serwera produkcyjnego](../backup/media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

1. Wybierz **pozycję Serwery VMware** i wybierz pozycję **Dalej.**

   ![Kreator dodatku serwera produkcyjnego](../backup/media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

1. Określ adres IP programu vCenter.

   ![Określanie serwera VMware](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

1. W polu **Port SSL** wprowadź port używany do komunikacji z programem vCenter.

   > [!TIP] 
   > Port 443 jest portem domyślnym, ale można go zmienić, jeśli program vCenter nasłuchuje na innym porcie.

1. W **polu Określ poświadczenia** wybierz poświadczenia utworzone w poprzedniej sekcji.

1. Wybierz **pozycję Dodaj,** aby dodać program vCenter do listy serwerów, a następnie wybierz pozycję **Dalej.**

   ![Dodawanie serwera VMware i poświadczeń](../backup/media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

1. Na stronie **Podsumowanie** wybierz pozycję **Dodaj,** aby dodać program vCenter do Azure Backup Server.

   Nowy serwer jest dodawany natychmiast. VCenter nie potrzebuje agenta.

   ![Dodawanie serwera VMware do Azure Backup Server](../backup/media/backup-azure-backup-server-vmware/tasks-screen.png)

1. Na stronie **Finish** (Zakończ) przejrzyj ustawienia, a następnie wybierz pozycję **Close (Zamknij).**

   ![Strona zakończenia](../backup/media/backup-azure-backup-server-vmware/summary-screen.png)

   Serwer vCenter zostanie wyświetlony w obszarze **Serwer produkcyjny** z:
   - Wpisz jako **VMware Server** 
   - Stan agenta : **OK** 
   
      Jeśli stan agenta **jest nieznany,** **wybierz** pozycję **Odśwież.**

## <a name="configure-a-protection-group"></a>Konfigurowanie grupy ochrony

Grupy ochrony zbierają wiele maszyn wirtualnych i stosują te same ustawienia przechowywania danych i kopii zapasowych do wszystkich maszyn wirtualnych w grupie.

1. W konsoli Azure Backup Server wybierz pozycję **Ochrona**  >  **Nowy.**

   ![Otwórz kreatora Tworzenie nowej grupy ochrony](../backup/media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na stronie **powitaowej Kreatora tworzenia nowej grupy ochrony** wybierz pozycję **Dalej.**

   ![Okno dialogowe Kreatora tworzenia nowej grupy ochrony](../backup/media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na stronie **Wybierz typ grupy ochrony** wybierz pozycję **Serwery,** a następnie wybierz pozycję **Dalej.** Zostanie **wyświetlona strona Wybierz członków** grupy.

1. Na stronie **Wybieranie członków grupy** wybierz maszyny wirtualne (lub foldery maszyn wirtualnych), których kopię zapasową chcesz wrócić, a następnie wybierz przycisk **Dalej.**

   > [!NOTE]
   > Po wybraniu folderu lub maszyn wirtualnych foldery w tym folderze są również wybierane do kopii zapasowej. Możesz usunąć zaznaczenie folderów lub maszyn wirtualnych, dla których nie chcesz wrócić do kopii zapasowej. Jeśli kopię zapasową maszyny wirtualnej lub folderu jest już tworzona, nie można jej wybrać, co gwarantuje, że nie utworzono zduplikowanych punktów odzyskiwania dla maszyny wirtualnej.

   ![Wybieranie członków grupy](../backup/media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na stronie **Wybierz metodę ochrony danych** wprowadź nazwę grupy ochrony i ustawienia ochrony. 

1. Ustaw ochronę krótkoterminową na **Dysk,** włącz ochronę w trybie online, a następnie wybierz pozycję **Dalej.**

   ![Wybieranie metody ochrony danych](../backup/media/backup-azure-backup-server-vmware/name-protection-group.png)

1. Określ, jak długo chcesz przechowywać kopię zapasową danych na dysku.

   - **Zakres przechowywania:** liczba dni przechowywania punktów odzyskiwania dysku.
   - **Ekspresowa pełna kopia zapasowa:** jak często są podejmowane punkty odzyskiwania dysku. Aby zmienić godziny lub daty tworzenia krótkoterminowych kopii zapasowych, wybierz pozycję **Modyfikuj**.

   :::image type="content" source="media/azure-vmware-solution-backup/new-protection-group-specify-short-term-goals.png" alt-text="Określanie krótkoterminowych celów ochrony opartej na dyskach":::

1. Na stronie **Review Disk Storage Allocation (Przeglądanie** alokacji zasobów) przejrzyj miejsce na dysku dla kopii zapasowych maszyn wirtualnych.

   - Zalecane przydziały dysków są oparte na określonym zakresie przechowywania, typie obciążenia i rozmiarze chronionych danych. Określ wymagane zmiany, a następnie wybierz pozycję **Dalej.**
   - **Rozmiar danych:** Rozmiar danych w grupie ochrony.
   - **Miejsce na dysku:** Zalecana ilość miejsca na dysku dla grupy ochrony. Jeśli chcesz zmodyfikować to ustawienie, wybierz miejsce nieco większe niż szacowana wielkość poszczególnych źródeł danych.
   - **Szczegóły puli magazynów:** Przedstawia stan puli magazynów, w tym całkowity i pozostały rozmiar dysku.

   :::image type="content" source="media/azure-vmware-solution-backup/review-disk-allocation.png" alt-text="Przejrzyj miejsce na dysku podane w puli magazynów":::

   > [!NOTE]
   > W niektórych scenariuszach zgłaszany rozmiar danych jest większy niż rzeczywisty rozmiar maszyny wirtualnej. Wiemy o problemie i obecnie go badamy.

1. Na stronie **Wybieranie metody tworzenia repliki** wskaż sposób tworzenia początkowej kopii zapasowej, a następnie wybierz pozycję **Dalej.**

   - Wartość domyślna to **Automatycznie za pośrednictwem sieci i** **Teraz.** Jeśli używasz wartości domyślnej, określ czas poza szczytem. W przypadku wybrania **opcji Później** określ dzień i czas.
   - W przypadku dużych ilości danych lub mniej niż optymalnych warunków sieciowych należy rozważyć replikowanie danych w trybie offline przy użyciu nośników wymiennych.

   ![Wybieranie metody tworzenia repliki](../backup/media/backup-azure-backup-server-vmware/replica-creation.png)

1. W **przypadku opcji sprawdzania spójności** wybierz sposób i czas automatyzacji sprawdzania spójności, a następnie wybierz pozycję **Dalej.**

   - Sprawdzanie spójności można przeprowadzać, gdy dane repliki stają się niespójne lub zgodnie z ustawionym harmonogramem.
   - Jeśli nie chcesz konfigurować automatycznego sprawdzania spójności, możesz uruchomić sprawdzanie ręczne, klikając prawym przyciskiem myszy grupę ochrony **Wykonaj sprawdzanie spójności.**

1. Na stronie **Określanie danych ochrony online** wybierz foldery maszyn wirtualnych lub maszyn wirtualnych, których kopię zapasową chcesz wrócić, a następnie wybierz przycisk **Dalej.** 

   > [!TIP]
   > Możesz wybrać członków indywidualnie lub wybrać **pozycję Zaznacz wszystko,** aby wybrać wszystkich członków.

   ![Określanie danych ochrony online](../backup/media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych** online wskaż, jak często chcesz tworzyć kopie zapasowe danych z magazynu lokalnego na platformie Azure. 

   - Punkty odzyskiwania w chmurze dla danych, które mają być generowane zgodnie z harmonogramem. 
   - Po wygenerowaniu punktu odzyskiwania jest on następnie przesyłany do magazynu usługi Recovery Services na platformie Azure.

   ![Określanie harmonogramu tworzenia kopii zapasowych online](../backup/media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na stronie **Określanie zasad przechowywania w** trybie online wskaż, jak długo punkty odzyskiwania utworzone na podstawie kopii zapasowych mają być zachowywane na platformie Azure.

   - Nie ma limitu czasu przechowywania danych na platformie Azure.
   - Jedynym ograniczeniem jest to, że nie można mieć więcej niż 9999 punktów odzyskiwania na chronione wystąpienie. W tym przykładzie chronionym wystąpieniem jest serwer VMware.

   ![Określanie zasad przechowywania w trybie online](../backup/media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na **stronie Podsumowanie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz grupę.**

   ![Członek grupy ochrony i podsumowanie ustawień](../backup/media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="monitor-with-the-azure-backup-server-console"></a>Monitorowanie za pomocą konsoli Azure Backup Server aplikacji

Po skonfigurowaniu grupy ochrony do tworzenia kopii zapasowych Azure VMware Solution wirtualnych można monitorować stan zadania tworzenia kopii zapasowej i alertu przy użyciu konsoli Azure Backup Server wirtualnej. Oto co można monitorować.

- W obszarze **zadań** Monitorowanie:
   - W **obszarze** Alerty możesz monitorować błędy, ostrzeżenia i informacje ogólne.  Możesz wyświetlać aktywne i nieaktywne alerty oraz skonfigurować powiadomienia e-mail.
   - W **obszarze** Zadania można wyświetlić zadania uruchomione przez Azure Backup Server dla określonego chronionego źródła danych lub grupy ochrony. Możesz śledzić postęp zadania lub sprawdzać zasoby używane przez zadania.
- W obszarze **zadania** Ochrona można sprawdzić stan woluminów i udziałów w grupie ochrony. Możesz również sprawdzić ustawienia konfiguracji, takie jak ustawienia odzyskiwania, alokacja dysku i harmonogram tworzenia kopii zapasowych.
- W obszarze **zadań** Zarządzanie można wyświetlić karty **Dyski,** Online i Agenci, aby sprawdzić stan dysków w puli magazynów, rejestrację na platformie Azure i stan wdrożonego agenta programu DPM. 

:::image type="content" source="media/azure-vmware-solution-backup/monitor-backup-jobs.png" alt-text="Monitorowanie stanu zadań tworzenia kopii zapasowej w Azure Backup Server":::

## <a name="restore-vmware-virtual-machines"></a>Przywracanie maszyn wirtualnych VMware

W Azure Backup Server konsola administratora istnieją dwa sposoby znalezienia danych, które można odzyskać. Możesz wyszukiwać i przeglądać. Podczas odzyskiwania danych możesz chcieć przywrócić dane lub maszynę wirtualną do tej samej lokalizacji. Z tego powodu program Azure Backup Server trzy opcje odzyskiwania dla kopii zapasowych maszyn wirtualnych VMware:

- **Odzyskiwanie oryginalnej lokalizacji (OLR): użyj OLR,** aby przywrócić chronioną maszynę wirtualną do jej oryginalnej lokalizacji. Maszynę wirtualną można przywrócić do oryginalnej lokalizacji tylko wtedy, gdy od momentu tworzenia kopii zapasowej nie dodano ani nie usunięto żadnych dysków. Jeśli dyski zostały dodane lub usunięte, należy użyć odzyskiwania lokalizacji alternatywnej.
- **Odzyskiwanie lokalizacji alternatywnej (ALR):** użyj metody , gdy brakuje oryginalnej maszyny wirtualnej lub nie chcesz zakłócać oryginalnej maszyny wirtualnej. Podaj lokalizację hosta ESXi, puli zasobów, folderu oraz magazynu danych i ścieżki magazynu. Aby ułatwić odróżnienie przywróconej maszyny wirtualnej od oryginalnej maszyny wirtualnej, Azure Backup Server dołącza do nazwy maszyny wirtualnej tekst *"-Recovered".*
- **Odzyskiwanie poszczególnych lokalizacji plików (ILR):** jeśli chroniona maszyna wirtualna jest maszyną wirtualną z systemem Windows Server, poszczególne pliki lub foldery na maszynie wirtualnej można odzyskać za pomocą funkcji ILR Azure Backup Server. Aby odzyskać poszczególne pliki, zapoznaj się z procedurą w dalszej części tego artykułu. Przywracanie pojedynczego pliku z maszyny wirtualnej jest dostępne tylko dla maszyny wirtualnej z systemem Windows i punktów odzyskiwania dysku.

### <a name="restore-a-recovery-point"></a>Przywracanie punktu odzyskiwania

1. W Azure Backup Server konsola administratora wybierz **widok** Odzyskiwanie. 

1. Korzystając z **okienka** Przeglądaj, przeglądaj lub filtruj, aby znaleźć maszynę wirtualną, którą chcesz odzyskać. Po wybraniu maszyny wirtualnej lub folderu w okienku **Punkty odzyskiwania są wyświetlane dostępne punkty odzyskiwania.

   ![Dostępne punkty odzyskiwania](../backup/media/restore-azure-backup-server-vmware/recovery-points.png)

1. W **okienku Punkty odzyskiwania** dla wybierz datę rozpoczęcia korzystania z punktu odzyskiwania. Daty kalendarza pogrubioną czcionką mają dostępne punkty odzyskiwania. Alternatywnie możesz kliknąć prawym przyciskiem myszy maszynę wirtualną i wybrać pozycję **Pokaż** wszystkie punkty odzyskiwania, a następnie wybrać punkt odzyskiwania z listy.

   > [!NOTE] 
   > Aby uzyskać krótkoterminową ochronę, wybierz punkt odzyskiwania oparty na dysku, aby przyspieszyć odzyskiwanie. Po wygaśnięciu krótkoterminowych punktów odzyskiwania będą dostępne tylko punkty **odzyskiwania online** do odzyskania.

1. Przed odzyskaniem z punktu odzyskiwania online upewnij się, że lokalizacja przemieszczania zawiera wystarczająco dużo wolnego miejsca do przechowywania pełnego nieskompresowanych rozmiarów maszyny wirtualnej, którą chcesz odzyskać. Lokalizację przemieszczania można wyświetlić lub zmienić, uruchamiając **Kreatora konfigurowania ustawień subskrypcji.**

   :::image type="content" source="media/azure-vmware-solution-backup/mabs-recovery-folder-settings.png" alt-text="Azure Backup Server folderu odzyskiwania":::

1. Wybierz **pozycję Odzyskaj,** aby otworzyć Kreatora **odzyskiwania.**

   ![Kreator odzyskiwania, strona Przegląd wyboru odzyskiwania](../backup/media/restore-azure-backup-server-vmware/recovery-wizard.png)

1. Wybierz **przycisk** Dalej, aby przejść do **ekranu Określanie opcji** odzyskiwania. Wybierz **ponownie pozycję** Dalej, aby przejść do ekranu Wybieranie **typu** odzyskiwania. 

   > [!NOTE]
   > Obciążenia VMware nie obsługują włączania ograniczania przepustowości sieci.

1. Na stronie **Wybierz typ odzyskiwania** odzyskaj do oryginalnego wystąpienia lub nowej lokalizacji.

   - W przypadku wybrania **opcji Odzyskaj** do oryginalnego wystąpienia nie trzeba wybierać więcej opcji w kreatorze. Używane są dane dla oryginalnego wystąpienia.
   - Jeśli wybierzesz **opcję Odzyskaj** jako maszynę wirtualną na dowolnym hoście, na ekranie Określ lokalizację docelową podaj informacje dotyczące hosta **ESXi,** puli **zasobów,** **folderu** i **ścieżki**. 

   ![Wybieranie strony Typ odzyskiwania](../backup/media/restore-azure-backup-server-vmware/recovery-type.png)

1. Na stronie **Podsumowanie** przejrzyj ustawienia i wybierz pozycję **Odzyskaj,** aby rozpocząć proces odzyskiwania. 

   Ekran **Stan** odzyskiwania przedstawia postęp operacji odzyskiwania.

### <a name="restore-an-individual-file-from-a-vm"></a>Przywracanie pojedynczego pliku z maszyny wirtualnej

Poszczególne pliki można przywrócić z chronionego punktu odzyskiwania maszyny wirtualnej. Ta funkcja jest dostępna tylko dla maszyn wirtualnych z systemem Windows Server. Przywracanie poszczególnych plików jest podobne do przywracania całej maszyny wirtualnej, z wyjątkiem tego, że przeglądasz zestaw VMDK i znajdujesz pliki przed rozpoczęciem procesu odzyskiwania. 

> [!NOTE]
> Przywracanie pojedynczego pliku z maszyny wirtualnej jest dostępne tylko dla maszyny wirtualnej z systemem Windows i punktów odzyskiwania dysku.

1. W Azure Backup Server konsola administratora wybierz **widok** Odzyskiwanie.

1. Korzystając z **okienka** Przeglądaj, przeglądaj lub filtruj, aby znaleźć maszynę wirtualną, którą chcesz odzyskać. Po wybraniu maszyny wirtualnej lub folderu w okienku **Punkty odzyskiwania są wyświetlane dostępne punkty odzyskiwania.

   ![Dostępne punkty odzyskiwania](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

1. W **okienku Punkty odzyskiwania** dla użyj kalendarza, aby wybrać datę zawierającą odpowiednie punkty odzyskiwania. W zależności od tego, jak skonfigurowano zasady tworzenia kopii zapasowych, daty mogą mieć więcej niż jeden punkt odzyskiwania. 

1. Po wybraniu dnia, w którym został podjęty punkt odzyskiwania, upewnij się, że jest wybierany prawidłowy **czas odzyskiwania**. 

   > [!NOTE]
   > Jeśli wybrana data ma wiele punktów odzyskiwania, wybierz punkt  odzyskiwania, wybierając go z menu rozwijanego Czas odzyskiwania. 

   Po wybraniu punktu odzyskiwania lista elementów, które można odzyskać, zostanie wyświetlona w **okienku** Ścieżka.

1. Aby znaleźć pliki do odzyskania, w okienku **Ścieżka** kliknij dwukrotnie element w kolumnie **Element** do odzyskania, aby go otworzyć. Następnie wybierz plik lub foldery, które chcesz odzyskać. Aby wybrać wiele elementów, naciśnij klawisz **Ctrl** podczas zaznaczania każdego elementu. Użyj **okienka Ścieżka,** aby przeszukać listę plików lub folderów wyświetlanych w kolumnie **Element do odzyskania.**
    
   > [!NOTE]
   > **Lista wyszukiwania poniżej** nie wyszukuje podfolderów. Aby przeszukać podfoldery, kliknij dwukrotnie folder . Użyj **przycisku W** górę, aby przejść z folderu podrzędnego do folderu nadrzędnego. Możesz wybrać wiele elementów (plików i folderów), ale muszą one znajdować się w tym samym folderze nadrzędnym. Nie można odzyskać elementów z wielu folderów w tym samym zadaniu odzyskiwania.

   ![Przeglądanie wyboru odzyskiwania](../backup/media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

1. Po wybraniu elementów do odzyskania na wstążce narzędzia konsola administratora  wybierz pozycję Odzyskaj, aby otworzyć **Kreatora odzyskiwania**. W **Kreatorze odzyskiwania** na **ekranie Przejrzyj wybór odzyskiwania** są zaznaczone elementy do odzyskania.

1. Na **ekranie Określanie opcji odzyskiwania** wykonaj jedną z następujących czynności:

   - Wybierz **pozycję Modyfikuj,** aby włączyć ograniczanie przepustowości sieci. W **oknie dialogowym** Ograniczanie przepustowości wybierz pozycję **Włącz ograniczanie** przepustowości sieci, aby je włączyć. Po włączeniu skonfiguruj ustawienia **i** **harmonogram pracy.**
   - Wybierz **przycisk Dalej,** aby pozostawić wyłączone ograniczanie przepustowości sieci.

1. Na **ekranie Wybierz typ odzyskiwania** wybierz pozycję **Dalej.** Pliki lub foldery można odzyskać tylko do folderu sieciowego.

1. Na **ekranie Określanie miejsca** docelowego wybierz **pozycję Przeglądaj,** aby znaleźć lokalizację sieciową plików lub folderów. Azure Backup Server folder, w którym kopiowane są wszystkie odzyskane elementy. Nazwa folderu ma prefiks MABS_day miesiąc-rok. Po wybraniu lokalizacji dla odzyskanych plików lub folderu zostaną podane szczegóły dla tej lokalizacji.

   ![Określanie lokalizacji do odzyskania plików](../backup/media/restore-azure-backup-server-vmware/specify-destination.png)

1. Na **ekranie Określanie opcji odzyskiwania** wybierz, które ustawienie zabezpieczeń ma być stosowane. Możesz zmodyfikować ograniczanie przepustowości sieci, ale ograniczenie jest domyślnie wyłączone. Ponadto odzyskiwanie **sieci SAN** **i powiadomienia** nie są włączone.

1. Na **ekranie Podsumowanie** przejrzyj ustawienia i wybierz pozycję **Odzyskaj,** aby rozpocząć proces odzyskiwania. Ekran **Stan** odzyskiwania przedstawia postęp operacji odzyskiwania.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak wrócić do kopii Azure VMware Solution wirtualnych za pomocą Azure Backup Server, możesz dowiedzieć się więcej na temat: 

- [Rozwiązywanie problemów podczas konfigurowania kopii zapasowych w Azure Backup Server](../backup/backup-azure-mabs-troubleshoot.md)
- [Zarządzanie cyklem życia maszyn Azure VMware Solution wirtualnych](lifecycle-management-of-azure-vmware-solution-vms.md)
