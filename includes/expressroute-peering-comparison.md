---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: b6ab3e80c7a27263039de3d371adea3d49ddfcc9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75437018"
---
|  | **Prywatna komunikacja równorzędna** | **Komunikacja równorzędna firmy Microsoft** |  **Publiczna Komunikacja równorzędna** (przestarzałe dla nowych obwodów) |
| --- | --- | --- | --- |
| **Max. # prefiksy obsługiwane na komunikację równorzędną** |4000 domyślnie 10 000 z ExpressRoute Premium |200 |200 |
| **Obsługiwane zakresy adresów IP** |Dowolny prawidłowy adres IP w sieci WAN. |Publiczne adresy IP należące do Ciebie lub przez dostawcę połączenia. |Publiczne adresy IP należące do Ciebie lub przez dostawcę połączenia. |
| **Wymagania dotyczące liczby** |Prywatne i publiczne jako liczby. Jeśli zdecydujesz się na użycie jednego z nich, musisz mieć własny numer publiczny. |Prywatne i publiczne jako liczby. Należy jednak udowodnić własność publicznych adresów IP. |Prywatne i publiczne jako liczby. Należy jednak udowodnić własność publicznych adresów IP. |
| **Obsługiwane protokoły IP**| Protokół IPv4 |  IPv4, IPv6 | Protokół IPv4 |
| **Adresy IP interfejsu routingu** |RFC1918 i publiczne adresy IP |Publiczne adresy IP zarejestrowane w rejestrach routingu. |Publiczne adresy IP zarejestrowane w rejestrach routingu. |
| **Obsługa skrótów MD5** |Tak |Tak |Tak |