---
title: Aparat głębokości usługi Azure urządzenia Kinect DK
description: Zapoznaj się z zasadami działania i kluczowymi funkcjami aparatu głębokości w systemie Azure urządzenia Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: urządzenia Kinect, Azure, czujnik, zestaw SDK, aparat głębokości, TOF, zasady, wydajność, unieważnienie
ms.openlocfilehash: 22f04b983ed7c6a2ab19a5c1c709621655ee31c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277511"
---
# <a name="azure-kinect-dk-depth-camera"></a>Aparat głębokości usługi Azure urządzenia Kinect DK

Ta strona zawiera informacje na temat korzystania z aparatu głębokości na platformie Azure urządzenia Kinect DK. Aparat głębokości jest drugim z dwóch kamer. Jak opisano w poprzednich sekcjach, inny aparat jest kamerą RGB.  

## <a name="operating-principles"></a>Zasady operacyjne

Aparat głębokości urządzenia Kinect na platformie Azure zawiera implementację zasady czasu trwania (ToF) modulowanej ciągłej fazy amplitudy (AMCW). Aparat rzutuje modulowane oświetlenie na spektrum w sąsiedztwie podczerwieni (imię Nir) na scenę. Następnie rejestruje pośrednie pomiary czasu, przez który lampa jest przenoszona z aparatu do sceny i z powrotem.

Pomiary te są przetwarzane w celu wygenerowania mapy głębokości. Mapa głębokości jest zestawem wartości współrzędnych Z i dla każdego piksela obrazu, mierzoną w jednostkach milimetrów.

Podobnie jak w przypadku mapy głębokości, uzyskujemy również czysty odczyt w podczerwieni. Wartość pikseli w odczytaniu czystego środowiska IR jest proporcjonalna do liczby sygnalizatorów zwracanych z sceny. Obraz wygląda podobnie do zwykłego obrazu podczerwieni. Na poniższym rysunku przedstawiono przykład mapy głębokości (po lewej) i odpowiedni obraz czystego środowiska IR (po prawej).

