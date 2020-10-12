---
title: VirtualNetworkCombo — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Network. VirtualNetworkCombo dla Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 711f5293b205c1f500c6d9e08154342285ef959b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87033216"
---
# <a name="microsoftnetworkvirtualnetworkcombo-ui-element"></a>Element interfejsu użytkownika Microsoft. Network. VirtualNetworkCombo

Grupa kontrolek służąca do wybierania nowej lub istniejącej sieci wirtualnej.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Gdy użytkownik wybiera nową sieć wirtualną, użytkownik może dostosować nazwę i prefiks adresu każdej podsieci. Konfigurowanie podsieci jest opcjonalne.

![Microsoft. Network. VirtualNetworkCombo New](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-new.png)

Gdy użytkownik wybiera istniejącą sieć wirtualną, użytkownik musi zmapować każdą podsieć, której wymaga szablon wdrożenia, do istniejącej podsieci. Konfigurowanie podsieci w tym przypadku jest wymagane.

![Istniejąca firma Microsoft. Network. VirtualNetworkCombo](./media/managed-application-elements/microsoft-network-virtualnetworkcombo-existing.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Network.VirtualNetworkCombo",
  "label": {
    "virtualNetwork": "Virtual network",
    "subnets": "Subnets"
  },
  "toolTip": {
    "virtualNetwork": "",
    "subnets": ""
  },
  "defaultValue": {
    "name": "vnet01",
    "addressPrefixSize": "/16"
  },
  "constraints": {
    "minAddressPrefixSize": "/16"
  },
  "options": {
    "hideExisting": false
  },
  "subnets": {
    "subnet1": {
      "label": "First subnet",
      "defaultValue": {
        "name": "subnet-1",
        "addressPrefixSize": "/24"
      },
      "constraints": {
        "minAddressPrefixSize": "/24",
        "minAddressCount": 12,
        "requireContiguousAddresses": true
      }
    },
    "subnet2": {
      "label": "Second subnet",
      "defaultValue": {
        "name": "subnet-2",
        "addressPrefixSize": "/26"
      },
      "constraints": {
        "minAddressPrefixSize": "/26",
        "minAddressCount": 8,
        "requireContiguousAddresses": true
      }
    }
  },
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "name": "vnet01",
  "resourceGroup": "rg01",
  "addressPrefixes": ["10.0.0.0/16"],
  "newOrExisting": "new",
  "subnets": {
    "subnet1": {
      "name": "subnet-1",
      "addressPrefix": "10.0.0.0/24",
      "startAddress": "10.0.0.1"
    },
    "subnet2": {
      "name": "subnet-2",
      "addressPrefix": "10.0.1.0/26",
      "startAddress": "10.0.1.1"
    }
  }
}
```

## <a name="remarks"></a>Uwagi

- Jeśli ta wartość jest określona, pierwszy nienakładający się prefiks adresu `defaultValue.addressPrefixSize` jest ustalany automatycznie na podstawie istniejących sieci wirtualnych w ramach subskrypcji użytkownika.
- Wartość domyślna dla `defaultValue.name` i `defaultValue.addressPrefixSize` ma wartość **null**.
- `constraints.minAddressPrefixSize` musi być określony. Wszystkie istniejące sieci wirtualne z przestrzenią adresową mniejszą niż określona wartość są niedostępne do wybrania.
- `subnets` musi być określona i `constraints.minAddressPrefixSize` musi być określona dla każdej podsieci.
- Podczas tworzenia nowej sieci wirtualnej prefiks adresu każdej podsieci jest obliczany automatycznie na podstawie prefiksu adresu sieci wirtualnej i odpowiednich `addressPrefixSize` .
- W przypadku korzystania z istniejącej sieci wirtualnej wszystkie podsieci mniejsze niż odpowiednie nie `constraints.minAddressPrefixSize` są dostępne do wyboru. Ponadto w przypadku wybrania opcji podsieci, które nie mają co najmniej `minAddressCount` dostępnych adresów, nie są dostępne do wyboru. Wartość domyślna to **0**. Aby upewnić się, że dostępne adresy są ciągłe, określ **wartość true** dla `requireContiguousAddresses` . Wartość domyślna to **true**.
- Tworzenie podsieci w istniejącej sieci wirtualnej nie jest obsługiwane.
- Jeśli `options.hideExisting` ma **wartość true**, użytkownik nie może wybrać istniejącej sieci wirtualnej. Wartość domyślna to **fałsz**.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
