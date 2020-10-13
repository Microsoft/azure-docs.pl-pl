---
title: Używanie atrybutu HeadPose
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak używać atrybutu HeadPose, aby automatycznie obrócić prostokąt powierzchni lub wykryć gesty głowy w strumieniu wideo.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: fe9cc44542e97880b076d871dde82f5a02a58957
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88928976"
---
# <a name="use-the-headpose-attribute"></a>Używanie atrybutu HeadPose

W tym przewodniku zobaczysz, jak można użyć atrybutu HeadPose wykrytej właściwości, aby włączyć niektóre kluczowe scenariusze.

## <a name="rotate-the-face-rectangle"></a>Obróć prostokąt czołowy

Prostokąt z powierzchnią zwrócony po każdej wykrytej powierzchni oznacza lokalizację i rozmiar powierzchni na obrazie. Domyślnie prostokąt jest zawsze wyrównany do obrazu (jego boki są pionowe i poziome); może to być niewydajne w przypadku ramek z pochyloną buzią. W sytuacjach, w których chcesz programowo przyciąć twarze na obrazie, lepiej jest obrócić prostokąt, aby przyciąć.

Przykładowa aplikacja platformy [WPF w Cognitive Services kroju](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) używa atrybutu HeadPose, aby obrócić wykryte prostokąty powierzchni.

### <a name="explore-the-sample-code"></a>Poznawanie przykładowego kodu

Można programistycznie obrócić prostokąt powierzchni przy użyciu atrybutu HeadPose. Jeśli ten atrybut zostanie określony podczas wykrywania twarzy (zobacz [jak wykrywać twarze](HowtoDetectFacesinImage.md)), będzie można wysłać do niego zapytanie później. Poniższa metoda w aplikacji [WPF Cognitive Services kroju](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) pobiera listę obiektów **DetectedFace** i zwraca listę obiektów **[czołowych](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** . W **tym miejscu jest** klasą niestandardową, która przechowuje dane powierzchni, łącznie ze zaktualizowanymi współrzędnymi prostokąta. Nowe wartości są obliczane dla opcji **Top**, **Left**, **Width**i **Height**, a nowe pole **FaceAngle** określa obrót.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>Wyświetl zaktualizowany prostokąt

W tym miejscu możesz użyć zwracanych obiektów **czołowych** na ekranie. Poniższe wiersze z [FaceDetectionPage. XAML](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) pokazują, jak nowy prostokąt jest renderowany na podstawie tych danych:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Wykrywanie gestów głównych

Możesz wykrywać gesty głowy, takie jak Nodding i powstrząsając, śledząc zmiany HeadPose w czasie rzeczywistym. Tej funkcji można użyć jako niestandardowego detektora na żywo.

Wykrywanie wartości na żywo to zadanie określenia, że podmiot jest rzeczywistą osobą, a nie z obrazem lub reprezentacją wideo. Detektor gestu głowy może pomóc w sprawdzeniu, czy jest to sposób na żywo, szczególnie w przeciwieństwie do obrazu reprezentującego osobę.

> [!CAUTION]
> Aby wykrywać gesty głowy w czasie rzeczywistym, należy wywołać interfejs API rozpoznawania twarzy z dużą szybkością (więcej niż raz na sekundę). Jeśli masz subskrypcję warstwy Bezpłatna (F0), nie będzie to możliwe. Jeśli masz subskrypcję płatnej warstwy, upewnij się, że zostały obliczone koszty tworzenia szybkich wywołań interfejsu API na potrzeby wykrywania gestów głównych.

Zapoznaj się z przykładem [Front HeadPose](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) w witrynie GitHub, aby zapoznać się z przykładem wykrywania gestów głównych.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z aplikacją [WPF dla Cognitive Services kroju](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) w witrynie GitHub, aby zapoznać się z przykładem dla obróconych prostokątów powierzchni. Można też zapoznać się z [przykładową aplikacją HeadPose Front](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) , która śledzi atrybut HeadPose w czasie rzeczywistym w celu wykrycia przesunięć głównych.