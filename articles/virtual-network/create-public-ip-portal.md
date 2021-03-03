---
title: Tworzenie publicznego adresu IP — Azure Portal
description: Dowiedz się, jak utworzyć publiczny adres IP w Azure Portal
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: 5c5650d896442f10846e16903a1231010d032a44
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675212"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Tworzenie publicznego adresu IP przy użyciu Azure Portal

W tym artykule pokazano, jak utworzyć zasób publicznego adresu IP przy użyciu Azure Portal. 

Aby uzyskać więcej informacji na temat zasobów, z którymi można powiązać ten publiczny adres IP, i różnice między podstawowymi a standardowymi jednostkami SKU, zobacz [publiczne adresy IP](./public-ip-addresses.md). 

Ten artykuł koncentruje się na adresach IPv4. Aby uzyskać więcej informacji na temat adresów IPv6, zobacz [IPv6 dla sieci wirtualnej platformy Azure](./ipv6-overview.md).

# <a name="standard-sku"></a>[**Standardowy SKU**](#tab/option-create-public-ip-standard-zones)

Wykonaj następujące kroki, aby utworzyć strefę standardowa — nadmiarowy publiczny adres IP o nazwie **myStandardZRPublicIP**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób**. 
3. W polu wyszukiwania wprowadź **publiczny adres IP**. W wynikach wyszukiwania wybierz pozycję **publiczny adres IP** .
4. Na stronie **publiczny adres IP** wybierz pozycję **Utwórz**.
5. Na stronie **Tworzenie publicznego adresu IP** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                       |
    | ---                     | ---                         |
    | Wersja protokołu IP              | Wybierz protokół IPv4                 |    
    | SKU                     | Wybierz pozycję **standardowa**         |
    | Warstwy                   | Wybierz pozycję **regionalne**         |
    | Nazwa                    | Wprowadź **myStandardZRPublicIP**          |
    | Przypisanie adresu IP   | Uwaga Ta opcja jest zablokowana jako "statyczna"                                        |
    | Preferencja routingu      | Pozostaw wartość domyślną **sieci Microsoft**. </br> Aby uzyskać więcej informacji o preferencjach routingu, zobacz [co to jest preferencja routingu (wersja zapoznawcza)?](./routing-preference-overview.md). |
    | Limit czasu bezczynności (minuty)  | Pozostaw wartość domyślną **4**.        |
    | Etykieta nazwy DNS          | Pozostaw pustą wartość.    |
    | Subskrypcja            | Wybierz subskrypcję.   |
    | Grupa zasobów          | Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę **zasobu**. </br> Wybierz przycisk **OK**. |
    | Lokalizacja                | Wybierz **Wschodnie stany USA 2**      |
    | Strefa dostępności       | Wybierz **strefowo nadmiarowe**, brak strefy lub wybierz konkretną strefę (patrz Uwaga poniżej) |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Utwórz standardowy adres IP w Azure Portal" border="false":::

> [!NOTE]
> Te wybory są prawidłowe w regionach, w których [strefy dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones). </br>
W tych regionach można wybrać określoną strefę, chociaż nie będzie ona odporna na awarie stref. </br> Aby uzyskać więcej informacji na temat stref dostępności, zobacz temat [strefy dostępności — Omówienie](https://docs.microsoft.com/azure/availability-zones/az-overview).

\* = Warstwa odnosi się do funkcji [międzyregionowego modułu równoważenia obciążenia](../load-balancer/cross-region-overview.md) , która jest obecnie dostępna w wersji zapoznawczej.

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-create-public-ip-basic)

W tej sekcji Utwórz podstawowy publiczny adres IP o nazwie **myBasicPublicIP**. 

> [!NOTE]
> Podstawowe publiczne adresy IP nie obsługują stref dostępności.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób**. 
3. W polu wyszukiwania wprowadź **publiczny adres IP**. W wynikach wyszukiwania wybierz pozycję **publiczny adres IP** .
4. Na stronie **publiczny adres IP** wybierz pozycję **Utwórz**.
5. Na stronie **Tworzenie publicznego adresu IP** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                       |
    | ---                     | ---                         |
    | Wersja protokołu IP              | Wybierz protokół IPv4                 |    
    | SKU                     | Wybierz pozycję **podstawowa**         |
    | Nazwa                    | Wprowadź *myBasicPublicIP*          |
    | Preferencja routingu      | Pozostaw wartość domyślną **sieci Microsoft**. </br> Aby uzyskać więcej informacji o preferencjach routingu, zobacz [co to jest preferencja routingu (wersja zapoznawcza)?](./routing-preference-overview.md). |
    | Przypisanie adresu IP   | Wybierz pozycję **static** (patrz Uwaga poniżej)                                     |
    | Limit czasu bezczynności (minuty)  | Pozostaw wartość domyślną **4**.       |
    | Etykieta nazwy DNS          | Pozostaw pustą wartość    |
    | Subskrypcja            | Wybierz subskrypcję.   |
    | Grupa zasobów          | Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę **zasobu**. </br> Wybierz przycisk **OK**. |
    | Lokalizacja                | Wybierz **Wschodnie stany USA 2**      |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Utwórz standardowy adres IP w Azure Portal" border="false":::

Jeśli jest akceptowalny do zmiany adresu IP z upływem czasu, można wybrać opcję **dynamicznego** przypisywania adresów IP.

---

## <a name="additional-information"></a>Dodatkowe informacje 

Aby uzyskać więcej informacji na temat poszczególnych pól wymienionych powyżej, zobacz [Zarządzanie publicznymi adresami IP](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Następne kroki
- Skojarz [publiczny adres IP z maszyną wirtualną](./associate-public-ip-address-vm.md#azure-portal)
- Dowiedz się więcej o [publicznych adresach IP](./public-ip-addresses.md#public-ip-addresses) na platformie Azure.
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address).