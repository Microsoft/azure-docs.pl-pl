---
title: Jak utworzyć nakładkę z Media Encoder Standard
description: Dowiedz się, jak utworzyć nakładkę z Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6d68f35cd7eff745a3d17009b65b1ed73a190173
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490254"
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

* Odczytywanie [kodowania wideo i audio przy użyciu Media Services](encode-concept.md)
* Przeczytaj [, jak kodować przy użyciu niestandardowego przekształcenia — .NET](transform-custom-presets-how-to.md). Wykonaj kroki opisane w tym artykule, aby skonfigurować platformę .NET, która jest wymagana do pracy z transformacjemi, a następnie wróć tutaj, aby wypróbować wstępnie ustawioną pozycję nakładki.
* Zobacz [dokument odwołania transformacje](/rest/api/media/transforms).

Po zapoznaniu się z transformacjemi Pobierz przykład nakładki.

## <a name="overlays-preset-sample"></a>Przykład predefiniowanych ustawień

Pobierz [próbkę nakładki multimediów i usług](https://github.com/Azure-Samples/media-services-overlays) , aby rozpocząć pracę z nakładkami.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
