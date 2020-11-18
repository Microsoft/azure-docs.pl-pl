---
title: Konfigurowanie trybu dystrybucji Azure Load Balancer
titleSuffix: Azure Load Balancer
description: W tym artykule Rozpocznij Konfigurowanie trybu dystrybucji dla Azure Load Balancer w celu obsługi koligacji źródłowego adresu IP.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: aa2d2cda59f23ef0b961ccac60b119996423688e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682336"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Skonfiguruj tryb dystrybucji dla Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Tryb dystrybucji oparty na skrócie

Domyślny tryb dystrybucji dla usługi Azure Load Balancer to skrót krotki z pięcioma elementami. 

Krotka składa się z:
* **Źródłowy adres IP**
* **Port źródłowy**
* **Docelowy adres IP**
* **Port docelowy**
* **Typ protokołu**

Skrót jest używany do mapowania ruchu do dostępnych serwerów. Algorytm zawiera lepkość tylko w ramach sesji transportu. Pakiety znajdujące się w tej samej sesji są kierowane do tego samego adresu IP centrum danych za punktem końcowym ze zrównoważonym obciążeniem. Gdy klient uruchamia nową sesję z tego samego źródłowego adresu IP, port źródłowy zmieni się i spowoduje, że ruch przejdzie do innego punktu końcowego centrum danych.

