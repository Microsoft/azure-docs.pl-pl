---
title: Poznaj język zapytań usługi Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — opis języka zapytań IoT Hub w programie SQL Server, który służy do pobierania informacji o urządzeniu/module bliźniaczych reprezentacji i zadaniach z Centrum IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: cae2bcb1a3302814a426fa0cb2dfb36ba1b013fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218370"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Język zapytań usługi IoT Hub dla urządzeń i bliźniaczych reprezentacji modułów, zadań i routingu komunikatów

IoT Hub oferuje zaawansowany język przypominający SQL, który umożliwia pobieranie informacji dotyczących [urządzeń bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md), [modułów bliźniaczych reprezentacji](iot-hub-devguide-module-twins.md), [zadań](iot-hub-devguide-jobs.md)i [routingu komunikatów](iot-hub-devguide-messages-d2c.md). W tym artykule przedstawiono następujące informacje:

* Wprowadzenie do głównych funkcji języka zapytań IoT Hub i
* Szczegółowy opis języka. Aby uzyskać szczegółowe informacje dotyczące języka zapytań dla routingu wiadomości, zobacz [zapytania w obszarze Routing komunikatów](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Zapytania o sznurki urządzenia i modułu

[Urządzenia bliźniaczych reprezentacji](iot-hub-devguide-device-twins.md) i [bliźniaczych reprezentacji modułu](iot-hub-devguide-module-twins.md) mogą zawierać dowolne obiekty JSON jako Tagi i właściwości. IoT Hub umożliwia wykonywanie zapytań dotyczących bliźniaczych reprezentacji i modułu bliźniaczych reprezentacji w postaci pojedynczego dokumentu JSON zawierającego wszystkie informacje o bliźniaczych wartościach.

Załóżmy na przykład, że urządzenie usługi IoT Hub bliźniaczych reprezentacji ma następującą strukturę (sznurka modułu będzie podobna bezpośrednio do dodatkowej moduleId):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Zapytania dotyczące sznurka urządzenia

IoT Hub udostępnia bliźniaczych reprezentacji urządzenia jako kolekcję dokumentów o nazwie **Devices**. Na przykład następujące zapytanie pobiera cały zestaw bliźniaczych reprezentacji urządzeń:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Zestawy SDK usługi Azure IoT](iot-hub-devguide-sdks.md) obsługują stronicowanie dużych wyników.

