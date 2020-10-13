---
title: Intencje i jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Pojedynczy cel reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub zamierzenie wyrażone w wypowiedzi użytkownika. Zdefiniuj zestaw intencji, który odnosi się do akcji, które użytkownicy chcą wykonać w aplikacji.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: cf138248e878b21531df2035dfeda1b90162ea99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536107"
---
# <a name="intents-in-your-luis-app"></a>Intencje w aplikacji LUIS

Cel reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub cel wyrażony w [wypowiedź](luis-concept-utterance.md)użytkownika.

Zdefiniuj zestaw intencji, który odnosi się do akcji, które użytkownicy chcą wykonać w aplikacji. Na przykład aplikacja do podróży definiuje kilka intencji:

Intencje dla aplikacji podróży   |   Przykładowe wypowiedzi   |
------|------|
 BookFlight     |   "Zarezerwuj do mnie następny tydzień" <br/> "Przylot mnie do Rio na 24" <br/> "Muszę mieć bilet z płaszczyzną w następnej niedzielę do Rio de Janeiro"    |
 Powitanie     |   „Hi” (Cześć) <br/>"Hello" <br/>„Good morning” (Dzień dobry)  |
 CheckWeather (Sprawdzanie pogody) | "Co to jest pogoda, jak w Boston?" <br/> "Pokaż mi prognozę dla tego weekendu" |
 Brak         | "Pobierz przepis cookie"<br>"Czy udało Ci się wygrać?" |

Wszystkie aplikacje są dostarczane ze wstępnie zdefiniowanym zamiarem "[none](#none-intent)", który jest zamiarem alternatywnym.

## <a name="prebuilt-domains-provide-intents"></a>Wbudowane domeny zapewniają intencje
Oprócz elementów docelowych, które można zdefiniować, można użyć prebudowanych intencji z jednej z [prekompilowanych domen](luis-how-to-use-prebuilt-domains.md).

## <a name="return-all-intents-scores"></a>Zwróć wszystkie wyniki intencji
Do jednego celu przypiszesz wypowiedź. Gdy LUIS odbiera wypowiedź w punkcie końcowym, domyślnie zwraca najwyższy zamiar dla tego wypowiedź.

Jeśli chcesz uzyskać wyniki dla wszystkich intencji dla wypowiedź, możesz podać flagę w ciągu zapytania w interfejsie API przewidywania.

|Wersja interfejsu API przewidywania|Flaga|
|--|--|
|Wersja 2|`verbose=true`|
|Czytanie|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Cel w porównaniu do jednostki
Cel reprezentuje akcję, którą aplikacja powinna wykonać dla użytkownika i opiera się na całej wypowiedź. Wypowiedź może mieć tylko jeden górny cel oceniania, ale może mieć wiele jednostek.

<a name="how-do-intents-relate-to-entities"></a>

Utwórz cel, gdy _zamiaru_ użytkownika wywoła akcję w aplikacji klienckiej, taką jak wywołanie funkcji checkweather (). Następnie Utwórz jednostki do reprezentowania parametrów wymaganych do wykonania akcji.

