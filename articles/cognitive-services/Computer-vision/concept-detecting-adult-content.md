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
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "71718513"
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

Możesz wykryć zawartość dla dorosłych za pomocą interfejsu API [analizy obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . Po dodaniu wartości `Adult` do parametru zapytania **visualFeatures** interfejs API zwraca trzy właściwości logiczne &mdash; `isAdultContent` , `isRacyContent` i `isGoryContent` &mdash; w swojej odpowiedzi JSON. Metoda zwraca również odpowiadające im właściwości &mdash; `adultScore` , `racyScore` i `goreScore` &mdash; reprezentujące wyniki zaufania między zerem a jedną dla każdej odpowiedniej kategorii.

- [Szybki Start: analizowanie obrazu (zestaw SDK dla platformy .NET)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Szybki Start: analizowanie obrazu (interfejs API REST)](./quickstarts/csharp-analyze.md)
