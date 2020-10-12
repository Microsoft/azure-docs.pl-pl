---
title: Adresy IP usługi API Management Azure | Microsoft Docs
description: Dowiedz się, jak pobrać adresy IP usługi Azure API Management i zmienić je.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 45501fee9ae6ff47643a1ed197a07c4ba598e981
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80047746"
---
# <a name="ip-addresses-of-azure-api-management"></a>Adresy IP API Management platformy Azure

W tym artykule opisano sposób pobierania adresów IP usługi Azure API Management. Adresy IP mogą być publiczne lub prywatne, jeśli usługa znajduje się w sieci wirtualnej.

Przy użyciu adresów IP można tworzyć reguły zapory, filtrować ruch przychodzący do usług zaplecza lub ograniczać ruch wychodzący.

## <a name="ip-addresses-of-api-management-service"></a>Adresy IP usługi API Management

Każde wystąpienie usługi API Management w warstwie Deweloper, podstawowa, standardowa lub Premium ma publiczne adresy IP, które nie są dostępne wyłącznie dla tego wystąpienia usługi (nie są one udostępniane innym zasobom). 

Adresy IP można pobrać z pulpitu nawigacyjnego przeglądu zasobu w Azure Portal.

![API Management adres IP](media/api-management-howto-ip-addresses/public-ip.png)

Można je również programowo pobrać przy użyciu następującego wywołania interfejsu API:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Publiczne adresy IP będą częścią odpowiedzi:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

W [przypadku wdrożeń w wielu regionach](api-management-howto-deploy-multi-region.md)każde wdrożenie regionalne ma jeden publiczny adres IP.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>Adresy IP usługi API Management w sieci wirtualnej

Jeśli usługa API Management znajduje się w sieci wirtualnej, będzie mieć dwa typy adresów IP — Public i Private.

Publiczne adresy IP są używane do komunikacji wewnętrznej na porcie `3443` — do zarządzania konfiguracją (na przykład przez Azure Resource Manager). W konfiguracji zewnętrznej sieci wirtualnej są one również używane na potrzeby ruchu interfejsu API środowiska uruchomieniowego. Gdy żądanie jest wysyłane z API Management do publicznego zaplecza (dostępnego z Internetu), publiczny adres IP będzie widoczny jako źródło żądania.

Prywatne wirtualne adresy IP (VIP), dostępne **tylko** w [trybie wewnętrznej sieci wirtualnej](api-management-using-with-internal-vnet.md), służą do nawiązywania połączenia z siecią w celu API Management punktów końcowych — bram, portalu deweloperów i płaszczyzny zarządzania do bezpośredniego dostępu do interfejsu API. Można ich używać do konfigurowania rekordów DNS w sieci.

Zobaczysz adresy obu typów w Azure Portal i w odpowiedzi na wywołanie interfejsu API:

![API Management w adresie IP sieci wirtualnej](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management używa publicznego adresu IP dla połączeń poza siecią wirtualną i prywatnego adresu IP dla połączeń w sieci wirtualnej.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Adresy IP dla warstwy zużycia API Management usługi

Jeśli usługa API Management jest usługą warstwy zużycia, nie ma dedykowanego adresu IP. Usługa warstwa użycia jest uruchamiana w infrastrukturze udostępnionej i bez deterministycznych adresów IP. 

W celu ograniczenia ruchu można użyć zakresu adresów IP centrów danych platformy Azure. Szczegółowe instrukcje można znaleźć w [artykule dotyczącym dokumentacji Azure Functions](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) .

## <a name="changes-to-the-ip-addresses"></a>Zmiany adresów IP

W warstwach Deweloper, podstawowa, standardowa i Premium API Management publiczne adresy IP (VIP) są statyczne przez okres istnienia usługi, z następującymi wyjątkami:

* Usługa została usunięta, a następnie ponownie utworzona.
* Subskrypcja usługi jest [zawieszona](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) lub [ostrzegana](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (na przykład w przypadku niepłatności), a następnie przywrócona.
* Usługa Virtual Network platformy Azure została dodana do usługi lub usunięta z niej.
* Usługa API Management jest przełączana między zewnętrznym i wewnętrznym trybem wdrożenia sieci wirtualnej.

W przypadku [wdrożeń z obsługą wiele regionów](api-management-howto-deploy-multi-region.md)regionalny adres IP zmienia się, jeśli region jest opuszczone, a następnie przywracany.
