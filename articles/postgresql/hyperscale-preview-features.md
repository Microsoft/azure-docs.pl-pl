---
title: Funkcje w wersji zapoznawczej w Azure Database for PostgreSQL-Citus
description: Zaktualizowana lista funkcji obecnie dostępnych w wersji zapoznawczej
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: 5f0552e09464a6b571a665cfe3895f48c3aa8c41
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258456"
---
# <a name="preview-features-for-postgresql---hyperscale-citus"></a>Funkcje w wersji zapoznawczej dla PostgreSQL-i skalowania (Citus)

Azure Database for PostgreSQL-Citus) oferuje wersje zapoznawcze dla niezwolnionych funkcji. Wersje zapoznawcze są udostępniane bez umowy dotyczącej poziomu usług i nie są zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać więcej informacji, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure

## <a name="features-currently-in-preview"></a>Funkcje obecnie dostępne w wersji zapoznawczej

Poniżej przedstawiono funkcje dostępne obecnie w wersji zapoznawczej:

* **[Warstwa podstawowa](concepts-hyperscale-tiers.md)**. Uruchom grupę serwerów, używając tylko węzła koordynatora i bez węzłów procesu roboczego. Ekonomiczny sposób na wstępne testowanie i programowanie oraz obsługę małych obciążeń produkcyjnych.
* **[PostgreSQL 12 i 13](concepts-hyperscale-versions.md)**.
  Użyj najnowszej wersji bazy danych w grupie serwerów.
* **[Citus 10](concepts-hyperscale-versions.md#citus-and-other-extension-versions)**.
  Instalowane automatycznie w grupach serwerów z systemem PostgreSQL 13.
* **[Magazyn kolumnowy](concepts-hyperscale-columnar.md)**.
  Zapisz kolumny wybranych tabel (zamiast wierszy) w sposób ciągły na dysku. Obsługuje kompresję na dysku. Dobre dla obciążeń analitycznych i magazynów danych.
* **[Odczytuj repliki](howto-hyperscale-read-replicas-portal.md)** (tylko w tym samym regionie). Wszelkie zmiany, które nastąpiły do podstawowej grupy serwerów, zostaną odzwierciedlone w swojej replice, a zapytania względem repliki nie powodują dodatkowego obciążenia oryginalnego.
  Repliki są przydatnym narzędziem do poprawiania wydajności dla obciążeń tylko do odczytu.
* **[Zarządzane PgBouncer](concepts-hyperscale-limits.md#managed-pgbouncer-preview)**.
  Pulę połączenia, który umożliwia wielu klientom jednoczesne łączenie się z grupą serwerów, ograniczając jednocześnie liczbę aktywnych połączeń. Jest to zgodne z żądaniami połączeń, zachowując bezproblemowy węzeł koordynatora.
* **[pgAudit](concepts-hyperscale-audit.md)**. Udostępnia szczegółowe informacje o rejestrowaniu sesji i obiektu za pośrednictwem standardowej funkcji rejestrowania PostgreSQL. Tworzy dzienniki inspekcji wymagane do przekazania pewnych audytów urzędu skarbowego, finansowego lub ISO.

### <a name="available-regions-for-preview-features"></a>Dostępne regiony dla funkcji wersji zapoznawczej

Rozszerzenie pgAudit jest dostępne we wszystkich [regionach obsługiwanych przez funkcja (Citus)](concepts-hyperscale-configuration-options.md#regions).
Inne funkcje w wersji zapoznawczej są dostępne tylko w **regionie Wschodnie stany USA** .

## <a name="does-my-server-group-have-access-to-preview-features"></a>Czy moja grupa serwerów ma dostęp do funkcji w wersji zapoznawczej?

Aby określić, czy Grupa serwerów Citus jest włączona w wersji zapoznawczej, przejdź do strony **Przegląd** grupy serwerów w Azure Portal.
Jeśli zostanie wyświetlona **warstwa właściwości: podstawowa (wersja zapoznawcza)** lub **warstwa: standardowa (wersja zapoznawcza)** , Grupa serwerów ma dostęp do funkcji w wersji zapoznawczej.

### <a name="how-to-get-access"></a>Jak uzyskać dostęp

Podczas tworzenia nowej grupy serwerów Citus (nowy) zaznacz pole wyboru **Włącz funkcje w wersji zapoznawczej.**

## <a name="contact-us"></a>Skontaktuj się z nami

Poinformuj nas o Twoim środowisku, korzystając z funkcji wersji zapoznawczej, wysyłając wiadomość e-mail z [pytaniami do usługi Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com).
(Ten adres e-mail nie jest kanałem pomocy technicznej. W przypadku problemów technicznych Otwórz [żądanie pomocy technicznej](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
