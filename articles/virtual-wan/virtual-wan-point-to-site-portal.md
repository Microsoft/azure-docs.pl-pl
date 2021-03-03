---
title: 'Samouczek: Tworzenie połączenia punkt-lokacja z platformą Azure za pomocą usługi Azure Virtual WAN'
description: Z tego samouczka dowiesz się, jak utworzyć połączenie punkt-lokacja VPN z platformą Azure w usłudze Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: 9d207e2ee0ddff49ab01094626b9af1c8505cb4e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732494"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Samouczek: Tworzenie połączenia sieci VPN użytkownika przy użyciu wirtualnej sieci WAN platformy Azure

W tym samouczku pokazano, w jaki sposób przy użyciu usługi Azure Virtual WAN utworzyć połączenie z zasobami na platformie Azure za pośrednictwem połączenia sieci VPN protokołu IPsec/IKE (IKEv2) lub OpenVPN. Ten typ połączenia wymaga, aby klient sieci VPN był skonfigurowany na komputerze klienckim. Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie wirtualnej sieci WAN
> * Tworzenie konfiguracji P2S
> * Tworzenie koncentratora wirtualnego
> * Wybieranie pul adresów klienta
> * Określ serwery DNS
> * Generuj pakiet konfiguracyjny profilu klienta sieci VPN
> * Konfigurowanie klientów sieci VPN
> * Wyświetlanie wirtualnej sieci WAN

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Tworzenie wirtualnej sieci WAN

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Tworzenie konfiguracji P2S

Konfiguracja typu punkt-lokacja (P2S) definiuje parametry połączenia klientów zdalnych.

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>Tworzenie koncentratora wirtualnego i bramy

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="choose-p2s-client-address-pools"></a><a name="chooseclientpools"></a> Wybierz P2S pule adresów klienta

[!INCLUDE [Choose pools](../../includes/virtual-wan-allocating-p2s-pools.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>Określ serwer DNS

To ustawienie można skonfigurować podczas tworzenia centrum lub w późniejszym czasie. Aby zmodyfikować, zlokalizuj centrum wirtualne. W obszarze **Sieć VPN użytkownika (wskaż lokację)** wybierz pozycję **Konfiguruj** i wprowadź adresy IP serwera DNS w polu tekstowym **niestandardowe serwery DNS** . Można określić maksymalnie 5 serwerów DNS.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="niestandardowy serwer DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>Generuj pakiet profilu klienta VPN

Wygeneruj i Pobierz pakiet profilu klienta sieci VPN, aby skonfigurować klientów sieci VPN.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>Konfigurowanie klientów sieci VPN

Użyj pobranego pakietu profilu, aby skonfigurować klientów sieci VPN dostępu zdalnego. Procedura dla każdego systemu operacyjnego jest inna. Postępuj zgodnie z instrukcjami odnoszącymi się do systemu.
Po zakończeniu konfigurowania klienta można nawiązać połączenie.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Wyświetlanie wirtualnej sieci WAN

1. Przejdź do wirtualnej sieci WAN.
1. Na stronie **Przegląd** każdy punkt na mapie reprezentuje centrum.
1. W sekcji **centra i połączenia** można wyświetlić stan centrum, lokację, region, stan połączenia sieci VPN oraz liczbę bajtów do i wychodzące.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Czyszczenie zasobów

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wirtualnej sieci WAN, zobacz:

> [!div class="nextstepaction"]
> * [Często zadawane pytania dotyczące usługi Virtual WAN](virtual-wan-faq.md)
