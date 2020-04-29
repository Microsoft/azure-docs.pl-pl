---
title: Najlepsze rozwiązania dotyczące tworzenia aplikacji LUIS
description: Poznaj najlepsze rozwiązania, aby uzyskać najlepsze wyniki z modelu aplikacji LUIS.
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 525d450084723a53ae090319d9ebf3f68d63beee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382385"
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
|[Definiowanie unikatowych intencji](#do-define-distinct-intents)<br>[Dodaj deskryptory do intencji](#do-add-descriptors-to-intents) |[Dodaj wiele przykładowych wyrażenia długości do intencji](#dont-add-many-example-utterances-to-intents)<br>[Użyj kilku lub prostych jednostek](#dont-use-few-or-simple-entities) |
|[Znajdź słodkie miejsce między ogólnym i zbyt specyficzne dla każdego zamiaru](#do-find-sweet-spot-for-intents)|[Korzystanie z LUIS jako platformy szkoleniowej](#dont-use-luis-as-a-training-platform)|
|[Iteracyjne Kompilowanie aplikacji przy użyciu wersji](#do-build-your-app-iteratively-with-versions)<br>[Kompiluj jednostki na potrzeby dekompozycji modelu](#do-build-for-model-decomposition)|[Dodaj wiele przykładowych wyrażenia długości tego samego formatu, ignorując inne formaty](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Dodaj wzorce w późniejszych iteracjach](#do-add-patterns-in-later-iterations)|[Mieszanie definicji intencji i jednostek](#dont-mix-the-definition-of-intents-and-entities)|
|[Zrównoważ swój wyrażenia długości we wszystkich intencjach](#balance-your-utterances-across-all-intents) , z wyjątkiem intencji none.<br>[Dodaj przykład wyrażenia długości do opcji none](#do-add-example-utterances-to-none-intent)|[Utwórz deskryptory ze wszystkimi możliwymi wartościami](#dont-create-descriptors-with-all-the-possible-values)|
|[Korzystanie z funkcji Sugeruj w przypadku aktywnego uczenia](#do-leverage-the-suggest-feature-for-active-learning)|[Dodaj zbyt wiele wzorców](#dont-add-many-patterns)|
|[Monitorowanie wydajności aplikacji za pomocą testów wsadowych](#do-monitor-the-performance-of-your-app)|[Uczenie i publikowanie za pomocą każdego pojedynczego przykładu wypowiedź dodanego](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definiuj różne intencje
Upewnij się, że słownictwo dla każdego zamiaru dotyczy tego zamiaru i nie nakłada się na inny cel. Na przykład jeśli chcesz mieć aplikację, która obsługuje układy podróży, takie jak loty lotnicze i Hotele, możesz wybierać te obszary tematyczne jako osobne intencje lub te same cele z jednostkami dla określonych danych wewnątrz wypowiedź.

Jeśli słownictwo między dwoma intencjami jest takie samo, Połącz zamiar i użyj jednostek.

Rozważmy następujący przykład wyrażenia długości:

|Przykładowe wypowiedzi|
|--|
|Książka a samolotem|
|Książka w hotelu|

`Book a flight`i `Book a hotel` używaj tego samego słownika `book a `. Ten format jest taki sam, więc powinien być taki sam, jak w przypadku innych słów `flight` i `hotel` jako wyodrębnionych jednostek.

## <a name="do-add-descriptors-to-intents"></a>Dodaj deskryptory do intencji

Deskryptory ułatwiają opisywanie funkcji dla zamiaru. Deskryptor może być listą fraz wyrazów, które są istotne dla tego zamiaru lub jednostki, która jest istotna dla tego celu.

## <a name="do-find-sweet-spot-for-intents"></a>Znajdź słodycze dla intencji
Użyj danych przewidywania z LUIS, aby określić, czy twoje intencje są nakładane. Nakładające się intencje odmylić LUIS. Wynika to z tego, że największe zamiar oceniania jest zbyt blisko innego celu. Ponieważ LUIS nie używa dokładnie tej samej ścieżki za pomocą danych do szkolenia za każdym razem, nakładające się przeznaczenie ma szansę na pierwsze lub drugie szkolenie. Chcesz, aby wyniki wypowiedź dla każdego zamiaru były od siebie oddzielone, aby nie nastąpiło Przerzucanie/flop. Dobre rozróżnienie w przypadku intencji powinno spowodować, że oczekiwane jest najlepsze zamierzone za każdym razem.

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Wykonaj iteracyjne Kompilowanie aplikacji przy użyciu wersji

Każdy cykl tworzenia powinien znajdować się w nowej [wersji](luis-concept-version.md), sklonowany z istniejącej wersji.

## <a name="do-build-for-model-decomposition"></a>Wykonaj kompilację dla dekompozycji modelu

Dekompozycja modelu ma typowy proces:

* Tworzenie **zamiaru** na podstawie założeń użytkownika aplikacji klienta
* Dodaj 15-30 przykład wyrażenia długości na podstawie rzeczywistych danych wejściowych użytkownika
* Oznacz koncepcję danych najwyższego poziomu na przykład wypowiedź
* Podziel koncepcję danych na podskładniki
* Dodawanie deskryptorów (funkcji) do podskładników
* Dodaj deskryptory (funkcje) do celu

Po utworzeniu zamiaru i dodaniu przykładu wyrażenia długości, w poniższym przykładzie opisano dekompozycję jednostki.

Zacznij od zidentyfikowania kompletnych koncepcji dotyczących danych, które mają zostać wyodrębnione w wypowiedź. To jest jednostka, którą uczysz. Następnie rozłożyć frazę na części. Obejmuje to zidentyfikowanie podskładników (jako jednostek) wraz z deskryptorami i ograniczeniami.

Na przykład jeśli chcesz wyodrębnić adres, można wywołać `Address`największą jednostkę polecaną maszynowo. Podczas tworzenia adresu Zidentyfikuj niektóre jego składniki, takie jak adres ulicy, miasto, Województwo i kod pocztowy.

Kontynuuj składanie tych elementów, **ograniczając** kod pocztowy do wyrażenia regularnego. Rozdziel adres ulicy na części numeru ulicy (przy użyciu wstępnie skompilowanego numeru), nazwy ulicy i typu ulicy. Typ ulicy można opisać przy użyciu listy **deskryptorów** , takiej jak promień, okrąg, droga i tor.

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

## <a name="dont-add-many-example-utterances-to-intents"></a>Nie dodawaj wielu przykładowych wyrażenia długości do intencji

Po opublikowaniu aplikacji należy dodać tylko wyrażenia długości z aktywnego uczenia w procesie cyklu życia projektu. Jeśli wyrażenia długości są zbyt podobne, Dodaj wzorzec.

## <a name="dont-use-few-or-simple-entities"></a>Nie używaj kilku lub prostych jednostek

Jednostki są kompilowane do wyodrębniania i przewidywania danych. Należy pamiętać, że każdy z zamiarów ma jednostki, które opisują dane w zamiarach. Pozwala to LUIS na przewidywalność celu, nawet jeśli aplikacja kliencka nie musi używać wyodrębnionej jednostki.

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

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Nie twórz deskryptorów ze wszystkimi możliwymi wartościami

Podaj kilka przykładów w [frazach](luis-concept-feature.md) deskryptorów, ale nie każdy wyraz. LUIS generalize i bierze pod uwagę kontekst.

## <a name="dont-add-many-patterns"></a>Nie dodawaj wielu wzorców

Nie dodawaj zbyt wielu [wzorców](luis-concept-patterns.md). LUIS jest przeznaczona do szybkiego uczenia się z mniejszą liczbą przykładów. Nie należy przeciążać systemu niepotrzebnie.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nie szkol ani nie Publikuj za pomocą każdego pojedynczego przykładu wypowiedź

Dodaj 10 lub 15 wyrażenia długości przed rozpoczęciem szkolenia i publikowania. Pozwala to zobaczyć wpływ na dokładność przewidywania. Dodanie pojedynczego wypowiedź może nie mieć widocznego wpływu na wynik.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [zaplanować aplikację](luis-how-plan-your-app.md) w aplikacji Luis.
