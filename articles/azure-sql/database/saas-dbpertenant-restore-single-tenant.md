---
title: Przywracanie bazy danych w wielodostępnej aplikacji SaaS
description: Dowiedz się, jak przywrócić Azure SQL Database pojedynczej dzierżawy po przypadkowej usunięciu danych
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 88496a39b0186cefb7c64e227530b5d73e693094
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780482"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Przywracanie pojedynczej dzierżawy za pomocą aplikacji SaaS dla dzierżawy
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Model dla dzierżawy bazy danych ułatwia przywrócenie pojedynczej dzierżawy do wcześniejszego punktu w czasie bez wpływu na innych dzierżawców.

W tym samouczku przedstawiono dwa wzorce odzyskiwania danych:

> [!div class="checklist"]
> * Przywracanie bazy danych do równoległej bazy danych (obok siebie).
> * Przywracanie bazy danych w miejscu, zastępując istniejącą bazę danych.

| Wzorce | Opis |
|:--|:--|
| Przywracanie do równoległej bazy danych | Ten wzorzec może służyć do wykonywania zadań, takich jak przegląd, Inspekcja i zgodność, aby umożliwić dzierżawcom inspekcję danych z wcześniejszego punktu. Bieżąca baza danych dzierżawy pozostaje w trybie online i nie ulega zmianie. |
| Przywracanie w miejscu | Ten wzorzec jest zazwyczaj używany do odzyskiwania dzierżawy do wcześniejszego punktu, po Przypadkowe usunięcie lub uszkodzenie danych przez dzierżawcę. Oryginalna baza danych jest wyłączona i zastąpiona przywróconą bazą danych. |
|||

Do wykonania zadań opisanych w tym samouczku niezbędne jest spełnienie następujących wymagań wstępnych:

* Wdrożono aplikację Wingtip SaaS. Aby wdrożyć program w mniej niż pięć minut, zobacz [wdrażanie i eksplorowanie aplikacji Wingtip SaaS](./saas-dbpertenant-get-started-deploy.md).
* Zainstalowany jest program Azure PowerShell. Aby uzyskać szczegółowe informacje, zobacz [wprowadzenie do Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Wprowadzenie do wzorców przywracania dzierżawy SaaS

Istnieją dwa proste wzorce przywracania danych poszczególnych dzierżawców. Ponieważ bazy danych dzierżawy są od siebie odizolowane, przywrócenie jednej dzierżawy nie ma wpływu na dane innych dzierżawców. Funkcja Azure SQL Database w czasie przywracania (kopie) jest używana w obu wzorcach. KOPIE zawsze tworzy nową bazę danych.

* **Przywracanie równolegle**: w pierwszym wzorcu tworzona jest nowa równoległa baza danych wraz z bieżącą bazą danych dzierżawy. Dzierżawa otrzymuje dostęp tylko do odczytu do przywróconej bazy danych. Przywrócone dane mogą być przeglądane i potencjalnie używane do zastępowania bieżących wartości danych. Do projektanta aplikacji można określić, jak dzierżawa uzyskuje dostęp do przywróconej bazy danych i jakie opcje odzyskiwania są udostępniane. Po prostu umożliwienie dzierżawcom przeglądania ich danych w wcześniejszym punkcie może być wszystko, co jest wymagane w niektórych scenariuszach.

* **Przywracanie w miejscu**: Drugi wzorzec jest przydatny, jeśli dane zostały utracone lub uszkodzone, a dzierżawca chce powrócić do wcześniejszego punktu. Dzierżawa jest wyłączona podczas przywracania bazy danych. Oryginalna baza danych zostanie usunięta, a nazwa przywróconej bazy danych zostanie zmieniona. Łańcuch kopii zapasowych oryginalnej bazy danych pozostanie dostępny po usunięciu, aby można było przywrócić bazę danych do wcześniejszego punktu w czasie, w razie potrzeby.

Jeśli baza danych używa [aktywnej replikacji geograficznej](active-geo-replication-overview.md) i przywraca równolegle, zalecamy skopiowanie wszelkich wymaganych danych z przywróconej kopii do oryginalnej bazy danych. Jeśli pierwotna baza danych zostanie zastąpiona przywróconą bazą danych, należy ponownie skonfigurować i zsynchronizować replikację geograficzną.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Pobierz bilety Wingtip SaaS dla skryptów aplikacji dla dzierżawców

Wingtip bilety SaaS wielodostępne skrypty bazy danych i kod źródłowy aplikacji są dostępne w repozytorium GitHub [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Aby uzyskać instrukcje dotyczące pobierania i odblokowywania Wingtip biletów SaaS, zobacz [Ogólne wskazówki](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Po utworzeniu bazy danych może upłynąć od 10 do 15 minut, zanim pierwsza pełna kopia zapasowa będzie dostępna do przywrócenia z programu. Jeśli aplikacja została zainstalowana ponownie, może być konieczne poczekanie przez kilka minut przed podjęciem tego scenariusza.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Symuluj przypadkowe usuwanie danych dzierżawy

Aby zademonstrować te scenariusze odzyskiwania, najpierw "przypadkowo" usunąć zdarzenie w jednej z baz danych dzierżaw. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Otwórz aplikację Events, aby przejrzeć bieżące zdarzenia

1. Otwórz centrum zdarzeń ( http://events.wtp.&lt ; User &gt; . trafficmanager.NET) i wybierz pozycję **contoso — korytarz**.

   ![Centrum zdarzeń](./media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Przewiń listę zdarzeń i zanotuj ostatnie zdarzenie na liście.

   ![Ostatnie zdarzenie pojawia się](./media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Przypadkowe" Usuwanie ostatniego zdarzenia

1. W ISE programu PowerShell Otwórz pozycję... \\ Moduły uczenia \\ ciągłość biznesowa i odzyskiwanie po awarii \\ RestoreTenant \\ *Demo-RestoreTenant.ps1* i ustawiają następującą wartość:

   * **$DemoScenario**  =  **1**, *Usuń ostatnie zdarzenie (bez sprzedaży biletów)*.
2. Naciśnij klawisz F5, aby uruchomić skrypt i usunąć ostatnie zdarzenie. Zostanie wyświetlony następujący komunikat z potwierdzeniem:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Zostanie otwarta strona contoso Events. Przewiń w dół i sprawdź, czy zdarzenie zostało utracone. Jeśli wydarzenie nadal znajduje się na liście, wybierz pozycję **Odśwież** i sprawdź, czy nie zostało ono usunięte.
   ![Ostatnie usunięcie zdarzenia](./media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Przywracanie bazy danych dzierżawy równolegle z produkcyjną bazą danych

To ćwiczenie przywraca bazę danych korytarza firmy Contoso do punktu w czasie przed usunięciem zdarzenia. W tym scenariuszu przyjęto założenie, że chcesz przejrzeć usunięte dane w równoległej bazie danych.

 Skrypt *Restore-TenantInParallel.ps1* tworzy równoległą bazę danych dzierżawy o nazwie *ContosoConcertHall \_ Old* z wpisem katalogu równoległego. Ten wzorzec przywracania najlepiej nadaje się do odzyskiwania po niewielkiej utracie danych. Tego wzorca można także użyć, jeśli trzeba przejrzeć dane pod kątem zgodności lub inspekcji. Jest to zalecane podejście w przypadku korzystania [z aktywnej replikacji geograficznej](active-geo-replication-overview.md).

1. Ukończ sekcję [symulowanie przypadkowego usuwania danych w dzierżawie](#simulate-a-tenant-accidentally-deleting-data) .
2. W ISE programu PowerShell Otwórz pozycję... \\ Moduły uczenia \\ ciągłość biznesowa i odzyskiwanie po awarii \\ RestoreTenant \\ _Demo-RestoreTenant.ps1_.
3. Ustaw **$DemoScenario**  =  **2**, *Przywróć dzierżawę równolegle*.
4. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt przywraca bazę danych dzierżawy do punktu w czasie przed usunięciem zdarzenia. Baza danych zostanie przywrócona do nowej bazy danych o nazwie _ContosoConcertHall \_ Old_. Metadane wykazu, które istnieją w tej przywróconej bazie danych, zostaną usunięte, a następnie baza danych zostanie dodana do wykazu przy użyciu klucza złożonego *z \_ starej nazwy ContosoConcertHall* .

Skrypt demonstracyjny otwiera stronę zdarzeń dla tej nowej bazy danych dzierżawy w przeglądarce. Zwróć uwagę na adres URL, na którym na ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` tej stronie są wyświetlane dane z przywróconej bazy danych, w której *_OLD* zostanie dodany do nazwy.

Przewiń zdarzenia wymienione w przeglądarce, aby upewnić się, że zdarzenie usunięte z poprzedniej sekcji zostało przywrócone.

Udostępnienie przywróconej dzierżawy jako dodatkowej dzierżawy z własną aplikacją zdarzeń jest mało prawdopodobne, aby zapewnić dostęp dzierżawy do przywróconych danych. Służy do zilustrowania wzorca przywracania. Zazwyczaj użytkownik otrzymuje dostęp tylko do odczytu do starych danych i zachowuje przywróconą bazę danych przez zdefiniowany czas. W przykładzie można usunąć przywrócony wpis dzierżawy po zakończeniu, uruchamiając scenariusz _Usuń przywróconą dzierżawę_ .

1. Ustaw **$DemoScenario**  =  **4**, *Usuń przywróconą dzierżawę*.
2. Aby uruchomić skrypt, naciśnij klawisz F5.
3. *\_ Stary wpis ContosoConcertHall* został usunięty z wykazu. Zamknij stronę zdarzeń dla tej dzierżawy w przeglądarce.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Przywracanie dzierżawy w miejscu, zastępowanie istniejącej bazy danych dzierżawy

W tym ćwiczeniu firma Contoso uzgadnia dzierżawcę z punktem przed usunięciem zdarzenia. Skrypt *Restore-TenantInPlace* przywraca bazę danych dzierżawy do nowej bazy danych i usuwa oryginalną. Ten wzorzec przywracania najlepiej nadaje się do odzyskiwania po poważnych uszkodzeniach danych, a Dzierżawca może wymagać znacznej utraty danych.

1. W ISE programu PowerShell Otwórz plik **Demo-RestoreTenant.ps1** .
2. Ustaw **$DemoScenario**  =  **5** i *Przywróć dzierżawę*.
3. Aby uruchomić skrypt, naciśnij klawisz F5.

Skrypt przywraca bazę danych dzierżawy do punktu przed usunięciem zdarzenia. Najpierw przyjmujemy, że dzierżawa korytarza contoso jest wyłączona, aby uniemożliwić dalsze aktualizacje. Następnie równoległa baza danych jest tworzona przez przywrócenie z punktu przywracania. Przywrócona baza danych ma nazwę z sygnaturą czasową, aby upewnić się, że nazwa bazy danych nie powoduje konfliktu z nazwą istniejącej bazy danych dzierżawy. Następnie zostanie usunięta stara baza danych dzierżawy, a przywrócona baza danych zostanie zmieniona na oryginalną nazwę bazy danych. Poza tym korytarz z firmy Contoso jest przełączany w tryb online, aby umożliwić aplikacji dostęp do przywróconej bazy danych.

Baza danych została pomyślnie przywrócona do punktu w czasie przed usunięciem zdarzenia. Gdy zostanie otwarta strona **zdarzenia** , potwierdź, że ostatnie zdarzenie zostało przywrócone.

Po przywróceniu bazy danych potrwa ona od 10 do 15 minut, zanim pierwsza pełna kopia zapasowa będzie dostępna do przywrócenia z powrotem.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przywracanie bazy danych do równoległej bazy danych (obok siebie).
> * Przywracanie bazy danych w miejscu.

Wypróbuj samouczek dotyczący [zarządzania schematem bazy danych dzierżawy](saas-tenancy-schema-management.md) .

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dodatkowe samouczki, które kompilują w aplikacji Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Omówienie zagadnień dotyczących ciągłości działalności biznesowej zapewnianej przez usługę Azure SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Informacje o SQL Database kopiach zapasowych](automated-backups-overview.md)