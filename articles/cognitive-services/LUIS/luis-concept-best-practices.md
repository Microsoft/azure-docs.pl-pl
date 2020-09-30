---
title: Najlepsze rozwiązania dotyczące tworzenia aplikacji LUIS
description: Poznaj najlepsze rozwiązania, aby uzyskać najlepsze wyniki z modelu aplikacji LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: a16267b0bc5d9b550c73dc4e1dafee8304ec0237
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541825"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Najlepsze rozwiązania dotyczące tworzenia aplikacji do interpretacji języka (LUIS)
Tworzenie aplikacji LUIS za pomocą procesu tworzenia aplikacji:

* Tworzenie modeli języka (intencje i jednostki)
* Dodaj kilka przykładów szkoleniowych wyrażenia długości (15-30 na intencję)
* Publikowanie w punkcie końcowym
* Testuj z punktu końcowego

Po [opublikowaniu](luis-how-to-publish-app.md)aplikacji użyj cyklu życia programowania, aby dodać funkcje, publikować i testować z punktu końcowego. Nie rozpoczynaj następnego cyklu tworzenia przez dodanie więcej przykładowych wyrażenia długości, ponieważ to nie pozwala LUIS na naukę modelu z rzeczywistymi wyrażenia długościami użytkowników.

Nie rozszerzaj wyrażenia długości do momentu, aż bieżący zestaw obu przykładów i punkt końcowy wyrażenia długości nie zwracają wątpliwości, wysokie wyniki przewidywania. Popraw wyniki przy użyciu [aktywnej nauki](luis-concept-review-endpoint-utterances.md).




## <a name="do-and-dont"></a>Zrób i nie
Poniższa lista zawiera najlepsze rozwiązania dotyczące aplikacji LUIS:

