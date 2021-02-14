---
title: Rozwiązywanie problemów dotyczących komunikacji równorzędnej sieci wirtualnych
description: Kroki pomagające w rozwiązaniu większości problemów z siecią wirtualną za pomocą komunikacji równorzędnej.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: f3e1ea8861646490875667f6238b3f8813bd6c4b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518168"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Rozwiązywanie problemów dotyczących komunikacji równorzędnej sieci wirtualnych

Ten przewodnik rozwiązywania problemów zawiera kroki ułatwiające rozwiązywanie typowych problemów z [siecią równorzędną sieci wirtualnej](virtual-network-peering-overview.md) .

![Diagram komunikacji równorzędnej sieci wirtualnych](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Konfigurowanie komunikacji równorzędnej sieci wirtualnej między dwiema sieciami wirtualnymi

Czy sieci wirtualne znajdują się w tej samej subskrypcji lub w różnych subskrypcjach?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Sieci wirtualne znajdują się w tej samej subskrypcji

Aby skonfigurować komunikację równorzędną sieci wirtualnych dla sieci wirtualnych należących do tej samej subskrypcji, użyj metod w następujących artykułach:

* Jeśli sieci wirtualne znajdują się w *tym samym regionie*, zobacz [Tworzenie komunikacji równorzędnej](./virtual-network-manage-peering.md#create-a-peering).
* Jeśli sieci wirtualne znajdują się w *różnych regionach*, zobacz [wirtualne sieci równorzędne](./virtual-network-peering-overview.md). 

> [!Note]
> Łączność między globalnymi sieciami wirtualnymi nie działa w przypadku następujących zasobów: 
>
> * Maszyny wirtualne (VM) za podstawową jednostką SKU wewnętrznego modułu równoważenia obciążenia (ILB)
> * Pamięć podręczna Redis (używa podstawowej jednostki SKU ILB)
> * Application Gateway (używa podstawowej jednostki SKU ILB)
> * Zestawy skalowania maszyn wirtualnych (używa podstawowej jednostki SKU ILB)
> * Klastry usługi Azure Service Fabric (używa podstawowej jednostki SKU ILB)
> * SQL Server zawsze włączone (używa podstawowej jednostki SKU ILB)
> * Azure App Service Environment dla usługi PowerApps (używa podstawowej jednostki SKU ILB)
> * Azure API Management (używa podstawowej jednostki SKU ILB)
> * Azure Active Directory Domain Services (Azure AD DS) (używa podstawowej jednostki SKU ILB)

Aby uzyskać więcej informacji, zobacz [wymagania i ograniczenia](./virtual-network-peering-overview.md#requirements-and-constraints) globalnej komunikacji równorzędnej.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Sieci wirtualne znajdują się w różnych subskrypcjach lub Active Directory dzierżawców

Aby skonfigurować wirtualne sieci równorzędne dla sieci wirtualnych w różnych subskrypcjach lub Active Directory dzierżawców, zobacz [Tworzenie komunikacji równorzędnej w różnych subskrypcjach interfejsu wiersza polecenia platformy Azure](./create-peering-different-subscriptions.md#cli).

> [!Note]
> Aby skonfigurować komunikację równorzędną sieci, musisz mieć uprawnienia **współautora sieci** w obu subskrypcjach. Aby uzyskać więcej informacji, zobacz [uprawnienia komunikacji równorzędnej](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Konfigurowanie komunikacji równorzędnej sieci wirtualnej z topologią gwiazdy, która korzysta z zasobów lokalnych

![Diagram komunikacji równorzędnej sieci wirtualnych z lokalną szprychą](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Dla połączenia typu lokacja-lokacja lub połączenia ExpressRoute

Wykonaj kroki opisane w temacie: [Konfigurowanie tranzytu bramy sieci VPN dla komunikacji równorzędnej sieci wirtualnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="for-point-to-site-connections"></a>Dla połączeń punkt-lokacja

1. Wykonaj kroki opisane w temacie: [Konfigurowanie tranzytu bramy sieci VPN dla komunikacji równorzędnej sieci wirtualnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Po nawiązaniu lub zmianie komunikacji równorzędnej sieci wirtualnej Pobierz i zainstaluj ponownie pakiet punkt-lokacja, aby klienci punkt-lokacja uzyskali zaktualizowane trasy do sieci wirtualnej szprych.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Konfigurowanie komunikacji równorzędnej sieci wirtualnej z siecią wirtualną topologii gwiazdy

![Diagram komunikacji równorzędnej sieci wirtualnej z szprychą sieci wirtualnej](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Sieci wirtualne znajdują się w tym samym regionie


1. W sieci wirtualnej centrum Skonfiguruj wirtualne urządzenie sieciowe (urządzenie WUS).
1. W sieciach wirtualnych szprychy są stosowane trasy zdefiniowane przez użytkownika z typem następnego przeskoku "urządzenie wirtualne sieci".

Aby uzyskać więcej informacji, zobacz Tworzenie [łańcucha usług](./virtual-network-peering-overview.md#service-chaining).

> [!Note]
> Jeśli potrzebujesz pomocy przy konfigurowaniu urządzenie WUS, [skontaktuj się z dostawcą urządzenie WUS](https://mskb.pkisolutions.com/kb/2984655).

Aby uzyskać pomoc dotyczącą rozwiązywania problemów z konfiguracją i routingiem urządzenia urządzenie WUS, zobacz [problemy dotyczące sieciowego urządzenia wirtualnego na platformie Azure](./virtual-network-troubleshoot-nva.md).

### <a name="the-virtual-networks-are-in-different-regions"></a>Sieci wirtualne znajdują się w różnych regionach

Przesyłanie za pośrednictwem globalnej komunikacji równorzędnej sieci wirtualnej jest teraz obsługiwane. Łączność nie działa w przypadku globalnej komunikacji równorzędnej sieci wirtualnej dla następujących zasobów:

* Maszyny wirtualne za jednostką SKU wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa
* Pamięć podręczna Redis (używa podstawowej jednostki SKU ILB)
* Application Gateway (używa podstawowej jednostki SKU ILB)
* Zestawy skalowania (używa podstawowej jednostki SKU ILB)
* Klastry usługi Service Fabric (używają jednostki SKU wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa)
* SQL Server zawsze włączone (używa podstawowej jednostki SKU ILB)
* App Service Environment (używa podstawowej jednostki SKU ILB)
* API Management (używa jednostki SKU wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa)
* Azure AD DS (używa podstawowej jednostki SKU ILB)

Aby dowiedzieć się więcej o globalnych wymaganiach dotyczących komunikacji równorzędnej i ograniczeniach, zobacz [wirtualne sieci równorzędne](./virtual-network-peering-overview.md#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Rozwiązywanie problemów z łącznością między dwiema równorzędnymi sieciami wirtualnymi

Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma wymagane [role i uprawnienia](virtual-network-manage-peering.md#permissions). Wybierz sieć wirtualną, wybierz pozycję **Komunikacja równorzędna**, a następnie sprawdź pole **stan** . Jaki jest stan?

### <a name="the-peering-status-is-connected"></a>Stan komunikacji równorzędnej to "Połączono"

Aby rozwiązać ten problem:

1. Sprawdź przepływy ruchu sieciowego:

   Użyj [rozwiązywania problemów z połączeniami](../network-watcher/network-watcher-connectivity-overview.md) i [weryfikacji przepływu IP](../network-watcher/network-watcher-ip-flow-verify-overview.md) ze źródłowej maszyny wirtualnej do docelowej maszyny wirtualnej, aby określić, czy istnieje sieciowej grupy zabezpieczeń lub UDR powodujące zakłócenia w przepływach ruchu.

   Jeśli używasz zapory lub urządzenie WUS: 
   1. Udokumentować parametry UDR, aby można je było przywrócić po zakończeniu tego kroku.
   2. Usuń UDR ze źródłowej podsieci maszyny wirtualnej lub karty sieciowej, która wskazuje na urządzenie WUS w następnym przeskoku. Sprawdź łączność ze źródłowej maszyny wirtualnej bezpośrednio do lokalizacji docelowej, która pomija urządzenie WUS. Jeśli ten krok nie działa, zobacz [Narzędzie do rozwiązywania problemów urządzenie WUS](./virtual-network-troubleshoot-nva.md).

2. Wykonaj śledzenie sieci: 
   1. Rozpocznij śledzenie sieci na docelowej maszynie wirtualnej. Dla systemu Windows można użyć **narzędzia Netsh**. W przypadku systemu Linux Użyj **TCPDump**.
   2. Uruchom **TcpPing** lub **PsPing** ze źródła do docelowego adresu IP.

      Jest to przykład polecenia **TcpPing** : `tcping64.exe -t <destination VM address> 3389`

   3. Po zakończeniu **TcpPing** Zatrzymaj śledzenie sieci w miejscu docelowym.
   4. Jeśli pakiety dotarły ze źródła, nie ma problemu z siecią. Aby zlokalizować problem z konfiguracją, należy przeanalizować zarówno zaporę maszyny wirtualnej, jak i aplikację nasłuchującą na tym porcie.

   > [!Note]
   > Nie można połączyć się z następującymi typami zasobów za pośrednictwem globalnej komunikacji równorzędnej sieci wirtualnej (sieci wirtualne w różnych regionach):
   >
   > * Maszyny wirtualne za jednostką SKU wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa
   > * Pamięć podręczna Redis (używa podstawowej jednostki SKU ILB)
   > * Application Gateway (używa podstawowej jednostki SKU ILB)
   > * Zestawy skalowania (używa podstawowej jednostki SKU ILB)
   > * Klastry usługi Service Fabric (używają jednostki SKU wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa)
   > * SQL Server zawsze włączone (używa podstawowej jednostki SKU ILB)
   > * App Service Environment (używa podstawowej jednostki SKU ILB)
   > * API Management (używa jednostki SKU wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa)
   > * Azure AD DS (używa podstawowej jednostki SKU ILB)

Aby uzyskać więcej informacji, zobacz [wymagania i ograniczenia](./virtual-network-peering-overview.md#requirements-and-constraints) globalnej komunikacji równorzędnej.

### <a name="the-peering-status-is-disconnected"></a>Stan komunikacji równorzędnej to "Rozłączono"

Aby rozwiązać ten problem, Usuń komunikację równorzędną z obu sieci wirtualnych, a następnie utwórz je ponownie.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Rozwiązywanie problemów z łącznością między siecią wirtualną gwiazdy i zasobem lokalnym

Czy sieć korzysta z bramy urządzenie WUS lub sieci VPN innej firmy?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Moja sieć używa bramy urządzenie WUS lub sieci VPN innej firmy

Aby rozwiązać problemy z łącznością, które wpływają na usługę urządzenie WUS lub bramę sieci VPN innej firmy, zobacz następujące artykuły:

* [URZĄDZENIE WUS narzędzia do rozwiązywania problemów](./virtual-network-troubleshoot-nva.md)
* [Tworzenie łańcuchów usług](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Moja sieć nie używa bramy urządzenie WUS lub VPN innej firmy

Czy sieć wirtualna centrum i Sieć wirtualna szprych mają bramę sieci VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Zarówno sieć wirtualna centrum, jak i Sieć wirtualna szprychy mają bramę sieci VPN

Korzystanie z bramy zdalnej nie jest obsługiwane.

Jeśli sieć wirtualna szprych ma już bramę sieci VPN, opcja **Użyj bramy zdalnej** nie jest obsługiwana w sieci wirtualnej szprych. Jest to spowodowane ograniczeniem komunikacji równorzędnej sieci wirtualnej.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Zarówno sieć wirtualna centrum, jak i Sieć wirtualna szprych nie mają bramy sieci VPN

W przypadku połączeń typu lokacja-lokacja i Azure ExpressRoute sprawdź następujące główne przyczyny problemów z łącznością ze zdalną siecią wirtualną w środowisku lokalnym:

* Upewnij się, że w sieci wirtualnej z bramą jest zaznaczone pole wyboru **Zezwalaj na ruch przesłany dalej** .
* W sieci wirtualnej, która nie ma bramy, sprawdź, czy jest zaznaczone pole wyboru **Użyj bramy zdalnej** .
* Skontaktuj się z administratorem sieci, aby upewnić się, że wszystkie te urządzenia mają dodaną zdalną przestrzeń adresową sieci wirtualnej.

Dla połączeń punkt-lokacja:

* Upewnij się, że w sieci wirtualnej z bramą jest zaznaczone pole wyboru **Zezwalaj na ruch przesłany dalej** .
* W sieci wirtualnej, która nie ma bramy, sprawdź, czy jest zaznaczone pole wyboru **Użyj bramy zdalnej** .
* Pobierz i zainstaluj ponownie pakiet klienta punkt-lokacja. Trasy sieci wirtualnej, które są nowo połączone za pomocą komunikacji równorzędnej, nie są automatycznie dodawane do klientów punkt-lokacja.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Rozwiązywanie problemów z łącznością sieciową gwiazdy między sieciami wirtualnymi szprych w tym samym regionie

Sieć centrum musi zawierać urządzenie WUS. Skonfiguruj UDR w szprychach, które mają ustawioną urządzenie WUSę w następnym przeskoku, i Włącz opcję **Zezwalaj na ruch przesłany dalej** w sieci wirtualnej centrum.

Aby uzyskać więcej informacji, zobacz Tworzenie [łańcucha usług](./virtual-network-peering-overview.md#service-chaining)i omawiaj wymagania przy użyciu wybranego przez siebie [dostawcy urządzenie WUS](https://mskb.pkisolutions.com/kb/2984655) .

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Rozwiązywanie problemów z łącznością sieciową gwiazdy między sieciami wirtualnymi szprych w różnych regionach

Przesyłanie za pośrednictwem globalnej komunikacji równorzędnej sieci wirtualnej jest teraz obsługiwane. Łączność między globalnymi sieciami wirtualnymi nie działa w przypadku następujących zasobów:

* Maszyny wirtualne za jednostką SKU wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa
* Pamięć podręczna Redis (używa podstawowej jednostki SKU ILB)
* Application Gateway (używa podstawowej jednostki SKU ILB)
* Zestawy skalowania (używa podstawowej jednostki SKU ILB)
* Klastry usługi Service Fabric (używają jednostki SKU wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa)
* SQL Server zawsze włączone (używa podstawowej jednostki SKU ILB)
* App Service Environment (używa podstawowej jednostki SKU ILB)
* API Management (używa jednostki SKU wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa)
* Azure AD DS (używa podstawowej jednostki SKU ILB)

Aby uzyskać więcej informacji, zobacz [wymagania i ograniczenia](./virtual-network-peering-overview.md#requirements-and-constraints) globalnej komunikacji równorzędnej i [różnych topologii sieci VPN](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Rozwiązywanie problemów z łącznością sieciową gwiazdy między aplikacją sieci Web a siecią wirtualną szprych

Aby rozwiązać ten problem:

1. Zaloguj się do witryny Azure Portal. 
1. W aplikacji sieci Web wybierz pozycję **Sieć**, a następnie wybierz pozycję integracja z siecią **wirtualną**.
1. Sprawdź, czy jest wyświetlana zdalna Sieć wirtualna. Ręcznie wprowadź zdalną przestrzeń adresową sieci wirtualnej (**Synchronizacja sieci** i **Dodawanie tras**).

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Integrowanie aplikacji z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md)
* [Routing sieci VPN typu punkt-lokacja — informacje](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Rozwiązywanie problemów z konfiguracją komunikacji równorzędnej sieci wirtualnej 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Bieżąca dzierżawa `<TENANT ID>` nie ma autoryzacji dostępu do połączonej subskrypcji

Aby rozwiązać ten problem, zobacz [Tworzenie komunikacji równorzędnej — interfejs wiersza polecenia platformy Azure](./create-peering-different-subscriptions.md#cli).

### <a name="not-connected"></a>Brak połączenia

Aby rozwiązać ten problem, Usuń komunikację równorzędną z obu sieci wirtualnych, a następnie utwórz je ponownie.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Nie można nawiązać komunikacji równorzędnej z siecią wirtualną datakostkami

Aby rozwiązać ten problem, należy skonfigurować komunikację równorzędną sieci wirtualnej w obszarze **Azure Databricks**, a następnie określić docelową sieć wirtualną przy użyciu **identyfikatora zasobu**. Aby uzyskać więcej informacji, zobacz [równorzędną sieć wirtualną z siecią wirtualną do zdalnej sieci wirtualnej](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>Zdalna Sieć wirtualna nie ma bramy

Ten problem występuje w przypadku równorzędnych sieci wirtualnych od różnych dzierżawców i nowszych `Use Remote Gateways` . Ograniczenie Azure Portal polega na tym, że nie można zweryfikować obecności bramy sieci wirtualnej w sieci wirtualnej innej dzierżawy.

Istnieją dwa sposoby rozwiązania problemu:

 * Usuń komunikację równorzędną i aktywuj `Use Remote Gateways` opcję podczas tworzenia nowej komunikacji równorzędnej.
 * Aby włączyć, użyj programu PowerShell lub interfejsu wiersza polecenia, a nie Azure Portal `Use Remote Gateways` .

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)