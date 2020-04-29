---
title: Filtrowanie niewulgarności — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Użyj filtrowania wulgarności, aby określić poziom niewulgarnego tłumaczenia tekstu w interfejs API tłumaczenia tekstu w usłudze Translator Cognitive Services platformy Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: e7e2097e0d3daf360f1fa0f30bf3fd2c62c07163
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73836228"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Dodawanie filtrowania niewulgarności za pomocą interfejs API tłumaczenia tekstu w usłudze Translator

Zwykle usługa translator zachowuje braki, która jest obecna w źródle w tłumaczeniu. Stopień nieseksu i kontekst, który sprawia, że wyrazy nie różnią się między kulturami. W efekcie stopień niedostępności w języku docelowym może być wzmacniany lub zmniejszany.

Jeśli chcesz uniknąć wyświetlania niedostępności w tłumaczeniu, nawet jeśli w tekście źródłowym znajduje się brak wulgarności, użyj opcji filtrowania wulgarności dostępnej w metodzie tłumaczenia (). Ta opcja umożliwia wybranie, czy ma zostać wyświetlona liczba usuniętych nietrwałych, oznaczonych za pomocą odpowiednich tagów, czy nie podejmować żadnych akcji.

Metoda Przetłumacz () przyjmuje parametr "Options", który zawiera nowy element "ProfanityAction". Akceptowane wartości ProfanityAction to "NoAction", "oznaczył" i "usunięte".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Zaakceptowane wartości ProfanityAction i przykładów
|ProfanityAction wartość | Akcja | Przykład: Źródło — japoński | Przykład: Target — angielski|
| :---|:---|:---|:---|
| NoAction | Domyślne. Analogicznie jak ustawienie opcji nie jest możliwe. Niezbyt wulgarne przekazanie z lokalizacji źródłowej do docelowej. | 彼は変態です. | Jest to jerk. |
| Oznacz | Wyrazy wulgarne są otoczone nie>ami \<tagów XML... \</profanity>. | 彼は変態です. | Jest to \<wulgarne>jerk\</profanity>. |
| Usunięte | Wyrazy wulgarne są usuwane z danych wyjściowych bez zastępowania. | 彼は. | Jest to. |

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Zastosuj filtrowanie wulgarne przy użyciu wywołania interfejsu API usługi translator](reference/v3-0-translate.md)
