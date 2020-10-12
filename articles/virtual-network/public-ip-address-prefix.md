---
title: Prefiks publicznego adresu IP platformy Azure
description: Dowiedz się więcej o tym, czym jest prefiks publicznego adresu IP platformy Azure i jak może on ułatwić przypisywanie przewidywalnych publicznych adresów IP do zasobów.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 53dd6d2dda762b3cbf53f4aaec6cd3692a9656e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87432577"
---
# <a name="public-ip-address-prefix"></a>Prefiks publicznego adresu IP

Prefiks publicznego adresu IP jest zarezerwowanym zakresem adresów IP na platformie Azure. Platforma Azure zapewnia ciągły zakres adresów dla Twojej subskrypcji w zależności od tego, ile określisz. 

Jeśli nie znasz adresów publicznych, zobacz [publiczne adresy IP.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Publiczne adresy IP są przypisywane z puli adresów w każdym regionie świadczenia usługi Azure. Listę zakresów używanych przez platformę Azure można [pobrać](https://www.microsoft.com/download/details.aspx?id=56519) dla każdego regionu. Na przykład 40.121.0.0/16 jest jednym z ponad 100 zakresów platformy Azure używanych w regionie Wschodnie stany USA. Zakres obejmuje użyteczne adresy 40.121.0.1-40.121.255.254.

Prefiks publicznego adresu IP można utworzyć w regionie i subskrypcji platformy Azure, określając nazwę i liczbę adresów, które mają być uwzględnione w prefiksie. 

Zakresy publicznych adresów IP są przypisywane z prefiksem wyboru. Jeśli utworzysz prefiks/28, platforma Azure przyznaje 16 adresów IP z jednego z jego zakresów.

Nie wiesz, który zakres platformy Azure zostanie przypisany do momentu utworzenia zakresu, ale adresy są ciągłe. 

Dla prefiksów publicznych adresów IP jest dostępna opłata, aby uzyskać więcej informacji, zobacz [Cennik publicznego adresu IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Dlaczego warto utworzyć prefiks publicznego adresu IP?

W przypadku tworzenia zasobów publicznego adresu IP platforma Azure przypisuje dostępny publiczny adres IP z dowolnego zakresu używanego w tym regionie. 

Do momentu przydzielenia adresu IP przez platformę Azure nie jest znany dokładny adres IP. Ten proces może być przyczyną problemów podczas tworzenia reguł zapory zezwalających na określone adresy IP. Dla każdego dodanego adresu IP należy dodać odpowiednią regułę zapory.

Po przypisaniu adresów do zasobów z prefiksu publicznego adresu IP aktualizacje reguły zapory nie są wymagane. Do reguły zostanie dodany cały zakres.

## <a name="benefits"></a>Korzyści

- Tworzenie zasobów publicznych adresów IP z znanego zakresu.
- Konfiguracja reguły zapory z zakresami, które zawierają publiczne adresy IP, które są obecnie przypisane, i adresów, do których jeszcze nie przypisano. Ta konfiguracja eliminuje konieczność zmiany reguł zapory w miarę przypisywania adresów IP do nowych zasobów.
- Domyślny rozmiar zakresu, który można utworzyć, to/28 lub 16 adresów IP.
- Nie ma ograniczeń dotyczących liczby możliwych do utworzenia zakresów. Istnieją limity maksymalnej liczby statycznych publicznych adresów IP, które mogą znajdować się w ramach subskrypcji platformy Azure. Liczba utworzonych zakresów nie może obejmować bardziej statycznych publicznych adresów IP, niż jest to możliwe w subskrypcji. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Adresy tworzone przy użyciu adresów z prefiksu można przypisać do dowolnego zasobu platformy Azure, do którego można przypisać publiczny adres IP.
- Można łatwo sprawdzić, które adresy IP są określone i nie są określone w zakresie.

## <a name="scenarios"></a>Scenariusze
Można skojarzyć następujące zasoby ze statycznym publicznym adresem IP z prefiksu:

|Zasób|Scenariusz|Kroki|
|---|---|---|
|Maszyny wirtualne| Skojarzenie publicznych adresów IP z prefiksu do maszyn wirtualnych na platformie Azure zmniejsza obciążenie związane z zarządzaniem w przypadku dodawania adresów IP do listy dozwolonych w zaporze. Można dodać cały prefiks z pojedynczą regułą zapory. W miarę skalowania maszyn wirtualnych na platformie Azure można kojarzyć adresy IP z tego samego prefiksu oszczędności, czasu i kosztów zarządzania.| Aby skojarzyć adresy IP z prefiksem do maszyny wirtualnej: </br> 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) </br> 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) </br> 3. [Skojarz adres IP z interfejsem sieciowym maszyny wirtualnej.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> Można także [skojarzyć adresy IP z zestawem skalowania maszyn wirtualnych](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Usługi równoważenia obciążenia w warstwie Standardowa | Skojarzenie publicznych adresów IP z prefiksu do konfiguracji adresu IP frontonu lub reguły ruchu wychodzącego modułu równoważenia obciążenia zapewnia uproszczenie publicznej przestrzeni adresów IP platformy Azure. Uprość ten scenariusz dzięki pielęgnacji połączeń wychodzących z zakresu sąsiadujących adresów IP. | Aby skojarzyć adresy IP z prefiksem do modułu równoważenia obciążenia: </br> 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) </br> 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) </br> 3. podczas tworzenia modułu równoważenia obciążenia wybierz lub zaktualizuj adres IP utworzony w kroku 2 powyżej jako adres IP frontonu modułu równoważenia obciążenia. |
| Azure Firewall | Możesz użyć publicznego adresu IP z prefiksu dla wychodzącego elementu Republic. Cały ruch wychodzący sieci wirtualnej jest tłumaczony na publiczny adres IP [zapory platformy Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . | Aby skojarzyć adres IP od prefiksu do zapory: </br> 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) </br> 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) </br> 3. podczas [wdrażania zapory platformy Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)należy wybrać adres IP, który został wcześniej przekazany z prefiksu.|
| Application Gateway (wersja 2) | Możesz użyć publicznego adresu IP z prefiksu do automatycznego skalowania i strefowo nadmiarowej bramy aplikacji w wersji 2. | Aby skojarzyć adres IP od prefiksu do bramy: </br> 1. [Utwórz prefiks.](manage-public-ip-address-prefix.md) </br> 2. [Utwórz adres IP z prefiksu.](manage-public-ip-address-prefix.md) </br> 3. podczas [wdrażania Application Gateway](../application-gateway/quick-create-portal.md#create-an-application-gateway)upewnij się, że wybrano adres IP, który został wcześniej przekazany z prefiksu.|

## <a name="constraints"></a>Ograniczenia

- Nie można określić adresów IP dla prefiksu. Platforma Azure udostępnia adresy IP dla prefiksu na podstawie określonego rozmiaru.
- Można domyślnie utworzyć prefiks do 16 adresów IP lub/28. Przejrzyj [limity dotyczące sieci](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) , aby uzyskać więcej informacji, Zwiększ liczbę żądań i [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .
- Nie można zmienić zakresu, po utworzeniu prefiksu.
- Z zakresu prefiksu można przypisać tylko statyczne publiczne adresy IP utworzone przy użyciu standardowej jednostki SKU. Aby dowiedzieć się więcej o jednostkach SKU publicznych adresów IP, zobacz [publiczny adres IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Adresy z zakresu można przypisywać tylko do zasobów Azure Resource Manager. Nie można przypisywać adresów do zasobów w klasycznym modelu wdrażania.
- Wszystkie publiczne adresy IP utworzone na podstawie prefiksu muszą istnieć w tym samym regionie i subskrypcji platformy Azure co prefiks. Adresy muszą być przypisane do zasobów w tym samym regionie i w ramach subskrypcji.
- Nie można usunąć prefiksu, jeśli wszystkie znajdujące się w nim adresy są przypisane do zasobów publicznych adresów IP skojarzonych z zasobem. Usuń skojarzenie wszystkich zasobów publicznego adresu IP, które mają najpierw przypisane adresy IP od prefiksu.


## <a name="next-steps"></a>Następne kroki

- [Utwórz](manage-public-ip-address-prefix.md) prefiks publicznego adresu IP
