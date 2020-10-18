---
title: Przegląd — Azure Database for MySQL — elastyczny serwer
description: Dowiedz się więcej o Azure Database for MySQL elastycznym serwerze, usłudze relacyjnej bazy danych w chmurze firmy Microsoft w oparciu o wersję MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/21/2020
ms.openlocfilehash: 951de77f2ef10a06ff03801872cebcef088172b7
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167053"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>Serwer elastyczny Azure Database for MySQL (wersja zapoznawcza)

Azure Database for MySQL obsługiwane przez program MySQL Community Edition jest dostępny w dwóch trybach wdrożenia:
- Pojedynczy serwer 
- Serwer elastyczny (wersja zapoznawcza)

Ten artykuł zawiera omówienie i wprowadzenie do podstawowych pojęć związanych z elastycznym modelem wdrażania serwera. Aby uzyskać informacje na temat decydowania o opcji wdrożenia odpowiedniej dla obciążenia, zobacz [Wybieranie odpowiedniego serwera MySQL na platformie Azure](./../select-right-deployment-type.md).

## <a name="overview"></a>Omówienie

Azure Database for MySQL elastyczny serwer to w pełni zarządzana usługa bazy danych, która umożliwia dokładniejszą kontrolę i elastyczność w zakresie funkcji zarządzania bazami danych i ustawień konfiguracji. Ogólnie rzecz biorąc, usługa zapewnia większą elastyczność i dostosowanie konfiguracji serwera na podstawie wymagań użytkownika. Elastyczna architektura serwera umożliwia użytkownikom wybór wysokiej dostępności w ramach strefy pojedynczej dostępności i wielu stref dostępności. Elastyczne serwery zapewniają również lepsze funkcje optymalizacji kosztów z możliwością zatrzymywania/uruchamiania serwera i magazynowych jednostek SKU, idealnym rozwiązaniem w przypadku obciążeń, które nie wymagają ciągłej pojemności obliczeniowej. Usługa obecnie obsługuje wersję społeczności MySQL 5,7. Usługa jest obecnie dostępna w wersji zapoznawczej, już dziś w wielu [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/).

Serwery elastyczne najlepiej nadają się do 
- Rozwój aplikacji wymagający lepszej kontroli i dostosowań.
- Strefa nadmiarowa wysokiej dostępności
- Zarządzane okna obsługi

