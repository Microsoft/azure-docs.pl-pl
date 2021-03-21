---
title: 'Współdziałanie na platformie Azure: Analiza płaszczyzny danych'
description: Ten artykuł zawiera analizę płaszczyzny danych Instalatora testów, którego można użyć do analizowania współdziałania między ExpressRoute, sieci VPN typu lokacja-lokacja i komunikacji równorzędnej sieci wirtualnych na platformie Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 4797fca5b4a756ca502147172e6f1590b9eacbe4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98234175"
---
# <a name="interoperability-in-azure--data-plane-analysis"></a>Współdziałanie na platformie Azure: Analiza płaszczyzny danych

W tym artykule opisano analizę płaszczyzny danych [konfiguracji testu][Setup]. Można także sprawdzić konfigurację konfiguracji [testu][Configuration] i [analizę płaszczyzny kontroli][Control-Analysis] dla konfiguracji testu.

Analiza płaszczyzny danych bada ścieżki podejmowane przez pakiety, które przechodzą z jednej sieci lokalnej (LAN lub sieci wirtualnej) na inną w ramach topologii. Ścieżka danych między dwiema sieciami lokalnymi nie musi być symetryczna. Dlatego w tym artykule analizujemy ścieżkę przekazywania z sieci lokalnej do innej sieci, która jest oddzielona od ścieżki odwrotnej.

## <a name="data-path-from-the-hub-vnet"></a>Ścieżka danych z sieci wirtualnej centrum

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do sieci wirtualnej szprychy

Komunikacja równorzędna sieci wirtualnych (VNet) emuluje funkcjonalność mostka sieciowego między dwoma sieci wirtualnych, które są połączone za pomocą komunikacji równorzędnej. Traceroute dane wyjściowe z piasty do maszyny wirtualnej w sieci wirtualnej szprychy są pokazane tutaj:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.11.30.4

Trace complete.
```

Na poniższej ilustracji przedstawiono graficzny widok połączenia sieci wirtualnej Hub i sieci wirtualnej szprychy z perspektywy Network Watcher platformy Azure:


![1][1]

### <a name="path-to-the-branch-vnet"></a>Ścieżka do sieci wirtualnej gałęzi

Traceroute dane wyjściowe z koncentratora do maszyny wirtualnej w sieci wirtualnej gałęzi jest pokazane tutaj:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms     1 ms     1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     2 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

W tym traceroute pierwszy przeskok to Brama sieci VPN w usłudze Azure VPN Gateway Sieć wirtualna centrum. Drugim przeskokiem jest Brama sieci VPN w oddziale firmy. Adres IP bramy sieci VPN gałęzi nie jest anonsowany przez sieć wirtualną centrali. Trzeci przeskok to maszyna wirtualna w sieci wirtualnej gałęzi.

Na poniższej ilustracji przedstawiono graficzny widok połączenia sieci wirtualnej centrum i sieci wirtualnej gałęzi z perspektywy Network Watcher:

![2][2]

Dla tego samego połączenia na poniższej ilustracji przedstawiono widok siatki w Network Watcher:

![3][3]

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Traceroute dane wyjściowe z Centrum sieci wirtualnej do maszyny wirtualnej w lokalizacji lokalnej 1 są wyświetlane tutaj:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     2 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

W tym traceroute pierwszy przeskok jest punktem końcowym tunelu bramy usługi Azure ExpressRoute do routera brzegowego firmy Microsoft (MSEE). Drugi i trzeci przeskoki są routerem brzegowym klienta (CE) i lokalizacją lokalną sieci LAN 1. Te adresy IP nie są anonsowane w sieci wirtualnej centrum. Czwarty przeskok to maszyna wirtualna w lokalizacji lokalnej 1.


### <a name="path-to-on-premises-location-2"></a>Ścieżka do lokalizacji lokalnej 2

Traceroute dane wyjściowe z Centrum sieci wirtualnej do maszyny wirtualnej w lokalizacji lokalnej 2 przedstawiono tutaj:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    75 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

W tym traceroute pierwszy przeskok jest punktem końcowym tunelu bramy ExpressRoute do MSEE. Drugi i trzeci przeskoki to router CE i lokalne adresy IP w lokalizacji lokalnej 2. Te adresy IP nie są anonsowane w sieci wirtualnej centrum. Czwarty przeskok to maszyna wirtualna w lokalizacji lokalnej 2.

### <a name="path-to-the-remote-vnet"></a>Ścieżka do zdalnej sieci wirtualnej

Traceroute dane wyjściowe z koncentratora do maszyny wirtualnej w zdalnej sieci wirtualnej są wyświetlane tutaj:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     2 ms     2 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    69 ms  10.17.30.4

Trace complete.
```

