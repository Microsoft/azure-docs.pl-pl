---
title: Przewodnik dla funkcji w wyrażeniach
description: Przewodnik po funkcjach w wyrażeniach dla aplikacji Azure Logic Apps i Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: f557753c61af1e57490ae2d10b7f42475bd7c0a6
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870231"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Przewodnik dotyczący używania funkcji w wyrażeniach dla aplikacji Azure Logic Apps i Power Automate

W przypadku definicji przepływu pracy w [usłudze Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [power automatyce](https://docs.microsoft.com/flow/getting-started)niektóre [wyrażenia](../logic-apps/logic-apps-workflow-definition-language.md#expressions) otrzymują swoje wartości z akcji środowiska uruchomieniowego, które mogą jeszcze nie istnieć, gdy przepływ pracy zacznie działać. Aby odwołać się do tych wartości lub przetworzyć wartości w tych wyrażeniach, można użyć *funkcji dostarczonych* przez [język definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Ta strona odwołania dotyczy zarówno usługi Azure Logic Apps, jak i Power Automate, ale jest wyświetlana w dokumentacji usługi Azure Logic Apps. Chociaż ta strona odnosi się specjalnie do aplikacji logiki, te funkcje działają zarówno dla przepływów, jak i aplikacji logiki. Aby uzyskać więcej informacji o funkcjach i wyrażeniach w programie Power Automate, zobacz [Używanie wyrażeń w warunkach](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Na przykład wartości można obliczyć za pomocą funkcji matematycznych, takich jak [funkcja add(),](../logic-apps/workflow-definition-language-functions-reference.md#add)jeśli chcesz sumę z liczby całkowitej lub floats. Oto inne przykładowe zadania, które można wykonywać z funkcjami:

| Zadanie | Składnia funkcji | Wynik |
| ---- | --------------- | ------ |
| Zwraca ciąg w formacie małych liter. | toLower("<*tekst*>") <p>Na przykład: toLower('Hello') | "cześć" |
| Zwraca unikatowy identyfikator globalny (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Aby znaleźć funkcje [na podstawie ich ogólnego przeznaczenia,](#ordered-by-purpose)zapoznaj się z poniższymi tabelami. Lub, aby uzyskać szczegółowe informacje na temat każdej funkcji, zobacz [listę alfabetyczną](#alphabetical-list).

> [!NOTE]
> W składni dla definicji parametrów, znak zapytania (?), który pojawia się po parametr oznacza, że parametr jest opcjonalny.
> Na przykład zobacz [getFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funkcje w wyrażeniach

Aby pokazać, jak używać funkcji w wyrażeniu, w tym `customerName` przykładzie pokazano, `accountName` jak można uzyskać wartość z parametru i przypisać tę wartość do właściwości za pomocą funkcji [parameters()](#parameters) w wyrażeniu:

```json
"accountName": "@parameters('customerName')"
```

Oto kilka innych ogólnych sposobów używania funkcji w wyrażeniach:

| Zadanie | Składnia funkcji w wyrażeniu |
| ---- | -------------------------------- |
| Wykonywanie pracy z elementem, przekazując ten element do funkcji. | "\@<*functionName*>(<*item*>)" |
| 1. Pobierz wartość *parameterName*'s za `parameters()` pomocą funkcji zagnieżdżonej. </br>2. Wykonaj pracę z wynikiem, przekazując tę wartość do *functionName*. | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1. Pobierz wynik z zagnieżdżonej *funkcji wewnętrznejName*. </br>2. Przekaż wynik funkcji *zewnętrznejName2*. | "\@<*functionName2*>(<*functionName*>(<*item*>)". |
| 1. Pobierz wynik z *functionName*. </br>2. Biorąc pod uwagę, że wynik jest obiektem z *property propertyName*, pobierz wartość tej właściwości. | "\@<*functionName*>(<*item*>).<*propertyName*>" |
|||

Na przykład `concat()` funkcja może przyjmować dwie lub więcej wartości ciągu jako parametry. Ta funkcja łączy te ciągi w jeden ciąg.
Możesz albo przekazać w literałach ciągów, na przykład "Sophia" i "Owen", aby uzyskać połączony ciąg "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Lub można uzyskać wartości ciągów z parametrów. `parameters()` W tym przykładzie użyto funkcji w każdym `concat()` parametrze i `firstName` i `lastName` parametrów. Następnie należy przekazać wynikowe ciągi do `concat()` funkcji, dzięki czemu można uzyskać połączony ciąg, na przykład "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Tak czy inaczej, oba przykłady `customerName` przypisać wynik do właściwości.

Oto dostępne funkcje uporządkowane według ich ogólnego przeznaczenia lub możesz przeglądać funkcje w oparciu o [kolejność alfabetyczną.](#alphabetical-list)

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funkcje ciągów

Aby pracować z ciągami, można użyć tych funkcji ciągu, a także niektórych [funkcji zbierania](#collection-functions).
Funkcje ciągów działają tylko na ciągach.

| Ciąg, funkcja | Zadanie |
| --------------- | ---- |
| [Concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Połącz dwa lub więcej ciągów i zwróć połączony ciąg. |
| [Endswith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Sprawdź, czy ciąg kończy się określonym podciągiem. |
| [Formatnumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Zwraca liczbę jako ciąg na podstawie określonego formatu |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generowanie unikatowego identyfikatora (GUID) jako ciągu. |
| [Indexof](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Zwraca pozycję początkową podciągu. |
| [Lastindexof](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Zwraca pozycję początkową dla ostatniego wystąpienia podciągu. |
| [Zastąpić](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Zamień podciąg na określony ciąg i zwróć zaktualizowany ciąg. |
| [Split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Zwraca tablicę zawierającą podciągi, oddzielone przecinkami, z większego ciągu opartego na określonym znaku ogranicznika w oryginalnym ciągu. |
| [Startswith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Sprawdź, czy ciąg zaczyna się od określonego podciągu. |
| [Podciąg](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Zwraca znaki z ciągu, zaczynając od określonej pozycji. |
| [Tolower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Zwraca ciąg w formacie małych liter. |
| [Toupper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Zwraca ciąg wielkimi literami. |
| [Przycinanie](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Usuń interweniujący i końcowy biały znak z ciągu i zwraca zaktualizowany ciąg. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funkcje zbierania

Aby pracować z kolekcji, zazwyczaj tablice, ciągi i czasami słowniki, można użyć tych funkcji kolekcji.

| Kolekcja, funkcja | Zadanie |
| ------------------- | ---- |
| [Zawiera](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Sprawdź, czy kolekcja ma określony element. |
| [Pusty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Sprawdź, czy kolekcja jest pusta. |
| [Pierwszym](../logic-apps/workflow-definition-language-functions-reference.md#first) | Zwraca pierwszy element z kolekcji. |
| [Przecięcia](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Zwraca kolekcję, która ma *tylko* wspólne elementy w określonych kolekcjach. |
| [Element](../logic-apps/workflow-definition-language-functions-reference.md#item) | W przypadku wewnątrz powtarzania akcji nad tablicą, zwraca bieżący element w tablicy podczas bieżącej iteracji akcji. |
| [Dołączyć](../logic-apps/workflow-definition-language-functions-reference.md#join) | Zwraca ciąg, który ma *wszystkie* elementy z tablicy, oddzielone określonym znakiem. |
| [Ostatnio](../logic-apps/workflow-definition-language-functions-reference.md#last) | Zwróć ostatni element z kolekcji. |
| [Długość](../logic-apps/workflow-definition-language-functions-reference.md#length) | Zwraca liczbę elementów w ciągu lub tablicy. |
| [Pominąć](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Usuń elementy z przodu kolekcji i zwróć *wszystkie pozostałe* elementy. |
| [wziąć](../logic-apps/workflow-definition-language-functions-reference.md#take) | Zwracaj przedmioty z przodu kolekcji. |
| [Unii](../logic-apps/workflow-definition-language-functions-reference.md#union) | Zwraca kolekcję, która ma *wszystkie* elementy z określonych kolekcji. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funkcje porównywania logicznego

Aby pracować z warunkami, porównać wartości i wyniki wyrażenia lub ocenić różne rodzaje logiki, można użyć tych funkcji porównania logicznego.
Aby uzyskać pełne informacje o każdej funkcji, zobacz [listę alfabetyczną](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja porównywania logicznego | Zadanie |
| --------------------------- | ---- |
| [I](../logic-apps/workflow-definition-language-functions-reference.md#and) | Sprawdź, czy wszystkie wyrażenia są prawdziwe. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Sprawdź, czy obie wartości są równoważne. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Sprawdź, czy pierwsza wartość jest większa niż druga wartość. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Sprawdź, czy pierwsza wartość jest większa lub równa drugiej wartości. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Sprawdź, czy wyrażenie jest prawdziwe czy fałszywe. Na podstawie wyniku zwraca określoną wartość. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Sprawdź, czy pierwsza wartość jest mniejsza niż druga wartość. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Sprawdź, czy pierwsza wartość jest mniejsza lub równa drugiej wartości. |
| [Nie](../logic-apps/workflow-definition-language-functions-reference.md#not) | Sprawdź, czy wyrażenie jest fałszywe. |
| [Lub](../logic-apps/workflow-definition-language-functions-reference.md#or) | Sprawdź, czy co najmniej jedno wyrażenie jest prawdziwe. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funkcje konwersji

Aby zmienić typ lub format wartości, możesz użyć tych funkcji konwersji.
Na przykład można zmienić wartość z wartości logicznej na całkowitej.
Aby uzyskać więcej informacji o tym, jak aplikacje logiki obsługują typy zawartości podczas konwersji, zobacz [Obsługa typów zawartości](../logic-apps/logic-apps-content-type.md).
Aby uzyskać pełne informacje o każdej funkcji, zobacz [listę alfabetyczną](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja konwersji | Zadanie |
| ------------------- | ---- |
| [tablica](../logic-apps/workflow-definition-language-functions-reference.md#array) | Zwraca tablicę z jednego określonego wejścia. Dla wielu wejść, zobacz [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Zwraca wersję zakodowaną w base64 dla ciągu. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Zwraca wersję binarną dla ciągu zakodowanego base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Zwraca wersję ciągu dla ciągu zakodowanego base64. |
| [binarny](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Zwraca wersję binarną dla wartości wejściowej. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Zwraca wersję logiczną dla wartości wejściowej. |
| [tworzenieArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Zwraca tablicę z wielu danych wejściowych. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Zwraca identyfikator URI danych dla wartości wejściowej. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Zwraca wersję binarną dla identyfikatora URI danych. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Zwraca wersję ciągu dla identyfikatora URI danych. |
| [dekodowanieBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Zwraca wersję ciągu dla ciągu zakodowanego base64. |
| [dekodowanieDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Zwraca wersję binarną dla identyfikatora URI danych. |
| [dekodowanieskładeksu](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Zwraca ciąg, który zastępuje znaki ucieczki z dekodowanych wersji. |
| [Encodeuricomponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Zwraca ciąg, który zastępuje znaki niebezpieczne dla adresów URL znakami ucieczki. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Zwraca numer zmiennoprzecinkowy dla wartości wejściowej. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Zwraca wersję całkowitą dla ciągu. |
| [Json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Zwraca wartość typu ISON (JavaScript Object Notation) lub obiekt dla ciągu lub pliku XML. |
| [ciąg](../logic-apps/workflow-definition-language-functions-reference.md#string) | Zwraca wersję ciągu dla wartości wejściowej. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Zwraca wersję zakodowaną w identyfikatorze URI dla wartości wejściowej, zastępując znaki niebezpieczne dla adresów URL znakami ucieczki. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Zwraca wersję binarną dla ciągu zakodowanego zgodnie z identyfikatorem URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Zwraca wersję ciągu dla ciągu zakodowanego w identyfikatorze URI. |
| [Xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Zwraca wersję XML dla ciągu. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Funkcje matematyczne

Aby pracować z liczby całkowite i pływaków, można użyć tych funkcji matematycznych.
Aby uzyskać pełne informacje o każdej funkcji, zobacz [listę alfabetyczną](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja matematyczna | Zadanie |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Zwraca wynik z dodawania dwóch liczb. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Zwraca wynik z dzielenia dwóch liczb. |
| [Max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Zwraca najwyższą wartość z zestawu liczb lub tablicy. |
| [Min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Zwraca najniższą wartość z zestawu liczb lub tablicy. |
| [Mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Zwróć pozostałą część z podzielenia dwóch liczb. |
| [Mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Zwróć produkt z pomnożenia dwóch liczb. |
| [Rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Zwraca losową ćę całkowitą z określonego zakresu. |
| [Zakres](../logic-apps/workflow-definition-language-functions-reference.md#range) | Zwraca tablicę całkowitą, która rozpoczyna się od określonej liczby całkowitej. |
| [Sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Zwraca wynik z odejmowania drugiej liczby od pierwszej liczby. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funkcje daty i godziny

Aby pracować z datami i godzinami, można użyć tych funkcji daty i godziny.
Aby uzyskać pełne informacje o każdej funkcji, zobacz [listę alfabetyczną](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja daty lub godziny | Zadanie |
| --------------------- | ---- |
| [Adddays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Dodaj liczbę dni do sygnatury czasowej. |
| [Addhours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Dodaj liczbę godzin do sygnatury czasowej. |
| [Addminutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Dodaj liczbę minut do sygnatury czasowej. |
| [Addseconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Dodaj liczbę sekund do sygnatury czasowej. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Dodaj liczbę jednostek czasu do sygnatury czasowej. Zobacz też [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Konwertuj sygnaturę czasową z uniwersalnej strefy czasowej (UTC) na docelową strefę czasową. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na uniwersalną koordynacyjną (UTC). |
| [dzieńPomiesiętny](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Zwraca składnik dzień miesiąca z sygnatury czasowej. |
| [Dayofweek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Zwraca składnik dzień tygodnia z sygnatury czasowej. |
| [Dayofyear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Zwraca składnik dzień roku z sygnatury czasowej. |
| [Formatdatetime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Zwraca datę z sygnatury czasowej. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Zwraca bieżący sygnatura czasowa oraz określone jednostki czasu. Zobacz też [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Zwraca bieżący sygnatura czasowa minus określone jednostki czasu. Zobacz też [otracaćFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Zwraca początek dnia dla sygnatury czasowej. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Zwraca początek godziny dla sygnatury czasowej. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Zwraca początek miesiąca dla sygnatury czasowej. |
| [odejmowanieOdczasu](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Odejmij liczbę jednostek czasu od sygnatury czasowej. Zobacz też [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [Kleszcze](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Zwraca `ticks` wartość właściwości dla określonej sygnatury czasowej. |
| [Utcnow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Zwraca bieżący sygnatura czasowa jako ciąg. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funkcje przepływu pracy

Te funkcje przepływu pracy mogą ci pomóc:

* Uzyskaj szczegółowe informacje o wystąpieniu przepływu pracy w czasie wykonywania.
* Praca z danych wejściowych używanych do tworzenia wystąpienia aplikacji logiki lub przepływów.
* Odwołaj się do wyjść z wyzwalaczy i akcji.

Na przykład można odwołać się do wyjść z jednej akcji i użyć tych danych w późniejszej akcji.
Aby uzyskać pełne informacje o każdej funkcji, zobacz [listę alfabetyczną](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja przepływu pracy | Zadanie |
| ----------------- | ---- |
| [Działania](../logic-apps/workflow-definition-language-functions-reference.md#action) | Zwraca dane wyjściowe bieżącej akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz też [akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [ciało akcji](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Zwraca dane wyjściowe `body` akcji w czasie wykonywania. Zobacz też [body](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs (Nieumiejeć)](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Zwraca dane wyjściowe akcji w czasie wykonywania. Zobacz [dane wyjściowe](../logic-apps/workflow-definition-language-functions-reference.md#outputs) i [akcje](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [Działania](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Zwraca dane wyjściowe akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz też [akcja](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [Ciała](#body) | Zwraca dane wyjściowe `body` akcji w czasie wykonywania. Zobacz też [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [wartości formularzaDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Utwórz tablicę z wartościami, które pasują do nazwy klucza w *danych formularza* lub danych *zakodowanych* w formularzu. |
| [wartość danych formularza](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Zwraca pojedynczą wartość, która pasuje do nazwy klucza w *danych formularza* akcji lub *danych wyjściowych zakodowanych w formularzu*. |
| [Element](../logic-apps/workflow-definition-language-functions-reference.md#item) | W przypadku wewnątrz powtarzania akcji nad tablicą, zwraca bieżący element w tablicy podczas bieżącej iteracji akcji. |
| [Elementy](../logic-apps/workflow-definition-language-functions-reference.md#items) | W pętli Foreach lub Until zwraca bieżący element z określonej pętli.|
| [iteracjaIndeksy](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Gdy wewnątrz Until pętli, zwraca wartość indeksu dla bieżącej iteracji. Tej funkcji można użyć wewnątrz zagnieżdżonych pętli Until. |
| [listaCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Zwróć "adres URL wywołania zwrotnego", który wywołuje wyzwalacz lub akcję. |
| [wieloczęścioweBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Zwraca obiekt dla określonej części w danych wyjściowych akcji, która ma wiele części. |
| [Wyjść](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Zwraca dane wyjściowe akcji w czasie wykonywania. |
| [Parametry](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Zwraca wartość parametru opisanego w definicji przepływu pracy. |
| [Wynik](../logic-apps/workflow-definition-language-functions-reference.md#result) | Zwraca dane wejściowe i wyjściowe ze wszystkich akcji wewnątrz `For_each`określonej akcji o określonym zakresie, takich jak , `Until`i `Scope`. |
| [Wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub z innych par nazw i wartości JSON. Zobacz też [triggerOutputs](#triggerOutputs) i [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody (Ciało wyzwalające)](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Zwraca dane wyjściowe `body` wyzwalacza w czasie wykonywania. Zobacz [wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Zwraca pojedynczą wartość pasującą nazwę klucza w *danych formularza* lub wyjściach wyzwalaczy *zakodowanych w formularzu.* |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Zwraca obiekt dla określonej części w wieloczęściowym wyjściu wyzwalacza. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Utwórz tablicę, której wartości odpowiadają nazwie klucza w *danych formularza* lub danych *wyzwalaczy zakodowanych* w formularzu. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazw i wartości JSON. Zobacz [wyzwalacz](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [Zmiennych](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Zwraca wartość dla określonej zmiennej. |
| [Przepływu pracy](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Zwraca wszystkie szczegóły dotyczące samego przepływu pracy w czasie wykonywania. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funkcje analizowania identyfikatorów URI

Aby pracować z jednolitymi identyfikatorami zasobów (URI) i uzyskać różne wartości właściwości dla tych identyfikatorów URI, można użyć tych funkcji analizowania identyfikatorów URI.
Aby uzyskać pełne informacje o każdej funkcji, zobacz [listę alfabetyczną](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funkcja analizowania identyfikatorów URI | Zadanie |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Zwraca `host` wartość jednolitego identyfikatora zasobu (URI). |
| [uriPath (ścieżka uripath)](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Zwraca `path` wartość jednolitego identyfikatora zasobu (URI). |
| [uriPathAndQuery (uriPathAndQuery)](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Zwraca `path` wartości `query` i dla jednolitego identyfikatora zasobu (URI). |
| [uriPort (port uriport)](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Zwraca `port` wartość jednolitego identyfikatora zasobu (URI). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Zwraca `query` wartość jednolitego identyfikatora zasobu (URI). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Zwraca `scheme` wartość jednolitego identyfikatora zasobu (URI). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funkcje manipulacji: JSON & XML

Aby pracować z obiektami JSON i węzłami XML, można użyć tych funkcji manipulacji.
Aby uzyskać pełne informacje o każdej funkcji, zobacz [listę alfabetyczną](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Manipulacja, funkcja | Zadanie |
| --------------------- | ---- |
| [dodajwłaściwość](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Dodaj właściwość i jej wartość lub parę nazwa-wartość do obiektu JSON i zwróć zaktualizowany obiekt. |
| [Łączonej](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Zwraca pierwszą wartość niekwaśną z jednego lub więcej parametrów. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Usuń właściwość z obiektu JSON i zwróć zaktualizowany obiekt. |
| [Setproperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Ustaw wartość właściwości obiektu JSON i zwróć zaktualizowany obiekt. |
| [Xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Sprawdź xml dla węzłów lub wartości, które pasują do wyrażenia XPath (XML Path Language) i zwraca pasujące węzły lub wartości. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Wszystkie funkcje - lista alfabetyczna

W tej sekcji wymieniono wszystkie dostępne funkcje w kolejności alfabetycznej.

<a name="action"></a>

### <a name="action"></a>action

Zwraca dane wyjściowe *bieżącej* akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON, które można przypisać do wyrażenia.
Domyślnie ta funkcja odwołuje się do całego obiektu akcji, ale opcjonalnie można określić właściwość, której wartość ma być.
Zobacz też [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

`action()` Funkcji można używać tylko w następujących miejscach:

* Właściwość `unsubscribe` dla akcji elementu webhook, dzięki czemu `subscribe` można uzyskać dostęp do wyniku z oryginalnego żądania
* Właściwość `trackedProperties` dla działania
* Warunek `do-until` pętli dla akcji

```
action()
action().outputs.body.<property>
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Właściwość*> | Nie | Ciąg | Nazwa właściwości obiektu action, której wartość ma wartość: **nazwa**, **startTime**, **endTime**, **dane wejściowe, dane wyjściowe,** **stan**, **kod**, **trackingId**i **clientTrackingId**. **outputs** W witrynie Azure portal można znaleźć te właściwości, przeglądając szczegóły określonej historii uruchamiania. Aby uzyskać więcej informacji, zobacz [INTERFEJS API REST - Akcje uruchamiania przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*wyjście akcji*> | Ciąg | Dane wyjściowe z bieżącej akcji lub właściwości |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>ciało akcji

Zwraca dane wyjściowe `body` akcji w czasie wykonywania.
Skrót do `actions('<actionName>').outputs.body`.
Zobacz [body()](#body) i [actions()](#actions).

```
actionBody('<actionName>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*nazwa działania*> | Yes | Ciąg | Nazwa żądanego `body` wyjścia akcji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*wyjście z ciała-ciało*> | Ciąg | Dane `body` wyjściowe z określonej akcji |
||||

*Przykład*

W tym `body` przykładzie pobiera `Get user`dane wyjściowe z akcji Twitter:

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

### <a name="actionoutputs"></a>actionOutputs (Nieumiejeć)

Zwraca dane wyjściowe akcji w czasie wykonywania.  i jest skrótem od `actions('<actionName>').outputs`. Zobacz [actions()](#actions). Funkcja `actionOutputs()` jest rozpoznawana `outputs()` w logice App Designer, więc należy rozważyć użycie [outputs()](#outputs), a nie . `actionOutputs()` Chociaż obie funkcje działają `outputs()` w ten sam sposób, jest preferowany.

```
actionOutputs('<actionName>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*nazwa działania*> | Yes | Ciąg | Nazwa żądanego wyjścia akcji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*Wyjście*> | Ciąg | Dane wyjściowe z określonej akcji |
||||

*Przykład*

W tym przykładzie pobiera `Get user`dane wyjściowe z akcji Twitter:

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

Zwraca dane wyjściowe akcji w czasie wykonywania lub wartości z innych par nazw i wartości JSON, które można przypisać do wyrażenia. Domyślnie funkcja odwołuje się do całego obiektu akcji, ale opcjonalnie można określić właściwość, której wartość ma być.
Aby zapoznać się z wersjami skróconymi, zobacz [actionBody()](#actionBody), [actionOutputs()](#actionOutputs)i [body()](#body).
Aby zapoznać się z bieżącą akcją, zobacz [action()](#action).

> [!NOTE]
> Wcześniej można było użyć `actions()` funkcji `conditions` lub elementu podczas określania, że akcja została uruchomiony na podstawie danych wyjściowych z innej akcji. Jednak aby zadeklarować jawnie zależności między akcjami, należy `runAfter` teraz użyć właściwości akcji zależnej.
> Aby dowiedzieć `runAfter` się więcej o właściwości, zobacz [Catch i obsługi błędów z runAfter właściwości](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*nazwa działania*> | Yes | Ciąg | Nazwa obiektu akcji, którego dane wyjściowe mają być  |
| <*Właściwość*> | Nie | Ciąg | Nazwa właściwości obiektu action, której wartość ma wartość: **nazwa**, **startTime**, **endTime**, **dane wejściowe, dane wyjściowe,** **stan**, **kod**, **trackingId**i **clientTrackingId**. **outputs** W witrynie Azure portal można znaleźć te właściwości, przeglądając szczegóły określonej historii uruchamiania. Aby uzyskać więcej informacji, zobacz [INTERFEJS API REST - Akcje uruchamiania przepływu pracy](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*wyjście akcji*> | Ciąg | Dane wyjściowe z określonej akcji lub właściwości |
||||

*Przykład*

W tym `status` przykładzie pobiera wartość `Get user` właściwości z akcji Twitter w czasie wykonywania:

```
actions('Get_user').outputs.body.status
```

I zwraca ten wynik:`"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Zwraca wynik z dodawania dwóch liczb.

```
add(<summand_1>, <summand_2>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, *<summand_2*> | Yes | Całkowita, Float lub mieszana | Liczby do dodania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*suma wyniku*> | Całkowita lub float | Wynik z dodania określonych liczb |
||||

*Przykład*

W tym przykładzie dodano określone liczby:

```
add(1, 1.5)
```

I zwraca ten wynik:`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>Adddays

Dodaj liczbę dni do sygnatury czasowej.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Dni*> | Yes | Liczba całkowita | Dodatnia lub ujemna liczba dni do dodania |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa plus określona liczba dni  |
||||

*Przykład 1*

W tym przykładzie dodaje 10 dni do określonego sygnatury czasowej:

```
addDays('2018-03-15T13:00:00Z', 10)
```

I zwraca ten wynik:`"2018-03-25T00:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć dni od określonego sygnatury czasowej:

```
addDays('2018-03-15T00:00:00Z', -5)
```

I zwraca ten wynik:`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>Addhours

Dodaj liczbę godzin do sygnatury czasowej.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Godzin*> | Yes | Liczba całkowita | Dodatnia lub ujemna liczba godzin do dodania |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa plus określona liczba godzin  |
||||

*Przykład 1*

W tym przykładzie dodaje 10 godzin do określonego sygnatury czasowej:

```
addHours('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik:`"2018-03-15T10:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć godzin od określonego sygnatury czasowej:

```
addHours('2018-03-15T15:00:00Z', -5)
```

I zwraca ten wynik:`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>Addminutes

Dodaj liczbę minut do sygnatury czasowej.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Minut*> | Yes | Liczba całkowita | Dodatnia lub ujemna liczba minut do dodania |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa plus określona liczba minut |
||||

*Przykład 1*

W tym przykładzie dodaje 10 minut do określonego sygnatury czasowej:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

I zwraca ten wynik:`"2018-03-15T00:20:00.0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć minut od określonego sygnatury czasowej:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

I zwraca ten wynik:`"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>dodajwłaściwość

Dodaj właściwość i jej wartość lub parę nazwa-wartość do obiektu JSON i zwróć zaktualizowany obiekt. Jeśli właściwość już istnieje w czasie wykonywania, funkcja kończy się niepowodzeniem i zgłasza błąd.

```
addProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Yes | Obiekt | Obiekt JSON, w którym chcesz dodać właściwość |
| <*Właściwość*> | Yes | Ciąg | Nazwa właściwości do dodania |
| <*Wartość*> | Yes | Dowolne | Wartość właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany obiekt*> | Obiekt | Zaktualizowany obiekt JSON z określoną właściwością |
||||

Aby dodać właściwość podrzędną do istniejącej właściwości, użyj tej składni:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Yes | Obiekt | Obiekt JSON, w którym chcesz dodać właściwość |
| <*własność nadrzędna*> | Yes | Ciąg | Nazwa właściwości nadrzędnej, w której chcesz dodać właściwość podrzędną |
| <*własność dziecka*> | Yes | Ciąg | Nazwa właściwości podrzędnej do dodania |
| <*Wartość*> | Yes | Dowolne | Wartość ustawiona dla określonej właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany obiekt*> | Obiekt | Zaktualizowany obiekt JSON, którego właściwość została ustawiona |
||||

*Przykład 1*

W tym `middleName` przykładzie dodaje właściwość do obiektu JSON, który jest konwertowany z ciągu do JSON za pomocą funkcji [JSON().](#json) Obiekt zawiera już `firstName` `surName` właściwości i. Funkcja przypisuje określoną wartość do nowej właściwości i zwraca zaktualizowany obiekt:

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

W tym `middleName` przykładzie dodaje `customerName` właściwość podrzędną do istniejącej właściwości w obiekcie JSON, który jest konwertowany z ciągu do JSON za pomocą funkcji [JSON().](#json) Funkcja przypisuje określoną wartość do nowej właściwości i zwraca zaktualizowany obiekt:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
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

Dodaj liczbę sekund do sygnatury czasowej.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Sekund*> | Yes | Liczba całkowita | Dodatnia lub ujemna liczba sekund do dodania |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa plus określona liczba sekund  |
||||

*Przykład 1*

W tym przykładzie dodaje 10 sekund do określonego sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

I zwraca ten wynik:`"2018-03-15T00:00:10.0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje pięć sekund do określonego sygnatury czasowej:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

I zwraca ten wynik:`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Dodaj liczbę jednostek czasu do sygnatury czasowej.
Zobacz też [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Interwał*> | Yes | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*jednostka czasowa*> | Yes | Ciąg | Jednostka czasu do użycia z *interwałem:*"Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Rok" |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa plus określona liczba jednostek czasu  |
||||

*Przykład 1*

W tym przykładzie dodaje jeden dzień do określonego sygnatury czasowej:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik:`"2018-01-02T00:00:00.0000000Z"`

*Przykład 2*

W tym przykładzie dodaje jeden dzień do określonego sygnatury czasowej:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

I zwraca wynik przy użyciu opcjonalnego formatu "D":`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>i

Sprawdź, czy wszystkie wyrażenia są prawdziwe.
Zwraca wartość true, gdy wszystkie wyrażenia są prawdziwe, lub zwraca wartość false, gdy co najmniej jedno wyrażenie jest false.

```
and(<expression1>, <expression2>, ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Yes | Wartość logiczna | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy wszystkie wyrażenia są prawdziwe. Zwraca wartość false, gdy co najmniej jedno wyrażenie jest fałszywe. |
||||

*Przykład 1*

Te przykłady sprawdzić, czy określone wartości logiczne są prawdziwe:

```
and(true, true)
and(false, true)
and(false, false)
```

I zwraca te wyniki:

* Pierwszy przykład: Oba wyrażenia są `true`prawdziwe, więc zwraca .
* Drugi przykład: Jedno wyrażenie jest `false`fałszywe, więc zwraca .
* Trzeci przykład: Oba wyrażenia są `false`fałszywe, więc zwraca .

*Przykład 2*

Te przykłady sprawdzić, czy określone wyrażenia są prawdziwe:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

I zwraca te wyniki:

* Pierwszy przykład: Oba wyrażenia są `true`prawdziwe, więc zwraca .
* Drugi przykład: Jedno wyrażenie jest `false`fałszywe, więc zwraca .
* Trzeci przykład: Oba wyrażenia są `false`fałszywe, więc zwraca .

<a name="array"></a>

### <a name="array"></a>tablica

Zwraca tablicę z jednego określonego wejścia.
Dla wielu wejść zobacz [createArray()](#createArray).

```
array('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg do tworzenia tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [> wartości *<]* | Tablica | Tablica zawierająca pojedyncze określone dane wejściowe |
||||

*Przykład*

W tym przykładzie tworzy tablicę z ciągu "hello":

```
array('hello')
```

I zwraca ten wynik:`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Zwraca wersję zakodowaną w base64 dla ciągu.

```
base64('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg wejściowy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciąg podstawowy64*> | Ciąg | Wersja zakodowana w base64 dla ciągu wejściowego |
||||

*Przykład*

W tym przykładzie konwertuje ciąg "hello" na ciąg zakodowany base64:

```
base64('hello')
```

I zwraca ten wynik:`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Zwraca wersję binarną dla ciągu zakodowanego base64.

```
base64ToBinary('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg zakodowany w kodowaniu base64 do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciągbinowy dla base64*> | Ciąg | Wersja binarna dla ciągu zakodowanego base64 |
||||

*Przykład*

W tym przykładzie konwertuje ciąg "aGVsbG8=" zakodowany base64 na ciąg binarny:

```
base64ToBinary('aGVsbG8=')
```

I zwraca ten wynik:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Zwraca wersję ciągu dla ciągu zakodowanego base64, skutecznie dekodując ciąg base64.
Użyj tej funkcji, a nie [dekodowaniaBase64()](#decodeBase64).
Chociaż obie funkcje działają `base64ToString()` w ten sam sposób, jest preferowany.

```
base64ToString('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg kodowany base64 do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dekodowany-base64-string*> | Ciąg | Wersja ciągu dla ciągu zakodowanego base64 |
||||

*Przykład*

W tym przykładzie konwertuje ciąg "aGVsbG8=" kodowany base64 na ciąg:

```
base64ToString('aGVsbG8=')
```

I zwraca ten wynik:`"hello"`

<a name="binary"></a>

### <a name="binary"></a>binarny

Zwraca wersję binarną dla ciągu.

```
binary('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość binarna dla wejścia*> | Ciąg | Wersja binarna dla określonego ciągu |
||||

*Przykład*

W tym przykładzie konwertuje ciąg "hello" na ciąg binarny:

```
binary('hello')
```

I zwraca ten wynik:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Zwraca dane wyjściowe `body` akcji w czasie wykonywania.
Skrót do `actions('<actionName>').outputs.body`.
Zobacz [actionBody()](#actionBody) i [actions()](#actions).

```
body('<actionName>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*nazwa działania*> | Yes | Ciąg | Nazwa żądanego `body` wyjścia akcji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*wyjście z ciała-ciało*> | Ciąg | Dane `body` wyjściowe z określonej akcji |
||||

*Przykład*

W tym `body` przykładzie `Get user` pobiera dane wyjściowe z akcji Twitter:

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

Zwraca wersję logiczną dla wartości.

```
bool(<value>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Dowolne | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Wersja logiczna dla określonej wartości |
||||

*Przykład*

W tych przykładach konwertowane są określone wartości na wartości logiczne:

```
bool(1)
bool(0)
```

I zwraca te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>Łączonej

Zwraca pierwszą wartość niekwaśną z jednego lub więcej parametrów.
Puste ciągi, puste tablice i puste obiekty nie mają wartości null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, *<object_2*>, ... | Yes | Dowolna, może mieszać typy | Jeden lub więcej elementów do sprawdzenia wartości null |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*element pierwszy bez wartości null*> | Dowolne | Pierwszy element lub wartość, która nie jest null. Jeśli wszystkie parametry mają wartość null, ta funkcja zwraca wartość null. |
||||

*Przykład*

Te przykłady zwracają pierwszą wartość nie null z określonych wartości lub null, gdy wszystkie wartości są null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

I zwraca te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`"hello"`
* Trzeci przykład:`null`

<a name="concat"></a>

### <a name="concat"></a>concat

Połącz dwa lub więcej ciągów i zwróć połączony ciąg.

```
concat('<text1>', '<text2>', ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Yes | Ciąg | Co najmniej dwa ciągi do połączenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*text1text2...*> | Ciąg | Ciąg utworzony na podstawie połączonych ciągów wejściowych |
||||

*Przykład*

W tym przykładzie łączy ciągi "Hello" i "World":

```
concat('Hello', 'World')
```

I zwraca ten wynik:`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>zawiera

Sprawdź, czy kolekcja ma określony element.
Zwraca wartość true po znalezieniu elementu lub zwraca wartość false, gdy nie zostanie znaleziony.
W tej funkcji rozróżniana jest wielkość liter.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

W szczególności ta funkcja działa na tych typach kolekcji:

* *Ciąg,* aby znaleźć *podciąg*
* *Tablica* do znajdowania *wartości*
* *Słownik,* aby znaleźć *klucz*

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Yes | Ciąg, tablica lub słownik | Kolekcja do sprawdzenia |
| <*Wartość*> | Yes | Odpowiednio ciąg, tablica lub słownik | Element do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true po znalezieniu elementu. Zwraca wartość false, gdy nie znaleziono. |
||||

*Przykład 1*

W tym przykładzie sprawdza ciąg "hello world" dla podciąg "świat" i zwraca true:

```
contains('hello world', 'world')
```

*Przykład 2*

W tym przykładzie sprawdza ciąg "hello world" dla podciąg "universe" i zwraca false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Konwertuj sygnaturę czasową z uniwersalnej strefy czasowej (UTC) na docelową strefę czasową.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*destinationTimeZone*> | Yes | Ciąg | Nazwa docelowej strefy czasowej. Aby zapoznać się z nazwami stref czasowych, zobacz [Wartości indeksu stref czasowych firmy Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale może być trzeba usunąć wszelkie znaki interpunkcyjne z nazwy strefy czasowej. |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura czasowa konwersji*> | Ciąg | Sygnatura czasowa przekonwertowana na docelową strefę czasową |
||||

*Przykład 1*

W tym przykładzie sygnatura czasowa konwertuje określoną strefę czasową:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

I zwraca ten wynik:`"2018-01-01T00:00:00.0000000"`

*Przykład 2*

W tym przykładzie sygnatura czasowa konwertuje określoną strefę czasową i format:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik:`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na docelową strefę czasową.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Sourcetimezone*> | Yes | Ciąg | Nazwa źródłowej strefy czasowej. Aby zapoznać się z nazwami stref czasowych, zobacz [Wartości indeksu stref czasowych firmy Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale może być trzeba usunąć wszelkie znaki interpunkcyjne z nazwy strefy czasowej. |
| <*destinationTimeZone*> | Yes | Ciąg | Nazwa docelowej strefy czasowej. Aby zapoznać się z nazwami stref czasowych, zobacz [Wartości indeksu stref czasowych firmy Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale może być trzeba usunąć wszelkie znaki interpunkcyjne z nazwy strefy czasowej. |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura czasowa konwersji*> | Ciąg | Sygnatura czasowa przekonwertowana na docelową strefę czasową |
||||

*Przykład 1*

W tym przykładzie konwertuje strefę czasową źródła do docelowej strefy czasowej:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

I zwraca ten wynik:`"2018-01-01T00:00:00.0000000"`

*Przykład 2*

W tym przykładzie konwertuje strefę czasową do określonej strefy czasowej i formatu:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik:`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konwertuj sygnaturę czasową ze źródłowej strefy czasowej na uniwersalną koordynacyjną (UTC).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Sourcetimezone*> | Yes | Ciąg | Nazwa źródłowej strefy czasowej. Aby zapoznać się z nazwami stref czasowych, zobacz [Wartości indeksu stref czasowych firmy Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ale może być trzeba usunąć wszelkie znaki interpunkcyjne z nazwy strefy czasowej. |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura czasowa konwersji*> | Ciąg | Sygnatura czasowa przekonwertowana na utc |
||||

*Przykład 1*

W tym przykładzie konwertuje sygnaturę czasową na UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

I zwraca ten wynik:`"2018-01-01T08:00:00.0000000Z"`

*Przykład 2*

W tym przykładzie konwertuje sygnaturę czasową na UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

I zwraca ten wynik:`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>tworzenieArray

Zwraca tablicę z wielu danych wejściowych.
W przypadku pojedynczych tablic wejściowych zobacz [tablica()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Yes | Wszelkie, ale nie mieszane | Co najmniej dwa elementy do utworzenia tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Tablica | Tablica utworzona na podstawie wszystkich elementów wejściowych |
||||

*Przykład*

W tym przykładzie tworzy tablicę z tych danych wejściowych:

```
createArray('h', 'e', 'l', 'l', 'o')
```

I zwraca ten wynik:`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Zwraca identyfikator jednolitego zasobu danych (URI) dla ciągu.

```
dataUri('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dane-uri*> | Ciąg | Identyfikator URI danych dla ciągu wejściowego |
||||

*Przykład*

W tym przykładzie tworzy identyfikator URI danych dla ciągu "hello":

```
dataUri('hello')
```

I zwraca ten wynik:`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Zwraca wersję binarną dla identyfikatora jednolitego zasobu danych (URI).
Użyj tej funkcji, a nie [dekodowaniaDataUri()](#decodeDataUri).
Chociaż obie funkcje działają `dataUriBinary()` w ten sam sposób, jest preferowany.

```
dataUriToBinary('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Identyfikator URI danych do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*bin-for-data-uri*> | Ciąg | Wersja binarna dla identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie tworzy wersję binarną dla tego identyfikatora URI danych:

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

Zwraca wersję ciągu dla identyfikatora zasobów jednolitych danych (URI).

```
dataUriToString('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Identyfikator URI danych do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ciąg-dla danych-uri*> | Ciąg | Wersja ciągu dla identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie tworzy ciąg dla tego identyfikatora URI danych:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Zwróć dzień miesiąca z sygnatury czasowej.

```
dayOfMonth('<timestamp>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień miesiąca*> | Liczba całkowita | Dzień miesiąca z określonej sygnatury czasowej |
||||

*Przykład*

W tym przykładzie zwraca liczbę dla dnia miesiąca z tej sygnatury czasowej:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

I zwraca ten wynik:`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Zwróć dzień tygodnia z sygnatury czasowej.

```
dayOfWeek('<timestamp>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień tygodnia*> | Liczba całkowita | Dzień tygodnia od określonego sygnatury czasowej, w którym niedziela jest 0, poniedziałek to 1, i tak dalej |
||||

*Przykład*

W tym przykładzie zwraca liczbę dla dnia tygodnia z tej sygnatury czasowej:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

I zwraca ten wynik:`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Zwróć dzień roku z sygnatury czasowej.

```
dayOfYear('<timestamp>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dzień w roku*> | Liczba całkowita | Dzień roku z określonej sygnatury czasowej |
||||

*Przykład*

W tym przykładzie zwraca numer dnia roku z tej sygnatury czasowej:

```
dayOfYear('2018-03-15T13:27:36Z')
```

I zwraca ten wynik:`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>dekodowanieBase64

Zwraca wersję ciągu dla ciągu zakodowanego base64, skutecznie dekodując ciąg base64.
Należy rozważyć użycie [base64ToString()](#base64ToString) zamiast `decodeBase64()`.
Chociaż obie funkcje działają `base64ToString()` w ten sam sposób, jest preferowany.

```
decodeBase64('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg kodowany base64 do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dekodowany-base64-string*> | Ciąg | Wersja ciągu dla ciągu zakodowanego base64 |
||||

*Przykład*

W tym przykładzie tworzy ciąg dla ciągu zakodowanego base64:

```
decodeBase64('aGVsbG8=')
```

I zwraca ten wynik:`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>dekodowanieDataUri

Zwraca wersję binarną dla identyfikatora jednolitego zasobu danych (URI).
Należy rozważyć użycie [dataUriToBinary()](#dataUriToBinary)zamiast `decodeDataUri()`.
Chociaż obie funkcje działają `dataUriToBinary()` w ten sam sposób, jest preferowany.

```
decodeDataUri('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg identyfikatora URI danych do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*bin-for-data-uri*> | Ciąg | Wersja binarna dla ciągu identyfikatora URI danych |
||||

*Przykład*

W tym przykładzie zwraca wersję binarną dla tego identyfikatora URI danych:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

I zwraca ten wynik:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>dekodowanieskładeksu

Zwraca ciąg, który zastępuje znaki ucieczki z dekodowanych wersji.

```
decodeUriComponent('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg ze znakami ucieczki do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dekodowane-uri*> | Ciąg | Zaktualizowany ciąg z zdekodowanymi znakami ucieczki |
||||

*Przykład*

W tym przykładzie znaki ucieczki w tym ciągu zdekodowane wersje:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Zwraca wynik liczby całkowitej z podzielenia dwóch liczb.
Aby uzyskać pozostały wynik, zobacz [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Dywidendy*> | Yes | Całkowita lub float | Liczba do podzielenia przez *dzielnik* |
| <*Dzielnik*> | Yes | Całkowita lub float | Liczba, która dzieli *dywidendę,* ale nie może być 0 |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik ilorazu*> | Liczba całkowita | Wynik liczby całkowitej z podzielenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

Oba przykłady dzielą pierwszą liczbę przez drugą liczbę:

```
div(10, 5)
div(11, 5)
```

I zwrócić ten wynik:`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>Encodeuricomponent

Zwraca wersję zakodowaną w jednolitym identyfikatorze zasobu (URI) dla ciągu, zastępując znaki niebezpieczne dla adresów URL znakami ucieczki.
Należy rozważyć użycie [uriComponent()](#uriComponent), a nie `encodeUriComponent()`.
Chociaż obie funkcje działają `uriComponent()` w ten sam sposób, jest preferowany.

```
encodeUriComponent('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg do konwersji do formatu zakodowanego na URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zakodowane-uri*> | Ciąg | Ciąg zakodowany w kodach URI ze znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzy wersję zakodowaną w identyfikatorze URI dla tego ciągu:

```
encodeUriComponent('https://contoso.com')
```

I zwraca ten wynik:`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Sprawdź, czy kolekcja jest pusta.
Zwraca wartość true, gdy kolekcja jest pusta lub zwraca false, gdy nie jest pusty.

```
empty('<collection>')
empty([<collection>])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Yes | Ciąg, tablica lub obiekt | Kolekcja do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy kolekcja jest pusta. Zwraca wartość false, gdy nie jest pusta. |
||||

*Przykład*

Te przykłady sprawdzić, czy określone kolekcje są puste:

```
empty('')
empty('abc')
```

I zwraca te wyniki:

* Pierwszy przykład: Przekazuje pusty ciąg, `true`więc funkcja zwraca .
* Drugi przykład: Przekazuje ciąg "abc", `false`więc funkcja zwraca .

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Sprawdź, czy ciąg kończy się określonym podciągiem.
Zwraca wartość true po znalezieniu podciągów lub zwraca wartość false, gdy nie zostanie znaleziony.
W tej funkcji nie jest rozróżniana wielkość liter.

```
endsWith('<text>', '<searchText>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Yes | Ciąg | Ciąg do sprawdzenia |
| <*tekst wyszukiwania*> | Yes | Ciąg | Podciąg końcowy, aby znaleźć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true po znalezieniu końcowego podciągu. Zwraca wartość false, gdy nie znaleziono. |
||||

*Przykład 1*

W tym przykładzie sprawdza, czy ciąg "hello world" kończy się ciągiem "świat":

```
endsWith('hello world', 'world')
```

I zwraca ten wynik:`true`

*Przykład 2*

W tym przykładzie sprawdza, czy ciąg "hello world" kończy się ciągiem "wszechświat":

```
endsWith('hello world', 'universe')
```

I zwraca ten wynik:`false`

<a name="equals"></a>

### <a name="equals"></a>equals

Sprawdź, czy obie wartości, wyrażenia lub obiekty są równoważne.
Zwracaj wartość true, gdy oba są równoważne lub zwracaj false, gdy nie są równoważne.

```
equals('<object1>', '<object2>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*obiekt2*> | Yes | Różnych | Wartości, wyrażenia lub obiekty do porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy oba są równoważne. Zwraca wartość false, gdy nie jest odpowiednikiem. |
||||

*Przykład*

Te przykłady sprawdzić, czy określone dane wejściowe są równoważne.

```
equals(true, 1)
equals('abc', 'abcd')
```

I zwraca te wyniki:

* Pierwszy przykład: Obie wartości są równoważne, więc funkcja zwraca `true`.
* Drugi przykład: Obie wartości nie są równoważne, więc funkcja zwraca `false`.

<a name="first"></a>

### <a name="first"></a>Pierwszym

Zwraca pierwszy element z ciągu lub tablicy.

```
first('<collection>')
first([<collection>])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Yes | Ciąg lub tablica | Kolekcja, w której można znaleźć pierwszy element |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*element pierwszej kolekcji*> | Dowolne | Pierwszy element w kolekcji |
||||

*Przykład*

Te przykłady znaleźć pierwszy element w tych kolekcjach:

```
first('hello')
first(createArray(0, 1, 2))
```

I zwrócić te wyniki:

* Pierwszy przykład:`"h"`
* Drugi przykład:`0`

<a name="float"></a>

### <a name="float"></a>float

Konwertuj wersję ciągu dla liczby zmiennoprzecinkowej na rzeczywistą liczbę zmiennoprzecinkową.
Tej funkcji można używać tylko podczas przekazywania parametrów niestandardowych do aplikacji, na przykład aplikacji logiki lub przepływu.

```
float('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg, który ma prawidłową liczbę zmiennoprzecinkową do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość float*> | Liczba zmiennoprzecinkowa | Numer zmiennoprzecinkowy dla określonego ciągu |
||||

*Przykład*

W tym przykładzie tworzy wersję ciągu dla tej liczby zmiennoprzecinkowej:

```
float('10.333')
```

I zwraca ten wynik:`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>Formatdatetime

Zwraca sygnaturę czasową w określonym formacie.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura czasowa sformatowana w formatach*> | Ciąg | Zaktualizowany sygnatura czasowa w określonym formacie |
||||

*Przykład*

W tym przykładzie sygnatura czasowa konwertuje do określonego formatu:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

I zwraca ten wynik:`"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>wartości formularzaDataMultiValues

Zwraca tablicę z wartościami, które pasują do nazwy klucza w *danych formularza* akcji lub danych *wyjściowych zakodowanych w formularzu.*

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*nazwa działania*> | Yes | Ciąg | Akcja, której dane wyjściowe ma wartość klucza, którą chcesz |
| <*Klucz*> | Yes | Ciąg | Nazwa klucza, którego wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<> *tablica z wartościami klucza]* | Tablica | Tablica ze wszystkimi wartościami zgodnymi z określonym kluczem |
||||

*Przykład*

W tym przykładzie tworzy tablicę z wartości klucza "Podmiot" w określonym danych formularza lub danych zakodowanych w formularzu:

```
formDataMultiValues('Send_an_email', 'Subject')
```

I zwraca tekst tematu w tablicy, na przykład:`["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>wartość danych formularza

Zwraca pojedynczą wartość, która pasuje do nazwy klucza w *danych formularza akcji* lub danych *wyjściowych zakodowanych w formularzu.*
Jeśli funkcja znajdzie więcej niż jedno dopasowanie, funkcja zgłasza błąd.

```
formDataValue('<actionName>', '<key>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*nazwa działania*> | Yes | Ciąg | Akcja, której dane wyjściowe ma wartość klucza, którą chcesz |
| <*Klucz*> | Yes | Ciąg | Nazwa klucza, którego wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość klucza*> | Ciąg | Wartość w określonym kluczu  |
||||

*Przykład*

W tym przykładzie tworzy ciąg z wartości klucza "Podmiot" w określonym danych formularza lub danych zakodowanych w formularzu:

```
formDataValue('Send_an_email', 'Subject')
```

I zwraca tekst tematu jako ciąg, na przykład:`"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>Formatnumber

Zwraca liczbę jako ciąg, który jest oparty na określonym formacie.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Numer*> | Yes | Całkowita lub podwójna | Wartość, którą chcesz sformatować. |
| <*Formacie*> | Yes | Ciąg | Ciąg formatu złożonego określający format, którego chcesz użyć. Dla obsługiwanych ciągów formatu numerycznego, zobacz [Standardowe ciągi formatu numerycznego](https://docs.microsoft.com/dotnet/standard/base-types/standard-numeric-format-strings), które są obsługiwane przez `number.ToString(<format>, <locale>)`. |
| <*Ustawień regionalnych*> | Nie | Ciąg | Ustawienia regionalne, które mają `number.ToString(<format>, <locale>)`być używane jako obsługiwane przez program . Jeśli nie zostanie określona, wartością domyślną jest `en-us`. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sformatowany numer*> | Ciąg | Określona liczba jako ciąg w określonym formacie. Tę wartość zwracaną można `int` `float`oddać na wartość lub . |
||||

*Przykład 1*

Załóżmy, że chcesz `1234567890`sformatować liczbę . W tym przykładzie jest to numer jako ciąg "1,234,567,890.00".

```
formatNumber(1234567890, '{0:0,0.00}', 'en-us')
```

*Przykład 2"

Załóżmy, że chcesz `1234567890`sformatować liczbę . W tym przykładzie sformatować liczbę do ciągu "1.234.567.890,00".

```
formatNumber(1234567890, '{0:0,0.00}', 'is-is')
```

*Przykład 3*

Załóżmy, że chcesz `17.35`sformatować liczbę . W tym przykładzie sformatować liczbę do ciągu "$17.35".

```
formatNumber(17.36, 'C2')
```

*Przykład 4*

Załóżmy, że chcesz `17.35`sformatować liczbę . W tym przykładzie sformatować liczbę do ciągu "17,35 kr".

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Zwraca bieżący sygnatura czasowa oraz określone jednostki czasu.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Interwał*> | Yes | Liczba całkowita | Liczba określonych jednostek czasu do dodania |
| <*jednostka czasowa*> | Yes | Ciąg | Jednostka czasu do użycia z *interwałem:*"Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Rok" |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Bieżący sygnatura czasowa plus określona liczba jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżący sygnatura czasowa to "2018-03-01T00:00:00.0000000Z".
W tym przykładzie dodaje pięć dni do tego sygnatury czasowe:

```
getFutureTime(5, 'Day')
```

I zwraca ten wynik:`"2018-03-06T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżący sygnatura czasowa to "2018-03-01T00:00:00.0000000Z".
W tym przykładzie dodaje pięć dni i konwertuje wynik do formatu "D":

```
getFutureTime(5, 'Day', 'D')
```

I zwraca ten wynik:`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Zwraca bieżący sygnatura czasowa minus określone jednostki czasu.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Interwał*> | Yes | Liczba całkowita | Liczba określonych jednostek czasu do odjęciu |
| <*jednostka czasowa*> | Yes | Ciąg | Jednostka czasu do użycia z *interwałem:*"Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Rok" |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Bieżący sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

Załóżmy, że bieżący sygnatura czasowa to "2018-02-01T00:00:00.0000000Z".
W tym przykładzie odejmuje pięć dni od tego sygnatury czasowych:

```
getPastTime(5, 'Day')
```

I zwraca ten wynik:`"2018-01-27T00:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że bieżący sygnatura czasowa to "2018-02-01T00:00:00.0000000Z".
W tym przykładzie odejmuje pięć dni i konwertuje wynik na format "D":

```
getPastTime(5, 'Day', 'D')
```

I zwraca ten wynik:`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Sprawdź, czy pierwsza wartość jest większa niż druga wartość.
Zwraca wartość true, gdy pierwsza wartość jest większa, lub zwraca wartość false, gdy mniej.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Całkowita, Float lub Ciąg | Pierwsza wartość, aby sprawdzić, czy większa niż druga wartość |
| <*Compareto*> | Yes | Odpowiednio całkowita, float lub string | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest większa niż druga wartość. Zwraca wartość false, gdy pierwsza wartość jest równa lub mniejsza niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzić, czy pierwsza wartość jest większa niż druga wartość:

```
greater(10, 5)
greater('apple', 'banana')
```

I zwrócić te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Sprawdź, czy pierwsza wartość jest większa lub równa drugiej wartości.
Zwraca wartość true, gdy pierwsza wartość jest większa lub równa, lub zwraca wartość false, gdy pierwsza wartość jest mniejsza.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Całkowita, Float lub Ciąg | Pierwsza wartość, aby sprawdzić, czy większa lub równa drugiej wartości |
| <*Compareto*> | Yes | Odpowiednio całkowita, float lub string | Wartość porównania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest większa lub równa drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest mniejsza niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzić, czy pierwsza wartość jest większa lub równa niż druga wartość:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

I zwrócić te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="guid"></a>

### <a name="guid"></a>guid

Generowanie unikatowego identyfikatora globalnego (GUID) jako ciągu, na przykład "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Ponadto można określić inny format identyfikatora GUID innego niż domyślny format "D", który jest 32 cyframi oddzielonymi myślnikami.

```
guid('<format>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Formacie*> | Nie | Ciąg | Specyfikator pojedynczego [formatu](https://msdn.microsoft.com/library/97af8hh4) zwracanego identyfikatora GUID. Domyślnie format to "D", ale można użyć "N", "D", "B", "P" lub "X". |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wartość GUID*> | Ciąg | Losowo generowany identyfikator GUID |
||||

*Przykład*

W tym przykładzie generuje ten sam identyfikator GUID, ale jako 32 cyfry, oddzielone łącznikami i ujęte w nawiasy:

```
guid('P')
```

I zwraca ten wynik:`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Sprawdź, czy wyrażenie jest prawdziwe czy fałszywe.
Na podstawie wyniku zwraca określoną wartość.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wyrażenie*> | Yes | Wartość logiczna | Wyrażenie do sprawdzenia |
| <*wartośćIfTrue*> | Yes | Dowolne | Wartość zwracana, gdy wyrażenie jest prawdziwe |
| <*wartośćIfFalse*> | Yes | Dowolne | Wartość zwracana, gdy wyrażenie jest fałszywe |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*określona wartość zwrotu*> | Dowolne | Określona wartość zwracana na podstawie tego, czy wyrażenie jest prawdziwe, czy fałszywe |
||||

*Przykład*

W tym `"yes"` przykładzie zwraca, ponieważ określone wyrażenie zwraca true.
W przeciwnym razie `"no"`w przykładzie zwraca:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>Indexof

Zwraca pozycję początkową lub wartość indeksu dla podciągów.
W tej funkcji nie jest rozróżniana wielkość liter, a indeksy zaczynają się od liczby 0.

```
indexOf('<text>', '<searchText>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Yes | Ciąg | Ciąg, który ma podciąg do znalezienia |
| <*tekst wyszukiwania*> | Yes | Ciąg | Podciąg, aby znaleźć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość indeksu*>| Liczba całkowita | Pozycja początkowa lub wartość indeksu dla określonego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, zwróć liczbę -1. |
||||

*Przykład*

W tym przykładzie znajduje wartość indeksu początkowego dla podciągu "świat" w ciągu "hello world":

```
indexOf('hello world', 'world')
```

I zwraca ten wynik:`6`

<a name="int"></a>

### <a name="int"></a>int

Zwraca wersję całkowitą dla ciągu.

```
int('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik liczby całkowitej*> | Liczba całkowita | Wersja całkowita dla określonego ciągu |
||||

*Przykład*

W tym przykładzie tworzy wersję całkowitą dla ciągu "10":

```
int('10')
```

I zwraca ten wynik:`10`

<a name="item"></a>

### <a name="item"></a>element

Gdy jest używana wewnątrz akcji powtarzania zaoferując tablicy, zwraca bieżący element w tablicy podczas bieżącej iteracji akcji.
Można również uzyskać wartości z właściwości tego elementu.

```
item()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*bieżący element tablicy*> | Dowolne | Bieżący element w tablicy bieżącej iteracji akcji |
||||

*Przykład*

W tym `body` przykładzie pobiera element z bieżącej wiadomości dla akcji "Send_an_email" wewnątrz bieżącej iteracji pętli dla każdego:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>Elementy

Zwraca bieżący element z każdego cyklu w pętli dla każdego.
Użyj tej funkcji wewnątrz pętli dla każdego.

```
items('<loopName>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*nazwa pętli*> | Yes | Ciąg | Nazwa dla każdej pętli |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Element*> | Dowolne | Element z bieżącego cyklu w określonej pętli dla każdego |
||||

*Przykład*

W tym przykładzie pobiera bieżący element z określonej dla każdej pętli:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iteracjaIndeksy

Zwraca wartość indeksu dla bieżącej iteracji wewnątrz Until pętli. Tej funkcji można użyć wewnątrz zagnieżdżonych pętli Until. 

```
iterationIndexes('<loopName>')
```

| Parametr | Wymagany | Typ | Opis | 
| --------- | -------- | ---- | ----------- | 
| <*nazwa pętli*> | Yes | Ciąg | Nazwa pętli Until | 
||||| 

| Wartość zwracana | Typ | Opis | 
| ------------ | ---- | ----------- | 
| <*Indeks*> | Liczba całkowita | Wartość indeksu bieżącej iteracji wewnątrz określonej pętli Until | 
|||| 

*Przykład* 

W tym przykładzie tworzy zmienną licznika i zwiększa, że zmienna o jeden podczas każdej iteracji w Until pętli, aż wartość licznika osiągnie pięć. W przykładzie tworzy również zmienną, która śledzi bieżący indeks dla każdej iteracji. W Until pętli podczas każdej iteracji przykładowy zwiększa licznik, a następnie przypisuje wartość licznika do bieżącej wartości indeksu, a następnie zwiększa licznik. Podczas gdy w pętli, w tym przykładzie odwołuje `iterationIndexes` się do bieżącego indeksu iteracji przy użyciu funkcji:

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

Zwraca wartość typu ISON (JavaScript Object Notation) lub obiekt dla ciągu lub pliku XML.

```
json('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg lub kod XML | Ciąg lub XML do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wynik JSON*> | Typ macierzysty JSON lub obiekt macierzysty | Wartość typu macierzystego JSON lub obiekt dla określonego ciągu lub XML. Jeśli ciąg ma wartość null, funkcja zwraca pusty obiekt. |
||||

*Przykład 1*

W tym przykładzie konwertuje ten ciąg na wartość JSON:

```
json('[1, 2, 3]')
```

I zwraca ten wynik:`[1, 2, 3]`

*Przykład 2*

W tym przykładzie konwertuje ten ciąg na JSON:

```
json('{"fullName": "Sophia Owen"}')
```

I zwraca ten wynik:

```
{
  "fullName": "Sophia Owen"
}
```

*Przykład 3*

W tym przykładzie konwertuje ten kod XML na JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

I zwraca ten wynik:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>Przecięcia

Zwraca kolekcję, która ma *tylko* wspólne elementy w określonych kolekcjach.
Aby pojawić się w wyniku, element musi pojawić się we wszystkich kolekcjach przekazanych do tej funkcji.
Jeśli jeden lub więcej elementów ma taką samą nazwę, ostatni element o tej nazwie pojawia się w wyniku.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*kolekcja1*>, *kolekcja <2*>, ... | Yes | Tablica lub obiekt, ale nie oba | Kolekcje, z których *chcesz, aby tylko* wspólne elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*przedmioty wspólne*> | Tablica lub obiekt, odpowiednio | Kolekcja, która ma tylko wspólne elementy w określonych kolekcjach |
||||

*Przykład*

W tym przykładzie znajduje wspólne elementy w tych tablicach:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

I zwraca tablicę z *tylko* tymi elementami:`[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Zwraca ciąg, który ma wszystkie elementy z tablicy i ma każdy znak oddzielony *ogranicznikiem*.

```
join([<collection>], '<delimiter>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Yes | Tablica | Tablica, która ma elementy do sprzężenia |
| <*Ogranicznik*> | Yes | Ciąg | Separator, który pojawia się między każdym znakiem w wynikowym ciągu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*char1*><*ogranicznik*><*char2*><*>...* | Ciąg | Wynikowy ciąg utworzony ze wszystkich elementów w określonej tablicy |
||||

*Przykład*

W tym przykładzie tworzy ciąg ze wszystkich elementów w tej tablicy z określonym znakiem jako ogranicznik:

```
join(createArray('a', 'b', 'c'), '.')
```

I zwraca ten wynik:`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>ostatni

Zwróć ostatni element z kolekcji.

```
last('<collection>')
last([<collection>])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Yes | Ciąg lub tablica | Kolekcja, w której można znaleźć ostatni element |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*ostatni element kolekcji*> | Ciąg lub tablica, odpowiednio | Ostatni element w kolekcji |
||||

*Przykład*

Te przykłady znaleźć ostatni element w tych kolekcjach:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

I zwraca te wyniki:

* Pierwszy przykład:`"d"`
* Drugi przykład:`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>Lastindexof

Zwraca pozycję początkową lub wartość indeksu dla ostatniego wystąpienia podciągów.
W tej funkcji nie jest rozróżniana wielkość liter, a indeksy zaczynają się od liczby 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Yes | Ciąg | Ciąg, który ma podciąg do znalezienia |
| <*tekst wyszukiwania*> | Yes | Ciąg | Podciąg, aby znaleźć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*końcowa wartość indeksu*> | Liczba całkowita | Pozycja początkowa lub wartość indeksu dla ostatniego wystąpienia określonego podciągu. <p>Jeśli ciąg nie zostanie znaleziony, zwróć liczbę -1. |
||||

*Przykład*

W tym przykładzie znajduje wartość indeksu początkowego dla ostatniego wystąpienia podciągu "świat" w ciągu "hello world":

```
lastIndexOf('hello world', 'world')
```

I zwraca ten wynik:`6`

<a name="length"></a>

### <a name="length"></a>length

Zwraca liczbę elementów w kolekcji.

```
length('<collection>')
length([<collection>])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Yes | Ciąg lub tablica | Kolekcja z elementami do zliczenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*długość lub liczba*> | Liczba całkowita | Liczba elementów w kolekcji |
||||

*Przykład*

W tych przykładach zlicza się liczbę elementów w tych kolekcjach:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

I zwrócić ten wynik:`4`

<a name="less"></a>

### <a name="less"></a>less

Sprawdź, czy pierwsza wartość jest mniejsza niż druga wartość.
Zwraca wartość true, gdy pierwsza wartość jest mniejsza lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Całkowita, Float lub Ciąg | Pierwsza wartość, aby sprawdzić, czy mniejsza niż druga wartość |
| <*Compareto*> | Yes | Odpowiednio całkowita, float lub string | Pozycja porównawcza |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest mniejsza niż druga wartość. Zwraca wartość false, gdy pierwsza wartość jest równa lub większa niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzić, czy pierwsza wartość jest mniejsza niż druga wartość.

```
less(5, 10)
less('banana', 'apple')
```

I zwrócić te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Sprawdź, czy pierwsza wartość jest mniejsza lub równa drugiej wartości.
Zwraca wartość true, gdy pierwsza wartość jest mniejsza lub równa, lub zwraca wartość false, gdy pierwsza wartość jest większa.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Całkowita, Float lub Ciąg | Pierwsza wartość, aby sprawdzić, czy jest mniejsza lub równa drugiej wartości |
| <*Compareto*> | Yes | Odpowiednio całkowita, float lub string | Pozycja porównawcza |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true, gdy pierwsza wartość jest mniejsza lub równa drugiej wartości. Zwraca wartość false, gdy pierwsza wartość jest większa niż druga wartość. |
||||

*Przykład*

Te przykłady sprawdzić, czy pierwsza wartość jest mniejsza lub równa niż druga wartość.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

I zwrócić te wyniki:

* Pierwszy przykład:`true`
* Drugi przykład:`false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listaCallbackUrl

Zwróć "adres URL wywołania zwrotnego", który wywołuje wyzwalacz lub akcję.
Ta funkcja działa tylko z wyzwalaczami i akcjami dla typów łączników **HttpWebhook** i **ApiConnectionWebhook,** ale nie **z typami Manual**, **Recurrence**, **HTTP**i **APIConnection.**

```
listCallbackUrl()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*adres URL wywołania zwrotnego*> | Ciąg | Adres URL wywołania zwrotnego dla wyzwalacza lub akcji |
||||

*Przykład*

W tym przykładzie pokazano przykładowy adres URL wywołania zwrotnego, który ta funkcja może zwrócić:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Zwraca najwyższą wartość z listy lub tablicy z liczbami, które są włącznie na obu końcach.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*numer1*>, <*numer2*>, ... | Yes | Całkowita, Float lub obie te te elementy | Zestaw liczb, z których ma być najwyższa wartość |
| [<numer *1*>, <numer *2*>,...] | Yes | Tablica — całkowita, float lub obie te 2 | Tablica liczb, z których ma być najwyższa wartość |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość maksymalna*> | Całkowita lub float | Najwyższa wartość w określonej tablicy lub zestawie liczb |
||||

*Przykład*

Te przykłady uzyskać najwyższą wartość z zestawu liczb i tablicy:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

I zwrócić ten wynik:`3`

<a name="min"></a>

### <a name="min"></a>min

Zwraca najniższą wartość z zestawu liczb lub tablicy.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*numer1*>, <*numer2*>, ... | Yes | Całkowita, Float lub obie te te elementy | Zestaw liczb, z których ma być najniższa wartość |
| [<numer *1*>, <numer *2*>,...] | Yes | Tablica — całkowita, float lub obie te 2 | Tablica liczb, z których ma być najniższa wartość |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość min.*> | Całkowita lub float | Najniższa wartość w określonym zestawie liczb lub określonej tablicy |
||||

*Przykład*

Te przykłady uzyskać najniższą wartość w zestawie liczb i tablicy:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

I zwrócić ten wynik:`1`

<a name="mod"></a>

### <a name="mod"></a>Mod

Zwróć pozostałą część z podzielenia dwóch liczb.
Aby uzyskać wynik liczby całkowitej, zobacz [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Dywidendy*> | Yes | Całkowita lub float | Liczba do podzielenia przez *dzielnik* |
| <*Dzielnik*> | Yes | Całkowita lub float | Liczba, która dzieli *dywidendę*, ale nie może być 0. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*modulo-wynik*> | Całkowita lub float | Pozostała część z podzielenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

W tym przykładzie dzieli pierwszą liczbę przez drugą liczbę:

```
mod(3, 2)
```

I zwrócić ten wynik:`1`

<a name="mul"></a>

### <a name="mul"></a>Mul

Zwróć produkt z pomnożenia dwóch liczb.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*wieloplikowy1*> | Yes | Całkowita lub float | Liczba do pomnożenia przez *multiplicand2* |
| <*multiplicand2*> | Yes | Całkowita lub float | Liczba wielokrotności *multiplicand1* |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik produktu*> | Całkowita lub float | Produkt z pomnożenia pierwszej liczby przez drugą liczbę |
||||

*Przykład*

W tych przykładach wielokrotność pierwszej liczby przez drugą liczbę:

```
mul(1, 2)
mul(1.5, 2)
```

I zwrócić te wyniki:

* Pierwszy przykład:`2`
* Drugi przykład`3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>wieloczęścioweBody

Zwraca obiekt dla określonej części w danych wyjściowych akcji, która ma wiele części.

```
multipartBody('<actionName>', <index>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*nazwa działania*> | Yes | Ciąg | Nazwa akcji, która ma dane wyjściowe z wieloma częściami |
| <*Indeks*> | Yes | Liczba całkowita | Wartość indeksu dla części, którą chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Ciała*> | Ciąg | Treść dla określonej części |
||||

<a name="not"></a>

### <a name="not"></a>not

Sprawdź, czy wyrażenie jest fałszywe.
Zwraca wartość true, gdy wyrażenie jest false lub zwraca false, gdy true.

```json
not(<expression>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wyrażenie*> | Yes | Wartość logiczna | Wyrażenie do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy wyrażenie jest false. Zwraca wartość false, gdy wyrażenie jest prawdziwe. |
||||

*Przykład 1*

Te przykłady sprawdzić, czy określone wyrażenia są fałszywe:

```json
not(false)
not(true)
```

I zwrócić te wyniki:

* Pierwszy przykład: Wyrażenie jest fałszywe, `true`więc funkcja zwraca .
* Drugi przykład: Wyrażenie jest prawdziwe, `false`więc funkcja zwraca .

*Przykład 2*

Te przykłady sprawdzić, czy określone wyrażenia są fałszywe:

```json
not(equals(1, 2))
not(equals(1, 1))
```

I zwrócić te wyniki:

* Pierwszy przykład: Wyrażenie jest fałszywe, `true`więc funkcja zwraca .
* Drugi przykład: Wyrażenie jest prawdziwe, `false`więc funkcja zwraca .

<a name="or"></a>

### <a name="or"></a>lub

Sprawdź, czy co najmniej jedno wyrażenie jest prawdziwe.
Zwraca wartość true, gdy co najmniej jedno wyrażenie jest prawdziwe, lub zwraca false, gdy wszystkie są false.

```
or(<expression1>, <expression2>, ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Yes | Wartość logiczna | Wyrażenia do sprawdzenia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false | Wartość logiczna | Zwraca wartość true, gdy co najmniej jedno wyrażenie jest prawdziwe. Zwraca wartość false, gdy wszystkie wyrażenia są fałszywe. |
||||

*Przykład 1*

Te przykłady sprawdzić, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(true, false)
or(false, false)
```

I zwrócić te wyniki:

* Pierwszy przykład: Co najmniej jedno wyrażenie jest `true`prawdziwe, więc funkcja zwraca .
* Drugi przykład: Oba wyrażenia są fałszywe, `false`więc funkcja zwraca .

*Przykład 2*

Te przykłady sprawdzić, czy co najmniej jedno wyrażenie jest prawdziwe:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

I zwrócić te wyniki:

* Pierwszy przykład: Co najmniej jedno wyrażenie jest `true`prawdziwe, więc funkcja zwraca .
* Drugi przykład: Oba wyrażenia są fałszywe, `false`więc funkcja zwraca .

<a name="outputs"></a>

### <a name="outputs"></a>Wyjść

Zwraca dane wyjściowe akcji w czasie wykonywania. Użyj tej funkcji, `actionOutputs()`a nie `outputs()` , który jest rozpoznawany w Logic App Designer. Chociaż obie funkcje działają `outputs()` w ten sam sposób, jest preferowany.

```
outputs('<actionName>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*nazwa działania*> | Yes | Ciąg | Nazwa żądanego wyjścia akcji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | -----| ----------- |
| <*Wyjście*> | Ciąg | Dane wyjściowe z określonej akcji |
||||

*Przykład*

W tym przykładzie pobiera `Get user`dane wyjściowe z akcji Twitter:

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

Zwraca wartość parametru opisanego w definicji przepływu pracy.

```
parameters('<parameterName>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Parametername*> | Yes | Ciąg | Nazwa parametru, którego wartość ma zostać |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość parametru*> | Dowolne | Wartość określonego parametru |
||||

*Przykład*

Załóżmy, że masz tę wartość JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

W tym przykładzie pobiera wartość dla określonego parametru:

```
parameters('fullName')
```

I zwraca ten wynik:`"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Zwraca losową liczę całkowitą z określonego zakresu, która jest włącznie tylko na końcu początkowym.

```
rand(<minValue>, <maxValue>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Minvalue*> | Yes | Liczba całkowita | Najniższa liczba całkowita w zakresie |
| <*Maxvalue*> | Yes | Liczba całkowita | Liczba całkowita, która następuje po najwyższej liczby całkowitej w zakresie, który funkcja może zwrócić |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik losowy*> | Liczba całkowita | Losowa liczba całkowita zwrócona z określonego zakresu |
||||

*Przykład*

W tym przykładzie pobiera losową liczbę całkowitą z określonego zakresu, z wyłączeniem wartości maksymalnej:

```
rand(1, 5)
```

I zwraca jedną z tych liczb `1` `2`w `3`wyniku: , , , lub`4`

<a name="range"></a>

### <a name="range"></a>range

Zwraca tablicę całkowitą, która rozpoczyna się od określonej liczby całkowitej.

```
range(<startIndex>, <count>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Startindex*> | Yes | Liczba całkowita | Wartość całkowita uruchamiana jako pierwszy element |
| <*Liczba*> | Yes | Liczba całkowita | Liczba liczb całkowitych w tablicy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<> *wynik zakresu]* | Tablica | Tablica z liczbami całkowitymi zaczyna się od określonego indeksu |
||||

*Przykład*

W tym przykładzie tworzy tablicę liczb całkowitych, która rozpoczyna się od określonego indeksu i ma określoną liczbę liczb całkowitych:

```
range(1, 4)
```

I zwraca ten wynik:`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>Zastąp

Zamień podciąg na określony ciąg i zwróć ciąg wynikowy. W tej funkcji rozróżniana jest wielkość liter.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Yes | Ciąg | Ciąg, który ma podciąg do zastąpienia |
| <*staryTekst*> | Yes | Ciąg | Podciąg do wymiany |
| <*nowy Tekst*> | Yes | Ciąg | Ciąg zastępczy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany tekst*> | Ciąg | Zaktualizowany ciąg po wymianie podciągów <p>Jeśli podciąg nie zostanie znaleziony, zwróć oryginalny ciąg. |
||||

*Przykład*

W tym przykładzie znajduje się podciąg "stary" w "starym ciągu" i zastępuje "stary" "nowy":

```
replace('the old string', 'old', 'new')
```

I zwraca ten wynik:`"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Usuń właściwość z obiektu i zwróć zaktualizowany obiekt. Jeśli właściwość, którą próbujesz usunąć, nie istnieje, funkcja zwraca oryginalny obiekt.

```
removeProperty(<object>, '<property>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Yes | Obiekt | Obiekt JSON, z którego chcesz usunąć właściwość |
| <*Właściwość*> | Yes | Ciąg | Nazwa właściwości do usunięcia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany obiekt*> | Obiekt | Zaktualizowany obiekt JSON bez określonej właściwości |
||||

Aby usunąć właściwość podrzędną z istniejącej właściwości, użyj tej składni:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Yes | Obiekt | Obiekt JSON, którego właściwość chcesz usunąć |
| <*własność nadrzędna*> | Yes | Ciąg | Nazwa właściwości nadrzędnej z właściwością podrzędną, którą chcesz usunąć |
| <*własność dziecka*> | Yes | Ciąg | Nazwa właściwości podrzędnej do usunięcia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany obiekt*> | Obiekt | Zaktualizowany obiekt JSON, którego właściwość podrzędna została usunięta |
||||

*Przykład 1*

W tym przykładzie usuwa `middleName` właściwość z obiektu JSON, który jest konwertowany z ciągu do JSON za pomocą funkcji [JSON()](#json) i zwraca zaktualizowany obiekt:

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

W tym przykładzie usuwa właściwość `middleName` podrzędną z właściwości nadrzędnej `customerName` w obiekcie JSON, która jest konwertowana z ciągu na JSON za pomocą funkcji [JSON()](#json) i zwraca zaktualizowany obiekt:

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

### <a name="result"></a>wynik

Zwraca dane wejściowe i wyjściowe ze wszystkich akcji, które znajdują `For_each`się `Until`wewnątrz `Scope` określonej akcji o określonym zakresie, takiej jak , lub akcja. Ta funkcja jest przydatna, zwracając wyniki z akcji nie powiodło się, dzięki czemu można zdiagnozować i obsługiwać wyjątki. Aby uzyskać więcej informacji, zobacz [Get context and results for failures](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*nazwa scopedActionName*> | Yes | Ciąg | Nazwa akcji o określonym zakresie, z której mają być zwracane dane wejściowe i wyjściowe ze wszystkich akcji wewnętrznych |
||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*obiekt tablicy*> | Obiekt tablicowy | Tablica zawierająca tablice danych wejściowych i wyjściowych z każdej akcji, która pojawia się wewnątrz określonej akcji o określonym zakresie |
||||

*Przykład*

W tym przykładzie zwraca dane wejściowe i wyjściowe z każdej iteracji akcji HTTP wewnątrz, która znajduje się wewnątrz `For_each` pętli przy użyciu `result()` funkcji w `Compose` akcji:

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

Oto jak przykład zwrócona tablica może `outputs` wyglądać, gdzie obiekt zewnętrzny zawiera dane wejściowe `For_each` i wyjściowe z każdej iteracji akcji wewnątrz akcji.

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

Ustaw wartość właściwości obiektu JSON i zwróć zaktualizowany obiekt. Jeśli właściwość, którą próbujesz ustawić, nie istnieje, właściwość zostanie dodana do obiektu. Aby dodać nową właściwość, użyj funkcji [addProperty().](#addProperty)

```
setProperty(<object>, '<property>', <value>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Yes | Obiekt | Obiekt JSON, którego właściwość chcesz ustawić |
| <*Właściwość*> | Yes | Ciąg | Nazwa istniejącej lub nowej właściwości do |
| <*Wartość*> | Yes | Dowolne | Wartość ustawiona dla określonej właściwości |
|||||

Aby ustawić właściwość podrzędną w obiekcie `setProperty()` podrzędnym, należy użyć zagnieżdżonego wywołania. W przeciwnym razie funkcja zwraca tylko obiekt podrzędny jako dane wyjściowe.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Obiektu*> | Yes | Obiekt | Obiekt JSON, którego właściwość chcesz ustawić |
| <*własność nadrzędna*> | Yes | Ciąg | Nazwa właściwości nadrzędnej z właściwością podrzędną, którą chcesz ustawić |
| <*własność dziecka*> | Yes | Ciąg | Nazwa właściwości podrzędnej do ustawionego |
| <*Wartość*> | Yes | Dowolne | Wartość ustawiona dla określonej właściwości |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany obiekt*> | Obiekt | Zaktualizowany obiekt JSON, którego właściwość została ustawiona |
||||

*Przykład 1*

W tym `surName` przykładzie ustawia właściwość w obiekcie JSON, który jest konwertowany z ciągu na JSON za pomocą funkcji [JSON().](#json) Funkcja przypisuje określoną wartość do właściwości i zwraca zaktualizowany obiekt:

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

W tym `surName` przykładzie ustawia `customerName` właściwość podrzędną dla właściwości nadrzędnej w obiekcie JSON, która jest konwertowana z ciągu na JSON przy użyciu funkcji [JSON().](#json) Funkcja przypisuje określoną wartość do właściwości i zwraca zaktualizowany obiekt:

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

Usuń elementy z przodu kolekcji i zwróć *wszystkie pozostałe* elementy.

```
skip([<collection>], <count>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Yes | Tablica | Kolekcja, której elementy chcesz usunąć |
| <*Liczba*> | Yes | Liczba całkowita | Dodatnia liczba całkowita dla liczby elementów do usunięcia z przodu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<> *zbierania aktualizacji]* | Tablica | Zaktualizowana kolekcja po usunięciu określonych elementów |
||||

*Przykład*

W tym przykładzie usuwa jeden element, numer 0, z przodu określonej tablicy:

```
skip(createArray(0, 1, 2, 3), 1)
```

I zwraca tę tablicę z pozostałymi elementami:`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Zwraca tablicę zawierającą podciągi, oddzielone przecinkami, na podstawie określonego znaku ogranicznika w oryginalnym ciągu.

```
split('<text>', '<delimiter>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Yes | Ciąg | Ciąg do oddzielenia na podciągi na podstawie określonego ogranicznika w oryginalnym ciągu |
| <*Ogranicznik*> | Yes | Ciąg | Znak w oryginalnym ciągu do użycia jako ogranicznik |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<*podciąg1*>,<*podciąg2*>,...] | Tablica | Tablica zawierająca podciągi z oryginalnego ciągu, oddzielona przecinkami |
||||

*Przykład*

W tym przykładzie tworzy tablicę z podciągami z określonego ciągu na podstawie określonego znaku jako ogranicznik:

```
split('a_b_c', '_')
```

I zwraca tę tablicę jako wynik:`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Zwraca początek dnia dla sygnatury czasowej.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Określony sygnatura czasowa, ale zaczynający się od znaku zero godziny dla dnia |
||||

*Przykład*

W tym przykładzie znajduje początek dnia dla tej sygnatury czasowej:

```
startOfDay('2018-03-15T13:30:30Z')
```

I zwraca ten wynik:`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Zwraca początek godziny dla sygnatury czasowej.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Określony sygnatura czasowa, ale zaczynający się od znaku zero-minute dla godziny |
||||

*Przykład*

W tym przykładzie znajduje początek godziny dla tej sygnatury czasowej:

```
startOfHour('2018-03-15T13:30:30Z')
```

I zwraca ten wynik:`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Zwraca początek miesiąca dla sygnatury czasowej.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Określony sygnatura czasowa, ale rozpoczynający się pierwszego dnia miesiąca w znaku zero godziny |
||||

*Przykład*

W tym przykładzie zwraca początek miesiąca dla tej sygnatury czasowej:

```
startOfMonth('2018-03-15T13:30:30Z')
```

I zwraca ten wynik:`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Sprawdź, czy ciąg zaczyna się od określonego podciągu.
Zwraca wartość true po znalezieniu podciągów lub zwraca wartość false, gdy nie zostanie znaleziony.
W tej funkcji nie jest rozróżniana wielkość liter.

```
startsWith('<text>', '<searchText>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Yes | Ciąg | Ciąg do sprawdzenia |
| <*tekst wyszukiwania*> | Yes | Ciąg | Ciąg początkowy do znalezienia |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| true lub false  | Wartość logiczna | Zwraca wartość true po znalezieniu podciągów początkowych. Zwraca wartość false, gdy nie znaleziono. |
||||

*Przykład 1*

W tym przykładzie sprawdza, czy ciąg "hello world" zaczyna się od podciągu "hello":

```
startsWith('hello world', 'hello')
```

I zwraca ten wynik:`true`

*Przykład 2*

W tym przykładzie sprawdza, czy ciąg "hello world" zaczyna się od podciągu "pozdrowienia":

```
startsWith('hello world', 'greetings')
```

I zwraca ten wynik:`false`

<a name="string"></a>

### <a name="string"></a>ciąg

Zwraca wersję ciągu dla wartości.

```
string(<value>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Dowolne | Wartość do przekonwertowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość ciągu*> | Ciąg | Wersja ciągu dla określonej wartości |
||||

*Przykład 1*

W tym przykładzie tworzy wersję ciągu dla tej liczby:

```
string(10)
```

I zwraca ten wynik:`"10"`

*Przykład 2*

W tym przykładzie tworzy ciąg dla określonego obiektu JSON\\i używa znaku ukośnika odwrotnego ( ) jako znaku ucieczki dla znaku podwójnego cudzysłowu (").

```
string( { "name": "Sophie Owen" } )
```

I zwraca ten wynik:`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>Sub

Zwraca wynik z odejmowania drugiej liczby od pierwszej liczby.

```
sub(<minuend>, <subtrahend>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Yes | Całkowita lub float | Liczba, od której można odjąć *odchylić odchylić odchylić* |
| <*Odjemnik*> | Yes | Całkowita lub float | Liczba do odjęcie od *minuend* |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Wynik*> | Całkowita lub float | Wynik odejmowania drugiego numeru od pierwszej liczby |
||||

*Przykład*

W tym przykładzie odejmuje drugą liczbę od pierwszej liczby:

```
sub(10.3, .3)
```

I zwraca ten wynik:`10`

<a name="substring"></a>

### <a name="substring"></a>Podciąg

Zwraca znaki z ciągu, zaczynając od określonej pozycji lub indeksu.
Wartości indeksu zaczynają się od liczby 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Yes | Ciąg | Ciąg, którego znaki chcesz |
| <*Startindex*> | Yes | Liczba całkowita | Liczba dodatnia równa lub większa niż 0, której chcesz użyć jako pozycji wyjściowej lub wartości indeksu |
| <*Długość*> | Yes | Liczba całkowita | Dodatnia liczba znaków, które mają być w podciąg |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wynik podciągowy*> | Ciąg | Podciąg z określoną liczbą znaków, zaczynając od określonej pozycji indeksu w ciągu źródłowym |
||||

*Przykład*

W tym przykładzie tworzy podciąg pięcioznakowy z określonego ciągu, zaczynając od wartości indeksu 6:

```
substring('hello world', 6, 5)
```

I zwraca ten wynik:`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>odejmowanieOdczasu

Odejmij liczbę jednostek czasu od sygnatury czasowej.
Zobacz też [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg zawierający sygnaturę czasowa |
| <*Interwał*> | Yes | Liczba całkowita | Liczba określonych jednostek czasu do odjęciu |
| <*jednostka czasowa*> | Yes | Ciąg | Jednostka czasu do użycia z *interwałem:*"Drugi", "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc", "Rok" |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zaktualizowany sygnatura czasowa*> | Ciąg | Sygnatura czasowa pomniejszona o określoną liczbę jednostek czasu |
||||

*Przykład 1*

W tym przykładzie odejmuje jeden dzień od tego sygnatury czasowej:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

I zwraca ten wynik:`"2018-01-01T00:00:00:0000000Z"`

*Przykład 2*

W tym przykładzie odejmuje jeden dzień od tego sygnatury czasowej:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

I zwraca ten wynik przy użyciu opcjonalnego formatu "D":`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>wziąć

Zwracaj przedmioty z przodu kolekcji.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Kolekcji*> | Yes | Ciąg lub tablica | Kolekcja, której elementy chcesz |
| <*Liczba*> | Yes | Liczba całkowita | Dodatnia liczba całkowita dla liczby elementów, które mają od przodu |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*> podzbioru* lub [<*podzbiór*>] | Ciąg lub tablica, odpowiednio | Ciąg lub tablica, która ma określoną liczbę elementów pobranych z przodu oryginalnej kolekcji |
||||

*Przykład*

Te przykłady uzyskać określoną liczbę elementów z przodu tych kolekcji:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

I zwrócić te wyniki:

* Pierwszy przykład:`"abc"`
* Drugi przykład:`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>Kleszcze

Zwraca `ticks` wartość właściwości dla określonej sygnatury czasowej.
*Znacznik* to 100-nanosekundowy interwał.

```
ticks('<timestamp>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Sygnatury czasowej*> | Yes | Ciąg | Ciąg sygnatury czasowej |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*liczba znaczników*> | Liczba całkowita | Liczba znaczników od określonej sygnatury czasowej |
||||

<a name="toLower"></a>

### <a name="tolower"></a>Tolower

Zwraca ciąg w formacie małych liter. Jeśli znak w ciągu nie ma wersji małych liter, ten znak pozostaje niezmieniony w zwracanym ciągu.

```
toLower('<text>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Yes | Ciąg | Ciąg do zwrócenia w formacie małych liter |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*tekst przy małych literach*> | Ciąg | Oryginalny ciąg w formacie małych liter |
||||

*Przykład*

W tym przykładzie konwertuje ten ciąg na małe litery:

```
toLower('Hello World')
```

I zwraca ten wynik:`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>Toupper

Zwraca ciąg wielkimi literami. Jeśli znak w ciągu nie ma wersji wielkich liter, ten znak pozostaje niezmieniony w zwróconym ciągu.

```
toUpper('<text>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Yes | Ciąg | Ciąg do zwrócenia w formacie wielkich liter |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*tekst wielkich liter*> | Ciąg | Oryginalny ciąg w formacie wielkich liter |
||||

*Przykład*

W tym przykładzie konwertuje ten ciąg na wielkie litery:

```
toUpper('Hello World')
```

I zwraca ten wynik:`"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>Wyzwalacz

Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazw i wartości JSON, które można przypisać do wyrażenia.

* Wewnątrz danych wejściowych wyzwalacza ta funkcja zwraca dane wyjściowe z poprzedniego wykonania.

* Wewnątrz warunku wyzwalacza ta funkcja zwraca dane wyjściowe z bieżącego wykonywania.

Domyślnie funkcja odwołuje się do całego obiektu wyzwalacza, ale opcjonalnie można określić właściwość, której wartość ma być.
Ponadto ta funkcja ma dostępne wersje skrócone, zobacz [triggerOutputs()](#triggerOutputs) i [triggerBody()](#triggerBody).

```
trigger()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wyjście wyzwalacza*> | Ciąg | Dane wyjściowe z wyzwalacza w czasie wykonywania |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody (Ciało wyzwalające)

Zwraca dane wyjściowe `body` wyzwalacza w czasie wykonywania.
Skrót do `trigger().outputs.body`.
Zobacz [trigger()](#trigger).

```
triggerBody()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wyzwalacz-wyjście ciała*> | Ciąg | Wyjście `body` z wyzwalacza |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Zwraca tablicę z wartościami, które pasują do nazwy klucza w danych *formularza* wyzwalacza lub danych *wyjściowych zakodowanych w formularzu.*

```
triggerFormDataMultiValues('<key>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Klucz*> | Yes | Ciąg | Nazwa klucza, którego wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| [<> *tablica z wartościami klucza]* | Tablica | Tablica ze wszystkimi wartościami zgodnymi z określonym kluczem |
||||

*Przykład*

W tym przykładzie tworzy tablicę z wartości klucza "feedUrl" w wyzwalaczu RSS danych formularza lub danych zakodowanych w formularzu:

```
triggerFormDataMultiValues('feedUrl')
```

I zwraca tę tablicę jako przykładowy wynik:`["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Zwraca ciąg z pojedynczą wartością, która pasuje do nazwy klucza w *danych formularza* wyzwalacza lub danych *wyjściowych zakodowanych w formularzu.*
Jeśli funkcja znajdzie więcej niż jedno dopasowanie, funkcja zgłasza błąd.

```
triggerFormDataValue('<key>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Klucz*> | Yes | Ciąg | Nazwa klucza, którego wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość klucza*> | Ciąg | Wartość w określonym kluczu |
||||

*Przykład*

W tym przykładzie tworzy ciąg z wartości klucza "feedUrl" w wyzwalaczu RSS danych formularza lub danych zakodowanych w formularzu:

```
triggerFormDataValue('feedUrl')
```

I zwraca ten ciąg jako przykładowy wynik:`"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Zwraca obiekt dla określonej części w danych wyjściowych wyzwalacza, który ma wiele części.

```
triggerMultipartBody(<index>)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Indeks*> | Yes | Liczba całkowita | Wartość indeksu dla części, którą chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*Ciała*> | Ciąg | Treść określonej części w wieloczęściowym wyjściu wyzwalacza |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Zwraca dane wyjściowe wyzwalacza w czasie wykonywania lub wartości z innych par nazw i wartości JSON.
Skrót do `trigger().outputs`.
Zobacz [trigger()](#trigger).

```
triggerOutputs()
```

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wyjście wyzwalacza*> | Ciąg | Dane wyjściowe z wyzwalacza w czasie wykonywania  |
||||

<a name="trim"></a>

### <a name="trim"></a>Przycinanie

Usuń interweniujący i końcowy biały znak z ciągu i zwraca zaktualizowany ciąg.

```
trim('<text>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Tekst*> | Yes | Ciąg | Ciąg, który ma interweniujący i końcowy biały, aby usunąć |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*updatedText*> | Ciąg | Zaktualizowana wersja oryginalnego ciągu bez interlinia lub końcowego odstępu |
||||

*Przykład*

W tym przykładzie usuwa interweniujący i końcowy biały z ciągu " Hello World ":

```
trim(' Hello World  ')
```

I zwraca ten wynik:`"Hello World"`

<a name="union"></a>

### <a name="union"></a>unia

Zwraca kolekcję, która ma *wszystkie* elementy z określonych kolekcji.
Aby pojawić się w wyniku, element może pojawić się w dowolnej kolekcji przekazane do tej funkcji. Jeśli jeden lub więcej elementów ma taką samą nazwę, ostatni element o tej nazwie pojawia się w wyniku.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*kolekcja1*>, *kolekcja <2*>, ...  | Yes | Tablica lub obiekt, ale nie oba | Kolekcje, z których mają być *wszystkie* elementy |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*aktualizacjaCollection*> | Tablica lub obiekt, odpowiednio | Kolekcja ze wszystkimi elementami z określonych kolekcji — bez duplikatów |
||||

*Przykład*

W tym przykładzie pobiera *wszystkie* elementy z tych kolekcji:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

I zwraca ten wynik:`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Zwraca wersję zakodowaną w jednolitym identyfikatorze zasobu (URI) dla ciągu, zastępując znaki niebezpieczne dla adresów URL znakami ucieczki.
Użyj tej funkcji, a nie [encodeUriComponent()](#encodeUriComponent).
Chociaż obie funkcje działają `uriComponent()` w ten sam sposób, jest preferowany.

```
uriComponent('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg do konwersji do formatu zakodowanego na URI |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*zakodowane-uri*> | Ciąg | Ciąg zakodowany w kodach URI ze znakami ucieczki |
||||

*Przykład*

W tym przykładzie tworzy wersję zakodowaną w identyfikatorze URI dla tego ciągu:

```
uriComponent('https://contoso.com')
```

I zwraca ten wynik:`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Zwraca wersję binarną dla składnika jednolitego identyfikatora zasobu (URI).

```
uriComponentToBinary('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg zakodowany w kodowaniu URI do konwersji |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*bin-for-encoded-uri*> | Ciąg | Wersja binarna dla ciągu zakodowanego zgodnie z identyfikatorem URI. Zawartość binarna jest zakodowana w formacie `$content`base64 i reprezentowana przez program . |
||||

*Przykład*

W tym przykładzie tworzy wersję binarną dla tego ciągu zakodowanego na identyfikatorze URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Zwraca wersję ciągu dla jednolitego identyfikatora zasobu (URI) zakodowanego ciągu, skutecznie dekodując ciąg zakodowany w identyfikatorze URI.

```
uriComponentToString('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg zakodowany w kodowanie identyfikatorów URI do dekodowania |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*dekodowane-uri*> | Ciąg | Zdekodowana wersja dla ciągu zakodowanego zgodnie z identyfikatorem URI |
||||

*Przykład*

W tym przykładzie tworzy zdekodowaną wersję ciągu dla tego ciągu zakodowanego w uisięcowym:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

I zwraca ten wynik:`"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Zwraca `host` wartość jednolitego identyfikatora zasobu (URI).

```
uriHost('<uri>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Yes | Ciąg | Identyfikator URI, którego `host` wartość ma zostać |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość hosta*> | Ciąg | Wartość `host` określonego identyfikatora URI |
||||

*Przykład*

W tym `host` przykładzie znajduje wartość dla tego identyfikatora URI:

```
uriHost('https://www.localhost.com:8080')
```

I zwraca ten wynik:`"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath (ścieżka uripath)

Zwraca `path` wartość jednolitego identyfikatora zasobu (URI).

```
uriPath('<uri>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Yes | Ciąg | Identyfikator URI, którego `path` wartość ma zostać |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość ścieżki*> | Ciąg | Wartość `path` dla określonego identyfikatora URI. Jeśli `path` nie ma wartości, zwróć znak "/". |
||||

*Przykład*

W tym `path` przykładzie znajduje wartość dla tego identyfikatora URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik:`"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery (uriPathAndQuery)

Zwraca `path` wartości `query` i dla jednolitego identyfikatora zasobu (URI).

```
uriPathAndQuery('<uri>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Yes | Ciąg | Identyfikator URI, którego `path` i `query` wartości chcesz |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość-zapytanie ścieżki*> | Ciąg | I `path` `query` wartości dla określonego identyfikatora URI. Jeśli `path` nie określi wartości, zwróć znak "/". |
||||

*Przykład*

W tym `path` przykładzie znajduje się i `query` wartości dla tego identyfikatora URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik:`"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort (port uriport)

Zwraca `port` wartość jednolitego identyfikatora zasobu (URI).

```
uriPort('<uri>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Yes | Ciąg | Identyfikator URI, którego `port` wartość ma zostać |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość portu*> | Liczba całkowita | Wartość `port` dla określonego identyfikatora URI. Jeśli `port` nie określi wartości, zwróć domyślny port protokołu. |
||||

*Przykład*

W tym `port` przykładzie zwraca wartość dla tego identyfikatora URI:

```
uriPort('http://www.localhost:8080')
```

I zwraca ten wynik:`8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Zwraca `query` wartość jednolitego identyfikatora zasobu (URI).

```
uriQuery('<uri>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Yes | Ciąg | Identyfikator URI, którego `query` wartość ma zostać |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość zapytania*> | Ciąg | Wartość `query` określonego identyfikatora URI |
||||

*Przykład*

W tym `query` przykładzie zwraca wartość dla tego identyfikatora URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik:`"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Zwraca `scheme` wartość jednolitego identyfikatora zasobu (URI).

```
uriScheme('<uri>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Identyfikator uri*> | Yes | Ciąg | Identyfikator URI, którego `scheme` wartość ma zostać |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość schematu*> | Ciąg | Wartość `scheme` określonego identyfikatora URI |
||||

*Przykład*

W tym `scheme` przykładzie zwraca wartość dla tego identyfikatora URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

I zwraca ten wynik:`"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>Utcnow

Zwraca bieżącą sygnaturę czasną.

```
utcNow('<format>')
```

Opcjonalnie można określić inny format za pomocą parametru> *formatu* <.


| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Formacie*> | Nie | Ciąg | Specyfikator [pojedynczego formatu](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) lub [wzorzec formatu niestandardowego](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Domyślnym formatem sygnatury czasowej jest ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK), który jest zgodny z [iso 8601](https://en.wikipedia.org/wiki/ISO_8601) i zachowuje informacje o strefie czasowej. |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*sygnatura bieżącego czasu*> | Ciąg | Bieżąca data i godzina |
||||

*Przykład 1*

Załóżmy, że dzisiaj jest 15 kwietnia 2018 o 13:00:00.
W tym przykładzie pobiera bieżącą sygnaturę czasą:

```
utcNow()
```

I zwraca ten wynik:`"2018-04-15T13:00:00.0000000Z"`

*Przykład 2*

Załóżmy, że dzisiaj jest 15 kwietnia 2018 o 13:00:00.
W tym przykładzie pobiera bieżący znacznik czasu przy użyciu opcjonalnego formatu "D":

```
utcNow('D')
```

I zwraca ten wynik:`"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>Zmiennych

Zwraca wartość dla określonej zmiennej.

```
variables('<variableName>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Variablename*> | Yes | Ciąg | Nazwa zmiennej, której wartość ma być |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wartość zmienna*> | Dowolne | Wartość dla określonej zmiennej |
||||

*Przykład*

Załóżmy, że bieżąca wartość zmiennej "numItems" wynosi 20.
W tym przykładzie pobiera wartość całkowitą dla tej zmiennej:

```
variables('numItems')
```

I zwraca ten wynik:`20`

<a name="workflow"></a>

### <a name="workflow"></a>przepływ pracy

Zwraca wszystkie szczegóły dotyczące samego przepływu pracy w czasie wykonywania.

```
workflow().<property>
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Właściwość*> | Nie | Ciąg | Nazwa właściwości przepływu pracy, której wartość ma być <p>Obiekt przepływu pracy ma następujące właściwości: **nazwa**, **typ**, **id**, **lokalizacja**i **uruchom**. Wartość właściwości **run** jest również obiektem o następujących właściwościach: **name**, **type**i **id**. |
|||||

*Przykład*

W tym przykładzie zwraca nazwę bieżącego przebiegu przepływu pracy:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Zwraca wersję XML dla ciągu zawierającego obiekt JSON.

```
xml('<value>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Wartość*> | Yes | Ciąg | Ciąg z obiektem JSON do konwersji <p>Obiekt JSON musi mieć tylko jedną właściwość główną, która nie może być tablicą. <br>Użyj znaku ukośnika odwrotnego (\\) jako znaku ucieczki dla znaku podwójnego cudzysłowu ("). |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*wersja xml*> | Obiekt | Zakodowany kod XML dla określonego ciągu lub obiektu JSON |
||||

*Przykład 1*

W tym przykładzie tworzy wersję XML dla tego ciągu, który zawiera obiekt JSON:

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

W tym przykładzie tworzy XML dla ciągu, który zawiera ten obiekt JSON:

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

Sprawdź xml dla węzłów lub wartości, które pasują do wyrażenia XPath (XML Path Language) i zwraca pasujące węzły lub wartości. Wyrażenie XPath lub po prostu "XPath" ułatwia poruszanie się po strukturze dokumentu XML, dzięki czemu można wybrać węzły lub wartości obliczeniowe w zawartości XML.

```
xpath('<xml>', '<xpath>')
```

| Parametr | Wymagany | Typ | Opis |
| --------- | -------- | ---- | ----------- |
| <*Xml*> | Yes | Dowolne | Ciąg XML do wyszukiwania węzłów lub wartości zgodnych z wartością wyrażenia XPath |
| <*Xpath*> | Yes | Dowolne | Wyrażenie XPath używane do znajdowania pasujących węzłów lub wartości XML |
|||||

| Wartość zwracana | Typ | Opis |
| ------------ | ---- | ----------- |
| <*xml-węzeł*> | XML | Węzeł XML, gdy tylko jeden węzeł pasuje do określonego wyrażenia XPath |
| <*Wartość*> | Dowolne | Wartość z węzła XML, gdy tylko pojedyncza wartość jest zgodna z określonym wyrażeniem XPath |
| [<> *xml-node1,* <> *xml-node2,* ...] </br>— lub — </br>[<*wartość1*>, <*wartość2*>, ...] | Tablica | Tablica z węzłami lub wartościami XML zgodnymi z określonym wyrażeniem XPath |
||||

*Przykład 1*

W tym przykładzie znajduje `<name></name>` się węzły, które pasują do węzła w określonych argumentów i zwraca tablicę z tymi wartościami węzła:

`xpath(xml(parameters('items')), '/produce/item/name')`

Oto argumenty:

* Ciąg "items", który zawiera ten kod XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  W przykładzie użyto funkcji [parameters(),](#parameters) aby uzyskać ciąg XML z argumentu "items", ale musi również przekonwertować ciąg na format XML za pomocą funkcji [xml().](#xml)

* To wyrażenie XPath, które jest przekazywane jako ciąg:

  `"/produce/item/name"`

Oto tablica wyników z węzłami, które pasują `<name></name`do:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Przykład 2*

W poniższym przykładzie 1 w tym `<count></count>` przykładzie znajduje się `sum()` węzły, które pasują do węzła i dodaje te wartości węzła z funkcją:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

I zwraca ten wynik:`30`

*Przykład 3*

W tym przykładzie oba wyrażenia znajdują `<location></location>` węzły, które pasują do węzła, w określonych argumentach, które obejmują XML z obszarem nazw. 

> [!NOTE]
>
> Jeśli pracujesz w widoku kodu, wyjmij podwójny cudzysłów\\(") za pomocą znaku ukośnika odwrotnego ( ). 
> Na przykład należy użyć znaków ucieczki podczas serializacji wyrażenia jako ciąg JSON. 
> Jednak jeśli pracujesz w logic app designer lub edytora wyrażeń, nie trzeba uniknąć podwójnego cudzysłowu, ponieważ znak ukośnika odwrotnego jest dodawany automatycznie do podstawowej definicji, na przykład:
> 
> * Widok kodu:`xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Edytor wyrażeń:`xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
> 
> Poniższe przykłady dotyczą wyrażeń wprowadzonych w edytorze wyrażeń.

* *Wyrażenie 1*

  `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

* *Wyrażenie 2*

  `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

Oto argumenty:

* Ten kod XML, który obejmuje obszar `xmlns="http://contoso.com"`nazw dokumentu XML, :

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Albo wyrażenie XPath w tym miejscu:

  * `/*[name()="file"]/*[name()="location"]`

  * `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]`

Oto węzeł wynikowy, `<location></location>` który pasuje do węzła:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Przykład 4*

Poniżej przykładu 3 w tym `<location></location>` przykładzie znajduje wartość w węźle:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

I zwraca ten wynik:`"Paris"`

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [języku definicji przepływu pracy](../logic-apps/logic-apps-workflow-definition-language.md)
