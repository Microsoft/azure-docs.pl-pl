---
title: Monitorowanie kopii zapasowych SQL Server na maszynie wirtualnej platformy Azure i zarządzanie nimi
description: W tym artykule opisano sposób monitorowania baz danych SQL Server, które są uruchomione na maszynie wirtualnej platformy Azure, oraz zarządzania nimi.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 3938e26e134f7d823d8a6f6fac631ebf4442e6ab
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519140"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Zarządzanie kopiami zapasowymi baz danych programu SQL Server i ich monitorowanie

W tym artykule opisano typowe zadania związane z zarządzaniem bazami danych usługi SQL Server i ich monitorowaniem, które są uruchomione na maszynie wirtualnej platformy Azure i które są wykonywane w magazynie usługi Azure Backup Recovery Services przez usługę [Azure Backup](backup-overview.md) Service. Dowiesz się, jak monitorować zadania i alerty, zatrzymywać i wznawiać ochronę bazy danych, uruchamiać zadania tworzenia kopii zapasowych i wyrejestrować maszynę wirtualną z kopii zapasowych.

Jeśli nie skonfigurowano jeszcze kopii zapasowych baz danych usługi SQL Server, zobacz Tworzenie kopii zapasowych baz danych SQL Server na platformie [Azure](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs-in-the-portal"></a>Monitorowanie zadań tworzenia kopii zapasowej w portalu

Azure Backup wszystkie zaplanowane i na żądanie operacje  w obszarze Zadania tworzenia kopii zapasowej w portalu, z wyjątkiem zaplanowanych kopii zapasowych dzienników, ponieważ mogą być bardzo częste. Zadania, które widzisz w tym portalu, obejmują odnajdywanie i rejestrację bazy danych, konfigurowanie kopii zapasowych oraz wykonywanie kopii zapasowych i przywracanie.

![Portal zadań tworzenia kopii zapasowej](./media/backup-azure-sql-database/sql-backup-jobs-list.png)

Aby uzyskać szczegółowe informacje na temat scenariuszy monitorowania, przejdź do tematu [Monitorowanie w](backup-azure-monitoring-built-in-monitor.md) Azure Portal i Monitorowanie przy [użyciu Azure Monitor.](backup-azure-monitoring-use-azuremonitor.md)  

## <a name="view-backup-alerts"></a>Wyświetlanie alertów dotyczących kopii zapasowych

Ponieważ kopie zapasowe dzienników są kopie zapasowe co 15 minut, monitorowanie zadań tworzenia kopii zapasowej może być pracochłonne. Azure Backup monitorowania przez wysyłanie alertów e-mail. Alerty e-mail to:

- Wyzwalane w przypadku wszystkich niepowodzeń tworzenia kopii zapasowej.
- Skonsolidowane na poziomie bazy danych według kodu błędu.
- Wysyłane tylko w przypadku niepowodzenia tworzenia pierwszej kopii zapasowej bazy danych.

Aby monitorować alerty kopii zapasowej bazy danych:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Na pulpicie nawigacyjnym magazynu wybierz pozycję **Alerty kopii zapasowej.**

   ![Wybieranie pozycji Alerty kopii zapasowej](./media/backup-azure-sql-database/sql-backup-alerts-list.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Zatrzymywanie ochrony bazy danych programu SQL Server

Możesz zatrzymać kopię zapasową bazy SQL Server na kilka sposobów:

- Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej i usuń wszystkie punkty odzyskiwania.
- Zatrzymaj wszystkie przyszłe zadania tworzenia kopii zapasowej i pozostaw punkty odzyskiwania bez zmian.

Jeśli zdecydujesz się opuścić punkty odzyskiwania, pamiętaj o tych szczegółach:

- Wszystkie punkty odzyskiwania pozostaną nienaruszone na zawsze, a całe przycinanie zostanie zatrzymane po zatrzymaniu ochrony z zachowaniem danych.
- Zostanie naliczona opłata za wystąpienie chronione i zużyty magazyn. Aby uzyskać więcej informacji, [zobacz Azure Backup cennika.](https://azure.microsoft.com/pricing/details/backup/)
- Jeśli usuniesz źródło danych bez zatrzymywania tworzenia kopii zapasowych, tworzenie nowych kopii zapasowych nie powiedzie się. Stare punkty odzyskiwania wygasną zgodnie z zasadami, ale najnowszy punkt odzyskiwania będzie zawsze przechowywany do momentu zatrzymania tworzenia kopii zapasowych i usunięcia danych.

Aby zatrzymać ochronę bazy danych:

1. Na pulpicie nawigacyjnym magazynu wybierz pozycję **Elementy kopii zapasowej.**

2. W **obszarze Typ zarządzania kopiami zapasowymi** wybierz pozycję SQL na maszynie **wirtualnej platformy Azure.**

    ![Wybieranie pozycji SQL na maszynie wirtualnej platformy Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Wybierz bazę danych, dla której chcesz zatrzymać ochronę.

    ![Wybieranie bazy danych w celu zatrzymania ochrony](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. W menu bazy danych wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej.**

    ![Wybieranie pozycji Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-sql-database/stop-db-button.png)

5. W menu **Zatrzymaj tworzenie kopii** zapasowej wybierz, czy dane mają być zachowywane, czy usunięte. Jeśli chcesz, podaj przyczynę i komentarz.

    ![Zachowaj lub usuń dane w menu Zatrzymaj tworzenie kopii zapasowej](./media/backup-azure-sql-database/stop-backup-button.png)

6. Wybierz pozycję **Zatrzymaj tworzenie kopii zapasowej.**

> [!NOTE]
>
>Aby uzyskać więcej informacji na temat opcji usuwania danych, zobacz często zadawane pytania poniżej:
>
>- [Co się stanie z kopiami zapasowymi, jeśli usunę bazę danych z wystąpienia z ochroną automatyczną?](faq-backup-sql-server.yml#if-i-delete-a-database-from-an-autoprotected-instance--what-will-happen-to-the-backups-)
>- [Jeśli zatrzymam wykonywanie kopii zapasowej automatycznie chronionej bazy danych, jakie będzie jej zachowanie?](faq-backup-sql-server.yml#if-i-change-the-name-of-the-database-after-it-has-been-protected--what-will-be-the-behavior-)
>
>

## <a name="resume-protection-for-a-sql-database"></a>Wznawianie ochrony bazy danych SQL

Po zatrzymaniu ochrony bazy danych SQL  w przypadku wybrania opcji Zachowaj dane kopii zapasowej można później wznowić ochronę. Jeśli nie zachowasz danych kopii zapasowej, nie możesz wznowić ochrony.

Aby wznowić ochronę bazy danych SQL:

1. Otwórz element kopii zapasowej i wybierz pozycję **Wznów tworzenie kopii zapasowej.**

    ![Wybieranie pozycji Wznów tworzenie kopii zapasowej w celu wznowienia ochrony bazy danych](./media/backup-azure-sql-database/resume-backup-button.png)

2. W menu **Zasady kopii zapasowych** wybierz zasady, a następnie wybierz pozycję **Zapisz**.

## <a name="run-an-on-demand-backup"></a>Uruchamianie tworzenia kopii zapasowej na żądanie

Możesz uruchamiać różne typy kopii zapasowych na żądanie:

- Pełna kopia zapasowa
- Pełna kopia zapasowa tylko do kopiowania
- Różnicowa kopia zapasowa
- Kopia zapasowa dziennika

Należy określić czas przechowywania pełnej kopii zapasowej tylko do kopiowania, ale zakres przechowywania pełnej kopii zapasowej na żądanie zostanie automatycznie ustawiony na 45 dni od bieżącego czasu.

Aby uzyskać więcej informacji, zobacz [SQL Server kopii zapasowych.](backup-architecture.md#sql-server-backup-types)

## <a name="modify-policy"></a>Modyfikowanie zasad

Zmodyfikuj zasady, aby zmienić częstotliwość tworzenia kopii zapasowych lub zakres przechowywania.

> [!NOTE]
> Wszelkie zmiany w okresie przechowywania zostaną zastosowane retrospektywnie do wszystkich starszych punktów odzyskiwania oprócz nowych.

Na pulpicie nawigacyjnym magazynu przejdź do **tematu Zarządzanie** zasadami tworzenia kopii zapasowych  >   i wybierz zasady, które chcesz edytować.

  ![Zarządzanie zasadami tworzenia kopii zapasowych](./media/backup-azure-sql-database/modify-backup-policy.png)

  ![Modyfikowanie zasad kopii zapasowych](./media/backup-azure-sql-database/modify-backup-policy-impact.png)

Modyfikacja zasad będzie mieć wpływ na wszystkie skojarzone elementy kopii zapasowej i wyzwoli odpowiednie **zadania konfiguracji** ochrony.

### <a name="inconsistent-policy"></a>Niespójne zasady

Czasami operacja modyfikowania zasad może prowadzić do **niespójnej** wersji zasad dla niektórych elementów kopii zapasowej. Dzieje się tak, gdy odpowiednie **zadanie konfigurowania ochrony** nie powiedzie się dla elementu kopii zapasowej po wyzwoleniu operacji modyfikowania zasad. W widoku elementu kopii zapasowej jest on wyświetlany w następujący sposób:

  ![Niespójne zasady](./media/backup-azure-sql-database/inconsistent-policy.png)

Wersję zasad dla wszystkich elementów, których to problem, można naprawić jednym kliknięciem:

  ![Naprawianie niespójnych zasad](./media/backup-azure-sql-database/fix-inconsistent-policy.png)

## <a name="unregister-a-sql-server-instance"></a>Wyrejestrowywanie wystąpienia programu SQL Server

Wyrejestruj SQL Server po wyłączeniu ochrony, ale przed usunięciem magazynu:

1. Na pulpicie nawigacyjnym magazynu w **obszarze Zarządzanie** wybierz pozycję **Infrastruktura kopii zapasowych.**  

   ![Wybieranie pozycji Infrastruktura zapasowa](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. W obszarze **Serwery zarządzania** wybierz pozycję **Serwery chronione**.

   ![Wybieranie pozycji Serwery chronione](./media/backup-azure-sql-database/protected-servers.png)

3. W **chronione serwery**, wybierz serwer, aby wyrejestrować. Aby usunąć magazyn, musisz wyrejestrować wszystkie serwery.

4. Kliknij prawym przyciskiem myszy chroniony serwer i wybierz polecenie **Wyrejestruj**.

   ![Wybieranie pozycji Usuń](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Ponowne rejestrowanie rozszerzenia na maszynie wirtualnej SQL Server wirtualnej

Czasami może to mieć wpływ na rozszerzenie obciążenia maszyny wirtualnej. W takich przypadkach wszystkie operacje wyzwalane na maszynie wirtualnej zaczną się nie powieść. Następnie może być konieczne ponowne zarejestrowanie rozszerzenia na maszynie wirtualnej. Operacja **Zarejestruj ponownie ponownie** instaluje rozszerzenie kopii zapasowej obciążenia na maszynie wirtualnej, aby kontynuować operacje. Tę opcję można znaleźć w obszarze **Infrastruktura kopii zapasowych** w magazynie usługi Recovery Services.

![Serwery chronione w ramach infrastruktury kopii zapasowych](./media/backup-azure-sql-database/protected-servers-backup-infrastructure.png)

Tej opcji należy używać ostrożnie. Wyzwolenie na maszynie wirtualnej z rozszerzeniem o już dobrej kondycji spowoduje ponowne uruchomienie rozszerzenia. Może to spowodować niepowodzenie wszystkich zadań w toku. Przed wyzwoleniem operacji [](backup-sql-server-azure-troubleshoot.md#re-registration-failures) ponownego rejestrowania sprawdź, czy występują objawy.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz Troubleshoot backups on a SQL Server database (Rozwiązywanie problemów [z tworzeniem kopii zapasowych w SQL Server danych).](backup-sql-server-azure-troubleshoot.md)
