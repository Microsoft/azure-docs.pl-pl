---
title: Rozwiązanie VMware firmy Azure według CloudSimple — wybierz rozwiązanie do równoważenia obciążenia dla chmur prywatnych CloudSimple
description: Opisuje opcje równoważenia obciążenia wdrażanie aplikacji w chmurze prywatnej
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 06d2305c209011e4fb1b8ee05a016d2e15a49833
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898032"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Wybierz rozwiązanie do równoważenia obciążenia dla chmur prywatnych CloudSimple

Podczas wdrażania aplikacji w chmurze prywatnej CloudSimple można wybrać dowolną z kilku opcji równoważenia obciążenia.

Możesz wybrać wirtualny lub oparty na oprogramowaniu moduł równoważenia obciążenia w chmurze prywatnej usługi CloudSimple, a nawet użyć modułu równoważenia obciążenia (P7) platformy Azure działającego w ramach subskrypcji platformy Azure, aby frontonować maszyny wirtualne warstwy sieci Web działające w chmurze prywatnej CloudSimple. Oto kilka opcji:

## <a name="virtual-load-balancers"></a>Wirtualne moduły równoważenia obciążenia

Wirtualne urządzenia usługi równoważenia obciążenia można wdrożyć w środowisku VMware za pomocą interfejsu vCenter i skonfigurować je do frontonu ruchu aplikacji.

Niektórzy popularni dostawcy to: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Moduł równoważenia obciążenia (P7) platformy Azure

W przypadku korzystania z usługi Azure Application Gateway jako modułu równoważenia obciążenia P7 dla aplikacji działającej w chmurze prywatnej nie trzeba zarządzać oprogramowaniem usługi równoważenia obciążenia. Oprogramowanie usługi równoważenia obciążenia jest zarządzane przez platformę Azure. Wszystkie maszyny wirtualne warstwy sieci Web w chmurze prywatnej używają prywatnych adresów IP i nie wymagają dodatkowych reguł translatora adresów sieciowych ani publicznych adresów IP do rozpoznawania nazw. Maszyny wirtualne warstwy sieci Web komunikują się z Application Gateway platformy Azure za pośrednictwem połączenia prywatnego, o małym opóźnieniu i dużej przepustowości.

Aby dowiedzieć się więcej o konfigurowaniu tego rozwiązania, zapoznaj się z przewodnikiem po rozwiązaniu dotyczącym korzystania z usługi Azure Application Gateway jako modułu równoważenia obciążenia P7.

## <a name="azure-internal-load-balancer"></a>Wewnętrzny moduł równoważenia obciążenia platformy Azure

Jeśli zdecydujesz się uruchomić aplikację w ramach wdrożenia hybrydowego, w którym warstwa frontonu internetowego jest uruchomiona w sieci wirtualnej platformy Azure w ramach subskrypcji platformy Azure, a warstwa bazy danych aplikacji jest uruchomiona na maszynach wirtualnych programu VMware w chmurze prywatnej CloudSimple, możesz użyć wewnętrznego modułu równoważenia obciążenia platformy Azure przed maszynami wirtualnymi warstwy bazy danych na potrzeby zarządzania ruchem.

Aby dowiedzieć się więcej, zobacz Dokumentacja [wewnętrzna Load Balancer](../load-balancer/components.md#frontend-ip-configurations) platformy Azure.

## <a name="global-server-load-balancer"></a>Moduł równoważenia obciążenia serwera globalnego

Jeśli szukasz modułu równoważenia obciążenia opartego na systemie DNS, możesz skorzystać z rozwiązań innych firm dostępnych w witrynie Azure Marketplace lub przejść na natywne rozwiązanie platformy Azure.

Azure Traffic Manager to oparty na systemie DNS moduł równoważenia obciążenia, który umożliwia optymalne dystrybuowanie ruchu do usług w globalnych regionach platformy Azure i lokalnych, zapewniając wysoką dostępność i czas odpowiedzi. Aby dowiedzieć się więcej, zobacz dokumentację usługi Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) .