![Głębokość i IR obok siebie](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>Najważniejsze funkcje

Cechy techniczne aparatu głębokości obejmują:

- wieloToFowy układ obrazu z technologią Advanced Pixel, który umożliwia wyższe częstotliwości modulacji i precyzję głębokości.
- Dwie diody laserowe imię Nir umożliwiają wyświetlanie trybów głębokości zbliżonego i szerokiego (FoV).
- Na świecie najmniejszy ToF piksel o 3,5 μm przez 3,5 μm.
- Automatyczne przechwycenie z dokładnością do pikseli w przypadku dużego zakresu dynamicznego umożliwiającego czyszczenie blisko i daleko obiektów.
- Globalna migawki, która pozwala zwiększyć wydajność w przypadku światła słonecznego.
- Wielofazowy Metoda obliczania głębokości, która umożliwia niezawodną dokładność nawet w przypadku obecności mikroukładu, laseru i zasilacza.
- Małe błędy systematyczne i losowe.

![Moduł głębokości](./media/concepts/depth-camera-depth-module.jpg)

Aparat głębokości przesyła nieoczyszczone modulowane obrazy IR do komputera hosta. Na komputerze procesor GPU przyspieszenie aparatu, który konwertuje nieprzetworzony sygnał na mapy głębokości.Aparat głębokości obsługuje kilka trybów. **Wąskie Tryby widoku (FOV)** są idealnym rozwiązaniem w przypadku scen z mniejszymi zakresami w wymiarach X i Y, ale większymi zakresami w wymiarze z. Jeśli scena ma duże wartości X i Y, ale mniejsze zakresy Z, są lepiej dopasowane **tryby FOV** .

Aparat głębokości obsługuje **tryby pakowania 2x2** , aby zwiększyć zakres z w porównaniu do odpowiednich **trybów unbinned**. Pakowania odbywa się przy obniżyć rozdzielczości obrazu. Wszystkie tryby można uruchamiać do 30 klatek na sekundę (FPS) z wyjątkiem trybu 1 megapikseli (MP), który jest uruchamiany z maksymalną szybkością klatek równą 15 fps. Aparat głębokości udostępnia również **tryb pasywnego IR**. W tym trybie, świecki w aparacie nie są aktywne i zaobserwowano tylko oświetlenie otoczenia.

## <a name="camera-performance"></a>Wydajność aparatu fotograficznego

Wydajność aparatu jest mierzona jako błędy systematyczne i losowe.

### <a name="systematic-error"></a>Błąd systematyczny

Błąd systematyczny jest definiowany jako różnica między zmierzoną głębokością po usunięciu szumu i poprawną głębokością (z rzeczywistością). Obliczamy średni czasowo dla wielu klatek statycznej sceny w celu wyeliminowania szumu głębokości tak jak to możliwe. Dokładniej, systematyczne błędy są zdefiniowane jako:

![Błąd systematyczny głębokości](./media/concepts/depth-camera-systematic-error.png)

Gdzie *d <sub>t</sub>* wskazuje głębokość miary w czasie *t*, *N* to liczba klatek używanych w przypadku średniej procedury, a wartość *d <sub>gt</sub>* to podstawa prawdy.

Systematyczna Specyfikacja błędów aparatu głębokości jest wykluczana z pominięciem wielościeżkowego zakłócenia (MPI). MPI jest, gdy jeden piksel czujnika integruje światło, które jest odzwierciedlone przez więcej niż jeden obiekt. MPI jest częściowo zminimalizowany w naszym aparacie głębokości przy użyciu wyższych częstotliwości modulacji wraz z nieprawidłową głębokością, którą wprowadzimy w przyszłości.

### <a name="random-error"></a>Błąd losowy

Załóżmy, że zajmiemy 100 obrazów tego samego obiektu bez przechodzenia do aparatu. Głębokość obiektu będzie nieco inna w przypadku obrazów 100. Różnica jest spowodowana przez zakłócenia zrzutu. Hałasem zrzutu jest liczba photons, która powoduje, że czujnik zmienia się w miarę upływu czasu. Definiujemy ten losowy błąd na sceny statycznej jako odchylenie standardowe głębokości w czasie obliczanym jako:

![Losowy błąd głębokości](./media/concepts/depth-camera-random-error.png)

Gdzie *N* wskazuje liczbę pomiarów głębokości, *d <sub>t</sub>* reprezentuje pomiar głębokości w czasie *t* i *d* oznacza wartość średniej obliczoną dla wszystkich pomiarów głębokości *d <sub>t</sub>*.

## <a name="invalidation"></a>Unieważniania

W niektórych sytuacjach aparat głębokości może nie zapewniać prawidłowych wartości dla niektórych pikseli. W tych sytuacjach głębokość pikseli jest unieważniona. Nieprawidłowe piksele są wskazywane przez wartość głębokości równą 0. Powody aparatu głębokości nie mogą generować prawidłowych wartości:

- Poza aktywną maską oświetlenia IR
- Nasycenie sygnału IR
- Niski sygnał IR
- Odstający filtr
- Zakłócenia wielu ścieżek

### <a name="illumination-mask"></a>Maska oświetlenia

Piksele są unieważnione, gdy znajdują się poza aktywną maską oświetlenia IR. Nie zalecamy użycia sygnału takich pikseli do głębokości obliczeniowej. Na poniższym rysunku przedstawiono przykład unieważnienia według maski oświetlenia. Niezweryfikowane piksele to czarne kolory poza okręgiem w trybie szerokiej FoV (po lewej), a sześciokąt w FoVych trybach wąskich (prawy).

![Unieważnienie poza maską oświetlenia](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>Siła sygnału

Piksele są unieważnione, gdy zawierają nasycone sygnały IR. Po nasyceniu pikseli informacje o fazie są tracone. Na poniższym obrazie przedstawiono przykład unieważnienia przez nasycenie sygnału IR. Zobacz strzałki wskazujące na przykładowe piksele w obrazie głębokości i w podczerwieni.

![Nasycenie unieważnienia](./media/concepts/depth-camera-invalidation-saturation.png)

Unieważnienie może również wystąpić, gdy sygnał IR nie jest wystarczająco silny, aby wygenerować głębokość. Poniższy rysunek pokazuje przykład unieważnienia przez niski sygnał IR. Zobacz strzałki wskazujące na przykładowe piksele w obrazie głębi i w IR.

![Niski sygnał unieważnienia](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>Niejednoznaczna Głębokość

Piksele mogą być również unieważnione, jeśli otrzymają sygnały z więcej niż jednego obiektu w scenie. Typowy przypadek, w którym takie sortowanie może być widoczne, znajduje się w rogach.  Ze względu na geometrię sceny, sygnalizatora IR z aparatu został przesunięty do jednej ściany i na drugą. To odbicie światła powoduje niejednoznaczność w zmierzonej głębokości pikseli. Filtry w algorytmie głębokości wykrywają te niejednoznaczne sygnały i unieważnią piksele.

Poniższe figury pokazują przykłady unieważnienia przez wykrywanie wielu ścieżek. Można też zobaczyć, jak obszar powierzchni, który został unieważniony z jednego widoku aparatu (górny wiersz) może pojawić się ponownie w innym widoku aparatu (dolny wiersz). Ten obraz pokazuje, że powierzchnie unieważnione z jednej perspektywy mogą być widoczne z innego.

![Unieważnienie wielościeżkowe — róg](./media/concepts/depth-camera-invalidation-multipath.png)

Inny typowy przypadek wielościeżkowy to piksele, które zawierają sygnał mieszany z pierwszego planu i tła (na przykład wokół krawędzi obiektu). Podczas szybkiego ruchu może pojawić się więcej niesprawdzonych pikseli wokół krawędzi. Dodatkowe niezweryfikowane piksele wynikają z interwału ekspozycji z nieprzetworzonej głębokości przechwytywania.

![Unieważnienie krawędzi wielościeżkowej](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>Następne kroki

[Układy współrzędnych](coordinate-systems.md)
