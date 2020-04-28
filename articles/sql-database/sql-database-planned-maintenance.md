---
title: Planowanie zdarzeń konserwacji na platformie Azure
description: Dowiedz się, jak przygotować się do zaplanowanych zdarzeń konserwacji do Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: ba882176fbe17f7b74c786f421dde8fadd58d9b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73821316"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planowanie zdarzeń konserwacji platformy Azure w usłudze Azure SQL Database

Dowiedz się, jak przygotować się do zaplanowanych zdarzeń konserwacji w usłudze Azure SQL Database.

## <a name="what-is-a-planned-maintenance-event"></a>Co to jest planowane zdarzenie konserwacji

Dla każdej bazy danych usługa Azure SQL DB utrzymuje kworum replik baz danych, w których jedna replika jest podstawowym. Zawsze, gdy replika podstawowa musi być obsługą online, a co najmniej jedna replika pomocnicza musi być w dobrej kondycji. Podczas planowanej konserwacji członkowie kworum bazy danych przechodzą do trybu offline po jednej naraz, z zamiarem, że istnieje jedna replika podstawowa, a co najmniej jedna replika pomocnicza jest w trybie online, aby zapewnić brak przestojów klientów. Jeśli replika podstawowa musi zostać przełączona w tryb offline, proces ponownej konfiguracji/przełączenia w tryb failover nastąpi, gdy jedna replika pomocnicza stanie się nowym serwerem podstawowym.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Czego można oczekiwać podczas planowanego zdarzenia konserwacji

Ponowne konfiguracje/przełączenia w tryb failover są zwykle ukończone w ciągu 30 sekund — średnia wynosi 8 sekund. Jeśli jest już połączony, aplikacja musi ponownie nawiązać połączenie z nową repliką podstawową bazy danych w dobrej kondycji. Jeśli nastąpi próba ponownego skonfigurowania nowego połączenia, aby Nowa replika podstawowa była w trybie online, zostanie wyświetlony komunikat o błędzie 40613 (baza danych jest niedostępna): "baza danych" {DatabaseName} "na serwerze" {ServerName} "jest obecnie niedostępna. Spróbuj ponownie nawiązać połączenie później. ". Jeśli baza danych ma długotrwałe zapytanie, to zapytanie zostanie przerwane podczas ponownej konfiguracji i musi zostać ponownie uruchomione.

## <a name="retry-logic"></a>Logika ponawiania próby

Każda aplikacja produkcyjna klienta, która nawiązuje połączenie z usługą bazy danych w chmurze, powinna implementować niezawodną [logikę ponawiania](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)połączenia. Pomoże to ograniczyć te sytuacje i ogólnie rzecz biorąc, te błędy powinny być widoczne dla użytkownika końcowego.

## <a name="frequency"></a>Częstotliwość

Średnio 1,7 zdarzeń konserwacji planowanej odbywa się co miesiąc.

## <a name="resource-health"></a>Kondycja zasobów

Jeśli baza danych SQL napotkała błędy logowania, Sprawdź okno [Resource Health](../service-health/resource-health-overview.md#get-started) w [Azure Portal](https://portal.azure.com) dla bieżącego stanu. Sekcja historia kondycji zawiera przyczynę przestoju dla każdego zdarzenia (jeśli jest dostępna).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Resource Health](sql-database-resource-health.md) SQL Database
- Aby uzyskać więcej informacji na temat logiki ponawiania, zobacz [logika ponawiania dla błędów przejściowych](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
