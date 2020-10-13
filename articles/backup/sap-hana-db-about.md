---
title: Informacje o kopii zapasowej bazy danych SAP HANA na maszynach wirtualnych platformy Azure
description: W tym artykule dowiesz się, jak tworzyć kopie zapasowe baz danych SAP HANA, które są uruchomione na maszynach wirtualnych platformy Azure.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: e30510817401fd8db23dc9f1d62fab495fac7ab2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022313"
---
# <a name="about-sap-hana-database-backup-in-azure-vms"></a>Informacje o kopii zapasowej bazy danych SAP HANA na maszynach wirtualnych platformy Azure

Bazy danych SAP HANA są obciążeniami o znaczeniu krytycznym, które wymagają małego celu punktu odzyskiwania (RPO) i szybkiego celu czasu odzyskiwania (RTO). Teraz można [tworzyć kopie zapasowe baz danych SAP HANA działających na maszynach wirtualnych platformy Azure](./tutorial-backup-sap-hana-db.md) przy użyciu [Azure Backup](./backup-overview.md).

Azure Backup jest [BACKINT certyfikowany](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) przez SAP, aby zapewnić natywną obsługę kopii zapasowych, wykorzystując natywne interfejsy API SAP HANA. Ta oferta z Azure Backup jest wyrównania Azure Backup mantrą kopii zapasowych o **zerowej infrastrukturze** , eliminując konieczność wdrażania infrastruktury kopii zapasowych i zarządzania nią. Teraz można bezproblemowo tworzyć kopie zapasowe i przywracać SAP HANA bazy danych działające na maszynach wirtualnych platformy Azure ([maszyny wirtualne serii M](../virtual-machines/m-series.md) są również obsługiwane) i korzystać z możliwości zarządzania przedsiębiorstwem, które Azure Backup zapewnia.

## <a name="added-value"></a>Dodana wartość

Używanie Azure Backup do tworzenia kopii zapasowych i przywracania SAP HANA baz danych programu daje następujące korzyści:

* **15-minutowy cel punktu odzyskiwania (RPO)**: możliwe jest odzyskanie krytycznych danych z maksymalnie 15 minut.
* Przywracanie z **jednym kliknięciem do punktu w czasie**: przywrócenie danych produkcyjnych do alternatywnych serwerów Hana odbywa się w prosty sposób. Łańcuch tworzenia kopii zapasowych i wykazów do wykonania przywracania jest zarządzany przez platformę Azure w tle.
* **Długoterminowe przechowywanie**: w celu uzyskania rygorystycznych wymagań dotyczących zgodności i inspekcji. Przechowuj kopie zapasowe przez lata na podstawie czasu przechowywania, po upływie którego punkty odzyskiwania zostaną automatycznie oczyszczone przez wbudowaną funkcję zarządzania cyklem życia.
* **Zarządzanie kopiami zapasowymi z platformy Azure**: Użyj funkcji zarządzania i monitorowania Azure Backup, aby uzyskać Ulepszone środowisko zarządzania. Interfejs wiersza polecenia platformy Azure jest również obsługiwany.

Aby wyświetlić scenariusze tworzenia kopii zapasowych i przywracania, które obsługuje dzisiaj, zapoznaj się z artykułem [Obsługa scenariusza SAP HANA](./sap-hana-backup-support-matrix.md#scenario-support).

## <a name="backup-architecture"></a>Architektura kopii zapasowych

![Diagram architektury tworzenia kopii zapasowych](./media/sap-hana-db-about/backup-architecture.png)

* Proces tworzenia kopii zapasowej rozpoczyna się od [utworzenia magazynu Recovery Services](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) na platformie Azure. Ten magazyn będzie używany do przechowywania kopii zapasowych i punktów odzyskiwania utworzonych w czasie.
* Maszyna wirtualna platformy Azure z systemem SAP HANA Server jest zarejestrowana w magazynie, a bazy danych do utworzenia kopii zapasowej są [odnajdywane](./tutorial-backup-sap-hana-db.md#discover-the-databases). Aby umożliwić usłudze Azure Backup odnajdywanie baz danych, należy uruchomić [skrypt rejestracji](https://aka.ms/scriptforpermsonhana) na serwerze Hana jako użytkownik główny.
* Ten skrypt tworzy użytkownika **AZUREWLBACKUPHANAUSER** DB i odpowiadający mu klucz o tej samej nazwie w **hdbuserstore**. Zapoznaj się z sekcją co to jest  [skrypt poprzedzający rejestrację](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) , aby dowiedzieć się więcej na temat tego, co robi skrypt.
* Usługa Azure Backup teraz instaluje **wtyczkę Azure Backup dla platformy Hana** na zarejestrowanym serwerze SAP HANA.
* Użytkownik **AZUREWLBACKUPHANAUSER** DB utworzony przez skrypt przedrejestrowania jest używany przez **wtyczkę Azure Backup platformy Hana** do wykonywania wszystkich operacji tworzenia kopii zapasowej i przywracania. W przypadku próby skonfigurowania kopii zapasowej dla SAP HANA baz danych bez uruchamiania tego skryptu może zostać wyświetlony następujący błąd: **UserErrorHanaScriptNotRun**.
* Aby [skonfigurować tworzenie kopii zapasowych](./tutorial-backup-sap-hana-db.md#configure-backup) w odnalezionych bazach danych, wybierz wymagane zasady tworzenia kopii zapasowych i Włącz kopie zapasowe.

* Po skonfigurowaniu kopii zapasowej usługa Azure Backup konfiguruje następujące parametry BACKINT na poziomie bazy danych na serwerze chronionym SAP HANA:
  * [catalog_backup_using_backint: true]
  * [enable_accumulated_catalog_backup: false]
  * [parallel_data_backup_backint_channels: 1]
  * [log_backup_timeout_s: 900)]
  * [backint_response_timeout: 7200]

>[!NOTE]
>Upewnij się, że te parametry *nie* występują na poziomie hosta. Parametry na poziomie hosta przesłonią te parametry i mogą spowodować nieoczekiwane zachowanie.
>

* **Wtyczka Azure Backup dla platformy Hana** obsługuje wszystkie harmonogramy tworzenia kopii zapasowych i Szczegóły zasad. Wyzwala zaplanowaną kopię zapasową i komunikuje się z **aparatem tworzenia kopii zapasowych Hana** za pomocą interfejsów API BACKINT.
* **Aparat kopii zapasowych Hana** zwraca strumień BACKINT z danymi, których kopia zapasowa ma zostać utworzona.
* Wszystkie zaplanowane kopie zapasowe i kopie zapasowe na żądanie (wyzwolone za pomocą Azure Portal), które są pełnymi lub różnicowanymi, są inicjowane przez **wtyczkę Azure Backup dla platformy Hana**. Kopie zapasowe dzienników są jednak zarządzane i wyzwalane przez sam **Aparat kopii zapasowych Hana** .
* Azure Backup dla SAP HANA, to rozwiązanie z certyfikatem BackInt, nie jest zależne od dysków źródłowych ani typów maszyn wirtualnych. Kopia zapasowa jest wykonywana przez strumienie wygenerowane przez platformę HANA.

## <a name="using-azure-vm-backup-with-azure-sap-hana-backup"></a>Korzystanie z kopii zapasowej maszyny wirtualnej platformy Azure za pomocą usługi Azure SAP HANA Backup

Oprócz tworzenia kopii zapasowych i odzyskiwania na poziomie bazy danych za pomocą programu SAP HANA Backup na platformie Azure można użyć rozwiązania do tworzenia kopii zapasowych maszyny wirtualnej platformy Azure w celu utworzenia kopii zapasowej dysków systemu operacyjnego i innych niż bazy danych.

Do tworzenia kopii zapasowych i odzyskiwania bazy danych można używać [BACKINT certyfikowane rozwiązanie do tworzenia kopii zapasowych w usłudze Azure SAP HANA](#backup-architecture) .

[Kopia zapasowa maszyny wirtualnej platformy Azure](backup-azure-vms-introduction.md) może służyć do tworzenia kopii zapasowej systemu operacyjnego i innych dysków nienależących do bazy danych. Kopia zapasowa maszyny wirtualnej jest wykonywana raz dziennie i zawiera kopie zapasowe wszystkich dysków (z wyjątkiem **dysków akcelerator zapisu (WA)** i **Ultra disks**). Ponieważ kopia zapasowa bazy danych jest tworzona przy użyciu rozwiązania do tworzenia kopii zapasowych w usłudze Azure SAP HANA, można wykonać kopię zapasową spójną na poziomie plików tylko dla dysków systemu operacyjnego i innych niż bazy danych za pomocą funkcji [tworzenia kopii zapasowych i przywracania dysków dla maszyn wirtualnych platformy Azure](selective-disk-backup-restore.md) .

>[!NOTE]
> Używanie skryptów gotowych do utworzenia kopii zapasowej maszyny wirtualnej platformy Azure umożliwia tworzenie kopii zapasowych spójnych na poziomie aplikacji dla woluminów danych bazy danych. Jeśli jednak obszar dziennika znajduje się na dyskach WA, wykonanie migawki tych dysków może nie zagwarantować spójności obszaru dziennika. HANA ma jawny sposób generowania kopii zapasowych dzienników z tego powodu. Włącz te same w SAP HANA i można utworzyć ich kopię zapasową za pomocą usługi Azure SAP HANA Backup.

Aby przywrócić maszynę wirtualną z systemem SAP HANA, wykonaj następujące kroki:

* [Przywróć nową maszynę wirtualną z kopii zapasowej maszyny wirtualnej platformy Azure](backup-azure-arm-restore-vms.md) z najnowszego punktu odzyskiwania. Lub Utwórz nową pustą maszynę wirtualną i Dołącz dyski z najnowszego punktu odzyskiwania.
* Ponieważ nie są tworzone kopie zapasowe dysków WA, nie są one przywracane. Utwórz puste dyski WA i obszar dziennika.
* Po ustawieniu wszystkich innych konfiguracji (takich jak adres IP, nazwa systemu i tak dalej), maszyna wirtualna jest ustawiana na odbieranie danych z Azure Backup.
* Teraz Przywróć bazę danych do maszyny wirtualnej z [kopii zapasowej usługi Azure SAP HANA DB](sap-hana-db-restore.md#restore-to-a-point-in-time-or-to-a-recovery-point) do żądanego punktu w czasie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [przywrócić bazę danych SAP HANA działającej na maszynie wirtualnej platformy Azure](./sap-hana-db-restore.md)
* Dowiedz się [, jak zarządzać bazami danych SAP HANA, których kopia zapasowa została utworzona przy użyciu Azure Backup](./sap-hana-db-manage.md)
