---
title: 'Konfigurowanie niestandardowych zasad protokołu IPsec dla wirtualnej sieci WAN platformy Azure: Portal | Microsoft Docs'
description: Dowiedz się, jak skonfigurować niestandardowe zasady protokołu IPsec dla wirtualnej sieci WAN platformy Azure przy użyciu portalu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 07eae453441ea8bff81d7cdb60f9c46c08a22829
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91851181"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Konfigurowanie niestandardowych zasad protokołu IPsec dla wirtualnej sieci WAN przy użyciu portalu

W Azure Portal można skonfigurować niestandardowe zasady protokołu IPsec dla połączenia wirtualnej sieci prywatnej sieci WAN. Zasady niestandardowe są przydatne, gdy obie strony (lokalna i Brama sieci VPN platformy Azure) mają korzystać z tych samych ustawień dla usług IKE phase 1 i IKE Phase 2.

## <a name="working-with-custom-policies"></a>Praca z zasadami niestandardowymi

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Konfigurowanie zasad

1. **Znajdź koncentrator wirtualny**. Przejdź w przeglądarce do witryny [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) i zaloguj się przy użyciu konta platformy Azure. Przejdź do wirtualnego zasobu sieci WAN i Znajdź koncentrator wirtualny, z którym jest połączona lokacja sieci VPN.
2. **Wybierz witrynę sieci VPN**. Na stronie Omówienie centrum kliknij pozycję **Sieć VPN (lokacja** lokacja), a następnie wybierz LOKACJĘ sieci VPN, dla której chcesz skonfigurować niestandardowe zasady protokołu IPSec.

   ![Wybierz](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Edytuj połączenie sieci VPN**. Z **menu kontekstowego** **...** wybierz pozycję **Edytuj połączenie sieci VPN**.

   ![edytuj](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Skonfiguruj ustawienia**. Na stronie **Edytowanie połączenia sieci VPN** Zmień ustawienie IPSec z domyślne na niestandardowe i Dostosuj zasady protokołu IPSec. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

   ![Skonfiguruj i Zapisz](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).