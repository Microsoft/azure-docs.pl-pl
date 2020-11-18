---
title: Jak utworzyć nakładkę z Media Encoder Standard
description: Dowiedz się, jak utworzyć nakładkę z Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 743fe146042c7b52394cc4ee8ced49a0f540e79c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844288"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Jak utworzyć nakładkę z Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard umożliwia nałożenie obrazu, pliku audio lub innego wideo na inne wideo. Dane wejściowe muszą określać dokładnie jeden plik. Możesz określić plik obrazu w formacie JPG, PNG, GIF lub BMP albo plik audio (taki jak WAV, MP3, WMA lub M4A) lub plik wideo.


## <a name="prerequisites"></a>Wymagania wstępne

* Zbierz informacje o koncie, które są potrzebne do skonfigurowania *appsettings.js* w pliku w przykładzie. Jeśli nie masz pewności, jak to zrobić, zobacz [Szybki Start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](../../active-directory/develop/quickstart-register-app.md). Poniższe wartości są oczekiwane w *appsettings.js* pliku.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Jeśli nie znasz jeszcze przekształceń, zaleca się wykonanie następujących czynności:

* Odczytywanie [kodowania wideo i audio przy użyciu Media Services](encoding-concept.md)
* Przeczytaj [, jak kodować przy użyciu niestandardowego przekształcenia — .NET](customize-encoder-presets-how-to.md). Wykonaj kroki opisane w tym artykule, aby skonfigurować platformę .NET, która jest wymagana do pracy z transformacjemi, a następnie wróć tutaj, aby wypróbować wstępnie ustawioną pozycję nakładki.
* Zobacz [dokument odwołania transformacje](/rest/api/media/transforms).

Po zapoznaniu się z transformacjemi Pobierz przykład nakładki.

## <a name="overlays-preset-sample"></a>Przykład predefiniowanych ustawień

Pobierz [próbkę nakładki multimediów i usług](https://github.com/Azure-Samples/media-services-overlays) , aby rozpocząć pracę z nakładkami.

## <a name="next-steps"></a>Następne kroki

* [Podcinanie wideo przy kodowaniu przy użyciu Media Services-.NET](subclip-video-dotnet-howto.md)
