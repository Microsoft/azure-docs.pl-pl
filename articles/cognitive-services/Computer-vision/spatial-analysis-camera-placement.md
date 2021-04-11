---
title: Rozmieszczenie aparatu analizy przestrzennej
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak skonfigurować kamerę do użycia z analizą przestrzenną
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: d58e4303078733eb1014171271d27907ff7a95b0
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286270"
---
# <a name="camera-placement-guide"></a>Przewodnik umieszczania w aparacie

Ten artykuł zawiera zalecenia dotyczące umieszczania w aparacie dla analizy przestrzennej (publiczna wersja zapoznawcza). Zawiera ogólne wytyczne, a także szczegółowe zalecenia dotyczące wysokości, kąta i kamery między punktami dla wszystkich uwzględnionych operacji. 

> [!NOTE]
> Ten przewodnik jest przeznaczony dla aparatu M3045-V. Ta kamera będzie używać rozwiązania 1920 x 1080, 106 stopni poziomego widoku, 59 zakresu w pionie i o stałej długości 2.8 mm. Poniższe zasady zostaną zastosowane do wszystkich kamer, ale szczególne wskazówki dotyczące wysokości kamery oraz odległości między kamerą a punktem ogniskowym będą musiały zostać dostosowane do użytku z innymi aparatami. 

## <a name="general-guidelines"></a>Ogólne wskazówki

Podczas pozycjonowania kamer na potrzeby analizy przestrzennej należy wziąć pod uwagę następujące ogólne wytyczne:

* **Wysokość oświetlenia.** Umieść kamery poniżej osprzętu oświetlenia, aby nie blokować kamer.
* **Przeszkody.** Aby uniknąć przesłonięcia widoków aparatu, zanotuj przeszkody, takie jak Poles, Signer, odłożenia, ściany i istniejące kamery LP.
* **Niejasne środowisko.** Oświetlenie zewnętrzne ma wpływ na jakość obrazu aparatu. Aby uniknąć poważnych warunków niejasnych, należy unikać kierowania kamer w oknach zewnętrznych i szklanych.
* **Lokalne zasady i regulacje dotyczące prywatności.** Regulacje lokalne mogą ograniczać możliwości przechwytywania kamer. Przed umieszczeniem kamer upewnij się, że rozumiesz lokalne zasady i przepisy.
* **Budowanie struktury.** Instalacje GRZEWCZe, zraszacze i istniejące okablowanie mogą ograniczać twarde Instalowanie kamer.
* **Zarządzanie kablem.** Upewnij się, że można skierować kabel Ethernet z lokalizacji zaplanowanych do zainstalowania aparatu fotograficznego do przełącznika "Włącz ruch przez Internet" (PoE).

## <a name="height-focal-point-distance-and-angle"></a>Wysokość, odległość i kąt punktu odniesienia

Należy wziąć pod uwagę trzy rzeczy podczas decydowania o sposobie instalowania aparatu na potrzeby analizy przestrzennej:
- Wysokość kamery
- Odległość między kamerą a punktem ogniskowym
- Kąt kamery względem płaszczyzny podłogi

Ważne jest również, aby wiedzieć, że większość osób przegląda (kierunek przechodzenia przez osobę) w odniesieniu do pola aparatu widoku, jeśli jest to możliwe. Ten kierunek jest istotny dla wydajności systemu.

![Obraz osoby idącej w kierunku](./media/spatial-analysis/person-walking-direction.png)

Na poniższej ilustracji przedstawiono widok podniesienia uprawnień dla osób idących kierunek.