|Zamiar   | Jednostka | Przykładowa wypowiedź   |
|------------------|------------------------------|------------------------------|
| CheckWeather (Sprawdzanie pogody) | {"Type": "Location", "Entity": "Seattle"}<br>{"Type": "wbudowane. datetimeV2. Date", "Entity": "jutro", "Solution": "2018 r-05-23"} | Jak wygląda Pogoda `Seattle` `tomorrow` ? |
| CheckWeather (Sprawdzanie pogody) | {"Type": "date_range", "Entity": "weekend"} | Pokaż mi prognozę dla `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Prebudowane intencje domeny

[Prebudowane domeny](luis-how-to-use-prebuilt-domains.md) zapewniają intencje wyrażenia długości.

## <a name="none-intent"></a>Intencja None

Opcja **Brak** jest tworzona, ale pozostaje pusta w celu. Intencją **none** nie jest wymagane i nie można jej usunąć ani zmienić jej nazwy. Wypełnij ją wypowiedziami, które znajdują się poza Twoją domeną.

Opcja **Brak** jest zamiarem rezerwowym, istotnym dla każdej aplikacji i powinna mieć 10% całkowitej wyrażenia długości. Jest on używany do uczenia LUIS wyrażenia długości, które nie są ważne w domenie aplikacji (obszar tematu). Jeśli nie dodasz żadnych wyrażenia długości dla zamiaru **Brak** , Luis wymusza wypowiedź, który znajduje się poza domeną, do jednej z intencji domeny. Spowoduje to pochylenie wyników przewidywania przez uczenie LUIS niewłaściwego zamiaru wypowiedź.

Gdy wypowiedź jest przewidywany jako cel, aplikacja kliencka może zadawać więcej pytań lub podać menu, aby skierować użytkownika do prawidłowych opcji.

## <a name="negative-intentions"></a>Ujemne zamiary
Jeśli chcesz określić ujemne i pozytywne zamiary, na przykład **"chcę mieć** samochód" i " **nie** chcę, aby samochód", możesz utworzyć dwa intencje (jedną wartość dodatnią i jedną ujemną) i dodać odpowiednie wyrażenia długości dla każdego z nich. Można też utworzyć pojedynczy cel i oznaczyć dwa różne wartości dodatnie i ujemne jako jednostki.

## <a name="intents-and-patterns"></a>Intencje i wzorce

Jeśli masz przykład wyrażenia długości, który można zdefiniować w części lub całości jako wyrażenie regularne, rozważ użycie [jednostki wyrażenia regularnego](luis-concept-entity-types.md#regular-expression-entity) sparowanego ze [wzorcem](luis-concept-patterns.md).

Użycie jednostki wyrażenia regularnego gwarantuje ekstrakcję danych, tak aby wzorzec był dopasowany. Dopasowywanie do wzorca gwarantuje dokładne przeznaczenie.

## <a name="intent-balance"></a>Saldo intencji
Intencje domeny aplikacji powinny mieć saldo wyrażenia długości dla każdego zamiaru. Nie masz jednego z zamiaru o 10 wyrażenia długości i innych zamiarach z 500 wyrażenia długości. Nie jest to zrównoważone. Jeśli masz tę sytuację, zapoznaj się z zamiarem 500 wyrażenia długości, aby sprawdzić, czy wiele zamiar można zreorganizować do [wzorca](luis-concept-patterns.md).

Zamiara **Brak** nie jest uwzględniona w saldzie. Ta metoda powinna zawierać 10% łącznej liczby wyrażenia długości w aplikacji.

## <a name="intent-limits"></a>Limity intencji
Przejrzyj [limity](luis-limits.md#model-boundaries) , aby zrozumieć, ile intencji można dodać do modelu.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Jeśli potrzebujesz więcej niż maksymalnej liczby intencji
Najpierw należy rozważyć, czy system używa zbyt wielu intencji.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Można łączyć wiele założeń w jeden cel z jednostkami
Zamiary, które są zbyt podobne, mogą utrudniać LUIS. Intencje powinny być wystarczająco zróżnicowane, aby przechwytywać główne zadania, dla których użytkownik żąda, ale nie muszą przechwytywać wszystkich ścieżek wykonywanych w kodzie. Na przykład BookFlight i FlightCustomerService mogą być oddzielnymi intencjami w aplikacji podróży, ale BookInternationalFlight i BookDomesticFlight są zbyt podobne. Jeśli system musi je odróżnić, należy użyć jednostek lub innych logiki zamiast intencji.

### <a name="dispatcher-model"></a>Model dyspozytora
Dowiedz się więcej na temat łączenia aplikacji LUIS i QnA Maker z [modelem wysyłania](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps).

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Zażądaj pomocy dla aplikacji o znaczącej liczbie zamiarów
W przypadku zmniejszenia liczby intencji lub podzielenia intencji w wielu aplikacjach nie będzie to możliwe, skontaktuj się z pomocą techniczną. Jeśli Twoja subskrypcja platformy Azure obejmuje usługi pomocy technicznej, skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [jednostkach](luis-concept-entity-types.md), które są ważnymi wyrazami istotnymi dla intencji
* Dowiedz się [, jak dodawać intencje i zarządzać nimi](luis-how-to-add-intents.md) w aplikacji Luis.
* Zapoznaj się z [najlepszymi rozwiązaniami](luis-concept-best-practices.md) założeń
