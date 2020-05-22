---
title: Ogólne nazwane jednostki
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 60cd6b8ba2cbfca497ae1b92113b12e85a34bf26
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83778205"
---
## <a name="general-entity-types"></a>Ogólne typy jednostek:

### <a name="person"></a>Person (Osoba)

Rozpoznawaj nazwiska osób w tekście.

Języki:
* Publiczna wersja zapoznawcza:,,,,,,,,,,,,,,,,,, `Arabic` `Czech` `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` , `Swedish` i`Turkish`

| Nazwa podtypu | Opis                                                      | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------------------|---------------------------------------|
| Nie dotyczy          | Rozpoznawane nazwy `Bill Gates` osób, na przykład`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>Persontype
Typ zadania lub rola zatrzymywana przez osobę.

Języki:
* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                                                                | Dostępne począwszy od wersji modelu |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Typy zadań `civil engineer` na przykład,,, `salesperson` `chef` `librarian``nursing aide` | `2020-02-01`                           |

### <a name="location"></a>Lokalizacja

Tereny, struktury, funkcje geograficzne i geopolityczne.

Języki:

* Publiczna wersja zapoznawcza:,,,,,,,,,,,,,,,,,, `Arabic` `Czech` `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` , `Swedish` i`Turkish`

| Nazwa podtypu              | Opis                                                                              | Dostępne począwszy od wersji modelu |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy                       | lokalizacje, `Atlantic Ocean` na przykład, `library` , `Eiffel Tower``Statue of Liberty`  | `2019-10-01`                           |
| Jednostka geopolityczna (GPE) — tylko w języku angielskim| Miasta, kraje/regiony, Stany na przykład,, `Seattle` `Pennsylvania` `South Africa` ,`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>Organizacja  

Uznawane organizacje, korporacje, agencje i inne grupy osób. Na przykład: firmy, grupy polityczne, zespoły muzyczne, trefle sportowe, instytucje rządowe i organizacje publiczne. Narodowe i religijne nie są uwzględnione w tym typie jednostki. 

Języki: 

* Publiczna wersja zapoznawcza:,,,,,,,,,,,,,,,,,, `Arabic` `Czech` `Chinese-Simplified` `Danish` `Dutch` `English` `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish` `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` , `Swedish` i`Turkish`

| Nazwa podtypu | Opis                                                                                             | Dostępne począwszy od wersji modelu |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | organizacje, `Microsoft` na przykład, `NASA` , `National Oceanic and Atmospheric Administration``VOA` | `2019-10-01`                           |

### <a name="event"></a>Zdarzenie  

Zdarzenia historyczne, społeczne i naturalne.  

Języki: 

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                                            | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Zdarzenia takie jak `wedding` , `hurricane` ,,, `car accident` `solar eclipse``American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Produkt  

Obiekty fizyczne różnych kategorii.  

Języki: 

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                                                        | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Na `Microsoft Surface laptop` przykład,, `sunglasses` , `motorcycle` , `bag``Xbox` | `2020-02-01`                           |
| Obliczanie    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>Czy  

Jednostka opisująca możliwości lub wiedzę fachową.  

Języki: 

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                                                 | Dostępne począwszy od wersji modelu |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>Numer telefonu

Numery telefonów (tylko numery telefonów USA). 

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                    | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Numery telefonów US, na przykład`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>Poczta e-mail

Adres e-mail. 

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                      | Dostępne począwszy od wersji modelu |
|--------------|--------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adres e-mail, na przykład`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>Adres URL

Internetowe adresy URL.

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                                          | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adresy URL do witryn sieci Web, na przykład`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>Adres IP

Adres protokołu internetowego

Języki:

* Publiczna wersja zapoznawcza:`English`

| Nazwa podtypu | Opis                              | Dostępne począwszy od wersji modelu |
|--------------|------------------------------------------|----------------------------------------|
| Nie dotyczy          | Adres sieciowy na przykład`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

Jednostki daty i godziny. 

* Dostępne począwszy od wersji modelu`2019-10-01`

Języki:

* Publiczna wersja zapoznawcza: `Chinese-Simplified` ,, `English` `French` `German` i`Spanish`

| Nazwa podtypu    | Przykłady                     |
|-------------|------------------------------|
| Nie dotyczy         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Data  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Czas trwania | `2.5 minutes`, `one and a half hours`         |
| Set | `every Saturday`         |

###  <a name="quantity"></a>Liczba

Liczby i ilości liczbowe. 

* Dostępne począwszy od wersji modelu`2019-10-01`

Języki:

* Publiczna wersja zapoznawcza: `Chinese-Simplified` ,, `English` `French` `German` i`Spanish`

| Nazwa podtypu    | Przykłady                     |
|-------------|------------------------------|
| Liczba         | `6`, `six`                   |
| Procentowe  | `50%`, `fifty percent`       |
| Liczbą     | `2nd`, `second`              |
| Wiek         | `90 day old`, `30 years old` |
| Waluta    | `$10.99`, `&euro;30.00`           |
| Wymiar   | `10 miles`, `40 cm`          |
| Temperatura | `32 degrees`, `10°C`         |