W tym traceroute pierwszy przeskok jest punktem końcowym tunelu bramy ExpressRoute do MSEE. Drugi przeskok to adres IP bramy zdalnej sieci wirtualnej. Zakres adresów IP drugiego przeskoku nie jest anonsowany w sieci wirtualnej centrum. Trzeci przeskok to maszyna wirtualna w zdalnej sieci wirtualnej.

## <a name="data-path-from-the-spoke-vnet"></a>Ścieżka danych z sieci wirtualnej szprychy

Sieć wirtualna szprychy udostępnia widok sieci wirtualnej centrum. Za pośrednictwem komunikacji równorzędnej sieci wirtualnej, Sieć wirtualna szprych korzysta z łączności z bramą zdalną piasty z siecią wirtualną, tak jakby była bezpośrednio podłączona do sieci wirtualnej szprych.

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej piasty

Dane wyjściowe traceroute z sieci wirtualnej szprychy do maszyny wirtualnej w sieci wirtualnej centrum są wyświetlane tutaj:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet"></a>Ścieżka do sieci wirtualnej gałęzi

Dane wyjściowe traceroute z sieci wirtualnej szprychy do maszyny wirtualnej w sieci wirtualnej gałęzi są pokazane tutaj:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.10.30.142
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

W tym traceroute pierwszy przeskok jest bramą sieci VPN koncentratora. Drugim przeskokiem jest Brama sieci VPN w oddziale firmy. Adres IP bramy sieci VPN gałęzi nie jest anonsowany w sieci wirtualnej Hub/szprychy. Trzeci przeskok to maszyna wirtualna w sieci wirtualnej gałęzi.

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Dane wyjściowe traceroute z sieci wirtualnej szprychy do maszyny wirtualnej w lokalizacji lokalnej 1 są przedstawione tutaj:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    24 ms     2 ms     3 ms  10.10.30.132
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4     3 ms     2 ms     2 ms  10.2.30.10

Trace complete.
```

W tym traceroute pierwszy przeskok jest punktem końcowym tunelu bramy ExpressRoute Gateway sieci wirtualnej do MSEE. Drugi i trzeci przeskoki są routerem CE i lokalnymi adresami IP sieci LAN. Te adresy IP nie są anonsowane w sieci wirtualnej Hub/szprychy. Czwarty przeskok to maszyna wirtualna w lokalizacji lokalnej 1.

### <a name="path-to-on-premises-location-2"></a>Ścieżka do lokalizacji lokalnej 2

Traceroute dane wyjściowe z sieci wirtualnej szprychy do maszyny wirtualnej w lokalizacji lokalnej 2 przedstawiono tutaj:

```console
C:\Users\rb>tracert 10.1.31.10

Tracing route to 10.1.31.10 over a maximum of 30 hops

  1    76 ms    75 ms    76 ms  10.10.30.134
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    75 ms    75 ms    75 ms  10.1.31.10

Trace complete.
```

W tym traceroute pierwszy przeskok jest punktem końcowym tunelu bramy ExpressRoute Gateway sieci wirtualnej do MSEE. Drugi i trzeci przeskoki to router CE i lokalne adresy IP w lokalizacji lokalnej 2. Te adresy IP nie są anonsowane w sieci wirtualnych Hub/szprychy. Czwarty przeskok to maszyna wirtualna w lokalizacji lokalnej 2.

### <a name="path-to-the-remote-vnet"></a>Ścieżka do zdalnej sieci wirtualnej

Dane wyjściowe traceroute z sieci wirtualnej szprychy do maszyny wirtualnej w zdalnej sieci wirtualnej są pokazane tutaj:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1     2 ms     1 ms     1 ms  10.10.30.133
  2     *        *        *     Request timed out.
  3    71 ms    70 ms    70 ms  10.17.30.4

Trace complete.
```

