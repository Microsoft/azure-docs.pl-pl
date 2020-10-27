---
title: Przegląd — Azure Database for MySQL pojedynczy serwer
description: Dowiedz się więcej na temat Azure Database for MySQL pojedynczego serwera, usługi relacyjnej bazy danych w chmurze firmy Microsoft w oparciu o wersję MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: b0f8c5806ba22708db6dc537d391c1f1b1a183ec
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543818"
---
# <a name="azure-database-for-mysql-single-server"></a>Usługa Azure Database for MySQL — pojedynczy serwer

[Azure Database for MySQL](overview.md) obsługiwane przez program MySQL Community Edition jest dostępny w dwóch trybach wdrożenia:
- Pojedynczy serwer 
- Serwer elastyczny (wersja zapoznawcza)

Ten artykuł zawiera omówienie i wprowadzenie do podstawowych pojęć związanych z modelem wdrażania na jednym serwerze. Aby dowiedzieć się więcej o elastycznym trybie wdrażania serwera, zapoznaj się z tematem [elastyczne Omówienie serwera](flexible-server/index.yml). Aby uzyskać informacje na temat decydowania o opcji wdrożenia odpowiedniej dla obciążenia, zobacz [Wybieranie odpowiedniego serwera MySQL na platformie Azure](select-right-deployment-type.md).

## <a name="overview"></a>Omówienie

Pojedynczy serwer to w pełni zarządzana usługa bazy danych o minimalnych wymaganiach dotyczących dostosowań bazy danych. Platforma jednoserwerowa została zaprojektowana tak, aby obsługiwała większość funkcji zarządzania bazami danych, takich jak stosowanie poprawek, kopii zapasowych, wysoka dostępność, zabezpieczenia z minimalną konfiguracją i kontrolą użytkownika. Architektura jest zoptymalizowana pod kątem zapewnienia dostępności na 99,99% w ramach pojedynczej strefy dostępności. Obsługuje ona wersję społeczności MySQL 5,6, 5,7 i 8,0. Usługa jest ogólnie dostępna dzisiaj w wielu [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/). 

Pojedyncze serwery najlepiej nadają się do obsługi natywnych aplikacji w chmurze, które mają obsługiwać automatyczne stosowanie poprawek, bez konieczności przeprowadzania szczegółowej kontroli nad harmonogramem poprawek i niestandardowych ustawień konfiguracji programu MySQL. 

## <a name="high-availability"></a>Wysoka dostępność

Model wdrażania na jednym serwerze jest zoptymalizowany pod kątem wbudowanej wysokiej dostępności i elastyczności przy niższych kosztach. Architektura oddziela zasoby obliczeniowe i magazyn. Aparat bazy danych jest uruchamiany w zastrzeżonym kontenerze obliczeniowym, podczas gdy pliki danych znajdują się w usłudze Azure Storage. Magazyn przechowuje trzy lokalnie nadmiarowe kopie synchroniczne plików bazy danych, zapewniając trwałość danych. 

W przypadku planowanych lub nieplanowanych zdarzeń trybu failover, jeśli serwer ulegnie awarii, usługa zachowuje wysoką dostępność serwerów za pomocą następującej zautomatyzowanej procedury:

1. Zostanie zainicjowany nowy kontener obliczeniowy
2. Magazyn z plikami danych jest mapowany na nowy kontener 
3. Aparat bazy danych MySQL jest przełączany w tryb online w nowym kontenerze obliczeniowym
4. Usługa bramy zapewnia przezroczyste przełączanie do trybu failover, co nie wymaga żadnych zmian po stronie aplikacji. 
  
Typowe zakresy czasu pracy w trybie failover z 60-120 sekund. Natywny projekt w chmurze usługi Single Server umożliwia działowi IT wsparcie w 99,99% dostępności, eliminując koszt pasywnej rezerwy gorącą.

Wiodąca w branży Azure branżowa umowa dotycząca poziomu usług (SLA) na 99,99%, obsługiwana przez globalną sieć centrów danych zarządzanych przez firmę Microsoft, pomaga zapewnić, że aplikacje działają w 24/7.

:::image type="content" source="media/single-server-overview/1-singleserverarchitecture.png" alt-text="Diagram koncepcyjny Azure Database for MySQL jednej architektury serwera"::: 

