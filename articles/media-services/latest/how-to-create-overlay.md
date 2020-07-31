---
title: Jak utworzyć nakładkę z Media Encoder Standard
description: Dowiedz się, jak utworzyć nakładkę z Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 0d1a6d5626e081ff50f65b3a4396e223b33f920d
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433639"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Jak utworzyć nakładkę z Media Encoder Standard

Media Encoder Standard umożliwia nałożenie obrazu na istniejący film wideo. Obecnie obsługiwane są następujące formaty: PNG, jpg, GIF i BMP.

## <a name="prerequisites"></a>Wymagania wstępne

* Zbierz informacje o koncie, które są potrzebne do skonfigurowania *appsettings.js* w pliku w przykładzie. Jeśli nie masz pewności, jak to zrobić, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Poniższe wartości są oczekiwane w *appsettings.js* pliku.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Jeśli nie znasz jeszcze przekształceń, zaleca się wykonanie następujących czynności:

* Odczytywanie [kodowania wideo i audio przy użyciu Media Services](encoding-concept.md)
* Przeczytaj [, jak kodować przy użyciu niestandardowego przekształcenia — .NET](customize-encoder-presets-how-to.md). Wykonaj kroki opisane w tym artykule, aby skonfigurować platformę .NET, która jest wymagana do pracy z transformacjemi, a następnie wróć tutaj, aby wypróbować wstępnie ustawioną pozycję nakładki.
* Zobacz [dokument odwołania transformacje](https://docs.microsoft.com/rest/api/media/transforms).

Po zapoznaniu się z transformacjemi Pobierz przykład nakładki.

## <a name="overlays-preset-sample"></a>Przykład predefiniowanych ustawień

Pobierz [próbkę nakładki multimediów i usług](https://github.com/Azure-Samples/media-services-overlays) , aby rozpocząć pracę z nakładkami.

## <a name="next-steps"></a>Następne kroki

* [Podcinanie wideo przy kodowaniu przy użyciu Media Services-.NET](subclip-video-dotnet-howto.md)