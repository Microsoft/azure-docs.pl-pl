---
title: Przegląd Azure Database for PostgreSQL-ze skalowaniem (Citus)
description: Zawiera omówienie opcji wdrożenia Citus (preskaling)
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 1734128384d63749d3c777cf6315278fced9d140
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025144"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Co to jest Azure Database for PostgreSQL-ze skalowaniem (Citus)?

Azure Database for PostgreSQL to usługa relacyjnej bazy danych w chmurze firmy Microsoft skompilowanej dla deweloperów. Jest on oparty na wersji społecznościowej aparatu bazy danych [PostgreSQL](https://www.postgresql.org/) typu open source.

Funkcja przeskalowania (Citus) to opcja wdrażania, która skaluje zapytania na wielu maszynach przy użyciu fragmentowania. Aparat zapytań przetwarza równolegle przychodzące zapytania SQL między tymi serwerami w celu przyspieszenia odpowiedzi w dużych zestawach danych. Obsługuje ona aplikacje, które wymagają większej skali i wydajności niż inne opcje wdrażania: zwykle obciążenia, które zbliżają się do--lub już przekraczają--100 GB danych.

Funkcja przeskalowania (Citus) zapewnia:

- Skalowanie w poziomie na wielu maszynach przy użyciu fragmentowania
- Przetwarzanie równoległe zapytań na tych serwerach w celu uzyskania szybszych odpowiedzi w dużych zestawach danych
- Doskonała pomoc techniczna dla aplikacji wielodostępnych, analizy operacyjnej w czasie rzeczywistym oraz obciążeń transakcyjnych o wysokiej przepływności

Aplikacje skompilowane dla PostgreSQL mogą uruchamiać zapytania rozproszone na potrzeby tworzenia i skalowania (Citus) przy użyciu standardowych [bibliotek połączeń](./concepts-connection-libraries.md) i minimalnych zmian.

## <a name="next-steps"></a>Następne kroki

- Zacznij od [utworzenia pierwszej](./quickstart-create-hyperscale-portal.md) grupy serwerów Azure Database for PostgreSQL-Citus.
- Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/postgresql/) , aby obporównania kosztów i kalkulatory. Funkcja [przeskaling](concepts-hyperscale-reserved-pricing.md) (Citus) oferuje również płatne rabaty wystąpień zarezerwowanych
- Określ najlepszy [początkowy rozmiar](howto-hyperscale-scale-initial.md) grupy serwerów
