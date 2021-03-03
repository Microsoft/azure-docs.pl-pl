---
title: Zarządzanie kopiami zapasowymi baz danych SAP HANAymi na maszynach wirtualnych platformy Azure
description: W tym artykule przedstawiono typowe zadania związane z zarządzaniem i monitorowaniem SAP HANA baz danych uruchomionych na maszynach wirtualnych platformy Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 54d3341a83873ad3cc50815f04a0b252bb44438e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703770"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>Zarządzanie kopiami zapasowymi baz danych platformy SAP HANA i ich monitorowanie

W tym artykule opisano typowe zadania związane z zarządzaniem i monitorowaniem SAP HANA baz danych, które są uruchomione na maszynie wirtualnej platformy Azure (VM) i których kopia zapasowa jest wykonywana w magazynie Recovery Services Azure Backup przez usługę [Azure Backup](./backup-overview.md) . Dowiesz się, jak monitorować zadania i alerty, wyzwalać kopie zapasowe na żądanie, edytować zasady, zatrzymywać i wznawiać ochronę bazy danych oraz wyrejestrować maszynę wirtualną z kopii zapasowych.

Jeśli nie skonfigurowano jeszcze kopii zapasowych dla SAP HANA baz danych, zobacz [Tworzenie kopii zapasowych SAP HANA baz danych na maszynach wirtualnych platformy Azure](./backup-azure-sap-hana-database.md).

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorowanie zadań ręcznego tworzenia kopii zapasowej w portalu

Azure Backup przedstawia wszystkie zadania wyzwalane ręcznie w sekcji **zadania tworzenia kopii zapasowej** w Azure Portal.

![Sekcja zadań tworzenia kopii zapasowej](./media/sap-hana-db-manage/backup-jobs.png)

Zadania widoczne w tym portalu obejmują odnajdywanie i rejestrowanie bazy danych oraz operacje tworzenia kopii zapasowych i przywracania. Zaplanowane zadania, w tym kopie zapasowe dzienników, nie są wyświetlane w tej sekcji. Ręcznie wyzwolone kopie zapasowe z SAP HANA natywnych klientów (Panel sterowania Studio/Panel sterowania/DBA) również nie są wyświetlane w tym miejscu.

![Lista zadań tworzenia kopii zapasowej](./media/sap-hana-db-manage/backup-jobs-list.png)

