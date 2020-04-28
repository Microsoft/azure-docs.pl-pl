---
title: Przywracanie SAP HANA baz danych na maszynach wirtualnych platformy Azure
description: W tym artykule opisano sposób przywracania SAP HANA baz danych uruchomionych w usłudze Azure Virtual Machines.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74287920"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Przywracanie SAP HANA baz danych na maszynach wirtualnych platformy Azure

W tym artykule opisano sposób przywracania SAP HANA baz danych uruchomionych na maszynie wirtualnej platformy Azure (VM), Azure Backup których kopia zapasowa została utworzona w magazynie Azure Backup Recovery Services. Przywracanie może służyć do tworzenia kopii danych dla scenariuszy deweloperskich/testowych lub powrotu do poprzedniego stanu.

Aby uzyskać więcej informacji na temat tworzenia kopii zapasowych SAP HANA baz danych, zobacz [Tworzenie kopii zapasowych SAP HANA baz danych na maszynach wirtualnych platformy Azure](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database).

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>Przywracanie do punktu w czasie lub do punktu odzyskiwania

Azure Backup można przywrócić SAP HANA baz danych uruchomionych na maszynach wirtualnych platformy Azure w następujący sposób:

* Przywracanie do określonej daty lub czasu (w drugim) przy użyciu kopii zapasowych dziennika. Azure Backup automatycznie określa odpowiednie pełne, różnicowe kopie zapasowe i łańcuch kopii zapasowych dziennika, które są wymagane do przywrócenia w oparciu o wybrany czas.

* Przywróć do konkretnej pełnej lub różnicowej kopii zapasowej, aby przywrócić do określonego punktu odzyskiwania.

## <a name="prerequisites"></a>Wymagania wstępne

Przed przywróceniem bazy danych należy zwrócić uwagę na następujące kwestie:

* Bazę danych można przywrócić tylko do wystąpienia SAP HANA, które znajduje się w tym samym regionie.

* Wystąpienie docelowe musi być zarejestrowane w tym samym magazynie co Źródło

* Azure Backup nie może zidentyfikować dwóch różnych wystąpień SAP HANA na tej samej maszynie wirtualnej. W związku z tym przywracanie danych z jednego wystąpienia do innego na tej samej maszynie wirtualnej nie jest możliwe

* Aby upewnić się, że docelowe wystąpienie SAP HANA jest gotowe do przywrócenia, sprawdź stan **gotowości do utworzenia kopii zapasowej** :

  * Otwieranie magazynu, w którym zarejestrowano wystąpienie SAP HANA docelowego

  * Na pulpicie nawigacyjnym magazynu w obszarze **wprowadzenie**wybierz pozycję **kopia zapasowa**

![Tworzenie kopii zapasowej na pulpicie nawigacyjnym magazynu](media/sap-hana-db-restore/getting-started-backup.png)

* W obszarze **kopia zapasowa**w obszarze **co chcesz utworzyć kopię zapasową?** wybierz **SAP HANA na maszynie wirtualnej platformy Azure**

![Wybierz SAP HANA na maszynie wirtualnej platformy Azure](media/sap-hana-db-restore/sap-hana-backup.png)

* W obszarze **odnajdowanie baz danych na maszynach wirtualnych** kliknij pozycję **Wyświetl szczegóły.**

![Wyświetl szczegóły](media/sap-hana-db-restore/view-details.png)

* Przejrzyj **gotowość do tworzenia kopii zapasowej** docelowej maszyny wirtualnej

![Serwery chronione](media/sap-hana-db-restore/protected-servers.png)

