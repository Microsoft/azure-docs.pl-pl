---
title: Konfigurowanie preferencji routingu sieciowego
titleSuffix: Azure Storage
description: Skonfiguruj preferencję routingu sieciowego dla konta usługi Azure Storage, aby określić, w jaki sposób ruch sieciowy jest kierowany do konta z klientów przez Internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700846"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Konfigurowanie preferencji routingu sieciowego dla usługi Azure Storage

W tym artykule opisano, jak można skonfigurować preferencję routingu sieciowego i punkty końcowe specyficzne dla trasy dla konta magazynu. 

Preferencja routingu sieciowego określa, w jaki sposób ruch sieciowy jest kierowany do konta z klientów przez Internet. Punkty końcowe specyficzne dla trasy są nowymi punktami końcowymi tworzonymi przez usługę Azure Storage dla konta magazynu. Te punkty końcowe kierują ruchem do odpowiedniej ścieżki bez zmiany domyślnego preferencji routingu. Aby dowiedzieć się więcej, zobacz [preferencja routingu sieciowego dla usługi Azure Storage](network-routing-preference.md).

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>Skonfiguruj preferencję routingu dla domyślnego publicznego punktu końcowego

Domyślnie preferencja routingu dla publicznego punktu końcowego konta magazynu jest ustawiona na Microsoft Global Network. Jako domyślne preferencje routingu dla publicznego punktu końcowego konta magazynu można wybrać między siecią globalną i routingiem internetowym firmy Microsoft. Aby dowiedzieć się więcej o różnicach między tymi dwoma typami routingu, zobacz [preferencja routingu sieciowego dla usługi Azure Storage](network-routing-preference.md). 

Aby zmienić preferencję routingu na Routing internetowy:

1. Przejdź do swojego konta magazynu w portalu.

2. W obszarze **Ustawienia** wybierz pozycję **Sieć**.

    > [!div class="mx-imgBorder"]
    > ![Opcja menu Sieć](./media/configure-network-routing-preference/networking-option.png)

3.  Na karcie **zapory i sieci wirtualne** w obszarze **Routing sieciowy** Zmień ustawienie **preferencji Routing** na **Routing internetowy**.

4.  Kliknij pozycję **Zapisz**.

    > [!div class="mx-imgBorder"]
    > ![Opcja routingu internetowego](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>Konfigurowanie punktu końcowego specyficznego dla trasy

Można również skonfigurować punkt końcowy specyficzny dla trasy. Można na przykład ustawić preferencję routingu domyślnego punktu końcowego na *Routing internetowy*, a następnie opublikować punkt końcowy specyficzny dla trasy, który umożliwi ruch między klientami w Internecie a kontem magazynu, aby można było je kierować za pośrednictwem sieci globalnej firmy Microsoft.

1.  Przejdź do swojego konta magazynu w portalu.

2.  W obszarze **Ustawienia** wybierz pozycję **Sieć**.

3.  Na karcie **zapory i sieci wirtualne** w obszarze **Publikowanie punktów końcowych właściwych dla trasy** wybierz preferencję routingu punktu końcowego określonego dla trasy, a następnie kliknij przycisk **Zapisz**. To preferencje ma wpływ tylko na punkt końcowy dotyczący trasy. Preferencja nie ma wpływu na domyślne preferencje routingu.  

    Na poniższej ilustracji przedstawiono wybraną opcję **routingu sieciowego firmy Microsoft** .

    > [!div class="mx-imgBorder"]
    > ![Opcja routingu sieci firmy Microsoft](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>Znajdź nazwę punktu końcowego dla punktu końcowego określonego dla trasy

W przypadku skonfigurowania punktu końcowego określonego dla trasy można znaleźć punkt końcowy we właściwościach konta magazynu.

1.  W obszarze **Ustawienia** wybierz pozycję **Właściwości**.

    > [!div class="mx-imgBorder"]
    > ![opcja menu właściwości](./media/configure-network-routing-preference/properties.png)

2.  Punkt końcowy **routingu sieci firmy Microsoft** jest wyświetlany dla każdej usługi, która obsługuje preferencje routingu. Ten obraz pokazuje punkt końcowy dla usług BLOB i plików.

    > [!div class="mx-imgBorder"]
    > ![Opcja routingu sieci firmy Microsoft dla punktów końcowych specyficznych dla trasy](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>Zobacz też

- [Preferencja routingu sieciowego](network-routing-preference.md)
- [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](storage-network-security.md)
- [Zalecenia dotyczące zabezpieczeń usługi BLOB Storage](../blobs/security-recommendations.md)
