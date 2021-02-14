---
title: Azure Database for PostgreSQL pojedynczy serwer
description: Zawiera przegląd Azure Database for PostgreSQL jednego serwera.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 18d944cd2b3bfde1a0ee5fc53e83e4016b11b01f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379220"
---
# <a name="azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL pojedynczy serwer

[Azure Database for PostgreSQL](./overview.md) obsługiwane przez PostgreSQL Community Edition są dostępne w trzech trybach wdrożenia:

- Pojedynczy serwer
- Serwer elastyczny (wersja zapoznawcza)
- Hiperskala (Citus)

Ten artykuł zawiera omówienie i wprowadzenie do podstawowych pojęć związanych z modelem wdrażania na jednym serwerze. Aby dowiedzieć się więcej o elastycznym trybie wdrażania serwera, zobacz odpowiednio Omówienie [elastycznych serwerów](./flexible-server/overview.md) i Citus.

## <a name="overview"></a>Omówienie

Pojedynczy serwer to w pełni zarządzana usługa bazy danych o minimalnych wymaganiach dotyczących dostosowań bazy danych. Platforma jednoserwerowa została zaprojektowana tak, aby obsługiwała większość funkcji zarządzania bazami danych, takich jak stosowanie poprawek, kopii zapasowych, wysoka dostępność, zabezpieczenia z minimalną konfiguracją i kontrolą użytkownika. Architektura jest zoptymalizowana pod kątem zapewnienia dostępności na 99,99% w ramach pojedynczej strefy dostępności. Obsługuje ona wersję Community PostgreSQL 9,5, 9,6, 10 i 11. Usługa jest ogólnie dostępna dzisiaj w wielu [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/).

Pojedyncze serwery najlepiej nadają się do obsługi natywnych aplikacji w chmurze, które mają obsługiwać automatyczne stosowanie poprawek bez potrzeby szczegółowej kontroli nad harmonogramem poprawek i niestandardowymi ustawieniami konfiguracji PostgreSQL.

## <a name="high-availability"></a>Wysoka dostępność

Model wdrażania na jednym serwerze jest zoptymalizowany pod kątem wbudowanej wysokiej dostępności i elastyczności przy niższych kosztach. Architektura oddziela zasoby obliczeniowe i magazyn. Aparat bazy danych jest uruchamiany w zastrzeżonym kontenerze obliczeniowym, podczas gdy pliki danych znajdują się w usłudze Azure Storage. Magazyn przechowuje trzy lokalnie nadmiarowe kopie synchroniczne plików bazy danych, zapewniając trwałość danych.

W przypadku planowanych lub nieplanowanych zdarzeń trybu failover, jeśli serwer ulegnie awarii, usługa zachowuje wysoką dostępność serwerów za pomocą następującej zautomatyzowanej procedury:

1. Zostanie zainicjowany nowy kontener obliczeniowy.
2. Magazyn z plikami danych jest mapowany do nowego kontenera.
3. Aparat bazy danych PostgreSQL jest przełączany w tryb online w nowym kontenerze obliczeniowym.
4. Usługa bramy zapewnia przezroczyste przełączanie do trybu failover, co nie wymaga żadnych zmian po stronie aplikacji.
   
 :::image type="content" source="./media/overview/overview-azure-postgres-single-server.png" alt-text="Azure Database for PostgreSQL pojedynczy serwer":::

Typowe zakresy czasu pracy w trybie failover z 60-120 sekund. Natywny projekt w chmurze usługi Single Server umożliwia działowi IT wsparcie w 99,99% dostępności, eliminując koszt pasywnej rezerwy gorącą.

Wiodąca w branży Azure branżowa umowa dotycząca poziomu usług (SLA) na 99,99%, obsługiwana przez globalną sieć centrów danych zarządzanych przez firmę Microsoft, pomaga zapewnić, że aplikacje działają w 24/7.

## <a name="automated-patching"></a>Automatyczne stosowanie poprawek

Usługa wykonuje zautomatyzowane stosowanie poprawek podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych. Poprawki obejmują aktualizacje zabezpieczeń i oprogramowania. W przypadku aparatu PostgreSQL uaktualnienia wersji pomocniczej są automatyczne i uwzględniane jako część cyklu poprawek. Nie jest wymagane wykonanie jakiejkolwiek czynności przez użytkownika ani ustawień konfiguracji. Częstotliwość stosowania poprawek to usługa zarządzana na podstawie krytycznego ładunku. Ogólnie rzecz biorąc usługa jest zgodna z harmonogramem wydań w ramach ciągłej integracji i wydania. Użytkownicy mogą subskrybować powiadomienie o [planowanej konserwacji]() , aby otrzymywać powiadomienie o nadchodzącej konserwacji 72 godzin przed wydarzeniem.

## <a name="automatic-backups"></a>Automatyczne kopie zapasowe

