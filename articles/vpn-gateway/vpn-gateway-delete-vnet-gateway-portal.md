---
title: 'Azure VPN Gateway: usuwanie bramy: Portal'
description: Usuń bramę sieci wirtualnej przy użyciu Azure Portal w modelu wdrażania Menedżer zasobów.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 09/02/2020
ms.author: cherylmc
ms.topic: how-to
ms.openlocfilehash: 7a7eb87f57676b469203e45e48b6f863cdf894c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400657"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Usuwanie bramy sieci wirtualnej przy użyciu portalu

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasyczny)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Ten artykuł zawiera instrukcje dotyczące usuwania bram sieci VPN platformy Azure wdrożonych przy użyciu modelu wdrażania Menedżer zasobów. Istnieje kilka różnych metod, które można wykonać, aby usunąć bramę sieci wirtualnej dla konfiguracji bramy sieci VPN.

- Jeśli chcesz usunąć wszystko i zacząć od początku, tak jak w przypadku środowiska testowego, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje usunięcie wszystkich zasobów w grupie. Ta metoda jest zalecana tylko wtedy, gdy nie chcesz zachować żadnych zasobów w grupie zasobów. Nie można selektywnie usunąć tylko kilku zasobów przy użyciu tego podejścia.

- Jeśli chcesz zachować część zasobów w grupie zasobów, usuwanie bramy sieci wirtualnej staje się nieco bardziej skomplikowane. Aby można było usunąć bramę sieci wirtualnej, należy najpierw usunąć wszystkie zasoby zależne od bramy. Kroki, które należy wykonać, zależą od typu utworzonych połączeń i zasobów zależnych dla każdego połączenia.

> [!IMPORTANT]
> Poniższe instrukcje opisują sposób usuwania bram sieci VPN platformy Azure wdrożonych przy użyciu modelu wdrażania Menedżer zasobów. Aby usunąć bramę sieci VPN wdrożoną przy użyciu klasycznego modelu wdrażania, użyj Azure PowerShell zgodnie z opisem w [tym miejscu](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>Usuwanie bramy VPN Gateway

Aby usunąć bramę sieci wirtualnej, należy najpierw usunąć wszystkie zasoby, które odnoszą się do bramy sieci wirtualnej. Zasoby muszą zostać usunięte w określonej kolejności z powodu zależności.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

W tym momencie Brama sieci wirtualnej zostanie usunięta. Następne kroki pomagają usunąć wszystkie zasoby, które nie są już używane.

### <a name="to-delete-the-local-network-gateway"></a>Aby usunąć bramę sieci lokalnej

1. W obszarze **wszystkie zasoby**Znajdź bramy sieci lokalnej skojarzone z poszczególnymi połączeniami.
2. W bloku **Przegląd** dla bramy sieci lokalnej kliknij pozycję **Usuń**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Aby usunąć zasób publicznego adresu IP dla bramy

1. W obszarze **wszystkie zasoby**Znajdź zasób publicznego adresu IP, który został skojarzony z bramą. Jeśli Brama sieci wirtualnej była aktywna — zostaną wyświetlone dwa publiczne adresy IP. 
2. Na stronie **Przegląd** dla publicznego adresu IP kliknij przycisk **Usuń**, a następnie pozycję **tak** , aby potwierdzić.

### <a name="to-delete-the-gateway-subnet"></a>Aby usunąć podsieć bramy

1. W obszarze **wszystkie zasoby**Znajdź sieć wirtualną. 
2. W bloku **podsieci** kliknij pozycję **GatewaySubnet**, a następnie kliknij pozycję **Usuń**. 
3. Kliknij przycisk **tak** , aby potwierdzić, że chcesz usunąć podsieć bramy.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>Usuwanie bramy sieci VPN przez usunięcie grupy zasobów

Jeśli nie chcesz przechowywać żadnych zasobów w grupie zasobów i chcesz zacząć od razu, możesz usunąć całą grupę zasobów. Jest to szybka metoda usuwania wszystkiego. Poniższe kroki dotyczą tylko modelu wdrażania Menedżer zasobów.

1. W obszarze **wszystkie zasoby**Znajdź grupę zasobów i kliknij, aby otworzyć blok.
2. Kliknij polecenie **Usuń**. W bloku Usuń Wyświetl zasoby, których to dotyczy. Upewnij się, że chcesz usunąć wszystkie te zasoby. W przeciwnym razie wykonaj kroki opisane w sekcji Usuwanie bramy sieci VPN w górnej części tego artykułu.
3. Aby to zrobić, wpisz nazwę grupy zasobów, która ma zostać usunięta, a następnie kliknij przycisk **Usuń**.
