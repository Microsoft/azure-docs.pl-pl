---
title: 'Azure ExpressRoute: Resetowanie komunikacji równorzędnej obwodu przy użyciu Azure Portal'
description: Informacje na temat wyłączania i włączania komunikacji równorzędnej obwodu usługi Azure ExpressRoute przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582991"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>Resetowanie komunikacji równorzędnej obwodu ExpressRoute Użyj Azure Portal

W tym artykule opisano sposób wyłączania i włączania komunikacji równorzędnej obwodu usługi ExpressRoute przy użyciu Azure Portal. Wyłączenie komunikacji równorzędnej spowoduje wyłączenie sesji protokołu BGP zarówno dla podstawowego, jak i pomocniczego połączenia obwodu usługi ExpressRoute. Utracisz łączność za pośrednictwem tej komunikacji równorzędnej z firmą Microsoft. Po włączeniu komunikacji równorzędnej zostanie przeprowadzona sesja protokołu BGP na podstawowym i pomocniczym połączeniu z obwodem usługi ExpressRoute. Będziesz odzyskać łączność za pośrednictwem tej komunikacji równorzędnej z firmą Microsoft. Komunikacja równorzędna firmy Microsoft i prywatna Komunikacja równorzędna Azure można włączyć i wyłączyć niezależnie od obwodu usługi ExpressRoute. Przy pierwszym konfigurowaniu komunikacji równorzędnej w obwodzie ExpressRoute, Komunikacja równorzędna jest domyślnie włączona.

Istnieje kilka scenariuszy, w których może być przydatne Resetowanie komunikacji równorzędnej ExpressRoute.
* Przetestuj projekt i implementację odzyskiwania po awarii. Na przykład masz dwa obwody usługi ExpressRoute. Można wyłączyć komunikację równorzędną jednego obwodu i wymusić przełączenie ruchu sieciowego do trybu failover w innym obwodzie.
* Włącz wykrywanie dwukierunkowego przekazywania (BFD) w prywatnej komunikacji równorzędnej Azure lub komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute. BFD domyślnie włączone w prywatnej komunikacji równorzędnej Azure, jeśli obwód usługi ExpressRoute został utworzony po 1 2018 sierpnia i w komunikacji równorzędnej firmy Microsoft. Jeśli obwód ExpressRoute zostanie utworzony po styczniu 10 2020. Jeśli obwód został utworzony przed tym, BFD nie został włączony. Możesz włączyć BFD, wyłączając komunikację równorzędną i włączając ją ponownie. 

### <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

## <a name="reset-a-peering"></a>Resetowanie komunikacji równorzędnej

1. Wybierz obwód, dla którego chcesz wprowadzić zmiany konfiguracji komunikacji równorzędnej.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Lista obwodów ExpressRoute":::

1. Wybierz konfigurację komunikacji równorzędnej, którą chcesz włączyć lub wyłączyć.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Przegląd obwodu usługi ExpressRoute":::

1. Usuń zaznaczenie pola wyboru **Włącz komunikację równorzędną** i wybierz pozycję **Zapisz** , aby wyłączyć konfigurację komunikacji równorzędnej.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Wyłącz prywatną komunikację równorzędną":::

1. Można ponownie włączyć komunikację równorzędną, sprawdzając opcję **Włącz komunikację równorzędną** i wybierając pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz pomocy dotyczącej rozwiązywania problemów z programem ExpressRoute, zapoznaj się z następującymi artykułami:
* [Weryfikowanie połączenia usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)
