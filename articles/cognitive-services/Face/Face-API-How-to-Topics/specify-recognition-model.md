---
title: Jak określić model rozpoznawania — kroju
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób wybierania modelu rozpoznawania, który ma być używany z aplikacją platformy Azure.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: 016b8bf010f597e963e0901d1ec48486f79bbb35
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913130"
---
# <a name="specify-a-face-recognition-model"></a>Określanie modelu rozpoznawania twarzy

W tym przewodniku pokazano, jak określić model rozpoznawania kroju na potrzeby wykrywania i identyfikowania kroju oraz wyszukiwania podobieństw przy użyciu usługi Azure Search.

Usługa twarzy używa modeli uczenia maszynowego do wykonywania operacji na ludzkich twarzy na obrazach. Kontynuujemy ulepszanie dokładności naszych modeli w oparciu o opinie klientów i postępy w badaniach, a firma Microsoft zapewnia te udoskonalenia jako aktualizacje modelu. Deweloperzy mogą określić, która wersja modelu rozpoznawania kroju ma być używana; mogą wybrać model, który najlepiej pasuje do swojego przypadku użycia.

Usługa Azure Front Service ma dostępne trzy modele rozpoznawania. Modele _recognition_01_ (opublikowane 2017) i _recognition_02_ (opublikowane 2019) są stale obsługiwane w celu zapewnienia zgodności z poprzednimi wersjami dla klientów korzystających z FaceLists lub **osób** , które utworzyły te modele. **FaceList** lub **osoba** musi zawsze używać modelu rozpoznawania, który został utworzony za pomocą, a nowe twarze zostaną skojarzone z tym modelem po dodaniu. Tego nie można zmienić po utworzeniu, a klienci będą musieli używać odpowiedniego modelu rozpoznawania z odpowiednimi **FaceListami** lub **osobami** .

Możesz przechodzić do nowszych modeli rozpoznawania we własnym wygodie. należy jednak utworzyć nowe FaceLists i grup osób wynosi z wybranym modelem rozpoznawania.

Model _recognition_03_ (opublikowany 2020) jest najdokładniejszym modelem, który jest obecnie dostępny. Jeśli jesteś nowym klientem, zalecamy korzystanie z tego modelu. _Recognition_03_ zapewni lepszą dokładność zarówno porównania podobieństwa, jak i porównania dopasowania przez osoby. Należy zauważyć, że każdy model działa niezależnie od innych, a próg ufności ustawiony dla jednego modelu nie jest przeznaczony do porównania między innymi modelami rozpoznawania.

