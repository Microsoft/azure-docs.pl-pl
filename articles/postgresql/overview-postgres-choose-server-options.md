---
title: Wybierz odpowiednią opcję serwera PostgreSQL na platformie Azure
description: Zawiera wskazówki dotyczące wybierania odpowiedniej opcji serwera PostgreSQL dla wdrożeń.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: db80ae66761e49ce90f25a22f0e74cf8a1300796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797233"
---
# <a name="choose-the-right-postgresql-server-option-in-azure"></a>Wybierz odpowiednią opcję serwera PostgreSQL na platformie Azure

Dzięki platformie Azure obciążenia serwera PostgreSQL mogą działać w hostowanej infrastrukturze maszyny wirtualnej jako usługi (IaaS) lub jako usługa hostowana jako platforma (PaaS). PaaS ma wiele opcji wdrażania, z których każda ma wiele warstw usług. Po wybraniu opcji między IaaS i PaaS należy zdecydować, czy chcesz zarządzać bazą danych, stosować poprawki i tworzyć kopie zapasowe, czy też chcesz delegować te operacje na platformę Azure.

Podczas podejmowania decyzji Rozważ następujące trzy opcje w PaaS lub alternatywnie uruchomione na maszynach wirtualnych platformy Azure (IaaS)
- [Azure Database for PostgreSQL — pojedynczy serwer](./overview-single-server.md)
- [Serwer elastyczny usługi Azure Database for PostgreSQL](./flexible-server/overview.md)
- [Azure Database for PostgreSQL — skalowanie (Citus)](hyperscale/index.yml)

Opcja **PostgreSQL na maszynach wirtualnych platformy Azure** znajduje się w kategorii branża IaaS. Za pomocą tej usługi można uruchomić serwer PostgreSQL w ramach w pełni zarządzanej maszyny wirtualnej na platformie Azure w chmurze. Wszystkie najnowsze wersje i wersje programu PostgreSQL można zainstalować na maszynie wirtualnej IaaS. W najbardziej znaczącej różnicy między Azure Database for PostgreSQL PostgreSQL na maszynach wirtualnych platformy Azure oferuje kontrolę nad aparatem bazy danych. Jednak ten formant jest kosztem odpowiedzialności za zarządzanie maszynami wirtualnymi i wieloma zadaniami administracyjnymi bazy danych (DBA). Te zadania obejmują obsługę i stosowanie poprawek do serwerów baz danych, odzyskiwania bazy danych i projektu o wysokiej dostępności.

Główne różnice między tymi opcjami są wymienione w poniższej tabeli:

| **Atrybut** | **Postgres na maszynach wirtualnych platformy Azure** | **PostgreSQL jako PaaS** |
| ----- | ----- | ----- |
| <B> Umowa SLA dotycząca dostępności |– 99,99% z zestawami dostępności <br> -99,95% z pojedynczymi maszynami wirtualnymi | -Jeden serwer – 99,99% <br> -Elastyczny serwer — nie jest dostępny w wersji zapoznawczej <br> -99,95 Citus% (gdy wysoka dostępność jest włączona)|
| <B> Stosowanie poprawek systemu operacyjnego i PostgreSQL | — Zarządzane przez klienta | -Jeden serwer — automatyczny <br> -Elastyczny serwer — automatycznie z opcjonalnym oknem zarządzanym przez klienta <br> -Ze skalowaniem (Citus) — automatyczne |
| <B> Wysoka dostępność | -Klienci mogą korzystać z architektów, implementować, testować i obsługiwać wysoką dostępność. Możliwości mogą obejmować klastrowanie, replikację itp. | -Jeden serwer: wbudowane <br> -Elastyczny serwer: wbudowany <br> -Ze skalowaniem (Citus): kompilacja z wstrzymywaniem |
| <B> Nadmiarowość stref | — Maszyny wirtualne platformy Azure można skonfigurować do uruchamiania w różnych strefach dostępności. W przypadku rozwiązania lokalnego klienci muszą tworzyć i obsługiwać własne pomocnicze centrum danych oraz zarządzać nimi. | -Jeden serwer: nie <br> -Elastyczny serwer: tak <br> -Citus): nie |
| <B> Scenariusz hybrydowy | — Zarządzane przez klienta |-Jeden serwer: Odczyt i replika <br> -Elastyczny serwer: nie jest dostępny w wersji zapoznawczej <br> -Citus): nie |
| <B> Tworzenie kopii zapasowej i przywracanie | — Zarządzane przez klienta | -Jeden serwer: Wbudowana z konfiguracją użytkownika dla lokalnego i geograficznego <br> -Elastyczny serwer: wbudowany z konfiguracją użytkownika w magazynie strefowo nadmiarowy <br> -Citus): Wbudowana |
| <B> Operacje monitorowania bazy danych | — Zarządzane przez klienta | — Pojedynczy serwer, elastyczny serwer i funkcja wieloskalowania (Citus): Wszyscy klienci oferują możliwość ustawiania alertów dotyczących operacji bazy danych i działania na podstawie progów. |
| <B> Zaawansowana ochrona przed zagrożeniami | — Klienci muszą samodzielnie skompilować tę ochronę. |-Jeden serwer: tak <br> -Elastyczny serwer: nie jest dostępny w wersji zapoznawczej <br> -Citus): nie |
| <B> Odzyskiwanie po awarii | — Zarządzane przez klienta | -Pojedynczy serwer: geograficznie nadmiarowa kopia zapasowa i geograficzna do odczytu <br> -Elastyczny serwer: nie jest dostępny w wersji zapoznawczej <br> -Citus): nie |
| <B> Inteligentna wydajność | — Zarządzane przez klienta | -Jeden serwer: tak <br> -Elastyczny serwer: nie jest dostępny w wersji zapoznawczej <br> -Citus): nie |

