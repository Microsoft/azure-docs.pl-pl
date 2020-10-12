---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80272959"
---
| Typ danych | Funkcje dozwolone w wyrażeniach lambda z `any` | Funkcje dozwolone w wyrażeniach lambda z `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Wszystko z wyjątkiem `search.ismatch` i `search.ismatchscoring` | Ta sama |
| `Collection(Edm.String)` | Porównania z `eq` lub `search.in` <br/><br/> Łączenie wyrażeń podrzędnych z `or` | Porównania z `ne` lub `not search.in()` <br/><br/> Łączenie wyrażeń podrzędnych z `and` |
| `Collection(Edm.Boolean)` | Porównania z `eq` lub `ne` | Ta sama |
| `Collection(Edm.GeographyPoint)` | Używanie `geo.distance` z `lt` lub `le` <br/><br/> `geo.intersects` <br/><br/> Łączenie wyrażeń podrzędnych z `or` | Używanie `geo.distance` z `gt` lub `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Łączenie wyrażeń podrzędnych z `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Porównania przy użyciu `eq` , `ne` , `lt` , `gt` , `le` lub `ge` <br/><br/> Łączenie porównań z innymi wyrażeniami podrzędnymi przy użyciu `or` <br/><br/> Łączenie porównania z wyjątkiem `ne` innych wyrażeń podrzędnych przy użyciu `and` <br/><br/> Wyrażenia używające kombinacji `and` i `or` w [DISJUNCTIVE normalnej (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Porównania przy użyciu `eq` , `ne` , `lt` , `gt` , `le` lub `ge` <br/><br/> Łączenie porównań z innymi wyrażeniami podrzędnymi przy użyciu `and` <br/><br/> Łączenie porównania z wyjątkiem `eq` innych wyrażeń podrzędnych przy użyciu `or` <br/><br/> Wyrażenia używające kombinacji `and` i `or` w [CONJUNCTIVE normalnej (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
