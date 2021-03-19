---
title: Tworzenie funkcji ciągu definicji interfejsu użytkownika
description: Opisuje funkcje ciągów, które mają być używane podczas konstruowania definicji interfejsu użytkownika dla Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87098160"
---
# <a name="createuidefinition-string-functions"></a>CreateUiDefinition — funkcje ciągów

Te funkcje do użycia z ciągami JSON.

## <a name="concat"></a>concat

Łączy jeden lub więcej ciągów.

Na przykład, jeśli wartość wyjściowa elementu `element1` if `"Contoso"` , wówczas ten przykład zwraca ciąg `"Demo Contoso app"` :

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

Określa, czy ciąg ma kończyć się wartością.

Poniższy przykład zwraca wartość true.

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

Generuje unikatowy ciąg globalny (GUID).

Poniższy przykład zwraca wartość taką jak `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` :

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

Zwraca pierwszą pozycję wartości w ciągu lub-1, jeśli nie zostanie znaleziona.

Poniższy przykład zwraca wartość 2.

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

Zwraca ostatnią pozycję wartości w ciągu lub-1, jeśli nie zostanie znaleziona.

Poniższy przykład zwraca wartość 3.

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>Zastąp

Zwraca ciąg, w którym wszystkie wystąpienia określonego ciągu w bieżącym ciągu są zastępowane innym ciągiem.

Poniższy przykład zwraca `"Contoso.com web app"` :

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

Określa, czy ciąg rozpoczyna się od wartości.

Poniższy przykład zwraca wartość true.

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>podciąg

Zwraca podciąg określonego ciągu. Podciąg zaczyna się od określonego indeksu i ma określoną długość.

Poniższy przykład zwraca `"web"` :

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

Zwraca ciąg przekonwertowany na małe litery.

Poniższy przykład zwraca `"contoso"` :

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

Zwraca ciąg przekonwertowany na wielkie litery.

Poniższy przykład zwraca `"CONTOSO"` :

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../management/overview.md).

