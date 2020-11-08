---
title: 'Samouczek: Tworzenie połączenia punkt-lokacja z platformą Azure za pomocą usługi Azure Virtual WAN'
description: Z tego samouczka dowiesz się, jak utworzyć połączenie punkt-lokacja VPN z platformą Azure w usłudze Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/06/2020
ms.author: cherylmc
ms.openlocfilehash: 0671e41703ecc4b8580c8439eec5bac954b4dbe0
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368835"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Samouczek: Tworzenie połączenia sieci VPN użytkownika przy użyciu wirtualnej sieci WAN platformy Azure

W tym samouczku pokazano, w jaki sposób przy użyciu usługi Azure Virtual WAN utworzyć połączenie z zasobami na platformie Azure za pośrednictwem połączenia sieci VPN protokołu IPsec/IKE (IKEv2) lub OpenVPN. Ten typ połączenia wymaga, aby klient sieci VPN był skonfigurowany na komputerze klienckim. Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie wirtualnej sieci WAN
> * Tworzenie konfiguracji P2S
> * Tworzenie koncentratora wirtualnego
> * Określ serwery DNS
> * Generowanie i instalowanie pakietu konfiguracyjnego profilu klienta sieci VPN
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

## <a name="specify-dns-server"></a><a name="dns"></a>Określ serwer DNS

To ustawienie można skonfigurować podczas tworzenia centrum lub w późniejszym czasie. Aby zmodyfikować, zlokalizuj centrum wirtualne. W obszarze **Sieć VPN użytkownika (wskaż lokację)** wybierz pozycję **Konfiguruj** i wprowadź adresy IP serwera DNS w polu tekstowym **niestandardowe serwery DNS** . Można określić maksymalnie 5 serwerów DNS.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="niestandardowy serwer DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-and-install-client-profile-package"></a><a name="download"></a>Generowanie i instalowanie pakietu profilu klienta

Wygeneruj pakiet profilu klienta sieci VPN, aby skonfigurować klientów sieci VPN.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-vpn-clients"></a>Konfigurowanie klientów sieci VPN

Użyj pobranego profilu, aby skonfigurować klientów sieci VPN dostępu zdalnego. Procedura dla każdego systemu operacyjnego jest inna. Postępuj zgodnie z instrukcjami odnoszącymi się do systemu.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Wyświetlanie wirtualnej sieci WAN

1. Przejdź do wirtualnej sieci WAN.
1. Na stronie **Przegląd** każdy punkt na mapie reprezentuje centrum.
1. W sekcji **centra i połączenia** można wyświetlić stan centrum, lokację, region, stan połączenia sieci VPN oraz liczbę bajtów do i wychodzące.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Oczyszczanie zasobów

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wirtualnej sieci WAN, zobacz:

> [!div class="nextstepaction"]
> * [Często zadawane pytania dotyczące usługi Virtual WAN](virtual-wan-faq.md)
