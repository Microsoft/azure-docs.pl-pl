---
title: Często zadawane pytania — tworzenie kopii zapasowych baz danych SQL Server na maszynach wirtualnych Azure
description: Znajdź odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych baz danych SQL Server na maszynach wirtualnych platformy Azure przy użyciu Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 56dcf29a5c703a72fd137a5f1bf129f568a03b4b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735813"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Często zadawane pytania dotyczące SQL Server baz danych uruchomionych w ramach kopii zapasowej maszyny wirtualnej platformy Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych SQL Server baz danych, które działają na maszynach wirtualnych platformy Azure i korzystają z usługi [Azure Backup](backup-overview.md) .

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Czy można użyć Azure Backup dla maszyny wirtualnej IaaS, jak również SQL Server na tym samym komputerze?

Tak. na tej samej maszynie wirtualnej można tworzyć kopie zapasowe maszyn wirtualnych i kopie zapasowe SQL. W takim przypadku wewnętrznie wyzwalają kopię zapasową tylko do kopiowania na maszynie wirtualnej, aby nie obciąć dzienników.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>Czy rozwiązanie ponawia próbę lub Autoodzyskiwanie kopii zapasowych?

W pewnych okolicznościach usługa Azure Backup wyzwala odtworzenie kopii zapasowych. Funkcja Autokorekty może wystąpić w przypadku każdego z sześciu warunków wymienionych poniżej:

- Jeśli dziennik lub różnicowa kopia zapasowa nie powiedzie się z powodu błędu walidacji LSN, w zamian zostanie przekonwertowany następny dziennik lub różnicowa kopia zapasowa.
- Jeśli nie wykonano pełnej kopii zapasowej przed dziennikiem lub różnicową kopią zapasową, ten dziennik lub różnicowa kopia zapasowa nie zostanie przekonwertowana na pełną kopię zapasową.
- Jeśli Najnowsza pełna kopia zapasowa punktu w czasie jest starsza niż 15 dni, następny dziennik lub różnicowa kopia zapasowa jest konwertowana na pełną kopię zapasową.
- Wszystkie zadania tworzenia kopii zapasowej, które zostały anulowane z powodu uaktualnienia rozszerzenia, zostaną wyzwolone po zakończeniu uaktualniania, a rozszerzenie zostanie uruchomione.
- Jeśli zdecydujesz się zastąpić bazę danych podczas przywracania, kolejny dziennik/różnicowe kopie zapasowe nie powiedzie się i zostanie wyzwolona pełna kopia zapasowa.
- W przypadkach, gdy do resetowania łańcuchów dzienników jest wymagana pełna kopia zapasowa z powodu zmiany modelu odzyskiwania bazy danych, pełna wartość zostanie wyzwolona automatycznie w następnym harmonogramie.

Funkcja Autokorekty jest domyślnie włączona dla wszystkich użytkowników. Jeśli jednak wybierzesz opcję rezygnacji z niej, wykonaj następujące czynności:

- Na wystąpieniu SQL Server w folderze *C:\Program Files\Azure Backup\bin obciążenie* Utwórz lub Edytuj **ExtensionSettingsOverrides.jsna** pliku.
- W **ExtensionSettingsOverrides.jsna**, ustaw *{"EnableAutoHealer": false}*.
- Zapisz zmiany i zamknij plik.
- Na wystąpieniu SQL Server Otwórz **zadanie Zarządzaj** , a następnie uruchom ponownie usługę **AzureWLBackupCoordinatorSvc** .

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Czy mogę określić liczbę współbieżnych kopii zapasowych uruchamianych na serwerze SQL?

Tak. Można ograniczyć szybkość uruchamiania zasad tworzenia kopii zapasowych, aby zminimalizować wpływ na wystąpienie SQL Server. Aby zmienić ustawienie:

