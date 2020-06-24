---
title: Omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL
description: Zawiera omówienie usługi relacyjnej bazy danych Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: deb720638e0921762eb9c5800d218c3cfccb7f55
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213605"
---
# <a name="what-is-azure-database-for-postgresql"></a>Co to jest usługa Azure Database for PostgreSQL?
Azure Database for PostgreSQL to usługa relacyjnej bazy danych w chmurze firmy Microsoft skompilowanej dla deweloperów. Jest on oparty na wersji społecznościowej aparatu bazy danych [PostgreSQL](https://www.postgresql.org/) Open Source i jest dostępny w dwóch opcjach wdrażania: pojedynczy serwer i funkcja do skalowania (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL — pojedynczy serwer
Opcja wdrożenia pojedynczego serwera oferuje następujące funkcje:

- Wbudowana [wysoka dostępność](concepts-high-availability.md) bez dodatkowych kosztów (99,99% umowy SLA)
- Przewidywalna wydajność dzięki płatnościom zgodnym z rzeczywistym użyciem
- [Skalowanie w pionie](concepts-pricing-tiers.md) w ciągu kilku sekund
- [Monitorowanie i zgłaszanie alertów](concepts-monitoring.md) w celu oceny serwera
- Mechanizmy zabezpieczeń i zgodności klasy korporacyjnej
- [Zabezpieczanie w celu ochrony](concepts-security.md) poufnych danych w czasie spoczynku i ruchowo
- [Automatyczne kopie zapasowe i przywracanie do punktu w czasie](concepts-business-continuity.md) przez maksymalnie 35 dni


Wszystkie powyższe możliwości prawie w ogóle nie wymagają administracji i są udostępniane bez żadnych dodatkowych kosztów. Umożliwiają one skoncentrowanie się na szybkim tworzeniu aplikacji i skróceniu czasu wprowadzenia na rynek, a nie poświęcają zbyt wiele czasu na zarządzanie maszynami wirtualnymi i infrastrukturą. Możesz kontynuować opracowywanie aplikacji przy użyciu wybranych przez siebie narzędzi typu "open source", bez konieczności uczenia się nowych umiejętności.

Opcja wdrożenia pojedynczego serwera oferuje trzy warstwy cenowe: podstawowe, Ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci. Każda warstwa oferuje inne możliwości w zakresie zasobów, aby wspierać obciążenia bazy danych. Możesz utworzyć swoją pierwszą aplikację na podstawie małej bazy danych za jedynie kilka dolarów miesięcznie, a następnie dostosować skalowanie do potrzeb rozwiązania. Dynamiczna skalowalność umożliwia bazie danych przezroczyste odpowiadanie na gwałtownie zmieniające się wymagania dotyczące zasobów. Zapłacisz tylko za potrzebne zasoby i tylko wtedy, gdy będą używane. Aby uzyskać szczegółowe informacje, zobacz  [Warstwy cenowe](concepts-pricing-tiers.md).

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-ze skalowaniem (Citus)
Opcja Citus) w poziomie skaluje zapytania na wielu maszynach przy użyciu fragmentowania. Aparat zapytań parallelizes przychodzące zapytania SQL między tymi serwerami w celu przyspieszenia odpowiedzi w dużych zestawach danych. Obsługuje ona aplikacje, które wymagają większej skali i wydajności, zazwyczaj obciążeń, które zbliżają się do--lub już przekraczają--100 GB danych.

Opcja wdrożenia Citus (preskaling) oferuje następujące funkcje:

- Skalowanie w poziomie na wielu maszynach przy użyciu fragmentowania
- Przetwarzanie równoległe zapytania na tych serwerach w celu uzyskania szybszych odpowiedzi w dużych zestawach danych
- Doskonała pomoc techniczna dla aplikacji wielodostępnych, analizy operacyjnej w czasie rzeczywistym oraz obciążeń transakcyjnych o wysokiej przepływności

Aplikacje skompilowane dla PostgreSQL mogą uruchamiać zapytania rozproszone na potrzeby tworzenia i skalowania (Citus) przy użyciu standardowych [bibliotek połączeń](./concepts-connection-libraries.md) i minimalnych zmian.

## <a name="contacts"></a>Kontakty
Aby dowiedzieć się więcej na temat pytań lub sugestii dotyczących pracy z Azure Database for PostgreSQL, Wyślij wiadomość e-mail do zespołu Azure Database for PostgreSQL ([ @Ask Azure DB dla PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Ten adres jest przeznaczony dla ogólnych pytań zamiast biletów pomocy technicznej.

Ponadto należy wziąć pod uwagę następujące kwestie dotyczące kontaktu:
- Aby skontaktować się z pomocą techniczną platformy Azure lub rozwiązać problem związany z Twoim kontem, należy wysłać [bilet z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Aby przekazać opinię lub poprosić o nowe funkcje, utwórz wpis w platformie [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Następne kroki
- Zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/postgresql/) , aby obporównania kosztów i kalkulatory. Zarówno opcje wdrożenia [pojedynczego serwera,](concept-reserved-pricing.md) [jak i ze](concepts-hyperscale-reserved-pricing.md) skalowaniem (Citus) oferują również płatne rabaty za wystąpienia zarezerwowane
- Zacznij od utworzenia pierwszej Azure Database for PostgreSQL [pojedynczego serwera](./quickstart-create-server-database-portal.md) lub [skalowania (Citus)](./quickstart-create-hyperscale-portal.md)
- Skompiluj pierwszą aplikację w języku Python, PHP, Ruby, C\#, Java, Node.js: [Biblioteki połączeń](./concepts-connection-libraries.md)
