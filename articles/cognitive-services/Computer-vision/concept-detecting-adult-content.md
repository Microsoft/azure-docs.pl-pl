---
title: Zawartość dla dorosłych, racy, gory — przetwarzanie obrazów
titleSuffix: Azure Cognitive Services
description: Pojęcia związane z wykrywaniem zawartości dla dorosłych na obrazach przy użyciu przetwarzanie obrazów API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceef604fe07a11be89376e26c6fecc49298ebacf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778863"
---
# <a name="detect-adult-content"></a>Wykrywanie zawartości dla dorosłych

przetwarzanie obrazów wykrywać treści dla dorosłych na obrazach, dzięki czemu deweloperzy mogą ograniczyć wyświetlanie tych obrazów w oprogramowaniu. Flagi zawartości są stosowane z wynikiem od 0 do 1, dzięki czemu deweloperzy mogą interpretować wyniki zgodnie z własnymi preferencjami.

> [!NOTE]
> Większość tej funkcji jest oferowana przez [usługę Azure Content Moderator](../content-moderator/overview.md) Service. Zapoznaj się z tą alternatywą dla rozwiązań bardziej rygorystycznych scenariuszy moderowania zawartości, takich jak moderowanie tekstu i przepływy pracy przeglądu przez ludzi.

## <a name="content-flag-definitions"></a>Definicje flagi zawartości

Klasyfikacja "dla dorosłych" zawiera kilka różnych kategorii:

- **Obrazy** dla dorosłych są jawnie seksualne i często wykazują pogoręć i czyny seksualne.
- **Obrazy erotywne** mają charakter seksualny i często zawierają mniej o charakterze seksualnym niż obrazy oznaczone jako **Obrazy dla dorosłych.**
- **Obrazy Gory'ego** pokazują cycy/gąb.

## <a name="use-the-api"></a>Używanie interfejsu API

Możesz wykrywać zawartość dla dorosłych za pomocą [interfejsu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API analizowanie obrazów API. Po dodaniu wartości do parametru `Adult` **zapytania visualFeatures** interfejs API zwraca trzy właściwości logiczne , i w &mdash; `isAdultContent` odpowiedzi `isRacyContent` `isGoryContent` &mdash; JSON. Metoda zwraca również odpowiednie właściwości , i , które reprezentują oceny ufności z &mdash; `adultScore` `racyScore` `goreScore` &mdash; przedziału od zera do jednego dla każdej odpowiedniej kategorii.

- [Szybki start: przetwarzanie obrazów API REST lub bibliotek klienckich](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
