---
title: Architektura sieci platformy Azure
description: Ten artykuł zawiera ogólny opis sieci infrastruktury Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89567875"
---
# <a name="azure-network-architecture"></a>Architektura sieci platformy Azure
Architektura sieci platformy Azure zapewnia łączność z Internetu z centrami danych platformy Azure. Wszystkie wdrożone obciążenia (IaaS, PaaS i SaaS) na platformie Azure korzystają z sieci centrum danych platformy Azure.

## <a name="network-topology"></a>Topologia sieci
Architektura sieci centrum danych platformy Azure składa się z następujących składników:

- Sieć brzegowa
- Sieć rozległa
- Sieć regionalnych bram
- Sieć centrum danych

![Diagram sieci platformy Azure](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>Składniki sieci
Krótki opis składników sieci.

- Sieć brzegowa

   - Punkt rozgraniczania między sieciami firmy Microsoft i innymi sieciami (na przykład Internet, Sieć przedsiębiorstwa)
   - Zapewnia komunikację równorzędną z Internetem i [ExpressRoute](../../expressroute/expressroute-introduction.md) na platformie Azure

- Sieć rozległa

   - Inteligentne sieci szkieletowe firmy Microsoft obejmujące Globus
   - Zapewnia łączność między [regionami platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

- Brama regionalna

   - Punkt agregacji dla wszystkich centrów danych w regionie świadczenia usługi Azure
   - Zapewnia ogromną łączność między centrami danych w regionie świadczenia usługi Azure (na przykład wiele setek terabits na centrum danych)

- Sieć centrum danych

   - Zapewnia łączność między serwerami w centrum danych z niską zasubskrybowaną przepustowością

Powyższe składniki sieciowe zostały zaprojektowane w celu zapewnienia maksymalnej dostępności do obsługi zawsze dostępnych danych firmy w chmurze. Nadmiarowość jest zaprojektowana i wbudowana w sieć ze fizycznego aspektu do kontroli protokołu.

## <a name="datacenter-network-resiliency"></a>Odporność sieci centrum danych
Przyjrzyjmy się zasadzie odporności na projektowanie przy użyciu sieci centrum danych.

Sieć centrów danych jest zmodyfikowaną wersją [sieci zamykaną](https://en.wikipedia.org/wiki/Clos_network), co zapewnia wysoką przepustowość wielopunktową dla ruchu w skali chmury. Sieć jest zbudowana przy użyciu dużej liczby urządzeń z asortymentami, aby zmniejszyć wpływ awarii sprzętu. Te urządzenia są strategicznie zlokalizowane w różnych lokalizacjach fizycznych z odrębną domeną napięcia i chłodzenia, aby ograniczyć wpływ zdarzenia środowiska.  Na płaszczyźnie kontroli wszystkie urządzenia sieciowe działają jako tryb routingu warstwy 3 modelu OSI, co eliminuje historyczny problem z pętlą ruchu. Wszystkie ścieżki między różnymi warstwami są aktywne w celu zapewnienia wysokiej nadmiarowości i przepustowości przy użyciu routingu Equal-Cost wielościeżkowego (ECMP).

Na poniższym diagramie przedstawiono, że sieć centrów danych jest zbudowana przez różne warstwy urządzeń sieciowych. Paski na diagramie reprezentują grupy urządzeń sieciowych zapewniające nadmiarowość i wysoką przepustowość.

![Sieć centrum danych](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tego, co firma Microsoft pomaga w zabezpieczeniu infrastruktury platformy Azure, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)
