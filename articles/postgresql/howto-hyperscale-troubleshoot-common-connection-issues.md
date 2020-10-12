---
title: Rozwiązywanie problemów z połączeniami — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Dowiedz się, jak rozwiązywać problemy z połączeniem do Azure Database for PostgreSQL-Citus
keywords: Połączenie PostgreSQL, parametry połączenia, problemy z łącznością, błąd przejściowy, błąd połączenia
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 10/8/2019
ms.openlocfilehash: e1c6825820ae943d10157279dfe93922a7521b75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295621"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Rozwiązywanie problemów z połączeniami w celu Azure Database for PostgreSQL-Citus

Problemy z połączeniem mogą być spowodowane przez kilka rzeczy, takich jak:

* Ustawienia zapory
* Limit czasu połączenia
* Nieprawidłowe informacje logowania
* Osiągnięto limit połączeń dla grupy serwerów
* Problemy związane z infrastrukturą usługi
* Konserwacja usługi
* Węzeł koordynatora przechodzi do trybu failover na nowym sprzęcie

Ogólnie rzecz biorąc problemy z połączeniami ze skalą (Citus) można klasyfikować w następujący sposób:

* Błędy przejściowe (krótko-lub sporadyczne)
* Błędy trwałe lub nieprzejściowe (błędy, które regularnie powtarzają się)

## <a name="troubleshoot-transient-errors"></a>Rozwiązywanie problemów z błędami przejściowymi

Błędy przejściowe występują z kilku powodów. Najbardziej typową czynnością jest konserwacja systemu, błąd ze sprzętem lub oprogramowaniem, a także rdzeń wirtualny uaktualnienia węzłów koordynatora.

Włączenie węzłów grupy serwerów o wysokiej dostępności (Citus) umożliwia automatyczne ograniczenie tego rodzaju problemów. Jednak aplikacja powinna być nadal przygotowana, aby skrócić swoje połączenie. Ponadto inne zdarzenia mogą zająć więcej czasu, na przykład wtedy, gdy duża transakcja powoduje długotrwałe odzyskiwanie.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki rozwiązywania przejściowych problemów z łącznością

1. Sprawdź [pulpit nawigacyjny usługi Microsoft Azure](https://azure.microsoft.com/status) , aby uzyskać znane awarie, które wystąpiły w czasie, w którym aplikacja zgłosiła błędy.
2. Aplikacje łączące się z usługą w chmurze, takie jak funkcja Citus, powinny oczekiwać błędów przejściowych i reagować na nie. Na przykład aplikacje powinny implementować logikę ponowień, aby obsługiwać te błędy zamiast obsłużyć je jako błędy aplikacji dla użytkowników.
3. Ponieważ Grupa serwerów zbliża się do limitów zasobów, błędy mogą wyglądać jak przejściowe problemy z łącznością. Zwiększenie ilości pamięci RAM lub dodanie węzłów procesu roboczego i ponowne zrównoważenie danych może pomóc.
4. Jeśli problemy z łącznością będą kontynuowane lub trwają dłużej niż 60 sekund lub występuje więcej niż raz dziennie, należy wysłać żądanie pomocy technicznej platformy Azure, wybierając pozycję **Uzyskaj pomoc techniczną** w witrynie [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Rozwiązywanie problemów z trwałymi błędami

Jeśli aplikacja trwale nie może nawiązać połączenia ze zbyt dużą skalą (Citus), najczęstsze przyczyny są błędnej konfiguracji zapory lub błędu użytkownika.

* Konfiguracja zapory węzła koordynatora: Upewnij się, że Zapora serwera Citus jest skonfigurowana tak, aby zezwalać na połączenia z klienta, w tym serwery proxy i bramy.
* Konfiguracja zapory klienta: Zapora na kliencie musi zezwalać na połączenia z serwerem bazy danych. Niektóre zapory wymagają zezwolenia nie tylko na aplikacje według nazwy, ale dopuszczają adresy IP i porty serwera.
* Błąd użytkownika: podwójne sprawdzenie parametrów połączenia. Mogą istnieć niewpisane parametry, takie jak nazwa serwera. Parametry połączenia dla różnych platform języka i PSQL można znaleźć w Azure Portal. Przejdź do strony **Parametry połączenia** w grupie serwerów moja skala (Citus). Należy również pamiętać, że klastry ze skalowaniem (Citus) mają tylko jedną bazę danych i jej wstępnie zdefiniowaną nazwę to **Citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroki rozwiązywania problemów z łącznością trwałą

1. Skonfiguruj [reguły zapory](howto-hyperscale-manage-firewall-using-portal.md) w taki sposób, aby zezwalały na adres IP klienta. Wyłącznie do celów testowania tymczasowego, należy skonfigurować regułę zapory przy użyciu adresu 0.0.0.0 jako początkowy adres IP i przy użyciu wartości 255.255.255.255 jako końcowego adresu IP. Ta reguła otwiera serwer dla wszystkich adresów IP. Jeśli reguła rozwiązuje problem z łącznością, usuń ją i Utwórz regułę zapory dla odpowiednio ograniczonego adresu IP lub zakresu adresów.
2. Na wszystkich zaporach między klientem a Internetem upewnij się, że port 5432 jest otwarty dla połączeń wychodzących.
3. Sprawdź parametry połączenia i inne ustawienia połączenia.
4. Sprawdź kondycję usługi na pulpicie nawigacyjnym.

## <a name="next-steps"></a>Następne kroki

* Poznaj koncepcje [reguł zapory w Azure Database for PostgreSQL-Citus](concepts-hyperscale-firewall-rules.md)
* Zobacz, jak [zarządzać regułami zapory dla Azure Database for PostgreSQL-Citus](howto-hyperscale-manage-firewall-using-portal.md)
