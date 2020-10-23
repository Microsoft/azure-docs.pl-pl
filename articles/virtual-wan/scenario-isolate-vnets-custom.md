---
title: 'Scenariusz: niestandardowa izolacja dla sieci wirtualnych'
titleSuffix: Azure Virtual WAN
description: Scenariusze dotyczące routingu — Zapobiegaj dostępowi wybranych sieci wirtualnych do siebie nawzajem
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ca1ee8418bc08d70a031d81a15dc1b4ace2f1a3a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461825"
---
# <a name="scenario-custom-isolation-for-vnets"></a>Scenariusz: niestandardowa izolacja dla sieci wirtualnych

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W niestandardowym scenariuszu izolacji dla sieci wirtualnych celem jest uniemożliwienie konkretnego zestawu sieci wirtualnych z możliwością uzyskania dostępu do innego określonego zestawu sieci wirtualnych. Jednak sieci wirtualnych są wymagane do uzyskania dostępu do wszystkich gałęzi (sieci VPN/ER/użytkownika). Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Projekt

Aby ustalić, ile tabel tras będzie potrzebnych, można utworzyć macierz łączności. W tym scenariuszu będzie wyglądać podobnie do poniższego, gdzie każda komórka reprezentuje, czy źródło (wiersz) może komunikować się z miejscem docelowym (kolumna):

| Źródło | Do:| *Niebieska sieci wirtualnych* | *Red sieci wirtualnych* | *Gałęzie*|
|---|---|---|---|---|
| **Niebieska sieci wirtualnych** |   &#8594;|   Direct     |           |  Direct |
| **Red sieci wirtualnych**  |   &#8594;|              |   Direct  |  Direct |
| **Gałęzie**   |   &#8594;|   Direct     |   Direct  |  Direct |

Każda z komórek w poprzedniej tabeli zawiera opis, czy wirtualne połączenie sieci WAN ("od", po stronie przepływu, nagłówki wierszy) komunikuje się z miejscem docelowym (po stronie "do" przepływu, nagłówki kolumn w kursywie). W tym scenariuszu nie ma zapór ani sieciowych urządzeń wirtualnych, dlatego komunikacja odbywa się bezpośrednio za pośrednictwem wirtualnej sieci WAN (w związku z tym wyraz "Direct" w tabeli).

Liczba różnych wzorców wierszy będzie wymagana w tym scenariuszu. W takim przypadku trzy tabele tras trasy, które będą wywoływać **RT_BLUE** i **RT_RED** dla sieci wirtualnych, i **domyślne** dla gałęzi. Należy pamiętać, że gałęzie zawsze muszą być skojarzone z domyślną tabelą routingu.

Gałęzie muszą poznać prefiksy z czerwonych i niebieskich sieci wirtualnych, więc wszystkie sieci wirtualnych będą musiały zostać propagowane do wartości domyślnych (również do **RT_BLUE** lub **RT_RED**). Niebieskie i czerwone sieci wirtualnych muszą poznać prefiksy gałęzi, więc gałęzie są propagowane do obu tabel tras **RT_BLUE** i **RT_RED** . W związku z tym jest to ostateczny projekt:

* Niebieskie sieci wirtualne:
  * Skojarzona tabela tras: **RT_BLUE**
  * Propagowanie do tabel tras: **RT_BLUE** i **domyślnych**
* Czerwone sieci wirtualne:
  * Skojarzona tabela tras: **RT_RED**
  * Propagowanie do tabel tras: **RT_RED** i **domyślnych**
* Gałęzi
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **RT_BLUE**, **RT_RED** i **domyślnych**

> [!NOTE]
> Ponieważ wszystkie gałęzie muszą być skojarzone z domyślną tabelą tras, a także do propagowania do tego samego zestawu tabel routingu, wszystkie gałęzie będą miały ten sam profil łączności. Innymi słowy, nie można zastosować koncepcji czerwona/niebieska dla sieci wirtualnych do gałęzi.

> [!NOTE]
> Jeśli wirtualna sieć WAN jest wdrażana w wielu regionach, należy utworzyć **RT_BLUE** i **RT_RED** tabele tras w każdym centrum, a trasy z poszczególnych połączeń sieci wirtualnej muszą być propagowane do tabel tras w każdym koncentratorze wirtualnym przy użyciu etykiet propagacji.

Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="architecture"></a>Przepływ pracy

Na **rysunku 1**znajdują się niebieskie i czerwone połączenia sieci wirtualnej.

* Sieci wirtualnych połączone z niebieską siecią mogą się łączyć ze sobą, a także dotrzeć do wszystkich rozgałęzień (VPN/ER/P2S).
* Czerwona sieci wirtualnych może nawiązać połączenie ze sobą, a także dotrzeć do wszystkich rozgałęzień (VPN/ER/P2S).

Podczas konfigurowania routingu należy wziąć pod uwagę następujące czynności.

1. Utwórz dwie niestandardowe tabele tras w Azure Portal, **RT_BLUE** i **RT_RED**.
2. Dla **RT_BLUE**tabeli tras dla następujących ustawień:
   * **Skojarzenie**: zaznacz wszystkie niebieskie sieci wirtualnych.
   * **Propagacja**: w przypadku gałęzi, wybierz opcję dla gałęzi, co oznacza, że połączenia z gałęzią (VPN/er/P2S) będą propagowane tras do tej tabeli tras.
3. Powtórz te same czynności dla **RT_REDj** tabeli tras dla Red sieci wirtualnych i rozgałęzień (VPN/er/P2S).

Spowoduje to zmianę konfiguracji routingu, tak jak pokazano na poniższej ilustracji.

**Rysunek 1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="Rysunek 1":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
