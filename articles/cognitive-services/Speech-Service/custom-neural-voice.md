---
title: Niestandardowy przegląd głosu neuronowych — usługa mowy
titleSuffix: Azure Cognitive Services
description: Custom neuronowych Voice to funkcja zamiany tekstu na mowę, która pozwala na utworzenie jednorazowej, dostosowanej syntetycznej liczby głosu dla aplikacji przez udostępnienie własnych danych audio jako próbki.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 36885e4673b83d1db7972f03c4a6309f766206c5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713103"
---
# <a name="what-is-custom-neural-voice"></a>Co to jest niestandardowy neuronowych Voice?

Custom neuronowych Voice to funkcja [zamiany tekstu na mowę](./text-to-speech.md) (TTS), która pozwala na utworzenie jednorazowego, dostosowanego głosu syntetycznego dla aplikacji, dostarczając własne dane audio jako przykład. Zamiana tekstu na mowę polega na konwertowaniu tekstu na syntetyczną mowę przy użyciu modelu uczenia maszynowego, który brzmi jak wybrany głos. Korzystając z [interfejsu API REST](./rest-text-to-speech.md), możesz umożliwić aplikacjom korzystanie z [wstępnie skompilowanych głosów](./language-support.md#neural-voices) lub własnych niestandardowych modeli [głosu](./how-to-custom-voice-prepare-data.md) opracowanych za pomocą funkcji niestandardowego głosu neuronowych. Niestandardowy głos neuronowych jest oparty na technologii neuronowych TTS, która tworzy naturalny głos dźwiękowy, który często nie jest odróżniany w porównaniu z głosem ludzkim.
Realistyczny i naturalny dźwięk dźwięku niestandardowego głosu neuronowych może reprezentować marki, Personify maszyny i umożliwić użytkownikom współpracę z aplikacjami w naturalny sposób.

> [!NOTE]
> Funkcja niestandardowego głosu neuronowych wymaga rejestracji, a dostęp do niej jest ograniczony w oparciu o kryteria kwalifikowania i użycia firmy Microsoft. Klienci, którzy chcą korzystać z tej funkcji, muszą zarejestrować swoje przypadki użycia za pomocą [formularza pobrania](https://aka.ms/customneural).

## <a name="the-basics-of-custom-neural-voice"></a>Podstawowe informacje na temat niestandardowego głosu neuronowych

Podstawowa technologia neuronowych TTS używana na potrzeby niestandardowego głosu neuronowych składa się z trzech głównych składników: Analizator tekstu, model neuronowych akustyczny i neuronowych vocoder. Aby wygenerować naturalną syntetyczną mowę z tekstu, tekst jest pierwszym wejściem do analizatora tekstu, który dostarcza dane wyjściowe w postaci sekwencji fonem. Fonem to podstawowa jednostka dźwiękowa, która odróżnia jeden wyraz od drugiego w określonym języku. Sekwencja fonemów definiuje wymowy wyrazów dostarczonych w tekście. 

Następnie sekwencja fonem przechodzi do modelu akustycznego neuronowych w celu przewidywania funkcji akustycznych, które definiują sygnały mowy, takie jak Timbre, styl mowy, szybkość, intonations i wzorce obciążeniowe. Na koniec neuronowych vocoder konwertuje funkcje akustyczne na fale dźwiękowe, aby wygenerowała syntetyczną mowę.

![Obraz wprowadzający dla niestandardowego głosu neuronowych.](./media/custom-voice/cnv-intro.png)

Modele głosu neuronowych TTS są przeszkolone przy użyciu głębokiej sieci neuronowych na podstawie próbek zapisujących głosów ludzkich. W tym [blogu](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)opisano sposób, w jaki neuronowych TTS współpracuje z najnowocześniejszymi modelami syntezy mowy neuronowych. W blogu wyjaśniono również, jak można dostosować uniwersalny model podstawowy do mniej niż 2 godzin danych mowy (lub mniej niż 2 000 zarejestrowanych wyrażenia długości) od prelegenta docelowego i dowiedzieć się, jak mówić. Aby dowiedzieć się więcej o tym, jak neuronowych vocoder jest szkolony, zapoznaj się z [wpisem w blogu](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).

Dzięki możliwości dostosowywania niestandardowego głosu neuronowych można dostosować aparat neuronowych TTS, aby lepiej odpowiadał Twoim scenariuszom użytkownika. Aby utworzyć niestandardowy głos neuronowych, użyj programu [Speech Studio](https://speech.microsoft.com/customvoice) do przekazywania nagranego dźwięku i odpowiednich skryptów, uczenia modelu i wdrażania dźwięku w niestandardowym punkcie końcowym. W zależności od przypadku użycia funkcja niestandardowego głosu neuronowych może służyć do konwertowania tekstu na mowę w czasie rzeczywistym (np. używanym w inteligentnym Asystencie wirtualnym) lub generowania zawartości audio w trybie offline (np. w książce dźwiękowej lub instrukcji w aplikacjach elektronicznych) za pomocą tekstu wejściowego dostarczonego przez użytkownika. Jest to udostępniane za pośrednictwem [interfejsu API REST](./rest-text-to-speech.md), [zestawu mowy SDK](./get-started-text-to-speech.md?pivots=programming-language-csharp&tabs=script%2cwindowsinstall)lub [portalu sieci Web](https://speech.microsoft.com/audiocontentcreation).

## <a name="terms-and-definitions"></a>Warunki i definicje

| **Okres**      | **Definicja**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Model głosowy   | Model zamiany tekstu na mowę, który może naśladować unikalne charakterystyki Vocal głośnika docelowego. *Model głosowy* jest również znany jako *czcionka głosu* lub *głos syntetyczny*. Model głosowy to zestaw parametrów w formacie binarnym, który nie jest czytelny dla człowieka i nie zawiera nagrań audio. Nie można go odtworzyć, aby utworzyć lub skonstruować dźwięk głosu ludzkiego. |
| Talent głosu  | Osoby lub głośniki docelowe, których głosy są rejestrowane i używane do tworzenia modeli głosowych, które są przeznaczone do dźwięku, takiego jak głos talent głosu.                                                                                                                                                                                                                                                   |
| Standard TTS  | Standardowa lub "tradycyjna" Metoda TTS, która dzieli mówiony język na fragmenty fonetyczne, tak aby można je było przemieszać i dopasować przy użyciu klasycznego programowania lub metod statystycznych.                                                                                                                                                                                                    |
| Neuronowych TTS    | Neuronowych TTS służy do uczenia mowy przy użyciu sieci głębokiej neuronowych, w których wystąpiły "zdobyte" sposób, w jaki wymowy są łączone w naturalnym człowieku, zamiast przy użyciu programowania proceduralnego lub metod statystycznych. Oprócz nagrań docelowej talent głosu, neuronowych TTS używa biblioteki źródłowej/modelu bazowego skompilowanego za pomocą nagrania głosu z wielu różnych głośników.          |
| Dane szkoleniowe | Niestandardowy zestaw danych szkolenia głosowego neuronowych, który obejmuje nagrania audio talent głosu i powiązane transkrypcje tekstu.                                                                                                                                                                                                                                                               |
| Osoba       | Osoba, która opisuje, kto ma być tym głosem. Dobry projekt osoby będzie informować wszystkie o tworzeniu głosu, niezależnie od tego, czy wybierasz już utworzony dostępny model głosowy, czy zaczynasz od zera przez rzutowanie i nagrywanie nowego talent głosu.                                                                                                |
| Skrypt        | Skrypt to plik tekstowy, który zawiera wyrażenia długości, który ma być mówiony przez talent głosu. (Termin "*wyrażenia długości*" obejmuje zarówno pełne zdania, jak i krótsze frazy.                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Odpowiedzialne użycie sztucznej inteligencji

Aby dowiedzieć się, jak używać niestandardowej neuronowych głosu, zobacz [uwagi dotyczące przezroczystości](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=/azure/cognitive-services/speech-service/context/context). Przejrzyste notatki firmy Microsoft mają na celu ułatwienie zrozumienia sposobu działania technologii AI. właściciele systemu wyboru mogą mieć wpływ na wydajność i zachowanie systemu oraz znaczenie dla całego systemu, w tym technologii, osób i środowiska.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Custom Voice](how-to-custom-voice.md)
* [Tworzenie niestandardowego punktu końcowego głosu i korzystanie z niego](how-to-custom-voice-create-voice.md)