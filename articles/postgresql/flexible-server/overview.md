---
title: Azure Database for PostgreSQL — elastyczny serwer
description: Zawiera omówienie Azure Database for PostgreSQL-elastyczny serwer.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 03/17/2021
ms.openlocfilehash: 8e5c204709f0eadb6c1a4b6f479d4833f335b264
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594890"
---
# <a name="azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL — elastyczny serwer

[Azure Database for PostgreSQL](../overview.md) obsługiwane przez PostgreSQL Community Edition są dostępne w trzech trybach wdrożenia:

- [Pojedynczy serwer](../overview-single-server.md)
- Serwer elastyczny (wersja zapoznawcza)
- Hiperskala (Citus)

Ten artykuł zawiera omówienie i wprowadzenie do podstawowych pojęć związanych z elastycznym modelem wdrażania serwera.

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

## <a name="overview"></a>Omówienie

Serwer elastyczny Azure Database for PostgreSQL to w pełni zarządzana usługa bazy danych, która umożliwia dokładniejszą kontrolę i elastyczność w zakresie funkcji zarządzania bazami danych i ustawień konfiguracji. Ogólnie rzecz biorąc, usługa zapewnia większą elastyczność i dostosowanie konfiguracji serwera na podstawie wymagań użytkownika. Elastyczna architektura serwera pozwala użytkownikom na rozmieścić aparat bazy danych z warstwą klienta w celu zmniejszenia opóźnień, wybrać wysoką dostępność w ramach jednej strefy dostępności i w wielu strefach dostępności. Elastyczne serwery zapewniają również lepszą kontrolę kosztów z możliwością zatrzymywania/uruchamiania serwera i warstwy obliczeniowej do naliczania, które są idealnym rozwiązaniem w przypadku obciążeń, które nie wymagają ciągłego stałej pojemności obliczeniowej. Usługa obecnie obsługuje społeczność PostgreSQL 11 i 12. Usługa jest obecnie dostępna w wersji zapoznawczej, już dziś w wielu  [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/).

![Elastyczny serwer — Omówienie](./media/overview/overview-flexible-server.png)


Serwery elastyczne najlepiej nadają się do

- Rozwój aplikacji wymagający lepszej kontroli i dostosowań.
- Strefa nadmiarowa wysokiej dostępności
- Zarządzane okna obsługi
  
## <a name="high-availability"></a>Wysoka dostępność

Elastyczny model wdrażania serwera jest przeznaczony do obsługi wysokiej dostępności w ramach strefy pojedynczej dostępności i wielu stref dostępności. Architektura oddziela zasoby obliczeniowe i magazyn. Aparat bazy danych działa na maszynie wirtualnej z systemem Linux, podczas gdy pliki danych znajdują się w usłudze Azure Storage. Magazyn przechowuje trzy lokalnie nadmiarowe kopie synchroniczne plików bazy danych, zapewniając trwałość danych.

W przypadku planowanych lub nieplanowanych zdarzeń trybu failover, jeśli serwer ulegnie awarii, usługa zachowuje wysoką dostępność serwerów za pomocą następującej zautomatyzowanej procedury:

1. Zainicjowano nową maszynę wirtualną obliczeniową z systemem Linux.
2. Magazyn z plikami danych jest mapowany na nową maszynę wirtualną
3. Aparat bazy danych PostgreSQL jest przełączany w tryb online na nowej maszynie wirtualnej.

Na poniższej ilustracji przedstawiono przejście do awarii maszyny wirtualnej i magazynu.

 :::image type="content" source="./media/overview/overview-azure-postgres-flex-virtualmachine.png" alt-text="Elastyczny serwer — awarie maszyn wirtualnych i magazynu":::

Jeśli skonfigurowano nadmiarową wysoką dostępność strefy, usługa inicjuje i utrzymuje serwer rezerwy dynamicznej w strefie dostępności w tym samym regionie świadczenia usługi Azure. Zmiany danych na serwerze źródłowym są synchronicznie replikowane na serwerze rezerwy w celu zapewnienia zerowej utraty danych. Gdy zostanie wyzwolone planowane lub nieplanowane zdarzenie trybu failover z nadmiarową strefą o nieodpowiedniej dostępności, serwer rezerwy natychmiast przejdzie w tryb online i jest dostępny do przetwarzania przychodzących transakcji. Pozwala to na odporność usługi ze względu na awarię strefy dostępności w regionie świadczenia usługi Azure, który obsługuje wiele stref dostępności, jak pokazano na poniższej ilustracji.

 :::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Strefa nadmiarowa wysokiej dostępności":::

 Aby uzyskać więcej informacji, zobacz [dokument o wysokiej dostępności](./concepts-high-availability.md) .

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatyczne stosowanie poprawek za pomocą zarządzanego okna obsługi

