---
title: Ulepszanie syntezy za pomocą niestandardowej usługi rozpoznawania mowy
titleSuffix: Azure Cognitive Services
description: Niestandardowy głos to zestaw narzędzi online, które umożliwiają tworzenie rozpoznawalnego, jednoczęściowego głosu dla marki. Wszystkie potrzebne do rozpoczęcia pracy to kilku plików audio i skojarzonych transkrypcji. Skorzystaj z poniższych linków, aby rozpocząć tworzenie niestandardowego środowiska zamiany mowy na tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 99af0ee46e2827ac8a5749d58bb19cf85f96aa46
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577465"
---
# <a name="get-started-with-custom-voice"></a>Wprowadzenie do usługi Custom Voice

[Niestandardowy głos](https://aka.ms/customvoice) to zestaw narzędzi online, które umożliwiają tworzenie rozpoznawalnego, jednoczęściowego głosu dla marki. Wszystkie potrzebne do rozpoczęcia pracy to kilku plików audio i skojarzonych transkrypcji. Skorzystaj z poniższych linków, aby rozpocząć tworzenie niestandardowego środowiska zamiany tekstu na mowę.

## <a name="whats-in-custom-voice"></a>Co znajduje się w niestandardowym głosowaniu?

Przed rozpoczęciem pracy z niestandardowym głosem musisz mieć konto platformy Azure i subskrypcję usługi mowy. Po utworzeniu konta możesz przygotować swoje dane, wyszkolić i przetestować modele, oszacować jakość głosu i ostatecznie wdrożyć niestandardowy model głosowy.

Na poniższym diagramie przedstawiono kroki umożliwiające utworzenie niestandardowego modelu głosu przy użyciu [niestandardowego portalu głosu](https://aka.ms/customvoice). Skorzystaj z linków, aby dowiedzieć się więcej.

![Niestandardowy diagram architektury głosu](media/custom-voice/custom-voice-diagram.png)

1. [Subskrybuj i Utwórz projekt](#set-up-your-azure-account) — Utwórz konto platformy Azure i Utwórz subskrypcję usługi mowy. Ta ujednolicona subskrypcja zapewnia dostęp do funkcji zamiany mowy na tekst, zamiany tekstu na mowę, tłumaczenia mowy i niestandardowego portalu głosu. Następnie za pomocą subskrypcji usługi mowy Utwórz swój pierwszy niestandardowy projekt głosowy.

2. [Przekazuj](how-to-custom-voice-create-voice.md#upload-your-datasets) dane — przekazywanie danych (audio i tekstowych) przy użyciu niestandardowego portalu głosu lub niestandardowego interfejsu API mowy. Z poziomu portalu można zbadać i oszacować wyniki wymowy oraz współczynnik sygnałów do szumów. Aby uzyskać więcej informacji, zobacz [jak przygotować dane dla niestandardowego głosu](how-to-custom-voice-prepare-data.md).

3. [Uczenie modelu](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) — używanie danych do tworzenia niestandardowego modelu głosu zamiany tekstu na mowę. Możesz nauczyć model w różnych językach. Po przeprowadzeniu szkolenia Przetestuj model i, jeśli wynik jest zadowalający, możesz wdrożyć model.

4. [Wdrażanie modelu](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) — Tworzenie niestandardowego punktu końcowego dla modelu głosu zamiany tekstu na mowę i używanie go na potrzeby syntezy mowy w produktach, narzędziach i aplikacjach.

## <a name="custom-neural-voices"></a>Niestandardowe głosy neuronowych

Niestandardowy głos obecnie obsługuje warstwy Standardowa i neuronowych. Usługa Custom neuronowych Voice pozwala użytkownikom na tworzenie modeli głosu o wyższej jakości, a jednocześnie wymaga mniej danych, a także udostępnia środki ułatwiające wdrażanie odpowiedzialnych plików AI. Zalecamy korzystanie z niestandardowego głosu neuronowych w celu opracowywania bardziej realistycznych głosów dla bardziej naturalnych interfejsów konwersacji oraz umożliwienie klientom i użytkownikom końcowym korzystania z najnowszych technologii zamiany tekstu na mowę w odpowiedzialny sposób. [Dowiedz się więcej na temat niestandardowego głosu neuronowych](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

> [!NOTE]
> W ramach zobowiązania firmy Microsoft w zakresie projektowania odpowiedzialnych plików AI Neuronowychmy niestandardową obsługę głosu. Użytkownik może uzyskać dostęp do technologii dopiero po przejrzeniu aplikacji i zapewnieniu jej używania z naszymi właściwymi zasadami AI. Dowiedz się więcej [na temat zasad dotyczących limitu dostępu](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) i [Zastosuj tutaj](https://aka.ms/customneural). [Języki](language-support.md#customization) i [regiony](regions.md#custom-voices) obsługiwane w przypadku wersji Standard i neuronowych niestandardowego głosu są różne. Przed rozpoczęciem Sprawdź szczegóły.  

## <a name="set-up-your-azure-account"></a>Skonfiguruj swoje konto platformy Azure

Wymagana jest subskrypcja usługi mowy, aby można było użyć portalu Custom Speech w celu utworzenia modelu niestandardowego. Postępuj zgodnie z tymi instrukcjami, aby utworzyć subskrypcję usługi mowy na platformie Azure. Jeśli nie masz konta platformy Azure, możesz utworzyć nowe konto.  

Po utworzeniu konta platformy Azure i subskrypcji usługi mowy należy zalogować się do portalu niestandardowego programu Voice i połączyć swoją subskrypcję.

1. Pobierz klucz subskrypcji usługi rozpoznawania mowy z Azure Portal.
2. Zaloguj się do [niestandardowego portalu głosowego](https://aka.ms/custom-voice).
3. Wybierz swoją subskrypcję i Utwórz projekt mowy.
4. Jeśli chcesz przełączyć się na inną subskrypcję mowy, użyj ikony koło zębate znajdującej się w górnym obszarze nawigacji.

> [!NOTE]
> Aby można było korzystać z usługi, na platformie Azure musi być utworzony klucz usługi F0 lub S0. Niestandardowy głos neuronowych obsługuje tylko warstwę S0. 

## <a name="how-to-create-a-project"></a>Jak utworzyć projekt

Zawartość, taka jak dane, modele, testy i punkty końcowe, są zorganizowane w **projekty** w portalu niestandardowym. Każdy projekt jest specyficzny dla kraju/języka i płci głosu, który chcesz utworzyć. Na przykład możesz utworzyć projekt dla głosu żeńskiego dla rozmowy telefonicznej botów, która używa języka angielskiego w Stany Zjednoczone ("pl-US").

Aby utworzyć swój pierwszy projekt, wybierz kartę **Zamiana tekstu na mowę/niestandardową** , a następnie kliknij pozycję **Nowy projekt**. Postępuj zgodnie z instrukcjami wyświetlanymi przez kreatora, aby utworzyć projekt. Po utworzeniu projektu zobaczysz cztery karty: **dane**, **szkolenia**, **testowanie** i **wdrażanie**. Skorzystaj z linków w [sekcji Następne kroki](#next-steps) , aby dowiedzieć się, jak korzystać z każdej karty.

> [!IMPORTANT]
> [Niestandardowy Portal głosu](https://aka.ms/custom-voice) został niedawno zaktualizowany! Jeśli utworzono poprzednie dane, modele, testy i opublikowane punkty końcowe w portalu CRIS.ai lub za pomocą interfejsów API, należy utworzyć nowy projekt w nowym portalu, aby połączyć się ze starymi jednostkami.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Jak przeprowadzić migrację do niestandardowego głosu neuronowych

Warstwa szkoleniowa Standard/non-neuronowych (adaptacyjna, statystyczna parametryczna, concacenative) niestandardowego głosu jest przestarzała. Zawiadomienie został wysłany do wszystkich istniejących subskrypcji mowy przed 2/28/2021. W okresie wycofania (3/1/2021-2/29/2024) istniejący użytkownicy warstwy standardowej mogą nadal korzystać z utworzonych modeli innych niż neuronowych. Wszyscy nowi użytkownicy/nowe zasoby mowy powinny przejść do neuronowych warstwy/niestandardowego głosu neuronowych. Po 2/29/2024 wszystkie głosy niestandardowe/neuronowych nie będą już obsługiwane. 

Jeśli używasz niestandardowego głosu nieneuronowychego/standardowego, Przeprowadź migrację do niestandardowego neuronowych głosu bezpośrednio po poniższych krokach. Przechodzenie do niestandardowego głosu neuronowych pomoże Ci w tworzeniu bardziej realistycznych głosów dla jeszcze bardziej naturalnych interfejsów konwersacji oraz umożliwienie klientom i użytkownikom końcowym skorzystania z najnowszej technologii zamiany tekstu na mowę w odpowiedzialny sposób. 

1. Dowiedz się więcej [na temat zasad dotyczących limitu dostępu](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) i [Zastosuj tutaj](https://aka.ms/customneural). Należy zauważyć, że dostęp do usługi Custom neuronowych Voice jest uzależniony od uznania firmy Microsoft na podstawie kryteriów kwalifikujących. Klienci mogą uzyskać dostęp do technologii dopiero po przejrzeniu ich aplikacji i zapewnieniu jej używania z naszymi [właściwymi zasadami AI](https://microsoft.com/ai/responsible-ai) i [kodeksem postępowania](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 
2. Po zatwierdzeniu aplikacji uzyskasz dostęp do funkcji szkolenia "neuronowych". Upewnij się, że logujesz się do [niestandardowego portalu głosowego](https://speech.microsoft.com/customvoice) , korzystając z tej samej subskrypcji platformy Azure, którą podano w aplikacji. 
    > [!IMPORTANT]
    > Aby chronić talentę głosu i zapobiegać szkoleniu modeli głosowych z nieautoryzowanym nagrywaniem lub bez potwierdzenia z Talenti głosu, firma Microsoft wymaga od klientów przekazania zarejestrowanej instrukcji głosowej talent, która udzieliła swojej zgody. Podczas przygotowywania skryptu nagrywania upewnij się, że zawarto to zdanie. "I [stan imię i nazwisko] wie, że nagrania mojego głosu będą używane przez użytkownika [Nadaj nazwę firmie] do tworzenia i używania syntetycznej wersji mojego głosu".
    > To zdanie należy przekazać do karty **talent głosu** jako plik z wyrazami zgody. Zostanie ona użyta do sprawdzenia, czy nagrania w Twoich zestawach danych są wykonywane przez tę samą osobę, która wyraża zgodę.
3. Po utworzeniu niestandardowego modelu głosu neuronowych należy wdrożyć model głosowy w nowym punkcie końcowym. Aby utworzyć nowy niestandardowy punkt końcowy głosu z modelem głosu neuronowych, przejdź do pozycji **Zamiana tekstu na mowę > niestandardowego > wdrożenia głosu**. Wybierz pozycję **Wdróż model** i wprowadź **nazwę** i **Opis** niestandardowego punktu końcowego. Następnie wybierz niestandardowy model głosu neuronowych, który chcesz skojarzyć z tym punktem końcowym, i Potwierdź wdrożenie.  
4. Zaktualizuj swój kod w swoich aplikacjach, jeśli nowy punkt końcowy został utworzony z nowym modelem. 

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie niestandardowych danych głosowych](how-to-custom-voice-prepare-data.md)
- [Tworzenie niestandardowego głosu](how-to-custom-voice-create-voice.md)
- [Przewodnik: zapisywanie przykładów głosu](record-custom-voice-samples.md)
