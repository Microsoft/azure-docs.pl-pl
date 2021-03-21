---
title: Często zadawane pytania
description: Często zadawane pytania dotyczące usługi zakotwiczenia obiektów platformy Azure.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 04/01/2020
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 6de027f317072512bd9893303d3a79d0f34f3e3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101749083"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Często zadawane pytania dotyczące kotwic obiektów platformy Azure

Kotwice obiektów platformy Azure umożliwiają aplikacji wykrywanie obiektu w świecie fizycznym przy użyciu modelu 3D i oszacowanie jego ułożenia 6-DoF.

Aby uzyskać więcej informacji, zobacz [kotwice obiektów platformy Azure — omówienie](overview.md).

## <a name="product-faq"></a>Często zadawane pytania dotyczące produktu
**P: jakie zalecenia są dostępne dla obiektów, które powinny być używane?**

Odp **.:** Zalecamy stosowanie następujących właściwości obiektów:

* 1-10 metrów dla każdego wymiaru
* Niesymetryczne, z wystarczającą zmiennością geometrii
* Niskie odbicie (powierzchnie matowy) z jasnym kolorem
* Obiekty stacjonarne
* Nie lub małe ilości przegubu
* Wyczyść tło z niewielkim lub minimalnym bałaganem
* Skanowany obiekt powinien mieć 1:1 Dopasuj do modelu, który został przeszkolony

**P: Jakie są maksymalne wymiary obiektów, które można przetworzyć w celu pozyskiwania modelu?**

Odp **.:** Każdy wymiar modelu CAD powinien być mniejszy niż 10 metrów.

**P: co to jest maksymalny rozmiar modelu CAD, który można przetworzyć w celu pozyskiwania?**

Odp **.:** Rozmiar pliku modelu powinien być mniejszy niż 150 MB.

**P: Jakie są obsługiwane formaty programu CAD?**

Odp **.:** Obecnie obsługujemy `fbx` `ply` typy plików,,, `obj` `glb` i `gltf` .

**P: co to jest kierunek ciężkości i jednostka wymagana przez usługę pozyskiwania modelu? Jak możemy ją poznać?**

Odp **.:** Kierunek ciężkości jest wektorem w dół wskazującym na ziemię. W przypadku modeli CAD kierunek ciężkości jest zwykle przeciwieństwem kierunku do góry. Na przykład w wielu przypadkach + Z reprezentuje w tym przypadku, w którym przypadek-Z lub `Vector3(0.0, 0.0, -1.0)` przedstawia kierunek ciężkości. Podczas określania wagi należy nie tylko rozważyć model, ale również orientację, w której model będzie widoczny w czasie wykonywania. Jeśli próbujesz wykryć krzesło w świecie rzeczywistym na płaskiej powierzchni, grawitacja może być `Vector3(0.0, 0.0, -1.0)` . Jeśli jednak krzesło jest na 45 stopni, grawitacja może być `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)` .

