---
title: Co nowego — rozumienie języka (LUIS)
description: Ten artykuł jest regularnie aktualizowany o wiadomości dotyczące interfejsu API rozpoznawania języka usług Wirtualnych platformy Azure.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 6334b157f68f09f2b165c6073ba63f45a0caf3c4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538022"
---
# <a name="whats-new-in-language-understanding"></a>Co nowego w zrozumieniu języka

Dowiedz się, co nowego w usłudze. Elementy te obejmują informacje o wersji, filmy, wpisy w blogu i inne rodzaje informacji. Zaznacz tę stronę w zakładce, aby być na bieżąco z usługą.

## <a name="release-notes"></a>Informacje o wersji

### <a name="march-2020"></a>Marzec 2020 r.

* Protokół TLS 1.2 jest teraz wymuszany dla wszystkich żądań HTTP do tej usługi. Aby uzyskać więcej informacji, zobacz [zabezpieczenia usług Azure Cognitive Services](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>4 listopada 2019 - Ignite

* Wideo — [zaawansowane modele zrozumienia języka naturalnego (NLU) przy użyciu usługi LUIS i azure cognitive services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Większa produktywność deweloperów
    * Ogólna dostępność naszego [punktu końcowego przewidywania V3](luis-migration-api-v3.md).
    * Możliwość importowania i eksportowania aplikacji w formacie .lu[(LUDown).](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown) To toruje drogę do skutecznego procesu ciągłej integracji/ciągłego wdrażania.
* Rozszerzenie języka
    * [arabski i hindi](luis-language-support.md) w publicznej wersji zapoznawczej.
* Wstępnie utworzone modele
    * [Wstępnie utworzone domeny są](luis-reference-prebuilt-domains.md) teraz ogólnie dostępne (GA)
    * Japońskie [wstępnie utworzone jednostki](luis-reference-prebuilt-entities.md#japanese-entity-support) — wiek, waluta, liczba i procent nie są obsługiwane w wersji 3.
    * Włoskie [wstępnie utworzone encje](luis-reference-prebuilt-entities.md#italian-entity-support) — wiek, waluta, wymiar, liczba i rozdzielczość procentowa zmienione z V2.
* Ulepszone środowisko użytkownika w [preview.luis.ai portalu](https://preview.luis.ai) — odnowione środowisko etykietowania umożliwiające tworzenie i debugowanie złożonych modeli. Wypróbuj samouczki portalu podglądu:
    * [Tylko intencje](tutorial-intents-only.md)
    * [Rozkładalna jednostka wyuczona maszynowo](tutorial-machine-learned-entity.md)
* Rozwijanie możliwości rozumienia języka - [tworzenie zaawansowanych modeli językowych](luis-concept-entity-types.md) przy mniejszym wysiłku.
* Zdefiniuj funkcje uczenia maszynowego na poziomie modelu i włącz modele, które mają być używane jako sygnały do innych modeli, na przykład przy użyciu jednostek jako funkcji intencji i innych jednostek.
* Nowe, [rozszerzone limity](luis-boundaries.md) - wyższe maksimum dla list fraz i zwrotów całkowitych, nowy model jako limity funkcji
* Wyodrębnij informacje z tekstu w formacie głębokiej struktury hierarchii, dzięki czemu aplikacje konwersacji są bardziej zaawansowane.

    ![obraz jednostki nauczanych maszynowo](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 września 2019 r.

* Zasób tworzenia platformy Azure — [migruj teraz](luis-migration-authoring.md).
    * 500 aplikacji na zasób platformy Azure
    * 100 wersji na aplikację
* Tureckie wsparcie dla wstępnie zbudowanych podmiotów
* Włoskie wsparcie dla datetimeV2

### <a name="july-23-2019"></a>23 lipca 2019 r.

* Aktualizowanie [tekstu aparatów rozpoznawania](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) do 1.2.3
    * Aparaty rozpoznawania wieku, temperatury, wymiaru i waluty w języku włoskim.
    * Poprawa rozpoznawania holiday w języku angielskim, aby poprawnie obliczyć daty oparte na Święcie Dziękczynienia.
    * Ulepszenia w języku francuskim DateTime w celu zmniejszenia fałszywych alarmów jednostek innych niż Data i non-Time.
    * Obsługa kalendarza/ szkoły / roku obrachunkowego i akronimów w języku angielskim DateRange.
    * Ulepszone rozpoznawanie numerów telefonicznych w języku chińskim i japońskim.
    * Ulepszona obsługa NumberRange w języku angielskim.
    * Usprawnienia wydajności.

### <a name="june-24-2019"></a>24 czerwca 2019 r.

* [Wstępnie skompilowana jednostka OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) do obsługi zamawiania, taka jak następny, poprzedni i ostatni. Tylko kultura angielska.

### <a name="may-6-2019---build-conference"></a>6 maja 2019 - //Build Conference

Na konferencji Build 2019 zostały wydane następujące funkcje:

* [Przewodnik po migracji interfejsu API w wersji Zapoznawczej systemu V3](luis-migration-api-v3.md)
* [Ulepszony pulpit nawigacyjny analizy](luis-how-to-use-dashboard.md)
* [Ulepszone wstępnie utworzone domeny](luis-reference-prebuilt-domains.md)
* [Encje listy dynamicznej](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Podmioty zewnętrzne](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogi

[Struktura botów](https://blog.botframework.com/)

## <a name="videos"></a>Filmy wideo

### <a name="2019-ignite-videos"></a>Filmy Ignite 2019

[Modele zaawansowanego rozumienia języka naturalnego (NLU) przy użyciu usługi LUIS i usługi Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Tworzenie filmów

[Jak używać sztucznej inteligencji konwersacyjnej platformy Azure do skalowania firmy dla następnej generacji](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Aktualizacje usług

[Anonse aktualizacji platformy Azure dla usług Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