|Zalecenia|Zakazy|
|--|--|
|[Planowanie schematu](#do-plan-your-schema)|[Kompiluj i Publikuj bez planu](#dont-publish-too-quickly)|
|[Definiowanie unikatowych intencji](#do-define-distinct-intents)<br>[Dodawanie funkcji do intencji](#do-add-features-to-intents)<br>
[Użyj zastosowanych jednostek maszyn](#do-use-machine-learned-entities) |[Dodaj wiele przykładowych wyrażenia długości do intencji](#dont-add-many-example-utterances-to-intents)<br>[Użyj kilku lub prostych jednostek](#dont-use-few-or-simple-entities) |
|[Znajdź słodkie miejsce między ogólnym i zbyt specyficzne dla każdego zamiaru](#do-find-sweet-spot-for-intents)|[Korzystanie z LUIS jako platformy szkoleniowej](#dont-use-luis-as-a-training-platform)|
|[Iteracyjne Kompilowanie aplikacji przy użyciu wersji](#do-build-your-app-iteratively-with-versions)<br>[Kompiluj jednostki na potrzeby dekompozycji modelu](#do-build-for-model-decomposition)|[Dodaj wiele przykładowych wyrażenia długości tego samego formatu, ignorując inne formaty](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Dodaj wzorce w późniejszych iteracjach](#do-add-patterns-in-later-iterations)|[Mieszanie definicji intencji i jednostek](#dont-mix-the-definition-of-intents-and-entities)|
|[Zrównoważ swój wyrażenia długości we wszystkich intencjach](#balance-your-utterances-across-all-intents) , z wyjątkiem intencji none.<br>[Dodaj przykład wyrażenia długości do opcji none](#do-add-example-utterances-to-none-intent)|[Tworzenie list fraz ze wszystkimi możliwymi wartościami](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Korzystanie z funkcji Sugeruj w przypadku aktywnego uczenia](#do-leverage-the-suggest-feature-for-active-learning)|[Dodaj zbyt wiele wzorców](#dont-add-many-patterns)|
|[Monitorowanie wydajności aplikacji za pomocą testów wsadowych](#do-monitor-the-performance-of-your-app)|[Uczenie i publikowanie za pomocą każdego pojedynczego przykładu wypowiedź dodanego](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Zaplanuj schemat

Przed rozpoczęciem tworzenia schematu aplikacji należy określić, co i gdzie planujesz korzystać z tej aplikacji. Im bardziej szczegółowe i konkretne planowanie, tym lepiej Twoja aplikacja.

* Zbadaj użytkowników skierowanych
* Definiowanie kompleksowej osób do reprezentowania aplikacji — głosu, awatara, obsługi problemu (proaktywna, reaktywna)
* Identyfikuj Interakcje użytkownika (tekst, mowę), za pomocą których kanałów, które są przekazywane do istniejących rozwiązań lub tworzenia nowego rozwiązania dla tej aplikacji
* Kompleksowa podróż z użytkownikami
    * Co należy oczekiwać do wykonania tej aplikacji, a nie do wykonania? * Jakie są priorytety czynności, jakie powinien wykonać?
    * Jakie są główne przypadki użycia?
* Zbieranie danych — [informacje](data-collection.md) o zbieraniu i przygotowywaniu danych

## <a name="do-define-distinct-intents"></a>Definiuj różne intencje
Upewnij się, że słownictwo dla każdego zamiaru dotyczy tego zamiaru i nie nakłada się na inny cel. Na przykład jeśli chcesz mieć aplikację, która obsługuje układy podróży, takie jak loty lotnicze i Hotele, możesz wybierać te obszary tematyczne jako osobne intencje lub te same cele z jednostkami dla określonych danych wewnątrz wypowiedź.

Jeśli słownictwo między dwoma intencjami jest takie samo, Połącz zamiar i użyj jednostek.

Rozważmy następujący przykład wyrażenia długości:

|Przykładowe wypowiedzi|
|--|
|Książka a samolotem|
|Książka w hotelu|

`Book a flight` i `Book a hotel` Używaj tego samego słownika `book a ` . Ten format jest taki sam, więc powinien być taki sam, jak w przypadku innych słów `flight` i `hotel` jako wyodrębnionych jednostek.

## <a name="do-add-features-to-intents"></a>Dodaj funkcje do intencji

Funkcje opisują koncepcje dotyczące zamiaru. Funkcja może być listą fraz wyrazów, które są istotne dla tego zamiaru lub jednostki, która jest istotna dla tego celu.

## <a name="do-find-sweet-spot-for-intents"></a>Znajdź słodycze dla intencji
Użyj danych przewidywania z LUIS, aby określić, czy twoje intencje są nakładane. Nakładające się intencje odmylić LUIS. Wynika to z tego, że największe zamiar oceniania jest zbyt blisko innego celu. Ponieważ LUIS nie używa dokładnie tej samej ścieżki za pomocą danych do szkolenia za każdym razem, nakładające się przeznaczenie ma szansę na pierwsze lub drugie szkolenie. Chcesz, aby wyniki wypowiedź dla każdego zamiaru były od siebie oddzielone, aby nie nastąpiło Przerzucanie/flop. Dobre rozróżnienie w przypadku intencji powinno spowodować, że oczekiwane jest najlepsze zamierzone za każdym razem.

## <a name="do-use-machine-learned-entities"></a>Użyj obiektów, na które nauczysz się

Jednostki, na które nauczyno się maszyny, są dostosowane do Twojej aplikacji i wymagają pomyślnego etykietowania. Jeśli nie używasz jednostek obsługiwanych przez maszynę, być może używasz niewłaściwego narzędzia.

Jednostki, na których nauczyne maszyny, mogą używać innych jednostek jako funkcji. Te inne jednostki mogą być jednostkami niestandardowymi, takimi jak jednostki wyrażenia regularnego lub jednostki listy, lub można użyć wstępnie utworzonych jednostek jako funkcji.

Dowiedz się więcej o [obowiązujących jednostkach maszyn](luis-concept-entity-types.md#effective-machine-learned-entities).

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Wykonaj iteracyjne Kompilowanie aplikacji przy użyciu wersji

Każdy cykl tworzenia powinien znajdować się w nowej [wersji](luis-concept-version.md), sklonowany z istniejącej wersji.

## <a name="do-build-for-model-decomposition"></a>Wykonaj kompilację dla dekompozycji modelu

Dekompozycja modelu ma typowy proces:

* Tworzenie **zamiaru** na podstawie założeń użytkownika aplikacji klienta
* Dodaj 15-30 przykład wyrażenia długości na podstawie rzeczywistych danych wejściowych użytkownika
* Oznacz koncepcję danych najwyższego poziomu na przykład wypowiedź
* Podziel koncepcję danych na podjednostki
* Dodawanie funkcji do podjednostek
* Dodawanie funkcji do intencji

Po utworzeniu zamiaru i dodaniu przykładu wyrażenia długości, w poniższym przykładzie opisano dekompozycję jednostki.

Zacznij od zidentyfikowania kompletnych koncepcji dotyczących danych, które mają zostać wyodrębnione w wypowiedź. To jest Twoja jednostka uczenia maszynowego. Następnie rozłożyć frazę na części. Obejmuje to identyfikowanie podjednostek i funkcji.

Na przykład jeśli chcesz wyodrębnić adres, można wywołać najważniejsze jednostki uczenia maszynowego `Address` . Podczas tworzenia adresu Zidentyfikuj niektóre jego podjednostki, takie jak adres ulicy, miasto, Województwo i kod pocztowy.

Kontynuuj tworzenie tych elementów według:
* Dodawanie wymaganej funkcji kodu pocztowego jako jednostki wyrażenia regularnego.
* Tworzenie adresu ulicy w częściach:
    * **Numer ulicy** z wymaganą funkcją wstępnie skompilowanej jednostki liczby.
    * **Nazwa ulicy**.
    * **Typ ulicy** z wymaganą funkcją jednostki listy, w tym słowami, takimi jak drogi, okrąg, droga i tor.

Interfejs API tworzenia w wersji 3 pozwala na dekompozycję modelu.

## <a name="do-add-patterns-in-later-iterations"></a>Dodaj wzorce w późniejszych iteracjach

Należy zrozumieć, jak działa aplikacja przed dodaniem [wzorców](luis-concept-patterns.md) , ponieważ wzorce są ważone bardziej silnie niż przykład wyrażenia długości i spowodują pochylenie.

Po zrozumieniu sposobu działania aplikacji Dodaj wzorce, które są stosowane do aplikacji. Nie trzeba dodawać ich razem z każdą [iteracją](luis-concept-app-iteration.md).

Nie trzeba dodawać ich na początku projektu modelu, ale łatwiej jest zobaczyć, jak każdy wzorzec zmienia model po przetestowaniu modelu z wyrażenia długości.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Aby zrównoważyć wyrażenia długości na wszystkie intencje

Aby przewidywania LUIS były dokładne, liczba przykładowych wyrażenia długości w każdym zamiarie (z wyjątkiem intencji None) musi być stosunkowo równa.

Jeśli masz zamiar z 100 przykładem wyrażenia długości i zamiarem z 20 przykład wyrażenia długości, zamiaru 100-wypowiedź będzie miała wyższą stawkę prognozowania.

## <a name="do-add-example-utterances-to-none-intent"></a>Dodaj przykład wyrażenia długości do None

Ta metoda jest zamiarem rezerwowym, co oznacza, że wszystko poza aplikacją. Dodaj jeden przykład wypowiedź do opcji Brak dla każdego 10 przykładowych wyrażenia długości w pozostałej części aplikacji LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Korzystanie z funkcji Sugeruj w przypadku aktywnego uczenia

Regularnie korzystaj z **wyrażenia długości punktu końcowego** usługi [Active Learning](luis-how-to-review-endpoint-utterances.md), zamiast dodawać więcej przykładowych wyrażenia długości do zamiar. Ponieważ aplikacja stale otrzymuje punkt końcowy wyrażenia długości, ta lista rośnie i ulega zmianie.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorowanie wydajności aplikacji

Monitoruj dokładność przewidywania przy użyciu zestawu [testów wsadowych](luis-concept-batch-test.md) .

Przechowuj oddzielny zestaw wyrażenia długości, które nie są używane jako [przykład wyrażenia długości](luis-concept-utterance.md) lub Endpoint wyrażenia długości. Kontynuuj ulepszanie aplikacji dla zestawu testowego. Dostosuj zestaw testów, aby odzwierciedlał rzeczywiste wyrażenia długości użytkownika. Ten zestaw testów służy do obliczania każdej iteracji lub wersji aplikacji.

## <a name="dont-publish-too-quickly"></a>Nie Publikuj zbyt szybko

Publikowanie aplikacji za szybko, bez konieczności [planowania](#do-plan-your-schema), może prowadzić do kilku problemów, takich jak:

* Twoja aplikacja nie będzie działała w Twoim rzeczywistym scenariuszu na akceptowalnym poziomie wydajności.
* Schemat (intencje i jednostki) nie będzie odpowiedni i jeśli opracowano logikę aplikacji klienta po schemacie, może być konieczne ponowne zapisanie go od podstaw. Może to spowodować nieoczekiwane opóźnienia i dodatkowy koszt projektu, nad którym pracujesz.
* Wyrażenia długości dodawane do modelu może spowodować odliczenie się do przykładowego zestawu wypowiedź, który trudno jest debugować i identyfikować. Spowoduje to również usunięcie niejednoznaczności po zatwierdzeniu do określonego schematu.

## <a name="dont-add-many-example-utterances-to-intents"></a>Nie dodawaj wielu przykładowych wyrażenia długości do intencji

Po opublikowaniu aplikacji należy dodać tylko wyrażenia długości z aktywnego uczenia w procesie cyklu życia projektu. Jeśli wyrażenia długości są zbyt podobne, Dodaj wzorzec.

## <a name="dont-use-few-or-simple-entities"></a>Nie używaj kilku lub prostych jednostek

Jednostki są kompilowane do wyodrębniania i przewidywania danych. Należy pamiętać, że każdy z zamiarów ma jednostki uczenia maszynowego, które opisują dane w zamiarach. Pozwala to LUIS na przewidywalność celu, nawet jeśli aplikacja kliencka nie musi używać wyodrębnionej jednostki.

## <a name="dont-use-luis-as-a-training-platform"></a>Nie używaj LUIS jako platformy szkoleniowej

LUIS jest charakterystyczny dla domeny modelu języka. Nie jest ona przeznaczona do pracy jako ogólna platforma szkoleń w języku naturalnym.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nie dodawaj wielu przykładowych wyrażenia długości w tym samym formacie, ignorując inne formaty

LUIS oczekuje wariacji w wyrażenia długości celu. Wyrażenia długości może się różnić w zależności od tego, co ma takie samo ogólne znaczenie. Wariacje mogą obejmować długość wypowiedź, wybór wyrazów oraz umieszczanie wyrazów.

|Nie używaj tego samego formatu|Użyj różnych formatów|
|--|--|
|Kup bilet w Seattle<br>Kup bilet w Paryżu<br>Kup bilet do Orlando|Kup 1 bilet do Seattle<br>Zarezerwuj dwa stanowiska na czerwonym okiem do paryskiego następnego poniedziałku<br>Chcę zasubskrybować 3 bilety do Orlando dla przerwania sprężyny|

W drugiej kolumnie są stosowane różne czasowniki (zakup, rezerwacja, książka), różne ilości (1, 2, 3) i różne rozmieszczenia słów, ale wszystkie mają takie same zamiar zakupu biletów lotniczych do podróży.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Nie mieszaj definicji intencji i jednostek

Utwórz zamiar dla każdej akcji, którą zajmie bot. Użyj jednostek jako parametrów, które umożliwiają wykonanie tej akcji.

W przypadku botów, które będą księgować loty lotnicze, należy utworzyć intencję **BookFlight** . Nie należy tworzyć zamierzeń dla każdego linii lotniczej lub każdego miejsca docelowego. Użyj tych elementów danych jako [jednostek](luis-concept-entity-types.md) i oznacz je w przykładowym wyrażenia długości.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Nie twórz list fraz ze wszystkimi możliwymi wartościami

Podaj kilka przykładów na [listach fraz](luis-concept-feature.md) , ale nie każdy wyraz lub frazę. LUIS generalize i bierze pod uwagę kontekst.

## <a name="dont-add-many-patterns"></a>Nie dodawaj wielu wzorców

Nie dodawaj zbyt wielu [wzorców](luis-concept-patterns.md). LUIS jest przeznaczona do szybkiego uczenia się z mniejszą liczbą przykładów. Nie należy przeciążać systemu niepotrzebnie.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nie szkol ani nie Publikuj za pomocą każdego pojedynczego przykładu wypowiedź

Dodaj 10 lub 15 wyrażenia długości przed rozpoczęciem szkolenia i publikowania. Pozwala to zobaczyć wpływ na dokładność przewidywania. Dodanie pojedynczego wypowiedź może nie mieć widocznego wpływu na wynik.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zaplanować aplikację](luis-how-plan-your-app.md) w aplikacji Luis.
