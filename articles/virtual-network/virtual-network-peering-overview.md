---
title: Komunikacja równorzędna sieci wirtualnych
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o komunikacji równorzędnej sieci wirtualnych na platformie Azure, w tym o sposobie łączenia sieci w usłudze Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: feea2d54edd8a93e6e0effbef03389ef895d5ffb
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216754"
---
# <a name="virtual-network-peering"></a>Komunikacja równorzędna sieci wirtualnych

Wirtualne sieci równorzędne umożliwiają bezproblemowe łączenie dwóch lub większej liczby [sieci wirtualnych](virtual-networks-overview.md) na platformie Azure. Sieci wirtualne są wyświetlane jako jedna na potrzeby łączności. Ruch między maszynami wirtualnymi w wirtualnych sieciach równorzędnych używa infrastruktury sieci szkieletowej firmy Microsoft. Podobnie jak ruch między maszynami wirtualnymi w tej samej sieci, ruch jest kierowany przez sieć *prywatną* firmy Microsoft.

Platforma Azure obsługuje następujące typy komunikacji równorzędnej:

* Komunikacja równorzędna sieci wirtualnych: łączenie sieci wirtualnych w tym samym regionie świadczenia usługi Azure.
* Globalna komunikacja równorzędna sieci wirtualnych: łączenie sieci wirtualnych w różnych regionach platformy Azure.

Korzystanie z wirtualnych sieci równorzędnych, lokalnych lub globalnych, zapewnia m.in. następujące korzyści:

* Połączenie o małych opóźnieniach i dużej przepustowości między zasobami w różnych sieciach wirtualnych.
* Możliwość komunikowania się zasobów w jednej sieci wirtualnej z zasobami w innej sieci wirtualnej.
* Możliwość transferu danych między sieciami wirtualnymi w ramach subskrypcji platformy Azure, Azure Active Directory dzierżawców, modeli wdrażania i regionów świadczenia usługi Azure.
* Możliwość komunikacji równorzędnej sieci wirtualnych utworzonych za pomocą Azure Resource Manager.
* Możliwość komunikacji równorzędnej z siecią wirtualną utworzoną za pomocą Menedżer zasobów do jednej utworzonej za pomocą klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Understand Azure deployment models](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Informacje na temat modeli wdrażania platformy Azure).
* Brak przestojów zasobów w sieci wirtualnej podczas tworzenia komunikacji równorzędnej albo po utworzeniu komunikacji równorzędnej.

Ruch sieciowy między wirtualnymi sieciami równorzędnymi jest prywatny. Ruch między sieciami wirtualnymi jest utrzymywany w sieci szkieletowej firmy Microsoft. Do komunikacji między sieciami wirtualnymi nie jest wymagany publiczny Internet, bramy ani szyfrowanie.

## <a name="connectivity"></a>Łączność

W przypadku sieci wirtualnych za pomocą komunikacji równorzędnej zasoby w jednej sieci wirtualnej mogą łączyć się bezpośrednio z zasobami w równorzędnej sieci wirtualnej.

Opóźnienie sieciowe między maszynami wirtualnymi w równorzędnych sieciach wirtualnych w tym samym regionie jest takie samo jak opóźnienie w pojedynczej sieci wirtualnej. Przepływność sieci zależy od przepustowości dozwolonej dla maszyny wirtualnej proporcjonalnie do jej rozmiaru. Nie ma żadnych dodatkowych ograniczeń przepustowości w obrębie komunikacji równorzędnej.

Ruch między maszynami wirtualnymi w równorzędnych sieciach wirtualnych odbywa się bezpośrednio za pomocą infrastruktury sieci szkieletowej firmy Microsoft, a nie przy użyciu bramy lub publicznego Internetu.

Aby zablokować dostęp do innych sieci wirtualnych lub podsieci, można zastosować sieciowe grupy zabezpieczeń w jednej sieci wirtualnej.
Podczas konfigurowania komunikacji równorzędnej sieci wirtualnej należy otworzyć lub zamknąć reguły sieciowej grupy zabezpieczeń między sieciami wirtualnymi. W przypadku otwarcia pełnej łączności między równorzędnymi sieciami wirtualnymi można zastosować sieciowe grupy zabezpieczeń, aby zablokować lub odmówić określonego dostępu. Pełna łączność jest opcją domyślną. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [grupy zabezpieczeń](./network-security-groups-overview.md).

## <a name="service-chaining"></a>Tworzenie łańcuchów usług

Łańcuchy usług umożliwiają kierowanie ruchu z jednej sieci wirtualnej do urządzenia wirtualnego lub bramy w sieci równorzędnej za pośrednictwem tras zdefiniowanych przez użytkownika.