W tym traceroute pierwszy przeskok jest punktem końcowym tunelu bramy ExpressRoute Gateway sieci wirtualnej do MSEE. Drugi przeskok to adres IP bramy zdalnej sieci wirtualnej. Zakres adresów IP drugiego przeskoku nie jest anonsowany w sieci wirtualnej Hub/szprychy. Trzeci przeskok to maszyna wirtualna w zdalnej sieci wirtualnej.

## <a name="data-path-from-the-branch-vnet"></a>Ścieżka danych z sieci wirtualnej gałęzi

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej piasty

Dane wyjściowe traceroute z sieci wirtualnej gałęzi do maszyny wirtualnej w sieci wirtualnej centrum są pokazane tutaj:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     3 ms  10.10.30.4

Trace complete.
```

W tym traceroute pierwszy przeskok to Brama sieci VPN gałęzi. Drugi przeskok to Brama sieci VPN koncentratora. Adres IP bramy sieci VPN koncentratora nie jest anonsowany w zdalnej sieci wirtualnej. Trzeci przeskok to maszyna wirtualna w sieci wirtualnej centrum.

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do sieci wirtualnej szprychy

Traceroute wyjście z sieci wirtualnej gałęzi do maszyny wirtualnej w sieci wirtualnej szprychy jest pokazane tutaj:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1     1 ms    <1 ms     1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     4 ms     3 ms     2 ms  10.11.30.4

Trace complete.
```

W tym traceroute pierwszy przeskok to Brama sieci VPN gałęzi. Drugi przeskok to Brama sieci VPN koncentratora. Adres IP bramy sieci VPN koncentratora nie jest anonsowany w zdalnej sieci wirtualnej. Trzeci przeskok to maszyna wirtualna w sieci wirtualnej szprych.

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Dane wyjściowe traceroute z sieci wirtualnej gałęzi do maszyny wirtualnej w lokalizacji lokalnej 1 są przedstawione tutaj:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1     1 ms    <1 ms    <1 ms  10.11.30.100
  2     *        *        *     Request timed out.
  3     3 ms     2 ms     2 ms  10.2.30.125
  4     *        *        *     Request timed out.
  5     3 ms     3 ms     3 ms  10.2.30.10

Trace complete.
```

W tym traceroute pierwszy przeskok to Brama sieci VPN gałęzi. Drugi przeskok to Brama sieci VPN koncentratora. Adres IP bramy sieci VPN koncentratora nie jest anonsowany w zdalnej sieci wirtualnej. Trzeci przeskok to punkt końcowy tunelu sieci VPN na podstawowym routerze CE. Czwarty przeskok to wewnętrzny adres IP lokalizacji lokalnej 1. Ten adres IP sieci LAN nie jest anonsowany poza routerem CE. Piąty przeskok to docelowa maszyna wirtualna w lokalizacji lokalnej 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Ścieżka do lokalizacji lokalnej 2 i zdalnej sieci wirtualnej

Jak opisano w analizie płaszczyzny kontroli, Sieć wirtualna gałęzi nie ma wglądu w lokalizację lokalną 2 lub do zdalnej sieci wirtualnej na konfigurację sieci. Następujące wyniki polecenia ping potwierdzają: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

C:\Users\rb>ping 10.17.30.4

Pinging 10.17.30.4 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.17.30.4:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

## <a name="data-path-from-on-premises-location-1"></a>Ścieżka danych z lokalizacji lokalnej 1

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej piasty

Traceroute dane wyjściowe z lokalizacji lokalnej 1 do maszyny wirtualnej w sieci wirtualnej centrum są wyświetlane tutaj:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     2 ms     2 ms     2 ms  10.10.30.4

Trace complete.
```

W tym traceroute pierwsze dwa przeskoki są częścią sieci lokalnej. Trzeci przeskok to podstawowy interfejs MSEE, który jest przeznaczony dla routera CE. Czwarty przeskok to brama ExpressRoute sieci wirtualnej centrum. Zakres adresów IP bramy ExpressRoute sieci wirtualnej koncentratora nie jest anonsowany w przypadku lokalnej pamięci. Piąty przeskok to docelowa maszyna wirtualna.

Network Watcher zapewnia tylko widok skoncentrowany na platformie Azure. W przypadku perspektyw lokalnych korzystamy z platformy Azure Network Performance Monitor. Network Performance Monitor udostępnia agentów, którzy można instalować na serwerach w sieci poza platformą Azure na potrzeby analizy ścieżki danych.