IoT Hub umożliwia pobieranie filtrowania bliźniaczych reprezentacji urządzeń z dowolnym warunkiem. Na przykład, aby odbierać bliźniaczych reprezentacji urządzeń, gdzie tag **Location. region** jest ustawiony na wartość **US** , użyj następującego zapytania:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Operatory logiczne i porównania arytmetyczne są również obsługiwane. Na przykład aby pobrać bliźniaczych reprezentacji urządzenia znajdujące się w Stanach Zjednoczonych i skonfigurować do wysyłania telemetrii mniej niż co minutę, użyj następującego zapytania:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Jako wygoda jest również możliwość użycia stałych tablicowych z operatorami **in** i **nZa** (Not in). Na przykład aby pobrać bliźniaczych reprezentacji urządzenia, które raportuje Wi-Fi lub połączenie przewodowe, użyj następującego zapytania:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Często konieczne jest zidentyfikowanie wszystkich bliźniaczych reprezentacji urządzeń, które zawierają określoną właściwość. W tym celu IoT Hub obsługuje funkcję `is_defined()` . Na przykład aby pobrać bliźniaczych reprezentacji urządzenia, które definiują `connectivity` Właściwość, należy użyć następującego zapytania:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Zapoznaj się z sekcją [klauzula WHERE](iot-hub-devguide-query-language.md#where-clause) , aby uzyskać pełne informacje o możliwościach filtrowania.

Grupowanie i agregacje są również obsługiwane. Na przykład aby znaleźć liczbę urządzeń w każdym stanie konfiguracji telemetrii, użyj następującego zapytania:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

To zapytanie grupujące zwróci wynik podobny do następującego przykładu:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

W tym przykładzie trzy urządzenia zgłosiły pomyślną konfigurację, dwie nadal stosują konfigurację, a jeden zgłosił błąd.

Zapytania projekcji umożliwiają deweloperom zwracanie tylko właściwości, o których się interesują. Na przykład aby pobrać czas ostatniego działania wszystkich odłączonych urządzeń, użyj następującego zapytania:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Zapytania bliźniaczye modułu

Wykonywanie zapytań dotyczących modułu bliźniaczych reprezentacji jest podobne do wykonywania zapytań dotyczących urządzeń bliźniaczych reprezentacji, ale przy użyciu innej kolekcji/przestrzeni nazw; zamiast z **urządzeń** należy wysyłać zapytania z **urządzeń. moduły**:

```sql
SELECT * FROM devices.modules
```

Nie zezwalamy na sprzężenie między kolekcjami urządzeń i urządzeń. moduły. Jeśli chcesz wykonywać zapytania dotyczące modułu bliźniaczych reprezentacji na różnych urządzeniach, należy to zrobić w oparciu o Tagi. To zapytanie zwróci wszystkie bliźniaczych reprezentacji modułów na wszystkich urządzeniach ze stanem skanowania:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

To zapytanie zwróci wszystkie moduły bliźniaczych reprezentacji ze stanem skanowania, ale tylko dla określonego podzestawu urządzeń:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>Przykład w języku C#

Funkcja zapytania jest udostępniana przez [zestaw SDK usługi C#](iot-hub-devguide-sdks.md) **w klasie** .

Oto przykład prostego zapytania:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

Obiekt **zapytania** jest skonkretyzowany przy użyciu rozmiaru strony (do 100). Następnie pobierane są wiele stron, wywołując metody **GetNextAsTwinAsync** wiele razy.

Obiekt zapytania uwidacznia wiele **kolejnych** wartości, w zależności od opcji deserializacji wymaganej przez zapytanie. Na przykład, sznurki lub obiekty zadania lub zwykły kod JSON podczas korzystania z projekcji.

### <a name="nodejs-example"></a>Przykład Node.js

Funkcja zapytania jest udostępniana przez [zestaw SDK usługi Azure IoT dla Node.js](iot-hub-devguide-sdks.md) w obiekcie **rejestru** .

Oto przykład prostego zapytania:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

Obiekt **zapytania** jest skonkretyzowany przy użyciu rozmiaru strony (do 100). Następnie pobierane są wiele stron przez wywołanie metody **nextAsTwin** wiele razy.

Obiekt zapytania uwidacznia wiele **kolejnych** wartości, w zależności od opcji deserializacji wymaganej przez zapytanie. Na przykład, sznurki lub obiekty zadania lub zwykły kod JSON podczas korzystania z projekcji.

### <a name="limitations"></a>Ograniczenia

> [!IMPORTANT]
> Wyniki zapytania mogą zawierać kilka minut opóźnienia w odniesieniu do najnowszych wartości w urządzeniu bliźniaczych reprezentacji. W przypadku wykonywania zapytań dotyczących poszczególnych urządzeń bliźniaczych reprezentacji według identyfikatora należy użyć [interfejsu API REST Get-bliźniaczy](/java/api/com.microsoft.azure.sdk.iot.device.devicetwin). Ten interfejs API zawsze zwraca najnowsze wartości i ma wyższe limity ograniczania. Interfejs API REST można wydać bezpośrednio lub użyć funkcji równoważnych w jednym z [zestawów SDK usługi Azure IoT Hub](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks).

Obecnie porównania są obsługiwane tylko w typach pierwotnych (bez obiektów), na przykład `... WHERE properties.desired.config = properties.reported.config` jest obsługiwane tylko wtedy, gdy te właściwości mają wartości pierwotne.

## <a name="get-started-with-jobs-queries"></a>Wprowadzenie do zapytań zadań

[Zadania](iot-hub-devguide-jobs.md) umożliwiają wykonywanie operacji na zestawach urządzeń. Poszczególne sznurki urządzeń zawierają informacje o zadaniach, których częścią jest kolekcja o nazwie **Jobs (zadania**).

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Obecnie ta kolekcja jest Queryable jako **Devices.Jobs** w języku zapytań IoT Hub.

> [!IMPORTANT]
> Obecnie Właściwość Jobs nie jest nigdy zwracana podczas wykonywania zapytania dotyczącego urządzenia bliźniaczych reprezentacji. Oznacza to, że zapytania zawierające element "z urządzeń". Do właściwości Jobs (zadania) można korzystać tylko bezpośrednio z zapytaniami przy użyciu polecenia `FROM devices.jobs` .
>
>

Na przykład aby uzyskać wszystkie zadania (w przeszłości i zaplanowane), które mają wpływ na pojedyncze urządzenie, można użyć następującego zapytania:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Zwróć uwagę, jak to zapytanie udostępnia stan specyficzny dla urządzenia (a także odpowiedź metody bezpośredniej) dla każdego zwróconego zadania.

Istnieje również możliwość filtrowania z dowolnego warunku logicznego we wszystkich właściwościach obiektu w kolekcji **Devices.Jobs** .

Na przykład aby pobrać wszystkie ukończone zadania aktualizacji z użyciem sznurka urządzenia, które zostały utworzone po września 2016 dla określonego urządzenia, użyj następującego zapytania:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Możesz również pobrać wyniki poszczególnych urządzeń z jednego zadania.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Ograniczenia

Obecnie zapytania w usłudze **Devices.Jobs** nie obsługują:

* Projekcje, w związku z tym tylko `SELECT *` jest możliwe.
* Warunki odwołujące się do sznurka urządzenia oprócz właściwości zadania (zobacz poprzednią sekcję).
* Wykonywanie agregacji, takich jak Count, AVG, Group by.

## <a name="basics-of-an-iot-hub-query"></a>Podstawy zapytania IoT Hub

Każde zapytanie IoT Hub składa się z klauzul SELECT i FROM z opcjonalnymi klauzulami WHERE i GROUP BY. Każde zapytanie jest uruchamiane w kolekcji dokumentów JSON, na przykład bliźniaczych reprezentacji urządzeń. Klauzula FROM wskazuje kolekcję dokumentów, w której ma zostać wykonana iteracja (**urządzenia**, **urządzenia. moduły** lub **Devices.Jobs**). Następnie zostanie zastosowany filtr w klauzuli WHERE. W przypadku agregacji wyniki tego kroku są pogrupowane jak określono w klauzuli GROUP BY. Dla każdej grupy generowany jest wiersz określony w klauzuli SELECT.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Klauzula FROM

Klauzula **from <from_specification>** może przyjmować tylko trzy wartości: **od urządzeń** do wykonywania zapytań dotyczących urządzeń bliźniaczych reprezentacji, **od urządzeń. modułów** w celu wysyłania zapytań do modułu bliźniaczych reprezentacji lub **z Devices.Jobs** w celu wykonywania zapytań dotyczących poszczególnych urządzeń.

## <a name="where-clause"></a>Klauzula WHERE

Klauzula **where <filter_condition>** jest opcjonalna. Określa jeden lub więcej warunków, które dokumenty JSON w kolekcji FROM muszą spełniać, aby były uwzględniane jako część wyniku. Każdy dokument JSON musi oszacować określone warunki do wartości "true", aby można je było uwzględnić w wyniku.

Dozwolone warunki są opisane w sekcji [wyrażenia i warunki](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>Klauzula SELECT

**>SELECT <select_list** jest obowiązkowy i określa, jakie wartości są pobierane z zapytania. Określa wartości JSON, które będą używane do generowania nowych obiektów JSON.
Dla każdego elementu przefiltrowany (i opcjonalnie pogrupowany) podzbiór kolekcji FROM, etap projekcji generuje nowy obiekt JSON. Ten obiekt jest zbudowany z wartościami określonymi w klauzuli SELECT.

Poniżej znajduje się Gramatyka klauzuli SELECT:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** odwołuje się do dowolnej właściwości dokumentu JSON w kolekcji from. Niektóre przykłady klauzul SELECT można znaleźć w sekcji wprowadzenie do korzystania z zapytań dotyczących sznurów urządzeń.

Obecnie klauzule wyboru inne niż **SELECT*** są obsługiwane tylko w zapytaniach agregacji na urządzeniu bliźniaczych reprezentacji.

## <a name="group-by-clause"></a>GROUP BY, klauzula

Klauzula **GROUP BY <group_specification>** jest opcjonalnym krokiem, który jest wykonywany po filtrze określonym w klauzuli WHERE oraz przed określoną projekcją w zaznaczeniu. Grupuje dokumenty na podstawie wartości atrybutu. Te grupy są używane do generowania zagregowanych wartości, jak określono w klauzuli SELECT.

Przykładem zapytania korzystającego z polecenia Grupuj według jest:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Formalna składnia dla polecenia Grupuj według jest następująca:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** odwołuje się do dowolnej właściwości dokumentu JSON w kolekcji from.

Obecnie klauzula GROUP BY jest obsługiwana tylko podczas wysyłania zapytań do bliźniaczych reprezentacjiu urządzenia.

> [!IMPORTANT]
> Termin `group` jest obecnie traktowany jako specjalne słowo kluczowe w zapytaniach. W przypadku użycia `group` jako nazwy właściwości należy rozważyć zablokowanie jej przy użyciu podwójnych nawiasów, aby uniknąć błędów, np. `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'` .
>

## <a name="expressions-and-conditions"></a>Wyrażenia i warunki

Na wysokim poziomie *wyrażenie*:

* Oblicza wystąpienie typu JSON (takie jak Boolean, Number, String, array lub Object).
* Jest definiowana przez manipulowanie danymi pochodzącymi z dokumentu JSON i stałych, przy użyciu wbudowanych operatorów i funkcji.

*Warunki* są wyrażeniami, które są obliczane jako wartość logiczna. Każda stała inna niż wartość logiczna **true** jest traktowana jako **false**. Ta reguła zawiera **wartości null**, **undefined**, wszelkie wystąpienia obiektu lub tablicy, dowolny ciąg i wartość logiczną **false**.

Składnia wyrażeń jest następująca:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Aby zrozumieć, co oznacza każdy symbol w składni wyrażeń, zapoznaj się z poniższą tabelą:

| Symbol | Definicja |
| --- | --- |
| attribute_name | Każda właściwość dokumentu JSON w kolekcji **from** . |
| binary_operator | Dowolny operator binarny wymieniony w sekcji [operatorów](#operators) . |
| function_name| Każda funkcja wymieniona w sekcji [Functions](#functions) . |
| decimal_literal |Wartość zmiennoprzecinkowa wyrażona w notacji dziesiętnej. |
| hexadecimal_literal |Liczba wyrażona przez ciąg "0x", po którym następuje ciąg cyfr szesnastkowych. |
| string_literal |Literały ciągu są ciągami Unicode reprezentowanymi przez sekwencję zero lub więcej znaków Unicode lub sekwencji unikowych. Literały ciągu są ujęte w apostrofy lub podwójne cudzysłowy. Dozwolone ucieczki: `\'` , `\"` , `\\` , `\uXXXX` dla znaków Unicode, zdefiniowane przez 4 cyfry szesnastkowe. |

### <a name="operators"></a>Operatory

Obsługiwane są następujące operatory:

| Family | Operatory |
| --- | --- |
| Arytmetyczny |+, -, *, /, % |
| Wartości logiczne |AND, OR, NOT |
| Porównanie |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funkcje

Podczas wykonywania zapytania dotyczącego bliźniaczych reprezentacji i zadań jedyną obsługiwaną funkcją jest:

| Funkcja | Opis |
| -------- | ----------- |
| IS_DEFINED (Właściwość) | Zwraca wartość Boolean wskazującą, czy do właściwości przypisano wartości (w tym `null` ). |

W warunkach trasy obsługiwane są następujące funkcje matematyczne:

| Funkcja | Opis |
| -------- | ----------- |
| ABS (x) | Zwraca wartość bezwzględną (dodatnią) podanego wyrażenia liczbowego. |
| EXP (x) | Zwraca wartość wykładniczą określonego wyrażenia liczbowego (e ^ x). |
| MOC (x, y) | Zwraca wartość określonego wyrażenia do określonej potęgi (x ^ y).|
| KWADRAT (x)    | Zwraca kwadrat określonej wartości liczbowej. |
| GÓRNy limit (x) | Zwraca najmniejszą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub większą. |
| PODŁOGa (x) | Zwraca największą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub mniejszą. |
| ZNAK (x) | Zwraca liczbę dodatnią (+ 1), zero (0) lub ujemną (-1) znak w określonym wyrażeniu liczbowym.|
| SQRT (x) | Zwraca pierwiastek kwadratowy z określonej wartości liczbowej. |

W warunkach trasy obsługiwane są następujące funkcje sprawdzania typu i rzutowania:

| Funkcja | Opis |
| -------- | ----------- |
| AS_NUMBER | Konwertuje ciąg wejściowy na liczbę. `noop` Jeśli dane wejściowe są liczbami; `Undefined` Jeśli ciąg nie reprezentuje liczby.|
| IS_ARRAY | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest tablicą. |
| IS_BOOL | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest wartością logiczną. |
| IS_DEFINED | Zwraca wartość logiczną wskazującą, do właściwości przypisano wartość. Jest to obsługiwane tylko wtedy, gdy wartość jest typem pierwotnym. Typy pierwotne obejmują ciąg, wartość logiczną, numeryczną lub `null` . DateTime, typy obiektów i tablice nie są obsługiwane. |
| IS_NULL | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia ma wartość null. |
| IS_NUMBER | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest liczbą. |
| IS_OBJECT | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest obiektem JSON. |
| IS_PRIMITIVE | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest typem pierwotnym (String, Boolean, numeric lub `null` ). |
| IS_STRING | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest ciągiem. |

W warunkach trasy obsługiwane są następujące funkcje ciągów:

| Funkcja | Opis |
| -------- | ----------- |
| CONCAT (x, y,...) | Zwraca ciąg, który jest wynikiem połączenia co najmniej dwóch wartości ciągu. |
| Długość (x) | Zwraca liczbę znaków określonego wyrażenia ciągu.|
| LOWER (x) | Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych wielkich liter na małe litery. |
| UPPER (x) | Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych małych liter na wielkie litery. |
| Podciąg (String, początek [, Długość]) | Zwraca część wyrażenia ciągu rozpoczynającą się od określonego znaku na podstawie zera i kontynuuje określoną długość lub na końcu ciągu. |
| INDEX_OF (ciąg, fragment) | Zwraca pozycję początkową pierwszego wystąpienia drugiego ciągu wyrażenia w ramach pierwszego określonego wyrażenia ciągu lub wartość -1, jeśli ciąg nie zostanie znaleziony.|
| STARTS_WITH (x, y) | Zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu zaczyna się od drugiego. |
| ENDS_WITH (x, y) | Zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu ma kończyć się drugim. |
| ZAWIERA (x, y) | Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wykonywać zapytania w aplikacjach przy użyciu [zestawów SDK usługi Azure IoT](iot-hub-devguide-sdks.md).