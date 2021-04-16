---
title: Limity — usługa LUIS
description: Ten artykuł zawiera znane limity usługi Azure Cognitive Services Language Understanding (LUIS). Usługa LUIS ma kilka obszarów limitów. Limit modelu steruje intencjami, jednostkami i funkcjami usługi LUIS. Limity przydziału na podstawie typu klucza. Kombinacja klawiatury steruje witryną internetową usługi LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 1f917087eb15d8c77356995299e27dfc1657cb5d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497204"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Limity modelu i kluczy usługi LUIS
Usługa LUIS ma kilka obszarów limitów. Pierwszy z nich to [limit modelu](#model-limits), który kontroluje intencje, jednostki i funkcje usługi LUIS. Drugi obszar to [limity przydziału](#key-limits) na podstawie typu klucza. Trzecim obszarem limitów jest kombinacja [klawiatury do](#keyboard-controls) kontrolowania witryny internetowej usługi LUIS. Czwartym obszarem jest [mapowanie regionów na świecie między](luis-reference-regions.md) witryną internetową tworzenia usługi LUIS a interfejsami API [punktu końcowego](luis-glossary.md#endpoint) usługi LUIS.

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Limity modelu

Jeśli Twoja aplikacja przekracza limity modelu usługi LUIS, rozważ użycie aplikacji wysyłania usługi [LUIS](luis-concept-enterprise.md#dispatch-tool-and-model) lub kontenera [usługi LUIS.](luis-container-howto.md)

|Warstwowy|Limit|
|--|:--|
| [Nazwa aplikacji][luis-get-started-create-app] | *Domyślna maksymalna liczba znaków |
| Aplikacje| 500 aplikacji na zasób tworzenia platformy Azure |
| [Testowanie wsadowe][batch-testing]| 10 zestawów danych, 1000 wypowiedzi na zestaw danych|
| Lista jawna | 50 na aplikację|
| Jednostki zewnętrzne | brak limitów |
| [Intencje][intents]|500 na aplikację: 499 intencji niestandardowych i wymagana _intencja None._<br>[Aplikacja oparta na wysyłaniu](https://aka.ms/dispatch-tool) ma odpowiednie 500 źródeł wysyłania.|
| [Wyświetlanie listy jednostek](./luis-concept-entity-types.md) | Element nadrzędny: 50, element podrzędny: 20 000 elementów. Nazwa kanonicka to *maksymalna domyślna wartość znaku. Wartości synonimów nie mają ograniczeń długości. |
| [jednostki i role uczenia maszynowego:](./luis-concept-entity-types.md)<br> Kompozytowe<br>Prosty<br>rola jednostki|Limit 100 jednostek nadrzędnych lub 330 jednostek, w zależności od tego, który limit użytkownik trafi najpierw. Rola jest liczony jako jednostka na potrzeby tego limitu. Przykładem jest złożone z prostą jednostką, która ma 2 role: 1 złożony + 1 prosty + 2 role = 4 z 330 jednostek.<br>Podjęzyki mogą być zagnieżdżone na maksymalnie 5 poziomach, z maksymalnie 20 obiektami podrzędnym na poziomie.|
|Model jako funkcja| Maksymalna liczba modeli, które mogą być używane jako funkcja dla określonego modelu, czyli 10 modeli. Maksymalna liczba list fraz używanych jako funkcja dla określonego modelu to 10 list fraz.|
| [Wersja zapoznawcza — jednostki listy dynamicznej](./luis-migration-api-v3.md)|2 listy ok. 1 tys. na żądanie punktu końcowego przewidywania zapytania|
| [Wzorce](luis-concept-patterns.md)|500 wzorców na aplikację.<br>Maksymalna długość wzorca to 400 znaków.<br>3 Jednostki Pattern.any na wzorzec<br>Maksymalnie 2 zagnieżdżone opcjonalne teksty we wzorcu|
| [Pattern.any](./luis-concept-entity-types.md)|100 na aplikację, 3 jednostki pattern.any na wzorzec |
| [Lista fraz][phrase-list]|500 list fraz. 10 globalnych list fraz ze względu na model jako limit funkcji. Lista fraz niewymiennych zawiera maksymalnie 5000 fraz. Wymienna lista fraz zawiera maksymalnie 50 000 fraz. Maksymalna łączna liczba fraz na aplikację to 500 000 fraz.|
| [Wstępnie utworzone jednostki](./howto-add-prebuilt-models.md) | brak limitu|
| [Jednostki wyrażenia regularnego](./luis-concept-entity-types.md)|20 jednostek<br>Maksymalnie 500 znaków. wzorzec jednostki wyrażenia regularnego|
| [Role](./luis-concept-entity-types.md)|300 ról na aplikację. 10 ról na jednostkę|
| [Wypowiedź][utterances] | 500 znaków<br><br>Jeśli masz tekst dłuższy niż ten limit znaków, musisz segmentować wypowiedź przed wprowadzeniem danych wejściowych do usługi LUIS, a otrzymasz odpowiedzi dla poszczególnych intencji na segment. Istnieją oczywiste przerwy, z których można korzystać, takie jak znaki interpunkacji i długie przerwy w mowie.|
| [Przykłady wypowiedzi][utterances] | 15 000 na aplikację — nie ma limitu liczby wypowiedzi na intencję<br><br>Jeśli musisz wytszkolić aplikację przy użyciu większej liczby przykładów, użyj modelu [wysyłania.](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) Trenuje się poszczególne aplikacje usługi LUIS (znane jako aplikacje podrzędne do nadrzędnej aplikacji wysyłania) przy użyciu co najmniej jednej intencji, a następnie trenuje aplikację wysyłania, która próbkuje z wypowiedzi każdej podrzędnej aplikacji LUIS, aby skierować żądanie przewidywania do odpowiedniej aplikacji podrzędnej. |
| [Versions](./luis-concept-app-iteration.md) (Wersje)| 100 wersji na aplikację |
| [Nazwa wersji][luis-how-to-manage-versions] | 128 znaków |

*Maksymalna domyślna długość znaku to 50 znaków.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Unikatowość nazwy

Nazwy obiektów muszą być unikatowe w porównaniu z innymi obiektami na tym samym poziomie.

|Obiekty|Ograniczenia|
|--|--|
|Intencja, jednostka|Wszystkie nazwy intencji i jednostek muszą być unikatowe w wersji aplikacji.|
|Składniki jednostki ML|Wszystkie składniki jednostki uczenia maszynowego (jednostki podrzędne) muszą być unikatowe w obrębie tej jednostki dla składników na tym samym poziomie.|
|Funkcje | Wszystkie nazwane funkcje, takie jak listy fraz, muszą być unikatowe w ramach wersji aplikacji.|
|Role jednostki|Wszystkie role w jednostce lub składniku jednostki muszą być unikatowe, jeśli znajdują się na tym samym poziomie jednostki (nadrzędny, podrzędny, grandchild itp.).|

## <a name="object-naming"></a>Nazewnictwo obiektów

Nie używaj następujących znaków w następujących nazwach.

|Obiekt|Wykluczanie znaków|
|--|--|
|Nazwy intencji, jednostki i roli|`:`<br>`$` <br> `&`|
|Nazwa wersji|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Użycie zasobów i limity

Język Language Understand ma oddzielne zasoby, jeden typ do tworzenia i jeden typ do wykonywania zapytań do punktu końcowego przewidywania. Aby dowiedzieć się więcej o różnicach między typami kluczy, zobacz [Authoring and query prediction endpoint keys in LUIS](luis-how-to-azure-subscription.md)(Tworzenie kluczy punktu końcowego przewidywania i wykonywanie zapytań w u usługi LUIS).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Limity zasobów tworzenia

Użyj _rodzaju_, `LUIS.Authoring` , podczas filtrowania zasobów w Azure Portal. Usługa LUIS ogranicza 500 aplikacji na zasób tworzenia na platformie Azure.

|Zasób tworzenia|Tworzenie modułu TPS|
|--|--|
|Starter (początkowy)|1 mln/miesiąc, 5/s|
|F0 — warstwa Bezpłatna |1 mln/miesiąc, 5/s|

* TPS = transakcje na sekundę

[Dowiedz się więcej o cenach.][pricing]

### <a name="query-prediction-resource-limits"></a>Limity zasobów przewidywania zapytań

Użyj _rodzaju_, `LUIS` , podczas filtrowania zasobów w Azure Portal. Zasób punktu końcowego przewidywania zapytania usługi LUIS używany w środowisku uruchomieniowym jest prawidłowy tylko dla zapytań punktu końcowego.

|Zasób Przewidywanie zapytań|Wykonywanie zapytań o moduł TPS|
|--|--|
|F0 — warstwa Bezpłatna |10 tys./miesiąc, 5/s|
|S0 — warstwa Standardowa|50/s|

### <a name="sentiment-analysis"></a>Analiza tonacji

[Integracja analizy tonacji](luis-how-to-publish-app.md#enable-sentiment-analysis), która dostarcza informacje o tonacji, jest zapewniana bez konieczności użycia innego zasobu platformy Azure.

### <a name="speech-integration"></a>Integracja mowy

[Integracja mowy zapewnia](../speech-service/how-to-recognize-intents-from-speech-csharp.md) 1000 żądań punktu końcowego na koszt jednostkowy.

[Dowiedz się więcej o cenach.][pricing]

## <a name="keyboard-controls"></a>Kontrolki klawiatury

|Klawiatury | Opis |
|--|--|
|Control+E|przełączanie między tokenami i jednostkami na liście wypowiedzi|

## <a name="website-sign-in-time-period"></a>Okres logowania do witryny internetowej

Dostęp do logowania to **60 minut.** Po upływie tego czasu wystąpi ten błąd. Musisz zalogować się ponownie.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
