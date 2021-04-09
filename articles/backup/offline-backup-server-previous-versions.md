---
title: Kopia zapasowa offline dla Data Protection Manager (DPM) i Microsoft Azure Backup Server (serwera usługi MAB) — poprzednie wersje
description: Za pomocą Azure Backup można wysyłać dane z sieci za pomocą usługi Azure Import/Export. W tym artykule opisano przepływ pracy kopii zapasowej offline dla poprzednich wersji programu DPM i Azure Backup Server.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 0405ab66b7714f00349419e94bb064267ca711a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98702189"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>Przepływ pracy kopii zapasowej offline dla programu DPM i Azure Backup Server (poprzednie wersje)

>[!IMPORTANT]
>Te kroki dotyczą programu DPM 2019 RTM i starszych wersji oraz serwera usługi MAB v3 RTM i wcześniejszych wersji.

Azure Backup ma kilka wbudowanych wydajności, które oszczędzają koszty związane z siecią i magazynem podczas wstępnej pełnej kopii zapasowej danych na platformie Azure. Wstępne pełne kopie zapasowe zwykle przesyłają duże ilości danych i wymagają większej przepustowości sieci w porównaniu z kolejnymi kopiami zapasowymi, które przesyłają tylko różnice/przyrosty. Azure Backup kompresuje początkowe kopie zapasowe. W procesie umieszczania w trybie offline, Azure Backup mogą używać dysków do przekazywania skompresowanych danych początkowej kopii zapasowej w trybie offline do platformy Azure.

Proces rozsadzenia w trybie offline Azure Backup jest ściśle zintegrowany z [usługą Azure Import/Export](../import-export/storage-import-export-service.md). Ta usługa służy do transferowania danych do platformy Azure przy użyciu dysków. Jeśli masz terabajty (TBs) początkowych danych kopii zapasowej, które muszą być transferowane za pośrednictwem sieci o dużej opóźnieniu i o niskiej przepustowości, możesz użyć przepływu pracy w trybie offline, aby wysłać początkową kopię zapasową na co najmniej jednym dysku twardym do centrum danych platformy Azure. Ten artykuł zawiera omówienie i dalsze kroki kończące ten przepływ pracy dla programu System Center Data Protection Manager (DPM) i Microsoft Azure Backup Server (serwera usługi MAB).

> [!NOTE]
> Proces tworzenia kopii zapasowych w trybie offline dla agenta Microsoft Azure Recovery Services (MARS) różni się od programu DPM i serwera usługi MAB. Aby uzyskać informacje na temat używania kopii zapasowej offline z agentem MARS, zobacz [przepływ pracy kopii zapasowej offline w Azure Backup](backup-azure-backup-import-export.md). Kopia zapasowa offline nie jest obsługiwana w przypadku kopii zapasowych stanu systemu wykonywanych przy użyciu agenta Azure Backup.
>

## <a name="overview"></a>Omówienie

Korzystając z możliwości wypełniania w trybie offline Azure Backup i usługi Azure Import/Export, można łatwo przekazać dane w trybie offline na platformę Azure przy użyciu dysków. Proces tworzenia kopii zapasowej w trybie offline obejmuje następujące kroki:

> [!div class="checklist"]
>
> * Dane kopii zapasowej są zapisywane w lokalizacji tymczasowej, a nie przesyłane przez sieć.
> * Dane w lokalizacji tymczasowej są następnie zapisywane na co najmniej jednym dysku SATA przy użyciu narzędzia *AzureOfflineBackupDiskPrep* .
> * Zadanie importowania platformy Azure jest tworzone automatycznie przez narzędzie.
> * Dyski SATA są następnie wysyłane do najbliższego centrum danych platformy Azure.
> * Po zakończeniu przekazywania danych kopii zapasowej do platformy Azure Azure Backup kopiuje dane kopii zapasowej do magazynu kopii zapasowych, a przyrostowe kopie zapasowe są zaplanowane.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Kopia zapasowa offline jest obsługiwana dla wszystkich modeli wdrażania Azure Backup tworzenia kopii zapasowych danych z lokalizacji lokalnej do chmury firmy Microsoft. Modele te obejmują:

> [!div class="checklist"]
>
> * Tworzenie kopii zapasowych plików i folderów za pomocą agenta MARS lub agenta Azure Backup.
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików przy użyciu programu DPM.
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików z serwera usługi MAB.

>[!NOTE]
>Subskrypcje dostawcy CSP platformy Azure nie są obsługiwane do użycia z rozrzutem offline dla programu DPM 2019 RTM i starszych wersji oraz serwera usługi MAB v3 RTM i wcześniejszych wersji. Kopie zapasowe online przez sieć są nadal obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem przepływu pracy tworzenia kopii zapasowej offline upewnij się, że zostały spełnione następujące wymagania wstępne:

* Utworzono [magazyn Recovery Services](backup-azure-recovery-services-vault-overview.md) . Aby go utworzyć, wykonaj kroki opisane w temacie [Tworzenie magazynu Recovery Services](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Agent Azure Backup lub serwera usługi MAB lub DPM został zainstalowany w systemie Windows Server lub kliencie systemu Windows, w zależności od tego, czy komputer jest zarejestrowany w magazynie Recovery Services. Upewnij się, że jest używana tylko [Najnowsza wersja Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) .
* [Pobierz plik ustawień publikowania platformy Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) na komputerze, z którego planujesz utworzyć kopię zapasową danych. Subskrypcja, z której jest pobierany plik ustawień publikowania, może się różnić od subskrypcji zawierającej magazyn Recovery Services. Jeśli Twoja subskrypcja znajduje się w ramach suwerennych chmur platformy Azure, użyj następujących linków w celu pobrania pliku ustawień publikowania platformy Azure.

    | Region suwerennej chmury | Link pliku ustawień publikowania platformy Azure |
    | --- | --- |
    | Stany Zjednoczone | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | Chiny | [Link](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* W subskrypcji, z której pobrano plik ustawień publikowania, utworzono konto usługi Azure Storage z modelem wdrażania Menedżer zasobów. Na koncie magazynu Utwórz nowy kontener obiektów blob, który będzie używany jako miejsce docelowe.

  ![Tworzenie konta magazynu przy użyciu programu Menedżer zasobów Development](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* Tworzona jest lokalizacja tymczasowa, która może być udziałem sieciowym lub dodatkowym dyskiem na komputerze, wewnętrznym lub zewnętrznym, z wystarczającą ilością miejsca na dysku do przechowywania kopii początkowej. Na przykład jeśli chcesz utworzyć kopię zapasową serwera plików 500-GB, upewnij się, że obszar przejściowy jest co najmniej 500 GB. (Mniejsza ilość jest używana z powodu kompresji).
* W przypadku dysków wysyłanych do platformy Azure upewnij się, że są używane wewnętrzne dyski twarde SATA (2,5 cala) lub 2,5-calowe i 3,5 cala. Możesz użyć dysków twardych o pojemności do 10 TB. Zapoznaj się z [dokumentacją usługi Azure Import/Export](../import-export/storage-import-export-requirements.md#supported-hardware) , aby zapoznać się z najnowszym zestawem dysków obsługiwanych przez usługę.
* Dyski SATA muszą być połączone z komputerem (nazywanym *komputerem kopiującym*), z którego są wykonywane kopie kopii zapasowych z lokalizacji tymczasowej do dysków SATA. Upewnij się, że funkcja BitLocker jest włączona na komputerze kopiowania.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Przygotowywanie serwera do procesu tworzenia kopii zapasowej w trybie offline

>[!NOTE]
> Jeśli nie możesz znaleźć wymienionych narzędzi, takich jak *AzureOfflineBackupCertGen.exe*, w instalacji agenta Mars, Zapisz w celu AskAzureBackupTeam@microsoft.com uzyskania dostępu do nich.

* Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na serwerze i uruchom następujące polecenie:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    Narzędzie tworzy kopię zapasową offline usługi Azure Active Directory, jeśli taka nie istnieje.

    Jeśli aplikacja już istnieje, ten plik wykonywalny prosi o ręczne przekazanie certyfikatu do aplikacji w dzierżawie. Wykonaj kroki opisane w [tej sekcji](#manually-upload-an-offline-backup-certificate) , aby ręcznie przekazać certyfikat do aplikacji.

* Narzędzie *AzureOfflineBackupCertGen.exe* generuje plik *OfflineApplicationParams.xml* . Skopiuj ten plik na serwer z programem serwera usługi MAB lub DPM.
* Zainstaluj [najnowszego agenta Mars](https://aka.ms/azurebackup_agent) w wystąpieniu programu DPM lub serwerze Azure Backup.
* Zarejestruj serwer na platformie Azure.
* Uruchom następujące polecenie:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* Poprzednie polecenie tworzy plik `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` .

## <a name="manually-upload-an-offline-backup-certificate"></a>Ręczne przekazywanie certyfikatu kopii zapasowej offline

Wykonaj następujące kroki, aby ręcznie przekazać certyfikat kopii zapasowej offline do wcześniej utworzonej aplikacji Azure Active Directory przeznaczonej do tworzenia kopii zapasowych w trybie offline.

1. Zaloguj się w witrynie Azure Portal.
1. Przejdź do   >  **rejestracje aplikacji** Azure Active Directory.
1. Na karcie **posiadane aplikacje** Znajdź aplikację z formatem nazwy wyświetlanej `AzureOfflineBackup _<Azure User Id` .

    ![Znajdź aplikację na karcie aplikacje należące do firmy](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. Wybierz aplikację. W obszarze **Zarządzaj** w lewym okienku przejdź do pozycji **Certyfikaty & wpisy tajne**.
1. Sprawdź, czy istnieją już istniejące certyfikaty lub klucze publiczne. Jeśli nie istnieje, możesz bezpiecznie usunąć aplikację, wybierając przycisk **Usuń** na stronie **Przegląd** aplikacji. Następnie można ponowić próbę wykonania kroków w celu [przygotowania serwera do procesu tworzenia kopii zapasowej w trybie offline](#prepare-the-server-for-the-offline-backup-process) i pominąć poniższe kroki. W przeciwnym razie wykonaj następujące kroki z wystąpienia programu DPM lub serwera Azure Backup, na którym chcesz skonfigurować kopie zapasowe w trybie offline.
1. Z **menu Start** — **Uruchom**, wpisz *Certlm. msc*. W oknie **Certyfikaty — komputer lokalny** wybierz kartę **Certyfikaty — komputer lokalny**  >  **osobiste** . Poszukaj certyfikatu o nazwie `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
1. Wybierz certyfikat, kliknij prawym przyciskiem myszy **wszystkie zadania**, a następnie wybierz opcję **Eksportuj**, bez klucza prywatnego, w formacie CER.
1. Przejdź do aplikacji Azure offline Backup w Azure Portal.
1. Wybierz pozycję **Zarządzaj**  >  **certyfikatami & klucze tajne**  >  **Przekaż certyfikat**. Przekaż certyfikat wyeksportowany w poprzednim kroku.

    ![Przekazywanie certyfikatu](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. Na serwerze otwórz rejestr, wprowadzając polecenie **regedit** w oknie uruchamiania.
1. Przejdź do wpisu rejestru *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
1. Kliknij prawym przyciskiem myszy pozycję **CloudBackupProvider**, a następnie Dodaj nową wartość ciągu o nazwie `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Aby znaleźć identyfikator użytkownika platformy Azure, wykonaj jedną z następujących czynności:
    >
    >* Z poziomu programu PowerShell połączonego z platformą Azure Uruchom `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` polecenie.
    >* Przejdź do ścieżki rejestru `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` .

1. Kliknij prawym przyciskiem myszy ciąg dodany w poprzednim kroku, a następnie wybierz polecenie **Modyfikuj**. W polu wartość Podaj odcisk palca certyfikatu wyeksportowanego w kroku 7. Następnie wybierz przycisk **OK**.
1. Aby uzyskać wartość odcisku palca, kliknij dwukrotnie certyfikat. Wybierz kartę **szczegóły** i przewiń w dół do momentu wyświetlenia pola odcisk palca. Wybierz **odcisk palca** i skopiuj wartość.

    ![Kopiuj wartość z pola odcisku palca](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. Przejdź do sekcji [przepływ pracy](#workflow) , aby kontynuować proces tworzenia kopii zapasowej offline.

## <a name="workflow"></a>Przepływ pracy

Informacje przedstawione w tej sekcji ułatwiają zakończenie przepływu pracy tworzenia kopii zapasowych w trybie offline, aby dane mogły zostać dostarczone do centrum danych platformy Azure i przekazane do usługi Azure Storage. Jeśli masz pytania dotyczące usługi import lub dowolnego aspektu procesu, zapoznaj się z [dokumentacją dotyczącą przeglądu usługi Import Service](../import-export/storage-import-export-service.md) .

### <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej offline

1. Po zaplanowaniu kopii zapasowej zostanie wyświetlona następująca strona w systemie Windows Server, kliencie systemu Windows lub programie DPM.

    ![Strona importowania](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    Poniżej znajduje się odpowiednia strona w programie DPM. <br/>

    ![Strona importowania programu DPM i Azure Backup Server](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    Wypełnij pola:

   * **Lokalizacja tymczasowa**: tymczasowa lokalizacja przechowywania, w której zapisano początkową kopię zapasową. Tymczasowa lokalizacja może znajdować się w udziale sieciowym lub na komputerze lokalnym. Jeśli komputer kopii i komputer źródłowy są inne, określ pełną ścieżkę sieciową lokalizacji tymczasowej.
   * **Ustawienia publikowania platformy Azure**: ścieżka lokalna do pliku ustawień publikowania.
   * **Nazwa zadania importowania platformy Azure**: unikatowa nazwa, za pomocą której usługa Azure Import/Export i Azure Backup śledzi transfer danych wysyłanych na dyskach na platformę Azure.
   * **Identyfikator subskrypcji platformy Azure**: Identyfikator subskrypcji platformy Azure dla subskrypcji z lokalizacji, w której pobrano plik ustawień publikowania platformy Azure.
   * **Konto usługi Azure Storage**: nazwa konta magazynu w subskrypcji platformy Azure skojarzona z plikiem ustawień publikowania platformy Azure.
   * **Kontener usługi Azure Storage**: Nazwa docelowego obiektu blob magazynu na koncie usługi Azure Storage, do którego są importowane dane kopii zapasowej.

   Zapisz **lokalizację przemieszczania** i informacje o **nazwie zadania importowania platformy Azure** podane przez użytkownika. Jest to wymagane do przygotowania dysków.

1. Zakończ przepływ pracy. Aby zainicjować kopię zapasową offline, wybierz pozycję **Wykonaj kopię** zapasową teraz w konsoli zarządzania agentami Azure Backup. Początkowa kopia zapasowa jest zapisywana w obszarze przejściowym w ramach tego kroku.

    ![Utwórz kopię zapasową teraz](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    Aby zakończyć odpowiedni przepływ pracy w programie DPM lub Azure Backup Server, kliknij prawym przyciskiem myszy **grupę ochrony**. Wybierz opcję **Utwórz punkt odzyskiwania** . Następnie wybierz opcję **ochrony w trybie online** .

    ![Program DPM i serwera usługi MAB teraz kopię zapasową](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    Po zakończeniu operacji lokalizacja tymczasowa jest gotowa do użycia podczas przygotowywania dysku.

    ![Postęp tworzenia kopii zapasowej](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotowanie dysków SATA i dostarczenie do platformy Azure

Narzędzie *AzureOfflineBackupDiskPrep* służy do przygotowywania dysków SATA, które są wysyłane do najbliższego centrum danych platformy Azure. To narzędzie jest dostępne w katalogu instalacji agenta Recovery Services w następującej ścieżce:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Przejdź do katalogu i Skopiuj katalog *AzureOfflineBackupDiskPrep* do komputera kopii, na którym są połączone dyski SATA, które mają zostać przygotowane. Upewnij się, że:

   * Komputer kopiowania może uzyskać dostęp do lokalizacji tymczasowej dla przepływu pracy w trybie offline przy użyciu tej samej ścieżki sieciowej, która została podana w przepływie pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline".
   * Funkcja BitLocker jest włączona na komputerze kopiowania.
   * Komputer kopiowania może uzyskać dostęp do Azure Portal. W razie potrzeby komputer kopii może być taki sam jak komputer źródłowy.

     > [!IMPORTANT]
     > Jeśli komputer źródłowy jest maszyną wirtualną, należy użyć innego serwera fizycznego lub komputera klienckiego jako komputera kopii.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze kopiowania przy użyciu katalogu narzędzi *AzureOfflineBackupDiskPrep* jako bieżący katalog. Uruchom następujące polecenie:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parametr | Opis |
    | --- | --- |
    | s: &lt; *ścieżka lokalizacji tymczasowej*&gt; |To obowiązkowe dane wejściowe są używane w celu zapewnienia ścieżki do lokalizacji tymczasowej wprowadzonej w przepływie pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline". |
    | p: &lt; *ścieżka do PublishSettingsFile*&gt; |To opcjonalne dane wejściowe są używane w celu zapewnienia ścieżki do pliku ustawień publikowania platformy Azure wprowadzonego w przepływie pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline". |

    > [!NOTE]
    > &lt;Ścieżka do wartości AzurePublishSettingFile &gt; jest obowiązkowa, gdy komputer kopii i komputer źródłowy są różne.
    >
    >

    Po uruchomieniu polecenia Narzędzie żąda wyboru zadania importowania platformy Azure odpowiadającego dyskom, które muszą zostać przygotowane. Jeśli tylko jedno zadanie importu jest skojarzone z podaną lokalizacją przemieszczania, zobaczysz stronę podobną do tej.

    ![Dane wejściowe narzędzia przygotowywania dysku platformy Azure](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Wprowadź literę dysku bez kropki końcowej dla zainstalowanego dysku, który ma zostać przygotowany do przeniesienia na platformę Azure. Po wyświetleniu monitu podaj potwierdzenie formatowania dysku.

    Następnie narzędzie rozpocznie przygotowanie dysku i skopiowanie danych kopii zapasowej. Jeśli udostępniony dysk nie ma wystarczającej ilości miejsca na dane kopii zapasowej, może być konieczne dołączenie dodatkowych dysków po wyświetleniu monitu przez narzędzie. <br/>

    Po pomyślnym zakończeniu działania narzędzia zostanie przygotowany co najmniej jeden dostarczony dysk do wysyłki na platformę Azure. Zadanie importu o nazwie podanej podczas przepływu pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline" jest również tworzone na platformie Azure. Na koniec narzędzie wyświetla adres wysyłkowy centrum danych platformy Azure, w którym należy dostarczyć dyski.

    ![Przygotowanie dysku platformy Azure zakończone](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. Po zakończeniu wykonywania polecenia zobaczysz również opcję aktualizowania informacji o wysyłce.

    ![Aktualizuj opcję informacji o wysyłce](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. Szczegóły można wprowadzić od razu. Narzędzie przeprowadzi Cię przez proces, który obejmuje serię danych wejściowych. Jeśli nie masz informacji, takich jak numer śledzenia lub inne szczegóły związane z wysyłką, możesz zakończyć sesję. Procedurę aktualizowania szczegółów dotyczących wysyłki można znaleźć w dalszej części tego artykułu.

1. Wyślij dyski na adres dostarczony przez to narzędzie. Zachowaj numer śledzenia do użytku w przyszłości.

   > [!IMPORTANT]
   > Żadne dwa zadania importowania platformy Azure nie mogą mieć tego samego numeru śledzenia. Upewnij się, że dyski przygotowane przez narzędzie w ramach pojedynczego zadania importowania platformy Azure są dostarczane razem w jednym pakiecie i że istnieje jeden unikatowy numer śledzenia dla pakietu. Nie łącz dysków przygotowanych w ramach różnych zadań importowania platformy Azure w jednym pakiecie.

1. Jeśli masz informacje o numerze śledzenia, przejdź do komputera źródłowego, który oczekuje na ukończenie zadania importu. Uruchom następujące polecenie w wierszu polecenia z podwyższonym poziomem uprawnień z katalogiem narzędzi *AzureOfflineBackupDiskPrep* jako bieżącym katalogiem.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Opcjonalnie można uruchomić następujące polecenie z innego komputera, takiego jak komputer z kopią, z katalogiem narzędzia *AzureOfflineBackupDiskPrep* jako bieżącym katalogiem.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parametr | Opis |
    | --- | --- |
    | 't | To obowiązkowe dane wejściowe są używane do aktualizowania szczegółów wysyłki zadania importowania platformy Azure. |
    | s: &lt; *ścieżka lokalizacji tymczasowej*&gt; | To obowiązkowe dane wejściowe są używane, gdy polecenie nie jest uruchamiane na komputerze źródłowym. Służy do zapewnienia ścieżki do lokalizacji tymczasowej wprowadzonej w przepływie pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline". |
    | p: &lt; *ścieżka do PublishSettingsFile*&gt; | To obowiązkowe dane wejściowe są używane, gdy polecenie nie jest uruchamiane na komputerze źródłowym. Służy on do zapewnienia ścieżki do pliku ustawień publikowania platformy Azure wprowadzonego w przepływie pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline". |

    Narzędzie automatycznie wykrywa zadanie importu, które oczekuje na komputer źródłowy, lub zadania importu skojarzone z lokalizacją przejściową, gdy polecenie jest uruchamiane na innym komputerze. Następnie dostępna jest opcja aktualizacji informacji o wysyłce przez serię danych wejściowych.

    ![Wprowadź informacje o wysyłce](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. Po otrzymaniu wszystkich danych wejściowych Przejrzyj szczegóły i zatwierdź podane informacje o wysyłce, wprowadzając **wartość tak**.

    ![Przejrzyj informacje o wysyłce](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. Po pomyślnym zaktualizowaniu informacji o wysyłce Narzędzie udostępnia lokalizację lokalną, w której są przechowywane wprowadzone dane dotyczące wysyłki.

    ![Informacje o wysyłce magazynu](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > Upewnij się, że dyski osiągnęły centrum danych platformy Azure w ciągu dwóch tygodni od dostarczenia informacji o wysyłce przy użyciu narzędzia *AzureOfflineBackupDiskPrep* . Niewykonanie tej czynności może spowodować, że dyski nie zostaną przetworzone.

Po wykonaniu poprzednich kroków centrum danych platformy Azure jest gotowe do odebrania dysków i przetworzenia ich w celu przetransferowania danych kopii zapasowej z dysków do klasycznego typu konta usługi Azure Storage.

### <a name="time-to-process-the-drives"></a>Czas do przetworzenia dysków

Czas przetwarzania zadania importowania platformy Azure różni się. Czas procesu zależy od czynników, takich jak czas wysyłki, typ zadania, typ i rozmiar kopiowanych danych oraz rozmiar dostarczonych dysków. Usługa Azure Import/Export nie ma umowy SLA. Po odebraniu dysków usługa dokłada starań, aby zakończyć kopiowanie danych kopii zapasowej na konto usługi Azure Storage w ciągu 7 do 10 dni. W następnej sekcji opisano, jak można monitorować stan zadania importowania platformy Azure.

### <a name="monitor-azure-import-job-status"></a>Monitoruj stan zadania importowania platformy Azure

Gdy dyski są w trakcie przesyłania lub do centrum danych platformy Azure, które mają zostać skopiowane do konta magazynu, Agent Azure Backup lub program DPM lub konsola serwera usługi MAB na komputerze źródłowym pokazuje następujący stan zadania dla zaplanowanych kopii zapasowych:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Aby sprawdzić stan zadania importowania:

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze źródłowym i uruchom następujące polecenie:

     `AzureOfflineBackupDiskPrep.exe u:`

1. Dane wyjściowe przedstawiają bieżący stan zadania importu.

    ![Sprawdź stan zadania importowania](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Aby uzyskać więcej informacji na temat różnych stanów zadania importowania platformy Azure, zobacz [Wyświetlanie stanu zadań importu/eksportu platformy Azure](../import-export/storage-import-export-view-drive-status.md).

### <a name="finish-the-workflow"></a>Kończenie przepływu pracy

Po zakończeniu zadania importowania dane początkowej kopii zapasowej są dostępne na koncie magazynu. W czasie następnej zaplanowanej kopii zapasowej Azure Backup kopiuje zawartość danych z konta magazynu do magazynu Recovery Services.

   ![Kopiowanie danych do magazynu Recovery Services](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

Podczas kolejnej zaplanowanej kopii zapasowej Program Azure Backup wykonuje przyrostową kopię zapasową za pomocą początkowej kopii w celu utworzenia.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać odpowiedzi na pytania dotyczące przepływu pracy usługi Azure Import/Export, zobacz [Używanie usługi Microsoft Azure Import/Export do transferowania danych do magazynu obiektów BLOB](../import-export/storage-import-export-service.md).
