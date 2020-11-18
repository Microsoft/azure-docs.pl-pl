---
title: Zarządzaj po migracji
titleSuffix: Azure SQL Database
description: Dowiedz się, jak zarządzać pojedynczymi bazami danych w puli i po migracji do Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: joesackmsft
ms.author: josack
ms.reviewer: sstein
ms.date: 02/13/2019
ms.openlocfilehash: fe49dce276a15d9d7bc8ddaa5618c0e43dec62e9
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841227"
---
# <a name="new-dba-in-the-cloud--managing-azure-sql-database-after-migration"></a>Nowa usługa DBA w chmurze — zarządzanie Azure SQL Database po migracji
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Przechodzenie od tradycyjnego samodzielnego środowiska z własnym zarządzaniem do środowiska PaaSu może wydawać się nieco przeciążeniem. Jako deweloper aplikacji lub DBA o to, aby poznać podstawowe możliwości platformy, które pomogą Ci zapewnić dostępność aplikacji, jej wykonywanie, bezpieczną i odporność — zawsze. Ten artykuł ma na celu dokładne wykonanie. W artykule zwięzłe organizowanie zasobów i przedstawiono wskazówki dotyczące najlepszego korzystania z kluczowych możliwości Azure SQL Database z pojedynczymi i w puli bazach danych w celu zapewnienia wydajnego działania aplikacji i osiągnięcia optymalnych wyników w chmurze. Typowymi odbiorcami tego artykułu będą:

- Ocenia migrację swoich aplikacji do Azure SQL Database — modernizacja aplikacji.
- Są w trakcie migrowania swoich aplikacji — Scenariusz migracji w toku.
- Ostatnio przeprowadzono migrację do Azure SQL Database — Nowa usługa DBA w chmurze.

W tym artykule omówiono niektóre podstawowe cechy Azure SQL Database jako platformę, z której można łatwo korzystać podczas pracy z pojedynczymi bazami danych i bazami danych w puli w pulach elastycznym. Są one następujące:

- Monitorowanie baz danych za pomocą witryny Azure Portal
- Ciągłość biznesowa i odzyskiwanie po awarii (BCDR)
- Zabezpieczenia i zgodność
- Inteligentne monitorowanie i konserwacja bazy danych
- Przenoszenie danych

## <a name="monitor-databases-using-the-azure-portal"></a>Monitorowanie baz danych za pomocą witryny Azure Portal