## <a name="total-cost-of-ownership-tco"></a>Łączny koszt posiadania (TCO)

TCO to często podstawowe zagadnienia, które określa najlepsze rozwiązanie do hostowania baz danych. Jest to prawdziwe, niezależnie od tego, czy jesteś w trakcie uruchamiania z małym środkiem pieniężnym czy zespołem w firmie, który działa w ramach ścisłych ograniczeń budżetowych. W tej sekcji opisano podstawowe informacje dotyczące rozliczeń i licencjonowania na platformie Azure, które mają zastosowanie do Azure Database for PostgreSQL i PostgreSQL na maszynach wirtualnych platformy Azure.

## <a name="billing"></a>Rozliczenia

Azure Database for PostgreSQL jest obecnie dostępna jako usługa w kilku warstwach z różnymi cenami dla zasobów. Wszystkie zasoby są rozliczane godzinowo według ustalonej stawki. Aby uzyskać najnowsze informacje na temat aktualnie obsługiwanych warstw usług, rozmiarów obliczeń i ilości magazynu, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/postgresql/server/) można dynamicznie dostosować warstwy usług i rozmiary obliczeń w taki sposób, aby były zgodne z różnymi potrzebami przepływności aplikacji. Opłaty są naliczane za wychodzący ruch internetowy przy zwykłych [stawkach za transfer danych](https://azure.microsoft.com/pricing/details/data-transfers/).

W przypadku Azure Database for PostgreSQL firma Microsoft automatycznie konfiguruje, poprawki i uaktualnia oprogramowanie bazy danych. Te automatyczne akcje zmniejszają koszty administracyjne. Ponadto Azure Database for PostgreSQL ma możliwości [automatycznego tworzenia kopii zapasowych]() . Te funkcje pomagają osiągnąć znaczący koszt oszczędności, zwłaszcza w przypadku dużej liczby baz danych. W przeciwieństwie do PostgreSQL na maszynach wirtualnych platformy Azure można wybrać i uruchomić dowolną wersję PostgreSQL. Należy jednak płacić za zainicjowaną maszynę wirtualną, koszt magazynu związany z danymi, kopiami zapasowymi, danymi monitorowania i magazynem dzienników oraz kosztami określonego typu licencji PostgreSQL (jeśli istnieje).

Azure Database for PostgreSQL zapewnia wbudowaną wysoką dostępność dla dowolnego rodzaju przerw na poziomie węzła, utrzymując gwarancję SLA na 99,99% dla usługi. Jednak w przypadku wysokiej dostępności bazy danych na maszynach wirtualnych można używać opcji wysokiej dostępności, takich jak [replikacja strumieniowa](https://www.postgresql.org/docs/12/warm-standby.html#STREAMING-REPLICATION) , która jest dostępna w bazie danych PostgreSQL. Korzystanie z obsługiwanej opcji wysokiej dostępności nie zapewnia dodatkowej umowy SLA. Jednak pozwala to osiągnąć więcej niż 99,99% dostępności bazy danych przy dodatkowych kosztach i obciążeniu administracyjnym.

Aby uzyskać więcej informacji na temat cen, zobacz następujące artykuły:
- [Cennik Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/)
- [Cennik maszyny wirtualnej](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Administracja

W przypadku wielu firm decyzja o przejściu do usługi w chmurze polega na tym, że jest to bardzo dużo na odciążeniu złożoności administracji, ponieważ ma ona koszt.

W programie IaaS firmy Microsoft:

- Zarządza podstawową infrastrukturą.
- Zapewnia zautomatyzowane stosowanie poprawek dla podstawowego sprzętu i systemu operacyjnego

W programie PaaS firmy Microsoft:

- Zarządza podstawową infrastrukturą.
- Zapewnia zautomatyzowane stosowanie poprawek dla podstawowego sprzętu, systemu operacyjnego i aparatu bazy danych.
- Zarządza wysoką dostępnością bazy danych.
- Automatycznie wykonuje kopie zapasowe i replikuje wszystkie dane w celu zapewnienia odzyskiwania po awarii.
- Domyślnie szyfruje dane przechowywane i ruchowo.
- Monitoruje serwer i oferuje funkcje do uzyskiwania szczegółowych informacji o wydajności zapytań i wydajności.

Za pomocą Azure Database for PostgreSQL można nadal administrować swoją bazą danych. Nie trzeba jednak już zarządzać aparatem bazy danych, systemem operacyjnym ani sprzętem. Przykłady elementów, którymi można zarządzać, to m.in.:

- Bazy danych
- Logowanie
- Dostrajanie indeksu
- Dostrajanie zapytań
- Inspekcja
- Zabezpieczenia

Ponadto skonfigurowanie wysokiej dostępności w innym centrum danych wymaga minimalnej konfiguracji lub jej administracji.

- Dzięki PostgreSQL na maszynach wirtualnych platformy Azure masz pełną kontrolę nad systemem operacyjnym i konfiguracją wystąpienia serwera PostgreSQL. W przypadku maszyny wirtualnej należy określić, kiedy należy zaktualizować lub uaktualnić system operacyjny i oprogramowanie bazy danych oraz jakie poprawki mają być stosowane. Należy również określić, kiedy zainstalować dodatkowe oprogramowanie, np. aplikację antywirusową. Niektóre funkcje automatyczne zapewniają znacznie uproszczenie stosowania poprawek, kopii zapasowych i wysokiej dostępności. Można kontrolować rozmiar maszyny wirtualnej, liczbę dysków i ich konfiguracje magazynu. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych i usług w chmurze dla platformy Azure](../virtual-machines/sizes.md).

## <a name="time-to-move-to-azure-postgresql-service-paas"></a>Czas przejścia do usługi Azure PostgreSQL Service (PaaS)

- Azure Database for PostgreSQL to odpowiednie rozwiązanie dla aplikacji zaprojektowanych w chmurze, gdy produktywność deweloperów i krótki czas wprowadzenia na rynek nowych rozwiązań mają kluczowe znaczenie. Dzięki funkcjom programistycznym, takim jak DBA, usługa jest odpowiednia dla architektów i deweloperów rozwiązań w chmurze, ponieważ obniża potrzebę zarządzania bazowym systemem operacyjnym i bazą danych.

- Aby uniknąć czasu i wydatków pozyskiwania nowego sprzętu lokalnego, PostgreSQL na maszynach wirtualnych platformy Azure to odpowiednie rozwiązanie dla aplikacji, które wymagają szczegółowej kontroli i dostosowania aparatu PostgreSQL, które nie są obsługiwane przez usługę lub wymagają dostępu do podstawowego systemu operacyjnego.

## <a name="next-steps"></a>Następne kroki

- Zobacz cennik usługi Azure Database for [PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/).
- Rozpoczęcie pracy przez utworzenie pierwszego serwera.