Przeczytaj, aby dowiedzieć się, jak określić wybrany model w różnych operacjach tworzenia kroju, unikając konfliktów modelu. Jeśli jesteś użytkownikiem zaawansowanym i chcesz określić, czy chcesz przełączyć się do najnowszego modelu, przejdź do sekcji [Oceń różne modele](#evaluate-different-models) , aby obliczyć nowy model i porównać wyniki przy użyciu bieżącego zestawu danych.


## <a name="prerequisites"></a>Wymagania wstępne

Należy zapoznać się z pojęciami wykrywania i identyfikacji elementu AI. Jeśli nie, zobacz następujące przewodniki po raz pierwszy:

* [Pojęcia dotyczące wykrywania czołowego](../concepts/face-detection.md)
* [Pojęcia dotyczące rozpoznawania](../concepts/face-recognition.md)
* [Jak wykrywać twarze w obrazie](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>Wykrywanie twarzy z określonym modelem

Wykrywanie twarzy służy do identyfikowania wizualizacji ludzkich i znajdowania ich lokalizacji granicznych. Wyodrębnia również funkcje i zapisuje je do użycia w identyfikacji. Wszystkie te informacje stanowią reprezentację jednej z nich.

Model rozpoznawania jest używany podczas wyodrębniania funkcji rozpoznawania, dlatego można określić wersję modelu podczas wykonywania operacji wykrywania.

W przypadku korzystania z interfejsu API [wykrywania kroju] i przypisania wersji modelu do `recognitionModel` parametru. Dostępne są następujące wartości:
* recognition_01
* recognition_02
* recognition_03


Opcjonalnie można określić parametr _returnRecognitionModel_ (domyślnie **false** ), aby wskazać, czy _recognitionModel_ powinien być zwracany w odpowiedzi. Dlatego adres URL żądania dla interfejsu API REST [wykrywania czołowego] będzie wyglądać następująco:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Jeśli używasz biblioteki klienckiej, możesz przypisać wartość `recognitionModel` przez przekazanie ciągu reprezentującego wersję. Jeśli pozostawisz ją bez przypisania, `recognition_01` zostanie użyta domyślna wersja modelu. Zobacz Poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Identyfikowanie twarzy z określonym modelem

Usługa kroju może wyodrębnić dane z obrazu i skojarzyć je z obiektem **osoby** (za pośrednictwem wywołania interfejsu API [dodawania](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) , na przykład), a wiele obiektów **osób** może być przechowywanych razem w danej **osobie** . Następnie można porównać nową miarę z grupą **osób** (z wywołaniem o [identyfikacji w miejscu] ) i można zidentyfikować osobę pasującą w tej grupie.

Grupa **osób** powinna mieć jeden unikatowy model rozpoznawania dla wszystkich **osób** i można ją określić przy użyciu `recognitionModel` parametru podczas tworzenia grupy ( [osoba-Create] lub [LargePersonGroup-Create]). Jeśli ten parametr nie jest określony, `recognition_01` używany jest oryginalny model. Grupa zawsze będzie używać modelu rozpoznawania, który został utworzony za pomocą programu, a nowe twarze zostaną skojarzone z tym modelem po ich dodaniu. nie można go zmienić po utworzeniu grupy. Aby sprawdzić model, za pomocą którego jest konfigurowana dana **osoba** , użyj interfejsu API [-Get] z parametrem _returnRecognitionModel_ ustawionym jako **true** .

Zobacz Poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

W tym kodzie zostanie utworzona zbiór **osób** z identyfikatorem `mypersongroupid` i jest on skonfigurowany do korzystania z modelu _recognition_02_ w celu wyodrębnienia funkcji rozpoznawania.

Odpowiednio należy określić model, który ma być używany podczas wykrywania twarzy w celu porównania z tą **osobą** (za pomocą interfejsu API [wykrywanej] przez funkcję). Używany model powinien zawsze być zgodny z konfiguracją **osoby** . w przeciwnym razie operacja zakończy się niepowodzeniem z powodu niezgodnych modeli.

Nie wprowadzono zmian w interfejsie API [identyfikacji czołowej] . wystarczy określić wersję modelu do wykrycia.

## <a name="find-similar-faces-with-specified-model"></a>Znajdź podobne twarze z określonym modelem

Możesz również określić model rozpoznawania dla wyszukiwania podobieństwa. Wersję modelu można przypisać do programu `recognitionModel` podczas tworzenia listy czołowej za pomocą interfejsu API [FaceList-Create] lub [LargeFaceList-Create]. Jeśli ten parametr nie jest określony, `recognition_01` model jest używany domyślnie. Lista twarzy zawsze będzie używać modelu rozpoznawania, który został utworzony za pomocą programu, a nowe twarze zostaną skojarzone z tym modelem, gdy zostaną dodane do listy. nie można zmienić tego po utworzeniu. Aby sprawdzić model, z którym jest skonfigurowana lista czołowa, użyj interfejsu API [FaceList-Get] z parametrem _returnRecognitionModel_ ustawionym jako **true** .

Zobacz Poniższy przykład kodu dla biblioteki klienta .NET.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");
```

Ten kod tworzy listę czołową o nazwie `My face collection` przy użyciu modelu _recognition_03_ do wyodrębniania funkcji. Po przeszukiwaniu tej listy twarzy pod kątem podobnych twarzy na nowej wykrytej powierzchni należy wykrycie tej twarzy ( [Wykrywanie twarzy]) przy użyciu modelu _recognition_03_ . Jak w poprzedniej sekcji, model musi być spójny.

Nie wprowadzono zmian w funkcji [Front-Find podobnego] interfejsu API; należy określić wersję modelu tylko do wykrywania.

## <a name="verify-faces-with-specified-model"></a>Weryfikuj twarze z określonym modelem

Interfejs API [weryfikacji twarzy] sprawdza, czy dwie twarze należą do tej samej osoby. Nie wprowadzono zmian w sprawdzaniu interfejsu API w odniesieniu do modeli rozpoznawania, ale można tylko porównać twarze, które zostały wykryte w tym samym modelu.

## <a name="evaluate-different-models"></a>Oceń różne modele

Jeśli chcesz porównać wyniki różnych modeli rozpoznawania na własnych danych, musisz wykonać następujące działania:
1. Utwórz odpowiednio trzy grup osób wynosi przy użyciu _recognition_01_ , _recognition_02_ i _recognition_03_ .
1. Użyj danych obrazu w celu wykrycia twarzy i zarejestrowania ich w **osobnych** w tych trzech **osobach** . 
1. Uczenie grup osób wynosi przy użyciu interfejsu API uczenia użytkowników.
1. Testuj ze stroną i zidentyfikuj wszystkie trzy **osoby** i Porównaj wyniki.


Jeśli zwykle określono próg ufności (wartość z zakresu od zero do 1, która określa, jak pewność, że model musi być identyfikowany jako), może być konieczne użycie różnych progów dla różnych modeli. Próg dla jednego modelu nie powinien być współużytkowany z innym i nie musi dawać takich samych wyników.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób określania modelu rozpoznawania, który ma być używany z różnymi interfejsami API usługi kroju. Następnie postępuj zgodnie z przewodnikiem Szybki Start, aby rozpocząć korzystanie z wykrywania czołowego.

* [Zestaw SDK platformy .NET](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Zestaw SDK dla języka Python](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Zestaw SDK dla wejść](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face - Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Głowa — Znajdź podobne]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Sprawdzanie kroju]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[Element PersonGroup — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[Osoba — Pobierz]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[Element LargePersonGroup — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList — Pobierz]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList — tworzenie]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc