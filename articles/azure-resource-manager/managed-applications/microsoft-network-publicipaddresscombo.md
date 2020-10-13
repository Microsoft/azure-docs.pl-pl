---
title: PublicIpAddressCombo — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Network. PublicIpAddressCombo dla Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 5def6db9d551b3882204c9f997f164a0df7ac223
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87063285"
---
# <a name="microsoftnetworkpublicipaddresscombo-ui-element"></a>Element interfejsu użytkownika Microsoft. Network. PublicIpAddressCombo

Grupa kontrolek służących do wybierania nowego lub istniejącego publicznego adresu IP.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

![Microsoft.Network.PublicIpAddressCombo](./media/managed-application-elements/microsoft-network-publicipaddresscombo.png)

- Jeśli użytkownik wybierze opcję "Brak" dla publicznego adresu IP, pole tekstowe etykieta nazwy domeny jest ukryte.
- Jeśli użytkownik wybierze istniejący publiczny adres IP, pole tekstowe etykieta nazwy domeny jest wyłączone. Jego wartość to etykieta nazwy domeny wybranego adresu IP.
- Sufiks nazwy domeny (na przykład westus.cloudapp.azure.com) są automatycznie aktualizowane na podstawie wybranej lokalizacji.

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Network.PublicIpAddressCombo",
  "label": {
    "publicIpAddress": "Public IP address",
    "domainNameLabel": "Domain name label"
  },
  "toolTip": {
    "publicIpAddress": "",
    "domainNameLabel": ""
  },
  "defaultValue": {
    "publicIpAddressName": "ip01",
    "domainNameLabel": "mydomain"
  },
  "constraints": {
    "required": {
      "domainNameLabel": true
    }
  },
  "options": {
    "hideNone": false,
    "hideDomainNameLabel": false,
    "hideExisting": false,
    "zone": 3
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

Jeśli użytkownik nie wybierze publicznego adresu IP, kontrolka zwróci następujące dane wyjściowe:

```json
{
  "newOrExistingOrNone": "none"
}
```

Jeśli użytkownik wybierze nowy lub istniejący adres IP, kontrolka zwróci następujące dane wyjściowe:

```json
{
  "name": "ip01",
  "resourceGroup": "rg01",
  "domainNameLabel": "mydomain",
  "publicIPAllocationMethod": "Dynamic",
  "sku": "Basic",
  "newOrExistingOrNone": "new"
}
```

- Gdy `options.hideNone` jest określony jako **true**, `newOrExistingOrNone` będzie mieć tylko wartość **New** lub **exist**.
- Gdy `options.hideDomainNameLabel` jest określony jako **true**, `domainNameLabel` jest niezadeklarowany.

## <a name="remarks"></a>Uwagi

- Jeśli `constraints.required.domainNameLabel` jest ustawiona na **true**, użytkownik musi podać etykietę nazwy domeny podczas tworzenia nowego publicznego adresu IP. Istniejące publiczne adresy IP bez etykiet nie są dostępne do wyboru.
- Jeśli `options.hideNone` jest ustawiona na **true**, opcja wyboru **Brak** dla publicznego adresu IP jest ukryta. Wartość domyślna to **fałsz**.
- Jeśli `options.hideDomainNameLabel` ma wartość **true**, pole tekstowe etykiety nazwy domeny jest ukryte. Wartość domyślna to **fałsz**.
- Jeśli `options.hideExisting` ma wartość true, użytkownik nie może wybrać istniejącego publicznego adresu IP. Wartość domyślna to **fałsz**.
- Dla programu `zone` dostępne są tylko publiczne adresy IP dla określonej strefy lub strefy odporne na publiczne adresy IP.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
