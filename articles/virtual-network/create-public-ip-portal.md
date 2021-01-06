---
title: Tworzenie publicznego adresu IP — Azure Portal
description: Dowiedz się, jak utworzyć publiczny adres IP w Azure Portal
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: bd35d3288591a01c14e306960601eef6dea7e761
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97954145"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Szybki Start: Tworzenie publicznego adresu IP przy użyciu Azure Portal

W tym artykule pokazano, jak utworzyć zasób publicznego adresu IP przy użyciu Azure Portal. Aby uzyskać więcej informacji na temat zasobów, z którymi może być skojarzone, różnica między podstawową i standardową jednostką SKU i innymi powiązanymi informacjami, zobacz [publiczne adresy IP](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  Na potrzeby tego przykładu będziemy skupić się tylko na adresach IPv4. Aby uzyskać więcej informacji na temat adresów IPv6, zobacz [IPv6 dla sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

# <a name="standard-sku---using-zones"></a>[**Standardowa jednostka SKU — używanie stref**](#tab/option-create-public-ip-standard-zones)

Wykonaj następujące kroki, aby utworzyć strefę standardowa — nadmiarowy publiczny adres IP o nazwie **myStandardZRPublicIP**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób**. 
3. W polu wyszukiwania wpisz *publiczny adres IP*.
4. W wynikach wyszukiwania wybierz pozycję **publiczny adres IP**. Następnie na stronie **publiczny adres IP** wybierz pozycję **Utwórz**.
5. Na stronie **Tworzenie publicznego adresu IP** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                       |
    | ---                     | ---                         |
    | Wersja protokołu IP              | Wybierz protokół IPv4                 |    
    | Jednostka SKU                     | Wybierz pozycję **standardowa**         |
    | Warstwa (jeśli pokazana *)                  | Wybierz pozycję **regionalne**         |
    | Nazwa                    | Wprowadź *myStandardZRPublicIP*          |
    | Przypisanie adresu IP   | Uwaga Ta wartość zostanie zablokowana jako "statyczna"                                        |
    | Limit czasu bezczynności (minuty)  | Pozostaw wartość 4        |
    | Etykieta nazwy DNS          | Pozostaw wartość pustą    |
    | Subskrypcja            | Wybierz subskrypcję.   |
    | Grupa zasobów          | Wybierz pozycję **Utwórz nowy** , wprowadź nazwę zasobu, a następnie wybierz przycisk **OK** . |
    | Lokalizacja                | Wybierz **Wschodnie stany USA 2**      |
    | Strefa dostępności       | Wybierz **strefę nadmiarową** lub wybierz konkretną strefę (patrz Uwaga poniżej) |

Należy pamiętać, że są to tylko prawidłowe wybory w regionach z [strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).  (Można również wybrać określoną strefę w tych regionach, chociaż nie będzie ona odporna na awarie strefy).

\* = Warstwa odnosi się do [Międzyregionowej funkcji Load Balancer](https://docs.microsoft.com/azure/load-balancer/cross-region-overview) , obecnie w wersji zapoznawczej.

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-create-public-ip-basic)

Wykonaj następujące kroki, aby utworzyć podstawowy statyczny publiczny adres IP o nazwie **myBasicPublicIP**.  Podstawowe publiczne adresy IP nie mają koncepcji dotyczących stref dostępności.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób**. 
3. W polu wyszukiwania wpisz *publiczny adres IP*.
4. W wynikach wyszukiwania wybierz pozycję **publiczny adres IP**. Następnie na stronie **publiczny adres IP** wybierz pozycję **Utwórz**.
5. Na stronie **Tworzenie publicznego adresu IP** wprowadź lub wybierz następujące informacje: 

    | Ustawienie                 | Wartość                       |
    | ---                     | ---                         |
    | Wersja protokołu IP              | Wybierz protokół IPv4                 |    
    | Jednostka SKU                     | Wybierz pozycję **standardowa**         |
    | Nazwa                    | Wprowadź *myBasicPublicIP*          |
    | Przypisanie adresu IP   | Wybierz pozycję **static** (patrz Uwaga poniżej)                                     |
    | Limit czasu bezczynności (minuty)  | Pozostaw wartość 4        |
    | Etykieta nazwy DNS          | Pozostaw wartość pustą    |
    | Subskrypcja            | Wybierz subskrypcję.   |
    | Grupa zasobów          | Wybierz pozycję **Utwórz nowy** , wprowadź nazwę zasobu, a następnie wybierz przycisk **OK** . |
    | Lokalizacja                | Wybierz **Wschodnie stany USA 2**      |

Jeśli jest akceptowalny do zmiany adresu IP z upływem czasu, można wybrać opcję **dynamicznego** przypisywania adresów IP.

---

## <a name="additional-information"></a>Dodatkowe informacje 

Aby uzyskać szczegółowe informacje na temat poszczególnych pól wymienionych powyżej, zobacz [Zarządzanie publicznymi adresami IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Następne kroki
- Skojarz [publiczny adres IP z maszyną wirtualną](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal)
- Dowiedz się więcej o [publicznych adresach IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) na platformie Azure.
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address).