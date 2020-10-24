---
title: Wygaśnięcie danych w Azure Cosmos DB z czasem wygaśnięcia
description: Dzięki funkcji TTL Microsoft Azure Cosmos DB zapewnia możliwość automatycznego przeczyszczania dokumentów z systemu po upływie czasu.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/02/2020
ms.reviewer: sngun
ms.openlocfilehash: 2700b18797db3805a081b549605369e73889867b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476981"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Czas wygaśnięcia (TTL) w usłudze Azure Cosmos DB

W przypadku **czasu** wygaśnięcia lub czasu wygaśnięcia usługa Azure Cosmos DB zapewnia możliwość automatycznego usuwania elementów z kontenera po upływie określonego czasu. Domyślnie można ustawić czas na żywo na poziomie kontenera i zastąpić wartość dla każdego elementu. Po ustawieniu czasu wygaśnięcia w kontenerze lub na poziomie elementu Azure Cosmos DB automatycznie usunie te elementy po upływie czasu, od momentu ostatniej modyfikacji. Wartość czasu wygaśnięcia jest konfigurowana w sekundach. W przypadku skonfigurowania czasu wygaśnięcia system automatycznie usunie przeterminowane elementy na podstawie wartości czasu wygaśnięcia, bez konieczności operacji usuwania jawnie wydanej przez aplikację kliencką. Maksymalna wartość parametru TTL to 2147483647.

Usuwanie wygasłych elementów to zadanie w tle, które wykorzystuje [jednostki](request-units.md)żądań od lewej do końca, które są jednostkami żądania, które nie zostały zużyte przez żądania użytkowników. Nawet po wygaśnięciu czasu wygaśnięcia, jeśli kontener jest przeciążony przy użyciu żądań i jeśli nie ma wystarczającej ilości RU, usuwanie danych jest opóźnione. Dane są usuwane, gdy jest wystarczająco dużo jednostek ru dostępne do wykonania operacji usuwania. Mimo że usuwanie danych jest opóźnione, dane nie są zwracane przez żadne zapytania (przez dowolny interfejs API) po wygaśnięciu czasu wygaśnięcia.

> Ta zawartość jest powiązana z Azure Cosmos DBą czasu wygaśnięcia magazynu transakcyjnego. Jeśli szukasz wartości TTL magazynu analitycal, która umożliwia NoETLe scenariusze HTAP za pomocą [linku Synapse platformy Azure](./synapse-link.md), kliknij [tutaj](./analytical-store-introduction.md#analytical-ttl).

## <a name="time-to-live-for-containers-and-items"></a>Czas wygaśnięcia kontenerów i elementów

Wartość czasu wygaśnięcia jest ustawiana w sekundach i interpretowana jako Delta od momentu ostatniej modyfikacji elementu. Można ustawić czas wygaśnięcia dla kontenera lub elementu w kontenerze:

1. **Czas wygaśnięcia w kontenerze** (ustawienie using `DefaultTimeToLive` ):

   - Jeśli nie ma (lub ma wartość null), elementy nie są automatycznie wygasłe.

   - Jeśli jest obecny, a wartość jest równa "-1", jest równe nieskończoność, a elementy domyślnie nie wygasają.

   - Jeśli jest obecny, a wartość jest równa liczbie *"n"* — elementy wygaśnie *"n"* sek. po ich ostatniej modyfikacji.

2. **Czas wygaśnięcia elementu** (zestaw przy użyciu `ttl` ):

   - Ta właściwość ma zastosowanie tylko wtedy `DefaultTimeToLive` , gdy jest obecny i nie jest ustawiona na wartość null dla kontenera nadrzędnego.

   - Jeśli jest obecny, zastępuje `DefaultTimeToLive` wartość kontenera nadrzędnego.

## <a name="time-to-live-configurations"></a>Czas do konfiguracji na żywo

* Jeśli wartość czasu wygaśnięcia jest ustawiona na *"n"* w kontenerze, elementy w tym kontenerze wygasną po upływie *n* sekund.  Jeśli istnieją elementy w tym samym kontenerze, które mają własny czas na żywo, ustaw wartość-1 (wskazującą, że nie wygasną) lub jeśli niektóre elementy przesłonili ustawienie czasu wygaśnięcia o innej liczbie, te elementy wygasną na podstawie ich wartości czasu wygaśnięcia. 

* Jeśli wartość czasu wygaśnięcia nie jest ustawiona w kontenerze, czas wygaśnięcia dla elementu w tym kontenerze nie ma żadnego wpływu. 

* Jeśli wartość czasu wygaśnięcia dla kontenera wynosi-1, element w tym kontenerze, który ma wartość czasu wygaśnięcia ustawioną na n, utraci ważność po n sekundach, a pozostałe elementy nie wygasną.

## <a name="examples"></a>Przykłady

W tej sekcji przedstawiono kilka przykładów zawierających różne wartości czasu na żywo przypisane do kontenera i elementów:

### <a name="example-1"></a>Przykład 1

Wartość parametru TTL w kontenerze ma wartość null (DefaultTimeToLive = null)

|Czas wygaśnięcia dla elementu| Wynik|
|---|---|
|TTL = null|    Czas wygaśnięcia jest wyłączony. Element nigdy nie wygaśnie (domyślnie).|
|czas wygaśnięcia =-1   |Czas wygaśnięcia jest wyłączony. Element nigdy nie wygaśnie.|
|czas wygaśnięcia = 2000 |Czas wygaśnięcia jest wyłączony. Element nigdy nie wygaśnie.|


### <a name="example-2"></a>Przykład 2

Wartość parametru TTL w kontenerze jest ustawiona na-1 (DefaultTimeToLive =-1)

|Czas wygaśnięcia dla elementu| Wynik|
|---|---|
|TTL = null |Czas wygaśnięcia jest włączony. Element nigdy nie wygaśnie (domyślnie).|
|czas wygaśnięcia =-1   |Czas wygaśnięcia jest włączony. Element nigdy nie wygaśnie.|
|czas wygaśnięcia = 2000 |Czas wygaśnięcia jest włączony. Element wygaśnie po upływie 2000 sekund.|


### <a name="example-3"></a>Przykład 3

Wartość parametru TTL w kontenerze jest ustawiona na 1000 (DefaultTimeToLive = 1000)

|Czas wygaśnięcia dla elementu| Wynik|
|---|---|
|TTL = null|    Czas wygaśnięcia jest włączony. Element wygaśnie po 1000 sekundach (wartość domyślna).|
|czas wygaśnięcia =-1   |Czas wygaśnięcia jest włączony. Element nigdy nie wygaśnie.|
|czas wygaśnięcia = 2000 |Czas wygaśnięcia jest włączony. Element wygaśnie po upływie 2000 sekund.|

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak skonfigurować czas wygaśnięcia w następujących artykułach:

* [Jak skonfigurować czas wygaśnięcia](how-to-time-to-live.md)