---
title: Omówienie — Azure Database for MySQL — serwer elastyczny
description: Dowiedz się więcej Azure Database for MySQL serwera elastycznego, usługi relacyjnej bazy danych w chmurze firmy Microsoft opartej na programie MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/21/2020
ms.openlocfilehash: e3259cddc3790f92f56d7f59783cd66a2502b704
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813330"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL — serwer elastyczny (wersja zapoznawcza)

Azure Database for MySQL obsługiwane przez program MySQL Community Edition są dostępne w dwóch trybach wdrażania:
- Pojedynczy serwer 
- Serwer elastyczny (wersja zapoznawcza)

Ten artykuł zawiera omówienie i wprowadzenie do podstawowych pojęć związanych z elastycznym modelem wdrażania serwera. Aby dowiedzieć się, jak zdecydować, która opcja wdrażania jest odpowiednia dla Twojego obciążenia, zobacz wybieranie odpowiedniej opcji serwera [MySQL na platformie Azure.](./../select-right-deployment-type.md)

## <a name="overview"></a>Omówienie

Azure Database for MySQL Elastyczny serwer to w pełni zarządzana usługa bazy danych zaprojektowana w celu zapewnienia bardziej szczegółowej kontroli i elastyczności funkcji zarządzania bazami danych i ustawień konfiguracji. Ogólnie rzecz biorąc, usługa zapewnia większą elastyczność i dostosowywanie konfiguracji serwera w zależności od wymagań użytkownika. Elastyczna architektura serwera umożliwia użytkownikom wybór wysokiej dostępności w jednej strefie dostępności i w wielu strefach dostępności. Serwery elastyczne zapewniają również lepsze mechanizmy kontroli optymalizacji kosztów z możliwością zatrzymania/uruchomienia serwera i jednostki SKU z możliwością serii, idealne dla obciążeń, które nie wymagają ciągłej pełnej pojemności obliczeniowej. Usługa obsługuje obecnie wersje community programu MySQL 5.7 i 8.0. Usługa jest obecnie dostępna w wersji zapoznawczej w wielu różnych regionach świadczenia [usługi Azure.](https://azure.microsoft.com/global-infrastructure/services/)

Serwery elastyczne najlepiej nadają się do 
- Rozwój aplikacji wymaga lepszej kontroli i dostosowywania.
- Strefowo nadmiarowa wysoka dostępność
- Okna konserwacji zarządzanej

![Diagram koncepcyjny serwera elastycznego](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="high-availability-within-and-across-availability-zones"></a>Wysoka dostępność w strefach dostępności i między strefami dostępności

Model wdrażania serwera elastycznego zaprojektowano w celu obsługi wysokiej dostępności w jednej strefie dostępności i w wielu strefach dostępności. Architektura oddziela zasoby obliczeniowe i magazynowe. Aparat bazy danych działa na maszynie wirtualnej, podczas gdy pliki danych znajdują się w usłudze Azure Storage. Magazyn przechowuje trzy lokalnie nadmiarowe synchroniczne kopie plików bazy danych, zapewniając trwałość danych przez cały czas. 

W ramach jednej strefy dostępności, jeśli serwer nie działa z powodu planowanych lub nieplanowanych zdarzeń, usługa utrzymuje wysoką dostępność serwerów przy użyciu następującej zautomatyzowanej procedury:

1. Aprowizowana jest nowa obliczeniowa maszyna wirtualna.
2. Magazyn z plikami danych jest mapowany na nową maszynę wirtualną
3. Aparat bazy danych MySQL zostanie w trybie online na nowej maszynie wirtualnej.
4. Aplikacje klienckie mogą ponownie nawiązać połączenie, gdy serwer będzie gotowy do akceptowania połączeń.
   
:::image type="content" source="media/overview/2-flexible-server-architecture.png" alt-text="Diagram koncepcyjny wysokiej dostępności pojedynczej strefy"::: 

Jeśli skonfigurowano strefowo nadmiarową wysoką dostępność, usługa apowiuje i utrzymuje serwer rezerwy gorącej w strefie dostępności w tym samym regionie świadczenia usługi Azure. Zmiany danych na serwerze źródłowym są replikowane synchronicznie do serwera rezerwowego w celu zapewnienia zerowej utraty danych. W przypadku strefowo nadmiarowej wysokiej dostępności po wyzwoleniu zdarzenia planowanego lub nieplanowanego trybu failover serwer rezerwowy przechodzi natychmiast w tryb online i jest dostępny do przetwarzania transakcji przychodzących. Typowy czas pracy w trybu failover wynosi od 60 do 120 sekund. Dzięki temu usługa może obsługiwać wysoką dostępność i zapewniać lepszą odporność z tolerancją na awarie pojedynczej strefy dostępności w danym regionie świadczenia usługi Azure. 

Aby [uzyskać więcej informacji, zobacz pojęcia dotyczące](concepts-high-availability.md) wysokiej dostępności.

:::image type="content" source="media/overview/3-flexible-server-overview-zone-redundant-ha.png" alt-text="Diagram koncepcyjny strefowo nadmiarowej wysokiej dostępności"::: 

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatyczne stosowanie poprawek za pomocą okna konserwacji zarządzanej

Usługa wykonuje automatyczne stosowanie poprawek podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych. Poprawki obejmują aktualizacje zabezpieczeń i oprogramowania. W przypadku aparatu MySQL uaktualnienia wersji pomocniczej są również uwzględniane w ramach wydania planowanej konserwacji. Użytkownicy mogą skonfigurować harmonogram poprawek, aby był zarządzany przez system, lub zdefiniować harmonogram niestandardowy. Podczas harmonogramu konserwacji jest stosowana poprawka, a serwer może wymagać ponownego uruchomienia w ramach procesu stosowania poprawek w celu ukończenia aktualizacji. Zgodnie z harmonogramem niestandardowym użytkownicy mogą przewidywać cykl poprawek i wybierać okno obsługi z minimalnym wpływem na firmę. Ogólnie rzecz biorąc, usługa jest zgodna z miesięcznym harmonogramem wydania w ramach ciągłej integracji i wydania.

Aby [uzyskać więcej informacji,](concepts-maintenance.md) zobacz Planowana konserwacja. 

## <a name="automatic-backups"></a>Automatyczne kopie zapasowe

Usługa serwera elastycznego automatycznie tworzy kopie zapasowe serwera i zapisuje je w magazynie lokalnie nadmiarowym lub geograficznie nadmiarowym skonfigurowanym przez użytkownika. Kopie zapasowe mogą służyć do przywracania serwera do dowolnego punktu w czasie w okresie przechowywania kopii zapasowej. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Okres przechowywania można opcjonalnie skonfigurować do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES. 

Aby dowiedzieć [się więcej,](concepts-backup-restore.md) zobacz Pojęcia dotyczące kopii zapasowych.

## <a name="network-isolation"></a>Izolacja sieci

Dostępne są dwie opcje sieci służące do nawiązywania połączenia z Azure Database for MySQL elastycznym serwerem. Dostępne opcje to **dostęp prywatny (integracja z siecią wirtualną)** i **dostęp publiczny (dozwolone adresy IP).** 

* **Dostęp prywatny (integracja z siecią wirtualną)** — serwer elastyczny można wdrożyć w usłudze [Azure Virtual Network.](../../virtual-network/virtual-networks-overview.md) Sieci wirtualne platformy Azure zapewniają prywatną i bezpieczną komunikację sieciową. Zasoby w sieci wirtualnej mogą komunikować się za pośrednictwem prywatnych adresów IP.

   Wybierz opcję Integracja z siecią wirtualną, jeśli chcesz mieć następujące możliwości:
   * Nawiązywanie połączenia z zasobów platformy Azure w tej samej sieci wirtualnej z serwerem elastycznym przy użyciu prywatnych adresów IP
   * Nawiązywanie połączenia z zasobów spoza platformy Azure do serwera elastycznego przy użyciu sieci VPN lub usługi ExpressRoute
   * Brak publicznego punktu końcowego

* **Dostęp publiczny (dozwolone adresy IP)** — możesz wdrożyć serwer elastyczny z publicznym punktem końcowym. Publiczny punkt końcowy jest publicznie rozpoznalnym adresem DNS. Fraza "dozwolone adresy IP" odnosi się do zakresu adresów IP, które chcesz nadać uprawnienia dostępu do serwera. Te uprawnienia są nazywane **regułami zapory.**

Aby dowiedzieć [się więcej,](concepts-networking.md) zobacz Pojęcia dotyczące sieci.

## <a name="adjust-performance-and-scale-within-seconds"></a>Dostosowanie wydajności i skalowania w kilka sekund

Usługa serwera elastycznego jest dostępna w trzech warstwach SKU: z możliwością Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci. Warstwa z możliwością rozbudowy najlepiej nadaje się do projektowania w niskich kosztach i obciążeń o niskiej współbieżności, które nie wymagają ciągłej pełnej pojemności obliczeniowej. Urządzenia Ogólnego przeznaczenia zoptymalizowane pod kątem pamięci są lepiej dopasowane do obciążeń produkcyjnych wymagających wysokiej współbieżności, skalowania i przewidywalnej wydajności. Możesz utworzyć pierwszą aplikację na małej bazie danych za kilka dolarów miesięcznie, a następnie bezproblemowo dostosować skalę do potrzeb rozwiązania. Skalowanie magazynu jest w trybie online i obsługuje automatyczne zwiększenie magazynu. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Płacisz tylko za zużyte zasoby. 

Aby dowiedzieć [się więcej,](concepts-compute-storage.md) zobacz Pojęcia związane z obliczeniami i magazynem.

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>Skalowanie w górę obciążenia odczytu z maksymalnie 10 replikami do odczytu

MySQL to jeden z popularnych aparatów baz danych do uruchamiania internetowych i mobilnych aplikacji. Wielu naszych klientów korzysta z niej w swoich usługach edukacyjnych online, usługach przesyłania strumieniowego wideo, cyfrowych rozwiązaniach do płatności, platformach handlu elektronicznego, usługach gier, portalach wiadomości, witrynach internetowych dla instytucji rządowych i służby zdrowia. Te usługi są wymagane do obsługi i skalowania w przypadku wzrostu ruchu w Internecie lub aplikacji mobilnej.

Po stronie aplikacji aplikacja jest zwykle opracowywana w języku Java lub php i migrowana do uruchamiania w zestawach skalowania maszyn wirtualnych platformy [Azure](../../virtual-machine-scale-sets/overview.md)lub usłudze Azure App Services albo jest konteneryzowana do uruchamiania w usłudze   Azure Kubernetes Service [](../../app-service/overview.md)    [(AKS).](../../aks/intro-kubernetes.md) W przypadku zestawu skalowania maszyn wirtualnych, usługi App Service lub AKS jako podstawowej infrastruktury skalowanie aplikacji jest upraszczane przez natychmiastowe aprowizowanie nowych maszyn wirtualnych i replikowanie bez stanowych składników aplikacji w celu obsługi żądań, ale często baza danych jest wąskim gardłem jako scentralizowanym składnikiem stanowym.

Funkcja repliki do odczytu umożliwia replikowanie danych z Azure Database for MySQL serwera elastycznego na serwer tylko do odczytu. Można replikować z serwera źródłowego do **maksymalnie 10 replik.** Repliki są aktualizowane asynchronicznie przy użyciu natywnej technologii replikacji aparatu MySQL opartej na pozycji pliku dziennika binarnego [(binlog).](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) Możesz użyć rozwiązania serwera proxy usługi równoważenia obciążenia, takiego jak [ProxySQL,](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) aby bezproblemowo skalować obciążenie aplikacji w celu odczytu replik bez żadnych kosztów refaktoryzacji aplikacji. 

Aby dowiedzieć [się więcej, zobacz Read Replica concepts](concepts-read-replicas.md) (Pojęcia dotyczące repliki do odczytu). 


## <a name="stopstart-server-to-optimize-cost"></a>Zatrzymywanie/uruchamianie serwera w celu optymalizacji kosztów

Elastyczna usługa serwera umożliwia zatrzymywanie i uruchamianie serwera na żądanie w celu zoptymalizowania kosztów. Rozliczanie warstwy obliczeniowej jest zatrzymywane natychmiast po zatrzymaniu serwera. Dzięki temu można znacznie zaoszczędzić na kosztach podczas opracowywania, testowania i w przypadku ograniczonych w czasie przewidywalnych obciążeń produkcyjnych. Serwer pozostaje w stanie zatrzymania przez siedem dni, chyba że zostanie ponownie uruchomiony wcześniej. 

Aby dowiedzieć [się więcej,](concept-servers.md) zobacz Server concepts (Pojęcia dotyczące serwera). 

## <a name="enterprise-grade-security-and-privacy"></a>Zabezpieczenia i prywatność klasy korporacyjnej

Usługa serwera elastycznego używa modułu kryptograficznego zweryfikowanego w standardzie FIPS 140-2 do szyfrowania magazynowanych danych. Dane, w tym kopie zapasowe i pliki tymczasowe utworzone podczas uruchamiania zapytań, są szyfrowane. Usługa używa 256-bitowego szyfru AES uwzględnionego w szyfrowaniu usługi Azure Storage, a klucze mogą być zarządzane przez system (ustawienie domyślne). 

Usługa szyfruje dane w ruchu przy użyciu domyślnie wymuszonych zabezpieczeń warstwy transportu. Serwery elastyczne obsługują tylko połączenia szyfrowane przy użyciu Transport Layer Security (TLS 1.2), a wszystkie połączenia przychodzące z TLS 1.0 i TLS 1.1 zostaną odrzucone. 

Aby [dowiedzieć się więcej, zobacz, jak używać szyfrowanych](https://docs.mongodb.com/manual/tutorial/configure-ssl) połączeń z serwerami elastycznymi.

Serwery elastyczne umożliwiają pełny prywatny dostęp do serwerów przy użyciu [integracji z siecią wirtualną](../../virtual-network/virtual-networks-overview.md) platformy Azure. Serwery w sieci wirtualnej platformy Azure są dostępne i połączone tylko za pośrednictwem prywatnych adresów IP. Dzięki integracji z siecią wirtualną dostęp publiczny jest odmawiany i nie można uzyskać dostępu do serwerów przy użyciu publicznych punktów końcowych. 

Aby dowiedzieć [się więcej,](concepts-networking.md) zobacz Pojęcia dotyczące sieci.


## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów

Usługa serwera elastycznego jest wyposażona w wbudowane funkcje monitorowania wydajności i alertów. Wszystkie metryki platformy Azure mają częstotliwość co minutę, a każda metryka zawiera historię z 30 dni. Możesz skonfigurować alerty dotyczące metryk. Usługa uwidacznia metryki serwera hosta w celu monitorowania wykorzystania zasobów i umożliwia konfigurowanie dzienników wolnych zapytań. Za pomocą tych narzędzi można szybko zoptymalizować obciążenia i skonfigurować serwer pod kątem najlepszej wydajności. 

Aby dowiedzieć [się więcej,](concepts-monitoring.md) zobacz Pojęcia dotyczące monitorowania.

## <a name="migration"></a>Migracja

Usługa uruchamia wersję community programu MySQL. Zapewnia to pełną zgodność aplikacji i wymaga minimalnego kosztu refaktoryzacji w celu migrowania istniejącej aplikacji opracowanej na aparat mySQL do usługi pojedynczego serwera. Migrację na pojedynczy serwer można przeprowadzić przy użyciu jednej z następujących opcji:

- **Zrzut i przywracanie** — w przypadku migracji offline, w których użytkownicy mogą pozwolić sobie na przestój, zrzucanie i przywracanie przy użyciu narzędzi społeczności, takich jak mysqldump/mydumper, może zapewnić najszybszy sposób migracji. Aby uzyskać szczegółowe informacje, zobacz Migrowanie przy użyciu zrzutu i przywracania. 
- **Azure Database Migration Service** — w celu bezproblemowej i uproszczonej migracji na pojedynczy serwer przy [minimalnych przestojach](../../dms/tutorial-mysql-azure-mysql-online.md) można Azure Database Migration Service wykorzystać. 

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Jedną z zalet uruchamiania obciążenia na platformie Azure jest zasięg globalny. Elastyczny serwer usługi Azure Database for MySQL jest obecnie dostępny w następujących regionach świadczenia usługi Azure:

| Region (Region) | Dostępność | Strefowo nadmiarowa ha | 
| --- | --- | --- |
| West Europe | :heavy_check_mark: | :heavy_check_mark: |
| Europa Północna | :heavy_check_mark: | :heavy_check_mark: |
| Południowe Zjednoczone Królestwo | :heavy_check_mark: | :heavy_check_mark: | 
| Wschodnie stany USA 2 | :heavy_check_mark: | :heavy_check_mark: |
| Zachodnie stany USA 2 | :heavy_check_mark: | :heavy_check_mark: |
| Central US | :heavy_check_mark: | :x: | 
| East US | :heavy_check_mark: | :heavy_check_mark: |
| Kanada Środkowa | :heavy_check_mark: | :x: | 
| Southeast Asia | :heavy_check_mark: | :heavy_check_mark: |
| Korea Środkowa | :heavy_check_mark: | :x: | 
| Japonia Wschodnia | :heavy_check_mark: | :heavy_check_mark: | 
| Australia Wschodnia | :heavy_check_mark: | :heavy_check_mark: |

Pracujemy nad dodaniem nowych regionów wkrótce.

## <a name="contacts"></a>Kontakty
Jeśli masz pytania lub sugestie dotyczące serwera elastycznego Azure Database for MySQL, wyślij wiadomość e-mail do zespołu Azure Database for MySQL ([ @Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Ten adres e-mail nie jest aliasem pomocy technicznej.

Ponadto, w zależności od potrzeb, należy wziąć pod uwagę następujące punkty kontaktowe:

- Aby skontaktować się z pomocą techniczną platformy Azure, [wyślij zgłoszenie z witryny Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Następne kroki
Teraz, po przeczytaniu wprowadzenia do Azure Database for MySQL w trybie wdrażania na jednym serwerze, możesz:

- Utwórz pierwszy serwer. 
  - [Tworzenie serwera Azure Database for MySQL elastycznego przy użyciu Azure Portal](quickstart-create-server-portal.md)
  - [Tworzenie serwera elastycznego Azure Database for MySQL interfejsu wiersza polecenia platformy Azure](quickstart-create-server-cli.md)
  - [Zarządzanie serwerem Azure Database for MySQL elastycznym przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-manage-server-portal.md)

- Skompilowanie pierwszej aplikacji przy użyciu preferowanego języka:
  - [Python](connect-python.md)
  - [Php](connect-php.md)
