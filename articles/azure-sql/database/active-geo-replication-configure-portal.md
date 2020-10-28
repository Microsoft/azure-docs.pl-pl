---
title: 'Samouczek: replikacja geograficzna & trybu failover w portalu'
description: Skonfiguruj replikację geograficzną dla bazy danych przy użyciu Azure Portal i zainicjuj pracę w trybie failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: 71c73fec4f559b34b097556243617636acd77480
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673273"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-in-the-azure-portal-azure-sql-database"></a>Samouczek: Konfigurowanie aktywnej replikacji geograficznej i trybu failover w Azure Portal (Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

W tym artykule opisano sposób konfigurowania [aktywnej replikacji geograficznej Azure SQL Database](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities) przy użyciu [Azure Portal](https://portal.azure.com) i inicjowania trybu failover.

Najlepsze rozwiązania dotyczące korzystania z grup autotrybu failover można znaleźć [w temacie najlepsze rozwiązania dotyczące Azure SQL Database](auto-failover-group-overview.md#best-practices-for-sql-database) i [najlepszych rozwiązań dotyczących wystąpienia zarządzanego usługi Azure SQL](auto-failover-group-overview.md#best-practices-for-sql-managed-instance). 



## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować aktywną replikację geograficzną przy użyciu Azure Portal, wymagany jest następujący zasób:

* Baza danych w Azure SQL Database: podstawowa baza danych, która ma zostać zreplikowana do innego regionu geograficznego.

> [!Note]
> W przypadku korzystania z Azure Portal można utworzyć pomocniczą bazę danych tylko w ramach tej samej subskrypcji, co podstawowa. Jeśli pomocnicza baza danych musi znajdować się w innej subskrypcji, użyj interfejsu [API REST usługi CREATE DATABASE](/rest/api/sql/databases/createorupdate) lub [polecenia ALTER DATABASE Transact-SQL API](/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Dodawanie pomocniczej bazy danych

Poniższe kroki tworzą nową pomocniczą bazę danych w ramach powiązania replikacji geograficznej.  

Aby dodać pomocniczą bazę danych, musisz być właścicielem subskrypcji lub współwłaścicielem.

Pomocnicza baza danych ma taką samą nazwę jak podstawowa baza danych i ma domyślnie tę samą warstwę usług i rozmiar obliczeniowy. Pomocnicza baza danych może być pojedynczą bazą danych lub bazą danych w puli. Aby uzyskać więcej informacji, zobacz [model zakupów oparty na](service-tiers-dtu.md) jednostkach DTU i [model zakupu oparty na rdzeń wirtualny](service-tiers-vcore.md).
Po utworzeniu i umieszczeniu w nim pliku podrzędnego dane rozpoczynają replikację z podstawowej bazy danych do nowej pomocniczej bazy danych.

> [!NOTE]
> Jeśli baza danych partnera już istnieje (na przykład w wyniku zakończenia poprzedniej relacji z replikacją geograficzną), polecenie nie powiedzie się.

1. W [Azure Portal](https://portal.azure.com)przejdź do bazy danych, która ma zostać skonfigurowana na potrzeby replikacji geograficznej.
2. Na stronie SQL Database wybierz opcję **replikacja geograficzna** , a następnie wybierz region, aby utworzyć pomocniczą bazę danych. Można wybrać dowolny region inny niż region hostujący podstawową bazę danych, ale zalecamy [sparowany region](../../best-practices-availability-paired-regions.md).

    ![Konfigurowanie replikacji geograficznej](./media/active-geo-replication-configure-portal/configure-geo-replication.png)
3. Wybierz lub skonfiguruj serwer i warstwę cenową pomocniczej bazy danych.

    ![Utwórz formularz pomocniczy](./media/active-geo-replication-configure-portal/create-secondary.png)
4. Opcjonalnie można dodać pomocniczą bazę danych do puli elastycznej. Aby utworzyć pomocniczą bazę danych w puli, kliknij pozycję **Pula elastyczna** i wybierz pulę na serwerze docelowym. Pula musi już istnieć na serwerze docelowym. Ten przepływ pracy nie tworzy puli.
5. Kliknij przycisk **Utwórz** , aby dodać pomocniczą.
6. Zostanie utworzona pomocnicza baza danych i rozpocznie się proces umieszczania.

    ![Mapa serwerów pomocniczych](./media/active-geo-replication-configure-portal/seeding0.png)
7. Po zakończeniu procesu umieszczania w pomocniczej bazie danych zostanie wyświetlony stan.

    ![Ukończono rozmieszczanie](./media/active-geo-replication-configure-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Inicjowanie trybu failover

Pomocnicza baza danych może zostać przełączona, aby stała się podstawowym.  

1. W [Azure Portal](https://portal.azure.com)przejdź do podstawowej bazy danych w ramach powiązania replikacji geograficznej.
2. W bloku SQL Database wybierz pozycję **wszystkie ustawienia**  >  **replikacja geograficzna** .
3. Na liście **serwery pomocnicze** wybierz bazę danych, która ma stać się nowym podstawowym, a następnie kliknij pozycję **wymuszone przełączanie do trybu failover** .

    ![tryb failover](./media/active-geo-replication-configure-portal/secondaries.png)
4. Kliknij przycisk **tak** , aby rozpocząć pracę w trybie failover.

Polecenie natychmiast przełącza pomocniczą bazę danych do roli podstawowej. Ten proces jest zwykle zakończony w ciągu 30 sekund lub mniej.

Istnieje krótki okres, w którym obie bazy danych są niedostępne (w kolejności od 0 do 25 sekund), podczas gdy role są przełączane. Jeśli podstawowa baza danych ma wiele pomocniczych baz danych, polecenie automatycznie ponownie skonfiguruje inne serwery pomocnicze w celu nawiązania połączenia z nowym serwerem podstawowym. Cała operacja powinna trwać krócej niż minutę w normalnych warunkach.

> [!NOTE]
> To polecenie zostało zaprojektowane w celu szybkiego odzyskania bazy danych w przypadku przestoju. Wyzwala tryb failover bez synchronizacji danych (wymuszone przejście w tryb failover).  Jeśli podstawowy jest w trybie online i trwa zatwierdzanie transakcji po wydaniu polecenia, może wystąpić utrata danych.

## <a name="remove-secondary-database"></a>Usuwanie pomocniczej bazy danych

Ta operacja trwale kończy replikację do pomocniczej bazy danych i zmienia rolę pomocniczą do zwykłej bazy danych do odczytu i zapisu. Jeśli łączność z pomocniczą bazą danych zostanie przerwana, polecenie powiedzie się, ale pomocnicza nie stanie się do odczytu i zapisu do momentu przywrócenia łączności.  

1. W [Azure Portal](https://portal.azure.com)przejdź do podstawowej bazy danych w ramach powiązania replikacji geograficznej.
2. Na stronie Baza danych SQL Wybierz opcję **replikacja geograficzna** .
3. Z listy **serwery pomocnicze** wybierz bazę danych, która ma zostać usunięta z powiązania replikacji geograficznej.
4. Kliknij przycisk **Zatrzymaj replikację** .

    ![Usuń pomocniczy](./media/active-geo-replication-configure-portal/remove-secondary.png)
5. Zostanie otwarte okno potwierdzenia. Kliknij przycisk **tak** , aby usunąć bazę danych z powiązania replikacji geograficznej. (Ustaw ją na bazę danych do odczytu i zapisu, która nie jest częścią żadnej replikacji).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o aktywnej replikacji geograficznej, zobacz [aktywną replikację geograficzną](active-geo-replication-overview.md).
* Aby dowiedzieć się więcej o grupach autostartu trybu failover, zobacz [grupy autotrybu failover](auto-failover-group-overview.md)
* Aby zapoznać się z omówieniem i scenariuszami ciągłości działania, zobacz temat [ciągłość działania — Omówienie](business-continuity-high-availability-disaster-recover-hadr-overview.md).