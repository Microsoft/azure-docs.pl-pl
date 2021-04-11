---
title: Preferencje routingu nietaryfowe na platformie Azure
description: Dowiedz się, jak skonfigurować preferencję routingu dla zasobów wychodzących danych do dostawcy usługi CDN.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: e6276ec1a1c3f52b6574ee60d40e743a183086eb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060588"
---
# <a name="what-is-routing-preference-unmetered"></a>Czym jest nietaryfowa preferencja routingu?

Nietaryfowe preferencje routingu są dostępne dla dostawców usług Content Delivery Network (CDN), którzy mają hostowaną zawartość źródłową na platformie Azure. Usługa umożliwia dostawcom sieci CDN nawiązywanie bezpośredniego połączenia komunikacji równorzędnej z routerami usługi Microsoft Global Network Edge w różnych lokalizacjach.

![Nietaryfowe preferencje routingu](media/routing-preference-unmetered/unmetered.png)

Ruch sieciowy pochodzący z pochodzenia na platformie Azure przeznaczony dla dostawcy sieci CDN korzysta z zalet bezpośredniej łączności.
* Opłaty za transfer danych w przypadku ruchu pochodzącego z zasobów platformy Azure, które są kierowane za pośrednictwem tych bezpośrednich linków, są bezpłatne.
* Bezpośrednie łączenie między dostawcą sieci CDN a źródłem na platformie Azure zapewnia optymalną wydajność, ponieważ nie ma żadnych przeskoków między. Dzięki temu obciążenie sieci CDN często pobiera dane ze źródła.

## <a name="configuring-routing-preference-unmetered"></a>Konfigurowanie nietaryfowych preferencji routingu

Aby skorzystać z preferencji routingu nietaryfowego, dostawcy sieci CDN muszą być częścią tego programu. Jeśli dostawca sieci CDN nie jest częścią programu, skontaktuj się z dostawcą usługi CDN.

Następnie skonfiguruj preferencję routingu dla zasobów i ustaw dla ustawienia Typ preferencji Routing wartość **Internet**. Można skonfigurować preferencje routingu podczas tworzenia publicznego adresu IP, a następnie skojarzyć publiczny adres IP z zasobami, takimi jak maszyny wirtualne, usługi równoważenia obciążenia połączone z Internetem i nie tylko. [Dowiedz się, jak skonfigurować preferencję routingu dla publicznego adresu IP przy użyciu Azure Portal](routing-preference-portal.md)

Możesz również włączyć preferencję routingu dla konta magazynu i opublikować drugi punkt końcowy, który musi być używany przez dostawcę usługi CDN do pobierania danych z lokalizacji źródłowej magazynu. Na przykład opublikowanie punktu końcowego specyficznego dla trasy internetowej dla konta magazynu *StorageAccountA* spowoduje opublikowanie drugiego punktu końcowego dla usług magazynu, jak pokazano poniżej:

![Preferencja routingu dla kont magazynu](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie preferencji routingu dla maszyny wirtualnej przy użyciu Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Konfigurowanie preferencji routingu dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure](configure-routing-preference-virtual-machine-cli.md)
* [Skonfiguruj preferencję routingu dla konta magazynu](../storage/common/network-routing-preference.md)