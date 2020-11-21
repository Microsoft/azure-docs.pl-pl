---
title: Wytyczne dotyczące transkrypcji oznaczonych przez człowieka — usługa mowy
titleSuffix: Azure Cognitive Services
description: Aby poprawić dokładność rozpoznawania mowy, na przykład gdy wyrazy są usuwane lub nieprawidłowo zastępowane, można użyć transkrypcji z danymi audio. Transkrypcje oznaczone przez człowieka to słowo-by-Word, Verbatim transkrypcje pliku dźwiękowego.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: d1d62d36d5cc48edc198e5d7bfc49f7f31b89a2c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025620"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Jak utworzyć transkrypcje z etykietami ludzkimi

Jeśli chcesz poprawić dokładność rozpoznawania, szczególnie problemy, które są spowodowane usunięciem lub nieprawidłowym zastępowaniem wyrazów, musisz użyć transkrypcji z etykietą z danymi audio. Co to są transkrypcje z oznaczeniem personalnym? To bardzo proste. są one słowami "Verbatim" w pliku audio.

Do usprawnienia rozpoznawania jest wymagane duże przykładowe dane transkrypcji, dlatego zalecamy udostępnienie od 10 do 1 000 godzin danych transkrypcji. Na tej stronie zawarto Przegląd wytycznych dotyczących tworzenia transkrypcji o wysokiej jakości. Ten przewodnik jest podzielony na ustawienia regionalne, z sekcjami dla angielskiej wersji językowej USA, chińskiej (mandarynek) i niemieckiej.

## <a name="us-english-en-us"></a>Angielski (EN-US)

Transkrypcje o numerach oznaczonych przez człowieka dla dźwięku w języku angielskim muszą być dostarczane jako zwykły tekst, tylko przy użyciu znaków ASCII. Unikaj używania znaków interpunkcyjnych alfabetu łacińskiego-1 lub Unicode. Te znaki są często przypadkowo dodawane podczas kopiowania tekstu z aplikacji do przetwarzania wyrazów lub odpadków danych ze stron sieci Web. Jeśli te znaki są obecne, upewnij się, że zostały zaktualizowane przy użyciu odpowiedniego podstawiania kodu ASCII.

Oto kilka przykładów:

| Znaki do uniknięcia | Podstawienie | Uwagi |
| ------------------- | ------------ | ----- |
| "Hello World" | „Hello world” | Znaczniki otwierania i zamykania cudzysłowu zostały zastąpione odpowiednimi znakami ASCII. |
| Dzień Jan | Dzień Jan | Apostrof został zastąpiony odpowiednim znakiem ASCII. |
| to dobry — nie, był doskonały! | to dobry — nie, był doskonały! | Pauza została zastąpiona dwoma łącznikami. |

### <a name="text-normalization-for-us-english"></a>Normalizacja tekstu dla języka angielskiego (Stany Zjednoczone)

Normalizacja tekstu to transformacja wyrazów w spójnym formacie używanym podczas uczenia modelu. Niektóre reguły normalizacji są stosowane do tekstu automatycznie, ale zalecamy korzystanie z tych wytycznych podczas przygotowywania danych transkrypcji z etykietami ludzkimi:

- Napisz skróty w słowach.
- Napisz niestandardowe ciągi liczbowe w słowach (na przykład warunki księgowe).
- Znaki inne niż alfabetyczne lub mieszane znaki alfanumeryczne powinny być uzyskanego jako wymawiane.
- Skróty, które są wymawiane jako słowa, nie powinny być edytowane (takie jak "radary", "Laser", "RAM" lub "NATO").
- Napisz skróty, które są wymawiane jako oddzielne litery z każdą literą rozdzieloną spacją.

Poniżej przedstawiono kilka przykładów normalizacji, które należy wykonać w transkrypcji:

| Oryginalny tekst               | Tekst po normalizacji              |
| --------------------------- | ------------------------------------- |
| Transparent Dr Bruce            | Transparent Bruce                   |
| Tomasz, 007             | Tomasz, podwójny i siódmy           |
| Ke $ ha                       | Kesha                                 |
| Jak długo 2x4         | Jak długo dwa cztery           |
| Spotkanie przechodzi z 1-3pm | Spotkanie przechodzi od jednej do trzech PM |
| Mój typ krwi to O +         | Mój typ krwi to O wartości pozytywnej           |
| H20 wody                | Wodna jest 2 O                        |
| Odtwórz OU812 przez Van Halen     | Odtwórz O U 8 1 2 przez Van Halen           |
| UTF-8 with BOM              | U T F 8 z BOM                      |

Następujące reguły normalizacji są automatycznie stosowane do transkrypcji:

- Używaj małych liter.
- Usuń wszystkie znaki interpunkcyjne z wyjątkiem apostrofów w słowach.
- Rozwijaj liczbę do wyrazów/postaci głosowej, takich jak kwoty dolara.

Poniżej przedstawiono kilka przykładów normalizacji, które są wykonywane automatycznie podczas transkrypcji:

| Oryginalny tekst                          | Tekst po normalizacji          |
| -------------------------------------- | --------------------------------- |
| "Wielka krowa" Batman.               | Wielka krowa Batmana              |
| "Co?" Batman. | Co to jest Batman Robin |
| Przejdź do Get-em!                            | Uzyskaj em                         |
| Mam dwustronną wspólną                     | Jestem wspólnie rozłączony                |
| 104 Elm ulica                         | 1 0 4 Elm ulica            |
| Dostosuj do 102,7                          | Dostosuj do 1 0 2 punktu siedmiu    |
| Pi jest około 3,14                       | Pi ma około trzy punkty 1 4  |
| Koszty IT \$ 3,14                        | koszty IT 3 14           |

