---
title: 'Instrukcje: korzystanie z poleceń niestandardowych za pomocą niestandardowej usługi rozpoznawania mowy'
titleSuffix: Azure Cognitive Services
description: W tym artykule określisz wyjściowy dźwięk aplikacji poleceń niestandardowych.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: sausin
ms.openlocfilehash: b1e488653f210089ee5ed6757fb2f3d1e4fb0fe4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294301"
---
# <a name="use-custom-commands-with-custom-voice"></a>Używanie poleceń niestandardowych z głosem niestandardowym

W tym artykule opisano sposób wybierania niestandardowego głosu wyjściowego dla aplikacji poleceń niestandardowych.

## <a name="select-a-custom-voice"></a>Wybieranie niestandardowego głosu

1. W aplikacji polecenia niestandardowe wybierz pozycję **Ustawienia** w okienku po lewej stronie.
1. Wybierz opcję **niestandardowy głos** w środkowym okienku.
1. Wybierz żądany niestandardowy lub publiczny głos z tabeli.
1. Wybierz pozycję **Zapisz**.

> [!div class="mx-imgBorder"]
> ![Przykładowe zdania z parametrami](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Dla **publicznych głosów** **typy neuronowych** są dostępne tylko dla określonych regionów. Aby sprawdzić dostępność, zobacz [głosy standardowe i neuronowych według regionu/punktu końcowego](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).
> - W przypadku **niestandardowych głosów**można je utworzyć na stronie niestandardowego projektu głosu. Zobacz Rozpoczynanie [pracy z niestandardowym głosem](./how-to-custom-voice.md).

Teraz aplikacja będzie odpowiadać w wybranym głosowaniu zamiast domyślnego głosu.
