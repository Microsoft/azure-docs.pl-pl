---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 7aee201b0419b65c7ea8ddcb6f2d42ffaff4711e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101749997"
---
|  | **Prywatna komunikacja równorzędna** | **Komunikacja równorzędna firmy Microsoft** |  **Publiczna Komunikacja równorzędna** (przestarzałe dla nowych obwodów) |
| --- | --- | --- | --- |
| **Max. # prefiksy obsługiwane na komunikację równorzędną** |4000 domyślnie 10 000 z ExpressRoute Premium |200 |200 |
| **Obsługiwane zakresy adresów IP** |Dowolny prawidłowy adres IP w sieci WAN. |Publiczne adresy IP należące do Ciebie lub przez dostawcę połączenia. |Publiczne adresy IP należące do Ciebie lub przez dostawcę połączenia. |
| **Wymagania dotyczące liczby** |Prywatne i publiczne jako liczby. Jeśli zdecydujesz się na użycie jednego z nich, musisz mieć własny numer publiczny. |Prywatne i publiczne jako liczby. Należy jednak udowodnić własność publicznych adresów IP. |Prywatne i publiczne jako liczby. Należy jednak udowodnić własność publicznych adresów IP. |
| **Obsługiwane protokoły IP**| IPv4, IPv6 (wersja zapoznawcza) |  IPv4, IPv6 | Protokół IPv4 |
| **Adresy IP interfejsu routingu** |RFC1918 i publiczne adresy IP |Publiczne adresy IP zarejestrowane w rejestrach routingu. |Publiczne adresy IP zarejestrowane w rejestrach routingu. |
| **Obsługa skrótów MD5** |Tak |Tak |Tak |