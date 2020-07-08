---
title: Zaplanuj aplikację QnA Maker
description: Planowanie aplikacji QnA Maker wymaga poznania sposobu, w jaki QnA Maker działa i współdziała z innymi usługami platformy Azure, a także z pewnymi pojęciami dotyczącymi bazy wiedzy.
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875715"
---
# <a name="plan-your-qna-maker-app"></a>Planowanie aplikacji QnA Maker

Planowanie aplikacji QnA Maker wymaga poznania sposobu, w jaki QnA Maker działa i współdziała z innymi usługami platformy Azure, a także z pewnymi pojęciami dotyczącymi bazy wiedzy.

## <a name="azure-resources"></a>Zasoby platformy Azure

Każdy [zasób platformy Azure](azure-resources.md#resource-purposes) utworzony przy użyciu QNA Maker ma określony cel. Ponieważ każdy zasób ma własne przeznaczenie, limity i [warstwę cenową](azure-resources.md#pricing-tier-considerations), ważne jest, aby zrozumieć, jakie zasoby są wykonywane w ramach procesu planowania.

|Zasób|Przeznaczenie|
|--|--|
| Zasób [QNA Maker](azure-resources.md#qna-maker-resource)|Tworzenie i prognozowanie zapytań|
| Zasób [Wyszukiwanie poznawcze](azure-resources.md#cognitive-search-resource)|Przechowywanie i wyszukiwanie danych|
| Zasób [usługi App Service zasób i plan aplikacji](azure-resources.md#app-service-and-app-service-plan)|Punkt końcowy przewidywania zapytania|
| Zasób [Application Insights](azure-resources.md#application-insights)|Dane telemetryczne przewidywania zapytań|

### <a name="resource-planning"></a>Planowanie zasobów

Podczas uczenia się tworzenia i przewidywania zapytań przy użyciu warstwy Bezpłatna, `F0` każdy z zasobów działa i zapewnia obsługę prognozowania tworzenia i zapytań. Po przejściu do środowiska produkcyjnego, na żywo, w scenariuszu należy przeprowadzić ponowną ocenę wybranego zasobu.

#### <a name="qna-maker-resource"></a>Zasób QnA Maker

Pojedynczy zasób QnA Maker może obsługiwać więcej niż jedną bazę wiedzy. Liczba baz wiedzy zależy od ilości obsługiwanych indeksów Wyszukiwanie poznawcze warstwy cenowej. Dowiedz się więcej na temat [relacji indeksów z bazami wiedzy](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Rozmiar bazy wiedzy i przepływność

Planując tworzenie rzeczywistej aplikacji, Zaplanuj zasoby pod kątem rozmiaru bazy wiedzy oraz oczekiwane żądania prognozowania zapytań.

Rozmiar bazy wiedzy jest kontrolowany przez:
* Limity warstwy cenowej [zasobów wyszukiwanie poznawcze](../../../search/search-limits-quotas-capacity.md)
* [Limity QnA Maker](../limits.md)

Żądanie prognozowania zapytań bazy wiedzy jest kontrolowane przez plan aplikacji sieci Web i aplikację sieci Web. Zapoznaj się z [zalecanymi ustawieniami](azure-resources.md#recommended-settings) w celu zaplanowania warstwy cenowej.

### <a name="resource-sharing"></a>Udostępnianie zasobów

Jeśli masz już niektóre z tych zasobów, możesz rozważyć udostępnianie zasobów. Niektóre zasoby [mogą być udostępniane](azure-resources.md#share-services-with-qna-maker), ale jest to zaawansowany scenariusz.

Wszystkie bazy wiedzy utworzone w tym samym QnA Maker zasobów współużytkują ten sam punkt końcowy przewidywania zapytania **testowego** .

### <a name="understanding-impact-of-resource-selection"></a>Zrozumienie wpływu wyboru zasobów

Wybór wybranego zasobu oznacza, że bazy wiedzy pomyślnie odpowiadają przewidywania zapytań.

Jeśli baza wiedzy nie działa prawidłowo, zwykle przyczyną problemu jest niewłaściwe zarządzanie zasobami.

Niewłaściwy wybór zasobów wymaga zbadania, aby określić, który [zasób musi zostać zmieniony](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Bazy wiedzy

Baza wiedzy jest bezpośrednio związana z jej zasobem QnA Maker i zawiera pary pytań i odpowiedzi (QnA) używane do odpowiedzi na żądania prognozowania zapytań.

### <a name="language-considerations"></a>Zagadnienia dotyczące języka

Pierwsza baza wiedzy utworzona w ramach zasobu QnA Maker ustawia język dla zasobu. Dla zasobu QnA Maker można korzystać tylko z jednego języka.

Strukturę zasobów QnA Maker według języka lub Użyj usługi [translator](../../translator/translator-info-overview.md) , aby zmienić zapytanie z innego języka do języka bazy wiedzy przed wysłaniem zapytania do punktu końcowego przewidywania zapytań.

### <a name="ingesting-data-sources"></a>Pozyskiwanie źródeł danych

Pozyskiwane [źródła danych](knowledge-base.md)używane do tworzenia bazy wiedzy mogą być następujące:

* Publiczny adres URL
* Prywatny adres URL programu SharePoint
* Plik

Proces pozyskiwania umożliwia konwersję [obsługiwanych typów zawartości](content-types.md) na potrzeby promocji. Wszelkie dalsze edytowanie *odpowiedzi* odbywa się z użyciem promocji. Po utworzeniu bazy wiedzy można edytować [pary QNA](question-answer-set.md) w portalu QNA Maker z [tworzeniem tekstu sformatowanego](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Zagadnienia dotyczące formatu danych

Ze względu na to, że ostatni format pary QnA jest w promocji, zrozumienie, że [Pomoc techniczna](../reference-markdown-format.md) jest ważna.

Połączone obrazy muszą być dostępne z publicznego adresu URL, aby można je było wyświetlić w okienku testowania portalu QnA Maker oraz dowolnej aplikacji klienckiej, ponieważ QnA Maker nie zapewnia uwierzytelniania zawartości, w tym obrazów.

### <a name="bot-personality"></a>Bot osobowość

Dodaj bot osobowość do bazy wiedzy przy użyciu funkcji [Chit-Chat](../how-to/chit-chat-knowledge-base.md). Ta funkcja jest dostępna z odpowiedziami w określonym sygnale konwersacji, takim jak *profesjonalne* i *przyjazne*. Ta Chit — rozmowa jest udostępniana jako zestaw konwersacji, który ma całkowitą kontrolę do dodawania, edytowania i usuwania.

Jeśli Twój bot nawiązuje połączenie z bazą wiedzy, zaleca się bot osobowości. W przypadku łączenia się z kilkoma usługami, z których jedna jest baza wiedzy, można nadal korzystać z funkcji Chit-Chat w bazie wiedzy, ale należy sprawdzić, jak działa usługa bot, jeśli jest to poprawny projekt architektury używany przez użytkownika.

### <a name="conversation-flow-with-a-knowledge-base"></a>Przepływ konwersacji z bazą wiedzy

Przepływ konwersacji zwykle zaczyna się od powitania od użytkownika, takiego jak `Hi` lub `Hello` . Baza wiedzy może odpowiedzieć na ogólną odpowiedź, taką jak `Hi, how can I help you` , i może również udostępniać wybrane monity, aby kontynuować konwersację.

Należy zaprojektować przepływ konwersacji przy użyciu pętli, aby użytkownik wie, jak korzystać z bot i nie został porzucony przez bot w konwersacji. [Monity monitujące](../how-to/multiturn-conversation.md) zapewniają łączenie między parami QNA, które zezwalają na przepływ konwersacji.

### <a name="authoring-with-collaborators"></a>Tworzenie za pomocą współpracowników

Współpracownicy mogą być innymi programistami, którzy korzystają z pełnego stosu deweloperów aplikacji bazy wiedzy lub mogą być ograniczeni do samego tworzenia bazy wiedzy.

Tworzenie bazy wiedzy obsługuje kilka [uprawnień dostępu opartych na rolach](../how-to/collaborate-knowledge-base.md) , które są stosowane w Azure Portal, aby ograniczyć zakres możliwości współpracowników.

## <a name="integration-with-client-applications"></a>Integracja z aplikacjami klienckimi

Integracja z [aplikacjami klienckimi](integration-with-other-applications.md) oznacza Wysyłanie zapytań do punktu końcowego przewidywania środowiska uruchomieniowego. Zapytanie jest wysyłane do konkretnej bazy wiedzy z zestawem SDK lub żądaniem opartym na protokole REST do punktu końcowego aplikacji sieci Web QnA Maker.

Aby poprawnie uwierzytelnić żądanie klienta, aplikacja kliencka musi wysłać poprawne poświadczenia i identyfikator bazy wiedzy. Jeśli używasz Azure Bot Service, skonfiguruj ustawienie jako część konfiguracji bot w Azure Portal.

### <a name="conversation-flow-in-a-client-application"></a>Przepływ konwersacji w aplikacji klienckiej

Przepływ konwersacji w [aplikacji klienckiej](integration-with-other-applications.md), na przykład na platformie Azure bot, może wymagać funkcjonalności przed i po współdziałaniu z bazą wiedzy.

Jeśli aplikacja kliencka obsługuje przepływ konwersacji, należy zapewnić alternatywną metodę obsługi monitów lub Chit-Chit, zaprojektować te wczesne i upewnić się, że zapytanie używane w aplikacji klienckiej jest prawidłowo obsługiwane przez inną usługę lub wysłane do bazy wiedzy.

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>Wysyłanie między QnA Maker i Language Understanding (LUIS)

Aplikacja kliencka może udostępniać kilka funkcji, tylko jeden z nich jest odbierany przez bazę wiedzy. Inne funkcje nadal muszą zrozumieć tekst konwersacji i wyodrębnić jego znaczenie.

Typową architekturą aplikacji klienckich jest użycie jednocześnie QnA Maker i [Language Understanding (Luis)](../../LUIS/what-is-luis.md) . LUIS zapewnia klasyfikację tekstu i wyodrębnianie dla dowolnego zapytania, w tym do innych usług, podczas gdy QnA Maker zapewnia odpowiedzi z bazy wiedzy.

W [udostępnionej architekturze](../choose-natural-language-processing-service.md)wysyłanie między tymi dwiema usługami odbywa się przy użyciu narzędzia do [wysyłania](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) z platformy bot Framework.

### <a name="active-learning-from-a-client-application"></a>Aktywna nauka z aplikacji klienckiej

QnA Maker używa _aktywnego uczenia_ do ulepszania bazy wiedzy, sugerując pytania alternatywne do odpowiedzi. Aplikacja kliencka jest odpowiedzialna za część tej [aktywnej nauki](active-learning-suggestions.md). Aplikacja kliencka, za pomocą kolejnych zapytań, może określić, że odpowiedź zwrócona z bazy wiedzy nie była odpowiedzią, której szuka użytkownik, i określić lepszą odpowiedź. Aplikacja kliencka musi [wysłać te informacje z powrotem do bazy wiedzy](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) , aby zwiększyć jakość przewidywania.

### <a name="providing-a-default-answer"></a>Dostarczanie odpowiedzi domyślnej

Jeśli Twoja baza wiedzy nie znajdzie odpowiedzi, zwróci _domyślną odpowiedź_. Tę odpowiedź można skonfigurować w portalu QnA Maker, na stronie **Ustawienia** lub w [interfejsach API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Ta domyślna odpowiedź różni się od domyślnej odpowiedzi usługi Azure bot. Domyślna odpowiedź Azure bot jest konfigurowana w Azure Portal, dla bot, jako część ustawień konfiguracji i jest zwracana, gdy próg wyniku nie zostanie spełniony.

## <a name="prediction"></a>Prediction (Prognoza)

Prognoza jest odpowiedzią z bazy wiedzy i zawiera więcej informacji niż tylko odpowiedź. Aby uzyskać odpowiedź przewidywania zapytania, użyj [interfejsu API GeneateAnswer](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Wahania oceny przewidywania

Ocena może ulec zmianie w zależności od kilku czynników:

* Liczba odpowiedzi zażądanych w odpowiedzi na [GenerateAnswer](query-knowledge-base.md) z `top` właściwością
* Różne dostępne pytania alternatywne
* Filtrowanie metadanych
* Zapytanie wysyłane do programu `test` lub `production` bazy wiedzy

Istnieje [dwufazowe klasyfikowanie odpowiedzi](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
* Wyszukiwanie poznawcze pierwszej kolejności — aby odpowiedź zwracała się z Wyszukiwanie poznawcze, liczba _dozwolonych odpowiedzi_ musi być wystarczająco wysoka, że najlepsze odpowiedzi są zwracane przez wyszukiwanie poznawcze, aby mogły przejść do rangi QNA Maker
* QnA Maker — druga ranga — Zastosuj cechowania i uczenie maszynowe, aby określić najlepszą odpowiedź.

### <a name="service-updates"></a>Aktualizacje usług

Aktualizacje usługi są automatycznie zarządzane przez zastosowanie [najnowszych aktualizacji środowiska uruchomieniowego](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

### <a name="scaling-throughput-and-resiliency"></a>Skalowanie, przepływność i odporność

Skalowanie, przepływność i odporność są określane przez [zasoby platformy Azure](../how-to/set-up-qnamaker-service-azure.md), ich warstwy cenowe oraz wszystkie otaczające architektury, takie jak [Traffic Manager](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Analiza za pomocą Application Insights

Wszystkie zapytania do bazy wiedzy są przechowywane w Application Insights. Użyj naszych [najpopularniejszych zapytań](../how-to/get-analytics-knowledge-base.md) , aby zrozumieć metryki.

## <a name="development-lifecycle"></a>Cykl projektowania oprogramowania

[Cykl rozwoju](development-lifecycle-knowledge-base.md) bazy wiedzy odbywa się na bieżąco: edytowanie, testowanie i publikowanie bazy wiedzy.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Opracowywanie QnA Maker par na podstawie bazy wiedzy

[Pary QNA](question-answer-set.md) powinny być projektowane i tworzone w oparciu o użycie aplikacji klienckiej.

Każda para może zawierać:
* Filtrowanie metadanych przy wysyłaniu zapytań. Dzięki temu można oznakować pary QnA z dodatkowymi informacjami o źródle, zawartości, formacie i przeznaczeniu danych.
* Monity monitujące — należy określić ścieżkę w bazie wiedzy, aby użytkownik dotarł do odpowiedniej odpowiedzi.
* Pytania alternatywne — ważne jest, aby zezwolić na dopasowanie wyszukiwania do odpowiedzi z różnych form pytań. [Aktywne sugestie dotyczące uczenia](active-learning-suggestions.md) załączają się do innych pytań.

### <a name="devops-development"></a>Programowanie DevOps

Opracowywanie bazy wiedzy do wstawienia do potoku DevOps wymaga odizolowania bazy wiedzy podczas [testowania wsadowego](../quickstarts/batch-testing.md).

Baza wiedzy udostępnia indeks Wyszukiwanie poznawcze ze wszystkimi innymi bazami wiedzy w ramach zasobu QnA Maker. Baza wiedzy jest izolowana przez partycję, więc udostępnianie indeksu może spowodować różnice w wyniku porównywania z opublikowanym bazą wiedzy.

Aby można było korzystać z tego _samego wyniku_ w `test` `production` bazach wiedzy i, Izoluj zasób QNA Maker do pojedynczej bazy wiedzy. W tej architekturze zasób musi być aktywny tylko na czas trwania odizolowanego testu wsadowego.

## <a name="next-step"></a>Następny krok

* [Zasoby platformy Azure](../how-to/set-up-qnamaker-service-azure.md)
* [Pary pytań i odpowiedzi](question-answer-set.md)