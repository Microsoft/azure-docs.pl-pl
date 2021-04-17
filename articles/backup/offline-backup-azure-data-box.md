---
title: Tworzenie kopii zapasowej w trybie offline przy użyciu Azure Data Box
description: Dowiedz się, jak za pomocą usługi Azure Data Box inicjować duże początkowe dane kopii zapasowej w trybie offline z agenta MARS do magazynu usługi Recovery Services.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 78adc479ce5733e208d2334d30d7b88e4edf8d6b
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576095"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Backup kopii zapasowej offline przy użyciu Azure Data Box

Za pomocą usługi [Azure Data Box](../databox/data-box-overview.md) można inicjować duże początkowe kopie zapasowe usługi Microsoft Azure Recovery Services (MARS) w trybie offline (bez używania sieci) do magazynu usługi Recovery Services. Ten proces pozwala zaoszczędzić czas i przepustowość sieci, która w przeciwnym razie byłaby zużywana podczas przenoszenia dużych ilości danych kopii zapasowej w trybie online za pośrednictwem sieci o dużym opóźnieniu. To ulepszenie jest obecnie dostępne w wersji zapoznawczej. Tworzenie kopii zapasowej w trybie offline Azure Data Box zapewnia dwie odrębne korzyści w witrynie Backup w trybie offline w [oparciu o usługę Azure Import/Export:](./backup-azure-backup-import-export.md)