## <a name="mandarin-chinese-zh-cn"></a>Chińskie mandarynki (zh-CN)

Transkrypcje oznaczone przez człowieka dla mandarynek w języku chińskim, muszą być kodowane przy użyciu znacznika kolejności bajtów. Unikaj używania znaków interpunkcyjnych połówkowej szerokości. Te znaki mogą być przypadkowo uwzględniane podczas przygotowywania danych w programie przetwarzania tekstu lub danych odpadków ze stron sieci Web. Jeśli te znaki są obecne, upewnij się, że zostały zaktualizowane przy użyciu odpowiedniego podstawiania pełnej szerokości.

Oto kilka przykładów:

| Znaki do uniknięcia | Podstawienie   | Uwagi |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | Znaki otwierające i zamykające cudzysłowy zostały zastąpione odpowiednimi znakami. |
| 需要什么帮助? | 需要什么帮助？| Znak zapytania został zastąpiony odpowiednim znakiem. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalizacja tekstu dla elementu chińskiego mandarynki

Normalizacja tekstu to transformacja wyrazów w spójnym formacie używanym podczas uczenia modelu. Niektóre reguły normalizacji są stosowane do tekstu automatycznie, ale zalecamy korzystanie z tych wytycznych podczas przygotowywania danych transkrypcji z etykietami ludzkimi:

- Napisz skróty w słowach.
- Napisz ciągi liczbowe w postaci wymawianej.

Poniżej przedstawiono kilka przykładów normalizacji, które należy wykonać w transkrypcji:

| Oryginalny tekst | Tekst po normalizacji |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

Następujące reguły normalizacji są automatycznie stosowane do transkrypcji:

- Usuń wszystkie znaki interpunkcyjne
- Rozwiń liczbę liczb do formularza mówionego
- Konwertuj litery o pełnej szerokości na litery o połówkowej szerokości
- Używanie wielkich liter w przypadku wszystkich wyrazów angielskich

Poniżej przedstawiono kilka przykładów normalizacji, które są wykonywane automatycznie podczas transkrypcji:

| Oryginalny tekst | Tekst po normalizacji |
| ------------- | ------------------------ |
| 3,1415 | 三 点 一 四 一 五 |
| ¥3,5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Niemiecki (de-DE) i inne języki

Zapisy dla ludzi z etykietami dla języka niemieckiego (i innych języków w językach innych niż angielski lub mandarynki) muszą być kodowane w formacie UTF-8 przy użyciu znacznika kolejności bajtów. Dla każdego pliku audio należy podać jeden z etykiet oznaczonych przez człowieka.

### <a name="text-normalization-for-german"></a>Normalizacja tekstu dla języka niemieckiego

Normalizacja tekstu to transformacja wyrazów w spójnym formacie używanym podczas uczenia modelu. Niektóre reguły normalizacji są stosowane do tekstu automatycznie, ale zalecamy korzystanie z tych wytycznych podczas przygotowywania danych transkrypcji z etykietami ludzkimi:

- Zapisz punkty dziesiętne jako ",", a nie ".".
- Napisz separatory czasu jako ":", a nie "." (na przykład: 12:00 Uhr).
- Skróty, takie jak "CA". nie są zastępowane. Zalecamy używanie pełnego formularza mówionego.
- Cztery główne operatory matematyczne (+,-, \* i/) są usuwane. Zalecamy zastępowanie ich w formie zapisanej: "+", "" minus, "" źle, "i" geteilt ".
- Operatory porównania są usuwane (=, < i >). Zalecamy zastępowanie ich przy użyciu "Gleich", "Kleiner ALS" i "grösser ALS".
- Zapisz ułamki, takie jak 3/4, w formie pisanej (na przykład: "Drei Viertel" zamiast 3/4).
- Zamień symbol "€" na jego zapisaną formę "euro".

Poniżej przedstawiono kilka przykładów normalizacji, które należy wykonać w transkrypcji:

| Oryginalny tekst    | Tekst po normalizacji użytkownika | Tekst po normalizacji systemu       |
| ---------------- | ----------------------------- | ------------------------------------- |
| ES, ist 12,23 Uhr | ES, ist 12:23 Uhr              | Es ist Zwölf Uhr Drei und zwanzig Uhr |
| {12,45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3-4        | 2 plus 3 minus 4              | Zwei Plus Drei minus Vier             |

Następujące reguły normalizacji są automatycznie stosowane do transkrypcji:

- Używaj małych liter dla całego tekstu.
- Usuń wszystkie znaki interpunkcyjne, w tym różne typy cudzysłowów ("test", "test", "test" i «test»).
- Odrzuć wiersze ze wszystkimi znakami specjalnymi z tego zestawu: ¢ ¤ ¥ ¦ § © ª ¬® ° 2H μ × ÿ o ¬ ¬.
- Rozwiń liczbę liczb do formy mówionej, w tym kwoty dolara lub euro.
- Akceptuj tylko w przypadku, o i. Inne zostaną zastąpione przez "TH" lub odrzucane.

Poniżej przedstawiono kilka przykładów normalizacji, które są wykonywane automatycznie podczas transkrypcji:

| Oryginalny tekst    | Tekst po normalizacji |
| ---------------- | ------------------------ |
| Pierścień Frankfurter | pierścień Frankfurter         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie i testowanie danych](./how-to-custom-speech-test-and-train.md)
- [Inspekcja danych](how-to-custom-speech-inspect-data.md)
- [Oceń dane](how-to-custom-speech-evaluate-data.md)
- [Szkolenie modelu](how-to-custom-speech-train-model.md)
- [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)