---
title: Co nowego — Language Understanding (LUIS)
description: Ten artykuł jest regularnie aktualizowany za pomocą wiadomości o interfejs API usługi Language Understanding usługi Azure Cognitive Services.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 02/16/2021
ms.openlocfilehash: bd70bb1a031b61e77fa84ff5567d95dc6d1e8788
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706745"
---
# <a name="whats-new-in-language-understanding"></a>Co nowego w Language Understanding

Dowiedz się, co nowego w usłudze. Te elementy obejmują informacje o wersji, klipy wideo, wpisy w blogu i inne rodzaje informacji. Oznacz Tę stronę zakładką, aby zapewnić aktualność usługi.

## <a name="release-notes"></a>Informacje o wersji

### <a name="january-2021"></a>Styczeń 2021 r.

* Interfejs API przewidywania v3 obsługuje teraz [interfejs API sprawdzania pisowni usługi Bing](luis-tutorial-bing-spellcheck.md).
* Portale regionalne (au.luis.ai i eu.luis.ai) zostały skonsolidowane w jeden portal i adres URL. Jeśli używasz jednego z tych portali, nastąpi automatyczne przekierowanie do luis.ai.

### <a name="december-2020"></a>Grudzień 2020 r.

* Wszyscy użytkownicy LUIS są zobowiązani do [migracji do zasobu tworzenia Luis](luis-migration-authoring.md)
* Nowe [punkty końcowe oceny](luis-how-to-batch-test.md#batch-testing-using-the-rest-api) , które umożliwiają przesyłanie testów wsadowych przy użyciu interfejsu API REST, oraz uzyskiwanie wyników dokładności dla zamiar i jednostek. Dostępne od punktu końcowego LUIS v 3.0 — wersja zapoznawcza.

### <a name="june-2020"></a>Czerwiec 2020 r.

* [Wersja Zapoznawcza 3,0](luis-migration-authoring-entities.md) Zestawie
    * Wersja 3.2.0-Preview. 3 — [.NET — NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Wersja 4.0.0-Preview. 3- [js-npm](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* Stosowanie praktyk DevOps z usługą LUIS
    * Pojęcia
        * [DevOps praktyk dla LUIS](luis-concept-devops-sourcecontrol.md)
        * [Przepływy pracy ciągłej integracji i ciągłego dostarczania dla LUIS DevOps](luis-concept-devops-automation.md)
        * [Testowanie pod kątem LUIS DevOps](luis-concept-devops-testing.md)
    * Porady
        * [Zastosuj DevOps do tworzenia aplikacji LUIS przy użyciu akcji usługi GitHub](luis-how-to-devops-with-github.md)
    * [Pełny kod repozytorium GitHub](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>Maj 2020-Build

* Wydane jako **ogólnie dostępne** (ga):
    * [Kontener Language Understanding](luis-container-howto.md)
    * Portal w wersji zapoznawczej promowany dla [bieżącego portalu](https://www.luis.ai), [poprzedni](https://previous.luis.ai) Portal nadal jest dostępny
    * Nowe środowisko tworzenia i etykietowania jednostek uczenia maszynowego
    * [Proces uaktualniania](migrate-from-composite-entity.md) z jednostek złożonych i prostych do jednostek uczenia maszynowego
    * [Ustawianie](how-to-application-settings-portal.md) obsługi normalizowania wariantów wyrazów
* Zmiany wersji zapoznawczej interfejsu API tworzenia
    * Schemat aplikacji 7. x dla zagnieżdżonych jednostek uczenia maszynowego
    * [Migracja do wymaganej funkcji](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Nowe zasoby dla deweloperów
    * [Narzędzia do ciągłej integracji](developer-reference-resource.md#continuous-integration-tools)
    * Warsztat — uczenie najlepszych rozwiązań dla [ _naturalnego Language Understanding_ (NLU) przy użyciu Luis](developer-reference-resource.md#workshops)
* [Klucze zarządzane przez klienta](./encrypt-data-at-rest.md) — Szyfruj wszystkie dane używane w programie Luis przy użyciu własnego klucza
* [AI show](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (wideo) — Zobacz nowe funkcje w Luis



### <a name="march-2020"></a>Marzec 2020 r.

* Protokół TLS 1,2 jest teraz wymuszany dla wszystkich żądań HTTP do tej usługi. Aby uzyskać więcej informacji, zobacz [Azure Cognitive Services Security](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4 listopada 2019 — zapłon

* [Language Understanding zaawansowane modele wideo (NLU) z Luis i Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Ulepszona produktywność dla deweloperów
    * Ogólna dostępność naszego [punktu końcowego przewidywania v3](luis-migration-api-v3.md).
    * Możliwość importowania i eksportowania aplikacji przy użyciu `.lu` formatu ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). To paves sposób na efektywny proces ciągłej integracji/ciągłego dostarczania.
* Rozwinięcie języka
    * [Język arabski i hindi](luis-language-support.md) w publicznej wersji zapoznawczej.
* Wstępnie utworzone modele
    * [Wstępnie skompilowane domeny](luis-reference-prebuilt-domains.md) są teraz ogólnie dostępne (ga)
    * Japońskie wstępnie [skompilowane jednostki](luis-reference-prebuilt-entities.md#japanese-entity-support) — wiek, waluta, liczba i procent nie są obsługiwane w wersji 3.
    * Włoskie wstępnie [skompilowane jednostki](luis-reference-prebuilt-entities.md#italian-entity-support) — wiek, waluta, wymiar, numer i rozdzielczość procentową zmieniono z wersji 2.
* Ulepszone środowisko użytkownika w [portalu Preview.Luis.AI](https://preview.luis.ai) — środowisko etykietowania odnowionych umożliwiające tworzenie i debugowanie modeli złożonych. Wypróbuj samouczki dotyczące portalu w wersji zapoznawczej:
    * [Tylko intencje](tutorial-intents-only.md)
    * [Odtworzenie jednostki uczenia maszynowego](tutorial-machine-learned-entity.md)
* Dokładniejsze Omówienie języka — [tworzenie zaawansowanych modeli języków](luis-concept-entity-types.md) z mniejszym nakładem pracy.
* Zdefiniuj funkcje uczenia maszynowego na poziomie modelu i Włącz modele, które mają być używane jako sygnały do innych modeli, na przykład użycie jednostek jako funkcji do intencji i innych jednostek.
* Nowe, rozwinięte [limity](luis-limits.md) — większa liczba list fraz i łączna liczba fraz, nowy model jako limity funkcji
* Wyodrębnij informacje z tekstu w formacie głębokiej struktury hierarchii, co sprawia, że aplikacje do konwersacji są bardziej wydajne.

    ![obraz jednostki uczenia maszynowego](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 września, 2019

* Tworzenie zasobu platformy Azure — [Migrowanie teraz](luis-migration-authoring.md).
    * 500 aplikacji na zasób platformy Azure
    * 100 wersji na aplikację
* Wsparcie tureckie dla wstępnie utworzonych jednostek
* Obsługa języka włoskiego dla datetimeV2

### <a name="july-23-2019"></a>23 lipca 2019

* Aktualizowanie [aparatów rozpoznawania tekstu](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) do 1.2.3
    * Aparaty rozpoznawania wieku, temperatury, wymiaru i waluty w języku włoskim.
    * Poprawa rozpoznawania dni w języku angielskim w celu poprawnego obliczenia dat opartych na świętach.
    * Ulepszenia w francuskim elemencie DateTime, aby zmniejszyć liczbę fałszywych wartości niebędących nieaktualnymi i niebędącymi jednostkami czasu.
    * Obsługa kalendarza/szkoły/roku obrachunkowego i akronimów w języku angielskim DateRange.
    * Ulepszone rozpoznawanie telefonu w języku chińskim i japońskim.
    * Ulepszona obsługa NumberRange w języku angielskim.
    * Usprawnienia wydajności.

### <a name="june-24-2019"></a>24 czerwca 2019

* [OrdinalV2 prekompilowana jednostka](luis-reference-prebuilt-ordinal-v2.md) do obsługi kolejności takich jak Next, Previous i Last. Tylko kultura w języku angielskim.

### <a name="may-6-2019---build-conference"></a>6 maja 2019 — Konferencja Build

Następujące funkcje zostały wydane na konferencji Build 2019:

* [Przewodnik migracji interfejsu API v3](luis-migration-api-v3.md)
* [Udoskonalony pulpit nawigacyjny analizy](luis-how-to-use-dashboard.md)
* [Ulepszone prebudowane domeny](luis-reference-prebuilt-domains.md)
* [Jednostki listy dynamicznej](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Jednostki zewnętrzne](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogi

[Struktura botów](https://blog.botframework.com/)

## <a name="videos"></a>Filmy wideo

### <a name="2019-ignite-videos"></a>wideo o zapłonie 2019

[Zaawansowane modele naturalnych Language Understanding (NLU) korzystające z LUIS i platformy Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>wideo kompilacji 2019

[Jak skalować swoją firmę do nowej generacji przy użyciu funkcji Azure Conversation AI](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Aktualizacje usług

[Anonse aktualizacji platformy Azure dla Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)