---
title: Analizowanie danych urządzenia w aplikacji IoT Central platformy Azure | Microsoft Docs
description: Analizowanie danych urządzenia w aplikacji IoT Central platformy Azure.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f329b6377915a3df6e3f3e212cbd8d41f9ed80d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90017664"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Jak używać analiz do analizowania danych urządzenia

*Ten artykuł dotyczy operatorów, konstruktorów i administratorów.*

Usługa Azure IoT Central zapewnia rozbudowane funkcje analityczne służące do analizowania trendów historycznych i skorelowania różnych telemetrii z urządzeń. Aby rozpocząć, odwiedź stronę **Analiza** w okienku po lewej stronie.

## <a name="understanding-the-analytics-ui"></a>Informacje o interfejsie użytkownika analizy
Interfejs użytkownika analizy składa się z trzech głównych składników:
- **Panel konfiguracja danych:** Na panelu konfiguracja Zacznij od wybrania grupy urządzeń, dla której chcesz analizować dane. Następnie wybierz dane telemetryczne, które chcesz analizować, i wybierz metodę agregacji dla każdej telemetrii. **Podział przez** kontrolkę pomaga grupować dane przy użyciu właściwości urządzenia jako wymiarów.

- **Kontrola czasu:** Kontrola czasu służy do wybierania czasu trwania, dla którego chcesz analizować dane. Możesz przeciągnąć dowolny koniec suwaka czasu, aby wybrać przedział czasu. Kontrola czasu ma także suwak **rozmiaru interwału** kontrolujący przedział lub rozmiar interwału używany do agregowania danych. 

