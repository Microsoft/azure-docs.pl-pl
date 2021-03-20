---
title: Tworzenie funkcji matematycznych definicji interfejsu użytkownika
description: Opisuje funkcje, które mają być używane podczas wykonywania operacji matematycznych.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87098170"
---
# <a name="createuidefinition-math-functions"></a>CreateUiDefinition funkcje matematyczne

Funkcje umożliwiają wykonywanie operacji matematycznych.

## <a name="add"></a>add

Dodaje dwie liczby i zwraca wynik.

Poniższy przykład zwraca `3` :

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>CEIL —

Zwraca największą liczbę całkowitą większą lub równą podanej liczbie.

Poniższy przykład zwraca `4` :

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

Dzieli pierwszą liczbę przez drugą liczbę i zwraca wynik. Wynik jest zawsze liczbą całkowitą.

Poniższy przykład zwraca `2` :

```json
"[div(6, 3)]"
```

## <a name="floor"></a>wykładzin

Zwraca największą liczbę całkowitą mniejszą lub równą podanej liczbie.

Poniższy przykład zwraca `3` :

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

Zwraca większą liczbę dwóch liczb.

Poniższy przykład zwraca `2` :

```json
"[max(1, 2)]"
```

## <a name="min"></a>min

Zwraca mniejsze z dwóch liczb.

Poniższy przykład zwraca `1` :

```json
"[min(1, 2)]"
```

## <a name="mod"></a>Funkcja

Dzieli pierwszą liczbę przez drugą liczbę i zwraca resztę.

Poniższy przykład zwraca `0` :

```json
"[mod(6, 3)]"
```

Poniższy przykład zwraca `2` :

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

Mnoży dwie liczby i zwraca wynik.

Poniższy przykład zwraca `6` :

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>rand

Zwraca losową liczbę całkowitą znajdującą się w określonym zakresie. Ta funkcja nie generuje kryptograficznie zabezpieczonych liczb losowych.

Poniższy przykład może zwrócić `42` :

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>range

Generuje sekwencję liczb całkowitych w określonym zakresie.

Poniższy przykład zwraca `[1,2,3]` :

```json
"[range(1, 3)]"
```

## <a name="sub"></a>Sub

Odejmuje drugą liczbę od pierwszej liczby i zwraca wynik.

Poniższy przykład zwraca `1` :

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Resource Manager, zobacz [Azure Resource Manager omówienie](../management/overview.md).
