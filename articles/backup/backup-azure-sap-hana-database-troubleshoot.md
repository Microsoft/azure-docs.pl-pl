---
title: Rozwiązywanie problemów SAP HANA kopii zapasowych baz danych
description: Opisuje sposób rozwiązywania typowych błędów, które mogą wystąpić podczas Azure Backup kopii zapasowej SAP HANA baz danych.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: cdf4c26aa32d65ec63ec84d85e454adaaf2ece8d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517236"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowych SAP HANA na platformie Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z kopiami SAP HANA baz danych na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji na temat scenariuszy SAP HANA kopii zapasowych, które obecnie obsługujemy, zobacz [Obsługa scenariuszy](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Wymagania wstępne i uprawnienia

Zapoznaj się z [sekcjami Wymagań wstępnych](tutorial-backup-sap-hana-db.md#prerequisites) i Co skrypt przed rejestracją [robi](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) przed skonfigurowaniem kopii zapasowych.

## <a name="common-user-errors"></a>Typowe błędy użytkowników

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Komunikat o błędzie**      | <span style="font-weight:normal">Azure Backup nie ma wymaganych uprawnień roli do wykonywania kopii zapasowej</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Rola może zostać zastąpiona.                          |
| **Zalecana akcja** | Aby rozwiązać ten problem, uruchom skrypt w **okienku Odnajdywanie bazy** danych lub pobierz go [tutaj.](https://aka.ms/scriptforpermsonhana) Możesz również dodać rolę "SAP_INTERNAL_HANA_SUPPORT" do użytkownika kopii zapasowej obciążenia (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Komunikat o błędzie      | <span style="font-weight:normal">Nie można nawiązać połączenia z systemem HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Wystąpienie SAP HANA może nie być.<br/>Nie ustawiono wymaganych uprawnień Azure Backup do interakcji z bazą danych HANA. |
| **Zalecana akcja** | Sprawdź, czy SAP HANA bazy danych. Jeśli baza danych jest uruchomiona, sprawdź, czy ustawiono wszystkie wymagane uprawnienia. Jeśli brakuje jakichkolwiek uprawnień, uruchom [skrypt preregistracji,](https://aka.ms/scriptforpermsonhana) aby dodać brakujące uprawnienia. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Komunikat o błędzie      | <span style="font-weight:normal">Określone SAP HANA jest nieprawidłowe lub nie można go znaleźć</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Nie SAP HANA kopii zapasowych wielu wystąpień na jednej maszynie wirtualnej platformy Azure. |
| **Zalecana akcja** | Uruchom skrypt [preregistracji w](https://aka.ms/scriptforpermsonhana) SAP HANA, którego kopię zapasową chcesz wrócić. Jeśli problem będzie nadal występował, skontaktuj się z pomocą techniczną firmy Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Komunikat o błędzie      | <span style="font-weight:normal">Określona SAP HANA nie jest obsługiwana</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Azure Backup dla SAP HANA nie obsługuje przyrostowych kopii zapasowych i akcji wykonywanych na klientach natywnych SAP HANA (Studio/ panel sterowania/panel administratora bazy danych) |
| **Zalecana akcja** | Więcej informacji można znaleźć [tutaj.](./sap-hana-backup-support-matrix.md#scenario-support) |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Komunikat o błędzie      | <span style="font-weight:normal">Łańcuch dzienników kopii zapasowych został przerwany</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Miejsce docelowe kopii zapasowej dziennika może zostać zaktualizowane z backint do systemu plików lub plik wykonywalny backint mógł zostać zmieniony |
| **Zalecana akcja** | Wyzwalanie pełnej kopii zapasowej w celu rozwiązania problemu                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Komunikat o błędzie      | <span style="font-weight:normal">Wykryto uaktualnienie SDC do MDC</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Wystąpienie SAP HANA zostało uaktualnione z centrum SDC do usługi MDC. Tworzenie kopii zapasowych po aktualizacji nie powiedzie się. |
| **Zalecana akcja** | Wykonaj kroki wymienione w uaktualnieniu [centrum dystrybucji danych do usługi MDC,](#sdc-to-mdc-upgrade-with-a-change-in-sid) aby rozwiązać ten problem |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Komunikat o błędzie      | <span style="font-weight:normal">Wykryto nieprawidłową konfigurację usługi Backint</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Parametry zapasowe są niepoprawnie określone dla Azure Backup |
| **Zalecana akcja** | Sprawdź, czy ustawiono następujące parametry (backint):<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>Jeśli parametry oparte na funkcji Backint są obecne w hoście, usuń je. Jeśli parametry nie są obecne na poziomie hosta, ale zostały ręcznie zmodyfikowane na poziomie bazy danych, przywróć je do odpowiednich wartości zgodnie z wcześniejszym opisem. Możesz też uruchomić polecenie [zatrzymać ochronę i zachować](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) dane kopii zapasowej z Azure Portal, a następnie wybrać pozycję **Wznów tworzenie kopii zapasowej.** |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Komunikat o błędzie  |Źródłowe i docelowe systemy przywracania są niezgodne  |
|---------|---------|
|Możliwe przyczyny   | Systemy źródłowe i docelowe wybrane do przywrócenia są niezgodne        |
|Zalecana akcja   |   Upewnij się, że twój scenariusz przywracania nie znajduje się na poniższej liście możliwych niezgodnych przywracania: <br><br>   **Przypadek 1:** Podczas przywracania nie można zmienić nazwy bazy danych SYSTEMDB.  <br><br> **Przypadek 2.** Źródło — centrum dystrybucji danych i obiekt docelowy — MDC: źródłowej bazy danych nie można przywrócić jako bazy danych SYSTEMDB lub bazy danych dzierżawy w docelowej bazie danych. <br><br> **Przypadek 3.** Źródło — MDC i cel — SDC: źródłowej bazy danych (SYSTEMDB lub dzierżawy DB) nie można przywrócić do obiektu docelowego. <br><br>  Aby uzyskać więcej informacji, zobacz uwaga **1642148** na [launchpadzie obsługi oprogramowania SAP.](https://launchpad.support.sap.com) |

## <a name="restore-checks"></a>Sprawdzanie przywracania

### <a name="single-container-database-sdc-restore"></a>Przywracanie bazy danych pojedynczego kontenera (SDC)

Zadbaj o dane wejściowe podczas przywracania bazy danych pojedynczego kontenera (SDC) dla platformy HANA do innej maszyny SDC. Nazwa bazy danych powinna być nadana małymi literami i z dołączonym w nawiasach "sdc". Wystąpienie platformy HANA będzie wyświetlane w stolicach.

Załóżmy, że wróci do kopii zapasowej wystąpienia SDC HANA "H21". Na stronie elementów kopii zapasowej zostanie pokazana nazwa elementu kopii zapasowej **"h21(sdc)".** Jeśli spróbujemy przywrócić tę bazę danych do innego docelowego centrum dystrybucji danych, np. H11, należy wprowadzić następujące dane wejściowe.

![Przywrócona nazwa bazy danych centrum dystrybucji kluczy](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Pamiętaj o następujących kwestiach:

- Domyślnie przywrócona nazwa bazy danych zostanie wypełniona nazwą elementu kopii zapasowej. W tym przypadku h21 (sdc).
- Wybranie obiektu docelowego jako H11 nie spowoduje automatycznej zmiany przywróconej nazwy bazy danych. **Należy go edytować do h11 (sdc)**. Jeśli chodzi o centrum SDC, przywróconą nazwą bazy danych będzie identyfikator wystąpienia docelowego z małymi literami i "sdc" dołączonymi w nawiasach kwadratowych.
- Ponieważ centrum SDC może mieć tylko jedną bazę danych, należy również zaznaczyć pole wyboru, aby umożliwić zastąpienie istniejących danych bazy danych danymi punktu odzyskiwania.
- W systemie Linux jest wróżniana wielkość liter. Dlatego należy zachować przypadek.

### <a name="multiple-container-database-mdc-restore"></a>Przywracanie bazy danych wielu kontenerów (MDC)

W wielu bazach danych kontenerów dla platformy HANA standardową konfiguracją jest SYSTEMDB + 1 lub więcej baz danych dzierżawy. Przywrócenie całego wystąpienia SAP HANA oznacza przywrócenie bazy danych SYSTEMDB i baz danych dzierżawy. Najpierw przywracana jest baza danych SYSTEMDB, a następnie jest kontynuowana w przypadku bazy danych dzierżawy. System DB zasadniczo oznacza zastąpienie informacji o systemie w wybranym celu. To przywracanie zastępuje również informacje powiązane z elementem BackInt w wystąpieniu docelowym. Dlatego po przywróceniu systemowej bazy danych do wystąpienia docelowego ponownie uruchom skrypt rejestracji wstępnej. Tylko wtedy kolejne przywracanie bazy danych dzierżawy zakończy się powodzeniem.

## <a name="back-up-a-replicated-vm"></a>Back up a replicated VM

### <a name="scenario-1"></a>Scenariusz 1

Oryginalna maszyna wirtualna została zreplikowana przy użyciu Azure Site Recovery kopii zapasowej maszyny wirtualnej platformy Azure. Nowa maszyna wirtualna została s zbudowana w celu symulowania starej maszyny wirtualnej. Oznacza to, że ustawienia są dokładnie takie same. (Jest to spowodowane tym, że oryginalna maszyna wirtualna została usunięta, a przywracanie zostało wykonane z kopii zapasowej maszyny wirtualnej lub Azure Site Recovery).

Ten scenariusz może obejmować dwa możliwe przypadki. Dowiedz się, jak tworzyć kopię zapasową replikowanej maszyny wirtualnej w obu tych przypadkach:

1. Nowa utworzona maszyna wirtualna ma taką samą nazwę i znajduje się w tej samej grupie zasobów i subskrypcji co usunięta maszyna wirtualna.

    - Rozszerzenie jest już obecne na maszynie wirtualnej, ale nie jest widoczne dla żadnej z usług
    - Uruchamianie skryptu przed rejestracją
    - Zarejestruj ponownie rozszerzenie dla tej samej maszyny w witrynie Azure Portal **(** Szczegóły widoku kopii zapasowej -> Wybierz odpowiednią maszynę wirtualną platformy Azure >  ->   zarejestruj ponownie)
    - Po pomyślnym rozpoczęciu tworzenia kopii zapasowej już istniejących baz danych kopii zapasowych (z usuniętej maszyny wirtualnej)

2. Utworzona nowa maszyna wirtualna ma:

    - inna nazwa niż usunięta maszyna wirtualna
    - ta sama nazwa co usunięta maszyna wirtualna, ale znajduje się w innej grupie zasobów lub subskrypcji (w porównaniu z usuniętą maszyną wirtualną)

    Jeśli tak jest, wykonaj następujące czynności:

    - Rozszerzenie jest już obecne na maszynie wirtualnej, ale nie jest widoczne dla żadnej z usług
    - Uruchamianie skryptu przed rejestracją
    - Jeśli odnajdzesz i chronisz nowe bazy danych, w portalu zaczniesz widzieć zduplikowane aktywne bazy danych. Aby tego uniknąć, [zatrzymaj ochronę przy zachowaniu danych](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) dla starych baz danych. Następnie przejdź do pozostałych kroków.
    - Odnajdywanie baz danych w celu włączenia kopii zapasowej
    - Włączanie tworzenia kopii zapasowych w tych bazach danych
    - Istniejące bazy danych kopii zapasowych (z usuniętej maszyny wirtualnej) będą nadal przechowywane w magazynie (z ich kopiami zapasami przechowywanymi zgodnie z zasadami)

### <a name="scenario-2"></a>Scenariusz 2

Oryginalna maszyna wirtualna została zreplikowana przy użyciu Azure Site Recovery kopii zapasowej maszyny wirtualnej platformy Azure. Nowa maszyna wirtualna została zbudowana z zawartości — ma być używana jako szablon. Jest to nowa maszyna wirtualna z nowym identyfikatorem SID.

Wykonaj następujące kroki, aby włączyć kopie zapasowe na nowej maszynie wirtualnej:

- Rozszerzenie jest już obecne na maszynie wirtualnej, ale nie jest widoczne dla żadnej z usług
- Uruchom skrypt przed rejestracją. Na podstawie identyfikatora SID nowej maszyny wirtualnej mogą wystąpić dwa scenariusze:
  - Oryginalna maszyna wirtualna i nowa maszyna wirtualna mają ten sam identyfikator SID. Skrypt przed rejestracją zostanie uruchomiony pomyślnie.
  - Oryginalna maszyna wirtualna i nowa maszyna wirtualna mają różne identyfikatory SID. Skrypt przed rejestracją nie powiedzie się. Skontaktuj się z pomocą techniczną, aby uzyskać pomoc w tym scenariuszu.
- Odnajdywanie baz danych, których kopię zapasową chcesz chcieć wrócić
- Włączanie tworzenia kopii zapasowych w tych bazach danych

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Uaktualnienie wersji centrum dystrybucji SDC lub uaktualnienie wersji MDC na tej samej maszynie wirtualnej

Uaktualnienia do systemu operacyjnego, zmiany wersji kontrolera SDC lub zmiany wersji MDC, które nie powodują zmiany identyfikatora SID, mogą być obsługiwane w następujący sposób:

- Upewnij się, że nowa wersja systemu operacyjnego, SDC lub MDC jest obecnie obsługiwana [przez Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Zatrzymywanie ochrony z zachowaniem danych](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) dla bazy danych
- Przeprowadzanie uaktualnienia lub aktualizacji
- Ponownie uruchomić skrypt przed rejestracją. Często proces uaktualniania może usuwać [niezbędne role.](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) Uruchomienie skryptu przed rejestracją pomoże zweryfikować wszystkie wymagane role.
- Ponownie wznów ochronę bazy danych

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>Uaktualnienie SDC do MDC bez zmiany identyfikatora SID

Uaktualnienia z centrum SDC do usługi MDC, które nie powodują zmiany identyfikatora SID, mogą być obsługiwane w następujący sposób:

- Upewnij się, że nowa wersja rozwiązania MDC jest obecnie [obsługiwana przez Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Zatrzymywanie ochrony z zachowaniem danych](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) dla starej bazy danych centrum dystrybucji kluczy
- Wykonaj uaktualnienie. Po zakończeniu system HANA jest teraz MDC z systemowej bazy danych i bazami danych dzierżawy
- Ponowne uruchamiania [skryptu przed rejestracją](https://aka.ms/scriptforpermsonhana)
- Zarejestruj ponownie rozszerzenie dla tej samej maszyny w witrynie Azure Portal **(** Szczegóły widoku kopii zapasowej -> Wybierz odpowiednią maszynę wirtualną platformy Azure  ->   —> zarejestruj ponownie)
- Wybierz **pozycję Rediscover DBs** for the same VM (Ponowne wykrywanie kopii zapasowych dla tej samej maszyny wirtualnej). Ta akcja powinna pokazywać nowe bazy danych w kroku 3 jako SYSTEMDB i Tenant DB, a nie SDC
- Starsza baza danych centrum dystrybucji danych będzie nadal istnieć w magazynie i będzie przechowywać stare dane kopii zapasowej zgodnie z zasadami
- Konfigurowanie kopii zapasowej dla tych baz danych

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>Uaktualnienie SDC do MDC ze zmianą identyfikatora SID

Uaktualnienia z centrum SDC do usługi MDC, które powodują zmianę identyfikatora SID, mogą być obsługiwane w następujący sposób:

- Upewnij się, że nowa wersja rozwiązania MDC jest obecnie [obsługiwana przez Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- **Zatrzymywanie ochrony z zachowaniem danych** dla starej bazy danych centrum dystrybucji kluczy
- Wykonaj uaktualnienie. Po zakończeniu system HANA jest teraz MDC z systemowej bazy danych i bazami danych dzierżawy
- Ponownie uruchomić skrypt [przed rejestracją z](https://aka.ms/scriptforpermsonhana) poprawnymi szczegółami (nowy identyfikator SID i MDC). Ze względu na zmianę identyfikatora SID mogą wystąpić problemy z pomyślnym uruchomieniem skryptu. Jeśli masz Azure Backup, skontaktuj się z pomocą techniczną.
- Zarejestruj ponownie rozszerzenie dla tej samej maszyny w witrynie Azure Portal **(Szczegóły** widoku kopii zapasowej -> Wybierz odpowiednią maszynę wirtualną platformy Azure — >  ->   zarejestruj ponownie)
- Wybierz **pozycję Rediscover DBs** for the same VM (Ponowne wykrywanie kopii zapasowych dla tej samej maszyny wirtualnej). Ta akcja powinna pokazywać nowe bazy danych w kroku 3 jako SYSTEMDB i Tenant DB, a nie SDC
- Starsza baza danych centrum dystrybucji kluczy będzie nadal istnieć w magazynie i będzie przechowywać starą kopię zapasową danych zgodnie z zasadami
- Konfigurowanie kopii zapasowej dla tych baz danych

## <a name="re-registration-failures"></a>Błędy ponownej rejestracji

Przed wyzwoleniem operacji ponownego rejestrowania sprawdź, czy występują co najmniej jeden z następujących objawów:

- Wszystkie operacje (takie jak tworzenie kopii zapasowej, przywracanie i konfigurowanie kopii zapasowej) na maszynie wirtualnej nie powiodą się z jednym z następujących kodów błędów: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Jeśli w **obszarze Stan** kopii zapasowej elementu kopii zapasowej jest wyświetlona pozycja Nieokierowane, należy wykluczyć wszystkie inne przyczyny, które mogą spowodować taki sam stan: 

  - Brak uprawnień do wykonywania operacji związanych z tworzeniem kopii zapasowych na maszynie wirtualnej
  - Maszyna wirtualna jest zamykana, więc nie można tworzyć kopii zapasowych
  - Problemy z siecią

Te objawy mogą wystąpić z co najmniej jednego z następujących powodów:

- Rozszerzenie zostało usunięte lub odinstalowane z portalu.
- Maszyna wirtualna została przywrócona z powrotem w czasie za pośrednictwem przywracania dysku w miejscu.
- Maszyna wirtualna została zamknięta na dłuższy czas, więc konfiguracja rozszerzenia na tej maszynie wygasła.
- Maszyna wirtualna została usunięta, a inna maszyna wirtualna została utworzona o tej samej nazwie i w tej samej grupie zasobów co usunięta maszyna wirtualna.

W poprzednich scenariuszach zalecamy wyzwolenie operacji ponownego rejestrowania na maszynie wirtualnej.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj często [zadawane pytania dotyczące kopii](./sap-hana-faq-backup-azure-vm.yml) zapasowej baz danych SAP HANA na maszyn wirtualnych platformy Azure.
