---
title: Architektura infrastruktury BareMetal dla oracle
description: Dowiedz się więcej o architekturze kilku konfiguracji baremetalowej infrastruktury dla programu Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 1bdc32c14cfc8986c52a4ea916a6130ee29e6028
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559174"
---
# <a name="architecture-of-baremetal-infrastructure-for-oracle"></a>Architektura infrastruktury BareMetal dla oracle

W tym artykule przyjrzymy się opcjam architektury dla architektury BareMetal Infrastructure for Oracle i funkcjami, które obsługuje każda z nich.

## <a name="single-instance"></a>Jedno wystąpienie

Ta topologia obsługuje pojedyncze wystąpienie programu Oracle Database oracle Data Guard na przykład w celu migracji do infrastruktury BareMetal. Obsługuje ona korzystanie z węzła rezerwowego w celu wysokiej dostępności i prac konserwacyjnych.

[![Diagram przedstawiający architekturę pojedynczego wystąpienia usługi Oracle Database Oracle Data Guard.](media/oracle-baremetal-architecture/single-instance-architecture.png)](media/oracle-baremetal-architecture/single-instance-architecture.png#lightbox)

## <a name="oracle-real-application-clusters-rac-one-node"></a>Oracle Real Application Clusters (RAC) Jeden węzeł

Ta topologia obsługuje konfigurację RAC z magazynem udostępnionym i klastrem GRID. Wystąpienia bazy danych działają tylko w jednym węźle (konfiguracja aktywne/pasywne).

Funkcje obejmują:

- Aktywne/pasywne z jednym węzłem Oracle RAC

    - Automatyczne tryb fail over

    - Szybkie ponowne uruchamianie w drugim węźle

- Skalowanie i trybu fail over w czasie rzeczywistym przy użyciu oprogramowania Oracle RAC

- Konserwacja stopniowa bez przestojów

[![Diagram przedstawiający architekturę konfiguracji oracle RAC One Node (aktywne/pasywne) z jednym węzłem.](media/oracle-baremetal-architecture/one-node-rac-architecture.png)](media/oracle-baremetal-architecture/one-node-rac-architecture.png#lightbox)

## <a name="rac"></a>Rac

Ta topologia obsługuje konfigurację Oracle RAC z magazynem udostępnionym i klastrem siatki, podczas gdy wiele wystąpień na bazę danych działa współbieżnie (konfiguracja aktywne-aktywne).

- Wydajność jest łatwa do skalowania przez aprowizowanie w trybie online dodanych serwerów. 
-  Użytkownicy są aktywni na wszystkich serwerach, a wszystkie serwery mają dostęp do tych samych Oracle Database. 
-  Wszystkie typy konserwacji bazy danych mogą być wykonywane w trybie online lub w sposób stopniowy w celu minimalnego lub zerowego przestoju. 
- Systemy rezerwowe Oracle Active Data Guard (ADG) mogą z łatwością służyć jako systemy testowe do podwójnego zastosowania. 

Ta konfiguracja umożliwia przetestowanie wszystkich zmian na dokładnej kopii produkcyjnej bazy danych przed ich zastosowaniem w środowisku produkcyjnym.

> [!NOTE]
> Jeśli zamierzasz używać funkcji Active Data Guard Far Sync (tryb synchroniczny), należy wziąć pod uwagę strefy regionalne, w których ta funkcja jest obsługiwana. W przypadku regionów rozproszonych geograficznych zalecamy używanie funkcji Data Guard z trybem asynchronicznym.

[![Diagram przedstawiający architekturę konfiguracji Oracle RAC active-active.](media/oracle-baremetal-architecture/rac-architecture.png)](media/oracle-baremetal-architecture/rac-architecture.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat aprowizowania wystąpień programu BareMetal dla obciążeń Oracle.

> [!div class="nextstepaction"]
> [Aprowizuj infrastrukturę BareMetal dla programu Oracle](oracle-baremetal-provision.md)

