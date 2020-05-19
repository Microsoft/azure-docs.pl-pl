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
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 864f6a6d92306c40713f66b526c8a8df1683d3c4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586800"
---
# <a name="add-profanity-filtering-with-the-translator"></a>Dodawanie filtrowania niewulgarności przy użyciu translatora

Zwykle usługa translator zachowuje braki, która jest obecna w źródle w tłumaczeniu. Stopień nieseksu i kontekst, który sprawia, że wyrazy nie różnią się między kulturami. W efekcie stopień niedostępności w języku docelowym może być wzmacniany lub zmniejszany.

Jeśli chcesz uniknąć wyświetlania niedostępności w tłumaczeniu, nawet jeśli w tekście źródłowym znajduje się brak wulgarności, użyj opcji filtrowania wulgarności dostępnej w metodzie tłumaczenia (). Ta opcja umożliwia wybranie, czy ma zostać wyświetlona liczba usuniętych nietrwałych, oznaczonych za pomocą odpowiednich tagów, czy nie podejmować żadnych akcji.

Metoda Przetłumacz () przyjmuje parametr "Options", który zawiera nowy element "ProfanityAction". Akceptowane wartości ProfanityAction to "NoAction", "oznaczył" i "usunięte".

## <a name="accepted-values-of-profanityaction-and-examples"></a>Zaakceptowane wartości ProfanityAction i przykładów
|ProfanityAction wartość | Akcja | Przykład: Źródło — japoński | Przykład: Target — angielski|
| :---|:---|:---|:---|
| NoAction | Domyślne. Analogicznie jak ustawienie opcji nie jest możliwe. Niezbyt wulgarne przekazanie z lokalizacji źródłowej do docelowej. | 彼は変態です. | Jest to jerk. |
| Oznacz | Wyrazy wulgarne są otoczone \< nie>ami tagów XML... \< /profanity>. | 彼は変態です. | Jest to \< wulgarne>Jerk \< /profanity>. |
| Usunięte | Wyrazy wulgarne są usuwane z danych wyjściowych bez zastępowania. | 彼は. | Jest to. |

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Stosowanie filtrowania wulgarności przy użyciu wywołania translatora](reference/v3-0-translate.md)
