---
title: 'Azure ExpressRoute: Resetowanie komunikacji równorzędnej obwodu przy użyciu Azure Portal'
description: Informacje na temat wyłączania i włączania komunikacji równorzędnej obwodu usługi Azure ExpressRoute przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680288"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Resetowanie komunikacji równorzędnej obwodu usługi ExpressRoute przy użyciu Azure Portal

W tym artykule opisano sposób wyłączania i włączania komunikacji równorzędnej obwodu usługi Azure ExpressRoute przy użyciu Azure Portal. Wyłączenie komunikacji równorzędnej powoduje wyłączenie sesji Border Gateway Protocol (BGP) zarówno dla podstawowego, jak i pomocniczego połączenia obwodu usługi ExpressRoute. Po włączeniu komunikacji równorzędnej zostanie przywrócona sesja protokołu BGP na podstawowym i pomocniczym połączeniu z obwodem usługi ExpressRoute.

> [!Note]
> Przy pierwszym konfigurowaniu komunikacji równorzędnej w obwodzie ExpressRoute, Komunikacja równorzędna jest domyślnie włączona.

Resetowanie komunikacji równorzędnej ExpressRoute może być przydatne w następujących scenariuszach:

* Testujesz projekt i implementację odzyskiwania po awarii. Załóżmy na przykład, że masz dwa obwody usługi ExpressRoute. Można wyłączyć komunikację równorzędną jednego obwodu i wymusić, aby ruch sieciowy był używany przez inny obwód.

* Chcesz włączyć funkcję wykrywania dwukierunkowego przekazywania (BFD) na prywatnej komunikacji równorzędnej Azure lub komunikacji równorzędnej firmy Microsoft. Jeśli obwód usługi ExpressRoute został utworzony przed 1 sierpnia 2018 w prywatnej komunikacji równorzędnej Azure lub przed 10 stycznia 2020, w przypadku komunikacji równorzędnej firmy Microsoft, BFD nie został domyślnie włączony. Zresetuj komunikację równorzędną, aby włączyć BFD.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

W przeglądarce przejdź do [Azure Portal](https://portal.azure.com), a następnie zaloguj się przy użyciu konta platformy Azure.

## <a name="reset-a-peering"></a>Resetowanie komunikacji równorzędnej

Komunikację równorzędną firmy Microsoft i prywatną komunikację równorzędną platformy Azure można resetować niezależnie do obwodu usługi ExpressRoute.

1. Wybierz obwód, który chcesz zmienić.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Zrzut ekranu pokazujący Wybieranie obwodu na liście obwodów ExpressRoute.":::

1. Wybierz konfigurację komunikacji równorzędnej, którą chcesz zresetować.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Zrzut ekranu pokazujący wybór komunikacji równorzędnej w obwodzie usługi ExpressRoute.":::

1. Wyczyść pole wyboru **Włącz komunikację równorzędną** , a następnie wybierz pozycję **Zapisz** , aby wyłączyć konfigurację komunikacji równorzędnej.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Zrzut ekranu przedstawiający czyszczenie pola wyboru Włącz komunikację równorzędną.":::

1. Zaznacz pole wyboru **Włącz komunikację równorzędną** , a następnie wybierz pozycję **Zapisz** , aby ponownie włączyć konfigurację komunikacji równorzędnej.

## <a name="next-steps"></a>Następne kroki

Aby rozwiązać problemy z ExpressRoute, zobacz następujące artykuły:

* [Weryfikowanie połączenia usługi ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Rozwiązywanie problemów z wydajnością sieci](expressroute-troubleshooting-network-performance.md)
