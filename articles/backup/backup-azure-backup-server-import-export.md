---
title: Kopia zapasowa offline dla programu DPM i usługi Azure Backup Server
description: Za pomocą Azure Backup można wysyłać dane z sieci za pomocą usługi Azure Import/Export. W tym artykule wyjaśniono przepływ pracy kopii zapasowej offline dla programu DPM i Azure Backup Server.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 006c0fa4d67c9a85426d7a007912df65876313da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98701817"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>Przepływ pracy kopii zapasowej offline dla programu DPM i Azure Backup Server (serwera usługi MAB)

>[!IMPORTANT]
> Te kroki dotyczą programu DPM 2019 UR1 (lub nowszego) i serwera usługi MAB v3 UR1 (lub nowszego).

System Center Data Protection Manager i Azure Backup Server (serwera usługi MAB) integrują się z Azure Backup i używają kilku wbudowanych wydajności, które oszczędzają koszty sieci i magazynowania podczas wstępnego tworzenia pełnych kopii zapasowych danych na platformie Azure. Wstępne pełne kopie zapasowe zwykle przesyłają duże ilości danych i wymagają większej przepustowości sieci w porównaniu z kolejnymi kopiami zapasowymi, które przesyłają tylko różnice/przyrosty. Azure Backup kompresuje początkowe kopie zapasowe. W procesie umieszczania w trybie offline, Azure Backup mogą używać dysków do przekazywania skompresowanych danych początkowej kopii zapasowej w trybie offline do platformy Azure.

Proces rozsadzenia w trybie offline Azure Backup jest ściśle zintegrowany z [usługą Azure Import/Export](../import-export/storage-import-export-service.md). Ta usługa służy do transferowania danych do platformy Azure przy użyciu dysków. Jeśli masz terabajty (TBs) początkowych danych kopii zapasowej, które muszą być transferowane za pośrednictwem sieci o dużej opóźnieniu i o niskiej przepustowości, możesz użyć przepływu pracy w trybie offline, aby wysłać początkową kopię zapasową na co najmniej jednym dysku twardym do centrum danych platformy Azure. Ten artykuł zawiera omówienie i dalsze kroki kończące ten przepływ pracy dla programu System Center Data Protection Manager (DPM) i Microsoft Azure Backup Server (serwera usługi MAB).

> [!NOTE]
> Proces tworzenia kopii zapasowych w trybie offline dla agenta Microsoft Azure Recovery Services (MARS) różni się od programu DPM i serwera usługi MAB. Aby uzyskać informacje na temat używania kopii zapasowej offline z agentem MARS, zobacz [przepływ pracy kopii zapasowej offline w Azure Backup](backup-azure-backup-import-export.md). Kopia zapasowa offline nie jest obsługiwana w przypadku kopii zapasowych stanu systemu wykonywanych przy użyciu agenta Azure Backup.
>
> SERWERA usługi MAB UR1 Update umożliwia również podgląd kopii zapasowej offline przy użyciu Azure Data Box w serwera usługi MAB. Skontaktuj się z nami [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) , aby dowiedzieć się więcej.

## <a name="overview"></a>Omówienie

Korzystając z możliwości wypełniania w trybie offline Azure Backup i usługi Azure Import/Export, można łatwo przekazać dane w trybie offline na platformę Azure przy użyciu dysków. Proces tworzenia kopii zapasowej w trybie offline obejmuje następujące kroki:

> [!div class="checklist"]
>
> * Dane kopii zapasowej są zapisywane w lokalizacji tymczasowej, a nie przesyłane przez sieć.
> * Dane w lokalizacji tymczasowej są następnie zapisywane na co najmniej jednym dysku SATA przy użyciu narzędzia *AzureOfflineBackupDiskPrep* .
> * Zadanie importowania platformy Azure jest tworzone automatycznie przez narzędzie.
> * Dyski SATA są następnie wysyłane do najbliższego centrum danych platformy Azure.
> * Po zakończeniu przekazywania danych kopii zapasowej do platformy Azure Azure Backup kopiuje dane kopii zapasowej do magazynu kopii zapasowych, a przyrostowe kopie zapasowe są zaplanowane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed uruchomieniem przepływu pracy tworzenia kopii zapasowej offline upewnij się, że zostały spełnione następujące wymagania wstępne:

* Utworzono [magazyn Recovery Services](backup-azure-recovery-services-vault-overview.md) . Aby go utworzyć, wykonaj kroki opisane w samouczku [Tworzenie magazynu Recovery Servicesowego](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)— Backup-Windows-Server-to-Azure # Create-a-Recovery-Services-magazyn).
* Upewnij się, że tylko [Najnowsza wersja agenta Microsoft Azure Recovery Services](https://aka.ms/azurebackup_agent) jest zainstalowana na serwerze SC DPM lub serwera usługi MAB i zarejestrowanym w magazynie Recovery Services.
* Pakiet zbiorczy aktualizacji 1 jest instalowany na serwerze SC DPM 2019 lub serwera usługi MAB v3.

  > [!NOTE]
  > W przypadku programu DPM 2019 UR1 i serwera usługi MAB v3 UR1 rozpełnianie w trybie offline jest uwierzytelniane przy użyciu Azure Active Directory.

* Na serwerze DPM lub serwera usługi MAB upewnij się, że jest zainstalowany program Microsoft Edge lub Internet Explorer 11, a język JavaScript jest włączony.
* Utwórz konto usługi Azure Storage w tej samej subskrypcji co magazyn Recovery Services.
* Upewnij się, że masz odpowiednie [uprawnienia](../active-directory/develop/howto-create-service-principal-portal.md) do tworzenia aplikacji Azure Active Directory. Przepływ pracy kopii zapasowej offline tworzy aplikację Azure Active Directory w subskrypcji skojarzonej z kontem usługi Azure Storage. Celem aplikacji jest zapewnienie Azure Backup z bezpiecznym i dostępnym zakresem do usługi Azure import, która jest wymagana dla przepływu pracy kopii zapasowej offline.
* Zarejestruj dostawcę zasobów Microsoft. ImportExport z subskrypcją zawierającą konto usługi Azure Storage. Aby zarejestrować dostawcę zasobów:
    1. W menu głównym wybierz pozycję **subskrypcje**.
    2. Jeśli subskrybujesz wiele subskrypcji, wybierz subskrypcję, która jest używana do tworzenia kopii zapasowych w trybie offline. Jeśli używasz tylko jednej subskrypcji, Twoja subskrypcja zostanie wyświetlona.
    3. W menu subskrypcja wybierz pozycję **dostawcy zasobów** , aby wyświetlić listę dostawców.
    4. Na liście dostawców przewiń w dół do Microsoft. ImportExport. Jeśli stan to NotRegistered, wybierz pozycję **zarejestruj**.

       ![Rejestrowanie dostawcy zasobów](./media/backup-azure-backup-server-import-export/register-import-export.png)

* Tworzona jest lokalizacja tymczasowa, która może być udziałem sieciowym lub dodatkowym dyskiem na komputerze, wewnętrznym lub zewnętrznym, z wystarczającą ilością miejsca na dysku do przechowywania kopii początkowej. Na przykład jeśli chcesz utworzyć kopię zapasową serwera plików 500-GB, upewnij się, że obszar przejściowy jest co najmniej 500 GB. (Mniejsza ilość jest używana z powodu kompresji).
* W przypadku dysków wysyłanych do platformy Azure upewnij się, że są używane wewnętrzne dyski twarde SATA (2,5 cala) lub 2,5-calowe i 3,5 cala. Możesz użyć dysków twardych o pojemności do 10 TB. Zapoznaj się z [dokumentacją usługi Azure Import/Export](../import-export/storage-import-export-requirements.md#supported-hardware) , aby zapoznać się z najnowszym zestawem dysków obsługiwanych przez usługę.
* Dyski SATA muszą być połączone z komputerem (nazywanym *komputerem kopiującym*), z którego są wykonywane kopie kopii zapasowych z lokalizacji tymczasowej do dysków SATA. Upewnij się, że funkcja BitLocker jest włączona na komputerze kopiowania.

## <a name="workflow"></a>Przepływ pracy

Informacje przedstawione w tej sekcji ułatwiają zakończenie przepływu pracy w trybie offline, aby dane mogły zostać dostarczone do centrum danych platformy Azure i przekazane do usługi Azure Storage. Jeśli masz pytania dotyczące usługi import lub dowolnego aspektu procesu, zapoznaj się z dokumentacją dotyczącą [przeglądu usługi Import Service](../import-export/storage-import-export-service.md) .

## <a name="initiate-offline-backup"></a>Inicjowanie kopii zapasowej offline

1. Po utworzeniu nowej grupy ochrony z ochroną w trybie online lub dodaniu ochrony w trybie online do istniejącej grupy ochrony zostanie wyświetlony poniższy ekran. Aby wybrać metodę początkowej replikacji online, wybierz pozycję **transfer przy użyciu własnego dysku** i wybierz pozycję **dalej**.

    ![Ekran importowania](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. Zostanie otwarta strona logowania platformy Azure. Zaloguj się przy użyciu konta użytkownika platformy Azure, które ma uprawnienia roli *właściciela* w ramach subskrypcji platformy Azure.

    ![Strona logowania platformy Azure](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. Podaj dane wejściowe na stronie **Korzystanie z własnego dysku** .

   ![Dane wejściowe na potrzeby używania własnego dysku](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   Opis danych wejściowych jest następujący:

   * **Lokalizacja tymczasowa**: tymczasowa lokalizacja przechowywania, w której zapisano początkową kopię zapasową. Tymczasowa lokalizacja może znajdować się w udziale sieciowym lub na komputerze lokalnym. Jeśli komputer kopii i komputer źródłowy są inne, określ pełną ścieżkę sieciową lokalizacji tymczasowej.
   * **Azure Resource Manager konto magazynu**: Nazwa Menedżer zasobówgo konta magazynu (ogólnego przeznaczenia w wersji 1 lub 2) w dowolnej subskrypcji platformy Azure.
   * **Kontener usługi Azure Storage**: Nazwa docelowego kontenera magazynu obiektów BLOB na koncie usługi Azure Storage, do którego są importowane dane kopii zapasowej.
   * **Identyfikator subskrypcji platformy Azure**: Identyfikator subskrypcji platformy Azure, w której tworzone jest konto usługi Azure Storage.
   * **Nazwa zadania importowania platformy Azure**: unikatowa nazwa, za pomocą której usługa Azure Import i Azure Backup śledzi transfer danych wysyłanych na dyskach na platformę Azure.

    Zapisz **lokalizację przemieszczania** i informacje o **nazwie zadania importowania platformy Azure** podane przez użytkownika. Jest to wymagane do przygotowania dysków.

4. Ukończ przepływ pracy w celu utworzenia lub zaktualizowania ochrony. Aby zainicjować kopię zapasową offline, kliknij prawym przyciskiem myszy **grupę ochrony**, a następnie wybierz opcję **Utwórz punkt odzyskiwania** . Następnie wybierz opcję **ochrony w trybie online** .

   ![Utwórz punkt odzyskiwania](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. Monitoruj zadanie tworzenia repliki online w okienku Monitorowanie. Zadanie powinno zostać wykonane pomyślnie, a ostrzeżenie *oczekuje na zakończenie zadania importowania platformy Azure*.

   ![Kompletny punkt odzyskiwania](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. Po zakończeniu operacji lokalizacja tymczasowa jest gotowa do użycia podczas przygotowywania dysku.

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Przygotowanie dysków SATA i dostarczenie do platformy Azure

Narzędzie *AzureOfflineBackupDiskPrep* przygotowuje dyski SATA, które są wysyłane do najbliższego centrum danych platformy Azure. To narzędzie jest dostępne w katalogu instalacji agenta Azure Backup (w następującej ścieżce): `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. Przejdź do katalogu i Skopiuj katalog **AzureOfflineBackupDiskPrep** do innego komputera, na którym są połączone stacje dysków SATA. Na komputerze z podłączonymi dyskami SATA upewnij się, że:

   * Komputer kopiowania może uzyskać dostęp do lokalizacji tymczasowej dla przepływu pracy w trybie offline przy użyciu tej samej ścieżki sieciowej, która została podana w przepływie pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline".
   * Funkcja BitLocker jest włączona na komputerze kopiowania.
   * Program Azure PowerShell 3.7.0 jest zainstalowany na komputerze kopiującym (niewymagane w przypadku uruchamiania narzędzia AzureOfflineBackupDiskPrep na serwerze DPM lub serwera usługi MAB).
   * Są zainstalowane najnowsze zgodne przeglądarki (Microsoft Edge lub Internet Explorer 11), a język JavaScript jest włączony.
   * Komputer kopiowania może uzyskać dostęp do Azure Portal. W razie potrzeby komputer kopii może być taki sam jak komputer źródłowy.

     > [!IMPORTANT]
     > Jeśli komputer źródłowy jest maszyną wirtualną, należy użyć innego serwera fizycznego lub komputera klienckiego jako komputera kopii.

1. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień na komputerze kopiowania przy użyciu katalogu narzędzi *AzureOfflineBackupDiskPrep* jako bieżący katalog. Uruchom następujące polecenie:

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | Parametr | Opis |
    | --- | --- |
    | s: &lt; *ścieżka lokalizacji tymczasowej*&gt; |To obowiązkowe dane wejściowe są używane w celu zapewnienia ścieżki do lokalizacji tymczasowej wprowadzonej w przepływie pracy w sekcji "Inicjowanie kopii zapasowej w trybie offline". |
    | p: &lt; *ścieżka do PublishSettingsFile*&gt; |To opcjonalne dane wejściowe są używane w celu zapewnienia ścieżki do pliku ustawień publikowania platformy Azure. |

    Po uruchomieniu polecenia Narzędzie żąda wyboru zadania importowania platformy Azure odpowiadającego dyskom, które muszą zostać przygotowane. Jeśli tylko jedno zadanie importu jest skojarzone z podaną lokalizacją przemieszczania, zobaczysz ekran podobny do poniższego.

      ![Konsola przygotowywania dysku](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Wprowadź literę dysku bez kropki końcowej dla zainstalowanego dysku, który ma zostać przygotowany do przeniesienia na platformę Azure.
1. Potwierdź formatowanie dysku po wyświetleniu monitu.
1. Zostanie wyświetlony monit o zalogowanie się do subskrypcji platformy Azure. Wprowadź swoje poświadczenia.

    ![Ekran logowania platformy Azure](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    Następnie narzędzie rozpocznie przygotowanie dysku i skopiowanie danych kopii zapasowej. Jeśli udostępniony dysk nie ma wystarczającej ilości miejsca na dane kopii zapasowej, może być konieczne dołączenie dodatkowych dysków po wyświetleniu monitu przez narzędzie. <br/>

    Po pomyślnym wykonaniu tego narzędzia wiersz polecenia zawiera trzy informacje:
    * Co najmniej jeden z podanych dysków jest przygotowywany do wysyłania do platformy Azure.
    * Otrzymasz potwierdzenie, że zadanie importowania zostało utworzone. Zadanie importowania używa podanej nazwy.
    * Narzędzie wyświetla adres wysyłkowy centrum danych platformy Azure.

     ![Ukończono przygotowywanie dysku platformy Azure](./media/backup-azure-backup-server-import-export/console.png)

1. Po zakończeniu wykonywania polecenia zobaczysz również opcję aktualizowania informacji o wysyłce.

1. Wyślij dyski na adres dostarczony przez narzędzie i Zachowaj numer śledzenia na potrzeby przyszłego odwołania.

   > [!IMPORTANT]
   > Żadne dwa zadania importowania platformy Azure nie mogą mieć tego samego numeru śledzenia. Upewnij się, że dyski przygotowane przez narzędzie w ramach pojedynczego zadania importowania platformy Azure są dostarczane razem w jednym pakiecie i że istnieje jeden unikatowy numer śledzenia dla pakietu. Nie łącz dysków przygotowanych w ramach różnych zadań importowania platformy Azure w jednym pakiecie.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Aktualizowanie szczegółów wysyłki w ramach zadania importowania platformy Azure

Poniższa procedura umożliwia zaktualizowanie informacji o wysyłce zadania importowania platformy Azure. Informacje te zawierają szczegółowe informacje na temat:

* Nazwa przewoźnika, który dostarcza dyski do platformy Azure
* Zwróć szczegóły dotyczące wysyłki dla dysków

   1. Zaloguj się do subskrypcji platformy Azure.
   2. W menu głównym wybierz pozycję **wszystkie usługi** , a następnie w oknie dialogowym wszystkie usługi wpisz import. Po wyświetleniu **zadań Importuj/Eksportuj** wybierz ją.
       ![Wprowadzanie informacji o wysyłce](./media/backup-azure-backup-server-import-export/search-import-job.png)

       Zostanie otwarta lista **zadań Importuj/Eksportuj zadania** i zostanie wyświetlona lista wszystkich zadań importu/eksportu w wybranej subskrypcji.

   3. Jeśli masz wiele subskrypcji, pamiętaj o wybraniu subskrypcji użytej do zaimportowania danych kopii zapasowej. Następnie wybierz nowo utworzone zadanie importu, aby otworzyć jego szczegóły.

       ![Przejrzyj informacje o wysyłce](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. W menu Ustawienia zadania importowania wybierz pozycję **Zarządzaj informacjami o wysyłce** i wprowadź szczegóły dotyczące wysyłki zwrotnej.

       ![Przechowywanie informacji o wysyłce](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. Jeśli numer śledzenia jest używany przez firmę wysyłkową, wybierz transparent na stronie Przegląd zadania importowania platformy Azure i wprowadź następujące informacje:

      > [!IMPORTANT]
      > Upewnij się, że informacje o operatorze i numer śledzenia zostały zaktualizowane w ciągu dwóch tygodni od utworzenia zadania importowania platformy Azure. Niepowodzenie weryfikacji tych informacji w ciągu dwóch tygodni może skutkować usunięciem zadania i nieprzetwarzaniem dysków.

      ![Przegląd zadania](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![Informacje o śledzeniu](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Czas do przetworzenia dysków

Czas przetwarzania zadania importowania platformy Azure różni się. Czas procesu zależy od czynników, takich jak czas wysyłki, typ zadania, typ i rozmiar kopiowanych danych oraz rozmiar dostarczonych dysków. Usługa Azure Import/Export nie ma umowy SLA. Po odebraniu dysków usługa dokłada starań, aby zakończyć kopiowanie danych kopii zapasowej na konto usługi Azure Storage w ciągu 7 do 10 dni. W następnej sekcji opisano, jak można monitorować stan zadania importowania platformy Azure.

### <a name="monitor-azure-import-job-status"></a>Monitoruj stan zadania importowania platformy Azure

Stan zadania importowania można monitorować z Azure Portal, przechodząc do strony **Importuj/Eksportuj zadania** i wybierając zadanie. Aby uzyskać więcej informacji na temat stanu zadań importowania, zobacz artykuł [Magazyn importu usługi Export](../import-export/storage-import-export-service.md) .

### <a name="complete-the-workflow"></a>Ukończ przepływ pracy

Po zakończeniu zadania importowania dane początkowej kopii zapasowej są dostępne na koncie magazynu. W czasie następnej zaplanowanej kopii zapasowej Azure Backup kopiuje zawartość danych z konta magazynu do magazynu Recovery Services.

Podczas następnego zaplanowanego zadania tworzenia repliki w trybie online Data Protection Manager wykonuje przyrostową kopię zapasową za pomocą początkowej kopii zapasowej.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać odpowiedzi na pytania dotyczące przepływu pracy usługi Azure Import/Export, zobacz [Używanie usługi Microsoft Azure Import/Export do transferowania danych do magazynu obiektów BLOB](../import-export/storage-import-export-service.md).
