---
title: 'Szybki Start: używanie poleceń niestandardowych z niestandardowym głosem (wersja zapoznawcza) — usługa mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule określisz wyjściowy dźwięk aplikacji poleceń niestandardowych.
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872465"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>Szybki Start: używanie poleceń niestandardowych z niestandardowym głosem (wersja zapoznawcza)

W [poprzednim artykule](./quickstart-custom-speech-commands-create-parameters.md)został utworzony nowy projekt poleceń niestandardowych w celu reagowania na polecenia z parametrami.

W tym artykule wybierzemy niestandardowy głos wyjściowy dla utworzonej aplikacji.

## <a name="select-a-custom-voice"></a>Wybieranie niestandardowego głosu

1. Otwórz projekt, który został [wcześniej utworzony](./quickstart-custom-speech-commands-create-parameters.md)
1. Wybierz pozycję **Ustawienia** w okienku po lewej stronie
1. Wybieranie **niestandardowego głosu** z okienka środkowego
1. Wybieranie żądanego niestandardowego lub publicznego głosu z tabeli
1. Wybierz pozycję **Zapisz**

> [!div class="mx-imgBorder"]
> ![Przykładowe zdania z parametrami](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - Dla **publicznych głosów** **typy neuronowych** są dostępne tylko dla określonych regionów. Aby sprawdzić dostępność, zobacz [głosy standardowe i neuronowych według regionu/punktu końcowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - W przypadku **niestandardowych głosów**można je utworzyć na stronie niestandardowego projektu głosu. Zobacz Rozpoczynanie [pracy z niestandardowym głosem](./how-to-custom-voice.md).

Teraz aplikacja będzie odpowiadać w wybranym głosowaniu zamiast domyślnego głosu.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Szybki Start: Nawiązywanie połączenia z aplikacją niestandardową za pomocą zestawu Speech SDK (wersja zapoznawcza)](./quickstart-custom-speech-commands-speech-sdk.md)