![Elastyczny diagram koncepcyjny serwera](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="high-availability-within-and-across-availability-zones"></a>Wysoka dostępność w strefach dostępności i w poprzek

Elastyczny model wdrażania serwera jest przeznaczony do obsługi wysokiej dostępności w ramach strefy pojedynczej dostępności i wielu stref dostępności. Architektura oddziela zasoby obliczeniowe i magazyn. Aparat bazy danych działa na maszynie wirtualnej, podczas gdy pliki danych znajdują się w usłudze Azure Storage. Magazyn przechowuje trzy lokalnie nadmiarowe synchroniczne kopie plików bazy danych, zapewniając trwałość danych przez cały czas. 

W ramach pojedynczej strefy dostępności, jeśli serwer ulegnie awarii z powodu planowanych lub nieplanowanych zdarzeń, usługa zachowuje wysoką dostępność serwerów za pomocą następującej zautomatyzowanej procedury:

1. Zostanie zainicjowana Nowa maszyna wirtualna obliczeniowa.
2. Magazyn z plikami danych jest mapowany na nową maszynę wirtualną
3. Aparat bazy danych MySQL jest przełączany w tryb online na nowej maszynie wirtualnej.
4. Aplikacje klienckie mogą ponownie nawiązać połączenie, gdy serwer będzie gotowy do akceptowania połączeń.
   
:::image type="content" source="media/overview/2-flexible-server-architecture.png" alt-text="Diagram koncepcyjny wysokiej dostępności pojedynczej strefy"::: 

Jeśli skonfigurowano nadmiarową wysoką dostępność strefy, usługa inicjuje i utrzymuje serwer rezerwy dynamicznej w strefie dostępności w tym samym regionie świadczenia usługi Azure. Zmiany danych na serwerze źródłowym są synchronicznie replikowane na serwerze rezerwy w celu zapewnienia zerowej utraty danych. Gdy zostanie wyzwolone planowane lub nieplanowane zdarzenie trybu failover z nadmiarową strefą o nieodpowiedniej dostępności, serwer rezerwy natychmiast przejdzie w tryb online i jest dostępny do przetwarzania przychodzących transakcji. Typowe zakresy czasu pracy w trybie failover z 60-120 sekund. Dzięki temu usługa może obsługiwać wysoką dostępność i zapewniać ulepszoną odporność z tolerancją dla awarii pojedynczej strefy dostępności w danym regionie świadczenia usługi Azure. 

Zobacz [pojęcia wysokiej dostępności](concepts-high-availability.md) , aby uzyskać więcej szczegółów.

:::image type="content" source="media/overview/3-flexible-server-overview-zone-redundant-ha.png" alt-text="Diagram koncepcyjny wysokiej dostępności pojedynczej strefy"::: 

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatyczne stosowanie poprawek za pomocą zarządzanego okna obsługi

Usługa wykonuje zautomatyzowane stosowanie poprawek podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych. Poprawki obejmują aktualizacje zabezpieczeń i oprogramowania. W przypadku aparatu MySQL uaktualnienia wersji pomocniczej są również dołączone jako część planowanej wersji konserwacji. Użytkownicy mogą konfigurować harmonogram stosowania poprawek, aby był zarządzany przez system lub definiować własny harmonogram. W harmonogramie konserwacji poprawka zostanie zastosowana i serwer może wymagać ponownego uruchomienia w ramach procesu poprawek, aby ukończyć aktualizację. Zgodnie z harmonogramem niestandardowym użytkownicy mogą przewidywalnić swój cykl poprawek i wybrać okno obsługi z minimalnym wpływem na firmę. Ogólnie rzecz biorąc usługa jest zgodna z harmonogramem wydań w ramach ciągłej integracji i wydania.

Aby uzyskać więcej informacji, zobacz [zaplanowana konserwacja](concepts-maintenance.md) . 

## <a name="automatic-backups"></a>Automatyczne kopie zapasowe

Elastyczna usługa serwerowa automatycznie tworzy kopie zapasowe serwera i przechowuje je w ramach użytkownika skonfigurowanego lokalnie nadmiarowy lub geograficznie nadmiarowy. Za pomocą kopii zapasowych można przywrócić serwer do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Czas przechowywania można opcjonalnie skonfigurować do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES. 

Aby dowiedzieć się więcej, zobacz [pojęcia dotyczące kopii zapasowych](concepts-backup-restore.md) .

## <a name="network-isolation"></a>Izolacja sieci

Dostępne są dwie opcje sieciowe umożliwiające nawiązanie połączenia z serwerem elastycznym Azure Database for MySQL. Opcje są **dostęp prywatny (Integracja z siecią wirtualną)** i **dostęp publiczny (dozwolone adresy IP)**. 

* **Dostęp prywatny (Integracja z siecią wirtualną)** — możesz wdrożyć elastyczny serwer do [Virtual Network platformy Azure](../../virtual-network/virtual-networks-overview.md). Sieci wirtualne platformy Azure zapewniają prywatną i bezpieczną komunikację sieciową. Zasoby w sieci wirtualnej mogą komunikować się za pomocą prywatnych adresów IP.

   Wybierz opcję Integracja z siecią wirtualną, jeśli chcesz skorzystać z następujących możliwości:
   * Łączenie z zasobów platformy Azure w tej samej sieci wirtualnej z elastycznym serwerem przy użyciu prywatnych adresów IP
   * Używanie sieci VPN lub ExpressRoute do nawiązywania połączeń z zasobów spoza platformy Azure z serwerem elastycznym
   * Brak publicznego punktu końcowego

* **Dostęp publiczny (dozwolone adresy IP)** — można wdrożyć elastyczny serwer z publicznym punktem końcowym. Publiczny punkt końcowy jest publicznie rozpoznawalnym adresem DNS. Fraza "dozwolone adresy IP" odnosi się do zakresu adresów IP wybranych do udzielenia uprawnienia dostępu do serwera. Te uprawnienia są nazywane **regułami zapory**.

Zobacz [pojęcia dotyczące sieci](concepts-networking.md) , aby dowiedzieć się więcej.

## <a name="adjust-performance-and-scale-within-seconds"></a>Dostosowanie wydajności i skalowania w kilka sekund

Elastyczna usługa serwerowa jest dostępna w trzech warstwach SKU: możliwość przetworzenia, Ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci. Warstwa z możliwością naliczania jest Najlepsza dla obciążeń związanych z programowaniem i niskim kosztem współbieżności, które nie wymagają ciągłego stałej pojemności obliczeniowej. Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci są lepiej dopasowane do obciążeń produkcyjnych wymagających dużej współbieżności, skali i przewidywalnej wydajności. Możesz utworzyć swoją pierwszą aplikację na małej bazie danych za kilka dolarów miesięcznie, a następnie bezproblemowo dostosować skalę, aby zaspokoić potrzeby rozwiązania. Skalowanie magazynu jest w trybie online i obsługuje automatyczne przyrosty pamięci masowej. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Płacisz tylko za zasoby, z których korzystasz. 

Zobacz [pojęcia dotyczące obliczeń i magazynu](concepts-compute-storage.md) , aby dowiedzieć się więcej.

## <a name="stopstart-server-to-optimize-cost"></a>Zatrzymaj/Uruchom serwer, aby zoptymalizować koszt

Elastyczna usługa serwerowa umożliwia zatrzymanie i uruchomienie serwera na żądanie w celu zoptymalizowania kosztów. Rozliczanie w warstwie obliczeniowej jest zatrzymywane natychmiast po zatrzymaniu serwera. Dzięki temu można mieć znaczny koszt oszczędności podczas opracowywania, testowania i dla przewidywalnych obciążeń produkcyjnych. Serwer pozostaje w stanie zatrzymania przez siedem dni, chyba że zostanie jeszcze wcześniej uruchomiony. 

Aby dowiedzieć się więcej, zobacz [pojęcia dotyczące serwerów](concept-servers.md) . 

## <a name="enterprise-grade-security-and-privacy"></a>Bezpieczeństwo i prywatność klasy korporacyjnej

Elastyczna usługa serwerowa używa zatwierdzonego modułu kryptograficznego FIPS 140-2 do szyfrowania magazynu danych w spoczynku. Dane, w tym kopie zapasowe i pliki tymczasowe utworzone podczas wykonywania zapytań, są szyfrowane. Usługa używa szyfru AES 256-bitowego zawartego w szyfrowaniu usługi Azure Storage, a klucze mogą być zarządzane przez system (domyślnie). 

Usługa domyślnie szyfruje dane z użyciem zabezpieczeń warstwy transportowej. Elastyczne serwery obsługują tylko połączenia szyfrowane za pomocą Transport Layer Security (TLS 1,2) i wszystkie połączenia przychodzące z protokołem TLS 1,0 i TLS 1,1 zostaną odrzucone. 

Aby dowiedzieć się więcej, zobacz [jak używać szyfrowanych połączeń z elastycznymi serwerami](https://docs.mongodb.com/manual/tutorial/configure-ssl) .

Elastyczne serwery umożliwiają pełny dostęp do serwerów za pomocą integracji z [usługą Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) (VNET). Serwery w usłudze Azure Virtual Network mogą być osiągalne i połączone tylko za pomocą prywatnych adresów IP. Dzięki integracji z siecią wirtualną dostęp publiczny jest zabroniony i serwery nie można osiągnąć przy użyciu publicznych punktów końcowych. 

Zobacz [pojęcia dotyczące sieci](concepts-networking.md) , aby dowiedzieć się więcej.


## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów

Elastyczna usługa serwera jest wyposażona w wbudowaną funkcję monitorowania wydajności i alertów. Wszystkie metryki platformy Azure mają częstotliwość jednominutową, a każda Metryka zapewnia 30-dniową historię. Można skonfigurować alerty dotyczące metryk. Usługa udostępnia metryki serwera hosta do monitorowania wykorzystania zasobów i umożliwia konfigurowanie wolnych dzienników zapytań. Korzystając z tych narzędzi, można szybko zoptymalizować obciążenia i skonfigurować serwer w celu uzyskania najlepszej wydajności. 

Aby dowiedzieć się więcej, zobacz [zagadnienia dotyczące monitorowania](concepts-monitoring.md) .

## <a name="migration"></a>Migracja

Usługa uruchamia wersję społeczności MySQL. Zapewnia to pełną zgodność aplikacji i wymaga minimalnego kosztu refaktoryzacji w celu przeprowadzenia migracji istniejącej aplikacji opracowanej na aparat MySQL do jednego serwera. Migrację do pojedynczego serwera można przeprowadzić przy użyciu jednej z następujących opcji:

- **Zrzuć i Przywróć** — w przypadku migracji w trybie offline, w których użytkownicy mogą uzyskać pewien przestój, zrzuty i przywracanie przy użyciu narzędzi społeczności, takich jak mysqldump/Dumper, mogą zapewnić najszybszy sposób migracji. Aby uzyskać szczegółowe informacje, zobacz Migrowanie przy użyciu zrzutów i przywracania. 
- **Azure Database Migration Service** — w celu zapewnienia bezproblemowej i uproszczonej migracji na pojedynczy serwer z minimalnym przestojem, [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online) można wykorzystać. 

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Jedną z zalet korzystania z obciążeń na platformie Azure jest globalny zasięg. Elastyczny serwer dla Azure Database for MySQL jest dostępny dzisiaj w następujących regionach świadczenia usługi Azure:

- West Europe
- Europa Północna
- Południowe Zjednoczone Królestwo
- Wschodnie stany USA 2
- Zachodnie stany USA 2
- Central US
- East US
- Kanada Środkowa
- Southeast Asia
- Korea Środkowa
- Japonia Wschodnia
- Australia Wschodnia

Pracujemy nad dodaniem nowych serwerów wkrótce.

## <a name="contacts"></a>Kontakty
W przypadku pytań lub sugestii, które mogą znajdować się na Azure Database for MySQL elastycznym serwerze, Wyślij wiadomość e-mail do zespołu Azure Database for MySQL ([ @Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Ten adres e-mail nie jest aliasem pomocy technicznej.

Ponadto, w zależności od potrzeb, należy wziąć pod uwagę następujące punkty kontaktowe:

- Aby skontaktować się z pomocą techniczną platformy Azure, [wyślij zgłoszenie z witryny Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Następne kroki
Teraz, po zapoznaniu się z wprowadzeniem do Azure Database for MySQL trybu wdrożenia pojedynczego serwera, możesz:

- Utwórz swój pierwszy serwer. 
  - [Utwórz Azure Database for MySQL elastyczny serwer przy użyciu Azure Portal](quickstart-create-server-portal.md)
  - [Tworzenie Azure Database for MySQL elastycznego serwera przy użyciu interfejsu wiersza polecenia platformy Azure](quickstart-create-server-cli.md)
  - [Zarządzanie serwerem elastycznym Azure Database for MySQL przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-manage-server-portal.md)

- Kompiluj swoją pierwszą aplikację przy użyciu preferowanego języka:
  - [Python](connect-python.md)
  - [Obsługa](connect-php.md)