- Nie ma potrzeby nabywania własnych dysków i łączników zgodnych z platformą Azure. Azure Data Box dostarcza dyski skojarzone z wybraną jednostką [SKU urządzenie Data Box SKU.](https://azure.microsoft.com/services/databox/data/)
- Azure Backup (agent MARS) może bezpośrednio zapisywać dane kopii zapasowej na obsługiwanych jednostkach SKU Azure Data Box. Ta funkcja eliminuje konieczność aprowizowania lokalizacji przejściowej dla danych początkowej kopii zapasowej. Nie są również potrzebne narzędzia do formatowania i kopiowania tych danych na dyski.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box agenta MARS

W tym artykule wyjaśniono, jak za pomocą usługi Azure Data Box inicjować duże początkowe dane kopii zapasowej w trybie offline z agenta MARS do magazynu usługi Recovery Services.

## <a name="supported-platforms"></a>Obsługiwane platformy

Proces iniekcjowania danych z agenta MARS przy użyciu Azure Data Box jest obsługiwany w następujących jednostkach SKU systemu Windows.

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
| Windows Server 2008 SP2 (wersja 64-bitowa)        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Rozmiar danych kopii zapasowej i obsługiwane urządzenie Data Box SKU

| Rozmiar danych kopii zapasowej (po kompresji przez usługę MARS)* na serwer | Obsługiwana Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 TB                                                    | [Azure Data Box dysku](../databox/data-box-disk-overview.md) |
| >7,2 TB i <=80 TB**                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

*Typowe współczynniki kompresji wahają się od 10% do 20%. <br>
**Jeśli oczekujesz, że masz więcej niż 80 TB danych początkowej kopii zapasowej dla pojedynczego serwera MARS, skontaktuj się [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) z .

>[!IMPORTANT]
>Dane początkowej kopii zapasowej z jednego serwera muszą być zawarte w jednym wystąpieniu Azure Data Box lub dysku Azure Data Box i nie mogą być współużytowane przez wiele urządzeń z tymi samymi lub różnymi jednostkami SKU. Jednak urządzenie Azure Data Box może zawierać początkowe kopie zapasowe z wielu serwerów.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="azure-subscription-and-required-permissions"></a>Subskrypcja platformy Azure i wymagane uprawnienia

- Ten proces wymaga subskrypcji platformy Azure.
- Ten proces wymaga, aby użytkownik wyznaczony do wykonywania zasad tworzenia kopii zapasowych w trybie offline był właścicielem subskrypcji platformy Azure.
- Zadanie urządzenie Data Box i magazyn usługi Recovery Services (do którego dane muszą zostać rozesłane) muszą być w tych samych subskrypcjach.
- Zalecamy, aby docelowe konto magazynu skojarzone z Azure Data Box i magazynem usługi Recovery Services było w tym samym regionie. Nie jest to jednak konieczne.

### <a name="get-azure-powershell-370"></a>Pobierz Azure PowerShell 3.7.0

*Jest to najważniejsze wymaganie wstępne dla tego procesu.* Przed zainstalowaniem Azure PowerShell w wersji 3.7.0 wykonaj następujące testy.

#### <a name="step-1-check-the-powershell-version"></a>Krok 1. Sprawdzanie wersji programu PowerShell

1. Otwórz Windows PowerShell i uruchom następujące polecenie:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Jeśli w danych wyjściowych zostanie wyświetlona wersja wyższa niż 3.7.0, wykonaj "Krok 2". W przeciwnym razie przejdź do kroku 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Krok 2. Odinstalowywanie wersji programu PowerShell

Odinstaluj bieżącą wersję programu PowerShell.

1. Usuń moduły zależne, uruchamiając następujące polecenie w programie PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Aby zapewnić pomyślne usunięcie wszystkich modułów zależnych, uruchom następujące polecenie:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Krok 3. Instalowanie programu PowerShell w wersji 3.7.0

Po sprawdzeniu, czy nie ma żadnych modułów AzureRM, zainstaluj wersję 3.7.0 przy użyciu jednej z następujących metod:

- W usłudze GitHub użyj [tego linku.](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

Możesz też:

- Uruchom następujące polecenie w oknie programu PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell można również zainstalować przy użyciu pliku msi. Aby go usunąć, odinstaluj go przy użyciu opcji **Odinstaluj programy** w Panel sterowania.

### <a name="order-and-receive-the-data-box-device"></a>Zamawianie i odbieranie urządzenie Data Box urządzenia

Proces tworzenia kopii zapasowej w trybie offline przy użyciu usług MARS i Azure Data Box wymaga, aby urządzenia urządzenie Data Box w stanie Dostarczono przed wyzwoleniem tworzenia kopii zapasowej w trybie offline przy użyciu agenta MARS. Aby zamówić najbardziej odpowiednią jednostkę SKU dla wymagań, zobacz Rozmiar danych kopii zapasowej i obsługiwane jednostki SKU [urządzenie Data Box SKU.](#backup-data-size-and-supported-data-box-skus) Wykonaj kroki opisane w części [Samouczek: zamawianie Azure Data Box,](../databox/data-box-disk-deploy-ordered.md) aby zamówić i odebrać urządzenie Data Box urządzenia.

> [!IMPORTANT]
> Nie wybieraj opcji *BlobStorage dla* **typu Konto.** Agent MARS wymaga konta, które obsługuje stronicowe obiekty blob, co nie jest obsługiwane po wybraniu opcji *BlobStorage.* Wybierz pozycję Storage V2 (ogólnego  przeznaczenia w wersji **2)** jako rodzaj konta podczas tworzenia docelowego konta magazynu dla Azure Data Box magazynu.

![Wybieranie rodzaju konta w szczegółach wystąpienia](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Instalowanie i konfigurowanie agenta MARS

1. Pamiętaj o odinstalowaniu wszystkich poprzednich instalacji agenta MARS.
1. Pobierz najnowszego agenta MARS z [tej witryny internetowej.](https://aka.ms/azurebackup_agent)
1. Uruchom *MARSAgentInstaller.exe* i wykonaj *tylko* kroki [](./install-mars-agent.md#install-and-register-the-agent) instalowania i rejestrowania agenta w magazynie usługi Recovery Services, w którym mają być przechowywane kopie zapasowe.

   > [!NOTE]
   > Magazyn usługi Recovery Services musi znajdować się w tej samej subskrypcji co Azure Data Box usługi.

   Po zarejestrowaniu agenta w magazynie usługi Recovery Services wykonaj kroki opisane w następnych sekcjach.

## <a name="set-up-azure-data-box-devices"></a>Konfigurowanie Azure Data Box urządzeń

W zależności od Azure Data Box SKU, które zostały zamówione, wykonaj kroki opisane w odpowiednich sekcjach, które należy wykonać. Kroki pokazują, jak skonfigurować i przygotować urządzenia urządzenie Data Box agenta MARS w celu zidentyfikowania i przeniesienia danych początkowej kopii zapasowej.

### <a name="set-up-azure-data-box-disks"></a>Konfigurowanie dysków Azure Data Box wirtualnych

Jeśli zamówisz co najmniej jeden dysk Azure Data Box (każdy o pojemności do 8 TB), wykonaj kroki opisane tutaj, aby rozpakować, podłączyć i odblokować urządzenie Data Box [dysk.](../databox/data-box-disk-deploy-set-up.md)

>[!NOTE]
>Możliwe, że serwer z agentem MARS nie ma portu USB. W takiej sytuacji można połączyć dysk Azure Data Box z innym serwerem lub klientem i uwidocznić katalog główny urządzenia jako udział sieciowy.

### <a name="set-up-azure-data-box"></a>Konfigurowanie Azure Data Box

Jeśli zamówiliśmy wystąpienie Azure Data Box (do 100 TB), wykonaj kroki opisane tutaj, aby skonfigurować [urządzenie Data Box wystąpienie .](../databox/data-box-deploy-set-up.md)

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Zainstaluj swoje Azure Data Box jako system lokalny

Agent MARS działa w kontekście systemu lokalnego, dlatego wymaga, aby do ścieżki instalacji, w której Azure Data Box połączenie, był taki sam poziom uprawnień.

Aby upewnić się, że można zainstalować urządzenie urządzenie Data Box jako system lokalny przy użyciu protokołu NFS:

1. Włącz klienta funkcji systemu plików NFS na serwerze z systemem Windows, na którym jest zainstalowany agent MARS. Określ alternatywne źródło *WIM:D:\Sources\Install.wim:4.*
1. Pobierz narzędzie PsExec ze [strony Sysinternals](/sysinternals/downloads/psexec) na serwer z zainstalowanym agentem MARS.
1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie z katalogiem, *któryPSExec.exe* jako bieżący katalog.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Okno polecenia, które zostanie otwarte z powodu poprzedniego polecenia, znajduje się w kontekście systemu lokalnego. To okno polecenia służy do wykonywania kroków instalacji udziału stronicowych obiektów blob platformy Azure jako dysku sieciowego na serwerze z systemem Windows.
1. Postępuj zgodnie z instrukcjami w te [urządzenie Data Box,](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) aby połączyć serwer z agentem MARS z urządzeniem urządzenie Data Box za pośrednictwem systemu plików NFS. Uruchom następujące polecenie w wierszu polecenia System lokalny, aby zainstalować udział stronicowych obiektów blob platformy Azure.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Po zamontowaniu udziału sprawdź, czy możesz uzyskać dostęp do X: z serwera. Jeśli to możliwe, przejdź do następnej sekcji tego artykułu.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transfer danych początkowej kopii zapasowej do Azure Data Box urządzeń

1. Otwórz aplikację **Microsoft Azure Backup** na serwerze.
1. W **okienku Akcje** wybierz pozycję **Zaplanuj tworzenie kopii zapasowej.**

    ![Wybierz pozycję Zaplanuj tworzenie kopii zapasowej](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Postępuj zgodnie z instrukcjami Kreatora **planowania tworzenia kopii zapasowych.**

1. Dodaj elementy, wybierając przycisk **Dodaj** elementy. Zachowaj całkowity rozmiar elementów w ramach limitów rozmiaru obsługiwanych przez Azure Data Box [SKU,](#backup-data-size-and-supported-data-box-skus) które zamówione i odebrane.

    ![Dodawanie elementów do kopii zapasowej](./media/offline-backup-azure-data-box/add-items.png)

1. Wybierz odpowiednie zasady harmonogramu i przechowywania kopii zapasowych dla **plików i folderów** oraz stanu **systemu.** Stan systemu ma zastosowanie tylko w przypadku serwerów z systemem Windows, a nie dla klientów z systemem Windows.
1. Na stronie **Wybierz typ początkowej kopii zapasowej (pliki** i foldery) kreatora wybierz opcję Przenieś przy użyciu Microsoft Azure urządzenie Data Box kopii **zapasowych** i wybierz przycisk **Dalej.**

    ![Wybieranie typu początkowej kopii zapasowej](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Po wyświetleniu monitu zaloguj się do platformy Azure przy użyciu poświadczeń użytkownika, które mają dostęp właściciela w subskrypcji platformy Azure. Po tym, jak to się powiedzie, powinna zostać wyświetlony strona podobna do tej.

    ![Tworzenie zasobów i stosowanie wymaganych uprawnień](./media/offline-backup-azure-data-box/creating-resources.png)

   Następnie agent MARS pobiera zadania urządzenie Data Box subskrypcji, które są w stanie Dostarczono.

    ![Pobieranie urządzenie Data Box zadań dla identyfikatora subskrypcji](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Wybierz prawidłową urządzenie Data Box, dla której rozpakowanie, połączenie i odblokowanie dysku urządzenie Data Box dysku. Wybierz opcję **Dalej**.

    ![Wybieranie urządzenie Data Box zamówień](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Wybierz **pozycję Wykryj** urządzenie **na urządzenie Data Box wykrywanie** urządzeń. Ta akcja sprawia, że agent MARS skanuje dyski Azure Data Box i wykrywa je.

    ![urządzenie Data Box wykrywania urządzeń](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Jeśli wystąpienie usługi Azure Data Box zostało podłączone jako udział sieciowy (z powodu niedostępności portów USB lub zamówienia i zamontowania urządzenia urządzenie Data Box o pojemności 100 TB), wykrywanie na początku kończy się niepowodzeniem. Masz możliwość wprowadzenia ścieżki sieciowej do urządzenia urządzenie Data Box wirtualnego.

    ![Wprowadź ścieżkę sieciową](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Podaj ścieżkę sieciową do katalogu głównego Azure Data Box dysku. Ten katalog musi zawierać katalog o nazwie *PageBlob*.
    >
    >![Katalog główny Azure Data Box dysku](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Jeśli na przykład ścieżka dysku to , a dysk1 zawiera katalog o nazwie `\\mydomain\myserver\disk1\` *PageBlob,*  ścieżka w wprowadzana na stronie kreatora agenta MARS to `\\mydomain\myserver\disk1\` .
    >
    >W przypadku [skonfigurowania Azure Data Box o pojemności 100 TB](#set-up-azure-data-box-devices)wprowadź jako `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` ścieżkę sieciową do urządzenia.

1. Wybierz **przycisk** Dalej i wybierz przycisk **Zakończ** na następnej stronie, aby zapisać zasady tworzenia kopii zapasowej i przechowywania z konfiguracją kopii zapasowej w trybie offline przy użyciu Azure Data Box.

   Na poniższej stronie można potwierdzić, że zasady zostały pomyślnie zapisane.

    ![Zasady zostały pomyślnie zapisane](./media/offline-backup-azure-data-box/policy-saved.png)

1. Wybierz **pozycję Zamknij** na poprzedniej stronie.

1. Wybierz **pozycję Back Up Now (Teraz** kopii zapasowej) w **okienku Actions** (Akcje) konsoli agenta usługi MARS. Wybierz **pozycję Kopii zapasowej** na stronie kreatora.

    ![Kreator kopii zapasowej teraz](./media/offline-backup-azure-data-box/backup-now.png)

Agent MARS rozpoczyna kopię zapasową wybranych danych na Azure Data Box urządzenia. Ten proces może potrwać od kilku godzin do kilku dni. Ilość czasu zależy od liczby plików i szybkości połączenia między serwerem z agentem MARS a dyskiem Azure Data Box sieci.

Po zakończeniu tworzenia kopii zapasowej danych zobaczysz stronę agenta MARS, która będzie podobna do tej.

![Wyświetlany postęp tworzenia kopii zapasowej](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Kroki po kopii zapasowej

W tej sekcji wyjaśniono kroki, które należy wykonać po pomyślnym zakończeniu tworzenia kopii zapasowej Azure Data Box Disk danych.

- Wykonaj kroki opisane w tym artykule, aby [wysłać dysk Azure Data Box na platformę Azure.](../databox/data-box-disk-deploy-picked-up.md) Jeśli używasz urządzenia Azure Data Box o pojemności 100 TB, wykonaj następujące kroki, aby wysłać urządzenie [Azure Data Box na platformę Azure.](../databox/data-box-deploy-picked-up.md)

- [Monitoruj urządzenie Data Box zadania w](../databox/data-box-disk-deploy-upload-verify.md) Azure Portal. Po zakończeniu Azure Data Box agent MARS automatycznie przenosi dane z konta magazynu do magazynu usługi Recovery Services w czasie tworzenia następnej zaplanowanej kopii zapasowej. Następnie oznacza zadanie tworzenia kopii zapasowej jako *Zadanie ukończone,* jeśli punkt odzyskiwania zostanie pomyślnie utworzony.

    >[!NOTE]
    >Agent MARS wyzwala kopie zapasowe w czasie zaplanowanym podczas tworzenia zasad. Te zadania flaguje "Oczekiwanie Azure Data Box zadania do ukończenia" do czasu ukończenia zadania.

- Gdy agent MARS pomyślnie utworzy punkt odzyskiwania odpowiadający początkowej kopii zapasowej, można usunąć konto magazynu lub konkretną zawartość skojarzoną z Azure Data Box kopii zapasowej.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Agent Microsoft Azure Recovery Services (MARS) tworzy aplikację Azure Active Directory (Azure AD) dla Ciebie w dzierżawie. Ta aplikacja wymaga certyfikatu do uwierzytelniania, który jest tworzony i przekazywany podczas konfigurowania zasad rozsyłania w trybie offline. Używamy Azure PowerShell do utworzenia i przekazania certyfikatu do aplikacji usługi Azure AD.

### <a name="problem"></a>Problem

Podczas konfigurowania kopii zapasowej w trybie offline może wystąpić problem z powodu błędu w Azure PowerShell cmdlet. Dodanie wielu certyfikatów do tej samej aplikacji usługi Azure AD utworzonej przez agenta USŁUGI MAB może być nie być w stanie. Ten problem będzie mieć wpływ na Ciebie, jeśli skonfigurowano zasady iniekcjowania w trybie offline dla tego samego lub innego serwera.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Sprawdź, czy problem jest spowodowany przez tę konkretną główną przyczynę

Aby sprawdzić, czy problem jest taki sam jak opisany wcześniej, wykonaj jedną z następujących czynności.

#### <a name="step-1-of-verification"></a>Krok 1 weryfikacji

Sprawdź, czy podczas konfigurowania kopii zapasowej w trybie offline w konsoli usługi MAB jest wyświetlany następujący komunikat o błędzie.

![Nie można utworzyć zasad kopii zapasowej w trybie offline dla bieżącego konta platformy Azure](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Krok 2 weryfikacji

1. Otwórz folder **Temp** w ścieżce instalacji. Domyślna ścieżka folderu tymczasowego to *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp.* Poszukaj pliku *CBUICurr* i otwórz plik.

1. W pliku *CBUICurr* przewiń do ostatniego wiersza i sprawdź, czy problem jest taki sam jak w tym komunikacie o błędzie: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Obejście

Aby obejść ten problem, wykonaj poniższe kroki i ponów próbę konfiguracji zasad.

#### <a name="step-1-of-workaround"></a>Krok 1 obejścia

Zaloguj się do programu PowerShell wyświetlanego w interfejsie użytkownika usługi MAB przy użyciu innego konta z dostępem administratora do subskrypcji, w ramach urządzenie Data Box utworzone zadanie.

#### <a name="step-2-of-workaround"></a>Krok 2 obejścia

Jeśli żaden inny serwer nie ma skonfigurowanego iniekcjowania w trybie offline i żaden inny serwer nie jest zależny od `AzureOfflineBackup_<Azure User Id>` aplikacji, usuń tę aplikację. Wybierz **Azure Portal**  >  **Azure Active Directory**  >  **Rejestracje aplikacji**.

>[!NOTE]
> Sprawdź, czy aplikacja nie ma skonfigurowanego innego iniekcjowania w trybie offline, a także czy żaden `AzureOfflineBackup_<Azure User Id>` inny serwer nie jest zależny od tej aplikacji. Przejdź do **sekcji**  >  **Klucze ustawień** w sekcji **Klucze** publiczne. Nie powinny być w nim dodawane żadne inne klucze publiczne. Zobacz poniższy zrzut ekranu, aby uzyskać informacje.
>
>![Klucze publiczne](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Krok 3

Na serwerze, na którym próbujesz skonfigurować tworzenie kopii zapasowej w trybie offline, wykonaj następujące czynności.

1. Przejdź do karty **Zarządzanie certyfikatem komputera aplikacji** Osobiste i poszukaj  >   certyfikatu o nazwie `CB_AzureADCertforOfflineSeeding_<Timestamp>` .

2. Wybierz certyfikat, kliknij prawym przyciskiem myszy pozycję **Wszystkie zadania** i wybierz polecenie **Eksportuj** bez klucza prywatnego w formacie cer.

3. Przejdź do aplikacji kopii zapasowej w trybie offline platformy Azure wymienionej w kroku 2. Wybierz **pozycję Klucze**  >  **ustawień** Przekaż klucz  >  **publiczny.** Przekaż certyfikat wyeksportowany w poprzednim kroku.

    ![Przekazywanie klucza publicznego](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Na serwerze otwórz rejestr, wprowadzając **regedit** w oknie uruchamiania.

5. Przejdź do rejestru *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Kliknij prawym przyciskiem myszy **pozycję CloudBackupProvider** i dodaj nową wartość ciągu o nazwie `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Aby uzyskać identyfikator użytkownika platformy Azure, wykonaj jedną z tych czynności:
    >
    >- W programie PowerShell połączonym z platformą Azure uruchom `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` polecenie .
    > - Przejdź do ścieżki rejestru `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` o nazwie *CurrentUserId*.

6. Kliknij prawym przyciskiem myszy ciąg dodany w poprzednim kroku, a następnie wybierz polecenie **Modyfikuj.** W wartości podaj odcisk palca certyfikatu wyeksportowanego w kroku 2. Wybierz przycisk **OK**.

7. Aby uzyskać wartość odcisku palca, kliknij dwukrotnie certyfikat. Wybierz **kartę Szczegóły** i przewiń w dół, aż zobaczysz pole odcisku palca. Wybierz **pozycję Odcisk palca** i skopiuj wartość.

    ![Pole odcisku palca certyfikatu](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Pytania

W przypadku jakichkolwiek pytań lub wyjaśnień dotyczących problemów, które napotykasz, skontaktuj się z . [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
