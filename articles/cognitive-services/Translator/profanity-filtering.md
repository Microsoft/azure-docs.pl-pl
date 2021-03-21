---
title: Filtrowanie niewulgarności — translator
titleSuffix: Azure Cognitive Services
description: Użyj filtrowania wulgarności, aby określić poziom niewulgarnego tłumaczenia tekstu w usłudze Azure Cognitive Services translator.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 9f389d669e69dbfa6ec6d4d0b4716d2367443f17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896803"
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
