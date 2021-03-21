---
title: Co to jest usługa Azure Route Server (wersja zapoznawcza)?
description: Dowiedz się, jak usługa Azure Route Server może uprościć Routing między urządzeniem wirtualnym sieci (urządzenie WUS) i siecią wirtualną.
services: route-server
author: duongau
ms.service: route-server
ms.topic: overview
ms.date: 03/02/2021
ms.author: duau
ms.openlocfilehash: d868c064b96f58ab3febc1fd3b7f20b74d507cb0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437165"
---
# <a name="what-is-azure-route-server-preview"></a>Co to jest usługa Azure Route Server (wersja zapoznawcza)? 

Serwer tras platformy Azure upraszcza Routing dynamiczny między urządzeniem wirtualnym sieci (urządzenie WUS) i siecią wirtualną. Umożliwia ona wymianę informacji o routingu bezpośrednio za pomocą protokołu routingu Border Gateway Protocol (BGP) między dowolnymi urządzenie WUS, które obsługują protokół routingu BGP i sieć (SDN) w usłudze Azure Virtual Network (VNET) bez konieczności ręcznego konfigurowania lub konserwowania tabel tras. Serwer usługi Azure Route jest w pełni zarządzaną usługą i jest skonfigurowany z wysoką dostępnością.

> [!IMPORTANT]
> Usługa Azure Route Server (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-it-work"></a>Jak to działa?

Na poniższym diagramie przedstawiono sposób działania usługi Azure Route Server z SDWAN urządzenie WUS i zabezpieczeniami urządzenie WUS w sieci wirtualnej. Po ustanowieniu komunikacji równorzędnej BGP usługa Azure Route Server otrzyma trasę lokalną (10.250.0.0/16) z urządzenia SDWAN i trasy domyślnej (0.0.0.0/0) ze zapory. Trasy te są następnie automatycznie konfigurowane na maszynach wirtualnych w sieci wirtualnej. W efekcie cały ruch kierowany do sieci lokalnej zostanie wysłany do urządzenia SDWAN. Cały ruch związany z Internetem zostanie wysłany do zapory. W przeciwnym kierunku serwer tras platformy Azure wyśle adres sieci wirtualnej (10.1.0.0/16) do obu urządzeń WUS. Urządzenie SDWAN może propagować je do sieci lokalnej.

![Diagram przedstawiający serwer usługi Azure Route skonfigurowany w sieci wirtualnej.](./media/overview/route-server-overview.png)

## <a name="key-benefits"></a>Najważniejsze korzyści 

Serwer usługi Azure Route upraszcza konfigurację, zarządzanie i wdrażanie urządzenie WUS w sieci wirtualnej.  

* Nie trzeba już ręcznie aktualizować tabeli routingu na urządzenie WUS za każdym razem, gdy adresy sieci wirtualnej zostaną zaktualizowane. 

* Nie musisz już ręcznie aktualizować [tras zdefiniowanych przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) , gdy urządzenie WUS ogłasza nowe trasy lub Wycofaj stare. 

* Można połączyć wiele elementów równorzędnych urządzenie WUS z serwerem tras platformy Azure. Można skonfigurować atrybuty protokołu BGP w urządzenie WUS i, w zależności od projektu (np. aktywny-aktywny dla wydajności lub aktywny-pasywny dla odporności), pozwolić serwerowi usługi Azure Route na sprawdzenie, które wystąpienie urządzenie WUS jest aktywne lub które jest pasywne. 

* Interfejs między urządzenie WUS i serwerem usługi Azure Route jest oparty na standardowym protokole. Tak długo, jak urządzenie WUS obsługuje protokół BGP, można nawiązać połączenie równorzędne z serwerem tras platformy Azure. Aby uzyskać więcej informacji, zobacz [obsługiwane protokoły routingu na serwerze tras](route-server-faq.md#protocol).

* Serwer usługi Azure Route można wdrożyć w dowolnej nowej lub istniejącej sieci wirtualnej. 

## <a name="faq"></a>Często zadawane pytania

Często zadawane pytania dotyczące usługi Azure Route Server można znaleźć w temacie [często zadawane pytania dotyczące usługi Azure Route Server](route-server-faq.md).

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak skonfigurować usługę Azure Route Server](quickstart-configure-route-server-powershell.md)
- [Dowiedz się, jak usługa Azure Route Server współpracuje z usługą Azure ExpressRoute i siecią VPN platformy Azure](expressroute-vpn-support.md)
