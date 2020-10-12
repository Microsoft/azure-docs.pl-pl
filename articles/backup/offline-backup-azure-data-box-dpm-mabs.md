---
title: Kopia zapasowa offline z Azure Data Boxami dla programu DPM i serwera usługi MAB
description: Za pomocą Azure Data Box można wypełniać początkowe dane kopii zapasowej w trybie offline z programu DPM i serwera usługi MAB.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 2fd8a137abf8b76d1587894bfa3fe8447e0d646b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91271498"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Używanie Azure Data Box dla programu DPM i serwera usługi MAB (wersja zapoznawcza)

> [!NOTE]
> Ta funkcja ma zastosowanie do Data Protection Manager (DPM) 2019 UR2 i nowszych.<br><br>
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej serwera Microsoft Azure Backup (serwera usługi MAB). Jeśli interesuje Cię korzystanie z Azure Data Box do rozsadzenia w trybie offline za pomocą serwera usługi MAB, skontaktuj się z nami w serwisie [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) .

W tym artykule wyjaśniono, jak używać Azure Data Box do tworzenia początkowych danych kopii zapasowej w trybie offline z programu DPM i serwera usługi MAB do magazynu Recovery Services platformy Azure.

Za pomocą [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) można wypełniać duże początkowe kopie zapasowe programu DPM/serwera usługi MAB w trybie offline (bez użycia sieci) do magazynu Recovery Services. Ten proces oszczędza czas i przepustowość sieci, w przypadku których w przeciwnym razie można przenieść duże ilości danych kopii zapasowej w tryb online w sieci o dużej opóźnieniu. Ta funkcja jest obecnie w wersji zapoznawczej.

Kopia zapasowa offline oparta na Azure Data Box zapewnia dwie różne zalety [tworzenia kopii zapasowych w trybie offline na podstawie usługi Azure Import/Export](backup-azure-backup-server-import-export.md):

