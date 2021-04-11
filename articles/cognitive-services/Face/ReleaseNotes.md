---
title: Co nowego w usłudze rozpoznawania twarzy?
titleSuffix: Azure Cognitive Services
description: Informacje o wersji usługi Front Service obejmują historię zmian wersji dla różnych wersji.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
ms.custom: contperf-fy21q3
ms.openlocfilehash: c580828d29e92ecef7ecc73b8f3e5843c3ecd23d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078884"
---
# <a name="whats-new-in-face-service"></a>Co nowego w usłudze rozpoznawania twarzy?

Usługa Azure Site Recovery jest regularnie aktualizowana. Skorzystaj z tego artykułu, aby uzyskać aktualne informacje dotyczące ulepszeń, poprawek i aktualizacji dokumentacji.

## <a name="february-2021"></a>Luty 2021 r.

### <a name="new-face-api-detection-model"></a>Nowy model wykrywania Face API
* Nowy model wykrywania 03 jest najdokładniejszym modelem wykrywania, który jest obecnie dostępny. Jeśli jesteś nowym klientem, zalecamy korzystanie z tego modelu. Wykrywanie 03 usprawnia zarówno przywoływanie, jak i precyzję w przypadku mniejszych twarzy znalezionych w obrębie obrazów (64x64 pikseli). Dodatkowe ulepszenia obejmują ogólną redukcję fałszywie dodatnich i ulepszone wykrywanie w przypadku obróconych orientacji. Łączenie wykrywania 03 z nowym modelem rozpoznawania. 04 zapewni również lepszą dokładność rozpoznawania. Aby uzyskać więcej informacji [, zobacz Określanie modelu wykrywania kroju](./face-api-how-to-topics/specify-detection-model.md) .
### <a name="new-detectable-face-attributes"></a>Nowe wykrywalne atrybuty kroju
* Ten `faceMask` atrybut jest dostępny z najnowszym modelem wykrywania 03 wraz z dodatkowym atrybutem, `"noseAndMouthCovered"` który wykrywa, czy maska czołowa jest znoszone jako zamierzone, obejmującą zarówno nos, jak i ustanie. Aby użyć najnowszej funkcji wykrywania masek, użytkownicy muszą określić model wykrywania w żądaniu interfejsu API: Przypisz wersję modelu z parametrem _detectionModel_ do `detection_03` . Aby uzyskać więcej informacji [, zobacz Określanie modelu wykrywania kroju](./face-api-how-to-topics/specify-detection-model.md) .
### <a name="new-face-api-recognition-model"></a>Nowy model rozpoznawania Face API
* Nowy model rozpoznawania 04 to najbardziej precyzyjny model rozpoznawania, który jest obecnie dostępny. Jeśli jesteś nowym klientem, zalecamy użycie tego modelu do weryfikacji i identyfikacji. Zwiększa się to z dokładnością rozpoznawania 03, łącznie z ulepszonym rozpoznawaniem zarejestrowanego użytkownika w zakresie okładek (masek chirurgicznych, masek N95, masek tkaniny). Teraz klienci mogą tworzyć bezpieczne i bezproblemowe środowiska użytkownika, które wykrywają, czy zarejestrowany użytkownik jest w stanie obsłużyć z najnowszym modelem wykrywania 03, i rozpoznaje, kto jest w modelu rozpoznawania w wersji 04. Aby uzyskać więcej informacji, zobacz temat [Określanie modelu rozpoznawania kroju](./face-api-how-to-topics/specify-recognition-model.md) .


## <a name="january-2021"></a>Styczeń 2021 r.
### <a name="mitigate-latency"></a>Zmniejszanie opóźnień
* Zespół programu do obsługi aplikacji opublikował nowy artykuł opisujący potencjalne przyczyny opóźnienia podczas korzystania z usługi i możliwych strategii zaradczych. Zobacz [ograniczanie opóźnień podczas korzystania z usługi kroju](./face-api-how-to-topics/how-to-mitigate-latency.md).

