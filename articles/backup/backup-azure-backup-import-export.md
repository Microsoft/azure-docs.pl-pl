---
title: Tworzenie kopii zapasowej w trybie offline za pomocą usługi Azure Import/Export
description: Dowiedz się, jak za pomocą usługi Azure Import/Export korzystać z Azure Backup do wysyłania danych z sieci. W tym artykule opisano umieszczanie w trybie offline początkowych danych kopii zapasowej za pomocą usługi Azure Import/Export.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 3ea470c2e732b7e0ef46e9e5fa78c744aa30c955
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98704367"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Przepływ pracy kopii zapasowej offline w Azure Backup

Azure Backup ma kilka wbudowanych wydajności, które oszczędzają koszty związane z siecią i magazynem podczas wstępnej pełnej kopii zapasowej danych na platformie Azure. Wstępne pełne kopie zapasowe zwykle przesyłają duże ilości danych i wymagają większej przepustowości sieci w porównaniu z kolejnymi kopiami zapasowymi, które przesyłają tylko różnice/przyrosty. W procesie umieszczania w trybie offline, Azure Backup mogą używać dysków do przekazywania danych kopii zapasowej w trybie offline do platformy Azure.

Azure Backup proces rozsadzenia w trybie offline jest ściśle zintegrowany z [usługą Azure Import/Export](../import-export/storage-import-export-service.md). Za pomocą tej usługi można transferować dane początkowej kopii zapasowej do platformy Azure przy użyciu dysków. Jeśli masz terabajty (TBs) początkowych danych kopii zapasowej, które muszą być transferowane za pośrednictwem sieci o dużej opóźnieniu i o niskiej przepustowości, możesz użyć przepływu pracy w trybie offline do wysłania początkowej kopii zapasowej na co najmniej jednym dysku twardym do centrum danych platformy Azure. Poniższy obraz zawiera przegląd kroków w przepływie pracy.

  ![Przegląd procesu przepływu pracy importowania w trybie offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

Proces tworzenia kopii zapasowej w trybie offline obejmuje następujące kroki:

1. Zamiast wysyłać dane kopii zapasowej za pośrednictwem sieci, Zapisz dane kopii zapasowej w lokalizacji tymczasowej.
1. Użyj narzędzia *AzureOfflineBackupDiskPrep* , aby zapisać dane w lokalizacji tymczasowej do co najmniej jednego dysku SATA.
1. W ramach pracy przygotowawczej narzędzie *AzureOfflineBackupDiskPrep* tworzy zadanie importu platformy Azure. Wyślij dyski SATA do najbliższego centrum danych platformy Azure i odwołuje się do zadania importowania, aby połączyć działania.
1. W centrum danych Azure dane na dyskach są kopiowane do konta usługi Azure Storage.
1. Azure Backup kopiuje dane kopii zapasowej z konta magazynu do magazynu Recovery Services, a przyrostowe kopie zapasowe są zaplanowane.

## <a name="supported-configurations"></a>Obsługiwane konfiguracje

Następujące Azure Backup funkcje lub obciążenia obsługują użycie kopii zapasowej offline w programie:

> [!div class="checklist"]
>
> * Tworzenie kopii zapasowych plików i folderów za pomocą agenta Microsoft Azure Recovery Services (MARS), nazywanego również agentem Azure Backup.
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików za pomocą programu System Center Data Protection Manager (DPM).
> * Tworzenie kopii zapasowych wszystkich obciążeń i plików z Microsoft Azure Backup Server.

   > [!NOTE]
   > Kopia zapasowa offline nie jest obsługiwana w przypadku kopii zapasowych stanu systemu wykonywanych przy użyciu agenta Azure Backup.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Wymagania wstępne

  > [!NOTE]
  > Poniższe wymagania wstępne i przepływ pracy dotyczą tylko kopii zapasowych plików i folderów w trybie offline przy użyciu [najnowszego agenta Recovery Services platformy Azure](https://aka.ms/azurebackup_agent). Aby wykonywać kopie zapasowe offline dla obciążeń przy użyciu programu System Center DPM lub Azure Backup Server, zobacz [przepływ pracy kopii zapasowej offline dla programu DPM i Azure Backup Server](backup-azure-backup-server-import-export.md).

Przed rozpoczęciem przepływu pracy tworzenia kopii zapasowej offline wykonaj następujące wymagania wstępne:

* Utwórz [magazyn Recovery Services](backup-azure-recovery-services-vault-overview.md). Aby utworzyć magazyn, wykonaj kroki opisane w temacie [Tworzenie magazynu Recovery Services](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault).
* Upewnij się, że tylko [Najnowsza wersja agenta Azure Backup](https://aka.ms/azurebackup_agent) jest zainstalowana w systemie Windows Server lub kliencie systemu Windows, w zależności od tego, czy komputer jest zarejestrowany w magazynie Recovery Services.
* Na komputerze z uruchomionym agentem Azure Backup jest wymagane Azure PowerShell 3.7.0. Pobierz i [Zainstaluj wersję 3.7.0 Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* Na komputerze z uruchomionym agentem Azure Backup upewnij się, że jest zainstalowany program Microsoft Edge lub Internet Explorer 11, a język JavaScript jest włączony.
* Utwórz konto usługi Azure Storage w tej samej subskrypcji co magazyn Recovery Services.
* Upewnij się, że masz odpowiednie [uprawnienia](../active-directory/develop/howto-create-service-principal-portal.md) do tworzenia aplikacji Azure Active Directory. Przepływ pracy kopii zapasowej offline tworzy aplikację Azure Active Directory w subskrypcji skojarzonej z kontem usługi Azure Storage. Celem aplikacji jest zapewnienie Azure Backup z bezpiecznym i dostępnym zakresem do usługi Azure Import/Export, która jest wymagana dla przepływu pracy kopii zapasowej offline.
* Zarejestruj dostawcę zasobów *Microsoft. ImportExport* z subskrypcją zawierającą konto usługi Azure Storage. Aby zarejestrować dostawcę zasobów:
    1. W menu głównym wybierz pozycję **subskrypcje**.
    1. Jeśli subskrybujesz wiele subskrypcji, wybierz subskrypcję, która ma być używana do tworzenia kopii zapasowych w trybie offline. Jeśli używasz tylko jednej subskrypcji, Twoja subskrypcja zostanie wyświetlona.
    1. W menu subskrypcja wybierz pozycję **dostawcy zasobów** , aby wyświetlić listę dostawców.
    1. Na liście dostawców przewiń w dół do *Microsoft. ImportExport*. Jeśli **stan** to **NotRegistered**, wybierz pozycję **zarejestruj**.

        ![Rejestrowanie dostawcy zasobów](./media/backup-azure-backup-import-export/registerimportexport.png)

* Tworzona jest lokalizacja tymczasowa, która może być udziałem sieciowym lub dodatkowym dyskiem na komputerze, wewnętrznym lub zewnętrznym, z wystarczającą ilością miejsca na dysku do przechowywania kopii początkowej. Na przykład jeśli chcesz utworzyć kopię zapasową serwera plików 500-GB, upewnij się, że obszar przejściowy jest co najmniej 500 GB. (Mniejsza ilość jest używana z powodu kompresji).
* W przypadku wysyłania dysków na platformę Azure należy użyć tylko 2,5-calowego dysku SSD lub 2,5 3,5-calowych dysków twardych SATA II/III. Możesz użyć dysków twardych o pojemności do 10 TB. Zapoznaj się z [dokumentacją usługi Azure Import/Export](../import-export/storage-import-export-requirements.md#supported-hardware) , aby zapoznać się z najnowszym zestawem dysków obsługiwanych przez usługę.
* Dyski SATA muszą być połączone z komputerem (nazywanym *komputerem kopiującym*), z którego są wykonywane kopie kopii zapasowych z lokalizacji tymczasowej do dysków SATA. Upewnij się, że funkcja BitLocker jest włączona na komputerze kopiowania.

## <a name="workflow"></a>Przepływ pracy

W tej sekcji opisano przepływ pracy tworzenia kopii zapasowych w trybie offline, dzięki czemu można dostarczać dane do centrum danych platformy Azure i przekazywać je do usługi Azure Storage. Jeśli masz pytania dotyczące usługi import lub dowolnego aspektu procesu, zobacz [dokumentację usługi Azure Import/Export — Omówienie](../import-export/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej offline

1. Po zaplanowaniu kopii zapasowej w agencie Recovery Services zostanie wyświetlona ta strona.

    ![Strona importowania](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Wybierz opcję **transfer przy użyciu moich własnych dysków**.

    > [!NOTE]
    > Użyj opcji Azure Data Box, aby przetransferować dane początkowej kopii zapasowej do trybu offline. Ta opcja umożliwia zaoszczędzenie nakładu pracy wymaganego do pozyskiwania własnych dysków zgodnych z platformą Azure. Zapewnia ona urządzenia firmowe, bezpieczne i Tamperproof Azure Data Box, do których dane kopii zapasowej mogą być bezpośrednio zapisywane przez Recovery Services agenta.

1. Wybierz pozycję Dalej, a **następnie** Wypełnij pola uważnie.

    ![Wprowadź szczegóły dysku](./media/backup-azure-backup-import-export/your-disk-details.png)

   Wypełnij pola:

    * **Lokalizacja tymczasowa**: tymczasowa lokalizacja przechowywania, w której zapisano początkową kopię zapasową. Tymczasowa lokalizacja może znajdować się w udziale sieciowym lub na komputerze lokalnym. Jeśli komputer kopii i komputer źródłowy są inne, określ pełną ścieżkę sieciową lokalizacji tymczasowej.
    * **Azure Resource Manager konto magazynu**: Nazwa Menedżer zasobówgo konta magazynu (ogólnego przeznaczenia w wersji 1 lub 2) w dowolnej subskrypcji platformy Azure.
    * **Kontener usługi Azure Storage**: Nazwa docelowego kontenera magazynu obiektów BLOB na koncie usługi Azure Storage, do którego importowane są dane kopii zapasowej przed skopiowaniem do magazynu Recovery Services.
    * **Identyfikator subskrypcji platformy Azure**: Identyfikator subskrypcji platformy Azure, w której tworzone jest konto usługi Azure Storage.
    * **Nazwa zadania importowania platformy Azure**: unikatowa nazwa, za pomocą której usługa Azure Import/Export i Azure Backup śledzi transfer danych wysyłanych na dyskach na platformę Azure.
  
   Po wypełnieniu pól wybierz pozycję **dalej**. Zapisz **lokalizację przemieszczania** i informacje o **nazwie zadania importowania platformy Azure** . Jest to wymagane do przygotowania dysków.

1. Po wyświetleniu monitu zaloguj się do subskrypcji platformy Azure. Musisz się zalogować, aby Azure Backup mógł utworzyć aplikację Azure Active Directory. Wprowadź wymagane uprawnienia, aby uzyskać dostęp do usługi Azure Import/Export.

    ![Strona logowania do subskrypcji platformy Azure](./media/backup-azure-backup-import-export/azure-login.png)

1. Zakończ przepływ pracy. W konsoli agenta Azure Backup wybierz pozycję **Wykonaj kopię zapasową teraz**.

    ![Wykonaj kopię zapasową teraz](./media/backup-azure-backup-import-export/backupnow.png)

1. Na stronie **potwierdzenie** kreatora wybierz pozycję **Utwórz kopię zapasową**. Początkowa kopia zapasowa jest zapisywana w obszarze tymczasowym w ramach instalacji.

   ![Potwierdź, że wszystko jest gotowe do wykonania kopii zapasowej teraz](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Po zakończeniu operacji lokalizacja tymczasowa jest gotowa do użycia podczas przygotowywania dysku.

   ![Strona Kreatora tworzenia kopii zapasowej teraz](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotowanie dysków SATA i dostarczenie do platformy Azure

Narzędzie *AzureOfflineBackupDiskPrep* przygotowuje dyski SATA, które są wysyłane do najbliższego centrum danych platformy Azure. To narzędzie jest dostępne w katalogu instalacji agenta Azure Backup w następującej ścieżce:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Przejdź do katalogu i Skopiuj katalog *AzureOfflineBackupDiskPrep* do innego komputera, na którym są połączone stacje dysków SATA. Na komputerze z podłączonymi dyskami SATA upewnij się, że:

   * Komputer kopiowania może uzyskać dostęp do lokalizacji tymczasowej dla przepływu pracy w trybie offline przy użyciu tej samej ścieżki sieciowej, która została podana w przepływie pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline".
   * Funkcja BitLocker jest włączona na komputerze kopiowania.
   * Zainstalowano Azure PowerShell 3.7.0.
   * Są zainstalowane najnowsze zgodne przeglądarki (Microsoft Edge lub Internet Explorer 11), a język JavaScript jest włączony.
   * Komputer kopiowania może uzyskać dostęp do Azure Portal. W razie potrzeby komputer kopii może być taki sam jak komputer źródłowy.

     > [!IMPORTANT]
     > Jeśli komputer źródłowy jest maszyną wirtualną, komputer do kopiowania musi być innym serwerem fizycznym lub komputerem klienckim z komputera źródłowego.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze kopiowania przy użyciu katalogu narzędzi *AzureOfflineBackupDiskPrep* jako bieżący katalog. Uruchom następujące polecenie:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parametr | Opis |
    | --- | --- |
    | s: &lt; *ścieżka lokalizacji tymczasowej*&gt; |To obowiązkowe dane wejściowe są używane w celu zapewnienia ścieżki do lokalizacji tymczasowej wprowadzonej w przepływie pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline". |
    | p: &lt; *ścieżka do PublishSettingsFile*&gt; |To opcjonalne dane wejściowe są używane w celu zapewnienia ścieżki do pliku ustawień publikowania platformy Azure.  |

    Po uruchomieniu polecenia Narzędzie żąda wyboru zadania importowania platformy Azure odpowiadającego dyskom, które muszą zostać przygotowane. Jeśli tylko jedno zadanie importu jest skojarzone z podaną lokalizacją przemieszczania, zobaczysz stronę podobną do tej.

    ![Dane wejściowe narzędzia przygotowywania dysku platformy Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Wprowadź literę dysku bez kropki końcowej dla zainstalowanego dysku, który ma zostać przygotowany do przeniesienia na platformę Azure.
1. Potwierdź formatowanie dysku po wyświetleniu monitu.
1. Zostanie wyświetlony monit o zalogowanie się do subskrypcji platformy Azure. Wprowadź poświadczenia.

    ![Logowanie do subskrypcji platformy Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Następnie narzędzie rozpocznie przygotowanie dysku i skopiowanie danych kopii zapasowej. Jeśli udostępniony dysk nie ma wystarczającej ilości miejsca na dane kopii zapasowej, może być konieczne dołączenie dodatkowych dysków po wyświetleniu monitu przez narzędzie. <br/>

    Po pomyślnym wykonaniu tego narzędzia wiersz polecenia zawiera trzy informacje:

   1. Co najmniej jeden z podanych dysków jest przygotowywany do wysyłania do platformy Azure.
   1. Otrzymasz potwierdzenie, że zadanie importowania zostało utworzone. Zadanie importowania używa podanej nazwy.
   1. Narzędzie wyświetla adres wysyłkowy centrum danych platformy Azure.

      ![Przygotowanie dysku platformy Azure zakończone](./media/backup-azure-backup-import-export/console2.png)<br/>

1. Po zakończeniu wykonywania polecenia można zaktualizować informacje o wysyłce.

1. Wyślij dyski na adres dostarczony przez to narzędzie. Zachowaj numer śledzenia do użytku w przyszłości.

   > [!IMPORTANT]
   > Żadne dwa zadania importowania platformy Azure nie mogą mieć tego samego numeru śledzenia. Upewnij się, że dyski przygotowane przez narzędzie w ramach pojedynczego zadania importowania platformy Azure są dostarczane razem w jednym pakiecie i że istnieje jeden unikatowy numer śledzenia dla pakietu. Nie łącz dysków przygotowanych w ramach oddzielnych zadań importowania platformy Azure w jednym pakiecie.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizowanie szczegółów wysyłki w ramach zadania importowania platformy Azure

Poniższa procedura umożliwia zaktualizowanie informacji o wysyłce zadania importowania platformy Azure. Informacje te zawierają szczegółowe informacje na temat:

* Nazwa operatora dostarczającego dyski do platformy Azure.
* Zwróć szczegóły dotyczące wysyłki dla dysków.

1. Zaloguj się do subskrypcji platformy Azure.
1. W menu głównym wybierz pozycję **wszystkie usługi**. W oknie dialogowym **wszystkie usługi** wprowadź **Import**. Po wyświetleniu **zadań Importuj/Eksportuj** wybierz ją.

    ![Wprowadź informacje o wysyłce](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Zostanie otwarte menu **zadania importu/eksportu** i zostanie wyświetlona lista wszystkich zadań importu/eksportu w wybranej subskrypcji.

1. Jeśli masz wiele subskrypcji, wybierz subskrypcję używaną do importowania danych kopii zapasowej. Następnie wybierz nowo utworzone zadanie importu, aby otworzyć jego szczegóły.

    ![Przejrzyj informacje o wysyłce](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. W menu **Ustawienia** zadania importowania wybierz pozycję **Zarządzaj informacjami o wysyłce**. Wprowadź szczegóły dotyczące wysyłki zwrotnej.

    ![Informacje o wysyłce magazynu](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Jeśli numer śledzenia jest używany przez firmę wysyłkową, wybierz transparent na stronie Przegląd zadania importowania platformy Azure i wprowadź następujące szczegóły.

   > [!IMPORTANT]
   > Upewnij się, że informacje o operatorze i numer śledzenia zostały zaktualizowane w ciągu dwóch tygodni od utworzenia zadania importowania platformy Azure. Niepowodzenie weryfikacji tych informacji w ciągu dwóch tygodni może skutkować usunięciem zadania i nieprzetwarzaniem dysków.

   ![Alert dotyczący aktualizacji informacji śledzenia](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informacje o przewoźniku i numer śledzenia](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Czas do przetworzenia dysków

Czas przetwarzania zadania importowania platformy Azure różni się. Czas procesu jest oparty na czynnikach, takich jak czas dostawy, typ zadania, typ i rozmiar kopiowanych danych oraz rozmiar podanego dysku. Usługa Azure Import/Export nie ma umowy SLA. Po odebraniu dysków usługa dokłada starań, aby zakończyć kopiowanie danych kopii zapasowej na konto usługi Azure Storage w ciągu 7 do 10 dni.

### <a name="monitor-azure-import-job-status"></a>Monitoruj stan zadania importowania platformy Azure

Stan zadania importowania można monitorować z Azure Portal. Przejdź do strony **Importowanie/Eksportowanie zadań** i wybierz zadanie. Aby uzyskać więcej informacji na temat stanu zadań importowania, zobacz [co to jest usługa Azure Import/Export?](../import-export/storage-import-export-service.md).

### <a name="finish-the-workflow"></a>Kończenie przepływu pracy

Po pomyślnym zakończeniu zadania importowania dane początkowej kopii zapasowej są dostępne na koncie magazynu. W czasie następnej zaplanowanej kopii zapasowej Azure Backup kopiuje zawartość danych z konta magazynu do magazynu Recovery Services.

   ![Kopiowanie danych do magazynu Recovery Services](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

W czasie następnej zaplanowanej kopii zapasowej Azure Backup wykonuje przyrostową kopię zapasową.

### <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu początkowej kopii zapasowej można bezpiecznie usunąć dane zaimportowane do kontenera usługi Azure Storage i dane kopii zapasowej w lokalizacji tymczasowej.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać odpowiedzi na pytania dotyczące przepływu pracy usługi Azure Import/Export, zobacz [Używanie usługi Microsoft Azure Import/Export do transferowania danych do magazynu obiektów BLOB](../import-export/storage-import-export-service.md).
