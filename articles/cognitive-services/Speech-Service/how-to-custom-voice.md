---
title: Poprawa syntezy dzięki usłudze Custom Voice - Speech
titleSuffix: Azure Cognitive Services
description: Custom Voice to zestaw narzędzi online, które pozwalają stworzyć rozpoznawalny, jedyny w swoim rodzaju głos dla Twojej marki. Wszystko, czego potrzeba, aby rozpocząć, to kilka plików audio i związane z nimi transkrypcje. Skorzystaj z poniższych łączy, aby rozpocząć tworzenie niestandardowego środowiska zamiany mowy na tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 2aacf8881a57f0677177cd341a0ddc63224b26b6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402051"
---
# <a name="get-started-with-custom-voice"></a>Wprowadzenie do usługi Custom Voice

[Custom Voice](https://aka.ms/customvoice) to zestaw narzędzi online, które pozwalają stworzyć rozpoznawalny, jedyny w swoim rodzaju głos dla Twojej marki. Wszystko, czego potrzeba, aby rozpocząć, to kilka plików audio i związane z nimi transkrypcje. Skorzystaj z poniższych łączy, aby rozpocząć tworzenie niestandardowego środowiska zamiany tekstu na mowę.

## <a name="whats-in-custom-voice"></a>Co jest w głosie niestandardowym?

Przed rozpoczęciem korzystania z usługi Custom Voice musisz mieć konto platformy Azure i subskrypcję usługi mowy. Po utworzeniu konta możesz przygotować dane, wyszkolić i przetestować modele, ocenić jakość głosu, a ostatecznie wdrożyć niestandardowy model głosu.

Na poniższym diagramie przedstawiono kroki tworzenia niestandardowego modelu głosu przy użyciu [portalu Niestandardowy głos](https://aka.ms/customvoice). Skorzystaj z linków, aby dowiedzieć się więcej.

![Niestandardowy diagram architektury głosu](media/custom-voice/custom-voice-diagram.png)

1. [Subskrybuj i utwórz projekt](#set-up-your-azure-account) — utwórz konto platformy Azure i utwórz subskrypcję usługi mowy. Ta ujednolicona subskrypcja zapewnia dostęp do zamiany mowy na tekst, zamiany tekstu na mowę, tłumaczenia mowy i portalu Niestandardowego Głosu. Następnie korzystając z subskrypcji usługi mowy, utwórz pierwszy projekt Custom Voice.

2. [Przekaż dane](how-to-custom-voice-create-voice.md#upload-your-datasets) — przesyłaj dane (audio i tekstowe) za pomocą portalu Custom Voice lub niestandardowego interfejsu API głosowego. Z portalu można badać i oceniać wyniki wymowy i współczynniki sygnału do szumu. Aby uzyskać więcej informacji, zobacz [Jak przygotować dane dla niestandardowego głosu](how-to-custom-voice-prepare-data.md).

3. [Trenuj swój model](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) — użyj danych, aby utworzyć niestandardowy model głosu zamiany tekstu na mowę. Można trenować model w różnych językach. Po treningu, przetestować model i jeśli jesteś zadowolony z wyniku, można wdrożyć modelu.

4. [Wdrażanie modelu](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) — tworzenie niestandardowego punktu końcowego dla modelu głosu zamiany tekstu na mowę i używanie go do syntezy mowy w produktach, narzędziach i aplikacjach.

## <a name="custom-neural-voices"></a>Niestandardowe głosy neuronowe

Funkcja dostosowywania głosu neuronowego jest obecnie w publicznej wersji zapoznawczej, ograniczona do wybranych klientów. Wypełnij ten [formularz zgłoszeniowy,](https://go.microsoft.com/fwlink/?linkid=2108737) aby rozpocząć.

> [!NOTE]
> W ramach zobowiązania firmy Microsoft do projektowania odpowiedzialnej siatce, naszym zamiarem jest ochrona praw osób i społeczeństwa oraz wspieranie przejrzystych interakcji między człowiekiem a komputerem. Z tego powodu niestandardowy głos neuronowy nie jest ogólnie dostępny dla wszystkich klientów. Dostęp do technologii można uzyskać dopiero po zapoznaniu się z aplikacjami i zobowiążeniu się do korzystania z niej zgodnie z naszymi zasadami etycznymi. Dowiedz się więcej o naszym [procesie gating aplikacji](https://aka.ms/custom-neural-gating-overview).

## <a name="set-up-your-azure-account"></a>Konfigurowanie konta platformy Azure

Subskrypcja usługi mowy jest wymagana, zanim będzie można użyć portalu mowy niestandardowej do utworzenia modelu niestandardowego. Postępuj zgodnie z tymi instrukcjami, aby utworzyć subskrypcję usługi mowy na platformie Azure. Jeśli nie masz konta platformy Azure, możesz zarejestrować się w celu uzyskania nowego.  

Po utworzeniu konta platformy Azure i subskrypcji usługi mowy musisz zalogować się do portalu Niestandardowy głos i połączyć subskrypcję.

1. Pobierz klucz subskrypcji usługi mowy z witryny Azure portal.
2. Zaloguj się do [portalu Niestandardowy głos](https://aka.ms/custom-voice).
3. Wybierz subskrypcję i utwórz projekt mowy.
4. Jeśli chcesz przełączyć się na inną subskrypcję mowy, użyj ikony trybiku znajdującej się w górnej części nawigacji.

> [!NOTE]
> Usługa Custom Voice NIE obsługuje 30-dniowego bezpłatnego klucza próbnego. Przed rozpoczęciem korzystania z usługi należy utworzyć klucz F0 lub S0 na platformie Azure.

## <a name="how-to-create-a-project"></a>Jak utworzyć projekt

Zawartość, taka jak dane, modele, testy i punkty końcowe są zorganizowane w **projekty** w portalu Niestandardowy głos. Każdy projekt jest specyficzny dla kraju/języka i płci głosu, który chcesz utworzyć. Możesz na przykład utworzyć projekt dla żeńskiego głosu dla czatu w centrum obsługi telefonicznej, które używają języka angielskiego w Stanach Zjednoczonych (en-US).

Aby utworzyć pierwszy projekt, zaznacz kartę **Zamiana tekstu na mowę/Niestandardowy głos,** a następnie kliknij pozycję **Nowy projekt**. Postępuj zgodnie z instrukcjami dostarczonymi przez kreatora, aby utworzyć projekt. Po utworzeniu projektu zostaną wyświetlenia czterech kart: **Dane,** **Szkolenie,** **Testowanie**i **Wdrażanie.** Skorzystaj z łączy podanych w [następnych krokach,](#next-steps) aby dowiedzieć się, jak korzystać z każdej karty.

> [!IMPORTANT]
> [Portal Custom Voice](https://aka.ms/custom-voice) został niedawno zaktualizowany! Jeśli utworzono poprzednie dane, modele, testy i opublikowane punkty końcowe w portalu CRIS.ai lub za pomocą interfejsów API, należy utworzyć nowy projekt w nowym portalu, aby połączyć się z tymi starymi encjami.

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie niestandardowych danych głosowych](how-to-custom-voice-prepare-data.md)
- [Tworzenie niestandardowego głosu](how-to-custom-voice-create-voice.md)
- [Przewodnik: Nagrywanie próbek głosu](record-custom-voice-samples.md)