![Widok podniesienia uprawnień i planu](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>Wysokość kamery

Ogólnie rzecz biorąc, aparaty fotograficzne należy zainstalować 12-14 metrów od podstaw. Aby wykrywać maskę powierzchni, zalecamy zainstalowanie kamer o 8-12 metrów od podstaw. Planując Instalowanie aparatu w tym zakresie, weź pod uwagę przeszkody (na przykład odłożenie, światła wiszące, podpisywanie i wyświetlenia), które mogą mieć wpływ na widok aparatu, a następnie dostosuj wysokość w razie potrzeby.

## <a name="camera-to-focal-point-distance"></a>Odległość między kamerą a punktem ogniskowym

_Odległość między kamerą a punktem ogniskowym_ to odległość liniowa od punktu odniesienia (lub środka obrazu aparatu fotograficznego) do aparatu mierzonego na terenie.

![Odległość między kamerą a ogniskiem](./media/spatial-analysis/camera-focal-point.png)

Ta odległość jest mierzona na płaszczyźnie podłogi.

![Jak jest mierzone odległość między kamerą a ogniskiem](./media/spatial-analysis/camera-focal-point-floor-plane.png)

Z powyższych, wygląda to następująco:

![Jak należy mierzyć odległość między oddziałami między punktami](./media/spatial-analysis/camera-focal-point-above.png)

Skorzystaj z poniższej tabeli, aby określić odległość aparatu od punktu odniesienia na podstawie określonych wysokości instalacji. Te odległości umożliwiają optymalne umieszczanie. Należy zauważyć, że tabela zawiera wskazówki poniżej 12 "-14" zalecenia, ponieważ niektóre ograniczenia mogą ograniczać wysokość. W przypadku wykrywania masek czołowych zalecana odległość między aparatem a punktem odniesienia (min/max) to 4 "-10" dla wysokości kamery od 8 do 12.

| Wysokość kamery | Odległość między kamerą a punktem ogniskowym (min/max) |  
| ------------- | ---------------------------------------- |  
| 0,8            | 4.6 '-8 '                                  |  
| dziesięć           | 5.8 '-10 '                                 |  
| dwunastomiesięcznych           | 7 '-12 '                                   |  
| 14,5           | 8 "-14" "                                  |  
| 16           | 9.2 '-16 '                                 |  
| 20C           | 11,5 '-20 '                                |  

Poniższa ilustracja symuluje widoki aparatu z najbliższych i najbardziej odległych odległości między aparatami.

| Im                                      | Najdalej                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![Najbliższa odległość między aparatami](./media/spatial-analysis/focal-point-closest.png) | ![Najdalej odległość między kamerą a punktem](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>Zakresy montażowe kątów aparatu

W tej sekcji opisano akceptowalne zakresy instalowania kąta aparatu. Te zakresy montowania pokazują akceptowalny zakres optymalnego umieszczania.

### <a name="line-configuration"></a>Konfiguracja linii

Dla operacji **cognitiveservices. Vision. spatialanalysis-personcrossingline** , program +/-5 ° jest optymalnym kątem montażu aparatu do zmaksymalizowania dokładności.

Aby można było wykryć maskę czołową, program +/-30 stopni jest optymalnym kątem montażu aparatu w zakresie od 8 do 12.

Poniższa ilustracja symuluje widoki aparatu przy użyciu funkcji **cognitiveservices. Vision. spatialanalysis-personcrossingline** , aby można było zliczać wejścia w prawo.

| Widok z lewej strony                | Prawy Widok                |  
| ---------------------------- | ----------------------------- |  
| ![Kąt kamery z lewej strony](./media/spatial-analysis/camera-angle-left.png) | ![Kąt kamery do prawej](./media/spatial-analysis/camera-angle-right.png) |  

Na poniższej ilustracji przedstawiono sposób umieszczania i kątów montowania w aparacie z widoku oczu.

![Widok z lotu ptaka](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>Konfiguracja strefy

Zalecamy umieszczenie kamer na 10 metrów lub więcej, aby zagwarantować, że obszar objęty zakresem jest wystarczająco duży. 

Gdy strefa jest obok przeszkody, takiej jak ściana lub półka, aparaty instalacji znajdują się w określonej odległości od obiektu docelowego w akceptowalnym zakresie 120-stopniowym, jak pokazano na poniższej ilustracji.

![Akceptowalny kąt kamery](./media/spatial-analysis/camera-angle-acceptable.png)

Poniższa ilustracja zawiera symulacje dla lewego i prawego widoku kamery obszaru obok półki.

| Lewy widok        | Prawy Widok        |  
| ---------------- | ----------------- |  
| ![Lewy widok](./media/spatial-analysis/end-cap-left.png) | ![Prawy Widok](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>Kolejki

Do monitorowania kolejek można używać umiejętności **cognitiveservices. Vision. spatialanalysis-personcount**, **cognitiveservices. Vision. spatialanalysis-persondistance** i **cognitiveservices. Vision. spatialanalysis-personcrossingpolygon** . W celu uzyskania optymalnej jakości danych w kolejce w celu zminimalizowania zamknięcia osób w kolejce i upewnienia się, że lokalizacja kolejki jest spójna z upływem czasu.

![Wycofywanie kolejki pasów](./media/spatial-analysis/retractable-belt-queue.png)

Ten typ bariery jest preferowany przez nieprzezroczyste bariery dla tworzenia kolejki, aby zmaksymalizować dokładność szczegółowych informacji z systemu.

Istnieją dwa typy kolejek: liniowe i Zygzakowe.

Na poniższej ilustracji przedstawiono zalecenia dotyczące kolejek liniowych:

![Zalecenie dotyczące kolejki liniowej](./media/spatial-analysis/camera-angle-linear-queue.png)

Poniższa ilustracja zawiera symulacje dla lewego i prawego widoku dla kolejek liniowych. Należy pamiętać, że aparat można zainstalować po przeciwległej stronie kolejki.

| Lewy widok                          | Prawy Widok                          |  
| ---------------------------------- | ----------------------------------- |  
| ![Lewy kąt kolejki liniowej](./media/spatial-analysis/camera-angle-linear-left.png) | ![Prawy kąt kolejki liniowej](./media/spatial-analysis/camera-angle-linear-right.png) |  

W przypadku kolejek zygzaków najlepiej unikać umieszczania kamery bezpośrednio w kierunku linii kolejki, jak pokazano na poniższej ilustracji. Należy zauważyć, że każdy z czterech przykładowych pozycji kamery na ilustracji zapewnia idealny widok z akceptowalnym odchyleniem +/-15 stopni w każdym kierunku.

Poniższe ilustracje symulują widok z kamery umieszczonej w idealnej lokalizacji dla kolejki z zygzakami.

| Widok 1        | Widok 2        |  
| ------------- | ------------- |  
| ![Widok 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![Widok 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| Widok 3 |  Widok 4 |  
| ---- | ----  |
| ![Widok 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![Widok 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>Kolejki organiczne

Organiczne wiersze kolejki są organicznie. Ten styl kolejki jest akceptowalny, jeśli kolejki nie wykraczają poza 2-3 osób i formularze liniowe w ramach definicji strefy. Jeśli długość kolejki jest zwykle większa niż 2-3 osób, zalecamy użycie wycofanej bariery, aby pomóc w przekierowaniu kierunku kolejki i upewnić się, że formularze wierszy w definicji strefy.

## <a name="next-steps"></a>Następne kroki

* [Wdróż aplikację sieci Web zliczania osób](spatial-analysis-web-app.md)
* [Konfigurowanie operacji analizy przestrzennej](./spatial-analysis-operations.md)
* [Rejestrowanie i rozwiązywanie problemów](spatial-analysis-logging.md)
* [Przewodnik umieszczania strefy i linii](spatial-analysis-zone-line-placement.md)
