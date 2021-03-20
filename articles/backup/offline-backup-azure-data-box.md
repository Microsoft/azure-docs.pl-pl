---
title: Kopia zapasowa offline przy użyciu Azure Data Box
description: Dowiedz się, w jaki sposób można użyć Azure Data Box, aby wypełniać duże dane początkowej kopii zapasowej w trybie offline z agenta MARS do magazynu Recovery Services.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e789b6c9f4ff2e8cd168e6b5c138d423911d4743
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752587"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Backup kopii zapasowej offline przy użyciu Azure Data Box

Za pomocą [Azure Data Box](../databox/data-box-overview.md) można wypełniać duże początkowe kopie zapasowe Microsoft Azure Recovery Services (MARS) w trybie offline (bez użycia sieci) do magazynu Recovery Services. Ten proces oszczędza czas i przepustowość sieci, w przypadku których w przeciwnym razie można przenieść duże ilości danych kopii zapasowej w tryb online w sieci o dużej opóźnieniu. To ulepszenie jest obecnie dostępne w wersji zapoznawczej. Kopia zapasowa offline oparta na Azure Data Box zapewnia dwie różne zalety [tworzenia kopii zapasowych w trybie offline na podstawie usługi Azure Import/Export](./backup-azure-backup-import-export.md):

