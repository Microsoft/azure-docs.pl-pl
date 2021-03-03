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
ms.reviewer: sstein
ms.date: 1/21/2021
ms.openlocfilehash: d38ac9731959cf9a23052753b09c9e7819846705
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664121"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Zaplanuj zdarzenia konserwacji platformy Azure w Azure SQL Database i wystąpieniu zarządzanym Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Dowiedz się, jak przygotować się do planowanych zdarzeń konserwacji w bazie danych w Azure SQL Database i wystąpieniu zarządzanym Azure SQL.

## <a name="what-is-a-planned-maintenance-event"></a>Co to jest planowane zdarzenie konserwacji?

Aby zapewnić bezpieczne, zgodne, stabilne i wydajne usługi wystąpień zarządzanych w usłudze Azure SQL Azure SQL Database, aktualizacje są wykonywane za pomocą składników usługi niemal w sposób ciągły. Dzięki nowoczesnej i niezawodnej architekturze usług oraz innowacyjnym technologiom, takim jak [gorąca poprawka](https://aka.ms/azuresqlhotpatching), większość aktualizacji jest w pełni przejrzysta i nie ma wpływu na warunki dostępności usługi. Nadal niektóre typy aktualizacji powodują krótkie przerwania działania usługi i wymagają szczególnego traktowania. 

Dla każdej bazy danych Azure SQL Database i wystąpienie zarządzane usługi Azure SQL utrzymują kworum replik baz danych, w których jedna replika jest podstawowym. Przez cały czas replika podstawowa musi być obsługą online, a co najmniej jedna replika pomocnicza musi być w dobrej kondycji. Podczas planowanej konserwacji członkowie kworum bazy danych przechodzą do trybu offline po jednej naraz, z zamiarem, że istnieje jedna replika podstawowa, a co najmniej jedna replika pomocnicza jest w trybie online, aby zapewnić brak przestojów klientów. Jeśli replika podstawowa musi zostać przełączona w tryb offline, proces ponownej konfiguracji/przełączenia w tryb failover nastąpi, gdy jedna replika pomocnicza stanie się nowym serwerem podstawowym.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>Czego można oczekiwać podczas planowanego zdarzenia konserwacji

Zdarzenie obsługi może generować pojedyncze lub wielokrotne przełączenia w tryb failover, w zależności od Constellation replik podstawowych i pomocniczych na początku zdarzenia konserwacji. Średnio 1,7 trybu failover odbywa się według zaplanowanego zdarzenia konserwacji. Ponowne konfiguracje/przełączenia w tryb failover zazwyczaj kończą się w ciągu 30 sekund. Średnia to osiem sekund. Jeśli jest już połączony, aplikacja musi ponownie nawiązać połączenie z nową repliką podstawową bazy danych. Jeśli nastąpi próba ponownego skonfigurowania nowego połączenia, aby Nowa replika podstawowa była w trybie online, zostanie wyświetlony komunikat o błędzie 40613 (baza danych jest niedostępna): *"baza danych" {DatabaseName} "na serwerze" {ServerName} "jest obecnie niedostępna. Spróbuj ponownie nawiązać połączenie później. "* Jeśli baza danych ma długotrwałe zapytanie, ta kwerenda zostanie przerwana podczas ponownej konfiguracji i będzie musiała zostać ponownie uruchomiona.

## <a name="how-to-simulate-a-planned-maintenance-event"></a>Symulowanie planowanego zdarzenia konserwacji

Zapewnienie odporności aplikacji klienckiej na zdarzenia konserwacyjne przed wdrożeniem w środowisku produkcyjnym pomoże ograniczyć ryzyko błędów aplikacji i przyczynić się do dostępności aplikacji dla użytkowników końcowych. Możesz przetestować zachowanie aplikacji klienckiej w trakcie planowanych zdarzeń konserwacji, [inicjując ręczne](https://aka.ms/mifailover-techblog) przełączenie w tryb failover za pomocą programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Spowoduje to wygenerowanie identycznego zachowania jako zdarzenia konserwacji w trybie offline repliki podstawowej.

## <a name="retry-logic"></a>Logika ponowień

Każda aplikacja produkcyjna klienta, która nawiązuje połączenie z usługą bazy danych w chmurze, powinna implementować niezawodną [logikę ponawiania](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)połączenia. Dzięki temu przełączenie w tryb failover jest przezroczyste dla użytkowników końcowych lub co najmniej minimalizuje negatywne skutki.

## <a name="resource-health"></a>Kondycja zasobów

Jeśli baza danych ma błędy logowania, Sprawdź okno [Resource Health](../../service-health/resource-health-overview.md#get-started) w [Azure Portal](https://portal.azure.com) dla bieżącego stanu. Sekcja historia kondycji zawiera przyczynę przestoju dla każdego zdarzenia (jeśli jest dostępna).

## <a name="maintenance-window-feature"></a>Funkcja okna obsługi

Funkcja okna obsługi umożliwia konfigurację przewidywalnych harmonogramów okien obsługi dla kwalifikujących się baz danych Azure SQL i wystąpień zarządzanych SQL. Aby uzyskać więcej informacji, zobacz [okno obsługi](maintenance-window.md) .

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [Resource Health](resource-health-to-troubleshoot-connectivity.md) Azure SQL Database i wystąpienia zarządzanego Azure SQL.
- Aby uzyskać więcej informacji na temat logiki ponawiania, zobacz [logika ponawiania dla błędów przejściowych](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
- Skonfiguruj harmonogramy okna obsługi przy użyciu funkcji [okna obsługi](maintenance-window.md) .
