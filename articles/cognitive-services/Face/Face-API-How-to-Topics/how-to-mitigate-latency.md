---
title: Jak zmniejszyć opóźnienie podczas korzystania z usługi rozpoznawania twarzy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zmniejszyć opóźnienie podczas korzystania z usługi rozpoznawania twarzy.
services: cognitive-services
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.openlocfilehash: a306883573387a2a5c20a53c7015c6dbd3eddf65
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878672"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>How to: mitigate latency when using the Face service (Jak zmniejszyć opóźnienie podczas korzystania z usługi rozpoznawania twarzy)

Podczas korzystania z usługi rozpoznawania twarzy może wystąpić opóźnienie. Opóźnienie odnosi się do dowolnego rodzaju opóźnienia, które występuje podczas komunikacji za pośrednictwem sieci. Ogólnie rzecz biorąc, możliwe przyczyny opóźnienia obejmują:
- Fizyczna odległość, z którego każdy pakiet musi przebyć ze źródła do miejsca docelowego.
- Problemy ze nośnikiem transmisji.
- Błędy w routerach lub przełącznikach wzdłuż ścieżki transmisji.
- Czas wymagany przez aplikacje antywirusowe, zapory i inne mechanizmy zabezpieczeń do sprawdzania pakietów.
- Awarie w aplikacjach klienckich lub serwerowych.

W tym temacie opisano możliwe przyczyny opóźnienia specyficzne dla używania Azure Cognitive Services oraz sposób ograniczenia tych przyczyn.

> [!NOTE]
> Azure Cognitive Services nie zawierają żadnych Umowa dotycząca poziomu usług (SLA) dotyczących opóźnień.

## <a name="possible-causes-of-latency"></a>Możliwe przyczyny opóźnienia

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Powolne połączenie między usługą Cognitive Service i zdalnym adresem URL

Niektóre Azure Cognitive Services metody uzyskiwania danych ze zdalnego adresu URL, który należy podać. Na przykład podczas wywołania metody [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) usługi rozpoznawania twarzy można określić adres URL obrazu, na którym usługa próbuje wykryć twarze.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

Usługa rozpoznawania twarzy musi następnie pobrać obraz z serwera zdalnego. Jeśli połączenie z usługi rozpoznawania twarzy do serwera zdalnego jest powolne, będzie to miało wpływ na czas odpowiedzi metody Detect.

Aby temu uniknąć, rozważ [zapisanie obrazu w usłudze Azure Premium Blob Storage](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Na przykład:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Duży rozmiar przekazywania

Niektóre Azure Cognitive Services metody, które mogą uzyskiwać dane z pliku, który jest przesyłany. Na przykład po wywołaniu metody [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) usługi rozpoznawania twarzy można przekazać obraz, na którym usługa próbuje wykryć twarze.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Jeśli plik do przekazania jest duży, będzie to miało wpływ na czas odpowiedzi metody `DetectWithStreamAsync` z następujących powodów:
- Przekazywanie pliku trwa dłużej.
- Przetwarzanie pliku zajmuje usłudze dłużej, proporcjonalnie do rozmiaru pliku.

Czynniki:
- Rozważ [zapisanie obrazu w usłudze Azure Premium Blob Storage](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Na przykład:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Rozważ przekazanie mniejszego pliku.
    - Zapoznaj się z wytycznymi [dotyczącymi danych wejściowych na temat wykrywania twarzy](../concepts/face-detection.md#input-data) i [danych wejściowych na temat rozpoznawania twarzy.](../concepts/face-recognition.md#input-data)
    - W przypadku wykrywania twarzy w przypadku korzystania z modelu wykrywania zmniejszenie rozmiaru pliku `DetectionModel.Detection01` obrazu zwiększa szybkość przetwarzania. W przypadku korzystania z modelu wykrywania zmniejszenie rozmiaru pliku obrazu zwiększa szybkość przetwarzania tylko wtedy, gdy rozmiar pliku obrazu jest mniejszy niż `DetectionModel.Detection02` 1920 x 1080.
    - W przypadku rozpoznawania twarzy zmniejszenie rozmiaru twarzy do 200 x 200 pikseli nie wpływa na dokładność modelu rozpoznawania.
    - Wydajność metod i zależy również od liczby twarzy `DetectWithUrlAsync` `DetectWithStreamAsync` na obrazie. Usługa rozpoznawania twarzy może zwrócić do 100 twarzy dla obrazu. Twarze są klasyfikowane według rozmiaru prostokąta twarzy od dużego do małego.
    - Jeśli musisz wywołać wiele metod usługi, rozważ wywołanie ich równolegle, jeśli projekt aplikacji na to pozwala. Jeśli na przykład chcesz wykrywać twarze na dwóch obrazach, aby przeprowadzić porównanie twarzy:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Powolne połączenie między zasobem obliczeniowym a usługą rozpoznawania twarzy

Jeśli komputer ma powolne połączenie z usługą rozpoznawania twarzy, będzie to miało wpływ na czas odpowiedzi metod usługi.

Czynniki:
- Podczas tworzenia subskrypcji rozpoznawania twarzy upewnij się, że wybierasz region najbliżej miejsca hostowanej aplikacji.
- Jeśli musisz wywołać wiele metod usługi, rozważ ich równoległe wywoływanie, jeśli projekt aplikacji na to pozwala. Przykład można znaleźć w poprzedniej sekcji.
- Jeśli dłuższe opóźnienia mają wpływ na środowisko użytkownika, wybierz próg limitu czasu (np. maksymalnie 5 s) przed ponowieniem próby wywołania interfejsu API.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku opisano sposób ograniczania opóźnień podczas korzystania z usługi rozpoznawania twarzy. Następnie dowiesz się, jak skalować w górę z istniejących obiektów PersonGroup i FaceList odpowiednio do obiektów LargePersonGroup i LargeFaceList.

> [!div class="nextstepaction"]
> [Przykład: używanie funkcji na dużą skalę](how-to-use-large-scale.md)

## <a name="related-topics"></a>Powiązane tematy

- [Dokumentacja referencyjna (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Dokumentacja referencyjna (zestaw SDK platformy .NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
