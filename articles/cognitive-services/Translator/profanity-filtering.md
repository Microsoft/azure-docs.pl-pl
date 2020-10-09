---
title: Filtrowanie niewulgarności — translator
titleSuffix: Azure Cognitive Services
description: Użyj filtrowania wulgarności, aby określić poziom niewulgarnego tłumaczenia tekstu w usłudze Azure Cognitive Services translator.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7ebfe766e6362a3f62e70db8bf2dcae370aceee3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83996163"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Dodawanie filtrowania niewulgarności przy użyciu translatora

Zwykle usługa translator zachowuje braki, która jest obecna w źródle w tłumaczeniu. Stopień nieseksu i kontekst, który sprawia, że wyrazy nie różnią się między kulturami. W efekcie stopień niedostępności w języku docelowym może być wzmacniany lub zmniejszany.

Jeśli chcesz uniknąć wyświetlania niedostępności w tłumaczeniu, nawet jeśli w tekście źródłowym znajduje się brak wulgarności, użyj opcji filtrowania wulgarności dostępnej w metodzie tłumaczenia (). Ta opcja umożliwia wybranie, czy ma zostać wyświetlona liczba usuniętych nietrwałych, oznaczonych za pomocą odpowiednich tagów, czy nie podejmować żadnych akcji.

Metoda Przetłumacz () przyjmuje parametr "Options", który zawiera nowy element "ProfanityAction". Akceptowane wartości ProfanityAction to "NoAction", "oznaczył" i "usunięte".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Zaakceptowane wartości ProfanityAction i przykładów
|ProfanityAction wartość | Akcja | Przykład: Źródło — japoński | Przykład: Target — angielski|
| :---|:---|:---|:---|
| NoAction | Domyślne. Analogicznie jak ustawienie opcji nie jest możliwe. Niezbyt wulgarne przekazanie z lokalizacji źródłowej do docelowej. | 彼は変態です. | Jest to jerk. |
| Oznacz | Wyrazy wulgarne są otoczone tagami XML \<profanity> .. \</profanity> . | 彼は変態です. | Jest to \<profanity> Jerk \</profanity> . |
| Usunięte | Wyrazy wulgarne są usuwane z danych wyjściowych bez zastępowania. | 彼は. | Jest to. |

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Stosowanie filtrowania wulgarności przy użyciu wywołania translatora](reference/v3-0-translate.md)
