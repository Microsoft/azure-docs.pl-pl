---
title: Informacje o wersji — usługa do rozpoznawania
titleSuffix: Azure Cognitive Services
description: Informacje o wersji usługi Front Service obejmują historię zmian wersji dla różnych wersji.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76165866"
---
# <a name="face-release-notes"></a>Informacje o wersji produktu

Ten artykuł odnosi się do usługi systemowej w wersji 1,0.

### <a name="release-changes-in-june-2019"></a>Zmiany wersji w czerwcu 2019

* Dodano nowy model wykrywania twarzy z lepszą dokładnością dla małych, zamknięteych i rozmytych twarze. Korzystaj z niego za pośrednictwem [wykrywania czołowego](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList-Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), persona- [Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) i [LargePersonGroup — Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) miarę, określając nazwę modelu wykrywania nowej czcionki `detection_02` w `detectionModel` parametrze. Więcej szczegółów w temacie [jak określić model wykrywania](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Zmiany wersji z kwietnia 2019

* Ulepszona ogólna dokładność `age` atrybutów i `headPose` . Ten `headPose` atrybut jest również aktualizowany z `pitch` wartością włączoną teraz. Użyj tych atrybutów, określając je w `returnFaceAttributes` parametrze [Wykryj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) wartość `returnFaceAttributes` parametru. 

* Ulepszona szybkość [wykrywania czołowego](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList-Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), Person [-Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) i [LargePersonGroup — Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)miarę.

### <a name="release-changes-in-march-2019"></a>Zmiany wersji w marcu 2019

* Dodano nowy model rozpoznawania kroju ze zwiększoną dokładnością. Za jego pomocą można korzystać z [wykrywania czołowego](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [Personing-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) i [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) , określając nazwę modelu rozpoznawania nowej czcionki `recognition_02` w `recognitionModel` parametrze. Więcej szczegółów na [temat określania modelu rozpoznawania](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Zmiany wersji w styczniu 2019

* Dodano funkcję Snapshot do obsługi migracji danych między subskrypcjami: [migawka](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Więcej szczegółów na [temat migrowania danych do innej subskrypcji programu Marketo](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Zmiany wersji w październiku 2018

* Elegancja opis dla,,, i w odniesieniu do `status` `createdDateTime` `lastActionDateTime` `lastSuccessfulTrainingDateTime` [osoby — Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)stan szkolenia, [LargePersonGroup — Pobierz stan szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)i [LargeFaceList — Pobierz stan szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Zmiany wersji w maju 2018

* Ulepszony `gender` atrybut znacząco i udoskonalony `age` ,,,, `glasses` `facialHair` `hair` `makeup` atrybutów. Użyj ich za pomocą parametru [wykrywania ze stroną](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` . 

* Zwiększony limit rozmiaru pliku obrazu wejściowego z 4 MB do 6 MB w [wykrywaniu czołowym](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [LargeFaceList — Dodaj powierzchnie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), osoba, osoba [-Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) i [LargePersonGroup osoba — Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)miarę.

### <a name="release-changes-in-march-2018"></a>Zmiany wersji w marcu 2018

* Dodano kontener Million-Scale: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) i [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Więcej szczegółów na [temat korzystania z funkcji dużej skali](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Zwiększono możliwość [— Zidentyfikuj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` parametr z [1, 5] do [1, 100] i domyślnie 10.

### <a name="release-changes-in-may-2017"></a>Zmiany wersji w maju 2017

* Dodano `hair` atrybuty,,,,, `makeup` `accessory` `occlusion` `blur` `exposure` , i `noise` w parametrach [wykrywania ze stroną](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .

* Obsługiwane 10 000 osób w ramach danej osoby i [identyfikacji](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)na platformie.

* Obsługiwane stronicowanie w ramach [listy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) osób z parametrami opcjonalnymi: `start` i `top` .

* Obsługiwane współbieżność w dodawaniu i usuwaniu twarzy do różnych FaceLists i różnych osób w ramach osoby.

### <a name="release-changes-in-march-2017"></a>Zmiany wersji w marcu 2017
* Dodano `emotion` atrybut w parametrze [wykrywania ze stroną](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .

* Naprawiono, że nie można było ponownie wykryć powierzchni z prostokątem zwróconym z poziomu [powierzchni](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `targetFace` [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)

* Naprawiono możliwy do wykrycia rozmiar kroju, aby upewnić się, że jest on ściśle 36x36 do 4096x4096 pikseli.

### <a name="release-changes-in-november-2016"></a>Zmiany wersji w listopadzie 2016
* Dodano magazyn twarzy subskrypcję standardową w celu przechowywania dodatkowych utrwalonych [twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [podczas korzystania z tej osoby.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Opłata za przechowywane obrazy jest naliczana przy użyciu stawki wynoszącej 0,5 USD za 1000 twarzy proporcjonalnie do liczby dni. Subskrypcje warstwy Bezpłatna są nadal ograniczone do 1 000 łącznej liczby osób.

### <a name="release-changes-in-october-2016"></a>Zmiany wersji w październiku 2016
* Zmieniono komunikat o błędzie z więcej niż jedną stroną w targetFace z "zawiera więcej niż jedną miarę w obrazie" do "istnieje więcej niż jedna fasada w obrazie" w [FaceList — Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) osobę i osobę, którą można [dodać](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Zmiany wersji w lipcu 2016
* Obsługiwane jest uwierzytelnianie obiektów na potrzeby użytkowników w ramach [weryfikacji na miejscu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Dodano opcjonalny `mode` parametr umożliwiający wybór dwóch trybów roboczych: `matchPerson` i `matchFace` w programie [Front-Find podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i domyślne to `matchPerson` .

* Dodano opcjonalny `confidenceThreshold` parametr dla użytkownika w celu ustawienia progu, czy jedna z nich należy do obiektu osoby w [identyfikacji czołowej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Dodano `start` Parametry opcjonalne i do `top` [listy osób](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) , aby umożliwić użytkownikowi określenie punktu początkowego i łącznej liczby grup osób wynosi do wyświetlenia.

### <a name="v10-changes-from-v0"></a>V 1.0 zmiany z v0
* Zaktualizowano główny punkt końcowy usługi z ```https://westus.api.cognitive.microsoft.com/face/v0/``` do ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` . Zmiany zastosowane do: [wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)z uwzględnieniem kroju, rozpoznawanie i [Określanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)kroju, tworzenie [podobnych](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i [grup czołowych](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Zaktualizowano minimalny wykrywalny rozmiar kroju do 36x36 pikseli. Powierzchnie mniejsze niż 36x36 pikseli nie będą wykrywane.

* Przestarzałe dane osobowe i osoby na platformie VO. Nie można uzyskać dostępu do tych danych za pomocą usługi Front V 1.0.

* Zaniechano punktu końcowego v0 interfejs API rozpoznawania twarzy 30 czerwca 2016.