* Aby dowiedzieć się więcej na temat typów przywracania obsługiwanych przez SAP HANA, zapoznaj się z artykułem SAP HANA Uwaga [1642148](https://launchpad.support.sap.com/#/notes/1642148)

## <a name="restore-a-database"></a>Przywracanie bazy danych

* Otwórz magazyn, w którym zarejestrowano bazę danych SAP HANA, która ma zostać przywrócona

* Na pulpicie nawigacyjnym magazynu w obszarze **chronione elementy**wybierz pozycję **elementy kopii zapasowej** .

![Elementy kopii zapasowej](media/sap-hana-db-restore/backup-items.png)

* W obszarze **elementy kopii zapasowej**w obszarze **Typ zarządzania kopiami zapasowymi** wybierz pozycję **SAP HANA na maszynie wirtualnej platformy Azure**

![Typ zarządzania kopiami zapasowymi](media/sap-hana-db-restore/backup-management-type.png)

* Wybierz bazę danych do przywrócenia

 ![Baza danych do przywrócenia](media/sap-hana-db-restore/database-to-restore.png)

* Przejrzyj menu Baza danych. Zawiera informacje o kopii zapasowej bazy danych, w tym:

  * Najstarsze i najnowsze punkty przywracania

  * Stan kopii zapasowej dziennika dla ostatnich 24 i 72 godzin dla bazy danych

![Menu bazy danych](media/sap-hana-db-restore/database-menu.png)

* Wybierz pozycję **Przywróć bazę danych**

* W obszarze **Przywracanie konfiguracji**Określ, gdzie (lub jak) przywrócić dane:

  * **Lokalizacja alternatywna**: Przywracanie bazy danych do alternatywnej lokalizacji i zachowywanie oryginalnej źródłowej bazy danych.

  * **Zastąp bazę**danych: Przywróć dane do tego samego wystąpienia SAP HANA co oryginalne źródło. Ta opcja zastępuje oryginalną bazę danych.

![Przywróć konfigurację](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>Przywróć do lokalizacji alternatywnej

* W menu **Przywróć konfigurację** w obszarze **gdzie należy przywrócić**wybierz pozycję **Lokalizacja alternatywna**.

![Przywróć do lokalizacji alternatywnej](media/sap-hana-db-restore/restore-alternate-location.png)

* Wybierz nazwę hosta SAP HANA i nazwę wystąpienia, do którego chcesz przywrócić bazę danych.
* Sprawdź, czy docelowe wystąpienie SAP HANA jest gotowe do przywrócenia, zapewniając **gotowość do wykonania kopii zapasowej.** Więcej informacji można znaleźć w [sekcji wymagania wstępne](#prerequisites) .
* W polu **Nazwa przywróconej bazy danych** wprowadź nazwę docelowej bazy danych.

> [!NOTE]
> Przywracanie kontenerów pojedyncza baza danych (SDC) musi być zgodne z tymi [sprawdzeniami](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore).

* Jeśli ma to zastosowanie, wybierz opcję **Zastąp, jeśli baza danych o takiej samej nazwie już istnieje w wybranym wystąpieniu platformy Hana**.
* Kliknij przycisk **OK**.

![Przywracanie konfiguracji — Ekran końcowy](media/sap-hana-db-restore/restore-configuration-last.png)

* W obszarze **Wybierz punkt przywracania**wybierz pozycję **dzienniki (punkt w czasie)** , aby [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time). Lub wybierz opcję **pełna & różnicowa** , aby [przywrócić do określonego punktu odzyskiwania](#restore-to-a-specific-recovery-point).

### <a name="restore-and-overwrite"></a>Przywróć i Zastąp

* W menu **Przywróć konfigurację** w obszarze **gdzie można przywrócić**wybierz pozycję **Zastąp bazę danych** > **OK**.

![Zastąp bazę danych](media/sap-hana-db-restore/overwrite-db.png)

* W obszarze **Wybierz punkt przywracania**wybierz pozycję **dzienniki (punkt w czasie)** , aby [przywrócić do określonego punktu w czasie](#restore-to-a-specific-point-in-time). Lub wybierz opcję **pełna & różnicowa** , aby [przywrócić do określonego punktu odzyskiwania](#restore-to-a-specific-recovery-point).

### <a name="restore-to-a-specific-point-in-time"></a>Przywracanie do określonego punktu w czasie

Jeśli wybrano opcję **dzienniki (punkt w czasie)** jako typ przywracania, wykonaj następujące czynności:

* Wybierz punkt odzyskiwania z grafu dziennika, a następnie wybierz **przycisk OK** , aby wybrać punkt przywracania.

![Punkt przywracania](media/sap-hana-db-restore/restore-point.png)

* W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.

![Wybierz pozycję Przywróć](media/sap-hana-db-restore/restore-restore.png)

* Śledź postęp przywracania w obszarze **powiadomień** lub śledź go, wybierając pozycję **Przywróć zadania** w menu Baza danych.

![Przywracanie zostało wyzwolone pomyślnie](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>Przywracanie do określonego punktu odzyskiwania

Jeśli wybrano opcję **pełny & różnicowa** jako typ przywracania, wykonaj następujące czynności:

* Wybierz punkt odzyskiwania z listy i wybierz pozycję **OK** , aby wybrać punkt przywracania.

![Przywróć określony punkt odzyskiwania](media/sap-hana-db-restore/specific-recovery-point.png)

* W menu **Przywracanie** wybierz pozycję **Przywróć**, aby uruchomić zadanie przywracania.

![Uruchom zadanie przywracania](media/sap-hana-db-restore/restore-specific.png)

* Śledź postęp przywracania w obszarze **powiadomień** lub śledź go, wybierając pozycję **Przywróć zadania** w menu Baza danych.

![Postęp przywracania](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> W przypadku przywracania wielu baz danych (MDC) po przywróceniu bazy danych systemowych do wystąpienia docelowego jeden musi ponownie uruchomić skrypt przed rejestracją. Kolejne Przywracanie bazy danych dzierżawy zakończy się powodzeniem. Aby dowiedzieć się więcej, zobacz [temat Rozwiązywanie problemów — przywracanie MDC](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore).

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak](sap-hana-db-manage.md) zarządzać bazami danych SAP HANA kopia zapasowa przy użyciu Azure Backup