Aby włączyć tworzenie łańcucha usług, należy skonfigurować trasy zdefiniowane przez użytkownika, które wskazują na maszyny wirtualne w sieciach wirtualnych równorzędnych jako adres IP *następnego przeskoku* . Trasy zdefiniowane przez użytkownika mogą także wskazywać bramy sieci wirtualnej w celu włączenia łańcucha usług.

Można wdrożyć sieci gwiazdy, w których Centrum sieci wirtualnej hostuje składniki infrastruktury, takie jak sieciowe urządzenie wirtualne lub Brama sieci VPN. Następnie wszystkie sieci wirtualne typu gwiazda można połączyć za pomocą komunikacji równorzędnej z centralną siecią wirtualną. Ruch przepływów przez wirtualne urządzenia sieciowe lub bramy sieci VPN w sieci wirtualnej centrum.

Wirtualne sieci równorzędne umożliwiają użycie kolejnego przeskoku w trasie zdefiniowanej przez użytkownika jako adresu IP maszyny wirtualnej w wirtualnej sieci równorzędnej lub bramie sieci VPN. Nie można kierować między sieciami wirtualnymi za pomocą trasy zdefiniowanej przez użytkownika, która określa bramę usługi Azure ExpressRoute jako typ następnego przeskoku. Aby dowiedzieć się więcej o trasach definiowanych przez użytkownika, zobacz [User-defined routes overview](virtual-networks-udr-overview.md#user-defined) (Omówienie tras definiowanych przez użytkownika). Aby dowiedzieć się, jak utworzyć topologię sieci piasty i szprych, zobacz [topologia sieci gwiazdy na platformie Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Bramy i łączność lokalna

Każda sieć wirtualna, w tym równorzędna Sieć wirtualna, może mieć własną bramę. Sieć wirtualna może używać jej do łączenia się z siecią lokalną. Możesz również skonfigurować [połączenia sieci wirtualnej z](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) siecią wirtualną przy użyciu bram, nawet w przypadku wirtualnych sieci równorzędnych.

W przypadku skonfigurowania obu opcji wzajemnej łączności między sieciami wirtualnymi ruch między sieciami wirtualnymi odbywa się za pośrednictwem konfiguracji komunikacji równorzędnej. Ruch używa sieci szkieletowej platformy Azure.

Możesz również skonfigurować bramę w wirtualnej sieci równorzędnej jako punkt tranzytowy do sieci lokalnej. W takim przypadku Sieć wirtualna korzystająca z bramy zdalnej nie może mieć własnej bramy. Sieć wirtualna ma tylko jedną bramę. Brama jest lokalną lub zdalną bramą w równorzędnej sieci wirtualnej, jak pokazano na poniższym diagramie:

![przesyłanie w równorzędnych sieciach wirtualnych](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Zarówno Komunikacja równorzędna sieci wirtualnych, jak i globalne wirtualne sieci równorzędne obsługują tranzyt bramy.

Obsługiwane są Tranzyty bramy między sieciami wirtualnymi utworzonymi za poorednictwem różnych modeli wdrażania. Brama musi znajdować się w sieci wirtualnej w modelu Menedżer zasobów. Aby dowiedzieć się więcej na temat przesyłania danych za pomocą bramy, zobacz [Konfigurowanie bramy sieci VPN na potrzeby przesyłania danych w komunikacji równorzędnej sieci wirtualnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

W przypadku równorzędnych sieci wirtualnych, które współużytkują pojedyncze połączenie usługi Azure ExpressRoute, ruch między nimi odbywa się przez relację komunikacji równorzędnej. Ten ruch używa sieci szkieletowej platformy Azure. Użytkownicy mogą nadal korzystać z bram lokalnych w poszczególnych sieciach wirtualnych, aby łączyć się z obwodem lokalnym. W przeciwnym razie możesz użyć udostępnionej bramy i skonfigurować tranzyt dla łączności lokalnej.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby upewnić się, że sieci wirtualne są połączone za pomocą komunikacji równorzędnej, możesz sprawdzić obowiązujące trasy. Sprawdź trasy dla interfejsu sieciowego w dowolnej podsieci w sieci wirtualnej. Jeśli istnieje komunikacja równorzędna w sieci wirtualnej, wszystkie podsieci w tej sieci wirtualnej mają trasy z typem następnego przeskoku *Komunikacja równorzędna sieci wirtualnych* dla każdej przestrzeni adresowej w każdej równorzędnej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [diagnozowanie problemu z routingiem maszyny wirtualnej](diagnose-network-routing-problem.md).

Możesz również rozwiązywać problemy z łącznością z maszyną wirtualną w równorzędnej sieci wirtualnej przy użyciu usługi Azure Network Watcher. Kontrola łączności pozwala zobaczyć, jak ruch jest kierowany z interfejsu sieciowego źródłowej maszyny wirtualnej do interfejsu sieciowego docelowej maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z usługą Azure Network Watcher przy użyciu Azure Portal](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Możesz również spróbować [rozwiązywać problemy z komunikacją równorzędną sieci wirtualnej](virtual-network-troubleshoot-peering-issues.md).

## <a name="constraints-for-peered-virtual-networks"></a>Ograniczenia dotyczące równorzędnych sieci wirtualnych<a name="requirements-and-constraints"></a>

Następujące ograniczenia mają zastosowanie tylko wtedy, gdy sieci wirtualne są globalnie połączone za pomocą sieci równorzędnych:

* Zasoby w jednej sieci wirtualnej nie mogą komunikować się z adresem IP frontonu podstawowego wewnętrznego Load Balancer (ILB) w wirtualnej sieci równorzędnej.
* Niektóre usługi korzystające z podstawowego modułu równoważenia obciążenia nie działają w przypadku globalnej komunikacji równorzędnej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [jakie są ograniczenia dotyczące globalnej komunikacji równorzędnej sieci wirtualnej i modułów równoważenia obciążenia?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

Aby uzyskać więcej informacji, zobacz [wymagania i ograniczenia](virtual-network-manage-peering.md#requirements-and-constraints). Aby dowiedzieć się więcej o obsługiwanej liczbie komunikacji równorzędnej, zobacz [limity dotyczące sieci](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Uprawnienia

Aby dowiedzieć się więcej o uprawnieniach wymaganych do utworzenia komunikacji równorzędnej sieci wirtualnej, zobacz [uprawnienia](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Cennik

Jest naliczona nominalna opłata za ruch przychodzący i ruchu wyjściowego, który używa połączenia komunikacji równorzędnej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Cennik usługi Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network).

Tranzyt bramy to właściwość komunikacji równorzędnej, która umożliwia sieci wirtualnej korzystanie z bramy sieci VPN/ExpressRoute w równorzędnej sieci wirtualnej. Tranzyt bramy działa zarówno w przypadku połączeń między sieciami lokalnymi, jak i między sieciami. Ruch skierowany do bramy (ruchu przychodzącego lub wychodzącego) w równorzędnej sieci wirtualnej wiąże się z naliczeniem połączeń równorzędnych sieci wirtualnych za pomocą połączenia wirtualnej szprychy (lub sieci wirtualnej bez bramy). Aby uzyskać więcej informacji, zobacz [VPN Gateway cennika](https://azure.microsoft.com/pricing/details/vpn-gateway/) dla opłat za bramę sieci VPN i cennika usługi ExpressRoute Gateway na potrzeby opłat za bramę ExpressRoute.

>[!NOTE]
> Poprzednia wersja tego dokumentu stwierdziła, że opłaty za komunikację równorzędną w sieci wirtualnej nie będą stosowane w połączeniu z siecią wirtualną szprychy (ani z siecią wirtualną bez bramy) z tranzytem bramy. Teraz odzwierciedla dokładne ceny na stronie z cennikiem.

## <a name="next-steps"></a>Następne kroki

* Można utworzyć komunikację równorzędną między dwiema sieciami wirtualnymi. Sieci mogą należeć do tej samej subskrypcji, różnych modeli wdrażania w tej samej subskrypcji lub w różnych subskrypcjach. Ukończ samouczek dla jednego z następujących scenariuszy:

    |Model wdrażania platformy Azure             | Subskrypcja  |
    |---------                          |---------|
    |Resource Manager — w obu przypadkach              |[Ta sama](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Różne](create-peering-different-subscriptions.md)|
    |Jedna sieć — Resource Manager, druga — model klasyczny  |[Ta sama](create-peering-different-deployment-models.md)|
    |                                   |[Różne](create-peering-different-deployment-models-subscriptions.md)|

* Aby dowiedzieć się, jak utworzyć topologię sieci piasty i szprych, zobacz [topologia sieci gwiazdy na platformie Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Aby dowiedzieć się więcej na temat wszystkich ustawień komunikacji równorzędnej sieci wirtualnej, zobacz [Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej](virtual-network-manage-peering.md).
* Aby uzyskać odpowiedzi na często zadawane pytania dotyczące komunikacji równorzędnej sieci wirtualnej i sieci wirtualnych za pomocą komunikacji równorzędnej, zobacz wirtualne połączenia [równorzędne](virtual-networks-faq.md#vnet-peering).