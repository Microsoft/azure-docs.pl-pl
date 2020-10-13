---
title: Rozwiązywanie problemów z kopiami zapasowymi baz danych SAP HANA
description: Opisuje sposób rozwiązywania typowych błędów, które mogą wystąpić podczas tworzenia kopii zapasowej SAP HANA baz danych przy użyciu Azure Backup.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 5cdad55ef849b9ced31646466e2c2c170ebf0827
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89377688"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowych baz danych SAP HANA na platformie Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczących tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji na SAP HANA obecnie obsługiwane scenariusze tworzenia kopii zapasowych, zobacz temat [Obsługa scenariusza](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Wymagania wstępne i uprawnienia

Przed skonfigurowaniem kopii zapasowych zapoznaj się z sekcją [wymagania wstępne](tutorial-backup-sap-hana-db.md#prerequisites) i [co to jest skrypt przed rejestracją](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

## <a name="common-user-errors"></a>Typowe błędy użytkowników

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Komunikat o błędzie**      | <span style="font-weight:normal">Azure Backup nie ma wymaganych uprawnień roli do przeprowadzenia kopii zapasowej</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Rola mogła zostać nadpisywana.                          |
| **Zalecana akcja** | Aby rozwiązać ten problem, uruchom skrypt z okienka **odnajdywanie bazy danych** lub Pobierz go [tutaj](https://aka.ms/scriptforpermsonhana). Alternatywnie Dodaj rolę "SAP_INTERNAL_HANA_SUPPORT" do użytkownika kopii zapasowej obciążenia (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Komunikat o błędzie      | <span style="font-weight:normal">Nie można nawiązać połączenia z systemem HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Wystąpienie SAP HANA może nie działać.<br/>Nie ustawiono wymaganych uprawnień do Azure Backup, aby można było korzystać z bazy danych HANA. |
| **Zalecana akcja** | Sprawdź, czy baza danych SAP HANA działa. Jeśli baza danych jest uruchomiona i działa, sprawdź, czy są ustawione wszystkie wymagane uprawnienia. Jeśli brakuje któregoś z uprawnień, uruchom [skrypt rejestracyjny](https://aka.ms/scriptforpermsonhana) , aby dodać brakujące uprawnienia. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Komunikat o błędzie      | <span style="font-weight:normal">Określone wystąpienie SAP HANA jest nieprawidłowe lub nie można go znaleźć</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Nie można utworzyć kopii zapasowej wielu wystąpień SAP HANA na jednej maszynie wirtualnej platformy Azure. |
| **Zalecana akcja** | Uruchom [skrypt rejestracji](https://aka.ms/scriptforpermsonhana) jednokrotnej w wystąpieniu SAP HANA, dla którego chcesz utworzyć kopię zapasową. Jeśli problem nadal występuje, skontaktuj się z pomocą techniczną firmy Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Komunikat o błędzie      | <span style="font-weight:normal">Określona operacja SAP HANA nie jest obsługiwana</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Azure Backup dla SAP HANA nie obsługuje przyrostowych kopii zapasowych i akcji wykonywanych na SAP HANA natywnych klientów (Studio/Panel sterowania/DBA) |
| **Zalecana akcja** | Więcej informacji można znaleźć [tutaj](./sap-hana-backup-support-matrix.md#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Komunikat o błędzie      | <span style="font-weight:normal">Ta SAP HANA baza danych nie obsługuje żądanego typu kopii zapasowej</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Azure Backup nie obsługuje przyrostowych kopii zapasowych i kopii zapasowych przy użyciu migawek |
| **Zalecana akcja** | Więcej informacji można znaleźć [tutaj](./sap-hana-backup-support-matrix.md#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Komunikat o błędzie      | <span style="font-weight:normal">Łańcuch dzienników kopii zapasowej został przerwany</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Miejsce docelowe kopii zapasowej dziennika mogło zostać zaktualizowane z BACKINT do systemu plików lub plik wykonywalny BACKINT mógł zostać zmieniony |
| **Zalecana akcja** | Wyzwól pełną kopię zapasową, aby rozwiązać ten problem                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Komunikat o błędzie      | <span style="font-weight:normal">Wykryto uaktualnienie SDC do MDC</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Wystąpienie SAP HANA zostało uaktualnione z SDC do MDC. Kopie zapasowe zakończą się niepowodzeniem po aktualizacji. |
| **Zalecana akcja** | Aby rozwiązać ten problem, wykonaj kroki opisane w temacie [SDC to MDC upgrade](#sdc-to-mdc-upgrade-with-a-change-in-sid) . |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Komunikat o błędzie      | <span style="font-weight:normal">Wykryto nieprawidłową konfigurację BACKINT</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Parametry zapasowe są niepoprawnie określone dla Azure Backup |
| **Zalecana akcja** | Sprawdź, czy są ustawione następujące parametry (BACKINT):<br/>\* [catalog_backup_using_backint: true]<br/>\* [enable_accumulated_catalog_backup: false]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>Jeśli na HOŚCIE znajdują się BACKINT parametry, usuń je. Jeśli parametry nie są dostępne na poziomie hosta, ale zostały ręcznie zmodyfikowane na poziomie bazy danych, przywróć je do odpowiednich wartości zgodnie z wcześniejszym opisem. Możesz też uruchomić polecenie [Zatrzymaj ochronę i zachować dane kopii zapasowej](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) z Azure Portal, a następnie wybrać polecenie **Wznów wykonywanie kopii zapasowej**. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Komunikat o błędzie  |Systemy źródłowe i docelowe na potrzeby przywracania są niezgodne  |
|---------|---------|
|Możliwe przyczyny   | Systemy źródłowe i docelowe wybrane do przywracania są niezgodne        |
|Zalecana akcja   |   Upewnij się, że Twój Scenariusz przywracania nie znajduje się na poniższej liście możliwych niezgodnych operacji przywracania: <br><br>   **Przypadek 1:** Nie można zmienić nazwy SYSTEMDB podczas przywracania.  <br><br> **Przypadek 2:** Source-SDC i Target-MDC: źródłowej bazy danych nie można przywrócić jako SYSTEMDB lub dzierżawcy bazy danych na serwerze docelowym. <br><br> **Przypadek 3:** Source-MDC i Target-SDC: nie można przywrócić źródłowej bazy danych (SYSTEMDB lub dzierżawcy bazy danych) do obiektu docelowego. <br><br>  Aby uzyskać więcej informacji, zobacz Uwaga **1642148** w programie [Launchpad SAP support](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Testy przywracania

### <a name="single-container-database-sdc-restore"></a>Przywracanie bazy danych z jednym kontenerem (SDC)

Weź pod uwagę dane wejściowe podczas przywracania pojedynczej bazy danych kontenerów (SDC) dla platformy HANA na inną maszynę SDC. Nazwa bazy danych powinna być połączona małymi literami z literą "SDC" w nawiasach. Wystąpienie HANA będzie wyświetlane w Wielkiej litery.

Załóżmy, że utworzono kopię zapasową wystąpienia SDC HANA "H21". Na stronie elementy kopii zapasowej będzie wyświetlana nazwa elementu kopii zapasowej jako **"H21 (SDC)"**. Jeśli próbujesz przywrócić tę bazę danych do innego obiektu docelowego SDC, powiedz H11, a następnie należy podać następujące dane wejściowe.

![Nazwa przywróconej bazy danych SDC](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Pamiętaj o następujących kwestiach:

- Domyślnie przywrócona Nazwa bazy danych zostanie wypełniona nazwą elementu kopii zapasowej. W tym przypadku H21 (SDC).
- Wybranie elementu docelowego jako H11 nie spowoduje automatycznego zmiany przywróconej nazwy bazy danych. **Powinien być edytowany w H11 (SDC)**. W odniesieniu do SDC nazwa przywróconej bazy danych będzie IDENTYFIKATORem wystąpienia docelowego z małymi literami i "SDC" dołączonym w nawiasach.
- Ponieważ SDC może mieć tylko jedną bazę danych, należy również zaznaczyć pole wyboru, aby umożliwić przesłonięcie istniejących danych bazy danych z użyciem danych punktu odzyskiwania.
- W systemie Linux jest rozróżniana wielkość liter. Należy zachować ostrożność.

### <a name="multiple-container-database-mdc-restore"></a>Przywracanie wielu baz danych (MDC)

W przypadku wielu baz danych kontenerów dla platformy HANA Standardowa konfiguracja to SYSTEMDB + 1 lub więcej baz danych dzierżawców. Przywrócenie całego wystąpienia SAP HANA oznacza przywrócenie zarówno SYSTEMDB, jak i baz danych dzierżawcy. Najpierw przywraca SYSTEMDB, a następnie przechodzi do bazy danych dzierżawcy. Zasadniczo systemowa baza danych zastępuje informacje o systemie w wybranym miejscu docelowym. To przywracanie przesłania również informacje dotyczące BackInt w wystąpieniu docelowym. Dlatego po przywróceniu bazy danych systemowych do wystąpienia docelowego Uruchom ponownie skrypt przed rejestracją. Kolejne Przywracanie bazy danych dzierżawy zakończy się powodzeniem.

## <a name="back-up-a-replicated-vm"></a>Tworzenie kopii zapasowej zreplikowanej maszyny wirtualnej

### <a name="scenario-1"></a>Scenariusz 1

Oryginalna maszyna wirtualna została zreplikowana przy użyciu Azure Site Recovery lub kopii zapasowej maszyny wirtualnej platformy Azure. Nowa maszyna wirtualna została skompilowana w celu symulowania starej maszyny wirtualnej. Oznacza to, że ustawienia są dokładnie takie same. (Dzieje się tak, ponieważ oryginalna maszyna wirtualna została usunięta, a przywracanie zostało wykonane z kopii zapasowej maszyny wirtualnej lub Azure Site Recovery).

Ten scenariusz może obejmować dwa możliwe przypadki. Dowiedz się, jak utworzyć kopię zapasową zreplikowanej maszyny wirtualnej w obu tych przypadkach:

1. Nowa utworzona maszyna wirtualna ma taką samą nazwę i należy do tej samej grupy zasobów i subskrypcji co usunięta maszyna wirtualna.

    - Rozszerzenie już istnieje na maszynie wirtualnej, ale nie jest widoczne dla żadnej z usług
    - Uruchamianie skryptu przed rejestracją
    - Zarejestruj ponownie rozszerzenie dla tego samego komputera w Azure Portal (szczegóły widoku**kopii zapasowej**  ->  **View details** -> wybierz odpowiednią maszynę wirtualną platformy Azure — > ponownej rejestracji)
    - Kopia zapasowa już istniejącej bazy danych (z usuniętej maszyny wirtualnej) powinna zostać pomyślnie uruchomiona

2. Nowa utworzona maszyna wirtualna ma:

    - inna nazwa niż usunięta maszyna wirtualna
    - taka sama nazwa jak usunięta maszyna wirtualna, ale znajduje się w innej grupie zasobów lub subskrypcji (w porównaniu do usuniętej maszyny wirtualnej)

    W takim przypadku wykonaj następujące czynności:

    - Rozszerzenie już istnieje na maszynie wirtualnej, ale nie jest widoczne dla żadnej z usług
    - Uruchamianie skryptu przed rejestracją
    - Jeśli wykryjesz i zaczniesz ochronę nowych baz danych, zobaczysz zduplikowane aktywne bazy danych w portalu. Aby tego uniknąć, [Zatrzymaj ochronę z zachowaniem Zachowaj dane](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) dla starych baz danych. Następnie kontynuuj pozostałe kroki.
    - Odkryj bazy danych, aby włączyć tworzenie kopii zapasowej
    - Włącz wykonywanie kopii zapasowych w tych bazach danych
    - Już istniejące kopie zapasowe baz danych (z usuniętej maszyny wirtualnej) będą nadal przechowywane w magazynie (z kopiami zapasowymi zachowywanymi zgodnie z zasadami)

### <a name="scenario-2"></a>Scenariusz 2

Oryginalna maszyna wirtualna została zreplikowana przy użyciu Azure Site Recovery lub kopii zapasowej maszyny wirtualnej platformy Azure. Nowa maszyna wirtualna została skompilowana z zawartości — do użycia jako szablon. To jest nowa maszyna wirtualna z nowym identyfikatorem SID.

Wykonaj następujące kroki, aby włączyć tworzenie kopii zapasowych na nowej maszynie wirtualnej:

- Rozszerzenie już istnieje na maszynie wirtualnej, ale nie jest widoczne dla żadnej z usług
- Uruchom skrypt przed rejestracją. Na podstawie identyfikatora SID nowej maszyny wirtualnej mogą wystąpić dwa scenariusze:
  - Oryginalna maszyna wirtualna i Nowa maszyna wirtualna mają ten sam identyfikator SID. Skrypt przed rejestracją zostanie uruchomiony pomyślnie.
  - Oryginalna maszyna wirtualna i Nowa maszyna wirtualna mają różne identyfikatory SID. Skrypt poprzedzający rejestrację zakończy się niepowodzeniem. Skontaktuj się z pomocą techniczną, aby uzyskać pomoc w tym scenariuszu.
- Odnajdywanie baz danych, dla których chcesz utworzyć kopię zapasową
- Włącz wykonywanie kopii zapasowych w tych bazach danych

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Uaktualnienie wersji SDC lub uaktualnienie wersji MDC na tej samej maszynie wirtualnej

Uaktualnienia do systemu operacyjnego, zmiany wersji SDC lub zmiany wersji MDC, które nie powodują zmiany identyfikatora SID, można obsłużyć w następujący sposób:

- Upewnij się, że nowa wersja systemu operacyjnego, SDC lub wersja MDC są obecnie [obsługiwane przez Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Zatrzymaj ochronę z zachowaniem zachowania danych](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) dla bazy danych
- Wykonaj uaktualnienie lub aktualizację
- Uruchom ponownie skrypt przed rejestracją. Zazwyczaj proces uaktualniania usuwa niezbędne role. Uruchomienie skryptu przed rejestracją pomoże sprawdzić wszystkie wymagane role
- Ponownie Wznów ochronę bazy danych

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SDC do MDC upgrade bez zmiany identyfikatora SID

Uaktualnienia z SDC do MDC, które nie powodują zmiany identyfikatora SID, mogą być obsługiwane w następujący sposób:

- Upewnij się, że nowa wersja MDC jest obecnie [obsługiwana przez Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- [Zatrzymaj ochronę z zachowaniem zachowania danych](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) dla starej bazy danych SDC
- Wykonaj uaktualnienie. Po zakończeniu system HANA jest teraz MDC z systemową bazą danych i dzierżawcą baz danych
- Uruchom ponownie [skrypt przed rejestracją](https://aka.ms/scriptforpermsonhana)
- Zarejestruj ponownie rozszerzenie dla tego samego komputera w Azure Portal (szczegóły widoku**kopii zapasowej**  ->  **View details** -> wybierz odpowiednią maszynę wirtualną platformy Azure — > ponownej rejestracji)
- Wybierz pozycję **Rediscovery baz danych** dla tej samej maszyny wirtualnej. Ta akcja powinna zawierać nowy baz danych w kroku 3 jako SYSTEMDB i bazę danych dzierżawy, a nie SDC
- Starsza baza danych SDC będzie nadal istnieć w magazynie i ma stare dane kopii zapasowej przechowywane zgodnie z zasadami
- Skonfiguruj kopię zapasową tych baz danych

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SDC MDC uaktualnianie przy użyciu zmiany identyfikatora SID

Uaktualnienia z SDC do MDC, które powodują zmianę identyfikatora SID można obsłużyć w następujący sposób:

- Upewnij się, że nowa wersja MDC jest obecnie [obsługiwana przez Azure Backup](sap-hana-backup-support-matrix.md#scenario-support)
- **Zatrzymaj ochronę z zachowaniem zachowania danych** dla starej bazy danych SDC
- Wykonaj uaktualnienie. Po zakończeniu system HANA jest teraz MDC z systemową bazą danych i dzierżawcą baz danych
- Uruchom ponownie [skrypt poprzedzający rejestrację](https://aka.ms/scriptforpermsonhana) z prawidłowymi szczegółami (nowy identyfikator SID i MDC). Ze względu na zmianę identyfikatora SID mogą wystąpić problemy z pomyślnie uruchomionym skryptem. Skontaktuj się z pomocą techniczną Azure Backup, Jeśli napotykasz problemy.
- Zarejestruj ponownie rozszerzenie dla tego samego komputera w Azure Portal (szczegóły widoku**kopii zapasowej**  ->  **View details** -> wybierz odpowiednią maszynę wirtualną platformy Azure — > ponownej rejestracji)
- Wybierz pozycję **Rediscovery baz danych** dla tej samej maszyny wirtualnej. Ta akcja powinna zawierać nowy baz danych w kroku 3 jako SYSTEMDB i bazę danych dzierżawy, a nie SDC
- Starsza baza danych SDC będzie nadal istnieć w magazynie i ma stare dane kopii zapasowej przechowywane zgodnie z zasadami
- Skonfiguruj kopię zapasową tych baz danych

## <a name="re-registration-failures"></a>Błędy ponownej rejestracji

Przed wyzwoleniem operacji ponownego rejestrowania Sprawdź co najmniej jeden z następujących objawów:

- Wszystkie operacje (takie jak tworzenie kopii zapasowej, przywracanie i konfigurowanie kopii zapasowej) kończą się niepowodzeniem na maszynie wirtualnej z jednym z następujących kodów błędów: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Jeśli obszar **stanu kopii** zapasowej dla elementu kopii zapasowej jest wyświetlany jako **nieosiągalny**, należy wykluczyć wszystkie inne przyczyny, które mogą spowodować wystąpienie tego samego stanu:

  - Brak uprawnień do wykonywania operacji związanych z kopiami zapasowymi na maszynie wirtualnej
  - Maszyna wirtualna jest wyłączona, dlatego nie można wykonać kopii zapasowych
  - Problemy z siecią

Te objawy mogą wystąpić z następujących powodów:

- Rozszerzenie zostało usunięte lub odinstalowane z portalu.
- Maszyna wirtualna została przywrócona z powrotem w czasie za pomocą przywracania z dysku w miejscu.
- Maszyna wirtualna została zamknięta przez dłuższy czas, więc konfiguracja rozszerzenia wygasła.
- Maszyna wirtualna została usunięta, a inna maszyna wirtualna została utworzona o tej samej nazwie i w tej samej grupie zasobów co usunięta maszyna wirtualna.

W powyższych scenariuszach zalecamy wyzwolenie operacji ponownego zarejestrowania na maszynie wirtualnej.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [często zadawane pytania](./sap-hana-faq-backup-azure-vm.md) dotyczące tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure.