- **Kontrolka wykresu:** Kontrolka wykresu wizualizuje dane jako wykres liniowy. Widoczność poszczególnych wierszy można przełączać, współpracując z legendą wykresu. 


  ![Przegląd interfejsu użytkownika analizy](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Wykonywanie zapytania dotyczącego danych

Należy rozpocząć od wybrania **grupy urządzeń** i danych telemetrycznych, które mają być analizowane. Gdy skończysz, wybierz pozycję **Analizuj** , aby rozpocząć wizualizację danych.

- **Grupa urządzeń:** [Grupa urządzeń](tutorial-use-device-groups.md) to zdefiniowana przez użytkownika grupa urządzeń. Na przykład wszystkie lodówki w Oakland lub wszystkie wersje 2,0 wiatru.

- Dane **telemetryczne:** Wybierz dane telemetryczne, które chcesz analizować i eksplorować. Możesz wybrać wiele telemetrii do przeanalizowania. Domyślna metoda agregacji jest ustawiana na wartość średnia dla liczb i liczby dla typu danych String. Obsługiwane metody agregacji dla liczbowych typów danych to Average, maksimum, minimum, Count i, sum.  Liczba obsługiwanych metod agregacji dla typu danych string to Count.

- **Podziel według:** Kontrolka "dzielenie przez" pomaga grupować dane przy użyciu właściwości urządzenia jako wymiarów. Wartości urządzenia i właściwości chmury są przyłączone wraz z danymi telemetrycznymi jako i gdy są wysyłane przez urządzenie. Jeśli właściwość chmury lub urządzenia została zaktualizowana, dane telemetryczne są pogrupowane według różnych wartości na wykresie.

    > [!TIP]
    > Aby wyświetlić dane osobno dla każdego urządzenia, wybierz opcję Identyfikator urządzenia w kontrolce "dzielenie przez".

## <a name="interacting-with-your-data"></a>Korzystanie z danych

Po wykonaniu zapytania o dane możesz rozpocząć wizualizację na wykresie liniowym. Możesz pokazać/ukryć telemetrię, zmienić czas trwania, wyświetlić dane telemetryczne w siatce danych.

- **Panel edytora czasu:** Domyślnie będziemy pobierać dane z ostatniego dnia. Możesz przeciągnąć dowolny koniec suwaka czasu, aby zmienić czas trwania. Można również użyć kontrolki kalendarza, aby wybrać jeden z wstępnie zdefiniowanych zasobników czasu lub wybrać niestandardowy zakres czasu. Kontrola czasu ma także suwak **rozmiaru interwału** kontrolujący przedział lub rozmiar interwału używany do agregowania danych.

    ![Edytor czasu](media/howto-create-analytics/timeeditorpanel.png)

    - **Wewnętrzny suwak zakresu dat**: Użyj dwóch kontrolek punktu końcowego, przeciągając je w żądanym przedziale czasu. Ten wewnętrzny zakres dat jest ograniczony przez kontrolkę suwaka zewnętrznego zakresu dat.
    
   
    - **Kontrolka suwaka zewnętrznego zakresu dat**: Użyj kontrolek punktu końcowego, aby wybrać zewnętrzny zakres dat, który będzie dostępny dla wewnętrznej kontroli zakresu dat.

    - **Przyciski zwiększania i zmniejszania zakresu dat**: Zwiększ lub Zmniejsz zakres czasu, wybierając jeden z przycisków dla interwału, który chcesz.

    - **Suwak rozmiaru interwału**: Użyj go, aby powiększyć i wykroczyć interwały w tym samym przedziale czasu. Ta akcja zapewnia dokładniejszą kontrolę nad przenoszeniem między dużymi wycinkami czasu. Można jej używać do wyświetlania szczegółowych widoków o wysokiej rozdzielczości, nawet do milisekund. Domyślny punkt początkowy suwaka jest ustawiany jako najbardziej optymalny widok danych z zaznaczenia, który równoważy rozdzielczość, szybkość zapytania i stopień szczegółowości.
    
    - **Selektor zakresu dat**: za pomocą tego formantu sieci Web można łatwo wybrać odpowiednie zakresy dat i godzin. Można również użyć kontrolki, aby przełączać się między różnymi strefami czasowymi. Po wprowadzeniu zmian do zastosowania w bieżącym obszarze roboczym wybierz pozycję Zapisz.

    > [!TIP]
    > Rozmiar interwału jest określany dynamicznie na podstawie wybranego przedziału czasu. Mniejsze przedziały czasu umożliwią agregowanie danych do bardzo szczegółowych interwałów wynoszących maksymalnie kilka sekund.


- **Legenda wykresu:** Legenda wykresu przedstawia wybraną telemetrię na wykresie. Możesz umieścić wskaźnik myszy nad każdym elementem w legendzie, aby ustawić fokus na wykresie. W przypadku korzystania z funkcji "Split by" dane telemetryczne są pogrupowane według odpowiednich wartości wybranego wymiaru. Widoczność poszczególnych danych telemetrycznych lub całej grupy można zmienić, klikając nazwę grupy.  


- **Kontrolka formatu osi y:** tryb osi y jest przełączany przez dostępne opcje widoku osi y. Ten formant jest dostępny tylko wtedy, gdy różne telemetrii są wizualizowane. Oś y można ustawić, wybierając jeden z trzech trybów:

    - **Stos:** Wykres dla każdej telemetrii jest układany, a każdy wykres ma własną oś y. Ten tryb jest ustawiony jako domyślny.
    - **Udostępnione:** Wykres dla każdej telemetrii jest wykreślany na tej samej osi y.
    - **Nakładanie się:** Służy do układania wielu linii na tej samej osi y, przy czym dane osi y zmieniają się w oparciu o wybrany wiersz.

  ![Rozmieszczanie danych na osi y z różnymi trybami wizualizacji](media/howto-create-analytics/yaxiscontrol.png)

- **Kontrolka powiększenia:** Opcja Powiększ umożliwia przechodzenie do szczegółów danych. Jeśli znajdziesz okres, na którym chcesz się skoncentrować w zestawie wyników, użyj wskaźnika myszy, aby uzyskać obszar, a następnie przeciągnij go do wybranego punktu końcowego. Następnie kliknij prawym przyciskiem myszy wybrany obszar i kliknij polecenie Powiększ.

  ![Powiększ dane](media/howto-create-analytics/zoom.png)

W obszarze wielokropka istnieje więcej kontrolek wykresu do współpracy z danymi:

- **Wyświetl siatkę:** Wyniki są dostępne w formacie tabeli, umożliwiając wyświetlanie określonej wartości dla każdego punktu danych.

- **Pobierz jako plik CSV:** Wyniki są dostępne do wyeksportowania jako plik z wartościami rozdzielanymi przecinkami (CSV). Plik CSV zawiera dane dla każdego urządzenia. Wyniki są eksportowane z zastosowaniem interwału i określonego przedziału czasu. 

- **Upuść znacznik:** Kontrolka "Drop marker" umożliwia zakotwiczenie określonych punktów danych na wykresie. Jest to przydatne, gdy próbujesz porównać dane dla wielu wierszy w różnych okresach czasu.

  ![Wyświetlanie widoku siatki dla analizy](media/howto-create-analytics/additionalchartcontrols.png)
