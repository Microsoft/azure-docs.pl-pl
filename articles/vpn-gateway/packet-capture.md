---
title: 'Azure VPN Gateway: Konfigurowanie przechwytywania pakietów'
description: Dowiedz się więcej na temat funkcji przechwytywania pakietów, których można używać na bramach sieci VPN.
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 2429a8d08baa34aed120cffa069abae1fb9a3df9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75353521"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>Konfigurowanie przechwytywania pakietów dla bram sieci VPN

Problemy związane z łącznością i wydajnością są często czasochłonne i bardzo dużo czasu i wysiłku wystarczy zawęzić przyczynę problemu. Możliwość przechwycenia pakietów znacznie pomaga skrócić czas ograniczania zakresu problemu do określonych części sieci, na przykład to, czy problem znajduje się po stronie klienta sieci, stronie platformy Azure, czy w innym miejscu. Po zawężaniu problemu jest znacznie bardziej wydajne debugowanie i podejmowanie akcji zaradczych.

Dostępne są kilka powszechnie dostępnych narzędzi do przechwytywania pakietów. Jednak uzyskanie odpowiednich przechwytywania pakietów przy użyciu tych narzędzi jest często bardzo trudne, zwłaszcza podczas pracy z dużą ilością scenariuszy ruchu sieciowego. Możliwości filtrowania udostępniane przez funkcję przechwytywania pakietów bramy sieci VPN stają się głównym rozróżnieniem. Oprócz powszechnie dostępnych narzędzi do przechwytywania pakietów można użyć funkcji przechwytywania pakietów bramy sieci VPN.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Możliwości filtrowania przechwytywania pakietów przez bramę sieci VPN

Przechwytywanie pakietów usługi VPN Gateway można uruchamiać na bramie lub w określonym połączeniu w zależności od potrzeb klientów. Możesz również uruchomić przechwycenia pakietów na wielu tunelach w tym samym czasie. Można przechwytywać ruch pojedynczy lub dwukierunkowy, ruch IKE i ESP oraz wewnętrzne pakiety wraz z filtrowaniem na bramie sieci VPN.

Użycie filtru 5 krotek (podsieć źródłowa, podsieć docelowa, port źródłowy, port docelowy, protokół) i flagi TCP (SYN, ACK, FIN, URG, PSH —, RST) jest pomocne podczas izolowania problemów dotyczących dużej ilości ruchu sieciowego.

Podczas przechwytywania pakietów można używać tylko jednej opcji na właściwość.

## <a name="setup-packet-capture-using-powershell"></a>Konfigurowanie przechwytywania pakietów przy użyciu programu PowerShell

Zapoznaj się z poniższymi przykładami poleceń programu PowerShell, aby uruchomić i zatrzymać przechwytywanie pakietów. Aby uzyskać więcej informacji na temat opcji parametrów (takich jak tworzenie filtru), zobacz ten [dokument](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)programu PowerShell.

### <a name="start-packet-capture-for-a-vpn-gateway"></a>Rozpocznij przechwytywanie pakietów dla bramy sieci VPN

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Opcjonalny parametr **-danych filtru** może służyć do zastosowania filtru.

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>Zatrzymywanie przechwytywania pakietu dla bramy sieci VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>Rozpocznij przechwytywanie pakietów dla połączenia bramy sieci VPN

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Opcjonalny parametr **-danych filtru** może służyć do zastosowania filtru.

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>Zatrzymywanie przechwytywania pakietu na połączeniu z bramą sieci VPN

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Najważniejsze zagadnienia

- Przechwytywanie pakietów może mieć wpływ na wydajność. Pamiętaj, aby zatrzymać przechwytywanie pakietów, gdy nie jest to konieczne.
- Sugerowany minimalny czas trwania przechwytywania pakietów to 600 sekund. Krótszy czas trwania przechwytywania pakietów może nie zapewniać kompletnych danych ze względu na zsynchronizowanie problemów między wieloma składnikami ścieżki.
- Pliki danych przechwytywania pakietów są generowane w formacie PCAP. Otwórz pliki PCAP za pomocą programu Wireshark lub innych powszechnie dostępnych aplikacji.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat VPN Gateway, zobacz [Informacje o VPN Gateway](vpn-gateway-about-vpngateways.md)