1. W wystąpieniu SQL Server w folderze *C:\Program Files\Azure obciążenie Backup\bin* Utwórz *ExtensionSettingsOverrides.jsna* pliku.
2. W *ExtensionSettingsOverrides.jsna* pliku Zmień ustawienie **DefaultBackupTasksThreshold** na niższą wartość (na przykład 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
Wartość domyślna DefaultBackupTasksThreshold wynosi **20**.

3. Zapisz zmiany i zamknij plik.
4. W wystąpieniu programu SQL Server otwórz **Menedżera zadań**. Uruchom ponownie usługę **AzureWLBackupCoordinatorSvc**.<br/> <br/>
 Chociaż ta metoda pomaga, jeśli aplikacja do tworzenia kopii zapasowych zużywa znaczną ilość zasobów, SQL Server [Gubernatorów zasobów](/sql/relational-databases/resource-governor/resource-governor) jest bardziej ogólnym sposobem określania limitów ilości CPU, fizycznego wejścia/wyjścia i pamięci, które mogą być używane przez przychodzące żądania aplikacji.

> [!NOTE]
> W środowisku użytkownika można nadal kontynuować pracę i zaplanować dowolną liczbę kopii zapasowych w danym momencie. Jednak będą one przetwarzane w przedziale powiedzieć, 5, zgodnie z powyższym przykładem.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Czy można uruchamiać pełną kopię zapasową z repliki pomocniczej?

Zgodnie z ograniczeniami SQL można uruchomić kopię pełnej kopii zapasowej na replice pomocniczej. Jednak pełna kopia zapasowa jest niedozwolona.

## <a name="can-i-protect-availability-groups-on-premises"></a>Czy można chronić grupy dostępności lokalnie?

Nie. Azure Backup chroni SQL Server bazy danych działające na platformie Azure. Jeśli grupa dostępności (AG) jest rozłożona między maszynami opartymi na platformie Azure i lokalnymi, można chronić ją tylko wtedy, gdy replika podstawowa jest uruchomiona na platformie Azure. Ponadto Azure Backup chroni tylko węzły działające w tym samym regionie świadczenia usługi Azure co magazyn Recovery Services.

## <a name="can-i-protect-availability-groups-across-regions"></a>Czy mogę chronić grupy dostępności w różnych regionach?

Magazyn Azure Backup Recovery Services może wykrywać i chronić wszystkie węzły znajdujące się w tym samym regionie co magazyn. Jeśli usługa zawsze włączona Grupa dostępności SQL Server obejmuje wiele regionów świadczenia usługi Azure, skonfiguruj kopię zapasową z regionu, który ma węzeł podstawowy. Azure Backup może wykrywać i chronić wszystkie bazy danych w grupie dostępności zgodnie z preferencjami kopii zapasowych. Jeśli preferencja kopii zapasowej nie jest spełniona, wykonywanie kopii zapasowych kończy się niepowodzeniem i zostanie wyświetlony alert o błędzie.

## <a name="do-successful-backup-jobs-create-alerts"></a>Czy zakończone pomyślnie zadania tworzenia kopii zapasowej tworzą alerty?

Nie. Zakończone pomyślnie zadania tworzenia kopii zapasowej nie generują alertów. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powiodły. Szczegółowe zachowanie alertów portalu zostało udokumentowane w [tym miejscu](backup-azure-monitoring-built-in-monitor.md). Jednak jeśli interesuje Cię posiadanie alertów nawet w przypadku zadań zakończonych powodzeniem, możesz użyć [monitorowania za pomocą Azure monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Czy można zobaczyć zaplanowane zadania tworzenia kopii zapasowej w menu zadania tworzenia kopii zapasowej?

W menu **zadania tworzenia kopii zapasowej** są wyświetlane wszystkie operacje zaplanowane i na żądanie, z wyjątkiem zaplanowanych kopii zapasowych dziennika, ponieważ mogą one być bardzo częste. W przypadku zaplanowanych zadań dzienników Użyj [funkcji monitorowania za pomocą Azure monitor](backup-azure-monitoring-use-azuremonitor.md).

## <a name="are-future-databases-automatically-added-for-backup"></a>Czy przyszłe bazy danych są automatycznie dodawane do zadania tworzenia kopii zapasowej?

Tak, możesz uzyskać tę możliwość dzięki funkcji [ochrony autoprotection](backup-sql-server-database-azure-vms.md#enable-auto-protection).  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Co się stanie z kopiami zapasowymi, jeśli usunę bazę danych z wystąpienia z ochroną automatyczną?

Jeśli baza danych zostanie porzucona z wystąpienia z ochroną chronioną, nadal będą podejmowane kopie zapasowe bazy danych. Oznacza to, że usunięta baza danych zaczyna wyglądać jak w złej kondycji w obszarze **elementy kopii zapasowej** i nadal jest chroniona.

Prawidłowym sposobem zatrzymania ochrony tej bazy danych jest **zatrzymanie wykonywania kopii zapasowej** z **usuwaniem danych** z tej bazy danych.  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Czy w przypadku zatrzymania operacji tworzenia kopii zapasowej bazy danych z ochroną chronioną jej zachowaniem?

W przypadku **zatrzymania wykonywania kopii zapasowej z zachowaniem danych** nie będą wykonywane żadne przyszłe kopie zapasowe, a istniejące punkty odzyskiwania pozostaną nienaruszone. Baza danych nadal będzie traktowana jako chroniona i będzie wyświetlana w obszarze **elementy kopii zapasowej**.

Jeśli **zatrzymasz tworzenie kopii zapasowej z usuwaniem danych**, nie będą wykonywane żadne przyszłe kopie zapasowe, a istniejące punkty odzyskiwania również zostaną usunięte. Baza danych zostanie uznana za niechronioną i będzie wyświetlana w ramach wystąpienia w obszarze Konfiguruj kopię zapasową. Jednak w przeciwieństwie do innych zabezpieczonych baz danych, które można wybrać ręcznie lub które mogą być chronione przez autoochronę, ta baza danych jest niedostępna i nie można jej wybrać. Jedynym sposobem, aby ponownie chronić tę bazę danych, jest wyłączenie funkcji autoochrony dla tego wystąpienia. Teraz możesz wybrać tę bazę danych i skonfigurować na niej ochronę lub ponownie włączyć automatyczną ochronę na tym wystąpieniu.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Jeśli zmienię nazwę bazy danych po jej zabezpieczeniu, jakie będzie zachowanie?

Baza danych o zmienionej nazwie jest traktowana jako nowa baza danych. W związku z tym usługa będzie traktować tę sytuację tak, jakby baza danych nie została znaleziona, a kopie zapasowe kończą się niepowodzeniem.

Możesz wybrać bazę danych, która została zmieniona na nazwę i skonfigurować na niej ochronę. Jeśli w wystąpieniu włączono automatyczną ochronę, baza danych o zmienionej nazwie zostanie automatycznie wykryta i będzie chroniona.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Dlaczego nie mogę zobaczyć dodanej bazy danych dla wystąpienia z ochroną chronioną?

Baza danych, którą można [dodać do wystąpienia z ochroną](backup-sql-server-database-azure-vms.md#enable-auto-protection) chronioną, może nie być natychmiast widoczna w obszarze chronione elementy. Dzieje się tak, ponieważ odnajdywanie jest zazwyczaj uruchamiane co 8 godzin. Można jednak odnajdywać i chronić nowe bazy danych natychmiast po ręcznym uruchomieniu odnajdywania, wybierając pozycję **Rediscovery baz danych**, jak pokazano na poniższej ilustracji:

  ![Ręczne odnajdywanie nowo dodanej bazy danych](./media/backup-azure-sql-database/view-newly-added-database.png)
  
## <a name="can-i-protect-databases-that-have-tde-transparent-data-encryption-turned-on-and-will-the-database-stay-encrypted-through-the-entire-backup-process"></a>Czy można chronić bazy danych z włączonym TDE (Transparent Data Encryption) i czy baza danych pozostanie szyfrowana przez cały proces tworzenia kopii zapasowej?

Tak, Azure Backup obsługuje tworzenie kopii zapasowych SQL Server baz danych lub serwera z włączonym TDE. Usługa Backup obsługuje [TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) z kluczami zarządzanymi przez platformę Azure lub z kluczami zarządzanymi przez klienta (BYOK).  W ramach procesu tworzenia kopii zapasowej nie jest wykonywane żadne szyfrowanie SQL, dlatego po utworzeniu kopii zapasowej baza danych pozostanie zaszyfrowana.

## <a name="does-azure-backup-perform-a-checksum-operation-on-the-data-stream"></a>Czy Azure Backup wykonać operację sumy kontrolnej strumienia danych?

Wykonujemy operację sumy kontrolnej strumienia danych. Nie należy jednak mylić z [sumą kontrolną SQL](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).
Kopia zapasowa Azure obciążenia oblicza sumę kontrolną strumienia danych i zapisuje ją jawnie podczas operacji tworzenia kopii zapasowej. Ten strumień sum kontrolnych jest następnie traktowany jako odwołanie i jest weryfikowany krzyżowo z sumą kontrolną strumienia danych podczas operacji przywracania, aby upewnić się, że dane są spójne.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć kopię zapasową bazy danych SQL Server](backup-azure-sql-database.md) działającej na maszynie wirtualnej platformy Azure.