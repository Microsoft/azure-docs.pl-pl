---
title: Obsługiwane funkcje FHIR na platformie Azure — interfejs API platformy Azure dla FHIR
description: W tym artykule wyjaśniono, które funkcje specyfikacji FHIR wdrożone w interfejsie API platformy Azure dla FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 71097f13fffbbe5cb57a69c98fb0ab272e16af5c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026305"
---
# <a name="features"></a>Funkcje

Interfejs API platformy Azure dla usługi FHIR zapewnia w pełni zarządzane wdrożenie serwera programu Microsoft FHIR dla platformy Azure. Serwer jest implementacją standardu [FHIR](https://hl7.org/fhir) . Ten dokument zawiera listę głównych funkcji serwera FHIR.

## <a name="fhir-version"></a>Wersja FHIR

Najnowsza obsługiwana wersja: `4.0.1`

Obecnie obsługiwane są również poprzednie wersje: `3.0.2`

## <a name="rest-api"></a>Interfejs API REST

| Interfejs API                            | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| przeczytaj                           | Tak       | Tak       | Tak       |                                                     |
| vread                          | Tak       | Tak       | Tak       |                                                     |
| update                         | Tak       | Tak       | Tak       |                                                     |
| Aktualizuj przy użyciu optymistycznego blokowania | Tak       | Tak       | Tak       |                                                     |
| Aktualizuj (warunkowo)           | Tak       | Tak       | Tak       |                                                     |
| wysłana                          | Nie        | Nie        | Nie        |                                                     |
| delete                         | Tak       | Tak       | Tak       |                                                     |
| Usuń (warunkowe)           | Nie        | Nie        | Nie        |                                                     |
| historia                        | Tak       | Tak       | Tak       |                                                     |
| create                         | Tak       | Tak       | Tak       | Obsługa funkcji POST/PUT                               |
| Utwórz (warunkowo)           | Tak       | Tak       | Tak       | [#1382](https://github.com/microsoft/fhir-server/issues/1382) problemu |
| search                         | Częściowe   | Częściowe   | Częściowe   | Zobacz poniżej                                           |
| Wyszukiwanie łańcuchowe                 | Nie        | Tak       | Nie        |                                           |
| Wyszukiwanie w łańcuchu wstecznym         | Nie        | Nie        | Nie        |                                            |
| możliwości                   | Tak       | Tak       | Tak       |                                                     |
| partia                          | Tak       | Tak       | Tak       |                                                     |
| Transaction                    | Nie        | Tak       | Nie        |                                                     |
| stronicowania                         | Częściowe   | Częściowe   | Częściowe   | `self` i `next` są obsługiwane                     |
| pośredników                 | Nie        | Nie        | Nie        |                                                     |

## <a name="search"></a>Wyszukiwanie

Wszystkie typy parametrów wyszukiwania są obsługiwane. 

| Typ parametru wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-----------------------|-----------|-----------|-----------|---------|
| Liczba                | Tak       | Tak       | Tak       |         |
| Data/godzina         | Tak       | Tak       | Tak       |         |
| Ciąg                | Tak       | Tak       | Tak       |         |
| Token                 | Tak       | Tak       | Tak       |         |
| Dokumentacja             | Tak       | Tak       | Tak       |         |
| Złożenie             | Tak       | Tak       | Tak       |         |
| Ilość              | Tak       | Tak       | Tak       |         |
| URI                   | Tak       | Tak       | Tak       |         |
| Jako               | Nie        | Nie        | Nie        |         |


| Modyfikatory             | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Tak       | Tak       | Tak       |         |
|`:exact`               | Tak       | Tak       | Tak       |         |
|`:contains`            | Tak       | Tak       | Tak       |         |
|`:text`                | Tak       | Tak       | Tak       |         |
|`:in` klucza          | Nie        | Nie        | Nie        |         |
|`:below` klucza       | Nie        | Nie        | Nie        |         |
|`:above` klucza       | Nie        | Nie        | Nie        |         |
|`:not-in` klucza      | Nie        | Nie        | Nie        |         |
|`:[type]` odwoła  | Nie        | Nie        | Nie        |         |
|`:below` adresu         | Tak       | Tak       | Tak       |         |
|`:not`                 | Nie        | Nie        | Nie        |         |
|`:above` adresu         | Nie        | Nie        | Nie        | [#158](https://github.com/Microsoft/fhir-server/issues/158) problemu |

| Typowy parametr wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Tak       | Tak       | Tak       |         |
| `_lastUpdated`          | Tak       | Tak       | Tak       |         |
| `_tag`                  | Tak       | Tak       | Tak       |         |
| `_profile`              | Tak       | Tak       | Tak       |         |
| `_security`             | Tak       | Tak       | Tak       |         |
| `_text`                 | Nie        | Nie        | Nie        |         |
| `_content`              | Nie        | Nie        | Nie        |         |
| `_list`                 | Tak       | Tak       | Tak       |         |
| `_has`                  | Nie        | Nie        | Nie        |         |
| `_type`                 | Tak       | Tak       | Tak       |         |
| `_query`                | Nie        | Nie        | Nie        |         |
| `_filter`               | Nie        | Nie        | Nie        |         |

| Parametry wyników wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | Częściowe        | Częściowe   | Częściowe        |   `_sort=_lastUpdated` jest obsługiwana       |
| `_count`                | Tak       | Tak       | Tak       | `_count` jest ograniczone do 100 znaków. Jeśli ustawiona na wartość większą niż 100, zostaną zwrócone tylko 100, a w pakiecie zostanie zwrócone ostrzeżenie. |
| `_include`              | Tak       | Tak       | Tak       |Uwzględnione elementy są ograniczone do 100. Dołączenie do PaaS i OSS na Cosmos DB nie obejmuje: ITERING support.|
| `_revinclude`           | Tak       | Tak       | Tak       | Uwzględnione elementy są ograniczone do 100. Dołączenie do PaaS i OSS na Cosmos DB nie obejmuje: ITERING support.|
| `_summary`              | Częściowe   | Częściowe   | Częściowe   | `_summary=count` jest obsługiwana |
| `_total`                | Częściowe   | Częściowe   | Częściowe   | _total = non i _total = dokładne      |
| `_elements`             | Tak       | Tak       | Tak       |         |
| `_contained`            | Nie        | Nie        | Nie        |         |
| `containedType`         | Nie        | Nie        | Nie        |         |
| `_score`                | Nie        | Nie        | Nie        |         |

## <a name="extended-operations"></a>Operacje rozszerzone

Wszystkie obsługiwane operacje, które zwiększają interfejs API RESTful.

| Typ parametru wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|------------------------|-----------|-----------|-----------|---------|
| $export (cały system) | Tak       | Tak       | Tak       |         |
| Pacjent/$export        | Tak       | Tak       | Tak       |         |
| Grupuj/$export          | Tak       | Tak       | Tak       |         |

## <a name="persistence"></a>Trwałość

Serwer programu Microsoft FHIR ma podłączany moduł trwałości (zobacz [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Obecnie kod "open-source" serwera FHIR zawiera implementację [Azure Cosmos DB](../cosmos-db/index-overview.md) i [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB to baza danych oparta na globalnie rozproszonym modelu (SQL API, MongoDB API itp.). Obsługuje różne [poziomy spójności](../cosmos-db/consistency-levels.md). Domyślny szablon wdrożenia konfiguruje serwer FHIR ze `Strong` spójnością, ale zasady spójności mogą być modyfikowane (zwykle swobodne) na żądanie przez podstawę żądania przy użyciu `x-ms-consistency-level` nagłówka żądania.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Serwer FHIR używa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) na potrzeby kontroli dostępu. W konkretnym przypadku jest wymuszane Role-Based Access Control (RBAC), jeśli `FhirServer:Security:Enabled` parametr konfiguracji jest ustawiony na `true` , a wszystkie żądania (z wyjątkiem `/metadata` ) do serwera FHIR muszą mieć `Authorization` nagłówek żądania ustawiony na `Bearer <TOKEN>` . Token musi zawierać co najmniej jedną rolę zdefiniowaną w ramach tego `roles` żądania. Żądanie będzie dozwolone, jeśli token zawiera rolę, która zezwala na określoną akcję dla określonego zasobu.

Obecnie dozwolone akcje dla danej roli są stosowane *globalnie* w interfejsie API.

## <a name="service-limits"></a>Limity usługi

* [**Jednostki żądań (jednostek ru)**](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) — możesz skonfigurować do 10 000 jednostek RU w portalu dla interfejsu API platformy Azure dla FHIR. Wymagana jest co najmniej 400 jednostek ru lub 10 jednostek ru/GB, w zależności od tego, co będzie większe. Jeśli potrzebujesz więcej niż 10 000 jednostek ru, możesz umieścić bilet pomocy technicznej w celu zwiększenia tego problemu. Maksymalna dostępna wartość to 1 000 000.

* **Współbieżne połączenia** i **wystąpienia** — dzięki dafault masz pięć współbieżnych połączeń na dwóch wystąpieniach w klastrze (w sumie 10 współbieżnych żądań). Jeśli uważasz, że potrzebujesz więcej współbieżnych żądań, Otwórz bilet pomocy technicznej ze szczegółowymi informacjami dotyczącymi Twoich potrzeb.

* **Rozmiar pakietu** — każdy pakiet jest ograniczony do 500 elementów.

* **Rozmiar danych** — dane/dokumenty muszą być nieco mniejsze niż 2 MB.

## <a name="performance-expectations"></a>Oczekiwania dotyczące wydajności

Wydajność systemu zależy od liczby jednostek ru, połączeń współbieżnych i typu wykonywanych operacji (Put, post itp.). Poniżej znajdują się niektóre ogólne zakresy, których można oczekiwać na podstawie skonfigurowanych jednostek ru. Ogólnie rzecz biorąc, wydajność skaluje się liniowo ze wzrostem jednostek ru:

| Liczba jednostek ru | Zasoby/s |
|----------|---------------|
| 400      | 5-10          |
| 1000    | 100-150       |
| 10 000   | 225-400       |
| 100 000  | 2500 – 4000   |

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o obsługiwanych funkcjach FHIR w interfejsie API platformy Azure dla FHIR. Następnie wdróż interfejs API platformy Azure dla usługi FHIR.
 
>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-portal-quickstart.md)
