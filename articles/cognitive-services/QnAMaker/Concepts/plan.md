---
title: Zaplanuj aplikację QnA Maker
description: Dowiedz się, jak planować aplikację QnA Makerową. Dowiedz się, w jaki sposób QnA Maker działa i współdziała z innymi usługami platformy Azure, a niektóre koncepcje bazy wiedzy.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 048b53186aa0be388d9d801cd6590d4295a4faa7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353174"
---
# <a name="plan-your-qna-maker-app"></a>Planowanie aplikacji QnA Maker

Aby zaplanować aplikację QnA Maker, musisz zrozumieć, jak QnA Maker działa i współdziała z innymi usługami platformy Azure. Należy również mieć pełny opanujesz pojęć bazy wiedzy.

## <a name="azure-resources"></a>Zasoby platformy Azure

Każdy [zasób platformy Azure](azure-resources.md#resource-purposes) utworzony przy użyciu QNA Maker ma określony cel. Każdy zasób ma własne przeznaczenie, limity i [warstwę cenową](azure-resources.md#pricing-tier-considerations). Ważne jest, aby zrozumieć funkcję tych zasobów, dzięki czemu można użyć tej wiedzy w procesie planowania.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

| Zasób | Przeznaczenie |
|--|--|
| Zasób [QNA Maker](azure-resources.md#qna-maker-resource) | Tworzenie i prognozowanie zapytań |
| Zasób [Wyszukiwanie poznawcze](azure-resources.md#cognitive-search-resource) | Przechowywanie i wyszukiwanie danych |
| Zasób [usługi App Service zasób i plan aplikacji](azure-resources.md#app-service-and-app-service-plan) | Punkt końcowy przewidywania zapytania |
| Zasób [Application Insights](azure-resources.md#application-insights) | Dane telemetryczne przewidywania zapytań |

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

| Zasób | Przeznaczenie |
|--|--|
| Zasób [QNA Maker](azure-resources.md#qna-maker-resource) | Tworzenie, przepowiadanie zapytania i Telemetria|
| Zasób [Wyszukiwanie poznawcze](azure-resources.md#cognitive-search-resource) | Przechowywanie i wyszukiwanie danych |

---
### <a name="resource-planning"></a>Planowanie zasobów

Warstwa Bezpłatna, `F0` każdy zasób działa i może zapewnić zarówno środowisko tworzenia, jak i prognozowania zapytań. Możesz użyć tej warstwy, aby poznać tworzenie i prognozowanie zapytań. Po przejściu do scenariusza produkcyjnego lub na żywo Oceń wybrane zasoby.

#### <a name="qna-maker-resource"></a>Zasób QnA Maker

Pojedynczy zasób QnA Maker może obsługiwać więcej niż jedną bazę wiedzy. Liczba baz wiedzy zależy od ilości obsługiwanych indeksów Wyszukiwanie poznawcze warstwy cenowej. Dowiedz się więcej na temat [relacji indeksów z bazami wiedzy](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Rozmiar bazy wiedzy i przepływność

Podczas tworzenia rzeczywistej aplikacji Zaplanuj wystarczającą ilość zasobów dla rozmiaru bazy wiedzy i oczekiwanego żądania prognozowania zapytań.

Rozmiar bazy wiedzy jest kontrolowany przez:
* Limity warstwy cenowej [zasobów wyszukiwanie poznawcze](../../../search/search-limits-quotas-capacity.md)
* [Limity QnA Maker](../limits.md)

Żądanie prognozowania zapytań bazy wiedzy jest kontrolowane przez plan aplikacji sieci Web i aplikację sieci Web. Zapoznaj się z [zalecanymi ustawieniami](azure-resources.md#recommended-settings) w celu zaplanowania warstwy cenowej.

### <a name="resource-sharing"></a>Udostępnianie zasobów

Jeśli masz już niektóre z tych zasobów, możesz rozważyć udostępnianie zasobów. Zapoznaj się z informacjami o zasobach, które [mogą być współużytkowane](azure-resources.md#share-services-with-qna-maker) przez zrozumienie, że udostępnianie zasobów jest zaawansowanym scenariuszem.

Wszystkie bazy wiedzy utworzone w tym samym QnA Maker zasobów współużytkują ten sam punkt końcowy przewidywania zapytania **testowego** .

### <a name="understand-the-impact-of-resource-selection"></a>Zrozumienie wpływu wyboru zasobów

Wybór wybranego zasobu oznacza, że bazy wiedzy pomyślnie odpowiadają przewidywania zapytań.

Jeśli baza wiedzy nie działa prawidłowo, zwykle jest to problem z nieprawidłowym zarządzaniem zasobami.

Niewłaściwy wybór zasobów wymaga zbadania, aby określić, który [zasób musi zostać zmieniony](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Bazy wiedzy

Baza wiedzy jest bezpośrednio związana z jej zasobem QnA Maker. Przechowuje pary pytań i odpowiedzi (QnA), które są używane do odpowiedzi na żądania prognozowania zapytań.

### <a name="language-considerations"></a>Zagadnienia dotyczące języka

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Pierwsza baza wiedzy utworzona w ramach zasobu QnA Maker ustawia język dla zasobu. Dla zasobu QnA Maker można korzystać tylko z jednego języka.

Możesz utworzyć strukturę zasobów QnA Maker według języka lub użyć [translatora](../../translator/translator-info-overview.md) do zmiany zapytania z innego języka do języka bazy wiedzy przed wysłaniem zapytania do punktu końcowego przewidywania zapytań.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

Teraz możesz mieć bazy wiedzy w różnych językach w ramach tego samego QnA Makergo zasobu. Podczas tworzenia pierwszej bazy wiedzy możesz zdecydować, czy chcesz używać zasobu dla baz wiedzy w jednym języku, czy w wielu językach.

![QnA Maker zarządzane (wersja zapoznawcza) wybór bazy wiedzy dla wielu języków](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

> [!NOTE]
> W przypadku włączenia ustawień języka dla bazy wiedzy nie można utworzyć tylu baz wiedzy w zasobie QnA Maker. Aby uzyskać [więcej informacji na temat ograniczeń dotyczących ustawień języka](./azure-resources.md).

---

### <a name="ingest-data-sources"></a>Pozyskiwanie źródeł danych

Można użyć jednego z następujących pozyskiwanych [źródeł danych](../index.yml) do utworzenia bazy wiedzy:

* Publiczny adres URL
* Prywatny adres URL programu SharePoint
* Plik

Proces pozyskiwania umożliwia konwersję [obsługiwanych typów zawartości](../index.yml) na potrzeby promocji. Wszelkie dalsze edytowanie *odpowiedzi* odbywa się z użyciem promocji. Po utworzeniu bazy wiedzy można edytować [pary QNA](question-answer-set.md) w portalu QNA Maker przy użyciu [tekstu sformatowanego](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer).

### <a name="data-format-considerations"></a>Zagadnienia dotyczące formatu danych

Ze względu na to, że ostatni format pary QnA jest w promocji, ważne jest, aby zrozumieć [wsparcie promocji](../reference-markdown-format.md).

Połączone obrazy muszą być dostępne z publicznego adresu URL, aby można je było wyświetlić w okienku testów portalu QnA Maker lub w aplikacji klienckiej. QnA Maker nie zapewnia uwierzytelniania zawartości, w tym obrazów.

### <a name="bot-personality"></a>Bot osobowość

Dodaj bot osobowość do bazy wiedzy przy użyciu funkcji [Chit-Chat](../how-to/chit-chat-knowledge-base.md). Ta funkcja jest dostępna z odpowiedziami w określonym sygnale konwersacji, takim jak *profesjonalne* i *przyjazne*. Ta Chit — rozmowa jest udostępniana jako zestaw konwersacji, który ma całkowitą kontrolę do dodawania, edytowania i usuwania.

Jeśli Twój bot nawiązuje połączenie z bazą wiedzy, zaleca się bot osobowości. Możesz użyć funkcji Chit-Chat w bazie wiedzy nawet w przypadku łączenia się z innymi usługami, ale należy sprawdzić, jak działa usługa bot, jeśli jest to poprawny projekt architektoniczny do użytku.

### <a name="conversation-flow-with-a-knowledge-base"></a>Przepływ konwersacji z bazą wiedzy

Przepływ konwersacji zwykle zaczyna się od powitania od użytkownika, takiego jak `Hi` lub `Hello` . Twoja baza wiedzy może odpowiedzieć na ogólną odpowiedź, taką jak `Hi, how can I help you` , i może również udostępnić wybrane monity monitujące, aby kontynuować konwersację.

Należy zaprojektować przepływ konwersacji przy użyciu pętli, aby użytkownik wie, jak korzystać z bot i nie został porzucony przez bot w konwersacji. [Monity monitujące](../how-to/multiturn-conversation.md) zapewniają łączenie między parami QNA, które zezwalają na przepływ konwersacji.

### <a name="authoring-with-collaborators"></a>Tworzenie za pomocą współpracowników

Współpracownicy mogą być innymi programistami, którzy korzystają z pełnego stosu deweloperów aplikacji bazy wiedzy lub mogą być ograniczeni do samego tworzenia bazy wiedzy.

Tworzenie bazy wiedzy obsługuje kilka [uprawnień dostępu opartych na rolach](../index.yml) , które są stosowane w Azure Portal, aby ograniczyć zakres możliwości współpracowników.

## <a name="integration-with-client-applications"></a>Integracja z aplikacjami klienckimi

Integrację z [aplikacjami klienckimi](../index.yml) można przeprowadzić, wysyłając zapytanie do punktu końcowego przewidywania środowiska uruchomieniowego. Zapytanie jest wysyłane do konkretnej bazy wiedzy z zestawem SDK lub żądaniem opartym na protokole REST do punktu końcowego aplikacji sieci Web QnA Maker.

Aby poprawnie uwierzytelnić żądanie klienta, aplikacja kliencka musi wysłać poprawne poświadczenia i identyfikator bazy wiedzy. Jeśli używasz Azure Bot Service, skonfiguruj te ustawienia jako część konfiguracji bot w Azure Portal.

### <a name="conversation-flow-in-a-client-application"></a>Przepływ konwersacji w aplikacji klienckiej

Przepływ konwersacji w [aplikacji klienckiej](../index.yml), na przykład na platformie Azure bot, może wymagać funkcjonalności przed i po współdziałaniu z bazą wiedzy.

Czy aplikacja kliencka obsługuje przepływ konwersacji, dostarczając alternatywny sposób obsługi monitów uzupełniających lub takich jak Chit-Chit? Jeśli tak, Zaprojektuj te wczesne i upewnij się, że zapytanie aplikacji klienta jest prawidłowo obsługiwane przez inną usługę lub w przypadku wysłania do bazy wiedzy.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>Wysyłanie między QnA Maker i Language Understanding (LUIS)

Aplikacja kliencka może udostępniać kilka funkcji, tylko jeden z nich jest odbierany przez bazę wiedzy. Inne funkcje nadal muszą zrozumieć tekst konwersacji i wyodrębnić jego znaczenie.

Typową architekturą aplikacji klienckich jest użycie jednocześnie QnA Maker i [Language Understanding (Luis)](../../LUIS/what-is-luis.md) . LUIS zapewnia klasyfikację tekstu i wyodrębnianie dla dowolnego zapytania, w tym do innych usług. QnA Maker oferuje odpowiedzi z bazy wiedzy.

W takim scenariuszu [udostępnionej architektury](../choose-natural-language-processing-service.md) wysyłanie między tymi dwiema usługami odbywa się za pomocą narzędzia do [wysyłania](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) z platformy bot Framework.

### <a name="active-learning-from-a-client-application"></a>Aktywna nauka z aplikacji klienckiej

QnA Maker używa _aktywnego uczenia_ do ulepszania bazy wiedzy, sugerując pytania alternatywne do odpowiedzi. Aplikacja kliencka jest odpowiedzialna za część tej [aktywnej nauki](active-learning-suggestions.md). Dzięki zapytaniami konwersacji aplikacja kliencka może określić, że baza wiedzy zwróciła odpowiedź, która nie jest przydatna dla użytkownika i może określić lepszą odpowiedź. Aplikacja kliencka musi [wysłać te informacje z powrotem do bazy wiedzy](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) , aby zwiększyć jakość przewidywania.

### <a name="providing-a-default-answer"></a>Dostarczanie odpowiedzi domyślnej

Jeśli Twoja baza wiedzy nie znajdzie odpowiedzi, zwróci _domyślną odpowiedź_. Tę odpowiedź można skonfigurować na stronie **Ustawienia** w portalu QNA Maker lub w [interfejsach API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body).

Ta domyślna odpowiedź różni się od domyślnej odpowiedzi usługi Azure bot. Domyślną odpowiedź dla bot platformy Azure można skonfigurować w Azure Portal w ramach ustawień konfiguracji. Jest zwracana, gdy próg wyniku nie jest spełniony.

## <a name="prediction"></a>Przewidywanie

Prognoza jest odpowiedzią z bazy wiedzy i zawiera więcej informacji niż tylko odpowiedź. Aby uzyskać odpowiedź przewidywania zapytania, użyj [interfejsu API GenerateAnswer](query-knowledge-base.md).

### <a name="prediction-score-fluctuations"></a>Wahania oceny przewidywania

Ocena może ulec zmianie w zależności od kilku czynników:

* Liczba odpowiedzi zażądanych w odpowiedzi na [GenerateAnswer](query-knowledge-base.md) z `top` właściwością
* Różne dostępne pytania alternatywne
* Filtrowanie metadanych
* Zapytanie wysyłane do programu `test` lub `production` bazy wiedzy

Istnieje [dwufazowe klasyfikowanie odpowiedzi](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
- Wyszukiwanie poznawcze — pierwsza ranga. Ustaw liczbę _odpowiedzi_ , która jest wystarczająco duża, że najlepsze odpowiedzi są zwracane przez wyszukiwanie poznawcze, a następnie przekazana do rangi QNA Maker.
- QnA Maker — druga ranga. Zastosuj cechowania i uczenie maszynowe, aby określić najlepszą odpowiedź.

### <a name="service-updates"></a>Aktualizacje usług

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Zastosuj [najnowsze aktualizacje środowiska uruchomieniowego](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) , aby automatycznie zarządzać aktualizacjami usługi.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

W QnA Maker Managed (wersja zapoznawcza) środowisko uruchomieniowe jest zarządzane przez usługę QnA Maker. Dlatego aktualizacje usługi nie mają zastosowania.

---

### <a name="scaling-throughput-and-resiliency"></a>Skalowanie, przepływność i odporność

Skalowanie, przepływność i odporność są określane przez [zasoby platformy Azure](../how-to/set-up-qnamaker-service-azure.md), ich warstwy cenowe oraz wszystkie otaczające architektury, takie jak [Traffic Manager](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager).

### <a name="analytics-with-application-insights"></a>Analiza za pomocą Application Insights

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)

Wszystkie zapytania do bazy wiedzy są przechowywane w Application Insights. Użyj naszych [najpopularniejszych zapytań](../how-to/get-analytics-knowledge-base.md) , aby zrozumieć metryki.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)

W zarządzanym wdrożeniu dane telemetryczne są oferowane w ramach [usługi Azure monitor](../../../azure-monitor/index.yml). Użyj naszych [najpopularniejszych zapytań](../how-to/get-analytics-knowledge-base.md) , aby zrozumieć metryki.


---

## <a name="development-lifecycle"></a>Cykl projektowania oprogramowania

[Cykl rozwoju](development-lifecycle-knowledge-base.md) bazy wiedzy odbywa się na bieżąco: edytowanie, testowanie i publikowanie bazy wiedzy.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>Opracowywanie QnA Maker par na podstawie bazy wiedzy

[Pary QNA](question-answer-set.md) powinny być projektowane i tworzone w oparciu o użycie aplikacji klienckiej.

Każda para może zawierać:
* Filtrowanie metadanych w przypadku wykonywania zapytań w celu umożliwienia znakowania par QnA z dodatkowymi informacjami o źródle, zawartości, formacie i przeznaczeniu danych.
* Monity uzupełniające — pomaga określić ścieżkę w bazie wiedzy, aby użytkownik dotarł do odpowiedniej odpowiedzi.
* Pytania alternatywne — ważne, aby zezwolić na dopasowanie wyszukiwania do odpowiedzi z różnych form pytań. [Aktywne sugestie dotyczące uczenia](active-learning-suggestions.md) załączają się do innych pytań.

### <a name="devops-development"></a>Programowanie DevOps

Opracowywanie bazy wiedzy do wstawienia do potoku DevOps wymaga odizolowania bazy wiedzy podczas [testowania wsadowego](../index.yml).

Baza wiedzy udostępnia indeks Wyszukiwanie poznawcze ze wszystkimi innymi bazami wiedzy w ramach zasobu QnA Maker. Baza wiedzy jest izolowana przez partycję, więc udostępnianie indeksu może spowodować różnice w wyniku porównywania z opublikowanym bazą wiedzy.

Aby uzyskać ten _sam wynik_ w `test` `production` bazach wiedzy i, Izoluj zasób QNA Maker do pojedynczej bazy wiedzy. W tej architekturze zasób musi tylko mieć wartość Live, tak długo, jak odizolowany test wsadowy.

## <a name="next-steps"></a>Następne kroki

* [Zasoby platformy Azure](../how-to/set-up-qnamaker-service-azure.md)
* [Pary pytań i odpowiedzi](question-answer-set.md)