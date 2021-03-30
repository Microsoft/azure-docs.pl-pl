---
title: 'Wirtualna sieć WAN: Tworzenie tabeli tras koncentratora wirtualnego do urządzenie WUS: Azure Portal'
description: Tabela tras wirtualnego koncentratora sieci WAN do kierowania ruchu do sieciowego urządzenia wirtualnego przy użyciu portalu.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8de7ad2808e5319819410b3125472e28496647b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91267146"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>Tworzenie tabeli tras wirtualnego centrum sieci WAN dla urządzeń WUS: Azure Portal

W tym artykule opisano sposób kierowania ruchu z rozgałęzienia (lokacji lokalnej) połączonej z koncentratorem wirtualnej sieci WAN z siecią wirtualną szprychy za pośrednictwem sieciowego urządzenia wirtualnego (urządzenie WUS).

![Diagram usługi Virtual WAN](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Sprawdź, czy zostały spełnione następujące kryteria:

*  Masz wirtualne urządzenie sieciowe (urządzenie WUS). Sieciowe urządzenie wirtualne to oferowane przez siebie oprogramowanie innych firm, które jest zazwyczaj inicjowane z portalu Azure Marketplace w sieci wirtualnej.

    * Prywatny adres IP musi być przypisany do interfejsu sieciowego urządzenie WUS.

    * URZĄDZENIE WUS nie jest wdrożona w koncentratorze wirtualnym. Należy ją wdrożyć w oddzielnej sieci wirtualnej.

    *  Z siecią wirtualną urządzenie WUS może być połączona jedna lub wiele sieci wirtualnych. W tym artykule odwołujemy się do sieci wirtualnej urządzenie WUS jako "pośrednia Sieć wirtualna". Te sieci wirtualne mogą być połączone z siecią wirtualną urządzenie WUS przy użyciu wirtualnych sieci równorzędnych. Łącza komunikacji równorzędnej sieci wirtualnych są przedstawiane za pomocą czarnych strzałek na powyższym rysunku między siecią wirtualną 1, VNet 2 i urządzenie WUS sieci wirtualnej.
*  Utworzono dwie sieci wirtualne. Będą one używane jako szprychy sieci wirtualnych.

    * Przestrzenie adresowe sieci wirtualnej szprych: VNet1:10.0.2.0/24 i VNet2:10.0.3.0/24. Jeśli potrzebujesz informacji na temat sposobu tworzenia sieci wirtualnej, zobacz [Tworzenie sieci wirtualnej](../virtual-network/quick-create-portal.md).

    * Upewnij się, że w żadnym z sieci wirtualnych nie ma żadnych bram sieci wirtualnej.

    * Sieci wirtualnych nie wymaga podsieci bramy.

## <a name="1-sign-in"></a><a name="signin"></a>1. Zaloguj się

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. Tworzenie wirtualnej sieci WAN

Utwórz wirtualną sieć WAN. Użyj następujących przykładowych wartości:

* **Nazwa wirtualnej sieci WAN:** myVirtualWAN
* **Grupa zasobów:** testRG
* **Lokalizacja:** Zachodnie stany USA

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. Tworzenie centrum

Utwórz centrum. Użyj następujących przykładowych wartości:

* **Lokalizacja:** Zachodnie stany USA
* **Nazwa:** westushub
* **Prywatna przestrzeń adresowa centrum:** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. Tworzenie i stosowanie tabeli tras centrum

Zaktualizuj centrum za pomocą tabeli tras centrum. Użyj następujących przykładowych wartości:

* **Przestrzenie adresowe sieci wirtualnej szprych:** (VNet1 i VNet2) 10.0.2.0/24 i 10.0.3.0/24
* **Prywatny adres IP interfejsu sieciowego urządzenie WUS DMZ:** 10.0.4.5

1. Przejdź do wirtualnej sieci WAN.
2. Kliknij centrum, dla którego chcesz utworzyć tabelę tras.
3. Kliknij przycisk **...**, a następnie kliknij pozycję **Edytuj centrum wirtualne**.
4. Na stronie **Edytowanie wirtualnego centrum** przewiń w dół i zaznacz pole wyboru **Użyj tabeli do routingu**.
5. W kolumnie **jeśli prefiks lokalizacji docelowej jest** kolumną Dodaj przestrzenie adresowe. W kolumnie **Wyślij do następnego przeskoku** Dodaj prywatny adres IP urządzenie WUS strefy DMZ.

   > [!NOTE]
   > Sieć DMZ urządzenie WUS ma zastosowanie do lokalnego centrum.
   
6. Kliknij przycisk **Potwierdź** , aby zaktualizować zasób centrum przy użyciu ustawień tabeli tras.

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. Tworzenie połączeń sieci wirtualnej

Utwórz połączenie z siecią wirtualną z każdej pośredniej sieci wirtualnej szprychy (VNet1 i VNet2) do centrum. Te połączenia sieci wirtualnej są przedstawiane za pomocą niebieskich strzałek na powyższym rysunku. Następnie utwórz połączenie VNet z sieci wirtualnej urządzenie WUS do koncentratora (czarna strzałka na rysunku).

 W tym kroku można użyć następujących wartości:

| Nazwa sieci wirtualnej| Nazwa połączenia|
| --- | --- |
| Sieć wirtualna 1 | testconnection1 |
| Sieć wirtualna 2 | testconnection2 |
| NVAVNet | testconnection3 |

Powtórz poniższą procedurę dla każdej sieci wirtualnej, która ma zostać nawiązane połączenie.

1. Na stronie sieci wirtualnej WAN kliknij pozycję **Połączenia sieci wirtualnych**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.
4. Kliknij przycisk **OK** , aby utworzyć połączenie.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
