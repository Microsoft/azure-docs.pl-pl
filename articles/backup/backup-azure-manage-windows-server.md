---
title: Zarządzanie magazynami i serwerami usługi Azure Recovery Services
description: W tym artykule dowiesz się, jak monitorować magazyny Recovery Services i zarządzać nimi za pomocą pulpitu nawigacyjnego Omówienie magazynu Recovery Services.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 74351d781287d863db8be0fc7d20517e0479106c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002134"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Monitorowanie magazynów usługi Recovery Services i zarządzanie nimi

W tym artykule wyjaśniono, jak za pomocą pulpitu nawigacyjnego **Omówienie** magazynu Recovery Services monitorować Recovery Services i zarządzać nimi. Po otwarciu magazynu Recovery Services z listy zostanie otwarty pulpit nawigacyjny **Przegląd** dla wybranego magazynu. Pulpit nawigacyjny zawiera różne szczegóły dotyczące magazynu. Istnieją *kafelki* , które pokazują: stan alertów krytycznych i ostrzeżeń, zadań w toku i niezakończonych kopii zapasowych oraz ilość lokalnie nadmiarowego magazynu (LRS) i używanej magazyn Geograficznie nadmiarowy (GRS). W przypadku tworzenia kopii zapasowych maszyn wirtualnych platformy Azure w magazynie [kafelek **Stan wstępnego sprawdzania kopii zapasowej** wyświetla wszystkie elementy krytyczne lub ostrzegawcze](#backup-pre-check-status). Poniższy obraz przedstawia pulpit nawigacyjny **omówienia** dla **magazynu contoso**. Kafelek **elementy kopii zapasowej** przedstawia dziewięć elementów zarejestrowanych w magazynie.

![Pulpit nawigacyjny magazynu Recovery Services](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

Wymagania wstępne dotyczące tego artykułu są następujące: subskrypcja platformy Azure, magazyn Recovery Services i istnieje co najmniej jeden element kopii zapasowej skonfigurowany dla magazynu.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Otwórz magazyn Recovery Services

Aby monitorować alerty lub wyświetlać dane zarządzania dotyczące magazynu Recovery Services, otwórz magazyn.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) przy użyciu subskrypcji platformy Azure.

2. W portalu wybierz pozycję **Wszystkie usługi**.

   ![Otwórz listę magazynów Recovery Services krok 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. W oknie dialogowym **wszystkie usługi** wpisz **Recovery Services**. Po rozpoczęciu pisania zawartość listy jest filtrowana w oparciu o wpisywane dane. Gdy zostanie wyświetlona opcja **magazyny Recovery Services** , wybierz ją, aby otworzyć listę magazynów Recovery Services w ramach subskrypcji.

    ![Utwórz magazyn Recovery Services krok 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Z listy magazynów wybierz magazyn, aby otworzyć jego pulpit nawigacyjny **przeglądu** .

    ![Pulpit nawigacyjny magazynu Recovery Services](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Pulpit nawigacyjny przegląd używa kafelków do dostarczania alertów i danych zadań kopii zapasowej.

## <a name="monitor-backup-jobs-and-alerts"></a>Monitorowanie zadań i alertów kopii zapasowej

Pulpit nawigacyjny **Omówienie** magazynu Recovery Services zawiera kafelki dotyczące monitorowania i informacji o użyciu. Kafelki w sekcji monitorowanie zawierają alerty krytyczne i ostrzegawcze oraz zadania w toku i zakończone niepowodzeniem. Wybierz konkretny alert lub zadanie, aby otworzyć menu alerty kopii zapasowej lub zadania tworzenia kopii zapasowej, odfiltrowane dla tego zadania lub alertu.

![Tworzenie kopii zapasowych zadań pulpitu nawigacyjnego](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

Sekcja monitorowanie zawiera wyniki wstępnie zdefiniowanych **alertów kopii zapasowych** i zapytań **zadań tworzenia kopii zapasowych** . Kafelki monitorowania udostępniają aktualne informacje dotyczące:

* Alerty krytyczne i ostrzeżenia dla zadań tworzenia kopii zapasowej (w ciągu ostatnich 24 godzin)
* Sprawdzenie stanu wstępnego dla maszyn wirtualnych platformy Azure. Aby uzyskać pełne informacje na temat stanu przed sprawdzeniem, zobacz [Stan wstępnego sprawdzania kopii zapasowej](#backup-pre-check-status).
* Zadania tworzenia kopii zapasowej w toku i zadania, które zakończyły się niepowodzeniem (w ciągu ostatnich 24 godzin).

Kafelki użycia zapewniają:

* Liczba elementów kopii zapasowych skonfigurowanych dla magazynu.
* Magazyn platformy Azure (oddzielony przez LRS i GRS) używany przez magazyn.

Wybierz kafelki (z wyjątkiem magazynu kopii zapasowych), aby otworzyć menu skojarzone. Na powyższym obrazie kafelek alerty kopii zapasowych zawiera trzy alerty krytyczne. Po wybraniu wiersza alerty krytyczne w kafelku alerty kopii zapasowej program otworzy alerty kopii zapasowej odfiltrowane pod kątem alertów krytycznych.

![Menu alertów kopii zapasowej odfiltrowane pod kątem alertów krytycznych](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

Menu alerty kopii zapasowej na powyższym obrazie jest filtrowane według: stan jest aktywny, ważność ma krytyczne znaczenie, a czas to ostatnie 24 godziny.

### <a name="backup-pre-check-status"></a>Stan wstępnego sprawdzania kopii zapasowej

Wstępne sprawdzanie kopii zapasowej Sprawdź konfigurację maszyn wirtualnych pod kątem problemów, które mogą mieć negatywny wpływ na kopie zapasowe. Te informacje są agregowane, aby można było je wyświetlić bezpośrednio z poziomu pulpitu nawigacyjnego magazynu Recovery Services i zapewnić zalecenia dotyczące środków naprawczych w celu zapewnienia zgodności z kopiami zapasowymi spójnymi na poziomie plików lub aplikacjami. Nie wymagają one infrastruktury i nie mają dodatkowych kosztów.  

Wstępne sprawdzanie kopii zapasowej przebiegu w ramach zaplanowanych operacji tworzenia kopii zapasowej maszyn wirtualnych platformy Azure. Znajdują się one w jednym z następujących stanów:

* **Zakończony powodzeniem**: ten stan wskazuje, że konfiguracja maszyny wirtualnej powinna prowadzić do pomyślnego wykonywania kopii zapasowych i nie trzeba wykonywać żadnych działań naprawczych.
* **Ostrzeżenie**: ten stan wskazuje co najmniej jeden problem w konfiguracji maszyny wirtualnej, który *może* prowadzić do błędów kopii zapasowych. Zapewnia to *zalecane* kroki, aby zapewnić pomyślne tworzenie kopii zapasowych. Na przykład nie ma zainstalowanego najnowszego agenta maszyny wirtualnej może spowodować sporadyczne niepowodzenie wykonywania kopii zapasowych. Ta sytuacja zapewni stan ostrzegawczy.
* **Krytyczny**: ten stan wskazuje co najmniej jeden krytyczny problem w konfiguracji maszyny wirtualnej, który *będzie* prowadzić do błędów kopii zapasowych i zawiera *wymagane* kroki, aby zapewnić pomyślne tworzenie kopii zapasowych. Na przykład problem z siecią spowodowaną aktualizacją reguł sieciowej grupy zabezpieczeń maszyny wirtualnej spowoduje niepowodzenie wykonywania kopii zapasowych, ponieważ uniemożliwia to maszynie wirtualnej komunikowanie się z usługą Azure Backup. Ta sytuacja zapewni stan krytyczny.

Postępuj zgodnie z poniższymi instrukcjami, aby rozpocząć rozwiązywanie wszelkich problemów zgłoszonych przez program Backup wstępne sprawdzanie kopii zapasowych maszyn wirtualnych w magazynie Recovery Services.

* Wybierz kafelek **Stan wstępnego sprawdzania kopii zapasowej (maszyny wirtualne platformy Azure)** na pulpicie nawigacyjnym magazynu Recovery Services.
* Wybierz maszynę wirtualną z stanem wstępnego sprawdzania kopii zapasowej **krytyczne** lub **Ostrzeżenie**. Ta akcja spowoduje otwarcie okienka **Szczegóły maszyny wirtualnej** .
* Wybierz powiadomienie okienka w górnej części okienka, aby wyświetlić opis problemu z konfiguracją i czynności zaradcze.

## <a name="manage-backup-alerts"></a>Zarządzanie alertami kopii zapasowych

Aby uzyskać dostęp do menu alerty kopii zapasowej, w menu magazyn Recovery Services wybierz pozycję **alerty kopii zapasowych**.

![Alerty kopii zapasowej](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

Raport alerty kopii zapasowych zawiera listę alertów dla magazynu.

![Raport o alertach kopii zapasowej](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Alerty

Na liście alerty kopii zapasowej są wyświetlane wybrane informacje dotyczące filtrowanych alertów. W menu alerty kopii zapasowej można odfiltrować alerty krytyczne lub ostrzegawcze.

| Poziom alertu | Zdarzenia, które generują alerty |
| ----------- | ----------- |
| Krytyczne | Alerty krytyczne są wyświetlane, gdy: zadania tworzenia kopii zapasowej kończą się niepowodzeniem, zadania odzyskiwania kończą się niepowodzeniem i po zatrzymaniu ochrony na serwerze, zachowując dane.|
| Ostrzeżenie | Alerty ostrzegawcze są wyświetlane, gdy: zadania tworzenia kopii zapasowej zostały zakończone z ostrzeżeniami. Na przykład w przypadku mniej niż 100 plików kopie zapasowe nie są tworzone z powodu problemów z uszkodzeniem lub w przypadku pomyślnego utworzenia kopii zapasowej więcej niż 1 000 000 plików). |
| Informacyjne | Obecnie żadne alerty informacyjne nie są używane. |

### <a name="viewing-alert-details"></a>Wyświetlanie szczegółów alertu

Raport alerty kopii zapasowej śledzi osiem szczegółowych informacji o każdym alercie. Aby edytować szczegóły w raporcie, użyj przycisku **Wybierz kolumny** .

![Przycisk Wybieranie kolumn dla alertów kopii zapasowych](./media/backup-azure-manage-windows-server/backup-alerts.png)

Domyślnie wszystkie szczegóły, z wyjątkiem **ostatniego wystąpienia czasu**, są wyświetlane w raporcie.

* Alerty
* Element kopii zapasowej
* Serwer chroniony
* Ważność
* Czas trwania
* Czas utworzenia
* Stan
* Ostatni czas wystąpienia

### <a name="change-the-details-in-alerts-report"></a>Zmiana szczegółów w raporcie alertów

1. Aby zmienić informacje raportu, w menu **alerty kopii zapasowej** wybierz pozycję **Wybierz kolumny**.

   ![Wybierz pozycję Wybierz kolumny](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Zostanie otwarte menu **Wybierz kolumny** .

2. W menu **Wybierz kolumny** wybierz Szczegóły, które mają być wyświetlane w raporcie.

    ![Menu wybierz kolumny](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. Wybierz pozycję **gotowe** , aby zapisać zmiany i zamknąć menu wybierz kolumny.

   Jeśli wprowadzisz zmiany, ale nie chcesz zachować zmian, wybierz pozycję **Zresetuj** , aby przywrócić wybraną ostatnią konfigurację.

### <a name="change-the-filter-in-alerts-report"></a>Zmień filtr w raporcie alertów

Użyj menu **Filtr** , aby zmienić ważność, stan, godzinę rozpoczęcia i godzinę zakończenia alertów.

> [!NOTE]
> Edytowanie filtru alertów kopii zapasowych nie powoduje zmiany alertów krytycznych lub ostrzeżeń na pulpicie nawigacyjnym przeglądu magazynu.
>  

1. Aby zmienić filtr alertów kopii zapasowych, w menu alerty kopii zapasowej wybierz opcję **Filtr**.

   ![Wybieranie menu filtru](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Zostanie wyświetlone menu filtr.

   ![Menu alertów filtru](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Edytuj ważność, stan, godzinę rozpoczęcia i godzinę zakończenia, a następnie wybierz pozycję **gotowe** , aby zapisać zmiany.

## <a name="configuring-notifications-for-alerts"></a>Konfigurowanie powiadomień o alertach

Skonfiguruj powiadomienia, aby generować wiadomości e-mail w przypadku wystąpienia alertu ostrzegawczego lub krytycznego. Alerty e-mail można wysyłać co godzinę lub w przypadku wystąpienia określonego alertu.

   ![Filtrowanie alertów](./media/backup-azure-manage-windows-server/configure-notification.png)

Domyślnie powiadomienia E-mail są **włączone**. Wybierz pozycję **wyłączone** , aby zatrzymać powiadomienia e-mail.

W obszarze kontrola **powiadamiania** wybierz opcję **na alert** , jeśli nie chcesz grupować lub nie masz wielu elementów, które mogą generować alerty. Każdy alert powoduje jedno powiadomienie (ustawienie domyślne), a wiadomość e-mail dotycząca rozwiązania jest wysyłana natychmiast.

W przypadku wybrania opcji **Podsumowanie godzinowe** zostanie wysłana wiadomość e-mail z informacją o nierozwiązanych alertach wygenerowanych w ciągu ostatniej godziny. Wiadomość e-mail dotycząca rozwiązania jest wysyłana po upływie godziny.

Wybierz ważność alertu (krytyczne lub ostrzeżenie) służącą do generowania poczty e-mail. Obecnie nie ma żadnych alertów dotyczących informacji.

## <a name="manage-backup-items"></a>Zarządzanie elementami kopii zapasowej

Magazyn Recovery Services zawiera wiele typów danych kopii zapasowej. [Dowiedz się więcej](backup-overview.md#what-can-i-back-up) na temat tego, co można utworzyć kopii zapasowej. Aby zarządzać różnymi serwerami, komputerami, bazami danych i obciążeniami, wybierz kafelek **elementy kopii zapasowej** , aby wyświetlić zawartość magazynu.

![Kafelek elementy kopii zapasowej](./media/backup-azure-manage-windows-server/backup-items.png)

Zostanie wyświetlona lista elementów kopii zapasowej uporządkowanych według typu zarządzania kopiami zapasowymi.

![Lista elementów kopii zapasowej](./media/backup-azure-manage-windows-server/list-backup-items.png)

Aby poznać określony typ chronionego wystąpienia, wybierz element w kolumnie Typ zarządzania kopiami zapasowymi. Na przykład na powyższym obrazie istnieją dwie maszyny wirtualne platformy Azure chronione w tym magazynie. W przypadku wybrania **maszyny wirtualnej platformy Azure** program otwiera listę chronionych maszyn wirtualnych w tym magazynie.

![Lista chronionych maszyn wirtualnych](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

Lista maszyn wirtualnych ma przydatne dane: Grupa zasobów skojarzonych, poprzednie [Sprawdzanie kopii zapasowej](#backup-pre-check-status), stan ostatniej kopii zapasowej i Data ostatniego punktu przywracania. Wielokropek, w ostatniej kolumnie, otwiera menu, aby wyzwolić typowe zadania. Przydatne dane podane w kolumnach różnią się w zależności od typu kopii zapasowej.

![Otwórz menu wielokropka dla typowych zadań](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Zarządzanie zadaniami tworzenia kopii zapasowych

Kafelek **zadania tworzenia kopii zapasowej** na pulpicie nawigacyjnym magazynu pokazuje liczbę zadań, które są w toku lub zakończyły się niepowodzeniem w ciągu ostatnich 24 godzin. Kafelek zawiera możliwość wypróbowania innowacyjnego w menu zadania tworzenia kopii zapasowej.

![Kafelek zadań wstecznych](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Aby wyświetlić dodatkowe szczegóły dotyczące zadań, wybierz opcję **w toku** lub **nie można** otworzyć menu zadania tworzenia kopii zapasowej odfiltrowanego dla tego stanu.

### <a name="backup-jobs-menu"></a>Menu zadania tworzenia kopii zapasowej

W menu **zadania tworzenia kopii zapasowej** są wyświetlane informacje o typie elementu, operacji, stanie, czasie rozpoczęcia i czasie trwania.  

Aby otworzyć menu zadania tworzenia kopii zapasowej, w menu głównym magazynu wybierz pozycję **zadania tworzenia kopii zapasowej**.

![Wybierz zadania tworzenia kopii zapasowej](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Zostanie wyświetlona lista zadań tworzenia kopii zapasowej.

![Lista zadań tworzenia kopii zapasowej](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

Menu zadania tworzenia kopii zapasowej pokazuje stan wszystkich operacji dla wszystkich typów kopii zapasowych w ciągu ostatnich 24 godzin. Użyj **filtru** , aby zmienić filtry. Filtry zostały wyjaśnione w poniższych sekcjach.

Aby zmienić filtry:

1. W menu zadania tworzenia kopii zapasowej magazynu wybierz opcję **Filtr**.

   ![Wybieranie filtru dla zadań tworzenia kopii zapasowej](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Zostanie otwarte menu filtr.

   ![Menu filtr otwiera zadania tworzenia kopii zapasowej](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Wybierz ustawienia filtru i wybierz pozycję **gotowe**. Lista filtrowanych odświeża się na podstawie nowych ustawień.

#### <a name="item-type"></a>Typ elementu

Typ elementu to typ zarządzania kopiami zapasowymi chronionego wystąpienia. Istnieją cztery typy: Zapoznaj się z poniższą listą. Można wyświetlić wszystkie typy elementów lub jeden typ elementu. Nie można wybrać dwóch lub trzech typów elementów. Dostępne typy elementów to:

* Wszystkie typy elementów
* Maszyna wirtualna platformy Azure
* Pliki i foldery
* Azure Storage
* Obciążenie platformy Azure

#### <a name="operation"></a>Operacja

Można wyświetlić jedną operację lub wszystkie operacje. Nie można wybrać dwóch lub trzech operacji. Dostępne są następujące operacje:

* Wszystkie operacje
* Zarejestruj
* Konfigurowanie kopii zapasowych
* Backup
* Przywracanie
* Wyłączanie kopii zapasowej
* Usuwanie danych kopii zapasowej

#### <a name="status"></a>Stan

Możesz wyświetlić wszystkie stany lub jeden z nich. Nie można wybrać dwóch lub trzech stanów. Dostępne są następujące stany:

* Wszystkie Stany
* Ukończone
* W toku
* Niepowodzenie
* Anulowane
* Ukończono z ostrzeżeniami

#### <a name="start-time"></a>Godzina rozpoczęcia

Dzień i godzina rozpoczęcia zapytania. Wartość domyślna to 24-godzinny okres.

#### <a name="end-time"></a>Godzina zakończenia

Dzień i godzina zakończenia zapytania.

### <a name="export-jobs"></a>Eksportuj zadania

Użyj **zadania eksportu** , aby utworzyć arkusz kalkulacyjny zawierający wszystkie informacje o menu zadania. Arkusz kalkulacyjny ma jeden arkusz zawierający podsumowanie wszystkich zadań i poszczególnych arkuszy dla każdego zadania.

Aby wyeksportować informacje o zadaniach do arkusza kalkulacyjnego, wybierz pozycję **Eksportuj zadania**. Usługa tworzy arkusz kalkulacyjny przy użyciu nazwy magazynu i daty, ale można zmienić nazwę.

## <a name="monitor-backup-usage"></a>Monitorowanie użycia kopii zapasowej

Kafelek magazyn kopii zapasowych na pulpicie nawigacyjnym pokazuje Magazyn używany na platformie Azure. Użycie magazynu jest dostępne dla:

* Użycie magazynu LRS w chmurze skojarzone z magazynem
* Użycie magazynu GRS w chmurze skojarzone z magazynem

## <a name="troubleshooting-monitoring-issues"></a>Rozwiązywanie problemów z monitorowaniem

**Problem:** Zadania i/lub alerty z agenta Azure Backup nie są wyświetlane w portalu.

**Kroki rozwiązywania problemów:** Proces, ```OBRecoveryServicesManagementAgent``` , wysyła dane zadania i alertu do usługi Azure Backup. Czasami ten proces może zostać zablokowany lub zamknięty.

1. Aby sprawdzić, czy proces nie działa, Otwórz **Menedżera zadań** i sprawdź, czy ```OBRecoveryServicesManagementAgent``` jest uruchomiony.

2. Jeśli proces nie jest uruchomiony, Otwórz **Panel sterowania** i Przeglądaj listę usług. Uruchom lub Uruchom ponownie **Microsoft Azure Recovery Services agenta zarządzania**.

    Aby uzyskać więcej informacji, Przejrzyj dzienniki pod adresem:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Na przykład:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Następne kroki

* [Przywracanie systemu Windows Server lub klienta systemu Windows z platformy Azure](backup-azure-restore-windows-server.md)
* Aby dowiedzieć się więcej na temat Azure Backup, zobacz [omówienie Azure Backup](./backup-overview.md)
