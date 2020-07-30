---
title: Węzły — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Zapoznaj się z dwoma typami węzłów, koordynatorem i pracownikami w grupie serwerów w Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382801"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Węzły w Azure Database for PostgreSQL — funkcja do skalowania (Citus)

Typ hostujący (Citus) Azure Database for PostgreSQL umożliwia współrzędnie serwerów (nazywanych węzłami) w architekturze "udostępnione Nothing". Węzły w grupie serwerów wspólnie przechowują więcej danych i wykorzystują więcej rdzeni procesora CPU niż jest to możliwe na jednym serwerze. Architektura umożliwia również skalowanie bazy danych przez dodanie większej liczby węzłów do grupy serwerów.

## <a name="coordinator-and-workers"></a>Koordynator i procesy robocze

Każda grupa serwerów ma węzeł koordynatora i wielu procesów roboczych. Aplikacje wysyłają swoje zapytania do węzła koordynatora, który przekazuje go do odpowiednich pracowników i gromadzi wyniki. Aplikacje nie mogą łączyć się bezpośrednio z pracownikami.

Funkcja Citus) umożliwia administratorowi bazy danych *dystrybuowanie* tabel, przechowując różne wiersze w różnych węzłach procesu roboczego. Tabele rozproszone są kluczem do skalowania wydajności. Niepowodzenie dystrybucji tabel pozostawia je w całości w węźle koordynatora i nie może korzystać z równoległości między maszynami.

Dla każdego zapytania w tabelach rozproszonych koordynator kieruje go do jednego węzła procesu roboczego lub parallelizes go w kilka w zależności od tego, czy wymagane dane znajdują się na jednym lub wielu węzłach. Koordynator decyduje o tym, co należy zrobić, aby poznać tabele metadanych. Te tabele śledzą nazwy DNS i kondycję węzłów procesu roboczego oraz dystrybucję danych między węzłami.

## <a name="next-steps"></a>Następne kroki
- Informacje o [tabelach rozproszonych](concepts-hyperscale-distributed-data.md)
