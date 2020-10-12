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
ms.openlocfilehash: 272367d5311952b45c73febe0e05b1ec2d225261
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88056690"
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

Możliwość dostosowania głosu neuronowych jest obecnie dostępna w publicznej wersji zapoznawczej, ograniczona do wybranych klientów. Wypełnij ten [formularz aplikacji](https://go.microsoft.com/fwlink/?linkid=2108737) , aby rozpocząć pracę.

> [!NOTE]
> W ramach zobowiązania firmy Microsoft do projektowania odpowiedzialnych plików AI, nasza intencja polega na ochronie praw osób i społeczeństwa oraz wspieraniu przezroczystych interakcji między komputerami. Z tego powodu niestandardowe Neuronowyche nie są ogólnie dostępne dla wszystkich klientów. Użytkownik może uzyskać dostęp do technologii dopiero po przejrzeniu aplikacji i zapewnieniu jej używania z naszymi zasadami etyki. Dowiedz się więcej na temat naszego [procesu kontroli aplikacji](https://aka.ms/custom-neural-gating-overview).

## <a name="set-up-your-azure-account"></a>Skonfiguruj swoje konto platformy Azure

Wymagana jest subskrypcja usługi mowy, aby można było użyć portalu Custom Speech w celu utworzenia modelu niestandardowego. Postępuj zgodnie z tymi instrukcjami, aby utworzyć subskrypcję usługi mowy na platformie Azure. Jeśli nie masz konta platformy Azure, możesz utworzyć nowe konto.  

Po utworzeniu konta platformy Azure i subskrypcji usługi mowy należy zalogować się do portalu niestandardowego programu Voice i połączyć swoją subskrypcję.

1. Pobierz klucz subskrypcji usługi rozpoznawania mowy z Azure Portal.
2. Zaloguj się do [niestandardowego portalu głosowego](https://aka.ms/custom-voice).
3. Wybierz swoją subskrypcję i Utwórz projekt mowy.
4. Jeśli chcesz przełączyć się na inną subskrypcję mowy, użyj ikony koło zębate znajdującej się w górnym obszarze nawigacji.

> [!NOTE]
> Aby można było korzystać z usługi, na platformie Azure musi być utworzony klucz F0 lub S0.

## <a name="how-to-create-a-project"></a>Jak utworzyć projekt

Zawartość, taka jak dane, modele, testy i punkty końcowe, są zorganizowane w **projekty** w portalu niestandardowym. Każdy projekt jest specyficzny dla kraju/języka i płci głosu, który chcesz utworzyć. Na przykład możesz utworzyć projekt dla żeńskiego głosu dla rozmowy telefonicznej botów, która używa języka angielskiego w Stany Zjednoczone (EN-US).

Aby utworzyć swój pierwszy projekt, wybierz kartę **Zamiana tekstu na mowę/niestandardową** , a następnie kliknij pozycję **Nowy projekt**. Postępuj zgodnie z instrukcjami wyświetlanymi przez kreatora, aby utworzyć projekt. Po utworzeniu projektu zobaczysz cztery karty: **dane**, **szkolenia**, **testowanie**i **wdrażanie**. Skorzystaj z linków w [sekcji Następne kroki](#next-steps) , aby dowiedzieć się, jak korzystać z każdej karty.

> [!IMPORTANT]
> [Niestandardowy Portal głosu](https://aka.ms/custom-voice) został niedawno zaktualizowany! Jeśli utworzono poprzednie dane, modele, testy i opublikowane punkty końcowe w portalu CRIS.ai lub za pomocą interfejsów API, należy utworzyć nowy projekt w nowym portalu, aby połączyć się ze starymi jednostkami.

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie niestandardowych danych głosowych](how-to-custom-voice-prepare-data.md)
- [Tworzenie niestandardowego głosu](how-to-custom-voice-create-voice.md)
- [Przewodnik: zapisywanie przykładów głosu](record-custom-voice-samples.md)
