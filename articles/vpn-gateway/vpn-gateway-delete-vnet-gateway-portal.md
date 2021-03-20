---
title: 'Azure VPN Gateway: usuwanie bramy: Portal'
description: Usuwanie bramy sieci wirtualnej przy użyciu Azure Portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/10/2021
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 413fd8c7f03ef44abe4bece39ca717c533dea66b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376392"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Usuwanie bramy sieci wirtualnej przy użyciu portalu

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasyczny)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Ten artykuł pomaga usunąć bramę sieci wirtualnej. Istnieje kilka różnych metod, które można wykonać, aby usunąć bramę dla konfiguracji bramy sieci VPN.

* Jeśli chcesz usunąć wszystko i zacząć od początku, tak jak w przypadku środowiska testowego, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie. Ta metoda jest zalecana tylko wtedy, gdy nie chcesz zachować żadnych zasobów w grupie zasobów. Nie można selektywnie usunąć tylko kilku zasobów przy użyciu tego podejścia.

* Jeśli chcesz zachować część zasobów w grupie zasobów, usuwanie bramy sieci wirtualnej staje się nieco bardziej skomplikowane. Aby można było usunąć bramę sieci wirtualnej, należy najpierw usunąć wszystkie zasoby zależne od bramy. Kroki, które należy wykonać, zależą od typu utworzonych połączeń i zasobów zależnych dla każdego połączenia.

> [!IMPORTANT]
> Kroki podane w tym artykule mają zastosowanie do modelu wdrażania przy użyciu usługi Resource Manager. Aby usunąć bramę sieci VPN wdrożoną przy użyciu klasycznego modelu wdrażania, wykonaj kroki opisane w artykule [usuwanie bramy: klasycznej](vpn-gateway-delete-vnet-gateway-classic-powershell.md) .

## <a name="delete-a-vpn-gateway"></a>Usuwanie bramy VPN Gateway

Aby usunąć bramę sieci wirtualnej, należy najpierw usunąć wszystkie zasoby, które odnoszą się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

W tym momencie Brama sieci wirtualnej zostanie usunięta.

### <a name="to-delete-additional-resources"></a>Aby usunąć dodatkowe zasoby

Poniższe kroki ułatwiają usunięcie wszelkich zasobów, które nie są już używane.

#### <a name="to-delete-the-local-network-gateway"></a>Aby usunąć bramę sieci lokalnej

1. W obszarze **wszystkie zasoby** Znajdź bramy sieci lokalnej skojarzone z poszczególnymi połączeniami.
1. W bloku **Przegląd** dla bramy sieci lokalnej kliknij pozycję **Usuń**.

#### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Aby usunąć zasób publicznego adresu IP dla bramy

1. W obszarze **wszystkie zasoby** Znajdź zasób publicznego adresu IP, który został skojarzony z bramą. Jeśli Brama sieci wirtualnej była aktywna — zostaną wyświetlone dwa publiczne adresy IP.
1. Na stronie **Przegląd** dla publicznego adresu IP kliknij przycisk **Usuń**, a następnie pozycję **tak** , aby potwierdzić.

#### <a name="to-delete-the-gateway-subnet"></a>Aby usunąć podsieć bramy

1. W obszarze **wszystkie zasoby** Znajdź sieć wirtualną. 
1. W bloku **podsieci** kliknij pozycję **GatewaySubnet**, a następnie kliknij pozycję **Usuń**. 
1. Kliknij przycisk **tak** , aby potwierdzić, że chcesz usunąć podsieć bramy.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Usuwanie bramy sieci VPN przez usunięcie grupy zasobów

Jeśli nie chcesz przechowywać żadnych zasobów w grupie zasobów i chcesz zacząć od razu, możesz usunąć całą grupę zasobów. Jest to szybka metoda usuwania wszystkiego. Poniższe kroki dotyczą tylko modelu wdrażania Menedżer zasobów.

1. W obszarze **wszystkie zasoby** Znajdź grupę zasobów i kliknij, aby otworzyć blok.
1. Kliknij polecenie **Usuń**. W bloku Usuń Wyświetl zasoby, których to dotyczy. Upewnij się, że chcesz usunąć wszystkie te zasoby. W przeciwnym razie wykonaj kroki opisane w sekcji Usuwanie bramy sieci VPN w górnej części tego artykułu.
1. Aby to zrobić, wpisz nazwę grupy zasobów, która ma zostać usunięta, a następnie kliknij przycisk **Usuń**.
