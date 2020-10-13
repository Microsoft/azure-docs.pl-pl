---
title: Uaktualnianie wirtualnej sieci WAN platformy Azure z warstwy Podstawowa do standardowa — Azure Portal | Microsoft Docs
description: Możesz uaktualnić wirtualny typ sieci WAN w celu uzyskania większej funkcjonalności.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 6290d89ed0ee539b4df4c2c8bc9070097da98c81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91447364"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Uaktualnianie wirtualnej sieci WAN z warstwy Podstawowa do standardowa

Ten artykuł ułatwia uaktualnienie podstawowej sieci WAN do standardowej sieci WAN. Typ sieci WAN "Basic" tworzy wszystkie centra wewnątrz niej jako podstawowe centra SKU. W podstawowym centrum jest ograniczone tylko do funkcjonalności sieci VPN typu lokacja-lokacja. Typ sieci WAN "standardowy" tworzy wszystkie centra wewnątrz niej jako standardowe centra jednostek SKU. W przypadku korzystania z centrów standardowych można włączyć sieć VPN ExpressRoute, użytkownika (punkt-lokacja), koncentratora pełnego sieci i tranzytu sieci wirtualnej za pośrednictwem centrów platformy Azure.

W poniższej tabeli przedstawiono konfiguracje dostępne dla każdego typu sieci WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Aby zmienić typ wirtualnej sieci WAN

1. Na stronie wirtualnej sieci WAN wybierz pozycję **Konfiguracja** , aby otworzyć stronę konfiguracja.

   ![Zrzut ekranu przedstawiający stronę "Konfiguracja" z polem tekstowym informacji o uaktualnianiu do wirtualnej sieci WAN typu standardowego wyróżnionej na dole.](./media/upgrade-virtual-wan/1.png)
2. W polu Typ wirtualnej sieci WAN wybierz pozycję **standardowa** z listy rozwijanej.

   ![Zrzut ekranu przedstawiający menu rozwijane "wirtualny typ sieci WAN".](./media/upgrade-virtual-wan/2.png)
3. Należy pamiętać, że w przypadku uaktualniania do standardowej wirtualnej sieci WAN nie można wrócić do podstawowej wirtualnej sieci WAN. Wybierz pozycję **Potwierdź** , jeśli chcesz przeprowadzić uaktualnienie.

   ![Zrzut ekranu przedstawiający okno dialogowe "potwierdzenie uaktualnienia".](./media/upgrade-virtual-wan/4.png)
4. Po zapisaniu zmiany Twoja wirtualna Strona sieci WAN będzie wyglądać podobnie do tego przykładu.

   ![Diagram usługi Virtual WAN](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).