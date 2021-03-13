---
title: Architektura łączności — Azure Database for PostgreSQL — pojedynczy serwer
description: Opisuje architekturę łączności dla Azure Database for PostgreSQL-jednego serwera.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: conceptual
ms.date: 2/11/2021
ms.openlocfilehash: 104e6503ba47d17c17cfec2b4e62ec3f69f18330
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200012"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Architektura łączności w Azure Database for PostgreSQL
W tym artykule opisano architekturę Azure Database for PostgreSQL łączności oraz sposób kierowania ruchu do wystąpienia bazy danych Azure Database for PostgreSQL z klientów zarówno w ramach platformy Azure, jak i poza nią.

## <a name="connectivity-architecture"></a>Architektura łączności
Połączenie z Azure Database for PostgreSQL jest nawiązywane za pomocą bramy, która jest odpowiedzialna za kierowanie połączeń przychodzących do lokalizacji fizycznej serwera w naszych klastrach. Na poniższym diagramie przedstawiono przepływ ruchu.

:::image type="content" source="./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png" alt-text="Przegląd architektury łączności":::


Gdy klient łączy się z bazą danych, parametry połączenia z serwerem są rozpoznawane jako adres IP bramy. Brama nasłuchuje na adresie IP na porcie 5432. W klastrze bazy danych ruch jest przekazywany do odpowiednich Azure Database for PostgreSQL. W związku z tym, aby nawiązać połączenie z serwerem, na przykład z sieci firmowej, należy otworzyć **zaporę po stronie klienta, aby zezwolić na ruch wychodzący z naszych bram**. Poniżej znajdziesz pełną listę adresów IP używanych przez nasze bramy na region.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>Adresy IP bramy Azure Database for PostgreSQL

Usługa bramy jest hostowana w grupie bezstanowych węzłów obliczeniowych znajdujących się za adresem IP, który klient będzie docierał najpierw podczas próby nawiązania połączenia z serwerem Azure Database for PostgreSQL. 

W ramach trwającej konserwacji usług będziemy okresowo odświeżać sprzęt obliczeniowy obsługujący bramy, aby zapewnić największą bezpieczeństwo i wydajne środowisko łączności. Po odświeżeniu sprzętu bramy jest najpierw tworzony nowy pierścień węzłów obliczeniowych. Ten nowy pierścień służy do obsługi ruchu dla wszystkich nowo utworzonych serwerów Azure Database for PostgreSQL i ma inny adres IP ze starszych pierścieni bramy w tym samym regionie, aby odróżnić ruch. Starsza wersja sprzętu bramy nadal obsługuje istniejące serwery, ale jest planowana do likwidacji w przyszłości. Przed wycofaniem sprzętu bramy klienci korzystający z serwerów i nawiązujący połączenie ze starszymi pierścieniami bramy będą powiadamiani za pośrednictwem poczty e-mail i w Azure Portal w ciągu trzech miesięcy przed likwidacją. Likwidowanie bram może mieć wpływ na łączność z serwerami, jeśli 

* Adresy IP bramy są zakodowane w parametrach połączenia aplikacji. Nie jest to **zalecane**. Należy użyć w pełni kwalifikowanej nazwy domeny (FQDN) serwera w formacie <servername> . Postgres.Database.Azure.com, w parametrach połączenia dla aplikacji. 
* Nie aktualizujesz nowszych adresów IP bramy w zaporze po stronie klienta, aby zezwolić na ruch wychodzący, aby uzyskać dostęp do naszych nowych pierścieni bramy.

W poniższej tabeli wymieniono adresy IP bramy bramy Azure Database for PostgreSQL dla wszystkich obszarów danych. Najbardziej aktualne informacje dotyczące adresów IP bramy dla każdego regionu są przechowywane w poniższej tabeli. W poniższej tabeli przedstawiono następujące kolumny:

* **Adresy IP bramy:** Ta kolumna zawiera listę bieżących adresów IP bram hostowanych w najnowszej generacji sprzętu. W przypadku aprowizacji nowego serwera zalecamy otwarcie zapory po stronie klienta, aby zezwolić na ruch wychodzący dla adresów IP wymienionych w tej kolumnie.
* **Adresy IP bramy (likwidowanie):** Ta kolumna zawiera listę adresów IP bram hostowanych w starszej generacji sprzętu, które są obecnie likwidowane. W przypadku aprowizacji nowego serwera można zignorować te adresy IP. Jeśli masz istniejący serwer, Kontynuuj zachowywanie reguły ruchu wychodzącego dla zapory dla tych adresów IP, ponieważ jeszcze jej nie zlikwidowano. Jeśli porzucasz reguły zapory dla tych adresów IP, mogą wystąpić błędy łączności. Zamiast tego należy spodziewać się, że nowe adresy IP wymienione w kolumnie adresy IP bramy zostaną wdrożone do reguły zapory wychodzącej zaraz po otrzymaniu powiadomienia o wycofaniu. Dzięki temu serwer zostanie zmigrowany do najnowszej wersji sprzętowej bramy, nie ma przerw w łączności z serwerem.
* **Adresy IP bramy (zlikwidowany):** Ta kolumna zawiera listę adresów IP pierścieni bramy, które są likwidowane i nie są już w trakcie operacji. Możesz bezpiecznie usunąć te adresy IP z reguły zapory dla ruchu wychodzącego. 