Na poniższej ilustracji przedstawiono widok topologii łączności maszyny wirtualnej w lokalizacji lokalnej 1 z maszyną wirtualną w sieci wirtualnej koncentratora za pośrednictwem ExpressRoute:

![4][4]

Jak wspomniano wcześniej, Konfiguracja testu używa sieci VPN typu lokacja-lokacja jako łączności kopii zapasowej dla ExpressRoute między lokalizacją lokalną 1 i siecią wirtualną piasty. Aby przetestować ścieżkę danych kopii zapasowej, przyciągnąćmy błąd łącza ExpressRoute między lokalnym routerem CE i odpowiednim MSEE. Aby wywołać błąd łącza ExpressRoute, Zamknij interfejs CE, który nawiąże MSEE:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     3 ms  10.10.30.4

Trace complete.
```

Na poniższej ilustracji przedstawiono widok topologii łączności maszyny wirtualnej w lokalizacji lokalnej 1 z maszyną wirtualną w sieci wirtualnej koncentratora za pośrednictwem połączenia VPN między lokacjami, gdy ExpressRoute łączność nie działa:

![5][5]

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do sieci wirtualnej szprychy

Traceroute dane wyjściowe z lokalizacji lokalnej 1 do maszyny wirtualnej w sieci wirtualnej szprychy są wyświetlane tutaj:

Wróćmy do ExpressRoute podstawowej łączności, aby przeprowadzić analizę ścieżki danych w kierunku sieci wirtualnej szprychy:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5     3 ms     2 ms     2 ms  10.11.30.4

Trace complete.
```

Wywołaj podstawową łączność ExpressRoute 1 dla pozostałej części analizy ścieżki danych.

### <a name="path-to-the-branch-vnet"></a>Ścieżka do sieci wirtualnej gałęzi

Traceroute dane wyjściowe z lokalizacji lokalnej 1 do maszyny wirtualnej w sieci wirtualnej gałęzi jest pokazane tutaj:

```console
C:\Users\rb>tracert 10.11.30.68

Tracing route to 10.11.30.68 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2    <1 ms    <1 ms    <1 ms  192.168.30.0
  3     3 ms     2 ms     2 ms  10.11.30.68

Trace complete.
```

### <a name="path-to-on-premises-location-2"></a>Ścieżka do lokalizacji lokalnej 2

Podczas omawiania [analizy płaszczyzny kontroli][Control-Analysis], lokalizacja lokalna 1 nie ma wglądu w lokalizację lokalną 2 dla konfiguracji sieci. Następujące wyniki polecenia ping potwierdzają: 

```console
C:\Users\rb>ping 10.1.31.10

Pinging 10.1.31.10 with 32 bytes of data:

Request timed out.
...
Request timed out.

Ping statistics for 10.1.31.10:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
```

### <a name="path-to-the-remote-vnet"></a>Ścieżka do zdalnej sieci wirtualnej

Traceroute dane wyjściowe z lokalizacji lokalnej 1 do maszyny wirtualnej w zdalnej sieci wirtualnej są wyświetlane tutaj:

```console
C:\Users\rb>tracert 10.17.30.4

Tracing route to 10.17.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.2.30.3
  2     2 ms     5 ms     7 ms  192.168.30.0
  3    <1 ms    <1 ms    <1 ms  192.168.30.18
  4     *        *        *     Request timed out.
  5    69 ms    70 ms    69 ms  10.17.30.4

Trace complete.
```

## <a name="data-path-from-on-premises-location-2"></a>Ścieżka danych z lokalizacji lokalnej 2

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej piasty

Traceroute dane wyjściowe z lokalizacji lokalnej 2 do maszyny wirtualnej w sieci wirtualnej centrum są wyświetlane tutaj:

```console
C:\Windows\system32>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.4
  3    <1 ms    <1 ms    <1 ms  192.168.31.22
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do sieci wirtualnej szprychy

Traceroute dane wyjściowe z lokalizacji lokalnej 2 do maszyny wirtualnej w sieci wirtualnej szprychy są pokazane tutaj:

```console
C:\Windows\system32>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops
  1    <1 ms    <1 ms     1 ms  10.1.31.3
  2    <1 ms    <1 ms    <1 ms  192.168.31.0
  3    <1 ms    <1 ms    <1 ms  192.168.31.18
  4     *        *        *     Request timed out.
  5    75 ms    74 ms    74 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Ścieżka do sieci wirtualnej gałęzi, lokalizacji lokalnej 1 i zdalnej sieci wirtualnej

