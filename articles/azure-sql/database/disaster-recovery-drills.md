---
title: Próbne odzyskiwanie po awarii
description: Poznaj wskazówki i najlepsze rozwiązania dotyczące korzystania z Azure SQL Database w celu przeprowadzenia próbnego odzyskiwania po awarii.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 12/18/2018
ms.openlocfilehash: f53a08a12c5afda8dbc3f25d9102f52b870ceea4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91321666"
---
# <a name="performing-disaster-recovery-drills"></a>Wykonywanie operacji przechodzenia do odzyskiwania po awarii
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Zalecane jest, aby sprawdzanie gotowości aplikacji dla przepływu pracy odzyskiwania odbywało się okresowo. Sprawdzanie zachowania aplikacji oraz implikacje utraty danych i/lub zakłócenia, które obejmuje przełączenie w tryb failover, jest dobrą metodą inżynieryjną. Jest to również wymagane przez większość standardów branżowych w ramach certyfikacji prowadzonej w ramach ciągłości działania.

Przeprowadzenie drążenia odzyskiwania po awarii składa się z:

* Symulowanie awarii warstwy danych
* Odzyskiwania
* Weryfikuj odzyskiwanie po odzyskiwaniu integralności aplikacji

W zależności od sposobu, w jaki [zaprojektowano swoją aplikację pod kątem ciągłości](business-continuity-high-availability-disaster-recover-hadr-overview.md)działania, przepływ pracy do wykonania drążenia może się różnić. W tym artykule opisano najlepsze rozwiązania dotyczące przeprowadzania przechodzenia do szczegółów odzyskiwania po awarii w kontekście Azure SQL Database.

## <a name="geo-restore"></a>Przywracanie geograficzne

Aby zapobiec utracie danych podczas przechodzenia do szczegółów odzyskiwania po awarii, wykonaj drążenie przy użyciu środowiska testowego, tworząc kopię środowiska produkcyjnego i używając go do sprawdzenia przepływu pracy trybu failover aplikacji.

### <a name="outage-simulation"></a>Symulacja przestojów

Aby symulować awarię, można zmienić nazwę źródłowej bazy danych. Ta zmiana nazwy powoduje błędy łączności aplikacji.

### <a name="recovery"></a>Odzyskiwania

* Wykonaj operację przywracania geograficznego bazy danych na innym serwerze, zgodnie z opisem w [tym miejscu](disaster-recovery-guidance.md).
* Zmień konfigurację aplikacji, aby połączyć się z odzyskaną bazą danych, a następnie postępuj zgodnie z przewodnikiem [Konfigurowanie bazy danych po odzyskaniu](disaster-recovery-guidance.md) .

### <a name="validation"></a>Walidacja

Ukończ przechodzenie do szczegółów, sprawdzając, czy odzyskiwanie po stronie integralnej aplikacji (w tym parametry połączenia, logowania, podstawowe testy funkcjonalności lub inne funkcje walidacji w standardowych procedurach signoffs aplikacji).

## <a name="failover-groups"></a>Grupy trybu failover

W przypadku bazy danych, która jest chroniona za pomocą grup trybu failover, ćwiczenia przechodzenia do szczegółów obejmują planowane przejście w tryb failover na serwer pomocniczy. Planowana praca w trybie failover gwarantuje, że podstawowa i pomocnicza baza danych w grupie trybu failover pozostają zsynchronizowane po przełączeniu ról. W przeciwieństwie do nieplanowanego przejścia w tryb failover ta operacja nie powoduje utraty danych, dzięki czemu można wykonać drążenie w środowisku produkcyjnym.

### <a name="outage-simulation"></a>Symulacja przestojów

Aby symulować awarię, można wyłączyć aplikację sieci Web lub maszynę wirtualną połączonej z bazą danych. Ta symulacja przestoju skutkuje błędami łączności dla klientów sieci Web.

### <a name="recovery"></a>Odzyskiwania

* Upewnij się, że konfiguracja aplikacji w regionie DR wskazuje poprzednią pomocniczą, która jest w pełni dostępną nową podstawową.
* Zainicjuj [zaplanowaną pracę w trybie failover](scripts/setup-geodr-and-failover-database-powershell.md) grupy trybu failover z serwera pomocniczego.
* Aby ukończyć odzyskiwanie, postępuj zgodnie z przewodnikiem [Konfigurowanie bazy danych po odzyskaniu](disaster-recovery-guidance.md) .

### <a name="validation"></a>Walidacja

Wykonaj drążenie, sprawdzając odzyskiwanie po stronie odzyskiwania integralności aplikacji (w tym łączność, podstawowe testowanie funkcji lub inne walidacje wymagane przez signoffs drążenia).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o scenariuszach ciągłości biznesowej, zobacz [scenariusze ciągłości](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Aby dowiedzieć się więcej o Azure SQL Database zautomatyzowanych kopii zapasowych, zobacz [SQL Database zautomatyzowane kopie zapasowe](automated-backups-overview.md)
* Aby dowiedzieć się więcej o korzystaniu z automatycznych kopii zapasowych na potrzeby odzyskiwania, zobacz [przywracanie bazy danych z kopii zapasowych inicjowanych przez usługę](recovery-using-backups.md).
* Aby dowiedzieć się więcej na temat szybszych opcji odzyskiwania, zobacz [aktywnej replikacji geograficznej](active-geo-replication-overview.md) i [grup Autotryb failover](auto-failover-group-overview.md).
