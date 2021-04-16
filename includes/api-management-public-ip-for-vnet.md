---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531651"
---
* **Publiczny adres [IPv4 standardowej wersji SKU,](../articles/virtual-network/public-ip-addresses.md#standard)** jeśli klient korzysta z interfejsu API w wersji 2021-01-01-preview lub nowszej. Zasób publicznego adresu IP jest wymagany podczas konfigurowania sieci wirtualnej na potrzeby dostępu zewnętrznego lub wewnętrznego. W przypadku wewnętrznej sieci wirtualnej publiczny adres IP jest używany tylko do operacji zarządzania. Dowiedz się więcej [o adresach IP API Management](../articles/api-management/api-management-howto-ip-addresses.md).

  * Adres IP musi znajdować się w tym samym regionie i subskrypcji co API Management i sieci wirtualnej.

  * Wartość adresu IP jest przypisywana jako wirtualny publiczny adres IPv4 API Management w tym regionie. 

  * Podczas zmiany z zewnętrznej na wewnętrzną sieć wirtualną (lub odwrotnie), zmiany podsieci w sieci lub aktualizowania stref dostępności dla wystąpienia usługi API Management należy skonfigurować inny publiczny adres IP. 

  > [!IMPORTANT]
  > Obecnie podczas tworzenia lub aktualizowania Azure Portal interfejsu API w wersji 2021-01-0 API Management 1 (wersja zapoznawcza). Tę wersję interfejsu API można określić przy użyciu szablonu Azure Resource Manager lub API Management API REST. Interfejs wiersza polecenia platformy Azure Azure PowerShell obecnie obsługuje interfejs API w wersji 2020-12-01.