W miarę dyskusji w [analizie płaszczyzny kontroli][Control-Analysis], lokalizacja lokalna 1 nie ma wglądu w sieć wirtualną gałęzi, do lokalizacji lokalnej 1 lub do zdalnej sieci wirtualnej zgodnie z konfiguracją sieci. 

## <a name="data-path-from-the-remote-vnet"></a>Ścieżka danych ze zdalnej sieci wirtualnej

### <a name="path-to-the-hub-vnet"></a>Ścieżka do sieci wirtualnej piasty

Traceroute dane wyjściowe z zdalnej sieci wirtualnej do maszyny wirtualnej w sieci wirtualnej centrum są wyświetlane tutaj:

```console
C:\Users\rb>tracert 10.10.30.4

Tracing route to 10.10.30.4 over a maximum of 30 hops

  1    65 ms    65 ms    65 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    69 ms    68 ms    68 ms  10.10.30.4

Trace complete.
```

### <a name="path-to-the-spoke-vnet"></a>Ścieżka do sieci wirtualnej szprychy

Traceroute dane wyjściowe z zdalnej sieci wirtualnej do maszyny wirtualnej w sieci wirtualnej szprychy są pokazane tutaj:

```console
C:\Users\rb>tracert 10.11.30.4

Tracing route to 10.11.30.4 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3    71 ms    69 ms    69 ms  10.11.30.4

Trace complete.
```

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Ścieżka do sieci wirtualnej gałęzi i lokalizacji lokalnej 2

Podczas omawiania [analizy płaszczyzny kontroli][Control-Analysis]zdalna Sieć wirtualna nie ma wglądu w sieć wirtualną gałęzi ani do lokalizacji lokalnej 2 zgodnie z konfiguracją sieci. 

### <a name="path-to-on-premises-location-1"></a>Ścieżka do lokalizacji lokalnej 1

Dane wyjściowe traceroute z zdalnej sieci wirtualnej do maszyny wirtualnej w lokalizacji lokalnej 1 są przedstawione tutaj:

```console
C:\Users\rb>tracert 10.2.30.10

Tracing route to 10.2.30.10 over a maximum of 30 hops

  1    67 ms    67 ms    67 ms  10.17.30.36
  2     *        *        *     Request timed out.
  3     *        *        *     Request timed out.
  4    69 ms    69 ms    69 ms  10.2.30.10

Trace complete.
```

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute i połączenie sieci VPN typu lokacja-lokacja wspólnie

###  <a name="site-to-site-vpn-over-expressroute"></a>Sieć VPN typu lokacja-lokacja za pośrednictwem ExpressRoute

Sieć VPN typu lokacja-lokacja można skonfigurować za pomocą komunikacji równorzędnej firmy Microsoft, aby prywatnie wymieniać dane między siecią lokalną i usługą Azure sieci wirtualnych. Za pomocą tej konfiguracji można wymieniać dane z poufnością, autentycznością i integralnością. Wymiana danych to również ochrona przed odtwarzaniem. Aby uzyskać więcej informacji o sposobie konfigurowania sieci VPN typu lokacja-lokacja w trybie tunelowania przy użyciu ExpressRoute komunikacji równorzędnej firmy Microsoft, zobacz [sieci VPN typu lokacja-lokacja za pośrednictwem usługi ExpressRoute Microsoft peering][S2S-Over-ExR]. 

Podstawowe ograniczenie konfiguracji sieci VPN typu lokacja-lokacja, która używa komunikacji równorzędnej firmy Microsoft, to przepływność. Przepływność przez tunel IPsec jest ograniczona przez pojemność bramy sieci VPN. Przepływność bramy sieci VPN jest mniejsza niż ExpressRoute przepływność. W tym scenariuszu użycie tunelu IPsec w przypadku wysoce bezpiecznego ruchu i użycie prywatnej komunikacji równorzędnej dla całego ruchu pomaga zoptymalizować wykorzystanie przepustowości ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Sieć VPN typu lokacja-lokacja jako bezpieczna ścieżka trybu failover dla usługi ExpressRoute

