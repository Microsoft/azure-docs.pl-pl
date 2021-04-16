---
title: Przewodnik informacyjny dotyczący funkcji w wyrażeniach
description: Przewodnik informacyjny dotyczący funkcji w wyrażeniach dla Azure Logic Apps i Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: reference
ms.date: 03/30/2021
ms.openlocfilehash: d2ea08551299d66edd919a828877c134c84ef938
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477778"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Przewodnik informacyjny dotyczący używania funkcji w wyrażeniach dla Azure Logic Apps i Power Automate

W przypadku [](../logic-apps/logic-apps-overview.md) definicji przepływu pracy w Azure Logic Apps [](../logic-apps/logic-apps-workflow-definition-language.md#expressions) i [Power Automate](/flow/getting-started)niektóre wyrażenia uzyskają wartości z akcji środowiska uruchomieniowego, które mogą jeszcze nie istnieć, gdy przepływ pracy zacznie działać. Aby odwołać się do tych wartości lub przetworzyć wartości w tych wyrażeniach, można użyć funkcji *dostarczonych* przez język definicji [przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Ta strona referencyjna dotyczy zarówno Azure Logic Apps, jak i Power Automate, ale jest wyświetlana w Azure Logic Apps dokumentacji. Chociaż ta strona odnosi się w szczególności do aplikacji logiki, funkcje te działają zarówno w przypadku przepływów, jak i aplikacji logiki. Aby uzyskać więcej informacji na temat funkcji i wyrażeń w Power Automate, zobacz [Używanie wyrażeń w warunkach](/flow/use-expressions-in-conditions).

Na przykład można obliczyć wartości przy użyciu funkcji matematycznych, takich jak [funkcja add(),](../logic-apps/workflow-definition-language-functions-reference.md#add) gdy chcesz uzyskać sumę z liczb całkowitych lub zmiennoprzecinków. Poniżej znajdują się inne przykładowe zadania, które można wykonywać za pomocą funkcji:

| Zadanie | Składnia funkcji | Wynik |
| ---- | --------------- | ------ |
| Zwraca ciąg w formacie małych liter. | toLower('<*text*>') <p>Na przykład: toLower('Hello') | "hello" |
| Zwraca unikatowy identyfikator globalny (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Aby znaleźć funkcje [na podstawie ich ogólnego przeznaczenia,](#ordered-by-purpose)zapoznaj się z następującymi tabelami. Aby uzyskać szczegółowe informacje o każdej funkcji, zobacz [listę alfabetyczną](#alphabetical-list).

## <a name="functions-in-expressions"></a>Funkcje w wyrażeniach

Aby pokazać, jak używać funkcji w wyrażeniu, w tym przykładzie pokazano, jak pobrać wartość z parametru i przypisać tę wartość do właściwości przy użyciu funkcji `customerName` `accountName` [parameters()](#parameters) w wyrażeniu:

```json
"accountName": "@parameters('customerName')"
```

Poniżej podano inne ogólne sposoby używania funkcji w wyrażeniach:

| Zadanie | Składnia funkcji w wyrażeniu |
| ---- | -------------------------------- |
| Wykonaj pracę z elementem, przekazując ten element do funkcji. | " \@ < *functionName*>(<*item*>)" |
| 1. Pobierz wartość *parametru parameterName* za pomocą zagnieżdżonych `parameters()` funkcji. </br>2. Wykonaj pracę z wynikiem, przekazując tę wartość do *właściwości functionName*. | " \@ < *functionName*>(parameters('<*parameterName*>'))" |
| 1. Pobierz wynik z zagnieżdżonych funkcji wewnętrznej *functionName*. </br>2. Przekaż wynik do funkcji zewnętrznej *functionName2*. | " \@ < *functionName2*>(<*functionName*>(<*item*>))" |
| 1. Pobierz wynik z *funkcji functionName*. </br>2. Biorąc pod uwagę, że wynik jest obiektem z *właściwością propertyName,* pobierz wartość tej właściwości. | " \@ < *functionName*>(<*item*>).<*propertyName*>" |
|||

Na przykład funkcja `concat()` może przyjąć co najmniej dwie wartości ciągu jako parametry. Ta funkcja łączy te ciągi w jeden ciąg. Możesz przekazać literały ciągu, na przykład "Sophia" i "Owen", aby uzyskać połączony ciąg "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Możesz też pobrać wartości ciągu z parametrów. W tym przykładzie `parameters()` użyto funkcji w każdym `concat()` parametrze oraz `firstName` parametrach i `lastName` . Następnie przekaż wynikowe ciągi do funkcji, aby uzyskać połączony ciąg, na przykład `concat()` "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

W obu przypadkach oba przykłady przypisz wynik do `customerName` właściwości .

Poniżej znajdują się inne uwagi dotyczące funkcji w wyrażeniach:

* Parametry funkcji są oceniane od lewej do prawej.

* W składni definicji parametrów znak zapytania (?) wyświetlany po parametrze oznacza, że parametr jest opcjonalny. Na przykład zobacz [getFutureTime()](#getFutureTime).

W poniższych sekcjach funkcje są uporządkowane w oparciu o ich przeznaczenie ogólne lub można je przeglądać w [kolejności alfabetycznej.](#alphabetical-list)

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funkcje ciągów

Do pracy z ciągami można użyć tych funkcji ciągów, a także niektórych [funkcji kolekcji](#collection-functions). Funkcje ciągów działają tylko na ciągach.

| Funkcja String | Zadanie |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Połącz co najmniej dwa ciągi i zwróć połączony ciąg. |
| [Endswith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Sprawdź, czy ciąg kończy się określonym podciągiem. |
| [Formatnumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Zwracanie liczby jako ciągu na podstawie określonego formatu |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Wygeneruj unikatowy identyfikator globalny (GUID) jako ciąg. |
| [Indexof](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Zwróć pozycję początkową dla podciągu. |
| [Lastindexof](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Zwraca pozycję początkową dla ostatniego wystąpienia podciągu. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Zwraca liczbę elementów w ciągu lub tablicy. |
| [Zastąpić](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Zastąp podciąg określonym ciągiem i zwróć zaktualizowany ciąg. |
| [Split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Zwraca tablicę zawierającą ciągi podrzędne oddzielone przecinkami od większego ciągu na podstawie określonego znaku ogranicznika w oryginalnym ciągu. |
| [Startswith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Sprawdź, czy ciąg zaczyna się od określonego podciągu. |
| [Podciąg](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Zwraca znaki z ciągu, zaczynając od określonej pozycji. |
| [Tolower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Zwraca ciąg w formacie małych liter. |
| [Toupper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Zwraca ciąg w formacie wielkich liter. |
| [Przycinanie](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Usuń wiodące i końcowe białe znaki z ciągu i zwróć zaktualizowany ciąg. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funkcje zbierania

Aby pracować z kolekcjami, zazwyczaj tablicami, ciągami, a czasami słownikami, można użyć tych funkcji kolekcji.

| Funkcja Collection | Zadanie |
| ------------------- | ---- |
| [Zawiera](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Sprawdź, czy kolekcja ma określony element. |
| [puste](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Sprawdź, czy kolekcja jest pusta. |
| [Pierwszym](../logic-apps/workflow-definition-language-functions-reference.md#first) | Zwróć pierwszy element z kolekcji. |
| [Przecięcia](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Zwraca kolekcję, która *zawiera tylko* wspólne elementy w określonych kolekcjach. |
| [Element](../logic-apps/workflow-definition-language-functions-reference.md#item) | Gdy element znajduje się wewnątrz powtarzających się akcji na tablicy, zwróć bieżący element w tablicy podczas bieżącej iteracji akcji. |
| [Dołączyć](../logic-apps/workflow-definition-language-functions-reference.md#join) | Zwraca ciąg zawierający wszystkie *elementy* z tablicy rozdzielone określonym znakiem. |
| [Ostatnio](../logic-apps/workflow-definition-language-functions-reference.md#last) | Zwróć ostatni element z kolekcji. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Zwraca liczbę elementów w ciągu lub tablicy. |
| [Pominąć](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Usuń elementy z przodu kolekcji i zwróć *wszystkie inne* elementy. |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Zwraca elementy z przodu kolekcji. |
| [Unii](../logic-apps/workflow-definition-language-functions-reference.md#union) | Zwraca kolekcję, która *zawiera wszystkie* elementy z określonych kolekcji. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funkcje porównania logicznego

Aby pracować z warunkami, porównywać wartości i wyniki wyrażeń lub oceniać różne rodzaje logiki, można użyć tych funkcji porównania logicznego. Pełne informacje na temat każdej funkcji można znaleźć na [alfabetycznej liście](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Jeśli do porównywania wartości używasz funkcji logicznych lub warunków, wartości null są konwertowane na wartości puste ciągów `""` (). Zachowanie warunków różni się w przypadku porównywania z pustym ciągiem zamiast wartości null. Aby uzyskać więcej informacji, zobacz [funkcję string().](#string) 

| Funkcja porównania logicznego | Zadanie |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Sprawdź, czy wszystkie wyrażenia są prawdziwe. |
| [Równa](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Sprawdź, czy obie wartości są równoważne. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Sprawdź, czy pierwsza wartość jest większa niż druga wartość. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Sprawdź, czy pierwsza wartość jest większa niż lub równa drugiej wartości. |
| [Jeśli](../logic-apps/workflow-definition-language-functions-reference.md#if) | Sprawdź, czy wyrażenie ma wartość true czy false. Na podstawie wyniku zwróć określoną wartość. |
| [Mniej](../logic-apps/workflow-definition-language-functions-reference.md#less) | Sprawdź, czy pierwsza wartość jest mniejsza niż druga wartość. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Sprawdź, czy pierwsza wartość jest mniejsza niż lub równa drugiej wartości. |
| [Nie](../logic-apps/workflow-definition-language-functions-reference.md#not) | Sprawdź, czy wyrażenie ma wartość false. |
| [lub](../logic-apps/workflow-definition-language-functions-reference.md#or) | Sprawdź, czy co najmniej jedno wyrażenie ma wartość true. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funkcje konwersji

Aby zmienić typ lub format wartości, można użyć tych funkcji konwersji. Na przykład można zmienić wartość z wartości logicznych na liczbę całkowitą. Aby uzyskać więcej informacji na temat Logic Apps typów zawartości podczas konwersji, zobacz [Handle content types (Obsługa typów zawartości).](../logic-apps/logic-apps-content-type.md) Pełne informacje na temat każdej funkcji można znaleźć na [alfabetycznej liście](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Azure Logic Apps automatycznie lub niejawnie wykonuje kodowanie i dekodowanie base64, dzięki czemu nie trzeba ręcznie wykonywać tych konwersji przy użyciu funkcji kodowania i dekodowania. Jeśli jednak i tak użyjemy tych funkcji w projektancie, w projektancie mogą wystąpić nieoczekiwane zachowania renderowania. Te zachowania mają wpływ tylko na widoczność funkcji, a nie na ich wpływ, chyba że edytujesz wartości parametrów funkcji, co powoduje usunięcie funkcji i ich efektów z kodu. Aby uzyskać więcej informacji, zobacz [Niejawne konwersje typów danych](#implicit-data-conversions).

| Funkcja konwersji | Zadanie |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Zwraca tablicę z jednego określonego wejścia. Aby uzyskać informacje o wielu danych wejściowych, [zobacz createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Zwróć wersję zakodowaną w formacie base64 dla ciągu. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Zwróć wersję binarną dla ciągu zakodowanego w formacie base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Zwróć wersję ciągu dla ciągu zakodowanego w formacie base64. |
| [Binarnym](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Zwróć wersję binarną dla wartości wejściowej. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Zwróć wersję logiczną dla wartości wejściowej. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Zwraca tablicę z wielu danych wejściowych. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Zwróć wartość URI danych dla wartości wejściowej. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Zwróć wersję binarną dla danych URI. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Zwróć wersję ciągu dla wartości URI danych. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Zwraca wersję ciągu dla ciągu zakodowanego w formacie base64. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Zwraca wersję binarną dla danych URI. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Zwraca ciąg, który zastępuje znaki ucieczki zdekodowane wersje. |
| [Encodeuricomponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Zwraca ciąg, który zastępuje znaki niebezpieczne dla adresu URL znakami ucieczki. |
| [liczba zmiennoprzecinkowa](../logic-apps/workflow-definition-language-functions-reference.md#float) | Zwraca liczbę zmiennoprzecinkową dla wartości wejściowej. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Zwraca wersję liczby całkowitej dla ciągu. |
| [Json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Zwróć wartość JavaScript Object Notation typu JSON (JSON) dla ciągu lub kodu XML. |
| [ciąg](../logic-apps/workflow-definition-language-functions-reference.md#string) | Zwraca wersję ciągu dla wartości wejściowej. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Zwróć wersję zakodowaną w formacie URI dla wartości wejściowej, zastępując znaki niebezpieczne dla adresu URL znakami ucieczki. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Zwróć wersję binarną dla ciągu zakodowanych w formacie URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Zwróć wersję ciągu dla ciągu zakodowanego w formacie URI. |
| [Xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Zwróć wersję XML dla ciągu. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>Niejawne konwersje typów danych

Azure Logic Apps są automatycznie lub niejawnie konwertowane między niektórymi typami danych, dzięki czemu nie trzeba wykonywać tych konwersji ręcznie. Jeśli na przykład używasz wartości innych niż ciągi, w których ciągi są oczekiwane jako dane wejściowe, Logic Apps automatycznie konwertuje wartości inne niż ciągi na ciągi.

Załóżmy na przykład, że wyzwalacz zwraca wartość liczbową jako dane wyjściowe:

`triggerBody()?['123']`

Jeśli używasz tych liczbowych danych wyjściowych, w których oczekiwane jest wprowadzanie ciągu, takich jak adres URL, Logic Apps automatycznie konwertuje wartość na ciąg przy użyciu notacji nawiasów klamrowych ( `{}` ):

`@{triggerBody()?['123']}`

<a name="base64-encoding-decoding"></a>

### <a name="base64-encoding-and-decoding"></a>Kodowanie i dekodowanie Base64

Logic Apps automatycznie lub niejawnie wykonuje kodowanie lub dekodowanie base64, więc nie trzeba ręcznie wykonywać tych konwersji przy użyciu odpowiednich funkcji:

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> Jeśli ręcznie dodasz dowolną z tych funkcji do przepływu pracy za pomocą Projektanta aplikacji logiki, na przykład za pomocą edytora wyrażeń, odchodzisz od projektanta i wracasz do projektanta, funkcja zniknie z projektanta, pozostawiając tylko wartości parametrów. To zachowanie występuje również w przypadku wybrania wyzwalacza lub akcji, która używa tej funkcji bez edytowania wartości parametrów funkcji. Ten wynik ma wpływ tylko na widoczność funkcji, a nie na jej efekt. W widoku kodu nie ma to wpływu na funkcję. Jeśli jednak edytujesz wartości parametrów funkcji, zarówno funkcja, jak i jej efekt zostaną usunięte z widoku kodu, pozostawiając tylko wartości parametrów funkcji.

<a name="math-functions"></a>

## <a name="math-functions"></a>Funkcje matematyczne

Aby pracować z liczbami całkowitymi i zmiennoprzecinkami, można użyć tych funkcji matematycznych.
Pełne informacje na temat każdej funkcji można znaleźć na [alfabetycznej liście](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Math, funkcja | Zadanie |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Zwróć wynik dodania dwóch liczb. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Zwróć wynik dzielenia dwóch liczb. |
| [Max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Zwraca najwyższą wartość z zestawu liczb lub tablicy. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Zwraca najniższą wartość z zestawu liczb lub tablicy. |
| [Mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Zwróć resztę z dzielenia dwóch liczb. |
| [Mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Zwróć iloczyn z mnożenia dwóch liczb. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Zwraca losową liczbę całkowitą z określonego zakresu. |
| [Zakres](../logic-apps/workflow-definition-language-functions-reference.md#range) | Zwraca tablicę liczb całkowitych, która rozpoczyna się od określonej liczby całkowitej. |
| [Sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Zwróć wynik od odejmowania drugiej liczby od pierwszej liczby. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funkcje daty i godziny

Do pracy z datami i godzinami można użyć tych funkcji daty i godziny.
Pełne informacje na temat każdej funkcji można znaleźć na [alfabetycznej liście](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja daty lub czasu | Zadanie |
| --------------------- | ---- |
| [Adddays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Dodaj liczbę dni do znacznika czasu. |
| [Addhours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Dodaj liczbę godzin do znacznika czasu. |
| [Addminutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Dodaj kilka minut do znacznika czasu. |
| [Addseconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Dodaj liczbę sekund do znacznika czasu. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Dodaj liczbę jednostek czasu do znacznika czasu. Zobacz również [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Przekonwertuj znacznik czasu z uniwersalnej strefy czasowej (UTC) na docelową strefę czasową. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Przekonwertuj znacznik czasu ze źródłowej strefy czasowej na docelową strefę czasową. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Przekonwertuj znacznik czasu ze źródłowej strefy czasowej na uniwersalny współrzędny czas (UTC). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Zwróć dzień składnika miesiąca ze znacznika czasu. |
| [Dayofweek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Zwróć składnik dnia tygodnia ze znacznika czasu. |
| [Dayofyear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Zwraca składnik dnia roku ze znacznika czasu. |
| [Formatdatetime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Zwróć datę ze znacznika czasu. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Zwróć bieżący znacznik czasu plus określone jednostki czasu. Zobacz też [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Zwróć bieżącą sygnaturę czasową pomniejszoną o określone jednostki czasu. Zobacz również [temat subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Zwróć początek dnia dla znacznika czasu. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Zwróć początek godziny dla znacznika czasu. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Zwróć początek miesiąca dla znacznika czasu. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Odejmij liczbę jednostek czasu od znacznika czasu. Zobacz też [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [Kleszcze](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Zwraca wartość `ticks` właściwości dla określonego znacznika czasu. |
| [Utcnow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Zwróć bieżący znacznik czasu jako ciąg. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funkcje przepływu pracy

Te funkcje przepływu pracy mogą ułatwić:

* Uzyskaj szczegółowe informacje o wystąpieniu przepływu pracy w czasie wykonywania.
* Praca z wejściami używanymi do wystąpienia aplikacji logiki lub przepływów.
* Przywołuj dane wyjściowe z wyzwalaczy i akcji.

Na przykład można odwoływać się do danych wyjściowych z jednej akcji i używać tych danych w późniejszej akcji.
Pełne informacje na temat każdej funkcji można znaleźć na [alfabetycznej liście](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja przepływu pracy | Zadanie |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Zwraca dane wyjściowe bieżącej akcji w czasie wykonywania lub wartości z innych par nazwa-wartość JSON. Zobacz też [akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Zwraca dane wyjściowe akcji `body` w czasie wykonywania. Zobacz też [treść](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Zwraca dane wyjściowe akcji w czasie wykonywania. Zobacz [dane wyjściowe](../logic-apps/workflow-definition-language-functions-reference.md#outputs) i [akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [Działania](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Zwraca dane wyjściowe akcji w czasie wykonywania lub wartości z innych par nazwa-wartość JSON. Zobacz też [akcję](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [Ciała](#body) | Zwraca dane wyjściowe akcji `body` w czasie wykonywania. Zobacz też [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Utwórz tablicę z wartościami, które pasują do nazwy klucza w danych *wyjściowych* akcji zakodowanych w formularzu lub danych wyjściowych akcji zakodowanych *w* postaci. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Zwraca pojedynczą wartość, która jest taka sama jak nazwa klucza w danych *formularza* akcji lub zakodowanych w postaci danych *wyjściowych*. |
| [Element](../logic-apps/workflow-definition-language-functions-reference.md#item) | W przypadku powtarzania akcji w tablicy zwróć bieżący element w tablicy podczas bieżącej iteracji akcji. |
| [Elementy](../logic-apps/workflow-definition-language-functions-reference.md#items) | W pętli Foreach lub Until zwróć bieżący element z określonej pętli.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | W pętli Until zwróć wartość indeksu dla bieżącej iteracji. Tej funkcji można używać wewnątrz zagnieżdżonych pętli Until. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Zwróć "adres URL wywołania zwrotnego", który wywołuje wyzwalacz lub akcję. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Zwracanie treści dla określonej części danych wyjściowych akcji, która ma wiele części. |
| [Wyjść](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Zwraca dane wyjściowe akcji w czasie wykonywania. |
| [Parametry](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Zwróć wartość parametru, który jest opisany w definicji przepływu pracy. |
| [Wynik](../logic-apps/workflow-definition-language-functions-reference.md#result) | Zwraca dane wejściowe i wyjściowe z akcji najwyższego poziomu wewnątrz określonej akcji o określonym zakresie, takich jak `For_each` `Until` , i `Scope` . |
| [Wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub z innych par nazwa-wartość JSON. Zobacz też [triggerOutputs i](#triggerOutputs) [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Zwracanie danych wyjściowych `body` wyzwalacza w czasie wykonywania. Zobacz [wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Zwraca pojedynczą wartość pasującą do nazwy klucza w *danych wyjściowych* wyzwalacza zakodowanych w formularzu lub w danych wyjściowych *wyzwalacza.* |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Zwracanie treści dla określonej części wieloczęściowych danych wyjściowych wyzwalacza. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Utwórz tablicę, której wartości pasują do nazwy klucza w danych *wyjściowych* wyzwalacza zakodowanych w formularzu lub danych wyjściowych *wyzwalacza* zakodowanych w formularzu. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Zwracanie danych wyjściowych wyzwalacza w czasie wykonywania lub wartości z innych par nazwa-wartość w pliku JSON. Zobacz [wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [Zmiennych](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Zwraca wartość dla określonej zmiennej. |
| [Przepływu pracy](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Zwraca wszystkie szczegóły dotyczące samego przepływu pracy w czasie wykonywania. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funkcje analizy URI

Aby pracować z identyfikatorami URI i uzyskać różne wartości właściwości dla tych identyfikatorów URI, można użyć tych funkcji analizy identyfikatora URI.
Pełne informacje na temat każdej funkcji można znaleźć na [alfabetycznej liście](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja analizy URI | Zadanie |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Zwraca wartość `host` identyfikatora URI. |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Zwraca wartość `path` identyfikatora URI. |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Zwraca wartości `path` i `query` dla identyfikatora URI. |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Zwraca wartość `port` identyfikatora URI. |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Zwraca wartość `query` identyfikatora URI. |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Zwraca wartość `scheme` identyfikatora URI. |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funkcje manipulowania: JSON & XML

Do pracy z obiektami JSON i węzłami XML można użyć tych funkcji manipulowania.
Pełne informacje na temat każdej funkcji można znaleźć na [alfabetycznej liście](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Manipulowanie funkcją | Zadanie |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Dodaj właściwość i jej wartość lub parę nazwa-wartość do obiektu JSON i zwróć zaktualizowany obiekt. |
| [Łączonej](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Zwróć pierwszą wartość niezerową z co najmniej jednego parametru. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Usuń właściwość z obiektu JSON i zwróć zaktualizowany obiekt. |
| [Setproperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ustaw wartość właściwości obiektu JSON i zwróć zaktualizowany obiekt. |
| [Xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Sprawdź xml dla węzłów lub wartości, które pasują do wyrażenia XPath (XML Path Language) i zwróć pasujące węzły lub wartości. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Wszystkie funkcje — lista alfabetyczna

W tej sekcji wymieniono wszystkie dostępne funkcje w kolejności alfabetycznej.

<a name="action"></a>

### <a name="action"></a>action

Zwraca dane *wyjściowe* bieżącej akcji w czasie wykonywania lub wartości z innych par nazwa-wartość JSON, które można przypisać do wyrażenia.
Domyślnie ta funkcja odwołuje się do całego obiektu akcji, ale opcjonalnie można określić właściwość, której wartość ma zostać dodana.
Zobacz też [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Funkcji można używać `action()` tylko w tych miejscach:

* Właściwość `unsubscribe` akcji dla obiektu webhook, aby można było uzyskać dostęp do wyniku z oryginalnego `subscribe` żądania
* Właściwość `trackedProperties` akcji
* Warunek `do-until` pętli dla akcji

```
action()
action().outputs.body.<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Właściwość*> | Nie | Ciąg | Nazwa właściwości obiektu akcji, której wartość ma być: **name**, **startTime, endTime**, **inputs**, **outputs**, **status**, **code,** **trackingId** i **clientTrackingId.**  W Azure Portal te właściwości można znaleźć, przeglądając szczegóły określonej historii uruchamiania. Aby uzyskać więcej informacji, zobacz [Rest API - Workflow Run Actions (Interfejs API REST — akcje uruchamiania przepływu pracy).](/rest/api/logic/workflowrunactions/get) |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*action-output*> | Ciąg | Dane wyjściowe z bieżącej akcji lub właściwości |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Zwraca dane wyjściowe akcji `body` w czasie wykonywania.
Skrót dla `actions('<actionName>').outputs.body` .
Zobacz [body()](#body) i [actions()](#actions).

```
actionBody('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName (nazwa akcji)*> | Tak | Ciąg | Nazwa dla danych wyjściowych `body` akcji, które chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*action-body-output*> | Ciąg | Dane `body` wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera dane `body` wyjściowe z akcji w serwisie `Get user` Twitter:

```
actionBody('Get_user')
```

I zwraca ten wynik:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Zwraca dane wyjściowe akcji w czasie wykonywania.  i jest skrótem dla `actions('<actionName>').outputs` . Zobacz [actions()](#actions). Funkcja `actionOutputs()` jest rozpoznawana jako w Projektancie aplikacji logiki, więc rozważ użycie `outputs()` funkcji [outputs()](#outputs)zamiast `actionOutputs()` . Mimo że obie funkcje działają w taki sam sposób, `outputs()` preferowane jest rozwiązanie .

```
actionOutputs('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Tak | Ciąg | Nazwa danych wyjściowych akcji, które mają |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*Wyjście*> | Ciąg | Dane wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera dane wyjściowe z akcji `Get user` Twitter:

```
actionOutputs('Get_user')
```

I zwraca ten wynik:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>akcje

Zwraca dane wyjściowe akcji w czasie wykonywania lub wartości z innych par nazwa-wartość JSON, które można przypisać do wyrażenia. Domyślnie funkcja odwołuje się do całego obiektu akcji, ale opcjonalnie można określić właściwość, której wartość ma zostać dodana.
Aby uzyskać skrócone wersje, zobacz [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)i [body()](#body).
Aby uzyskać bieżącą akcję, [zobacz action()](#action).

> [!TIP]
> Funkcja `actions()` zwraca dane wyjściowe w postaci ciągu. Jeśli musisz pracować ze zwracaną wartością jako obiektem JSON, musisz najpierw przekonwertować wartość ciągu. Wartość ciągu można przekształcić w obiekt JSON za pomocą akcji [Prze analizuj JSON.](logic-apps-perform-data-operations.md#parse-json-action)

> [!NOTE]
> Wcześniej można było użyć funkcji lub elementu podczas określania, że akcja została uruchomiono na podstawie danych `actions()` `conditions` wyjściowych innej akcji. Jednak aby zadeklarować jawne zależności między akcjami, należy teraz użyć właściwości akcji `runAfter` zależnej.
> Aby dowiedzieć się więcej na temat właściwości , zobacz Catch and handle failures with the runAfter property (Przechwytuj i `runAfter` [obsługuj błędy za pomocą właściwości runAfter).](../logic-apps/logic-apps-workflow-definition-language.md)

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName (nazwa akcji)*> | Tak | Ciąg | Nazwa obiektu akcji, którego dane wyjściowe mają  |
| <*Właściwość*> | Nie | Ciąg | Nazwa właściwości obiektu akcji, której wartość ma być: **name**, **startTime,** **endTime**, **inputs**, **outputs**, **status**, **code,** **trackingId** i **clientTrackingId.** W Azure Portal te właściwości można znaleźć, przeglądając szczegóły określonej historii uruchamiania. Aby uzyskać więcej informacji, zobacz [Rest API - Workflow Run Actions (Interfejs API REST — akcje uruchamiania przepływu pracy).](/rest/api/logic/workflowrunactions/get) |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*action-output*> | Ciąg | Dane wyjściowe z określonej akcji lub właściwości |
||||

*Przykład*

Ten przykład pobiera wartość `status` właściwości z akcji usługi Twitter w czasie `Get user` wykonywania:

```
actions('Get_user').outputs.body.status
```

I zwraca ten wynik: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Zwróć wynik dodania dwóch liczb.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub mieszana | Liczby do dodania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*suma wyników*> | Liczba całkowita lub liczba zmiennoprzecinkowa | Wynik dodania określonych liczb |
||||

*Przykład*

W tym przykładzie dodano określone liczby:

```
add(1, 1.5)
```

I zwraca ten wynik: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>Adddays

Dodaj liczbę dni do znacznika czasu.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Dni*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba dni do dodania |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Znacznik czasu plus określona liczba dni  |
||||

*Przykład 1*

W tym przykładzie dodano 10 dni do określonego znacznika czasu:

```
addDays('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik: `"2018-03-25T00:00:00.0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć dni od określonego znacznika czasu:

```
addDays('2018-03-15T00:00:00Z', -5)
```

I zwraca ten wynik: `"2018-03-10T00:00:00.0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>Addhours

Dodaj liczbę godzin do znacznika czasu.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Godzin*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba godzin do dodania |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Znacznik czasu plus określona liczba godzin  |
||||

*Przykład 1*

W tym przykładzie dodano 10 godzin do określonego znacznika czasu:

```
addHours('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik: "2018-03-15T10:00:00.0000000Z"

*Przykład 2*

Ten przykład odejmuje pięć godzin od określonego znacznika czasu:

```
addHours('2018-03-15T15:00:00Z', -5)
```

I zwraca ten wynik: `"2018-03-15T10:00:00.0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>Addminutes

Dodaj kilka minut do znacznika czasu.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Minut*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba minut do dodania |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Znacznik czasu plus określona liczba minut |
||||

*Przykład 1*

W tym przykładzie dodano 10 minut do określonego znacznika czasu:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

I zwraca ten wynik: `"2018-03-15T00:20:00.0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć minut od określonego znacznika czasu:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

I zwraca ten wynik: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Dodaj właściwość i jej wartość lub parę nazwa-wartość do obiektu JSON i zwróć zaktualizowany obiekt. Jeśli właściwość już istnieje w czasie wykonywania, funkcja kończy się niepowodzeniem i zgłasza błąd.

```
addProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Tak | Obiekt | Obiekt JSON, do którego chcesz dodać właściwość |
| <*Właściwość*> | Tak | Ciąg | Nazwa właściwości do dodania |
| <*Wartość*> | Tak | Dowolne | Wartość właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Obiekt | Zaktualizowany obiekt JSON z określoną właściwością |
||||

Aby dodać właściwość nadrzędną do istniejącej właściwości, użyj `setProperty()` funkcji , a nie funkcji `addProperty()` . W przeciwnym razie funkcja zwraca tylko obiekt podrzędny jako dane wyjściowe.

```
setProperty(<object>['<parent-property>'], '<parent-property>', addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Tak | Obiekt | Obiekt JSON, do którego chcesz dodać właściwość |
| <*właściwość parent-property*> | Tak | Ciąg | Nazwa właściwości nadrzędnej, w której chcesz dodać właściwość podrzędną |
| <*child-property*> | Tak | Ciąg | Nazwa właściwości podrzędnej do dodania |
| <*Wartość*> | Tak | Dowolne | Wartość do ustawienia dla określonej właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Obiekt | Zaktualizowany obiekt JSON, którego właściwość została ustawiona |
||||

*Przykład 1*

W tym przykładzie dodano właściwość do obiektu JSON, który jest konwertowany z ciągu na `middleName` JSON przy użyciu [funkcji JSON().](#json) Obiekt zawiera już właściwości `firstName` `surName` i . Funkcja przypisuje określoną wartość do nowej właściwości i zwraca zaktualizowany obiekt:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Oto bieżący obiekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Przykład 2*

W tym przykładzie dodano właściwość podrzędną do istniejącej właściwości w obiekcie JSON, który jest konwertowany z ciągu na JSON przy użyciu `middleName` `customerName` funkcji [JSON().](#json) Funkcja przypisuje określoną wartość do nowej właściwości i zwraca zaktualizowany obiekt:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne'))
```

Oto bieżący obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>Addseconds

Dodaj liczbę sekund do znacznika czasu.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Sekund*> | Tak | Liczba całkowita | Dodatnia lub ujemna liczba sekund do dodania |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Znacznik czasu plus określona liczba sekund  |
||||

*Przykład 1*

W tym przykładzie dodano 10 sekund do określonego znacznika czasu:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik: `"2018-03-15T00:00:10.0000000Z"`

*Przykład 2*

Ten przykład odejmuje pięć sekund do określonego znacznika czasu:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

I zwraca ten wynik: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Dodaj liczbę jednostek czasu do znacznika czasu.
Zobacz również [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Interwał*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem:*"Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Znacznik czasu plus określona liczba jednostek czasu  |
||||

*Przykład 1*

W tym przykładzie dodano jeden dzień do określonego znacznika czasu:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik: `"2018-01-02T00:00:00.0000000Z"`

*Przykład 2*

W tym przykładzie dodano jeden dzień do określonego znacznika czasu:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Zwraca wynik przy użyciu opcjonalnego formatu "D": `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>oraz

Sprawdź, czy wszystkie wyrażenia są prawdziwe.
Zwraca wartość true, gdy wszystkie wyrażenia mają wartość true, lub wartość false, gdy co najmniej jedno wyrażenie ma wartość false.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenie1*>, <*wyrażenie2*>, ... | Tak | Wartość logiczna | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy wszystkie wyrażenia mają wartość true. Zwraca wartość false, gdy co najmniej jedno wyrażenie ma wartość false. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy określone wartości logiczne są prawdziwe:

```
and(true, true)
and(false, true)
and(false, false)
```

Zwraca następujące wyniki:

* Pierwszy przykład: oba wyrażenia mają wartość true, więc zwraca wartość `true` .
* Drugi przykład: jedno wyrażenie ma wartość false, więc zwraca wartość `false` .
* Trzeci przykład: Oba wyrażenia mają wartość false, więc zwraca wartość `false` .

*Przykład 2*

Te przykłady sprawdzają, czy określone wyrażenia są prawdziwe:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Zwraca następujące wyniki:

* Pierwszy przykład: oba wyrażenia mają wartość true, więc zwraca wartość `true` .
* Drugi przykład: jedno wyrażenie ma wartość false, więc zwraca wartość `false` .
* Trzeci przykład: Oba wyrażenia mają wartość false, więc zwraca wartość `false` .

<a name="array"></a>

### <a name="array"></a>array

Zwraca tablicę z jednego określonego wejścia.
Aby uzyskać informacje o wielu danych wejściowych, [zobacz createArray()](#createArray).

```
array('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do tworzenia tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<wartość *>]* | Tablica | Tablica zawierająca pojedyncze określone dane wejściowe |
||||

*Przykład*

Ten przykład tworzy tablicę z ciągu "hello":

```
array('hello')
```

I zwraca ten wynik: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Zwróć wersję zakodowaną w formacie base64 dla ciągu.

> [!NOTE]
> Azure Logic Apps automatycznie lub niejawnie wykonuje kodowanie i dekodowanie base64, dzięki czemu nie trzeba ręcznie wykonywać tych konwersji przy użyciu funkcji kodowania i dekodowania. Jeśli jednak mimo to używasz tych funkcji, w projektancie mogą wystąpić nieoczekiwane zachowania renderowania. Te zachowania mają wpływ tylko na widoczność funkcji, a nie na ich wpływ, chyba że edytujesz wartości parametrów funkcji, co powoduje usunięcie funkcji i ich efektów z kodu. Aby uzyskać więcej informacji, zobacz [Base64 encoding and decoding (Kodowanie i dekodowanie Base64).](#base64-encoding-decoding)

```
base64('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg wejściowy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*base64-string*> | Ciąg | Wersja zakodowana w formacie base64 dla ciągu wejściowego |
||||

*Przykład*

W tym przykładzie ciąg "hello" jest konwertowany na ciąg zakodowany w formacie base64:

```
base64('hello')
```

I zwraca ten wynik: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Zwraca wersję binarną dla ciągu zakodowanych w formacie base64.

> [!NOTE]
> Azure Logic Apps lub niejawnie wykonuje kodowanie i dekodowanie base64, dzięki czemu nie trzeba ręcznie wykonywać tych konwersji przy użyciu funkcji kodowania i dekodowania. Jeśli jednak mimo to używasz tych funkcji w projektancie, możesz doświadczyć nieoczekiwanych zachowań renderowania w projektancie. Te zachowania mają wpływ tylko na widoczność funkcji, a nie na ich wpływ, chyba że dokonasz edycji wartości parametrów funkcji, co spowoduje usunięcie funkcji i ich efektów z kodu. Aby uzyskać więcej informacji, zobacz [Base64 encoding and decoding (Kodowanie i dekodowanie Base64).](#base64-encoding-decoding)

```
base64ToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg zakodowany w formacie base64 do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | Ciąg | Wersja binarna dla ciągu zakodowanych w formacie base64 |
||||

*Przykład*

W tym przykładzie ciąg zakodowany w formacie base64 jest konwertowany na ciąg binarny "aGVsbG8=":

```
base64ToBinary('aGVsbG8=')
```

I zwraca ten wynik:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Zwraca wersję ciągu dla ciągu zakodowanego w formacie base64, efektywnie dekodując ciąg base64. Użyj tej funkcji zamiast [decodeBase64(),](#decodeBase64)która jest przestarzała.

> [!NOTE]
> Azure Logic Apps automatycznie lub niejawnie wykonuje kodowanie i dekodowanie base64, dzięki czemu nie trzeba ręcznie wykonywać tych konwersji przy użyciu funkcji kodowania i dekodowania. Jeśli jednak i tak użyjemy tych funkcji w projektancie, w projektancie mogą wystąpić nieoczekiwane zachowania renderowania. Te zachowania mają wpływ tylko na widoczność funkcji, a nie na ich wpływ, chyba że edytujesz wartości parametrów funkcji, co powoduje usunięcie funkcji i ich efektów z kodu. Aby uzyskać więcej informacji, zobacz [Base64 encoding and decoding (Kodowanie i dekodowanie Base64).](#base64-encoding-decoding)

```
base64ToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg zakodowany w formacie base64 do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciąg decoded-base64*> | Ciąg | Wersja ciągu dla ciągu zakodowanego w formacie base64 |
||||

*Przykład*

W tym przykładzie ciąg zakodowany w formacie base64 jest konwertowany na ciąg "aGVsbG8=":

```
base64ToString('aGVsbG8=')
```

I zwraca ten wynik: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binarny

Zwróć wersję binarną dla ciągu.

```
binary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | Ciąg | Wersja binarna dla określonego ciągu |
||||

*Przykład*

W tym przykładzie ciąg "hello" jest konwertowany na ciąg binarny:

```
binary('hello')
```

I zwraca ten wynik:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Zwraca dane wyjściowe akcji `body` w czasie wykonywania. Skrót dla `actions('<actionName>').outputs.body` . Zobacz [actionBody()](#actionBody) i [actions()](#actions).

```
body('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName (nazwa akcji)*> | Tak | Ciąg | Nazwa dla danych wyjściowych `body` akcji, które chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*action-body-output*> | Ciąg | Dane `body` wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera dane `body` wyjściowe z akcji w `Get user` serwisie Twitter:

```
body('Get_user')
```

I zwraca ten wynik:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

Zwraca wersję logiczną wartości.

```
bool(<value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Dowolne | Wartość do przekonwertowania na wartość logiczną. |
|||||

Jeśli używasz z obiektem , wartość obiektu musi być ciągiem lub liczbą całkowitą, która może `bool()` zostać przekonwertowana na wartość logiczną.

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| `true` lub `false` | Wartość logiczna | Wersja logiczna określonej wartości. |
||||

*Dane wyjściowe*

W tych przykładach przedstawiono różne obsługiwane typy danych wejściowych dla `bool()` programu :

| Wartość wejściowa | Typ | Wartość zwracana |
| ----------- | ---------- | ---------------------- |
| `bool(1)` | Liczba całkowita | `true` |
| `bool(0)` | Liczba całkowita    | `false` |
| `bool(-1)` | Liczba całkowita | `true` |
| `bool('true')` | String (ciąg) | `true` |
| `bool('false')` | String (ciąg) | `false` |

<a name="coalesce"></a>

### <a name="coalesce"></a>Łączonej

Zwróć pierwszą wartość o wartości innych niż null z jednego lub większej liczby parametrów.
Puste ciągi, puste tablice i puste obiekty nie mają wartości null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Tak | Dowolny, może mieszać typy | Co najmniej jeden element do sprawdzenia pod |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Dowolne | Pierwszy element lub wartość, która nie ma wartości null. Jeśli wszystkie parametry mają wartość null, ta funkcja zwraca wartość null. |
||||

*Przykład*

Te przykłady zwracają pierwszą wartość niezerową z określonych wartości lub wartość null, gdy wszystkie wartości mają wartość null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

Zwraca następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `"hello"`
* Trzeci przykład: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Połącz co najmniej dwa ciągi i zwróć połączony ciąg.

> [!NOTE]
> Azure Logic Apps automatycznie lub niejawnie wykonuje kodowanie i dekodowanie base64, więc nie trzeba ręcznie wykonywać tych konwersji w przypadku używania funkcji z danymi, które potrzebują kodowania lub `concat()` dekodowania:
> 
> * `concat('data:;base64,',<value>)`
> * `concat('data:,',encodeUriComponent(<value>))`
> 
> Jeśli jednak mimo to użyjemy tej funkcji w projektancie, w projektancie mogą wystąpić nieoczekiwane zachowania renderowania. Te zachowania mają wpływ tylko na widoczność funkcji, a nie na efekt, chyba że edytujesz wartości parametrów funkcji, co powoduje usunięcie funkcji i efektu z kodu. 
> Aby uzyskać więcej informacji, zobacz [Base64 encoding and decoding (Kodowanie i dekodowanie Base64).](#base64-encoding-decoding)

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Tak | Ciąg | Co najmniej dwa ciągi do połączenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Ciąg | Ciąg utworzony na podstawie połączonych ciągów wejściowych |
||||

*Przykład*

Ten przykład łączy ciągi "Hello" i "World":

```
concat('Hello', 'World')
```

I zwraca ten wynik: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>zawiera

Sprawdź, czy kolekcja ma określony element. Zwraca wartość true, gdy element zostanie znaleziony, lub zwraca wartość false, gdy nie zostanie znaleziony. W tej funkcji jest wielkość liter.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

W szczególności ta funkcja działa na następujących typach kolekcji:

* Ciąg *do* wyszukiwania *podciągu*
* *Tablica* do znalezienia *wartości*
* Słownik *do znalezienia* *klucza*

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg, tablica lub słownik | Kolekcja do sprawdzenia |
| <*Wartość*> | Tak | Odpowiednio ciąg, tablica lub słownik | Element do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy element zostanie znaleziony. Zwraca wartość false, jeśli nie znaleziono. |
||||

*Przykład 1*

Ten przykład sprawdza ciąg "hello world" dla podciągu "world" i zwraca wartość true:

```
contains('hello world', 'world')
```

*Przykład 2*

W tym przykładzie ciąg "hello world" jest sprawdzany podciągiem "universe" i zwraca wartość false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Przekonwertuj znacznik czasu z uniwersalnej strefy czasowej (UTC) na docelową strefę czasową.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*destinationTimeZone*> | Tak | Ciąg | Nazwa docelowej strefy czasowej. Aby uzyskać informacje na temat nazw stref czasowych, zobacz Domyślne strefy czasowe systemu [Microsoft Windows,](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)ale może być konieczne usunięcie wszelkich znaków interpunkcji z nazwy strefy czasowej. |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*skonwertowany znacznik czasu*> | Ciąg | Sygnatura czasowa przekonwertowana na docelową strefę czasową |
||||

*Przykład 1*

W tym przykładzie sygnatura czasowa jest konwertowana na określoną strefę czasową:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

I zwraca ten wynik: `"2018-01-01T00:00:00.0000000"`

*Przykład 2*

W tym przykładzie sygnatura czasowa jest konwertowana na określoną strefę czasową i format:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Przekonwertuj znacznik czasu ze źródłowej strefy czasowej na docelową strefę czasową.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Sourcetimezone*> | Tak | Ciąg | Nazwa źródłowej strefy czasowej. Aby uzyskać informacje na temat nazw stref czasowych, zobacz Domyślne strefy czasowe systemu [Microsoft Windows,](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)ale może być konieczne usunięcie wszelkich znaków interpunkcji z nazwy strefy czasowej. |
| <*destinationTimeZone*> | Tak | Ciąg | Nazwa docelowej strefy czasowej. Aby uzyskać informacje o nazwach stref czasowych, zobacz Domyślne strefy czasowe systemu [Microsoft Windows,](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)ale może być konieczne usunięcie wszelkich znaków interpunkcji z nazwy strefy czasowej. |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*skonwertowany znacznik czasu*> | Ciąg | Sygnatura czasowa przekonwertowana na docelową strefę czasową |
||||

*Przykład 1*

W tym przykładzie źródłową strefę czasową konwertuje na docelową strefę czasową:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

I zwraca ten wynik: `"2018-01-01T00:00:00.0000000"`

*Przykład 2*

W tym przykładzie jest konwertowana strefa czasowa na określoną strefę czasową i format:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Przekonwertuj znacznik czasu ze źródłowej strefy czasowej na uniwersalny czas koordynowany (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Sourcetimezone*> | Tak | Ciąg | Nazwa źródłowej strefy czasowej. Aby uzyskać informacje o nazwach stref czasowych, zobacz Domyślne strefy czasowe systemu [Microsoft Windows,](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones)ale może być konieczne usunięcie wszelkich znaków interpunkcji z nazwy strefy czasowej. |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*skonwertowany znacznik czasu*> | Ciąg | Sygnatura czasowa przekonwertowana na czas UTC |
||||

*Przykład 1*

W tym przykładzie sygnatura czasowa jest konwertowana na czas UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

I zwraca ten wynik: `"2018-01-01T08:00:00.0000000Z"`

*Przykład 2*

W tym przykładzie sygnatura czasowa jest konwertowana na czas UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Zwraca tablicę z wielu danych wejściowych.
Aby uzyskać informacje o pojedynczych tablicach wejściowych, [zobacz array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Tak | Dowolne, ale nie mieszane | Co najmniej dwa elementy do utworzenia tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Tablica | Tablica utworzona na podstawie wszystkich elementów wejściowych |
||||

*Przykład*

Ten przykład tworzy tablicę z tych danych wejściowych:

```
createArray('h', 'e', 'l', 'l', 'o')
```

I zwraca ten wynik: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Zwracanie identyfikatora URI (Data Uniform Resource Identifier) dla ciągu.

```
dataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*data-URI*> | Ciąg | Data URI dla ciągu wejściowego |
||||

*Przykład*

W tym przykładzie jest y URI danych dla ciągu "hello":

```
dataUri('hello')
```

I zwraca ten wynik: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Zwraca wersję binarną dla identyfikatora URI danych.
Użyj tej funkcji zamiast [funkcji decodeDataUri()](#decodeDataUri).
Mimo że obie funkcje działają w taki sam sposób, `dataUriBinary()` preferowane jest rozwiązanie .

```
dataUriToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Data URI do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Ciąg | Wersja binarna dla danych URI |
||||

*Przykład*

Ten przykład tworzy wersję binarną dla tego URI danych:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Zwraca wersję ciągu dla identyfikatora URI danych.

```
dataUriToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Data URI do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | Ciąg | Wersja ciągu dla danych URI |
||||

*Przykład*

Ten przykład tworzy ciąg dla tego URI danych:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Zwróć dzień miesiąca ze znacznika czasu.

```
dayOfMonth('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień miesiąca*> | Liczba całkowita | Dzień miesiąca od określonego znacznika czasu |
||||

*Przykład*

Ten przykład zwraca liczbę dla dnia miesiąca z tego znacznika czasu:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

I zwraca ten wynik: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Zwróć dzień tygodnia ze znacznika czasu.

```
dayOfWeek('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień tygodnia*> | Liczba całkowita | Dzień tygodnia od określonego znacznika czasu, gdzie niedziela to 0, poniedziałek to 1 i tak dalej |
||||

*Przykład*

W tym przykładzie zwracana jest liczba dni tygodnia od tego znacznika czasu:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

I zwraca ten wynik: `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Zwróć dzień roku ze znacznika czasu.

```
dayOfYear('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień roku*> | Liczba całkowita | Dzień roku z określonego znacznika czasu |
||||

*Przykład*

Ten przykład zwraca liczbę dni roku z tego znacznika czasu:

```
dayOfYear('2018-03-15T13:27:36Z')
```

I zwraca ten wynik: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>decodeBase64 (przestarzałe)

Ta funkcja jest przestarzała, dlatego zamiast tego użyj [funkcji base64ToString().](#base64ToString)

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Zwraca wersję binarną dla identyfikatora URI danych. Rozważ użycie [funkcji dataUriToBinary()](#dataUriToBinary)zamiast `decodeDataUri()` . Mimo że obie funkcje działają w taki sam sposób, `dataUriToBinary()` preferowana jest funkcja .

> [!NOTE]
> Azure Logic Apps lub niejawnie wykonuje kodowanie i dekodowanie base64, dzięki czemu nie trzeba ręcznie wykonywać tych konwersji przy użyciu funkcji kodowania i dekodowania. Jeśli jednak mimo to używasz tych funkcji w projektancie, możesz doświadczyć nieoczekiwanych zachowań renderowania w projektancie. Te zachowania mają wpływ tylko na widoczność funkcji, a nie na ich wpływ, chyba że dokonasz edycji wartości parametrów funkcji, co spowoduje usunięcie funkcji i ich efektów z kodu. Aby uzyskać więcej informacji, zobacz [Base64 encoding and decoding (Kodowanie i dekodowanie Base64).](#base64-encoding-decoding)

```
decodeDataUri('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg URI danych do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Ciąg | Wersja binarna dla ciągu URI danych |
||||

*Przykład*

W tym przykładzie zwracana jest wersja binarna dla tego URI danych:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Zwraca ciąg, który zastępuje znaki ucieczki zdekodowane wersje.

```
decodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg ze znakami ucieczki do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Ciąg | Zaktualizowany ciąg z zdekodowanych znaków ucieczki |
||||

*Przykład*

W tym przykładzie znaki ucieczki w tym ciągu są zastępowane zdekodowane wersje:

```
decodeUriComponent('https%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Zwróć wynik dzielenia dwóch liczb. Aby uzyskać wynik pozostałej części, zobacz [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Dywidendy*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba do podzielenia przez *dzielnik* |
| <*Dzielnik*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, która dzieli *dzielną*, ale nie może być 0 |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik ilorazu*> | Liczba całkowita lub zmiennoprzecinkowa | Wynik dzielenia pierwszej liczby przez drugą liczbę. Jeśli dzielna lub dzielnik ma typ Float, wynik ma typ Float. <p><p>**Uwaga:** Aby przekonwertować wynik zmiennoprzecinkowy na liczbę całkowitą, spróbuj utworzyć i wywołując [funkcję na platformie Azure](../logic-apps/logic-apps-azure-functions.md) z poziomu aplikacji logiki. |
||||

*Przykład 1*

Oba przykłady zwracają tę wartość z typem Liczba całkowita: `2`

```
div(10,5)
div(11,5)
```

*Przykład 2*

Oba przykłady zwracają tę wartość z typem float: `2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>Encodeuricomponent

Zwracanie zakodowanej wersji identyfikatora URI dla ciągu przez zastąpienie znaków niebezpiecznych dla adresu URL znakami ucieczki. Rozważ użycie [funkcji uriComponent()](#uriComponent)zamiast `encodeUriComponent()` . Mimo że obie funkcje działają w taki sam sposób, `uriComponent()` preferowana jest funkcja .

> [!NOTE]
> Azure Logic Apps lub niejawnie wykonuje kodowanie i dekodowanie base64, dzięki czemu nie trzeba ręcznie wykonywać tych konwersji przy użyciu funkcji kodowania i dekodowania. Jeśli jednak mimo to używasz tych funkcji w projektancie, możesz doświadczyć nieoczekiwanych zachowań renderowania w projektancie. Te zachowania mają wpływ tylko na widoczność funkcji, a nie na ich wpływ, chyba że dokonasz edycji wartości parametrów funkcji, co spowoduje usunięcie funkcji i ich efektów z kodu. Aby uzyskać więcej informacji, zobacz [Base64 encoding and decoding (Kodowanie i dekodowanie Base64).](#base64-encoding-decoding)

```
encodeUriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do przekonwertowania na format zakodowany w formacie URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*kodowany-URI*> | Ciąg | Ciąg zakodowany w formacie URI ze znakami ucieczki |
||||

*Przykład*

Ten przykład tworzy wersję zakodowaną w formacie URI dla tego ciągu:

```
encodeUriComponent('https://contoso.com')
```

I zwraca ten wynik: `"https%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>puste

Sprawdź, czy kolekcja jest pusta.
Zwraca wartość true, gdy kolekcja jest pusta, lub wartość false, gdy nie jest pusta.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg, tablica lub obiekt | Kolekcja do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy kolekcja jest pusta. Zwraca wartość false, gdy pole nie jest puste. |
||||

*Przykład*

Te przykłady sprawdzają, czy określone kolekcje są puste:

```
empty('')
empty('abc')
```

Zwraca następujące wyniki:

* Pierwszy przykład: przekazuje pusty ciąg, więc funkcja zwraca wartość `true` .
* Drugi przykład: przekazuje ciąg "abc", więc funkcja zwraca wartość `false` .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Sprawdź, czy ciąg kończy się określonym podciągiem.
Zwraca wartość true po znalezionym podciągu lub zwraca wartość false, gdy nie zostanie znaleziony.
W tej funkcji nie jest wróżniana wielkość liter.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg do sprawdzenia |
| <*searchText*> | Tak | Ciąg | Końcowy podciąg do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true, gdy zostanie znaleziony końcowy podciąg. Zwraca wartość false, jeśli nie zostanie znaleziona. |
||||

*Przykład 1*

W tym przykładzie sprawdza, czy ciąg "hello world" kończy się ciągiem "world":

```
endsWith('hello world', 'world')
```

I zwraca ten wynik: `true`

*Przykład 2*

W tym przykładzie sprawdza, czy ciąg "hello world" kończy się ciągiem "universe":

```
endsWith('hello world', 'universe')
```

I zwraca ten wynik: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Sprawdź, czy obie wartości, wyrażenia lub obiekty są równoważne.
Zwraca wartość true, gdy oba są równoważne, lub wartość false, gdy nie są równoważne.

```
equals('<object1>', '<object2>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object1,*>, <*object2*> | Tak | Różnych | Wartości, wyrażenia lub obiekty do porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy oba są równoważne. Zwraca wartość false, jeśli nie jest równoważna. |
||||

*Przykład*

Te przykłady sprawdzają, czy określone dane wejściowe są równoważne.

```
equals(true, 1)
equals('abc', 'abcd')
```

Zwraca następujące wyniki:

* Pierwszy przykład: obie wartości są równoważne, więc funkcja zwraca wartość `true` .
* Drugi przykład: Obie wartości nie są równoważne, więc funkcja zwraca wartość `false` .

<a name="first"></a>

### <a name="first"></a>Pierwszym

Zwróć pierwszy element z ciągu lub tablicy.

```
first('<collection>')
first([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg lub tablica | Kolekcja, w której ma być odnajdyny pierwszy element |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Dowolne | Pierwszy element w kolekcji |
||||

*Przykład*

Te przykłady znajdują pierwszy element w tych kolekcjach:

```
first('hello')
first(createArray(0, 1, 2))
```

I zwróć następujące wyniki:

* Pierwszy przykład: `"h"`
* Drugi przykład: `0`

<a name="float"></a>

### <a name="float"></a>float

Przekonwertuj wersję ciągu na liczbę zmiennoprzecinkową na rzeczywistą liczbę zmiennoprzecinkową.
Tej funkcji można używać tylko podczas przekazywania parametrów niestandardowych do aplikacji, na przykład aplikacji logiki lub przepływu.

```
float('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg, który ma prawidłową liczbę zmiennoprzecinkową do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość zmiennoprzecinkowa*> | Float | Liczba zmiennoprzecinkowa dla określonego ciągu |
||||

*Przykład*

Ten przykład tworzy wersję ciągu dla tego numeru zmiennoprzecinkowego:

```
float('10.333')
```

I zwraca ten wynik: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>Formatdatetime

Zwróć znacznik czasu w określonym formacie.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ponownie sformatowany znacznik czasu*> | Ciąg | Zaktualizowany znacznik czasu w określonym formacie |
||||

*Przykład*

W tym przykładzie sygnatura czasowa jest konwertowana na określony format:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

I zwraca ten wynik: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Zwraca tablicę z wartościami, które pasują do nazwy klucza w danych *formularza* akcji lub danych *wyjściowych zakodowanych w postaci.*

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName (nazwa akcji)*> | Tak | Ciąg | Akcja, której dane wyjściowe mają wartość klucza, której potrzebujesz |
| <*Klucz*> | Tak | Ciąg | Nazwa klucza, którego wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Tablica | Tablica ze wszystkimi wartościami, które pasują do określonego klucza |
||||

*Przykład*

W tym przykładzie tworzymy tablicę na podstawie wartości klucza "Podmiot" w danych formularza określonej akcji lub danych wyjściowych zakodowanych w postaci:

```
formDataMultiValues('Send_an_email', 'Subject')
```

Zwraca tekst tematu w tablicy, na przykład: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Zwraca pojedynczą wartość, która pasuje do nazwy klucza w danych *wyjściowych* formularza lub zakodowanych w *formularzu akcji.*
Jeśli funkcja znajdzie więcej niż jedno dopasowanie, zgłasza błąd.

```
formDataValue('<actionName>', '<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName (nazwa akcji)*> | Tak | Ciąg | Akcja, której dane wyjściowe mają wartość klucza, której potrzebujesz |
| <*Klucz*> | Tak | Ciąg | Nazwa klucza, którego wartość ma zostać dodana |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*klucz-wartość*> | Ciąg | Wartość w określonym kluczu  |
||||

*Przykład*

Ten przykład tworzy ciąg na podstawie wartości klucza "Podmiot" w danych formularza określonej akcji lub danych wyjściowych zakodowanych w postaci:

```
formDataValue('Send_an_email', 'Subject')
```

Funkcja zwraca tekst tematu jako ciąg, na przykład: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>Formatnumber

Zwraca liczbę jako ciąg, który jest oparty na określonym formacie.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Numer*> | Tak | Liczba całkowita lub double | Wartość, którą chcesz sformatować. |
| <*Formacie*> | Tak | Ciąg | Ciąg formatu złożonego określający format, którego chcesz użyć. Aby uzyskać informacje na temat obsługiwanych ciągów formatu liczbowego, zobacz [Ciągi standardowego formatu liczbowego,](/dotnet/standard/base-types/standard-numeric-format-strings)które są obsługiwane przez program `number.ToString(<format>, <locale>)` . |
| <*Ustawień regionalnych*> | Nie | Ciąg | Do użycia z programem są obsługiwane przez program `number.ToString(<format>, <locale>)` . Jeśli nie zostanie określony, wartość domyślna to `en-us` . |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sformatowana liczba*> | Ciąg | Określona liczba jako ciąg w określonym formacie. Tę wartość zwracaną można rzutować na `int` element lub `float` . |
||||

*Przykład 1*

Załóżmy, że chcesz sformatować liczbę `1234567890` . Ten przykład formatuje tę liczbę jako ciąg "1 234 567 890,00".

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

*Przykład 2"

Załóżmy, że chcesz sformatować liczbę `1234567890` . Ten przykład formatuje liczbę jako ciąg "1.234.567.890,00".

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*Przykład 3*

Załóżmy, że chcesz sformatować liczbę `17.35` . Ten przykład formatuje liczbę na ciąg "$17,35".

```
formatNumber(17.35, 'C2')
```

*Przykład 4*

Załóżmy, że chcesz sformatować liczbę `17.35` . W tym przykładzie liczba jest formatna jako ciąg "17,35 kr".

```
formatNumber(17.35, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Zwróć bieżący znacznik czasu plus określone jednostki czasu.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Interwał*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem:*"Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Bieżąca sygnatura czasowa plus określona liczba jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżąca sygnatura czasowa to "2018-03-01T00:00:00.0000000Z".
Ten przykład dodaje pięć dni do tego znacznika czasu:

```
getFutureTime(5, 'Day')
```

I zwraca ten wynik: `"2018-03-06T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżąca sygnatura czasowa to "2018-03-01T00:00:00.0000000Z".
W tym przykładzie dodano pięć dni i przekonwertowaliśmy wynik na format "D":

```
getFutureTime(5, 'Day', 'D')
```

I zwraca ten wynik: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Zwróć bieżący znacznik czasu minus określone jednostki czasu.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Interwał*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do odejmowania |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem:*"Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Bieżący znacznik czasu minus określona liczba jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżący znacznik czasu to "2018-02-01T00:00:00.0000000Z".
Ten przykład odejmuje pięć dni od tego znacznika czasu:

```
getPastTime(5, 'Day')
```

I zwraca ten wynik: `"2018-01-27T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżący znacznik czasu to "2018-02-01T00:00:00.0000000Z".
Ten przykład odejmuje pięć dni i konwertuje wynik na format "D":

```
getPastTime(5, 'Day', 'D')
```

I zwraca ten wynik: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Sprawdź, czy pierwsza wartość jest większa niż druga wartość.
Zwraca wartość true, gdy pierwsza wartość jest większa, lub wartość false, gdy wartość jest mniejsza.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy jest większa niż druga wartość |
| <*Compareto*> | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub ciąg | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest większa niż druga wartość. Zwraca wartość false, gdy pierwsza wartość jest równa drugiej wartości lub mniejsza od drugiej. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest większa niż druga wartość:

```
greater(10, 5)
greater('apple', 'banana')
```

I zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Sprawdź, czy pierwsza wartość jest większa niż lub równa drugiej wartości.
Zwraca wartość true, gdy pierwsza wartość jest większa lub równa, lub wartość false, gdy pierwsza wartość jest mniejsza.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy jest większa niż lub równa drugiej wartości |
| <*Compareto*> | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub ciąg | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest większa lub równa drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest mniejsza niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest większa lub równa drugiej wartości:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

Zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Wygeneruj unikatowy identyfikator globalny (GUID) jako ciąg, na przykład "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Ponadto można określić inny format identyfikatora GUID niż domyślny format "D", czyli 32 cyfry rozdzielone łącznikami.

```
guid('<format>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu dla](/dotnet/api/system.guid.tostring#system_guid_tostring_system_string_) zwróconego identyfikatora GUID. Domyślnie format to "D", ale można użyć wartości "N", "D", "B", "P" lub "X". |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wartość identyfikatora GUID*> | Ciąg | Losowo wygenerowany identyfikator GUID |
||||

*Przykład*

W tym przykładzie jest generowany ten sam identyfikator GUID, ale jako 32 cyfry, rozdzielone łącznikami i ujęte w nawiasy:

```
guid('P')
```

I zwraca ten wynik: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Sprawdź, czy wyrażenie ma wartość true czy false. Na podstawie wyniku zwróć określoną wartość. Parametry są oceniane od lewej do prawej.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wyrażenie*> | Tak | Wartość logiczna | Wyrażenie do sprawdzenia |
| <*valueIfTrue*> | Tak | Dowolne | Wartość, która ma być zwracana, gdy wyrażenie ma wartość true |
| <*valueIfFalse*> | Tak | Dowolne | Wartość, która ma być zwracana, gdy wyrażenie ma wartość false |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*określona wartość zwracana*> | Dowolne | Określona wartość zwracana na podstawie tego, czy wyrażenie ma wartość true, czy false |
||||

*Przykład*

Ten przykład zwraca wartość `"yes"` , ponieważ określone wyrażenie zwraca wartość true.
W przeciwnym razie przykład zwraca `"no"` wartość :

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>Indexof

Zwróć pozycję początkową lub wartość indeksu dla podciągu.
W tej funkcji nie jest wielkość liter, a indeksy zaczynają się od liczby 0.

```
indexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg, który ma podciąg do znalezienia |
| <*tekst wyszukiwania*> | Tak | Ciąg | Podciąg do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*index-value*>| Liczba całkowita | Pozycja początkowa lub wartość indeksu dla określonego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, zwróć liczbę -1. |
||||

*Przykład*

Ten przykład umożliwia znalezienie wartości indeksu początkowego dla podciągu "world" w ciągu "hello world":

```
indexOf('hello world', 'world')
```

I zwraca ten wynik: `6`

<a name="int"></a>

### <a name="int"></a>int

Zwraca wersję liczb całkowitych dla ciągu.

```
int('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*integer-result*> | Liczba całkowita | Wersja liczb całkowitych dla określonego ciągu |
||||

*Przykład*

W tym przykładzie podano wersję całkowitą dla ciągu "10":

```
int('10')
```

I zwraca ten wynik: `10`

<a name="item"></a>

### <a name="item"></a>element

Gdy element jest używany wewnątrz powtarzających się akcji na tablicy, zwraca bieżący element w tablicy podczas bieżącej iteracji akcji.
Wartości można również pobrać z właściwości tego elementu.

```
item()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Dowolne | Bieżący element w tablicy dla bieżącej iteracji akcji |
||||

*Przykład*

Ten przykład pobiera element z bieżącego komunikatu dla akcji "Send_an_email" wewnątrz bieżącej iteracji pętli `body` for-each:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>Elementy

Zwraca bieżący element z każdego cyklu w pętli for-each.
Użyj tej funkcji w pętli for-each.

```
items('<loopName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Tak | Ciąg | Nazwa pętli for-each |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Element*> | Dowolne | Element z bieżącego cyklu w określonej pętli for-each |
||||

*Przykład*

Ten przykład pobiera bieżący element z określonej pętli for-each:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Zwróć wartość indeksu dla bieżącej iteracji wewnątrz pętli Until. Tej funkcji można używać wewnątrz zagnieżdżonych pętli Until. 

```
iterationIndexes('<loopName>')
```

| Parametr | Wymagane | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*loopName (nazwa pętli)*> | Tak | Ciąg | Nazwa pętli Until | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Indeks*> | Liczba całkowita | Wartość indeksu dla bieżącej iteracji wewnątrz określonej pętli Until | 
|||| 

*Przykład* 

Ten przykład tworzy zmienną licznika i zwiększa tę zmienną o jeden podczas każdej iteracji w pętli Until, aż wartość licznika osiągnie pięć. Przykład tworzy również zmienną, która śledzi bieżący indeks dla każdej iteracji. W pętli Until podczas każdej iteracji przykład zwiększa licznik, a następnie przypisuje wartość licznika do bieżącej wartości indeksu, a następnie zwiększa licznik. W pętli ten przykład odwołuje się do bieżącego indeksu iteracji przy użyciu `iterationIndexes` funkcji :

`iterationIndexes('Until_Max_Increment')`

```json
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until_Max_Increment": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
            },           
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5)",
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Zwraca wartość JavaScript Object Notation (JSON), obiekt lub tablicę obiektów dla ciągu lub kodu XML.

```
json('<value>')
json(xml('value'))
```

> [!IMPORTANT]
> Bez schematu XML definiującego strukturę danych wyjściowych funkcja może zwracać wyniki, w których struktura znacznie różni się od oczekiwanego formatu, w zależności od danych wejściowych.
>  
> To zachowanie sprawia, że ta funkcja jest nieodpowiednia w scenariuszach, w których dane wyjściowe muszą być zgodne z dobrze zdefiniowanym kontraktem, na przykład w krytycznych systemach biznesowych lub rozwiązaniach.

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg lub XML | Ciąg lub xml do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wynik JSON*> | Natywny typ, obiekt lub tablica JSON | Wartość typu natywnego JSON, obiekt lub tablica obiektów z ciągu wejściowego lub kodu XML. <p><p>- Jeśli przekażemy kod XML, który ma pojedynczy element podrzędny w elemencie głównym, funkcja zwraca pojedynczy obiekt JSON dla tego elementu podrzędnego. <p> - Jeśli przekażemy kod XML zawierający wiele elementów podrzędnych w elemencie głównym, funkcja zwraca tablicę zawierającą obiekty JSON dla tych elementów podrzędnych. <p>- Jeśli ciąg ma wartość null, funkcja zwraca pusty obiekt. |
||||

*Przykład 1*

W tym przykładzie ten ciąg jest konwertowany na wartość JSON:

```
json('[1, 2, 3]')
```

I zwraca ten wynik: `[1, 2, 3]`

*Przykład 2*

W tym przykładzie ten ciąg jest konwertowany na JSON:

```
json('{"fullName": "Sophia Owen"}')
```

I zwraca ten wynik:

```json
{
  "fullName": "Sophia Owen"
}
```

*Przykład 3*

W tym przykładzie użyto funkcji i w celu przekonwertowania kodu XML, który ma pojedynczy element podrzędny w elemencie głównym, na obiekt JSON o nazwie `json()` `xml()` dla tego elementu `person` podrzędnego:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))`

I zwraca ten wynik:

```json
{
   "?xml": { 
      "@version": "1.0" 
   },
   "root": {
      "person": {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      }
   }
}
```

*Przykład 4*

W tym przykładzie użyto funkcji i w celu przekonwertowania kodu XML zawierającego wiele elementów podrzędnych w elemencie głównym na tablicę o nazwie , która zawiera obiekty `json()` `xml()` JSON dla tych elementów `person` podrzędnych:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> <person id='2'> <name>John Doe</name> <occupation>Engineer</occupation> </person> </root>'))`

I zwraca ten wynik:

```json
{
   "?xml": {
      "@version": "1.0"
   },
   "root": {
      "person": [
         {
            "@id": "1",
            "name": "Sophia Owen",
            "occupation": "Engineer"
         },
         {
            "@id": "2",
            "name": "John Doe",
            "occupation": "Engineer"
         }
      ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>Przecięcia

Zwraca kolekcję, która *zawiera tylko* wspólne elementy w określonych kolekcjach.
Aby element pojawił się w wyniku, musi znajdować się we wszystkich kolekcjach przekazanych do tej funkcji.
Jeśli co najmniej jeden element ma taką samą nazwę, ostatni element o tej nazwie pojawi się w wyniku.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Tak | Tablica lub obiekt, ale nie oba | Kolekcje, z których mają *być tylko* wspólne elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*common-items*> | Odpowiednio tablica lub obiekt | Kolekcja, która zawiera tylko wspólne elementy w określonych kolekcjach |
||||

*Przykład*

Ten przykład umożliwia znalezienie wspólnych elementów w tych tablicach:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

Zwraca tablicę z *tylko tymi* elementami: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Zwraca ciąg, który zawiera wszystkie elementy z tablicy i ma każdy znak oddzielony *ogranicznikiem*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Tablica | Tablica, która ma elementy do sprzężenia |
| <*Ogranicznik*> | Tak | Ciąg | Separator, który pojawia się między poszczególnymi znakami w wynikowym ciągu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*char1* >< *ogranicznik* >< *char2* >< *ogranicznik*>... | Ciąg | Wynikowy ciąg utworzony na podstawie wszystkich elementów w określonej tablicy |
||||

*Przykład*

Ten przykład tworzy ciąg ze wszystkich elementów w tej tablicy z określonym znakiem jako ogranicznikiem:

```
join(createArray('a', 'b', 'c'), '.')
```

I zwraca ten wynik: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>ostatni

Zwraca ostatni element z kolekcji.

```
last('<collection>')
last([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg lub tablica | Kolekcja, w której ma być odnajdyny ostatni element |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Odpowiednio ciąg lub tablica | Ostatni element w kolekcji |
||||

*Przykład*

W tych przykładach znajduje się ostatni element w tych kolekcjach:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

Zwraca następujące wyniki:

* Pierwszy przykład: `"d"`
* Drugi przykład: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>Lastindexof

Zwróć pozycję początkową lub wartość indeksu dla ostatniego wystąpienia podciągu. W tej funkcji nie jest wielkość liter, a indeksy zaczynają się od liczby 0.

```json
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg, który ma podciąg do znalezienia |
| <*searchText*> | Tak | Ciąg | Podciąg do znalezienia |
|||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*end-index-value*> | Liczba całkowita | Pozycja początkowa lub wartość indeksu dla ostatniego wystąpienia określonego podciągu. |
|||

Jeśli wartość ciągu lub podciągu jest pusta, występuje następujące zachowanie:

* Jeśli tylko wartość ciągu jest pusta, funkcja zwraca wartość `-1` .

* Jeśli wartości ciągu i podciągu są puste, funkcja zwraca wartość `0` .

* Jeśli tylko wartość podciągu jest pusta, funkcja zwraca długość ciągu minus 1.

*Przykłady*

W tym przykładzie znajduje wartość indeksu początkowego dla ostatniego wystąpienia podciągu `world` podciągu w ciągu `hello world hello world` . Zwrócony wynik to `18` :

```json
lastIndexOf('hello world hello world', 'world')
```

W tym przykładzie brakuje parametru podciągu i zwraca wartość , ponieważ wartość ciągu wejściowego `22` ( `23` ) minus 1 jest większa niż 0.

```json
lastIndexOf('hello world hello world', '')
```

<a name="length"></a>

### <a name="length"></a>length

Zwraca liczbę elementów w kolekcji.

```
length('<collection>')
length([<collection>])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg lub tablica | Kolekcja z elementami do zliczenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Liczba całkowita | Liczba elementów w kolekcji |
||||

*Przykład*

Te przykłady zliczają liczbę elementów w tych kolekcjach:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

I zwróć ten wynik: `4`

<a name="less"></a>

### <a name="less"></a>less

Sprawdź, czy pierwsza wartość jest mniejsza niż druga wartość.
Zwraca wartość true, gdy pierwsza wartość jest mniejsza, lub wartość false, gdy pierwsza wartość jest większa.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy jest mniejsza niż druga wartość |
| <*Compareto*> | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub ciąg | Element porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest mniejsza niż druga wartość. Zwraca wartość false, gdy pierwsza wartość jest równa drugiej wartości lub większa od drugiej. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest mniejsza niż druga wartość.

```
less(5, 10)
less('banana', 'apple')
```

I zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Sprawdź, czy pierwsza wartość jest mniejsza niż lub równa drugiej wartości.
Zwraca wartość true, gdy pierwsza wartość jest mniejsza lub równa, lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub ciąg | Pierwsza wartość do sprawdzenia, czy jest mniejsza niż lub równa drugiej wartości |
| <*Compareto*> | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub ciąg | Element porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest mniejsza lub równa drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest większa niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzają, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

Zwróć następujące wyniki:

* Pierwszy przykład: `true`
* Drugi przykład: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Zwróć "adres URL wywołania zwrotnego", który wywołuje wyzwalacz lub akcję.
Ta funkcja działa tylko z wyzwalaczami i akcjami dla typów łączników **HttpWebhook** i **ApiConnectionWebhook,** ale nie z typami **Ręczny,** **Cykl,** **HTTP** i **APIConnection.**

```
listCallbackUrl()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*callback-URL*> | Ciąg | Adres URL wywołania zwrotnego dla wyzwalacza lub akcji |
||||

*Przykład*

W tym przykładzie pokazano przykładowy adres URL wywołania zwrotnego, który może zwrócić ta funkcja:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Zwraca najwyższą wartość z listy lub tablicy z liczbami, które są włącznie po obu stronach.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub obie | Zestaw liczb, z których chcesz uzyskać najwyższą wartość |
| [<*number1*>, <*number2*>, ...] | Tak | Tablica — liczba całkowita, liczba zmiennoprzecinkowa lub obie | Tablica liczb, z których chcesz uzyskać najwyższą wartość |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*max-value*> | Liczba całkowita lub liczba zmiennoprzecinkowa | Najwyższa wartość w określonej tablicy lub zestawie liczb |
||||

*Przykład*

Te przykłady uzyskają najwyższą wartość z zestawu liczb i tablicy:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

I zwróć ten wynik: `3`

<a name="min"></a>

### <a name="min"></a>min

Zwraca najniższą wartość z zestawu liczb lub tablicy.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Tak | Liczba całkowita, liczba zmiennoprzecinkowa lub obie | Zestaw liczb, z których chcesz uzyskać najniższą wartość |
| [<*number1*>, <*number2*>, ...] | Tak | Tablica — liczba całkowita, liczba zmiennoprzecinkowa lub obie | Tablica liczb, z której chcesz uzyskać najniższą wartość |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość minimalna*> | Liczba całkowita lub zmiennoprzecinkowa | Najniższa wartość w określonym zestawie liczb lub określonej tablicy |
||||

*Przykład*

Te przykłady uzyskają najniższą wartość w zestawie liczb i tablicy:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

I zwróć ten wynik: `1`

<a name="mod"></a>

### <a name="mod"></a>Mod

Zwróć resztę z dzielenia dwóch liczb.
Aby uzyskać wynik liczby całkowitej, zobacz [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Dywidendy*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba do podzielenia przez *dzielnik* |
| <*Dzielnik*> | Tak | Liczba całkowita lub zmiennoprzecinkowa | Liczba, która dzieli *dzielną*, ale nie może być 0. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Liczba całkowita lub zmiennoprzecinkowa | Pozostała część z dzielenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

W tym przykładzie pierwsza liczba jest dzielna przez drugą liczbę:

```
mod(3, 2)
```

I zwróć ten wynik: `1`

<a name="mul"></a>

### <a name="mul"></a>Mul

Zwróć iloczyn z mnożenia dwóch liczb.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Tak | Liczba całkowita lub liczba zmiennoprzecinkowa | Liczba do pomnożenia przez *multiplicand2* |
| <*multiplicand2*> | Tak | Liczba całkowita lub liczba zmiennoprzecinkowa | Liczba, która mnoży *mnożenie1* |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*product-result*> | Liczba całkowita lub liczba zmiennoprzecinkowa | Iloczyn z mnożenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

W tych przykładach pierwsza liczba jest wielokrotna przez drugą liczbę:

```
mul(1, 2)
mul(1.5, 2)
```

I zwróć następujące wyniki:

* Pierwszy przykład: `2`
* Drugi przykład `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Zwracanie treści dla określonej części danych wyjściowych akcji, która ma wiele części.

```
multipartBody('<actionName>', <index>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName (nazwa akcji)*> | Tak | Ciąg | Nazwa akcji, która ma dane wyjściowe z wieloma częściami |
| <*Indeks*> | Tak | Liczba całkowita | Wartość indeksu dla chcieć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Ciała*> | Ciąg | Treść określonej części |
||||

<a name="not"></a>

### <a name="not"></a>not

Sprawdź, czy wyrażenie ma wartość false.
Zwraca wartość true, gdy wyrażenie ma wartość false, lub zwraca wartość false, gdy wartość true.

```json
not(<expression>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wyrażenie*> | Tak | Wartość logiczna | Wyrażenie do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy wyrażenie ma wartość false. Zwraca wartość false, gdy wyrażenie ma wartość true. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy określone wyrażenia są fałszywe:

```json
not(false)
not(true)
```

Zwróć następujące wyniki:

* Pierwszy przykład: wyrażenie ma wartość false, więc funkcja zwraca wartość `true` .
* Drugi przykład: Wyrażenie ma wartość true, więc funkcja zwraca wartość `false` .

*Przykład 2*

Te przykłady sprawdzają, czy określone wyrażenia są fałszywe:

```json
not(equals(1, 2))
not(equals(1, 1))
```

Zwróć następujące wyniki:

* Pierwszy przykład: wyrażenie ma wartość false, więc funkcja zwraca wartość `true` .
* Drugi przykład: Wyrażenie ma wartość true, więc funkcja zwraca wartość `false` .

<a name="or"></a>

### <a name="or"></a>lub

Sprawdź, czy co najmniej jedno wyrażenie ma wartość true.
Zwraca wartość true, gdy co najmniej jedno wyrażenie ma wartość true, lub zwraca wartość false, gdy wszystkie wyrażenia mają wartość false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wyrażenie1*>, <*wyrażenie2*>, ... | Tak | Wartość logiczna | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, jeśli co najmniej jedno wyrażenie ma wartość true. Zwraca wartość false, gdy wszystkie wyrażenia mają wartość false. |
||||

*Przykład 1*

Te przykłady sprawdzają, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(true, false)
or(false, false)
```

I zwróć następujące wyniki:

* Pierwszy przykład: co najmniej jedno wyrażenie ma wartość true, więc funkcja zwraca wartość `true` .
* Drugi przykład: oba wyrażenia mają wartość false, więc funkcja zwraca wartość `false` .

*Przykład 2*

Te przykłady sprawdzają, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

I zwróć następujące wyniki:

* Pierwszy przykład: co najmniej jedno wyrażenie ma wartość true, więc funkcja zwraca wartość `true` .
* Drugi przykład: oba wyrażenia mają wartość false, więc funkcja zwraca wartość `false` .

<a name="outputs"></a>

### <a name="outputs"></a>Wyjść

Zwraca dane wyjściowe akcji w czasie wykonywania. Użyj tej funkcji, a nie `actionOutputs()` funkcji , która jest rozpoznawana jako w `outputs()` Projektancie aplikacji logiki. Mimo że obie funkcje działają w taki sam sposób, `outputs()` preferowane jest rozwiązanie .

```
outputs('<actionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*actionName (nazwa akcji)*> | Tak | Ciąg | Nazwa dla danych wyjściowych akcji, które chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*Wyjście*> | Ciąg | Dane wyjściowe z określonej akcji |
||||

*Przykład*

Ten przykład pobiera dane wyjściowe z akcji w serwisie `Get user` Twitter:

```
outputs('Get_user')
```

I zwraca ten wynik:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Zwróć wartość parametru, który jest opisany w definicji przepływu pracy.

```
parameters('<parameterName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Parametername*> | Tak | Ciąg | Nazwa parametru, którego wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*parametr-wartość*> | Dowolne | Wartość określonego parametru |
||||

*Przykład*

Załóżmy, że masz tę wartość JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Ten przykład pobiera wartość dla określonego parametru:

```
parameters('fullName')
```

I zwraca ten wynik: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Zwraca losową liczbę całkowitą z określonego zakresu, który jest inkluzywny tylko na początku.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Minvalue*> | Tak | Liczba całkowita | Najniższa liczba całkowita w zakresie |
| <*Maxvalue*> | Tak | Liczba całkowita | Liczba całkowita, która następuje po najwyższej wartości całkowitej z zakresu, który może zwrócić funkcja |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*random-result*> | Liczba całkowita | Losowa liczba całkowita zwrócona z określonego zakresu |
||||

*Przykład*

Ten przykład pobiera losową liczbę całkowitą z określonego zakresu, z wyłączeniem wartości maksymalnej:

```
rand(1, 5)
```

Funkcja zwraca jedną z tych liczb w wyniku: `1` `2` , , , `3` lub `4`

<a name="range"></a>

### <a name="range"></a>range

Zwraca tablicę liczb całkowitych, która rozpoczyna się od określonej liczby całkowitej.

```
range(<startIndex>, <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Startindex*> | Tak | Liczba całkowita | Wartość całkowita, która rozpoczyna tablicę jako pierwszy element |
| <*Liczba*> | Tak | Liczba całkowita | Liczba liczb całkowitych w tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*zakres-wynik>]* | Tablica | Tablica z liczbami całkowitymi rozpoczynająca się od określonego indeksu |
||||

*Przykład*

Ten przykład tworzy tablicę liczb całkowitych, która rozpoczyna się od określonego indeksu i ma określoną liczbę liczb całkowitych:

```
range(1, 4)
```

I zwraca ten wynik: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Zastąp

Zastąp podciąg określonym ciągiem i zwróć ciąg wynikowy. W tej funkcji jest wielkość liter.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg, który ma podciąg do zastąpienia |
| <*staryTekst*> | Tak | Ciąg | Podciąg do zastąpienia |
| <*newText*> | Tak | Ciąg | Ciąg zastępczy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany tekst*> | Ciąg | Zaktualizowany ciąg po zastąpieniu podciągu <p>Jeśli podciąg nie zostanie znaleziony, zwróć oryginalny ciąg. |
||||

*Przykład*

W tym przykładzie znajduje podciąg "old" w ciągu "the old string" (stary ciąg) i zastępuje ciąg "old" ciągiem "new":

```
replace('the old string', 'old', 'new')
```

I zwraca ten wynik: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Usuń właściwość z obiektu i zwróć zaktualizowany obiekt. Jeśli właściwość, która próbujesz usunąć, nie istnieje, funkcja zwraca oryginalny obiekt.

```
removeProperty(<object>, '<property>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Tak | Obiekt | Obiekt JSON, z którego chcesz usunąć właściwość |
| <*Właściwość*> | Tak | Ciąg | Nazwa właściwości do usunięcia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Obiekt | Zaktualizowany obiekt JSON bez określonej właściwości |
||||

Aby usunąć właściwość podrzędną z istniejącej właściwości, użyj tej składni:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Tak | Obiekt | Obiekt JSON, którego właściwość chcesz usunąć |
| <*właściwość parent-property*> | Tak | Ciąg | Nazwa właściwości nadrzędnej z właściwością podrzędną, którą chcesz usunąć |
| <*child-property*> | Tak | Ciąg | Nazwa właściwości podrzędnej do usunięcia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Obiekt | Zaktualizowany obiekt JSON, którego właściwość podrzędna została usunięta |
||||

*Przykład 1*

W tym przykładzie właściwość jest usuwana z obiektu JSON, który jest konwertowany z ciągu na JSON przy użyciu funkcji `middleName` [JSON()](#json) i zwraca zaktualizowany obiekt:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Oto bieżący obiekt JSON:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Przykład 2*

W tym przykładzie właściwość podrzędna jest usuwana z właściwości nadrzędnej w obiekcie JSON, który jest konwertowany z ciągu na JSON przy użyciu funkcji `middleName` `customerName` [JSON()](#json) i zwraca zaktualizowany obiekt:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Oto bieżący obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

Zwraca wyniki z akcji najwyższego poziomu w określonej akcji o określonym zakresie, takiej jak `For_each` `Until` akcja , lub `Scope` . Funkcja akceptuje pojedynczy parametr, który jest nazwą zakresu, i zwraca tablicę zawierającą informacje z akcji pierwszego poziomu `result()` w tym zakresie. Te obiekty akcji zawierają te same atrybuty, które są zwracane przez funkcję, takie jak czas rozpoczęcia akcji, czas zakończenia, stan, dane wejściowe, identyfikatory korelacji i `actions()` dane wyjściowe.

> [!NOTE]
> Ta funkcja zwraca informacje *tylko* z akcji pierwszego poziomu w akcji w zakresie, a nie z bardziej zagnieżdżonych akcji, takich jak akcje przełącznika lub warunku.

Na przykład można użyć tej funkcji, aby uzyskać wyniki z akcji, które zakończyły się niepowodzeniem, dzięki czemu można diagnozować i obsługiwać wyjątki. Aby uzyskać więcej informacji, [zobacz Get context and results for failures (Uzyskiwanie kontekstu i wyników dla niepowodzeń).](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures)

```
result('<scopedActionName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Tak | Ciąg | Nazwa akcji o zakresie, w której chcesz wprowadzić dane wejściowe i wyjściowe z akcji najwyższego poziomu w tym zakresie |
||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*array-object*> | Obiekt tablicy | Tablica zawierająca tablice danych wejściowych i wyjściowych z każdej akcji najwyższego poziomu w określonym zakresie |
||||

*Przykład*

W tym przykładzie zwracane są dane wejściowe i wyjściowe z każdej iteracji akcji HTTP wewnątrz pętli, używając `For_each` `result()` funkcji w akcji `Compose` :

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Oto jak przykład zwracana tablica może wyglądać, gdy obiekt zewnętrzny zawiera dane wejściowe i wyjściowe z każdej iteracji `outputs` akcji wewnątrz `For_each` akcji.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>Setproperty

Ustaw wartość właściwości obiektu JSON i zwróć zaktualizowany obiekt. Jeśli właściwość, która próbujesz ustawić, nie istnieje, zostanie dodana do obiektu . Aby dodać nową właściwość, użyj [funkcji addProperty().](#addProperty)

```
setProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Tak | Obiekt | Obiekt JSON, którego właściwość chcesz ustawić |
| <*Właściwość*> | Tak | Ciąg | Nazwa istniejącej lub nowej właściwości do ustawienia |
| <*Wartość*> | Tak | Dowolne | Wartość do ustawienia dla określonej właściwości |
|||||

Aby ustawić właściwość podrzędną w obiekcie podrzędnym, zamiast tego użyj wywołania `setProperty()` zagnieżdżone. W przeciwnym razie funkcja zwraca tylko obiekt podrzędny jako dane wyjściowe.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Tak | Obiekt | Obiekt JSON, którego właściwość chcesz ustawić |
| <*właściwość parent-property*> | Tak | Ciąg | Nazwa właściwości nadrzędnej z właściwością podrzędną, którą chcesz ustawić |
| <*child-property*> | Tak | Ciąg | Nazwa właściwości podrzędnej do ustawienia |
| <*Wartość*> | Tak | Dowolne | Wartość do ustawienia dla określonej właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updated-object*> | Obiekt | Zaktualizowany obiekt JSON, którego właściwość została ustawiona |
||||

*Przykład 1*

W tym przykładzie ustawiana jest właściwość w obiekcie JSON, który jest konwertowany z ciągu na `surName` JSON przy użyciu [funkcji JSON().](#json) Funkcja przypisuje określoną wartość do właściwości i zwraca zaktualizowany obiekt:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Oto bieżący obiekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Przykład 2*

W tym przykładzie ustawiana jest właściwość podrzędna właściwości nadrzędnej w obiekcie JSON, który jest konwertowany z ciągu na JSON przy użyciu `surName` `customerName` funkcji [JSON().](#json) Funkcja przypisuje określoną wartość do właściwości i zwraca zaktualizowany obiekt:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Oto bieżący obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Oto zaktualizowany obiekt JSON:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>Pomiń

Usuń elementy z przodu kolekcji i zwróć *wszystkie inne* elementy.

```
skip([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Tablica | Kolekcja, której elementy chcesz usunąć |
| <*Liczba*> | Tak | Liczba całkowita | Dodatnia liczba całkowita dla liczby elementów do usunięcia z przodu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*updated-collection>]* | Tablica | Zaktualizowana kolekcja po usunięciu określonych elementów |
||||

*Przykład*

W tym przykładzie usuwany jest jeden element , numer 0, z przodu określonej tablicy:

```
skip(createArray(0, 1, 2, 3), 1)
```

Zwraca tę tablicę z pozostałymi elementami: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Zwraca tablicę zawierającą podciągi rozdzielone przecinkami na podstawie określonego znaku ogranicznika w oryginalnym ciągu.

```
split('<text>', '<delimiter>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg do rozdzielenia na podciągi na podstawie określonego ogranicznika w oryginalnym ciągu |
| <*Ogranicznik*> | Tak | Ciąg | Znak w oryginalnym ciągu do użycia jako ogranicznik |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*podciąg1*>,<*podciąg2*>,...] | Tablica | Tablica zawierająca podciągi z oryginalnego ciągu oddzielone przecinkami |
||||

*Przykład*

Ten przykład tworzy tablicę z podciągami z określonego ciągu na podstawie określonego znaku jako ogranicznika:

```
split('a_b_c', '_')
```

Funkcja i zwraca tę tablicę jako wynik: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Zwróć początek dnia dla znacznika czasu.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Określony znacznik czasu, ale rozpoczynający się od znacznika godziny zerowej dla dnia |
||||

*Przykład*

W tym przykładzie znajduje się początek dnia dla tego znacznika czasu:

```
startOfDay('2018-03-15T13:30:30Z')
```

I zwraca ten wynik: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Zwróć początek godziny dla znacznika czasu.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Określona sygnatura czasowa, ale rozpoczynająca się od znaku zerominutowego dla godziny |
||||

*Przykład*

W tym przykładzie znajduje się początek godziny dla tego znacznika czasu:

```
startOfHour('2018-03-15T13:30:30Z')
```

I zwraca ten wynik: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Zwróć początek miesiąca dla znacznika czasu.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Określona sygnatura czasowa, ale zaczynająca się pierwszego dnia miesiąca od znacznika zerogodzinnego |
||||

*Przykład 1*

Ten przykład zwraca początek miesiąca dla tego znacznika czasu:

```
startOfMonth('2018-03-15T13:30:30Z')
```

I zwraca ten wynik: `"2018-03-01T00:00:00.0000000Z"`

*Przykład 2*

Ten przykład zwraca początek miesiąca w określonym formacie dla tego znacznika czasu:

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

I zwraca ten wynik: `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Sprawdź, czy ciąg zaczyna się od określonego podciągu.
Zwraca wartość true po znalezionym podciągu lub zwraca wartość false, gdy nie zostanie znaleziony.
W tej funkcji nie jest wróżniana wielkość liter.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg do sprawdzenia |
| <*searchText*> | Tak | Ciąg | Ciąg początkowy do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true po znalezionym początkowym podciągu. Zwraca wartość false, jeśli nie zostanie znaleziona. |
||||

*Przykład 1*

W tym przykładzie sprawdza, czy ciąg "hello world" zaczyna się od podciągu "hello":

```
startsWith('hello world', 'hello')
```

I zwraca ten wynik: `true`

*Przykład 2*

W tym przykładzie sprawdza, czy ciąg "hello world" zaczyna się od podciągu "greetings":

```
startsWith('hello world', 'greetings')
```

I zwraca ten wynik: `false`

<a name="string"></a>

### <a name="string"></a>ciąg

Zwróć wersję ciągu dla wartości.

```
string(<value>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Dowolne | Wartość do konwersji. Jeśli ta wartość ma wartość null lub ma wartość null, wartość jest konwertowana na pusty ciąg `""` (). <p><p>Jeśli na przykład przypiszesz zmienną ciągu do nieistniejącej właściwości, do której można uzyskać dostęp za pomocą operatora , wartość null zostanie przekonwertowana na `?` pusty ciąg. Jednak porównywanie wartości null nie jest tym samym co porównywanie pustego ciągu. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość ciągu*> | Ciąg | Wersja ciągu dla określonej wartości. Jeśli parametr *value* ma wartość null lub ma wartość null, ta wartość jest zwracana jako pusta wartość ciągu `""` (). |
||||





*Przykład 1*

Ten przykład tworzy wersję ciągu dla tego numeru:

```
string(10)
```

I zwraca ten wynik: `"10"`

*Przykład 2*

Ten przykład tworzy ciąg dla określonego obiektu JSON i używa znaku ukośnika odwrotnego ( ) jako znaku ucieczki dla podwójnego \\ cudzysłowu (").

```
string( { "name": "Sophie Owen" } )
```

I zwraca ten wynik: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Sub

Zwróć wynik od odejmowania drugiej liczby od pierwszej liczby.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Tak | Liczba całkowita lub liczba zmiennoprzecinkowa | Liczba, od której należy *odjąć podjęcie* |
| <*Odjemnik*> | Tak | Liczba całkowita lub liczba zmiennoprzecinkowa | Liczba do odjęcia *od wartości minimalnej* |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wynik*> | Liczba całkowita lub liczba zmiennoprzecinkowa | Wynik odejmowania drugiej liczby od pierwszej liczby |
||||

*Przykład*

Ten przykład odejmuje drugą liczbę od pierwszej liczby:

```
sub(10.3, .3)
```

I zwraca ten wynik: `10`

<a name="substring"></a>

### <a name="substring"></a>Podciąg

Zwraca znaki z ciągu, zaczynając od określonej pozycji lub indeksu. Wartości indeksu zaczynają się od liczby 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg, którego znaki mają |
| <*Startindex*> | Tak | Liczba całkowita | Liczba dodatnia równa lub większa niż 0, która ma być wartością początkową lub wartością indeksu |
| <*Długość*> | Nie | Liczba całkowita | Dodatnia liczba znaków, które mają być w podciągu |
|||||

> [!NOTE]
> Upewnij się, że suma z dodawania *wartości parametrów startIndex* i *length* jest mniejsza niż długość ciągu podanego dla *parametru tekstowego.*
> W przeciwnym razie wystąpi błąd, w przeciwieństwie do podobnych funkcji w innych językach, gdzie wynik jest podciągiem od *startIndex* do końca ciągu. Parametr *length* jest opcjonalny i jeśli nie zostanie podany, funkcja **substring()** przyjmuje wszystkie znaki, począwszy od *startIndex* do końca ciągu.

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*substring-result*> | Ciąg | Podciąg z określoną liczbą znaków, zaczynając od określonej pozycji indeksu w ciągu źródłowym |
||||

*Przykład*

W tym przykładzie tworzy pięcio znakowy podciąg z określonego ciągu, zaczynając od wartości indeksu 6:

```
substring('hello world', 6, 5)
```

I zwraca ten wynik: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Odejmij liczbę jednostek czasu od znacznika czasu.
Zobacz też [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg zawierający znacznik czasu |
| <*Interwał*> | Tak | Liczba całkowita | Liczba określonych jednostek czasu do odejmowania |
| <*timeUnit*> | Tak | Ciąg | Jednostka czasu do użycia z *interwałem:*"Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu aktualizacji*> | Ciąg | Sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Ten przykład odejmuje jeden dzień od tego znacznika czasu:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik: `"2018-01-01T00:00:00.0000000Z"`

*Przykład 2*

Ten przykład odejmuje jeden dzień od tego znacznika czasu:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

I zwraca ten wynik przy użyciu opcjonalnego formatu "D": `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take (pobierz)

Zwraca elementy z przodu kolekcji.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Tak | Ciąg lub tablica | Kolekcja, której elementy chcesz |
| <*Liczba*> | Tak | Liczba całkowita | Dodatnia liczba całkowita dla liczby elementów, które mają być od przodu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*subset*> lub [<*subset*>] | Odpowiednio ciąg lub tablica | Ciąg lub tablica, która ma określoną liczbę elementów pozyskinych z przodu oryginalnej kolekcji |
||||

*Przykład*

Te przykłady pobrać określoną liczbę elementów z przodu tych kolekcji:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

Zwróć następujące wyniki:

* Pierwszy przykład: `"abc"`
* Drugi przykład: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Kleszcze

Zwraca liczbę takt, które są interwałami 100-nanosekundowymi, od 1 stycznia 0001 12:00:00 o północy (lub DateTime.Ticks w języku C#) do określonego znacznika czasu. Aby uzyskać więcej informacji, zobacz ten temat: [Właściwość DateTime.Ticks (System)](/dotnet/api/system.datetime.ticks).

```
ticks('<timestamp>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Tak | Ciąg | Ciąg znacznika czasu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Liczba całkowita | Liczba takt od określonego znacznika czasu |
||||

<a name="toLower"></a>

### <a name="tolower"></a>Tolower

Zwraca ciąg w formacie małych liter. Jeśli znak w ciągu nie ma wersji z małymi literami, ten znak pozostaje niezmieniony w zwracanych ciągach.

```
toLower('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg do zwrócenia w formacie małych liter |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*małe litery —tekst*> | Ciąg | Oryginalny ciąg w formacie małych liter |
||||

*Przykład*

W tym przykładzie ten ciąg jest konwertowany na małe litery:

```
toLower('Hello World')
```

I zwraca ten wynik: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>Toupper

Zwraca ciąg w formacie wielkich liter. Jeśli znak w ciągu nie ma wersji z wielkich liter, ten znak pozostaje niezmieniony w zwracanych ciągach.

```
toUpper('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg zwracany w formacie wielkich liter |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wielkie litery tekstowe*> | Ciąg | Oryginalny ciąg w formacie wielkich liter |
||||

*Przykład*

W tym przykładzie ten ciąg jest konwertowany na wielkie litery:

```
toUpper('Hello World')
```

I zwraca ten wynik: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Wyzwalacz

Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazwa-wartość JSON, które można przypisać do wyrażenia.

* Wewnątrz danych wejściowych wyzwalacza ta funkcja zwraca dane wyjściowe z poprzedniego wykonania.

* Wewnątrz warunku wyzwalacza ta funkcja zwraca dane wyjściowe z bieżącego wykonania.

Domyślnie funkcja odwołuje się do całego obiektu wyzwalacza, ale opcjonalnie można określić właściwość, której wartość ma zostać dodana.
Ponadto ta funkcja ma dostępne skrócone wersje, zobacz [triggerOutputs()](#triggerOutputs) i [triggerBody()](#triggerBody).

```
trigger()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Ciąg | Dane wyjściowe z wyzwalacza w czasie wykonywania |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Zwraca dane wyjściowe wyzwalacza `body` w czasie wykonywania.
Skrót dla `trigger().outputs.body` .
Zobacz [trigger()](#trigger).

```
triggerBody()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | Ciąg | Dane `body` wyjściowe z wyzwalacza |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Zwraca tablicę z wartościami, które pasują do nazwy klucza w danych *formularza* wyzwalacza lub zakodowanych *w postaci danych wyjściowych.*

```
triggerFormDataMultiValues('<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Klucz*> | Tak | Ciąg | Nazwa klucza, którego wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Tablica | Tablica ze wszystkimi wartościami, które pasują do określonego klucza |
||||

*Przykład*

W tym przykładzie tworzymy tablicę na podstawie wartości klucza "feedUrl" w danych wyjściowych formularza lub zakodowanych w formacie wyzwalacza RSS:

```
triggerFormDataMultiValues('feedUrl')
```

I zwraca tę tablicę jako przykładowy wynik: `["https://feeds.a.dj.com/rss/RSSMarketsMain.xml"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Zwraca ciąg z pojedynczą wartością, która odpowiada nazwie klucza w danych *formularza* wyzwalacza lub danych wyjściowych *zakodowanych w postaci.*
Jeśli funkcja znajdzie więcej niż jedno dopasowanie, funkcja zrzuci błąd.

```
triggerFormDataValue('<key>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Klucz*> | Tak | Ciąg | Nazwa klucza, którego wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*klucz-wartość*> | Ciąg | Wartość w określonym kluczu |
||||

*Przykład*

W tym przykładzie jest tworzymy ciąg na podstawie wartości klucza "feedUrl" w danych wyjściowych formularza lub zakodowanych w formacie wyzwalacza RSS:

```
triggerFormDataValue('feedUrl')
```

I zwraca ten ciąg jako przykładowy wynik: `"https://feeds.a.dj.com/rss/RSSMarketsMain.xml"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Zwracanie treści dla określonej części danych wyjściowych wyzwalacza, która ma wiele części.

```
triggerMultipartBody(<index>)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Indeks*> | Tak | Liczba całkowita | Wartość indeksu dla chcieć części |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Ciała*> | Ciąg | Treść określonej części wieloczęściowych danych wyjściowych wyzwalacza |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazwa-wartość JSON.
Skrót dla `trigger().outputs` .
Zobacz [trigger()](#trigger).

```
triggerOutputs()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Ciąg | Dane wyjściowe z wyzwalacza w czasie wykonywania  |
||||

<a name="trim"></a>

### <a name="trim"></a>Przycinanie

Usuń wiodące i końcowe białe znaki z ciągu i zwróć zaktualizowany ciąg.

```
trim('<text>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Tak | Ciąg | Ciąg, który ma wiodące i końcowe białe znaki do usunięcia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updatedText (tekst zaktualizowany)*> | Ciąg | Zaktualizowana wersja oryginalnego ciągu bez wiodącego lub końcowego odstępu |
||||

*Przykład*

Ten przykład usuwa wiodące i końcowe białe znaki z ciągu " Hello world ":

```
trim(' Hello World  ')
```

I zwraca ten wynik: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>unia

Zwraca kolekcję, która *zawiera wszystkie* elementy z określonych kolekcji.
Aby pojawić się w wyniku, element może pojawić się w dowolnej kolekcji przekazanej do tej funkcji. Jeśli co najmniej jeden element ma taką samą nazwę, ostatni element o tej nazwie pojawi się w wyniku.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Tak | Tablica lub obiekt, ale nie oba | Kolekcje, z których *mają być wszystkie* elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Odpowiednio tablica lub obiekt | Kolekcja ze wszystkimi elementami z określonych kolekcji — bez duplikatów |
||||

*Przykład*

Ten przykład pobiera *wszystkie* elementy z tych kolekcji:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

I zwraca ten wynik: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Zwraca wersję zakodowaną w postaci identyfikatora URI dla ciągu, zastępując znaki niebezpieczne dla adresu URL znakami ucieczki.
Użyj tej funkcji zamiast [funkcji encodeUriComponent()](#encodeUriComponent).
Mimo że obie funkcje działają w taki sam sposób, `uriComponent()` preferowane jest rozwiązanie .

```
uriComponent('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg do przekonwertowania na format zakodowany w formacie URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Ciąg | Ciąg zakodowany w formacie URI ze znakami ucieczki |
||||

*Przykład*

Ten przykład tworzy wersję zakodowaną w formacie URI dla tego ciągu:

```
uriComponent('https://contoso.com')
```

I zwraca ten wynik: `"https%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Zwraca wersję binarną dla składnika identyfikatora URI.

```
uriComponentToBinary('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg zakodowany w formacie URI do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-URI*> | Ciąg | Wersja binarna dla ciągu zakodowanych w formacie URI. Zawartość binarna jest zakodowana w formacie base64 i reprezentowana `$content` przez . |
||||

*Przykład*

Ten przykład tworzy wersję binarną dla tego ciągu zakodowane w formacie URI:

```
uriComponentToBinary('https%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Zwraca wersję ciągu dla ciągu zakodowany za pomocą identyfikatora URI, efektywnie dekodując ciąg zakodowany w identyfikatorze URI.

```
uriComponentToString('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg zakodowany w formacie URI do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zdekodowany-uri*> | Ciąg | Zdekodowana wersja ciągu zakodowana w formacie URI |
||||

*Przykład*

W tym przykładzie owana jest zdekodowana wersja ciągu dla tego ciągu zakodowane w formacie URI:

```
uriComponentToString('https%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Zwraca wartość `host` identyfikatora URI.

```
uriHost('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Tak | Ciąg | URI, którego `host` wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość hosta*> | Ciąg | Wartość `host` określonego URI |
||||

*Przykład*

W tym przykładzie `host` znajduje się wartość tego URI:

```
uriHost('https://www.localhost.com:8080')
```

I zwraca ten wynik: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Zwraca wartość `path` identyfikatora URI.

```
uriPath('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Tak | Ciąg | URI, którego `path` wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*path-value*> | Ciąg | Wartość `path` określonego URI. Jeśli `path` nie ma wartości, zwróć znak "/". |
||||

*Przykład*

W tym przykładzie `path` znajduje się wartość tego URI:

```
uriPath('https://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Zwróć wartości `path` i `query` dla jednolitego identyfikatora zasobu (URI).

```
uriPathAndQuery('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Tak | Ciąg | URI, którego `path` wartości `query` i mają być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*path-query-value*> | Ciąg | Wartości `path` i dla określonego `query` URI. Jeśli `path` nie określi wartości, zwróć znak "/". |
||||

*Przykład*

W tym przykładzie można `path` znaleźć wartości i dla tego `query` URI:

```
uriPathAndQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Zwraca wartość `port` identyfikatora URI.

```
uriPort('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Tak | Ciąg | URI, którego `port` wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość portu*> | Liczba całkowita | Wartość `port` określonego URI. Jeśli `port` nie określi wartości, zwróć domyślny port protokołu. |
||||

*Przykład*

W tym przykładzie `port` zwracana jest wartość tego URI:

```
uriPort('https://www.localhost:8080')
```

I zwraca ten wynik: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Zwraca wartość `query` identyfikatora URI.

```
uriQuery('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Tak | Ciąg | URI, którego `query` wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*query-value*> | Ciąg | Wartość `query` określonego URI |
||||

*Przykład*

W tym przykładzie `query` zwracana jest wartość tego URI:

```
uriQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Zwraca wartość `scheme` identyfikatora URI.

```
uriScheme('<uri>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Tak | Ciąg | URI, którego `scheme` wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*scheme-value*> | Ciąg | Wartość `scheme` określonego URI |
||||

*Przykład*

W tym przykładzie `scheme` zwracana jest wartość tego URI:

```
uriScheme('https://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>Utcnow

Zwróć bieżący znacznik czasu.

```
utcNow('<format>')
```

Opcjonalnie można określić inny format przy użyciu <*format>* parametru.


| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Formacie*> | Nie | Ciąg | Pojedynczy [specyfikator formatu lub](/dotnet/standard/base-types/standard-date-and-time-format-strings) niestandardowy [wzorzec formatu](/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślny format znacznika czasu to ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss.fffffffK), który jest zgodny ze standardem [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*znacznik czasu bieżącego*> | Ciąg | Bieżąca data i godzina |
||||

*Przykład 1*

Załóżmy, że dzisiaj jest 15 kwietnia 2018 r. o 13:00:00.
Ten przykład pobiera bieżący znacznik czasu:

```
utcNow()
```

I zwraca ten wynik: `"2018-04-15T13:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że dzisiaj jest 15 kwietnia 2018 r. o 13:00:00.
Ten przykład pobiera bieżący znacznik czasu przy użyciu opcjonalnego formatu "D":

```
utcNow('D')
```

I zwraca ten wynik: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Zmiennych

Zwraca wartość określonej zmiennej.

```
variables('<variableName>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Variablename*> | Tak | Ciąg | Nazwa zmiennej, której wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zmienna-wartość*> | Dowolne | Wartość określonej zmiennej |
||||

*Przykład*

Załóżmy, że bieżąca wartość zmiennej "numItems" wynosi 20.
Ten przykład pobiera wartość całkowitą dla tej zmiennej:

```
variables('numItems')
```

I zwraca ten wynik: `20`

<a name="workflow"></a>

### <a name="workflow"></a>przepływ pracy

Zwraca wszystkie szczegóły dotyczące samego przepływu pracy w czasie wykonywania.

```
workflow().<property>
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Właściwość*> | Nie | Ciąg | Nazwa właściwości przepływu pracy, której wartość ma być <p><p>Domyślnie obiekt przepływu pracy ma następujące właściwości: `name` , , , , i `type` `id` `location` `run` `tags` . <p><p>- Wartość `run` właściwości jest obiektem JSON, który zawiera następujące właściwości: `name` , i `type` `id` . <p><p>— Właściwość jest obiektem JSON, który zawiera tagi skojarzone z aplikacją logiki w Azure Logic Apps lub przepływie w Power Automate i wartości `tags` tych tagów. [](../azure-resource-manager/management/tag-resources.md) Aby uzyskać więcej informacji na temat tagów w zasobach platformy Azure, zapoznaj się z tematem Tagowanie zasobów, grup zasobów i subskrypcji dla [organizacji logicznej na platformie Azure.](../azure-resource-manager/management/tag-resources.md) <p><p>**Uwaga:** domyślnie aplikacja logiki nie ma tagów, ale przepływ Power Automate ma tagi `flowDisplayName` `environmentName` i . |
|||||

*Przykład 1*

W tym przykładzie zwracana jest nazwa bieżącego uruchomienia przepływu pracy:

`workflow().run.name`

*Przykład 2*

Jeśli używasz Power Automate, możesz utworzyć wyrażenie, które używa właściwości output do uzyskania wartości z właściwości `@workflow()` `tags` lub `flowDisplayName` `environmentName` przepływu.

Możesz na przykład wysyłać niestandardowe powiadomienia e-mail z samego przepływu, które są linkami z powrotem do przepływu. Powiadomienia te mogą zawierać link HTML, który zawiera nazwę wyświetlaną przepływu w tytule wiadomości e-mail i ma następującą składnię:

`<a href=https://flow.microsoft.com/manage/environments/@{workflow()['tags']['environmentName']}/flows/@{workflow()['name']}/details>Open flow @{workflow()['tags']['flowDisplayName']}</a>`

<a name="xml"></a>

### <a name="xml"></a>xml

Zwróć wersję XML dla ciągu zawierającego obiekt JSON.

```
xml('<value>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Tak | Ciąg | Ciąg z obiektem JSON do przekonwertowania <p>Obiekt JSON musi mieć tylko jedną właściwość główną, która nie może być tablicą. <br>Użyj znaku ukośnika odwrotnego ( \\ ) jako znaku ucieczki dla podwójnego cudzysłowu ("). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*xml-version*> | Obiekt | Kodowany kod XML dla określonego ciągu lub obiektu JSON |
||||

*Przykład 1*

W tym przykładzie dla tego ciągu jest y y json json w wersji XML:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

I zwraca ten wynik XML:

```xml
<name>Sophia Owen</name>
```

*Przykład 2*

Załóżmy, że masz ten obiekt JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

W tym przykładzie kod XML jest tworzyny dla ciągu zawierającego ten obiekt JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

I zwraca ten wynik XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>Xpath

Sprawdź xml dla węzłów lub wartości, które pasują do wyrażenia XPath (XML Path Language) i zwróć pasujące węzły lub wartości. Wyrażenie XPath lub po prostu "XPath" ułatwia nawigowanie po strukturze dokumentu XML, dzięki czemu można wybrać węzły lub wartości obliczeniowe w zawartości XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Wymagane | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Tak | Dowolne | Ciąg XML do wyszukiwania węzłów lub wartości, które pasują do wartości wyrażenia XPath |
| <*Xpath*> | Tak | Dowolne | Wyrażenie XPath używane do znalezienia pasujących węzłów LUB wartości XML |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Węzeł XML, gdy tylko jeden węzeł pasuje do określonego wyrażenia XPath |
| <*Wartość*> | Dowolne | Wartość z węzła XML, gdy tylko jedna wartość pasuje do określonego wyrażenia XPath |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-lub- </br>[<*value1*>, <*value2*>, ...] | Tablica | Tablica z węzłami XML lub wartościami, które pasują do określonego wyrażenia XPath |
||||

*Przykład 1*

Załóżmy, że masz ten `'items'` ciąg XML: 

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Ten przykład przekazuje wyrażenie XPath w celu znalezienia węzłów, które pasują do węzła w ciągu XML, i zwraca tablicę `'/produce/item/name'` `<name></name>` z tymi `'items'` wartościami węzła:

`xpath(xml(parameters('items')), '/produce/item/name')`

W przykładzie użyto również [funkcji parameters(),](#parameters) aby pobrać ciąg XML z i przekonwertować ciąg na format XML przy `'items'` użyciu funkcji [xml().](#xml)

Oto tablica wyników z węzłami, które pasują `<name></name` do :

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Przykład 2*

W przykładzie 1 ten przykład przekazuje wyrażenie XPath, , aby znaleźć pierwszy element, który jest `'/produce/item/name[1]'` `name` elementem podrzędnym `item` elementu .

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

Oto wynik: `Gala`

*Przykład 3*

W przykładzie 1 w tym przykładzie przedstawiono wyrażenie XPath, , aby znaleźć ostatni element, który jest `'/produce/item/name[last()]'` `name` elementem `item` podrzędnym elementu .

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

Oto wynik: `Honeycrisp`

*Przykład 4*

W tym przykładzie załóżmy, że `items` ciąg XML zawiera również atrybuty i `expired='true'` `expired='false'` :

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Ten przykład przekazuje wyrażenie XPath, , aby znaleźć wszystkie `'//name[@expired]'` `name` elementy, które mają `expired` atrybut :

`xpath(xml(parameters('items')), '//name[@expired]')`

Oto wynik: `[ Gala, Honeycrisp ]`

*Przykład 5*

W tym przykładzie załóżmy, że `items` ciąg XML zawiera tylko ten atrybut: `expired = 'true'`

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Ten przykład przekazuje wyrażenie XPath, , aby znaleźć wszystkie `'//name[@expired = 'true']'` `name` elementy, które mają atrybut `expired = 'true'` :

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

Oto wynik: `[ Gala ]`

*Przykład 6*

W tym przykładzie załóżmy, że `items` ciąg XML zawiera również następujące atrybuty: 

* `expired='true' price='12'`
* `expired='false' price='40'`

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true' price='12'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false' price='40'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Ten przykład przekazuje wyrażenie XPath, `'//name[price>35]'` , aby znaleźć wszystkie `name` elementy, które mają `price > 35` :

`xpath(xml(parameters('items')), '//name[price>35]')`

Oto wynik: `Honeycrisp`

*Przykład 7*

W tym przykładzie załóżmy, że `items` ciąg XML jest taki sam jak w przykładzie 1:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

Ten przykład umożliwia znalezienie węzłów, które są zgodne z `<count></count>` węzłem, i dodanie tych wartości węzła do `sum()` funkcji :

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Oto wynik: `30`

*Przykład 8*

W tym przykładzie załóżmy, że masz ten ciąg XML, który zawiera przestrzeń nazw dokumentu `xmlns="https://contoso.com"` XML:

```xml
<?xml version="1.0"?><file xmlns="https://contoso.com"><location>Paris</location></file>
```

Te wyrażenia używają wyrażenia XPath `/*[name()="file"]/*[name()="location"]` lub , aby znaleźć `/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]` węzły zgodne z `<location></location>` węzłem. Te przykłady pokazują składnię używaną w Projektancie aplikacji logiki lub w edytorze wyrażeń:

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]')`

Oto węzeł wynikowy, który pasuje do `<location></location>` węzła: 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> Jeśli pracujesz w widoku kodu, przed znakiem podwójnego cudzysłowu (") należy użyć znaku ukośnika odwrotnego ( \\ ). 
> Na przykład należy użyć znaków ucieczki podczas serializacji wyrażenia jako ciągu JSON. 
> Jeśli jednak pracujesz w Projektancie aplikacji logiki lub edytorze wyrażeń, nie musisz usuwać podwójnego cudzysłowu, ponieważ znak ukośnika odwrotnego jest automatycznie dodawany do podstawowej definicji, na przykład:
> 
> * Widok kodu: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Edytor wyrażeń: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*Przykład 9*

W przykładzie 8 w tym przykładzie użyto wyrażenia XPath, , aby znaleźć `'string(/*[name()="file"]/*[name()="location"])'` wartość w `<location></location>` węźle:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Oto wynik: `Paris`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [języku definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md)
