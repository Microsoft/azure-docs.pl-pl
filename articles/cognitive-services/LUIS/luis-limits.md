---
title: Limity — LUIS
description: Ten artykuł zawiera znane limity usługi Azure Cognitive Services Language Understanding (LUIS). LUIS ma kilka obszarów ograniczeń. Limit modelu kontroluje intencje, jednostki i funkcje w LUIS. Limity przydziału na podstawie typu klucza. Kombinacja klawiatury kontroluje witrynę sieci Web LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 41423ce34a62dfdbd5b9a60f683a2366a94d1bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97976796"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Limity dotyczące modelu i kluczy LUIS
LUIS ma kilka obszarów ograniczeń. Pierwszy to [Limit modelu](#model-limits), który steruje intencjami, jednostkami i funkcjami w Luis. Drugi obszar ma [limity przydziału](#key-limits) na podstawie typu klucza. Trzeci obszar limitów jest [kombinacją klawiatury](#keyboard-controls) służącą do kontrolowania witryny sieci Web Luis. Czwarty obszar to [Mapowanie regionów świata](luis-reference-regions.md) między witryną sieci Web Luis Authoring a interfejsem API Luis [Endpoint](luis-glossary.md#endpoint) .

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Limity modelu

Jeśli Twoja aplikacja przekracza limity modelu LUIS, rozważ użycie aplikacji do [wysyłania Luis](luis-concept-enterprise.md#dispatch-tool-and-model) lub [kontenera Luis](luis-container-howto.md).

|Warstwowy|Limit|
|--|:--|
| [Nazwa aplikacji][luis-get-started-create-app] | * Maksimum znaku domyślnego |
| Aplikacje| 500 aplikacji na zasób tworzenia na platformie Azure |
| [Testowanie wsadowe][batch-testing]| 10 zestawów danych, 1000 wyrażenia długości na zestaw danych|
| Lista jawna | 50 na aplikację|
| Jednostki zewnętrzne | Brak limitów |
| [Intencje][intents]|500 na aplikację: 499 niestandardowe intencje i wymagane opcje _none_ .<br>Aplikacja [oparta na wysyłce](https://aka.ms/dispatch-tool) ma odpowiednie źródła wysyłania 500.|
| [Wyświetlanie listy jednostek](./luis-concept-entity-types.md) | Element nadrzędny: 50, element podrzędny: 20 000 elementów. Nazwa kanoniczna to * domyślny znak maksymalny. Wartości synonimów nie mają ograniczenia długości. |
| [jednostki uczenia maszynowego + role](./luis-concept-entity-types.md):<br> złożone<br>ułatwia<br>rola jednostki|Limit 100 jednostek nadrzędnych lub 330 jednostek, w zależności od liczby trafień użytkownika. Rola jest traktowana jako jednostka na potrzeby tego limitu. Przykładem jest kompozyt z prostą jednostką, która ma 2 role: 1 złożona + 1 prosta + 2 role = 4 jednostek 330.<br>Podjednostki mogą być zagnieżdżane do 5 poziomów, z maksymalnie 10 elementami podrzędnymi na poziom.|
|Model jako funkcja| Maksymalna liczba modeli, które mogą być używane jako funkcja do określonego modelu do 10 modeli. Maksymalna liczba list fraz używanych jako funkcja dla określonego modelu do 10 list fraz.|
| [Wersja zapoznawcza — jednostki listy dynamicznej](./luis-migration-api-v3.md)|2 listy ~ 1K na żądanie punktu końcowego przewidywania zapytań|
| [Wzorce](luis-concept-patterns.md)|500 wzorców na aplikację.<br>Maksymalna długość wzorca to 400 znaków.<br>3 wzorzec. wszystkie jednostki na wzorzec<br>Maksymalnie 2 zagnieżdżonych dodatkowych tekstów w wzorcu|
| [Wzorzec. any](./luis-concept-entity-types.md)|100 na aplikację, 3 wzorce. wszystkie jednostki na wzorzec |
| [Lista fraz][phrase-list]|500 list fraz. 10 globalnych list fraz spowodowanych przez model jako limit funkcji. Lista fraz niewymiennych ma maksymalnie 5 000 fraz. Lista fraz wymiennych ma maksymalnie 50 000 fraz. Maksymalna liczba fraz dla każdej aplikacji 500 000 fraz.|
| [Wstępnie utworzone jednostki](./howto-add-prebuilt-models.md) | bez limitu|
| [Jednostki wyrażenia regularnego](./luis-concept-entity-types.md)|20 jednostek<br>maksymalnie 500 znaków. na wzorzec jednostki wyrażenia regularnego|
| [Role](./luis-concept-entity-types.md)|300 ról na aplikację. 10 ról na jednostkę|
| [Wypowiedź][utterances] | 500 znaków<br><br>Jeśli masz tekst dłuższy niż ten limit znaków, musisz podzielić wypowiedź przed wprowadzeniem do LUIS, a otrzymasz indywidualne odpowiedzi na segment. Istnieją oczywiste przerwy, z którymi można korzystać, takie jak znaki interpunkcyjne i długie przerwy w mowie.|
| [Przykłady wypowiedź][utterances] | 15 000 na aplikację — nie ma limitu liczby wyrażenia długości na zamiar<br><br>Jeśli konieczne jest uczenie aplikacji z więcej przykładów, użyj podejścia modelu [wysyłania](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) . Możesz przeszkolić pojedyncze aplikacje LUIS (nazywane aplikacjami podrzędnymi do nadrzędnej aplikacji do wysyłania) z co najmniej jednym zamiarem, a następnie przeszkolić aplikację do wysyłania, która próbuje pobrać z poszczególnych podrzędnych aplikacji LUIS wyrażenia długości, aby skierować żądanie przewidywania do odpowiedniej aplikacji podrzędnej. |
| [Versions](./luis-concept-app-iteration.md) (Wersje)| 100 wersji na aplikację |
| [Nazwa wersji][luis-how-to-manage-versions] | 128 znaków |

* Wartość domyślna to 50 znaków.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Unikatowość nazw

Nazwy obiektów muszą być unikatowe w porównaniu z innymi obiektami tego samego poziomu.

|Obiekty|Ograniczenia|
|--|--|
|Cel, jednostka|Wszystkie nazwy zamierzeń i jednostek muszą być unikatowe w wersji aplikacji.|
|Składniki jednostki ML|Wszystkie składniki jednostki uczenia maszynowego (jednostki podrzędne) muszą być unikatowe w tej jednostce dla składników na tym samym poziomie.|
|Funkcje | Wszystkie nazwane funkcje, takie jak listy fraz, muszą być unikatowe w ramach wersji aplikacji.|
|Role jednostki|Wszystkie role w jednostce lub składniku jednostki muszą być unikatowe, gdy znajdują się na tym samym poziomie jednostki (element nadrzędny, podrzędny, grandchild itp.).|

## <a name="object-naming"></a>Nazywanie obiektów

Nie należy używać następujących znaków w następujących nazwach.

|Obiekt|Wyklucz znaki|
|--|--|
|Zamierzenia, jednostki i nazwy ról|`:`<br>`$` <br> `&`|
|Nazwa wersji|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Użycie i limity zasobów

Zrozumienie języka ma oddzielne zasoby, jeden typ do tworzenia i jeden typ do wykonywania zapytań dotyczących punktu końcowego przewidywania. Aby dowiedzieć się więcej o różnicach między typami kluczy, zobacz [Tworzenie kluczy punktu końcowego prognozowanie i wykonywanie zapytań w Luis](luis-how-to-azure-subscription.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Limity zasobów tworzenia

Użyj _rodzaju_, `LUIS.Authoring` , podczas filtrowania zasobów w Azure Portal. LUIS ogranicza 500 aplikacji na zasób tworzenia na platformie Azure.

|Tworzenie zasobu|TPS tworzenia|
|--|--|
|Starter (początkowy)|1 milion/miesiąc, 5/s|
|F0 — warstwa Bezpłatna |1 milion/miesiąc, 5/s|

* TPS = liczba transakcji na sekundę

[Dowiedz się więcej o cenach.][pricing]

### <a name="query-prediction-resource-limits"></a>Limity zasobów przewidywania zapytań

Użyj _rodzaju_, `LUIS` , podczas filtrowania zasobów w Azure Portal. Zasób punktu końcowego przewidywania zapytań LUIS używany w środowisku uruchomieniowym jest prawidłowy tylko dla zapytań punktów końcowych.

|Zasób predykcyjny zapytania|TPS zapytania|
|--|--|
|F0 — warstwa Bezpłatna |10 tysięcy/miesiąc, 5/sekundę|
|S0 — warstwa standardowa|50/sekundę|

### <a name="sentiment-analysis"></a>Analiza tonacji

[Integracja z analizą tonacji](luis-how-to-publish-app.md#enable-sentiment-analysis), która dostarcza informacje tonacji, jest udostępniana bez konieczności używania innego zasobu platformy Azure.

### <a name="speech-integration"></a>Integracja mowy

[Integracja mowy](../speech-service/how-to-recognize-intents-from-speech-csharp.md) zapewnia 1000 żądań punktów końcowych według kosztu jednostkowego.

[Dowiedz się więcej o cenach.][pricing]

## <a name="keyboard-controls"></a>Kontrolki klawiatury

|Wprowadzanie z klawiatury | Opis |
|--|--|
|Control + E|przełącza między tokenami i jednostkami na liście wyrażenia długości|

## <a name="website-sign-in-time-period"></a>Okres logowania do witryny sieci Web

Dostęp do logowania wynosi od **60 minut**. Ten błąd zostanie wyświetlony po upływie tego czasu. Musisz zalogować się ponownie.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