## <a name="automated-patching"></a>Automatyczne stosowanie poprawek 

Usługa wykonuje zautomatyzowane stosowanie poprawek podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych. Poprawki obejmują aktualizacje zabezpieczeń i oprogramowania. W przypadku aparatu MySQL uaktualnienia wersji pomocniczej są automatyczne i uwzględniane jako część cyklu poprawek. Nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika ani ustawień konfiguracji. Częstotliwość stosowania poprawek to usługa zarządzana na podstawie krytycznego ładunku. Ogólnie rzecz biorąc usługa jest zgodna z harmonogramem wydań w ramach ciągłej integracji i wydania. Użytkownicy mogą subskrybować powiadomienie o [planowanej konserwacji](concepts-monitoring.md) , aby otrzymywać powiadomienie o nadchodzącej konserwacji 72 godzin przed wydarzeniem.

## <a name="automatic-backups"></a>Automatyczne kopie zapasowe

Usługa Single Server automatycznie tworzy kopie zapasowe serwera i przechowuje je w ramach użytkownika skonfigurowanego lokalnie nadmiarowy lub geograficznie nadmiarowy. Za pomocą kopii zapasowych można przywrócić serwer do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Czas przechowywania można opcjonalnie skonfigurować do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES. Szczegóły można znaleźć w [kopiach zapasowych](concepts-backup.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Dostosowanie wydajności i skalowania w kilka sekund

Usługa Single Server jest dostępna w trzech warstwach SKU: podstawowa, Ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci. Warstwa Podstawowa najlepiej nadaje się w przypadku obciążeń o małym koszcie i niskich współbieżności. Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci są lepiej dopasowane do obciążeń produkcyjnych wymagających dużej współbieżności, skali i przewidywalnej wydajności. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Skalowanie magazynu jest w trybie online i obsługuje automatyczne przyrosty pamięci masowej. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Płacisz tylko za zasoby, z których korzystasz. Aby uzyskać szczegółowe informacje, zobacz [Warstwy cenowe](./concepts-pricing-tiers.md).

## <a name="enterprise-grade-security-compliance-and-governance"></a>Zabezpieczenia, zgodność i zarządzanie klasy korporacyjnej

Usługa Single Server używa zatwierdzonego modułu kryptograficznego FIPS 140-2 do szyfrowania magazynu danych w spoczynku. Dane, w tym kopie zapasowe i pliki tymczasowe utworzone podczas wykonywania zapytań, są szyfrowane. Usługa używa szyfru AES 256-bitowego zawartego w szyfrowaniu usługi Azure Storage, a klucze mogą być zarządzane przez system (ustawienie domyślne) lub [zarządzane przez klienta](concepts-data-encryption-mysql.md). Usługa domyślnie szyfruje dane z użyciem protokołu Transport Layer Security (SSL/TLS). Usługa obsługuje protokół TLS w wersji 1,2, 1,1 i 1,0 z możliwością wymuszania [minimalnej wersji protokołu TLS](concepts-ssl-connection-security.md). 

Usługa umożliwia prywatnym dostęp do serwerów za pomocą [prywatnego linku](concepts-data-access-security-private-link.md) i zapewnia funkcję [zaawansowanej ochrony przed zagrożeniami](concepts-data-access-and-security-threat-protection.md) . Zaawansowana ochrona przed zagrożeniami wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Oprócz uwierzytelniania natywnego usługa Single Server obsługuje uwierzytelnianie  [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) . Uwierzytelnianie usługi Azure AD to mechanizm łączenia się z serwerami MySQL przy użyciu tożsamości zdefiniowanych i zarządzanych w usłudze Azure AD. Uwierzytelnianie za pomocą usługi Azure AD umożliwia zarządzanie tożsamościami użytkowników bazy danych i innymi usługami platformy Azure w centralnej lokalizacji, co upraszcza i scentralizowano kontrolę dostępu.

[Rejestrowanie inspekcji](concepts-audit-logs.md) jest dostępne do śledzenia wszystkich działań na poziomie bazy danych. 

Usługa Single Server to skarga ze wszystkimi wiodącymi w branży certyfikatami, takimi jak FedRAMP, HIPAA, PCI DSS. Odwiedź [Centrum zaufania Azure](https://www.microsoft.com/trustcenter/security), aby uzyskać informacje o zabezpieczeniach platformy Azure. 

Aby uzyskać więcej informacji na temat funkcji zabezpieczeń Azure Database for MySQL, zobacz [Omówienie zabezpieczeń](concepts-security.md).

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów

Usługa Single Server jest wyposażona w wbudowaną funkcję monitorowania wydajności i alertów. Wszystkie metryki platformy Azure mają częstotliwość jednominutową, a każda Metryka zapewnia 30-dniową historię. Można skonfigurować alerty dotyczące metryk. Usługa umożliwia konfigurowanie wolnych dzienników zapytań i zawiera zróżnicowaną funkcję [magazynu zapytań](concepts-query-store.md) . Magazyn zapytań upraszcza Rozwiązywanie problemów z wydajnością, pomagając szybko znaleźć najdłuższych uruchomionych i większości zapytań intensywnie korzystających z zasobów. Korzystając z tych narzędzi, można szybko zoptymalizować obciążenia i skonfigurować serwer w celu uzyskania najlepszej wydajności. Aby uzyskać szczegółowe informacje, zobacz [monitorowanie](concepts-monitoring.md) .

## <a name="migration"></a>Migracja

Usługa uruchamia wersję społeczności MySQL. Zapewnia to pełną zgodność aplikacji i wymaga minimalnego kosztu refaktoryzacji w celu przeprowadzenia migracji istniejącej aplikacji opracowanej na aparat MySQL do jednego serwera. Migrację do pojedynczego serwera można przeprowadzić przy użyciu jednej z następujących opcji:

- **Zrzuć i Przywróć** — w przypadku migracji w trybie offline, w których użytkownicy mogą uzyskać pewien przestój, zrzuty i przywracanie przy użyciu narzędzi społeczności, takich jak mysqldump/Dumper, mogą zapewnić najszybszy sposób migracji. Aby uzyskać szczegółowe informacje [, zobacz Migrowanie przy użyciu zrzutów i przywracania](concepts-migrate-dump-restore.md) . 
- **Azure Database Migration Service** — w celu zapewnienia bezproblemowej i uproszczonej migracji na pojedynczy serwer z minimalnym przestojem, [Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-online.md) można wykorzystać. 
- **Replikacja danych** — w przypadku minimalnych okresów migracji, replikacji danych, które opierają się na replikacji opartej na binlogach, można również wykorzystać. Replikacja danych w celu zapewnienia większej kontroli nad migracją jest preferowana w przypadku niewielkiej liczby przestojów. Szczegóły można znaleźć [w temacie replikacja danych](concepts-data-in-replication.md) .

## <a name="contacts"></a>Kontakty
Aby dowiedzieć się więcej na temat pytań lub sugestii dotyczących pracy z Azure Database for MySQL, Wyślij wiadomość e-mail do zespołu Azure Database for MySQL ([ @Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Ten adres e-mail nie jest aliasem pomocy technicznej.

Ponadto, w zależności od potrzeb, należy wziąć pod uwagę następujące punkty kontaktowe:

- Aby skontaktować się z pomocą techniczną platformy Azure, [wyślij zgłoszenie z witryny Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Następne kroki
Teraz, po zapoznaniu się z wprowadzeniem do Azure Database for MySQL trybu wdrożenia pojedynczego serwera, możesz:

- Utwórz swój pierwszy serwer. 
  - [Tworzenie serwera usługi Azure Database for MySQL za pomocą witryny Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
  - [Tworzenie serwera usługi Azure Database for MySQL za pomocą interfejsu wiersza polecenia platformy Azure](quickstart-create-mysql-server-database-using-azure-cli.md)
  - [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MySQL](sample-scripts-azure-cli.md)

- Kompiluj swoją pierwszą aplikację przy użyciu preferowanego języka:
  - [Python](./connect-python.md)
  - [Node.JS](./connect-nodejs.md)
  - [Java](./connect-java.md)
  - [Ruby](./connect-ruby.md)
  - [PHP](./connect-php.md)
  - [.NET (C#)](./connect-csharp.md)
  - [Przejdź](./connect-go.md)
