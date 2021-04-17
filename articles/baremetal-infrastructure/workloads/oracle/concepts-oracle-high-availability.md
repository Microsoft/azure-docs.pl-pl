---
title: Wysoka dostępność i odzyskiwanie po awarii dla programu Oracle na platformie BareMetal
description: Dowiedz się więcej o wysokiej dostępności i odzyskiwaniu po awarii dla Oracle na platformie Azure BareMetal.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: ab0337622eaa8c1368760fcbcd28533dacb3adce
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590307"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>Wysoka dostępność i odzyskiwanie po awarii dla programu Oracle na platformie BareMetal

W tym artykule przyjrzymy się podstawom wysokiej dostępności i odzyskiwania po awarii. Następnie przedstawimy sposób osiągnięcia wysokiej dostępności i odzyskiwania po awarii w środowisku Oracle w infrastrukturze BareMetal.

## <a name="high-availability-vs-disaster-recovery"></a>Wysoka dostępność a odzyskiwanie po awarii

Zarówno wysoka dostępność, jak i odzyskiwanie po awarii zapewniają pokrycie, ale z różnych typów awarii. Używają różnych funkcji i opcji Oracle Database.

Wysoka dostępność umożliwia systemowi pokonanie wielu awarii bez wpływu na środowisko użytkownika aplikacji. Typowe cechy systemu o wysokiej dostępie obejmują:

- Nadmiarowy sprzęt, który nie ma single point of failure.
- Automatyczne odzyskiwanie po awariach innych niż krytyczne, takich jak awarie dysków lub uszkodzone kable sieciowe.
- Możliwość rzutowania zmian sprzętu i oprogramowania bez zauważalnego wpływu na przetwarzanie.
- Spełnia lub przekracza cele celów czasu odzyskiwania (RTO) i celów punktu odzyskiwania (RPO).

Najpowszechnszą funkcją oprogramowania Oracle używaną w celu wysokiej dostępności jest oracle [real application clusters (RAC).](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92)

Odzyskiwanie po awarii chroni przed nieodwracalnymi zlokalizowanymi awariami, które mogą zaszkodzić podstawowej strategii wysokiej dostępności. W ekosystemie Oracle jest on dostarczany za pośrednictwem replikacji bazy danych, znanej także jako [Oracle Data Guard.](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o funkcjach wysokiej dostępności dla programu Oracle:

> [!div class="nextstepaction"]
> [Funkcje wysokiej dostępności dla programu Oracle w infrastrukturze BareMetal](high-availability-features.md)
