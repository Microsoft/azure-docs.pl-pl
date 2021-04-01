---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3c1e34bb418f9be2e26afc117343f1fa50bd8566
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "76308898"
---
Te często zadawane pytania dotyczą połączeń punkt-lokacja wykorzystujących klasyczny model wdrażania.

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>Których systemów operacyjnych klienta można używać z połączeniami typu punkt-lokacja?

Obsługiwane są następujące systemy operacyjne klientów:

* Windows 7 (32-bitowy i 64-bitowy)
* Windows Server 2008 R2 (tylko 64-bitowy)
* Windows 8 (32-bitowy i 64-bitowy)
* Windows 8.1 (32-bitowy i 64-bitowy)
* Windows Server 2012 (tylko 64-bitowy)
* Windows Server 2012 R2 (tylko 64-bitowy)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>Czy można użyć dowolnego oprogramowania klienta VPN obsługującego protokół SSTP do połączenia punkt-lokacja?

Nie. Obsługa jest ograniczona tylko do wymienionych wersji systemu operacyjnego Windows.

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>Ile punktów końcowych klienta sieci VPN może istnieć w konfiguracji punkt-lokacja?

Liczba punktów końcowych klienta sieci VPN zależy od jednostki SKU i protokołu bramy.
[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Czy w przypadku połączenia punkt-lokacja można użyć własnego głównego urzędu certyfikacji PKI?

Tak. Wcześniej można było używać tylko certyfikatów głównych z podpisem własnym. Nadal można przekazać do 20 certyfikatów głównych.

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>Czy można pominąć serwery proxy i zapory, korzystając z połączenia punkt-lokacja?

Tak. Do celów tunelowania przez zaporę jest wykorzystywany protokół SSTP (Secure Socket Tunneling Protocol). Ten tunel jest wyświetlany jako połączenie HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Czy w przypadku ponownego uruchomienia komputera klienckiego skonfigurowanego pod kątem połączenia typu punkt-lokacja połączenie z siecią VPN zostanie nawiązane automatycznie?

Domyślnie komputer kliencki nie przywraca automatycznie połączenia z siecią VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Czy w przypadku połączeń punkt-lokacja jest obsługiwane automatyczne ponowne nawiązywanie połączenia i DDNS na klientach sieci VPN?

Nie. Automatyczne ponowne nawiązywanie połączenia i DDNS nie są obecnie obsługiwane w przypadku połączeń VPN typu punkt-lokacja.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>Czy w ramach tej samej sieci wirtualnej można korzystać z konfiguracji typu lokacja-lokacja i punkt-lokacja?

Tak. Oba te rozwiązania będą działać, o ile zastosowana zostanie brama sieci VPN typu RouteBased. W przypadku klasycznego modelu wdrażania należy użyć bramy dynamicznej. Połączenia typu punkt-lokacja nie są obsługiwane w przypadku bram sieci VPN o statycznym routingu ani bram, w których używane jest polecenie cmdlet **-VpnType PolicyBased**.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Czy można skonfigurować klienta typu punkt-lokacja pod kątem jednoczesnego nawiązywania połączenia z wieloma sieciami wirtualnymi?

Tak. Sieci wirtualne nie mogą jednak mieć nakładających się prefiksów IP, a przestrzenie adresowe w przypadku połączenia punkt-lokacja nie mogą nakładać się między sieciami wirtualnymi.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Jakiej przepływności można oczekiwać w przypadku połączeń typu lokacja-lokacja lub punkt-lokacja?

Trudno jest utrzymać dokładną przepływność tuneli VPN. Protokoły IPsec i SSTP należą do niejawnie ciężkich protokołów sieci VPN. Przepływność ograniczają również opóźnienia i przepustowość między lokalizacjami lokalnymi i Internetem.
