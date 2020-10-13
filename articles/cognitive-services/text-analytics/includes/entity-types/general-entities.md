---
title: Ogólne nazwane jednostki
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: 630f04bf2cc9e7de6331f9d25754a20fe2327d76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779575"
---
Następujące kategorie jednostek są zwracane podczas wysyłania żądań do `/entities/recognition/general` punktu końcowego.

| Kategoria   | Subcategory (Podkategoria) | Opis                          | Uruchamianie wersji modelu                                                    | Uwagi |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| Osoba     | Nie dotyczy         | Nazwy osób.  | `2019-10-01`  | Zwrócone także przez NER v 2.1 |
| Persontype | Nie dotyczy         | Typy zadań lub role przechowywane przez osobę. | `2020-02-01` | |
|Lokalizacja    | Nie dotyczy         | Punkty orientacyjne, struktury, funkcje geograficzne i geopolityczne, w naturalny i ludzki sposób     |  `2019-10-01` | Zwrócone także przez NER v 2.1 |
|Lokalizacja     | Jednostka geopolityczna (GPE)        | Miasta, kraje/regiony, Stany.      | `2020-02-01` | |
|Lokalizacja     | Strukturalnych                       | Struktury zbudowanych. | `2020-04-01` | |
|Lokalizacja     | Geograficznych       | Funkcje geograficzne i naturalne, takie jak rzek, oceany i Deserts. |  `2020-04-01` | |
|Organizacja  | Nie dotyczy | Firmy, grupy polityczne, zespoły muzyczne, trefle sportowe, instytucje rządowe i organizacje publiczne.  | `2019-10-01` | Narodowe i religijne nie są uwzględnione w tym typie jednostki. Zwrócone także przez NER v 2.1 |
|Organizacja | Leczniczych | Firmy medyczne i grupy. | `2020-04-01` |  |
|Organizacja | Wymiana zapasowa | Giełdowe grupy wymiany. | `2020-04-01` | |
| Organizacja | Sports | Organizacje dotyczące sportu. | `2020-04-01` |  |
| Wydarzenie  | Nie dotyczy | Zdarzenia historyczne, społeczne i wystąpienia naturalne. | `2020-02-01` |  |
| Wydarzenie  | Twórcz | Wydarzenia i święta kulturowe. | `2020-04-01` | |
| Wydarzenie  | Tocz | Zdarzenia występujące w naturalny sposób. | `2020-04-01` |  |
| Wydarzenie  | Sports | Wydarzenia sportowe.  | `2020-04-01` | |
| Produkt | Nie dotyczy | Obiekty fizyczne różnych kategorii. | `2020-02-01` | |
| Produkt | Produkty obliczeniowe | Produkty obliczeniowe. |  `2020-02-01 ` | |
| Czy | Nie dotyczy | Możliwość, umiejętność lub doświadczenie. | `2020-02-01` |  |
| Adres | Nie dotyczy | Pełne adresy wysyłkowe.  | `2020-04-01` |  |
| PhoneNumber | Nie dotyczy | Numery telefonów (tylko numery telefonów USA i UE). | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Poczta e-mail | Nie dotyczy | Adresy e-mail. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Adres URL | Nie dotyczy | Adresy URL do witryn sieci Web. | `2019-10-01` | Zwrócone także przez NER v 2.1  |
| Adres IP | Nie dotyczy | Adresy IP sieci. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| DateTime | Nie dotyczy | Daty i godziny dnia. | `2019-10-01` | Zwrócone także przez NER v 2.1 | 
| Data i godzina | Data | Daty kalendarzowe. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Data i godzina | Godzina | Razy dziennie | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Data i godzina | Zakres dat | Zakresy dat. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Data i godzina | Zakres czasu | Zakresy czasu. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Data i godzina | Czas trwania | Czasów trwania. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Data i godzina | Zestaw | Ustawianie, powtarzające się czasy. |  `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Liczba | Nie dotyczy | Liczby i ilości liczbowe. | `2019-10-01` | Zwrócone także przez NER v 2.1  |
| Ilość | Liczba | Numery. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Ilość | Procent | Wartości procentowe.| `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Ilość | Liczba porządkowa | Liczby porządkowe. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Ilość | Wiek | Ważności. | `2019-10-01` |  Zwrócone także przez NER v 2.1 |
| Ilość | Waluta | Waluty. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Ilość | Wymiar | Wymiary i pomiary. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
| Ilość | Temperatura | Nosząc. | `2019-10-01` | Zwrócone także przez NER v 2.1 |
