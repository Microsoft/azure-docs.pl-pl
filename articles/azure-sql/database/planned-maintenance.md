---
title: Planowanie zdarzeń konserwacji na platformie Azure
description: Dowiedz się, jak przygotować się do zaplanowanych zdarzeń konserwacji w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 2f5f69a5f145ae8bbf23aa1e5dbca07b30db0e21
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84042694"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Planowanie zdarzeń konserwacji platformy Azure w Azure SQL Database i wystąpieniu zarządzanym Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Dowiedz się, jak przygotować się do planowanych zdarzeń konserwacji w bazie danych w Azure SQL Database i wystąpieniu zarządzanym Azure SQL.

## <a name="what-is-a-planned-maintenance-event"></a>Co to jest planowane zdarzenie konserwacji

Dla każdej bazy danych Azure SQL Database i wystąpienie zarządzane usługi Azure SQL utrzymują kworum replik baz danych, w których jedna replika jest podstawowym. Zawsze, gdy replika podstawowa musi być obsługą online, a co najmniej jedna replika pomocnicza musi być w dobrej kondycji. Podczas planowanej konserwacji członkowie kworum bazy danych przechodzą do trybu offline po jednej naraz, z zamiarem, że istnieje jedna replika podstawowa, a co najmniej jedna replika pomocnicza jest w trybie online, aby zapewnić brak przestojów klientów. Jeśli replika podstawowa musi zostać przełączona w tryb offline, proces ponownej konfiguracji/przełączenia w tryb failover nastąpi, gdy jedna replika pomocnicza stanie się nowym serwerem podstawowym.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Czego można oczekiwać podczas planowanego zdarzenia konserwacji

Ponowne konfiguracje/przełączenia w tryb failover są zwykle ukończone w ciągu 30 sekund — średnia wynosi 8 sekund. Jeśli jest już połączony, aplikacja musi ponownie nawiązać połączenie z nową repliką podstawową bazy danych w dobrej kondycji. Jeśli nastąpi próba ponownego skonfigurowania nowego połączenia, aby Nowa replika podstawowa była w trybie online, zostanie wyświetlony komunikat o błędzie 40613 (baza danych jest niedostępna): "baza danych" {DatabaseName} "na serwerze" {ServerName} "jest obecnie niedostępna. Spróbuj ponownie nawiązać połączenie później. ". Jeśli baza danych ma długotrwałe zapytanie, to zapytanie zostanie przerwane podczas ponownej konfiguracji i musi zostać ponownie uruchomione.

## <a name="retry-logic"></a>Logika ponawiania próby

Każda aplikacja produkcyjna klienta, która nawiązuje połączenie z usługą bazy danych w chmurze, powinna implementować niezawodną [logikę ponawiania](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)połączenia. Pomoże to ograniczyć te sytuacje i ogólnie rzecz biorąc, te błędy powinny być widoczne dla użytkownika końcowego.

## <a name="frequency"></a>Częstotliwość

Średnio 1,7 zdarzeń konserwacji planowanej odbywa się co miesiąc.

## <a name="resource-health"></a>Kondycja zasobów

Jeśli baza danych ma błędy logowania, Sprawdź okno [Resource Health](../../service-health/resource-health-overview.md#get-started) w [Azure Portal](https://portal.azure.com) , aby uzyskać bieżący stan. Sekcja historia kondycji zawiera przyczynę przestoju dla każdego zdarzenia (jeśli jest dostępna).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Resource Health](resource-health-to-troubleshoot-connectivity.md) Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL
- Aby uzyskać więcej informacji na temat logiki ponawiania, zobacz [logika ponawiania dla błędów przejściowych](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