W [Azure Portal](https://portal.azure.com/)można monitorować wykorzystanie poszczególnych baz danych, wybierając bazę danych i klikając wykres **monitorowania** . Spowoduje to wyświetlenie okna **Metryka**, które możesz zmienić, klikając przycisk **Edytuj wykres**. Dodaj następujące metryki:

- Procent użycia procesora CPU
- Procent użycia jednostek DTU
- Procent użycia operacji we/wy na danych
- Procent użycia rozmiaru bazy danych

Po dodaniu tych metryk można nadal wyświetlać je na wykresie **monitorowania** z więcej informacji w oknie **Metryka** . Wszystkie cztery metryki pokazują średnią wartość procentową wykorzystania względem jednostek **DTU** bazy danych. Aby uzyskać więcej informacji o warstwach usług, zobacz artykuł model [zakupów oparty](service-tiers-dtu.md) na jednostkach DTU i [rdzeń wirtualny model zakupu](service-tiers-vcore.md) .  

![Monitorowanie warstw usług pod względem wydajności bazy danych.](./media/manage-data-after-migrating-to-database/sqldb_service_tier_monitoring.png)

Możesz również skonfigurować alerty dotyczące metryk wydajności. Kliknij przycisk **Dodaj alert** w oknie **Metryka**. Użyj kreatora, aby skonfigurować alert. Istnieje możliwość ustawienia alertu, który będzie wysyłany, gdy metryki przekroczą określony próg lub spadną poniżej określonego progu.

Jeśli na przykład spodziewasz się, że obciążenie bazy danych wzrośnie, możesz skonfigurować alert polegający na wysłaniu wiadomości e-mail, gdy dowolna z metryk wydajności osiągnie 80%. Można to zrobić jako wczesne ostrzeżenie, aby ustalić, kiedy może zajść potrzeba przełączenia do następnego największego rozmiaru obliczeniowego.

Metryki wydajności mogą również pomóc w ustaleniu, czy można obniżyć poziom obniżenia rozmiaru. Załóżmy, że używasz bazy danych, której warstwa i poziom to Standardowa S2, a wszystkie metryki wydajności pokazują, że baza danych wykorzystuje średnio nie więcej niż 10% w określonym czasie. Istnieje prawdopodobieństwo, że baza danych będzie działać równie dobrze na poziomie Standardowa S1. Należy jednak pamiętać o obciążeniach, które zwiększają lub zmieniają się przed podjęciem decyzji o konieczności przejścia do mniejszego rozmiaru.

## <a name="business-continuity-and-disaster-recovery-bcdr"></a>Ciągłość biznesowa i odzyskiwanie po awarii (BCDR)

Ciągłość działania i możliwości odzyskiwania po awarii umożliwiają kontynuowanie prowadzenia działalności firmy, jak zwykle w przypadku awarii. Awaria może być zdarzeniem na poziomie bazy danych (na przykład ktoś niesprawnie odrzuca kluczową tabelę) lub zdarzenie na poziomie centrum danych (katastrofami regionalny, na przykład tsunami).

### <a name="how-do-i-create-and-manage-backups-on-sql-database"></a>Jak mogę tworzyć kopie zapasowe i zarządzać nimi na SQL Database

Nie można tworzyć kopii zapasowych na Azure SQL Database, ponieważ nie jest to konieczne. SQL Database automatycznie tworzy kopie zapasowe baz danych, dzięki czemu nie trzeba już martwić się o planowanie i tworzenie kopii zapasowych oraz zarządzanie nimi. Platforma wykonuje pełną kopię zapasową co tydzień, różnicową kopię zapasową co kilka godzin i kopię zapasową dziennika co 5 minut, aby zapewnić wydajność odzyskiwania po awarii i niewielką utratę danych. Pierwsza pełna kopia zapasowa odbywa się zaraz po utworzeniu bazy danych. Te kopie zapasowe są dostępne przez pewien czas o nazwie "okres przechowywania" i różnią się w zależności od wybranej warstwy usług. SQL Database zapewnia możliwość przywracania do dowolnego punktu w czasie w tym okresie przechowywania przy użyciu funkcji [odzyskiwania do punktu w czasie (kopie)](recovery-using-backups.md#point-in-time-restore).

|Warstwa usługi|Okres przechowywania w dniach|
|---|:---:|
|Podstawowy|7|
|Standardowa|35|
|Premium|35|
|||

Ponadto funkcja [długoterminowego przechowywania (LTR)](long-term-retention-overview.md) umożliwia przechowywanie plików kopii zapasowych przez dłuższy czas, przez maksymalnie 10 lat, a także przywracanie danych z tych kopii zapasowych w dowolnym momencie w tym okresie. Ponadto kopie zapasowe bazy danych są przechowywane w magazynie replikowanym geograficznie w celu zapewnienia odporności z katastrofami regionalnego. Możesz również przywrócić te kopie zapasowe w dowolnym regionie świadczenia usługi Azure w dowolnym momencie w okresie przechowywania. Zobacz temat [ciągłość działania — Omówienie](business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="how-do-i-ensure-business-continuity-in-the-event-of-a-datacenter-level-disaster-or-regional-catastrophe"></a>Jak mogę zapewnić ciągłość działania w przypadku awarii na poziomie centrum danych lub regionalnej katastrofami

Ponieważ kopie zapasowe bazy danych są przechowywane w magazynie replikowanym geograficznie, aby zapewnić, że w przypadku awarii regionalnej można przywrócić kopię zapasową do innego regionu platformy Azure. Jest to nazywane przywracaniem geograficznym. Cel punktu odzyskiwania jest zwykle < 1 godzina i ERT (szacowany czas odzyskiwania — kilka minut na godziny).

W przypadku baz danych o kluczowym znaczeniu usługa Azure SQL Database oferuje aktywną replikację geograficzną. Zasadniczo polega to na tworzeniu kopii zapasowej z replikacją geograficzną w innym regionie. Na przykład jeśli baza danych jest początkowo hostowana w regionie zachodnie stany USA, i chcesz uzyskać odporność na awarie regionalne. Utworzysz aktywną replikę geograficzną bazy danych w regionie zachodnie stany USA, aby powiedzieć Wschodnie stany USA. Gdy Calamity się na zachodnie stany USA, możesz przejść do trybu failover w regionie Wschodnie stany USA. Konfigurowanie ich w grupie automatycznej pracy awaryjnej jest jeszcze lepsze, ponieważ gwarantuje to, że baza danych zostanie automatycznie przełączona w tryb failover do pomocniczej w regionie Wschodnie stany USA w przypadku awarii. Cel punktu odzyskiwania wynosi < 5 sekund, a ERT < 30 sekund.

Jeśli grupa autotrybu failover nie zostanie skonfigurowana, aplikacja musi aktywnie monitorować awarię i inicjować przejście w tryb failover do pomocniczej bazy danych. Można utworzyć maksymalnie 4 takie aktywne repliki geograficzne w różnych regionach świadczenia usługi Azure. Jest ona jeszcze lepsza. Możesz również uzyskać dostęp do tych pomocniczych aktywnych replik geograficznych w celu uzyskania dostępu tylko do odczytu. Jest to bardzo przydatne w celu zmniejszenia opóźnień dla scenariusza aplikacji rozproszonej geograficznie.

### <a name="how-does-my-disaster-recovery-plan-change-from-on-premises-to-sql-database"></a>Jak zmienia się plan odzyskiwania po awarii z lokalnego na SQL Database

Podsumowując, konfiguracja SQL Server wymaga, aby aktywnie zarządzać dostępnością przy użyciu funkcji, takich jak klaster trybu failover, dublowanie bazy danych, replikacja transakcji lub wysyłanie dzienników oraz zarządzanie kopiami zapasowymi w celu zapewnienia ciągłości działalności biznesowej. Dzięki SQL Database platforma zarządza tymi elementami, dzięki czemu możesz skupić się na tworzeniu i optymalizowaniu aplikacji bazy danych i nie martw się o zarządzanie awarią. Można skonfigurować plany odzyskiwania i tworzenia kopii zapasowych oraz pracę z kilkoma kliknięciami Azure Portal (lub kilkoma poleceniami przy użyciu interfejsów API programu PowerShell).

Aby dowiedzieć się więcej na temat odzyskiwania po awarii, zobacz: [Azure SQL Database odzyskiwania po awarii 101](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/)

## <a name="security-and-compliance"></a>Zabezpieczenia i zgodność

SQL Database bardzo poważnie zapewnia bezpieczeństwo i ochronę prywatności. Zabezpieczenia w ramach SQL Database są dostępne na poziomie bazy danych i na poziomie platformy i są najlepiej zrozumiałe w przypadku kategoryzowania do kilku warstw. Dla każdej warstwy można kontrolować i zapewniać optymalne zabezpieczenia aplikacji. Są to następujące warstwy:

- Uwierzytelnianie & tożsamości ([uwierzytelnianie SQL i Azure Active Directory [Azure AD] uwierzytelnianie](logins-create-manage.md)).
- Monitorowanie aktywności ([Inspekcja](../../azure-sql/database/auditing-overview.md) i [wykrywanie zagrożeń](threat-detection-configure.md)).
- Ochrona rzeczywistych danych ([transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) i [Always Encrypted [AE]](/sql/relational-databases/security/encryption/always-encrypted-database-engine)).
- Kontrolowanie dostępu do danych poufnych i uprzywilejowanych ([zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) i [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking)).

[Azure Security Center](https://azure.microsoft.com/services/security-center/) oferuje scentralizowane zarządzanie zabezpieczeniami w ramach obciążeń uruchomionych na platformie Azure, lokalnie i w innych chmurach. Możesz sprawdzić, czy podstawowa ochrona SQL Database, taka jak [Inspekcja](../../azure-sql/database/auditing-overview.md) i [transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) są skonfigurowane dla wszystkich zasobów i utworzyć zasady na podstawie własnych wymagań.

### <a name="what-user-authentication-methods-are-offered-in-sql-database"></a>Jakie metody uwierzytelniania użytkowników są oferowane w SQL Database

Istnieją dwie metody uwierzytelniania oferowane w SQL Database:

- [Uwierzytelnianie Azure Active Directory](authentication-aad-overview.md)
- [Uwierzytelnianie SQL](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)

Tradycyjne uwierzytelnianie systemu Windows nie jest obsługiwane. Azure Active Directory (Azure AD) to scentralizowana usługa zarządzania tożsamościami i dostępem. Dzięki temu możesz bardzo łatwo zapewnić dostęp logowania jednokrotnego do wszystkich pracowników w organizacji. Oznacza to, że poświadczenia są współużytkowane przez wszystkie usługi platformy Azure w celu łatwiejszego uwierzytelniania. 

Usługa Azure AD obsługuje [usługę Azure ad Multi-Factor Authentication](authentication-mfa-ssms-overview.md) i za pomocą [kilku kliknięć](../../active-directory/hybrid/how-to-connect-install-express.md) można zintegrować usługę Azure AD z Active Directory systemu Windows Server. Uwierzytelnianie SQL działa tak samo jak w przeszłości. Podaj nazwę użytkownika/hasło, a następnie możesz uwierzytelnić użytkowników w dowolnej bazie danych na danym serwerze. Umożliwia to również SQL Database i usługi Azure Synapse Analytics (wcześniej SQL Data Warehouse) do oferowania kont użytkowników Multi-Factor Authentication i Gości w domenie usługi Azure AD. Jeśli masz już Active Directory lokalnie, możesz sfederować katalog z Azure Active Directory, aby zwiększyć katalog na platformie Azure.

|**Jeśli ty...**|**SQL Database/analiza Synapse Azure**|
|---|---|
|Preferuj, aby nie używać Azure Active Directory (Azure AD) na platformie Azure|Użyj [uwierzytelniania SQL](security-overview.md)|
|Używanie usługi AD na SQL Server lokalnie|[Sfederować usługi AD z usługą Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md)i korzystaj z uwierzytelniania usługi Azure AD. Dzięki temu można korzystać z logowania jednokrotnego.|
|Należy wymusić Multi-Factor Authentication|Wymagaj Multi-Factor Authentication jako zasady za pomocą [dostępu warunkowego firmy Microsoft](conditional-access-configure.md)i Użyj [uwierzytelniania uniwersalnego usługi Azure AD z obsługą Multi-Factor Authentication](authentication-mfa-ssms-overview.md).|
|Posiadanie kont Gościa z kont Microsoft (live.com, outlook.com) lub innych domen (gmail.com)|Użyj [uwierzytelniania uniwersalnego usługi Azure AD](authentication-mfa-ssms-overview.md) w usłudze SQL Database/magazyn danych, która korzysta z [funkcji współpracy B2B usługi Azure AD](../../active-directory/external-identities/what-is-b2b.md).|
|Są zalogowane do systemu Windows przy użyciu poświadczeń usługi Azure AD z domeny federacyjnej|Użyj [uwierzytelniania zintegrowanego usługi Azure AD](authentication-aad-configure.md).|
|Są zalogowane w systemie Windows przy użyciu poświadczeń z domeny niefederacyjnego z platformą Azure|Użyj [uwierzytelniania zintegrowanego usługi Azure AD](authentication-aad-configure.md).|
|Posiadanie usług warstwy środkowej, które muszą łączyć się z usługą SQL Database lub Synapse Analytics|Użyj [uwierzytelniania zintegrowanego usługi Azure AD](authentication-aad-configure.md).|
|||

### <a name="how-do-i-limit-or-control-connectivity-access-to-my-database"></a>Jak mogę ograniczanie lub kontrolowanie dostępu do łączności z moją bazą danych

Istnieje wiele technik do dyspozycji, których można użyć do uzyskania optymalnej organizacji łączności dla aplikacji.

- Reguły zapory
- Punkty końcowe usługi sieci wirtualnej
- Zastrzeżone adresy IP

#### <a name="firewall"></a>Firewall

Zapora uniemożliwia dostęp do serwera z zewnętrznej jednostki, zezwalając na dostęp tylko określonym podmiotom do serwera. Domyślnie wszystkie połączenia z bazami danych znajdującymi się na serwerze są niedozwolone, z wyjątkiem połączeń (optionally7) pochodzących z innych usług platformy Azure. Za pomocą reguły zapory można otworzyć dostęp do serwera tylko do jednostek (na przykład na komputerze dewelopera), które zostały zatwierdzone przez zezwolenie na ten komputer za pomocą zapory. Pozwala także określić zakres adresów IP, które mają zezwalać na dostęp do serwera. Na przykład adresy IP komputerów deweloperów w organizacji można dodać jednocześnie, określając zakres na stronie Ustawienia zapory.

Reguły zapory można tworzyć na poziomie serwera lub na poziomie bazy danych. Reguły zapory adresów IP na poziomie serwera można utworzyć przy użyciu Azure Portal lub programu SSMS. Aby dowiedzieć się więcej na temat sposobu ustawiania reguły zapory na poziomie serwera i na poziomie bazy danych, zobacz: [Tworzenie reguł zapory adresów IP w SQL Database](secure-database-tutorial.md#create-firewall-rules).

#### <a name="service-endpoints"></a>Punkty końcowe usługi

Domyślnie baza danych jest skonfigurowana do "Zezwalaj usługom platformy Azure na dostęp do serwera", co oznacza, że każda maszyna wirtualna na platformie Azure może próbować nawiązać połączenie z bazą danych. Te próby będą nadal musiały zostać uwierzytelnione. Jeśli jednak nie chcesz, aby baza danych była dostępna przez żadne adresy IP platformy Azure, możesz wyłączyć opcję "Zezwalaj na dostęp do serwera usług platformy Azure". Ponadto możesz skonfigurować [punkty końcowe usługi sieci wirtualnej](vnet-service-endpoint-rule-overview.md).

Punkty końcowe usługi (SE) umożliwiają uwidocznienie krytycznych zasobów platformy Azure tylko do własnej prywatnej sieci wirtualnej na platformie Azure. Dzięki temu można zasadniczo wyeliminować publiczny dostęp do Twoich zasobów. Ruch między siecią wirtualną do platformy Azure pozostaje w sieci szkieletowej platformy Azure. Bez wymuszania routingu pakietów wymuszonego tunelowania. Twoja sieć wirtualna wymusza ruch internetowy do organizacji i ruch usługi platformy Azure, aby przejść do tej samej trasy. Za pomocą punktów końcowych usługi można zoptymalizować ten sposób, ponieważ pakiety są przesyłane bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Azure.

![Punkty końcowe usługi sieci wirtualnej](./media/manage-data-after-migrating-to-database/vnet-service-endpoints.png)

#### <a name="reserved-ips"></a>Zastrzeżone adresy IP

Inną opcją jest udostępnienie [zarezerwowanych](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) adresów IP dla maszyn wirtualnych, a następnie dodanie tych określonych wirtualnych nazw w ustawieniach zapory serwera. Przypisanie zarezerwowanych adresów IP pozwala zaoszczędzić problemy związane z aktualizowaniem reguł zapory przy użyciu zmieniających się adresów IP.

### <a name="what-port-do-i-connect-to-sql-database-on"></a>Z którym portem nawiązuje połączenie SQL Database

Port 1433. SQL Database komunikuje się za pośrednictwem tego portu. Aby nawiązać połączenie z sieci firmowej, należy dodać regułę ruchu wychodzącego w ustawieniach zapory w organizacji. Jako wskazówkę, unikaj ujawniania portu 1433 poza granicą platformy Azure.

### <a name="how-can-i-monitor-and-regulate-activity-on-my-server-and-database-in-sql-database"></a>Jak monitorować i regulować aktywność na serwerze i w bazie danych w SQL Database

#### <a name="sql-database-auditing"></a>Inspekcja usługi SQL Database

Za pomocą SQL Database można włączyć inspekcję w celu śledzenia zdarzeń bazy danych. [Inspekcja SQL Database](../../azure-sql/database/auditing-overview.md) rejestruje zdarzenia bazy danych i zapisuje je w pliku dziennika inspekcji na koncie usługi Azure Storage. Inspekcja jest szczególnie przydatna, jeśli zamierzasz uzyskać wgląd w potencjalne naruszenia zabezpieczeń i zasad, zachować zgodność z przepisami itp. Pozwala on definiować i konfigurować pewne kategorie zdarzeń, które są potrzebne do inspekcji, i na podstawie tego, że można uzyskać dostęp do wstępnie skonfigurowanych raportów i pulpitu nawigacyjnego, aby zapoznać się z omówieniem zdarzeń występujących w bazie danych. Zasady inspekcji można stosować zarówno na poziomie bazy danych, jak i na poziomie serwera. Przewodnik dotyczący sposobu włączania inspekcji dla serwera/bazy danych, zobacz: [Włączanie inspekcji SQL Database](secure-database-tutorial.md#enable-security-features).

#### <a name="threat-detection"></a>Wykrywanie zagrożeń

Dzięki funkcji [wykrywania zagrożeń](threat-detection-configure.md)można korzystać z funkcji naruszenia zabezpieczeń lub naruszeń zasad, które są bardzo proste. Nie musisz być ekspertem ds. zabezpieczeń, aby rozwiązywać potencjalne zagrożenia lub naruszenia w systemie. Wykrywanie zagrożeń ma także pewne wbudowane funkcje, takie jak wykrywanie iniekcji SQL. Iniekcja SQL to próba zmiany lub złamania danych oraz w pełni typowy sposób ataku na aplikacje bazy danych. Wykrywanie zagrożeń uruchamia wiele zestawów algorytmów, które wykrywają potencjalne luki w zabezpieczeniach i ataki iniekcji SQL, a także nietypowe wzorce dostępu do baz danych (takie jak dostęp z nietypowej lokalizacji lub nieznanego podmiotu zabezpieczeń). Urzędy zabezpieczeń lub inni Wyznaczeni Administratorzy otrzymają powiadomienie e-mail w przypadku wykrycia zagrożenia w bazie danych. Każde powiadomienie zawiera szczegółowe informacje o podejrzanych działaniach i zaleceniach dotyczących dalszych badań i łagodzenia zagrożeń. Aby dowiedzieć się, jak włączyć wykrywanie zagrożeń, zobacz: [Włączanie wykrywania zagrożeń](secure-database-tutorial.md#enable-security-features).

### <a name="how-do-i-protect-my-data-in-general-on-sql-database"></a>Jak mogę chronić moje dane ogólnie na SQL Database

Szyfrowanie zapewnia silny mechanizm ochrony i zabezpieczania poufnych danych przed intruzami. Zaszyfrowane dane nie są stosowane do intruza bez klucza odszyfrowywania. W tym celu dodaje dodatkową warstwę ochrony na podstawie istniejących warstw zabezpieczeń wbudowanych SQL Database. Istnieją dwa aspekty ochrony danych w SQL Database:

- Dane, które są przechowywane w plikach danych i dziennika
- Dane, które są w locie

W SQL Database Domyślnie dane przechowywane w plikach danych i dziennika w podsystemie magazynowania są całkowicie i zawsze szyfrowane za pośrednictwem [transparent Data Encryption [TDE]](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Kopie zapasowe są również szyfrowane. Z TDE nie ma żadnych zmian wymaganych na stronie aplikacji, które uzyskują dostęp do tych danych. Szyfrowanie i odszyfrowywanie odbywa się w sposób przezroczysty; w związku z tym nazwa.
Aby chronić poufne dane w locie i w spoczynku, SQL Database zapewnia funkcję o nazwie [Always Encrypted (AE)](/sql/relational-databases/security/encryption/always-encrypted-database-engine). AE to forma szyfrowania po stronie klienta, która szyfruje poufne kolumny w bazie danych (dzięki czemu są one szyfrowane dla administratorów baz danych i nieautoryzowanych użytkowników). Serwer odbiera zaszyfrowane dane, aby rozpocząć od. Klucz dla Always Encrypted jest również przechowywany po stronie klienta, więc tylko autoryzowani klienci mogą odszyfrować poufne kolumny. Administratorzy serwera i danych nie mogą zobaczyć poufnych danych, ponieważ klucze szyfrowania są przechowywane na kliencie. AE szyfruje poufne kolumny w tabeli na końcu, od nieautoryzowanych klientów do dysku fizycznego. AE obsługuje teraz porównania równości, więc przetwarzający może kontynuować zapytania o zaszyfrowane kolumny w ramach poleceń SQL. Always Encrypted może być używana z różnymi opcjami magazynu kluczy, takimi jak [Azure Key Vault](always-encrypted-azure-key-vault-configure.md), magazyn certyfikatów systemu Windows i lokalne sprzętowe moduły zabezpieczeń.

|**Właściwości**|**Zawsze szyfrowane**|**Niewidoczne szyfrowanie danych**|
|---|---|---|
|**Zakres szyfrowania**|Od końca do końca|Dane w spoczynku|
|**Serwer może uzyskać dostęp do poufnych danych**|Nie|Tak, ponieważ szyfrowanie jest przeznaczone dla danych przechowywanych w spoczynku|
|**Dozwolone operacje T-SQL**|Porównanie równości|Wszystkie obszary powierzchni T-SQL są dostępne|
|**Zmiany aplikacji wymagane do korzystania z tej funkcji**|Minimalny|Bardzo minimalne|
|**Stopień szczegółowości szyfrowania**|Poziom kolumny|Poziom bazy danych|
||||

### <a name="how-can-i-limit-access-to-sensitive-data-in-my-database"></a>Jak ograniczyć dostęp do poufnych danych w mojej bazie danych

Każda aplikacja ma pewną liczbę poufnych danych w bazie danych, która musi być chroniona przez wszystkie osoby. Niektórzy pracownicy w organizacji muszą wyświetlać te dane, jednak inne nie powinny mieć możliwości wyświetlania tych danych. Jednym z przykładów jest wynagrodzenie pracownika. Menedżer powinien mieć dostęp do informacji o płacach dla ich bezpośrednich raportów, jednak indywidualni członkowie zespołu nie powinni mieć dostępu do informacji o płacach swoich elementów równorzędnych. Innym scenariuszem są deweloperzy danych, którzy mogą korzystać z poufnych danych podczas etapów tworzenia lub testowania, na przykład SSNs klientów. Te informacje nie muszą być ujawnione dla deweloperów. W takich przypadkach poufne dane muszą być maskowane lub nie być widoczne. SQL Database oferuje dwa takie podejścia, aby uniemożliwić nieautoryzowanym użytkownikom wyświetlanie poufnych danych:

[Dynamiczne maskowanie danych](dynamic-data-masking-overview.md) to funkcja maskowania danych, która pozwala ograniczyć narażenie na dane poufne przez zamaskowanie ich dla użytkowników nieuprzywilejowanych w warstwie aplikacji. Definiujesz regułę maskowania, która może utworzyć wzorzec maskowania (na przykład, aby wyświetlić tylko ostatnie cztery cyfry numeru PESEL: XXX-XX-0000 i oznaczyć większość z nich jako XS) i określić, którzy użytkownicy mają być wykluczeni z reguły maskowania. Maskowanie odbywa się na bieżąco i dostępne są różne funkcje maskowania dla różnych kategorii danych. Dynamiczne maskowanie danych pozwala na automatyczne wykrywanie poufnych danych w bazie danych i stosowanie maskowania.

[Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) umożliwiają kontrolowanie dostępu na poziomie wiersza. Oznacza to, że niektóre wiersze w tabeli bazy danych na podstawie użytkownika wykonującego zapytanie (członkostwo w grupie lub kontekst wykonywania) są ukryte. Ograniczenie dostępu odbywa się w warstwie bazy danych zamiast w warstwie aplikacji, aby uprościć logikę aplikacji. Rozpoczynając od utworzenia predykatu filtru, odfiltruj wiersze, które nie są uwidocznione, a następnie określ zasady zabezpieczeń, które będą miały dostęp do tych wierszy. Na koniec użytkownik końcowy uruchamia swoje zapytanie i, w zależności od uprawnień użytkownika, wyświetla te wiersze z ograniczeniami lub nie może ich zobaczyć wcale.

### <a name="how-do-i-manage-encryption-keys-in-the-cloud"></a>Jak mogę zarządzać kluczami szyfrowania w chmurze

Dostępne są opcje zarządzania kluczami zarówno dla Always Encrypted (szyfrowanie po stronie klienta), jak i Transparent Data Encryption (szyfrowanie w spoczynku). Zaleca się regularne obracanie kluczy szyfrowania. Częstotliwość rotacji powinna być zgodna z przepisami organizacji wewnętrznej i wymaganiami dotyczącymi zgodności.

#### <a name="transparent-data-encryption-tde"></a>Transparent Data Encryption (TDE)

Istnieje dwukluczowa hierarchia w TDE — dane w każdej bazie danych użytkownika są szyfrowane za pomocą symetrycznej bazy danych AES-256 — unikatowego klucza szyfrowania bazy danych, który z kolei jest szyfrowany przez serwer z unikatowym kluczem głównym RSA 2048. Kluczem głównym może być zarządzana:

- Automatycznie przez SQL Database platformy.
- Lub przy użyciu [Azure Key Vault](always-encrypted-azure-key-vault-configure.md) jako magazynu kluczy.

Domyślnie klucz główny dla Transparent Data Encryption jest zarządzany przez usługę SQL Database dla wygody. Jeśli Twoja organizacja będzie mieć kontrolę nad kluczem głównym, istnieje możliwość użycia Azure Key Vault] (zawsze szyfrowany-Azure-Key-Store-configure.md) jako magazynu kluczy. Korzystając z Azure Key Vault, organizacja zakłada kontrolę nad kontrolami, rotacją i uprawnieniami do obsługi kluczy. [Obracanie lub przełączanie typu klucza głównego TDE](/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql-key-rotation) jest szybkie, ponieważ powoduje tylko ponowne zaszyfrowanie bazy danych szyfrowania danych. W przypadku organizacji z separacją ról między zabezpieczeniami i zarządzaniem danymi administrator zabezpieczeń może udostępnić klucz klucza głównego TDE w Azure Key Vault i podać administratorowi bazy danych identyfikator klucza Azure Key Vault, który ma być używany do szyfrowania na serwerze. Key Vault jest zaprojektowana tak, że firma Microsoft nie widzi ani nie wyodrębni kluczy szyfrowania. Możesz również centralnie zarządzać kluczami dla swojej organizacji.

#### <a name="always-encrypted"></a>Zawsze szyfrowane

Istnieje również [dwukluczowa hierarchia](/sql/relational-databases/security/encryption/overview-of-key-management-for-always-encrypted) w Always Encrypted — kolumna danych poufnych jest szyfrowana za pomocą klucza szyfrowania AES 256-kolumnowego (CEK), który z kolei jest szyfrowany przy użyciu klucza głównego kolumny (CMK). Sterowniki klienta podane dla Always Encrypted nie mają ograniczeń dotyczących długości CMKs. Zaszyfrowana wartość CEK jest przechowywana w bazie danych, a CMK jest przechowywana w zaufanym magazynie kluczy, takim jak magazyn certyfikatów systemu Windows, Azure Key Vault lub sprzętowy moduł zabezpieczeń.

- Można obrócić zarówno [CEK, jak i CMK](/sql/relational-databases/security/encryption/rotate-always-encrypted-keys-using-powershell) .
- Obrót CEK jest rozmiarem operacji na danych i może być czasochłonny w zależności od rozmiaru tabel zawierających zaszyfrowane kolumny. W związku z tym rozsądne jest zaplanowanie rotacji CEK.
- Jednak rotacja CMK nie zakłóca wydajności bazy danych i może być wykonywana z rozdzielonymi rolami.

Na poniższym diagramie przedstawiono opcje magazynu kluczy dla kluczy głównych kolumn w Always Encrypted

![Zawsze zaszyfrowane dostawcy sklepu CMK](./media/manage-data-after-migrating-to-database/always-encrypted.png)

### <a name="how-can-i-optimize-and-secure-the-traffic-between-my-organization-and-sql-database"></a>Jak mogę zoptymalizować i zabezpieczyć ruch między moją organizacją a SQL Database

Ruch sieciowy między organizacją a SQL Database będzie ogólnie kierowany przez sieć publiczną. Jeśli jednak zdecydujesz się zoptymalizować tę ścieżkę i zwiększyć jej bezpieczeństwo, możesz przyjrzeć się do usługi Azure ExpressRoute. ExpressRoute pozwala na przełączenie sieci firmowej na platformę Azure za pośrednictwem połączenia prywatnego. Dzięki temu nie możesz przejść przez publiczny Internet. Uzyskuje się również wyższe zabezpieczenia, niezawodność i optymalizację routingu, które tłumaczą się na mniejsze opóźnienia sieci i znacznie szybciej niż zwykle w przypadku korzystania z publicznej sieci Internet. Jeśli planujesz transfer znaczących fragmentów danych między organizacją i platformą Azure, korzystanie z usługi ExpressRoute może przynieść oszczędności kosztów. Możesz wybrać spośród trzech różnych modeli łączności dla połączenia z organizacji do platformy Azure:

- [Współpraca między lokacjami w chmurze](../../expressroute/expressroute-connectivity-models.md#CloudExchange)
- [Dowolny z dowolnym](../../expressroute/expressroute-connectivity-models.md#IPVPN)
- [Punkt-punkt](../../expressroute/expressroute-connectivity-models.md#Ethernet)

ExpressRoute umożliwia również przekroczenie limitu liczby przydziałów przepustowości, która nie ma dodatkowej opłaty. Istnieje również możliwość skonfigurowania łączności między regionami przy użyciu ExpressRoute. Aby wyświetlić listę dostawców połączeń ExpressRoute, zobacz: [partnerzy ExpressRoute i lokalizacje komunikacji równorzędnej](../../expressroute/expressroute-locations.md). W poniższych artykułach szczegółowo opisano trasę Express:

- [Wprowadzenie do trasy Express](../../expressroute/expressroute-introduction.md)
- [Wymagania wstępne](../../expressroute/expressroute-prerequisites.md)
- [Przepływy pracy](../../expressroute/expressroute-workflows.md)

### <a name="is-sql-database-compliant-with-any-regulatory-requirements-and-how-does-that-help-with-my-own-organizations-compliance"></a>Jest SQL Database zgodne z wymaganiami prawnymi i w jaki sposób pomaga w zakresie zgodności z moją organizacją

SQL Database jest zgodny z zakresem regulacji compliancies. Aby wyświetlić najnowszy zestaw compliancies, które zostały spełnione przez SQL Database, odwiedź [Centrum zaufania firmy Microsoft](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) i przejdź do sekcji compliancies, które są ważne dla Twojej organizacji, aby sprawdzić, czy SQL Database jest uwzględniona w ramach zgodnych usług platformy Azure. Należy pamiętać, że chociaż SQL Database mogą być certyfikowane jako zgodne usługi, pomoc w zakresie zgodności usługi w organizacji, ale nie gwarantuje jej automatycznie.

## <a name="intelligent-database-monitoring-and-maintenance-after-migration"></a>Inteligentne monitorowanie i konserwacja bazy danych po migracji

Po przeprowadzeniu migracji bazy danych do SQL Database chcesz monitorować swoją bazę danych (na przykład sprawdzić, jak wykorzystanie zasobów jest podobne lub DBCC checks) i przeprowadzić regularne konserwacje (na przykład ponownie skompilować lub zreorganizować indeksy, statystyki itp.). Na szczęście SQL Database jest inteligentnie w tym sensie, że wykorzystuje trendy historyczne oraz zarejestrowane metryki i statystyki, aby aktywnie pomóc w monitorowaniu i obsłudze bazy danych, dzięki czemu aplikacja będzie działać optymalnie. W niektórych przypadkach Azure SQL Database mogą automatycznie wykonywać zadania konserwacji w zależności od konfiguracji konfiguracji. Istnieją trzy aspekty monitorowania bazy danych w SQL Database:

- Monitorowanie i Optymalizacja wydajności.
- Optymalizacja zabezpieczeń.
- Optymalizacja kosztów.

### <a name="performance-monitoring-and-optimization"></a>Monitorowanie i Optymalizacja wydajności

Dzięki szczegółowej analizie wydajności zapytań można uzyskać dopasowane zalecenia dotyczące obciążenia bazy danych, dzięki czemu aplikacje mogą działać na optymalnym poziomie — zawsze. Można go również skonfigurować tak, aby te zalecenia były stosowane automatycznie i nie trzeba bother wykonywania zadań konserwacyjnych. Za pomocą SQL Database Advisor można automatycznie zaimplementować zalecenia dotyczące indeksów na podstawie obciążenia — jest to tzw. Automatyczne dostrajanie. Zalecenia są rozwijane w miarę zmian obciążenia aplikacji, aby zapewnić najbardziej odpowiednie sugestie. Można również ręcznie przejrzeć te zalecenia i zastosować je według własnego uznania.  

### <a name="security-optimization"></a>Optymalizacja zabezpieczeń

SQL Database udostępnia zalecenia dotyczące zabezpieczeń, które ułatwiają Zabezpieczanie danych i wykrywanie zagrożeń w celu identyfikowania i badania podejrzanych działań bazy danych, które mogą stanowić potencjalny wątek w bazie danych. [Ocena luk w zabezpieczeniach](sql-vulnerability-assessment.md) to usługa służąca do skanowania baz danych i raportowania, która pozwala monitorować stan zabezpieczeń baz danych na dużą skalę i identyfikować zagrożenia bezpieczeństwa i dryfować od linii bazowej zabezpieczeń zdefiniowanej przez użytkownika. Po każdym skanowaniu dostępna jest dostosowana lista kroków akcji i skryptów korygowania, a także raport oceny, którego można użyć w celu spełnienia wymagań dotyczących zgodności.

Za pomocą Azure Security Center można zidentyfikować zalecenia dotyczące zabezpieczeń na tablicy i zastosować je za pomocą jednego kliknięcia.

### <a name="cost-optimization"></a>Optymalizacja kosztów

Platforma Azure SQL analizuje historię wykorzystania w bazach danych na serwerze, aby oszacować i zalecać opcje optymalizacji kosztów. Ta analiza zwykle wymaga fortnight do analizowania i kompilowania zaleceń z możliwością wykonania akcji. Elastyczna Pula jest jedną z tych opcji. Zalecenie jest wyświetlane w portalu jako transparent:

![zalecenia dotyczące puli elastycznej](./media/manage-data-after-migrating-to-database/elastic-pool-recommendations.png)

Tę analizę można także wyświetlić w sekcji "Advisor":

![zalecenia dotyczące puli elastycznej — Doradca](./media/manage-data-after-migrating-to-database/advisor-section.png)

### <a name="how-do-i-monitor-the-performance-and-resource-utilization-in-sql-database"></a>Jak mogę monitorowanie wydajności i wykorzystania zasobów w SQL Database

W SQL Database można korzystać z inteligentnego wglądu w dane platformy w celu monitorowania wydajności i odpowiednio dostrajania. Można monitorować wydajność i wykorzystanie zasobów w SQL Database przy użyciu następujących metod:

#### <a name="azure-portal"></a>Azure Portal

Azure Portal pokazuje użycie bazy danych, wybierając bazę danych i klikając wykres w okienku Przegląd. Można zmodyfikować wykres, aby pokazać wiele metryk, w tym wartość procentową procesora CPU, procent jednostek DTU, procent operacji we/wy danych, procent sesji i procent rozmiaru bazy danych.

![Wykres monitorowania](./media/manage-data-after-migrating-to-database/monitoring-chart.png)

![Monitorowanie CHART2](./media/manage-data-after-migrating-to-database/chart.png)

Na tym wykresie można także skonfigurować alerty według zasobu. Te alerty umożliwiają reagowanie na warunki zasobów za pomocą poczty e-mail, zapisywanie w punkcie końcowym protokołu HTTPS/HTTP lub wykonywanie akcji. Aby uzyskać więcej informacji, zobacz [tworzenie alertów](alerts-insights-configure-portal.md).

#### <a name="dynamic-management-views"></a>Dynamiczne widoki zarządzania

Można wysłać zapytanie do [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamicznego widoku zarządzania, aby przywrócić historię statystyk zużycia zasobów z ostatniej godziny i widoku wykazu systemu [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , aby zwracała historię dla ostatnich 14 dni.

#### <a name="query-performance-insight"></a>Szczegółowe informacje o wydajności zapytań

[Szczegółowe informacje o wydajności zapytań](query-performance-insight-use.md) pozwala wyświetlić historię najważniejszych zapytań zużywających zasoby i długotrwałych zapytań dla określonej bazy danych. Można szybko identyfikować NAJPOPULARNIEJSZe zapytania według wykorzystania zasobów, czasu trwania i częstotliwości wykonywania. Można śledzić zapytania i wykrywać regresję. Ta funkcja wymaga, aby [Magazyn zapytań](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) był włączony i aktywny dla bazy danych.

![Szczegółowe informacje o wydajności zapytań](./media/manage-data-after-migrating-to-database/query-performance-insight.png)

#### <a name="azure-sql-analytics-preview-in-azure-monitor-logs"></a>Azure SQL Analytics (wersja zapoznawcza) w dziennikach Azure Monitor

[Azure monitor Logs](../../azure-monitor/insights/azure-sql.md) pozwala zbierać i wizualizować kluczowe metryki wydajności Azure SQL Database, obsługujące do 150 000 baz danych i 5 000 pul elastycznych SQL na obszar roboczy. Można jej używać do monitorowania i odbierania powiadomień. Można monitorować metryki SQL Database i elastycznych pul w wielu subskrypcjach platformy Azure i elastycznych pulach i mogą służyć do identyfikowania problemów w każdej warstwie stosu aplikacji.

### <a name="i-am-noticing-performance-issues-how-does-my-sql-database-troubleshooting-methodology-differ-from-sql-server"></a>Mi obserwowanie problemy z wydajnością: w jaki sposób metodologia rozwiązywania problemów SQL Database różni się od SQL Server

Główna część technik rozwiązywania problemów, która powinna być używana do diagnozowania problemów z wydajnością zapytań i bazy danych, pozostaje taka sama. Gdy wszystkie te same aparaty bazy danych mają uprawnienia do chmury. Jednak platforma Azure SQL Database została wbudowana w "inteligencję". Może ułatwić rozwiązywanie problemów i diagnozowanie problemów z wydajnością. Może również wykonywać niektóre z tych działań naprawczych w Twoim imieniu, a w niektórych przypadkach automatycznie naprawiać je.

Twoje podejście do rozwiązywania problemów z wydajnością może znacząco korzystać z funkcji inteligentnych, takich jak [szczegółowe informacje o wydajności zapytań (QPI)](query-performance-insight-use.md) i [Database Advisor](database-advisor-implement-performance-recommendations.md) w połączeniu z tym, że różnica w metodologii różni się w tym aspekcie — nie trzeba już ręcznie wykonywać ręcznych informacji, które mogą pomóc w rozwiązaniu problemu. Platforma działa dla Ciebie. Przykładem jest QPI. Za pomocą QPI można przechodzić do szczegółów na poziomie zapytania i przeglądać trendy historyczne i dowiedzieć się, jak dokładnie kwerenda uległa pogorszeniu. Database Advisor zawiera zalecenia dotyczące zagadnień, które mogą pomóc w ulepszaniu ogólnej wydajności, podobnie jak w przypadku brakujących indeksów, upuszczania indeksów, parametryzacja zapytań itp.

W przypadku rozwiązywania problemów z wydajnością ważne jest, aby określić, czy jest to tylko aplikacja, czy baza danych, która ma wpływ na wydajność aplikacji. Często problem z wydajnością znajduje się w warstwie aplikacji. Może to być architektura lub wzorzec dostępu do danych. Rozważmy na przykład, że masz aplikację rozmawiania, która jest wrażliwa na opóźnienia sieci. W takim przypadku aplikacja pogorszy się, ponieważ wystąpi wiele krótkich żądań przechodzących ("rozmawiających") między aplikacją a serwerem i w sieci o przeciążonym obciążeniu. Aby poprawić wydajność w tym przypadku, można użyć [zapytań wsadowych](performance-guidance.md#batch-queries). Korzystanie z partii pomaga w nieznacznie, ponieważ teraz Twoje żądania są przetwarzane w partii. w ten sposób można w ten sposób skrócić czas oczekiwania na rozbicie i poprawić wydajność aplikacji.

Ponadto, jeśli zauważysz spadek ogólnej wydajności bazy danych, możesz monitorować [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) i [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) dynamiczne widoki zarządzania, aby zrozumieć użycie procesora CPU, operacji we/wy i pamięci. Może to mieć wpływ na wydajność, ponieważ baza danych jest Starved zasobów. Może być konieczna zmiana rozmiaru i/lub warstwy usługi na podstawie rosnących i zmniejszających się wymagań dotyczących obciążenia.

Aby uzyskać kompleksowy zestaw zaleceń dotyczących dostrajania problemów z wydajnością, zobacz: [dostrajanie bazy danych](performance-guidance.md#tune-your-database).

### <a name="how-do-i-ensure-i-am-using-the-appropriate-service-tier-and-compute-size"></a>Jak mogę upewnij się, że używam odpowiedniej warstwy usług i rozmiaru obliczeń

SQL Database oferuje różne warstwy usług w warstwach Podstawowa, standardowa i Premium. Każda warstwa usług otrzymuje przewidywalną wydajność, która jest powiązana z tą warstwą usług. W zależności od obciążenia może istnieć szereg aktywności, w których wykorzystanie zasobów może osiągnąć górny limit bieżącego rozmiaru obliczeniowego, w którym się znajdujesz. W takich przypadkach warto najpierw zacząć od oceny, czy dostrojenie może pomóc (na przykład dodając lub modyfikując indeks itp.). Jeśli nadal występują problemy z ograniczeniami, rozważ przeniesienie do wyższej warstwy usług lub rozmiaru obliczeń.

|**Warstwa usługi**|**Typowe scenariusze przypadków użycia**|
|---|---|
|**Podstawowe**|Aplikacje z kilku użytkownikami i bazą danych, która nie ma wysokich wymagań dotyczących współbieżności, skalowania i wydajności. |
|**Standardowa**|Aplikacje mające znaczące wymagania dotyczące współbieżności, skalowania i wydajności, powiązane z niskimi i średnimi wymaganiami we/wy. |
|**Tytułu**|Aplikacje z dużą liczbą równoczesnych użytkowników, dużym procesorem CPU/pamięci i wysokimi wymaganiami we/wy. Duże współbieżność, Wysoka przepływność i wrażliwe na opóźnienia aplikacje mogą korzystać z poziomu Premium. |
|||

Aby mieć pewność, że korzystasz z odpowiedniego rozmiaru obliczeń, możesz monitorować użycie zasobów zapytania i bazy danych za pomocą jednego z powyższych metod w "Jak mogę monitorować wydajność i wykorzystanie zasobów w SQL Database". Należy się dowiedzieć, że zapytania/bazy danych stale działają na gorąco na procesorze CPU/pamięci itp. Możesz rozważyć skalowanie do wyższego rozmiaru. Podobnie, jeśli należy zauważyć, że nawet w godzinach szczytu nie będziesz używać zasobów tak samo, jak to możliwe. Rozważ przeskalowanie w dół od bieżącego rozmiaru obliczeń.

Jeśli masz wzorzec aplikacji SaaS lub scenariusz konsolidacji bazy danych, rozważ użycie elastycznej puli do optymalizacji kosztów. Elastyczna Pula to doskonały sposób na osiągnięcie konsolidacji bazy danych i optymalizacji kosztów. Aby dowiedzieć się więcej o zarządzaniu wieloma bazami danych za pomocą puli elastycznej, zobacz: [zarządzanie pulami i bazami danych](elastic-pool-manage.md#azure-portal).

### <a name="how-often-do-i-need-to-run-database-integrity-checks-for-my-database"></a>Jak często muszę uruchamiać testy integralności bazy danych dla mojej bazy danych

SQL Database używa niektórych inteligentnych technik, które umożliwiają obsługę niektórych klas uszkodzeń danych automatycznie i bez utraty danych. Te techniki są wbudowane w usługę i są wykorzystywane przez usługę, gdy zajdzie taka potrzeba. Regularne tworzenie kopii zapasowych bazy danych w ramach usługi odbywa się przez przywrócenie ich i uruchomienie polecenia DBCC CHECKDB na tym serwerze. Jeśli wystąpią problemy, SQL Database proaktywnie adresy. [Automatyczne naprawianie](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) stron jest wykorzystywane do naprawiania uszkodzonych stron lub problemów z integralnością danych. Strony bazy danych zawsze są weryfikowane przy użyciu domyślnego ustawienia sumy KONTROLnej, które weryfikuje integralność strony. SQL Database aktywnie monitoruje i przegląda integralność danych bazy danych oraz, jeśli wystąpią problemy, rozwiązuje je z najwyższym priorytetem. Oprócz tych, możesz wybrać opcję opcjonalnego uruchamiania własnych kontroli integralności.  Aby uzyskać więcej informacji, zobacz [integralność danych w SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)

## <a name="data-movement-after-migration"></a>Przenoszenie danych po migracji

### <a name="how-do-i-export-and-import-data-as-bacpac-files-from-sql-database-using-the-azure-portal"></a>Jak mogę eksportować i importować dane jako pliki BACPAC z SQL Database przy użyciu Azure Portal

- **Eksport**: można wyeksportować bazę danych w Azure SQL Database jako plik BACPAC z Azure Portal

   ![eksport bazy danych](./media/manage-data-after-migrating-to-database/database-export1.png)

- **Import**: można także zaimportować dane jako plik BACPAC do bazy danych w Azure SQL Database przy użyciu Azure Portal.

   ![Importowanie bazy danych](./media/manage-data-after-migrating-to-database/import1.png)

### <a name="how-do-i-synchronize-data-between-sql-database-and-sql-server"></a>Jak mogę synchronizowanie danych między SQL Database i SQL Server

Możesz to osiągnąć na kilka sposobów:

- **[Synchronizacja danych](sql-data-sync-data-sql-server-sql-database.md)** — ta funkcja ułatwia synchronizowanie danych dwukierunkowych między wieloma bazami danych SQL Server i SQL Database. Aby przeprowadzić synchronizację z bazami danych SQL Server, należy zainstalować i skonfigurować agenta synchronizacji na komputerze lokalnym lub maszynie wirtualnej i otworzyć wychodzący port TCP 1433.
- **[Replikacja transakcji](https://azure.microsoft.com/blog/transactional-replication-to-azure-sql-database-is-now-generally-available/)** — z replikacją transakcji można synchronizować dane z bazy danych SQL Server, aby Azure SQL Database z wystąpieniem SQL Server wydawcą i Azure SQL Databaseem subskrybenta. Na razie tylko ta konfiguracja jest obsługiwana. Aby uzyskać więcej informacji na temat migrowania danych z bazy danych SQL Server do usługi Azure SQL z minimalnym czasem przestoju, zobacz: [Korzystanie z replikacji transakcji](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication) .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [SQL Database](sql-database-paas-overview.md).