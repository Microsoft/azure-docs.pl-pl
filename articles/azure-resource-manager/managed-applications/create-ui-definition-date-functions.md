---
title: Tworzenie funkcji daty definicji interfejsu użytkownika
description: Opisuje funkcje, które mają być używane podczas pracy z wartościami daty.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098190"
---
# <a name="createuidefinition-date-functions"></a>CreateUiDefinition — funkcje daty

Funkcje, które mają być używane podczas pracy z wartościami daty.

## <a name="addhours"></a>addgodz.

Dodaje całkowitą liczbę godzin do określonej sygnatury czasowej.

Poniższy przykład zwraca `"1991-01-01T00:59:59.000Z"` :

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addminut

Dodaje całkowitą liczbę minut do określonej sygnatury czasowej.

Poniższy przykład zwraca `"1991-01-01T00:00:59.000Z"` :

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>AddSeconds
Dodaje całkowitą liczbę sekund do określonej sygnatury czasowej.

Poniższy przykład zwraca `"1991-01-01T00:00:00.000Z"` :

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

Zwraca ciąg w formacie ISO 8601 bieżącej daty i godziny na komputerze lokalnym.

Poniższy przykład może zwrócić `"1990-12-31T23:59:59.000Z"` :

```json
"[utcNow()]"
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../management/overview.md).
