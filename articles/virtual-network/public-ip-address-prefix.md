---
title: Prefiks publicznego adresu IP platformy Azure
description: Dowiedz się więcej o tym, czym jest prefiks publicznego adresu IP platformy Azure i jak może on ułatwić przypisywanie przewidywalnych publicznych adresów IP do zasobów.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: allensu
ms.openlocfilehash: 0f71f845ef3209146ead79cafae2f3aa5c8c6d7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144519"
---
# <a name="public-ip-address-prefix"></a>Prefiks publicznego adresu IP

Prefiks publicznego adresu IP jest zarezerwowanym zakresem adresów IP dla publicznych punktów końcowych na platformie Azure. Platforma Azure przydziela ciągły zakres adresów do subskrypcji w zależności od tego, ile określisz. Jeśli nie znasz adresów publicznych, zobacz [publiczne adresy IP.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Publiczne adresy IP są przypisywane z puli adresów w każdym regionie świadczenia usługi Azure. Listę zakresów używanych przez platformę Azure można [pobrać](https://www.microsoft.com/download/details.aspx?id=56519) dla każdego regionu. Na przykład 40.121.0.0/16 jest jednym z ponad 100 zakresów platformy Azure używanych w regionie Wschodnie stany USA. Zakres obejmuje użyteczne adresy 40.121.0.1-40.121.255.254.

Prefiks publicznego adresu IP można utworzyć w regionie i subskrypcji platformy Azure, określając nazwę i liczbę adresów, które mają być uwzględnione w prefiksie. Na przykład jeśli utworzysz prefiks publicznego adresu IP/28, platforma Azure przydziela 16 adresów z jednego z jego zakresów. Nie wiesz, który zakres platformy Azure zostanie przypisany do momentu utworzenia zakresu, ale adresy są ciągłe. Prefiksy publicznych adresów IP mają opłatę. Aby uzyskać szczegółowe informacje, zobacz [Cennik publicznego adresu IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Dlaczego warto utworzyć prefiks publicznego adresu IP?

W przypadku tworzenia zasobów publicznego adresu IP platforma Azure przypisuje dostępny publiczny adres IP z dowolnego zakresu używanego w regionie. Gdy platforma Azure przypisze adres, wiadomo, jaki jest adres, ale do momentu przypisywania adresu przez platformę Azure nie jest znany adres, który można przypisać. Może to być problematyczne, jeśli na przykład ty lub partnerzy biznesowi skonfiguruje reguły zapory zezwalające na określone adresy IP. Za każdym razem, gdy przypiszesz nowy publiczny adres IP do zasobu, do reguły zapory należy dodać adres. Po przypisaniu adresów do zasobów z prefiksu publicznego adresu IP reguły zapory nie muszą być aktualizowane przy każdym przypisaniu jednego z tych adresów, ponieważ cały zakres można dodać do reguły.

## <a name="benefits"></a>Korzyści

- Można tworzyć zasoby publicznych adresów IP z znanego zakresu.
- Ty lub partnerzy biznesowi mogą tworzyć reguły zapory z zakresami zawierającymi publicznie przypisane adresy IP, a także adresy, które nie zostały jeszcze przypisane. Eliminuje to konieczność zmiany reguł zapory w miarę przypisywania adresów IP do nowych zasobów.
- Domyślny rozmiar zakresu, który można utworzyć, to/28 lub 16 adresów IP.
- Nie ma ograniczeń dotyczących liczby możliwych do utworzenia zakresów, ale istnieją ograniczenia dotyczące maksymalnej liczby statycznych publicznych adresów IP, które mogą znajdować się w ramach subskrypcji platformy Azure. W związku z tym liczba utworzonych zakresów nie może obejmować bardziej statycznych publicznych adresów IP, niż jest to możliwe w subskrypcji. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Adresy tworzone przy użyciu adresów z prefiksu można przypisać do dowolnego zasobu platformy Azure, do którego można przypisać publiczny adres IP.
- Można łatwo sprawdzić, które adresy IP są przyłączone i nie są jeszcze przyliczane w ramach zakresu.

## <a name="scenarios"></a>Scenariusze
Można skojarzyć następujące zasoby ze statycznym publicznym adresem IP z prefiksu:

|Zasób|Scenariusz|Kroki|
|---|---|---|
|Maszyny wirtualne| Skojarzenie publicznych adresów IP z prefiksu do maszyn wirtualnych na platformie Azure zmniejsza obciążenie związane z zarządzaniem, gdy chodzi o listy dozwolonych adresów IP w zaporze. Można po prostu dozwolonych cały prefiks z pojedynczą regułą zapory. W miarę skalowania maszyn wirtualnych na platformie Azure można kojarzyć adresy IP z tego samego prefiksu oszczędności, czasu i kosztów zarządzania.| Aby skojarzyć adresy IP z prefiksem do maszyny wirtualnej: 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. [Skojarz adres IP z interfejsem sieciowym maszyny wirtualnej.](virtual-network-network-interface-addresses.md#add-ip-addresses) Można także [skojarzyć adresy IP z zestawem skalowania maszyn wirtualnych](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Usługi równoważenia obciążenia w warstwie Standardowa | Skojarzenie publicznych adresów IP od prefiksu do konfiguracji adresu IP frontonu lub reguły wychodzącej Load Balancer zapewnia uproszczenie publicznej przestrzeni adresów IP platformy Azure. Możesz uprościć ten scenariusz, przechodząc do pielęgnacji połączeń wychodzących, które pochodzą z zakresu ciągłego adresów IP zdefiniowanych przez prefiks publicznego adresu IP. | Aby skojarzyć adresy IP od prefiksu do modułu równoważenia obciążenia: 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. Podczas tworzenia Load Balancer wybierz lub zaktualizuj adres IP utworzony w kroku 2 powyżej jako adres IP frontonu Load Balancer. |
| Azure Firewall | Możesz użyć publicznego adresu IP z prefiksu dla wychodzącego elementu Republic. Oznacza to, że cały wychodzący ruch sieciowy w sieci wirtualnej jest tłumaczony na publiczny adres IP [zapory platformy Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Ponieważ ten adres IP pochodzi ze wstępnie wskazanego prefiksu, bardzo łatwo jest wiedzieć, jak często publiczny adres IP na platformie Azure będzie wyglądać następująco. | 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. Podczas [wdrażania zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)należy wybrać adres IP, który został wcześniej przydzielony z prefiksu.|
| Application Gateway v2 | Możesz użyć publicznego adresu IP z prefiksu do automatycznego skalowania i strefowo nadmiarowej bramy aplikacji w wersji 2. Ponieważ ten adres IP pochodzi ze wstępnie wskazanego prefiksu, bardzo łatwo jest wiedzieć, jak często publiczny adres IP na platformie Azure będzie wyglądać następująco. | 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) 3. Podczas [wdrażania Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway)upewnij się, że wybrano adres IP, który został wcześniej przydzielony z prefiksu.|

## <a name="constraints"></a>Ograniczenia

- Nie można określić adresów IP dla prefiksu. Platforma Azure przydziela adresy IP dla prefiksu na podstawie określonego rozmiaru.
- Można domyślnie utworzyć prefiks do 16 adresów IP lub/28. Przejrzyj [limity dotyczące sieci](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) , aby uzyskać więcej informacji, Zwiększ liczbę żądań i [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .
- Nie można zmienić zakresu, po utworzeniu prefiksu.
- Z zakresu prefiksu można przypisać tylko statyczne publiczne adresy IP utworzone przy użyciu standardowej jednostki SKU. Aby dowiedzieć się więcej o jednostkach SKU publicznych adresów IP, zobacz [publiczny adres IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adresy z zakresu można przypisywać tylko do zasobów Azure Resource Manager. Nie można przypisywać adresów do zasobów w klasycznym modelu wdrażania.
- Wszystkie publiczne adresy IP utworzone na podstawie prefiksu muszą istnieć w tym samym regionie i subskrypcji platformy Azure co prefiks i muszą być przypisane do zasobów w tym samym regionie i w ramach subskrypcji.
- Nie można usunąć prefiksu, jeśli wszystkie znajdujące się w nim adresy są przypisane do zasobów publicznych adresów IP skojarzonych z zasobem. Usuń skojarzenie wszystkich zasobów publicznego adresu IP, które mają najpierw przypisane adresy IP od prefiksu.


## <a name="next-steps"></a>Następne kroki

- [Utwórz](manage-public-ip-address-prefix.md) prefiks publicznego adresu IP
