---
title: Konfigurowanie bramy sygnałów dla rejestrowania wideo opartego na zdarzeniach — Azure
description: Ten artykuł zawiera wskazówki dotyczące konfigurowania bramy sygnałów w grafie multimediów.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: 4204a43915f9c79cae7dfe82b37e741fee89fb4a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380243"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Konfigurowanie bramy sygnałów dla nagrywania wideo opartego na zdarzeniach

W obrębie grafu nośnika [węzeł procesora bramka sygnałów](media-graph-concept.md#signal-gate-processor) umożliwia przekazanie nośników z jednego węzła do drugiego, gdy Brama jest wyzwalana przez zdarzenie. Po wyzwoleniu Brama zostanie otwarta i umożliwi przepływ multimediów przez określony czas. W przypadku braku zdarzeń do wyzwolenia bramy zostanie ZAMKNIĘTA brama, a nośnik zakończy przepływ. Procesor bramy sygnałów ma zastosowanie do nagrywania wideo opartego na zdarzeniach.
Ten artykuł zawiera szczegółowe informacje na temat sposobu konfigurowania procesora sygnałów.

## <a name="suggested-pre-reading"></a>Sugerowane przed odczytem
-   [Graf multimedialny](media-graph-concept.md)
-   [Nagrywanie wideo oparte na zdarzeniach](event-based-video-recording-concept.md)


## <a name="problem"></a>Problem
Użytkownik może chcieć rozpocząć nagrywanie określonego czasu przed wyzwalaczem lub po jego wyzwoleniu przez zdarzenie. Użytkownik wie akceptowalne opóźnienie w systemie, więc użytkownik chce określić opóźnienie procesora sygnałów. Użytkownik chce określić najkrótszy i najdłuższy czas, w jakim okres ich rejestrowania może być bez względu na liczbę otrzymywanych nowych zdarzeń.
 
### <a name="use-case-scenario"></a>Scenariusz przypadków użycia
Załóżmy, że chcesz nagrać wideo za każdym razem, gdy otwierane są drzwi z przodu. Chcesz, aby **X** s przed otwieranymi drzwiami był uwzględniony w nagraniu. Nagranie ma trwać co najmniej **Y** s, jeśli drzwi nie są otwierane ponownie. Chcesz, aby nagranie było ostatnie **z przeważnie** s, jeśli drzwi są otwierane wielokrotnie. Wiadomo, że czujnik drzwi ma opóźnienie **K** s i chcesz zmniejszyć prawdopodobieństwo, że zdarzenia są nieuznawane ("późne nadejście"), więc chcesz zezwolić na dostarczenie zdarzeń co najmniej **k** sekund.


## <a name="solution"></a>Rozwiązanie

**_Modyfikowanie parametrów procesora sygnalizującego sygnał_* _

Procesor bramy sygnałów jest konfigurowany przez 4 parametry:
- _ *okno oceny aktywacji**
- **Przesunięcie sygnału aktywacji**
- **okno minimalnej aktywacji**
- **okno maksymalnej aktywacji**. 

Gdy procesor bramy sygnałów zostanie wyzwolony, pozostanie otwarty na minimalny czas aktywacji. Zdarzenie aktywacji rozpoczyna się od sygnatury czasowej dla najwcześniejszego zdarzenia i przesunięcia sygnału aktywacji. Jeśli procesor bramy sygnałów zostanie wyzwolony ponownie, podczas gdy jest otwarty, resetowanie czasomierza i Brama pozostaną otwarte przez co najmniej minimalny czas aktywacji. Procesor bramy sygnałów nigdy nie będzie dłużej dłuższy niż maksymalny czas aktywacji. Zdarzenie **(zdarzenie 1)** , które występuje przed innym zdarzeniem **(zdarzenie 2)** , w oparciu o sygnatury czasowe nośnika, podlega nieuznawaniu, jeśli system spowolnienia i **zdarzenie 1** dociera do procesora sygnałów po **zdarzeniu 2**. Jeśli **zdarzenie 1** nie dociera między przybyciem **zdarzenia 2** a **oknem oceny aktywacji** , **zdarzenie 1** zostanie pominięte i nie zostanie przesłane przez procesor bramy sygnałów. Identyfikatory korelacji są ustawiane dla każdego zdarzenia. Te identyfikatory są ustawiane na podstawie zdarzenia początkowego i są sekwencyjne dla każdego poniższego zdarzenia.

> [!IMPORTANT]
> Czas mediów zależy od sygnatury czasowej nośnika w przypadku wystąpienia zdarzenia na nośniku. Sekwencja zdarzeń docierających do bramy sygnału może nie odzwierciedlać sekwencji zdarzeń docierających do czasu nośnika.


### <a name="parameters-based-on-when-events-arrive-in-physical-time-to-the-signal-gate"></a>Parametry: (w zależności od tego, kiedy zdarzenia są odbierane w czasie fizycznym do bramy sygnału)

* **minimumActivationTime (najkrótszy możliwy czas trwania nagrania)** = minimalna liczba sekund, przez jaką procesor bramy sygnałów pozostanie otwarty po wyzwoleniu na otrzymywanie nowych zdarzeń, chyba że zostanie przerwany przez **maximumActivationTime**
* **maximumActivationTime (najdłuższy możliwy czas nagrywania)** = Maksymalna liczba sekund od zdarzenia początkowego, które procesor bramy sygnałów pozostanie otwarty po wyzwoleniu, aby otrzymywać nowe zdarzenia, niezależnie od tego, jakie zdarzenia są odbierane
* **activationSignalOffset** = liczba sekund między aktywacją procesora sygnałów a rozpoczęciem nagrywania wideo, zazwyczaj ta wartość jest ujemna, aby rozpocząć nagrywanie przed zdarzeniem wyzwalającym
* **activationEvaluationWindow** = liczba sekund rozpoczynająca się od początkowego zdarzenia wyzwalającego, w którym zdarzenie, które wystąpiło przed początkowym zdarzeniem, w czasie mediów musi dotrzeć do procesora bramy sygnałów, zanim zostanie pominięte i uznane za "późne przyjęcie"

> [!NOTE]
> Późne przyjęcie to każde zdarzenie, które dociera po przekazaniu okna oceny aktywacji, ale to zdarzenie zostało odebrane przed początkowym zdarzeniem w czasie nośnika.

### <a name="limits-of-parameters"></a>Limity parametrów

* **activationEvaluationWindow: od 0 do 10 sekund**

* **activationSignalOffset:-1 minutę do 1 minuty**

* **minimumActivationTime: 1 sekunda**

* **maximumActivationTime: 1 sekunda**


W oparciu o przypadek użycia parametry można ustawić w następujący sposób:

* **activationEvaluationWindow = K s**
* **activationSignalOffset =-X s**
* **minimumActivationWindow = Y s**
* **maximumActivationWindow = Z s**


Poniżej znajduje się przykładowa sekcja węzła procesora sygnałów sygnalizująca w topologii wykresu multimedialnego dla następujących wartości parametrów:
* **activationEvaluationWindow = 1 sekunda**
* **activationSignalOffset = – 5 sekund**
* **minimumActivationTime = 20 sekund**
* **maximumActivationTime = 40 sekund**

> [!IMPORTANT]
> Dla każdej wartości parametru jest oczekiwany [format czasu trwania ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) . 
**Np.: PT1S = 1 sekunda**


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


Rozważmy, jak ta konfiguracja procesora bramy sygnałów będzie zachowywać się w różnych scenariuszach rejestrowania.


**1 zdarzenie z 1 źródła ( *Normalna Aktywacja* )**

Procesor bramy sygnałów, który otrzymuje jedno zdarzenie, spowoduje nagranie, które zaczyna się od "przesunięcie sygnału aktywacji" (5) sekund przed odebraniem zdarzenia w bramie. Pozostała część nagrania to "minimalny czas aktywacji" (20) sekund, ponieważ żadne inne zdarzenia nie dotarły przed upływem minimalnego czasu aktywacji, aby ponownie wyzwolić bramę.

Przykładowy Diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Normalna Aktywacja":::

* Czas trwania rejestrowania =-offset + minimumActivationTime = [E1 + offset, E1 + minimumActivationTime]


**2 zdarzenia od 1 źródła ( *Aktywacja została ponownie wyzwolona* )**

Procesor bramy sygnałów, który otrzymuje dwa zdarzenia, spowoduje nagranie, które zaczyna się od "przesunięcie sygnału aktywacji" (pięć sekund), zanim pierwsze zdarzenie dotarło do bramy. Drugie zdarzenie uzyskuje pięć sekund po pierwszym zdarzeniu, czyli przed upływem "minimalny czas aktywacji" (20) sekund od pierwszego zdarzenia, w związku z czym Brama zostanie ponownie wyzwolona, aby pozostać otwarta. Pozostała część nagrania to "minimalny czas aktywacji" (20) sekund, ponieważ żadne inne zdarzenia nie docierają przed upływem minimalnego czasu aktywacji z drugiego zdarzenia, aby ponownie wyzwolić bramę.

Przykładowy Diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Aktywacja została ponownie wyzwolona":::

* Czas trwania rejestrowania =-offset + (przybycie drugiego zdarzenia — przybycie pierwszego zdarzenia) + minimumActivationTime


**N zdarzeń z 1 źródła ( *Maksymalna aktywacja* )**

Procesor bramy sygnałów otrzymujący N zdarzeń spowodowałaby nagranie, które zaczyna się od "przesunięcie sygnału aktywacji" (5) sekund przed pierwszym zdarzeniem, które dotarło do bramy. Po nadejściu każdego zdarzenia przed ukończeniem "minimalnego czasu aktywacji" (20) sekund od poprzedniego zdarzenia, brama powinna być ciągle ponownie wyzwalana i pozostanie otwarta do momentu "maksymalny czas aktywacji" (40) sekund po pierwszym zdarzeniu, w którym Brama zostanie zamknięta i nie będzie już akceptować żadnych nowych zdarzeń.

Przykładowy Diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Maksymalna aktywacja":::
 
* Czas trwania rejestrowania =-offset + maximumActivationTime

> [!IMPORTANT]
> Na diagramach zakłada się, że każde zdarzenie dociera do tego samego wystąpienia w czasie fizycznym i na nośniku. (Brak późnych przybycia)

## <a name="next-steps"></a>Następne kroki

### <a name="try-it-out"></a>Wypróbuj

[Samouczek nagrywania filmów wideo oparty na zdarzeniach](event-based-video-recording-tutorial.md)

Korzystając z samouczka nagrywania wideo opartego na zdarzeniach, Edytuj [topology.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), zmodyfikuj parametry węzła signalgateProcessor, a następnie postępuj zgodnie z pozostałą częścią samouczka. Przejrzyj nagrania wideo, aby przeanalizować efekt parametrów.



