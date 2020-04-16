---
title: Wprowadzenie do usługi Mowy niestandardowej — mowa
titleSuffix: Azure Cognitive Services
description: Mowa niestandardowa to zestaw narzędzi online, które umożliwiają ocenę i poprawę dokładności mowy do tekstu w aplikacjach, narzędziach i produktach. Wszystko, czego potrzeba, aby rozpocząć, to kilka testowych plików audio. Skorzystaj z poniższych łączy, aby rozpocząć tworzenie niestandardowego środowiska zamiany mowy na tekst.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: a52735ee62f564a5dc536fd2b7d3539406388341
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402063"
---
# <a name="what-is-custom-speech"></a>Co to jest mowa niestandardowa?

[Mowa niestandardowa](https://aka.ms/customspeech) to zestaw narzędzi online, które umożliwiają ocenę i poprawę dokładności mowy do tekstu firmy Microsoft dla aplikacji, narzędzi i produktów. Wszystko, czego potrzeba, aby rozpocząć, to kilka testowych plików audio. Skorzystaj z poniższych łączy, aby rozpocząć tworzenie niestandardowego środowiska zamiany mowy na tekst.

## <a name="whats-in-custom-speech"></a>Co jest w mowę niestandardową?

Aby można było wykonać cokolwiek za pomocą mowy niestandardowej, musisz mieć konto platformy Azure i subskrypcję usługi mowy. Po uzyskaniu konta możesz przygotować dane, wyszkolić i przetestować modele, sprawdzić jakość rozpoznawania, ocenić dokładność, a ostatecznie wdrożyć i użyć niestandardowego modelu zamiany mowy na tekst.

Ten diagram przedstawia elementy, które składają się na [portal mowy niestandardowej](https://aka.ms/customspeech). Skorzystaj z poniższych linków, aby dowiedzieć się więcej o każdym kroku.

![Wyróżnia różne składniki, które tworzą portal mowy niestandardowej.](./media/custom-speech/custom-speech-overview.png)

1. [Subskrybuj i utwórz projekt](#set-up-your-azure-account) — utwórz konto platformy Azure i subskrybuj usługę mowy. Ta ujednolicona subskrypcja zapewnia dostęp do zamiany mowy na tekst, zamiany tekstu na mowę, tłumaczenia mowy i [portalu Mowy niestandardowej.](https://speech.microsoft.com/customspeech) Następnie za pomocą subskrypcji usługi mowy utwórz pierwszy projekt mowy niestandardowej.

2. [Przekaż dane testowe](how-to-custom-speech-test-data.md) — przekaż dane testowe (pliki audio) w celu oceny oferty zamiany mowy na tekst firmy Microsoft dla aplikacji, narzędzi i produktów.

3. [Sprawdź jakość rozpoznawania](how-to-custom-speech-inspect-data.md) — użyj [portalu mowy niestandardowej,](https://speech.microsoft.com/customspeech) aby odtworzyć przesłany dźwięk i sprawdzić jakość rozpoznawania mowy danych testowych. Aby zapoznać się z pomiarami ilościowymi, zobacz [Inspekcja danych](how-to-custom-speech-inspect-data.md).

4. [Oceń dokładność](how-to-custom-speech-evaluate-data.md) — oceń dokładność modelu zamiany mowy na tekst. [Portal mowy niestandardowej](https://speech.microsoft.com/customspeech) zapewni *poziom błędu programu Word,* który może służyć do określenia, czy wymagane jest dodatkowe szkolenie. Jeśli masz satysfakcję z dokładności, możesz użyć interfejsów API usługi mowy bezpośrednio. Jeśli chcesz zwiększyć dokładność o względną średnią 5% - 20%, użyj karty **Szkolenie** w portalu, aby przesłać dodatkowe dane szkoleniowe, takie jak transkrypcje oznaczone przez człowieka i powiązany tekst.

5. [Trenuj model](how-to-custom-speech-train-model.md) — zwiększ dokładność modelu zamiany mowy na tekst, udostępniając pisemne transkrypcje (10-1000 godzin) i powiązany tekst (<200 MB) wraz z danymi testu audio. Te dane pomagają w szkoleniu modelu zamiany mowy na tekst. Po szkoleniu, ponownie przetestować, a jeśli jesteś zadowolony z wyniku, można wdrożyć modelu.

6. [Wdrażanie modelu](how-to-custom-speech-deploy-model.md) — tworzenie niestandardowego punktu końcowego dla modelu zamiany mowy na tekst i używanie go w aplikacjach, narzędziach lub produktach.

## <a name="set-up-your-azure-account"></a>Konfigurowanie konta platformy Azure

Subskrypcja usługi mowy jest wymagana, zanim będzie można użyć [portalu mowy niestandardowej](https://speech.microsoft.com/customspeech) do utworzenia modelu niestandardowego. Postępuj zgodnie z tymi instrukcjami, aby utworzyć standardową subskrypcję usługi mowy: [Utwórz subskrypcję mowy](get-started.md#new-resource).

> [!NOTE]
> Pamiętaj, aby utworzyć standardowe subskrypcje (S0), bezpłatne subskrypcje próbne (F0) nie są obsługiwane.

Po utworzeniu konta platformy Azure i subskrypcji usługi mowy musisz zalogować się do [portalu mowy niestandardowej](https://speech.microsoft.com/customspeech) i połączyć subskrypcję.

1. Pobierz klucz subskrypcji usługi mowy z witryny Azure portal.
2. Zaloguj się do [portalu mowy niestandardowej](https://aka.ms/custom-speech).
3. Wybierz subskrypcję, nad którą musisz pracować, i utwórz projekt mowy.
4. Jeśli chcesz zmodyfikować subskrypcję, użyj ikony **trybiku** znajdującej się w górnej części nawigacji.

## <a name="how-to-create-a-project"></a>Jak utworzyć projekt

Zawartość, taka jak dane, modele, testy i punkty końcowe, są zorganizowane w **projekty** w [portalu mowy niestandardowej.](https://speech.microsoft.com/customspeech) Każdy projekt jest specyficzny dla domeny i kraju/języka. Na przykład można utworzyć projekt dla centrów wywoławczych, które używają języka angielskiego w Stanach Zjednoczonych.

Aby utworzyć pierwszy projekt, zaznacz **mowę zamiany tekstu/mowę niestandardową,** a następnie kliknij przycisk **Nowy projekt**. Postępuj zgodnie z instrukcjami dostarczonymi przez kreatora, aby utworzyć projekt. Po utworzeniu projektu powinny zostać wyświetlene cztery karty: **Dane,** **Testowanie,** **Szkolenie**i **Wdrażanie.** Skorzystaj z łączy podanych w [następnych krokach,](#next-steps) aby dowiedzieć się, jak korzystać z każdej karty.

> [!IMPORTANT]
> Portal [mowy niestandardowej](https://aka.ms/custom-speech) został niedawno zaktualizowany! Jeśli utworzono poprzednie dane, modele, testy i opublikowane punkty końcowe w portalu CRIS.ai lub za pomocą interfejsów API, należy utworzyć nowy projekt w nowym portalu, aby połączyć się z tymi starymi encjami.

## <a name="next-steps"></a>Następne kroki

* [Przygotowanie i przetestowanie danych](how-to-custom-speech-test-data.md)
* [Sprawdź swoje dane](how-to-custom-speech-inspect-data.md)
* [Oceń swoje dane](how-to-custom-speech-evaluate-data.md)
* [Uczenie modelu](how-to-custom-speech-train-model.md)
* [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)
