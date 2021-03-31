---
title: Konfigurowanie bramy sygnałów dla rejestrowania wideo opartego na zdarzeniach — Azure
description: Ten artykuł zawiera wskazówki dotyczące konfigurowania bramy sygnałów w grafie multimediów.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94410797"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Konfigurowanie bramy sygnałów dla nagrywania wideo opartego na zdarzeniach

W obrębie grafu nośnika [węzeł procesora bramy sygnałów](media-graph-concept.md#signal-gate-processor) umożliwia przekazanie nośników z jednego węzła do drugiego, gdy Brama jest wyzwalana przez zdarzenie. Po jego wyzwoleniu Brama zostanie otwarta i umożliwi przepływ multimediów przez określony czas. W przypadku braku zdarzeń do wyzwolenia bramy Brama zostanie ZAMKNIĘTA, a nośnik przestaje przepływać. Możesz użyć procesora sygnałów do nagrywania wideo opartego na zdarzeniach.

W tym artykule dowiesz się, jak skonfigurować procesor bramy sygnałów.

## <a name="suggested-prereading"></a>Sugerowane odczyty
-   [Graf multimedialny](media-graph-concept.md)
-   [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)


## <a name="problem"></a>Problem
Użytkownik może chcieć rozpocząć nagrywanie w określonym czasie przed wyzwalaczem lub po jego wyzwoleniu przez zdarzenie. Użytkownik wie akceptowalne opóźnienia w systemie. Aby określić opóźnienie procesora sygnałów. Chcą również określić minimalny i maksymalny czas trwania nagrań, niezależnie od liczby odbieranych nowych zdarzeń.
 
### <a name="use-case-scenario"></a>Scenariusz przypadków użycia
Załóżmy, że chcesz nagrać wideo za każdym razem, gdy otwierane są drzwi z przodu. Chcesz nagrać: 

- Uwzględnij *X* s przed otwarciem drzwi. 
- Ostatnie co najmniej *Y* s, jeśli drzwi nie są otwierane ponownie. 
- Ostatni *z ostatnich sekund,* Jeśli drzwi są wielokrotnie otwierane. 
 
Wiadomo, że czujnik drzwi ma opóźnienie *K* s. Aby zmniejszyć prawdopodobieństwo, że zdarzenia nie są uznawane za późne nadejście, należy zezwolić na dostarczenie zdarzeń przez co najmniej *K* sekund.


## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, zmodyfikuj parametry procesora bramy sygnału.

Aby skonfigurować procesor bramy sygnałów, użyj następujących czterech parametrów:
- Okno oceny aktywacji
- Przesunięcie sygnału aktywacji
- Okno minimalnej aktywacji
- Okno maksymalnej aktywacji

Gdy procesor bramy sygnałów jest wyzwalany, pozostaje otwarty przez minimalny czas aktywacji. Zdarzenie aktywacji rozpoczyna się od sygnatury czasowej dla najwcześniejszego zdarzenia i przesunięcia sygnału aktywacji. 

Jeśli procesor bramy sygnałów zostanie wyzwolony ponownie, gdy jest otwarty, czasomierz resetuje się, a Brama pozostaje otwarta przez co najmniej minimalny czas aktywacji. Procesor bramy sygnałów nigdy nie pozostaje otwarty dłużej niż maksymalny czas aktywacji. 

Zdarzenie (zdarzenie 1), które występuje przed innym zdarzeniem (zdarzenie 2), w oparciu o sygnatury czasowe multimediów, może zostać pominięte, jeśli system spowolnienia i zdarzenie 1 docierają do procesora bramy sygnałów po zdarzeniu 2. Jeśli zdarzenie 1 nie dociera między przybyciem zdarzenia 2 a oknem oceny aktywacji, zdarzenie 1 zostanie pominięte. Nie jest ona przenoszona przez procesor bramy sygnałów. 

Identyfikatory korelacji są ustawiane dla każdego zdarzenia. Te identyfikatory są ustawiane na podstawie początkowego zdarzenia. Są one sekwencyjne dla każdego poniższego zdarzenia.

> [!IMPORTANT]
> Czas mediów zależy od sygnatury czasowej nośnika w przypadku wystąpienia zdarzenia na nośniku. Sekwencja zdarzeń, które docierają do bramy sygnalizującej, może nie odzwierciedlać sekwencji zdarzeń, które docierają do czasu nośnika.


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>Parametry w oparciu o czas fizyczny, jaki zdarzenia docierają do bramy sygnału

* **minimumActivationTime (najkrótszy możliwy czas nagrywania)**: minimalna liczba sekund, przez jaką procesor bramy sygnałów pozostaje otwarty po wyzwoleniu na otrzymywanie nowych zdarzeń, chyba że zostanie przerwany przez maximumActivationTime.
* **maximumActivationTime (najdłuższy możliwy czas nagrywania)**: Maksymalna liczba sekund od zdarzenia początkowego, które procesor bramy sygnałów pozostanie otwarty po wyzwoleniu, aby otrzymywać nowe zdarzenia, niezależnie od tego, jakie zdarzenia są odbierane.
* **activationSignalOffset**: liczba sekund między aktywacją procesora sygnalizującego i początkiem nagrań wideo. Zazwyczaj ta wartość jest ujemna, ponieważ zaczyna nagrywać przed zdarzeniem wyzwalającym.
* **activationEvaluationWindow**: rozpoczynając od początkowego zdarzenia wyzwalania, liczba sekund, w ciągu których zdarzenie, które wystąpiło przed początkowym zdarzeniem, w czasie mediów musi dotrzeć do procesora sygnalizującego sygnał, zanim zostanie on pominięty i uznany za późne przyjęcie.

> [!NOTE]
> *Późne przyjęcie* to każde zdarzenie, które dociera po przekazaniu okna oceny aktywacji, ale jest ono odbierane przed początkowym zdarzeniem w czasie mediów.

### <a name="limits-of-parameters"></a>Limity parametrów

* **activationEvaluationWindow**: od 0 do 10 sekund
* **activationSignalOffset**:-1 minutę do 1 minuty
* **minimumActivationTime**: 1 sekunda
* **maximumActivationTime**: 1 sekunda


W przypadku użycia należy ustawić parametry w następujący sposób:

* **activationEvaluationWindow**: *K* s
* **activationSignalOffset**: *-X* s
* **minimumActivationWindow**: *Y* s
* **maximumActivationWindow**: *Z* s


Poniżej przedstawiono przykład sposobu, w jaki sekcja węzła **procesora sygnałów sygnalizacyjnych** będzie wyglądała w topologii wykresu multimedialnego dla następujących wartości parametrów:
* **activationEvaluationWindow**: 1 sekunda
* **activationSignalOffset**: – 5 sekund
* **minimumActivationTime**: 20 sekund
* **maximumActivationTime**: 40 sekund

> [!IMPORTANT]
> Dla każdej wartości parametru jest oczekiwany [format czasu trwania ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) . Na przykład PT1S = 1 sekunda.


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Teraz Rozważmy, jak ta konfiguracja procesora bramy sygnałów będzie zachowywać się w różnych scenariuszach rejestrowania.

### <a name="recording-scenarios"></a>Scenariusze rejestrowania

**Jedno zdarzenie z jednego źródła (*Normalna Aktywacja*)**

Procesor bramy sygnalizującej, który odbiera jedno zdarzenie w nagraniu rozpoczynającym się 5 sekund (sygnał aktywacji = 5 sekund), zanim zdarzenie zostanie odebrane na bramie. Pozostała część nagrania to 20 sekund (minimalny czas aktywacji = 20 sekund), ponieważ żadne inne zdarzenia nie docierają przed końcem minimalnego czasu aktywacji, aby ponownie wyzwolić bramę.

Przykładowy Diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Diagram przedstawiający normalną aktywację jednego zdarzenia z jednego źródła.":::

* Czas trwania rejestrowania =-offset + minimumActivationTime = [E1 + offset, E1 + minimumActivationTime]


**Dwa zdarzenia z jednego źródła (*Aktywacja została ponownie wyzwolona*)**

Procesor bramy sygnalizującej, który odbiera dwa zdarzenia, powoduje nagranie, które zaczyna się 5 sekund (przesunięcie sygnału aktywacji = 5 sekund), zanim zdarzenie zostanie odebrane na bramie. Ponadto zdarzenie 2 odbiera 5 sekund po zdarzeniu 1. Ponieważ zdarzenie 2 dociera przed końcem minimalnego czasu aktywacji dla zdarzenia 1 (20 sekund), Brama zostanie ponownie wyzwolona. Pozostała część nagrania to 20 sekund (minimalny czas aktywacji = 20 sekund), ponieważ żadne inne zdarzenia nie docierają przed końcem minimalnego czasu aktywacji z zdarzenia 2, aby ponownie wyzwolić bramę.

Przykładowy Diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Diagram przedstawiający ponownie aktywację dwóch zdarzeń z jednego źródła.":::

* Czas trwania rejestrowania =-offset + (przybycie zdarzenia 2 — przybycie zdarzenia 1) + minimumActivationTime


***N* zdarzeń z jednego źródła (*Maksymalna aktywacja*)**

Procesor bramy sygnalizującej, który odbiera *N* zdarzeń, powoduje nagranie, które zaczyna się 5 sekund (przesunięcie sygnału aktywacji = 5 sekund), zanim pierwsze zdarzenie zostanie odebrane na bramie. Po nadejściu każdego zdarzenia przed upływem minimalnego czasu aktywacji wynoszącego 20 sekund od poprzedniego zdarzenia Brama jest ciągle ponownie wyzwalana. Pozostanie otwarte do momentu, aż maksymalny czas aktywacji wynoszący 40 sekund po pierwszym zdarzeniu. Następnie Brama jest zamykana i nie akceptuje już żadnych nowych zdarzeń.

Przykładowy Diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Diagram przedstawiający maksymalną aktywację N zdarzeń z jednego źródła.":::
 
* Czas trwania rejestrowania =-offset + maximumActivationTime

> [!IMPORTANT]
> Na powyższych diagramach przyjęto założenie, że każde zdarzenie dociera do tego samego momentu w czasie fizycznym i na nośniku. Oznacza to, że nie ma żadnych późnych przybycia.

## <a name="next-steps"></a>Następne kroki

Wypróbuj [samouczek nagrywania filmów wideo oparty na zdarzeniach](event-based-video-recording-tutorial.md). Zacznij od edycji [topology.jsna](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json). Zmodyfikuj parametry dla węzła signalgateProcessor, a następnie postępuj zgodnie z pozostałą częścią samouczka. Przejrzyj nagrania wideo, aby przeanalizować efekt parametrów.



