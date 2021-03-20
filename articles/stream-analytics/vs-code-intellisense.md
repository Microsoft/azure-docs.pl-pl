---
title: Funkcja IntelliSense w Azure Stream Analytics narzędziach dla Visual Studio Code
description: W tym artykule opisano sposób korzystania z funkcji IntelliSense w Azure Stream Analytics narzędziach dla Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: how-to
ms.openlocfilehash: 988b9d364f09a4c88bcaab1fe81ae80c8de5d4d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96491574"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>Funkcja IntelliSense w Azure Stream Analytics narzędziach dla Visual Studio Code

Technologia IntelliSense jest dostępna dla [Stream Analytics języku zapytań](/stream-analytics-query/stream-analytics-query-language-reference?bc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fbread%2ftoc.json&toc=https%253a%2f%2fdocs.microsoft.com%2fazure%2fstream-analytics%2ftoc.json) w [Azure Stream Analytics Narzędzia dla vs Code](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview). IntelliSense to pomoc dla uzupełniania kodu, która obejmuje wiele funkcji: członków listy, informacji o parametrach, szybkich informacji i kompletnego wyrazu. Funkcje IntelliSense są czasami wywoływane przez inne nazwy, takie jak "uzupełnianie kodu", "Content Assist" i "podpowiedzi do kodu".

![Demonstracja IntelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Funkcje IntelliSense

Funkcje IntelliSense w Stream Analytics narzędziach dla VS Code są obsługiwane przez usługę języka. Usługa językowa analizuje kod źródłowy i zapewnia inteligentne uzupełnianie kodu na podstawie semantyki języka. Jeśli usługa językowa wie, że możliwe zakończenia, sugestie IntelliSense pojawiają się w trakcie wpisywania. Jeśli będziesz kontynuować wpisywanie, lista elementów członkowskich, takich jak zmienne i metody, jest filtrowana w celu uwzględnienia tylko elementów członkowskich, które zawierają wpisane znaki. Po naciśnięciu `Tab` klawiszy lub funkcja `Enter` IntelliSense Wstawia wybrany element członkowski.

Funkcję IntelliSense można wyzwolić w dowolnym oknie edytora, wpisując znak wyzwalacza, taki jak znak kropki `.` .

![Funkcja autouzupełniania IntelliSense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Widżet sugestie obsługuje filtrowanie CamelCase. Możesz wpisać litery, które są pisane wielkimi literami w nazwie metody, aby ograniczyć sugestie. Na przykład "CRA" szybko wyświetli "aplikacje".

### <a name="types-of-completions"></a>Typy uzupełniania

Narzędzia Stream Analytics dla VS Code IntelliSense oferują różne typy uzupełniania, w tym sugestie serwera języka, fragmenty kodu i proste uzupełnianie tekstu oparte na programie Word.

|Ukończenie     |  Typ       |
| ----- | ------- |
| Słowa kluczowe | `keyword`
| Funkcje | `build-in function`, `user defined function`  |
| Nazwa zestawu danych| `input`, `output`, `intermediate result set`|
| Nazwa kolumny zestawu danych|`input`, `intermediate result set`|

#### <a name="name-completion"></a>Uzupełnianie nazw

Oprócz automatycznego uzupełniania słów kluczowych Stream Analytics Narzędzia dla VS Code odczytuje listę nazw wejściowych i wyjściowych zadań, a także nazwy kolumn w źródłach danych, gdy są skonfigurowane. Rozszerzenie zapamiętuje te informacje, aby zapewnić funkcje uzupełniania nazw, które są przydatne do wprowadzania instrukcji z kilkoma naciśnięciami klawiszy:

Podczas kodowania nie trzeba opuszczać edytora, aby wykonywać wyszukiwania w nazwach wejściowych zadań, nazwach danych wyjściowych i nazwach kolumn. Możesz zachować kontekst, znaleźć potrzebne informacje, wstawić elementy bezpośrednio do kodu i zapewnić, że technologia IntelliSense ukończy wpisywanie.

Należy pamiętać, że należy skonfigurować lokalne dane wejściowe lub dane wejściowe na żywo i zapisać plik konfiguracji, aby można było używać uzupełniania nazw.

![uzupełnianie nazw](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Informacje o parametrach

Opcja **Informacje o parametrach** IntelliSense otwiera listę parametrów, która zawiera informacje o liczbie, nazwach i typach parametrów wymaganych przez funkcję. Parametr pogrubiony wskazuje następny parametr, który jest wymagany podczas wpisywania funkcji.

Lista parametrów jest również wyświetlana dla zagnieżdżonych funkcji. Jeśli wpiszesz funkcję jako parametr do innej funkcji, na liście parametrów zostaną wyświetlone parametry funkcji wewnętrznej. Następnie po zakończeniu listy parametrów funkcji wewnętrznej lista parametrów powraca do wyświetlania zewnętrznych parametrów funkcji.

![Informacje o parametrach](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Szybkie informacje

Zgodnie z informacjami w usłudze językowej, można wyświetlić **szybkie informacje** dla każdego identyfikatora w kodzie. Przykłady identyfikatorów są danymi wejściowymi, wyjściowymi, pośrednimi wynikami lub funkcją. Gdy przesuwasz wskaźnik myszy nad identyfikatorem, jego deklaracja zostanie wyświetlona w oknie podręcznym. Są wyświetlane właściwości i schematy danych dla wejść, jeśli są skonfigurowane, i pośredniego zestawu danych.

![Szybkie informacje](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Rozwiązywanie problemów z funkcją IntelliSense

Przyczyną tego problemu jest brak konfiguracji wejściowej, która udostępnia dane. Możesz sprawdzić, czy [lokalne](visual-studio-code-local-run.md#define-a-local-input) dane wejściowe lub na [żywo](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) zostały poprawnie skonfigurowane.

## <a name="next-steps"></a>Następne kroki

* [Szybki Start: Tworzenie zadania Azure Stream Analytics w programie Visual Studio Code](quick-create-visual-studio-code.md)
* [Testowanie Stream Analytics zapytań lokalnie z przykładowymi danymi przy użyciu Visual Studio Code](visual-studio-code-local-run.md)
* [Przetestuj Stream Analytics zapytań lokalnie względem danych wejściowych strumienia na żywo za pomocą Visual Studio Code](visual-studio-code-local-run-live-input.md)