---
title: SizeSelector — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. COMPUTE. SizeSelector dla Azure Portal. Służy do wybierania rozmiaru maszyny wirtualnej.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d6408f8c08694ae681d302ae35f5778894091733
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063616"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Microsoft. COMPUTE. SizeSelector — element interfejsu użytkownika

Kontrolka służąca do wybierania rozmiaru dla co najmniej jednego wystąpienia maszyny wirtualnej.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Użytkownik widzi selektor z wartościami domyślnymi z definicji elementu.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft-compute-sizeselector.png)

Po wybraniu kontrolki użytkownik zobaczy rozwinięty widok dostępnych rozmiarów.

![Rozwinięto Microsoft. COMPUTE. SizeSelector](./media/managed-application-elements/microsoft-compute-sizeselector-expanded.png)

## <a name="schema"></a>Schemat

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
"Standard_D1"
```

## <a name="remarks"></a>Uwagi

- `recommendedSizes` powinien mieć co najmniej jeden rozmiar. Pierwszy zalecany rozmiar jest używany jako domyślny. Lista dostępnych rozmiarów nie jest posortowana według zalecanego stanu. Użytkownik może wybrać tę kolumnę, aby posortować według zalecanego stanu.
- Jeśli zalecany rozmiar nie jest dostępny w wybranej lokalizacji, rozmiar jest automatycznie pomijany. Zamiast tego jest używany następny zalecany rozmiar.
- `constraints.allowedSizes` i `constraints.excludedSizes` są opcjonalne, ale nie mogą być używane jednocześnie. Listę dostępnych rozmiarów można określić, wywołując [listę dostępnych rozmiarów maszyn wirtualnych w ramach subskrypcji](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Dowolny rozmiar nieokreślony w elemencie `constraints.allowedSizes` jest ukryty, a każdy rozmiar nieokreślony w polu `constraints.excludedSizes` jest wyświetlany.
- `osPlatform` musi być określony i może być w **systemie Windows** lub **Linux**. Służy do określania kosztów sprzętu maszyn wirtualnych.
- `imageReference` jest pomijany dla obrazów pierwszej firmy, ale udostępniany dla obrazów innych firm. Służy do określania kosztów oprogramowania maszyn wirtualnych.
- `count` służy do ustawiania odpowiedniego mnożnika dla elementu. Obsługuje ona wartość statyczną, taką jak **2**, lub wartość dynamiczną z innego elementu, na przykład `[steps('step1').vmCount]` . Wartość domyślna to **1**.
- `numAvailabilityZonesRequired`Może to być 1, 2 lub 3.
- Domyślnie `hideDiskTypeFilter` ma **wartość false**. Filtr typ dysku umożliwia użytkownikowi wyświetlanie wszystkich typów dysku lub tylko dysków SSD.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
