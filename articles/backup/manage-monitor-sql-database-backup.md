---
title: Zarządzanie i monitorowanie obiektów DBs serwera SQL na maszynie Wirtualnej platformy Azure
description: W tym artykule opisano sposób zarządzania i monitorowania baz danych programu SQL Server, które są uruchomione na maszynie Wirtualnej platformy Azure.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 14e3a4797fe60a3d1857f1e6d947fa0c669bdcfe
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537308"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Zarządzanie kopiami zapasowymi baz danych programu SQL Server i ich monitorowanie

W tym artykule opisano typowe zadania dotyczące zarządzania i monitorowania baz danych programu SQL Server, które są uruchomione na maszynie wirtualnej platformy Azure (VM) i które są archiwizowane w magazynie usług Azure Backup Recovery Services przez usługę [Azure Backup.](backup-overview.md) Dowiesz się, jak monitorować zadania i alerty, zatrzymywać i wznawiać ochronę baz danych, uruchamiać zadania tworzenia kopii zapasowych i wyrejestrować maszynę wirtualną z kopii zapasowych.

Jeśli nie skonfigurowano jeszcze kopii zapasowych baz danych programu SQL Server, zobacz [Tworzenie kopii zapasowych baz danych programu SQL Server na maszynach wirtualnych platformy Azure](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Monitorowanie ręcznych zadań tworzenia kopii zapasowych w portalu

Usługa Kopia zapasowa platformy Azure pokazuje wszystkie ręcznie wyzwalane zadania w portalu **zadań kopii zapasowej.** Zadania widoczne w tym portalu obejmują odnajdowanie i rejestrowanie baz danych oraz operacje tworzenia kopii zapasowych i przywracania.

![Portal zadań tworzenia kopii zapasowych](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Portal **zadań kopii zapasowej** nie pokazuje zaplanowanych zadań tworzenia kopii zapasowych. Do monitorowania zaplanowanych zadań kopii zapasowych należy użyć programu SQL Server Management Studio zgodnie z opisem w następnej sekcji.
>

Aby uzyskać szczegółowe informacje na temat monitorowania scenariuszy, przejdź do [monitorowania w witrynie Azure portal](backup-azure-monitoring-built-in-monitor.md) i monitorowania przy użyciu usługi Azure [Monitor.](backup-azure-monitoring-use-azuremonitor.md)  

## <a name="view-backup-alerts"></a>Wyświetlanie alertów dotyczących kopii zapasowych

Ponieważ kopie zapasowe dziennika występują co 15 minut, monitorowanie zadań tworzenia kopii zapasowych może być uciążliwe. Usługa Azure Backup ułatwia monitorowanie, wysyłając alerty e-mail. Alerty e-mail to:

- Wyzwalane dla wszystkich błędów kopii zapasowej.
- Skonsolidowane na poziomie bazy danych przez kod błędu.
- Wysyłane tylko w przypadku pierwszego błędu kopii zapasowej bazy danych.

Aby monitorować alerty kopii zapasowej bazy danych:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Na pulpicie nawigacyjnym przechowalni wybierz pozycję **Alerty i zdarzenia**.

   ![Wybieranie pozycji Alerty i zdarzenia](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. W **obszarze Alerty i zdarzenia**wybierz pozycję **Alerty kopii zapasowych**.

   ![Wybieranie pozycji Alerty kopii zapasowej](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Zatrzymywanie ochrony bazy danych programu SQL Server

Można zatrzymać tworzenie kopii zapasowej bazy danych programu SQL Server na kilka sposobów:

- Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowych i usuń wszystkie punkty odzyskiwania.
- Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowych i pozostaw punkty odzyskiwania bez zmian.

Jeśli zdecydujesz się pozostawić punkty odzyskiwania, pamiętaj o tych szczegółach:

- Wszystkie punkty odzyskiwania pozostaną nienaruszone na zawsze, wszystkie przycinanie zatrzymuje się na ochronie przystanku z zachowaniem danych.
- Zostanie naliczona opłata za chronione wystąpienie i zużyte miejsce do magazynowania. Aby uzyskać więcej informacji, zobacz [Ceny usługi Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
- Jeśli usuniesz źródło danych bez zatrzymywania kopii zapasowych, nowe kopie zapasowe nie powiodą się. Stare punkty odzyskiwania wygasną zgodnie z zasadami, ale jeden ostatni punkt odzyskiwania będzie zawsze przechowywany, dopóki nie zatrzymasz kopii zapasowych i nie usuniesz danych.

Aby zatrzymać ochronę bazy danych:

1. Na pulpicie nawigacyjnym przechowalni wybierz pozycję **Zapas elementów**.

2. W obszarze **Typ zarządzania kopiami zapasowymi**wybierz pozycję **SQL w usłudze Azure VM**.

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Wybierz bazę danych, dla której chcesz zatrzymać ochronę.

    ![Wybieranie bazy danych w celu zatrzymania ochrony](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. W menu bazy danych wybierz polecenie **Zatrzymaj tworzenie kopii zapasowej**.

    ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-sql-database/stop-db-button.png)

5. W menu **Zatrzymaj kopię zapasową** wybierz, czy dane mają być zachowywane, czy usuwane. Jeśli chcesz, podaj powód i komentarz.

    ![Zachowywanie lub usuwanie danych z menu Zatrzymaj kopię zapasową](./media/backup-azure-sql-database/stop-backup-button.png)

6. Wybierz **pozycję Zatrzymaj tworzenie kopii zapasowej**.

> [!NOTE]
>
>Aby uzyskać więcej informacji na temat opcji usuwania danych, zobacz często zadawane pytania poniżej:
>
>- [Jeśli usunę bazę danych z wystąpienia chronionego automatycznie, co się stanie z kopiami zapasowymi?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
>- [Jeśli zatrzymam wykonywanie kopii zapasowych automatycznie chronionej bazy danych, jakie będzie jej zachowanie?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Wznawianie ochrony bazy danych SQL

Po zatrzymaniu ochrony bazy danych SQL, jeśli wybierzesz opcję **Zachowaj dane kopii zapasowej,** można później wznowić ochronę. Jeśli nie zachowasz danych kopii zapasowej, nie możesz wznowić ochrony.

Aby wznowić ochronę bazy danych SQL:

1. Otwórz element kopii zapasowej i wybierz pozycję **Wznów kopię zapasową**.

    ![Wybieranie pozycji Wznów tworzenie kopii zapasowej w celu wznowienia ochrony bazy danych](./media/backup-azure-sql-database/resume-backup-button.png)

2. W menu **Zasady kopii zapasowych** wybierz zasady, a następnie wybierz pozycję **Zapisz**.

## <a name="run-an-on-demand-backup"></a>Uruchamianie kopii zapasowej na żądanie

Można uruchamiać różne typy kopii zapasowych na żądanie:

- Pełna kopia zapasowa
- Pełna kopia zapasowa tylko do kopiowania
- Różnicowa kopia zapasowa
- Kopia zapasowa dziennika

Chociaż należy określić czas przechowywania dla pełnej kopii zapasowej tylko do kopiowania, zakres przechowywania pełnej kopii zapasowej na żądanie zostanie automatycznie ustawiony na 45 dni od bieżącego czasu.

Aby uzyskać więcej informacji, zobacz [TYPY kopii zapasowych programu SQL Server](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Wyrejestrowywanie wystąpienia programu SQL Server

Wyrejestruj wystąpienie programu SQL Server po wyłączeniu ochrony, ale przed usunięciem przechowalni:

1. Na pulpicie nawigacyjnym przechowalni w obszarze **Zarządzaj**wybierz pozycję **Infrastruktura kopii zapasowych**.  

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. W obszarze **Serwery zarządzania** wybierz pozycję **Serwery chronione**.

   ![Wybieranie pozycji Serwery chronione](./media/backup-azure-sql-database/protected-servers.png)

3. W **obszarze Serwery chronione**wybierz serwer do wyrejestrowania. Aby usunąć magazyn, musisz wyrejestrować wszystkie serwery.

4. Kliknij prawym przyciskiem myszy chroniony serwer, a następnie wybierz polecenie **Wyrejestruj**.

   ![Wybieranie pozycji Usuń](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="modify-policy"></a>Modyfikowanie zasad

Modyfikuj zasady, aby zmienić częstotliwość tworzenia kopii zapasowych lub zakres przechowywania.

> [!NOTE]
> Wszelkie zmiany w okresie przechowywania będą stosowane retrospektywnie do wszystkich starszych punktów odzyskiwania oprócz nowych.

Na pulpicie nawigacyjnym przechowalni przejdź do **strony Zarządzanie zasadami** > **kopii zapasowych** i wybierz zasady, które chcesz edytować.

  ![Zarządzanie zasadami tworzenia kopii zapasowych](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Modyfikowanie zasad tworzenia kopii zapasowych](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Modyfikacja zasad wpłynie na wszystkie skojarzone elementy kopii zapasowej i wyzwoli odpowiednie zadania **ochrony konfiguracji.**

### <a name="inconsistent-policy"></a>Niespójne zasady

Czasami operacja modyfikowania zasad może prowadzić do **niespójnej** wersji zasad dla niektórych elementów kopii zapasowej. Dzieje się tak, gdy odpowiednie zadanie **ochrony konfiguracji** kończy się niepowodzeniem dla elementu kopii zapasowej po wyzwoleniu operacji modyfikowania zasad. W widoku elementu kopii zapasowej jest wyświetlany następujący sposób:

  ![Niespójne zasady](./media/backup-azure-sql-database/inconsistent-policy.png)

Wersję zasad dla wszystkich elementów, których dotyczy problem, można naprawić za pomocą jednego kliknięcia:

  ![Naprawianie niespójnych zasad](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Ponowne rejestrowanie rozszerzenia na maszynie Wirtualnej programu SQL Server

Czasami rozszerzenie obciążenia na maszynie Wirtualnej może uzyskać wpływ z jednego lub drugiego powodu. W takich przypadkach wszystkie operacje wyzwalane na maszynie Wirtualnej rozpocznie się niepowodzeniem. Następnie może być konieczne ponowne zarejestrowanie rozszerzenia na maszynie Wirtualnej. **Ponowne rejestrowanie** operacji ponownie instaluje rozszerzenie kopii zapasowej obciążenia na maszynie Wirtualnej, aby operacje były kontynuowane.

Użyj tej opcji z ostrożnością; po wyzwoleniu na maszynie wirtualnej z już zdrowym rozszerzeniem, ta operacja spowoduje ponowne uruchomienie rozszerzenia. Może to spowodować, że wszystkie zadania w toku zakończy się niepowodzeniem. Prosimy sprawdzić jeden lub więcej [objawów](backup-sql-server-azure-troubleshoot.md#re-registration-failures) przed uruchomieniem operacji ponownego rejestrowania.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z tworzeniem kopii zapasowych w bazie danych programu SQL Server](backup-sql-server-azure-troubleshoot.md).
