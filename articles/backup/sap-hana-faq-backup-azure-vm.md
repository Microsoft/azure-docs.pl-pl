---
title: Często zadawane pytania — tworzenie kopii zapasowych baz danych platformy SAP HANA na maszynach wirtualnych platformy Azure
description: W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych SAP HANA baz danych przy użyciu usługi Azure Backup.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: bf662600bafcd18b00c8f8d3b673fc3f9c110aca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95400211"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Często zadawane pytania — tworzenie kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowych SAP HANA baz danych przy użyciu usługi Azure Backup.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Ile pełnych kopii zapasowych jest obsługiwanych dziennie?

Obsługujemy tylko jedną pełną kopię zapasową dziennie. Nie można utworzyć różnicowej kopii zapasowej i pełnej kopii zapasowej w tym samym dniu.

### <a name="do-successful-backup-jobs-create-alerts"></a>Czy zakończone pomyślnie zadania tworzenia kopii zapasowej tworzą alerty?

Nie. Zakończone pomyślnie zadania tworzenia kopii zapasowej nie generują alertów. Alerty są wysyłane tylko w przypadku zadań tworzenia kopii zapasowej, które się nie powiodły. Szczegółowe zachowanie alertów portalu zostało udokumentowane w [tym miejscu](./backup-azure-monitoring-built-in-monitor.md). Jeśli jednak interesuje Cię alerty nawet w przypadku zadań zakończonych powodzeniem, możesz użyć [Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Czy można zobaczyć zaplanowane zadania tworzenia kopii zapasowej w menu zadania tworzenia kopii zapasowej?

Menu zadania tworzenia kopii zapasowej będzie zawierać tylko zadania tworzenia kopii zapasowej na żądanie. W przypadku zaplanowanych zadań Użyj [Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="are-future-databases-automatically-added-for-backup"></a>Czy przyszłe bazy danych są automatycznie dodawane do zadania tworzenia kopii zapasowej?

Nie, to nie jest obecnie obsługiwane.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Czy po usunięciu bazy danych z wystąpienia będą wykonywane kopie zapasowe?

Jeśli baza danych zostanie porzucona z wystąpienia SAP HANA, nadal będą podejmowane kopie zapasowe bazy danych. Oznacza to, że usunięta baza danych zaczyna wyglądać jak w złej kondycji w obszarze **elementy kopii zapasowej** i nadal jest chroniona.
Prawidłowym sposobem zatrzymania ochrony tej bazy danych jest wykonanie **operacji zatrzymania wykonywania kopii zapasowej z usuwaniem danych z** tej bazy danych.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Jeśli zmienię nazwę bazy danych po jej włączeniu, jakie będzie zachowanie?

Baza danych o zmienionej nazwie jest traktowana jako nowa baza danych. W związku z tym, usługa będzie traktować tę sytuację tak, jakby baza danych nie została znaleziona, i spowoduje niepowodzenie wykonywania kopii zapasowych. Baza danych o zmienionej nazwie będzie wyświetlana jako nowa baza danych i musi być skonfigurowana do ochrony.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Jakie są wymagania wstępne dotyczące tworzenia kopii zapasowych baz danych SAP HANA na maszynie wirtualnej platformy Azure?

Zapoznaj się z [wymaganiami wstępnymi](tutorial-backup-sap-hana-db.md#prerequisites) i [co robi skrypt przed rejestracją](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Jakie uprawnienia należy ustawić, aby platforma Azure mogła tworzyć kopie zapasowe baz danych SAP HANA.

Uruchomienie skryptu przed rejestracją ustawia wymagane uprawnienia, aby system Azure mógł tworzyć kopie zapasowe baz danych SAP HANA. Więcej informacji na temat tego, co robi skrypt przed rejestracją, znajduje się w [tym miejscu](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does).

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>Czy kopie zapasowe będą wykonywane po przeprowadzeniu migracji SAP HANA z SDC do MDC?

Zapoznaj się z [tą sekcją](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) przewodnika rozwiązywania problemów.

### <a name="what-should-be-done-while-upgrading-within-the-same-version"></a>Co należy zrobić podczas uaktualniania w tej samej wersji?

Zapoznaj się z [tą sekcją](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm) w przewodniku rozwiązywania problemów.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Czy można utworzyć kopię zapasową platformy Azure HANA względem wirtualnego adresu IP (modułu równoważenia obciążenia), a nie maszyny wirtualnej?

Obecnie nie mamy możliwości skonfigurowania rozwiązania wyłącznie dla wirtualnego adresu IP. Potrzebujemy maszyny wirtualnej, aby wykonać rozwiązanie.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Jak przenieść kopię zapasową na żądanie do lokalnego systemu plików zamiast z magazynu platformy Azure?

1. Zaczekaj na ukończenie aktualnie uruchomionej kopii zapasowej w wymaganej bazie danych (Sprawdź, czy w programie Studio została ukończona).
1. Wyłącz kopie zapasowe dzienników i ustaw kopię zapasową wykazu w **systemie plików** dla żądanej bazy danych, wykonując następujące czynności:
1. Kliknij dwukrotnie pozycję   ->  **Konfiguracja** SYSTEMDB  ->  **Wybierz pozycję Filtr bazy danych**  ->  **(Dziennik)**
    1. Ustaw enable_auto_log_backup na wartość **nie**.
    1. Ustaw catalog_backup_using_backint na **wartość false**.
1. Wykonaj kopię zapasową na żądanie (pełna/różnicowa/przyrostowo) w odpowiedniej bazie danych i poczekaj na zakończenie tworzenia kopii zapasowej i wykazu.
1. Jeśli chcesz również przenieść kopie zapasowe dziennika do systemu plików, ustaw enable_auto_log_backup na **tak**.
1. Przywróć poprzednie ustawienia, aby umożliwić tworzenie kopii zapasowych w magazynie platformy Azure:
    1. Ustaw wartość enable_auto_log_backup na **tak**.
    1. Ustaw catalog_backup_using_backint na **wartość true**.

>[!NOTE]
>Przeniesienie kopii zapasowych do lokalnego systemu plików i ponowne przełączenie do magazynu platformy Azure może spowodować przerwanie przez łańcuch dziennika kopii zapasowych dziennika w magazynie. Spowoduje to wyzwolenie pełnej kopii zapasowej, która po pomyślnym zakończeniu rozpocznie tworzenie kopii zapasowych dzienników.

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Jak mogę użyć SAP HANA kopii zapasowej za pomocą konfiguracji replikacji platformy HANA?

Obecnie Azure Backup nie ma możliwości zrozumienia konfiguracji HSR. Oznacza to, że podstawowy i pomocniczy węzeł HSR będą traktowane jako dwie pojedyncze, niepowiązane maszyny wirtualne. Najpierw musisz skonfigurować kopię zapasową w węźle podstawowym. Po przełączeniu w tryb failover kopia zapasowa musi być skonfigurowana w węźle pomocniczym (który będzie teraz węzłem podstawowym). Nie istnieje automatyczne przełączanie kopii zapasowej do drugiego węzła.

Aby utworzyć kopię zapasową danych z aktywnego (podstawowego) węzła w dowolnym momencie, możesz **przełączyć ochronę** do węzła pomocniczego, który teraz stał się serwerem podstawowym po zakończeniu pracy awaryjnej.

Aby przeprowadzić tę **ochronę przed przełączeniem**, wykonaj następujące kroki:

- [Zatrzymaj ochronę](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (z zachowaniem danych) na serwerze podstawowym
- Uruchamianie [skryptu przed rejestracją](https://aka.ms/scriptforpermsonhana) w węźle pomocniczym
- [Odnajdywanie baz danych](tutorial-backup-sap-hana-db.md#discover-the-databases) w węźle pomocniczym i Konfigurowanie na nich [kopii zapasowych](tutorial-backup-sap-hana-db.md#configure-backup)

Kroki te należy wykonać ręcznie po każdym przejściu w tryb failover. Te kroki można wykonać za pomocą wiersza polecenia/REST protokołu HTTP oprócz Azure Portal. Aby zautomatyzować te kroki, możesz użyć elementu Runbook platformy Azure.

Oto szczegółowy przykład sposobu, w jaki należy wykonać **ochronę przełącznika** :

W tym przykładzie masz dwa węzły — węzeł 1 (podstawowy) i węzeł 2 (pomocniczy) w konfiguracji HSR.  Kopie zapasowe są konfigurowane w węźle 1. Jak wspomniano powyżej, nie próbuj jeszcze konfigurować kopii zapasowych w węźle 2.

Gdy następuje pierwszy tryb failover, węzeł 2 stanie się podstawowym. Następnie

1. Zatrzymaj ochronę węzła 1 (poprzedni podstawowy) przy użyciu opcji Zachowaj dane.
1. Uruchom skrypt poprzedzający rejestrację w węźle 2 (który jest teraz podstawowym).
1. Odnajdywanie baz danych w węźle 2, przypisywanie zasad tworzenia kopii zapasowych i konfigurowanie kopii zapasowych.

Następnie pierwsza pełna kopia zapasowa jest wyzwalana w węźle 2 i po tym zakończeniu kopie zapasowe dziennika zaczynają się.

Po następnym przełączeniu w tryb failover węzeł 1 zmieni się na podstawowy, a węzeł 2 stanie się pomocniczy. Teraz Powtarzaj proces:

1. Zatrzymaj ochronę węzła 2 przy użyciu opcji Zachowaj dane.
1. Uruchom skrypt poprzedzający rejestrację w węźle 1 (który ponownie stał się podstawowym)
1. Następnie [Wznów tworzenie kopii zapasowej](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) w węźle 1 z wymaganymi zasadami (ponieważ kopie zapasowe zostały zatrzymane wcześniej w węźle 1).

Następnie zostanie wyzwolona pełna kopia zapasowa w węźle 1 i po zakończeniu wykonywania kopii zapasowych dziennika.

## <a name="restore"></a>Przywracanie

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Dlaczego nie widzę systemu HANA, do którego ma zostać przywrócona baza danych?

Sprawdź, czy zostały spełnione wszystkie wymagania wstępne dotyczące przywracania SAP HANA wystąpienia docelowego. Aby uzyskać więcej informacji, zobacz [wymagania wstępne — przywracanie SAP HANA baz danych na maszynie wirtualnej platformy Azure](./sap-hana-db-restore.md#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Dlaczego operacja zastępowania bazy danych nie powiedzie się w przypadku mojej usługi?

Upewnij się, że opcja **Wymuszaj zastępowanie** jest zaznaczona podczas przywracania.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Dlaczego widzę błąd "systemy źródłowe i docelowe na potrzeby przywracania są niezgodne"?

Zapoznaj się z SAP HANA Uwaga [1642148](https://launchpad.support.sap.com/#/notes/1642148) , aby zobaczyć, jakie typy przywracania są obecnie obsługiwane.

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>Czy można użyć kopii zapasowej bazy danych działającej w usłudze SLES, aby przywrócić systemowi RHEL HANA lub odwrotnie?

Tak, można użyć kopii zapasowych przesyłania strumieniowego wyzwalanych w bazie danych HANA działającej w systemie SLES, aby przywrócić ją do systemu RHEL HANA i na odwrót. Oznacza to, że przywracanie między systemami operacyjnymi jest możliwe przy użyciu kopii zapasowych przesyłania strumieniowego. Należy jednak upewnić się, że system HANA, do którego ma zostać przywrócona, i system HANA używany do przywracania, są zgodne do przywracania w zależności od SAP. Zapoznaj się z SAP HANA Uwaga [1642148](https://launchpad.support.sap.com/#/notes/1642148) , aby zobaczyć, które typy przywracania są zgodne.

## <a name="policy"></a>Zasady

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>Różne opcje dostępne podczas tworzenia nowych zasad dla SAP HANA kopii zapasowej

Przed utworzeniem zasad należy wyczyścić wymagania dotyczące punktu odzyskiwania i RTO oraz odpowiednie konsekwencje dotyczące kosztów.

RPO (cel punktu odzyskiwania) wskazuje, ile utraconych danych jest akceptowalnych dla użytkownika/klienta. Jest to określane przez częstotliwość tworzenia kopii zapasowych dziennika. Częstsze kopie zapasowe dzienników wskazują niższy cel punktu odzyskiwania, a minimalna wartość obsługiwana przez usługę Azure Backup to 15 minut. Częstotliwość tworzenia kopii zapasowych dziennika może wynosić 15 minut lub więcej.

RTO (cel czasu odzyskiwania) wskazuje, jak szybko dane powinny zostać przywrócone do ostatniego dostępnego punktu w czasie po przypadku utraty danych. Jest to zależne od strategii odzyskiwania wykorzystywanej przez platformę HANA, która zwykle zależy od liczby plików wymaganych do przywrócenia. Ma to również wpływ na koszty, a Poniższa tabela powinna pomóc w zrozumieniu wszystkich scenariuszy i ich skutków.

|Zasady tworzenia kopii zapasowej  |Cel czasu odzyskiwania  |Koszt  |
|---------|---------|---------|
|Dzienny pełny dziennik i dzienniki     |   Najszybciej, ponieważ potrzebujemy tylko jednej pełnej kopii i wymaganych dzienników do przywracania do punktu w czasie      |    Opcja Costliest, ponieważ pełna kopia jest wykonywana codziennie, więc więcej i więcej danych jest gromadzonych w zapleczu do czasu przechowywania   |
|Cotygodniowe pełne + dzienne różnice i dzienniki     |   Wolniejsza niż powyższa opcja, ale szybsza niż Następna opcja, ponieważ potrzebujemy jednej pełnej kopii i jednego różnicowego kopiowania + dzienników do przywracania do punktu w czasie      |    Mniej kosztowna opcja, ponieważ dzienna różnica jest zwykle mniejsza niż pełna, a pełna kopia jest wykonywana tylko raz w tygodniu.      |
|Cotygodniowe pełne + dzienne przyrostowe dzienniki     |  Najwolniejsze, ponieważ potrzebujemy jednej kopii przyrostowej + "n" i dzienników na potrzeby odzyskiwania do punktu w czasie       |     Najtańsza opcja, ponieważ dzienne przyrosty będzie mniejsze niż różnicowa, a pełna kopia jest wykonywana tylko raz w tygodniu    |

> [!NOTE]
> Powyższe opcje są najczęściej używane, ale nie są jedynymi opcjami. Można na przykład utworzyć cotygodniowe pełne kopie zapasowe i różnice dwa razy w tygodniu + tydzień +.

W związku z tym można wybrać wariant zasad w oparciu o cele punktu odzyskiwania i RTO oraz zagadnienia dotyczące kosztów.

### <a name="impact-of-modifying-a-policy"></a>Wpływ modyfikowania zasad

Podczas określania wpływu przełączania zasad elementu kopii zapasowej z zasad 1 (P1) do zasad 2 (P2) lub zasad edycji 1 (P1) należy pamiętać o kilku zasadach.

- Wszystkie zmiany są również stosowane z mocą wsteczną. Najnowsze zasady tworzenia kopii zapasowych są stosowane również w pozostałej części punktów odzyskiwania. Załóżmy na przykład, że dzienne pełne przechowywanie to 30 dni, a 10 punktów odzyskiwania zostały wykonane zgodnie z aktualnie aktywnymi zasadami. Jeśli dzienne pełne przechowywanie zostanie zmienione na 10 dni, wówczas czas wygaśnięcia tego punktu zostanie również ponownie obliczony jako czas rozpoczęcia + 10 dni i usunięte, jeśli wygasły.
- Zakres zmian obejmuje również dzień tworzenia kopii zapasowych, typ kopii zapasowej wraz z przechowywaniem. Na przykład: Jeśli zasady zostaną zmienione z codziennego zapełnienia na tydzień w niedziele, wszystkie wcześniejsze pełne, które nie są w niedziele, będą oznaczone do usunięcia.
- Element nadrzędny nie zostanie usunięty, dopóki element podrzędny nie zostanie aktywny/nie wygasł. Każdy typ kopii zapasowej ma czas wygaśnięcia zgodnie z aktualnie aktywnymi zasadami. Ale pełny typ kopii zapasowej jest traktowany jako element nadrzędny dla kolejnych "różnic", "przyrostów" i "dzienników". Elementy "Differential" i "log" nie są nadrzędne dla innych osób. Wartość "Increment" może być elementem nadrzędnym dla kolejnych "przyrostowych". Nawet wtedy, gdy element "Parent" jest oznaczony do usunięcia, nie jest usuwany, jeśli podrzędne "różniczke" lub "Logs" nie wygasły. Na przykład jeśli zasady są zmieniane z codziennego zapełnienia na tydzień w niedziele, wszystkie wcześniejsze pełne, które nie są w niedziele, będą oznaczone do usunięcia. Ale nie są usuwane w rzeczywistości, dopóki dzienniki, które zostały wykonane codziennie, nie wygasły. Innymi słowy, są one zachowywane zgodnie z najnowszym czasem trwania dziennika. Po wygaśnięciu dzienników zarówno dzienniki, jak i pełne zostaną usunięte.

Za pomocą tych zasad można przeczytać poniższą tabelę, aby poznać konsekwencje zmiany zasad.

|Stare zasady/nowe zasady  |Dzienne pełne i dzienniki  | Zapełnienia tygodniowo + dzienne różnice i dzienniki  |Tygodniowe zapełnienia + dzienne przyrosty + dzienniki  |
|---------|---------|---------|---------|
|Dzienne pełne i dzienniki     |   -      |    Poprzednie pełne zapełnienia, które nie są w tym samym dniu tygodnia, są oznaczane do usunięcia, ale utrzymywane do momentu okresu przechowywania dziennika     |    Poprzednie pełne zapełnienia, które nie są w tym samym dniu tygodnia, są oznaczane do usunięcia, ale utrzymywane do momentu okresu przechowywania dziennika     |
|Zapełnienia tygodniowo + dzienne różnice i dzienniki     |   Poprzednie cotygodniowe pełne przechowywanie jest ponownie obliczane zgodnie z najnowszymi zasadami. Poprzednie różnice są natychmiast usuwane      |    -     |    Poprzednie różnice są natychmiast usuwane     |
|Tygodniowe zapełnienia + dzienne przyrosty + dzienniki     |     Poprzednie cotygodniowe pełne przechowywanie jest ponownie obliczane zgodnie z najnowszymi zasadami. Poprzednie przyrosty są natychmiast usuwane    |     Poprzednie przyrosty są natychmiast usuwane    |    -     |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [tworzyć kopie zapasowe baz danych SAP HANA](./backup-azure-sap-hana-database.md) działających na maszynach wirtualnych platformy Azure.
