---
title: 'Scenariusz: kierowanie do usług udostępnionych sieci wirtualnych'
titleSuffix: Azure Virtual WAN
description: Scenariusze dotyczące routingu — skonfiguruj trasy, aby uzyskać dostęp do udostępnionej sieci wirtualnej usługi z obciążeniem, które ma być dostępne dla każdej sieci wirtualnej i gałęzi.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: b8cc59b805cd757edce79a14d124ea244b4652a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267486"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Scenariusz: kierowanie do usług udostępnionych sieci wirtualnych

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu celem jest skonfigurowanie tras w celu uzyskania dostępu do **udostępnionej** sieci wirtualnej usługi z obciążeniami, które mają być dostępne dla każdej sieci wirtualnej i gałęzi (VPN/er/P2S). Przykłady tych udostępnionych obciążeń mogą obejmować Virtual Machines z usługami takimi jak kontrolery domeny lub udziały plików, lub usługi platformy Azure uwidocznione wewnętrznie za pomocą [prywatnych punktów końcowych platformy Azure](../private-link/private-endpoint-overview.md).

Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Projekt

W celu podsumowania wymagań tego scenariusza możemy użyć macierzy łączności. W macierzy każda komórka zawiera informacje o tym, czy połączenie wirtualnej sieci WAN (po stronie "od" przepływu, nagłówki wierszy w tabeli) uzyskuje prefiks docelowy (po stronie "do" przepływu, nagłówki kolumn w postaci kursywy w tabeli) dla określonego przepływu ruchu. "X" oznacza, że łączność jest zapewniana przez wirtualną sieć WAN:

**Macierz łączności**

| Źródło             | Do:   |*Izolowany sieci wirtualnych*|*Udostępniona Sieć wirtualna*|*Gałęzie*|
|---|---|---|---|---|
|**Izolowany sieci wirtualnych**|&#8594;|                |        X        |       X      |
|**Udostępnione sieci wirtualnych**  |&#8594;|       X        |        X        |       X      |
|**Gałęzie**      |&#8594;|       X        |        X        |       X      |

Podobnie jak w przypadku [scenariusza izolowanej sieci wirtualnej](scenario-isolate-vnets.md), ta macierz łączności daje nam dwa różne wzorce wierszy, które przekładają się na dwie tabele tras (sieci wirtualnych usługi udostępnione i gałęzie mają te same wymagania dotyczące łączności). Wirtualna sieć WAN ma już domyślną tabelę tras, dlatego będziemy potrzebować innej niestandardowej tabeli tras, która będzie wywoływała **RT_SHARED** w tym przykładzie.

Sieci wirtualnych zostanie skojarzona z tabelą tras **RT_SHARED** . Ponieważ potrzebują łączności z oddziałami i sieci wirtualnych usługi udostępnionej, Sieć wirtualna i gałęzie udostępnionej usługi muszą zostać propagowane do **RT_SHARED** (w przeciwnym razie sieci wirtualnych nie będzie uczyć się gałęzi i udostępnionych prefiksów sieci wirtualnej). Ze względu na to, że gałęzie są zawsze skojarzone z domyślną tabelą tras, a wymagania dotyczące łączności są takie same dla usług udostępnionych sieci wirtualnych, zostanie ona skojarzona z domyślną tabelą tras.

W związku z tym jest to ostateczny projekt:

* Izolowane sieci wirtualne:
  * Skojarzona tabela tras: **RT_SHARED**
  * Propagowanie do tabel tras: **domyślne**
* Sieci wirtualne usług udostępnionych:
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **RT_SHARED** i **domyślnych**
* Gałęzi
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **RT_SHARED** i **domyślnych**

> [!NOTE]
> Jeśli wirtualna sieć WAN jest wdrażana w wielu regionach, należy utworzyć tabelę tras **RT_SHARED** w każdym centrum, a trasy z poszczególnych usług udostępnionych sieci wirtualnej i połączenia gałęzi muszą być propagowane do tabel tras w każdym koncentratorze wirtualnym przy użyciu etykiet propagacji.

Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Aby skonfigurować scenariusz, należy wziąć pod uwagę następujące czynności:

1. Zidentyfikuj sieć wirtualną **usług udostępnionych** .
2. Utwórz niestandardową tabelę tras. W tym przykładzie odwołujemy się do tabeli tras jako **RT_SHARED**. Aby uzyskać instrukcje dotyczące tworzenia tabeli tras, zobacz [jak skonfigurować Routing koncentratora wirtualnego](how-to-virtual-hub-routing.md). Użyj następujących wartości jako wytycznych:

   * **Skojarzenie**
     * W przypadku **sieci wirtualnych *z wyjątkiem* sieci wirtualnej usług udostępnionych**wybierz sieci wirtualnych do wyodrębnienia. Oznacza to, że wszystkie te sieci wirtualnych (poza siecią wirtualną usług udostępnionych) będą mogły uzyskać dostęp do miejsca docelowego na podstawie tras RT_SHARED tabeli tras.

   * **Propagacja**
      * W przypadku **gałęzi**, Propaguj trasy do tej tabeli tras, oprócz innych tabel tras, które mogły już zostać zaznaczone. Ze względu na ten krok tabela tras RT_SHARED będzie uczyć się tras ze wszystkich połączeń rozgałęzień (VPN/ER/użytkowników sieci VPN).
      * Dla **sieci wirtualnych**wybierz **sieć wirtualną usługi udostępnione**. Ze względu na ten krok RT_SHARED tabeli tras będzie uczyć się tras z połączenia sieci wirtualnej usług udostępnionych.

Spowoduje to wyświetlenie konfiguracji routingu na poniższej ilustracji:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Sieć wirtualna usług udostępnionych" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
