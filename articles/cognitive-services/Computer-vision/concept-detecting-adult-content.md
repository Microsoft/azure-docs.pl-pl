---
title: Dorosła, erotycznej, gorii Content — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z wykrywaniem treści dla dorosłych w obrazach przy użyciu interfejsu APi przetwarzanie obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7e41eb0f6a61f7b195e251739ae93207c731cac5
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535881"
---
# <a name="detect-adult-content"></a>Wykrywanie treści dla dorosłych

Przetwarzanie obrazów może wykryć dorosłe materiały w obrazach, dzięki czemu deweloperzy mogą ograniczyć wyświetlanie tych obrazów w oprogramowaniu. Flagi zawartości są stosowane z wynikiem między wartością zero a jedną, tak aby deweloperzy mogli interpretować wyniki zgodnie z ich własnymi preferencjami.

> [!NOTE]
> Wiele z tych funkcji jest oferowanych przez usługę [Content moderator platformy Azure](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) . Zapoznaj się z tą alternatywą dla rozwiązań bardziej rygorystycznych scenariuszy moderowania zawartości, takich jak moderowanie tekstu i przepływy pracy przeglądu przez ludzi.

## <a name="content-flag-definitions"></a>Definicje flag zawartości

W ramach klasyfikacji "Dorosła" są kilka różnych kategorii:

- Obrazy **dla dorosłych** są definiowane jako te, które są wyraźnie płciowo i często przedstawiają nagość i seks.
- Obrazy **erotycznej** są definiowane jako obrazy, które mają charakter płciowo sugerujący i często zawierają mniej wyraźną zawartość z Seksem niż obrazy otagowane jako **osoba dorosła**.
- Obrazy **gorii** są definiowane jako te, które przedstawiają Gore.

## <a name="use-the-api"></a>Używanie interfejsu API

Możesz wykryć zawartość dla dorosłych za pomocą interfejsu API [analizy obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Po dodaniu wartości `Adult` do parametru zapytania **visualFeatures** interfejs API zwraca trzy właściwości logiczne &mdash; `isAdultContent` , `isRacyContent` i `isGoryContent` &mdash; w swojej odpowiedzi JSON. Metoda zwraca również odpowiadające im właściwości &mdash; `adultScore` , `racyScore` i `goreScore` &mdash; reprezentujące wyniki zaufania między zerem a jedną dla każdej odpowiedniej kategorii.

- [Szybki Start: analizowanie obrazu (zestaw SDK dla platformy .NET)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Szybki Start: analizowanie obrazu (interfejs API REST)](./quickstarts/csharp-analyze.md)
