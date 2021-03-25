---
title: Obsługiwane funkcje FHIR na platformie Azure — interfejs API platformy Azure dla FHIR
description: W tym artykule wyjaśniono, które funkcje specyfikacji FHIR wdrożone w interfejsie API platformy Azure dla FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/30/2021
ms.author: cavoeg
ms.openlocfilehash: 9bd61d65d6d64dac6081d3491deb8a15efc4a45b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048423"
---
# <a name="features"></a>Funkcje

Interfejs API platformy Azure dla usługi FHIR zapewnia w pełni zarządzane wdrożenie serwera programu Microsoft FHIR dla platformy Azure. Serwer jest implementacją standardu [FHIR](https://hl7.org/fhir) . Ten dokument zawiera listę głównych funkcji serwera FHIR.

## <a name="fhir-version"></a>Wersja FHIR

Najnowsza obsługiwana wersja: `4.0.1`

Obecnie obsługiwane są również poprzednie wersje: `3.0.2`

## <a name="rest-api"></a>Interfejs API REST

| Interfejs API                            | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| odczyt                           | Tak       | Tak       | Tak       |                                                     |
| vread                          | Tak       | Tak       | Tak       |                                                     |
| update                         | Tak       | Tak       | Tak       |                                                     |
| Aktualizuj przy użyciu optymistycznego blokowania | Tak       | Tak       | Tak       |                                                     |
| Aktualizuj (warunkowo)           | Tak       | Tak       | Tak       |                                                     |
| wysłana                          | Nie        | Nie        | Nie        |                                                     |
| delete                         | Tak       | Tak       | Tak       |  Zobacz uwagę poniżej.                                   |
| Usuń (warunkowe)           | Nie        | Nie        | Nie        |                                                     |
| historia                        | Tak       | Tak       | Tak       |                                                     |
| create                         | Tak       | Tak       | Tak       | Obsługa funkcji POST/PUT                               |
| Utwórz (warunkowo)           | Tak       | Tak       | Tak       | [#1382](https://github.com/microsoft/fhir-server/issues/1382) problemu |
| search                         | Częściowe   | Częściowe   | Częściowe   | Zobacz sekcję Wyszukaj poniżej.                           |
| Wyszukiwanie łańcuchowe                 | Tak       | Tak       | Częściowe   | Patrz Uwaga 2 poniżej.                                   |
| Wyszukiwanie w łańcuchu wstecznym         | Tak       | Tak       | Częściowe   | Patrz Uwaga 2 poniżej.                                   |
| możliwości                   | Tak       | Tak       | Tak       |                                                     |
| partia                          | Tak       | Tak       | Tak       |                                                     |
| Transaction                    | Nie        | Tak       | Nie        |                                                     |
| stronicowania                         | Częściowe   | Częściowe   | Częściowe   | `self` i `next` są obsługiwane                     |
| pośredników                 | Nie        | Nie        | Nie        |                                                     |

> [!Note]
> Usunięcie zdefiniowane przez specyfikację FHIR wymaga, aby po usunięciu kolejne odczyty zasobów, które nie są specyficzne dla wersji, zwracały kod stanu HTTP 410, a zasób nie został już odnaleziony przez wyszukiwanie. Interfejs API platformy Azure dla usługi FHIR umożliwia również całkowite usunięcie (łącznie z całą historią) zasobu. Aby całkowicie usunąć zasób, można przekazać ustawienia parametru `hardDelete` do wartości true ( `DELETE {server}/{resource}/{id}?hardDelete=true` ). Jeśli nie przekażesz tego parametru lub nie ustawisz `hardDelete` wartości false, nadal będą dostępne wersje historyczne zasobu.


 **Uwaga 2**
* Dodaje obsługę programu MVP dla łańcuchowych i odwracających łańcucha FHIR w CosmosDB. 

  W interfejsie API platformy Azure dla usługi FHIR i serwera FHIR Open Source obsługiwanego przez Cosmos wyszukiwanie łańcuchowe wyszukiwania i łańcucha wstecznego jest implementacją MVP. Aby wykonać wyszukiwanie łańcuchowe na Cosmos DB, implementacja sprawdza wyrażenie wyszukiwania i wystawia zapytania podrzędne, aby rozwiązać dopasowane zasoby. Jest to wykonywane dla każdego poziomu wyrażenia. Jeśli dowolne zapytanie zwróci więcej niż 100 wyników, zostanie zgłoszony błąd. Domyślnie wyszukiwanie łańcuchowe jest za flagą funkcji. Aby użyć wyszukiwania łańcuchowego na Cosmos DB, użyj nagłówka `x-ms-enable-chained-search: true` . Aby uzyskać więcej informacji, zobacz [PR 1695](https://github.com/microsoft/fhir-server/pull/1695).

## <a name="search"></a>Wyszukaj

Wszystkie typy parametrów wyszukiwania są obsługiwane. 

| Typ parametru wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-----------------------|-----------|-----------|-----------|---------|
| Liczba                | Tak       | Tak       | Tak       |         |
| Data/godzina         | Tak       | Tak       | Tak       |         |
| Ciąg                | Tak       | Tak       | Tak       |         |
| Token                 | Tak       | Tak       | Tak       |         |
| Odwołanie             | Tak       | Tak       | Tak       |         |
| Złożenie             | Tak       | Tak       | Tak       |         |
| Liczba              | Tak       | Tak       | Tak       |         |
| URI                   | Tak       | Tak       | Tak       |         |
| Jako               | Nie        | Nie        | Nie        |         |


| Modyfikatory             | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Tak       | Tak       | Tak       |         |
|`:exact`               | Tak       | Tak       | Tak       |         |
|`:contains`            | Tak       | Tak       | Tak       |         |
|`:text`                | Tak       | Tak       | Tak       |         |
|`:[type]` odwoła  | Tak       | Tak       | Tak       |         |
|`:not`                 | Tak       | Tak       | Tak       |         |
|`:below` adresu         | Tak       | Tak       | Tak       |         |
|`:above` adresu         | Nie        | Nie        | Nie        | [#158](https://github.com/Microsoft/fhir-server/issues/158) problemu |
|`:in` klucza          | Nie        | Nie        | Nie        |         |
|`:below` klucza       | Nie        | Nie        | Nie        |         |
|`:above` klucza       | Nie        | Nie        | Nie        |         |
|`:not-in` klucza      | Nie        | Nie        | Nie        |         |

| Typowy parametr wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Tak       | Tak       | Tak       |         |
| `_lastUpdated`          | Tak       | Tak       | Tak       |         |
| `_tag`                  | Tak       | Tak       | Tak       |         |
| `_list`                 | Tak       | Tak       | Tak       |         |
| `_type`                 | Tak       | Tak       | Tak       | [#1562](https://github.com/microsoft/fhir-server/issues/1562) problemu        |
| `_security`             | Tak       | Tak       | Tak       |         |
| `_profile`              | Częściowe   | Częściowe   | Częściowe   | Obsługiwane w STU3. Jeśli baza danych została utworzona **po** 20 lutego 2021, będziesz mieć również pomoc techniczną. Pracujemy nad włączeniem _profile baz danych utworzonych przed 20 lutego 2021. |
| `_text`                 | Nie        | Nie        | Nie        |         |
| `_content`              | Nie        | Nie        | Nie        |         |
| `_has`                  | Nie        | Nie        | Nie        |         |
| `_query`                | Nie        | Nie        | Nie        |         |
| `_filter`               | Nie        | Nie        | Nie        |         |

| Parametry wyników wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — OSS (Cosmos DB) | Komentarz |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Tak       | Tak       | Tak       | [#1256](https://github.com/microsoft/fhir-server/issues/1256) problemu        |
| `_count`                | Tak       | Tak       | Tak       | `_count` jest ograniczone do 1000 znaków. Jeśli ustawiona na wartość większą niż 1000, zostaną zwrócone tylko 1000, a w pakiecie zostanie zwrócone ostrzeżenie. |
| `_include`              | Tak       | Tak       | Tak       |Uwzględnione elementy są ograniczone do 100. Dołączenie do PaaS i OSS na Cosmos DB nie obejmuje: ITERING support.|
| `_revinclude`           | Tak       | Tak       | Tak       | Uwzględnione elementy są ograniczone do 100. Dołączenie do PaaS i OSS na Cosmos DB nie [obejmuje: ITERING support](https://github.com/microsoft/fhir-server/issues/1313). [#1319](https://github.com/microsoft/fhir-server/issues/1319) problemu|
| `_summary`              | Częściowe   | Częściowe   | Częściowe   | `_summary=count` jest obsługiwana |
| `_total`                | Częściowe   | Częściowe   | Częściowe   | `_total=none` i `_total=accurate`      |
| `_sort`                 | Częściowe   | Częściowe   | Częściowe   |   `_sort=_lastUpdated` jest obsługiwana       |
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
| $convert — dane          | Tak       | Tak       | Tak       |         |


## <a name="persistence"></a>Trwałość

Serwer programu Microsoft FHIR ma podłączany moduł trwałości (zobacz [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Obecnie kod "open-source" serwera FHIR zawiera implementację [Azure Cosmos DB](../../cosmos-db/index-overview.md) i [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB to baza danych oparta na globalnie rozproszonym modelu (SQL API, MongoDB API itp.). Obsługuje różne [poziomy spójności](../../cosmos-db/consistency-levels.md). Domyślny szablon wdrożenia konfiguruje serwer FHIR ze `Strong` spójnością, ale zasady spójności mogą być modyfikowane (zwykle swobodne) na żądanie przez podstawę żądania przy użyciu `x-ms-consistency-level` nagłówka żądania.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Serwer FHIR używa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) na potrzeby kontroli dostępu. W konkretnym przypadku kontrola dostępu oparta na rolach (RBAC) jest wymuszana, jeśli `FhirServer:Security:Enabled` parametr konfiguracji jest ustawiony na `true` , a wszystkie żądania (z wyjątkiem `/metadata` ) do serwera FHIR muszą mieć `Authorization` nagłówek żądania ustawiony na `Bearer <TOKEN>` . Token musi zawierać co najmniej jedną rolę zdefiniowaną w ramach tego `roles` żądania. Żądanie będzie dozwolone, jeśli token zawiera rolę, która zezwala na określoną akcję dla określonego zasobu.

Obecnie dozwolone akcje dla danej roli są stosowane *globalnie* w interfejsie API.

## <a name="service-limits"></a>Limity usługi

* [**Jednostki żądań (jednostek ru)**](../../cosmos-db/concepts-limits.md) — możesz skonfigurować do 10 000 jednostek RU w portalu dla interfejsu API platformy Azure dla FHIR. Wymagana jest co najmniej 400 jednostek ru lub 10 jednostek ru/GB, w zależności od tego, co będzie większe. Jeśli potrzebujesz więcej niż 10 000 jednostek ru, możesz umieścić bilet pomocy technicznej w celu zwiększenia tego problemu. Maksymalna dostępna wartość to 1 000 000.

* **Współbieżne połączenia** i **wystąpienia** — domyślnie masz pięć współbieżnych połączeń w dwóch wystąpieniach w klastrze (w sumie 10 współbieżnych żądań). Jeśli uważasz, że potrzebujesz więcej współbieżnych żądań, Otwórz bilet pomocy technicznej ze szczegółowymi informacjami dotyczącymi Twoich potrzeb.

* **Rozmiar pakietu** — każdy pakiet jest ograniczony do 500 elementów.

* **Rozmiar danych** — dane/dokumenty muszą być nieco mniejsze niż 2 MB.

## <a name="performance-expectations"></a>Oczekiwania dotyczące wydajności

Wydajność systemu zależy od liczby jednostek ru, połączeń współbieżnych i typu wykonywanych operacji (Put, post itp.). Poniżej znajdują się niektóre ogólne zakresy, których można oczekiwać na podstawie skonfigurowanych jednostek ru. Ogólnie rzecz biorąc, wydajność skaluje się liniowo ze wzrostem jednostek ru:

| Liczba jednostek ru | Zasoby/s |    Maksymalna ilość miejsca w magazynie (GB) *    |
|----------|---------------|--------|                 
| 400      | 5-10          |     40   |
| 1000    | 100-150       |      100  |
| 10 000   | 225-400       |      1000  |
| 100 000  | 2500 – 4000   |      10 000  |

Uwaga: zgodnie z wymaganiami Cosmos DB wymagana jest minimalna przepływność 10 jednostek RU/s na GB magazynu. Aby uzyskać więcej informacji, zapoznaj się z [przydziałami usługi Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o obsługiwanych funkcjach FHIR w interfejsie API platformy Azure dla FHIR. Następnie wdróż interfejs API platformy Azure dla usługi FHIR.
 
>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-portal-quickstart.md)
