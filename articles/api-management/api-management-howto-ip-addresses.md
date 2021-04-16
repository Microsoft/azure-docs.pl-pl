---
title: Adresy IP usługi Azure API Management Service | Microsoft Docs
description: Dowiedz się, jak pobierać adresy IP usługi Azure API Management i kiedy się zmieniają.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: 5939292b6e810634723fada17521bb227764b989
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534034"
---
# <a name="ip-addresses-of-azure-api-management"></a>Adresy IP usługi Azure API Management

W tym artykule opisano sposób pobierania adresów IP usługi Azure API Management Service. Adresy IP mogą być publiczne lub prywatne, jeśli usługa znajduje się w sieci wirtualnej.

Za pomocą adresów IP można tworzyć reguły zapory, filtrować ruch przychodzący do usług zaplecza lub ograniczać ruch wychodzący.

## <a name="ip-addresses-of-api-management-service"></a>Adresy IP API Management usługi

Każde API Management usługi w warstwie Deweloper, Podstawowa, Standardowa lub Premium ma publiczne adresy IP, które są wyłączne tylko dla tego wystąpienia usługi (nie są one udostępniane innym zasobom). 

Adresy IP można pobrać z pulpitu nawigacyjnego przeglądu zasobu w Azure Portal.

![API Management IP](media/api-management-howto-ip-addresses/public-ip.png)

Można je również pobrać programowo za pomocą następującego wywołania interfejsu API:

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

We [wdrożeniach w wielu regionach](api-management-howto-deploy-multi-region.md)każde wdrożenie regionalne ma jeden publiczny adres IP.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>Adresy IP usługi API Management w sieci wirtualnej

Jeśli Twoja API Management znajduje się w sieci wirtualnej, będzie ona mieć dwa typy adresów IP — publiczne i prywatne.

Publiczne adresy IP są używane do komunikacji wewnętrznej na porcie — do zarządzania konfiguracją (na przykład za pośrednictwem `3443` Azure Resource Manager). W konfiguracji zewnętrznej sieci wirtualnej są one również używane dla ruchu interfejsu API środowiska uruchomieniowego. Gdy żądanie jest wysyłane z usługi API Management do zaplecza dostępnego publicznie (dostępnego z Internetu), publiczny adres IP będzie widoczny jako źródło żądania.

Prywatne wirtualne adresy IP (VIP), dostępne tylko w trybie wewnętrznej sieci wirtualnej, są używane do nawiązywania połączeń z poziomu sieci do punktów końcowych usługi API Management — bram, portalu dla deweloperów i płaszczyzny zarządzania w celu uzyskania bezpośredniego dostępu do interfejsu API.  [](api-management-using-with-internal-vnet.md) Można ich używać do konfigurowania rekordów DNS w sieci.

Adresy obu typów zostaną wyświetlonych w Azure Portal i w odpowiedzi wywołania interfejsu API:

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

API Management publiczny adres IP dla połączeń spoza sieci wirtualnej i prywatny adres IP dla połączeń w sieci wirtualnej.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Adresy IP usługi warstwy Zużycie API Management usługi

Jeśli Twoja API Management jest usługą w warstwie Zużycie, nie ma dedykowanego adresu IP. Usługa w warstwie Zużycie działa w infrastrukturze udostępnionej i bez deterministycznego adresu IP. 

Na potrzeby ograniczeń ruchu możesz użyć zakresu adresów IP centrów danych platformy Azure. Zapoznaj się [z artykułem Azure Functions dokumentacji,](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) aby uzyskać dokładne instrukcje.

## <a name="changes-to-the-ip-addresses"></a>Zmiany adresów IP

W warstwach Deweloper, Podstawowa, Standardowa i Premium usługi API Management publiczne adresy IP (VIP) są statyczne przez okres istnienia usługi, z następującymi wyjątkami:

* Usługa zostanie usunięta, a następnie ponownie utworzona.
* Subskrypcja usługi jest [wstrzymana lub](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) [ostrzegana](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (na przykład w przypadku braku płatności), a następnie przywracana.
* Usługa Azure Virtual Network jest dodawana do usługi lub usuwana z tej usługi.
* API Management jest przełączana między trybem wdrażania zewnętrznej i wewnętrznej sieci wirtualnej.

We [wdrożeniach w wielu regionach](api-management-howto-deploy-multi-region.md)regionalny adres IP zmienia się, jeśli region zostanie opuszczony, a następnie przywrócony. Regionalny adres IP zmienia się również po włączeniu, dodaniu lub usunięciu [stref dostępności.](zone-redundancy.md)
