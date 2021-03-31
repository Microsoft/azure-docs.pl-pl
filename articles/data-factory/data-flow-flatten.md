---
title: Spłaszcz transformację w strumieniu danych mapowania
description: Denormalizowanie danych hierarchicznych przy użyciu transformacji spłaszczania
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81413672"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Spłaszcz transformację w strumieniu danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Użyj przekształcenia Spłaszcz, aby przyjmować wartości tablicowe wewnątrz struktur hierarchicznych, takich jak JSON, i odrzucić je w poszczególnych wierszach. Ten proces jest nazywany denormalizacją.

## <a name="configuration"></a>Konfigurowanie

Transformacja spłaszczania zawiera następujące ustawienia konfiguracji

![Ustawienia spłaszczania](media/data-flow/flatten1.png "Ustawienia spłaszczania")

### <a name="unroll-by"></a>Odwrócenie według

Wybierz tablicę do odwrócenia. Dane wyjściowe będą mieć jeden wiersz dla każdego elementu w każdej tablicy. Jeśli odwrócenie przez tablicę w wierszu wejściowym ma wartość null lub jest puste, będzie istnieć jeden wiersz danych wyjściowych z wartościami nierzutowanymi jako null.

### <a name="unroll-root"></a>Odwrócenie elementu głównego

Domyślnie transformacja Spłaszcz odrzuci tablicę w górnej części hierarchii, w której istnieje. Opcjonalnie możesz wybrać tablicę jako element główny rozwinięcia. Element główny rozrzutu musi być tablicą obiektów złożonych, które są lub zawierają odwrócenie przez tablicę. W przypadku wybrania elementu nadrzędnego odwrócenia dane wyjściowe będą zawierać co najmniej jeden wiersz dla każdego elementu w elemencie głównym odrzutu. Jeśli wiersz wejściowy nie zawiera żadnych elementów w głównym elemencie nadrzędnym, zostanie on porzucony z danych wyjściowych. Wybranie elementu głównego odwrócenia spowoduje zawsze wyprowadzanie mniejszej lub równej liczby wierszy niż zachowanie domyślne.

### <a name="flatten-mapping"></a>Spłaszcz mapowanie

Podobnie jak w przypadku transformacji SELECT, wybierz rzutowanie nowej struktury z pól przychodzących i nieznormalizowanej tablicy. W przypadku zamapowania tablicy nieznormalizowanej kolumna wyjściowa będzie tego samego typu danych co tablica. Jeśli rozrzutowanie według tablic jest tablicą obiektów złożonych, które zawierają podtablice, mapowanie elementu subarry będzie wyprowadzać tablicę.

Zapoznaj się z kartą inspekcją i podglądem danych, aby zweryfikować dane wyjściowe mapowania.

## <a name="examples"></a>Przykłady

Zapoznaj się z poniższym obiektem JSON, aby zapoznać się z poniższymi przykładami transformacji spłaszczania

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>Brak elementu głównego Unroll z tablicą ciągów

| Odwrócenie według | Odwrócenie elementu głównego | Projekcja |
| --------- | ----------- | ---------- |
| towary. klienci | Brak | name <br> Klient = towary. Klient |

#### <a name="output"></a>Dane wyjściowe

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>Brak elementu głównego odwrócenia z tablicą złożoną

| Odwrócenie według | Odwrócenie elementu głównego | Projekcja |
| --------- | ----------- | ---------- |
| towary. Orders. wydana. orderItems | Brak | name <br> IDZamówienia = towary. Orders. IDZamówienia <br> itemName = towary. Orders. orderItems. itemName <br> itemQty = towary. Orders. orderItems. itemQty <br> Lokalizacja = Lokalizacja |

#### <a name="output"></a>Dane wyjściowe

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>Ten sam katalog główny jako odrzut macierzy

| Odwrócenie według | Odwrócenie elementu głównego | Projekcja |
| --------- | ----------- | ---------- |
| towary. zamówienia | towary. zamówienia | name <br> Items. Orders. <br> towary. klienci <br> location |

#### <a name="output"></a>Dane wyjściowe

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Odwrócenie elementu głównego za pomocą złożonej tablicy

| Odwrócenie według | Odwrócenie elementu głównego | Projekcja |
| --------- | ----------- | ---------- |
| towary. Orders. wydana. orderItem | towary. zamówienia |name <br> IDZamówienia = towary. Orders. IDZamówienia <br> itemName = towary. Orders. orderItems. itemName <br> itemQty = towary. Orders. orderItems. itemQty <br> Lokalizacja = Lokalizacja |

#### <a name="output"></a>Dane wyjściowe

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>Skrypt przepływu danych

### <a name="syntax"></a>Składnia

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>Przykład

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>Następne kroki

* Użyj [transformacji przestawnej](data-flow-pivot.md) , aby przestawić wiersze do kolumn.
* Użyj [transformacji UNPIVOT](data-flow-unpivot.md) , aby przestawić kolumny do wierszy.