Aby dowiedzieć się więcej o monitorowaniu, przejdź do [obszaru monitorowanie w Azure Portal](./backup-azure-monitoring-built-in-monitor.md) i [monitorowanie przy użyciu Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="view-backup-alerts"></a>Wyświetlanie alertów dotyczących kopii zapasowych

Alerty to prosta metoda monitorowania kopii zapasowych baz danych SAP HANA. Alerty ułatwiają skoncentrowanie się na zdarzeniach, których nie można uzyskać w wielu przypadkach, które generują kopie zapasowe. Azure Backup umożliwia Ustawianie alertów i monitorowanie ich w następujący sposób:

* Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
* Na pulpicie nawigacyjnym magazynu wybierz pozycję **alerty kopii zapasowych**.

  ![Alerty kopii zapasowej na pulpicie nawigacyjnym magazynu](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* Będziesz mieć możliwość wyświetlenia alertów:

  ![Lista alertów dotyczących kopii zapasowych](./media/sap-hana-db-manage/backup-alerts-list.png)

* Wybierz alerty, aby wyświetlić więcej szczegółów:

  ![Szczegóły alertu](./media/sap-hana-db-manage/alert-details.png)

Obecnie Azure Backup umożliwia wysyłanie alertów za pośrednictwem poczty e-mail. Te alerty są następujące:

* Wyzwolone dla wszystkich błędów tworzenia kopii zapasowej.
* Skonsolidowane na poziomie bazy danych według kodu błędu.
* Wysyłane tylko do pierwszego błędu kopii zapasowej bazy danych.

Aby dowiedzieć się więcej o monitorowaniu, przejdź do [obszaru monitorowanie w Azure Portal](./backup-azure-monitoring-built-in-monitor.md) i [monitorowanie przy użyciu Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

## <a name="management-operations"></a>Operacje zarządzania

Azure Backup umożliwia łatwe zarządzanie kopią zapasową bazy danych SAP HANA Database przy użyciu dużej ilości obsługiwanych operacji zarządzania. Te operacje zostały omówione bardziej szczegółowo w poniższych sekcjach.

### <a name="run-an-on-demand-backup"></a>Uruchamianie tworzenia kopii zapasowej na żądanie

Kopie zapasowe są uruchamiane zgodnie z harmonogramem zasad. Kopię zapasową można uruchomić na żądanie w następujący sposób:

1. W menu magazyn wybierz pozycję **elementy kopii zapasowej**.
2. W obszarze **elementy kopii zapasowej** wybierz maszynę wirtualną z uruchomioną SAP HANA bazą danych, a następnie wybierz pozycję **Utwórz kopię zapasową teraz**.
3. W obszarze **kopia zapasowa** wybierz typ kopii zapasowej, którą chcesz wykonać. Następnie wybierz przycisk **OK**. Ta kopia zapasowa zostanie zachowana zgodnie z zasadami skojarzonymi z tym elementem kopii zapasowej.
4. Monitoruj powiadomienia portalu. Postęp zadania można monitorować na pulpicie nawigacyjnym magazynu > **zadania tworzenia kopii zapasowej**  >  **w toku**. W zależności od rozmiaru bazy danych Tworzenie początkowej kopii zapasowej może chwilę potrwać.

Domyślnie przechowywanie kopii zapasowych na żądanie to 45 dni.

### <a name="hana-native-client-integration"></a>Integracja z klientem platformy HANA Native

#### <a name="backup"></a>Backup

Kopie zapasowe na żądanie wyzwalane przez dowolnych klientów natywnych platformy HANA (do **BACKINT**) zostaną wyświetlone na liście kopii na stronie **elementy kopii** zapasowej.

![Ostatnio wykonywane kopie zapasowe](./media/sap-hana-db-manage/last-backups.png)

[Te kopie zapasowe](#monitor-manual-backup-jobs-in-the-portal) można także monitorować na stronie **zadania tworzenia kopii zapasowej** .

Te kopie zapasowe na żądanie również zostaną wyświetlone na liście punktów przywracania do przywrócenia.

![Lista punktów przywracania](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>Przywracanie

Przywrócenie wyzwalane przez klientów platformy HANA Native (przy użyciu **BACKINT**) do przywrócenia na tym samym komputerze może być [monitorowane](#monitor-manual-backup-jobs-in-the-portal) ze strony **zadania tworzenia kopii zapasowej** .

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>Uruchamianie SAP HANA natywnej kopii zapasowej klienta na dysku lokalnym w bazie danych z włączoną funkcją Azure Backup

Jeśli chcesz utworzyć lokalną kopię zapasową (przy użyciu platformy HANA Studio/Panel sterowania) dla bazy danych, której kopia zapasowa jest tworzona przy użyciu Azure Backup, wykonaj następujące czynności:

1. Poczekaj na zakończenie wszystkich pełnych lub dzienników kopii zapasowych bazy danych. Sprawdź stan w SAP HANA Studio/Panel sterowania.
2. dla odpowiedniej bazy danych
    1. Nie usunięto parametrów BACKINT. Aby to zrobić, kliknij dwukrotnie pozycję   >  **Konfiguracja** systemdb  >  **Wybierz pozycję Filtr bazy danych**  >  **(log)**.
        * enable_auto_log_backup: nie
        * log_backup_using_backint: FAŁSZ
        * catalog_backup_using_backint: FAŁSZ
3. Wykonaj pełną kopię zapasową bazy danych na żądanie
4. Następnie Wycofaj kroki. Dla tej samej bazy danych wymienionej powyżej,
    1. ponownie włącz parametry BACKINT
        1. catalog_backup_using_backint: prawda
        1. log_backup_using_backint: prawda
        1. enable_auto_log_backup: tak

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>Zarządzanie wykazem platformy HANA dla bazy danych z włączonymi Azure Backupami i ich oczyszczanie

Jeśli chcesz edytować lub wyczyścić wykaz kopii zapasowych, wykonaj następujące czynności:

1. Poczekaj na zakończenie wszystkich pełnych lub dzienników kopii zapasowych bazy danych. Sprawdź stan w SAP HANA Studio/Panel sterowania.
2. dla odpowiedniej bazy danych
    1. Nie usunięto parametrów BACKINT. Aby to zrobić, kliknij dwukrotnie pozycję   >  **Konfiguracja** systemdb  >  **Wybierz pozycję Filtr bazy danych**  >  **(log)**.
        * enable_auto_log_backup: nie
        * log_backup_using_backint: FAŁSZ
        * catalog_backup_using_backint: FAŁSZ
3. Edytuj katalog i Usuń starsze wpisy
4. Następnie Wycofaj kroki. Dla tej samej bazy danych wymienionej powyżej,
    1. ponownie włącz parametry BACKINT
        1. catalog_backup_using_backint: prawda
        1. log_backup_using_backint: prawda
        1. enable_auto_log_backup: tak

### <a name="change-policy"></a>Zmień zasady

Można zmienić podstawowe zasady dla SAP HANA elementu kopii zapasowej.

* Na pulpicie nawigacyjnym magazynu przejdź do **pozycji elementy kopii zapasowej**:

  ![Wybierz elementy kopii zapasowej](./media/sap-hana-db-manage/backup-items.png)

* Wybierz **SAP HANA na maszynie wirtualnej platformy Azure**

  ![Wybierz SAP HANA na maszynie wirtualnej platformy Azure](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* Wybierz element kopii zapasowej, którego podstawowe zasady chcesz zmienić
* Wybierz istniejące zasady tworzenia kopii zapasowych.

  ![Wybierz istniejące zasady tworzenia kopii zapasowych](./media/sap-hana-db-manage/existing-backup-policy.png)

* Zmień zasady, wybierając z listy. [Utwórz nowe zasady tworzenia kopii zapasowych w](./backup-azure-sap-hana-database.md#create-a-backup-policy) razie konieczności.

  ![Wybieranie zasad z listy rozwijanej](./media/sap-hana-db-manage/choose-backup-policy.png)

* Zapisz zmiany

  ![Zapisz zmiany](./media/sap-hana-db-manage/save-changes.png)

* Modyfikacje zasad będą mieć wpływ na wszystkie powiązane elementy kopii zapasowej i wyzwalają odpowiednie zadania **konfigurowania ochrony** .

>[!NOTE]
> Każda zmiana w okresie przechowywania będzie stosowana z mocą wsteczną do wszystkich starszych punktów odzyskiwania poza nowymi.

### <a name="modify-policy"></a>Modyfikowanie zasad

Zmodyfikuj zasady, aby zmienić typy kopii zapasowych, częstotliwości i zakres przechowywania.

>[!NOTE]
>Wszelkie zmiany w okresie przechowywania zostaną zastosowane do wszystkich starszych punktów odzyskiwania oprócz nowych.

1. Na pulpicie nawigacyjnym magazynu przejdź do pozycji **zarządzaj > zasady tworzenia kopii zapasowych** i wybierz zasady, które chcesz edytować.

   ![Wybierz zasady do edycji](./media/sap-hana-db-manage/manage-backup-policies.png)

1. Wybierz pozycję **Modyfikuj**.

   ![Wybierz pozycję Modyfikuj](./media/sap-hana-db-manage/modify-policy.png)

1. Wybierz częstotliwość tworzenia kopii zapasowych.

   ![Wybierz częstotliwość tworzenia kopii zapasowych](./media/sap-hana-db-manage/choose-frequency.png)

Modyfikacje zasad będą mieć wpływ na wszystkie powiązane elementy kopii zapasowej i wyzwalają odpowiednie zadania **konfigurowania ochrony** .

### <a name="inconsistent-policy"></a>Niespójne zasady

Czasami operacja modyfikowania zasad może prowadzić do **niespójnej** wersji zasad dla niektórych elementów kopii zapasowej. Dzieje się tak, gdy odpowiednie zadanie **konfigurowania ochrony** nie powiedzie się dla elementu kopii zapasowej po wyzwoleniu operacji modyfikowania zasad. Jest on wyświetlany w następujący sposób w widoku elementu kopii zapasowej:

![Niespójne zasady](./media/sap-hana-db-manage/inconsistent-policy.png)

Możesz naprawić wersję zasad dla wszystkich elementów, których dotyczy problem, w jednym kliknięciem:

![Napraw wersję zasad](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>Zatrzymaj ochronę bazy danych SAP HANA

Można zatrzymać ochronę bazy danych SAP HANA na kilka sposobów:

* Zatrzymanie wszystkich przyszłych zadań tworzenia kopii zapasowych i usunięcie wszystkich punktów odzyskiwania.
* Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej i pozostaw punkty odzyskiwania bez zmian.

Jeśli zdecydujesz się na pozostawienie punktów odzyskiwania, pamiętaj o następujących szczegółach:

* Wszystkie punkty odzyskiwania pozostaną nienaruszone, a całe oczyszczanie zostanie zatrzymane po zatrzymaniu ochrony z zachowaniem danych.
* Zostanie naliczona opłata za chronione wystąpienie i zużyty magazyn. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Jeśli usuniesz źródło danych bez zatrzymywania kopii zapasowych, nowe kopie zapasowe zakończą się niepowodzeniem.

Aby zatrzymać ochronę bazy danych:

* Na pulpicie nawigacyjnym magazynu wybierz pozycję **elementy kopii zapasowej**.
* W obszarze **Typ zarządzania kopiami zapasowymi** wybierz pozycję **SAP HANA na maszynie wirtualnej platformy Azure**

  ![Wybierz SAP HANA na maszynie wirtualnej platformy Azure](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* Wybierz bazę danych, dla której chcesz zatrzymać ochronę:

  ![Wybierz bazę danych, aby zatrzymać ochronę](./media/sap-hana-db-manage/select-database.png)

* W menu Baza danych wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.

  ![Wybierz pozycję Zatrzymaj tworzenie kopii zapasowej](./media/sap-hana-db-manage/stop-backup.png)

* W menu **Zatrzymaj kopię zapasową** wybierz, czy chcesz zachować, czy usunąć dane. Jeśli chcesz, podaj przyczynę i komentarz.

  ![Wybierz opcję Zachowaj lub Usuń dane](./media/sap-hana-db-manage/retain-backup-data.png)

* Wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej**.

### <a name="resume-protection-for-an-sap-hana-database"></a>Wznów ochronę bazy danych SAP HANA

Po zatrzymaniu ochrony bazy danych SAP HANA, jeśli wybierzesz opcję **Zachowaj dane kopii zapasowej** , możesz później wznowić ochronę. Jeśli nie zachowasz kopii zapasowej danych, nie możesz wznowić ochrony.

Aby wznowić ochronę bazy danych SAP HANA:

* Otwórz element kopii zapasowej i wybierz pozycję **Wznów wykonywanie kopii zapasowej**.

   ![Wybierz pozycję Wznów wykonywanie kopii zapasowej](./media/sap-hana-db-manage/resume-backup.png)

* W menu **Zasady kopii zapasowych** wybierz zasady, a następnie wybierz pozycję **Zapisz**.

### <a name="upgrading-from-sdc-to-mdc"></a>Uaktualnianie z SDC do MDC

Dowiedz się, jak kontynuować tworzenie kopii zapasowej bazy danych SAP HANA [po uaktualnieniu z SDC do MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid).

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>Uaktualnianie z SDC do MDC bez zmiany identyfikatora SID

Dowiedz się, jak kontynuować tworzenie kopii zapasowej bazy danych SAP HANA, której [Identyfikator SID nie zmienił się po uaktualnieniu z SDC do MDC](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid).

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>Uaktualnianie do nowej wersji w SDC lub MDC

Dowiedz się, jak kontynuować tworzenie kopii zapasowej bazy danych SAP HANA, [której wersja jest uaktualniana](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm).

### <a name="unregister-an-sap-hana-instance"></a>Wyrejestruj wystąpienie SAP HANA

Wyrejestruj wystąpienie SAP HANA po wyłączeniu ochrony, ale przed usunięciem magazynu:

* Na pulpicie nawigacyjnym magazynu w obszarze **Zarządzaj** wybierz pozycję **infrastruktura kopii zapasowych**.

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/sap-hana-db-manage/backup-infrastructure.png)

* Wybierz **Typ zarządzania kopiami zapasowymi** jako **obciążenie na maszynie wirtualnej platformy Azure**

   ![Wybierz typ zarządzania kopiami zapasowymi jako obciążenie na maszynie wirtualnej platformy Azure](./media/sap-hana-db-manage/backup-management-type.png)

* W obszarze **serwery chronione** wybierz wystąpienie do wyrejestrowania. Aby usunąć magazyn, należy wyrejestrować wszystkie serwery i wystąpienia.

* Kliknij prawym przyciskiem myszy chronione wystąpienie i wybierz polecenie **Wyrejestruj**.

   ![Wybierz pozycję Wyrejestruj](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>Ponowne rejestrowanie rozszerzenia na maszynie wirtualnej serwera SAP HANA

Czasami rozszerzenie obciążenia maszyny wirtualnej może mieć wpływ na jedną przyczynę lub inną. W takich przypadkach wszystkie operacje wyzwalane na maszynie wirtualnej rozpoczną się niepowodzeniem. Może być konieczne ponowne zarejestrowanie rozszerzenia na maszynie wirtualnej. Operacja ponownego rejestrowania ponownie zainstaluje rozszerzenie kopii zapasowej obciążenia na maszynie wirtualnej w celu kontynuowania operacji.

Użyj tej opcji z przestrogą: po wyzwoleniu na maszynie wirtualnej z rozszerzeniem w dobrej kondycji ta operacja spowoduje ponowne uruchomienie rozszerzenia. Może to spowodować niepowodzenie wszystkich zadań w toku. Przed wyzwoleniem operacji ponownego rejestrowania Sprawdź, czy nie ma jednego lub kilku [objawów](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [rozwiązywać typowe problemy podczas tworzenia kopii zapasowych baz danych SAP HANA.](./backup-azure-sap-hana-database-troubleshoot.md)