ExpressRoute służy jako para nadmiarowego obwodu w celu zapewnienia wysokiej dostępności. W różnych regionach świadczenia usługi Azure można skonfigurować ExpressRoute Geograficznie nadmiarowy. Ponadto, jak pokazano w naszej konfiguracji testowej w regionie świadczenia usługi Azure, można użyć sieci VPN typu lokacja-lokacja, aby utworzyć ścieżkę trybu failover dla łączności ExpressRoute. Gdy te same prefiksy są anonsowane zarówno w ExpressRoute, jak i w sieci VPN typu lokacja-lokacja, platforma Azure ma priorytet ExpressRoute. Aby uniknąć asymetrycznego routingu między ExpressRoute i siecią VPN typu lokacja-lokacja, konfiguracja sieci lokalnej powinna również natłokować przy użyciu połączenia ExpressRoute przed użyciem połączenia sieci VPN typu lokacja-lokacja.

Aby uzyskać więcej informacji o konfigurowaniu współistniejących połączeń dla ExpressRoute i sieci VPN typu lokacja-lokacja, zobacz [współistnienie ExpressRoute i lokacja-][ExR-S2S-CoEx]lokacja.

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Zwiększanie łączności zaplecza z sieci wirtualnych i lokalizacjami gałęzi

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Łączność z siecią wirtualną przy użyciu komunikacji równorzędnej sieci wirtualnej

Architektura sieci wirtualnej typu Hub i szprych jest szeroko używana. Centrum to sieć wirtualna na platformie Azure, która działa jako centralny punkt łączności między satelitami sieci wirtualnych i sieci lokalnej. Szprychy są sieci wirtualnych tego elementu równorzędnego z koncentratorem i którego można użyć do izolowania obciążeń. Przepływy ruchu między lokalnym centrum danych a centrum za pośrednictwem połączenia ExpressRoute lub sieci VPN. Aby uzyskać więcej informacji o architekturze, zobacz [implementowanie topologii sieci Hub i gwiazdy na platformie Azure][Hub-n-Spoke].

W przypadku komunikacji równorzędnej sieci wirtualnych w obrębie regionu szprycha sieci wirtualnych może używać bram sieci wirtualnej Hub (zarówno bramy VPN, jak i ExpressRoute) do komunikowania się z sieciami zdalnymi.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Łączność między sieciami wirtualnymi przy użyciu połączenia VPN typu lokacja-lokacja

Możesz potrzebować oddziału sieci wirtualnych, które znajdują się w różnych regionach, i sieci lokalnych, aby komunikować się ze sobą za pośrednictwem sieci wirtualnej centrum. Natywne rozwiązanie platformy Azure dla tej konfiguracji to połączenie sieci VPN typu lokacja-lokacja przy użyciu sieci VPN. Alternatywą jest użycie wirtualnego urządzenia sieciowego (urządzenie WUS) do routingu w centrum.

Aby uzyskać więcej informacji, zobacz [co to jest VPN Gateway?][VPN] i [Wdróż urządzenie WUS o wysokiej][Deploy-NVA]dostępności.


## <a name="next-steps"></a>Następne kroki

Zobacz [często zadawane pytania dotyczące ExpressRoute][ExR-FAQ] :
-   Dowiedz się, ile obwodów usługi ExpressRoute można połączyć z bramą ExpressRoute.
-   Dowiedz się, ile bram ExpressRoute można połączyć z obwodem ExpressRoute.
-   Dowiedz się więcej na temat innych limitów skalowania ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Network Watcher widoku łączności z sieci wirtualnej z koncentratorem do sieci wirtualnej szprychy"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Network Watcher widoku łączności z sieci wirtualnej z koncentratorem do sieci wirtualnej gałęzi"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Network Watcher widok siatki łączności z sieci wirtualnej koncentratora do sieci wirtualnej gałęzi"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Network Performance Monitor widoku łączności z maszyny wirtualnej lokalizacji 1 do sieci wirtualnej koncentratora za pośrednictwem ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Network Performance Monitor widoku łączności z maszyny wirtualnej lokalizacji 1 do sieci wirtualnej koncentratora za pośrednictwem połączenia VPN typu lokacja-lokacja"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[Configuration]: ./connectivty-interoperability-configuration.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Configuration]: ./connectivty-interoperability-configuration.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md