Kierunek ciężkości można uzasadnić za pomocą narzędzia renderowania 3W, takiego jak [MeshLab](http://www.meshlab.net/).

Jednostka reprezentuje jednostkę miary modelu. Obsługiwane jednostki można znaleźć za pomocą wyliczenia **Microsoft. Azure. ObjectAnchors.** pozyskiwania. jednostki.

**P: jak długo trwa pozyskiwanie modelu CAD?**

Odp **.:** W przypadku `ply` modelu zwykle 3-15 min. W przypadku przesyłania modeli w innych formatach należy oczekiwać 15-60 minut w zależności od rozmiaru pliku.

**P: jakie urządzenia obsługują kotwice obiektów?**

Odp **.:** Urządzenie HoloLens 2 

**P: którą kompilację systemu operacyjnego powinien uruchomić moje urządzenie HoloLens?**

Odp **.:** Kompilacja systemu operacyjnego 18363,720 lub nowsza, wydana po 12 marca 2020.

  Więcej szczegółów w [systemie Windows 10 marca 2020 Update](https://support.microsoft.com/help/4551762).

**P: jak długo trwa wykrywanie obiektu w usłudze HoloLens?**

Odp **.:** Jest to zależne od rozmiaru obiektu i procesu skanowania. Aby szybciej wykryć, wypróbuj najlepsze rozwiązania w zakresie skanowania szczegółowego. W przypadku mniejszych obiektów w obrębie 2 metrów w każdym wymiarze wykrywanie może wystąpić w ciągu kilku sekund. W przypadku większych obiektów, takich jak samochód, użytkownik powinien przeprowadzić pełną pętlę wokół obiektu, aby uzyskać niezawodne wykrywanie, co oznacza, że wykrywanie może trwać dziesiątki sekund.

**P: Jakie są najlepsze rozwiązania w przypadku używania kotwic obiektów w aplikacji HoloLens?**

**Z**

 1. Wykonaj kalibrację oczu, aby uzyskać dokładne renderowanie.
 2. Upewnij się, że pomieszczenie ma rozbudowaną teksturę wizualną i dobre oświetlenie.
 3. Utrzymywanie obiektów w nieruchomy sposób, jeśli jest to możliwe.
 4. Opcjonalnie Wyczyść pamięć podręczną [mapowania przestrzennego](https://docs.microsoft.com/windows/mixed-reality/spatial-mapping) na urządzeniu HoloLens.
 5. Przeskanuj obiekt, przeciągając go. Upewnij się, że większość obiektów jest zaobserwowana.
 6. Ustaw wystarczająco duży obszar wyszukiwania, aby pokryć obiekt.
 7. Obiekt powinien pozostać nieruchomy podczas wykrywania.
 8. Rozpocznij wykrywanie obiektów i wizualizowanie renderowania na podstawie szacowanego ułożenia.
 9. Zablokuj wykryty obiekt lub Zatrzymaj śledzenie, gdy ułożenie jest stabilne i dokładne, aby zachować żywotność baterii.

**P: jak dokładne jest szacowane?**

Odp **.:** Zależy to od rozmiaru obiektu, materiału, środowiska itp. W przypadku małych obiektów Szacowana wartość może być w przypadku błędu 2 cm. W przypadku dużych obiektów, takich jak samochód, błąd może należeć do 2-8 cm.

**P: czy kotwice obiektów obsługują przesuwanie obiektów?**

Odp **.:** Nie obsługujemy **ciągłego przemieszczania** ani obiektów **dynamicznych** .

**P: czy kotwice obiektów mogą dokonywać odformatowania lub przegubów?**

Odp **.:** Częściowo, w zależności od tego, jak dużo zmian kształtu obiektu lub geometrii jest spowodowanych deformacji lub przegubem. W przypadku zmiany geometrii obiektu użytkownik może utworzyć inny model dla tej konfiguracji i używać go do wykrywania.

**P: jak wiele różnych obiektów może wykryć kotwice obiektów w tym samym czasie?**

Odp **.:** Obecnie obsługujemy wykrywanie pojedynczego modelu obiektów w danym momencie. 

**P: czy kotwice obiektów wykrywają wiele wystąpień tego samego modelu obiektów?**

Odp **.:** Tak, można wykryć do 3 obiektów tego samego typu modelu. Aplikacja może wywoływać `ObjectObserver.DetectAsync` wiele razy przy użyciu różnych zapytań w celu wykrywania wielu wystąpień tego samego modelu.

**P: co należy zrobić, jeśli środowisko uruchomieniowe obiektów nie może wykryć mojego obiektu?**

**Z**

* Upewnij się, że pomieszczenie ma wystarczającą liczbę tekstur, dodając kilka afiszów.
* Przeskanuj obiekt w całości.
* Dostosuj parametry modelu zgodnie z powyższym opisem.
* Podaj przyległe pole ograniczenia jako obszar wyszukiwania, który zawiera wszystkie lub większość obiektów.
* Wyczyść pamięć podręczną mapowania przestrzennego i ponownie Przeskanuj obiekt.
* Przechwyć diagnostykę i Wyślij do nas dane.

**P: jak wybrać parametry zapytania obiektu?**

**Z**

* Zapewnij ścisłe obszary wyszukiwania, aby najlepiej zakryć pełen obiekt, aby zwiększyć szybkość i dokładność wykrywania.
* Wartość domyślna `ObjectQuery.MinSurfaceCoverage` z modelu obiektów jest zwykle dobra. w przeciwnym razie Użyj mniejszej wartości, aby szybko wykryć.
* Użyj niewielkiej wartości, `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` Jeśli obiekt powinien być w pełni prawidłowy.
* Aplikacja powinna zawsze używać `1:1` modelu obiektów do wykrywania. Szacowana Skala powinna być bliska 1% błędu. Aplikacja może być ustawiona `ObjectQuery.MaxScaleChange` na `0` lub `0.1` w celu wyłączenia lub włączenia szacowania skalowania, a także jakościowe oszacowanie ułożenia wystąpienia.

**P: Jak mogę uzyskać danych diagnostycznych kotwice obiektów z urządzenia HoloLens?**

Odp **.:** Aplikacja może określić lokalizację archiwów diagnostyki. Obiekt zakotwiczenia przykładowej aplikacji zapisuje diagnostykę do folderu **TempState** .

**P: Dlaczego Model źródłowy nie jest wyrównany z obiektem fizycznym w przypadku użycia ułożenia zwracanego przez zestaw zakotwiczenia obiektów zestawu SDK aparatu Unity?**

Odp **.:** Środowisko Unity może zmienić układ współrzędnych podczas importowania modelu obiektów. Na przykład zakotwiczenie obiektu zestaw SDK usługi Unity powoduje odwrócenie osi Z po konwersji z prawego do lewego układu współrzędnych, ale Unity może zastosować dodatkowe rotację na osi X lub Y. Deweloper może określić ten dodatkowy obrót, wizualizowając i porównując systemy współrzędnych.

**P: czy obsługujesz 2D?**

Odp **.:** Ponieważ są one oparte na geometrii, obsługujemy tylko 3W.

**P: Czy można odróżnić ten sam model w różnych kolorach?**

Odp **.:** Ponieważ nasze algorytmy są oparte na geometrii, różne kolory tego samego modelu nie będą zachowywać się inaczej podczas wykrywania.

**P: Czy można używać kotwic obiektów bez połączenia z Internetem?**

**Z** 
* W celu pozyskiwania i uczenia się łączności wymagane jest połączenie w chmurze.
* Sesje środowiska uruchomieniowego są w pełni włączone i nie wymagają łączności, ponieważ wszystkie obliczenia występują na urządzeniu HoloLens 2.

## <a name="privacy-faq"></a>Często zadawane pytania dotyczące ochrony prywatności
**P: jak usługa Azure Object zakotwiczenie danych przechowuje dane?**

Odp **.:** Przechowywane są tylko metadane systemu, które są szyfrowane przy użyciu klucza szyfrowania danych zarządzanych przez firmę Microsoft.
