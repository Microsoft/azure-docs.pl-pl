---
title: Jak generować miniatury przy użyciu standardu Azure Media Services Encoder z platformą .NET
description: W tym artykule pokazano, jak używać platformy .NET do kodowania zasobów i generowania miniatur w tym samym czasie przy użyciu Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 887ac4197321b4015eff7cc839abd8f57ae67760
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512022"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>Jak generować miniatury przy użyciu standardu Encoder w programie .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Za pomocą Media Encoder Standard można wygenerować jeden lub więcej miniatur z wejściowego wideo w formatach plików obrazów [JPEG](https://en.wikipedia.org/wiki/JPEG) lub [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) .

## <a name="recommended-reading-and-practice"></a>Zalecany odczyt i ćwiczenia

Zaleca się zapoznanie się z niestandardowymi transformacjami przez zapoznanie się [z kodowaniem przy użyciu niestandardowego przekształcenia — .NET](customize-encoder-presets-how-to.md).

## <a name="transform-code-example"></a>Przykład kodu przekształcenia

Poniższy przykład kodu tworzy tylko miniaturę.  Należy ustawić następujące parametry:

- **początek** — pozycja w wejściowym filmie wideo z lokalizacji, w której ma zostać rozpoczęte Generowanie miniatur. Wartość może być w formacie ISO 8601 (na przykład PT05S rozpoczyna się od 5 sekund) lub liczba klatek (na przykład 10, aby zacząć od dziesiątej ramki) lub wartość względna do czasu trwania strumienia (na przykład 10% do uruchomienia o 10% czasu trwania strumienia). Obsługuje również makro {Najlepsza}, które informuje koder, aby wybierał najlepszą miniaturę z pierwszych kilku sekund filmu wideo i utworzy tylko jedną miniaturę bez względu na to, jakie inne ustawienia są przeznaczone dla kroku i zakresu. Wartość domyślna to makro {Najlepsza}.
- **krok** — interwały, w których są generowane miniatury. Wartość może być w formacie ISO 8601 (na przykład PT05S dla jednego obrazu co 5 sekund) lub liczba klatek (na przykład 30 dla jednego obrazu co 30 klatek) lub wartość względna do czasu trwania strumienia (na przykład 10% dla jednego obrazu co 10% czasu trwania strumienia). Wartość kroku będzie miała wpływ na pierwszą wygenerowaną miniaturę, która może nie być dokładnie określona w czasie rozpoczęcia przekształceń wstępnej. Jest to spowodowane koderem, który próbuje wybrać najlepszą miniaturę między godziną rozpoczęcia a pozycją kroku od czasu rozpoczęcia jako pierwszy wynik. Ponieważ wartość domyślna to 10%, oznacza to, że jeśli strumień ma długi czas trwania, to pierwsza wygenerowana Miniatura może znajdować się daleko od określonej w czasie rozpoczęcia. Spróbuj wybrać rozsądną wartość dla kroku, jeśli pierwsza miniatura powinna być bliska czasowi rozpoczęcia, lub ustaw wartość zakres na 1, jeśli podczas uruchamiania jest wymagana tylko jedna miniatura.
- **zakres** — pozycja względem predefiniowanego czasu rozpoczęcia w wejściowym wideo, w którym ma zostać zatrzymane Generowanie miniatur. Wartość może być w formacie ISO 8601 (na przykład PT5M30S, aby zatrzymać o 5 minut i 30 sekund od czasu rozpoczęcia) lub liczbę klatek (na przykład 300, aby zatrzymać ją w klatce 300th z ramki w czasie rozpoczęcia. Jeśli ta wartość wynosi 1, oznacza to, że jest tworzona tylko jedna Miniatura w czasie rozpoczęcia lub wartość względna do czasu trwania strumienia (na przykład 50%, aby zatrzymać o połowie okresu trwania strumienia od czasu rozpoczęcia). Wartość domyślna to 100%, co oznacza zatrzymanie na końcu strumienia.
- **warstwy** — kolekcja warstw obrazów wyjściowych, które mają zostać utworzone przez koder.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>Następne kroki
[Generuj miniatury przy użyciu REST](media-services-generate-thumbnails-rest.md)
