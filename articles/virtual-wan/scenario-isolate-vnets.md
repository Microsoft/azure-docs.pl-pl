---
title: 'Scenariusz: izolowanie sieci wirtualnych'
titleSuffix: Azure Virtual WAN
description: Scenariusze związane z izolacją sieci wirtualnych
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e5e2ce17be6d8a1fa82d8a92b9b788f0bd2a37b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92424744"
---
# <a name="scenario-isolating-vnets"></a>Scenariusz: izolowanie sieci wirtualnych

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu celem jest zapobieganie sieci wirtualnych się do innych. Jest to znane jako izolowanie sieci wirtualnych. Aby uzyskać informacje na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Projekt

W tym scenariuszu obciążenie w ramach określonej sieci wirtualnej pozostaje izolowane i nie może komunikować się z innymi sieci wirtualnych. Jednak sieci wirtualnych są wymagane do uzyskania dostępu do wszystkich gałęzi (VPN, ER i VPN użytkownika). Aby ustalić, ile tabel tras będzie potrzebnych, można utworzyć macierz łączności. W tym scenariuszu będzie wyglądać tak jak w poniższej tabeli, gdzie każda komórka reprezentuje, czy źródło (wiersz) może komunikować się z miejscem docelowym (kolumna):

| Źródło |   Działanie |  *Sieci wirtualnych* | *Gałęzie* |
| -------------- | -------- | ---------- | ---|
| Sieci wirtualnych     | &#8594;| Direct |   Direct    |
| Gałęzie   | &#8594;|  Direct  |   Direct    |

Każda z komórek w poprzedniej tabeli opisuje, czy wirtualne połączenie sieci WAN ("od", po stronie przepływu, nagłówki wierszy) komunikuje się z prefiksem lokalizacji docelowej (po stronie "do" przepływu, nagłówki kolumn w kursywie). W tym scenariuszu nie ma zapór ani sieciowych urządzeń wirtualnych, dlatego komunikacja odbywa się bezpośrednio za pośrednictwem wirtualnej sieci WAN (w związku z tym wyraz "Direct" w tabeli).

Ta macierz łączności daje nam dwa różne wzorce wierszy, które przekładają się na dwie tabele tras. Wirtualna sieć WAN ma już domyślną tabelę tras, dlatego potrzebna jest inna tabela tras. W tym przykładzie zmienimy nazwę tabeli tras **RT_VNET**.

Sieci wirtualnych zostanie skojarzona z tą tabelą tras **RT_VNET** . Ponieważ potrzebują łączności z gałęziami, gałęzie będą musiały zostać rozpropagowane do **RT_VNET** (w przeciwnym razie sieci wirtualnych nie będzie uczyć się prefiksów gałęzi). Ponieważ gałęzie są zawsze skojarzone z domyślną tabelą tras, sieci wirtualnych będzie musiał zostać propagowane do domyślnej tabeli tras. W związku z tym jest to ostateczny projekt:

* Sieci wirtualne:
  * Skojarzona tabela tras: **RT_VNET**
  * Propagowanie do tabel tras: **domyślne**
* Gałęzi
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **RT_VNET** i **domyślnych**

Zwróć uwagę, że ponieważ tylko gałęzie są propagowane do tabeli tras **RT_VNET**, będą to jedyne prefiksy, które sieci wirtualnych się uczyć, a nie inne sieci wirtualnych.

Aby uzyskać informacje na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Aby skonfigurować ten scenariusz, należy wziąć pod uwagę następujące czynności:

1. Utwórz niestandardową tabelę tras w każdym centrum. W przykładzie tabela tras jest **RT_VNet**. Aby utworzyć tabelę tras, zobacz [jak skonfigurować Routing koncentratora wirtualnego](how-to-virtual-hub-routing.md). Aby uzyskać więcej informacji na temat tabel tras, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
2. Podczas tworzenia tabeli tras **RT_VNet** skonfiguruj następujące ustawienia:

   * **Skojarzenie**: Wybierz sieci wirtualnych, które chcesz odizolować.
   * **Propagacja**: wybierz opcję dla gałęzi, co oznacza, że połączenia z gałęzią (VPN/er/P2S) będą propagowanie tras do tej tabeli tras.

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="Izolowany sieci wirtualnych":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
