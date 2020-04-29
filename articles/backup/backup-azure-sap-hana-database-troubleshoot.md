---
title: Rozwiązywanie problemów z kopiami zapasowymi baz danych SAP HANA
description: Opisuje sposób rozwiązywania typowych błędów, które mogą wystąpić podczas tworzenia kopii zapasowej SAP HANA baz danych przy użyciu Azure Backup.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79459314"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Rozwiązywanie problemów z tworzeniem kopii zapasowych baz danych SAP HANA na platformie Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów dotyczących tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji na SAP HANA obecnie obsługiwane scenariusze tworzenia kopii zapasowych, zobacz temat [Obsługa scenariusza](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Wymagania wstępne i uprawnienia

Przed skonfigurowaniem kopii zapasowych zapoznaj się z sekcją [wymagania wstępne](tutorial-backup-sap-hana-db.md#prerequisites) i [co to jest skrypt przed rejestracją](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

## <a name="common-user-errors"></a>Typowe błędy użytkowników

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Komunikat o błędzie**      | <span style="font-weight:normal">Usługa Azure Backup nie ma uprawnień do wykonywania kopii zapasowych wymaganych przez rolę.</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Rola mogła zostać nadpisywana.                          |
| **Zalecana akcja** | Aby rozwiązać ten problem, uruchom skrypt z okienka **odnajdywanie bazy danych** lub Pobierz go [tutaj](https://aka.ms/scriptforpermsonhana). Alternatywnie Dodaj rolę "SAP_INTERNAL_HANA_SUPPORT" do użytkownika kopii zapasowej obciążenia (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Komunikat o błędzie      | <span style="font-weight:normal">Nie można nawiązać połączenia z systemem HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Wystąpienie SAP HANA może nie działać.<br/>Nie są ustawione uprawnienia wymagane dla usługi Azure Backup w celu współdziałania z bazą danych HANA. |
| **Zalecana akcja** | Sprawdź, czy baza danych SAP HANA działa. Jeśli baza danych jest uruchomiona i działa, sprawdź, czy są ustawione wszystkie wymagane uprawnienia. Jeśli brakuje któregoś z uprawnień, uruchom [skrypt rejestracyjny](https://aka.ms/scriptforpermsonhana) , aby dodać brakujące uprawnienia. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Komunikat o błędzie      | <span style="font-weight:normal">Określone wystąpienie SAP HANA jest nieprawidłowe lub nie można go znaleźć</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Nie można utworzyć kopii zapasowej wielu wystąpień SAP HANA na jednej maszynie wirtualnej platformy Azure. |
| **Zalecana akcja** | Uruchom [skrypt rejestracji](https://aka.ms/scriptforpermsonhana) jednokrotnej w wystąpieniu SAP HANA, dla którego chcesz utworzyć kopię zapasową. Jeśli problem nadal występuje, skontaktuj się z pomocą techniczną firmy Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Komunikat o błędzie      | <span style="font-weight:normal">Określona operacja SAP HANA nie jest obsługiwana</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Usługa Azure Backup dla SAP HANA nie obsługuje przyrostowych kopii zapasowych i akcji wykonywanych na SAP HANA natywnych klientów (Studio/Panel sterowania/DBA) |
| **Zalecana akcja** | Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Komunikat o błędzie      | <span style="font-weight:normal">Ta SAP HANA baza danych nie obsługuje żądanego typu kopii zapasowej</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Usługa Azure Backup nie obsługuje przyrostowych kopii zapasowych i kopii zapasowych przy użyciu migawek |
| **Zalecana akcja** | Więcej informacji można znaleźć [tutaj](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Komunikat o błędzie      | <span style="font-weight:normal">Łańcuch dzienników kopii zapasowej został przerwany</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Miejsce docelowe kopii zapasowej dziennika mogło zostać zaktualizowane z BACKINT do systemu plików lub plik wykonywalny BACKINT mógł zostać zmieniony |
| **Zalecana akcja** | Wyzwól pełną kopię zapasową, aby rozwiązać ten problem                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| Komunikat o błędzie      | <span style="font-weight:normal">Systemy źródłowe i docelowe na potrzeby przywracania są niezgodne</span>    |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | System docelowy przywracania jest niezgodny ze źródłem |
| **Zalecana akcja** | Zapoznaj się z uwagą do [1642148](https://launchpad.support.sap.com/#/notes/1642148) dla oprogramowania SAP, aby dowiedzieć się więcej na temat typów przywracania obsługiwanych dzisiaj |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Komunikat o błędzie      | <span style="font-weight:normal">Wykryto uaktualnienie SDC do MDC</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Wystąpienie SAP HANA zostało uaktualnione z SDC do MDC. Kopie zapasowe zakończą się niepowodzeniem po aktualizacji. |
| **Zalecana akcja** | Aby rozwiązać ten problem, wykonaj kroki opisane w [sekcji Uaktualnianie z SAP HANA 1,0 do 2,0](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) . |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Komunikat o błędzie      | <span style="font-weight:normal">Wykryto nieprawidłową konfigurację BACKINT</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Możliwe przyczyny**    | Parametry zapasowe są niepoprawnie określone dla usługi Azure Backup |
| **Zalecana akcja** | Sprawdź, czy są ustawione następujące parametry (BACKINT):<br/>\*[catalog_backup_using_backint: true]<br/>\*[enable_accumulated_catalog_backup: false]<br/>\*[parallel_data_backup_backint_channels: 1]<br/>\*[log_backup_timeout_s: 900)]<br/>\*[backint_response_timeout: 7200]<br/>Jeśli na HOŚCIE znajdują się BACKINT parametry, usuń je. Jeśli parametry nie są dostępne na poziomie hosta, ale zostały ręcznie zmodyfikowane na poziomie bazy danych, przywróć je do odpowiednich wartości zgodnie z wcześniejszym opisem. Możesz też uruchomić polecenie [Zatrzymaj ochronę i zachować dane kopii zapasowej](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) z Azure Portal, a następnie wybrać polecenie **Wznów wykonywanie kopii zapasowej**. |

## <a name="restore-checks"></a>Testy przywracania

### <a name="single-container-database-sdc-restore"></a>Przywracanie bazy danych z jednym kontenerem (SDC)

Weź pod uwagę dane wejściowe podczas przywracania pojedynczej bazy danych kontenerów (SDC) dla platformy HANA na inną maszynę SDC. Nazwa bazy danych powinna być połączona małymi literami z literą "SDC" w nawiasach. Wystąpienie HANA będzie wyświetlane w Wielkiej litery.

Załóżmy, że utworzono kopię zapasową wystąpienia SDC HANA "H21". Na stronie elementy kopii zapasowej będzie wyświetlana nazwa elementu kopii zapasowej jako **"H21 (SDC)"**. Jeśli próbujesz przywrócić tę bazę danych do innego obiektu docelowego SDC, powiedz H11, a następnie należy podać następujące dane wejściowe.

![Nazwa przywróconej bazy danych SDC](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Pamiętaj o następujących kwestiach:

- Domyślnie przywrócona Nazwa bazy danych zostanie wypełniona nazwą elementu kopii zapasowej. W tym przypadku H21 (SDC).
- Wybranie elementu docelowego jako H11 nie spowoduje automatycznej zmiany przywróconej nazwy bazy danych. **Powinien być edytowany w H11 (SDC)**. W odniesieniu do SDC nazwa przywróconej bazy danych będzie IDENTYFIKATORem wystąpienia docelowego z małymi literami i "SDC" dołączonym w nawiasach.
- Ponieważ SDC może mieć tylko jedną bazę danych, należy również kliknąć pole wyboru, aby umożliwić przesłonięcie istniejących danych bazy danych z użyciem danych punktu odzyskiwania.
- W systemie Linux jest rozróżniana wielkość liter. Należy zachować ostrożność.

### <a name="multiple-container-database-mdc-restore"></a>Przywracanie wielu baz danych (MDC)

W przypadku wielu baz danych kontenerów dla platformy HANA Standardowa konfiguracja to SYSTEMDB + 1 lub więcej baz danych dzierżawców. Przywrócenie całego wystąpienia SAP HANA oznacza przywrócenie zarówno SYSTEMDB, jak i baz danych dzierżawcy. Najpierw przywraca SYSTEMDB, a następnie przechodzi do bazy danych dzierżawcy. Zasadniczo systemowa baza danych zastępuje informacje o systemie w wybranym miejscu docelowym. To przywracanie przesłania również informacje dotyczące BackInt w wystąpieniu docelowym. Dlatego po przywróceniu bazy danych systemowych do wystąpienia docelowego Uruchom ponownie skrypt przed rejestracją. Kolejne Przywracanie bazy danych dzierżawy zakończy się powodzeniem.

## <a name="upgrading-from-sap-hana-10-to-20"></a>Uaktualnianie z SAP HANA 1,0 do 2,0

Jeśli chronisz bazy danych SAP HANA 1,0 i chcesz przeprowadzić uaktualnienie do wersji 2,0, wykonaj następujące czynności:

- [Zatrzymaj ochronę](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) z zachowaniem zachowania danych dla starej bazy danych SDC.
- Wykonaj uaktualnienie. Po zakończeniu program HANA jest teraz MDC z systemową bazą danych i bazami danych dzierżaw
- Uruchom ponownie [skrypt poprzedzający rejestrację](https://aka.ms/scriptforpermsonhana) z prawidłowymi szczegółami (SID i MDC).
- Ponownie zarejestruj rozszerzenie dla tego samego komputera w Azure Portal (szczegóły wyświetlania > kopii zapasowej-> wybierz odpowiednią maszynę wirtualną platformy Azure — > ponownie register).
- Kliknij przycisk ponownie odkryj baz danych dla tej samej maszyny wirtualnej. Ta akcja powinna spowodować wyświetlenie nowego baz danych w kroku 2 z prawidłowymi szczegółami (SYSTEMDB i dzierżawcą bazy danych, a nie SDC).
- Skonfiguruj kopię zapasową tych nowych baz danych.

## <a name="upgrading-without-an-sid-change"></a>Uaktualnianie bez zmiany identyfikatora SID

Uaktualnienia do systemu operacyjnego lub SAP HANA, które nie powodują zmiany identyfikatora SID, można obsłużyć, jak opisano poniżej:

- [Zatrzymaj ochronę](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) z zachowaniem zachowania danych dla bazy danych
- Wykonaj uaktualnienie.
- Uruchom ponownie [skrypt przed rejestracją](https://aka.ms/scriptforpermsonhana). Zwykle został obserwowany proces uaktualniania usuwa niezbędne role. Uruchomienie skryptu przed rejestracją pomoże sprawdzić wszystkie wymagane role.
- Ponownie [Wznów ochronę](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) bazy danych

## <a name="re-registration-failures"></a>Błędy ponownej rejestracji

Przed wyzwoleniem operacji ponownego rejestrowania Sprawdź co najmniej jeden z następujących objawów:

- Wszystkie operacje (takie jak tworzenie kopii zapasowej, przywracanie i konfigurowanie kopii zapasowej) kończą się niepowodzeniem na maszynie wirtualnej z jednym z następujących kodów błędów: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Jeśli obszar **stanu kopii** zapasowej dla elementu kopii zapasowej jest wyświetlany jako **nieosiągalny**, należy wykluczyć wszystkie inne przyczyny, które mogą spowodować wystąpienie tego samego stanu:

  - Brak uprawnień do wykonywania operacji związanych z kopiami zapasowymi na maszynie wirtualnej
  - Maszyna wirtualna jest zamykana, dlatego nie można wykonać kopii zapasowych
  - Problemy z siecią

Te objawy mogą wystąpić z następujących powodów:

- Rozszerzenie zostało usunięte lub odinstalowane z portalu.
- Maszyna wirtualna została przywrócona z powrotem w czasie za pomocą przywracania z dysku w miejscu.
- Maszyna wirtualna została zamknięta przez dłuższy czas, więc konfiguracja rozszerzenia wygasła.
- Maszyna wirtualna została usunięta, a inna maszyna wirtualna została utworzona o tej samej nazwie i w tej samej grupie zasobów co usunięta maszyna wirtualna.

W powyższych scenariuszach zalecamy wyzwolenie operacji ponownego zarejestrowania na maszynie wirtualnej.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [często zadawane pytania](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) dotyczące tworzenia kopii zapasowych baz danych SAP HANA na maszynach wirtualnych platformy Azure.