| **Nazwa regionu** | **Adresy IP bramy** |**Adresy IP bramy (likwidowanie)** | **Adresy IP bramy (zlikwidowany)** |
|:----------------|:-------------------------|:-------------------------------------------|:------------------------------------------|
| Australia Środkowa| 20.36.105.0  | | |
| Australia Central2     | 20.36.113.0  | | |
| Australia Wschodnia | 13.75.149.87, 40.79.161.1     |  | |
| Australia Południowo-Wschodnia |191.239.192.109, 13.73.109.251   |  | |
| Brazylia Południowa |191.233.201.8, 191.233.200.16    |  | 104.41.11.5|
| Kanada Środkowa |40.85.224.249  | | |
| Kanada Wschodnia | 40.86.226.166    | | |
| Central US | 23.99.160.139, 52.182.136.37, 52.182.136.38 | 13.67.215.62 | |
| Chiny Wschodnie | 139.219.130.35    | | |
| Chiny Wschodnie 2 | 40.73.82.1  | | |
| Chiny Północne | 139.219.15.17    | | |
| Chiny Północne 2 | 40.73.50.0     | | |
| Azja Wschodnia | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     | | |
| East US |40.71.8.203, 40.71.83.113 |40.121.158.30|191.238.6.43 |
| Wschodnie stany USA 2 | 40.70.144.38, 52.167.105.38  | 52.177.185.181 | |
| Francja Środkowa | 40.79.137.0, 40.79.129.1  | | |
| Francja Południowa | 40.79.177.0     | | |
| Niemcy Środkowe | 51.4.144.100     | | |
| Niemcy Północne | 51.116.56.0 | |
| Niemcy Północne wschód | 51.5.144.179  | | |
| Niemcy Środkowo-Zachodnie | 51.116.152.0 | |
| Indie Środkowe | 104.211.96.159     | | |
| Indie Południowe | 104.211.224.146  | | |
| Indie Zachodnie | 104.211.160.80    | | |
| Japonia Wschodnia | 40.79.192.23, 40.79.184.8 | 13.78.61.196 | |
| Japonia Zachodnia | 191.238.68.11, 40.74.96.6, 40.74.96.7     | 104.214.148.156 | |
| Korea Środkowa | 52.231.17.13   | 52.231.32.42 | |
| Korea Południowa | 52.231.145.3     | 52.231.200.86 | |
| Północno-środkowe stany USA | 52.162.104.35, 52.162.104.36    | 23.96.178.199 | |
| Europa Północna | 52.138.224.6, 52.138.224.7  | 40.113.93.91 |191.235.193.75 |
| Północna Republika Południowej Afryki  | 102.133.152.0    | | |
| Zachodnia Republika Południowej Afryki | 102.133.24.0   | | |
| South Central US |104.214.16.39, 20.45.120.0  |13.66.62.124  |23.98.162.75 |
| Azja Południowo-Wschodnia | 40.78.233.2, 23.98.80.12     | 104.43.15.0 | |
| Szwajcaria Północna | 51.107.56.0 ||
| Szwajcaria Zachodnia | 51.107.152.0| ||
| Środkowy Zjednoczone Emiraty Arabskie | 20.37.72.64  | | |
| Północne Zjednoczone Emiraty Arabskie | 65.52.248.0    | | |
| Południowe Zjednoczone Królestwo | 51.140.184.11   | | |
| Zachodnie Zjednoczone Królestwo | 51.141.8.11  | | |
| Zachodnio-środkowe stany USA | 13.78.145.25     | | |
| West Europe |13.69.105.208, 104.40.169.187 | 40.68.37.158 | 191.237.232.75 |
| Zachodnie stany USA |13.86.216.212, 13.86.217.212 |104.42.238.205  | 23.99.34.75|
| Zachodnie stany USA 2 | 13.66.226.202  | | |
||||

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="what-you-need-to-know-about-this-planned-maintenance"></a>Co musisz wiedzieć o tej planowanej konserwacji?
Jest to zmiana DNS, która sprawia, że jest ona niewidoczna dla klientów. Gdy adres IP dla nazwy FQDN zostanie zmieniony na serwerze DNS, lokalna pamięć podręczna DNS będzie odświeżana w ciągu 5 minut i jest wykonywana automatycznie przez systemy operacyjne. Po odświeżeniu lokalnego DNS wszystkie nowe połączenia będą łączyć się z nowym adresem IP. wszystkie istniejące połączenia pozostaną połączone ze starym adresem IP bez przerwy, dopóki stare adresy IP nie zostaną całkowicie zlikwidowane. Stary adres IP będzie trwać od trzech do czterech tygodni przed zlikwidowaniem; w związku z tym nie należy mieć wpływu na aplikacje klienckie.

