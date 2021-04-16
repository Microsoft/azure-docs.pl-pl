---
title: Intencje i jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Pojedyncza intencja reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub zamierzenie wyrażone w wypowiedzi użytkownika. Zdefiniuj zestaw intencji odpowiadający czynnościom, które użytkownicy chcą podjąć w aplikacji.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 8e76e3e7683d43a7a39bc0c168a29016a988c705
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499414"
---
# <a name="intents-in-your-luis-app"></a>Intencje w aplikacji usługi LUIS

Intencja reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub cel wyrażony w wypowiedzi [użytkownika.](luis-concept-utterance.md)

Zdefiniuj zestaw intencji odpowiadający czynnościom, które użytkownicy chcą podjąć w aplikacji. Na przykład aplikacja do podróży definiuje kilka intencji:

Intencje aplikacji travel   |   Przykładowe wypowiedzi   |
------|------|
 BookFlight     |   "Book me a flight to Rio next week" (Zarezerwuj mi lot do Rio w następnym tygodniu) <br/> "Fly me to Rio on the 24th" (Poleć mnie do Rio na 24. <br/> "Potrzebuję biletu płaszczyzny w najbliższą niedzielę do Rio de Dob"    |
 Powitanie     |   „Hi” (Cześć) <br/>"Hello" <br/>„Good morning” (Dzień dobry)  |
 CheckWeather (Sprawdzanie pogody) | "What's the weather like in Boston?" (Jaka jest pogoda w Bostonie?) <br/> "Show me the forecast for this weekend" (Pokaż mi prognozę na ten weekend) |
 Brak         | "Get me a cookie recipe" (Pobierz mi przepis na plik cookie)<br>"Czy lakers wygrali?" |

Wszystkie aplikacje mają wstępnie zdefiniowaną intencję "[None",](#none-intent)która jest intencją rezerwy.

## <a name="prebuilt-domains-provide-intents"></a>Wstępnie utworzone domeny zapewniają intencje
Oprócz intencji, które definiujesz, możesz użyć wstępnie utworzonych intencji z jednej ze [wstępnie utworzonych domen](./howto-add-prebuilt-models.md).

## <a name="return-all-intents-scores"></a>Zwracanie wyników wszystkich intencji
Przypisujesz wypowiedź do jednej intencji. Gdy usługa LUIS odbiera wypowiedź w punkcie końcowym, domyślnie zwraca intencję o najwyższej wartości dla tej wypowiedzi.

Jeśli chcesz uzyskać wyniki dla wszystkich intencji dla wypowiedzi, możesz podać flagę w ciągu zapytania interfejsu API przewidywania.

|Wersja interfejsu API przewidywania|Flaga|
|--|--|
|Wersja 2|`verbose=true`|
|Wersja 3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Intencja w porównaniu z jednostką
Intencja reprezentuje akcję, która aplikacja powinna podjąć dla użytkownika i jest oparta na całej wypowiedzi. Wypowiedź może mieć tylko jedną intencję o najwyższej oceny, ale może mieć wiele jednostek.

<a name="how-do-intents-relate-to-entities"></a>

Utwórz intencję, gdy  intencja użytkownika wyzwoli akcję w aplikacji klienckiej, na przykład wywołanie funkcji checkweather(). Następnie utwórz jednostki reprezentujące parametry wymagane do wykonania akcji.

|Zamiar   | Jednostka | Przykładowa wypowiedź   |
|------------------|------------------------------|------------------------------|
| CheckWeather (Sprawdzanie pogody) | { "type": "location", "entity": "Seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Jaka jest pogoda w `Seattle` `tomorrow` ? |
| CheckWeather (Sprawdzanie pogody) | { "type": "date_range", "entity": "this weekend" } | Pokaż mi prognozę dla `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Wstępnie utworzone intencje domeny

[Wstępnie utworzone domeny zapewniają](./howto-add-prebuilt-models.md) intencje z wypowiedziami.

## <a name="none-intent"></a>Intencja None

Intencja **None** jest tworzona, ale celowo pozostaje pusta. Intencja **None** jest wymaganą intencją i nie można jej usunąć ani zmienić jej nazwy. Wypełnij ją wypowiedziami, które znajdują się poza Twoją domeną.

Intencja **None** jest intencją rezerwy, ważną w każdej aplikacji i powinna mieć 10% łącznej liczby wypowiedzi. Służy do uczenia wypowiedzi usługi LUIS, które nie są ważne w domenie aplikacji (obszarze tematu). Jeśli nie dodasz żadnych wypowiedzi dla intencji **None,** usługa LUIS wymusza wypowiedź, która znajduje się poza domeną, do jednej z intencji domeny. Spowoduje to skośność wyników przewidywania przez uczenie usługi LUIS niewłaściwej intencji wypowiedzi.

Gdy wypowiedź jest przewidywana jako intencja None, aplikacja klienca może zadawać więcej pytań lub udostępnić menu, aby skierować użytkownika do prawidłowych wyborów.

## <a name="negative-intentions"></a>Intencje negatywne
Jeśli chcesz określić intencje negatywne i pozytywne,  takie jak "Chcę  samochód" i "Nie chcę samochodu", możesz utworzyć dwie intencje (jedną pozytywną i jedną negatywną) i dodać odpowiednie wypowiedzi dla każdej z nich. Możesz też utworzyć jedną intencję i oznaczyć dwa różne pozytywne i negatywne terminy jako jednostkę.

## <a name="intents-and-patterns"></a>Intencje i wzorce

Jeśli masz przykładowe wypowiedzi, które mogą być definiowane jako część lub [](luis-concept-entity-types.md#regex-entity) całość jako wyrażenie regularne, rozważ użycie jednostki wyrażenia regularnego sparowanego ze [wzorcem](luis-concept-patterns.md).

Użycie jednostki wyrażenia regularnego gwarantuje wyodrębnianie danych w taki sposób, aby wzorzec został do siebie dopasowany. Dopasowanie wzorca gwarantuje zwrócenie dokładnej intencji.

## <a name="intent-balance"></a>Saldo intencji
Intencje domeny aplikacji powinny mieć równowagę między wypowiedziami w każdej intencji. Nie masz jednej intencji z 10 wypowiedziami i innej intencji z 500 wypowiedziami. Nie jest to zrównoważone. Jeśli masz taką sytuację, przejrzyj intencję z 500 wypowiedziami, aby sprawdzić, czy wiele intencji można zreorganizować w [taki wzorzec.](luis-concept-patterns.md)

Intencja **None** nie jest uwzględniona w saldie. Ta intencja powinna zawierać 10% łącznej liczby wypowiedzi w aplikacji.

## <a name="intent-limits"></a>Limity intencji
Przejrzyj [limity,](luis-limits.md#model-boundaries) aby dowiedzieć się, ile intencji można dodać do modelu.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Jeśli potrzebujesz więcej niż maksymalna liczba intencji
Najpierw zastanów się, czy system używa zbyt wielu intencji.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Czy wiele intencji może być połączonych w jedną intencję z jednostkami
Intencje, które są zbyt podobne, mogą utrudnić ujmę usługi LUIS w rozróżnienie między nimi. Intencje powinny być na tyle zróżnicowane, aby przechwytywać główne zadania, o które prosi użytkownik, ale nie muszą przechwytywać każdej ścieżki, którą zajmuje kod. Na przykład bookFlight i FlightCustomerService mogą być oddzielnymi intencjami w aplikacji do podróży, ale bookInternationalFlight i BookDomesticFlight są zbyt podobne. Jeśli twój system musi je rozróżnić, użyj jednostek lub innej logiki, a nie intencji.

### <a name="dispatcher-model"></a>Model dyspozytora
Dowiedz się więcej na temat łączenia aplikacji LUIS i QnA maker z [modelem wysyłania.](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Żądanie pomocy dotyczącej aplikacji o znacznej liczbie intencji
Jeśli zmniejszenie liczby intencji lub podzielenie intencji na wiele aplikacji nie działa, skontaktuj się z pomocą techniczną. Jeśli Subskrypcja platformy Azure obejmuje usługi pomocy technicznej, skontaktuj się z [pomocą techniczną platformy Azure.](https://azure.microsoft.com/support/options/)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [o jednostkach](luis-concept-entity-types.md), które są ważnymi słowami powiązanymi z intencjami
* Dowiedz się, [jak dodawać intencje i zarządzać nimi](luis-how-to-add-intents.md) w aplikacji usługi LUIS.
* Przegląd najlepszych [rozwiązań w zakresie intencji](luis-concept-best-practices.md)