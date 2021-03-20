---
title: Dorosła, erotycznej, gorii Content — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z wykrywaniem treści dla dorosłych w obrazach przy użyciu interfejs API przetwarzania obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d5961ecae2fbc154ae6f1acd74df2bb74024fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96532622"
---
# <a name="detect-adult-content"></a>Wykrywanie treści dla dorosłych

Przetwarzanie obrazów może wykryć dorosłe materiały w obrazach, dzięki czemu deweloperzy mogą ograniczyć wyświetlanie tych obrazów w oprogramowaniu. Flagi zawartości są stosowane z wynikami z zakresu od 0 do jednego, aby deweloperzy mogli interpretować wyniki zgodnie z ich własnymi preferencjami.

> [!NOTE]
> Wiele z tych funkcji jest oferowanych przez usługę [Content moderator platformy Azure](../content-moderator/overview.md) . Zapoznaj się z tą alternatywą dla rozwiązań bardziej rygorystycznych scenariuszy moderowania zawartości, takich jak moderowanie tekstu i przepływy pracy przeglądu przez ludzi.

## <a name="content-flag-definitions"></a>Definicje flag zawartości

Klasyfikacja "Dorosła" zawiera kilka różnych kategorii:

- Obrazy **dla dorosłych** są wyraźnie sekse i często pokazują nagość i seks.
- Obrazy **erotycznej** są z natury płciowo sugerowane i często zawierają mniej wyraźną zawartość w postaci seksu niż obrazy otagowane jako **osoba dorosła**.
- Obrazy **gorii** pokazują krew/Gore.

## <a name="use-the-api"></a>Używanie interfejsu API

Możesz wykryć zawartość dla dorosłych za pomocą interfejsu API [analizy obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Po dodaniu wartości `Adult` do parametru zapytania **visualFeatures** interfejs API zwraca trzy właściwości logiczne &mdash; `isAdultContent` , `isRacyContent` i `isGoryContent` &mdash; w swojej odpowiedzi JSON. Metoda zwraca również odpowiadające im właściwości &mdash; `adultScore` , `racyScore` i `goreScore` &mdash; reprezentujące wyniki zaufania między zerem a jedną dla każdej odpowiedniej kategorii.

- [Szybki Start: przetwarzanie obrazów interfejsów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
