---
title: 'Scenariusz: dowolne'
titleSuffix: Azure Virtual WAN
description: Scenariusze routingu-dowolnego-do-dowolnego
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 09dddad24794491b53a11f7b0e4347f43f11598b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92440488"
---
# <a name="scenario-any-to-any"></a>Scenariusz: dowolne

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W dowolnym scenariuszu każdy szprych może nawiązać połączenie z innym szprychą. Gdy istnieje wiele centrów, routing między centrami (znany również jako Inter-Hub) jest domyślnie włączony w standardowej wirtualnej sieci WAN. Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Projekt

Aby ustalić, ile tabel tras będzie potrzebnych w wirtualnym scenariuszu sieci WAN, można utworzyć macierz łączności, w której każda komórka reprezentuje, czy źródło (wiersz) może komunikować się z miejscem docelowym (kolumna).

| Źródło |   Działanie |  *Sieci wirtualnych* | *Gałęzie* |
| -------------- | -------- | ---------- | ---|
| Sieci wirtualnych     | &#8594;| Direct | Direct |
| Gałęzie   | &#8594;| Direct  | Direct |

Każda z komórek w poprzedniej tabeli opisuje, czy wirtualne połączenie sieci WAN ("od", po stronie przepływu, nagłówki wierszy) komunikuje się z prefiksem lokalizacji docelowej (po stronie "do" przepływu, nagłówki kolumn w kursywie). W tym scenariuszu nie ma zapór ani sieciowych urządzeń wirtualnych, dlatego komunikacja odbywa się bezpośrednio za pośrednictwem wirtualnej sieci WAN (w związku z tym wyraz "Direct" w tabeli).

Ponieważ wszystkie połączenia zarówno z sieci wirtualnych, jak i gałęzi (VPN, ExpressRoute i VPN użytkownika) mają takie same wymagania dotyczące łączności, wymagana jest pojedyncza tabela tras. W związku z tym wszystkie połączenia będą skojarzone i propagowane do tej samej tabeli tras, domyślnej tabeli tras:

* Sieci wirtualne:
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **domyślne**
* Gałęzi
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **domyślne**

Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Na **rysunku 1**wszystkie sieci wirtualnych i gałęzie (VPN, EXPRESSROUTE, P2S) mogą się łączyć ze sobą. W koncentratorze wirtualnym połączenia działają w następujący sposób:

* Połączenie sieci VPN łączy lokację sieci VPN z bramą sieci VPN.
* Połączenie sieci wirtualnej łączy sieć wirtualną z koncentratorem wirtualnym. Router koncentratora wirtualnego zapewnia funkcjonalność tranzytu między sieci wirtualnych.
* Połączenie ExpressRoute łączy obwód usługi ExpressRoute z bramą ExpressRoute.

Te połączenia (domyślnie podczas tworzenia) są skojarzone z domyślną tabelą tras, chyba że skonfigurowano konfigurację routingu połączenia do **żadnej**lub niestandardowej tabeli tras. Te połączenia również propagują trasy domyślnie do domyślnej tabeli tras. Dzięki temu można dowolnie dowolnie korzystać z dowolnego scenariusza, w którym wszystkie szprychy (Sieć wirtualna, sieci VPN, ER, P2S) mogą dotrzeć do siebie nawzajem.

**Rysunek 1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Rysunek 1":::

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Ten scenariusz jest domyślnie włączony dla standardowej wirtualnej sieci WAN. Jeśli ustawienie rozgałęzienia do gałęzi jest wyłączone w konfiguracji sieci WAN, to nie będzie można nawiązać połączenia między szprychami. Sieci VPN/ExpressRoute/użytkownika są traktowane jako szprychy gałęzie w wirtualnej sieci WAN

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