- Nie ma potrzeby pozyskiwania własnych dysków i łączników zgodnych z platformą Azure. Azure Data Box dostarcza dyski skojarzone z wybraną jednostką [SKU urządzenie Data Box](https://azure.microsoft.com/services/databox/data/).
- Azure Backup (Agent MARS) może bezpośrednio zapisywać dane kopii zapasowej na obsługiwanych jednostkach SKU Azure Data Box. Ta funkcja eliminuje konieczność udostępniania lokalizacji tymczasowej dla danych początkowej kopii zapasowej. Nie są też potrzebne narzędzia do formatowania i kopiowania tych danych na dyski.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box z agentem MARS

W tym artykule wyjaśniono, jak można użyć Azure Data Box, aby wypełniać duże dane początkowej kopii zapasowej w trybie offline z agenta MARS do magazynu Recovery Services.

## <a name="supported-platforms"></a>Obsługiwane platformy

Proces tworzenia inicjatora danych z agenta MARS przy użyciu Azure Data Box jest obsługiwany przez następujące jednostki SKU systemu Windows.

| **System operacyjny**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Stacja robocza**                        |                                                              |
| Windows 10 (wersja 64-bitowa)                     | Enterprise, Pro, Home                                       |
| Windows 8.1 (wersja 64-bitowa)                    | Enterprise, Pro                                             |
| Windows 8 (wersja 64-bitowa)                      | Enterprise, Pro                                             |
| Windows 7 (wersja 64-bitowa)                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server** (Serwer)                             |                                                              |
| Windows Server 2019 (wersja 64-bitowa)            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 (wersja 64-bitowa)            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 (wersja 64-bitowa)         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 (wersja 64-bitowa)            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 (wersja 64-bitowa)    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 (wersja 64-bitowa) | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 (wersja 64-bitowa)    | Standard, Workgroup                                         |
| Windows Server 2008 R2 z dodatkiem SP1 (wersja 64-bitowa)     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 z dodatkiem SP2 64 bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Rozmiar danych kopii zapasowych i obsługiwane urządzenie Data Box jednostek SKU

| Rozmiar danych kopii zapasowej (po kompresji przez MARS) * na serwer | Obsługiwana Azure Data Box jednostka SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 TB                                                    | [Dysk Azure Data Box](../databox/data-box-disk-overview.md) |
| >7,2 TB i <= 80 TB * *                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

* Typowe stawki kompresji różnią się od 10% do 20%. <br>
* * Jeśli spodziewasz się, że dane początkowej kopii zapasowej jednego serwera MARS mają więcej niż 80 TB, skontaktuj się z [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Początkowe dane kopii zapasowej z pojedynczego serwera muszą być zawarte w pojedynczym wystąpieniu Azure Data Box lub Azure Data Box dysku i nie mogą być współużytkowane przez wiele urządzeń z tymi samymi lub różnymi jednostkami SKU. Jednak urządzenie Azure Data Box może zawierać początkowe kopie zapasowe z wielu serwerów.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-subscription-and-required-permissions"></a>Subskrypcja platformy Azure i wymagane uprawnienia

- Proces wymaga subskrypcji platformy Azure.
- Proces wymaga, aby użytkownik wyznaczył przeprowadzenie zasad tworzenia kopii zapasowych w trybie offline jest właścicielem subskrypcji platformy Azure.
- W ramach tych samych subskrypcji wymagane jest zadanie urządzenie Data Box i magazyn Recovery Services (do którego dane muszą zostać umieszczone w magazynie).
- Zalecamy, aby docelowe konto magazynu skojarzone z zadaniem Azure Data Box i magazynem Recovery Services znajdują się w tym samym regionie. Nie jest to jednak konieczne.

### <a name="get-azure-powershell-370"></a>Pobierz Azure PowerShell 3.7.0

*Jest to najważniejszy warunek wstępny dla procesu*. Przed zainstalowaniem Azure PowerShell w wersji 3.7.0 należy wykonać następujące testy.

#### <a name="step-1-check-the-powershell-version"></a>Krok 1. Sprawdzanie wersji programu PowerShell

1. Otwórz program Windows PowerShell i uruchom następujące polecenie:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Jeśli dane wyjściowe są wyświetlane w wersji wyższej niż 3.7.0, zrób "krok 2". W przeciwnym razie przejdź do kroku 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Krok 2. Odinstalowywanie wersji programu PowerShell

Odinstaluj bieżącą wersję programu PowerShell.

1. Usuń moduły zależne, uruchamiając następujące polecenie w programie PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Aby upewnić się, że wszystkie moduły zależne zostały pomyślnie usunięte, uruchom następujące polecenie:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Krok 3. instalacja programu PowerShell w wersji 3.7.0

Po zweryfikowaniu, że nie ma żadnych modułów AzureRM, Zainstaluj wersję 3.7.0, używając jednej z następujących metod:

- W witrynie GitHub Użyj [tego linku](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).

Możesz też:

- Uruchom następujące polecenie w oknie programu PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell można również zainstalować przy użyciu pliku msi. Aby go usunąć, odinstaluj go za pomocą opcji **Odinstaluj programy** w panelu sterowania.

### <a name="order-and-receive-the-data-box-device"></a>Zamówienie i odbieranie urządzenia urządzenie Data Box

Proces tworzenia kopii zapasowej w trybie offline przy użyciu usług MARS i Azure Data Box wymaga, aby urządzenia urządzenie Data Box były w stanie dostarczonym przed wywołaniem kopii zapasowej w trybie offline przy użyciu agenta MARS. Aby zamówić najbardziej odpowiednią jednostkę SKU dla danego wymagania, zobacz [rozmiar danych kopii zapasowej i obsługiwane urządzenie Data Box jednostek SKU](#backup-data-size-and-supported-data-box-skus). Wykonaj kroki opisane w [samouczku: Zamów dysk Azure Data Box](../databox/data-box-disk-deploy-ordered.md) , aby zamówić i odbierać urządzenie Data Box urządzeń.

> [!IMPORTANT]
> Nie wybieraj *BlobStorage* dla **rodzaju konta**. Agent MARS wymaga konta obsługującego stronicowe obiekty blob, które nie jest obsługiwane w przypadku wybrania *BlobStorage* . Wybierz pozycję **Storage v2 (ogólnego przeznaczenia w wersji 2)** jako **rodzaj konta** podczas tworzenia docelowego konta magazynu dla zadania Azure Data Box.

![Wybierz rodzaj konta w szczegółach wystąpienia](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Instalowanie i Konfigurowanie agenta MARS

1. Upewnij się, że odinstalowano wszystkie poprzednie instalacje agenta MARS.
1. Pobierz najnowszego agenta MARS z [tej witryny sieci Web](https://aka.ms/azurebackup_agent).
1. Uruchom *MARSAgentInstaller.exe* i wykonaj *tylko* te czynności, aby [zainstalować i zarejestrować agenta](./install-mars-agent.md#install-and-register-the-agent) w magazynie Recovery Services, w którym mają być przechowywane kopie zapasowe.

   > [!NOTE]
   > Magazyn Recovery Services musi znajdować się w tej samej subskrypcji co zadanie Azure Data Box.

   Po zarejestrowaniu agenta w magazynie Recovery Services wykonaj kroki opisane w kolejnych sekcjach.

## <a name="set-up-azure-data-box-devices"></a>Konfigurowanie urządzeń Azure Data Box

W zależności od zamówionej jednostki SKU Azure Data Box wykonaj kroki opisane w odpowiednich sekcjach poniżej. W tym kroku przedstawiono sposób konfigurowania i przygotowywania urządzenie Data Box urządzeń dla agenta MARS w celu identyfikowania i przesyłania danych początkowej kopii zapasowej.

### <a name="set-up-azure-data-box-disks"></a>Konfigurowanie dysków Azure Data Box

Jeśli zostały uporządkowane co najmniej jeden Azure Data Box dysków (do 8 TB), wykonaj kroki opisane tutaj, aby [rozpakować, połączyć i odblokować dysk urządzenie Data Box](../databox/data-box-disk-deploy-set-up.md).

>[!NOTE]
>Istnieje możliwość, że serwer z agentem MARS nie ma portu USB. W takiej sytuacji można połączyć Azure Data Box dysk z innym serwerem lub klientem i udostępnić katalog główny urządzenia jako udział sieciowy.

### <a name="set-up-azure-data-box"></a>Skonfiguruj Azure Data Box

Jeśli zamówiłeś wystąpienie Azure Data Box (do 100 TB), postępuj zgodnie z poniższymi instrukcjami, [Aby skonfigurować wystąpienie urządzenie Data Box](../databox/data-box-deploy-set-up.md).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Zainstaluj wystąpienie Azure Data Box jako system lokalny

Agent MARS działa w kontekście systemu lokalnego, dlatego wymaga podania tego samego poziomu uprawnień do ścieżki instalacji, w której jest połączone wystąpienie Azure Data Box.

Aby mieć pewność, że urządzenie urządzenie Data Box można zainstalować jako system lokalny przy użyciu protokołu NFS:

1. Włącz klienta dla funkcji systemu plików NFS na serwerze z systemem Windows, na którym jest zainstalowany agent MARS. Określ alternatywny źródłowy *wim: D: \Sources\Install.wim: 4*.
1. Pobierz PsExec ze strony [Sysinternals](/sysinternals/downloads/psexec) na serwer z zainstalowanym agentem Mars.
1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie z katalogiem zawierającym *PSExec.exe* jako bieżący katalog.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Okno polecenia otwierane z powodu poprzedniego polecenia znajduje się w kontekście systemu lokalnego. Za pomocą tego okna polecenia można wykonać kroki instalacji udziału usługi Azure Page BLOB jako dysku sieciowego na serwerze z systemem Windows.
1. Postępuj zgodnie z instrukcjami w temacie [Connect to urządzenie Data Box](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) , aby połączyć serwer z agentem Mars do urządzenia urządzenie Data Box za pośrednictwem systemu plików NFS. Uruchom następujące polecenie w wierszu polecenia systemu lokalnego, aby zainstalować udział obiektów BLOB na stronie platformy Azure.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Po zainstalowaniu udziału Sprawdź, czy możesz uzyskać dostęp do X: z serwera. Jeśli chcesz, przejdź do następnej sekcji tego artykułu.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transferowanie danych początkowej kopii zapasowej do urządzeń Azure Data Box

1. Otwórz aplikację **Microsoft Azure Backup** na serwerze.
1. W okienku **Akcje** wybierz pozycję **Zaplanuj kopię zapasową**.

    ![Wybieranie harmonogramu kopii zapasowej](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Postępuj zgodnie z instrukcjami w **Kreatorze harmonogramu tworzenia kopii zapasowych**.

1. Dodaj elementy, wybierając przycisk **Dodaj elementy** . Zachowaj łączny rozmiar elementów w ramach [limitów rozmiaru obsługiwanych przez](#backup-data-size-and-supported-data-box-skus) zamówione i odebrane jednostki SKU Azure Data Box.

    ![Dodaj elementy do kopii zapasowej](./media/offline-backup-azure-data-box/add-items.png)

1. Wybierz odpowiednie harmonogramy i zasady przechowywania kopii zapasowych dla **plików i folderów** oraz **stanu systemu**. Stan systemu ma zastosowanie tylko do serwerów z systemem Windows, a nie dla klientów systemu Windows.
1. Na stronie **Wybierz typ początkowej kopii zapasowej (pliki i foldery)** kreatora wybierz opcję **Transfer przy użyciu Microsoft Azure urządzenie Data Box dyski** i wybierz przycisk **dalej**.

    ![Wybierz typ początkowej kopii zapasowej](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Po wyświetleniu monitu zaloguj się do platformy Azure przy użyciu poświadczeń użytkownika mających dostęp właściciela do subskrypcji platformy Azure. Po pomyślnym wykonaniu tej czynności powinna zostać wyświetlona strona podobna do tej.

    ![Tworzenie zasobów i stosowanie wymaganych uprawnień](./media/offline-backup-azure-data-box/creating-resources.png)

   Następnie Agent MARS pobiera urządzenie Data Box zadania w subskrypcji, które znajdują się w stanie dostarczone.

    ![Pobierz zadania urządzenie Data Box dla identyfikatora subskrypcji](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Wybierz poprawną kolejność urządzenie Data Box, dla której zostało rozpakowane, połączone i odblokowane urządzenie Data Box dysku. Wybierz opcję **Dalej**.

    ![Wybierz zamówienia urządzenie Data Box](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Wybierz pozycję **Wykryj urządzenie** na stronie **wykrywania urządzeń urządzenie Data Box** . Ta akcja powoduje, że Agent MARS skanuje lokalnie dołączone dyski Azure Data Box i wykrywa je.

    ![Wykrywanie urządzenia urządzenie Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Jeśli wystąpienie Azure Data Box zostało połączone jako udział sieciowy (z powodu niedostępności portów USB lub zamówione i zainstalowane urządzenie z 100-TB urządzenie Data Box), wykrywanie nie powiedzie się w pierwszej kolejności. Masz możliwość wprowadzenia ścieżki sieciowej do urządzenia urządzenie Data Box.

    ![Wprowadź ścieżkę sieciową](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Podaj ścieżkę sieciową do katalogu głównego dysku Azure Data Box. Ten katalog musi zawierać katalog o nazwie *PageBlob*.
    >
    >![Katalog główny Azure Data Box dysku](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Na przykład jeśli ścieżka dysku to `\\mydomain\myserver\disk1\` i *disk1* zawiera katalog o nazwie *PageBlob*, wprowadzona ścieżka na stronie kreatora agenta Mars to `\\mydomain\myserver\disk1\` .
    >
    >W przypadku [skonfigurowania urządzenia z Azure Data Box 100 TB](#set-up-azure-data-box-devices)wpisz `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` jako ścieżkę sieciową do urządzenia.

1. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Zakończ** na następnej stronie, aby zapisać zasady tworzenia kopii zapasowych i przechowywania z konfiguracją kopii zapasowej offline przy użyciu Azure Data Box.

   Poniższa Strona potwierdza, że zasady zostały pomyślnie zapisane.

    ![Zasady zostały pomyślnie zapisane](./media/offline-backup-azure-data-box/policy-saved.png)

1. Na poprzedniej stronie wybierz pozycję **Zamknij** .

1. Wybierz pozycję **Utwórz kopię zapasową teraz** w okienku **Akcje** konsoli agenta Mars. Wybierz pozycję **Utwórz kopię zapasową** na stronie kreatora.

    ![Kreator tworzenia kopii zapasowej teraz](./media/offline-backup-azure-data-box/backup-now.png)

Agent MARS rozpocznie tworzenie kopii zapasowych wybranych danych na urządzeniu Azure Data Box. Ten proces może potrwać od kilku godzin do kilku dni. Ilość czasu zależy od liczby plików i szybkości połączenia między serwerem a agentem MARS i dyskiem Azure Data Box.

Po zakończeniu tworzenia kopii zapasowej danych na agencie MARS zostanie wyświetlona strona, która będzie podobna do tej.

![Pokazany postęp tworzenia kopii zapasowej](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Kroki po wykonaniu kopii zapasowej

W tej sekcji opisano kroki, które należy wykonać po pomyślnym wykonaniu kopii zapasowej danych Azure Data Box Disk.

- Wykonaj kroki opisane w tym artykule, aby [dostarczyć Azure Data Box dysk do platformy Azure](../databox/data-box-disk-deploy-picked-up.md). Jeśli używasz urządzenia z systemem Azure Data Box 100 TB, wykonaj następujące kroki, aby [dostarczyć urządzenie Azure Data Box do platformy Azure](../databox/data-box-deploy-picked-up.md).

- [Monitoruj zadanie urządzenie Data Box](../databox/data-box-disk-deploy-upload-verify.md) w Azure Portal. Po zakończeniu zadania Azure Data Box Agent MARS automatycznie przenosi dane z konta magazynu do magazynu Recovery Services w czasie następnej zaplanowanej kopii zapasowej. Oznacza to, że zadanie tworzenia kopii zapasowej zostanie oznaczone jako *ukończone* w przypadku pomyślnego utworzenia punktu odzyskiwania.

    >[!NOTE]
    >Agent MARS wyzwala kopie zapasowe w planowanych godzinach podczas tworzenia zasad. Te zadania oznaczają flagę "Oczekiwanie na ukończenie zadania Azure Data Box, aż do momentu zakończenia zadania.

- Po pomyślnym utworzeniu przez agenta MARS punktu odzyskiwania, który odnosi się do początkowej kopii zapasowej, można usunąć konto magazynu lub określoną zawartość skojarzoną z zadaniem Azure Data Box.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Agent Microsoft Azure Recovery Services (MARS) tworzy aplikację Azure Active Directory (Azure AD) dla Ciebie w dzierżawie. Ta aplikacja wymaga certyfikatu do uwierzytelniania utworzonego i przekazanego podczas konfigurowania zasad rozmieszczania w trybie offline. Używamy Azure PowerShell do tworzenia i przekazywania certyfikatu do aplikacji usługi Azure AD.

### <a name="problem"></a>Problem

Podczas konfigurowania kopii zapasowej w trybie offline może wystąpić problem z powodu błędu w poleceniu cmdlet Azure PowerShell. Dodanie wielu certyfikatów do tej samej aplikacji usługi Azure AD utworzonej przez agenta MAB może być niemożliwe. Ten problem będzie miał wpływ na użytkownika, jeśli skonfigurowano zasady umieszczania w trybie offline dla tego samego lub innego serwera.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Sprawdź, czy problem jest spowodowany przez tę konkretną przyczynę główną

Aby sprawdzić, czy Twój problem jest taki sam jak wcześniej opisany, wykonaj jedną z następujących czynności.

#### <a name="step-1-of-verification"></a>Krok 1 weryfikacji

Sprawdź, czy podczas konfigurowania kopii zapasowej w trybie offline w konsoli MAB zostanie wyświetlony następujący komunikat o błędzie.

![Nie można utworzyć zasad kopii zapasowych offline dla bieżącego konta platformy Azure](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Krok 2 weryfikacji

1. Otwórz folder **temp** w ścieżce instalacji. Domyślna ścieżka folderu tymczasowego to *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Wyszukaj plik *CBUICurr* i Otwórz plik.

1. W pliku *CBUICurr* przewiń do ostatniego wiersza i sprawdź, czy problem jest taki sam jak w przypadku tego komunikatu o błędzie: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Obejście

Aby rozwiązać ten problem, należy wykonać następujące czynności, a następnie ponownie wykonać konfigurację zasad.

#### <a name="step-1-of-workaround"></a>Krok 1 obejścia

Zaloguj się do programu PowerShell, który jest wyświetlany w interfejsie użytkownika usługi MAB przy użyciu innego konta z dostępem administratora w ramach subskrypcji, która będzie miała utworzone zadanie urządzenie Data Box.

#### <a name="step-2-of-workaround"></a>Krok 2 obejścia

Jeśli żaden inny serwer nie ma skonfigurowanych wypełnień w trybie offline i żaden inny serwer nie jest zależny od `AzureOfflineBackup_<Azure User Id>` aplikacji, Usuń tę aplikację. Wybierz **Azure Portal**  >  **Azure Active Directory**  >  **rejestracje aplikacji**.

>[!NOTE]
> Sprawdź, czy `AzureOfflineBackup_<Azure User Id>` aplikacja nie ma skonfigurowanych innych wypełnień w trybie offline, a także czy żaden inny serwer nie jest zależny od tej aplikacji. Przejdź do pozycji **Ustawienia**  >  **klucze** w sekcji **klucze publiczne** . Nie należy dodawać żadnych innych kluczy publicznych. Aby uzyskać informacje, zobacz poniższy zrzut ekranu.
>
>![Klucze publiczne](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Krok 3

Na serwerze, który próbujesz skonfigurować do tworzenia kopii zapasowych w trybie offline, wykonaj następujące czynności.

1. Przejdź do karty **Zarządzanie certyfikatem komputera aplikacja**  >  **osobista** i poszukaj certyfikatu o nazwie `CB_AzureADCertforOfflineSeeding_<ResourceId>` .

2. Wybierz certyfikat, kliknij prawym przyciskiem myszy **wszystkie zadania** i wybierz opcję **Eksportuj** bez klucza prywatnego w formacie CER.

3. Przejdź do aplikacji usługi Azure offline Backup wymienionej w kroku 2. Wybierz pozycję **Ustawienia**  >  **klucze**  >  **Przekaż klucz publiczny**. Przekaż certyfikat wyeksportowany w poprzednim kroku.

    ![Przekaż klucz publiczny](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Na serwerze otwórz rejestr, wprowadzając polecenie **regedit** w oknie uruchamiania.

5. Przejdź do rejestru *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Kliknij prawym przyciskiem myszy pozycję **CloudBackupProvider**, a następnie Dodaj nową wartość ciągu o nazwie `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Aby uzyskać identyfikator użytkownika platformy Azure, wykonaj jedną z następujących czynności:
    >
    >- Z poziomu programu PowerShell połączonego z platformą Azure Uruchom `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` polecenie.
    > - Przejdź do ścieżki rejestru `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` o nazwie *CurrentUserId*.

6. Kliknij prawym przyciskiem myszy ciąg dodany w poprzednim kroku, a następnie wybierz polecenie **Modyfikuj**. W polu wartość Podaj odcisk palca certyfikatu wyeksportowanego w kroku 2. Wybierz przycisk **OK**.

7. Aby uzyskać wartość odcisku palca, kliknij dwukrotnie certyfikat. Wybierz kartę **szczegóły** i przewiń w dół do momentu wyświetlenia pola odcisk palca. Wybierz **odcisk palca** i skopiuj wartość.

    ![Pole odcisku palca certyfikatu](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Pytania

Aby dowiedzieć się więcej na temat pytań lub wyjaśnień dotyczących problemów, skontaktuj się z firmą [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