### <a name="what-are-we-decommissioning"></a>Co jest likwidowane?
Tylko węzły bramy zostaną zlikwidowane. Gdy użytkownicy łączą się z serwerami, pierwsze zatrzymanie połączenia jest węzłem bramy, zanim połączenie zostanie przekazane do serwera. Likwidowanie starych bram bramy (nie pierścieni dzierżawców, w których serwer jest uruchomiony) zapoznaj się z [architekturą połączeń](#connectivity-architecture) , aby uzyskać więcej informacji.

### <a name="how-can-you-validate-if-your-connections-are-going-to-old-gateway-nodes-or-new-gateway-nodes"></a>Jak można sprawdzić, czy Twoje połączenia przechodzą do starych węzłów bramy czy z nowych węzłów bramy?
Wyślij polecenie ping do nazwy FQDN serwera, na przykład  ``ping xxx.postgres.database.azure.com`` . Jeśli zwrotny adres IP jest jednym z adresów IP wymienionych w obszarze Address Gateway (likwidowanie) w dokumencie powyżej, oznacza to, że połączenie przechodzi przez starą bramę. Contrarily, jeśli zwrotny adres IP jest jednym z adresów IP wymienionych w obszarze addressed Address Gateways, oznacza to, że połączenie przechodzi przez nową bramę.

Możesz również testować przez [PSPing](https://docs.microsoft.com/sysinternals/downloads/psping) lub TCPPing serwera bazy danych z aplikacji klienckiej przy użyciu portu 3306 i upewnić się, że zwrotny adres IP nie jest jednym z likwidowanych adresów IP.

### <a name="how-do-i-know-when-the-maintenance-is-over-and-will-i-get-another-notification-when-old-ip-addresses-are-decommissioned"></a>Jak mogę wiedzieć, kiedy konserwacja jest w trybie failover i otrzymam kolejne powiadomienie, gdy stare adresy IP zostaną zlikwidowane?
Otrzymasz wiadomość e-mail z informacją o tym, kiedy zaczniemy pracę z konserwacją. Konserwacja może trwać do miesiąca w zależności od liczby serwerów, które muszą zostać zmigrowane w regionach Al. Przygotuj klienta do nawiązania połączenia z serwerem bazy danych przy użyciu nazwy FQDN lub nowego adresu IP z powyższej tabeli. 

### <a name="what-do-i-do-if-my-client-applications-are-still-connecting-to-old-gateway-server-"></a>Co zrobić, jeśli moje aplikacje klienckie nadal nawiązują połączenie ze starym serwerem bramy?
Oznacza to, że aplikacje nawiązują połączenie z serwerem przy użyciu statycznego adresu IP zamiast nazwy FQDN. Przejrzyj parametry połączenia i ustawienie puli połączeń, ustawienie AKS, a nawet w kodzie źródłowym.

### <a name="is-there-any-impact-for-my-application-connections"></a>Czy istnieją jakieś skutki dla połączeń aplikacji?
Ta konserwacja jest tylko zmianą systemu DNS, dlatego jest niewidoczna dla klienta. Po odświeżeniu pamięci podręcznej DNS na kliencie (automatycznie przez system operacyjny) wszystkie nowe połączenia będą łączyć się z nowym adresem IP, a wszystkie istniejące połączenia będą nadal działać do czasu całkowitego zlikwidowania starego adresu IP, co zwykle kilku tygodni później. Logika ponowień nie jest wymagana w tym przypadku, ale dobrze jest zobaczyć, że aplikacja ma skonfigurowaną logikę ponowień. Użyj nazwy FQDN, aby nawiązać połączenie z serwerem bazy danych, lub Włącz listę nowych "adresów IP bramy" w parametrach połączenia aplikacji.
Ta operacja konserwacji nie spowoduje porzucenia istniejących połączeń. Powoduje to jedynie, że nowe żądania połączenia są kierowane do nowego pierścienia bramy.

### <a name="can-i-request-for-a-specific-time-window-for-the-maintenance"></a>Czy mogę zażądać określonego przedziału czasu dla konserwacji? 
Ponieważ migracja powinna być przejrzysta i nie ma wpływu na łączność klienta, oczekuje się, że nie będzie żadnego problemu dla większości użytkowników. Przejrzyj swoją aplikację, a następnie upewnij się, że do nawiązania połączenia z serwerem bazy danych jest używana nazwa FQDN, lub Włącz listę nowych "adresów IP bramy" w parametrach połączenia aplikacji.

### <a name="i-am-using-private-link-will-my-connections-get-affected"></a>Korzystam z linku prywatnego, czy moje połączenia zostaną uwzględnione?
Nie, to jest wycofanie sprzętu bramy i brak relacji do prywatnego linku lub prywatnych adresów IP, będzie miało wpływ tylko na publiczne adresy IP wymienione w ramach likwidowania adresów IP.



## <a name="next-steps"></a>Następne kroki

* [Tworzenie reguł zapory Azure Database for PostgreSQL i zarządzanie nimi za pomocą Azure Portal](./howto-manage-firewall-using-portal.md)
* [Tworzenie reguł zapory Azure Database for PostgreSQL przy użyciu interfejsu wiersza polecenia platformy Azure i zarządzanie nimi](./howto-manage-firewall-using-cli.md)