![Tryb dystrybucji oparty na skrócie pięciu krotek](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Tryb koligacji źródłowego adresu IP

Moduł równoważenia obciążenia można również skonfigurować za pomocą trybu dystrybucji koligacji źródłowego adresu IP. Ten tryb dystrybucji jest również znany jako koligacja sesji lub koligacja adresów IP klienta. Tryb używa skrótu z dwiema kolekcjami (źródłowy adres IP i docelowy adres IP) lub z trzema kolekcjami (źródłowy adres IP, docelowy adres IP i typ protokołu), aby mapować ruch do dostępnych serwerów. Korzystając z koligacji źródłowego adresu IP, połączenia uruchamiane z tego samego komputera klienckiego są przekazywane do tego samego punktu końcowego centrum danych.

Na poniższej ilustracji przedstawiono konfigurację z dwoma spójnymi kolekcjami. Zauważ, że dwie kolekcje są uruchamiane za pomocą modułu równoważenia obciążenia do maszyny wirtualnej 1 (VM1). Kopia zapasowa VM1 jest następnie wykonywana przez VM2 i VM3.

![Tryb dystrybucji koligacji sesji dwóch krotek](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Tryb koligacji źródłowego adresu IP rozwiązuje niezgodność między Azure Load Balancer i bramą Pulpit zdalny (Brama usług pulpitu zdalnego). Korzystając z tego trybu, można utworzyć farmę bramy usług pulpitu zdalnego w jednej usłudze w chmurze.

Innym scenariuszem przypadku użycia jest przekazywanie multimediów. Przekazywanie danych odbywa się za pośrednictwem protokołu UDP, ale płaszczyzna kontroli jest realizowana za pośrednictwem protokołu TCP:

* Klient uruchamia sesję TCP na adres publiczny ze zrównoważonym obciążeniem i jest kierowany do określonego DIP. Kanał pozostaje aktywny do monitorowania kondycji połączenia.
* Nowa sesja UDP z tego samego komputera klienckiego jest uruchamiana w tym samym publicznym punkcie końcowym ze zrównoważonym obciążeniem. Połączenie jest kierowane do tego samego punktu końcowego DIP co poprzednie połączenie TCP. Przekazywanie multimediów może być wykonywane przy wysokiej przepływności, przy jednoczesnym zachowaniu kanału kontrolnego za pośrednictwem protokołu TCP.

> [!NOTE]
> W przypadku zmiany zestawu ze zrównoważonym obciążeniem przez usunięcie lub dodanie maszyny wirtualnej, rozkład żądań klientów jest obliczany ponownie. Nie można zależeć od nowych połączeń z istniejących klientów, aby zakończyć działanie na tym samym serwerze. Ponadto użycie trybu dystrybucji koligacji źródłowych adresów IP może spowodować nierówne dystrybucję ruchu. Klienci uruchamiani za serwerami proxy mogą być uważani za jedną unikatową aplikację kliencką.

## <a name="configure-source-ip-affinity-settings"></a>Konfigurowanie ustawień koligacji źródłowych adresów IP

### <a name="azure-portal"></a>Azure Portal

Konfigurację trybu dystrybucji można zmienić, modyfikując regułę równoważenia obciążenia w portalu.

1. Zaloguj się do Azure Portal i Znajdź grupę zasobów zawierającą moduł równoważenia obciążenia, który chcesz zmienić, klikając pozycję **grupy zasobów**.
2. Na ekranie przegląd modułu równoważenia obciążenia kliknij pozycję **reguły równoważenia obciążenia** w obszarze **Ustawienia**.
3. Na ekranie reguły równoważenia obciążenia kliknij regułę równoważenia obciążenia, w której chcesz zmienić tryb dystrybucji.
4. W ramach zasady tryb dystrybucji jest zmieniany, zmieniając pole listy rozwijanej **trwałość sesji** .  Dostępne są następujące opcje:
    
    * **Brak (oparte na skrócie)** — określa, że kolejne żądania z tego samego klienta mogą być obsługiwane przez dowolną maszynę wirtualną.
    * **Adres IP klienta (koligacja źródłowego adresu IP 2-krotka)** — określa, że kolejne żądania z tego samego adresu IP klienta będą obsługiwane przez tę samą maszynę wirtualną.
    * **IP i protokół klienta (koligacja źródłowego adresu IP 3-krotka)** — określa, że kolejne żądania z tego samego adresu IP klienta i kombinacji protokołów będą obsługiwane przez tę samą maszynę wirtualną.

5. Wybierz tryb dystrybucji, a następnie kliknij przycisk **Zapisz**.

### <a name="azure-powershell"></a>Azure PowerShell

W przypadku maszyn wirtualnych wdrożonych przy użyciu Menedżer zasobów należy użyć programu PowerShell, aby zmienić ustawienia dystrybucji modułu równoważenia obciążenia na istniejącą regułę równoważenia obciążenia. Następujące polecenie aktualizuje tryb dystrybucji: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

W przypadku klasycznych maszyn wirtualnych Użyj Azure PowerShell, aby zmienić ustawienia dystrybucji. Dodaj punkt końcowy platformy Azure do maszyny wirtualnej i skonfiguruj tryb dystrybucji modułu równoważenia obciążenia:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Ustaw wartość `LoadBalancerDistribution` elementu dla wymaganej ilości usługi równoważenia obciążenia. Określ sourceIP dla dwóch krotek (źródłowy adres IP i docelowy adres IP). Określ sourceIPProtocol dla funkcji równoważenia obciążenia dla trzech krotek (źródłowy adres IP, docelowy adres IP i typ protokołu). Określ brak dla domyślnego zachowania dla pięciu krotek równoważenia obciążenia.

Pobierz konfigurację trybu dystrybucji modułu równoważenia obciążenia punktu końcowego, używając następujących ustawień:

```azurepowershell
PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
LBSetName : MyLoadBalancedSet
LocalPort : 80
Name : HTTP
Port : 80
Protocol : tcp
Vip : 65.52.xxx.xxx
ProbePath :
ProbePort : 80
ProbeProtocol : tcp
ProbeIntervalInSeconds : 15
ProbeTimeoutInSeconds : 31
EnableDirectServerReturn : False
Acl : {}
InternalLoadBalancerName :
IdleTimeoutInMinutes : 15
LoadBalancerDistribution : sourceIP
```

Gdy `LoadBalancerDistribution` element nie jest obecny, Azure Load Balancer używa domyślnego algorytmu pięciu krotek.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Konfigurowanie trybu dystrybucji dla zestawu punktów końcowych ze zrównoważonym obciążeniem

Jeśli punkty końcowe są częścią zestawu punktów końcowych ze zrównoważonym obciążeniem, należy skonfigurować tryb dystrybucji dla zestawu punktów końcowych ze zrównoważonym obciążeniem:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Konfigurowanie trybu dystrybucji dla punktów końcowych Cloud Services

Aby zaktualizować usługę w chmurze, użyj zestawu Azure SDK dla programu .NET 2,5. Ustawienia punktu końcowego dla Cloud Services są wprowadzane w pliku csdef. Aby zaktualizować tryb dystrybucji modułu równoważenia obciążenia dla wdrożenia Cloud Services, wymagane jest uaktualnienie wdrożenia.

Oto przykład. csdef zmiany ustawień punktu końcowego:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>Przykład interfejsu API

Poniższy przykład pokazuje, jak zmienić konfigurację trybu dystrybucji modułu równoważenia obciążenia dla określonego zestawu o zrównoważonym obciążeniu we wdrożeniu. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Zmień tryb dystrybucji dla wdrożonego zestawu o zrównoważonym obciążeniu

Użyj klasycznego modelu wdrażania platformy Azure, aby zmienić istniejącą konfigurację wdrożenia. Dodaj `x-ms-version` Nagłówek i ustaw wartość w wersji 2014-09-01 lub nowszej.

#### <a name="request"></a>Żądanie

```http
POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
Content-Type: application/xml
```

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
            <Port> port-assigned-to-probe </Port>
            <Protocol> probe-protocol </Protocol>
            <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
            <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

Jak opisano wcześniej, ustaw `LoadBalancerDistribution` element na sourceIP dla koligacji dwóch krotek, sourceIPProtocol dla koligacji z trzema krotkami lub brak dla koligacji (koligacja z pięcioma krotką).

#### <a name="response"></a>Reakcja

```http
HTTP/1.1 202 Accepted
Cache-Control: no-cache
Content-Length: 0
Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
x-ms-servedbyregion: ussouth2
x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
Date: Thu, 16 Oct 2014 22:49:21 GMT
```

## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Load Balancer](load-balancer-overview.md)
* [Wprowadzenie do konfigurowania modułu równoważenia obciążenia dostępnego z Internetu](quickstart-load-balancer-standard-public-powershell.md)
* [Configure idle TCP timeout settings for your load balancer](load-balancer-tcp-idle-timeout.md) (Konfigurowanie ustawień limitu czasu bezczynności protokołu TCP dla modułu równoważenia obciążenia)
