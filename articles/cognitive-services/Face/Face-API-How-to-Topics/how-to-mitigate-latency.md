---
title: Jak ograniczyć czas oczekiwania podczas korzystania z usługi kroju
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak ograniczyć czas oczekiwania podczas korzystania z usługi kroju.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 1/5/2021
ms.author: v-jawe
ms.openlocfilehash: 2c771509de5ac246bac0d8e006a5d0b884a410b0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706813"
---
# <a name="how-to-mitigate-latency-when-using-the-face-service"></a>Instrukcje: zmniejszanie opóźnienia podczas korzystania z usługi kroju

W przypadku korzystania z usługi kroju mogą wystąpić opóźnienia. Opóźnienie odnosi się do dowolnego rodzaju opóźnienia występującego podczas komunikacji przez sieć. Ogólnie rzecz biorąc, możliwe przyczyny opóźnienia obejmują:
- Odległość fizyczna każdego pakietu musi być przenoszona ze źródła do miejsca docelowego.
- Problemy z nośnikiem transmisji.
- Błędy w routerach lub przełącznikach wzdłuż ścieżki transmisji.
- Czas wymagany przez aplikacje antywirusowe, zapory i inne mechanizmy zabezpieczeń do inspekcji pakietów.
- Usterki w aplikacjach klienta lub serwera.

W tym temacie omówiono możliwe przyczyny opóźnienia związane z korzystaniem z usługi Azure Cognitive Services i sposoby ograniczenia tych przyczyn.

> [!NOTE]
> Usługa Azure Cognitive Services nie zapewnia żadnych Umowa dotycząca poziomu usług (SLA) dotyczących opóźnień.

## <a name="possible-causes-of-latency"></a>Możliwe przyczyny opóźnienia

### <a name="slow-connection-between-the-cognitive-service-and-a-remote-url"></a>Wolne połączenie między usługą poznawczej a zdalnym adresem URL

Niektóre Cognitive Services platformy Azure zapewniają metody, które uzyskują dane ze zdalnego adresu URL, który jest udostępniany. Na przykład po wywołaniu [metody DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithUrlAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_String_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) usługi twarzy można określić adres URL obrazu, w którym usługa próbuje wykryć twarze.

```csharp
var faces = await client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
```

Usługa kroju musi następnie pobrać obraz z serwera zdalnego. Jeśli połączenie z usługą Front Service do serwera zdalnego działa wolno, co będzie miało wpływ na czas odpowiedzi metody wykrywania.

