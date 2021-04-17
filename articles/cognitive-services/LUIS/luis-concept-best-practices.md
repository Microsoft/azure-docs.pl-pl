---
title: Najlepsze rozwiązania dotyczące tworzenia aplikacji usługi LUIS
description: Poznaj najlepsze rozwiązania, aby uzyskać najlepsze wyniki z modelu aplikacji LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: d5fa2a1e865a4f54de268e7ad756d1d4363f3b78
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500213"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Najlepsze rozwiązania dotyczące tworzenia aplikacji do rozumienia języka (LUIS)
Użyj procesu tworzenia aplikacji, aby skompilować aplikację usługi LUIS:

* Tworzenie modeli językowych (intencji i jednostek)
* Dodaj kilka przykładowych wypowiedzi szkoleniowych (15–30 na intencję)
* Publikowanie w punkcie końcowym
* Testowanie z punktu końcowego

Po opublikowaniu [aplikacji użyj](luis-how-to-publish-app.md)cyklu projektowania, aby dodać funkcje, opublikować i przetestować z punktu końcowego. Nie rozpoczynaj następnego cyklu tworzenia, dodając więcej przykładowych wypowiedzi, ponieważ nie umożliwia to u usługi LUIS uczenia modelu za pomocą rzeczywistych wypowiedzi użytkownika.

Nie rozwijaj wypowiedzi, dopóki bieżący zestaw wypowiedzi przykładowych i końcowych nie zwróci pewnych, wysokich wyników przewidywania. Poprawianie wyników przy użyciu [uczenia aktywnego.](luis-concept-review-endpoint-utterances.md)




## <a name="do-and-dont"></a>Nie robisz i nie
Na poniższej liście przedstawiono najlepsze rozwiązania dotyczące aplikacji usługi LUIS:

