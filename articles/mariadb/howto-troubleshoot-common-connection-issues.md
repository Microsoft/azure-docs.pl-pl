---
title: Rozwiązywanie problemów z połączeniami — Azure Database for MariaDB
description: Dowiedz się, jak rozwiązywać problemy z połączeniem do Azure Database for MariaDB, w tym błędy przejściowe wymagające ponownych prób, problemy z zaporą i przestoje.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 3/18/2020
ms.openlocfilehash: b9a670472529dccd51cbfa71e385151bc63cff9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664736"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Rozwiązywanie problemów z połączeniem z usługą Azure Database for MariaDB

Problemy z połączeniem mogą być spowodowane różnymi elementami, w tym:

* Ustawienia zapory
* Limit czasu połączenia
* Nieprawidłowe informacje logowania
* Osiągnięto maksymalny limit dla niektórych zasobów Azure Database for MariaDB
* Problemy związane z infrastrukturą usługi
* Konserwacja w usłudze
* Alokacja obliczeniowa serwera jest zmieniana przez skalowanie liczby rdzeni wirtualnych lub przechodzenie do innej warstwy usług

Ogólnie rzecz biorąc problemy z połączeniem do Azure Database for MariaDB mogą być klasyfikowane w następujący sposób:

* Błędy przejściowe (krótko-lub sporadyczne)
* Błędy trwałe lub nieprzejściowe (błędy, które regularnie powtarzają się)

## <a name="troubleshoot-transient-errors"></a>Rozwiązywanie problemów dotyczących błędów przejściowych

Błędy przejściowe występują, gdy konserwacja jest przeprowadzana, system napotyka błąd z sprzętem lub oprogramowaniem lub zmienia rdzeni wirtualnych lub warstwę usług serwera. Usługa Azure Database for MariaDB ma wbudowaną wysoką dostępność i została zaprojektowana z myślą o ograniczeniu tego rodzaju problemów automatycznie. Jednak aplikacja traci połączenie z serwerem przez krótki okres zwykle krótszy niż 60 sekund. Niektóre zdarzenia mogą sporadycznie podejmować środki zaradcze, na przykład wtedy, gdy duża transakcja powoduje długotrwałe odzyskiwanie.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Kroki rozwiązywania przejściowych problemów z łącznością

1. Sprawdź [pulpit nawigacyjny usługi Microsoft Azure](https://azure.microsoft.com/status) , aby uzyskać znane awarie, które wystąpiły w czasie, w którym błędy zostały zgłoszone przez aplikację.
2. Aplikacje, które łączą się z usługą w chmurze, taką jak Azure Database for MariaDB, powinny oczekiwać błędów przejściowych i implementować logikę ponawiania w celu obsługi tych błędów zamiast obsłużyć te błędy jako błędy aplikacji dla użytkowników. Przegląd [obsługi błędów łączności przejściowej dla Azure Database for MariaDB](concepts-connectivity.md) w celu uzyskania najlepszych rozwiązań i projektowania wytycznych dotyczących obsługi błędów przejściowych.
3. Ponieważ serwer zbliża się do limitów zasobów, może wydawać się, że problem z łącznością jest przejściowy. Zobacz [ograniczenia w Azure Database for MariaDB](concepts-limits.md).
4. Jeśli problemy z łącznością będą kontynuowane lub jeśli czas, przez który aplikacja napotyka błąd, przekracza 60 sekund lub Jeśli zobaczysz wiele wystąpień błędu w danym dniu, Utwórz żądanie pomocy technicznej platformy Azure, wybierając pozycję **Uzyskaj pomoc** techniczną w witrynie [pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Rozwiązywanie problemów z trwałymi błędami

Jeśli aplikacja trwale nie może nawiązać połączenia z Azure Database for MariaDB, zazwyczaj wskazuje problem z jedną z następujących czynności:

* Konfiguracja zapory: serwer Azure Database for MariaDB lub Zapora po stronie klienta blokuje połączenia.
* Ponowna konfiguracja sieci po stronie klienta: dodano nowy adres IP lub serwer proxy.
* Błąd użytkownika: na przykład możesz mieć błędne parametry połączenia, takie jak nazwa serwera w parametrach połączenia lub brak sufiksu *\@ servername* w nazwie użytkownika.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Kroki rozwiązywania problemów z łącznością trwałą

1. Skonfiguruj [reguły zapory](howto-manage-firewall-portal.md) w taki sposób, aby zezwalały na adres IP klienta. Wyłącznie do celów testowania tymczasowego, należy skonfigurować regułę zapory przy użyciu adresu 0.0.0.0 jako początkowy adres IP i przy użyciu wartości 255.255.255.255 jako końcowego adresu IP. Spowoduje to otwarcie serwera do wszystkich adresów IP. Jeśli spowoduje to rozwiązanie problemu z łącznością, Usuń tę regułę i Utwórz regułę zapory dla odpowiednio ograniczonego adresu IP lub zakresu adresów.
2. Na wszystkich zaporach między klientem a Internetem upewnij się, że port 3306 jest otwarty dla połączeń wychodzących.
3. Sprawdź parametry połączenia i inne ustawienia połączenia. Zapoznaj się [z tematem łączenie aplikacji z Azure Database for MariaDB](howto-connection-string.md).
4. Sprawdź kondycję usługi na pulpicie nawigacyjnym. Jeśli uważasz, że wystąpiła awaria regionalna, zobacz [Omówienie ciągłości działania w Azure Database for MariaDB](concepts-business-continuity.md) w celu wykonania czynności związanych z odzyskiwaniem do nowego regionu.

## <a name="next-steps"></a>Następne kroki

* [Obsługa błędów łączności przejściowej dla Azure Database for MariaDB](concepts-connectivity.md)
