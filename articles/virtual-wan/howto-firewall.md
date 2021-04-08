---
title: Instalowanie zapory platformy Azure w wirtualnym koncentratorze sieci WAN
titleSuffix: Azure Virtual WAN
description: Procedura konfigurowania zapory platformy Azure w wirtualnym koncentratorze sieci WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 577340e485550e84941a33d82b58aa6ff1c933d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983671"
---
# <a name="configure-azure-firewall-in-a-virtual-wan-hub"></a>Konfigurowanie zapory platformy Azure w wirtualnym koncentratorze sieci WAN

**Zabezpieczony centrum** to wirtualne Centrum sieci WAN platformy Azure z zaporą platformy Azure. W tym artykule przedstawiono kroki umożliwiające konwersję wirtualnego koncentratora sieci WAN na zabezpieczone centrum przez zainstalowanie zapory platformy Azure bezpośrednio z poziomu stron portalu sieci wirtualnej platformy Azure.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach w tym artykule przyjęto założenie, że wirtualna sieć WAN została już wdrożona z co najmniej jednym koncentratorem.

Aby utworzyć nową wirtualną sieć WAN i nowe centrum, wykonaj czynności opisane w następujących artykułach:

* [Tworzenie wirtualnej sieci WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Tworzenie koncentratora](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-virtual-hubs"></a>Wyświetl centra wirtualne

Na stronie **Przegląd** dla wirtualnej sieci WAN przedstawiono listę centrów wirtualnych i zabezpieczonych centrów. Na poniższej ilustracji przedstawiono wirtualną sieć WAN bez zabezpieczonych centrów.

[![Zrzut ekranu przedstawia stronę przegląd wirtualnej sieci WAN z listą koncentratorów wirtualnych.](./media/howto-firewall/overview.png)](./media/howto-firewall/overview.png#lightbox)

## <a name="convert-to-secured-hub"></a>Konwertuj na zabezpieczone centrum

1. Na stronie **Przegląd** dla wirtualnej sieci WAN wybierz centrum, które chcesz przekonwertować na zabezpieczone centrum. Na stronie koncentrator wirtualny są dostępne dwie opcje wdrożenia zapory platformy Azure w tym centrum. Wybierz jedną z opcji.

   [![Zrzut ekranu przedstawia stronę przeglądową wirtualnej sieci WAN, w której można wybrać opcję Konwertuj na zabezpieczenia koncentratora lub zapory platformy Azure.](./media/howto-firewall/security.png)](./media/howto-firewall/security.png#lightbox)

1. Po wybraniu jednej z opcji zostanie wyświetlona strona **Konwertuj na zabezpieczenie centrum** . Wybierz centrum do przekonwertowania, a następnie wybierz pozycję **Dalej: Zapora platformy Azure** w dolnej części strony.

   [![Wybierz centrum](./media/howto-firewall/select-hub.png)](./media/howto-firewall/select-hub.png#lightbox)
1. Po zakończeniu przepływu pracy wybierz pozycję **Potwierdź**.

   [![Zrzut ekranu przedstawia okienko Konwertuj do bezpiecznego centrum z wybranym potwierdzeniem.](./media/howto-firewall/confirm.png)](./media/howto-firewall/confirm.png#lightbox)

1. Po przekształceniu centrum w zabezpieczone centrum można je wyświetlić na stronie **Omówienie** wirtualnej sieci WAN.

   [![Wyświetl zabezpieczone centrum](./media/howto-firewall/secured-hub.png)](./media/howto-firewall/secured-hub.png#lightbox)

## <a name="view-hub-resources"></a>Wyświetlanie zasobów centrum

Na stronie **Omówienie** wirtualnej sieci WAN wybierz zabezpieczone centrum. Na stronie centrum można wyświetlić wszystkie zasoby koncentratora wirtualnego, w tym zapory platformy Azure.

[![Wyświetlanie zasobów centrum](./media/howto-firewall/view-resources.png)](./media/howto-firewall/view-resources.png#lightbox)

Aby wyświetlić ustawienia zapory platformy Azure z bezpiecznego centrum, w obszarze **zabezpieczenia** wybierz pozycję **bezpieczne ustawienia koncentratora wirtualnego**.
[![Wyświetl ustawienia centrum](./media/howto-firewall/hub-settings.png)](./media/howto-firewall/hub-settings.png#lightbox)

## <a name="configure-additional-settings"></a>Skonfiguruj dodatkowe ustawienia

Aby skonfigurować dodatkowe ustawienia zapory platformy Azure dla koncentratora wirtualnego, wybierz link do **Menedżera zapory platformy Azure**. Aby uzyskać informacje na temat zasad zapory, zobacz [Menedżer zapory platformy Azure](../firewall-manager/secure-cloud-network.md#create-a-firewall-policy-and-secure-your-hub).

[![Ustawienia dodatkowe](./media/howto-firewall/additional-settings.png)](./media/howto-firewall/additional-settings.png#lightbox)

Aby powrócić do strony **omówienia** centrum, możesz wrócić do tyłu, klikając ścieżkę, jak pokazano na poniższej ilustracji.

[![Wróć do omówienia](./media/howto-firewall/arrow.png)](./media/howto-firewall/arrow.png#lightbox)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
