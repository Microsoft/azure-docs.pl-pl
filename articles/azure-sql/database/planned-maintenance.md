---
title: Planowanie zdarzeń konserwacji platformy Azure
description: Dowiedz się, jak przygotować się do zaplanowanych zdarzeń konserwacji w Azure SQL Database i wystąpieniu zarządzanym usługi Azure SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: f0bda1f4b9894b1ea5a68f44a728f715676d500e
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661150"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Zaplanuj zdarzenia konserwacji platformy Azure w Azure SQL Database i wystąpieniu zarządzanym Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Dowiedz się, jak przygotować się do planowanych zdarzeń konserwacji w bazie danych w Azure SQL Database i wystąpieniu zarządzanym Azure SQL.

## <a name="what-is-a-planned-maintenance-event"></a>Co to jest planowane zdarzenie konserwacji?

Dla każdej bazy danych Azure SQL Database i wystąpienie zarządzane usługi Azure SQL utrzymują kworum replik baz danych, w których jedna replika jest podstawowym. Przez cały czas replika podstawowa musi być obsługą online, a co najmniej jedna replika pomocnicza musi być w dobrej kondycji. Podczas planowanej konserwacji członkowie kworum bazy danych przechodzą do trybu offline po jednej naraz, z zamiarem, że istnieje jedna replika podstawowa, a co najmniej jedna replika pomocnicza jest w trybie online, aby zapewnić brak przestojów klientów. Jeśli replika podstawowa musi zostać przełączona w tryb offline, proces ponownej konfiguracji/przełączenia w tryb failover nastąpi, gdy jedna replika pomocnicza stanie się nowym serwerem podstawowym.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Czego można oczekiwać podczas planowanego zdarzenia konserwacji

Ponowne konfiguracje/przełączenia w tryb failover zazwyczaj kończą się w ciągu 30 sekund. Średnia wynosi 8 sekund. Jeśli jest już połączony, aplikacja musi ponownie nawiązać połączenie z nową repliką podstawową bazy danych w dobrej kondycji. Jeśli nastąpi próba ponownego skonfigurowania nowego połączenia, aby Nowa replika podstawowa była w trybie online, zostanie wyświetlony komunikat o błędzie 40613 (baza danych jest niedostępna): "baza danych" {DatabaseName} "na serwerze" {ServerName} "jest obecnie niedostępna. Ponów próbę połączenia później”. Jeśli baza danych ma długotrwałe zapytanie, ta kwerenda zostanie przerwana podczas ponownej konfiguracji i będzie musiała zostać ponownie uruchomiona.

## <a name="retry-logic"></a>Logika ponowień

Każda aplikacja produkcyjna klienta, która nawiązuje połączenie z usługą bazy danych w chmurze, powinna implementować niezawodną [logikę ponawiania](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)połączenia. Pomoże to ograniczyć te sytuacje i ogólnie rzecz biorąc, te błędy powinny być widoczne dla użytkownika końcowego.

## <a name="frequency"></a>Częstotliwość

Średnio 1,7 zdarzeń konserwacji planowanej odbywa się co miesiąc.

## <a name="resource-health"></a>Kondycja zasobów

Jeśli baza danych ma błędy logowania, Sprawdź okno [Resource Health](../../service-health/resource-health-overview.md#get-started) w [Azure Portal](https://portal.azure.com) dla bieżącego stanu. Sekcja historia kondycji zawiera przyczynę przestoju dla każdego zdarzenia (jeśli jest dostępna).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Resource Health](resource-health-to-troubleshoot-connectivity.md) Azure SQL Database i wystąpienia zarządzanego Azure SQL.
- Aby uzyskać więcej informacji na temat logiki ponawiania, zobacz [logika ponawiania dla błędów przejściowych](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
