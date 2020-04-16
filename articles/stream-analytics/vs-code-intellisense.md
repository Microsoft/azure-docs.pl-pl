---
title: IntelliSense w narzędziach usługi Azure Stream Analytics dla kodu programu Visual Studio
description: W tym artykule opisano sposób korzystania z funkcji intellisense w narzędziach usługi Azure Stream Analytics dla programu Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 4/11/2020
ms.topic: conceptual
ms.openlocfilehash: 25ece3174cd65df9c665888d913cf6b9029904b1
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394399"
---
# <a name="intellisense-in-azure-stream-analytics-tools-for-visual-studio-code"></a>IntelliSense w narzędziach usługi Azure Stream Analytics dla kodu programu Visual Studio

Technologia IntelliSense jest dostępna w [języku zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fstream-analytics%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure%2Fbread%2Ftoc.json) w [narzędziach usługi Azure Stream Analytics dla programu VS Code.](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa&ssr=false#overview) IntelliSense to pomoc w wypełnianiu kodu, która zawiera wiele funkcji: Lista członków, Informacje o parametrach, Szybkie informacje i Pełne słowo. Funkcje IntelliSense są czasami wywoływane przez inne nazwy, takie jak "uzupełnianie kodu", "pomoc w zawartości" i "podpowiedzi do kodu".

![Prezentacja IntelliSense](./media/vs-code-intellisense/intellisense.gif)

## <a name="intellisense-features"></a>Funkcje IntelliSense

Funkcje IntelliSense w narzędziach usługi Stream Analytics dla programu VS Code są obsługiwane przez usługę językową. Usługa języka analizuje kod źródłowy i zapewnia inteligentne uzupełnianie kodu na podstawie semantyki języka. Jeśli usługa językowa zna możliwe uzupełnienia, sugestie IntelliSense pojawiają się podczas pisania. Jeśli nadal piszesz, lista elementów członkowskich, takich jak zmienne i metody, jest filtrowana, aby uwzględnić tylko elementy członkowskie, które zawierają wpisane znaki. Po naciśnięciu `Tab` `Enter` klawiszy lub IntelliSense wstawia wybrany element członkowski.

IntelliSense można wyzwolić w dowolnym oknie edytora, wpisując znak `.`wyzwalacza, taki jak znak kropki .

![autouzupełnienie intellisense](./media/vs-code-intellisense/auto-completion.gif)

> [!TIP]
> Widżet sugestie obsługuje filtrowanie CamelCase. Można wpisać litery, które są pisane wielkimi literami w nazwie metody, aby ograniczyć sugestie. Na przykład "cra" szybko wywoła "createApplication".

### <a name="types-of-completions"></a>Rodzaje uzupełnień

Narzędzia analizy strumienia dla programu VS Code IntelliSense oferują różne typy uzupełnień, w tym sugestie serwera języka, fragmenty kodu i proste uzupełnienia tekstowe oparte na programach word.

|       |         |       |
| ----- | ------- | ----- |
| Słowa kluczowe | `keyword`
| Funkcje | `build-in function`, `user defined function`  |
| Nazwa zestawu danych| `input`, `output`, `intermediate result set`|
| Nazwa kolumny zestawu danych|`input`, `intermediate result set`|
|

#### <a name="name-completion"></a>Uzupełnianie nazwy

Oprócz automatycznego uzupełniania słów kluczowych narzędzia usługi Stream Analytics dla programu VS Code odczytują listę nazw wejściowych i wyjściowych zadań, a także nazwy kolumn w źródłach danych, gdy są skonfigurowane. Rozszerzenie zapamiętuje te informacje, aby zapewnić możliwości uzupełniania nazw, które są przydatne do wprowadzania instrukcji z kilkoma naciśnięciami klawiszy:

Podczas kodowania nie trzeba opuszczać edytora, aby wykonywać wyszukiwania nazw wejściowych zadań, nazwy danych wyjściowych i nazw kolumn. Możesz zachować swój kontekst, znaleźć potrzebne informacje, wstawić elementy bezpośrednio do kodu i poprosić IntelliSense o ukończenie pisania.

Należy zauważyć, że należy skonfigurować dane wejściowe lokalne lub dane wejściowe na żywo i zapisać plik konfiguracyjny, aby móc używać uzupełniania nazw.

![uzupełnianie nazwy](./media/vs-code-intellisense/name-completion.gif)

### <a name="parameter-info"></a>Informacje o parametrach

Opcja IntelliSense **Parameter Info** otwiera listę parametrów zawierającą informacje o liczbie, nazwach i typach parametrów wymaganych przez funkcję. Pogrubienie parametru wskazuje następny parametr, który jest wymagany podczas wpisywania funkcji.

Lista parametrów jest również wyświetlana dla funkcji zagnieżdżonych. Jeśli wpiszesz funkcję jako parametr do innej funkcji, na liście parametrów zostaną wyświetlone parametry funkcji wewnętrznej. Następnie po zakończeniu listy parametrów funkcji wewnętrznej lista parametrów zostanie przywrócona do wyświetlania parametrów funkcji zewnętrznej.

![informacje o parametrach](./media/vs-code-intellisense/parameter-info.gif)

### <a name="quick-info"></a>Szybkie informacje

Zgodnie z usługą języka, można wyświetlić **szybkie informacje** dla każdego identyfikatora w kodzie. Niektóre przykłady identyfikatorów są dane wejściowe, dane wyjściowe, zestaw wyników pośrednich lub funkcji. Po przesunięciu wskaźnika myszy na identyfikator jego deklaracja jest wyświetlana w wyskakującym oknie. Właściwości i schematy danych dla danych wejściowych, jeśli są skonfigurowane, i pośredni zestaw danych są wyświetlane.

![szybkie informacje](./media/vs-code-intellisense/quick-info.gif)

## <a name="troubleshoot-intellisense"></a>Rozwiązywanie problemów z intellisense

Ten problem jest spowodowany przez brak konfiguracji wejściowej, która dostarcza danych. Można sprawdzić, czy [lokalne wejście lub](visual-studio-code-local-run.md#define-a-local-input) dane [wejściowe na żywo](visual-studio-code-local-run-live-input.md#define-a-live-stream-input) zostały poprawnie skonfigurowane.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie zadania usługi Azure Stream Analytics w programie Visual Studio Code](quick-create-vs-code.md)
* [Testowanie zapytań usługi Stream Analytics lokalnie z przykładowymi danymi przy użyciu kodu programu Visual Studio](visual-studio-code-local-run.md)
* [Testowanie usługi Stream Analytics zapytania lokalnie względem danych wejściowych strumienia na żywo przy użyciu programu Visual Studio Code](visual-studio-code-local-run-live-input.md)
