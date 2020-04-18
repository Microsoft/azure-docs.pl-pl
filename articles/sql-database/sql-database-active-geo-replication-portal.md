---
title: 'Samouczek: Replikacja geograficzna & pracy awaryjnej w portalu'
description: Skonfiguruj replikację geograficzną dla pojedynczej lub puli bazy danych w usłudze Azure SQL Database przy użyciu portalu Azure i inicjuj tryb failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 59616fb217b28a8c47d9a5d13e2f4c1b9a8f6bb3
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605221"
---
# <a name="configure-active-geo-replication-for-azure-sql-database-in-the-azure-portal-and-initiate-failover"></a>Konfigurowanie aktywnej replikacji geograficznej dla usługi Azure SQL Database w portalu Azure i inicjowanie trybu failover

W tym artykule pokazano, jak skonfigurować [aktywną replikację geograficzną dla pojedynczych i puli baz danych](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities) w usłudze Azure SQL Database przy użyciu portalu [Azure](https://portal.azure.com) i zainicjować tryb failover.

Aby uzyskać informacje na temat grup automatycznego trybu failover z pojedynczymi i buforowanymi bazami danych, zobacz [Najważniejsze wskazówki dotyczące używania grup trybu failover z pojedynczymi i buforowanymi bazami danych](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). Aby uzyskać informacje na temat grup automatycznego trybu failover z wystąpieniami zarządzanymi, zobacz [Najważniejsze wskazówki dotyczące używania grup trybu failover z wystąpieniami zarządzanymi](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-managed-instances).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować aktywną replikację geograficzną przy użyciu witryny Azure Portal, potrzebny jest następujący zasób:

* Baza danych SQL platformy Azure: podstawowa baza danych, którą chcesz replikować do innego regionu geograficznego.

> [!Note]
> Korzystając z witryny Azure portal, można utworzyć pomocniczą bazę danych tylko w ramach tej samej subskrypcji jako podstawowy. Jeśli pomocnicza baza danych musi być w innej subskrypcji, użyj [interfejsu API Create Database REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate) lub ALTER DATABASE [Transact-SQL API](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql).

## <a name="add-a-secondary-database"></a>Dodawanie pomocniczej bazy danych

Poniższe kroki tworzą nową pomocniczą bazę danych w partnerstwie replikacji geograficznej.  

Aby dodać pomocniczą bazę danych, musisz być właścicielem subskrypcji lub współwłaścicielem.

Pomocnicza baza danych ma taką samą nazwę jak podstawowa baza danych i ma domyślnie tę samą warstwę usług i rozmiar obliczeń. Pomocnicza baza danych może być pojedynczą bazą danych lub pulą bazą danych. Aby uzyskać więcej informacji, zobacz [model zakupów oparty na uU](sql-database-service-tiers-dtu.md) i model zakupu oparty na [vCore](sql-database-service-tiers-vcore.md).
Po utworzeniu i rozstawieniu pomocniczego dane rozpoczynają replikowanie z podstawowej bazy danych do nowej pomocniczej bazy danych.

> [!NOTE]
> Jeśli baza danych partnera już istnieje (na przykład w wyniku zakończenia poprzedniej relacji replikacji geograficznej), polecenie kończy się niepowodzeniem.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do bazy danych, którą chcesz skonfigurować do replikacji geograficznej.
2. Na stronie bazy danych SQL wybierz **replikację geograficzną**, a następnie wybierz region, w który chcesz utworzyć pomocniczą bazę danych. Możesz wybrać dowolny region inny niż region obsługujący podstawową bazę danych, ale [zalecamy sparowany region](../best-practices-availability-paired-regions.md).

    ![Konfigurowanie replikacji geograficznej](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Wybierz lub skonfiguruj serwer i warstwę cenową dla pomocniczej bazy danych.

    ![Konfigurowanie pomocniczego](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Opcjonalnie można dodać pomocniczą bazę danych do puli elastycznej. Aby utworzyć pomocniczą bazę danych w puli, kliknij **pulę elastyczną** i wybierz pulę na serwerze docelowym. Pula musi już istnieć na serwerze docelowym. Ten przepływ pracy nie tworzy puli.
5. Kliknij **przycisk Utwórz,** aby dodać pomocniczy.
6. Tworzona jest pomocnicza baza danych i rozpoczyna się proces wysiewu.

    ![Konfigurowanie pomocniczego](./media/sql-database-geo-replication-portal/seeding0.png)
7. Po zakończeniu procesu wysiewu pomocnicza baza danych wyświetla jego stan.

    ![Wysiew kompletny](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="initiate-a-failover"></a>Inicjowanie pracy awaryjnej

Pomocnicza baza danych może zostać przełączona, aby stała się podstawową.  

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do podstawowej bazy danych w ramach współpracy replikacji geograficznej.
2. W bloku Baza danych SQL wybierz pozycję **Wszystkie ustawienia** > **replikacji geograficznej**.
3. Na liście **SECONDARIES** wybierz bazę danych, która ma stać się nową bazą podstawową, a następnie kliknij przycisk **Wymuszone tryb failover**.

    ![Pracy awaryjnej](./media/sql-database-geo-replication-failover-portal/secondaries.png)
4. Kliknij **przycisk Tak,** aby rozpocząć przetwórt pracy awaryjnej.

Polecenie natychmiast przełącza pomocniczą bazę danych do roli podstawowej. Ten proces zwykle powinien zakończyć się w ciągu 30 sekund lub mniej.

Istnieje krótki okres, w którym obie bazy danych są niedostępne (w kolejności od 0 do 25 sekund), podczas gdy role są przełączane. Jeśli podstawowa baza danych ma wiele pomocniczych baz danych, polecenie automatycznie ponownie konfiguruje inne pomocnicze bazy danych, aby połączyć się z nowym podstawowym. Cała operacja powinna potrwać mniej niż minutę, aby zakończyć w normalnych warunkach.

> [!NOTE]
> To polecenie jest przeznaczone do szybkiego odzyskiwania bazy danych w przypadku awarii. Wyzwala tryb failover bez synchronizacji danych (wymuszone tryb failover).  Jeśli podstawowy jest w trybie online i zatwierdzania transakcji, gdy polecenie jest wydawane niektóre utraty danych może wystąpić.

## <a name="remove-secondary-database"></a>Usuwanie pomocniczej bazy danych

Ta operacja trwale kończy replikację do pomocniczej bazy danych i zmienia rolę pomocniczej do regularnej bazy danych odczytu i zapisu. Jeśli łączność z pomocniczą bazą danych zostanie przerwana, polecenie powiedzie się, ale pomocniczy nie staje się odczytu i zapisu, dopóki po przywróceniu łączności.  

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do podstawowej bazy danych w ramach współpracy replikacji geograficznej.
2. Na stronie bazy danych SQL wybierz opcję **replikacja geograficzna**.
3. Na liście **SECONDARIES** wybierz bazę danych, którą chcesz usunąć ze partnerstwa replikacji geograficznej.
4. Kliknij **pozycję Zatrzymaj replikację**.

    ![Usuń drugorzędne](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Zostanie otwarte okno potwierdzenia. Kliknij **przycisk Tak,** aby usunąć bazę danych ze partnerstwa replikacji geograficznej. (Ustaw ją na bazę danych odczytu i zapisu, która nie jest częścią żadnej replikacji).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o aktywnej replikacji geograficznej, zobacz [aktywna replikacja geograficzna](sql-database-active-geo-replication.md).
* Aby dowiedzieć się więcej o grupach automatycznego trybu failover, zobacz [Grupy automatycznego trybu failover](sql-database-auto-failover-group.md)
* Aby zapoznać się z omówieniem ciągłości biznesowej i scenariuszami, zobacz [Omówienie ciągłości biznesowej](sql-database-business-continuity.md).