Usługa wykonuje zautomatyzowane stosowanie poprawek podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych. Poprawki obejmują aktualizacje zabezpieczeń i oprogramowania. W przypadku aparatu PostgreSQL wersje pomocnicze są również dołączone jako część planowanej wersji konserwacji. Użytkownicy mogą konfigurować harmonogram stosowania poprawek, aby był zarządzany przez system lub definiować własny harmonogram. W harmonogramie konserwacji poprawka jest stosowana i może być konieczne ponowne uruchomienie serwera w ramach procesu poprawek w celu ukończenia aktualizacji. Zgodnie z harmonogramem niestandardowym użytkownicy mogą przewidywalnić swój cykl poprawek i wybrać okno obsługi z minimalnym wpływem na firmę. Ogólnie rzecz biorąc usługa jest zgodna z harmonogramem wydań w ramach ciągłej integracji i wydania.

## <a name="automatic-backups"></a>Automatyczne kopie zapasowe

Elastyczna usługa serwerowa automatycznie tworzy kopie zapasowe serwera i przechowuje je w skonfigurowanym lokalnie miejscu dla strefy (ZRS). Za pomocą kopii zapasowych można przywrócić serwer do dowolnego punktu w czasie w ramach okresu przechowywania kopii zapasowych. Domyślny okres przechowywania kopii zapasowych wynosi siedem dni. Czas przechowywania można opcjonalnie skonfigurować do 35 dni. Wszystkie kopie zapasowe są szyfrowane za pomocą 256-bitowego szyfrowania AES. Aby uzyskać więcej informacji, zobacz [kopie zapasowe](./concepts-backup-restore.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Dostosowanie wydajności i skalowania w kilka sekund

Elastyczna usługa serwera jest dostępna w trzech warstwach obliczeniowych: możliwość przetworzenia, Ogólnego przeznaczenia i zoptymalizowana pod kątem pamięci. Warstwa z możliwością naliczania jest Najlepsza dla obciążeń związanych z programowaniem i niskim kosztem współbieżności, które nie wymagają ciągłego stałej pojemności obliczeniowej. Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci są lepiej dopasowane do obciążeń produkcyjnych wymagających dużej współbieżności, skali i przewidywalnej wydajności. Pierwszą aplikację można skompilować w małej bazie danych przez kilka dolarów miesięcznie, a następnie bezproblemowo dostosować skalę do potrzeb rozwiązania.

## <a name="stopstart-server-to-lower-tco"></a>Zatrzymaj/Uruchom serwer, aby zmniejszyć całkowity koszt posiadania

Elastyczna usługa serwerowa umożliwia zatrzymywanie i uruchamianie serwera na żądanie w celu obniżenia całkowitego kosztu posiadania. Rozliczanie w warstwie obliczeniowej jest zatrzymywane natychmiast po zatrzymaniu serwera. Dzięki temu można mieć znaczny koszt oszczędności podczas opracowywania, testowania i dla przewidywalnych obciążeń produkcyjnych. Serwer pozostaje w stanie zatrzymania przez siedem dni, chyba że zostanie uruchomiony ponownie wcześniej.

## <a name="enterprise-grade-security"></a>Bezpieczeństwo klasy korporacyjnej

Elastyczna usługa serwerowa używa zatwierdzonego modułu kryptograficznego FIPS 140-2 do szyfrowania magazynu danych w spoczynku. Dane, w tym kopie zapasowe i pliki tymczasowe utworzone podczas wykonywania zapytań, są szyfrowane. Usługa używa szyfru AES 256-bitowego zawartego w szyfrowaniu usługi Azure Storage, a klucze mogą być zarządzane przez system (domyślnie). Usługa domyślnie szyfruje dane z użyciem protokołu Transport Layer Security (SSL/TLS). Usługa wymusza i obsługuje tylko protokół TLS w wersji 1,2.

Elastyczne serwery umożliwiają pełny prywatny dostęp do serwerów za pomocą usługi Azure Virtual Network (Integracja z siecią VNet). Serwery w usłudze Azure Virtual Network mogą być osiągalne i połączone tylko za pomocą prywatnych adresów IP. Dzięki integracji z siecią wirtualną dostęp publiczny jest zabroniony i serwery nie można osiągnąć przy użyciu publicznych punktów końcowych.

## <a name="monitoring-and-alerting"></a>Monitorowanie i zgłaszanie alertów

Elastyczna usługa serwera jest wyposażona w wbudowaną funkcję monitorowania wydajności i alertów. Wszystkie metryki platformy Azure mają częstotliwość jednominutową, a każda Metryka zapewnia 30-dniową historię. Można skonfigurować alerty dotyczące metryk. Usługa udostępnia metryki serwera hosta do monitorowania wykorzystania zasobów i umożliwia konfigurowanie wolnych dzienników zapytań. Korzystając z tych narzędzi, można szybko zoptymalizować obciążenia i skonfigurować serwer w celu uzyskania najlepszej wydajności.

## <a name="azure-regions"></a>Regiony świadczenia usługi Azure

Jedną z zalet korzystania z obciążeń na platformie Azure jest globalny zasięg. Elastyczny serwer jest dostępny dzisiaj w następujących regionach platformy Azure:

| Region (Region) | Dostępność | Strefa — nadmiarowa HA | 
| --- | --- | --- |
| West Europe | :heavy_check_mark: | :heavy_check_mark: |
| Europa Północna | :heavy_check_mark: | :heavy_check_mark: |
| Południowe Zjednoczone Królestwo | :heavy_check_mark: | :heavy_check_mark: | 
| Wschodnie stany USA 2 | :heavy_check_mark: | :heavy_check_mark: |
| Zachodnie stany USA 2 | :heavy_check_mark: | :heavy_check_mark: |
| Central US | :heavy_check_mark: | :heavy_check_mark: | 
| East US | :heavy_check_mark: | :heavy_check_mark: | 
| Southeast Asia | :heavy_check_mark: | :heavy_check_mark: |
| Japonia Wschodnia | :heavy_check_mark: | :heavy_check_mark: | 
| Australia Wschodnia | :heavy_check_mark: | :heavy_check_mark: | 
| Kanada Środkowa | :heavy_check_mark: | :heavy_check_mark: | 

Będziemy nadal dodawać więcej regionów dla elastycznego serwera.

## <a name="migration"></a>Migracja

Usługa uruchamia wersję społeczności PostgreSQL. Pozwala to na pełną zgodność aplikacji i wymaga minimalnego kosztu refaktoryzacji w celu przeprowadzenia migracji istniejącej aplikacji opracowanej w aparacie PostgreSQL na elastyczny serwer. 

- **Zrzuty i przywracanie** — w przypadku migracji w trybie offline, w których użytkownicy mogą zapewnić przestój, zrzuty i przywracanie przy użyciu narzędzi społeczności, takich jak pg_dump i pg_restore mogą zapewnić najszybszy sposób migracji. Aby uzyskać szczegółowe informacje [, zobacz Migrowanie przy użyciu zrzutów i przywracania](../howto-migrate-using-dump-and-restore.md) .
- **Azure Database Migration Service** — w celu zapewnienia bezproblemowej i uproszczonej migracji do elastycznego serwera z minimalnym przestojem Azure Database Migration Service można korzystać z nich. Zobacz [DMS za pośrednictwem portalu](../../dms/tutorial-postgresql-azure-postgresql-online-portal.md) i [DMS za pomocą interfejsu wiersza polecenia](../../dms/tutorial-postgresql-azure-postgresql-online.md). Można przeprowadzić migrację z serwera Azure Database for PostgreSQL-pojedynczego do elastycznego serwera. Aby uzyskać szczegółowe informacje, zobacz ten [artykuł DMS](../../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) .

## <a name="contacts"></a>Kontakty
W przypadku pytań lub sugestii, które mogą znajdować się na Azure Database for PostgreSQL elastycznym serwerze, Wyślij wiadomość e-mail do zespołu Azure Database for PostgreSQL ([ @Ask Azure DB dla PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Należy pamiętać, że ten adres e-mail nie jest aliasem pomocy technicznej.

Ponadto, w zależności od potrzeb, należy wziąć pod uwagę następujące punkty kontaktowe:

- Aby skontaktować się z pomocą techniczną platformy Azure, [wyślij zgłoszenie z witryny Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby rozwiązać problem z Twoim kontem, wyślij [żądanie obsługi](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) w portalu Azure Portal.
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
  

## <a name="next-steps"></a>Następne kroki

Teraz, po zapoznaniu się z wprowadzeniem do Azure Database for PostgreSQL elastyczny tryb wdrażania serwera, możesz utworzyć pierwszy serwer: [utwórz Azure Database for PostgreSQL-elastyczny serwer przy użyciu Azure Portal](./quickstart-create-server-portal.md)