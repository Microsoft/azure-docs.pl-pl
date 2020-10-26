---
title: Pojęcia dotyczące wykrywania i atrybutów
titleSuffix: Azure Cognitive Services
description: Wykrywanie twarzy jest akcją lokalizowania ludzkich twarzy w obrazie i opcjonalnie zwracająca różne rodzaje danych związanych z twarzą.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 92b19941f34b9bf5656c9beb188a68d2cf01f674
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92504133"
---
# <a name="face-detection-and-attributes"></a>Wykrywanie i atrybuty

W tym artykule wyjaśniono koncepcje wykrywania i danych atrybutów. Wykrywanie twarzy jest akcją lokalizowania ludzkich twarzy w obrazie i opcjonalnie zwracająca różne rodzaje danych związanych z twarzą.

Za pomocą operacji [wykrywania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) można wykrywać twarze na obrazie. Co najmniej Każda wykryta wartość odpowiada polu faceRectangle w odpowiedzi. Ten zestaw współrzędnych pikseli dla lewej, górnej, szerokości i wysokości oznacza umieszczoną miarę. Korzystając z tych współrzędnych, można uzyskać lokalizację i jej rozmiar. W odpowiedzi interfejsu API powierzchnie są wyświetlane w kolejności rozmiaru od największych do najmniejszych.

## <a name="face-id"></a>Face ID

Identyfikator kroju jest unikatowym ciągiem identyfikatora dla każdej wykrytej kroju w obrazie. Możesz zażądać identyfikatora czołowego w wywołaniu funkcji API [Detection](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

## <a name="face-landmarks"></a>Punkty charakterystyczne twarzy

Punkty orientacyjne są zestawem łatwych do znalezienia punktów na stronie, takich jak uczniowie lub pozostała część nosa. Domyślnie istnieje 27 wstępnie zdefiniowanych punktów charakterystycznych. Na poniższej ilustracji przedstawiono wszystkie 27 punktów:

![Diagram przedstawiający wszystkie 27 punktów orientacyjnych z etykietą](../Images/landmarks.1.jpg)

Współrzędne punktów są zwracane w jednostkach pikseli.

## <a name="attributes"></a>Atrybuty

Atrybuty to zestaw funkcji, które mogą być opcjonalnie wykryte przez interfejs API [wykrywania kroju](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) . Następujące atrybuty mogą zostać wykryte:

* **Wiek** . Szacowany wiek w latach o określonej wartości.
* **Rozmycie** . Blurriness na obrazie. Ten atrybut zwraca wartość z przedziału od zera do jednej i nieformalnej oceny niskiej, średniej lub wysokiej.
* **Rozpoznawania emocji** . Lista emocji z ich pewnością wykrywania dla danej czołowej. Wyniki pewności są znormalizowane, a wyniki we wszystkich emocjiach są dodawane do jednego. Emocji zwracane są szczęście, smutek, neutralne, gniew, w tempie, obrzydzenie, niespodziewane i obaw.
* **Ekspozycja** . Ekspozycja kroju obrazu. Ten atrybut zwraca wartość z przedziału od zera do jednej i nieformalnej oceny nienadzorowanego narażenia, goodExposure lub prześwietlania.
* **Sierść twarzy** . Szacowana obecność twarzy i długość dla danej twarzy.
* **Płeć** . Szacowana płeć danej czołowej. Możliwe wartości to samce, sami i bezpłciowe.
* **Okulary** . Określa, czy dana wartość jest okularów. Możliwe wartości to noglasss, ReadingGlasses, okulary słoneczne i szklarnie.
* **Włosy** . Typ włosia kroju. Ten atrybut wskazuje, czy włosy jest widoczny, czy baldness jest wykryty i jakie są kolory w postaci włosów.
* Pozycja **główna** . Orientacja powierzchni w obszarze 3W. Ten atrybut jest opisywany przez kąty przechyłu, rzutowania i Yaw (w stopniach). Zakresy wartości to-90 stopni do 90 stopni,-90 stopni do 90 stopni i-90 stopnie odpowiednio do stopów. Na poniższym diagramie zapoznaj się z mapowaniami kątów:

    ![Nagłówek z osiami o szerokości, rzutowania i Yaw z etykietą](../Images/headpose.1.jpg)
* **Korzeń** . Czy jest to korzeń. Ten atrybut zwraca wartość logiczną dla eyeMakeup i lipMakeup.
* **Szum** . Szum wizualny wykryty w obrazie kroju. Ten atrybut zwraca wartość z przedziału od zera do jednej i nieformalnej oceny niskiej, średniej lub wysokiej.
* **Zamknięcia** . Czy istnieją obiekty, które blokują elementy. Ten atrybut zwraca wartość logiczną dla eyeOccluded, foreheadOccluded i mouthOccluded.
* **Uśmiech** . Wyrażenie uśmiechu danej wartości. Ta wartość jest z zakresu od 0 do nr uśmiechu i jeden dla jasnego uśmiechu.

> [!IMPORTANT]
> Atrybuty kroju są przewidywane przy użyciu algorytmów statystycznych. Mogą one być niedokładne. Należy zachować ostrożność podczas podejmowania decyzji na podstawie danych atrybutów.

## <a name="input-data"></a>Dane wejściowe

Skorzystaj z poniższych wskazówek, aby upewnić się, że obrazy wejściowe zawierają najbardziej precyzyjne wyniki wykrywania:

* Obsługiwane formaty obrazu wejściowego to JPEG, PNG, GIF dla pierwszej ramki i BMP.
* Rozmiar pliku obrazu nie może przekraczać 6 MB.
* Wykrywalny zakres rozmiaru jest 36 x 36 do 4096 x 4096 pikseli. Nie wykryto twarzy poza tym zakresem.
* Niektóre twarzy mogą nie zostać wykryte ze względu na wyzwania techniczne. Ekstremalny kąt działania (ułożenia głowy) lub zamknięcia czołowy (obiekty takie jak okulary słoneczne lub ręce, które blokują część czołową) mogą mieć wpływ na wykrywanie. Czołowe i bliskie twarze zapewniają najlepsze wyniki.

W przypadku wykrycia twarzy ze źródła wideo może być możliwe zwiększenie wydajności przez dostosowanie niektórych ustawień w aparacie wideo:

* **Wygładzanie** : wiele kamer wideo stosuje efekt wygładzania. Należy ją wyłączyć, jeśli jest to możliwe, ponieważ tworzy rozmycie między ramkami i zmniejsza przejrzystość.
* **Szybkość migawki** : szybsza szybkość migawki zmniejsza liczbę ruchów między ramkami i czyści każdą ramkę. Zalecamy szybkość migawki 1/60 sekund.
* **Kąt migawki** : Niektóre kamery określają kąt migawki zamiast szybkości migawki. Jeśli to możliwe, należy użyć mniejszego kąta migawki. Spowoduje to wyraźniejsze ramki wideo.

    >[!NOTE]
    > Aparat o niższym kącie ściętym będzie mniej jasny w każdej klatce, dzięki czemu obraz będzie ciemniejszy. Należy określić odpowiedni poziom użycia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już koncepcje wykrywania twarzy, Dowiedz się, jak napisać skrypt wykrywający twarze na danym obrazie.

* [Wykrywanie twarzy na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)