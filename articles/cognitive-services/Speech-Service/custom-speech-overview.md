---
title: Przegląd Custom Speech — usługa mowy
titleSuffix: Azure Cognitive Services
description: Custom Speech to zestaw narzędzi online, dzięki którym można oszacować i poprawić dokładność zamiany mowy na tekst dla aplikacji, narzędzi i produktów. Wszystkie te rzeczy są potrzebne do rozpoczęcia pracy z kilkuem plików audio. Skorzystaj z poniższych linków, aby rozpocząć tworzenie niestandardowego środowiska zamiany mowy na tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 6dbe97d615753f0a90c8ba80aa7afa6dafa15eb2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658472"
---
# <a name="what-is-custom-speech"></a>Czym jest usługa Custom Speech?

[Custom Speech](https://aka.ms/customspeech) to zestaw narzędzi opartych na interfejsie użytkownika, które umożliwiają ocenę i ulepszanie dokładności zamiany mowy na tekst dla aplikacji i produktów firmy Microsoft. Wszystkie te rzeczy są potrzebne do rozpoczęcia pracy z kilkuem plików audio. Skorzystaj z poniższych linków, aby rozpocząć tworzenie niestandardowego środowiska zamiany mowy na tekst.

## <a name="whats-in-custom-speech"></a>Co znajduje się w Custom Speech?

Przed rozpoczęciem pracy z Custom Speech musisz mieć konto platformy Azure i subskrypcję usługi mowy. Po otrzymaniu konta możesz przygotować swoje dane, wyszkolić i przetestować modele, sprawdzić jakość rozpoznawania, oszacować dokładność i ostatecznie wdrożyć i korzystać z niestandardowego modelu zamiany mowy na tekst.

Ten diagram przedstawia elementy wchodzące w skład [portalu Custom Speech](https://aka.ms/customspeech). Skorzystaj z poniższych linków, aby dowiedzieć się więcej o każdym z kroków.

![Wyróżnia różne składniki wchodzące w skład portalu Custom Speech.](./media/custom-speech/custom-speech-overview.png)

1. [Subskrybowanie i tworzenie projektu](#set-up-your-azure-account) — Tworzenie konta platformy Azure i Subskrybowanie usługi Speech. Ta ujednolicona subskrypcja zapewnia dostęp do funkcji zamiany mowy na tekst, zamiany tekstu na mowę, tłumaczenia mowy i [portalu Custom Speech](https://speech.microsoft.com/customspeech). Następnie za pomocą subskrypcji usługi mowy Utwórz pierwszy projekt Custom Speech.

1. [Przekazywanie danych testowych](how-to-custom-speech-test-data.md) — przekazywanie danych testowych (plików audio) w celu oszacowania oferty zamiany mowy na tekst firmy Microsoft dla aplikacji, narzędzi i produktów.

1. [Inspekcja jakości rozpoznawania](how-to-custom-speech-inspect-data.md) — użyj [portalu Custom Speech](https://speech.microsoft.com/customspeech) , aby odtworzyć załadowane audio i sprawdzić jakość rozpoznawania mowy danych testowych. Aby uzyskać miary ilościowe, zobacz [Sprawdzanie danych](how-to-custom-speech-inspect-data.md).

1. [Oceń i popraw dokładność](how-to-custom-speech-evaluate-data.md) — Oceń i popraw dokładność modelu zamiany mowy na tekst. [Portal Custom Speech](https://speech.microsoft.com/customspeech) będzie zawierać *Współczynnik błędów wyrazów*, który może służyć do określenia, czy wymagane jest dodatkowe szkolenie. Jeśli dokładność jest zadowalająca, można używać interfejsów API usługi mowy bezpośrednio. Jeśli chcesz poprawić dokładność przez średnią wartość wynoszącą 5%-20%, Użyj karty **szkolenia** w portalu, aby przekazać dodatkowe dane szkoleniowe, takie jak transkrypcje z etykietami ludzkimi i powiązane teksty.

1. [Uczenie i wdrażanie modelu](how-to-custom-speech-train-model.md) — poprawianie dokładności modelu zamiany mowy na tekst dzięki udostępnieniu zapisów pisanych (10-1000 godzin) i powiązanego tekstu (<200 MB) wraz z danymi testu dźwiękowego. Te dane ułatwiają uczenie modelu zamiany mowy na tekst. Po przekształceniu, ponownym przetestowaniu i, jeśli wynik jest zadowalający, możesz wdrożyć model w niestandardowym punkcie końcowym.

## <a name="set-up-your-azure-account"></a>Skonfiguruj swoje konto platformy Azure

Aby można było utworzyć niestandardowy model przy użyciu [portalu Custom Speech](https://speech.microsoft.com/customspeech) , wymagana jest subskrypcja usługi Azure Account and Speech Service. Jeśli nie masz konta i subskrypcji, [Wypróbuj usługę mowy bezpłatnie](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Pamiętaj, aby utworzyć standardowe subskrypcje (S0), bezpłatne subskrypcje (F0) nie są obsługiwane.

Po utworzeniu konta platformy Azure i subskrypcji usługi mowy należy zalogować się do [portalu Custom Speech](https://speech.microsoft.com/customspeech) i połączyć swoją subskrypcję.

1. Zaloguj się do [portalu Custom Speech](https://aka.ms/custom-speech).
1. Wybierz subskrypcję, na której chcesz korzystać, i Utwórz projekt mowy.
1. Jeśli chcesz zmodyfikować swoją subskrypcję, użyj ikony **koło zębate** znajdującej się w górnym obszarze nawigacji.

## <a name="how-to-create-a-project"></a>Jak utworzyć projekt

Zawartość, taka jak dane, modele, testy i punkty końcowe, są zorganizowane w **projekty** w [portalu Custom Speech](https://speech.microsoft.com/customspeech). Każdy projekt jest specyficzny dla domeny i kraju/języka. Na przykład możesz utworzyć projekt dla centrów wywołań, które używają języka angielskiego w Stany Zjednoczone.

Aby utworzyć swój pierwszy projekt, wybierz **mowę zamiany mowy na tekst/niestandardową**, a następnie kliknij pozycję **Nowy projekt**. Postępuj zgodnie z instrukcjami wyświetlanymi przez kreatora, aby utworzyć projekt. Po utworzeniu projektu powinny zostać wyświetlone cztery karty: **dane**, **testowanie**, **szkolenia** i **wdrożenia**. Skorzystaj z linków w [sekcji Następne kroki](#next-steps) , aby dowiedzieć się, jak korzystać z każdej karty.

> [!IMPORTANT]
> [Portal Custom Speech](https://aka.ms/custom-speech) został niedawno zaktualizowany! Jeśli utworzono poprzednie dane, modele, testy i opublikowane punkty końcowe w portalu CRIS.ai lub za pomocą interfejsów API, należy utworzyć nowy projekt w nowym portalu, aby połączyć się ze starymi jednostkami.

## <a name="model-lifecycle"></a>Cykl życia modelu

Niestandardowa mowa używa zarówno **modeli podstawowych** , jak i **modeli niestandardowych**. Każdy język ma jeden lub więcej **modeli podstawowych**. Ogólnie rzecz biorąc, gdy nowy model mowy jest publikowany do zwykłej usługi mowy, jest również importowany do usługi Custom Speech jako nowy **model podstawowy**. Zazwyczaj są one aktualizowane co 3-6 miesięcy, a starsze modele stają się mniej przydatne w miarę upływu czasu, ponieważ najnowszy model ma zwykle wyższą dokładność.

Z kolei **modele niestandardowe** są tworzone przez dostosowanie wybranego modelu podstawowego do określonego scenariusza klienta. Można nadal używać określonego modelu niestandardowego przez dłuższy czas po dodaniu go do Twoich potrzeb, ale zalecamy okresową aktualizację do najnowszego modelu podstawowego i pouczenie się z dodatkowymi danymi.

Inne kluczowe terminy związane z cyklem życia modelu obejmują:

* **Adaptacja**: Tworzenie modelu podstawowego i dostosowywanie go do domeny/scenariusza przy użyciu danych tekstowych i/lub danych audio
* **Dekodowanie**: korzystanie z modelu i wykonywanie rozpoznawania mowy (dekodowanie dźwięku na tekst)
* **Punkt końcowy**: specyficzne dla użytkownika wdrożenie modelu podstawowego lub modelu niestandardowego, który jest dostępny *tylko* dla danego użytkownika.

### <a name="expiration-timeline"></a>Oś czasu wygaśnięcia

W miarę jak nowe modele i nowe funkcje stają się dostępne i starsze, mniej dokładne modele są wycofywane, zobacz następujące osie czasu dla elementu model i wygaśnięcie punktu końcowego:

**Modele podstawowe** 

* Adaptacja: dostępne przez 1 rok. Po zaimportowaniu modelu jest on dostępny przez 1 rok do tworzenia modeli niestandardowych. Po 1 roku nowe modele niestandardowe muszą zostać utworzone przy użyciu nowszej wersji modelu bazowego.  
* Dekodowanie: dostępne przez 2 lata od importu. Oznacza to, że można utworzyć punkt końcowy i użyć transkrypcji usługi Batch przez 2 lata z tym modelem. 
* Punkty końcowe: dostępne na tej samej osi czasu co dekodowanie

**Modele niestandardowe**

* Dekodowanie: dostępne przez 2 lata po utworzeniu modelu. Oznacza to, że możesz użyć niestandardowego modelu przez 2 lata (Batch/czas rzeczywisty/test) po jego utworzeniu. Po 2 latach należy ponownie przeprowadzić **uczenie modelu**, ponieważ najczęściej model podstawowy zostanie uznany za przestarzały do adaptacji.  
* Punkty końcowe: dostępne na tej samej osi czasu co dekodowanie

Gdy model podstawowy lub model niestandardowy wygaśnie, zawsze będzie powracać do **najnowszej wersji podstawowego modelu**. W ten sposób implementacja nigdy nie zostanie przerwana, ale może stać się mniej dokładne dla *konkretnych danych* , jeśli modele niestandardowe osiągną wygaśnięcie. Możesz zobaczyć wygaśnięcie dla modelu w następujących miejscach w portalu Custom Speech:

* Podsumowanie szkolenia modelu
* Szczegóły szkolenia modelu
* Podsumowanie wdrożenia
* Szczegóły wdrożenia

Możesz również sprawdzić daty wygaśnięcia za pośrednictwem [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) i [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) niestandardowych interfejsów API mowy we `deprecationDates` właściwości w odpowiedzi JSON.

Należy pamiętać, że można uaktualnić model do niestandardowego punktu końcowego mowy bez przestoju, zmieniając model używany przez punkt końcowy w sekcji wdrażanie niestandardowego portalu mowy lub korzystając z niestandardowego interfejsu API mowy.

## <a name="next-steps"></a>Następne kroki

* [Przygotowywanie i testowanie danych](how-to-custom-speech-test-data.md)
* [Inspekcja danych](how-to-custom-speech-inspect-data.md)
* [Oceń i popraw dokładność modelu](how-to-custom-speech-evaluate-data.md)
* [Trenowanie i wdrażanie modelu](how-to-custom-speech-train-model.md)
