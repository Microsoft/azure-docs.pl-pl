---
title: Spłaszczanie transformacji w przepływie danych mapowania
description: Denormalize danych hierarchicznych przy użyciu spłaszczenia transformacji
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413672"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>Spłaszczanie transformacji w przepływie danych mapowania

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Spłaszczyć transformacji do podjęcia wartości tablicy wewnątrz struktur hierarchicznych, takich jak JSON i rozwiń je w poszczególnych wierszach. Proces ten jest znany jako denormalizacji.

## <a name="configuration"></a>Konfigurowanie

Transformacja spłaszczenia zawiera następujące ustawienia konfiguracji

![Spłaszcz ustawienia](media/data-flow/flatten1.png "Spłaszcz ustawienia")

### <a name="unroll-by"></a>Rozwiń

Wybierz tablicę do rozwiania. Dane wyjściowe będą miały jeden wiersz na element w każdej tablicy. Jeśli rozwiń przez tablicę w wierszu wejściowym jest null lub puste, będzie jeden wiersz wyjściowy z unrolled wartości jako null.

### <a name="unroll-root"></a>Rozwiń katalog główny

Domyślnie spłaszczenie transformacji rozwija tablicę na początku hierarchii, w jakiej istnieje. Opcjonalnie można wybrać tablicę jako root unroll. Katalog główny unroll musi być tablicą złożonych obiektów, które są lub zawierają rozwiń przez tablicę. Jeśli wybrano katalog główny, dane wyjściowe będą zawierać co najmniej jeden wiersz na elementy w katalogu głównym rozwiać. Jeśli wiersz wejściowy nie ma żadnych elementów w katalogu głównym unroll, zostanie usunięty z danych wyjściowych. Wybranie katalogu głównego rozwiać zawsze spowoduje wysunienie mniejszą lub taką samą liczbę wierszy niż domyślne zachowanie.

### <a name="flatten-mapping"></a>Spłaszcz mapowanie

Podobnie jak transformacja select, wybierz rzut nowej struktury z pól przychodzących i tablicy zdenormalizowanej. Jeśli zdenormalowana tablica jest mapowana, kolumna wyjściowa będzie tego samego typu danych co tablica. Jeśli rozwiń przez tablicę jest tablica złożonych obiektów, która zawiera podamaty, mapowanie elementu tego podarry spowoduje wysunie tablicę.

Zapoznaj się z kartą inspekcji i podglądem danych, aby zweryfikować dane wyjściowe mapowania.

## <a name="examples"></a>Przykłady

Zapoznaj się z następującym obiektem JSON, aby zapoznać się z poniższymi przykładami spłaszczenia transformacji

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

### <a name="no-unroll-root-with-string-array"></a>Brak wyrejestrowania katalogu głównego z tablicą ciągów

| Rozwiń | Rozwiń katalog główny | Projekcja |
| --------- | ----------- | ---------- |
| towary.klienci | Brak | name <br> klient = goods.customer |

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

### <a name="no-unroll-root-with-complex-array"></a>Brak rolowania roota ze złożoną tablicą

| Rozwiń | Rozwiń katalog główny | Projekcja |
| --------- | ----------- | ---------- |
| towary.zamówienia.shipped.orderItems | Brak | name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> lokalizacja = lokalizacja |

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

### <a name="same-root-as-unroll-array"></a>Ten sam katalog główny co tablica unroll

| Rozwiń | Rozwiń katalog główny | Projekcja |
| --------- | ----------- | ---------- |
| towary.zamówienia | towary.zamówienia | name <br> towary.zamówienia.shipped.orderItems.itemName <br> towary.klienci <br> location |

#### <a name="output"></a>Dane wyjściowe

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>Rozwiń katalog główny ze złożoną tablicą

| Rozwiń | Rozwiń katalog główny | Projekcja |
| --------- | ----------- | ---------- |
| towary.zamówienia.shipped.orderItem | towary.zamówienia |name <br> orderId = goods.orders.orderId <br> itemName = goods.orders.shipped.orderItems.itemName <br> itemQty = goods.orders.shipped.orderItems.itemQty <br> lokalizacja = lokalizacja |

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

* [Transformacja przestawna](data-flow-pivot.md) służy do obracania wierszy do kolumn.
* Transformacja [unpivot służy](data-flow-unpivot.md) do przestawiania kolumn do wierszy.
