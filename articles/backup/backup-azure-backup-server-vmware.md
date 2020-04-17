---
title: Tworzenie kopii zapasowych maszyn wirtualnych VMware za pomocą serwera kopii zapasowych platformy Azure
description: W tym artykule dowiesz się, jak używać usługi Azure Backup Server do tworzenia kopii zapasowych maszyn wirtualnych VMware uruchomionych na serwerze VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 92846f9bb9259e55a2c957716676ff42c032b2b5
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537410"
---
# <a name="back-up-vmware-vms-with-azure-backup-server"></a>Tworzenie kopii zapasowych maszyn wirtualnych VMware za pomocą serwera kopii zapasowych platformy Azure

W tym artykule wyjaśniono, jak wykonać kopię zapasową maszyn wirtualnych VMware uruchomionych na platformie Azure przy użyciu serwera kopii zapasowych usługi Azure.

W tym artykule wyjaśniono, jak:

- Skonfiguruj bezpieczny kanał, aby serwer kopii zapasowej platformy Azure mógł komunikować się z serwerami VMware za pośrednictwem protokołu HTTPS.
- Skonfiguruj konto VMware używane przez usługę Azure Backup Server do uzyskiwania dostępu do serwera VMware.
- Dodaj poświadczenia konta do usługi Azure Backup.
- Dodaj serwer vCenter lub ESXi do serwera kopii zapasowej platformy Azure.
- Skonfiguruj grupę ochrony zawierającą maszyny wirtualne VMware, których kopię zapasową chcesz utworzyć, określ ustawienia kopii zapasowej i zaplanuj tworzenie kopii zapasowej.

## <a name="before-you-start"></a>Przed rozpoczęciem

