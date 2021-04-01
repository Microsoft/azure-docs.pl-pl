---
title: Co to jest Azure Database for PostgreSQL
description: Zawiera omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL w kontekście elastycznego serwera.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: c3ea7930f41fe89538a817da032e993e534db9cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92491329"
---
# <a name="what-is-azure-database-for-postgresql"></a>Co to jest usługa Azure Database for PostgreSQL?

Azure Database for PostgreSQL jest usługą relacyjnej bazy danych w chmurze firmy Microsoft opartą na systemie [PostgreSQL Community Edition](https://www.postgresql.org/) (dostępnym w ramach aparatu bazy danych licencji GPLv2). Zalety Azure Database for PostgreSQL:

- Wbudowana wysoka dostępność
- Ochrona danych przy użyciu automatycznych kopii zapasowych i przywracanie do punktu w czasie przez maksymalnie 35 dni.
- Automatyczna konserwacja podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych, aby zapewnić bezpieczeństwo i aktualność usługi.
- Przewidywalna wydajność z zastosowaniem inkluzywnego cennika płatności zgodnie z rzeczywistym użyciem.
- Elastyczne skalowanie w ciągu kilku sekund.
- Bezpieczeństwo klasy korporacyjnej i wiodąca w branży zgodność w celu ochrony poufnych danych w czasie spoczynku i ruchowo.
- Monitorowanie i Automatyzacja upraszczające zarządzanie i monitorowanie wdrożeń na dużą skalę.
- Wiodące w branży środowisko pomocy technicznej.

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Azure Database for PostgreSQL":::

Powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Umożliwiają one skoncentrowanie się na szybkim tworzeniu aplikacji i skróceniu czasu wprowadzenia na rynek zamiast przydzielania cennych czasu i zasobów do zarządzania maszynami wirtualnymi i infrastrukturą. Ponadto możesz kontynuować tworzenie aplikacji przy użyciu tych samych wybranych przez siebie narzędzi typu open source oraz platformy w celu dostarczania zawartości z wymaganą szybkością i skutecznością bez konieczności uczenia się nowych umiejętności.

## <a name="deployment-models"></a>Modele wdrażania

Azure Database for PostgreSQL obsługiwane przez PostgreSQL Community Edition są dostępne w trzech trybach wdrożenia:

- Pojedynczy serwer
- Serwer elastyczny (wersja zapoznawcza)
- Hiperskala (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL — pojedynczy serwer

Azure Database for PostgreSQL pojedynczym serwerze to w pełni zarządzana usługa bazy danych z minimalnymi wymaganiami dotyczącymi dostosowywania bazy danych. Platforma jednoserwerowa została zaprojektowana tak, aby obsługiwała większość funkcji zarządzania bazami danych, takich jak stosowanie poprawek, kopii zapasowych, wysoka dostępność, zabezpieczenia z minimalną konfiguracją i kontrolą użytkownika. Architektura jest zoptymalizowana pod kątem wbudowanej wysokiej dostępności dzięki dostępności na 99,99% w ramach pojedynczej strefy dostępności. Obsługuje ona wersję Community PostgreSQL 9,5, 9, 6, 10 i 11. Usługa jest ogólnie dostępna dzisiaj w wielu [regionach świadczenia usługi Azure](https://azure.microsoft.com/global-infrastructure/services/).

Opcja wdrożenia pojedynczego serwera oferuje trzy warstwy cenowe: Podstawowa, Ogólnego przeznaczenia lub Zoptymalizowana pod kątem pamięci. Każda warstwa oferuje inne możliwości w zakresie zasobów, aby wspierać obciążenia bazy danych. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Zapłacisz tylko za potrzebne zasoby i tylko wtedy, gdy będą używane. Aby uzyskać szczegółowe informacje, zobacz [Warstwy cenowe](./concepts-pricing-tiers.md).

Pojedyncze serwery najlepiej nadają się do obsługi natywnych aplikacji w chmurze, które mają obsługiwać automatyczne stosowanie poprawek bez potrzeby szczegółowej kontroli nad harmonogramem poprawek i niestandardowymi ustawieniami konfiguracji PostgreSQL.

Aby zapoznać się z szczegółowym omówieniem trybu wdrażania na jednym serwerze, zobacz [Omówienie jednego serwera](./overview-single-server.md).

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL — serwer elastyczny (wersja zapoznawcza)

Azure Database for PostgreSQL elastyczny serwer to w pełni zarządzana usługa bazy danych, która umożliwia dokładniejszą kontrolę i elastyczność w zakresie funkcji zarządzania bazami danych i ustawień konfiguracji. Ogólnie rzecz biorąc, usługa zapewnia większą elastyczność i dostosowanie w zależności od wymagań użytkownika. Elastyczna architektura serwera umożliwia użytkownikom wybór wysokiej dostępności w ramach strefy pojedynczej dostępności i wielu stref dostępności. Elastyczny serwer zapewnia lepszą kontrolę kosztów, dzięki czemu można zatrzymać/uruchomić serwer i warstwę obliczeniową z możliwością naliczania, idealne rozwiązanie w przypadku obciążeń, które nie wymagają ciągłej pojemności obliczeniowej. Usługa obecnie obsługuje wersje społecznościowe PostgreSQL 11 i 12 z planami, aby wkrótce dodać nowsze wersje. Usługa jest obecnie w publicznej wersji zapoznawczej, która jest dostępna dzisiaj w wielu regionach świadczenia usługi Azure.

Serwery elastyczne najlepiej nadają się do

- Rozwój aplikacji wymagający lepszej kontroli i dostosowań.
- Kontrolki optymalizacji kosztów z możliwością zatrzymania/uruchomienia serwera.
- Strefa nadmiarowa wysokiej dostępności
- Zarządzane okna obsługi
  
Aby zapoznać się z szczegółowym omówieniem elastycznego trybu wdrażania serwera, zobacz [elastyczny przegląd serwera](./flexible-server/overview.md).

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL — skalowanie (Citus)

Opcja wdrożenia Hiperskala (Citus) skaluje zapytania w poziomie na wielu maszynach przy użyciu fragmentowania. Aparat zapytań przetwarza równolegle przychodzące zapytania SQL między tymi serwerami w celu przyspieszenia odpowiedzi w dużych zestawach danych. Obsługuje on aplikacje, które wymagają większej skali i wydajności. Ogólnie chodzi o obciążenia, które zbliżają się do rozmiaru 100 GB lub już ten rozmiar przekroczyły.

Opcja wdrożenia Hiperskala (Citus) zapewnia następujące funkcje:

- Skalowanie w poziomie na wielu maszynach przy użyciu fragmentowania
- Przetwarzanie równoległe zapytań na tych serwerach w celu uzyskania szybszych odpowiedzi w dużych zestawach danych
- Doskonała pomoc techniczna dla aplikacji wielodostępnych, analizy danych operacyjnych w czasie rzeczywistym oraz obciążeń transakcyjnych o wysokiej przepływności
  
Aplikacje skompilowane dla PostgreSQL mogą uruchamiać zapytania rozproszone na potrzeby tworzenia i skalowania (Citus) przy użyciu standardowych [bibliotek połączeń](./concepts-connection-libraries.md) i minimalnych zmian.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat trzech trybów wdrażania dla Azure Database for PostgreSQL i wybierz odpowiednie opcje w zależności od potrzeb.

- [Pojedynczy serwer](./overview-single-server.md)
- [Elastyczny serwer](./flexible-server/overview.md)
- Hiperskala (Citus)