Aby rozwiązać ten problem, należy rozważyć [przechowywanie obrazu w usłudze Azure Premium BLOB Storage](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Na przykład:

``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```

### <a name="large-upload-size"></a>Duży rozmiar przekazywania

Niektóre Cognitive Services platformy Azure zapewniają metody, które uzyskują dane z przekazanego pliku. Na przykład po wywołaniu [metody DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync#Microsoft_Azure_CognitiveServices_Vision_Face_FaceOperationsExtensions_DetectWithStreamAsync_Microsoft_Azure_CognitiveServices_Vision_Face_IFaceOperations_System_IO_Stream_System_Nullable_System_Boolean__System_Nullable_System_Boolean__System_Collections_Generic_IList_System_Nullable_Microsoft_Azure_CognitiveServices_Vision_Face_Models_FaceAttributeType___System_String_System_Nullable_System_Boolean__System_String_System_Threading_CancellationToken_) usługi twarzy można przekazać obraz, w którym usługa próbuje wykryć twarze.

```csharp
using FileStream fs = File.OpenRead(@"C:\images\face.jpg");
System.Collections.Generic.IList<DetectedFace> faces = await client.Face.DetectWithStreamAsync(fs, detectionModel: DetectionModel.Detection02);
```

Jeśli plik do przekazania jest duży, wpłynie to na czas odpowiedzi `DetectWithStreamAsync` metody z następujących powodów:
- Przekazywanie pliku trwa dłużej.
- Przetworzenie pliku przez usługę dłużej nie jest możliwe, proporcjonalnie do rozmiaru pliku.

Środki zaradcze
- Rozważ [przechowywanie obrazu w usłudze Azure Premium BLOB Storage](../../../storage/blobs/storage-upload-process-images.md?tabs=dotnet). Na przykład:
``` csharp
var faces = await client.Face.DetectWithUrlAsync("https://csdx.blob.core.windows.net/resources/Face/Images/Family1-Daughter1.jpg");
```
- Rozważ przekazanie mniejszego pliku.
    - Zapoznaj się z wytycznymi dotyczącymi [danych wejściowych na potrzeby wykrywania](../concepts/face-detection.md#input-data) i [wprowadzania danych w celu rozpoznawania kroju](../concepts/face-recognition.md#input-data).
    - W przypadku wykrywania wydajności podczas korzystania z modelu wykrywania `DetectionModel.Detection01` zmniejszenie rozmiaru pliku obrazu spowoduje zwiększenie szybkości przetwarzania. W przypadku korzystania z modelu wykrywania `DetectionModel.Detection02` zmniejszenie rozmiaru pliku obrazu spowoduje zwiększenie szybkości przetwarzania tylko wtedy, gdy plik obrazu jest mniejszy niż 1920 x 1080.
    - W celu rozpoznawania kroju zmniejszenie rozmiaru kroju do 200x200 pikseli nie ma wpływu na dokładność modelu rozpoznawania.
    - Wydajność `DetectWithUrlAsync` i `DetectWithStreamAsync` metody są również zależne od liczby twarzy w obrazie. Usługa twarzy może zwrócić nawet 100 twarzy dla obrazu. Powierzchnie są klasyfikowane według rozmiaru prostokąta twarzy od dużych do małych.
    - Jeśli musisz wywołać wiele metod usługi, rozważ wywołanie ich równolegle, jeśli projekt aplikacji pozwala na to. Jeśli na przykład chcesz wykryć twarze w dwóch obrazach, aby wykonać porównanie twarzy:
```csharp
var faces_1 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1MzAyNzYzOTgxNTE0NTEz/john-f-kennedy---mini-biography.jpg");
var faces_2 = client.Face.DetectWithUrlAsync("https://www.biography.com/.image/t_share/MTQ1NDY3OTIxMzExNzM3NjE3/john-f-kennedy---debating-richard-nixon.jpg");
Task.WaitAll (new Task<IList<DetectedFace>>[] { faces_1, faces_2 });
IEnumerable<DetectedFace> results = faces_1.Result.Concat (faces_2.Result);
```

### <a name="slow-connection-between-your-compute-resource-and-the-face-service"></a>Wolne połączenie między zasobem obliczeniowym i usługą twarzą

Jeśli komputer ma wolne połączenie z usługą kroju usługi, wpłynie to na czas odpowiedzi na metody usługi.

Środki zaradcze
- Podczas tworzenia subskrypcji usługi Marketo upewnij się, że wybrano region znajdujący się najbliżej lokalizacji, w której znajduje się aplikacja.
- Jeśli musisz wywołać wiele metod usługi, rozważ wywołanie ich równolegle, jeśli projekt aplikacji pozwala na to. Zapoznaj się z poprzednią sekcją przykładu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku wyjaśniono, jak ograniczyć czas oczekiwania podczas korzystania z usługi kroju. Następnie Dowiedz się, jak skalować w górę od istniejącej osoby i FaceList obiekty do obiektów LargePersonGroup i LargeFaceList.

> [!div class="nextstepaction"]
> [Przykład: korzystanie z funkcji dużej skali](how-to-use-large-scale.md)

## <a name="related-topics"></a>Powiązane tematy

- [Dokumentacja referencyjna (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Dokumentacja referencyjna (zestaw SDK dla platformy .NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)