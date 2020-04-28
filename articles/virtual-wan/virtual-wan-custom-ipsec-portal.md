---
title: 'Konfigurowanie niestandardowych zasad protokołu IPsec dla wirtualnej sieci WAN platformy Azure: Portal | Microsoft Docs'
description: Dowiedz się, jak skonfigurować niestandardowe zasady protokołu IPsec dla wirtualnej sieci WAN platformy Azure przy użyciu portalu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73515748"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Konfigurowanie niestandardowych zasad protokołu IPsec dla wirtualnej sieci WAN przy użyciu portalu

Można skonfigurować niestandardowe zasady protokołu IPsec dla wirtualnej sieci WAN w Azure Portal. Zasady niestandardowe są przydatne, gdy obie strony (lokalna i Brama sieci VPN platformy Azure) mają korzystać z tych samych ustawień dla usług IKE phase 1 i IKE Phase 2.

## <a name="working-with-custom-policies"></a>Praca z zasadami niestandardowymi

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Konfigurowanie zasad

1. **Znajdź koncentrator wirtualny**. Przejdź w przeglądarce do witryny [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) i zaloguj się przy użyciu konta platformy Azure. Znajdź koncentrator wirtualny dla witryny.
2. **Wybierz witrynę sieci VPN**. Na stronie centrum wybierz lokację sieci VPN, dla której chcesz skonfigurować zasady niestandardowe.

   ![Wybierz](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Edytuj połączenie sieci VPN**. Z **menu kontekstowego** **...** wybierz pozycję **Edytuj połączenie sieci VPN**.

   ![edytuj](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Skonfiguruj ustawienia**. Na stronie **Edytowanie połączenia sieci VPN** Skonfiguruj ustawienia. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

   ![Skonfiguruj i Zapisz](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).