- Nie musisz kupować własnych dysków i łączników zgodnych z platformą Azure. Azure Data Box dostarcza dyski skojarzone z wybraną jednostką [SKU urządzenie Data Box](https://azure.microsoft.com/services/databox/data/).

- Azure Backup (Agent MARS) może bezpośrednio zapisywać dane kopii zapasowej na obsługiwanych jednostkach SKU Azure Data Box. Ta funkcja eliminuje konieczność udostępniania lokalizacji tymczasowej dla danych początkowej kopii zapasowej. Nie są też potrzebne narzędzia do formatowania i kopiowania tych danych na dyski.

## <a name="supported-platforms"></a>Obsługiwane platformy

Obsługiwane są następujące platformy:

- Windows Server 2019 64 bit (standard, Datacenter, Essentials)
- Windows Server 2016 64 bit (standard, Datacenter, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Rozmiar danych kopii zapasowych i obsługiwane urządzenie Data Box jednostek SKU

Obsługiwane są następujące jednostki SKU urządzenie Data Box:

| Rozmiar danych kopii zapasowej (po kompresji przez MARS) \* na serwer | Obsługiwana Azure Data Box jednostka SKU |
| --- | --- |
| \<= 7,2 TB | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB i <= 80 TB\*\* | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\*Typowe stawki kompresji różnią się w zależności od 10-20% <br>
\*\*Skontaktuj się z, [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) jeśli oczekujesz, że masz więcej niż 80 TB początkowej kopii zapasowej danych dla pojedynczego źródła danych.

> [!IMPORTANT]
> Początkowe dane kopii zapasowej z pojedynczego źródła danych muszą być zawarte w obrębie jednego Azure Data Box lub Azure Data Box dysku i nie mogą być współużytkowane przez wiele urządzeń z tymi samymi lub różnymi jednostkami SKU. Jednak Azure Data Box mogą zawierać początkowe kopie zapasowe z wielu źródeł danych.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Agent MARS uruchomiony w programie DPM/serwera usługi MAB powinien zostać uaktualniony do [najnowszej wersji](https://aka.ms/azurebackup_agent) (2.0.9171.0 lub nowszej).

Uwaga:

### <a name="azure-subscription-and-required-permissions"></a>Subskrypcja platformy Azure i wymagane uprawnienia

- Prawidłowa subskrypcja platformy Azure.
- Użytkownik, który ma wykonać zasady kopii zapasowej offline, musi być właścicielem subskrypcji platformy Azure.
- Zadanie urządzenie Data Box i magazyn Recovery Services, do którego należy umieścić dane, muszą być dostępne w tych samych subskrypcjach.
    > [!NOTE]
    > Zalecamy, aby docelowe konto magazynu i magazyn Recovery Services znajdować się w tym samym regionie. Nie jest to jednak wymagane.

### <a name="order-and-receive-the-data-box-device"></a>Zamówienie i odbieranie urządzenia urządzenie Data Box

Przed wyzwoleniem kopii zapasowej offline upewnij się, że wymagane urządzenia urządzenie Data Box są w stanie *dostarczone* . Zobacz [rozmiar danych kopii zapasowej i obsługiwane urządzenie Data Box jednostek SKU](#backup-data-size-and-supported-data-box-skus) , aby zamówić najbardziej odpowiednią jednostkę SKU dla danego wymagania. Wykonaj kroki opisane w [tym artykule](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) , aby zamówić i odbierać urządzenie Data Box urządzenia.

> [!IMPORTANT]
> Nie wybieraj *BlobStorage* dla **rodzaju konta**. Serwer DPM/serwera usługi MAB wymaga konta, które obsługuje stronicowe obiekty blob, które nie są obsługiwane, jeśli wybrano *BlobStorage* . Wybierz pozycję  **Storage v2 (ogólnego przeznaczenia w wersji 2)** jako **rodzaj konta** podczas tworzenia docelowego konta magazynu dla zadania Azure Data Box.

![Konfigurowanie usługi Azure DATAbox](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Konfigurowanie Azure Data Box urządzeń

Po otrzymaniu urządzenia Azure Data Box, w zależności od uporządkowanej Azure Data Box jednostki SKU, wykonaj kroki opisane w odpowiednich sekcjach poniżej, aby skonfigurować i przygotować urządzenia urządzenie Data Box dla serwera programu DPM/serwera usługi MAB w celu identyfikowania i przesyłania danych początkowej kopii zapasowej.

### <a name="setup-azure-data-box-disk"></a>Azure Data Box dysk instalacyjny

Jeśli zostały uporządkowane co najmniej jeden Azure Data Box dysków (do 8 TB), wykonaj kroki opisane [tutaj](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up) , aby rozpakować, połączyć i odblokować dysk urządzenie Data Box.

> [!NOTE]
> Istnieje możliwość, że serwer DPM/serwera usługi MAB nie ma portu USB. W takim scenariuszu można podłączyć dysk Azure Data Box do innego serwera/klienta i uwidocznić katalog główny urządzenia jako udział sieciowy.

## <a name="setup-azure-data-box"></a>Azure Data Box Instalatora

Jeśli użytkownik zamówił Azure Data Box (do 100 TB), wykonaj kroki opisane [tutaj](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) , aby skonfigurować urządzenie Data Box.

### <a name="mount-your-azure-data-box-as-local-system"></a>Zainstaluj Azure Data Box jako system lokalny

Serwer DPM/serwera usługi MAB działa w kontekście systemu i dlatego wymaga podania tego samego poziomu uprawnień do ścieżki instalacji, w której Azure Data Box jest połączony. Wykonaj poniższe kroki, aby upewnić się, że urządzenie urządzenie Data Box można zainstalować jako system lokalny przy użyciu protokołu NFS.

1. Włącz opcję Klient dla systemu plików NFS na serwerze DPM/serwera usługi MAB.
Określ alternatywne źródło: *wim: D: \Sources\Install.wim: 4*
2. Pobierz **PSExec** z [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) serwera programu DPM/serwera usługi MAB.
3. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i uruchom następujące polecenie z katalogiem zawierającym *PSExec.exe* jako bieżący katalog.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. Okno polecenia otwierane w wyniku powyższego polecenia znajduje się w kontekście systemu lokalnego. To okno polecenia służy do wykonywania kroków w celu zainstalowania udziału usługi Azure Page BLOB jako dysku sieciowego na serwerze z systemem Windows.
5. Wykonaj [poniższe kroki,](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) aby połączyć serwer programu DPM/serwera usługi MAB z urządzeniem urządzenie Data Box za pośrednictwem systemu plików NFS, a następnie uruchom następujące polecenie w wierszu polecenia systemu lokalnego, aby zainstalować udział obiektów BLOB na stronie platformy Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. Po zainstalowaniu Sprawdź, czy możesz uzyskać dostęp do X: z serwera. Jeśli chcesz, przejdź do następnej sekcji tego artykułu.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transferowanie danych początkowej kopii zapasowej do urządzeń Azure Data Box

1. Na serwerze DPM/serwera usługi MAB postępuj zgodnie z instrukcjami, aby [utworzyć nową grupę ochrony](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Jeśli dodajesz ochronę w trybie online do istniejącej grupy ochrony, kliknij prawym przyciskiem myszy istniejącą grupę ochrony, a następnie wybierz pozycję **Dodaj ochronę w trybie online** i zacznij od **kroku 8**.
2. Na stronie **Wybierz członków grupy** Określ komputery i źródła, dla których chcesz utworzyć kopię zapasową.
3. Na stronie **Wybierz metodę ochrony danych** Określ sposób obsługi krótkoterminowej i długoterminowej kopii zapasowej. Upewnij się, że wybrano opcję **Chcę chronić w trybie online.**

   ![Utwórz nową grupę ochrony](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. Na stronie **Wybierz cele krótkoterminowe** Określ, jak chcesz utworzyć kopię zapasową do krótkoterminowego przechowywania na dysku.
5. Na stronie **Przejrzyj przydział dysku** Przejrzyj miejsce na dysku w puli magazynów przydzielone danej grupie ochrony.
6. Na stronie **Wybierz metodę tworzenia repliki** wybierz opcję **automatycznie przez sieć.**
7. Na stronie **Wybierz opcje sprawdzania spójności** wybierz sposób automatyzacji sprawdzania spójności.
8. Na stronie **Określ dane ochrony w trybie online** Wybierz członka, który chcesz włączyć ochrony online.

    ![Określ dane ochrony w trybie online](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. Na stronie **Określanie harmonogramu tworzenia kopii zapasowych online** Określ, jak często mają być wykonywane przyrostowe kopie zapasowe na platformie Azure.
10. Na stronie **Określanie zasad przechowywania w trybie online** Określ, w jaki sposób punkty odzyskiwania utworzone na podstawie kopii zapasowych na dzień/tydzień/miesiąc/rok mają być przechowywane na platformie Azure.
11. Na ekranie **Wybieranie replikacji online** kreatora wybierz opcję **transfer przy użyciu dysków należących do firmy Microsoft** i wybierz przycisk **dalej**.

    ![Wybierz początkową replikację online](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > Opcja **przeniesienia przy użyciu dysków należących do firmy Microsoft** nie jest dostępna dla serwera usługi MAB v3, ponieważ ta funkcja jest w wersji zapoznawczej. Skontaktuj się z nami, [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) Jeśli chcesz użyć tej funkcji dla serwera usługi MAB v3.

12. Po wyświetleniu monitu zaloguj się do platformy Azure przy użyciu poświadczeń użytkownika mających dostęp właściciela do subskrypcji platformy Azure. Po pomyślnym zalogowaniu zostanie wyświetlony następujący ekran:

    ![Po pomyślnym zalogowaniu](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     Następnie serwer DPM/serwera usługi MAB pobierze zadania urządzenie Data Box w subskrypcji, która jest w stanie *dostarczone* .

     > [!NOTE]
     > Pierwsze logowanie trwa dłużej niż zwykle. Moduł Azure PowerShell jest instalowany w tle, a także aplikacja usługi Azure AD jest zarejestrowana.
     >
     >  - Następujące moduły programu PowerShell są zainstalowane:<br>
          -AzureRM. profile     *5.8.3*<br>
          -AzureRM. resources   *6.7.3*<br>
          -AzureRM. Storage     *5.2.0*<br>
          — Azure. Storage       *4.6.1*<br>
     >  - Aplikacja usługi Azure AD jest zarejestrowana *jako \<object GUID of the user> AzureOfflineBackup_*.

13. Wybierz prawidłową kolejność pól danych, dla której zostało rozpakowane, połączone i odblokowane urządzenie Data Box dysk. Wybierz opcję **Dalej**.

    ![Wybierz DATAbox](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. Na ekranie **wykrywanie DataBox** wprowadź ścieżkę do urządzenia urządzenie Data Box, a następnie wybierz pozycję **Wykryj urządzenie**.

    ![Wprowadź ścieżkę sieciową](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Podaj ścieżkę sieciową do katalogu głównego dysku Azure Data Box. Ten katalog musi zawierać katalog o nazwie *PageBlob* , jak pokazano poniżej:
    >
    > ![Dysk USB](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Na przykład jeśli ścieżka dysku to `\\mydomain\myserver\disk1\` i *disk1* zawiera katalog o nazwie *PageBlob*, ścieżka do podanego w Kreatorze serwera DPM/serwera usługi MAB jest `\\mydomain\myserver\disk1\` .
    > W przypadku [skonfigurowania urządzenia z Azure Data Box 100 TB](https://docs.microsoft.com/azure/backup/offline-backup-azure-data-box#setup-azure-data-box)należy podać następujące elementy jako ścieżkę sieciową do urządzenia `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` .

15. Wybierz opcję **Dalej**. Na stronie **Podsumowanie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz grupę**.

    ![Wykrywanie DATAbox](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    Poniższy ekran potwierdza, że grupa ochrony została utworzona pomyślnie.

    ![Utworzono grupę ochrony](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. Na ekranie powyżej wybierz pozycję **Zamknij** .

    W takim przypadku początkowa replikacja danych odbywa się na dysk programu DPM/serwera usługi MAB. Po zakończeniu ochrony stan grupy będzie wyświetlany jako **OK** na stronie **Ochrona** .

17. Aby zainicjować kopię zapasową offline na urządzeniu Azure Data Box, kliknij prawym przyciskiem myszy **grupę ochrony**, a następnie wybierz opcję **Utwórz punkt odzyskiwania** . Następnie wybierz opcję **ochrony w trybie online** .

    ![Utwórz punkt odzyskiwania](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Punkt odzyskiwania](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   Serwer DPM/serwera usługi MAB rozpocznie tworzenie kopii zapasowych wybranych danych na urządzeniu Azure Data Box. Może to potrwać od kilku godzin do kilku dni, w zależności od rozmiaru danych i szybkości połączenia między serwerem DPM/serwera usługi MAB i Azure Data Box Disk.

   Stan zadania można monitorować w okienku **monitorowanie** . Po zakończeniu tworzenia kopii zapasowej danych zobaczysz ekran podobny do przedstawionego poniżej:

   ![Konsola administratora](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Kroki po wykonaniu kopii zapasowej

Wykonaj następujące kroki, gdy kopia zapasowa danych Azure Data Box Disk zostanie zakończona pomyślnie.

- Wykonaj kroki opisane w [tym artykule](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up) , aby dostarczyć Azure Data Box dysk do platformy Azure. Jeśli używasz urządzenia z systemem Azure Data Box 100 TB, wykonaj [następujące kroki](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) , aby dostarczyć Azure Data Box do platformy Azure.
- [Monitoruj zadanie urządzenie Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) w Azure Portal. Po *zakończeniu*zadania Azure Data Box serwer programu DPM/serwera usługi MAB automatycznie przenosi dane z konta magazynu do magazynu Recovery Services w czasie następnej zaplanowanej kopii zapasowej. Oznacza to, że zadanie tworzenia kopii zapasowej zostanie oznaczone jako *ukończone* , jeśli punkt odzyskiwania zostanie pomyślnie utworzony.

  > [!NOTE]
  > Serwer DPM/serwera usługi MAB wyzwala kopie zapasowe w planowanych godzinach podczas tworzenia grupy ochrony. Jednak te zadania będą flagą *oczekiwanie na ukończenie zadania Azure Data Box* do czasu ukończenia zadania.

- Gdy serwer programu DPM/serwera usługi MAB pomyślnie utworzy punkt odzyskiwania odpowiadający początkowej kopii zapasowej, możesz usunąć konto magazynu (lub określoną zawartość) skojarzone z zadaniem Azure Data Box.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Agent Microsoft Azure Backup (MAB) na serwerze programu DPM tworzy w dzierżawie aplikację usługi Azure AD. Ta aplikacja wymaga certyfikatu do uwierzytelnienia, które zostało utworzone i przekazane podczas konfigurowania zasad umieszczania w trybie offline.

Używamy Azure PowerShell do tworzenia i przekazywania certyfikatu do aplikacji usługi Azure AD.

### <a name="issue"></a>Problem

W momencie konfigurowania kopii zapasowej w trybie offline ze względu na znaną usterkę kodu w poleceniu cmdlet Azure PowerShell nie można dodać wielu certyfikatów do tej samej aplikacji usługi Azure AD utworzonej przez agenta MAB. Będzie to miało wpływ na to, jeśli skonfigurowano zasady wypełniania w trybie offline dla tego samego lub innego serwera.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Sprawdź, czy problem jest spowodowany przez tę konkretną przyczynę główną

Aby upewnić się, że przyczyną błędu jest ten [problem](#issue) , wykonaj jedną z następujących czynności:

#### <a name="step-1"></a>Krok 1

Sprawdź, czy podczas konfigurowania kopii zapasowej offline w konsoli programu DPM/serwera usługi MAB jest wyświetlany następujący komunikat o błędzie:

![Agent usług odzyskiwania Azure](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>Krok 2

1. Otwórz folder **temp** w ścieżce instalacji (domyślna ścieżka folderu tymczasowego to *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Wyszukaj plik *CBUICurr* i Otwórz plik.
2. W pliku *CBUICurr* przewiń do ostatniego wiersza i sprawdź, czy przyczyną błędu jest nie można utworzyć poświadczenia aplikacji usługi Azure AD na koncie klienta. Wyjątek: Aktualizacja istniejącego poświadczenia z KeyId \<some guid> jest niedozwolona.

### <a name="workaround"></a>Obejście

Aby rozwiązać ten problem, wykonaj następujące kroki i spróbuj ponownie wykonać konfigurację zasad.

1. Zaloguj się do strony logowania platformy Azure, która jest wyświetlana w interfejsie użytkownika serwera DPM/serwera usługi MAB przy użyciu innego konta z dostępem administratora w ramach subskrypcji, która ma utworzone zadanie importowania eksportu.
2. Jeśli żaden inny serwer nie ma skonfigurowanych wypełnień w trybie offline i żaden inny serwer nie jest zależny od `AzureOfflineBackup_<Azure User Id>` aplikacji, Usuń tę aplikację z **Azure Portal > Azure Active Directory > rejestracje aplikacji**.

   > [!NOTE]
   > Sprawdź, czy aplikacja `AzureOfflineBackup_<Azure User Id>` nie ma skonfigurowanych żadnych innych rozliczeń w trybie offline, a także czy żaden inny serwer nie jest zależny od tej aplikacji. Przejdź do pozycji **ustawienia > klucze** w sekcji klucze publiczne. Nie należy dodawać żadnych innych **kluczy publicznych** . Aby uzyskać informacje, zobacz następujący zrzut ekranu:
   >
   > ![Klucze publiczne](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>Krok 3

Na serwerze DPM/serwera usługi MAB próbujesz skonfigurować kopię zapasową offline, wykonaj następujące czynności:

1. Otwórz kartę **osobisty zarządzanie certyfikatem komputera**  >  **Personal** i poszukaj certyfikatu o nazwie `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
2. Wybierz certyfikat powyżej, kliknij prawym przyciskiem myszy **wszystkie zadania** i **Eksportuj** bez klucza prywatnego w formacie CER.
3. Przejdź do aplikacji usługi Azure offline Backup wymienionej w **punkcie 2**. W polu **Ustawienia**  >  **klucze**  >  **Przekaż klucz publiczny** Przekaż certyfikat wyeksportowany w powyższym kroku.

   ![Przekaż klucze publiczne](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. Na serwerze otwórz rejestr, wpisując **regedit** w oknie **uruchamiania** .
5. Przejdź do rejestru *Computer\HKEY \_ Local \_ MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*. Kliknij prawym przyciskiem myszy pozycję **CloudBackupProvider** i Dodaj nową wartość ciągu o nazwie `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Aby uzyskać identyfikator użytkownika platformy Azure, wykonaj jedną z następujących czynności:
    >
    >- Z poziomu programu PowerShell połączonego z platformą Azure Uruchom `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` polecenie.
    > - Przejdź do ścieżki rejestru `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` o nazwie *CurrentUserId*.

6. Kliknij prawym przyciskiem myszy ciąg dodany w powyższym kroku i wybierz polecenie **Modyfikuj**. W polu wartość Podaj odcisk palca certyfikatu wyeksportowanego w **punkcie 2** i wybierz **przycisk OK**.
7. Aby uzyskać wartość odcisku palca, kliknij dwukrotnie certyfikat, a następnie wybierz pozycję **szczegóły**  i przewiń w dół do momentu wyświetlenia pola odcisk palca. Wybierz **odcisk palca** i skopiuj wartość.

   ![Wartość odcisku palca](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Następne kroki

- [Rozmieszczanie w trybie offline przy użyciu własnego dysku (za pomocą usługi Azure Import/Export)](backup-azure-backup-server-import-export.md)
