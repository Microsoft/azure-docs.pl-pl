---
title: Obsługiwane funkcje platformy FHIR na platformie Azure — Azure API for FHIR
description: W tym artykule wyjaśniono, które funkcje specyfikacji FHIR zaimplementowane w Azure API for FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/15/2021
ms.author: cavoeg
ms.openlocfilehash: 56e3ba46ffb43aec907d729a2e74cdf6f7a62c32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530641"
---
# <a name="features"></a>Funkcje

Azure API for FHIR zapewnia w pełni zarządzane wdrożenie serwera Microsoft FHIR dla platformy Azure. Serwer jest implementacją [standardu FHIR.](https://hl7.org/fhir) Ten dokument zawiera listę głównych funkcji serwera FHIR.

## <a name="fhir-version"></a>Wersja FHIR

Najnowsza obsługiwana wersja: `4.0.1`

Obecnie obsługiwane są również poprzednie wersje: `3.0.2`

## <a name="rest-api"></a>Interfejs API REST

| Interfejs API                            | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — system operacyjny (Cosmos DB) | Komentarz                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| odczyt                           | Yes       | Yes       | Yes       |                                                     |
| vread                          | Yes       | Yes       | Yes       |                                                     |
| update                         | Yes       | Yes       | Yes       |                                                     |
| aktualizowanie za pomocą optymistycznego blokowania | Yes       | Yes       | Yes       |                                                     |
| aktualizacja (warunkowa)           | Yes       | Yes       | Yes       |                                                     |
| Patch                          | Nie        | Nie        | Nie        |                                                     |
| delete                         | Yes       | Yes       | Yes       |  Zobacz uwaga poniżej.                                   |
| usuwanie (warunkowe)           | Nie        | Nie        | Nie        |                                                     |
| historia                        | Yes       | Yes       | Yes       |                                                     |
| create                         | Yes       | Yes       | Yes       | Obsługa zarówno post/PUT                               |
| create (warunkowe)           | Yes       | Yes       | Yes       | Problem [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Częściowe   | Częściowe   | Częściowe   | Zobacz sekcję Wyszukiwanie poniżej.                           |
| wyszukiwanie łańcuchowe                 | Częściowe       | Yes       | Częściowe   | Zobacz uwaga 2 poniżej.                                   |
| wyszukiwanie odwrotne łańcuchowe         | Częściowe       | Yes       | Częściowe   | Zobacz uwaga 2 poniżej.                                   |
| możliwości                   | Yes       | Yes       | Yes       |                                                     |
| partia                          | Yes       | Yes       | Yes       |                                                     |
| Transakcji                    | Nie        | Yes       | Nie        |                                                     |
| Stronicowania                         | Częściowe   | Częściowe   | Częściowe   | `self` i `next` są obsługiwane                     |
| Pośredników                 | Nie        | Nie        | Nie        |                                                     |

> [!Note]
> Usunięcie zdefiniowane przez specyfikację FHIR wymaga, aby po usunięciu kolejnych odczytów zasobu innych niż specyficzne dla wersji zwracał kod stanu HTTP 410 i zasób nie był już wyszukiwany. Ta Azure API for FHIR umożliwia również całkowite usunięcie zasobu (w tym całej historii). Aby całkowicie usunąć zasób, możesz przekazać ustawienia parametrów `hardDelete` do wartości true ( `DELETE {server}/{resource}/{id}?hardDelete=true` ). Jeśli nie przekażemy tego parametru lub ustawisz wartość false, historyczne wersje zasobu będą `hardDelete` nadal dostępne.


 **Uwaga 2**
* Dodano obsługę MVP dla wyszukiwania łańcuchowego i odwrotnie łańcuchowego FHIR w bazie danych CosmosDB. 

  W modelu Azure API for FHIR i serwerze FHIR typu open source, który jest zapasowy przez usługę Cosmos, łańcuchowe wyszukiwanie i wyszukiwanie łańcuchowe to implementacja MVP. Aby wykonać wyszukiwanie łańcuchowe na Cosmos DB, implementacja przechodzi w dół wyrażenia wyszukiwania i wysyła zapytania podrzędne, aby rozwiązać dopasowane zasoby. Odbywa się to dla każdego poziomu wyrażenia. Jeśli jakiekolwiek zapytanie zwróci więcej niż 100 wyników, zostanie zgłoszony błąd. Domyślnie wyszukiwanie łańcuchowe znajduje się za flagą funkcji. Aby użyć wyszukiwania łańcuchowego na Cosmos DB, użyj nagłówka `x-ms-enable-chained-search: true` . Aby uzyskać więcej informacji, zobacz [PR 1695](https://github.com/microsoft/fhir-server/pull/1695).

## <a name="search"></a>Wyszukaj

Obsługiwane są wszystkie typy parametrów wyszukiwania. 

| Typ parametru wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — system operacyjny (Cosmos DB) | Komentarz |
|-----------------------|-----------|-----------|-----------|---------|
| Liczba                | Yes       | Yes       | Yes       |         |
| Date/DateTime         | Yes       | Yes       | Tak       |         |
| Ciąg                | Yes       | Yes       | Yes       |         |
| Token                 | Yes       | Yes       | Yes       |         |
| Odwołanie             | Yes       | Yes       | Yes       |         |
| Złożenie             | Yes       | Yes       | Yes       |         |
| Liczba              | Yes       | Yes       | Yes       |         |
| URI                   | Yes       | Yes       | Yes       |         |
| Specjalne               | Nie        | Nie        | Nie        |         |


| Modyfikatory             | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — system operacyjny (Cosmos DB) | Komentarz |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Yes       | Yes       | Yes       |         |
|`:exact`               | Yes       | Yes       | Yes       |         |
|`:contains`            | Yes       | Yes       | Yes       |         |
|`:text`                | Yes       | Yes       | Yes       |         |
|`:[type]` (odwołanie)  | Yes       | Yes       | Yes       |         |
|`:not`                 | Yes       | Yes       | Yes       |         |
|`:below` (URI)         | Yes       | Yes       | Yes       |         |
|`:above` (URI)         | Nie        | Nie        | Nie        | Problem [#158](https://github.com/Microsoft/fhir-server/issues/158) |
|`:in` (token)          | Nie        | Nie        | Nie        |         |
|`:below` (token)       | Nie        | Nie        | Nie        |         |
|`:above` (token)       | Nie        | Nie        | Nie        |         |
|`:not-in` (token)      | Nie        | Nie        | Nie        |         |

| Parametr wyszukiwania wspólnego | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — system operacyjny (Cosmos DB) | Komentarz |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Yes       | Yes       | Yes       |         |
| `_lastUpdated`          | Yes       | Yes       | Yes       |         |
| `_tag`                  | Yes       | Yes       | Yes       |         |
| `_list`                 | Yes       | Yes       | Yes       |         |
| `_type`                 | Yes       | Yes       | Yes       | Problem [#1562](https://github.com/microsoft/fhir-server/issues/1562)        |
| `_security`             | Yes       | Yes       | Yes       |         |
| `_profile`              | Częściowe   | Częściowe   | Częściowe   | Obsługiwane w przypadku 3. Jeśli baza danych została utworzona **po** 20 lutego 2021 r., obsługa będzie również w 4. Pracujemy nad włączeniem obsługi _profile baz danych utworzonych przed 20 lutego 2021 r. |
| `_text`                 | Nie        | Nie        | Nie        |         |
| `_content`              | Nie        | Nie        | Nie        |         |
| `_has`                  | Nie        | Nie        | Nie        |         |
| `_query`                | Nie        | Nie        | Nie        |         |
| `_filter`               | Nie        | Nie        | Nie        |         |

| Parametry wyników wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — system operacyjny (Cosmos DB) | Komentarz |
|-------------------------|-----------|-----------|-----------|---------|
| `_elements`             | Yes       | Yes       | Yes       | Problem [#1256](https://github.com/microsoft/fhir-server/issues/1256)        |
| `_count`                | Yes       | Yes       | Yes       | `_count` jest ograniczona do 1000 znaków. Jeśli wartość jest większa niż 1000, zostanie zwróconych tylko 1000, a w pakiecie zostanie zwrócone ostrzeżenie. |
| `_include`              | Yes       | Yes       | Yes       |Dołączone elementy są ograniczone do 100. Uwzględnianie w systemach PaaS i OSS na Cosmos DB nie obejmuje obsługi iterować.|
| `_revinclude`           | Yes       | Yes       | Yes       | Dołączone elementy są ograniczone do 100. Uwzględnij w paaS i OSS na Cosmos DB [nie obejmuje obsługi iterować](https://github.com/microsoft/fhir-server/issues/1313). Problem [#1319](https://github.com/microsoft/fhir-server/issues/1319)|
| `_summary`              | Częściowe   | Częściowe   | Częściowe   | `_summary=count` jest obsługiwany |
| `_total`                | Częściowe   | Częściowe   | Częściowe   | `_total=none` i `_total=accurate`      |
| `_sort`                 | Częściowe   | Częściowe   | Częściowe   |   `_sort=_lastUpdated` jest obsługiwany       |
| `_contained`            | Nie        | Nie        | Nie        |         |
| `containedType`         | Nie        | Nie        | Nie        |         |
| `_score`                | Nie        | Nie        | Nie        |         |

## <a name="extended-operations"></a>Operacje rozszerzone

Wszystkie obsługiwane operacje rozszerzające interfejs API RESTful.

| Typ parametru wyszukiwania | Obsługiwane — PaaS | Obsługiwane — OSS (SQL) | Obsługiwane — system operacyjny (Cosmos DB) | Komentarz |
|------------------------|-----------|-----------|-----------|---------|
| $export (cały system) | Yes       | Yes       | Yes       |         |
| Pacjent/$export        | Yes       | Yes       | Yes       |         |
| Grupa/$export          | Yes       | Yes       | Yes       |         |
| $convert danych          | Yes       | Yes       | Yes       |         |


## <a name="persistence"></a>Trwałość

Serwer Microsoft FHIR ma podłączany moduł trwałości (zobacz [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Obecnie kod open-source serwera FHIR zawiera implementację dla Azure Cosmos DB [i](../../cosmos-db/index-overview.md) [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB to globalnie dystrybuowana wielo modelowa baza danych (interfejs API SQL, interfejs API bazy danych MongoDB itp.). Obsługuje różne [poziomy spójności.](../../cosmos-db/consistency-levels.md) Domyślny szablon wdrożenia umożliwia skonfigurowanie spójności serwera FHIR, ale zasady spójności mogą być modyfikowane (zwykle luźne) na podstawie żądań przy użyciu `Strong` `x-ms-consistency-level` nagłówka żądania.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Serwer FHIR używa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) kontroli dostępu. W szczególności wymuszana jest kontrola dostępu oparta na rolach (RBAC), jeśli parametr konfiguracji jest ustawiony na wartość , a wszystkie żądania (z wyjątkiem ) do serwera FHIR muszą mieć nagłówek żądania ustawiony na `FhirServer:Security:Enabled` `true` wartość `/metadata` `Authorization` `Bearer <TOKEN>` . Token musi zawierać co najmniej jedną rolę zgodnie z definicją w `roles` oświadczenie. Żądanie będzie dozwolone, jeśli token zawiera rolę, która umożliwia określoną akcję dla określonego zasobu.

Obecnie dozwolone akcje dla danej roli są stosowane *globalnie w* interfejsie API.

## <a name="service-limits"></a>Limity usługi

* [**Jednostki żądań (JEDNOSTKI ŻĄDAŃ)**](../../cosmos-db/concepts-limits.md) — w portalu można skonfigurować maksymalnie 10 000 jednostek AZURE API FOR FHIR. Będziesz potrzebować co najmniej 400 lub 40 PROCESORÓW/GB, w zależności od tego, która z tych wartości jest większa. Jeśli potrzebujesz więcej niż 10 000 procesorów, możesz umieścić bilet pomocy technicznej, aby to zwiększyć. Maksymalna dostępna liczba to 1 000 000.

* **Połączenia współbieżne** i **wystąpienia** — domyślnie masz pięć połączeń współbieżnych w dwóch wystąpieniach w klastrze (łącznie 10 równoczesnych żądań). Jeśli uważasz, że potrzebujesz większej liczby współbieżnych żądań, otwórz bilet pomocy technicznej ze szczegółami swoich potrzeb.

* **Rozmiar pakietu** — każdy pakiet jest ograniczony do 500 elementów.

* **Rozmiar danych** — rozmiar danych/dokumentów musi być nieco mniejszy niż 2 MB.

## <a name="performance-expectations"></a>Oczekiwania dotyczące wydajności

Wydajność systemu zależy od liczby wystąpień żądań, połączeń współbieżnych i typu wykonywanych operacji (Put, Post itp.). Poniżej przedstawiono niektóre ogólne zakresy tego, czego można oczekiwać na podstawie skonfigurowanych procesorów. Ogólnie rzecz biorąc, wydajność jest skalowana liniowo ze wzrostem liczby procesorów:

| Liczba procesorów | Zasoby/s |    Maksymalny rozmiar magazynu (GB)*    |
|----------|---------------|--------|                 
| 400      | 5-10          |     10   |
| 1000    | 100-150       |      25  |
| 10 000   | 225-400       |      250  |
| 100 000  | 2,500-4,000   |      2500  |

Uwaga: na Cosmos DB wymagana jest minimalna przepływność 40 RU/s na GB magazynu. 

## <a name="next-steps"></a>Następne kroki

W tym artykule poczytaliśmy o obsługiwanych funkcjach FHIR w Azure API for FHIR. Następnie wd wdrażaj Azure API for FHIR.
 
>[!div class="nextstepaction"]
>[Wdrażanie usługi Azure API for FHIR](fhir-paas-portal-quickstart.md)