Usługa Single Server automatycznie tworzy kopie zapasowe serwera i przechowuje je w skonfigurowanym lokalnie nadmiarowym (LRS) lub magazynie geograficznie nadmiarowym. Za pomocą kopii zapasowych można przywrócić serwer do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Czas przechowywania można opcjonalnie skonfigurować do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES. Aby uzyskać szczegółowe informacje, zobacz [kopie zapasowe](./concepts-backup.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Dostosowanie wydajności i skalowania w kilka sekund

Usługa Single Server jest dostępna w trzech warstwach SKU: podstawowa, Ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci. Warstwa Podstawowa najlepiej nadaje się w przypadku obciążeń o małym koszcie i niskich współbieżności. Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci są lepiej dopasowane do obciążeń produkcyjnych wymagających dużej współbieżności, skali i przewidywalnej wydajności. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Skalowanie magazynu jest w trybie online i obsługuje automatyczne przyrosty pamięci masowej. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Płacisz tylko za zasoby, z których korzystasz. Aby uzyskać szczegółowe informacje, zobacz [Warstwy cenowe]().

## <a name="enterprise-grade-security-compliance-and-governance"></a>Zabezpieczenia, zgodność i zarządzanie klasy korporacyjnej

Usługa Single Server używa zatwierdzonego modułu kryptograficznego FIPS 140-2 do szyfrowania magazynu danych w spoczynku. Dane, w tym kopie zapasowe i pliki tymczasowe utworzone podczas wykonywania zapytań, są szyfrowane. Usługa używa szyfru AES 256-bitowego zawartego w szyfrowaniu usługi Azure Storage, a klucze mogą być zarządzane przez system (ustawienie domyślne) lub [zarządzane przez klienta](). Usługa domyślnie szyfruje dane z użyciem protokołu Transport Layer Security (SSL/TLS). Usługa obsługuje protokół TLS w wersji 1,2, 1,1 i 1,0 z możliwością wymuszania [minimalnej wersji protokołu TLS]().

Usługa umożliwia prywatnym dostęp do serwerów za pomocą prywatnego linku i zapewnia funkcję zaawansowanej ochrony przed zagrożeniami. Zaawansowana ochrona przed zagrożeniami wykrywa anomalie działania wskazujące nietypowe i potencjalnie szkodliwe próby uzyskania dostępu do baz danych lub ich wykorzystania.

Oprócz uwierzytelniania natywnego usługa Single Server obsługuje uwierzytelnianie Azure Active Directory. Uwierzytelnianie usługi Azure AD to mechanizm łączenia się z serwerami PostgreSQL przy użyciu tożsamości zdefiniowanych i zarządzanych w usłudze Azure AD. Uwierzytelnianie za pomocą usługi Azure AD umożliwia zarządzanie tożsamościami użytkowników bazy danych i innymi usługami platformy Azure w centralnej lokalizacji, co upraszcza i scentralizowano kontrolę dostępu.

[Rejestrowanie inspekcji]() (w wersji zapoznawczej) jest dostępne do śledzenia wszystkich działań na poziomie bazy danych.

Usługa Single Server jest zgodna ze wszystkimi wiodącymi w branży certyfikatami, takimi jak FedRAMP, HIPAA, PCI DSS. Odwiedź [Centrum zaufania Azure](), aby uzyskać informacje o zabezpieczeniach platformy Azure.

Aby uzyskać więcej informacji na temat funkcji zabezpieczeń Azure Database for PostgreSQL, zobacz [Omówienie zabezpieczeń]().

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów

Usługa Single Server jest wyposażona w wbudowaną funkcję monitorowania wydajności i alertów. Wszystkie metryki platformy Azure mają częstotliwość jednominutową, a każda Metryka zapewnia 30-dniową historię. Można skonfigurować alerty dotyczące metryk. Usługa umożliwia konfigurowanie wolnych dzienników zapytań i zawiera zróżnicowaną funkcję [magazynu zapytań](./concepts-query-store.md) . Magazyn zapytań upraszcza Rozwiązywanie problemów z wydajnością, pomagając szybko znaleźć najdłuższych uruchomionych i większości zapytań intensywnie korzystających z zasobów. Korzystając z tych narzędzi, można szybko zoptymalizować obciążenia i skonfigurować serwer w celu uzyskania najlepszej wydajności. Aby uzyskać szczegółowe informacje, zobacz [monitorowanie](./concepts-monitoring.md) .

## <a name="migration"></a>Migracja

Usługa uruchamia społeczność PostgreSQL. Zapewnia to pełną zgodność aplikacji i wymaga minimalnego kosztu refaktoryzacji w celu przeprowadzenia migracji istniejącej aplikacji opracowanej w aparacie PostgreSQL do pojedynczego serwera usługi. Migrację do pojedynczego serwera można przeprowadzić przy użyciu jednej z następujących opcji:

- **Zrzuty i przywracanie** — w przypadku migracji w trybie offline, w których użytkownicy mogą zapewnić przestój, zrzuty i przywracanie przy użyciu narzędzi społeczności, takich jak Pg_dump i Pg_restore mogą zapewnić najszybszy sposób migracji. Aby uzyskać szczegółowe informacje [, zobacz Migrowanie przy użyciu zrzutów i przywracania](./howto-migrate-using-dump-and-restore.md) .
- **Azure Database Migration Service** — w celu zapewnienia bezproblemowej i uproszczonej migracji na pojedynczy serwer z minimalnym przestojem, Azure Database Migration Service można wykorzystać. Zobacz [DMS za pośrednictwem portalu](../dms/tutorial-postgresql-azure-postgresql-online-portal.md) i [DMS za pomocą interfejsu wiersza polecenia](../dms/tutorial-postgresql-azure-postgresql-online.md).

## <a name="contacts"></a>Kontakty

Aby dowiedzieć się więcej na temat pytań lub sugestii dotyczących pracy z Azure Database for PostgreSQL, Wyślij wiadomość e-mail do zespołu Azure Database for PostgreSQL ([ @Ask Azure DB dla PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Ten adres e-mail nie jest aliasem pomocy technicznej.

Ponadto, w zależności od potrzeb, należy wziąć pod uwagę następujące punkty kontaktowe:

- Aby skontaktować się z pomocą techniczną platformy Azure, [wyślij zgłoszenie z witryny Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Następne kroki

Teraz, po zapoznaniu się z wprowadzeniem do Azure Database for PostgreSQL trybu wdrożenia pojedynczego serwera, możesz:
- Utwórz swój pierwszy serwer.
