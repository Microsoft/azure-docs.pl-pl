---
title: Opis aktualizacji urządzeń z usługą Azure IoT Hub grupy urządzeń | Microsoft Docs
description: Informacje o sposobie używania grup urządzeń.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679497"
---
# <a name="device-groups"></a>Grupy urządzeń

Grupa urządzeń to zbiór urządzeń. Grupy urządzeń umożliwiają skalowanie wdrożeń na wiele urządzeń. Każde urządzenie należy do dokładnie jednej grupy urządzeń naraz.
Można utworzyć wiele grup urządzeń w celu zorganizowania urządzeń. Na przykład firma Contoso może korzystać z grupy urządzeń "Samoloting" dla urządzeń w laboratorium testowym i w grupie urządzeń "Ocena" dla urządzeń używanych przez zespół pól w centrum operacji. Ponadto firma Contoso może zdecydować się na grupowanie urządzeń produkcyjnych w oparciu o ich regiony geograficzne, dzięki czemu mogą oni aktualizować urządzenia zgodnie z harmonogramem, który jest zgodny z regionalnymi strefami czasowymi. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>Używanie znacznika sznurka urządzenia do tworzenia grup urządzeń

Znaczniki sznurka urządzenia umożliwiają użytkownikom grupowanie urządzeń. Urządzenia muszą mieć klucz ADUGroup i wartość w postaci sznurka urządzenia, aby umożliwić ich grupowanie.

### <a name="device-twin-tag-format"></a>Format znacznika sznurka urządzenia

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Grupa urządzeń bez kategorii

Bez kategorii jest słowem zastrzeżonym, które jest używane do grupowania urządzeń, które:
- Nie masz znacznika przędzy urządzenia ADUGroup.
- Mają tag ADUGroup urządzenia, ale Grupa nie została utworzona przy użyciu tej nazwy grupy.

Rozważmy na przykład, że urządzenia mają następujące znaczniki sznurów urządzeń:

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Poniżej znajdują się urządzenia i możliwe grupy, które można dla nich tworzyć.

|Urządzenie |Group (Grupa)  |
|-----------|--------------|
|Device1    |Grupa1|
|Device2    |Grupa1|
|Device3    |Group2|
|Device4    |Brak kategorii|



## <a name="next-steps"></a>Następne kroki

[Tworzenie grupy urządzeń](./create-update-group.md)
