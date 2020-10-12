---
title: Skalowanie przepływności w Azure Cosmos DB
description: W tym artykule opisano, jak Azure Cosmos DB skaluje przepływność w różnych regionach, w których zainicjowano konto usługi Azure Cosmos.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 94b5b3d2ab1f576f87ead23b389252ec96a20f11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397355"
---
# <a name="how-does-azure-cosmos-db-globally-scale-the-provisioned-throughput"></a>Jak Azure Cosmos DB globalnie skalować zainicjowaną przepływność?

W Azure Cosmos DB, obsługiwana przepływność jest reprezentowana jako jednostki żądań na sekundę (RU/s lub w formacie plural jednostek ru). Jednostek ru Zmierz koszt operacji odczytu i zapisu w kontenerze Cosmos, jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png" alt-text="Jednostki żądania" border="false":::

Można udostępnić jednostek RU w kontenerze Cosmos lub bazie danych Cosmos. Jednostek ru udostępniane w kontenerze są dostępne wyłącznie dla operacji wykonywanych w tym kontenerze. Jednostek ru udostępniane w bazie danych są współdzielone przez wszystkie kontenery w tej bazie danych (z wyjątkiem kontenerów z wyłącznie przypisanymi jednostek ru).

W celu elastycznego skalowania przepływności aprowizacji można w dowolnym momencie zwiększyć lub zmniejszyć liczbę zainicjowanych jednostek RU/s. Aby uzyskać więcej informacji, zobacz artykuł [jak zapewnić przepływność](set-throughput.md) i elastyczne skalowanie kontenerów Cosmos oraz baz danych. Aby globalnie skalować przepływność, możesz w dowolnym momencie dodać lub usunąć regiony z konta usługi Cosmos. Aby uzyskać więcej informacji, zobacz [Dodawanie/usuwanie regionów z konta bazy danych](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Kojarzenie wielu regionów z kontem Cosmos jest ważne w wielu scenariuszach — w celu osiągnięcia małych opóźnień i [wysokiej dostępności](high-availability.md) na całym świecie.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Sposób dystrybuowania przepływności w różnych regionach

Jeśli zainicjujesz jednostek ru *"r"* w kontenerze Cosmos (lub bazie danych), Cosmos DB zapewnia dostępność *"r"* jednostek RU w *każdym* regionie skojarzonym z Twoim kontem Cosmos. Za każdym razem, gdy dodasz nowy region do konta, Cosmos DB automatycznie inicjuje jednostek ru *"R"* w nowo dodanym regionie. Operacje wykonywane względem kontenera Cosmos są gwarantowane w celu uzyskania jednostek ru *"R"* w każdym regionie. Nie można wybiórczo przypisywać jednostek ru do określonego regionu. Obsługa jednostek RU w kontenerze Cosmos (lub bazie danych) jest obsługiwana we wszystkich regionach skojarzonych z Twoim kontem Cosmos.

Przy założeniu, że kontener Cosmos jest skonfigurowany przy użyciu *elementu "R"* jednostek ru, a istnieją regiony *"N"* skojarzone z kontem Cosmos, wówczas:

- W przypadku skonfigurowania konta Cosmos z jednym regionem zapisu całkowita jednostek ru dostępna globalnie w kontenerze = *R* x *N*.

- Jeśli konto Cosmos jest skonfigurowane z wieloma regionami zapisu, łączna jednostek ru dostępna globalnie w kontenerze = *R* x (*N*+ 1). Dodatkowe jednostek ru *języka R* są automatycznie inicjowane w celu przetworzenia konfliktów aktualizacji i ruchu antyentropii w regionach.

Wybór [modelu spójności](consistency-levels.md) wpływa również na przepływność. Możesz uzyskać około 2x przepływność odczytu dla bardziej swobodnych poziomów spójności (np. *sesji*, *spójnego prefiksu* i spójności *ostatecznej* ) w porównaniu z silniejszymi poziomami spójności (np. *z ograniczeniami nieodświeżonymi* lub *silną* spójnością).

## <a name="next-steps"></a>Następne kroki

Następnie można dowiedzieć się, jak skonfigurować przepływność na kontenerze lub w bazie danych:

* [Pobieranie i Ustawianie przepływności dla kontenerów i baz danych](set-throughput.md) 