- Sprawdź, czy korzystasz z wersji vCenter/ESXi, która jest obsługiwana do tworzenia kopii zapasowych. Zapoznaj się z macierzą wsparcia [tutaj](https://docs.microsoft.com/azure/backup/backup-mabs-protection-matrix).
- Upewnij się, że skonfigurowałeś usługę Azure Backup Server. Jeśli tego nie zrobiłeś, [zrób to](backup-azure-microsoft-azure-backup.md) przed rozpoczęciem. Powinieneś być uruchomiony usługi Azure Backup Server z najnowszymi aktualizacjami.

## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Tworzenie bezpiecznego połączenia z serwerem vCenter

Domyślnie serwer kopii zapasowej platformy Azure komunikuje się z serwerami VMware za pośrednictwem protokołu HTTPS. Aby skonfigurować połączenie HTTPS, pobierz certyfikat urzędu certyfikacji VMware (CA) i zaimportuj go na serwerze kopii zapasowej platformy Azure.

### <a name="before-you-begin"></a>Przed rozpoczęciem

- Jeśli nie chcesz używać protokołu HTTPS, możesz [wyłączyć sprawdzanie poprawności certyfikatu HTTPS dla wszystkich serwerów VMware](backup-azure-backup-server-vmware.md#disable-https-certificate-validation).
- Zazwyczaj łączysz się z przeglądarki na komputerze z serwerem kopii zapasowych platformy Azure z serwerem vCenter/ESXi przy użyciu klienta sieci Web vSphere. Przy pierwszym uruchomieniu połączenie nie jest bezpieczne i wyświetli się następujące czynności.
- Ważne jest, aby zrozumieć, jak usługa Azure Backup Server obsługuje kopie zapasowe.
  - W pierwszym kroku serwer kopii zapasowej platformy Azure kopiuje kopie zapasowe danych w magazynie dysku lokalnego. Usługa Azure Backup Server używa puli magazynu, zestawu dysków i woluminów, na których serwer Azure Backup Server przechowuje punkty odzyskiwania dysku dla chronionych danych. Pulę pamięci masowej można bezpośrednio podłączyć do magazynu (DAS), kanał światłowodowy SAN lub urządzenie pamięci masowej iSCSI lub sieć SAN. Ważne jest, aby upewnić się, że masz wystarczającą ilość miejsca do lokalnej kopii zapasowej danych maszyny Wirtualnej VMware.
  - Usługa Azure Backup Server następnie kopie zapasowe z magazynu dysków lokalnych na platformie Azure.
  - [Uzyskaj pomoc,](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1807#figure-out-how-much-storage-space-you-need) aby dowiedzieć się, ile miejsca potrzebujesz. Informacje są przeznaczone dla programu DPM, ale mogą być używane również dla usługi Azure Backup Server.

### <a name="set-up-the-certificate"></a>Konfigurowanie certyfikatu

Skonfiguruj bezpieczny kanał w następujący sposób:

1. W przeglądarce na serwerze kopii zapasowej platformy Azure wprowadź adres URL klienta sieci Web vSphere. Jeśli strona logowania nie jest wyświetlana, sprawdź ustawienia połączenia i serwera proxy przeglądarki.

    ![Klient sieci Web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

2. Na stronie logowania klienta sieci Web vSphere kliknij pozycję **Pobierz zaufane główne certyfikaty urzędu certyfikacji**.

    ![Pobieranie zaufanego głównego certyfikatu urzędu certyfikacji](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

3. Plik o nazwie **pobieranie** jest pobierany. W zależności od przeglądarki zostanie wyświetlony komunikat z pytaniem, czy otworzyć lub zapisać plik.

    ![Pobieranie certyfikatu urzędu certyfikacji](./media/backup-azure-backup-server-vmware/download-certs.png)

4. Zapisz plik na komputerze z serwerem kopii zapasowych platformy Azure z rozszerzeniem zip.

5. Kliknij prawym przyciskiem myszy **download.zip** > **Wyodrębnij wszystko**. Plik zip wyodrębnia jego zawartość do folderu **certyfikatów,** który zawiera:
   - Plik certyfikatu głównego z rozszerzeniem, który zaczyna się od sekwencji numerowanych, takich jak .0 i .1.
   - Plik CRL ma rozszerzenie, które rozpoczyna się od sekwencji, takiej jak .r0 lub r1. Plik CRL jest skojarzony z certyfikatem.

    ![Pobrane certyfikaty](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

6. W folderze **certyfikatów** kliknij prawym przyciskiem myszy plik certyfikatu głównego > **Zmień nazwę**.

    ![Zmienianie nazwy certyfikatu głównego](./media/backup-azure-backup-server-vmware/rename-cert.png)

7. Zmień rozszerzenie certyfikatu głównego na .crt i potwierdź. Ikona pliku zmienia się na ikonę reprezentującą certyfikat główny.

8. Kliknij prawym przyciskiem myszy certyfikat główny i z wyskakującego menu wybierz polecenie **Zainstaluj certyfikat**.

9. W **Kreatorze importu certyfikatów**wybierz pozycję **Komputer lokalny** jako miejsce docelowe certyfikatu, a następnie kliknij przycisk **Dalej**. Potwierdź, jeśli pojawi się pytanie, czy chcesz zezwolić na zmiany na komputerze.

    ![Kreator — zapraszamy](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

10. Na stronie **Sklep z certyfikatami** wybierz pozycję **Umieść wszystkie certyfikaty w następującym magazynie,** a następnie kliknij przycisk **Przeglądaj,** aby wybrać magazyn certyfikatów.

    ![Przechowywanie certyfikatów](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

11. W **obszarze Wybierz Magazyn certyfikatów**wybierz pozycję **Zaufane główne urzędy certyfikacji** jako folder docelowy certyfikatów, a następnie kliknij przycisk **OK**.

    ![Folder docelowy certyfikatu](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

12. W **obszarze Kończenie Kreatora importu certyfikatów**sprawdź folder, a następnie kliknij przycisk **Zakończ**.

    ![Sprawdź, czy certyfikat znajduje się w odpowiednim folderze](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

13. Po potwierdzeniu importu certyfikatu zaloguj się do serwera vCenter, aby potwierdzić, że połączenie jest bezpieczne.

### <a name="disable-https-certificate-validation"></a>Wyłącz sprawdzanie poprawności certyfikatu HTTPS

Jeśli masz bezpieczne granice w organizacji i nie chcesz używać protokołu HTTPS między serwerami VMware a komputerem usługi Azure Backup Server, wyłącz protokół HTTPS w następujący sposób:

1. Skopiuj i wklej następujący tekst do pliku txt.

    ```text
    Windows Registry Editor Version 5.00
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
    "IgnoreCertificateValidation"=dword:00000001
    ```

2. Zapisz plik na komputerze z serwerem kopii zapasowych platformy Azure o nazwie **DisableSecureAuthentication.reg**.

3. Kliknij dwukrotnie plik, aby aktywować wpis rejestru.

## <a name="create-a-vmware-role"></a>Tworzenie roli VMware

Serwer kopii zapasowej platformy Azure potrzebuje konta użytkownika z uprawnieniami dostępu do hosta v-Center Server/ESXi. Utwórz rolę VMware z określonymi uprawnieniami, a następnie skojarz konto użytkownika z tą rolą.

1. Zaloguj się do serwera vCenter (lub hosta ESXi, jeśli nie używasz serwera vCenter).
2. W panelu **Nawigator** kliknij pozycję **Administracja**.

    ![Administracja](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

3. W **obszarze Role administracyjne** > **Roles**kliknij ikonę dodaj rolę (symbol +).

    ![Dodaj rolę](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

4. W **obszarze Utwórz** > **nazwę roli**wprowadź *pozycję BackupAdminRole*. Nazwa roli może być, co chcesz, ale powinna być rozpoznawalna dla celu roli.

5. Wybierz uprawnienia zgodnie z podsumowaniem w poniższej tabeli, a następnie kliknij przycisk **OK**.  Nowa rola pojawi się na liście w panelu **Role.**
   - Kliknij ikonę obok etykiety nadrzędnej, aby rozwinąć element nadrzędny i wyświetlić uprawnienia podrzędne.
   - Aby wybrać uprawnienia VirtualMachine, należy przejść kilka poziomów do nadrzędnej hierarchii podrzędnej.
   - Nie musisz wybierać wszystkich uprawnień podrzędnych w ramach uprawnień nadrzędnych.

    ![Nadrzędna hierarchia uprawnień podrzędnych](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

### <a name="role-permissions"></a>Uprawnienia roli

| Uprawnienia dla konta użytkownika vCenter 6.7                     | Uprawnienia dla konta użytkownika vCenter 6.5                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Klaster magazynu danych. Konfigurowanie klastra magazynu danych            | Klaster magazynu danych. Konfigurowanie klastra magazynu danych            |
| Datastore.AllocateSpace                                      | Datastore.AllocateSpace                                      |
| Datastore.Browse magazyn danych                                   | Datastore.Browse magazyn danych                                   |
| Datastore.Operacje na plikach niskiego poziomu                          | Datastore.Operacje na plikach niskiego poziomu                          |
| Global.Disable metody                                       | Global.Disable metody                                       |
| Global.Enable metody                                        | Global.Enable metody                                        |
| Global.Licenses                                              | Global.Licenses                                              |
| Zdarzenie Global.Log                                             | Zdarzenie Global.Log                                             |
| Global.Manage atrybuty niestandardowe                              | Global.Manage atrybuty niestandardowe                              |
| Atrybut niestandardowy Global.Set                                  | Atrybut niestandardowy Global.Set                                  |
| Host.Local operacji. Tworzenie maszyny wirtualnej                | Host.Local operacji. Tworzenie maszyny wirtualnej                |
| Sieć.Przypisywanie sieci                                       | Sieć.Przypisywanie sieci                                       |
| Zasobów. Przypisywanie maszyny wirtualnej do puli zasobów           | Zasobów. Przypisywanie maszyny wirtualnej do puli zasobów           |
| vApp.Dodaj maszynę wirtualną                                     | vApp.Dodaj maszynę wirtualną                                     |
| vApp.Assign puli zasobów                                    | vApp.Assign puli zasobów                                    |
| vApp.Wyrejestrowanie                                              | vApp.Wyrejestrowanie                                              |
| VirtualMachine.Configuration. Dodawanie lub usuwanie urządzenia          | VirtualMachine.Configuration. Dodawanie lub usuwanie urządzenia          |
| Maszyna wirtualna. Configuration.Acquire dzierżawa dysku            | Maszyna wirtualna. Configuration.Disk lease                     |
| Maszyna wirtualna. Configuration.Add new disk Configuration.Add new disk Configuration.Add new disk                   | Maszyna wirtualna. Configuration.Add new disk Configuration.Add new disk Configuration.Add new disk                   |
| Maszyna wirtualna. Konfiguracja.Konfiguracja zaawansowana        | Maszyna wirtualna. Konfiguracja.Zaawansowane                       |
| Maszyna wirtualna. Configuration.Toggle śledzenie zmian dysku   | Maszyna wirtualna. Śledzenie zmian w konfiguracji.Dysk          |
| Maszyna wirtualna. Configuration.Configure Host USB device Configuration.Configure Host USB device Configuration.Configure     | Maszyna wirtualna. Configuration.Host urządzenie USB               |
| Maszyna wirtualna. Configuration.Extend virtual disk Configuration.Extend virtual disk Configuration.Extend virtual disk Configuration.           | Maszyna wirtualna. Configuration.Extend virtual disk Configuration.Extend virtual disk Configuration.Extend virtual disk Configuration.           |
| Maszyna wirtualna. Configuration.Query nieuwzdzonych plików           | Maszyna wirtualna. Configuration.Query nieuwzdzonych plików           |
| Maszyna wirtualna. Miejsce umieszczania pliku swap.Change     | Maszyna wirtualna. Configuration.Swapfile umiejscowienie            |
| Maszyna wirtualna. Wykonanie programu operacji gość.gość | Maszyna wirtualna. Wykonanie programu operacji gość.gość |
| Maszyna wirtualna. Modyfikacje operacji gościa.gość | Maszyna wirtualna. Modyfikacje operacji gościa.gość |
| Maszyna wirtualna. Zapytania operacji gościa.gość    | Maszyna wirtualna. Zapytania operacji gościa.gość    |
| Maszyna wirtualna . Interakcji. Połączenie urządzenia             | Maszyna wirtualna . Interakcji. Połączenie urządzenia             |
| Maszyna wirtualna . Interakcji. Zarządzanie systemem operacyjnym dla gości przez VIX API | Maszyna wirtualna . Interakcji. Zarządzanie systemem operacyjnym dla gości przez VIX API |
| Maszyna wirtualna . Interakcji. Wyłącz zasilanie                      | Maszyna wirtualna . Interakcji. Wyłącz zasilanie                      |
| Maszyna wirtualna . Zapasy.Tworzenie nowych                        | Maszyna wirtualna . Zapasy.Tworzenie nowych                        |
| Maszyna wirtualna . Zapasy.Usuń                            | Maszyna wirtualna . Zapasy.Usuń                            |
| Maszyna wirtualna . Inventory.Register                          | Maszyna wirtualna . Inventory.Register                          |
| Maszyna wirtualna . Inicjowanie obsługi administracyjnej.Zezwalaj na dostęp do dysku             | Maszyna wirtualna . Inicjowanie obsługi administracyjnej.Zezwalaj na dostęp do dysku             |
| Maszyna wirtualna . Inicjowanie obsługi administracyjnej.Zezwalaj na dostęp do plików             | Maszyna wirtualna . Inicjowanie obsługi administracyjnej.Zezwalaj na dostęp do plików             |
| Maszyna wirtualna . Inicjowanie obsługi administracyjnej.Zezwalaj na dostęp do dysku tylko do odczytu   | Maszyna wirtualna . Inicjowanie obsługi administracyjnej.Zezwalaj na dostęp do dysku tylko do odczytu   |
| Maszyna wirtualna . Inicjowanie obsługi administracyjnej.Zezwalaj na pobieranie maszyny wirtualnej | Maszyna wirtualna . Inicjowanie obsługi administracyjnej.Zezwalaj na pobieranie maszyny wirtualnej |
| Maszyna wirtualna . Zarządzanie migawkami.  Tworzenie migawki       | Maszyna wirtualna . Zarządzanie migawkami.  Tworzenie migawki       |
| Maszyna wirtualna . Zarządzanie migawkami. Usuń migawkę        | Maszyna wirtualna . Zarządzanie migawkami. Usuń migawkę        |
| Maszyna wirtualna . Zarządzanie migawkami. Powrót do migawki     | Maszyna wirtualna . Zarządzanie migawkami. Powrót do migawki     |

<br>

| **Uprawnienia dla konta użytkownika vCenter 6.0**                | **Uprawnienia dla konta użytkownika vCenter 5.5** |
| ---------------------------------------------------------- | ------------------------------------------- |
| Datastore.AllocateSpace                                    | Sieć.Przypisywanie                              |
| Global.Manage atrybuty niestandardowe                           | Datastore.AllocateSpace                     |
| Atrybut niestandardowy Global.Set                               | VirtualMachine.Config.ChangeTracking        |
| Host.Local operacji. Tworzenie maszyny wirtualnej              | VirtualMachine.State.RemoveSnapshot         |
| Sieci.  Przypisywanie sieci                                   | VirtualMachine.State.CreateSnapshot         |
| Zasobów.  Przypisywanie maszyny wirtualnej do puli zasobów         | VirtualMachine.provisioning.DiskRandomRead  |
| Maszyna wirtualna. Configuration.Add new disk Configuration.Add new disk Configuration.Add new disk                | VirtualMachine.Interact.PowerOff            |
| Maszyna wirtualna. Konfiguracja.Zaawansowane                    | VirtualMachine.Inventory.Create             |
| Maszyna wirtualna. Śledzenie zmian w konfiguracji.Dysk        | VirtualMachine.Config.AddNewDisk            |
| Maszyna wirtualna. Configuration.Host urządzenie USB             | VirtualMachine.Config.HostUSBDevice         |
| Maszyna wirtualna. Configuration.Query nieuwzdzonych plików         | VirtualMachine.Config.AdvancedConfig        |
| Maszyna wirtualna. Configuration.Swapfile umiejscowienie          | VirtualMachine.Config.SwapPlacement         |
| Maszyna wirtualna. Interaction.Power Off                     | Global.ManageCustomFields                   |
| Maszyna wirtualna. Zapasów. Tworzenie nowego elementu                     |                                             |
| Maszyna wirtualna. Inicjowanie obsługi administracyjnej.Zezwalaj na dostęp do dysku            |                                             |
| Maszyna wirtualna. Inicjowania obsługi. Zezwalaj na dostęp do dysku tylko do odczytu |                                             |
| Maszyna wirtualna. Zarządzanie migawkami. Tworzenie migawki       |                                             |
| Maszyna wirtualna. Zarządzanie migawkami. Usuń migawkę       |                                             |

## <a name="create-a-vmware-account"></a>Tworzenie konta VMware

1. W panelu **nawigator** serwera vCenter kliknij pozycję **Użytkownicy i grupy**. Jeśli nie używasz serwera vCenter, utwórz konto na odpowiednim hoście ESXi.

    ![Opcja Użytkownicy i grupy](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Pojawi się panel **Użytkownicy i grupy vCenter.**

2. W panelu **Użytkownicy i grupy vCenter** wybierz kartę **Użytkownicy,** a następnie kliknij ikonę dodaj użytkowników (symbol +).

    ![Panel Użytkownicy i grupy vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

3. W oknie dialogowym **Nowy użytkownik** dodaj informacje o użytkowniku > **OK**. W tej procedurze nazwa użytkownika jest BackupAdmin.

    ![Okno dialogowe Nowy użytkownik](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

4. Aby skojarzyć konto użytkownika z rolą, w panelu **Nawigator** kliknij pozycję **Uprawnienia globalne**. W panelu **Uprawnienia globalne** wybierz kartę **Zarządzaj,** a następnie kliknij ikonę dodaj (symbol +).

    ![Panel Uprawnienia globalne](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

5. W **obszarze Główny uprawnienia globalne — dodaj uprawnienie**kliknij przycisk **Dodaj,** aby wybrać użytkownika lub grupę.

    ![Wybieranie użytkownika lub grupy](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

6. W **obszarze Wybierz użytkowników/grupy**wybierz pozycję **BackupAdmin** > **Add**. W **obszarze Użytkownicy**format *domeny\nazwa_użytkownika* jest używany dla konta użytkownika. Jeśli chcesz użyć innej domeny, wybierz ją z listy **Domeny.** Kliknij **przycisk OK,** aby dodać wybranych użytkowników do okna dialogowego **Dodawanie uprawnień.**

    ![Dodaj użytkownika BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

7. Z listy rozwijanej **"Przypisana rola"** wybierz pozycję **BackupAdminRole** > **OK**.

    ![Przypisywanie użytkownika do roli](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

Na karcie **Zarządzanie** w panelu **Uprawnienia globalne** na liście pojawi się nowe konto użytkownika i skojarzona rola.

## <a name="add-the-account-on-azure-backup-server"></a>Dodawanie konta na serwerze kopii zapasowych platformy Azure

1. Otwórz serwer kopii zapasowej platformy Azure. Jeśli nie możesz znaleźć ikony na pulpicie, otwórz usługę Kopia zapasowa platformy Microsoft Azure z listy aplikacji.

    ![Ikona serwera kopii zapasowych platformy Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

2. W konsoli serwera kopii zapasowych platformy Azure kliknij pozycję Zarządzanie**serwerami** > produkcyjnymi **zarządzania** >  **oprogramowaniem VMware**.

    ![Konsola serwera kopii zapasowych platformy Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

3. W oknie dialogowym **Zarządzanie poświadczeniami** kliknij pozycję **Dodaj**.

    ![Okno dialogowe Zarządzanie poświadczeniami serwera kopii zapasowych platformy Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

4. W **polu Dodaj poświadczenia**wprowadź nazwę i opis nowego poświadczenia oraz określ nazwę użytkownika i hasło zdefiniowane na serwerze VMware. Nazwa, *poświadczenie Contoso Vcenter* służy do identyfikowania poświadczeń w tej procedurze. Jeśli serwer VMware i serwer kopii zapasowej platformy Azure nie są w tej samej domenie, określ domenę w nazwie użytkownika.

    ![Okno dialogowe Dodawanie poświadczeń serwera kopii zapasowej platformy Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

5. Kliknij **przycisk Dodaj,** aby dodać nowe poświadczenia.

    ![Okno dialogowe Zarządzanie poświadczeniami serwera kopii zapasowych platformy Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

## <a name="add-the-vcenter-server"></a>Dodawanie serwera vCenter

Dodaj serwer vCenter do serwera kopii zapasowej platformy Azure.

1. W konsoli serwera kopii zapasowych platformy Azure kliknij pozycję Dodawanie**serwerów** > produkcyjnych **zarządzania** > **.**

    ![Kreator dodawania serwera produkcji](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

2. W **Kreatorze dodawania serwera produkcyjnego** > wybierz typ**serwera produkcyjnego** wybierz stronę wybierz **pozycję Serwery VMware**, a następnie kliknij przycisk **Dalej**.

    ![Kreator dodawania serwera produkcyjnego](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

3. W **obszarze Wybierz**nazwę**serwera/adres IP**wybierz pozycję Nazwa FQDN lub adres IP serwera VMware.   Jeśli wszystkie serwery ESXi są zarządzane przez ten sam vCenter, określ nazwę vCenter. W przeciwnym razie dodaj hosta ESXi.

    ![Określanie serwera VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. W **porcie SSL**wprowadź port używany do komunikowania się z serwerem VMware. 443 jest portem domyślnym, ale można go zmienić, jeśli serwer VMware nasłuchuje na innym porcie.

5. W **obszarze Określ poświadczenia**wybierz poświadczenia utworzone wcześniej.

    ![Określanie poświadczeń](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Kliknij **przycisk Dodaj,** aby dodać serwer VMware do listy serwerów. Następnie kliknij przycisk **Dalej**.

    ![Dodawanie serwera VMWare i poświadczeń](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Na stronie **Podsumowanie** kliknij przycisk **Dodaj,** aby dodać serwer VMware do serwera kopii zapasowej platformy Azure. Nowy serwer jest dodawany natychmiast, agent nie jest potrzebny na serwerze VMware.

    ![Dodawanie serwera VMware do serwera kopii zapasowych platformy Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

8. Sprawdź ustawienia na stronie **Zakończ.**

   ![Strona zakończenia](./media/backup-azure-backup-server-vmware/summary-screen.png)

Jeśli masz wiele hostów ESXi, które nie są zarządzane przez serwer vCenter lub masz wiele wystąpień serwera vCenter, musisz ponownie uruchomić kreatora, aby dodać serwery.

## <a name="configure-a-protection-group"></a>Konfigurowanie grupy ochrony

Dodaj maszyny wirtualne VMware do tworzenia kopii zapasowych. Grupy ochrony zbierają wiele maszyn wirtualnych i stosują te same ustawienia przechowywania danych i tworzenia kopii zapasowych do wszystkich maszyn wirtualnych w grupie.

1. W konsoli serwera kopii zapasowych platformy Azure kliknij pozycję **Ochrona**, > **Nowy**.

    ![Otwórz kreatora Tworzenie nowej grupy ochrony](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

1. Na stronie powitalnej **Kreatora tworzenia nowej grupy ochrony** kliknij przycisk **Dalej**.

    ![Okno dialogowe Kreatora tworzenia nowej grupy ochrony](./media/backup-azure-backup-server-vmware/protection-wizard.png)

1. Na stronie **Wybierz typ grupy Ochrona** wybierz pozycję **Serwery,** a następnie kliknij przycisk **Dalej**. Zostanie wyświetlona strona **Wybierz członków grupy.**

1. W **obszarze Wybierz członków grupy**wybierz maszyny wirtualne (lub foldery maszyn wirtualnych), których chcesz poprzeć. Następnie kliknij przycisk **Dalej**.

    - Po wybraniu folderu lub maszyny wirtualne lub foldery wewnątrz tego folderu są również wybrane do tworzenia kopii zapasowych. Możesz odznaczyć foldery lub maszyny wirtualne, których nie chcesz wysuwać kopii zapasowej.
1. Jeśli kopia zapasowa maszyny Wirtualnej lub folderu jest już utworzona, nie można jej wybrać. Gwarantuje to, że zduplikowane punkty odzyskiwania nie są tworzone dla maszyny Wirtualnej.

    ![Wybieranie członków grupy](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

1. Na stronie **Wybierz metodę ochrony danych** wprowadź nazwę grupy ochrony i ustawienia ochrony. Aby zrobić kopii zapasowej na platformie Azure, ustaw krótkoterminową ochronę na **dysku** i włącz ochronę online. Następnie kliknij przycisk **Dalej**.

    ![Wybieranie metody ochrony danych](./media/backup-azure-backup-server-vmware/name-protection-group.png)

1. W **obszarze Określanie celów krótkoterminowych**określ, jak długo dane mają być archiwizowane na dysku.
   - W **obszarze Zakres przechowywania**określ, ile dni mają być przechowywane punkty odzyskiwania dysku.
   - W **obszarze Częstotliwość synchronizacji**określ, jak często są pobierane punkty odzyskiwania dysku.
       - Jeśli nie chcesz ustawiać interwału tworzenia kopii zapasowych, możesz sprawdzić **tuż przed punktem odzyskiwania,** aby kopia zapasowa była uruchamiana tuż przed zaplanowaniem każdego punktu odzyskiwania.
       - Krótkoterminowe kopie zapasowe są pełne kopie zapasowe, a nie przyrostowe.
       - Kliknij **przycisk Modyfikuj,** aby zmienić godziny/daty wykonywania krótkoterminowych kopii zapasowych.

         ![Określanie celów krótkoterminowych](./media/backup-azure-backup-server-vmware/short-term-goals.png)

1. W **obszarze Przegląd alokacji dysku**przejrzyj miejsce na dysku przewidziane dla kopii zapasowych maszyn wirtualnych. dla maszyn wirtualnych.

   - Zalecane alokacje dysków są oparte na określonym zakresie przechowywania, typie obciążenia i rozmiarze chronionych danych. Wykonuj wymagane zmiany, a następnie kliknij przycisk **Dalej**.
   - **Rozmiar danych:** Rozmiar danych w grupie ochrony.
   - **Miejsce na dysku:** Zalecana ilość miejsca na dysku dla grupy ochrony. Jeśli chcesz zmodyfikować to ustawienie, należy przydzielić całkowitą ilość miejsca, która jest nieco większa niż kwota, która szacuje, że każde źródło danych rośnie.
   - **Kolokacji danych:** Po włączeniu kolokacji wiele źródeł danych w ochronie może mapować na pojedynczy wolumin repliki i punktu odzyskiwania. Kolokacja nie jest obsługiwana dla wszystkich obciążeń.
   - **Automatycznie rosnąć:** Jeśli to ustawienie zostanie włączone, jeśli dane w chronionej grupie przerastają początkową alokację, usługa Azure Backup Server próbuje zwiększyć rozmiar dysku o 25 procent.
   - **Szczegóły puli magazynowania:** Pokazuje stan puli magazynu, w tym całkowity i pozostały rozmiar dysku.

    ![Przeglądanie alokacji dysku](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

1. Na stronie **Wybierz metodę tworzenia repliki** określ sposób wykonywania początkowej kopii zapasowej, a następnie kliknij przycisk **Dalej**.
   - Wartość domyślna to **Automatycznie przez sieć** i **Teraz**.
   - Jeśli używasz wartości domyślnej, zaleca się określenie godziny poza szczytem. Wybierz **pozycję Później** i określ dzień i godzinę.
   - W przypadku dużych ilości danych lub mniej niż optymalnych warunków sieciowych należy rozważyć replikację danych w trybie offline przy użyciu nośników wymiennych.

    ![Wybierz metodę tworzenia repliki](./media/backup-azure-backup-server-vmware/replica-creation.png)

1. W **obszarze Opcje sprawdzania spójności**wybierz sposób i czas automatyzacji kontroli spójności. Następnie kliknij przycisk **Dalej**.
      - Można uruchomić sprawdzanie spójności, gdy dane repliki stają się niespójne lub zgodnie z ustawionym harmonogramem.
      - Jeśli nie chcesz konfigurować automatycznych kontroli spójności, możesz uruchomić ręczne sprawdzanie. W tym celu kliknij prawym przyciskiem myszy grupę ochrony > **Sprawdź spójność**.

1. Na stronie **Określ dane ochrony online** wybierz foldery maszyn wirtualnych lub maszyn wirtualnych, których chcesz poprzeć. Można wybrać członków indywidualnie lub kliknąć przycisk **Wybierz wszystko,** aby wybrać wszystkich członków. Następnie kliknij przycisk **Dalej**.

    ![Określanie danych ochrony online](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

1. Na stronie **Określ harmonogram tworzenia kopii zapasowych online** określ, jak często chcesz wykonać kopię zapasową danych z magazynu lokalnego na platformie Azure.

    - Punkty odzyskiwania w chmurze dla danych zostaną wygenerowane zgodnie z harmonogramem. Następnie kliknij przycisk **Dalej**.
    - Po wygenerowaniu punktu odzyskiwania jest on przenoszony do magazynu usług odzyskiwania na platformie Azure.

    ![Określ harmonogram tworzenia kopii zapasowych online](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

1. Na stronie **Określ zasady przechowywania w trybie online** podaj, jak długo chcesz zachować punkty odzyskiwania utworzone na podstawie codziennych/tygodniowych/miesięcznych/5 000 kopii zapasowych na platformie Azure. a następnie kliknij przycisk **Dalej**.

    - Nie ma limitu czasu, jak długo można przechowywać dane na platformie Azure.
    - Jedynym limitem jest to, że nie można mieć więcej niż 9999 punktów odzyskiwania na chronione wystąpienie. W tym przykładzie chronionym wystąpieniem jest serwer VMware.

    ![Określanie zasad przechowywania w trybie online](./media/backup-azure-backup-server-vmware/retention-policy.png)

1. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie kliknij pozycję **Utwórz grupę**.

    ![Członek grupy ochrony i podsumowanie ustawień](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="vmware-vsphere-67"></a>VMWare vSphere 6.7

Aby wykonać kopii zapasowej vSphere 6.7, wykonaj następujące czynności:

- Włącz TLS 1.2 na serwerze programu DPM

>[!NOTE]
>VMWare 6.7 miał TLS włączone jako protokół komunikacyjny.

- Ustaw klucze rejestru w następujący sposób:

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat rozwiązywania problemów podczas konfigurowania kopii zapasowych, zapoznaj się z [przewodnikiem rozwiązywania problemów dla usługi Azure Backup Server](./backup-azure-mabs-troubleshoot.md).
