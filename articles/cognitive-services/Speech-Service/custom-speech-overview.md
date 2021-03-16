---
title: Przegląd Custom Speech — usługa mowy
titleSuffix: Azure Cognitive Services
description: Custom Speech to zestaw narzędzi online, które umożliwiają ocenę i poprawienie dokładności mowy na tekst Microsoft dla aplikacji, narzędzi i produktów.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: 13bf0f2430e0d58dd9ef28061aad897acf94ac3f
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493054"
---
# <a name="what-is-custom-speech"></a>Czym jest usługa Custom Speech?

[Custom Speech](https://aka.ms/customspeech) to zestaw narzędzi opartych na interfejsie użytkownika, który umożliwia ocenę i ulepszenie dokładności mowy na tekst Microsoft dla aplikacji i produktów. Wszystkie potrzebne do rozpoczęcia pracy to kilku z testowanych plików audio. Aby rozpocząć tworzenie niestandardowego środowiska zamiany mowy na tekst, postępuj zgodnie z linkami w tym artykule.

## <a name="whats-in-custom-speech"></a>Co znajduje się w Custom Speech?

Przed rozpoczęciem pracy z Custom Speech musisz mieć konto platformy Azure i subskrypcję usługi mowy. Po utworzeniu konta możesz przygotować swoje dane, wyszkolić i przetestować modele, sprawdzić jakość rozpoznawania, oszacować dokładność i ostatecznie wdrożyć i korzystać z niestandardowego modelu zamiany mowy na tekst.

Ten diagram przedstawia elementy wchodzące w skład [obszaru Custom Speech w programie Speech Studio](https://aka.ms/customspeech). Skorzystaj z poniższych linków, aby dowiedzieć się więcej o każdym z kroków.

![Diagram, który wyróżnia składniki wchodzące w skład obszaru Custom Speech w programie Speech Studio.](./media/custom-speech/custom-speech-overview.png)

1. [Subskrybuj i Utwórz projekt](#set-up-your-azure-account). Utwórz konto platformy Azure i zasubskrybuj usługę mowy. Ta ujednolicona subskrypcja zapewnia dostęp do funkcji zamiany mowy na tekst, zamiany tekstu na mowę, tłumaczenia mowy i [mowy Studio](https://speech.microsoft.com/customspeech). Następnie użyj subskrypcji usługi mowy, aby utworzyć pierwszy projekt Custom Speech.

1. [Przekaż dane testowe](./how-to-custom-speech-test-and-train.md). Przekazywanie danych testowych (plików audio) w celu oszacowania oferty zamiany mowy na tekst firmy Microsoft dla aplikacji, narzędzi i produktów.

1. [Inspekcja jakości rozpoznawania](how-to-custom-speech-inspect-data.md). Użyj programu [Speech Studio](https://speech.microsoft.com/customspeech) , aby odtworzyć przekazane audio i sprawdzić jakość rozpoznawania mowy danych testowych. Aby uzyskać miary ilościowe, zobacz [Sprawdzanie danych](how-to-custom-speech-inspect-data.md).

1. [Oceń i popraw dokładność](how-to-custom-speech-evaluate-data.md). Oceń i popraw dokładność modelu zamiany mowy na tekst. Program [Speech Studio](https://speech.microsoft.com/customspeech) dostarczy *Współczynnik błędów wyrazu*, którego można użyć do określenia, czy wymagane jest dodatkowe szkolenie. Jeśli dokładność jest zadowalająca, można używać interfejsów API usługi mowy bezpośrednio. Jeśli chcesz poprawić dokładność przez średnią wartość z zakresu od 5% do 20%, Użyj karty **szkolenie** w portalu, aby przekazać dodatkowe dane szkoleniowe, takie jak transkrypcje ze znakami ludzkimi i powiązane teksty.

1. [Uczenie i wdrażanie modelu](how-to-custom-speech-train-model.md). Popraw dokładność modelu zamiany mowy na tekst, dostarczając Zapisano transkrypcje (od 10 do 1 000 godzin) i powiązanego tekstu (<200 MB) wraz z danymi testu dźwiękowego. Te dane ułatwiają uczenie modelu zamiany mowy na tekst. Po przeprowadzeniu szkolenia należy ponownie przetestować. Jeśli wynik jest zadowalający, możesz wdrożyć model w niestandardowym punkcie końcowym.

## <a name="set-up-your-azure-account"></a>Skonfiguruj swoje konto platformy Azure

Musisz mieć subskrypcję usługi Azure Account i Speech Service, aby można było użyć programu [Speech Studio](https://speech.microsoft.com/customspeech) do utworzenia modelu niestandardowego. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Pamiętaj, aby utworzyć subskrypcję standardową (S0). Bezpłatne subskrypcje (F0) nie są obsługiwane.

Jeśli planujesz uczenie modelu niestandardowego z **danymi audio**, wybierz jeden z następujących regionów, w których jest dostępny dedykowany sprzęt do szkoleń. Pozwala to skrócić czas potrzebny do uczenia modelu i umożliwia korzystanie z większej liczby materiałów audio na potrzeby szkoleń. W tych regionach usługa mowy będzie korzystać z maksymalnie 20 godzin korzystania z dźwięku na potrzeby szkolenia; w innych regionach zostanie użyta tylko do 8 godzin.

* Australia Wschodnia
* Kanada Środkowa
* Indie Środkowe
* East US
* Wschodnie stany USA 2
* Północno-środkowe stany USA
* Europa Północna
* South Central US
* Southeast Asia
* Południowe Zjednoczone Królestwo
* US Gov Arizona
* US Gov Wirginia
* West Europe
* Zachodnie stany USA 2

Po utworzeniu konta platformy Azure i subskrypcji usługi mowy należy zalogować się do programu [Speech Studio](https://speech.microsoft.com/customspeech) i połączyć swoją subskrypcję.

1. Zaloguj się do programu [Speech Studio](https://aka.ms/custom-speech).
1. Wybierz subskrypcję, której chcesz używać, i Utwórz projekt mowy.
1. Jeśli chcesz zmodyfikować swoją subskrypcję, wybierz przycisk koło zębate w górnym menu.

## <a name="how-to-create-a-project"></a>Jak utworzyć projekt

Zawartość, taka jak dane, modele, testy i punkty końcowe, są zorganizowane w *projekty* w programie [Speech Studio](https://speech.microsoft.com/customspeech). Każdy projekt jest specyficzny dla domeny i kraju/języka. Na przykład możesz utworzyć projekt dla centrów wywołań, które używają języka angielskiego w Stany Zjednoczone.

Aby utworzyć swój pierwszy projekt, wybierz opcję **Zamiana mowy na tekst/niestandardowa mowy**, a następnie wybierz pozycję **Nowy projekt**. Postępuj zgodnie z instrukcjami wyświetlanymi przez kreatora, aby utworzyć projekt. Po utworzeniu projektu powinny zostać wyświetlone cztery karty: **dane**, **testowanie**, **szkolenia** i **wdrożenia**. Skorzystaj z linków w [sekcji Następne kroki](#next-steps) , aby dowiedzieć się, jak korzystać z każdej karty.

> [!IMPORTANT]
> Ostatnio Zaktualizowano [mowę programu Speech Studio](https://aka.ms/custom-speech) "Custom Speech Portal". Jeśli utworzono poprzednie dane, modele, testy i opublikowane punkty końcowe w portalu CRIS.ai lub za pomocą interfejsów API, należy utworzyć nowy projekt w nowym portalu, aby połączyć się ze starymi jednostkami.

## <a name="model-and-endpoint-lifecycle"></a>Cykl życia modelu i punktu końcowego

Starsze modele zazwyczaj stają się mniej przydatne w miarę upływu czasu, ponieważ najnowszy model ma zwykle wyższą dokładność. W związku z tym modele podstawowe oraz modele niestandardowe i punkty końcowe utworzone za pomocą portalu podlegają wygaśnięciu po 1 roku na dostosowanie i 2 lata do dekodowania. Szczegółowy opis znajduje się w artykule [dotyczącym cyklu życia modelu i punktu końcowego](./how-to-custom-speech-model-and-endpoint-lifecycle.md) .

## <a name="next-steps"></a>Następne kroki

* [Przygotowywanie i testowanie danych](./how-to-custom-speech-test-and-train.md)
* [Inspekcja danych](how-to-custom-speech-inspect-data.md)
* [Oceń i popraw dokładność modelu](how-to-custom-speech-evaluate-data.md)
* [Trenowanie i wdrażanie modelu](how-to-custom-speech-train-model.md)