## <a name="december-2020"></a>Grudzień 2020 r.
### <a name="customer-configuration-for-face-id-storage"></a>Konfiguracja klienta dla magazynu identyfikatorów urządzeń
* Gdy usługa kroju nie przechowuje obrazów klientów, wyodrębnione funkcje są przechowywane na serwerze. Identyfikator kroju jest identyfikatorem funkcji frontu i zostanie użyty w celu [zidentyfikowania](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [sprawdzenia z kroju](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)i [założenia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). Funkcja przechowywanych funkcji jest wygaśnie i zostanie usunięta po upływie 24 godzin od oryginalnego wywołania wykrywania. Klienci mogą teraz określić, jak długo te identyfikatory są buforowane. Wartość maksymalna to nawet 24 godziny, ale teraz można ustawić minimalną wartość 60 sekund. Nowe zakresy czasu dla buforowanych identyfikatorów klas są dowolną wartością z zakresu od 60 sekund do 24 godzin. Więcej szczegółów można znaleźć w dokumentacji interfejsu API [wykrywania ze stroną](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (parametr *faceIdTimeToLive* ).

## <a name="november-2020"></a>Listopad 2020 r.
### <a name="sample-face-enrollment-app"></a>Przykładowa aplikacja do rejestracji
* Zespół opublikował przykładową aplikację do rejestracji w celu przedstawienia najlepszych rozwiązań dotyczących ustalania zrozumiałej zgody i tworzenia systemów rozpoznawania klasy o wysokiej dokładności za pomocą rejestracji wysokiej jakości. Przykład "open source" można znaleźć w przewodniku [tworzenia aplikacji rejestracyjnej](build-enrollment-app.md) i w serwisie [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample), gotowym do wdrożenia lub dostosowania przez deweloperów. 

## <a name="august-2020"></a>Sierpień 2020 r.
### <a name="customer-managed-encryption-of-data-at-rest"></a>Szyfrowanie danych w spoczynku przez klienta
* Usługa kroju automatycznie szyfruje dane podczas ich utrwalania w chmurze. Szyfrowanie usługi kroju chroni dane, aby pomóc spełnić wymagania dotyczące zabezpieczeń i zgodności w organizacji. Domyślnie subskrypcja używa kluczy szyfrowania zarządzanych przez firmę Microsoft. Dostępna jest również nowa opcja zarządzania subskrypcją przy użyciu własnych kluczy o nazwie klucze zarządzane przez klienta (CMK). Więcej szczegółów można znaleźć w [kluczach zarządzanych przez klienta](./encrypt-data-at-rest.md).

## <a name="april-2020"></a>Kwiecień 2020 r.
### <a name="new-face-api-recognition-model"></a>Nowy model rozpoznawania Face API
* Nowy model rozpoznawania 03 jest najdokładniejszym modelem, który jest obecnie dostępny. Jeśli jesteś nowym klientem, zalecamy korzystanie z tego modelu. Uznanie 03 zapewni lepszą dokładność porównania podobieństwa i porównywanie przez osoby. Więcej szczegółów można znaleźć w tematach [Określanie modelu rozpoznawania kroju](./face-api-how-to-topics/specify-recognition-model.md).

## <a name="june-2019"></a>Czerwiec 2019 r.

### <a name="new-face-api-detection-model"></a>Nowy model wykrywania Face API
* Nowy model wykrywania 02 oferuje ulepszoną dokładność małych, bocznych, zamknięte i rozmytych twarzy. Korzystaj z niego za pośrednictwem [wykrywania czołowego](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList-Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), persona- [Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) i [LargePersonGroup — Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) miarę, określając nazwę modelu wykrywania nowej czcionki `detection_02` w `detectionModel` parametrze. Więcej szczegółów w temacie [jak określić model wykrywania](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>Kwiecień 2019 r.

### <a name="improved-attribute-accuracy"></a>Ulepszona dokładność atrybutów
* Ulepszona ogólna dokładność `age` atrybutów i `headPose` . Ten `headPose` atrybut jest również aktualizowany z `pitch` wartością włączoną teraz. Użyj tych atrybutów, określając je w `returnFaceAttributes` parametrze [Wykryj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) wartość `returnFaceAttributes` parametru. 
### <a name="improved-processing-speeds"></a>Ulepszone szybkości przetwarzania
* Ulepszone szybkości [wykrywania czołowego](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList-Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), Person- [Add Front](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) i [LargePersonGroup — Dodawanie operacji ubocznych](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) .

## <a name="march-2019"></a>Marzec 2019 r.

### <a name="new-face-api-recognition-model"></a>Nowy model rozpoznawania Face API
* Model rozpoznawania 02 ma ulepszoną dokładność. Za jego pomocą można korzystać z [wykrywania czołowego](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [Personing-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) i [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) , określając nazwę modelu rozpoznawania nowej czcionki `recognition_02` w `recognitionModel` parametrze. Więcej szczegółów na [temat określania modelu rozpoznawania](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>Styczeń 2019 r.

### <a name="face-snapshot-feature"></a>Funkcja migawek czołowych
* Ta funkcja umożliwia obsługę migracji danych między subskrypcjami przez usługę: [migawka](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Więcej szczegółów na [temat migrowania danych do innej subskrypcji programu Marketo](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>Październik 2018 r.

### <a name="api-messages"></a>Komunikaty interfejsu API
* Elegancja opis dla,,, i w odniesieniu do `status` `createdDateTime` `lastActionDateTime` `lastSuccessfulTrainingDateTime` [osoby — Pobierz](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)stan szkolenia, [LargePersonGroup — Pobierz stan szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)i [LargeFaceList — Pobierz stan szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

## <a name="may-2018"></a>Maj 2018 r.

### <a name="improved-attribute-accuracy"></a>Ulepszona dokładność atrybutów
* Ulepszony `gender` atrybut znacząco i udoskonalony `age` ,,,, `glasses` `facialHair` `hair` `makeup` atrybutów. Użyj ich za pomocą parametru [wykrywania ze stroną](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .
### <a name="increased-file-size-limit"></a>Zwiększony limit rozmiaru pliku
* Zwiększony limit rozmiaru pliku obrazu wejściowego z 4 MB do 6 MB w [wykrywaniu czołowym](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [LargeFaceList — Dodaj powierzchnie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), osoba, osoba [-Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) i [LargePersonGroup osoba — Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)miarę.

## <a name="march-2018"></a>Marzec 2018 r.

### <a name="new-data-structure"></a>Nowa struktura danych
* [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) i [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Więcej szczegółów na [temat korzystania z funkcji dużej skali](Face-API-How-to-Topics/how-to-use-large-scale.md).
* Zwiększono możliwość [— Zidentyfikuj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` parametr z [1, 5] do [1, 100] i domyślnie 10.

## <a name="may-2017"></a>Maj 2017

### <a name="new-detectable-face-attributes"></a>Nowe wykrywalne atrybuty kroju
* Dodano `hair` atrybuty,,,,, `makeup` `accessory` `occlusion` `blur` `exposure` , i `noise` w parametrach [wykrywania ze stroną](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .
* Obsługiwane 10 000 osób w ramach danej osoby i [identyfikacji](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)na platformie.
* Obsługiwane stronicowanie w ramach [listy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) osób z parametrami opcjonalnymi: `start` i `top` .
* Obsługiwane współbieżność w dodawaniu i usuwaniu twarzy do różnych FaceLists i różnych osób w ramach osoby.

## <a name="march-2017"></a>March 2017

### <a name="new-detectable-face-attribute"></a>Nowy atrybut kroju możliwej do wykrycia
* Dodano `emotion` atrybut w parametrze [wykrywania ze stroną](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .
### <a name="fixed-issues"></a>Naprawione problemy
* Nie można ponownie wykryć powierzchni z prostokątem zwróconym przez [wykrywanie powierzchni](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) , tak jak `targetFace` w przypadku [FaceList — Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [osobę i osobę](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).
* Możliwy do wykrycia rozmiar jest ustawiony tak, aby zapewnić, że jest on ściśle od 36x36 do 4096x4096 pikseli.

## <a name="november-2016"></a>Listopad 2016
### <a name="new-subscription-tier"></a>Nowa warstwa subskrypcji
* Dodano magazyn twarzy subskrypcję standardową w celu przechowywania dodatkowych utrwalonych [twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [podczas korzystania z tej osoby.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Opłata za przechowywane obrazy jest naliczana przy użyciu stawki wynoszącej 0,5 USD za 1000 twarzy proporcjonalnie do liczby dni. Subskrypcje warstwy Bezpłatna są nadal ograniczone do 1 000 łącznej liczby osób.

## <a name="october-2016"></a>Październik 2016
### <a name="api-messages"></a>Komunikaty interfejsu API
* Zmieniono komunikat o błędzie z więcej niż jedną stroną w `targetFace` "from" z "zawiera więcej niż jedną miarę w obrazie" do "istnieje więcej niż jedna ilustracja w obrazie" w [FaceList — Dodaj](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [osobę](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)i osobę, która ma zostać dodana.

## <a name="july-2016"></a>Lipiec 2016
### <a name="new-features"></a>Nowe funkcje
* Obsługiwane jest uwierzytelnianie obiektów na potrzeby użytkowników w ramach [weryfikacji na miejscu](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
* Dodano opcjonalny `mode` parametr umożliwiający wybór dwóch trybów roboczych: `matchPerson` i `matchFace` w programie [Front-Find podobne](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i domyślne to `matchPerson` .
* Dodano opcjonalny `confidenceThreshold` parametr dla użytkownika w celu ustawienia progu, czy jedna z nich należy do obiektu osoby w [identyfikacji czołowej](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
* Dodano `start` Parametry opcjonalne i do `top` [listy osób](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) , aby umożliwić użytkownikowi określenie punktu początkowego i łącznej liczby grup osób wynosi do wyświetlenia.

## <a name="v10-changes-from-v0"></a>V 1.0 zmiany z v0

* Zaktualizowano główny punkt końcowy usługi z ```https://westus.api.cognitive.microsoft.com/face/v0/``` do ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` . Zmiany zastosowane do: [wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)z uwzględnieniem kroju, rozpoznawanie i [Określanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)kroju, tworzenie [podobnych](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) i [grup czołowych](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).
* Zaktualizowano minimalny wykrywalny rozmiar kroju do 36x36 pikseli. Powierzchnie mniejsze niż 36x36 pikseli nie będą wykrywane.
* Przestarzałe dane osobowe i osoby na platformie VO. Nie można uzyskać dostępu do tych danych za pomocą usługi Front V 1.0.
* Zaniechano punktu końcowego v0 Face API 30 czerwca 2016.