|Zalecenia|Zakazy|
|--|--|
|[Planowanie schematu](#do-plan-your-schema)|[Kompilowanie i publikowanie bez planu](#dont-publish-too-quickly)|
|[Definiowanie odrębnych intencji](#do-define-distinct-intents)<br>[Dodawanie funkcji do intencji](#do-add-features-to-intents)<br>
[Korzystanie z jednostek uczenia maszynowego](#do-use-machine-learned-entities) |[Dodawanie wielu przykładowych wypowiedzi do intencji](#dont-add-many-example-utterances-to-intents)<br>[Używanie kilku lub prostych jednostek](#dont-use-few-or-simple-entities) |
|[Znajdź przesłodki między zbyt ogólnym i zbyt specyficznym dla każdej intencji](#do-find-sweet-spot-for-intents)|[Używanie usługi LUIS jako platformy szkoleniowej](#dont-use-luis-as-a-training-platform)|
|[Iteracyjne tworzenie aplikacji przy użyciu wersji](#do-build-your-app-iteratively-with-versions)<br>[Tworzenie jednostek dla dekompozycji modelu](#do-build-for-model-decomposition)|[Dodawanie wielu przykładowych wypowiedzi w tym samym formacie, ignorując inne formaty](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Dodawanie wzorców w kolejnych iteracjach](#do-add-patterns-in-later-iterations)|[Mieszanie definicji intencji i jednostek](#dont-mix-the-definition-of-intents-and-entities)|
|[Zrównoważyć wypowiedzi we wszystkich intencjach z](#balance-your-utterances-across-all-intents) wyjątkiem intencji None.<br>[Dodawanie przykładowych wypowiedzi do intencji None](#do-add-example-utterances-to-none-intent)|[Tworzenie list fraz ze wszystkimi możliwymi wartościami](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Korzystanie z sugerowanej funkcji do aktywnego uczenia](#do-leverage-the-suggest-feature-for-active-learning)|[Dodawanie zbyt wielu wzorców](#dont-add-many-patterns)|
|[Monitorowanie wydajności aplikacji za pomocą testowania wsadowego](#do-monitor-the-performance-of-your-app)|[Trenuj i publikuj przy użyciu każdej dodanej pojedynczej przykładowej wypowiedzi](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Planowanie schematu

Przed rozpoczęciem tworzenia schematu aplikacji należy zidentyfikować, co i gdzie zamierzasz używać tej aplikacji. Im bardziej szczegółowe i szczegółowe planowanie, tym lepsza staje się aplikacja.

* Badanie użytkowników docelowych
* Definiowanie osób end-to-end do reprezentowania aplikacji — głos, awatar, obsługa problemów (proaktywna, reaktywna)
* Identyfikowanie interakcji użytkownika (tekstu, mowy), za pośrednictwem których kanałów, dokańcanie istniejących rozwiązań lub tworzenie nowego rozwiązania dla tej aplikacji
* Podróż użytkownika end-to-end
    * Czego powinna robić ta aplikacja, a czego nie? * Jakie są priorytety tego, co należy zrobić?
    * Jakie są główne przypadki użycia?
* Zbieranie danych [— informacje na](data-collection.md) temat zbierania i przygotowywania danych

## <a name="do-define-distinct-intents"></a>Definiowanie odrębnych intencji
Upewnij się, że słownictwo dla każdej intencji jest tylko dla tej intencji i nie nakłada się na inne intencje. Jeśli na przykład chcesz mieć aplikację, która obsługuje rezerwacje podróży, takie jak loty linii lotniczych i hotele, możesz wybrać te obszary tematowe jako oddzielne intencje lub tę samą intencję z jednostkami dla określonych danych wewnątrz wypowiedzi.

Jeśli słownictwo między dwiema intencjami jest takie samo, połącz intencję i użyj jednostek.

Rozważmy następujące przykładowe wypowiedzi:

|Przykładowe wypowiedzi|
|--|
|Rezerwacja lotu|
|Zarezerwuj hotel|

`Book a flight` i `Book a hotel` używają tego samego słownictwa co `book a ` . Ten format jest taki sam, więc powinien mieć tę samą intencję z różnymi wyrazami i `flight` jako `hotel` wyodrębnione jednostki.

## <a name="do-add-features-to-intents"></a>Dodawanie funkcji do intencji

Funkcje opisują pojęcia dotyczące intencji. Funkcją może być lista fraz słów, które są istotne dla tej intencji, lub jednostka, która jest istotna dla tej intencji.

## <a name="do-find-sweet-spot-for-intents"></a>Znajdź uśmierzeń dla intencji
Użyj danych przewidywania z usługi LUIS, aby określić, czy twoje intencje się nakładają. Nakładające się intencje mylą usługi LUIS. Wynik jest taki, że intencja o najwyższej oceny jest zbyt blisko innej intencji. Ponieważ usługa LUIS nie używa dokładnie tej samej ścieżki w danych do trenowania za każdym razem, nakładające się intencje mają szansę na pierwszą lub drugą w trenowanie. Chcesz, aby wynik wypowiedzi dla każdej intencji był bardziej od siebie odejmuje, więc ten rzut monetą nie nastąpi. Dobre rozróżnienie intencji powinno za każdym razem powodować oczekiwaną intencję o najwyższej intencji.

## <a name="do-use-machine-learned-entities"></a>Czy używać jednostek uczenia maszynowego

Jednostki uczenia maszynowego są dostosowane do twojej aplikacji i wymagają etykietowania, aby się powiodło. Jeśli nie używasz jednostek uczenia maszynowego, być może używasz niewłaściwego narzędzia.

Jednostki uczenia maszynowego mogą używać innych jednostek jako funkcji. Te inne jednostki mogą być jednostkami niestandardowymi, takimi jak jednostki wyrażeń regularnych lub jednostki listy, lub można użyć wstępnie utworzonych jednostek jako funkcji.

Dowiedz się więcej [o skutecznych jednostkach uczenia maszynowego.](luis-concept-entity-types.md#machine-learned-ml-entity)

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Iteracyjne tworzenie aplikacji przy użyciu wersji

Każdy cykl tworzenia powinien być w nowej [wersji](./luis-concept-app-iteration.md)sklonowany z istniejącej wersji.

## <a name="do-build-for-model-decomposition"></a>Tworzenie kompilacji dla dekompozycji modelu

Dekompozycja modelu ma typowy proces:

* tworzenie **intencji** na podstawie intencji użytkownika aplikacji klienckiej
* dodawanie 15–30 przykładowych wypowiedzi na podstawie rzeczywistych danych wejściowych użytkownika
* etykietowanie koncepcji danych najwyższego poziomu w przykładowej wypowiedzi
* podział koncepcji danych na podjedności
* dodawanie funkcji do podjęć
* dodawanie funkcji do intencji

Po utworzeniu intencji i dodaniu przykładowych wypowiedzi w poniższym przykładzie opisano dekompozycję jednostki.

Zacznij od zidentyfikowania kompletnych pojęć dotyczących danych, które chcesz wyodrębnić w wypowiedzi. Jest to jednostka uczenia maszynowego. Następnie rozkomponuj frazę na jej części. Obejmuje to identyfikowanie podjęć i cech.

Jeśli na przykład chcesz wyodrębnić adres, najwyższą jednostką uczenia maszynowego może być `Address` . Podczas tworzenia adresu zidentyfikuj niektóre z jego podjęć, takie jak ulica, miasto, stan i kod pocztowy.

Kontynuuj rozkładanie tych elementów przez:
* Dodanie wymaganej funkcji kodu pocztowego jako jednostki wyrażenia regularnego.
* Rozkładanie ulicy na części:
    * Numer **ulicy z** wymaganą funkcją wstępnie utworzonej jednostki numeru.
    * Nazwa **ulicy**.
    * Typ **ulicy z** wymaganą funkcją jednostki listy, w tym słowami takimi jak drogi, okrąg, drogi i tor.

Interfejs API tworzenia w wersji 3 umożliwia dekompozycje modelu.

## <a name="do-add-patterns-in-later-iterations"></a>Dodawanie wzorców w kolejnych iteracjach

Przed dodaniem wzorców należy [](luis-concept-patterns.md) zrozumieć, jak zachowuje się aplikacja, ponieważ wzorce są ważone bardziej niż przykładowe wypowiedzi i niesłyszące zaufanie.

Po zrozumieniu, jak działa aplikacja, dodaj wzorce stosowane do aplikacji. Nie trzeba dodawać ich z każdą [iteracją](luis-concept-app-iteration.md).

Dodanie ich na początku projektu modelu nie ma żadnych szkód, ale łatwiej jest zobaczyć, jak każdy wzorzec zmienia model po przetestowaniu modelu z wypowiedziami.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Zrównoważyć wypowiedzi we wszystkich intencjach

Aby przewidywania usługi LUIS były dokładne, liczba przykładowych wypowiedzi w każdej intencji (z wyjątkiem intencji None) musi być względnie równa.

Jeśli masz intencję ze 100 przykładami wypowiedzi i intencję z 20 przykładami wypowiedzi, intencja 100 wypowiedzi będzie mieć większą szybkość przewidywania.

## <a name="do-add-example-utterances-to-none-intent"></a>Dodawanie przykładowych wypowiedzi do intencji None

Ta intencja jest intencją rezerwy, wskazującą wszystko poza aplikacją. Dodaj jedną przykładowe wypowiedź do intencji None dla każdej 10 przykładowych wypowiedzi w pozostałej części aplikacji usługi LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Korzystanie z sugerowanej funkcji do aktywnego uczenia

Używaj [regularnych](luis-how-to-review-endpoint-utterances.md)  funkcji przeglądania wypowiedzi punktu końcowego w uczeniu aktywnym zamiast dodawania kolejnych przykładowych wypowiedzi do intencji. Ponieważ aplikacja stale otrzymuje wypowiedzi punktu końcowego, ta lista rośnie i zmienia się.

## <a name="do-monitor-the-performance-of-your-app"></a>Monitorowanie wydajności aplikacji

Monitoruj dokładność przewidywania przy użyciu zestawu [testów wsadowych.](./luis-how-to-batch-test.md)

Zachowaj oddzielny zestaw wypowiedzi, które nie są [](luis-concept-utterance.md) używane jako przykładowe wypowiedzi ani wypowiedzi punktu końcowego. Ulepszanie aplikacji dla zestawu testowego. Dostosuj zestaw testowy, aby odzwierciedlał rzeczywiste wypowiedzi użytkownika. Ten zestaw testowy umożliwia ocenę każdej iteracji lub wersji aplikacji.

## <a name="dont-publish-too-quickly"></a>Nie publikuj zbyt szybko

Zbyt szybkie publikowanie aplikacji bez [odpowiedniego planowania](#do-plan-your-schema)może prowadzić do kilku problemów, takich jak:

* Aplikacja nie będzie działać w rzeczywistym scenariuszu na akceptowalnym poziomie wydajności.
* Schemat (intencje i jednostki) nie byłby odpowiedni, a jeśli opracowano logikę aplikacji klienckiej zgodnie ze schematem, może być konieczne ponowne przepisanie jej od podstaw. Może to spowodować nieoczekiwane opóźnienia i dodatkowe koszty dla projektu, nad który pracujesz.
* Wypowiedzi, które dodajesz do modelu, mogą powodować odchylenie w kierunku zestawu przykładowych wypowiedzi, który jest trudny do debugowania i identyfikowania. Utrudni to również usunięcie niejednoznaczności po zobowiązaniu do określonego schematu.

## <a name="dont-add-many-example-utterances-to-intents"></a>Nie dodawaj wielu przykładowych wypowiedzi do intencji

Po opublikowaniu aplikacji można dodawać tylko wypowiedzi z aktywnego uczenia w procesie cyklu życia procesów projektowania. Jeśli wypowiedzi są zbyt podobne, dodaj wzorzec.

## <a name="dont-use-few-or-simple-entities"></a>Nie używaj kilku lub prostych jednostek

Jednostki są budowy do wyodrębniania i przewidywania danych. Ważne jest, aby każda intencja zawierała jednostki uczenia maszynowego, które opisują dane w intencji. Dzięki temu usługa LUIS może przewidzieć intencję, nawet jeśli aplikacja kliency nie musi używać wyodrębnianych jednostek.

## <a name="dont-use-luis-as-a-training-platform"></a>Nie używaj usługi LUIS jako platformy szkoleniowej

Usługa LUIS jest specyficzna dla domeny modelu językowego. Nie jest przeznaczona do pracy jako ogólna platforma trenowania języka naturalnego.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Nie dodawaj wielu przykładowych wypowiedzi w tym samym formacie, ignorując inne formaty

Usługa LUIS oczekuje zmian w wypowiedziach intencji. Wypowiedzi mogą się różnić przy zachowaniu tego samego ogólnego znaczenia. Odmiany mogą obejmować długość wypowiedzi, wybór słów i położenie wyrazów.

|Nie używaj tego samego formatu|Czy używać różnych formatów|
|--|--|
|Kup bilet do Seattle<br>Kup bilet do Paryża<br>Kupowanie biletu do Orlando|Kup 1 bilet do Seattle<br>Zarezerwuj dwa stanowiska na czerwonym okiem do Paryża w następny poniedziałek<br>Chcę zarezerwować 3 bilety do Orlando na wiosnę|

Druga kolumna używa różnych czasowników (buy, reserve, book), różnych ilości (1, dwóch, 3) i różnych układów słów, ale wszystkie mają taki sam zamiar zakupu biletów linii lotniczych na podróż.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Nie mieszaj definicji intencji i jednostek

Utwórz intencję dla każdej akcji, która będzie akcję, która będzie tworzyć bot. Użyj jednostek jako parametrów, które sprawiają, że ta akcja jest możliwa.

W przypadku bota, który będzie rezerwować loty linii lotniczych, utwórz **intencję BookFlight.** Nie należy tworzyć intencji dla każdej linii lotniczej ani dla każdego miejsca docelowego. Użyj tych fragmentów danych jako [jednostek i](luis-concept-entity-types.md) oznacz je w przykładowych wypowiedziach.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Nie twórz list fraz ze wszystkimi możliwymi wartościami

Podaj kilka przykładów na [listach fraz,](luis-concept-feature.md) ale nie w każdym wyrazie lub frazie. Usługa LUIS uogólnia i uwzględnia kontekst.

## <a name="dont-add-many-patterns"></a>Nie dodawaj wielu wzorców

Nie dodawaj zbyt wielu [wzorców](luis-concept-patterns.md). Usługa LUIS jest przeznaczona do szybkiego uczenia się przy użyciu mniejszej liczby przykładów. Nie przeciążaj niepotrzebnie systemu.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Nie trenuj i nie publikuj przy użyciu każdej przykładowej wypowiedzi

Dodaj 10 lub 15 wypowiedzi przed trenowania i publikowania. Dzięki temu można zobaczyć wpływ na dokładność przewidywania. Dodanie pojedynczej wypowiedzi może nie mieć widocznego wpływu na ocenę.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, [jak zaplanować aplikację](luis-how-plan-your-app.md) w aplikacji usługi LUIS.