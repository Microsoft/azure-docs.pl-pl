---
title: Magazyn na platformie BareMetal dla obciążeń Oracle
description: Dowiedz się więcej o magazynie oferowanym przez obciążenia BareMetal Infrastructure for Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559090"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Magazyn na platformie BareMetal dla obciążeń Oracle

W tym artykule podamy omówienie magazynu oferowanego przez obciążenia BareMetal Infrastructure for Oracle.

BareMetal Infrastructure for Oracle oferuje magazyn systemu plików NFS (NetApp Network File System). Magazyn NFS nie wymaga certyfikacji oracle Real Application Clusters (RAC). Aby uzyskać więcej informacji, [zobacz Oracle RAC Technologies Matrix for Linux Clusters (Macierz technologii Oracle RAC dla klastrów systemu Linux).](https://www.oracle.com/database/technologies/tech-generic-linux-new.html)

Ta oferta magazynu obejmuje obsługę warstwy 3 od partnera OEM przy użyciu kontrolerów magazynu A700 lub A800.

Magazyn infrastruktury BareMetal oferuje następujące możliwości magazynu w chmurze Premium:

- Woluminy magazynu dla danych/dziennika/kworum/fsA oferowane za pośrednictwem protokołu dNFS.
- Nadmiarowość dysku *(ochrona przed maksymalnie dwoma awariami dysków).*
- Skalowanie danych na zewnątrz do wielu woluminów ograniczonych do 100 TB na wolumin.
- Skalowanie w górę do wielu kontrolerów magazynu do 12 kontrolerów.
- Brak zarządzania na poziomie dysku *(dodawanie/usuwanie dysków)* automatycznie zajęte przez infrastrukturę.
- Brak przestojów w redystrybucji zawartości pliku do różnych woluminów.
- Możliwość powiększania/zmniejszania woluminów.
- Integracja z programem SnapCenter do tworzenia kopii zapasowych przy użyciu klonowania i programu SnapVault.
- Szyfrowanie danych w spoczynku, obsługa standardu FIPS (140-2).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zagadnieniach dotyczących poprawek infrastruktury BareMetal.

> [!div class="nextstepaction"]
> [Zagadnienia dotyczące poprawek dla programu BareMetal dla programu Oracle](oracle-baremetal-patching.md)

